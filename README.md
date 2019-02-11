# mobile_game

>Experimenting with react-native-game-engine and matter.js.
>Matter.js is a JS Physics engine library that helps you create shapes, collision detection, and gravity
>React Native Game Engine is a React library that provides the Game Loop that will update the state 
>Learning about gravity and the interactions with objects on mobile.

Added the Game Engine to the component
``` export default class App extends React.Component {
  render() {
    return (
      <GameEngine style={styles.container}>
        <Statusbar hidden={true}/>
      </GameEngine>
    );
  }
} 
```