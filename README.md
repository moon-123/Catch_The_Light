# Catch_The_Light

```C
#include <LiquidCrystal_I2C.h>
#include <Wire.h>
#define INTERVAL_CHECK_BUTTON 50  // 채터링 
#define LEVEL_1 20  // 1단계 난이도 시작 점수 설정 
#define LEVEL_2 40  // 2단계 난이도 시작 점수 설정 
#define LEVEL_3 60  // 3단계 난이도 시작 점수 설정 

LiquidCrystal_I2C lcd(0x27, 16, 2);   // LCD사용 선언 

// 초기 LCD출력
const String screen_1 = "Welcome";
const String screen_2 = "press to start";

//채터링용 변수
unsigned long buttonMillis = 0;
byte buttonStatus = HIGH;
unsigned long curMillis = 0;

// 변수선언
int randnum = 0;
int buttonState_1, buttonState_2, buttonState_3 = 0;
int score = 1;
int clickcount = 0;
int phase = 1;  // 단계
int i = -1;
int j, k;
int Flag, endFlag = 0;
unsigned int timer, count = 0;
unsigned int print_timer = 30 - timer;
int T = 0;

// 핀 번호 설정
const int LED_L1[3] = {22, 24, 26};    // 핀 번호 배열로 선언
const int LED_L2[3] = {28, 30, 32};    // 핀 번호 배열로 선언
const int LED_L3[3] = {34, 36, 38};    // 핀 번호 배열로 선언
const int LED_center[3] = {40, 42, 44};  // 가운데
const int LED_buttonpin_1 = 2;  // 1 버튼
const int LED_buttonpin_2 = 18;  // 2 버튼
const int LED_buttonpin_3 = 19;  // 3 버튼

int c[3]; // 랜덤 배열

// LED상태를 나타내는 변수
int ColorNumberL1, ColorNumberL2, ColorNumberL3, ColorNumberC = 0;


//빨강
const int red[3] = {0, 255, 255};
//파랑
const int blue[3] = {255, 255, 0};
//초록
const int green[3] = {255, 0, 255};
//defalt, 하양 
const int white[3] = {0, 0, 0};


// 30초가 되면 gameover함수 실행, 화면에 점수 출력, 이후 리셋버튼 외에는 안 먹힘 
void gameover(){

    // LCD출력 
    lcd.clear();
    lcd.setCursor(3, 0);
    lcd.print("GAME OVER");
    lcd.setCursor(0, 1);
    lcd.print("SCORE: ");
    lcd.print(score);

    // 종료됨을 알리는 LED 무한 반복
    while(1){
    for (int i = 0; i < 3; i++) { // 빨
        analogWrite(LED_L1[i], red[i]);
        analogWrite(LED_L2[i], red[i]);
        analogWrite(LED_L3[i], red[i]);
        analogWrite(LED_center[i], red[i]);
      }
      delay(300);
    for (int i = 0; i < 3; i++) { // 파 
        analogWrite(LED_L1[i], blue[i]);
        analogWrite(LED_L2[i], blue[i]);
        analogWrite(LED_L3[i], blue[i]);
        analogWrite(LED_center[i], blue[i]);
      }
      delay(300);
    for (int i = 0; i < 3; i++) { // 초 
        analogWrite(LED_L1[i], green[i]);
        analogWrite(LED_L2[i], green[i]);
        analogWrite(LED_L3[i], green[i]);
        analogWrite(LED_center[i], green[i]);
      }
      delay(300);
    }
    
  }


// 랜덤 배열 생성
void arrayMaker() {
  for (j = 0; j < 3; j++) {
    c[j] = random(3);
    for (k = 0; k < j; k++) {
      if (c[j] == c[k]) {
        j--;
        break;
      }
    }
  }
}


// 시작화면 LED출력 
void LED_Defalt(){
  for (int i = 0; i < 3; i++) { // 빨
        analogWrite(LED_L1[i], white[i]);
        analogWrite(LED_L2[i], white[i]);
        analogWrite(LED_L3[i], white[i]);
        analogWrite(LED_center[i], white[i]);
      }
  }

  
void RandomLED(int phase) {            // 랜덤 색 (빨, 파, 초)
  switch (c[0]) {                          // L1 LED 색깔 결정 
    case 0:
      for (int i = 0; i < 3; i++) { // 빨
        analogWrite(LED_L1[i], red[i]);
      }
      ColorNumberL1 = 0;
      break;
    case 1:
      for (int i = 0; i < 3; i++) { // 파
        analogWrite(LED_L1[i], blue[i]);
      }
      ColorNumberL1 = 1;
      break;
    case 2:
      for (int i = 0; i < 3; i++) { // 초
        analogWrite(LED_L1[i], green[i]);
      }
      ColorNumberL1 = 2;
      break;
  }
  switch (c[1]) {                          // L2 LED 색깔 결정
    case 0:
      for (int i = 0; i < 3; i++) { // 빨
        analogWrite(LED_L2[i], red[i]);
      }
      ColorNumberL2 = 0;
      break;
    case 1:
      for (int i = 0; i < 3; i++) { // 파
        analogWrite(LED_L2[i], blue[i]);
      }
      ColorNumberL2 = 1;
      break;
    case 2:
      for (int i = 0; i < 3; i++) { // 초
        analogWrite(LED_L2[i], green[i]);
      }
      ColorNumberL2 = 2;
      break;
  }
  if (phase == 2) {
    i = c[2];
  }

  switch (i) {                          // L3 LED 색깔 결정
    case 0:
      for (int i = 0; i < 3; i++) { // 빨
        analogWrite(LED_L3[i], red[i]);
      }
      ColorNumberL3 = 0;
      break;
    case 1:
      for (int i = 0; i < 3; i++) { // 파
        analogWrite(LED_L3[i], blue[i]);
      }
      ColorNumberL3 = 1;
      break;
    case 2:
      for (int i = 0; i < 3; i++) { // 초
        analogWrite(LED_L3[i], green[i]);
      }
      ColorNumberL3 = 2;
      break;
  }


}



void centerOn(int phase) {        // 가운데 LED 킴

  if (phase == 1) {
    randnum = random(2);
    switch (c[randnum]) {
      case 0:
        for (int i = 0; i < 3; i++) { // 빨
          analogWrite(LED_center[i], red[i]);
        }
        ColorNumberC = 0;
        break;

      case 1:
        for (int i = 0; i < 3; i++) { // 파
          analogWrite(LED_center[i], blue[i]);
        }
        ColorNumberC = 1;
        break;

      case 2:
        for (int i = 0; i < 3; i++) { // 초
          analogWrite(LED_center[i], green[i]);
        }
        ColorNumberC = 2;
        break;
    }
  }


  if (phase == 2) {   // 2단계 색 추가 
    randnum = random(3);
    switch (randnum) {
      case 0:
        for (int i = 0; i < 3; i++) { // 빨
          analogWrite(LED_center[i], red[i]);
        }
        ColorNumberC = 0;
        break;

      case 1:
        for (int i = 0; i < 3; i++) { // 파
          analogWrite(LED_center[i], blue[i]);
        }
        ColorNumberC = 1;
        break;

      case 2:
        for (int i = 0; i < 3; i++) { // 초
          analogWrite(LED_center[i], green[i]);
        }
        ColorNumberC = 2;
        break;

    }
  }
}

void LCD_display(int score) {
  lcd.setCursor(0, 0);
  lcd.print("score: ");
  lcd.print(score);
}



// 버튼 인터럽트 발생시 실행 함수 
void Button_1() {   // 채터링 고려
  byte bStatus = LOW;
  int c = 1;
  while (c <= 2) {
    if (buttonMillis == 0) {
      if (buttonStatus != bStatus) {
        buttonStatus = bStatus;
        buttonMillis = curMillis;
      }
    }

    else {
      if (buttonStatus != bStatus) {
        buttonMillis = 0;
        buttonStatus = bStatus;
      }
      else if (curMillis - buttonMillis > INTERVAL_CHECK_BUTTON) {
        if (buttonStatus == LOW) {
          buttonState_1 = HIGH;
        }
        buttonMillis = 0;
        buttonStatus = HIGH;
      }
    }
    c++;
  }
}

void Button_2() {
  byte bStatus = LOW;
  int c = 1;
  while (c <= 2) {
    if (buttonMillis == 0) {
      if (buttonStatus != bStatus) {
        buttonStatus = bStatus;
        buttonMillis = curMillis;
      }
    }

    else {
      if (buttonStatus != bStatus) {
        buttonMillis = 0;
        buttonStatus = bStatus;
      }
      else if (curMillis - buttonMillis > INTERVAL_CHECK_BUTTON) {
        if (buttonStatus == LOW) {
          buttonState_2 = HIGH;
        }
        buttonMillis = 0;
        buttonStatus = HIGH;
      }
    }
    c++;
  }
}

void Button_3() {
  byte bStatus = LOW;
  int c = 1;
  while (c <= 2) {
    if (buttonMillis == 0) {
      if (buttonStatus != bStatus) {
        buttonStatus = bStatus;
        buttonMillis = curMillis;
      }
    }

    else {
      if (buttonStatus != bStatus) {
        buttonMillis = 0;
        buttonStatus = bStatus;
      }
      else if (curMillis - buttonMillis > INTERVAL_CHECK_BUTTON) {
        if (buttonStatus == LOW) {
          buttonState_3 = HIGH;
        }
        buttonMillis = 0;
        buttonStatus = HIGH;
      }
    }
    c++;
  }
}



// 타이머 인터럽트 서브루틴 
ISR(TIMER0_COMPA_vect) {
  if (timer < 30 || endFlag == 0) {
    if (clickcount > 1) {
      count++;

      if (count > 250) {
        timer++;
        count = 0;
        TCNT0 = 0;
      }
    }
  }
  else if (timer == 30) {
    timer = 0;
  }
}


// 난이도 증가 함수 
void levelUp(int score) {
  if (score >= LEVEL_1) {
    phase = 2;
  }

  if (score >= LEVEL_2) {
    if (Flag == 0) {
      arrayMaker();
      Flag = 1;
    }
  }

  if (score >= LEVEL_2 && score < LEVEL_3) {
    if (clickcount % 5 == 0) {
      arrayMaker();
      clickcount++;
    }
  }

  if (score >= LEVEL_3) {
    if (clickcount % 2 == 0) {
      arrayMaker();
      clickcount++;
    }
  }
}




// 초기 실행
void setup() {
  Serial.begin(9600);
  lcd.init();   // lcd 초기선언
  lcd.backlight();  // 배경 빛남
  arrayMaker();

  
  // 초기화면
  lcd.setCursor(0, 0);
  lcd.print(screen_1);
  lcd.setCursor(0, 1);
  lcd.print(screen_2);
  LED_Defalt();

  // 핀 모드 설정 
  for (int i = 0; i < 3; i++) {
    pinMode(LED_L1[i], OUTPUT); // 왼쪽 LED 목차 1 Red
    pinMode(LED_L2[i], OUTPUT); // 왼쪽 LED 목차 1 Red
    pinMode(LED_L3[i], OUTPUT); // 왼쪽 LED 목차 1 Red
    pinMode(LED_center[i], OUTPUT);  // catch LED Red
  }

  pinMode(LED_buttonpin_1, INPUT_PULLUP);  // 1 버튼 입력
  pinMode(LED_buttonpin_2, INPUT_PULLUP);  // 2 버튼 입력
  pinMode(LED_buttonpin_3, INPUT_PULLUP);  // 3 버튼 입력


  // 버튼 인터럽트 설정, falling에 인터럽트 발생
  attachInterrupt(digitalPinToInterrupt(LED_buttonpin_1), Button_1, FALLING);
  attachInterrupt(digitalPinToInterrupt(LED_buttonpin_2), Button_2, FALLING);
  attachInterrupt(digitalPinToInterrupt(LED_buttonpin_3), Button_3, FALLING);

  TCCR0A = 0; // 레지스터 초기화
  TCCR0B = 0; // 레지스터 초기화
  TCNT0 = 0;  // 타이머값 초기화

  OCR0A = 255;  // 오버플로우 발생시 인터럽트, 255를 넘어가면

  // 모드설정
  TCCR0B |= (1 << WGM02);
  TCCR0B |= (1 << CS02) | (0 << CS00);
  TIMSK0 |= (1 << OCIE0A);

  // 전역 인터럽트 설정
  sei();

}






// 반복 실행
void loop() {
  // RandomLED_first(phase);   // 처음 두 LED의 색을 지정하고 킨다.
  curMillis = millis();

  //난이도 증가
  levelUp(score);

  // 시간 출력, 초기화면 방해받지 않기
  if (clickcount > 1) {
    if (endFlag != 1) {
      lcd.setCursor(0, 1);
      lcd.print(30-timer);
      lcd.print(" ");
    }
  }


  if (buttonState_1 == HIGH) {   // 1번버튼 누름
    RandomLED(phase);
    lcd.clear();  // lcd 초기화
    clickcount++; // 누른횟수 증가
    if (ColorNumberL1 == ColorNumberC) {  // 누른 버튼의 보기 색과 가운데 색이 같으면 정답, 점수 플러스
      score++;
      LCD_display(score);   // 점수 출력함수
      // 가운데 led 단계에 맞게 랜덤 출력
    } else {  // 다르면 오답, 점수 마이너스
      score--;
      LCD_display(score);
    }
    centerOn(phase);
    buttonState_1 = LOW;    // 버튼상태 초기화
  }


  if (buttonState_2 == HIGH) {   // 2번버튼 누름
    RandomLED(phase);
    lcd.clear();
    clickcount++;
    if (ColorNumberL2 == ColorNumberC) {  // 누른 버튼의 보기 색과 가운데 색이 같으면 정답, 점수 플러스
      score++;
      LCD_display(score);
    } else {  // 다르면 오답, 점수 마이너스
      score--;
      LCD_display(score);

    }
    centerOn(phase);
    buttonState_2 = LOW;
  }


  if (buttonState_3 == HIGH) {   // 3번버튼 누름
    RandomLED(phase);
    lcd.clear();
    clickcount++;
    if (ColorNumberL3 == ColorNumberC) {  // 누른 버튼의 보기 색과 가운데 색이 같으면 정답, 점수 플러스
      score++;
      LCD_display(score);
    } else {  // 다르면 오답, 점수 마이너스
      score--;
      LCD_display(score);
    }
    centerOn(phase);
    buttonState_3 = LOW;
  }

  // 30초 되면 게임오버 
  if (timer == 30) {
    endFlag = 1;
    gameover();
  }
}


```
