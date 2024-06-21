//----------------BI ISM ALLAH AR-RAHMAN AR-RAHIM---------------------------------
#include <LiquidCrystal.h> //déclaration de la bibliotheque LCD
//On nomme les pins qui donneront des instructions à l'écran
const int rs = 2, en = 3, d4 = 4,d5 = 5, d6 = 6,d7 = 7;
LiquidCrystal lcd = LiquidCrystal(rs, en, d4, d5, d6, d7);
//On nomme l'écran et on dit à l'écran quels pins lui donneront des instructions 
int Ap=A1 ,As=A0, Buzzer=11, LED_PERF = 9,LED_SER2=10,RAZc_Button=12,Buzzer_Button = 8;
int SERUM,counter,state,x,x1,button_state=0,volumeValue = 0,tempsValue = 0;// Variables pour stocker les valeurs lues
const int Monitoring_SELECT = 13, Troubleshoot_SELECT = A3, Infusion_Rate_SELECT = A4, SAISIE_DUREE = A5, SAISIE_VOLUME= A2; 
bool Conf_button = false; //bouton de confirmation de saisie des valeurs des parametres de perfusion
bool bouton1Etat = false,bouton2Etat = false,bouton3Etat = false;// Variables pour stocker l'état des boutons (selection MENU)

////             ---- VOID SETUP -----      ///////////
void setup(){
pinMode(Ap, INPUT); // IR Sensor pin INPUT
pinMode(As, INPUT); // XKC Y25 NPN Sensor pin INPUT
pinMode(LED_SER2, OUTPUT); // LED_SER2 Pin Output SERUM INDICATOR VIDE La rouge
pinMode(LED_PERF,OUTPUT); // LED_PERF Pin Output PERFUSEUR INDICATOR
pinMode(Buzzer, OUTPUT); // Buzzer Pin Output
pinMode(Buzzer_Button, INPUT_PULLUP); //bouton poussoir pour activer ou désactiver le son de détection de chaque gouttes
pinMode(RAZc_Button, INPUT_PULLUP);//bouton RAZ comptage de gouttes 
pinMode(Monitoring_SELECT, INPUT_PULLUP);//bouton selection de menu (programme 1 Monitoring)
pinMode(Troubleshoot_SELECT, INPUT_PULLUP);//bouton selection de menu (programme 2 Troubleshooting)
pinMode(Infusion_Rate_SELECT, INPUT_PULLUP);//bouton selection de menu (programme 3 Calculation Fusion Rate)
pinMode(SAISIE_VOLUME, INPUT); // Configuration des broches des potentiomètres en entrée
pinMode(SAISIE_DUREE, INPUT); // Configuration des broches des potentiomètres en entrée
 
// WELCOMING SCREEN //
lcd.begin(16, 2);
lcd.clear();lcd.setCursor(0,0);lcd.print("CHECKING DATA    "); //delay(20);
lcd.clear();lcd.setCursor(0,0);lcd.print("Please wait...      "); //delay(50);
lcd.setCursor(4, 0);lcd.print("    Read the       ");
lcd.setCursor(3, 1);lcd.print(" M E N U           ");//delay(200);
lcd.clear();lcd.setCursor(0, 0);lcd.print("Press Button1   ");
lcd.setCursor(0, 1);lcd.print("for: Monitoring    ");delay(200);
lcd.clear();lcd.setCursor(0, 0);lcd.print("Press Button2 to");
lcd.setCursor(0, 1);lcd.print("Check Parameters    ");delay(200);
lcd.clear();lcd.setCursor(0, 0);lcd.print("Press Button3 to");
lcd.setCursor(0, 1);lcd.print("Calculate   ");delay(200);
lcd.setCursor(0, 1);lcd.print("Infusion Rate");}

/////////////                  BOUCLE SANS LIMITE            /////////

