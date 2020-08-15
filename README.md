# InverviewQuestions
Здесь я собираю коллекцию вопросов, которые задают на собеседованиях (вакансия программист-разработчик C++)

## Вопросы по С++

## 0. Назовите отличия ссылки и указателя?

**Ответ:**
* ссылка должна быть инициализирована, указатель - нет;
* ссылку нельзя менять, указатель можно;
* ссылка не может указывать на ноль, а указатель может;
* ссылка - это другое имя для переменной, а указатель - это переменная, хранящая
адрес другой переменной;
* к ссылке нужно обращаться, как к обычной переменной, а для получения значения переменной
через указатель, необходимо сначала его разыменовать с помощью *.

## 1. Расскажите про new, delete, malloc, free, как они связаны и чем отличаются?

**Ответ:** new, delete - это операторы, а malloc и free - это стандартные библиотечные функции.
Оператор new отличается от функции malloc тем, что new выделяет память и вызывает конструктор,
а malloc только выделяет кусок памяти и возвращает указатель на неё, а инициализировать её
Вы должны сами. Так же оператор delete вызывает деструктор объекта и освобождает память, а
функция free только освобождает память. Функции malloc и free не взаимодействуют с объектами,
не могут инициализировать объект или правильно
его деконструировать.

## 2. Бывает такое, что оператор new не выделяет память?

**Ответ:** да, бывает, когда new передаётся указатель на уже выделенную память (например, с
помощью malloc). Это называется placement new. И оператор new без изменения возвращает
второй параметр - указатель (void* operator new(std::size_t, void*)). Это используется
для создания объектов в выделенном "хранилище" или после malloc.

**Пример:**

```c++

#include <iostream>

struct A{
  A(){ std::cout << "I'm constructor\n"; }
  ~A(){ std::cout << "I'm destructor\n"; }
};

int main() {
  void *pv = malloc(sizeof(A)); // или char *pv = new char[sizeof(A)];
  A *pa = new(pv) A;
  std::cout << std::hex << reinterpret_cast<A*>(pv) << " = " << pa << '\n';
  pa->~A();
  free(pv);                     // или delete[] pv;
  pv = nullptr;
}
```

**Важно! В этом случае деструктор нужно вызывать самостоятельно!**

## 3. Отличия между оператором delete, delete[].

**Ответ:** первый выполняет единичный вызов деструктора, а второй оператор - вызывает деструктор
для каждого элемента массива.

## 4. Что такое инкапсуляция, наследование, полиморфизм?

**Инкапсуляция** - это скрытие внутренней реализации от других компонентов (например,
использование get-, set- методов).

**Наследование** - главное преимущество ООП. Выведенный класс (наследник) может наследовать
свойства, методы и интерфейс другого класса, который называют базовым, а так же добавлять
свои черты, характерные только для него.
   
**Полиморфизм (шаблоны - статический полиморфизм, виртуальные функции, перегрузка функций
и операторов** - динамический полиморфизм) - повторное использование кода, одно и тоже имя
класса или функции для единого класса действий. Вызов конкретной версии функции зависит
от объекта, который передают в качества параметра.

## 5. Расскажите про порядок вызова конструкторов и деструкторов базового и производного классов при создании и удалении объекта.

**Ответ:** сначала вызывается конструктор базового класса, затем производного. При удалении вызывается
деструктор производного класса, а затем базового.

При наличии членов класса порядок вызовов такой: конструкторы членов базового класса,
конструктор базового класса, конструкторы членов производного класса, конструктор производного
класса. При удалении порядок обратный: деструктор производного класса, деструкторы членов
производного класса, деструктор базового класса, деструкторы членов базового класса.  

**Пример:**

