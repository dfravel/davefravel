---
title: "Solving 401 Unauthenticated with VueJS and Axios"
description: "VueJS, Vuex, Axios and solving the 401 unauthenticated error. Axios Interceptors to the rescue"
date: 2018-08-26
draft: false
tags: ["Development"]
---

I kept getting a "401 Unauthenticated" error with my VueJS/Laravel application. I could login without a problem. The Vuex state had my token. Local Storage has the token, but for some reason the initial call to fill the dashboard after login failed every time. What's worse - a quick refresh of the page and it was fine.

After much reading, searching, and head-scratching I discovered that Axios Interceptors would do the trick.

Here's the original code where I was calling the local storage token from within the API client:

```
import axios from "axios";
import { LocalStorage } from "quasar";
const token = LocalStorage.getItem("TOKEN NAME GOES HERE");

const apiClient = axios.create({
    baseURL: process.env.VUE_APP_API_URL,
    headers: {
        Accept: "application/json",
        "Content-Type": "application/json",
        Authorization: "Bearer " + token
    }
});

export default {
    fetchInstitutions() {
        return apiClient.get("/institution");
    },
```

Here's the new code where I've created an http-common.js file

```
import axios from "axios";
import { LocalStorage } from "quasar";

const apiClient = axios.create({
    baseURL: process.env.VUE_APP_API_URL,
    headers: {
        Accept: "application/json",
        "Content-Type": "application/json"
    }
});


apiClient.interceptors.request.use(
    config => {
        let token = LocalStorage.getItem("TOKEN NAME GOES HERE");

        if (token) {
            config.headers["Authorization"] = `Bearer ${token}`;
        }
        return config;
    },

    error => {
        return Promise.reject(error);
    }
);

export default apiClient;
```

And then import `http-common.js` into the file that calls the API:

```
import apiClient from "@/functions/http-common.js";

export default {
    fetchInstitutions() {
        return apiClient.get("/institution");
    },

...
```

(PS) I know that some people take issue with the use of storing the token in Local Storage. I don't care.
