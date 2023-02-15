ArduinoMyLog2
=============

## Project ArduinoMyLog2

This project will store into Blockchain, temperature and humidity data, provided by device Arduino. This project is based on a Smart Contract that use the open framework AntelopeIO and Javascript library EOSJS.


## Quick links and licensing references

- [AntelopeIO is an open framework for building next-generation web3 products and services](https://github.com/AntelopeIO)
- [Proton Blockchain is a fast scalable, layer one blockchain](https://protonscan.io)
- [EOSJS Javascript library](https://developers.eos.io/manuals/eosjs/latest/index)
- [Arduino library to control Grove Temper Humidity TH02](https://github.com/Seeed-Studio/Grove_Temper_Humidity_TH02)
- [NodeJS](https://nodejs.org)


## Tested for

* ARDUINO UNO R3 BOARD with ARDUINO ETHERNET 2 SHIELD (https://store.arduino.cc) (Note: any devices that provides data in json format could be included in this project)
* Grove - Temperature&Humidity Sensor (High-Accuracy & Mini) multifunctional sensor that gives you temperature and relative humidity 
(https://www.seeedstudio.com/Grove-Temperature-Humidity-Sensor-High-Accuracy-Mini.html)


## Notice
This software is experimental and a work in progress. Data inserted into blockchain will be accessible to anyone.
Under no circumstances should these files be used in relation to any critical system(s).
Use of these files is at your own risk.
THE SOFTWARE IS PROVIDED "AS IS". See license file LICENSE.md for details.

## Quick start

* We assume that we are using Proton Testnet and that arduinomylog.ino file has been loaded correctly on the Arduino and that by calling the Arduino IP into a browser, we obtain a json like this:

{"WeatherStation":[{"location":"Trento - Italy","temperature":"26.25","humidity":"63.75"}]}

* Now we have to choose a blockchain, on which storing the temperature and humidity data retrieved from the sensor based on the JSON generated by Arduino. For this example we'll use the TESTNET version of PROTON BLOCKCHAIN ( https://testnet.protonscan.io) ; if you are not familiar with how an blockchain works, I recommend reading more information here:https://www.proton.org and here https://docs.eosnetwork.com
then you can go to https://docs.protonchain.com/cli/usage.html#proton-account-account , and create your account. For this example account "arduinomylog" is used on PROTON TESTNET.

* Install Leap ( https://github.com/AntelopeIO/leap ), import your Key into wallet, then install Contract Development Toolkit ( https://github.com/AntelopeIO/cdt ) then verify Cleos and CDT version:

cleos version client

OUTPUT:

v3.2.1

cdt-cpp --version

OUTPUT:

cdt-cpp version 3.1.0

* Enter directory arduinomylog, download arduinomylog's files .cpp and .hpp into arduinomylog directory, and create the smart contract that will store temperature and humidity data provided by Arduino:

cd ~/cdt/build

cdt-cpp -abigen ~/cdt/arduinomylog/arduinomylog.cpp -o ~/cdt/arduinomylog/arduinomylog.wasm -I ~/cdt/build/include/

(Todo: add Ricardian data to Smart Contract)

* Before deploing the Smart Contract on Proton TestNet, be sure to have RAM on your account, then deploy the Smart Contract:

cleos -u https://[API_ENDPOINT] set contract [CONTRACT_ACCOUNT] [CONTRACT_NAME_FOLDER_PATH] -p [ACTIVE_KEY]

Note: API_ENDPOINT's servers could be find on https://proton-testnet.antelope.tools/endpoints
In this case:

cd ~/cdt

cleos -u https://[API_ENDPOINT] set contract arduinomylog /PATH/TO/arduinomylog -p arduinomylog@active

* Insert your account into Whitelist table; in this case:

cleos -u https://[API_ENDPOINT] push transaction '{"delay_sec": 0,"max_cpu_usage_ms": 0,"actions": [{"account":"arduinomylog","name": "login","data": {"username":"arduinomylog"},"authorization": [{"actor": "arduinomylog","permission":"active"}]}]}'

* check that NodeJS is installed:

node -v

v14.17.4

npm -v

7.20.6

* check tha EOSJS is installed (for this example we use v22.1.0 Release)

* insert your account's Private key, an API Endoint and your Arduino's IP, into file arduinomylog.js ; then change it's permissions:

chmod 700 arduinomylog.js

* intialize Nodejs and install software dependencies in order to obtain this package.json:

cat package.json

{
  "name": "arduinomylog",
  "version": "1.0.0",
  "description": "Temperature Humidity Logger",
  "main": "arduinomylog.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "eosjs": "^22.1.0",
    "node-fetch": "^2.6.1",
    "util": "^0.12.4"
  }
}


## Usage

node arduinomylog.js


## Monitor

See your data on blockchain (they are accessible to anyone) by visiting a blockchain explorer; in this case I'll use:

https://testnet.protonscan.io/account/arduinomylog?loadContract=true&tab=Tables&account=arduinomylog&scope=arduinomylog&limit=100&table=logs


## Optional

Modify file update.sh and add parameters to crontab to insert the temperature and humidity data into blockchain, every fifteen minutes:

0,15,30,45 * * * * /PATH/TO/update.sh
