// ConsoleApplication3.cpp : Defines the entry point for the console application.
//

/*
NAME: SUNIV SHRESTHA 
STUDENT ID: 217454028
*/

#define _CRT_SECURE_NO_DEPRECATE
#include <stdio.h>
#include <conio.h>

//-------------------------------------------------------------------------------------
// DEFINING THE LIMITS AND RANGES

#define MAX_COLUMNS 20 // THE TOTAL NUMBER OF COLUMNS IN THE EXCEL FILE 
#define MAX_ROWS 20 // THE TOTAL NUMBER OF ROWS IN THE EXCEL FILE 
#define FILE_NAME "data_1.csv" // THE FILE TO BE OPENED AND EDITED  

//-------------------------------------------------------------------------------------
// VARAIBLES TO STORE MAPS 

float rawData[MAX_ROWS][MAX_COLUMNS]; // FOR STORING THE DATA RETRIEVED FROM EXCEL FILE 
float errorMap[MAX_ROWS][MAX_COLUMNS]; // FOR STORING ERROR MAP
int classificationMap[MAX_ROWS][MAX_COLUMNS]; // FOR STORING THE CLASSIFICATION MAP
int patternMap[MAX_ROWS][MAX_COLUMNS]; // FOR STORING THE PATTERN MAP

//-------------------------------------------------------------------------------------
// VARAIBLE TO STORE AVERAGE

float average[6]; // TO STORE AVERAGE 

//-------------------------------------------------------------------------------------
// GENERAL VARAIBLES
float rangeValue[6]; // TO STORE THE RANGE STARTING FROM 0.00-2.50
int displayValue[11]; // TO STORE THE VALUES FROM 1-10
int countingvalue[11]; // TO STORE THE COUNT FOR DISPLAY VALUE 
int patterncount[11]; // TO STORE THE COUNT FOR PATTERNS 
int rowIndex = 0;
int columnIndex = 0;
int range = 0;

//-------------------------------------------------------------------------------------
// VARAIBLE FOR CREATING ERROR MAP

float LOW_ERROR = 0.0;
float HIGH_ERROR = 2.5;

//-------------------------------------------------------------------------------------
// TEMPRORARY VARIABLES

float tempfloat = 0.0F;
char newline = ' ';

//-------------------------------------------------------------------------------------
// VARAIBLES FOR FINDING THE CLASSIFICATION MAP

int	increment2 = 0;
int increment3 = 0;

//-------------------------------------------------------------------------------------
// VARAIBLES FOR COUNTING VALUES
int countDisplay = 0;
int countPattern = 0;

//-------------------------------------------------------------------------------------
// FUNCTION TO ROUND UP THE DATA 

float roundValue(float value, int decimalPlaces)
{
	int count = 0;
	int magnitude = 1;
	for (count = 0; count<decimalPlaces; count++) {
		magnitude = magnitude * 10;
	}
	value = ((int)(value * magnitude + 0.5) / (float)magnitude);
	return value;
}

