<script lang="ts">
  import Loading from "./components/Loading.svelte";
  import type { Routes } from "./lib/models/route";
  import Router, { back, forward, link } from "./lib/Router.svelte";
  import About from "./routes/About.svelte";
  import Error from "./routes/Error.svelte";
  import Fallback from "./routes/Fallback.svelte";
  import GlobalError from "./routes/GlobalError.svelte";
  import GlobalLoading from "./routes/GlobalLoading.svelte";
  import Home from "./routes/Home.svelte";
  import Login from "./routes/Login.svelte";
  import PostsOverview, {
    loadData as loadPosts,
  } from "./routes/PostsOverview.svelte";
  import { wait } from "./utils/wait";

  let loading = false;

  const routes: Routes = {
    "/": Home,
    "/about": About,
    "/login": Login,
    "/posts": {
      component: PostsOverview,
      loadData: loadPosts,
      loadingComponent: Loading,
      errorComponent: Error,
    },
    "/posts/:id": {
      asyncComponent: () => import("./routes/Post.svelte"),
      loadingComponent: Loading,
    },
    "/users": {
      asyncComponent: () => import("./routes/UsersOverview.svelte"),
    },
    "/2-seconds": {
      asyncComponent: () => import("./routes/TwoSeconds.svelte"),
    },
    "/5-seconds": {
      asyncComponent: () => import("./routes/FiveSeconds.svelte"),
    },
    "/users/:userId": {
      asyncComponent: () => import("./routes/User.svelte"),
    },
    "/users/:userId/*": {
      asyncComponent: () => import("./routes/User.svelte"),
    },
    "/pictures": {
      asyncComponent: () => import("./routes/Pictures.svelte"),
    },
    "*": Fallback,
  };
</script>

{#if loading}
  <progress />
{/if}

<h1>Svelte tourer</h1>

<button on:click={back}>Back</button>
<button on:click={forward}>Forward</button>

<nav>
  <ul>
    <li><a href="/" use:link>Home</a></li>
    <li><a href="/about" use:link>About</a></li>
    <li><a href="/about#toto" use:link>About to title</a></li>
    <li><a href="/posts" use:link>Posts</a></li>
    <li><a href="/users" use:link>Users</a></li>
    <li><a href="/2-seconds" use:link>2 seconds</a></li>
    <li><a href="/5-seconds" use:link>5 seconds</a></li>
    <li><a href="/users" use:link>Users</a></li>
    <li><a href="/pictures" use:link>Pictures with error</a></li>
    <li><a href="/todos/">Todos</a></li>
    <li><a href="https://www.google.com">Google</a></li>
  </ul>
</nav>

<Router
  {routes}
  on:navigationStart={() => (loading = true)}
  on:navigationFinish={() => (loading = false)}
  loadingComponent={GlobalLoading}
  errorComponent={GlobalError}
/>
