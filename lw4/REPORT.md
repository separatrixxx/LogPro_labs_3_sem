# Отчет по лабораторной работе №4

## по курсу "Логическое программирование"

## Обработка естественного языка

### студент: Лохматов Н.И.

## Результат проверки

| Преподаватель     | Дата         |  Оценка       |
|-------------------|--------------|---------------|
| Сошников Д.В. |              |               |
| Левинская М.А.|              |               |

> *Комментарии проверяющих*
## Введение

Для решения некоторых прикладных задач удобно использовать обработку сообщений на естественном языке, например, такая обработка нужна для создания интерфейса, который будет понятен любому пользователю. На языке Prolog решить задачи грамматического и синтаксического разборов предложения очень легко, так как можно использовать логику предикатов, что существенно упрощает работу.

## Задание

Вариант 9. Реализовать разбор фраз языка (вопросов), выделяя в них неизвестный объект  

```(Prolog)
 Запрос:    ?- an_q(['Кто', 'любит', 'шоколад', '?'], X).
            ?- an_q(['Где', 'лежат', 'деньги', '?'], X).
            ?- an_q(['Что', 'любит', 'Даша', '?'], X).
 Результат: X='любить'(agent(Y), object('шоколад')),
            X='лежать'(object('деньги'), loc(X)),
            X='любить'(agent("Даша"), object(Y)).
```

## Принцип решения

Основным предикатом для решения задачи является an_q. Сначала он с помощью предиката split разбивает введённый вопрос на три части: вопросительное местоимение, глагол и подлежащее. Для глагола находим форму инфинитива из словаря, представленного предикатами verbform. Существительные хранятся в предикатах agent, object и loc (в зависимости от смысла). Далее в зависимости от местоимения ("Кто", "Где" или "Что") происходит построение ответа, также от местоимения зависит последовательность предикатов в ответе.

### Исходный код

```(Prolog)
:- set_prolog_flag(encoding, utf8).
agent('Даша').
object('шоколад').
object('деньги').
verbform('любить', 'любит').
verbform('лежать', 'лежат').
loc('').
condition(W, CW, T, CW:T:L) :- member(W, L).
getElem([],_,_).
getElem([H|T],T,H).
split(Q, W, Verb, Ad) :-
    getElem(Q, Q1, W), 
    getElem(Q1, Q2, Verb),
    getElem(Q2,_,Ad).
find_agent2(W,S) :- W == 'Кто'; agent(S).
find_agent1(W) :- W == 'Кто'.
find_object2(W,S) :- W == 'Что'; object(S).
find_object1(W) :- W == 'Что'.
find_loc(W,S) :- W == 'Где', loc(S).
find_loc1(W) :- W == 'Где'.
find_loc2(S) :- loc(S).
an_q(Q, _) :- 
    write("X="),
    split(Q, W, V1, S), 
    verbform(V, V1), write(V), 
    (
        find_agent2(W,S), !, write("(agent("), 
        (
            find_agent1(W), !, write("Y),"); write(S), write("),")
        ), 
        (
            find_loc(W,S), !, write("loc("),
            (
                find_loc1(W), !, write("Y))"); write(S), write("))")
            );
            write("object("),
            (
                find_object1(W), !, write("Y))");
                write(S),write("))")
            )
        );
        write("(object("),
        (
            find_object1(W), !, write("Y),"); write(S), write("),")
        ), 
        (
            write("loc("),
            (
                find_loc1(W), !, write("X))"); write(S), write("))")
            )
        )
    ).
```

## Результаты

```(Prolog)
2 ?- an_q(['Кто', 'любит', 'шоколад', '?'], X).
X=любить(agent(Y),object(шоколад))
true.
3 ?- an_q(['Где', 'лежат', 'деньги', '?'], X).
X=лежать(object(деньги),loc(X))
true.
4 ?- an_q(['Что', 'любит', 'Даша', '?'], X). 
X=любить(agent(Даша),object(Y))
true.
```

## Выводы

В ходе проделанной лабораторной работы я получил навык работы с методами анализа естественно-языковых текстов. Язык программирования Prolog – хороший инструмент для грамматического разбора предложений. Он предлагает совершенно иные возможности для написания программ, нежели другие императивные и функциональные языки программирования.
Это обусловлено тем, что при работе с грамматикой мы разбиваем каждый элемент исходного выражения или выссказывания на части, каждую из которых легко идентифицировать.

