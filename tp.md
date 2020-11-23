## Pré-requis
- Se rendre sur https://expo.io/ et créer un compte
- Télécharger l'application Expo sur votre smartphone : 
    - Android: https://play.google.com/store/apps/details?id=host.exp.exponent&hl=fr&gl=US
    - Ios: https://apps.apple.com/fr/app/expo-client/id982107779

## TP 1: Créer une liste de musique
Afficher la liste de musique grâce à l'url suivante: `https://react-native-codelab-api.herokuapp.com/random`.
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

Dans un premier temps nous allons créer une liste d'item grâce au composant `VirtualizedList`: https://reactnative.dev/docs/virtualizedlist
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

Quand on touche un item de la liste des musiques on veut être redirigé vers l'écran de détail de la musique:
```
const MusicItem = ({ music }) => {
    return (
      <TouchableHighlight style={styles.item} onPress={() => navigation.navigate('Details', { music })}>
        <Text style={styles.title}>{music.name}</Text>
      </TouchableHighlight>
    );
};
```

Depuis le composant `Detail` nous aurons accès aux données de la musique grâce à la props `route`:
```
export default function Detail({ route }) {
  const { music } = route.params;
  ...
```

Dans le composant `Detail` afficher le titre et la pochette de la musique:
```
    <View style={styles.container}>
      <Text style={styles.name}>
        {music.name}
      </Text>
      <Image style={styles.image} source={{ uri: music.images[0].url }} />
    </View>
```


## TP 3: Partagez vos musiques
Dans ce TP nous allons ajouter un bouton pour pouvoir partager une musique, pour cela nous allons utiliser l'objet `Share` :https://reactnative.dev/docs/share

Dans le composant `Detail` ajouter un bouton "Partage" en dessous de la pochette:
```
<Button style={styles.share} onPress={onShare} title="Share" />
```

Puis creez une fonction `share`:
```
  const onShare = async () => {
    try {
      const result = await Share.share({
        message: `I just listened to ${music.name}, it's amazing`,
      });
      if (result.action === Share.sharedAction) {
        if (result.activityType) {
          // shared with activity type of result.activityType
        } else {
          // shared
        }
      } else if (result.action === Share.dismissedAction) {
        // dismissed
      }
    } catch (error) {
      alert(error.message);
    }
  };
```
