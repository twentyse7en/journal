# React Native: Map vs Flatlist

## Introduction
I'm trying to find how the regular `map` will perform versus `Flatlist` in react-native releam. I 'm trying to
render list of image and text and a input. While typing the input everything will re-render. So let's check how it will perform.

Device info: [Motorola Edge 13](https://www.gsmarena.com/motorola_edge_30-11500.php) | 6GB RAM | Android 13

## Map
Change the data length and checking perormance at difference length.

- 100: Good, no issue
- 250: Not good, lag was visible
- 500: Bad, no point increasing from here

```tsx
import React, {useState} from 'react';
import {
  View,
  Image,
  Text,
  ScrollView,
  StyleSheet,
  TextInput,
} from 'react-native';

const DATA = Array(100)
  .fill(0)
  .map((_, index) => index + 100);

export const List = () => {
  const [value, setValue] = useState('');

  return (
    <ScrollView>
      <TextInput value={value} onChangeText={setValue} />
      {DATA.map(val => (
        <View key={val}>
          <Image
            height={200}
            width={400}
            source={{uri: `https://placehold.jp/3d4070/ffffff/150x${val}.png`}}
          />
          <Text style={styles.textStyle}>
            hello world {value} {val}
          </Text>
        </View>
      ))}
    </ScrollView>
  );
};

const styles = StyleSheet.create({
  textStyle: {
    backgroundColor: 'white',
    borderRadius: 8,
    marginBottom: 8,
    color: 'red',
  },
});
```

Removed the image and tried again. Same result.

# FlatList

I tested with count 100, 500, 1000, 10000. No issues.

I didn't see blank screens. Sometimes when you scroll too fast, you could see blank screens.

```tsx
import React, {useState} from 'react';
import {
  View,
  Image,
  Text,
  ScrollView,
  FlatList,
  StyleSheet,
  TextInput,
} from 'react-native';

const DATA = Array(10000)
  .fill(0)
  .map((_, index) => index + 100);

export const List = () => {
  const [value, setValue] = useState('');

  return (
    <View style={styles.containerStyles}>
      <TextInput value={value} onChangeText={setValue} />
      <FlatList
        data={DATA}
        keyExtractor={item => item.toString()}
        renderItem={({item}) => (
          <View>
            <Image
              height={200}
              width={400}
              source={{
                uri: `https://placehold.jp/3d4070/ffffff/150x${item}.png`,
              }}
            />
            <Text style={styles.textStyle}>
              hello world {value} {item}
            </Text>
          </View>
        )}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  containerStyles: {
    flex: 1,
    borderWidth: 2,
    borderColor: 'red',
  },
  textStyle: {
    backgroundColor: 'white',
    borderRadius: 8,
    marginBottom: 8,
    color: 'red',
  },
});
```

## Conclusion
Use `FlatList` when you are dealing with large amount of data. If you are trying this on pure react then we need install some [library](https://github.com/bvaughn/react-window). Thank you react-native for having this.

For further [optimization](https://reactnative.dev/docs/optimizing-flatlist-configuration)