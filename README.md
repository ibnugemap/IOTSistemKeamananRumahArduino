# IOTSistemKeamananRumahArduino
 PROJECT ARDUINO IOT SISTEM KEAMANAN PINTU ANTI PENCURI DAN NOTIFIKASI TELEGRAM

Alat yang digunakan adalah :

1.WeMos D1 wifi ESP 8266

2.Sensor Magnet REED

3.Buzzer

4.Kabel Jumper

 
 Source Code:
 
 #include <ESP8266WiFi.h>
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h>
//==============Definisi PIN============
#define pin_SW 12
#define pin_buzer 13
#define buzerOn digitalWrite(pin_buzer,HIGH) //definisi buzer ON
#define buzerOff digitalWrite(pin_buzer,LOW)
//==============Variabel============
int a;
bool pintu = false;
//==============Inisialisasi SSID dan PASWORD=========
char ssid[] = "krupuk";
char password[] = "polytron12345"; // your network key
//===========Inisualisasi TelegramBot=========
#define BOTtoken "751648313:AAHir_uC7HGfQ4H7Zn1kyXX1ydzSOXMmxPU"
// your Bot Token (Get from Botfather)
WiFiClientSecure client;
UniversalTelegramBot bot(BOTtoken, client);
int Bot_mtbs = 1000; //mean time between scan messages
long Bot_lasttime; //last time messages' scan has been done
bool Start = false;
//==============SubProgram Terima Tekt============
void handleNewMessages(int numNewMessages) {
 Serial.println("handleNewMessages"); //Tampil
Serial Monitor
 Serial.println(String(numNewMessages));
 for (int i = 0; i < numNewMessages; i++) {
//Membaca pesan Baru
 String chat_id = String(bot.messages[i].chat_id);
//variabel Chat_ID
String text = bot.messages[i].text;
//Variabel Tekt
 String from_name = bot.messages[i].from_name;
 if (from_name == "") from_name = "Guest";

 if (text == "/ping") { //Jika
mendapat Pesan PING
 Serial.println(chat_id);
//Tampilkan Chat ID di Serial monitor
 bot.sendMessage(chat_id, "Sistem Siap", ""); //Kirim
ke telegram (sistem Siap)
 }


 }
}
//==============Inisialisasi Pin dan Pembacaan Awal============
void setup() {
 Serial.begin(115200);
 pinMode(pin_SW, INPUT_PULLUP);
 pinMode(pin_buzer, OUTPUT); // initialize digital ledPin as an
output.
 // Set WiFi to station mode and disconnect from an AP if it
was Previously
 // connected
 WiFi.mode(WIFI_STA); //Wifi mode Client
 WiFi.disconnect(); //DIsconnect
 delay(100);
 // attempt to connect to Wifi network:
 Serial.print("Connecting Wifi: ");
 Serial.println(ssid);
 WiFi.begin(ssid, password);
 while (WiFi.status() != WL_CONNECTED) { //Jika kondisi wifi
tidak konek masuk ke loop
 Serial.print(".");
 delay(500);
 }
 Serial.println("");
 Serial.println("WiFi connected"); //Tampilkan
status wifi
 Serial.print("IP address: ");
 Serial.println(WiFi.localIP());

}
void loop() {
 buzerOff; //matikan buzer
 String chat_id; //buat variabel
STRING chat_ID
 chat_id = 483071318; // masukan id (liat pada serial
monitor)
 a = digitalRead(pin_SW); //Meembaca pin
Digital
 
11
 if ((a==0)&&(pintu==true)){
 buzerOff;
 Serial.println("0");
 bot.sendMessage(chat_id, "Pintu Tertutup!!!", "");
//Kirim ke telegram
 pintu = false;
 }
 if (a==1){ //Jika Sensor
HIGH/Pintu Terbuka
 buzerOn; //Buzer On
 Serial.println("1");
 bot.sendMessage(chat_id, "Pintu Terbuka!!!", "");
//Kirim ke telegram
 pintu = true;
 }
 if (millis() > Bot_lasttime + Bot_mtbs) {
//fungsi milisecond
 int numNewMessages =
bot.getUpdates(bot.last_message_received + 1); //Variabel
NewMesaage
 while (numNewMessages) {
//Jika mendapat pesan masuk Loop
 Serial.println("got response");
 handleNewMessages(numNewMessages);
 numNewMessages = bot.getUpdates(bot.last_message_received
+ 1); //Cek Pesan masuk
 }

 Bot_lasttime = millis();
//Bot=Millis
 }
