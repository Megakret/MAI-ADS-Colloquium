1. Какие операции есть у указателей? Что будет при сложении указателя с int?

К указателям можно прибавлять и вычитать числа: 
```cpp
int a = 3;
int b = 5;
int* ptr_a = &a;
int* ptr_b = &b;
// Получим 5, так как a и b лежат подряд на стеке 
std::cout << *(ptr_a + 1) << '\n';
// Получим 3, так как a и b лежат подряд на стеке 
std::cout << *(ptr_b - 1) << '\n';
```
То на сколько байт сдвинется указатель зависит от типа указателя
```cpp
int a = 3;
int b = 5;
int* ptr_a = &a;
int* ptr_b = &b;
// Так как указатель это просто 64 безнаковое целое число мы можем закастить его к uint64_t
uint64_t ptr_a_value = reinterpret_cast<uint64_t>(ptr_a);
uint64_t ptr_a_value_shifted = reinterpret_cast<uint64_t>(ptr_a + 1);
// Видим, что реальный адрес увеличился на 4, а не на 1,
//так как размер int на большинстве соременных архитектур 4 байта
std::cout << ptr_a_value << ' ' << ptr_a_value_shifted << '\n';
```

Также есть постфиксные и инфиксные инкременты (работают также, как и у других типов)

3. Как работает вычитание указателей?

Вычитание указателей можно совершать только между указателями одинакого типа:
```cpp
int a = 3;
int b = 4;
int *a1 = &a;
int *b1 = &b;
long l = 1;
auto dif = a1 - b1;
std::cout << typeid(l).name() << '\n'; // l
std::cout << typeid(b1 - a1).name() << '\n'; // l
std::cout << typeid(a1 - b1).name() << '\n'; // l
std::cout << b1 - a1 << '\n'; // 1
std::cout << a1 - b1 << '\n'; // -1
```
Результат может быть отрицательным, так как тип получаемого выражения long (странно, что не int)

4. Что такое void*?

void* - универсальный тип для указателя, по сути хранит в себе только адрес в памяти 
Разыменовывать его нельзя, это даже не скомпилируется

5. Что такое nullptr? Отличие от NULL. 

NULL - константа равная 0

nullptr - константа типа nullptr_t, является единственным инстансом этого типа 

nullptr_t умеет неявно преобразовываться в любой тип указателя, благодаря чему работают выражения по типу 

int* == nullptr или YourStruct* == nullptr

Наличие отдельного типа для null указателей иключает сравнение их c числами

Например: 
```cpp
std::cout << (3 == nullptr) << '\n'; // это не компилируется
std::cout << (3 == NULL) << '\n'; // это компилируется
```
Это также убирает неопределенность при перегрузке функции:
```cpp
void f(int) { std::cout << "int\n"; }

void f(int *) { std::cout << "int pointer\n"; }

int main() {
    f(NULL); // Это не компилируется (call to function is ambiguous)
    f(nullptr); // Это компилируется, неопределенности тут нет
}
```

6. Что такое динамическая память?

Динамическая память - это вид памяти, которую можно выделять и освобожать во время выполнения программы. 
Динамическая память по сути синоним для слова heap (куча). В отличии от статической памяти (стека) мы можем 
выделять и освобождать память на куче в любое время. Например на стеке мы не можем удалить обьект из середины стека, так как для этого нам нужно было бы сначала удалить все обьекты сверху него. 

В С, чтобы выделять и освобождать динамическую память, используются malloc и free

В С++, чтобы выделять и освобождать динамическую память, используеются new, new[], delete, delete[], а также аллокаторы

7. Как работают операторы new, new[], delete, delete[]

Операторы new выделяет память и что важно ``конструирует`` обьект на это месте, возвращая указатель нужного типа.

Оператор delete ``деконструирует`` обьект и освобождает память.

Двойной delete это UB. Delete на nullptr ничего не делает и не вызывает UB. 

Операторы new[] и delete[] делают то же самое, только делают это для нескольких обьектов в памяти подряд (то есть
аллоцируют и деконструируют массивы в динамической памяти)
