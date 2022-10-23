# Learning-React-Query
Examples of React-Query step by step

Basically Used to fetch data.as async state
Often used to maintain server state also.

avoid useState() for loading and error handlings.
const {isLoading, isError, data} = query.fetch.fromAPI       // auto stored

React Query manages the client as well as server state by itself. like cacheing,
handling multiple requests for same data update stale data (Stale is information in an object that does not reflect reality  Eg an object is holding a handle to some other object, but the second object has been deleted in the meantime)
,performance optimizations and all.

Server state is not just Fetching but also need of updating.(Like how Youtube or instagram updates its feed everytime as soon as it exceeds certain duration or minutes or any quick chnages is available.
Things like
Basic query,Poll data,Dev tools, reusable query hooks, QueryById, parallel:Dynamic:Dependent:infinate & pagination Queries
Updating data using mutations(To create/update/delete data):-send a request to the server, receive a response, and based on a defined updater function we mutate our state and keep it fresh without making an additional request.

### Comparing to the traditional methods of Fetching the data retrieved 
### The traditional method executes the URL each and every time renders
### But React-Query executes only and only 
  ### 1) The backend data has changed.
  ### 2) The Cache timer has exceed. **5minutes**
        const { isLoading, data, isError, error, isFetching } = useQuery(
          'url_name',
          fetchUrl,
          {
            cacheTime:50000
          }
         )

### Basic of how to use Fetch from API/URL using React Query
const fetchUrl = () => {
  return axios.get('URL')
 }
 
 ### The Below function avoids using saparate state variables 
 ### And thus gives Errors, data all at once.
 inside function 
 
     const { isLoading, data, isError, error, isFetching } = useQuery(
      'url_name',
      fetchUrl
      )
  
     if(isLoading){
      return <h2>Loading</h2>
     }

     if(isError){
      return <h2>Loading</h2>
     }
