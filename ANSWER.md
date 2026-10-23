1. In EventList, what goes wrong if you use the array index as :key and the list gets filtered while a card has local state (for example an open details panel)?

If we use the array index as key when the list is filtered : 
- Vue matches components to items by key. After filtering, positions shift, so index 0 holds a different event, but Vue reuses the old card instance for key 0. Local state, such as an open details panel, stays with the position, so the wrong event appears open. Fix: :key="event.id", a unique and stable ID.

2. Why is visible a computed and not a function called from the template? What is the difference when you type in the search box?
- visible is a computed , not a function called from the template because : 
    + A computed is cached and re-runs only when a dependency changes (search text, events array). 
    + A template function re-runs on every render and every call.
- When typing in the search box, the search text changes, the computed runs once, and the result is reused across the template. A function filters again on each render and each call (v-if plus v-for means two filters). Small lists hide the cost, while large lists show lag.     

3. EventCard emits book instead of changing event.booked. Give two reasons.
- Props only reads the data from the parents when it updates
- Using emit to chagne a value instead of mutating props is better . Allow a strict one way data flow. 

4. You write const { booked } = reactive(event); booked++. The page does not change.
Why?
- It is the descrtor . createing a plain copy of the value from the object , so we can use 'booked++' because the type of the variable is const. 
5. Where does myBookings live, and why not in EventCard or EventList?
- myBookings live in 'App.vue' because : 
    + EventCard : is the state of the card and can change when filtering
    + EventList : is only the component that contains the cards