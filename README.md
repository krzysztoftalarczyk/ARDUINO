1.
Ceiling light on WS2812B RGB With Arduino supplied by the 5V/40A impulse power supply.

#include <FastLED.h>

#define DATA_PIN    8

#define BRIGHTNESS  40

#define LED_TYPE    WS2812B

#define COLOR_ORDER GRB

#define NUM_LEDS    576

CRGB leds[NUM_LEDS];


uint8_t paletteIndex = 0;


DEFINE_GRADIENT_PALETTE( temperature_gp ) {
    0,   1, 27,105,
   14,   1, 27,105,
   14,   1, 40,127,
   28,   1, 40,127,
   28,   1, 70,168,
   42,   1, 70,168,
   42,   1, 92,197,
   56,   1, 92,197,
   56,   1,119,221,
   70,   1,119,221,
   70,   3,130,151,
   84,   3,130,151,
   84,  23,156,149,
   99,  23,156,149,
   99,  67,182,112,
  113,  67,182,112,
  113, 121,201, 52,
  127, 121,201, 52,
  127, 142,203, 11,
  141, 142,203, 11,
  141, 224,223,  1,
  155, 224,223,  1,
  155, 252,187,  2,
  170, 252,187,  2,
  170, 247,147,  1,
  184, 247,147,  1,
  184, 237, 87,  1,
  198, 237, 87,  1,
  198, 229, 43,  1,
  212, 229, 43,  1,
  212, 220, 15,  1,
  226, 220, 15,  1,
  226, 171,  2,  2,
  240, 171,  2,  2,
  240,  80,  3,  3,
  255,  80,  3,  3};

CRGBPalette16 myPalette = temperature_gp;

void setup() {

  FastLED.addLeds<LED_TYPE, DATA_PIN, COLOR_ORDER>(leds, NUM_LEDS);
 
 FastLED.setBrightness(BRIGHTNESS);
 
}

void loop() {

  fill_palette(leds, NUM_LEDS, paletteIndex, 255 / NUM_LEDS, myPalette, BRIGHTNESS, LINEARBLEND);
    
EVERY_N_MILLISECONDS(10) {
     
paletteIndex++;
    
 }
 
 FastLED.show();





  2. Arduino Thermometr Atimeter with OLED Screen and pressure gauge supplied by powerbank.

 
#include "U8glib.h"

#include "BMP280.h"

#include "Wire.h"

#define P0 1021.97   

BMP280 bmp;


U8GLIB_SSD1306_128X64 u8g(U8G_I2C_OPT_NO_ACK);

char sT[20];

char sP[9];

char sA[9];

char sA_MIN[9];

char sA_MAX[9];

double A_MIN = 0;

double A_MAX = 0;

void draw(double T, double P, double A) {

u8g.setFont(u8g_font_unifont);

  dtostrf(T, 4, 2, sT);

dtostrf(P, 4, 2, sP);

dtostrf(A, 4, 2, sA);

  u8g.drawStr( 5, 10, "Temp: ");

u8g.drawStr( 5, 30, "Bar : ");

u8g.drawStr( 5, 50, "Alt : ");

u8g.drawStr( 50, 10, sT);

u8g.drawStr( 50, 30, sP);

u8g.drawStr( 50, 50, sA);

}

void draw2(double A_MIN, double A_MAX) {

u8g.setFont(u8g_font_unifont);

  dtostrf(A_MIN, 4, 2, sA_MIN);

dtostrf(A_MAX, 4, 2, sA_MAX);

u8g.drawStr( 5, 20, "A Min: ");

u8g.drawStr( 60, 20, sA_MIN);

u8g.drawStr( 5, 45, "A Max: ");

u8g.drawStr( 60, 45, sA_MAX);

}

void setup() {

Serial.begin(9600);

if (!bmp.begin()) {

Serial.println("BMP init failed!");

while (1);

}

else Serial.println("BMP init success!");

  bmp.setOversampling(4);

  u8g.setColorIndex(1);

u8g.setFont(u8g_font_unifont);

}

void loop(void) {

double T, P;

char result = bmp.startMeasurment();

if (result != 0) {

delay(result);

result = bmp.getTemperatureAndPressure(T, P);

if (result != 0) {

double A = bmp.altitude(P, P0);

 if ( A > A_MAX) {

A_MAX = A;

}

 if ( A < A_MIN || A_MIN == 0) {

A_MIN = A;

}


u8g.firstPage();

do {

draw(T, P, A);

} while ( u8g.nextPage() );

u8g.firstPage();

delay(1000);

 do {

draw2(A_MIN, A_MAX);

} while ( u8g.nextPage() );

u8g.firstPage();

delay(1000);
      
}

else {

Serial.println("Error.");

}

}

else {

Serial.println("Error.");

}

  delay(100);

}


