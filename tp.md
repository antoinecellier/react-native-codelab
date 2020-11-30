## Pré-requis
- Se rendre sur https://expo.io/ et créer un compte
- Télécharger l'application Expo sur votre smartphone : 
    - Android: https://play.google.com/store/apps/details?id=host.exp.exponent&hl=fr&gl=US
    - Ios: https://apps.apple.com/fr/app/expo-client/id982107779

## TP 1: Créer une liste de musique
Dans un premier temps nous allons créer une liste d'item grâce au composant `VirtualizedList`(https://reactnative.dev/docs/virtualizedlist) en utilisant les données du fichier `data.json`.

Dans le fichier `components/Home.js` voici comment nous allons utiliser le composant `VirtualizedList`:
```
export default function Home({ navigation }) {

      ....
      
      const getItemCount = (data) => {
        return data.length;
      };

      const MusicItem = ({ music }) => {
        return (
          <TouchableHighlight style={styles.item} onPress={() => {}}>
            <Text style={styles.title}>{music.name}</Text>
          </TouchableHighlight>
        );
      };

      return (
        <SafeAreaView style={styles.container}>
          <VirtualizedList
            data={musics}
            keyExtractor={(item) => item.id}
            getItemCount={getItemCount}
            getItem={(data, index) => data[index]}
            renderItem={({ item }) => <MusicItem music={item} />}
            ListEmptyComponent={() => <Text>Empty list</Text>}
          />
        </SafeAreaView>
      );
}
```

Nous voulons à présent récupèrer une liste de musique depuis une API à la place du fichier `data.json` :
```
  useEffect(() => {
    const fetchMusics = async () => {
      const response = await fetch(
        'https://react-native-codelab-api.herokuapp.com/random'
      );
      const json = await response.json();
      setMusics(json);
    };

    fetchMusics();
  }, []);
```


## TP 2: Mettre en place la navigation pour accèder au détail d'un musqiue
Ajouter une nouvelle route dans le fichier `App.js`:
```
<NavigationContainer>
  <Stack.Navigator>
    ...
    <Stack.Screen name="Details" component={Details} />
  </Stack.Navigator>
</NavigationContainer>
```

Quand on touche un item de la liste des musiques on veut être redirigé vers l'écran de détail de la musique.
Nous pouvons utliser la `props` `navigation` de `react-navigation` pour changer de route :
```
export default function Home({ navigation }) {

...

    const MusicItem = ({ music }) => {
        return (
          <TouchableHighlight style={styles.item} onPress={() => navigation.navigate('Details', { music })}>
            <Text style={styles.title}>{music.name}</Text>
          </TouchableHighlight>
        );
    };
...
```

Depuis le composant `Detail` nous aurons accès aux données de la musique grâce à la props `route`:
```
export default function Detail({ route }) {
  const { music } = route.params;
  ...
```

Dans le composant `Detail` afficher le titre et la pochette de la musique. Pour cela vous pouvez utiliser les composants `Text` (https://reactnative.dev/docs/text) et `Image` (https://reactnative.dev/docs/image) :
```
    <View style={styles.container}>
      <Text style={styles.name}>
        {music.name}
      </Text>
      <Image style={styles.image} source={{ uri: music.images[0].url }} />
    </View>
```


## TP 3: Partagez vos musiques
Dans ce TP nous allons ajouter un bouton pour pouvoir partager le titre d'une musique à une application tier, pour cela nous allons utiliser l'objet `Share` (https://reactnative.dev/docs/share).


Creez une fonction `onShare` en utilisant `Share`:
```
  const onShare = async () => {
    try {
      await Share.share({
        message: `I just listened to ${music.name}, it's amazing`,
      });
    } catch (error) {
      alert(error.message);
    }
  };
```

Dans le fichier `components/Detail.js` ajouter un `Button` (https://reactnative.dev/docs/button) "Partage" en dessous de la pochette:
```
<Button style={styles.share} onPress={onShare} title="Share" />
```
