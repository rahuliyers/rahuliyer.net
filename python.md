# Python

## Introduction

This was originally intended to be a simple tutorial for persons already having some programming experience in other programming languages and having an understanding of general programming concepts and computer architecture, but now its simply just my notes for revising Python. The reader should be comfortable looking up documentation online, and setting up his own environment prior to proceeding.

## Hello

In python, you can write a simple program to print the text "Hello" in one line:

```
print("Hello")
```

Make sure you type your code exactly like you see above, matching the case / capitalisation of all letters exactly.

When you run your program, you should see the text "Hello World", printed to the console.
Congratulations you've run your first python program!

Lets take a look at this a little closer. *Print* is a built-in python function. *Print" is the name of the function - it is used to represent code that allows us to print text to the console. In this case, the *print* function needs to know what text we wish to print - we specify this within *()*. Whatever we intend to pass to a function, is placed within the *()*.

You will notice that the message printed to the console is *Hello*, without the quotes. When we directly pass text to a function, we need to enclose it within single quotes or double quotes.  This is necessary for a number of reasons. For now, just follow along, and it will become clear why later in the course.

## Variables

To store values, we use *variables* for storage.
In python, one doesn't need to specify the variable type. One can store a variable by simply doing the following:

```
greeting = "Hello"
```

In the above example, the variable name is *greeting* , and the value stored in it is *Hello* . We use the *=* symbol for assigning a value to the variable.

We can then use the variable anywhere we would have use the raw text "Hello". For example, we can do the following:

```
greeting = "Hello"
print(greeting)
```
Run your program. You will see that "Hello" is printed to the console. You can use variables to store values that you need to use in different parts of your program. In this case, the *print* function uses the value of *greeting* when printing to the console.

## Rules for naming variables

There are some rules to naming variables -

- Variable names can contain only letters, numbers and underscores.

- Variable names can start only with a letter or underscore, and not with a number. For example, you can name your greeting variable as *greeting1* but not *1greeting*

- You may not use spaces in variable names. But you can use underscores to separate words in variable names. This means you can name variables like *diwali_greeting* or *christmas_greeting*, but naming them as *diwali greeting* or *christmas greeting* is not allowed because of the space.

- There are some words that are reserved for usage by the python language. You cannot use these keywords for variable names. A complete list of these keywords is:

| Keywords in Python|||||
|-----|-----|-----|-----|-----|
| False	| await	| else	| import	| pass |
| None	| break	| except	| in	| raise |
| True	| class	| finally	| is	| return |
| and	| continue	| for	| lambda	| try |
| as	| def	| from	| nonlocal	| while |
| assert	| del	| global	| not	| with |
| async	| elif	| if	| or	| yield |

- You may NOT use any of the keywords in the above list for variable or function names. We will learn what each of these keywords means later on .

## Variable Naming Style and Convention

Programmers tend to follow a naming convention for uniformity, and to make sure their code is readable and manageable by fellow programmers. These conventions may vary from language to language, between companies, or between projects or groups of developers. The important thing is, that whatever convention you use, you stick to it within your project or code base, so that all of your code follows a single convention. If working with other programmers, make sure that you have agreed what the convention should be.

- Aside from conventions, try to keep your variable names as short and descriptive as possible.
- Short : To reduce the amount of typing you have to do, and to save space on screen
- Descriptive : So that it is easy for any fellow programmer reading your code, to understand what the purpose of your variable or function is.
- For now, use lowercase letters in the names for all your variables, and avoid uppercase till later on in the course.

## Common Errors:

Try running the following code:

```
greeting = "Hello"
print(greetin)
```

What happens ? You should see an error in your console. From looking at the error message can you understand why?

The answer is that there is a spelling mistake in the variable name used in the *print* statement. Instead of using *greeting* we used *greetin*. Correct the spelling mistake and run the code again.

```
greeting = "Hello"
print(greeting)
```
This time your code should run without problem. Try the following:
```
greting = "Hello"
print(greting)
```
Hmm. This code seems to run without problem either! But wait, didn't we spell *greeting* wrong ? The thing you need to understand is, when using variables, you use the same spelling to refer to the same variable throughout your program, so that the python interpreter knows which variable you are referring to.

In the earlier case, there was an error, because the variable was declared as *greeting* and in the print statement we used *greetin*. When the interpreter saw *greetin*, it didnt know which variable you are refering to, since there was no variable names *gretin*

In the second case, although we have spelt the variable *greting*, we use the same spelling both when creating the variable, as well as in the print statement, so the Python Interpreter knows we are talking about the same variable.

- It is important you take care to use the same spelling of your variable throughout your program to avoid the errors like the kind described above.

Try the following:
```
diwali_greeting = "Happy Diwali"
christmas_greeting = "Merry Christmas"
print(diwali_greeting)
print(christmas_greeting)
```
## Strings

There are different kinds of variables for storing different kinds of data. A *string* is simply a sequence of characters. To create a string, one simply encloses characters within single or double quotes.

```
'Here is an example'
"Here is another example"
```

Hmmm, but what if we need to use double quotes or single quotes within our String ? Then we can do something like this:

```
'Here is an example where I use a double quotes inside my  "variable" '
'Here is an example where I use a single quotes inside my  'variable' '
```

If you try to use quotes improperly, you will have a problem. For example, if you try to do this:
```
"This is an example of using " quotes within quotes" which will not work."
```
If you try the above, the interpreter will treat "This is an example of using" as a string, and will not know what to do with "quotes within quotes" as the string has already ended.

