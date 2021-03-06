# ScheduleTable

A Schedule Table Library for Arduino

A schedule table is a data structure to schedule actions along a timeline.
A schedule table has a period and can process one or more actions.
For instance one can depict a schedule table with period 500 as follow

<img src="https://dl.dropboxusercontent.com/u/67740546/st.png">

Actions may be set a dates inside the schedule table.
Suppose you want to flash a LED every 500 ms, a first action, ON may be set
at date 200 and a second one, OFF, may be set at date 250.

<img src="https://dl.dropboxusercontent.com/u/67740546/sta.png">

When the schedule table is executed action ON is processed at 200 and OFF at
250. When the date reaches 500, the schedule table restarts at 0, so ON is
processed again at date 700 and OFF at date 750 and so on.

To use the ScheduleTable library you have to import the library in the IDE
or insert

```
#include <ScheduleTable.h>
```

at the beginning of your sketch.

## Creating a schedule table

To implement our LED behavior, we need a schedule table with 2 actions and a
500ms period. This is done by using a ScheduleTable class instance :

```
ScheduleTable blinkLED(2,500);
```

By default the time base is in milliseconds. If a different timebase is needed,
a third argument may be used to set the timebase. For example the following
declaration would set the timebase of the schedule table to 50ms so that
period is now expressed in multiple of 50ms.

```
ScheduleTable blinkLED(2,10,50);
```

Each schedule table may have their own timebase.

## Adding actions

Actions are functions called by the schedule table. An action is a function
that returns nothing and has no argument. Let's define 2 actions for our led,
ledOn and ledOff:

```
void ledOn()
{
  digitalWrite(13, HIGH);
}

void ledOff()
{
  digitalWrite(13, LOW);
}
```

These actions are now added to the schedule table. This is done in the setup
function along with a pinMode to set digital I/O 13 in OUTPUT.

```
pinMode(13, OUTPUT);
blinkLED.at(200,ledOn);
blinkLED.at(250,ledOff);
```

##Starting a schedule table

A schedule table must be started to process the actions. This is done by
calling the method start. Without any argument, the schedule table is started
for an infinite number of periods. For a fixed number of period, this number
is given as argument.

```
blinkLED.start(); // starts blinkLED for an infinite number of period
```

or

```
blinkLED.start(10); // starts blinkLED for 10 periods
```

##Stopping a schedule table

A schedule table may be stopped by calling the method stop

```
blinkLED.stop(); // stop blinkLED
```

##Setting the period

The period of a schedule table may be changed during the execution of the
application by calling the setPeriod method

```
blinkLED.setPeriod(1000); // change blinkLED period to 1000
```

If a shorter period is used, action beyond the period are not processed
anymore

##Processing a schedule table

Schedule tables should be updated as fast as possible and at least every
millisecond if you want to have the actions processed at a reasonably accurate
date. This is done by calling the class method update in loop.

```
ScheduleTable::update()
```

As a result the current date of all the schedule tables is updated and
processing of action is done.

##Full example

Here is the full example of blinkLED

```
#include <ScheduleTable.h>

ScheduleTable blinkLED(2,500);

void ledOn()
{
  digitalWrite(13, HIGH);
}

void ledOff()
{
  digitalWrite(13, LOW);
}

void setup() {
  pinMode(13, OUTPUT);
  
  blinkLED.at(200,ledOn);
  blinkLED.at(250,ledOff);
  
  blinkLED.start();
}

void loop() {
  ScheduleTable::update();
}
```

For more examples checks the examples directory.