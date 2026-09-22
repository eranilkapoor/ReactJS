# React JS

A step-by-step, beginner-to-expert ReactJS (web) learning path. This folder assumes
you're comfortable with the topics in [../JavaScript/README.md](../JavaScript/README.md);
TypeScript typing for React specifically is covered in
[../TypeScript/TypeScriptWithReact.md](../TypeScript/TypeScriptWithReact.md).

> **Scope note:** This pass covers core ReactJS for the web only. The React Native
> sections further down in the original curriculum outline below are kept for
> reference but do not yet have written files in this folder.

## Files in This Folder (Core Web React)

### Fundamentals
1. [Introduction to React.js](./Introduction.md)
2. [Create React App / Project Setup](./CreateReactApp.md)
3. [Components](./Components.md)
4. [Functional Components](./FunctionalComponents.md)
5. [Props](./Props.md)
6. [State](./State.md)

### Component Patterns
7. [Component Lifecycle & Lifecycle Hooks](./LifeCycleHooks.md)
8. [Hooks](./Hooks.md)
9. [Pure Components](./PureComponents.md)
10. [Higher Order Components (HOC)](./HigherOrderComponents(HOC).md)
11. [Context API](./ContextApi.md)
12. [Error Boundaries](./ErrorBoundaries.md)
13. [Lazy Loading & Code Splitting](./LazyLoading.md)
14. [Performance Optimization (memo, useMemo, useCallback)](./PerformanceOptimization.md)

### Data & Forms
15. [Forms](./Forms.md)
16. [Working with Ajax (Fetch & Axios)](./Ajax.md)

### Routing & State Management
17. [Routing (React Router)](./Routing.md)
18. [Redux](./Redux.md)
19. [Redux Toolkit](./ReduxToolKit.md)

### Rendering, Testing & Debugging
20. [Server-Side Rendering & Next.js](./NextJS-SSR.md)
21. [Testing React Apps (Jest & React Testing Library)](./Testing.md)
22. [Debugging React Apps](./Debugging.md)

## Prerequisites
- [JavaScript fundamentals](../JavaScript/README.md)
- HTML & CSS

## Next Steps
- [TypeScript with React](../TypeScript/TypeScriptWithReact.md) — type your components, hooks, and props
- [TechnicalArchitect](../TechnicalArchitect/README.md) — once comfortable here, section B (Frontend Architecture) covers advanced hooks patterns, micro-frontends, and Next.js at architect level

---

## Original Full Curriculum Outline (including React Native, for reference)

## Table Of Contents :-
- Pre Requisites :-
    * HTML
    * CSS
    * JavaScript

