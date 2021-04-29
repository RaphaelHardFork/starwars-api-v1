# Star Wars API

## Utilisation des hooks React 
A l'aide de l'article de [Robin Wieruch](https://www.robinwieruch.de/react-hooks-fetch-data)

## Step 1
Utilisation de `useEffect` dans le components `StarWarsApp`  

```js
 // variable d'état
  const [planetsList, setplanetsList] = useState([])
  const [isLoadding, setIsLoadding] = useState(false)
  const [isError, setIsError] = useState(false)

  // fetch data
  useEffect(() => {
    const fetchData = async () => {
      setIsLoadding(true)
      setIsError(false)
      try {
        let data = await axios(url)
        setIsLoadding(false)
        // agrandir la liste
        for (let elem of data.data.results) {
          planetsList.push(elem)
        }
        // modification des variables d'état
        setplanetsList(() => planetsList)
        setNextUrl(() => data.data.next)
      } catch {
        setIsError(true)
      }
      setIsLoadding(false)
    }
    fetchData()
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [url])
```
On peut noter la gestion des erreurs ainsi que l'affichage du chargement :  
```js
  // Après le return :
      {isError && <h2 className="infos">Oups... Il y a eu une erreur</h2>}

      {isLoadding ? <h2 className="infos">Chargement...</h2> : ''}

```
## Step 2 
**Utilisation d'un hook personnalisé (custom hook)**  
Pour simplifié le code tout est rassemblé dans `App.js`. Le Hook peut donc être importer depuis un autre fichier pour avoir un code plus modulaire.  
**ATTENTION:** un Custom Hook doit commencer par `use` suivi d'une majuscule : `useYourCustomHook`   
Ecriture du Hook : 
```js
// Custom Hook
const useDataFetcher = () => {
  const [url, setUrl] = useState('https://swapi.dev/api/planets/')
  const [nextUrl, setNextUrl] = useState('')
  const [isLoadding, setIsLoadding] = useState(false)
  const [isError, setIsError] = useState(false)
  const [planetsList, setplanetsList] = useState([])

  useEffect(() => {
    const fetchData = async () => {
      setIsLoadding(true)
      setIsError(false)
      try {
        let data = await axios(url)
        setIsLoadding(false)
        // agrandir la liste
        for (let elem of data.data.results) {
          planetsList.push(elem)
        }
        // modification des variables d'état
        setplanetsList(() => planetsList)
        setNextUrl(() => data.data.next)
      } catch {
        setIsError(true)
      }
      setIsLoadding(false)
    }
    fetchData()
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [url])
  return [{ nextUrl, planetsList, isLoadding, isError }, setUrl]
}
```
Importation du Hook dans notre `App`:  
```js
  const [{ nextUrl, planetsList, isLoadding, isError }, doFetch] = useDataFetcher()
```
Et enfin utilisation / appelle de notre Hook :  
```js
  // Fonction bouton Plus
  const handleButtonMore = () => {
    doFetch(nextUrl)
  }
```
