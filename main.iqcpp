#pragma region VEXcode Generated Robot Configuration
// Make sure all required headers are included.
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <math.h>
#include <string.h>


#include "vex.h"

using namespace vex;

// Brain should be defined by default
brain Brain;


// START IQ MACROS
#define waitUntil(condition)                                                   \
  do {                                                                         \
    wait(5, msec);                                                             \
  } while (!(condition))

#define repeat(iterations)                                                     \
  for (int iterator = 0; iterator < iterations; iterator++)
// END IQ MACROS


// Robot configuration code.
inertial BrainInertial = inertial();


// generating and setting random seed
void initializeRandomSeed(){
  wait(100,msec);
  double xAxis = BrainInertial.acceleration(xaxis) * 1000;
  double yAxis = BrainInertial.acceleration(yaxis) * 1000;
  double zAxis = BrainInertial.acceleration(zaxis) * 1000;
  // Combine these values into a single integer
  int seed = int(
    xAxis + yAxis + zAxis
  );
  // Set the seed
  srand(seed); 
}



void vexcodeInit() {

  // Initializing random seed.
  initializeRandomSeed(); 
}

#pragma endregion VEXcode Generated Robot Configuration

//----------------------------------------------------------------------------
//                                                                            
//    Module:       main.cpp                                                                                                   
//    Description:  IQ project                                               
//                                                                            
//----------------------------------------------------------------------------

// Include the IQ Library
#include "iq_cpp.h"

// Allows for easier use of the VEX Library
using namespace vex;


//Function Prototypes
void bettingSystem(int *playerInfo, int &betToMatch, int numOfPlayers);
void trashPile();
int countPlayers();
void configureAllSensors();
void rotateToAngle(double angle);
void dispenseCard(int numOfCards);
void dealInitialPlayerCards(int numPlayers);

//Main Game code
int main() {
  // Initializing Robot Configuration. DO NOT REMOVE!
  vexcodeInit();

  /*Initializing functions*/
  configureAllSensors();
  Brain.Screen.print("Program Started");

  /*Variables*/
  int BetToMatch = 0;
  int numberOfPlayers = countPlayers();
  int playerInfo[numberOfPlayers];

  for(int i = 0; i<numberOfPlayers;i++){
    playerInfo[i] = 0;
  }

  /*Running the game*/
  //numberOfPlayers = countPlayers();
  trashPile();
  dealInitialPlayerCards(numberOfPlayers);
  bettingSystem(playerInfo, BetToMatch, numberOfPlayers);
  
  rotateToAngle(0.0);
  dispenseCard(3);
  trashPile();

  bettingSystem(playerInfo, BetToMatch, numberOfPlayers);
  Brain.Screen.clearScreen();
  Brain.Screen.setCursor(1,1);

  rotateToAngle(0.0);
  dispenseCard(1);
  trashPile();

  bettingSystem(playerInfo, BetToMatch, numberOfPlayers);
  Brain.Screen.clearScreen();
  Brain.Screen.setCursor(1,1);

  rotateToAngle(0.0);
  dispenseCard(1);

  bettingSystem(playerInfo, BetToMatch, numberOfPlayers);
  Brain.Screen.clearScreen();
  Brain.Screen.setCursor(1,1);


  MotorEject.stop(brake);
  FlyWheelL.stop(brake);
  FlyWheelR.stop(brake);

  while(!TouchLED4.pressing());
  
  Brain.programStop();
}
//Configure all sensors to default values
void configureAllSensors(){
  BrainInertial.calibrate();
  wait(2,seconds);
  BrainInertial.setHeading(0,degrees);
  BrainInertial.setRotation(0,degrees);
  MotorEject.setPosition(0,turns);
  FlyWheelL.setPosition(0,turns);
  FlyWheelR.setPosition(0,turns);
  RotationMotor.setPosition(0,turns);
  Brain.Screen.clearScreen();
  Brain.Screen.setFont(mono15);
  Optical3.setLight(ledState::on);
}

//Rotates to specified angle within (-180, 180)
void rotateToAngle(double angle){

  RotationMotor.setVelocity(10, percent);

  //If angle is greater than the current position, spin reverse until the angle is reached
  if(RotationMotor.position(degrees)>angle){
    RotationMotor.spin(reverse);
    while(RotationMotor.position(degrees)>angle){}

  }
  //If angle is less than the current position, spin forwards until the angle is reached
  else if(RotationMotor.position(degrees)<angle){
    RotationMotor.spin(forward);
    while(RotationMotor.position(degrees)<angle){}
  }
  RotationMotor.stop(brake);
}