- Course Design :-
    * ES6 Introduction
        - ES6 Introduction
        - Setting up the Development Environment
            > Install Visual Studio Code 
            > Install latest and stable version of NodeJS -> Check it by node -v 
        - ES6 Features
            > let and const keyword
        - Modular Programming
        - Arrow Functions
        - Default, Named and wildcard Exports and their usage
        - Rest and Spread Operators
        - Template Literals
        - Destructuring 
            > Object and Array De-structuring 
        - this keyword in JavaScript
    * Introduction to React.js 
        - [Introduction to React.js] (#Introduction to React.js)
        - [Single Page Application (SPA)] (Single Page Application (SPA))
        - Understanding SPA features
        - Node Package Manager (NPM)
        - Create New App using Create-React-App (CRA)
        - Understanding Project folder structure
        - NPX
        - Introduction to JSX
        - Transpilling JSX to JavaScript using Babel
        - JSX Restrictions
        - React Fragment
    * Working with Components
        - Component Architecture
        - Creating Components
        - Rendering Components
        - Understanding Components Basics
        - Stateless Components and Stateful Components
        - Functional Components
        - Pure Components
        - Applying Styles to Components
        - Higher Order Components (HOC)
        - React Context API 
        - Error Boundaries
        - Lazy Loading
    * Props & State
        - Working with Props
        - Understanding & Using State
        - Props and State 
        - Component Hierarchy
        - Using the useState() API for State Manipulation
        - Event Handling
        - Passing data between Components
        - Data Binding
        - Virtual Dom
        - Diffing
        - Lifting State Up
        - Composition Vs Inheritance
    * Component LifeCycle
        - Understanding Component LifeCycle
        - LifeCycle methods
        - Startup and mounting
        - Deprecated methods
        - Updating
        - Deprecated methods
        - Unmounting
    * Forms
        - Working with Forms
        - Controlled Vs Uncontrolled Components
        - Does React control your form field?
        - When does React find out about changes to you form field?
        - What does React know about your from field?
        - Form field types
        - Controlling a text field
        - Other form fields
        - Getting data out of a form 
        - Validating form controls  
    * Working with List and Conditionals
        - Rendering Content Conditionally
        - Handling Dynamic Content "The JavaScript Way"
        - Outputting Lists
        - Lists & State 
        - Updating State Immutably
        - Lists & Keys
        - Flexible Lists 
    * Working with Ajax
        - fetch() API
        - Working with Http using Axios Library
        - Making Ajax Requests using Axios Library
    * Introduction to Routing 
        - What problem is routing trying to solve?
        - How does routing solve this problem?
        - Trying Components to URLs
        - Passing parameters via the URL 
        - Routing software
        - React-router 
        - Other routers
        - Simple router example 
        - Redirect example  
    * Debugging React Apps
        - Module Introduction
        - Understanding Error Messages
        - Finding Logical Errors by using Dev Tools Sourcemaps
        - Working with the React Developer Tools 
        - Using Error Boundaries(React 16+)
        - Wrap Up
    * Overview of Hooks
        - Overview of React Hooks
        - Using built in Hooks  
    * State Management Using Redux
        - What is State Management
        - Flux
        - Mobx 
        - Redux
        - Signle Source of Truth
        - Redux Principles
        - Global State 
        - Reducer
        - Actions 
        - Dispatchers
        - Integrating React with Redux
    * Working with Redux
        - Pitfalls of Local State
        - State management Patterns
        - Flux Pattern
        - Mobx vs Redux
        - Integrating React and Redux
        - Redux Architecture
        - Store
        - Reducers
        - Actions
        - Complex actions with Redux
        - Data Flow
        - Reducers and State Trees
        - Combining Reducers
        - Reducer Patterns
        - Computing Derived Data
        - Creating Redux Middleware
    * Asynchronous Redux
        - The difficulties of asynchronus Redux
        - Asynchronous middleware
        - Redux Thunk
        - Redux Saga
        - Redux Thunk vs Redux Saga
        - Dispatching async actions
        - Typing async results
        - Catching results
        - Handling errors
    * React Router
        - Working with React Router
        - Configure Routing
        - Working with Links & Creating Nested Routes
        - Navigating using the React Router
        - Dynamic Routes
        - Passing Parameters to Routes
        - Redirects
    * Server Side Rendring in React
        - Understanding Server Side Rendring (SSR)
        - React SSR frameworks
        - Use Next.js to Build SSR
        - Introducing Next.js with React
        - Add initial component from template
        - Add data for props
        - Add Redux store and setup
        - Add Redux actions
        - Add Redux reducers
        - Finalize overall components with Redux
    * Unit Testing
        - Unit testing React Applications Using Jest & Enzyme
    * Full Stack with GraphQL, React & Apollo
        - Itroducing GraphQL
        - REST vs GraphQL
        - Working with GraphQL
        - GraphQL Schemas
        - Fetching Data
        - Working with Apollo Client
    * Getting Started with React Native
        - What is React Native?
        - How React Native Works?
        - Expo vs React Native CLI
        - Installation of required softwares
        - Creating our first App
        - Running the App on an Android Emulator and iOS Simulator
    * Diving into the Basics of React Native
        - React Native Components
        - Setting Up A New Project
        - Planning the App
        - Working with Core Components
        - Getting Started with Styles
        - Flexbox & Layouts (Intro)
        - React Native Flexbox Deep Dive
        - Inline Styles & StyleSheet Objects
        - Components, Styles, Layouts
        - Working with State & Events
        - Styling List Items
        - Making it Scrollable with ScrollView!
        - A Better List: FlatList
        - Splitting the App Into Components
        - Passing Data Between Components
        - Working with Touchable Components
        - Flexbox Styling
        - Closing the Modal & Clearing Input
        - Finishing the Modal Styling
    * Debugging React Native Apps
        - What To Debug & How To Debug?
        - Running the App on a Real Device & Debugging
        - Handling Error Messages
        - Understanding Code Flow with console.log()
        - Using the Remote Debugger & Breakpoints
        - Working with the Device DevTools Overlay
        - Debugging the UI & Using React Native Debugger
    * Components, Styling, Layouts - Building Real Apps
        - Setup & App Planning
        - Custom Header Component
        - Screen Component
        - Styling a View as a Card Container
        - Extracting a Card Component
        - Color Theming with Constants
        - Configuring & Styling a TextInput
        - Cleaning User Input & Controlling the Soft Keyboard
        - Resetting & Confirming User Input
        - Switching Between Multiple Screens
        - Installing expo-font
        - Synthetic Style Cascade
        - Adding Local Images
        - Styling Images
        - Working with Network (Web) Images
        - Building a Custom Button Component
        - Adding Icons
        - Exploring UI Libraries
        - Styling List Items & Lists
        - ScrollView & Flexbox
        - Using FlatList Instead of ScrollView
    * Responsive & Adaptive User Interfaces and Apps
        - Finding Improvement Opportunities
        - Working with More Flexible Styling Rules
        - Introducing the Dimensions API
        - Calculating Sizes Dynamically
        - Problems with Different Device Orientations
        - Controlling Orientation & Using the KeyboardAvoidingView
        - Rendering Different Layouts
        - Updating All Code to Update Dynamically
        - The Dimensions API & Responsive UIs
        - Expo's ScreenOrientation API
        - Working with Platform.select() and Platform in if Checks
        - The Platform API
        - Using the SafeAreaView
    * Navigation with React Navigation
        - Planning the App
        - Adding Screens, AppLoading and Fonts
        - Installing React Navigation
        - Creating a StackNavigator
        - Navigating Between Screens
        - Pushing, Popping & Replacing
        - Configuring the Header with Navigation Options
        - Default Navigation Options & Config
        - Navigation Params & Configuration
        - Grid Styling & Some Refactoring
        - Adding Header Buttons
        - Fixing the Shadows
        - Adding Tabs-based Navigation
        - Setting Icons and Configuring Tabs
        - Adding MaterialBottomTabs
        - Adding a Favorites Stack
        - Configuring the Drawer
        - Adding a DefaultText Component
        - Passing Data Between Component & Navigation Options
    * State Management and Redux
        - What is State & What is Redux?
        - Redux & Store Setup
        - Selecting State Slices
        - Redux Data & Navigation Options
        - Dispatching Actions & Reducer Logic
        - Switching the Favorites Icon
        - Rendering a Fallback Text
        - Adding Filtering Logic
        - Dispatching Filter Actions
        - Debugging Redux in React Native Apps
    * Handling User Input
        - Configuring TextInputs
        - Adding Basic Validation
        - Getting Started with useReducer()
        - Finishing the Merged Form & Input Management
        - Moving Input Logic Into A Separate Component
        - Connecting Input Component & Form
        - Tweaking Styles & Handling the Soft Keyboard
    * HTTP Requests and Adding a Web Server + Database
        - Setup & How To Send Requests
        - Installing Redux Thunk
        - Storing Products on a Server
        - Fetching Products from the Server
        - Displaying a Loading Spinner & Handling Errors
        - Setting Up a Navigation Listener
        - Updating & Deleting Products
        - Handling Additional Errors
        - Storing Orders
        - Displaying an ActivityIndicator
        - Fetching Stored Orders
        - Adding "Pull to Refresh"
    * User Authentication 
        - How Authentication Works
        - Implementing a Basic Login Screen
        - Adding User Signup
        - Logging Users In
        - Managing the Loading State & Errors
        - Using the Token
        - Mapping Orders to Users
        - Using AsyncStorage
        - Implementing "Auto Login and Auto Logout"
        - Auto-Logout & Android (Warning)
    * Native Device Features
        - Planning the App
        - Screen & Navigation Setup
        - Redux & Adding Places
        - Accessing the Device Camera
        - Configuring the Camera Access
        - Using the Picked Image
        - Storing the Image on the Filesystem
        - Changed SQLite Import
        - Diving into SQLite for Permanent Data Storage
        - Storing and Fetching Data in the Local Database
        - Getting the User Location
        - Showing a Map Preview of the Location
        - Displaying an Interactive Map
        - Making the Picked Location Saveable
        - Storing Picked Places
        - Updating the Location Screen When the Location Changes
        - Displaying the Details Screen
        - Running the App on iOS and Android
    * Building Apps Without Expo
        - Alternatives to Expo
        - Building Apps with Just the React Native CLI
        - Live Reload and RN CLI Apps
        - Adding Native Modules to Non-Expo Apps
        - Understanding Expo's "Bare Workflow"
        - Ejecting from Expo's "Managed Workflow"
    * Publishing React Native Apps
        - Deployment Steps
        - Configuring the App & Publishing
        - Configuring Icons & The Splash Screen
        - Working with Offline Asset Bundles
        - Using "Over the Air Updates" (OTA Updates)
        - Building the Apps for Deployment (iOS & Android)
        - Publishing iOS Apps without Expo
        - Publishing Android Apps without Expo
        - Configuring Android Apps
    * Updating to React Navigation 5+
        - Preparing the Project
        - More Information & Updating the Project Dependencies
        - Moving from the "Registry-like" to the "Component-based" Navigation Config
        - First Migration Steps
        - Converting More Stack Navigators to the New Config
        - Migrating the Drawer Navigation
        - Replacing the "Switch" Navigator & Auth Flow
        - Logout & Further Fixes/ Adjustments
        - Extracting Screen Params
    * Push Notifications
        - Understanding Notifications
        - Sending Local Notifications
        - Getting Permissions
        - Controlling How Notifications Are Displayed
        - Reacting to Foreground and Background Notifications
        - How Push Notifications Work
        - Expo & Push Notifications
        - Getting a Push Token
        - Sending Push Notifications
        - Using Expo's Push Server
        - More on Push Tokens
        - Push Notifications in non-Expo Managed Apps