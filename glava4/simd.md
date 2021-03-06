# SIMD

##### *Я не могу долго быть одна. Когда я одна — я ничто. Просто набор скверных качеств.*
######Эрих Мария Ремарк "Земля обетованная"

Парадигма SIMD – single instruction multiple data (одна инструкция множество данных).
```
#pragma omp simd 
```
Клаузы:
* safelen(длина)
* linear(список[:шаг])
* aligned(список[:выравнивание])
* private(список)
* lastprivate(список)
* reduction(операция:список)
* collapse(n)

#### Клауза safelen

Если **safelen** специфицировано, значение в скобках указывает количество итераций цикла, которые будут независимы между собой. Размер вектора для векторизации будет меньше или равный этому значению(в зависимости от реализации OpenMP), но гарантировано , что оно не будет больше. Само значение в скобка должно быть положительным и целочисленным. 

#### Клауза linear 




#### Клаузы разделения данных

  
**private(список)** создаётся не инициализированный вектор для заданных переменных. Для аргументов simd функций, параметры имеют различное местоположение в памяти или значение для каждого экземпляра среди simd векторов.

**lastprivate(список)** обеспечивает такую же семантику, но в указанные переменные копируются значения полученные на последней итерации цикла. 

**reduction(операция:список)** создаёт векторную копию переменных и горизонтально по частям суммирует(в зависимости от операции заданной программистом) в оригинальное скалярное значение. 



```
void star( double *a, double *b, double *c, int n, int *ioff )
{
    int i;
    #pragma omp simd
    for ( i = 0; i < n; i++ )
    a[i] *= b[i] * c[i+ *ioff];
}

```

```
#pragma omp declare simd
```

Клаузы:
* simdlen(длина)
* linear(список[:шаг])
* aligned(список[:выравнивание])
* uniform(список)
* inbranch
* notinbranch

#### Клауза uniform 

Параметр, указанный с помощью **uniform**, является инвариантом цикла. Инвариантом одной порции, которая вызывается одновременно внутри одного simd цикла. Для простоты восприятия приведем пример:

```
#pragma omp declare simd uniform(i) linear(k) notinbranch
float P(const int i, const int k)
{
 return Q[i][k] * Q[k][i];
}

float accum(void)
{
 float tmp = 0.0;
 int i, k;
 
    #pragma omp parallel for reduction(+:tmp)
    for (i=0; i < N; i++)
    {
        float tmp1 = 0.0;
        #pragma omp simd reduction(+:tmp1)
        for (k=0; k < M; k++) 
        {
            tmp1 += P(i,k);
        }
    tmp += tmp1;
    }
 return tmp;
}

```
В данном примере переменная (i) является подобным инвариантом. Она не будет изменяться во время вложенного simd цикла. 


```
#pragma omp declare simd
float foo(float *B, float *C, int i)
{
    return B[i] + C[i];
}
for( i=0 ; i < N ; i++ )
{
    A[i] = foo(B, C, i);
}

```


####Конструкция  parallel for simd 

```
#pragma omp parallel for simd
```

Пример применения **simd** к циклу:

```
  #pragma omp parallel for simd
  for (i=0; i<N; i++) 
  { 
    a[i] = b[i] * c[i]; 
  }

```

