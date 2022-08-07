## Unnecessary null-checks 
Known as the six-billion dollar mistake, the null-pointer is undoubtedly the most infamous exception among
developers. Some of us go to great lengths to avoid it - littering null-checks everywhere throughout the codebase, 
just so testers and users don't encounter them.

Overly defensive null-checks implies a weak grasp of the control flow of the program.  
Other than null-checks of input arguments of a publicly exposed API, and in places where null is used to express  
the concept of an empty value, null-checks in most other circumstances are usually superfluous.  
This is because it masks the fundamental cause of the null, and also hinders readability of the code.  

## Masks fundamental cause 

Consider the following example: 

```java
var setOfDates = Set.of(LocalDate.of(2022,1,1),LocalDate.of(2022,1,2));
setOfDates.stream()
.filter(Objects::nonNull)
.collect(Collectors.toSet());
```

While it is theoretically possible to add a null object to a Set,
it is outside the control flow of the program - it is never intended that a null object be 
added at any point in time. And if we indeed encounter a null object added to the `setOfDates`, then something 
fundamentally wrong has happened, and a null exception should appropriately be raised at that point in time. 
Filtering the null dates will only mask the issue and delay the eventual discovery of it. 

## Hinders Readability 
Consider the following 3 snippets:

# A
```java
Stream.ofNullable(employees)
.flatMap(Collection::stream)
.filter(e -> e.equals("Peter"))
.collect(Collectors.toList());
```

# B
```java
Optional.ofNullable(employees)
.flatMap(Predicate.not(Collection.isEmpty))
.filter(e -> e.equals("Peter"))
.collect(Collectors.toList())
```

# C
```java
var employeesNullSafe = Collections.emptyIfNull(employees);

employeesNullSafe.stream()
.filter(e -> e.equals("Peter"))
.collect(Collectors.toList());
```

The first two lines of snippets A and B are essentially doing the same thing - to perform a null-check on the 'employees'
variable. However, just a simple null-check takes up 50% of the code snippet. Someone reading the code for the first time has to pause and digest the first 
2 lines before figuring out that it is just a null-check. Now if this coding-style is replicated throughout the codebase, it exponentially increases the cognitive complexity of the code.
