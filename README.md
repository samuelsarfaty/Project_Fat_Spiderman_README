# Project Fat Spiderman - Personal contributions.
By Samuel Sarfaty - 33506853

## 1. The player's weight system
My first task on the project was to develop the weight system for the player. The weight system works in three layers: first, there is a continuous variable with a range from 0 to 1 which determines the player's weight. Second, there are 3 states that the player can have depending on the value of the first variable: light, normal, and heavy. Lastly, according to the status, the player will have different movement properties and abilities.

To start working on the system, I made two variables: weight(float) and weightStatus(enum).

### Function UpdateWeightStatus