//dispenses a number of cards
void dispenseCard(int numOfCards){

  //Set motor velocities
  MotorEject.setVelocity(20, percent);
  FlyWheelL.setVelocity(100, percent);
  FlyWheelR.setVelocity(100, percent);

  //Start fly wheels 
  FlyWheelL.spin(forward);
  FlyWheelR.spin(forward);

  //Runs for loop once for each card
  for(int i = 0; i<numOfCards; i++){

    //Start the feeder motor
    MotorEject.spin(forward);

    //Waits until the card is seen and dispensed
    while(DistanceSensor.objectDistance(mm)>80){}
    while(DistanceSensor.objectDistance(mm)<80){}

    MotorEject.stop();
  }

  //Runs the feeder motor backwards to reset a possible overhanging card
  MotorEject.spin(reverse);
  wait(0.25, seconds);

  //Stops all motors
  MotorEject.stop(brake);
  FlyWheelL.stop(brake);
  FlyWheelR.stop(brake);
}

//Initial Card Dealing
void dealInitialPlayerCards(int numPlayers){
  FlyWheelL.spin(forward,100,percent);
  FlyWheelR.spin(forward,100,percent);

  for(int cardsSent = 0; cardsSent < numPlayers; cardsSent++){

    double rotatePerPlayer = 270.0 / numPlayers;
    double targetAngle = (rotatePerPlayer * (cardsSent + 1))-135;

    rotateToAngle(targetAngle);
    dispenseCard(2);
    
  }

  MotorEject.stop();
  FlyWheelL.stop();
  FlyWheelR.stop();
}

//Asking Players for input
int askForInput(int playerNum, int numberOfPlayers)
{
  //Rotates to the angle of the player
  rotateToAngle((-135) + (270/numberOfPlayers)*playerNum);

  //Waits until something is placed near the object
  while(!Optical3.isNearObject()){}

  //Waits until a recognized color is detected and outputs the color code
  bool running = true;
  while(running){
    
    Brain.Screen.clearLine(1);
    Brain.Screen.print(convertColorToString(Optical3.color()));
    color output = Optical3.color();
    
    if(output == red){
      running = false;
      return 0;
    }else if(output == green){
      running = false;
      return 1;
    }else if(output == blue){
      running = false;
      
      return 2;
    }
  }
  return -1;
}

//RAISE IS RED and 0
//CHECK/MATCH IS BLUE and 2
//FOLD IS GREEN and 1
//Main Betting System
void bettingSystem(int *playerInfo, int &betToMatch, int numOfPlayers)
{
  bool running = true;

  while(running)
  {

    //Run for each player
    for(int i = 0; i<numOfPlayers ; i++)
    {
      //make sure the player has not folded yet
      if(playerInfo[i] != -1)
      {
        //If the player's bet matches the needed bet
        if(playerInfo[i] == betToMatch){
          switch(askForInput(i, numOfPlayers))
          {
            //If the person raises add 10 to pot
            case 0:
              betToMatch = betToMatch + 10;
              playerInfo[i] = betToMatch;
              break;
              
          }
        }
        //If the player is below the needed bet
        else
        {
          switch(askForInput(i, numOfPlayers))
          {
            //If they fold set their info to -1
            case 1:
              playerInfo[i] = -1;
              break;
            //Case if they match
            case 2:
              playerInfo[i] = betToMatch;
              break;
            //Case if they raise
            case 0:
              betToMatch = betToMatch + 10;
              playerInfo[i] = betToMatch;
              break;  
          }
        }   
      }
    }
    
    //Used to make sure that all players are ready for the next round
    int holder = 0;
    //Make sure that all players are good to continue
    for(int i = 0; i<numOfPlayers; i++)
    {
      //if player bet is not equal to the bet
      if(playerInfo[i]==betToMatch || playerInfo[i] == -1)
      {
        holder ++;
      }
    }
    if(holder == numOfPlayers){
      running = false;
    }

    for(int i =0; i<numOfPlayers; i++){
      Brain.Screen.print("player %d : %d", i+1, playerInfo[i]);
      Brain.Screen.newLine();
    }
  }
}

//Dispense random number of cards to trash pile
void trashPile(){
  srand(time(0));
  int randomNum = rand() % 5 + 1;

  double originalAngle = RotationMotor.position(degrees);
  RotationMotor.spin(forward,10,percent);
  while(RotationMotor.position(degrees)<180){}
  RotationMotor.stop(brake);

  dispenseCard(randomNum);

  RotationMotor.spin(reverse,10,percent);
  while(RotationMotor.position(degrees)>originalAngle){}
  RotationMotor.stop(brake);
  
}

//Inital player count
int countPlayers(){
  int playerCount = 0;
  while(!TouchLED4.pressing() && playerCount < 7) {

      if(Bumper12.pressing()) {
          playerCount++;  
          wait(200, msec); 
          while(Bumper12.pressing()) {
              wait(20, msec);
          }
      }
      wait(20, msec);
  }
  return playerCount;
}