```c++
#include <iostream>

class Member1{
public:
  Member1(){ std::cout << "Member1 constructor\n"; }
  ~Member1(){ std::cout << "Member1 destructor\n"; }
};

class Member2{
public:
  Member2(){ std::cout << "Member2 constructor\n"; }
  ~Member2(){ std::cout << "Member2 destructor\n"; }
};

class Base{
  Member1 m1;
  Member2 m2;
public:
  Base(){ std::cout << "Base constructor\n"; }
  ~Base(){ std::cout << "Base destructor\n"; }
};

class Member3{
public:
  Member3(){ std::cout << "Member3 constructor\n"; }
  ~Member3(){ std::cout << "Member3 destructor\n"; }
};

class Member4{
public:
  Member4(){ std::cout << "Member4 constructor\n"; }
  ~Member4(){ std::cout << "Member4 destructor\n"; }
};

class Child : public Base{
  Member3 m3;
  Member4 m4;
  
public:
  Child(){ std::cout << "Child constructor\n"; }
  ~Child(){ std::cout << "Child destructor\n"; }
};


int main() {
  Child ch;
}
```
``` bash
Вывод:
Member1 constructor
Member2 constructor
Base constructor
Member3 constructor
Member4 constructor
Child constructor
Child destructor
Member4 destructor
Member3 destructor
Base destructor
Member2 destructor
Member1 destructor
```

## 6. Что такое виртуальные функции? Что такое чисто виртуальные функции и чисто виртуальный деструктор? Что такое абстрактный класс?

**Ответ:** виртуальная функция - это функция, объявление которой содержит ключевое слово
virtual. Если функция объявлена виртуальной в базовом классе, то она становится
виртуальной и в производных классах и там писать слово virtual избыточно. Тело этой
функции может быть переопределено в производном классе. Для безошибочного переопределения
виртуальной функции лучше добавлять ключевое слово override.

При вызове виртуальной функции происходит динамическое или позднее связывание компилятор
вставляет в программу фрагмент кода для определения  и вызова правильной функции,
зависящей от типа объекта.

Как работает механизм определения типа объекта: компилятор создает таблицу VTABLE
виртуальных функций для каждого класса, содержащего виртуальные функции. И помещает
туда адреса виртуальных функций данного класса. В каждом таком классе скрыто хранится
указатель на таблицу виртуальных функций VPTR.
При вызове виртуальной функции через указатель на базовый класс, компилятор вставляет
в программу код, который получает указатель VPTR и добавляет смещение для нужной функции. 

Чисто виртуальная функция - это функция, тело которой отсутствует и = 0; Её необходимо
определить в производном классе.

**Пример:** 
```c++
virtual void draw() = 0;
```
Абстрактный класс - это класс, содержащий хотя бы одну чисто виртуальную функцию.
Создать объект такого класса невозможно. Он нужен только для того, чтобы унаследовать его
интерфейс.

## 7. Может ли конструктор и деструктор быть виртуальными?

**Ответ:** виртуальных конструкторов не бывает. Возможна имитация виртуального конструктора с помощью
паттерна "Фабрики объектов".

Виртуальный деструктор необходим, когда в классе есть хоть одна виртуальная функция или класс
является базовым. При удалении объекта производного класса через указатель на базовый класс,
деструктор которого не является виртуальным, результат не определен. Чаще всего результатом
будет утечка памяти, так как не вызовется деструктор производного класса.

**Пример:**
```c++
#include <iostream>

class A{
public:
  A(){ std::cout << "A constructor\n"; }
  ~A(){ std::cout << "A destructor\n"; }
};
class B : public A{
public:
  B(){ std::cout << "B constructor\n"; }
  ~B(){ std::cout << "B destructor\n"; }
};


int main() {
  A *pb = new B;
  delete pb;
}
```
```bash
Вывод:
A constructor
B constructor
              <------ здесь должен быть вызов деструктора класса B
A destructor
```

## 8. Какое значение будет выведено на экран:

```c++
#include <iostream>

int f(int x){
  int count = 0;
  while(x){
    count++;
    x = x&(x-1);
  }
  return count;
}

int main() {
  std::cout << f(9999) << std::endl;
}
```

**Ответ:** 8, выведется количество не нулевых битов числа, передаваемого в качестве
параметра в функцию f.

## 9. Что такое ссылка? Как можно инициализировать ссылку одной или другой переменной в зависимости от условия?

