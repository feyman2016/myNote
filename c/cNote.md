## Good practice in C programming

#### 一、关于内存分配

使用malloc分配空间，返回的指针指向void类型，显示将该指针转换为相应的类型

```
int * ialloc(void)
{
    return (int *) malloc(sizeof(int) * 10);// 分配10个int空间，根据需求可以替换返回的指针类型
}
```

分配内存，并将一个字符串传入到该位置，返回分配的内存的指针

```
char * strdup(char * s)
{
    char * p;
    p = (char *) malloc(strlen(s) + 1);
    if ( NULL != p)
        strcpy(p,s);
    return p;
}
```

二、比较（Compare）

使用strcmp比较字符串数组

```
    char *a = "casd";
    char *b = "asd";
    cond = strcmp(a,b);
    cout << cond <<endl; //输出2，因为'c'-'a' == 2
```

三、复制

```

```

四、以指针作为函数参数

```
void point_list(int * a)
```

需要注意的是，调用函数时，指针的值将被复制到形式参数中，所以函数中对于形式参数的任何改变不会影响原指针。这一点很重要，但是可以改变指针指向的值。