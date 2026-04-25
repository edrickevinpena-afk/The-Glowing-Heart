#include <Adafruit_NeoPixel.h>
#include <WiFi.h>
#include <WebServer.h>

#define LED_PIN    8
#define NUM_LEDS   60

// 🎛️ CONTROL PANEL
#define MAX_BRIGHTNESS  255
#define MIN_BRIGHTNESS  30
#define FLOW_SPEED      200
#define BREATHE_SPEED   5

// 🎨 COLOR MULTIPLIERS
float G_MULT = 0.45;
float B_MULT = 0.0;

// 🌊 BRIGHTNESS CURVE
int brightnessCurve[] = {10, 20, 40, 80, 150, 200};
int curveLength = 6;

// WiFi AP credentials
const char* ssid = "LED Tester 💡";
const char* password = "12345678";

WebServer server(80);
Adafruit_NeoPixel strip(NUM_LEDS, LED_PIN, NEO_GRB + NEO_KHZ800);

int currentEffect = 0; // 0 = off
bool effectChanged = false;

// ─────────────────────────────────────────
// WEB PAGE
// ─────────────────────────────────────────
void handleRoot() {
  String html = R"rawhtml(
<!DOCTYPE html>
<html>
<head>
  <meta name='viewport' content='width=device-width, initial-scale=1'>
  <title>LED Controller</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: sans-serif;
      background: #1a1a2e;
      color: white;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 30px 16px;
      min-height: 100vh;
    }
    h1 { font-size: 1.4rem; margin-bottom: 6px; }
    p  { font-size: 0.85rem; opacity: 0.6; margin-bottom: 28px; }
    .grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 12px;
      width: 100%;
      max-width: 400px;
    }
    .btn {
      padding: 18px 10px;
      border: none;
      border-radius: 14px;
      font-size: 0.95rem;
      font-weight: bold;
      cursor: pointer;
      transition: opacity 0.2s, transform 0.1s;
    }
    .btn:active { transform: scale(0.96); }
    .btn.active { outline: 3px solid white; }
    .off  { background: #444; color: white; grid-column: span 2; }
    .b0   { background: linear-gradient(135deg,#ff6b6b,#ee5a24); color:white; }
    .b1   { background: linear-gradient(135deg,#ffd32a,#ffa801); color:#1a1a2e; }
    .b2   { background: linear-gradient(135deg,#0be881,#05c46b); color:#1a1a2e; }
    .b3   { background: linear-gradient(135deg,#0fbcf9,#0652dd); color:white; }
    .b4   { background: linear-gradient(135deg,#f8a5c2,#e84393); color:white; }
    .b5   { background: linear-gradient(135deg,#fff,#ccc);       color:#1a1a2e; }
    .status {
      margin-top: 28px;
      font-size: 0.85rem;
      opacity: 0.5;
    }
  </style>
</head>
<body>
  <h1>💡 LED Controller</h1>
  <p>Tap an effect to test it</p>
  <div class='grid'>
    <button class='btn off'    onclick='send(0)'>⏹ OFF</button>
    <button class='btn b0' id='b0' onclick='send(1)'>🌊 RFID Flow<br><small>Startup effect</small></button>
    <button class='btn b1' id='b1' onclick='send(2)'>☀️ Warm Pulse<br><small>Slow breathe</small></button>
    <button class='btn b2' id='b2' onclick='send(3)'>🌿 Forest Wave<br><small>Green flow</small></button>
    <button class='btn b3' id='b3' onclick='send(4)'>🌊 Ocean Drift<br><small>Blue fade</small></button>
    <button class='btn b4' id='b4' onclick='send(5)'>🌸 Rose Glow<br><small>Pink shimmer</small></button>
    <button class='btn b5' id='b5' onclick='send(6)'>🕯️ Candlelight<br><small>Warm white flicker</small></button>
  </div>
  <div class='status' id='status'>Ready</div>
  <script>
    function send(n) {
      document.getElementById('status').innerText = 'Sending...';
      fetch('/set?effect=' + n)
        .then(r => r.text())
        .then(t => {
          document.getElementById('status').innerText = t;
          // highlight active button
          for(let i=0;i<=5;i++){
            let b=document.getElementById('b'+i);
            if(b) b.classList.remove('active');
          }
          if(n>0){
            let active=document.getElementById('b'+(n-1));
            if(active) active.classList.add('active');
          }
        });
    }
  </script>
</body>
</html>
)rawhtml";
  server.send(200, "text/html", html);
}

void handleSet() {
  if (server.hasArg("effect")) {
    currentEffect = server.arg("effect").toInt();
    effectChanged = true;
    server.send(200, "text/plain", "Effect " + String(currentEffect) + " activated!");
  } else {
    server.send(400, "text/plain", "Missing effect param");
  }
}

// ─────────────────────────────────────────
// LED EFFECTS
// ─────────────────────────────────────────

// 🌊 Effect 1 — RFID flow (your original startup)
void effectRFIDFlow() {
  G_MULT = 0.45; B_MULT = 0.0; // morning yellow
  int totalSteps = NUM_LEDS + curveLength;
  for (int step = 0; step < totalSteps; step++) {
    if (effectChanged) return; // interrupt if new effect selected
    for (int i = 0; i < NUM_LEDS; i++) {
      int dist = step - i;
      if (dist < 0) continue;
      if (dist < curveLength) {
        int b = brightnessCurve[dist];
        strip.setPixelColor(i, strip.Color(b, (int)(b*G_MULT), (int)(b*B_MULT)));
      } else {
        strip.setPixelColor(i, strip.Color(MAX_BRIGHTNESS, (int)(MAX_BRIGHTNESS*G_MULT), (int)(MAX_BRIGHTNESS*B_MULT)));
      }
    }
    strip.show();
    delay(FLOW_SPEED);
  }
  setAll(MAX_BRIGHTNESS);
  strip.show();
}

// ☀️ Effect 2 — Warm Pulse (amber breathe)
void effectWarmPulse() {
  G_MULT = 0.35; B_MULT = 0.0;
  for (int b = MIN_BRIGHTNESS; b <= MAX_BRIGHTNESS; b++) {
    if (effectChanged) return;
    setAll(b); strip.show(); delay(BREATHE_SPEED);
  }
  for (int b = MAX_BRIGHTNESS; b >= MIN_BRIGHTNESS; b--) {
    if (effectChanged) return;
    setAll(b); strip.show(); delay(BREATHE_SPEED);
  }
}

// 🌿 Effect 3 — Forest Wave (green flow)
void effectForestWave() {
  G_MULT = 1.0; B_MULT = 0.0;
  int totalSteps = NUM_LEDS + curveLength;
  for (int step = 0; step < totalSteps; step++) {
    if (effectChanged) return;
    for (int i = 0; i < NUM_LEDS; i++) {
      int dist = step - i;
      if (dist < 0) continue;
      if (dist < curveLength) {
        int b = brightnessCurve[dist];
        // green: R=0, G=b, B=0
        strip.setPixelColor(i, strip.Color(0, b, 0));
      } else {
        strip.setPixelColor(i, strip.Color(0, MAX_BRIGHTNESS, 0));
      }
    }
    strip.show();
    delay(FLOW_SPEED);
  }
  for (int i = 0; i < NUM_LEDS; i++) strip.setPixelColor(i, strip.Color(0, MAX_BRIGHTNESS, 0));
  strip.show();
}

// 🌊 Effect 4 — Ocean Drift (blue fade)
void effectOceanDrift() {
  for (int b = MIN_BRIGHTNESS; b <= MAX_BRIGHTNESS; b++) {
    if (effectChanged) return;
    for (int i = 0; i < NUM_LEDS; i++) {
      // wave offset per LED for ripple effect
      int wave = (int)(b + sin(i * 0.4 + b * 0.05) * 30);
      wave = constrain(wave, 0, 255);
      strip.setPixelColor(i, strip.Color(0, (int)(wave * 0.3), wave));
    }
    strip.show();
    delay(BREATHE_SPEED + 2);
  }
  for (int b = MAX_BRIGHTNESS; b >= MIN_BRIGHTNESS; b--) {
    if (effectChanged) return;
    for (int i = 0; i < NUM_LEDS; i++) {
      int wave = (int)(b + sin(i * 0.4 + b * 0.05) * 30);
      wave = constrain(wave, 0, 255);
      strip.setPixelColor(i, strip.Color(0, (int)(wave * 0.3), wave));
    }
    strip.show();
    delay(BREATHE_SPEED + 2);
  }
}

// 🌸 Effect 5 — Rose Glow (pink shimmer)
void effectRoseGlow() {
  static float offset = 0;
  offset += 0.05;
  for (int i = 0; i < NUM_LEDS; i++) {
    if (effectChanged) return;
    int b = (int)(MAX_BRIGHTNESS * (0.6 + 0.4 * sin(i * 0.3 + offset)));
    strip.setPixelColor(i, strip.Color(b, (int)(b * 0.15), (int)(b * 0.25)));
  }
  strip.show();
  delay(20);
}

// 🕯️ Effect 6 — Candlelight (warm white flicker)
void effectCandlelight() {
  for (int i = 0; i < NUM_LEDS; i++) {
    if (effectChanged) return;
    int flicker = random(160, MAX_BRIGHTNESS);
    strip.setPixelColor(i, strip.Color(flicker, (int)(flicker * 0.45), (int)(flicker * 0.05)));
  }
  strip.show();
  delay(random(30, 80));
}

// ─────────────────────────────────────────
void setAll(int b) {
  for (int i = 0; i < NUM_LEDS; i++) {
    strip.setPixelColor(i, strip.Color(b, (int)(b*G_MULT), (int)(b*B_MULT)));
  }
}

// ─────────────────────────────────────────
void setup() {
  Serial.begin(115200);
  strip.begin();
  strip.setBrightness(255);
  strip.clear();
  strip.show();

  WiFi.softAP(ssid, password);
  Serial.println("AP started!");
  Serial.print("IP: ");
  Serial.println(WiFi.softAPIP());

  server.on("/", handleRoot);
  server.on("/set", handleSet);
  server.begin();

  Serial.println("Server started!");
}

void loop() {
  server.handleClient();

  if (effectChanged) {
    effectChanged = false;
    strip.clear();
    strip.show();
  }

  switch (currentEffect) {
    case 0: strip.clear(); strip.show(); break;
    case 1: effectRFIDFlow();   break;
    case 2: effectWarmPulse();  break;
    case 3: effectForestWave(); break;
    case 4: effectOceanDrift(); break;
    case 5: effectRoseGlow();   break;
    case 6: effectCandlelight(); break;
  }
}