**Ответ:** это другое имя для переменной. Ссылку необходимо инициализировать при объявлении
и изменять уже нельзя. С помощью тернарного оператора или с помощью лямбда-функции.

```c++
Пример №1:

  int a = 10;
  int b = 20;
  int &c = a > 10 ? a : b;
```
```c++
Пример №2:

  int a = 10;
  int b = 20;
  auto lm = [&a, &b]() -> int& { return a > 10 ? a : b; };
  int& d = lm();
```

## 10. В чем отличие структуры и объединения?

**Ответ:** в структуре все её члены хранятся одновременно, и к ним есть доступ. В объединении
существует только один тип, который в данный момент используется. Доступ к остальным
вызовет неопределенное поведение (undefined behavior).

## 11. Может ли шаблонная функция быть виртуальной?

**Ответ:** шаблонная функция не может быть виртуальной, потому что это слишком большое
усложнение устройства таблицы виртуальных функций. Шаблонные функции генерируются на
этапе компиляции (статическое связывание), а объявление функции виртуальной означает,
что компилятор должен выполнить позднее связывание (динамическое) и на этапе выполнения
вызовется «нужная функция» для каждого класса через указатель на таблицу виртуальных
функций плюс смещение. Эта таблица должна быть фиксированного размера и содержать
только одну запись для виртуальной функции, а в случае шаблонной виртуальной функции
их будет несколько и их количество не будет известно до трансляции всей программы.

## 12. Бывает ли специализация шаблона? Что такое частичная специализация?

**Ответ:** да, шаблон класса или функции можно специализировать, то есть указать реализацию
для конкретных аргументов. А частичная специализация означает, что параметров у шаблона
несколько и для одного или нескольких указываются конкретные аргументы, или,
возможно, указывается, что параметры имеют один и тот же тип.

```c++
// Это шаблон:
template<typename T1, typename T2>
class A{
…
};
```
```c++
// Частичная специализация (указан конкретный аргумент double):
template<typename T1>
class A<T1, double>{
…
};

// Частичная специализация (указано, что параметры имеют один и тот же тип):
template <typename T>
class A<T, T> {
…
};
```

## 13. Что будет выведено на экран?

```c++
#include <iostream>

int a=4;

int &f(int x){
  a = a + x;
  return a;
}

int main(void){
  int t = 5;
  std::cout << f(t) << std::endl; // 9
  f(t) = 20;
  std::cout << f(t) << std::endl; // 25
  t = f(t);
  std::cout << f(t) << std::endl; // 60
}
```

**Объяснение**

В первом случае на печать выведется **9**, потому что функция **f** принимает параметр
**t = 5**, внутри функции к глобальной переменной **a** прибавляется **5**, теперь
**a = 9**, а **f** возвращает ссылку на неё и печатается значение **a**, то есть **9**).

Далее неважно, что происходит внутри функции, важно, что возвращаемой ссылке на **a**
присваивается **20**, значит **a = 20**. Переменная **t** не поменяла своё значение, так как
в функцию она передается по значению, а не по ссылке.

Затем опять вызываем **f(5)**, при этом **a = 20**, **a = 20 + 5**, на печати увидим
число **25**.

Теперь присваиваем **t** значение **a** (в этот момент **a = 25 + 5**), значит, **t**
станет равно 30.

И, наконец, последняя печать. Вызываем **f(30)**, **a = 30 + 30**, и возвращается
значение **60**.

## 14. Можно ли использовать ссылку при возвращении из функции? Это хорошо или плохо?

**Нельзя:**

* никогда нельзя возвращать ссылку на локальную переменную, созданную внутри функции, потому что она разрушится сразу после выхода из функции;
* нельзя возвращать ссылку на выделенную память, потому что её нельзя будет очистить.

**Можно:**

* когда возвращается константная ссылка на член класса;
* когда Вы пишете оператор <<, оператор = и оператор индексирования, они должны возвращать
ссылку на объект.

Возвращать ссылку - это хорошо, потому что не вызывается лишний копирующий конструктор для возвращаемого значения, но, иногда лучше и правильнее возвращать по значению.

## 15. В чем отличие overload и overried?

