## Some python tricks
### 1.How to use multi python versions on windows?

Just install two (or more, using their installers) versions of Python on Windows 7 (for me work with 3.3 and 2.7).  
Follow the instuctions below, changing the parameters for your needs.  
Create the following environment variable (to default on double click):  
```
Name:  PY_PYTHON
Value: 3
```

To launch a script in a particular interpreter, add the following shebang (beginning of script):  
```
#! python2
```

To execute a script using a specific interpreter, use the following prompt command:  

```
> py -2 MyScript.py
```

To launch a specific interpreter:
```
> py -2
```

To launch the default interpreter (defined by the PY_PYTHON variable):
```
> py
```
