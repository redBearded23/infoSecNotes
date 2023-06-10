# Python Basics

## Basics

- indentation in python is very important; everything that is indented is considered part of the function/loop/if etc. --> interpreter uses formatting to determine how code is grouped
- all the statements with the same space to the right, belong to the same code block
- default indentation in python is 4 spaces, however, the number of spaces is up to you, but a minimum of 1 space has to be used
- to get input from command line Python uses the function: `sys.argv`
- comments:
  - Single line: `#`
  - Multiline Starting and Ending: `" "`

## Useful pentesting libraries

- `Request` - simple HTTP library
- `Scapy` - send, sniff, dissect and forge network packets, best bet for packet generation and manipulation in Python
- `Pwntools` - CTF & exploit development library

## Package Installation with `pip` --> Python Package Manager

- Python Package manager specifically for installing and managing Python packages

  - in Kali included
  - if installation necessary: `apt-get install python3-pip`
  - install package with pip: `pip3 install <package name>`
  - when downloaded, the packages are usually automatically placed in: `/usr/local/lib/<python version>/dist-packages` directory
  - if not found here: `pip3 show <package name>` to show details for package

## Installing Packages without `pip`

- if pip is not used, you can install packages like this:

  - download package i.e. with wget
  - copy files to `/usr/local/lib...` (siehe oben)
  - run `python3 setup.py install` --> installs all not yet installed unpacked packages

## Examples

- to format string literals Python uses f-Strings (see: [https://peps.python.org/pep-0498/](https://peps.python.org/pep-0498/)):

```python
name = "Eric"
age = 74
f"Hello, {name}. You are {age}." # prints: Hello, Eric. You are 74.
```

```python
# Text ausgeben
print("foobar") # Beachte kein Zeilenabschlusszeichen!!

#Variablen deklarieren
food = "ice cream"
money = 2000
# Arrays/Lists deklarieren
websites = ["facebook.com","google.com","amazon.com"]
```

## Math-Operators

| Operator | Description    | Example      |
| -------- | -------------- | ------------ |
| +        | Addition       | `1 + 2 = 3`  |
| -        | Substraktion   | `3 - 2 = 1`  |
| \*       | Multiplikation | `3 * 2 = 6`  |
| /        | Division       | `6 / 2 = 3`  |
| %        | Modulus        | `10 % 2 = 0` |
| \*\*     | Exponent       | `3 ** 2 = 9` |
| -        | Substraktion   | `3 - 2 = 1`  |
| -        | Substraktion   | `3 - 2 = 1`  |

## Logical Operators

| Operator | Description              |
| -------- | ------------------------ |
| >        | Greater than             |
| <        | Less than                |
| ==       | Equal to                 |
| !=       | Not equal to             |
| >=       | Greater than or equal to |
| <=       | Less than or equal to    |

## Boolean Operators

| Operator | Example                |
| -------- | ---------------------- |
| AND      | if x >= 5 AND x <= 100 |
| OR       | if x == 5 OR x == 100  |
| NOT      | if NOT y               |

## If-Then Else

```python
name = "bob"
hungry = True
if name == "bob" and hungry == True:
    print("bob is hungry")
elif name == "bob" and not hungry:
    print("Bob is not hungry")
else: # If all other if conditions are not met
    print("Not sure who this is or if they are hungry")
```

## Loops

```python
# While
i = 1
while i <= 10:
    print(i)
    i = i + 1

# For
websites = ["facebook.com", "google.com", "amazon.com"]
for site in websites:
    print(site)
```

## Functions

```python
def sayHello(name):
    print("Hello "+name+"! Nice to meet you.")

sayHello("Ben")

# Funktion mit Rückgabewert
def calcCost(item):
    if(item == "sweets"):
        return 3.99
    elif (item == "oranges"):
        return 1.99
    else:
        return 0.99

spent = 10
spent = spent + calcCost("sweets")
print("You have spent:" + str(spent))
```

## Files

```python
# Read files (reading = r)
# r stands for read; Alternative: readlines() and loop over each line in the file
f = open("file_name", "r")
print(f.read())
# Write to existing files (appending = a)
# when writing to a existing file, first open the file,
# then use the open-Method with the "a" 8append) as second argument
f = open("demofile1.txt", "a") # Append to an existing file
f.write("The file will include more text..")
f.close()
# Create/Write to a new file (writing = w)
f = open("demofile2.txt", "w") # Creating and writing to a new file
f.write("demofile2 file created, with this content in!")
f.close()
```

## Imports

- we import other libraries using the `import` keyword
- then we use that imports library name to reference its objects/functions etc.

```python
import datetime
current_time = datetime.datetime.now()
print(current_time)
```

## Other Stuff

...
