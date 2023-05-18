# Development of Smart Trolley for Retail Project 

The Smart Trolley is a shopping cart that can be used by a supermarket customer without the intervention of any store employees. The processes of adding the items to the trolley and making the payment is automated. The smart trolley is shown in figure 1.

<br>
 <p align="center"> 
 <img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/7ca5dd26-c133-48dc-8858-35449b1499f4" width="500"> </p>
 <br>

When customers add items to the trolley, they must scan its barcode using the barcode scanner mounted near the handles of the trolley. If they want to remove any items they can press the remove button, scan the barcode of the product and remove it from the trolley. Once all products have been selected customers can press the end button and receive the total amount and instructions to make the payment.

Four load cells were connected to the four wheels of the trolley. These load cells recorded the weight of items added. This was done in order to prevent the theft of items. The weight of the load cells was compared against the listed weight of items. If any items are added to the cart without scanning its barcode the two weights would not be equal and the system would notify supermarket employees to inspect the trolley.
<br>

Figures 2 and 3 shown below detail the work flow and the circuit design of the Smart Trolley system respectively.

<br> 
 <p align="center">
  <img src="https://github.com/DerickFernando51/Smart-Trolley/assets/124335793/2a729c82-9386-49f4-b26a-f22e9185ffe8" width="750"></p>
   <p align="center"> Figure 2: Flow chart of Smart Trolley System   </p> 
   <br><br><br><br><br>
   
 <p align="center"> 
   <img src="https://github.com/DerickFernando51/Smart-Trolley/assets/124335793/e72bb0f4-34d3-493b-af6e-af2f7479b5d7" width="550"></p>
   
  <p align="center">   Figure 3: Smart Trolley System circuit design  </p> 
  
  
  
  <br>
   <p align="center"> 
   <img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/24e1088f-3a92-4e3a-8c30-2722191493d9 " width="850"></p>  
 <br>
 

Figure 4 and 5 show the enclosure box of the Smart Trolley System prototype.
<br>

I was tasked with testing the existing features, adding new functionality to the system and developing the smart trolley mobile application.
<br><br>



## 1.0 Adding remove button 

The position and circuit diagram of the remove button is shown in figures 6 and 7
<br>
 <p align="center"> 
  <img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/01a4b15f-7352-479a-9b9a-26c3d6ddabde" width="800"></p>
  <br> 
   

•	When connecting the remove push button to the Smart Trolley system a pulldown resistor was used as shown. This is done to ensure that the wire is pulled to a logic level at the absence of an input and prevents the floating state of a pin.

•	The pulldown resistor was connected between the ground terminal of the push button and the and the ground pin of the microcontroller. The other terminal of the push button was connected to GPIO pin 27 of the ESP 32 
<br><br>
     
    int remobuttonState=0;// Initial state of remove button
    #define REMO_BUTTON_PIN 27 //Remove button is connected to pin 27 of ESP32

    void setup() {
       attachInterrupt(digitalPinToInterrupt(REMO_BUTTON_PIN), ISR1,CHANGE);
         //External interrupt triggered when remove button is pressed
       pinMode(REMO_BUTTON_PIN, INPUT_PULLUP)// Set remove button to input mode
       Serial.begin(115200);//Set Arduino serial monitor baud rate
       Serial2.begin(9600); // Set barcode serial monitor baud rate
      }

    void loop() {
      Serial.println("Press remove button to remove items");
      delay(5000);  
      if(remobuttonState==1){
        Remove_Item();
      }
    }

    void Remove_Item(){//This function executes if remove button state is equal to 1
      Serial.println("Scan the item to remove");
      while(Serial2.available(==0)){}//Wait for barcode to be scanned
      Code = Serial2.readString();//Assign scanned barcode to variable
      Serial.println(Code); //Display barcode in serial monitor
      remobuttonState=0;  //Reset remove button state
    }

    void ISR1(){//This function executes when the remove button is pressed
     remobuttonState=1;
    }

The code shown above can be explained as follows:
<br><br>
•	The remove button press is detected using an Arduino hardware interrupt. All the GPIO pins in the ESP32 microcontroller can be used as interrupts. Since the remove button is connected to pin 27 the interrupt is set to this pin.

•	The ISR (interrupt service routine) is the function that is called when the interrupt is triggered. Inside the ISR the state of the remove button is set to 1. The Remove_Item() function is executed if the remove button state is equal to 1.

•	Inside the remove button function the user is prompted to scan the barcode of the item to be removed. Then the scanned barcode is displayed in the Arduino serial monitor. Finally, the remove button state is reset to 0.
<br><br>

I soldered the push button connections and constructed the circuit. Then, I tested the remove button code and integrated it to the smart trolley system final code after confirming its accuracy.
<br><br>

## 2.0 Adding end button