To fix this problem, you could *escape* the additional double quotes, so the interpreter treats them as part of the string:

```
"This is an example of using \" quotes within quotes\" which will work."
```

Throughout this course you will learn about different kinds of *escape characters* which are used within strings to accomplish various purposes.


## Useful things you can do with String Variables.

Try running the following piece of code:

```
my_name ="john smith"
print(my_name)
```

The output will read 'john smith'. As 'john smith' is a name, wouldnt it be nice to print it as 'John Smith' instead. We could either modify the text from 'john smith' to 'John Smith', or do the following:

```
my_name ="john smith"
print(my_name.title())
```

The output this time is 'John Smith'. What did we just do? Well string variables have a number of built in *functions* within them that let you do different things to the data stored in the variable. What is a *function* anyway ? Well a function is a piece of code that *does something*, and which is referred to by a *name* for convenience, so we don't have to type in the entire piece of code each time, and simply use its name.

In the above code example, the name of the *function* we used is *title*. Pay attention to the syntax:
```
my_name.title()
```

Immediately after *my_name*, there is a *.* . This *.* allows us to access all the built-in functions within the string variable *my_name*. Next comes the text *title*, which is the name of the function we intend to call. String variables have many different built-in functions, so to tell the interpreter which function to call, we must provide the correct name. The last part is the parentheses or *()*. We will explain the purpose of this at a later time when we focus on functions in detail, but for now just understand that to call a function you must include the *()*, after the function name.

```
my_name ="john smith"
print(my_name.title())
```

There are many built in functions. Try the following code snippet:

```
my_name ="john smith"
print(my_name.capitalize())
print(my_name.upper())
```

*capitalize* simply turns the first character of the string into upper case, while *upper* turns the entire string into upper case.

```
my_name ="john smith"
print(my_name.capitalize())
print(my_name.upper())
print(my_name)
```

The output is:
```
John smith
JOHN SMITH
john smith
```

You will notice, that calling *capitalize* or *upper* does not change the variable value itself, so when we call *print(my_name)*, it prints the original unmodified string. When we call functions like *capitalize* or *upper* , the function is returning a new modified string, and the print function is printing the new string. The original string is unmodified and intact, which is very important to remember.

What if we wanted to store the modified string in my_name ? We would do something like this:

```
my_name ="john smith"
my_name = my_name.capitalize()
print(my_name)
my_name = my_name.upper()
print(my_name)
```

If you run the above code, you should get output like this:

```
John smith
JOHN SMITH
```
Take a look at his line:

```
my_name = my_name.capitalize()
```

my_name.capitalize() creates a new string that is a capitalized version of *my_name*. By assigining it back to my_name, my_name now stores the new value. So when we call *print(my_name)*, we see the new capitalized my_name.

```
my_name = my_name.upper()
print(my_name)
```
Similarly, in the above code, we are assigning the new string created by my_name.upper() back to my_name, so when we call *print(my_name)*, we get the newest string *JOHN SMITH*

You've just learned how to change the value of a variable.

## Concatenating Strings

**Concatenating** is a fancy word used to describe the process of combining two strings.

For example, consider the following code:

```
first_name = "john"
last_name = "smith"
```
How would one **CONCATENATE** `first_name` and `last_name` to create a new variable `full_name` with the value **John Smith** ? We would do this:

```
first_name = "john"
last_name = "smith"
full_name = first_name + last_name
print(full_name)
```

The output would be:

```
johnsmith
```

Wait a minute. There is no space between the first and last name ! That's not what we want. Lets add the space:

```
first_name = "john"
last_name = "smith"
full_name = first_name + " " + last_name
print(full_name)
```
Ouput:

```
john smith
```

Much better! Lets look at our concatenation again:

full_name = first_name + " " + last_name

The **+** symbol is used to concatenate two strings. It combines first_name with **" "** and last_name to create a new string, which we assign to full_name.

Lets try something more:

```
first_name = "john"
last_name = "smith"
full_name = first_name + " " + last_name
print("My name is: " + full_name.title() + " !!")
```

Output:

```
'John Smith'
```
As you can see, the code within the *()* of the print function is evaluated, and its the ouput of executing the code that is passed as *input* to the **print()** function which prints it to the console.


### Whitespace

What is **Whitespace**? Well Whitespace is just the empty space around strings. You can add whitespace to a string using *spaces, tabs or newline* characters. Let's try it out:

First without any whitespace at all:

```
print("There is no whitespace here")
```

Output:

```
There is no whitespace here
```

Now lets try adding some space before the text:

```
print("    There are four blank spaces before this text")
```

Output:

```
    There are four blank spaces before this text
```

Let's try tabs.

```
print("\tA solitary tab before this text")
```

Output:

```
    	A solitary tab before this text
```

A tab is just used for formatting. In the above example we used **\t** to include a solitary tab in our string. By using **\* before the **t**, we told the interpreter to **escape** the next character (in this case **t**). When the interpreter saw the **t** had been **escaped**, it treated it as a **tab** instead of a regular **t**, and added the necessary space.

Likewise, we can add new lines.

```
print("The sentence after this is going to be printed on a new line.\nThis sentence is on a new line.")
```

Output:

```
The sentence after this is going to be printed on a new line.
This sentence is on a new line.
```

In this case, the **\n** in the string tells the interpreter to insert a *new line* at the location of **\n**. This is another example of where an escape character has been used, to modify a string.
