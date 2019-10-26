# Intro-to-Elixir

## What is Elixir?
Elixir is a dynamic, functional language designed for building scalable and maintainable applications.

Elixir leverages the Erlang VM, known for running low-latency, distributed and fault-tolerant systems, while also being successfully used in web development and the embedded software domain.

## Pattern Matching
Pattern matching is a powerful part of Elixir. It allows us to match simple values, data structures, and even functions. In Elixir, the = operator is actually a match operator, comparable to the equals sign in algebra. Writing it turns the whole expression into an equation and makes Elixir match the values on the left hand with the values on the right hand. If the match succeeds, it returns the value of the equation. Otherwise, it throws an error. 

```elixir
iex> list = [1, 2, 3]
iex> [1, 2, 3] = list
[1, 2, 3]
iex> [] = list
** (MatchError) no match of right hand side value: [1, 2, 3]

iex> {:ok, value} = {:ok, "Successful!"}
{:ok, "Successful!"}
iex> value
"Successful!"
iex> {:ok, value} = {:error}
** (MatchError) no match of right hand side value: {:error}
```

## GenServers
A GenServer is a process like any other Elixir process and it can be used to keep state, execute code asynchronously and so on. The advantage of using a generic server process (GenServer) implemented using this module is that it will have a standard set of interface functions and include functionality for tracing and error reporting. It will also fit into a supervision tree.

### GenServer Callbacks
```elixir
def handle_call(:view, _from, state) do
  {:reply, returned_value, new_state}
end
```

```elixir
def handle_cast({:push, element}, state) do
  {:noreply, [element | state]}
end
```
### Basic GenServer Example

```elixir
defmodule Stack do
  use GenServer

  # Callbacks

  @impl true
  def init(stack) do
    {:ok, stack}
  end

  @impl true
  def handle_call(:pop, _from, [head | tail]) do
    {:reply, head, tail}
  end

  @impl true
  def handle_cast({:push, element}, state) do
    {:noreply, [element | state]}
  end
end

# Start the server
{:ok, pid} = GenServer.start_link(Stack, [:hello])

# This is the client
GenServer.call(pid, :pop)
#=> :hello

GenServer.cast(pid, {:push, :world})
#=> :ok

GenServer.call(pid, :pop)
#=> :world
```

Now let's see it in action!

## Supervisors and Workers
You have processes that do things - worker processes. There may be many workers of a type - all the same - but there may also be many types of workers. Workers perform the computation and tasks.

Overseeing the worker processes are supervisor processors - and overseeing the supervisor processes are supervisor processes (turtles all the way up, except for the top one who's the boss!)

Supervisors have 2 main jobs:
1.Look out for their workers (if they start crashing restart them in the way that that type of worker needs to be restarted)
2. If too many workers crash too often report up the line to their supervisors (by crashing and letting their supervisor restart them in the way that they need to be restarted)

# Let's take a look at an application with a Supervisor/Worker structure!
