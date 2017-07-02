## Generators

#### 1. Understand generators in python

When you use `send` and expression `yield` in a generator, you're treating it as a coroutine; a separate thread of execution that can run sequentially interleaved but not in parallel with its caller.When the caller executes `R = m.send(a)`, it puts the object `a` into the generator's input slot, transfers control to the generator, and waits for a response. The generator receives object `a` as the result of `X = yield i`, and runs until it hits another `yield` expression e.g. `Y = yield j`. Then it puts `j` into its output slot, transfers control back to the caller, and waits until it gets resumed again. The caller receives `j` as the result of `R = m.send(a)`, and runs until it hits another `S = m.send(b)` statement, and so on.`R = next(m)` is just the same as `R = m.send(None)`; it's putting `None` into the generator's input slot, so if the generator checks the result of `X = yield i` then `X` will be `None`.As a metaphor, consider a [dumb waiter](http://www.flickr.com/photos/duncan/7458208/)



When the server gets an order from a customer, they put the pad in the dumb waiter, `send` it to the kitchen, and wait by the hatch for the dish:`R = kitchen.send("Ham omelette, side salad")`The chef (who's been waiting by the hatch) picks up the order, prepares the dish, `yield`s it to the restaurant, and waits for the next order:`next_order = yield [HamOmelette(), SideSalad()]`The server (who's been waiting by the hatch) takes the dish to the customer and returns with another order, etc.Because both the server and chef wait by the hatch after `send`ing an order or `yield`ing a dish, there's only one person doing anything at any one time i.e. the process is single threaded. Both sides can use normal control flow, as the generator machinery (the dumb waiter) takes care of interleaving execution.