**Ответ:** overload - это перегрузка, два одинаковых имени функций с разными принимаемыми
аргументами, override - это переопределение (с помощью таблицы виртуальных функций), изменение
поведения функции в выведенном классе ( с помощью механизма виртуальных функций).

## 16. Когда вызывается конструктор копирования, а когда копирующий оператор присваивания?

**Ответ:** конструктор копирования вызывается, когда объект ещё не создан и на основе другого
объекта создается текущий, а копирующий оператор присваивания - когда объект уже создан и
происходит копирование всех элементов из одного объекта в другой.

Пример:

```c++
#include <iostream>

class A{
public:
  A(){ std::cout << "Constructor\n"; }
  A(const A&){ std::cout << "Copy constructor\n"; }
  A& operator=(const A&){
    std::cout << "Copy assignment operator\n";
    return *this;
  }
  ~A(){ std::cout << "Destructor\n"; }
};

int main() {
  A a1;
  A a2;
  A a3 = a1; // вызывается копирующий конструктор A(const A&), так как объект a3 не создан
  a2 = a1;   // вызывается оператор копирующего присваивания: A& operator=
}
```

```bash
Constructor
Constructor
Copy constructor
Copy assignment operator
Destructor
Destructor
Destructor
```


## 17. Когда Вы должны использовать список инициализации?

**Ответ:**

*  когда в классе есть ссылки,
*  когда есть константы,
*  когда у базового и выведенного класса есть конструктор с набором аргументов.

## 18. С++ типобезопасный?

**Ответ:** нет. Необходимо делать каст для двух разных типов указателей.

## 19. Какой код выполняется до функции main?

**Ответ:** конструкторы глобальных объектов.

## 20. Опишите выделение памяти и их отличия?

* Статическое выделение памяти - выделяется редактором связей и существует, пока выполняется
программа;
* Память в стеке - автоматическая память. Выделяется при вызове функции для хранения локальных
объектов и очищается автоматически после выхода этих объектов из зоны видимости, то есть после
возвращения управления из функции;
* Память в куче - динамическая память. Объекты создаются с помощью оператора new и должны быть
уничтожены с помощью вызова оператора delete самим программистом. 

## 21. Как сравнивают bool, int, float, указатели со значением и с нулем?
**Ответ:**
```c++
 bool:  if(a) or if(!a)
 int: if(a == 0)
 float if(a < 0.00001 && a > -0.00001)
 pointer: if(a == nullptr)
```

## 22. Что лучше const или define?

```c++
#define PI 3.14159265359
const double Pi = 3.14159265359;
```

**Ответ:** define - это директива препроцессора, которая не учитывает ни типов, ни
областей видимости. Препроцессор вставляет значение везде, где оно используется и создается
множество копий 3.14159265359 в объектном коде, константа никогда не порождает больше одной
копии этого значения. define трудно отлаживать, потому что у него нет имени, только магическое
число, нельзя взять адрес или создать ссылку на это значение.

У const есть тип, область видимости, можно взять адрес, создать константную ссылку на эту
переменную.

Определенно, лучше использовать const, а не define.

## 23. Отличия между указателем и массивом?
**Ответ:**

Массивы создаются в статической памяти или в стеке. Указатели могут указывать на любой тип
в памяти. В массивах можно изменять содержимое, а по указателю не всегда. 

```c++
char a[] = "GNU";
a[0] ='X';
char *p = "Linux"; // p указывает на константную строку
p[0] ='X';         // ошибка времени выполнения, нельзя изменять константную строку 
```

sizeof a - показывает количество занимаемой массивом памяти
sizeof p - показывает размер указателя.


## 24. Как определить размер массива?
```c++
int n[] = {1,1,1,1, 1,1,1,1, 1,1,1,1};
```
**Ответ:**

```c++
#include <iostream>
int main() {
  int n[] = {1,1,1,1, 1,1,1,1, 1,1,1,1};
  std::cout << sizeof(n)/sizeof(n[0]) << '\n';  
}
```
или с использованием функции из стандартной библиотеки:

