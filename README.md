# Project Fat Spiderman - Personal Contributions.
By Samuel Sarfaty - 33506853

# Part I - Programming

## 1. The player's weight system
My first task on the project was to develop the weight system for the player. The weight system works in three layers: first, there is a continuous variable with a range from 0 to 1 which determines the player's weight. Second, there are 3 states that the player can have depending on the value of the first variable: light, normal, and heavy. Lastly, according to the status, the player will have different movement properties and abilities.

To start working on the system, I made a few variables: weight(float), thresholdNormal(float), thresholdHeavy(float), and weightStatus(enum)

### 1.1 Function UpdateWeightStatus
![update weight stauts](https://user-images.githubusercontent.com/32599151/34072969-58752b62-e288-11e7-8b74-4e469447715a.png)

This function takes a float as an argument (weight) and returns a status. If weight is lower than thresholdNormal, 'Light' is returned. If weight is between thresholdNormal and thresholdHeavy, 'Normal' is returned. If weight doesn't fit into any of the aforementioned conditions, then 'Heavy' is returned.

### 1.2 Function UpdateAbilities
![update abilities](https://user-images.githubusercontent.com/32599151/34073121-c6b556ae-e28a-11e7-88cc-6c036d34c5db.png)

The purpose of this function is to change some of the properties of the character's movement depending on the weight status. Therefore, this function takes the value of weightStatus as an argument. The player properties affected are Max Walk Speed, Jump Z velocity, Gravity Scale, and Mass.

As soon as the function is called, the values of all the aforementioed properties are reset to their defaults. Then, depending on the weightStatus argument, the properties are multiplied by different amounts. The screenshot above exemplifies this function when taking the 'Light' status as an argument. We take the values of Max Walk Speed, Jump Z velocity, Gravity Scale, and Mass and multiply them by 1.3, 1.3, 0.7, and 0.01 respectively. After some playtesting, we decided that Jump Z velocity should not be affected, hence the missing connection to its set pin.

Given that these two functions are closely related, whenever UpdateWeightStatus is called, UpdateAbilities should be called right after to avoid conflicts between states and character properties. I decided to keep both functions separate to keep the blueprints neater, albeit I would have to remember to always call both functions together. I might refactor this code to join them in the future.

### 1.3 Shedding Weight
The next feature of the player was losing weight on a key press. For this, I first made a simple function called LoseWeight. This function takes a float 'loseRate' as argument. Weight is then reduced by 'loseRate'.

![loseweight](https://user-images.githubusercontent.com/32599151/34073217-a69d8718-e28c-11e7-84c5-4b4c4a2a09bc.png)

Then, a keypress sequence was added in the Event Graph to lose weight:

![lose weight on mouse press](https://user-images.githubusercontent.com/32599151/34073224-d1eb01ac-e28c-11e7-86c6-82ae35123da8.png)

When the player presses E, a timer by event starts. We check if weight is higher than 0. If so, we call the 'LoseWeight' function, followed by 'UpdateWeightStatus' which takes the new weight as argument, and then 'UpdateAbilities' which takes the return value of 'UpdateWeightStatus' as argument. This sequence is runs for as long as the E key remains pressed.

### 1.4 Function GainWeight
Regardless of the type of food the player gets, the function 'GainWeight' is always called:

![gainweight](https://user-images.githubusercontent.com/32599151/34073376-6df61732-e290-11e7-9808-70e6497cb76e.png)

## 2. Food Functionality
In order for the player to gain weight, food needs to be sucked. There are two types of food in the game: regular food and food source. Regular food gets sucked by the player, adds weight, and disappears. Food sources continously add weight to the player without disappearing. Although they share the same blueprint class, they are differentiated by the boolean 'isSource'. This variable is exposed in the editor so that every instance of the class can be set to either regular or source.

This function works in the opposite way than 'Lose Weight'. We take a float 'weightToGain' as argument and we add that number to the current weight.

### 2.1 Food Sources
Food sources increase the player's weight for as long as the player is aiming at the food souce and right click is held down. The logic is shown in the player's blueprint:

![rightclick](https://user-images.githubusercontent.com/32599151/34073823-9e331486-e29a-11e7-97c2-8b95a86dcfb8.png)

When the right mouse button is clicked, we first check that weight is lower than 1. We do this so that the player is not allowed to keep sucking food if the weight is at the maximum point. Then, we cast to the blueprint of the aimed food actor (the aiming mechanic was implemented by another team member). After casting, we check if the food is source. If so, and while right click is being held, the food source will call the functions 'GainWeight', 'UpdateWeightStatus', and 'Update abilities' continuously. The cycle stops when the right mouse button is released.

If, after right clicking, the aimed food is not a source, then we set the variable 'beingSucked' to true inside the food blueprint. This functionality will be explained in the next section.

### 2.2 Normal Foods
Every food blueprint has the following 'MoveToPlayer' function:

![move to player](https://user-images.githubusercontent.com/32599151/34073869-d3e8c390-e29b-11e7-8368-01791ddd6c3c.png)

This function takes the position of the food, player, and interpolates between the two at the speed set by the argument.

![food tick](https://user-images.githubusercontent.com/32599151/34073962-8d53a52e-e29d-11e7-9194-bf75b56a940f.png)

Every frame, we check if 'beingSucked' has been set to true by the player. If so, the food will move towards the player.

![food overlap 1](https://user-images.githubusercontent.com/32599151/34073980-c8854f44-e29d-11e7-9e77-4906011812ea.png)

Once the food reaches the player, an overlap event first checks if the food has collided with the player's root component. If so, we then cast to the player blueprint.

![food overlap 2](https://user-images.githubusercontent.com/32599151/34074010-75d2d50e-e29e-11e7-8734-e9a2309b8940.png)

Then, we check if the player's weight is less than 1 and if the food is not a source. If both conditions are met, then the gaining weight sequence is executed and the food is destroyed.

## 3. Death Box

The death box is a simple collider which can be placed next to traps. The first function of the deathbox is to detect collisions with the player.

![1](https://user-images.githubusercontent.com/32599151/34216823-623433ac-e5a1-11e7-9194-9880967eb6a6.png)

Next, we store the current weight of the player and then destroy the player. We store the weight value because when the player respawns, we want them to have the same weight as they did before dying.

![2](https://user-images.githubusercontent.com/32599151/34216894-99adc122-e5a1-11e7-8bdc-2ae05676bd9c.png)

## 4. Checkpoints and Respawning

The respawn system is handled in the GameMode blueprint. This seems appropriate as the GameMode is a persistent object across different levels. The respawn blueprint has two variables: startPosition and lastCheckpointLocation. The position at which the player is respawned will depend on the values of these variables.

### 4.1 Checkpoints

![checkpoint](https://user-images.githubusercontent.com/32599151/34217987-e2d2fc8e-e5a4-11e7-9c52-4ed86d9622ae.png)

The checkpoint actor has a collider which checks for collisions with the player. If the player touches this collider, then we cast to the GameMode and assign the value of lastCheckpointLocation to the checkpoint's position. This way, whenever the player reaches a new checkpoint, the value of lastCheckpointLocation is overwritten.

### 4.2 Respawn System

![1](https://user-images.githubusercontent.com/32599151/34217454-7566b128-e5a3-11e7-8b92-b528ae0cd853.png)

On start, we use the blueprint to store the player's starting position. From then onwards, we wait until the OnDestroyedEvent is called. This event is called whenever the player is destroyed; i.e. when he collides with the Death Box. Once the player is destroyed, we wait one second and the rest of the sequence is executed.

![2](https://user-images.githubusercontent.com/32599151/34217584-bca79b6a-e5a3-11e7-9b10-dd98f6eefcd3.png)

After the delay, we need to assess the right location for the player to respawn at. if lastCheckPointLocation is null, meaning that the player never collided with a checkpoint, then we respawn the player at the startPosition. If the player did collide with a checkpoint, then he will be respawned at lastCheckpointPosition.

![3](https://user-images.githubusercontent.com/32599151/34217769-458b9440-e5a4-11e7-981b-48a2ccdd0eb7.png)

Then, once the player is respawned, we take the value of weightAtDeath which was taken when the player died and we reassign it to the player. Otherwise, the player would be respawned with the default weight. Lastly, we use this weight value to update the status and abilities accordingly.

It was also important to make sure that the player spawns looking in the right direction. If they respawn facing backwards, then that might confuse the player. I noticed that the player always respawns at the specified location looking down the checkpoint's local X axis. Therefore, I aligned the checkpoint's flag with actor's X axis, which gives us control over the player's rotation upon respawning.

![flag](https://user-images.githubusercontent.com/32599151/34218482-32515e76-e5a6-11e7-9f6d-8cae4dcf9473.png)

Afterwards, the checkpoint model was improved. It now has a red emissive material that turns green when the player activates the checkpoint. I also added a sound cue as part of the activation sequence.

![audio](https://user-images.githubusercontent.com/32599151/34307527-7e5c5a3c-e740-11e7-85cb-202243897c01.png)

![last flag](https://user-images.githubusercontent.com/32599151/34307533-864c96e4-e740-11e7-98d2-4dd289ab6006.png)

## 5. User Interface

![ui](https://user-images.githubusercontent.com/32599151/34908676-5bb97936-f88b-11e7-96f2-361e89c54cfe.png)

The UI of the game consists of only a few elements: First, a progress bar which shows the player's weight as a continuous variable. A weight icon is placed to the left of the bar to symolize that the bar's fill represents weight. Then, the hexagonal symbols on top of the reflect the player's weight status (Light, Normal, or Heavy).

### 5.1 The Progress Bar

The progress bar comes as part of Unreal's UI system. A binding is necessary in order to connect the player's weight with the amount of fill in the bar.


![scrollbar binding](https://user-images.githubusercontent.com/32599151/34908731-41029c5c-f88c-11e7-97fd-a4d735203e72.png)

The progress bar ranges from 0 to 1, and the value of the player's weight also spans across this range. Therefore, the weight value was applied to the bar without needing any conversions.

### 5.2 Weight Status Symbols

Each hexagonal symbol was bound independently to the player's weight status. This way, whenever the player is in a specific status, the appropriate symbol would be displayed and the other two would be given an alpha of 0. This caused the inactive symbols to 'disappear' from the screen.

In the blueprint below, if weight status is normal, then the image will be opaque. Otherwise, it will be transparent.

![image binding](https://user-images.githubusercontent.com/32599151/34908777-2e2d169c-f88d-11e7-880a-5e1800d68f07.png)

This causes the following behavior in-game:

![hexagonal symbol](https://user-images.githubusercontent.com/32599151/34908804-d09512c2-f88d-11e7-9878-e957422f6630.png)

# Part II Modelling

## 1. Checkpoint

I started by making the base out of a cylinder and insetting edges. Then, I beveled the edges to make them smoother

![base 2](https://user-images.githubusercontent.com/32599151/34908950-bf1fa6ee-f890-11e7-836b-3b8a653b5ea1.png)

![base bevelled](https://user-images.githubusercontent.com/32599151/34908952-c46d2482-f890-11e7-95cb-90cf19ebe96e.png)

The flag started as a thin cube with a few loop cuts. I then contracted one side and pused the middle of the opposite side to make it look more like a flag

![flag cut](https://user-images.githubusercontent.com/32599151/34908969-160a4d92-f891-11e7-93ce-3e195a6260a9.png)

For the pole, I simply stretched a sylinder and made some loop cuts across. This was done to create more faces which would later be assigned an emissive material.

![edges on pole](https://user-images.githubusercontent.com/32599151/34908982-42172fb8-f891-11e7-9aa4-2c4cbf2a55b3.png)



































