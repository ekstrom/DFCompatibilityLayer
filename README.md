# DF Compatibility Layer

DF Compatibility Layer provides helper functions to make the same code run in DataFlex 14.1-20.0

## Example of use cases

-   You are maintaining a library that needs to support DataFlex 14.1-20.0
-   You are upgrading to for example DataFlex 20.0 and you want to be able to compile and run the same code on older versions of DataFlex. The idea is that you drop the use statement of this package when you want to drop support for older versions of DataFlex.

## Start using it

```
Use DFCompatibilityLayer.pkg
```

## Functions/macros in the DFCompatibilityLayer.pkg

### StringToUCharArray

StringToUCharArray converts a string to UChar[]. StringToUCharArray does the same thing as the built in version of StringToUCharArray in DataFlex 18.1+, but you can call it in DataFlex 14.1-18.0.

### UCharArrayToString

UCharArrayToString converts a UChar[] to a string. UCharArrayToString does the same thing as the built in version of UCharArrayToString in DataFlex 18.1+, but you can call it in DataFlex 14.1-18.0.

### PointerToString

Copy a string. In DataFlex 14.1-19.1, an address can be moved to a string to create a new copy of a string.

#### Example: Copy s1 to s2

Works in DataFlex < 20.0:

```
String s1 s2
Address aSource
Move "abc" to s1
Move (AddressOf(s1)) to aSource
Move aSource to s2
```

The example above does not work in DataFlex 20.0

To make it work with DataFlex 14.1-20.0, wrap PointerToString around the address before it is moved to a string:

```
String s1 s2
Address aSource
Move "abc" to s1
Move (AddressOf(s1)) to aSource
Move (PointerToString(aSource)) to s2
```

### SizeOfString

SizeOfString returns the number of bytes in memory of a string

#### Example: Copy s1 to s2 (same example as above but with using MemCopy)

Works in DataFlex < 20.0:

```
String s1 s2
Integer i
Address aDestination aSource
Move "abc" to s1
Boolean bSuccess
Move (Repeat(Character(0), Length(s1))) to s2
Move (AddressOf(s2)) to aDestination
Move (AddressOf(s1)) to aSource
Move (MemCopy(aDestination, aSource, Length(s1))) to bSuccess
```

The example above does not work in DataFlex 20.0

To make it work with DataFlex 14.1-20.0, replace Length with SizeOfString for functions that expect number of bytes as input instead of number of characters:

```
String s1 s2
Integer i
Address aDestination aSource
Move "abc" to s1
Boolean bSuccess
Move (Repeat(Character(0), SizeOfString(s1))) to s2
Move (AddressOf(s2)) to aDestination
Move (AddressOf(s1)) to aSource
Move (MemCopy(aDestination, aSource, SizeOfString(s1))) to bSuccess
```

### File_Exist_Compat

File_Exist_Compat checks if a file or folder exists for the native string encoding of the DataFlex version that you are running.
The built in File_Exist in DataFlex 14.1-19.1 does not use OEM (This would have been great), which is the native encoding of strings in those versions of DataFlex. This is not a problem in DataFlex 20.0, unless you have compensated for how it worked in DataFlex 14.1-19.1.

#### Example: Check if a file exists

Works in DataFlex < 20.0:

```
Boolean bFileExists
File_Exist (ToANSI("C:\example\ÅÄÖ.txt")) bFileExists
```

The example above does not work in DataFlex 20.0

To make it work with DataFlex 14.1-20.0, replace File_Exist with File_Exist_Compat and remove ToANSI:

```
Boolean bFileExists
File_Exist_Compat "C:\example\ÅÄÖ.txt" bFileExists
```
