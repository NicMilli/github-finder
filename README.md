# GitHub finder app
# [View the published app here!](https://github-finder-ten-alpha.vercel.app/)

This is a search tool to find users on github and view key information and stats from their profile. It was taught in [React Front-to-Back](https://www.udemy.com/course/react-front-to-back-2022/) on Udemy.

##Skills Used:
- APIs
- Alerts
- Asynchronous functions
- context API
- Reducers

##Technologies Used:
- Axios
- Daisy UI
- Tailwind
- React
- React-router-dom
- React-icons
- CSS3
- Git
- GitHub
- JavaScript

# Approach:
The aim was to creater a user interface for interacting with the GitHub API, providing an alternative layout for viewing a user profile. API calls are made using Axios to format HTTP get requests. The state is handled by a useContext hook and the state is distributed by the useReducer hook. This way no dedicated backend or database is needed as search data is fetched on demand and stored locally.

# Successes and short-comings:
* Success:
  * Learning React Hooks
  * A sleek, responsive design using Daisy UI
  * Making API calls using axios and React hooks

* Short-comings:
  * There is not much room to customize and so the app is mostly the same as that taught in React Front-to-Back on Udemy.

# Code Highlights:
The state is stored in the GitHub context file and distributed to other components using the context hook.

```javascript githubContext.js
 import { createContext, useReducer } from "react";
import GithubReducer from "./GithubReducer";

const GithubContext = createContext()

export const GithubProvider = ({children}) => {
    const initialState = {
        users: [],
        user: {},
        repos: [],
        loading: false
    }

    const [state, dispatch] = useReducer(GithubReducer, initialState)

    return (<GithubContext.Provider value={{
        ...state,
        dispatch,
        }}>
        {children}
        </GithubContext.Provider>)
    
}

export default GithubContext
```

API calls are made by axios in the GitHub actions file.

```javascript githubActions.js
import axios from 'axios'

const GITHUB_URL = process.env.REACT_APP_GITHUB_URL 
const GITHUB_TOKEN = process.env.REACT_APP_GITHUB_TOKEN 

const github = axios.create({
    baseURL: GITHUB_URL,
    headers: {Authorization: `token ${GITHUB_TOKEN}`}
})

// Get search results from API
export const searchUsers = async (text) => {

    const params = new URLSearchParams({
        q:text
    })

   const response = await github.get(`/search/users?${params}`)
   return response.data.items
}

//Get user and user repos
export const getUserAndRepos = async (login) => {
    const [user, repos] = await Promise.all([
        github.get(`/users/${login}`),
        github.get(`/users/${login}/repos`)
    ])

    return {user: user.data, repos: repos.data}
}
```

Finally the state is distributed to the relevant components using the GitHub reducer

```javascript githubReducer.js
const GithubReducer = (state, action) => {
    switch(action.type) {
        case 'GET_USERS':
            return {
                ...state,
                users: action.payload,
                loading: false
            }
        case 'GET_USER_AND_REPOS': 
        return {
            ...state,
            user: action.payload.user,
            repos: action.payload.repos,
            loading: false,
        }
        case 'SET_LOADING':
            return{
                ...state,
                loading:true
            }  
        case 'CLEAR_USERS':
            return{
                ...state,
                users: []
            }  
        default:
            return state
    }
}

export default GithubReducer
```
