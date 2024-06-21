## Hands-on Assignments: Day 4

We will continue where we left of in Day 3 assignment: **Dunder Mifflin Ticketing Portal**!

Here is what we have built till now:

<https://github.com/BuildWithHussain/vtf-training/assets/34810212/5bf44a0f-42f9-4327-a129-42dd62e8476f>

Here are the tasks at hand for this sprint:

* Search and Sort in Ticket List View
* Ticket Detail View
* Setup Authentication
* Fix Header Avatar
* Knowledge Base View

Before proceeding with the assignment, it is recommended to complete the Day 4 readings.

### Task 1: Search and Sort in List View

### Task 2: Setup Vue Router

It is time to setup routing in our VueJS app. Let's start by [installing Vue Router](https://router.vuejs.org/installation.html):

```bash
npm install vue-router@4
```

Now we can configure our router in the `main.js` file. Start by adding the following import:

```js
import { createRouter, createWebHistory } from 'vue-router'
```

Then we can create a new router instance:

```js
const routes = [] // TODO: define routes

const router = createRouter({
    history: createWebHistory(),
    routes: routes
})
```

Then we will plug-in the router instance to our app:

```diff
app = createApp(App)
...
+   app.use(router)
...
app.mount('#app')
```

Now the only step remaining is to tell Vue Router where to mount the components defined for each route. Update `App.vue`'s template to have the `router-view`:

```vue
<template>
    <Header />
    <router-view />
</template>
```

You will notice that our list view is no longer being rendered. It is your task now to configure the below 2 routes:

1. '/' -> Redirect to '/tickets'

1. '/tickets' -> Should load `TicketList` component we created in day 3

Make sure you load the components lazily. Here are some docs to help you out:

* [Setting up Redirect](https://router.vuejs.org/guide/essentials/redirect-and-alias.html)
* [Lazily Loading Routes](https://router.vuejs.org/guide/advanced/lazy-loading.html)

As soon as you setup the above routes, your frontend should load the tickets list and the route should be '/tickets'!

### Task 3: Ticket Details Page

Create a new component named `TicketDetails` (`TicketDetails.vue`) and configure a new route '/tickets/:name' which Should load this component. Also, **give a name** value of `TicketDetailsPage` to this route, so we can navigate to this page using this name instead of hardcoding the route. `:name` will be the ID of the ticket whose details will be displayed.

#### Displaying the `name`

Our task is to catch and display the route parameter `name` in our ticket details page.

Hint: Use `useRoute` hook to access the route parameters.

#### Linking List Items to Details Page

It is time to link the List and the Details page. We want to navigate to a particular ticket's detail page when it is clicked (row in the list). Now, here is the interesting part, FrappeUI's `ListView` component has built in support for this!

We can just pass a `getRowRoute` function in our options prop. We get the row data as argument to this function and have to return the route we want to navigate to:

```diff
<ListView
    ...
    :options="{
        ...,
+       getRowRoute: (row) => ({ 
+           name: 'TicketDetailsPage', 
+           params: { 'name': row.name } 
+       })
    }"
/>
```

Here `row.name` is the name of the ticket. Behind the scenes, FrappeUI will turn our rows into a Router Link! We can confirm that using [Vue Dev Tools](https://devtools.vuejs.org/):

![Row Route in Vue Dev Tools](../.github/images/row-route-bts.png)

#### Displaying ticket details

Now it is your time to do some exploration and implement the details page shown below:

<https://github.com/BuildWithHussain/vtf-training/assets/34810212/60011a1a-c52c-4657-90bc-544eb367890f>

You have to use the `createDocumentResource` utility from `frappe-ui` to fetch a particular ticket document. We already have the **name** of that document/ticket in route params!

Here are some implementation details:

* The Purchase Date is formatted using a library called [`dayjs`](https://day.js.org/docs/en/installation/node-js). Install this package in your frontend app. Here is a snippet of how to import and use this package in your components:

```js
import dayjs from 'dayjs'

function getFormattedDate(dateString) {
    // dateString comes from backend, e.g. '2024-06-13'
    return dayjs(dateString).format("YOU-FIGURE-THIS-OUT")
}
```

* The **Mark as resolved** button calls a whitelisted method that you have to create in your `Support Ticket` controller file:

```py
class SupportTicket(Document):
    @frappe.whitelist()
    def mark_as_resolved(self):
        self.status = "Resolved"
        self.save()
        # More steps could go here, e.g. email notification.     
```

Check out the documentation of `createDocumentResource` on frappeui.com to find out how you can easily work with whitelisted methods and you should be able to implement this feature in a breeze! Also, the status gets auto-updated in the frontend, FrappeUI magic!

### Task 4: Basic Authentication & Route Protection

...

### Task 5: Fix the Avatar

...

### Task 6: Knowledge Base Page

Add a new route at **'/kb'** that renders a component named `KnowledgeBase`. Here is how the `KnowledgeBase` component should look like:

[TODO]

Some things to note:

1. This should be a public route, i.e. not protected.
1.