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
  ### 2) The Cache timer has exceed. **5minutes** Will always be in sync with backend or remote data
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
      'url_name', //Its a unique Key
      fetchUrl
      )
  
     if(isLoading){
      return <h2>Loading</h2>
     }

     if(isError){
      return <h2>Loading</h2>
     }

### refetchinterval OR **Polling**
#### If we want to keep fetching after certain time frame Its better to use reFetchInterval and works only when the window is on focus else if not on focus it doesnt work.
#### only use it when data keeps changing every that time period.
        const { isLoading, data, isError, error, isFetching } = useQuery(
          'url_name',
          fetchUrl,
          {
            refetchInterval:2000 //2 second
          }
         )
         
#### only use it when data keeps changing every that time period in the Background.
        const { isLoading, data, isError, error, isFetching } = useQuery(
          'url_name',
          fetchUrl,
          {
            refetchInterval:2000,
            refetchIntervalInBackground:true //2 second
          }
         )

### useQuery on click of a button.
        const { isLoading, data, isError, error, isFetching, **reFetch** } = useQuery(
          'url_name',
          fetchUrl,
          {
            enabled:false
          }
         )
         
         inside the functionComponent
         
         <button onClick={**reFetch**}>Fetch on click of a button</button>
         
### Success and Error Callbacks
#### typically used when on an successful fetch an action needs to be completed like navigating to another page or side effect or such.
        
        const onSuccess = () => {
          console.log("Do anything after data fetching")
        }
        const onError = () => {
          console.log("Do anything after encountering Error")
        }
        const { isLoading, data, isError, error, isFetching, **reFetch** } = useQuery(
          'url_name or anyIndetifier',
          fetchUrl,
          {
            onSuccess:onSuccess,
            onError:onError
            //As per ES6 if the key and value are same u can also write it as 
            onSuccess,
            onError
          }
         )
         
### Data Transformation
#### Sometimes when we need certain data and not all the data from the API We can filter,sort or perform any action then and there itself by specifing in the useQuery Hook the SELECT keyword
        const { isLoading, data, isError, error, isFetching, **reFetch** } = useQuery(
          'url_name or anyIndetifier',
          fetchUrl,
          {
            select:(data) => {  //data here is the response
              const anyVar = data.//response data OBJ.map(hero => hero.name)
              return anyVar
            }
          }
         )

         inside the main return function
         <h2>
          {data.map((varName) => {
            return <div key={varName}>{varName}</div>
         </h2>


### Parallel Queries
#### When we have a lot of URL and need to execute them together to increase the concurrency we use Parallel queries i.e. back to back useQuery

            const fetchURL1 = () => {
              return axios.get('URL ENDPOINT')
            }
            const fetchURL2 = () => {
              return axios.get('URL ENDPOINT')
            }
            
            export const ParallelQueriesPage = () => {
              const {data : ABC} = useQuery('identifier', fetchURL1)
              const {data : ABCD} = useQuery('identifier', fetchURL2)
            }
            
 ### Dynamic Parallel Queries
 #### if we need a details of 1 single user or multiple user may be 1 or 3...Parallel queries may not be effecient that time its better to use DynamicParallelQueries by using useQueries.
 
            import { useQueries } from 'react-query'
            import axios from 'axios'

            const fetchSuperHero = heroId => {
              return axios.get(`http://localhost:4000/superheroes/${heroId}`)
            }

            export const DynamicParallelPage = ({ heroIds }) => {
              const queryResults = useQueries(
                heroIds.map(id => {
                  return {
                    queryKey: ['super-hero', id],
                    queryFn: () => fetchSuperHero(id)
                  }
                })
              )

              console.log({ queryResults })
              return <div>Dynamic Parallel Queries</div>
            }


### Dependent Queries
#### when We need to execute queries sequentially i.e. one after another.
#### i.e. the result of 1st query depends as input to 2nd query

              import { useQuery } from 'react-query'
              import axios from 'axios'

              const fetchUserByEmail = email => {
                return axios.get(`http://localhost:4000/users/${email}`)
              }

              const fetchCoursesByChannelId = channelId => {
                return axios.get(`http://localhost:4000/channels/${channelId}`)
              }

              export const DependentQueriesPage = ({ email }) => {
                const { data: user } = useQuery(['user', email], () =>
                  fetchUserByEmail(email)
                )
                const channelId = user?.data?.channelId
                const { data: courses } = useQuery(['courses', channelId], () => fetchCoursesByChannelId(channelId), {
                  enabled: !!channelId // !!here will convert to a boolean value if no channelID false else true
                })

                const coursesList = courses?.data.courses
                return (
                  <div>
                    DependentQueries
                    {channelId && <h3>{channelId}</h3>}
                    {coursesList?.map(course => {
                      return <h3>{course}</h3>
                    })}
                  </div>
                )
              }


### Initial Query Data
#### Using the data from the previous fetch to the current data holder.
#### The Above can be achived with useQueryClient. 
              import { useQuery, useQueryClient } from 'react-query'
              import axios from 'axios'

              const fetchSuperHero = ({ queryKey }) => {
                const heroId = queryKey[1]
                return axios.get(`http://localhost:4000/superheroes/${heroId}`)
              }

              export const useSuperHeroData = heroId => {
                const queryClient = useQueryClient()
                return useQuery(['super-hero', heroId], fetchSuperHero, {
                  initialData: () => {
                    const var = queryClient
                      .getQueryData('unique identifier')
                      ?.data?.//DO something
                    if (var) {
                      return { data: *** }
                    } else {
                      return undefined
                    }
                  }
                })
              }


### Pagination 
#### When the backend API supports paginatedURL's we can implement this using ReactQuery in an react application using the useState() state variable and setting the pageLimit and pageNUmber dynamically. ALso the previous page data can be stored until the new page data arrives.

              import { useState } from 'react'
              import { useQuery } from 'react-query'
              import axios from 'axios'

              const fetchColors = pageNumber => {
                return axios.get(`URLENDPOINT?_limit=2&_page=${pageNumber}`)
              }

              export const PaginatedQueriesPage = () => {
                const [pageNumber, setPageNumber] = useState(1)
                const { isLoading, isError, error, data, isFetching } = useQuery(
                  ['UNIQUE_KEY', pageNumber],
                  () => fetchColors(pageNumber),
                  {
                    keepPreviousData: true  //This enables to display the previous data to be displayed until the next data arrives.
                  }
                )