```c++
#include <iostream>
int main() {
  int n[] = {1,1,1,1, 1,1,1,1, 1,1,1,1};
  std::cout << std::size(n) << '\n';
}
```

## 25. Что означает int (*s[10])(int) ?

**Ответ:**

s[10] массив из 10 элементов
*s[10] указателей на 
(*s[10])(int) функции, принимающих аргументом int
int (*s[10])(int) и возвращающих int

## 26. Это корректно int id[sizeof(unsigned long)]?

**Ответ:**

Да, sizeof - это оператор времени компиляции, размер массива будет известен, как будто это
константа. Это эквивалентно int id[8] // если long - 8 байт

## 27. Что означают эти определения:

double * (* (*fp1)(int))[10];

**Ответ:**
fp1 - это указатель на функцию, которая получает аргумент (int) и возвращает указатель
на массив с 10 указателями на double.

float (*(* fp2)(int,int,int))(int);

**Ответ:**
fp2 - это указатель на функцию, которая получает три целых аргумента (int, int, int) и
возвращает указатель на функцию, которая получает аргумент (int) и возвращает float.

int (* (* fp3)())[10]();

**Ответ:**
fp3 - это указатель на функцию без аргументов, возвращающую массив указателей на функции
без аргументов и возвращающие int.

## 28. Что такое name mangling?
**Ответ:**
name mangling - это "искажение имени" или украшение имени. Используется для решения проблемы
повторяющихся имен. Допустим, есть две функции, которые отличаются только типом принимаемого
аргумента:

```c++
int  f (void) { return 1; }
int  f (int)  { return 0; }
```

Как компилятор может различить их вызов? Для этого используется name mangling и имена функций
изменяются на __f_v и __f_i:

```c++
int  __f_v (void) { return 1; }
int  __f_i (int)  { return 0; }
```
Это самый простой пример. На самом деле украшения имен могут быть значительно сложнее:

```c++
// У функции

void funny(int i){
     std::cout << i << '\n'
}

// будет имя _Z5funnyi

/////////////////////////////////////////////////

// А у шаблонной функции имя будет намного длинее

template<class T>
void funny(T(*fp)(T), T i){
  fp(i);
}

// вот такое: _Z5funnyIiEvPFT_S0_ES0_

```

## 29. Что выведет на экран следующая программа?

```c++
#include <iostream>

int main() {
  std::cout << 25u - 50 << '\n';
}

```
**Ответ:** 2^32 - 25, то есть 4294967271.

Объяснение: вычитание имеет бОльший приоритет и выполниться раньше, чем вывод на экран.
25u означает unsigned int, вместимость этого типа от 0 до 2^32-1. 50 приводится к типу
левого аргумента, и становится тоже unsigned int. При вычитании не может получиться
отрицательное число, а так как беззнаковые типы "свёрнуты в кольцо", то -25 в unsigned
int будет равно 2^32 - 25.

## 30. Что выведет на экран следующая программа?

```c++
#include <iostream>

int main() {
  unsigned char n[] = {1,1,1,1, 1,1,1,1, 1,1,1,1};
  (*(((int*)n)+1))++;
  for(auto&& i: n){
    std::cout << static_cast<int>(i) << ",";
  }

  std::cout << std::endl;
}
```
**Ответ:** 1,1,1,1, 2,1,1,1, 1,1,1,1

Объяснение: массив unsigned char занимает 12 байт и выглядит в битовом формате вот так:
[00000001, 00000001, 00000001, 00000001,    00000001, 00000001, 00000001, 00000001,    00000001, 00000001, 00000001, 00000001].

Будем разворачивать выражение изнутри:

(int*)n - мы приводим указатель на unsigned char к указателю на int;

((int*)n)+1 - приведенный указатель на int увеличиваем на 1, один в случае указателя на int
равен 4 байтам. Значит, теперь это выражение указывает не на 1ый элемент массива, а память,
начиная с 4ого элемента, заканчивая 7ым;
[00000001, 00000001, 00000001, 00000001,    **00000001, 00000001, 00000001, 00000001,**    00000001, 00000001, 00000001, 00000001].

