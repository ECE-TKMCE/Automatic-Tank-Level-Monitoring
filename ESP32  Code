// ================= PIN ASSIGNMENTS (ESP32-C6) =================
#define FLOAT_LOW   14    // Low level float switch
#define FLOAT_HIGH  27    // High level float switch
#define RELAY_PIN   26    // Relay input pin (Active LOW)
#define TDS_PIN     34    // TDS sensor analog pin

// ================= VARIABLES =================
bool pumpState = false;   // false = OFF, true = ON

// TDS variables
const float VREF = 3.3;   // ESP32 reference voltage
const int ADC_RES = 4095; // 12-bit ADC

void setup() {
  Serial.begin(115200);

  // Float switches
  pinMode(FLOAT_LOW, INPUT_PULLUP);
  pinMode(FLOAT_HIGH, INPUT_PULLUP);

  // Relay
  pinMode(RELAY_PIN, OUTPUT);
  digitalWrite(RELAY_PIN, HIGH);   // OFF initially

  Serial.println("===== Automatic Water Pump Controller Started =====");
}

void loop() {
  // ================= FLOAT SENSOR READ =================
  bool lowLevel  = (digitalRead(FLOAT_LOW)  == LOW);
  bool highLevel = (digitalRead(FLOAT_HIGH) == LOW);

  // ================= PUMP CONTROL LOGIC =================
  if (lowLevel && !highLevel) {
    pumpState = true;   // Start pump
  } 
  else if (highLevel) {
    pumpState = false;  // Stop pump
  }

  // ================= RELAY CONTROL =================
  if (pumpState) {
    digitalWrite(RELAY_PIN, LOW);   // ON
  } else {
    digitalWrite(RELAY_PIN, HIGH);  // OFF
  }

  // ================= TDS SENSOR READ =================
  int tdsRaw = analogRead(TDS_PIN);
  float voltage = (tdsRaw * VREF) / ADC_RES;

  // Standard approximate TDS formula
  float tdsValue = (133.42 * voltage * voltage * voltage
                   - 255.86 * voltage * voltage
                   + 857.39 * voltage) * 0.5;

  // ================= SERIAL OUTPUT =================
  Serial.println("------------------------------------------------");

  if (!lowLevel && !highLevel) {
    Serial.println("Tank Status : ABOVE Low but BELOW Full");
  } else if (lowLevel && !highLevel) {
    Serial.println("Tank Status : BELOW Low → Needs Filling!");
  } else if (highLevel) {
    Serial.println("Tank Status : FULL → Stop Pump");
  }

  Serial.print("Pump Status : ");
  Serial.println(pumpState ? "RUNNING (ON)" : "STOPPED (OFF)");

  Serial.print("TDS Raw ADC : ");
  Serial.println(tdsRaw);

  Serial.print("TDS Voltage: ");
  Serial.print(voltage, 2);
  Serial.println(" V");

  Serial.print("TDS Value  : ");
  Serial.print(tdsValue, 0);
  Serial.println(" ppm");

  Serial.println("------------------------------------------------\n");

  delay(2000);  // Update every 2 seconds
}
