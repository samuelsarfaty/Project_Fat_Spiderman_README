# Project Fat Spiderman - Personal contributions.
By Samuel Sarfaty - 33506853

## 1. The player's weight system
My first task on the project was to develop the weight system for the player. The weight system works in three layers: first, there is a continuous variable with a range from 0 to 1 which determines the player's weight. Second, there are 3 states that the player can have depending on the value of the first variable: light, normal, and heavy. Lastly, according to the status, the player will have different movement properties and abilities.

To start working on the system, I made a few variables: weight(float), thresholdNormal(float), thresholdHeavy(float), and weightStatus(enum)

### 1.1 Function UpdateWeightStatus
![update weight stauts](https://user-images.githubusercontent.com/32599151/34072969-58752b62-e288-11e7-8b74-4e469447715a.png)

This function takes a float as an argument (weight) and returns a status. If weight is lower than thresholdNormal, 'Light' is returned. If weight is between thresholdNormal and thresholdHeavy, 'Normal' is returned. If weight doesn't fit into any of the aforementioned conditions, then 'Heavy' is returned.

### 1.2 Function UpdateAbilities
![update abilities](https://user-images.githubusercontent.com/32599151/34073121-c6b556ae-e28a-11e7-88cc-6c036d34c5db.png)

The purpose of this function is to change some of the properties of the character's movement depending on the weight status. Therefore, this function takes the value of weightStatus as an argument. The player properties affected are Max Walk Speed, Jump Z velocity, Gravity Scale, and Mass.

As soon as the function is called, the values of all the values of all the aforementioed properties are reset to their defaults. Then, depending on the weightStatus argument, the properties are multiplied by different amounts. The screenshot above shows exemplifies this function when taking the 'Light' status as an argument. We take the values of Max Walk Speed, Jump Z velocity, Gravity Scale, and Mass and multiply them by 1.3, 1.3, 0.7, and 0.01 respectively. After some playtesting, we decided that Jump Z velocity should not be affected, hence the missing connection to its set pin.

Given that these two functions are closely related, whenever UpdateWeightStatus is called, UpdateAbilities should be called right after to avoid conflicts between states and character properties. I decided to keep both functions separate to keep the blueprints neater, albeit I would have to remember to always call both functions together. I might refactor this code to join them in the future.

### 1.3 Shedding Weight
The next feature of the player was losing weight on a key press. For this, I first made a simple function called LoseWeight. This function takes a float 'loseRate' as argument. Weight is then reduced by 'loseRate'.

![loseweight](https://user-images.githubusercontent.com/32599151/34073217-a69d8718-e28c-11e7-84c5-4b4c4a2a09bc.png)

Then, a keypress sequence was added in the Event Graph to lose weight:

![lose weight on mouse press](https://user-images.githubusercontent.com/32599151/34073224-d1eb01ac-e28c-11e7-86c6-82ae35123da8.png)

When the player presses E, a timer by event starts. We check if weight is higher than 0. If so, we call the 'LoseWeight' function, followed by 'UpdateWeightStatus' which takes the new weight as argument, and then 'UpdateAbilities' which takes the return value of 'UpdateWeightStatus' as argument. This sequence is runs for as long as the E key remains pressed.

## 2. Food Functionality
In order for the player to gain weight, food needs to be sucked. There are two types of food in the game: regular food and food source. Regular food gets sucked by the player, adds weight, and disappears. Food sources continously add weight to the player without disappearing. Although they share the same blueprint class, they are differentiated by the boolean 'isSource'. This variable is exposed in the editor so that every instance of the class can be set to either regular or source.

# 2.1 Function GainWeight
Regardless of the type of food the player gets, the function 'GainWeight' is always called:















