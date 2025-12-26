# print()
- To print message on the screen #output
```python
print("Hello world")
```

- To print text multiple time, let say 10 times
```python
print("hello "*10)
```

- To make a comment
```python
#This is my favourite food
print("I like pizza")
```
---
# Variables
- Variable is a container that store any type of values (String, integer, float, Boolean) and it represents this value
```python
first_name = "Saif" #String variable
```

- We can use F-format to type a string and include variables within it
```python
first_name = "Saif"
print(f"Hello {first_name}")
#{} those parentheses called place holder 
```

- To make an integer variable 
```python
age = 25 #integer variable
price = 10.5 #float variable
is_student = True #Boolean variable
print(f"my age is {age}")
print(f"The price of banana is {price}EGP")
print(f"Are you a student?: {is_student}")
```

- The best usage of the Boolean variable is in conditions
```python
#The if condition will be disscused later
is_student = True
if is_student:
	print("you are a student")
else:
	print("you are Not a student")
```
---

# Type casting
- Type casting: The process of converting one data type to another using some functions like *str()*, *int()*, *float()*,*bool()*
- you can determine the type of variable by using a built-in method called type()
```python
name = "saif"
print(type(name))
```

- Let's see how we can convert some data types
```python
name = "saif"
age = 23
GPA = 3.5
is_student = True
GPA = int(GPA)
print(GPA) # output : 3
print(float(age)) # output : 23.0
age = str(age)
print(age) # output : 23.0 , it looks to be float but it is string
print(type(age)) # output : <class 'str'>
```

- How can change integer variable or float to string be also noticed ? try to add 1 to the variable after converting it to string.. You will get an error 
```python
price = "25"
price += 1 
```

![[Pasted image 20251119231808.png]]

- so what if I tried to add string to string ? This is called concatenation
```python
first_name = 'Saif'
last_name = 'Omran'
print(first_name+" "+last_name) # output: Saif Omran
```

- Let's see how the typecasting work with bool()
```python
name = "Saif"
name = bool(name)
print(name) # output: True

```

>The *bool()* will always return True if there is any string except the string is empty, This is useful to check if the user type a name while registering on the website for example.

```python
name = "Saif"
name = bool(name)
if not name:
	print("Please enter your name")
```
---
# input()
- This function is used to take input from the user

>The *input()* returns data as string

```python
name = input("Please enter your first name: ")
print(f"Hello, {name}")
```

- To clarify that the input method returns string, let's see this example
```python
age = input("Enter your age, please: ")
age = age + 1 #Error
print(age)
```

![[Pasted image 20251120005829.png]]

```python
age = int(input("Enter your age, please: "))
age = age + 1 
print(age) 
#output
#Enter your age, please: 25
#26
```
---

- Example of typecasting with F-formatting, print(), input()
```python
width = float(input("Enter the width of the rec: "))
length = float(input("Enter the length of the rec: "))
area = width * length
print(f"The area: {area} cm²")
```
---
# Arithmetic operations
```python
age = 20
age = age + 1 #Nomal way to add
age += 1 #Add with augmented operator

```

>It is the same with the subtraction - multiplication * division / 

-The power operator
```python
number = 10
number = number ** 2
print(number) #output: 100
```

>You can use the augmented operator -> number ** = 2

```python
x = 3.14
y = -4
z = 5
result = round(x)  # 3
result = abs(y)  # 4
result = min(x, y, z)  # -4
result = max(x, y, z)  # 5
result = pow(4, 3)  # 64
```

>The pow() method takes 2 parameters -> base and power respectively

>There is a module (built-in library) in python called "math" which contains mathematical constants like e, pi and also some useful methods.

>Before use any module in python, you have to import it using *import* 

```python
import math
print(math.pi) #output: 3.141592653589793
print(math.e) #output: 2.718281828459045
x = 9.1
print(math.ceil(x)) #output: 10
y = 9.999
print(math.floor(y)) #output: 9
z = 25
print(math.sqrt(z)) #output: 5
```
---
# If condition (if else)
>If condition is a control statement that decides the flow of the code, if the condition is True do something and if not True do something else.

```python
age = input("Enter your age: )
if age >= 18:
	print("you are signed up")
else:
	print("you are below 18 years old")
```

- We can make more than condition before *else* using *elif*

```python
age = input("Enter your age: )
if age >= 18:
	print("you are signed up")
elif age < 0:
	print("you have not been born yet!")
else:
	print("you are below 18 years old")
```

>The condition are tested by their arrangement, so you have to make sure to arrange them correct logically

```Python
age = input("Enter your age: ) 
if age >= 18:
	print("you are signed up") 
elif age >= 100:
	print("you are too old")
else:
	print("you are below 18 years old")
```

- In the previous example if the user types the age 120 for example the printed statement will be "you are signed up" even that the second condition is true, but the first condition is true also, so the arrangement is very important to get the right answer as it is only one condition that is executed and if no condition is executed the *else* statement will be executed

>When you have a condition, and you want to check it, we use == comparison operator

```python
#simple program to print if the number is even or odd
num = int(input("Enter the number: "))
if num % 2 == 0:
    print("The number is even")
else:
    print("The number is odd")
```

- How if statement works with Boolean variable
```python
#program to see if the item is for sale or not
for_sale = True
if for_sale:
	print("This item is available")
else:
	print("This item is not for sale")

#output: This item is available
```

```python
#program to see if the item is for sale or not
for_sale = Flase
if for_sale:
	print("This item is available")
else:
	print("This item is not for sale")
	
#output: This item is not for sale
```
---
# Logical operators (or, and, not)

>These operators are used to check multiple conditions.
>or : at least one condition must be true.
>and: all conditions must be true.
>not: inverse the condition.

- Example for *OR* operator (one condition is enough)
```python
age = 20  
has_license = False  
  
if age >= 18 or has_license:  
	print("You can drive or are eligible for a license.")  
else:  
	print("You cannot drive and are not eligible for a license.")

```

- Example for *AND* operator (all or nothing)
```python
age = 20
have_ticket = True

if age > 18 and have_ticket:
	print("You can enter the show")
else:
	print("you are not allowed to enter the show")
```

- Example for *NOT* operator
```python
age = 20
have_license = Flase
 if age > 18 and not have_license:
	 print("you can NOT drive")
```
---
# Conditional expression
- instead of typing the if else statement as we have seen, we can type it in one line to print statement or to assign one of two values to a variable.

> X *if* condition *else* Y 

- Example
```python
num = 6
print("Positive" if num > 0 else "Negative")
```
- here we can print statement and test the condition in one line.

- Example
```python
a = 5
b = 7
max_num = a if a > b else b
print(max_num)
```
- here, we assigned the bigger number to the variable *max_num* by testing the condition in the same line of code.
---
# String methods
>String methods are built-in functions or procedures associated with string data types in various programming languages, allowing for manipulation and analysis of strings.

### len()
>len() method is used to count the numbers of the string including the spaces.
```python
name = "saif omran"
print(len(name)) # output : 10
```

### find()
>find() method is used to return the position number of the first occurrence of a character and it returns -1 if the character is not existing
#important_note : python is a zero-based counting language, it means that the first position is 0 and so on.
```python
name = "saif omran"
name = name.find("a")
print(name) # output : 1
```

### rfind()
>rfind() method is used to return the last occurrence of a character, and it returns -1 if the character is not existing
```python
name = "saif omran"
print(name.rfind("a")) #output : 8
```

### capitalize()
>capitalize() method is used to make the first letter of the string capital.
```python
name = "saif omran"
print(name.capitalize()) #output : Saif omran
```

### upper(), lower()
>upper() method makes all character of the string capital
>lower() method makes all character of the string small
```python
name = "saif omran"
gender = "MAle"
print(name.upper()) #output: SAIF OMRAN
print(name.lower()) #output: male
```

### isdigit()
>isdigit() method is used to check that all the string is number or not (Boolean method)
```python
phone_no = "123456"
print(phone_no.isdigit()) #output: true
num = "123 456"
print(num.isdigit()) #output : Flase 
num = "123H456"
print(num.isdigit()) #output : Flase
```

>space is not digit nor alphabet

### isalpha()
>isalpha() method is used to check that all the string is characters or not (Boolean method)
```python
name = "saifomran"
print(name.isalpha()) #output : True
name = "saif omran"
print(num.isdigit()) #output : Flase 
name = "saifomran12"
print(num.isdigit()) #output : Flase 
```

### count()
>count() method is used to count specific character in the string

```python
name = "saif aldeen atef omran"
print(name.count("a")) #output : 4
```

### replace()
>replace() method is used to change one character in a string with different one

```python
phone_no = "1-234-567-890"
print(phone_no.replace("-","")) #remove "-"
#output : 1234567890
```
---
# indexing
>[start : end : step]

Indexing in Python refers to the process of accessing individual elements within a sequence (like strings, lists, or tuples) using their position, known as an index.

- Example of indexing
```python
my_string = "Python"  
print(my_string[0]) # Output: P  
print(my_string[4]) # Output: o
  ```
  
- Example of negative indexing
```python
my_string = "Python"  
print(my_string[-2]) # Output: o
```

- Example of substring
```python
my_string = "Python"  
print(my_string[0:3]) # Output: Pyt
```
 >The end index is NOT included

- Example of step in indexing
```python
my_string = "Python is good"
print(my_string[0:10:2])  # Output: Pto s
```

- To make it easier if we want the string from its beginning we will not type any number in the start, same for the end of string
```python
my_string = "Python is good"
print(my_string[:10:2])  # Output: Pto s

my_string = "Python is good"
print(my_string[5::2])  # Output: ni od

my_string = "Python is good"
print(my_string[::2])  # Output: Pto sgo
```

- To reverse string set the step = -1
```python
num = "123456789"
print(num[::-1]) #output : 987654321
```
---
# format specifier
>format specifiers are used within string formatting methods to control how values are displayed

```python
    print(f"{123:d}")  # Output: 123
    print(f"{3.14159:.2f}")  # Output: 3.14, it rounds the result by default
    print(f"{'hello':s}")  # Output: hello
    print(f"{255:x}")  # Output: ff
    print(f"{255:X}")  # Output: FF
    print(f"{'text':<10}")  # Output: text
    print(f"{'text':>10}")  # Output:      text
    print(f"{'text':^10}")  # Output:   text
    print(f"{123:*^10}") # Output: ***123****
    print(f"{123:05}")  # Output: 00123 (zero-padding), 5 means the total digits after padding
    print(f"{10:+d}")   # Output: +10
    print(f"{-10:+d}")  # Output: -10
    print(f"{10: d}")   # Output:  10
    print(f"{12345678:,d}") # Output: 12,345,678
```
---
# While loop()
>While loop executes code WHILE some condition is True

```python
name = input("Enter your name: ")
while name == "":
	print("you did not enter your name.")
	name = input("Enter your name: ")
print(f"Hello {name}")
```

- In the previous example if the user did not enter the name the program will print "you did not enter your name" and will also output "Enter your name: " again waiting for input until the user types any input the program will print "Hello (name)".

>There is term called "infinite loop", it means that the program stuck in the loop and continuously execute the same thing forever like the next example.
>

```python
name = input("Enter your name: ")
while name == "":
	print("you did not enter your name.")
print(f"Hello {name}")
```

- In the previous example if the user did not enter the name the program will print "you did not enter your name." forever, because we deleted the escape statement.

---
# for loop

