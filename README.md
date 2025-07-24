// 🚗 Self-Parking RC Car Project
// 🧠 Using Arduino + Ultrasonic Sensors + L298N Motor Driver

// Define sensor pins
const int trigLeft = 2;
const int echoLeft = 3;
const int trigRight = 4;
const int echoRight = 5;
const int trigBack = 6;
const int echoBack = 7;

// Motor control pins
const int ENA = 9;
const int IN1 = 10;
const int IN2 = 11;
const int IN3 = 12;
const int IN4 = 13;
const int ENB = 8;

long duration;
int distanceLeft, distanceRight, distanceBack;

void setup() {
  Serial.begin(9600);

  pinMode(trigLeft, OUTPUT);
  pinMode(echoLeft, INPUT);
  pinMode(trigRight, OUTPUT);
  pinMode(echoRight, INPUT);
  pinMode(trigBack, OUTPUT);
  pinMode(echoBack, INPUT);

  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  pinMode(ENA, OUTPUT);
  pinMode(ENB, OUTPUT);

  Serial.println("System Ready: Searching for parking space...");
}

// Ultrasonic distance function
int getDistance(int trigPin, int echoPin) {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  return duration * 0.034 / 2; // Convert to cm
}

// Basic motor control
void forward() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  analogWrite(ENA, 150);
  analogWrite(ENB, 150);
}

void stopCar() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);
}

void reverseRight() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  analogWrite(ENA, 120);
  analogWrite(ENB, 120);
}

void loop() {
  distanceLeft = getDistance(trigLeft, echoLeft);
  distanceRight = getDistance(trigRight, echoRight);
  Serial.print("Left: "); Serial.print(distanceLeft);
  Serial.print(" | Right: "); Serial.println(distanceRight);

  // If both sides have enough space, initiate parking
  if (distanceLeft > 25 && distanceRight > 25) {
    Serial.println("Parking space detected! Initiating self-parking...");
    stopCar();
    delay(500);
    reverseRight();
    delay(3000);  // Adjust time based on chassis length
    stopCar();
    Serial.println("Car Parked Successfully!");
    while(1); // Stop further actions
  } else {
    forward();
  }

  delay(300);
}
