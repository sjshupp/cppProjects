/*For C++:
12/10/2019
A supermarket has 9 cashiers. 
Customers arrive and leave at random time intervals. 
Customers arrive faster than they leave. 
Within 0-5 seconds, the queues should fill up. 
Clear the screen every second to display the queues
and simulate the passing of time.*/

//This is the QueueClass
#include <iostream>
#include <vector>

#pragma once
class QueueClass
{
private:
	int maxSize;
	std::vector<char> queVect;
	int front;
	int rear;
	int nItems;
public:
	//Constructor for testing purposes
	QueueClass()
	{
		maxSize = 7;
		front = 0;
		rear = -1;
		nItems = 0;
		queVect.resize(maxSize);
	}
	//Constructor for the class
	QueueClass(int s) //Takes size integer as a parameter
	{
		maxSize = s;
		front = 0;
		rear = -1;
		nItems = 0;
		queVect.resize(maxSize);
	}

	//The following function is to put a char at the rear of the queue
	void push(char c)
	{
		if (rear == maxSize - 1)
			rear = -1;
		queVect[++rear] = c;
		nItems++;
	}

	//The following function is to remove a char from the front of the queue
	char remove()
	{
		char temp = queVect[front++];
		if (front == maxSize)
			front = 0;
		nItems--;
		return temp;
	}

	//Function to display the entirety of the queue
	void displayQueue()
	{
		for (int i = 0; i < nItems; i++)
			std::cout << queVect[i] << " ";
		std::cout << endl;
	}

	//Function to find if the queue is full
	bool isFull()
	{
		return (nItems == maxSize);
	}

	//Function to find if the queue is empty
	bool isEmpty()
	{
		return (nItems == 0);
	}

	//Function to return number of items in the queue
	int size()
	{
		return nItems;
	}
};

//This is the code for the SuperMarketClass
//It is a class that has multiple queues

#include "QueueClass.h"
#include <time.h>
#pragma once
class SuperMarketClass
{
private:
	std::vector<QueueClass> ourVector; //Master vector to store all the queues
	int numOfQueues, numOfElements;
public:

	//Constructor takes two arguments, the desired number of
	//queues and the number of elements in each queue
	SuperMarketClass(int numQ, int numE)
	{
		numOfElements = numE; //Set variables
		numOfQueues = numQ;
		ourVector.resize(numOfQueues); //Resize array
		srand(time(NULL)); //seed for random numbers
		for (int i = 0; i < numOfQueues; i++) //Iterate through num of queues
		{
			QueueClass newQueue(numOfElements); //Initialize a new queue
			ourVector[i] = newQueue; //Add the new queue to the master vector
		}
	}

	//Function to loop for time
	void timeLoop(int numOfMilliSeconds)
	{
		clock_t startTime = clock();
		while (clock() < startTime + numOfMilliSeconds);
	}

	//Function to insert a 'customer' into lowest size queue
	void insert()
	{
		QueueClass* queuePointer = &ourVector[0]; //Set a pointer
		int qSize = queuePointer->size(); //Set variable to queue's size
		for (int i = 0; i < numOfQueues; i++)
		{
			if (qSize > ourVector[i].size()) //If our size is bigger than the new vector's size
			{
				queuePointer = &ourVector[i]; //Point at the new address
				qSize = queuePointer->size(); //Set the new size
			}
		}
		if (queuePointer->isFull()) //If the queue is full, exit
			return;
		queuePointer->push('*'); //Else, push the asterik
	}

	//Function to randomly remove one char from the queue
	void randomRemove()
	{
		int queueNum = rand() % numOfQueues; //Initialize a random number that equals the number of queues
		ourVector[queueNum].remove(); //Remove from that queue
	}

	//Function to display all contents in each queue
	void displayAll()
	{
		int i = 0; //Variable for the number of queues
		for (i; i < numOfQueues; i++)
		{
			std::cout << "- - - - - -\n"
				<< "Cashier " << i + 1 << " | ";
			ourVector[i].displayQueue();
		}
		std::cout << "- - - - - -\n";
	}

	//Function to insert into all queues (for testing purposes)
	void insertAll()
	{
		char c = '*';
		for (int i = 0; i < numOfQueues; i++)
		{
			for (int j = 0; j < numOfElements; j++)
			{
				ourVector[i].push(c);
			}
		}
	}
};


//The main function

#include "SuperMarketClass.h"

int main()
{
	SuperMarketClass object(9, 5);
	srand(time(NULL));
	int randomInsert; //Initialize variable for random insertions
	int randomRemove; //Initialize variable for random removal
	for (int i = 0; i < 5; i++) // Loop for 0 - 5 seconds
	{
		for (int j = 0; j < 30; j++) //Loop for random removals/insertions before display
		{
			//Because random inserts occur more frequently than removals,
			//we are doing modulo 2 and 4 respectively
			randomInsert = rand() % 2;
			randomRemove = rand() % 4;
			if (randomInsert == 1)
			{
				object.insert();
			}
			if (randomRemove == 2)
			{
				object.randomRemove();
			}
		}
		object.displayAll();
		object.timeLoop(1000);
		system("CLS");
	}
	return 0;
}