void loop(){ 

counter= digitalRead(Ap); SERUM=digitalRead(As);x1=0;//perfusion comptage


//                         ----- ---- - - ---             MENU SELECTION      ---  -  - ---- -----                         //

 bouton1Etat  = digitalRead(Monitoring_SELECT); //note: ici j'ai utilisé les entrées analogiques entant que entrées numérique
 bouton2Etat  = digitalRead(Troubleshoot_SELECT);// avec la fonction digitalRead
 bouton3Etat  = digitalRead(Infusion_Rate_SELECT); // Read the state of the new button

  
 //------------ Lire l'état des boutons pour selectionner un programme ---------// 

  if (bouton1Etat == LOW) {// Vérifier si le bouton 1 est appuyé
    program1(); } // Exécuter le programme 1
 
  
 if (bouton2Etat == LOW) {// Vérifier si le bouton 1 est appuyé
    program2(); }// Exécuter le programme 1
   
  
  if (bouton3Etat == LOW) {// Vérifier si le bouton 1 est appuyé
    program3();  }// Exécuter le programme 1
 
  } 
  
    //////////////////               P R O G R A M 1              ///////////


  void program1() {  //  ---------  On commence avec programme 1 ----------
  
////////////////           XKC Y25 NPN OUTPUT        //////////
if (SERUM< 1 ){ 
digitalWrite(LED_SER2, HIGH);tone(Buzzer, 2000);
lcd.setCursor(0,0);lcd.print("SERUM:EMPTY            ");
} if (SERUM> 0 ){ 
digitalWrite(LED_SER2,LOW);noTone(Buzzer);
lcd.setCursor(0,0);lcd.print("SERUM:FULL             ");
}
/////////////////// ///       IR OUTPUT             ////// ////////////////////////////////////////
int counter = digitalRead(Ap); // lecture de la valeur du signal
if (counter == HIGH) { digitalWrite(LED_PERF, HIGH); tone(Buzzer,1175); }
else { digitalWrite(LED_PERF, LOW);noTone(Buzzer); }
//Programme boutton Activation /Desactivation Buzzer (personne veut entendre des gouttes injectée dans ses veines.. ^^);
 int buzzerButtonState = digitalRead(Buzzer_Button);
  if (buzzerButtonState == LOW) { // On vérifie si le bouton est appuyé
  if (counter == HIGH) { tone(Buzzer, 1175); // On émet un son si une goutte a été détectée 
  } else {noTone(Buzzer); } // Arréter le son si aucune goutte est détectée 
  } else { noTone(Buzzer); } // Arréter le buzzer si le boutton n'est pas appuyé
// COMPTAGE DE GOUTTES 
if (state == 0){
switch (counter) {
case 1 : state = 1; x = x + 1; break; 
case 0 : state = 0; break;}}
if (counter == LOW) {
state = 0;}
lcd.setCursor(0, 1); lcd.print(x);lcd.print("G           ");
lcd.setCursor(14, 0); lcd.print(counter);lcd.print("G             ");
// Comptage de volume injecté en ml en fonction de gouttes 1ml ou 1 cc = 20gouttes et aussi 1 goutte représente 0.05 ml de volume
if (x%20==0){ //si x est un multiple de 20
x1=x/20; //calculer le volume en ml 
lcd.setCursor(8, 1); lcd.print("                  ");lcd.print(x1);lcd.print("                    ");
lcd.setCursor(8, 1); lcd.print("V=");lcd.print(x1);lcd.print("ml");}
// Config du bouton de remise à zero du compteur de gouttes reçue par le sensor
button_state = digitalRead(RAZc_Button); // on lit l'état du bouton
if (button_state == LOW) {//Si le bouton est pressé l'état du bouton est LOW (en supposant que le bouton est connecté à la masse)
x = 0;// Réinitialisation du compteur
delay(50); }

}

  //////////////////               P R O G R A M 2             ///////////

  
  void program2() {

  if (SERUM< 1 && counter>0 ){ 
  digitalWrite(LED_SER2, HIGH); //serum vide onallume la led rouge
  digitalWrite(LED_PERF, HIGH); //une goutte détectée on allume la Led
  lcd.setCursor(0, 0); lcd.print("1/Check Solute   ");  lcd.setCursor(0, 1);lcd.print("Sensor Position     ");}
 
  if (SERUM>0  && counter< 1){  
  digitalWrite(LED_SER2,LOW);//Seum plein on allume la led verte
  lcd.setCursor(0, 0); lcd.print("1/ Check The               ");  lcd.setCursor(0, 1);lcd.print("  Roller Clamp            "); delay(30);
  lcd.setCursor(0, 0); lcd.print("2/ Check the              ");  lcd.setCursor(0, 1);lcd.print("  DROP Sensor               ");}delay(30);
  

  if (SERUM<1  && counter< 1){ 
  digitalWrite(LED_SER2, HIGH);//serum vide on allume la led rouge
  lcd.setCursor(0, 0); lcd.print("1/If Solute is      ");  lcd.setCursor(0, 1);lcd.print("  Not Empty      ");  delay(30);
  lcd.setCursor(0, 0); lcd.print("2/Check All            ");  lcd.setCursor(0, 1);lcd.print("  Sensors         ");}  delay(30);

  
  if (SERUM >0  && counter>0){ 
  digitalWrite(LED_PERF, HIGH); //une goutte détectée on allume la Led
  digitalWrite(LED_SER2,LOW);//Seum plein on allume la led verte
  lcd.setCursor(0, 0); lcd.print("No Issues Were  ");  lcd.setCursor(0, 1);lcd.print("   Detected!   ");}}
  
  ///////////////////                   ///////////////////
   

