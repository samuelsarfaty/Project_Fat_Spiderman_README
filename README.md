# Project Fat Spiderman - Personal contributions.
By Samuel Sarfaty - 33506853

## 1. The player's weight system
My first task on the project was to develop the weight system for the player. The weight system works in three layers: first, there is a continuous variable with a range from 0 to 1 which determines the player's weight. Second, there are 3 states that the player can have depending on the value of the first variable: light, normal, and heavy. Lastly, according to the status, the player will have different movement properties and abilities.

To start working on the system, I made a few variables: weight(float), thresholdNormal(float), thresholdHeavy(float), and weightStatus(enum)

### Function UpdateWeightStatus
![update weight stauts](https://user-images.githubusercontent.com/32599151/34072969-58752b62-e288-11e7-8b74-4e469447715a.png)

This function takes a float as an argument (weight) and returns a status. If weight is lower than thresholdNormal, 'Light' is returned. If weight is between thresholdNormal and thresholdHeavy, 'Normal' is returned. If weight doesn't fit into any of the aforementioned conditions, then 'Heavy' is returned.

### Function UpdateAbilities


The purpose of this function is to change some of the properties of the character's movement depending on the weight status. Therefore, this function takes the value of weightStatus as an argument. The player properties affected are Max Walk Speed, Jump Z velocity, Gravity Scale, and Mass.

As soon as the function is called, the values of all the values of all the aforementioed properties are reset to their defaults. Then, depending on the weightStatus argument, the properties are multiplied by different amounts. The screenshot above shows exemplifies this function when taking the 'Light' status as an argument. We take the values of Max Walk Speed, Jump Z velocity, Gravity Scale, and Mass and multiply them by 1.3, 1.3, 0.7, and 0.01 respectively. After some playtesting, we decided that Jump Z velocity should not be affected, hence the missing connection to its set pin.





