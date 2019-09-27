# 6: Stack and its applications

![Imgur](http://i.imgur.com/vi9bS6x.jpg)
[ Hey, a burger is a stack right?]

WHEW that last section took a lot of work, didn't it? Well you've made it halfway
through the Zoo! Now that you've conquered linked-lists let's move on to a
data structure called the Stack. It's a LIFO (Last in, First out) structure because
the first object gets to leave the structure first. Also, Stacks are called access-limited
because there's no ability to get an item at a certain index, all you have access to
is the top of the stack!

Before we jump in with some nice ASCII diagrams, let's talk functions. A Stack has
a few simple methods that we must implement. ``push()``, ``peek()``, ``pop()``,
``is_empty()``, and ``__len__()``. We've seen ``__len__()`` before, and ``is_empty()``
goes along nicely with the aforementioned. However, what's all this pushing and popping
and peeking about? Let's first take a look at ``push()``. Imagine the stack like a
PEZ dispenser.

```
    |    |
    |    |  <- Stack
    |    |
    ------
```

Let's ``push()`` the number 42 and the number 21 and see what happens!
```
      42                    21
      \/                    \/
    |    |     |    |     |    |     |    |
    |    |  >  |    |  >  |    |  >  | 21 |
    |    |  >  | 42 |  >  | 42 |  >  | 42 |
    ------     ------     ------     ------
```

The 42 "falls" to the bottom of the stack, then the 21 sits on top. Can you guess
what will happen when we call ``pop()``?
```
                 21                    42
                 /\                    /\
    |    |     |    |     |    |     |    |
    | 21 |  >  |    |  >  |    |  >  |    |
    | 42 |  >  | 42 |  >  | 42 |  >  |    |
    ------     ------     ------     ------
```

The order that we pushed the numbers into the stack was the opposite of the order
we popped them. (p.s. We can use ``peek()`` to see what's on top) Before we get
into the applications that a Stack object, we'll talk about the implementation of
one. There are two ways to implement a stack; one being with a list, the other with
a linked-list. We'll use a linked-list because Python's built in list operations make
it too easy. Typically we'd just follow DRY principles and just use List, but you're
supposed to be learning, not writing enterprise code (just yet!).

Let's start by making a top node, a tail node, and a size. Did you forget linked-lists
already? Let's pull up that diagram again. I'll show you what the sequence of events
looks like for the example that we did earlier. It's a slightly modified ``append()`` 
except we insert our new variable at the top.

```
    # push(42)
    self.top->[42]->None

    # push(21)
    self.top->[21]->[42]->None

```

That way the ``self.top`` variable points to the top of our stack! If you're clever, we can
use ``self.top`` to easily implement our ``peek()`` method. ``pop()`` is even easier, we simply
just remove the current top value and update our ``self.top`` variable to point to the next
value.

### Applications of Stack

At this point you know all you need to know about stacks. You're free to move on, however, you'd be missing out
on all the cool things that Stacks can do! First world changing application, parenthesis. Have you ever been programming
and went to compile and it wouldn't work because you were missing a bracket? OF COURSE NOT, this is Python! It's still a
problem that can be solved with Stacks.

But first, I highly urge you to run the following in your Python interpreter:
```python
    >>> from __future__ import braces
```

Funny right? Alright let's look at what a "well-formed" set of parenthesis looks like. "{[(({}[]))]}" Notice how 
every opening parenthesis has a closing one? "{{()}[]}}]}" This one just looks terrible. Now let's figure out an
algorithm to break this problem down.

We're going to first iterate over the entire parenthesis string. Easy enough, use a ``for char in string:`` and you
should be good. Notice the ``brackets`` dictionary, we establish the relationship between opening
and closing parenthesis. We can also iterate over both keys and values! So for each character we
check to see if it's in the keys first, if so we push it.

```python
    # See stack.py for a more commented version
    def check_parenthesis(string):
        stack = Stack()
        brackets = {'{':'}', '[':']', '(':')'}
        for character in string:
            if character in brackets.keys():
                stack.push(character)

            if character in brackets.values():
                try:
                    other = stack.pop()
                    if brackets[other] != character:
                        return False

                except ValueError:
                    return False
        if stack.size != 0:
            return False
        else:
            return True
```

Don't worry if it looks intimidating! If it's a closing parenthesis, we check to see 
that it has a matching pair on the ``Stack`` by calling ``pop()`` and using our neat
lookup dictionary to see if it matches. That's essentially the algorithm. You'll see at
the end of the method that we have a check to see that  the stack is empty. This ensures
that we don't have any lonely parenthesis left with no partner. 

Moving on to another fun application: postfix evaluation. You're familiar with infix notation
for mathematical equations ``(2 + 3) / 5``, in post fix it's simply ``2 3 + 5 /``. The algorithm
for evaluating a postfix can be done easily with our ``Stack`` object. The algorithm goes like
this: We scan our string one by one, if it's a number we push it immediately onto a stack. If we 
encounter an operator we'll pop the two items off the stack and evaluate them using the operator,
then we'll push the value back onto the stack. Below is the source, and as always feel free to read
the comments and break things in ``stack.py``

```python
def postfix_eval(string):
    stack = Stack()
    ops = {'+': lambda a, b: a + b,
           '-': lambda a, b: a - b,
           '*': lambda a, b: a * b, 
           '/': lambda a, b: a / b,
           '%': lambda a, b: a % b,
           '^': lambda a, b: a ** b,}
    tokens = string.split(' ') 

    for character in tokens:
        if character in ops.keys():
            try:
                right = stack.pop()
                left = stack.pop()
                result = ops[character](left, right)
                stack.push(result)
            except ValueError e:
                raise e
        else:
            try:
                value = int(character)
                stack.push(value)
            except ValueError e:
                raise e
    if stack.size > 1:
        raise ValueError()
    return stack.pop()
```

Before we move on to Queues, I'll note a few Pythonic things about the method above. First,
the dictionary with our operations. It takes advantage of ``lambda``, without going into too
much detail (that's for another repo), it allows us to map the character '+' to the actual add
operation. If you're not familiar with the ``try: except`` statements, they're there to catch
exceptions we raised in the stack and problems parsing characters.

That's it for Stacks, now onto Queues!