int main(void) {
	printf("\t\t\t\t\t\tNAME: SUNIV SHRESTHA\n\t\t\t\t\t\tSTUDENT ID: 217454028\n\n");

	//-------------------------------------------------------------------------------------
	// INITIALIZING VARAIBLES

	displayValue[0] = 0; // INITIALIZING DISPLAY VALUES
	displayValue[1] = 1;
	displayValue[2] = 2;
	displayValue[3] = 3;
	displayValue[4] = 4;
	displayValue[5] = 5;
	displayValue[6] = 6;
	displayValue[7] = 7;
	displayValue[8] = 8;
	displayValue[9] = 9;
	displayValue[10] =10;

	rangeValue[0] = 0.0; // INTIALIZING RANGE VALUES
	rangeValue[1] = 0.5;
	rangeValue[2] = 1.0;
	rangeValue[3] = 1.5;
	rangeValue[4] = 2.0;
	rangeValue[5] = 2.5;

	for (range = 0; range < 6; range++) { // INTIALIZING AVERAGE TO ZERO
		average[range] = 0;
	}

	for (range = 0; range<11; range++) { // INTIALIZING  countingvalue AND patterncount TO ZERO
		countingvalue[range] = 0;
		patterncount[range] = 0;
	}

	//-------------------------------------------------------------------------------------
	// OPENING THE FILE AND READING THE DATA

	FILE *infp;
	infp = fopen(FILE_NAME, "r");


	if (infp == NULL) {

		printf("Error: failed to open %s for reading\n", FILE_NAME);
		printf("Make sure you have %s in the same folder as the C coded .cpp file\n", FILE_NAME);
		return(1);
	}

	// Read the file into the data structure
	for (rowIndex = 0; rowIndex < MAX_ROWS; rowIndex++) {
		// Read up until the last value
		for (columnIndex = 0; columnIndex < MAX_COLUMNS - 1; columnIndex++) {
			if (fscanf(infp, "%f,", &tempfloat) != EOF) {
				rawData[rowIndex][columnIndex] = tempfloat;
			}
			else {
				printf("Error: incorrect file format at row %d, col %d.\n", rowIndex + 1, columnIndex + 1);
				return(1);
			}
		}

		// Read the last value and the newline char
		if (fscanf(infp, "%f%c", &tempfloat, &newline) != EOF) {
			if (newline != '\0' && newline != '\r' && newline != '\n') {
				printf("Error: incorrect file format at line %d. did not find a newline.\n", rowIndex + 1);
				return(1);
			}
			else {
				rawData[rowIndex][columnIndex] = tempfloat;
			}

			// Reset the character before the next read.
			newline = ' ';
		}
	}

	//-------------------------------------------------------------------------------------
	// PRINTING RAWDATA

	printf(" ----- RAW DATA -----\n\n");
	for (rowIndex = 0; rowIndex < MAX_ROWS; rowIndex++) {
		// Read up until the last value
		for (columnIndex = 0; columnIndex < MAX_COLUMNS; columnIndex++) {
			printf("%f ", roundValue(rawData[rowIndex][columnIndex], 3));
		}
	}
	printf(" \n");

	//-------------------------------------------------------------------------------------
	// PRINTING ROUNDED UP DATA

	printf(" ----- ROUNDED DATA -----\n\n");
	for (rowIndex = 0; rowIndex < MAX_ROWS; rowIndex++) {
		// Read up until the last value
		for (columnIndex = 0; columnIndex < MAX_COLUMNS; columnIndex++) {
			rawData[rowIndex][columnIndex] = roundValue(rawData[rowIndex][columnIndex], 3); // CHANGING THE DATA TO 3 DECIMAL PLACES USING roundValue FUNCTION AND OVERWRITING rawData
			printf("%.3f ", roundValue(rawData[rowIndex][columnIndex], 3));
		}
	}
	printf(" \n");

	//-------------------------------------------------------------------------------------
	// FINDING AND REMOVING ERRORS IN DATA

	for (rowIndex = 0; rowIndex < MAX_ROWS; rowIndex++) {
		// Read up until the last value
		for (columnIndex = 0; columnIndex < MAX_COLUMNS; columnIndex++) {
			if (rawData[rowIndex][columnIndex] > HIGH_ERROR) {  // CHECKING IF DATA IS EXCEEDING THE UPPER RANGE
				if (rawData[rowIndex][columnIndex] < LOW_ERROR) { // CHECKING IF DATA IS BELOW THE LOWER RANGE 
					errorMap[rowIndex][columnIndex] = LOW_ERROR; // STORING THE DATA BELOW LOWER VALUE TO errorMap BY CHANGING IT TO LOWER VALUE 
				}
				errorMap[rowIndex][columnIndex] = HIGH_ERROR; // STORING THE DATA ABOVE UPPER VALUE TO errorMap BY CHANGING IT TO UPPER VALUE 
			}
			else {
				errorMap[rowIndex][columnIndex] = rawData[rowIndex][columnIndex]; // COPYING THE ERROR FREE DATA TO errorMaP
			}
		}
	}


	//-------------------------------------------------------------------------------------
	// PRINTING THE ERROR MAP

	printf("-----ERROR MAP-----\n\n");
	for (rowIndex = 0; rowIndex < MAX_ROWS; rowIndex++) {
		for (columnIndex = 0; columnIndex < MAX_COLUMNS; columnIndex++){
			printf("%.3f ", errorMap[rowIndex][columnIndex]);
		}
	}
	printf("\n");

	//-------------------------------------------------------------------------------------
	// CALCULATING AVERAGE

	for (range = 1; range <= 5; range++) {
		float total = 0.0; // INITIALIZED TO COUNT THE TOTAL 
		int count = 0; // INITIALIZED TO COUNT THE NUMBER
		for (rowIndex = 0; rowIndex< MAX_ROWS; rowIndex++) {
			for (columnIndex = 0; columnIndex< MAX_COLUMNS; columnIndex++) {
				if (errorMap[rowIndex][columnIndex] >= rangeValue[range - 1] && errorMap[rowIndex][columnIndex] <= rangeValue[range]) {
					total = total + errorMap[rowIndex][columnIndex]; // CALCULATING THE TOTAL OF THE RANGE
					count = count + 1; // CALCULATING THE TOTAL COUNT OF THE RANGE
				}
			}
		}
		average[range] = total / count;	// CALCULATING THE AVERAGE AND STORING IT IN A ARRAY CALLED average.
	}


	// -------------------------------------------------------------------------------
	// PRINTING THE RANGE AND AVERAGE TABLE
	
	printf(" --- RANGE AND AVERAGE TABLE ---\n\n");
	printf("Value\t\tAverage\t\tValue > Average\t\tValue <= Average\n");
	int a = 1;
	printf("%.1f\n", rangeValue[0]);
	for (range = 0; range < 5; range++) {
		printf("%.1f\t\t%.3f\t\t\t%d\t\t\t%d\n", rangeValue[range + 1], average[range + 1], displayValue[a], displayValue[a + 1]);
		a = a + 2;
	}
	printf("\n");

	//-------------------------------------------------------------------------------------
	// PREPARING CLASSIFICATION MAP

	for (increment3 = 1; increment3 <= 5; increment3++) {
		increment2 = increment2 + 1;
		for (rowIndex = 0; rowIndex< MAX_ROWS; rowIndex++) {
			for (columnIndex = 0; columnIndex<MAX_COLUMNS; columnIndex++) {
				if (errorMap[rowIndex][columnIndex] > rangeValue[increment3 - 1] && errorMap[rowIndex][columnIndex] <= average[increment3]) {
					classificationMap[rowIndex][columnIndex] = displayValue[increment2];
				}
			}
		}
		increment2 = increment2 + 1;
		for (rowIndex = 0; rowIndex<MAX_ROWS; rowIndex++) {
			for (columnIndex = 0; columnIndex<MAX_COLUMNS; columnIndex++) {
				if (errorMap[rowIndex][columnIndex] > average[increment3] && errorMap[rowIndex][columnIndex] <= rangeValue[increment3]) {
					classificationMap[rowIndex][columnIndex] = displayValue[increment2];
				}
			}
		}
	}

	// -------------------------------------------------------------------------------
	// PRINTING CLASSIFICATION MAP

	printf(" --- CLASSIFICATION MAP ---\n\n");
	for (rowIndex = 0; rowIndex < MAX_ROWS; rowIndex++) {
		// Read up until the last value
		for (columnIndex = 0; columnIndex < MAX_COLUMNS; columnIndex++) {
			printf("%d  ", classificationMap[rowIndex][columnIndex]);
		}
		printf("\n");
	}
	printf("\n");

	//-------------------------------------------------------------------------------------
	// SEARCHING , STORING AND PRINTING PATTERNS 

	printf(" ----- PATTERN MAP -----\n\n");
	for (rowIndex = 0; rowIndex < MAX_ROWS; rowIndex++) {
		// Read up until the last value
		for (columnIndex = 0; columnIndex < MAX_COLUMNS; columnIndex++) {
			int MapValue = classificationMap[rowIndex][columnIndex]; // STORING VALUE FOR THE POINTER DATA
			int mapRight = classificationMap[rowIndex][columnIndex + 1]; // STORING VALUE FOR THE RIGHT OF POINTER DATA
			int mapBottomRight = classificationMap[rowIndex + 1][columnIndex + 1]; // STORING VALUE FOR THE BOTTOM OF POINTER DATA
			int	mapBottomLeft = classificationMap[rowIndex + 1][columnIndex]; // STORING VALUE FOR BOTTOM LEFT OF POINTER DATA
			if ((MapValue == mapRight) && (MapValue == mapBottomLeft) && (MapValue == mapBottomRight)) { // COMPARING TO FIND PATTERN
				patternMap[rowIndex][columnIndex] = classificationMap[rowIndex][columnIndex];   
				printf("%d ", patternMap[rowIndex][columnIndex]);     // PRINTING THE VALUE IF PATTERN FOUND     
			}
			else {
				printf("  "); // PRINTING BLANK SPACES IF PATTERN NOT FOUND
			}
		}
		printf("\n");
	}

	printf("\n");
	
	
	//-------------------------------------------------------------------------------------
	// COUNTING TOTAL NUMBER OF VALUES 

	for (rowIndex = 0; rowIndex < MAX_ROWS; rowIndex++) {
		// Read up until the last value
		for (columnIndex = 0; columnIndex < MAX_COLUMNS; columnIndex++) {
			countDisplay = countingvalue[classificationMap[rowIndex][columnIndex]];
			countingvalue[classificationMap[rowIndex][columnIndex]] = countDisplay + 1;
		}
	}


	//-------------------------------------------------------------------------------------
	// COUNTING PATTERNS FOUND

	for (rowIndex = 0; rowIndex < MAX_ROWS; rowIndex++) {
		// Read up until the last value
		for (columnIndex = 0; columnIndex < MAX_COLUMNS; columnIndex++) {
			countPattern = patterncount[patternMap[rowIndex][columnIndex]];
			patterncount[patternMap[rowIndex][columnIndex]] = countPattern + 1;
		}
	}

	//--------------------------------------------------------------------------
	// PRINT THE COUNT TABLE

	printf("-----COUNT TABLE-----\n\n ");
	printf("Display Value\t\t\tTotal Count\t\t\tPattern Count\n");
	for (range =1; range <=10; range++) {
		printf(" %d\t\t\t\t     %d\t\t\t\t\t%d\t\t\t\n", displayValue[range], countingvalue[range],patterncount[range]);
	}
	// EXIT
	return (0);
}