const int GOUTTES_PAR_CC = 20;// car on sait que 1ml equivaut 20gouttes 

// Fonction pour calculer le débit de perfusion en gouttes par minute
float calculerDebitPerfusion(int volumeEnCc, float tempsEnHeures) {
int   volumeEnGouttes = volumeEnCc * GOUTTES_PAR_CC;
float tempsEnMinutes = tempsEnHeures * 60.0;
float debitPerfusion = (float)volumeEnGouttes / tempsEnMinutes;
return debitPerfusion;
}

  //////////////////               P R O G R A M 3              ///////////
  
 void program3() {
 
 
// Lecture des valeurs des potentiomètres
  volumeValue = analogRead(SAISIE_VOLUME);
  tempsValue  =  analogRead(SAISIE_DUREE);   
  
// Conversion des valeurs pour obtenir un volume en cc et un temps en heures
  int volumeFinal = map(volumeValue, 0, 1023, 0, 2000); // 2 flacons de 1000 cc chacun
  float tempsFinal = map(tempsValue, 0, 1023, 0, 24); // Temps mappé de 0 à 24 heures
  
//Affichage des valeurs saisie des deux paramétres Volume et durée
  lcd.setCursor(0, 0);lcd.print("Volume:");lcd.print(volumeFinal);lcd.print("CC/mL   ");
  lcd.setCursor(0, 1);lcd.print("Duree:");lcd.print(tempsFinal);lcd.print("Heure     ");

  Conf_button = digitalRead(Buzzer_Button) == LOW; // Le bouton est actif à l'état bas  // Lecture de l'état du bouton

  if (Conf_button) {  // Si le bouton est pressé, calculer et afficher le débit de perfusion
    float debitPerfusion = calculerDebitPerfusion(volumeFinal, tempsFinal);// Calcul du débit de perfusion en gouttes/min
    int gouttesArrondies = round(debitPerfusion); // pour arrondir au nombre le plus proche on peu pas travailler avec 0.5 gouttes on prend l'arrondis
    
    lcd.setCursor(0, 0);lcd.print("Infusion Rate is: ");lcd.setCursor(0, 1);lcd.print(gouttesArrondies);lcd.print(" gouttes/min    ");
   
    while(digitalRead(Buzzer_Button) == LOW); // Attendre que le bouton soit relâché avant de continuer
  }

}
