<script lang="ts" context="module">
  import type { PathParams } from "../src/lib/models/params";
  import z from "zod";

  export const todosSearchParamsSchema = z.object({
    page: z.number().optional(),
    offset: z.number().max(10).optional(),
  });

  type TodosSearchParams = z.infer<typeof todosSearchParamsSchema>;

  export async function loadData(
    pathParams: PathParams,
    searchParams: TodosSearchParams
  ): Promise<Todo[]> {
    const rawTodos = await fetch("https://jsonplaceholder.typicode.com/todos");
    const todos = (await rawTodos.json()) as unknown as Todo[];

    const { page, offset } = searchParams;

    const definedPage = page ?? 1;
    const definedOffset = offset ?? 10;
    const startIndex = (definedPage - 1) * definedOffset;
    const endIndex = definedPage * definedOffset - 1;
    return todos.filter((_, index) => index >= startIndex && index <= endIndex);
  }
</script>

<script lang="ts">
  import { link } from "../src/lib/Router.svelte";
  import type { Todo } from "../src/models/todo";

  export let data: Todo[];
  export let searchParams: TodosSearchParams;
</script>

<h1>Todos</h1>

<ul>
  {#each data as post}
    <li>
      <a href="/todos/{post.id}" use:link>{post.title}</a>
    </li>
  {/each}
</ul>

<a
  href={`/todos/?page=${(searchParams.page ?? 1) - 1}&offset=${
    searchParams.offset ?? 10
  }`}
  use:link>Previous</a
>

<a
  href={`/todos/?page=${(searchParams.page ?? 1) + 1}&offset=${
    searchParams.offset ?? 10
  }`}
  use:link>Next</a
>