*(((int*)n)+1) - это выражение берет значение из массива (в битовом формате с 4 по 7 элемент
 включительно); (в битовом формате **00000001 00000001 00000001 00000001**)

(*(((int*)n)+1))++ - а здесь мы увеличиваем на 1 значение младшего бита, который из-за обратного порядка стоит в первом байте из четырёх: 00000010 00000001 00000001 00000001;

[00000001, 00000001, 00000001, 00000001,    **00000010, 00000001, 00000001, 00000001,**    00000001, 00000001, 00000001, 00000001].

Поэтому в массиве мы увидим увеличение только одного, 4го элемента.

[1,1,1,1, 2,1,1,1, 1,1,1,1]

## 32. Что нужно дописать, чтобы при вызове конструктора перемещения член класса действительно "переместился"?

```c++
class A{
  std::string str;
public:
  A(std::string_view s) : str(s){ }
  A(A&& oth) : str(oth.str){ }

...

};
```

**Ответ:** нужно дописать std::move в списке инициализации перемещающего конструктора:

```c++
  A(A&& oth) : str(std::move(oth.str)){ }
```
Но это гарантирует "перемещение" только в том случае, если у этого объекта, в свою очередь, есть
перемещающий конструктор (в данном случае, у std::string он есть).

## 33. Сколько раз будут выполняться циклы внутри программы:

```c++
#include <iostream>

int main() {

  unsigned char n = 256;
  for(unsigned char i = 0; i < n; ++i){
    std::cout << 'a';
  }

  unsigned char h = 128;
  for(unsigned char i = 0; i < 2 * h; ++i){
    std::cout << 'b';
  }
  
}
```
**Ответ:** в первом случае цикл не выполнится никогда, а во втором случае будет выполняться бесконечно.

**Объяснение:** в первом цикле условие прекращения цикла имеет тип беззнакового char, значит,
у нас присвоение n 256 вызовет переполнение и n станет равным 0. Цикл превратится в
for(unsigned char i = 0; i < 0; ++i), а, значит, условие цикла не выполняется, и мы даже не войдём
в тело цикла. Во втором цикле другая ситуация, он превращается в for(unsigned char i = 0; i < 256; ++i),
и так как i не может быть больше, чем 255, то условие цикла будет выполняться всегда.

## 34. Напишите код, вычисляющий факториал на этапе компиляции.

В C++17:

```c++
#include <iostream>

constexpr int factorial(int n){
  return n <= 1? 1 : (n * factorial(n - 1));
}

template<int n>
struct print_{
  print_() { std::cout << n << '\n'; }
};

int main(){
  print_<factorial(5)> out;
}
```

Старый C++ с помощью шаблона и enum:

```c++
#include <iostream>

template<int n>
struct factorial
{
  enum { value = n * factorial<n-1>::value };
};

template<>
struct factorial<0>
{
  enum { value = 1 };
};

int main()
{
  std::cout << factorial<6>::value << '\n';
}
```

## 35. Что быстрее постфиксные инкрементные операторы или префиксные? Чем отличается их сигнатура?

**Ответ:** префиксные операторы быстрее. В постфиксных операторах производится дополнительная операция
сохранения предыдущего состояния объекта, а затем только увеличение на 1, а в префиксном операторе
после инкрементирования объекта, сразу возвращается ссылка объект. Сигнатура их отличается фиктивным
параметром int у постфиксного оператора.

**Пример:**

```c++
// Префиксный оператор инкрементирования
A& operator++() {
   ++i;
   return *this;
}

// Постфиксный оператор инкрементирования
A operator++(int) {
   A ret(*this);
   ++i;
   return ret;
}

```

## 36. Что такое rvalue, lvalue?

**Ответ:** lvalue - это всё, что имеет имя, rvalue - это то, что имени не имеет. Исторически сложились
такие названия, потому что lvalue всегда было то, что писалось с левой стороны присваивающего
выражения, а rvalue - это то, что появлялось только справа от этого выражения.

```c++
// i - это lvalue, имеет имя и адрес,
// можно написать:
int i = 10;
// 10 - это rvalue, нет ни имени, ни адреса, 
// нельзя написать: 
10 = i;
```

