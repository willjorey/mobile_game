# mobile_game

>Experimenting with react-native-game-engine and matter.js.
>Matter.js is a JS Physics engine library that helps you create shapes, collision detection, and gravity
>React Native Game Engine is a React library that provides the Game Loop that will update the state 
>Learning about gravity and the interactions with objects on mobile.

Added the Game Engine to the component. Next is to add SYSTEMS and ENTITIES to the engine
``` 
export default class App extends React.Component {
  render() {
    return (
      <GameEngine style={styles.container}>
        <Statusbar hidden={true}/>
      </GameEngine>
    );
  }
} 
```

## Rendering a Box

>New entity box.js

```
import React, { Component } from "react";
import { View } from "react-native";
import { array, object, string } from 'prop-types';

export default class Box extends Component {
  render() {
    const width = this.props.size[0];
    const height = this.props.size[1];
    const x = this.props.body.position.x - width / 2;
    const y = this.props.body.position.y - height / 2;
    
    return (
      <View
        style={{
            position: "absolute",
            left: x,
            top: y,
            width: width,
            height: height,
            backgroundColor: this.props.color || "pink"
          }}/>
    );
  }
}

Box.propTypes = {
    size: array,
    body: object, 
    color: string
}

```

>In App.js we add new imports to follow the dimensions for all phones

```
const { width, height } = Dimensions.get("screen");
const boxSize = Math.trunc(Math.max(width, height) * 0.075);
const initialBox = Matter.Bodies.rectangle(width / 2, height / 2, boxSize, boxSize);
```

>Matter.Bodies.rectangle takes in an x, y, width, and height.

>Now we add the entity to the GameEngine 

```
      <GameEngine style={styles.container} entities={{initialBox:{
        body: initialBox,
        size:[boxSize, boxSize],
        color: 'red',
        renderer: Box
      }}}>
        <Statusbar hidden={true}/>
      </GameEngine>
```

##Rendering a Floor

```
const floor = Matter.Bodies.rectangle(width / 2, height - boxSize / 2, width, boxSize, { isStatic: true });

<GameEngine
style={styles.container}
entities={{ initialBox: { 
               body: initialBox, 
               size: [boxSize, boxSize], 
               color: 'red', 
               renderer: Box
         },
           floor: { 
               body: floor, 
               size: [width, boxSize], 
               color: "green", 
               renderer: Box 
        },
}>
<StatusBar hidden={true} />
</GameEngine>
```

##Adding Gravity

>Matter.Engine.create creates a new Physics engine, a controller that manages updating the simulation of the world. Engine.world will create a world that will contain all simulated bodies and constraints.

```
const engine = Matter.Engine.create({ enableSleeping: false });
const world = engine.world;
```

>Add our box and floor to the world. Matter.World.add takes in two parameters: a world, and an array of Matter.Bodies. We can add the line below after all our consts.

```
Matter.World.add(world, [initialBox, floor]);

```

>Add our engine and world to our list of entities in the GameEngine component.

```
entities={{ 
            physics: { 
               engine: engine, 
               world: world 
         },
            initialBox: { 
               body: initialBox, 
               size: [boxSize, boxSize], 
               color: 'red', 
               renderer: Box
         },
           floor: { 
               body: floor, 
               size: [width, boxSize], 
               color: "green", 
               renderer: Box 
        }
}}
```

>After refreshing the app, there's still no gravity because we have to add systems

>Systems will update our engine and game state, otherwise nothing will move. Systems will be an array of functions, that will be called on every game tick. Meaning, these functions will be run at a very fast speed, and can be used to check and update our game.

```
const Physics = (entities, { time }) => {
    let engine = entities["physics"].engine;
    Matter.Engine.update(engine, time.delta);
    return entities;
};
```

>Physics takes in an object of entities, and an Timer object of time. It will set a local variable engine to the entities object property physics.engine, and it will update it for every delta change in Time. Lastly it will return the updated entities.

>Add the system to the game engine

```
<GameEngine
style={styles.container}
systems={[Physics]}
entities={{ 
            physics: { 
               engine: engine, 
               world: world 
         },
            initialBox: { 
               body: initialBox, 
               size: [boxSize, boxSize], 
               color: 'red', 
               renderer: Box
         },
           floor: { 
               body: floor, 
               size: [width, boxSize], 
               color: "green", 
               renderer: Box 
        }
}}>
<StatusBar hidden={true} />
</GameEngine>
```