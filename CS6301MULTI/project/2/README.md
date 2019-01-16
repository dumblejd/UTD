# Project2 Pseudocode Code
## dxj170930 DiJin
## Samephore
```java
volatile int []waiting = new int[2];
volatile int []active = new int[2];
Semaphore []direction;
Semaphore mutex;
Bridge_Samaphore()
{
	waiting[0]=0;
	waiting[1]=0;
	active[0]=0;
	active[1]=0;
	direction=new Semaphore[] {new Semaphore(0),new Semaphore(0)};
	mutex = new Semaphore(1);
}
public int reverse(int myDirection)
{
	return 1-myDirection;
}
void ArriveBridge (int myDir) 
{
    mutex->P();
    if (activeCars[reverse(mydir)] >0  || waitingCars[reverse(mydir)]>0) // if there is someone on other side, stuck this one
    {
        waitingCars[mydir]++;         // need to wait
        mutex->V();
        direction[myDir]->P(); //get mutex
    } 
    else 
    { // ok to go ahead
        activeCars[mydir]++;
        mutex->V();
    }
}

void LeaveBridge (int myDir) 
{
    mutex->P();
    if (activeCars[myDir]-1 == 0 && waitingCars[reverse(myDir)]>0) 
  //if there is someone on other side, move one by one
    { 
        activeCars[myDir]--
        direction[reverse(myDir)]->V();
        activeCars[reverse(mydir)]++; 
    }
    else if(activeCars[myDir]-1 == 0 && waitingCars[myDir]>0)// other side has no car wait or active then release all car on my side
    {
    	activeCars[myDir]--;
    	while(waitingCars[myDir]>0)
    	{
        	activeCars[myDir]++
        	direction[myDir]->V();
        	waiting[mydir]--;
    	}
    }
    }
    else
    {
      activeCars[myDir]--;
    }
    mutex->V();
}

```


## Monitor

```java
lock lock=new ReentrantLock();
volatile int []waitingCars = new int[2];
volatile int []activeCars = new int[2];
Condition [] condition = new Condition[2];
condition[0]=lock.newCondition();
condition[1]=lock.newCondition();


public int reverse(int myDirection)
{
	return 1-myDirection;
}
void ArriveBridge (Direction myDir) 
{
	lock.lock();
	if (activeCars[myDir]-1 == 0 && waitingCars[reverse(myDir)]>0) // if there is someone on other side, stuck this one
	{
		waitingCars[mydir]++;       
		while(activeCars[myDir]-1 == 0 && waitingCars[reverse(myDir)]>0)   // need to wait
		{
			condition[myDir].await();
		}
	}
	else //ok to go
	{
		activeCars[myDir]++;
	}
	lock.unlock;
}
void LeaveBridge(Direction myDir)
{
	lock.lock();
    if (activeCars[myDir]-1 == 0 && waitingCars[reverse(myDir)]>0) 
	//if there is someone on other side, move one by one
	{
        activeCars[myDir]--
        condition[reverse(myDir)].signal();
        activeCars[reverse(myDir)]++; 
    }
    else if(activeCars[myDir]-1 == 0 && waitingCars[myDir]>0)
    // other side has no car wait or active then release all car on my side
    {
    	activeCars[myDir]--;
    	while(waitingCars[myDir]>0)
    	{
	    	activeCars[myDir]++
	    	direction[myDir].signal();
	    	waiting[mydir]--;
    	}
    }
    else
    {
      activeCars[myDir]--;
    }
	lock.unlock();
}



```