The position and circuit diagram of the end button is shown in figures 8 and 9
<p align = "center">
<img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/f4ef7a14-2ed7-4147-b90d-9d450be8d865" width ="800"></P>
 
 


    int endbuttonState=0;// Initial state of end button
    #define END_BUTTON_PIN 5 //End button is connected to pin 27 of ESP32
    String total; //Variable to track total amount

    void setup() {
      attachInterrupt(digitalPinToInterrupt(END_BUTTON_PIN), ISR2,CHANGE);
          //External interrupt triggered when end button is pressed
      pinMode(END_BUTTON_PIN, INPUT_PULLUP)// Set end button to input mode
      Serial.begin(115200);//Set Arduino serial monitor baud rate
      Serial2.begin(9600); // Set barcode serial monitor baud rate
    }

    void loop() {
      Serial.println("Press end button if you are finished purchasing");
      delay(5000);  
      if(endbuttonState==1){
        End_purchasing();
      }
    }

    void  End_purchasing(){//This function executes if end button state is equal to 1
     remobuttonState=1;
     Serial.println("Total amount: Rs. ");
     Serial.println(total);
    }

    void ISR2(){//This function executes when the end button is pressed
      endbuttonState = 1;
    }


 
 

The code shown above can be explained as follows:

  •	The end button press is detected using an Arduino hardware interrupt. All the GPIO pins in the ESP32 microcontroller can be used as interrupts. Since the remove button     is connected to pin 5 the interrupt is set to this pin.

  •	The ISR (interrupt service routine) is the function that is called when the interrupt is triggered. Inside the ISR the state of the remove button is set to 1. The           End_purchasing() function is executed if the remove button state is equal to 1.

  •	Inside the end button function the total amount is displayed in the Arduino serial monitor.  


I soldered the end push button connections and constructed the circuit. Then, I tested the end button code and integrated it to the smart trolley system final code after confirming that it functions as expected.
<br>

## 3.0 Testing load cells

Four 100kg CZL601 load cells shown in figure 10 were used for the purpose of preventing theft in this system. I was instructed to comprehensively test these load cells and determine whether the project could be progressed using this weight sensor and if not to propose a different sensor/method for this process.
<p align = "center">
<img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/4c71703a-cd18-44d1-9322-ad50e0e73023" width ="375"></P>
<br><br>
  
<p align = "center">
<img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/dd258d82-69da-41d4-91ac-520576e98c94" width ="550"></P>
<br>
 
  
<p align = "center">
<img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/1fbbfe1e-f755-4844-be84-86bf6bdaa16e" width ="800"></P>
<br>
 

Figure 11 shows the parts of the setup required to test load cells and figure 12 shows the setup created in the lab for this purpose.

<br>
The calibration curve was plotted to verify the accuracy of the load cells. Known weights in the range of 100g – 38 kg were used to obtain the required data points. Firstly, the actual weight was measured using a scale and then the weight measured by the load cells was obtained. The data points and the calibration curve are shown in table 1 and graph 1 respectively. The calibration factor is obtained by dividing the measured weight by the applied weight.

<br><br>
<p align = "center">
<img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/003f8751-0200-456c-8f42-92c0aa30cb69" width ="700"></P>
<br>
 

<p align = "center">
<img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/6ef4978c-fc4d-4926-a971-246aa729bc26" width ="700"></P>
<br>
 
<p align = "center">
<img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/741ca23e-baab-4b08-813c-6679173b0898" width ="600"></P>
<br>

Graph 2 shows the calibration curve given in CZL601 load cell datasheet. These load cells had an error of ±0.023% of full scale. The tests performed confirmed that the error in the weight measurement of products was within this range.
<br><br>

## 4.0 RFID proposal

The smart trolley system prevents theft by calculating the total weight of products and comparing it with the listed weight. I proposed using RFID tags instead of load cells for this purpose.

For this proposal to be practically implemented RFID tags would need to be pasted on all the supermarket products. Then, the barcode scanner is replaced by a RFID scanner. The system architecture of the RFID proposal is detailed in figure 13.

<br><br>
<p align = "center">
<img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/c139e495-c1b1-42eb-90fd-ad42d2e88524" width ="650"></P>
<br>

 
**Drawbacks of using load cells:**

•	Accuracy is not enough for light weight products. CZL601 load cell belong to accuracy class C3 (error of ±0.023% of full scale)

•	Products with similar weight, but different prices can be stolen without being detected by the system
<br><br>

**Advantages of using RFID tags:**

•	RFID scanner is a faster scanner than a barcode scanner

•	Can scan multiple items at once

•	Does not require direct line of sight. Uses NFC (Near Field Technology)

•	Will reduce the changes need to be made to the product as load cells will not need to be fixed to the four legs.
<br><br>

**Disadvantage of using RFID tags:**