Сейчас, кроме этих двух терминов появились ещё xvalue (eXpiring value) - это универсальная ссылка,
glvalue (general lvalue) - это lvalue или универсальная ссылка, prvalue(pure rvalue) - это rvalue,
которое не является универсальной ссылкой.

Эта схема отражает соответствие этих терминов между собой:

               expression
             ↙            ↘
          glvalue        rvalue
         ↙       ↘      ↙      ↘
     lvalue       xvalue       prvalue

Или вот такая диаграмма Венна выглядит более информативно:
```c++
    ______ ______
   /      X      \
  /      / \      \
 |   l  | x |  pr  |
  \      \ /      /
   \______X______/
       gl    r
```
Или схема, предложенная Б.Страуструпом:
```c++

  lvalue  xvalue  prvalue
      \    /  \    /
       \  /    \  /
        \/      \/
     glvalue   rvalue

    iM      im      Im
      \    /  \    /
       \  /    \  /
        \/      \/
        i        m

iM - имеет идентичность (имеет адрес), не может быть "перенесено",
im - имеет идентичность и может быть "перенесено",
Im - не имеет идентичности и может быть "пренесено",
i - имеет идентичность,
m - может быть "перенесено".
```

Примеры этих выражений:

```c++
class Foo
{
    std::string name;

public:

    Foo(std::string some_name) : name(std::move(some_name)){}

    std::string& original_name(){
        return name;
    }

    std::string copy_of_name() const{
        return name;
    }
};

...

Foo foo(str);
foo.copy_of_name();  // это выражение является prvalue, потому что возвращается объект
                     // std::string, а не ссылка, (также является rvalue)
foo.original_name(); // это выражение является lvalue, потому что возвращает ссылку на
		     // name (также является glvalue)
std::move(some_name) // это выражение является xvalue, так как вызвращает std::string&&
		     // (также оно одновременно и lvalue и rvalue).
```

## 37. Что делает std::move?

**Ответ:** std::move ничего не переносит и не перемещает, это шаблонная функция, которая делает приведение типа к rvalue ссылке.
Определение std::move выглядит примерно так:

```c++
template<typename T>
decltype(auto) move(T&& param){
   using return_type = typename remove_reference<T>::type&&;
   return static_cast<return_type>(param);
}
```

Сначала мы убираем ссылку с помощью remove_reference, это на тот случай, если тип T является
lvalue ссылкой, а затем делаем приведение типа к &&.

```c++
std::string str("hello");
std::move(str); // -> move<std::string> -> вернёт std::string&&
```

## 32. Что такое универсальная ссылка?
**Ответ:** универсальные ссылки - это ссылки, которые выглядят, как rvalue-ссылки, но могут
себя вести и как lvalue-ссылки и как rvalue-ссылки, могут быть связаны практически со всем
(const, volatile).

Универсальные ссылки возникают при выводе типа в двух контекстах: параметры шаблона функции и
объявления auto.

```c++
auto&& b = a;      // b - это универсальная ссылка,

template<typename T>
void f(T&& param); // param - это универсальная ссылка.
```
Инициатор определяет, какую ссылку будет представлять универсальная ссылка:

```c++
House h;
f(h);            // в f передается lvalue, тип param House& (lvalue-ссылка),
f(std::move(h)); // в f передается rvalue, тип param House&& (rvalue-ссылка).
```
При универсальной ссылке не может быть квалификатора const. 
```c++
template<typename T>
void f(const T&& param); // param - это **УЖЕ НЕ универсальная ссылка**, это просто rvalue-ссылка
```
Вид объявления универсальной ссылки должны быть **точно** T&&, здесь вывод типа отсутствует:
```c++
template<typename T>
void f(std::vector<T>&& param); // param - это **УЖЕ НЕ универсальная ссылка**, это просто
rvalue-ссылка
```
И здесь функция push_back является частью класса vector и не использует вывод типа:
```c++
template<class T, class Allocator=allocator<T> 
class vector {
public:
...
void push_back(T&& v);
...
};
```