•	RFID tags need to be pasted on all products
<br><br>

This proposal was rejected due to the above disadvantage listed. A decision was made to use a camera and identify products using computer vision and machine learning principles.
<br><br>

## 5.0 Sending barcodes to Firebase database

The barcodes scanned by the Smart Trolley System were stored in a Firebase real time database so that they can be accessed by the mobile application. The following should be defined in order for the MCU to gain access to the Firebase database:

• The API key

• URL of the real time database

• Wi-fi network credentials

• Email and password of an authorized user
<br><br><br>
The Arduino code for sending scanned barcodes to the Firebase database is given below. The initial part of the code involves defining the above mentioned parameters, assignning the parameters, connecting to Wi-Fi and setting the baud rate of the serial monitor. <br>
<br>

    #include <WiFi.h>
    #include <Firebase_ESP_Client.h>
    #include <Wire.h> 
	
	// Insert your network credentials
	#define WIFI_SSID "  "
	#define WIFI_PASSWORD "  "

	// Insert Firebase project API Key
	#define API_KEY " "

	// Insert Authorized Email and Corresponding Password
	#define USER_EMAIL " "
	#define USER_PASSWORD " "

	// Insert RTDB URLefine the RTDB URL
	#define DATABASE_URL " "

	// Define Firebase objects
	FirebaseData fbdo;
	FirebaseAuth auth;
	FirebaseConfig config;

 	// Database main path (to be updated in setup with the user UID)
	String databasePath;
	String parentPath;
	
	// Parent Node (to be updated in every loop)
	FirebaseJson json;
	
	// Firebase index variable
	int i=0;
 
	// Initialize WiFi
	void initWiFi() {
  	WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  	Serial.print("Connecting to WiFi ..");
  	while (WiFi.status() != WL_CONNECTED) {
    	Serial.print('.');
    	delay(1000);
  	}
  	Serial.println(WiFi.localIP());
  	Serial.println();
	}

 	void setup(){
  	  Serial.begin(115200);
	  
	  // Assign the api key (required)
  	  config.api_key = API_KEY;

  	  // Assign the user sign in credentials
  	  auth.user.email = USER_EMAIL;
  	  auth.user.password = USER_PASSWORD;

  	  // Assign the RTDB URL (required)
  	  config.database_url = DATABASE_URL; 
	 }

	void loop(){
  		while (Serial2.available() > 0) { //Wait for barcode to be scanned
    		Code = Serial2.readString(); //Assign scanned barcode to variable
    		Serial.println(Code); //Print barcode in serial monitor

			databasePath = "/Smart_Cart_Wishlist/"; //Name of firbase database path
    		parentPath= databasePath + String(i);  //Index of database path
    		i=i+1; //Increment index
    		json.set(itemPath.c_str(), String(Code)); //Send barcode to selected index
    		Serial.printf("Set json... %s\n", Firebase.RTDB.setJSON(&fbdo, parentPath.c_str(), &json) ? "ok" : fbdo.errorReason().c_str());
        			//Print confirmation of sending data to firebase, or error message if unsuccessful
  		}  
	}
	
<br><br>
The main loop of the above code can be explained as follows:

• The index variable (i) was used to specify the child node that the barcode should be saved to. 

• After an item was scanned the index variable was incremented.

• The json.set function saves the barcode in the specified child node. 
<br><br>
Figure 14 shows how the barcode data was saved in the Firebase database.

<br>
<p align = "center">
<img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/46760c12-46ca-4e9d-8c9a-b829f82b32ca" width ="600"></P>
<br><br><br>


 


## 6.0 Smart trolley mobile app

I created the Smart Trolley mobile application. This was done in Android studio IDE (Integrated development environment) using Kotlin programming language. Figure 15 details the architecture of the Smart Trolley system.

<br>
<p align = "center">
<img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/6e45a99d-deef-4770-bc64-c13b9841dbd6" width ="600"></P>
<br><br>
  
 

XML (Extensible Markup Language) was used to define the UI layout of this application. The layout of the main activity page is shown in figure 16. The android studio application was linked to the Firebase database and showed the stored data in real time using recycler view.

<br>
<p align = "center">
<img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/cb360607-adaa-440d-aca0-94df10926b67" width ="450"></P>
<br><br><br>

  
The QR code shown in figure 17 was generated and the mobile application could be opened by scanning this code. The intention was to paste this on the Smart Trolley allowing customers to easily open the app by scanning the code. Alterations were made to the manifest file of the Smart Trolley application to allow the app to be opened by scanning the QR code.

<br>
<p align = "center">
<img src="https://github.com/DerickFernando51/Smart-Trolley-System/assets/124335793/7e9233f7-1185-47a2-942d-509de00ec612" width ="475"></P>
<br><br><br>

 




