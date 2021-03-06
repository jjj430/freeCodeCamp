---
title: Reactive Extensions
localeTitle: Реактивные расширения
---
# Реактивные расширения в угловом

#### мотивация

Реактивные расширения для JavaScript (RxJS) - это библиотека для _наблюдаемых_ потоков данных. RxJS устанавливает с помощью Angular при выполнении командной строки `ng new [name-of-application]` . Это использует интерфейс командной строки с угловым интерфейсом (CLI). RxJS дополняет процесс упорядочения данных через `Observable` . Объект `Observable` облегчает поток _итерируемых_ данных.

Потоки данных не являются основным прецедентом. В конце концов, поток данных передает параллельные потоки событий. События выдают, поэтому приложение знает, когда поступают данные. Хотя потоки событий формируют ядро ​​того, что добавляет RxJS, эта статья также относится к ним как к потокам данных.

Потоки выполняются либо синхронно (немедленно), либо асинхронно (сверхурочно). RxJS легко справляется с обоими случаями через поток данных `Observable` . Тем не менее строгая асинхронность. Работа с _встроенной_ памятью, _повторяющиеся_ данные происходят немедленно, в то время как для получения внешних данных требуется время. Угловая поддержка библиотеки RxJS, так что она может обрабатывать оба варианта использования с потоками данных.

#### Реактивное программирование

Прежде чем погрузиться в воду, важно понять парадигму, поддерживающую библиотеку RxJS. Как уже упоминалось, он работает через объект `Observable` который оптимизирует данные, генерирующие события.

Функции RxJS вокруг базы `Observable` . Вся его библиотека дополняет то, что она может сделать. RxJS включает и другие объекты, включая `Subject` , `Subscription` и `Observer` . Каждый из них является собственным вариантом базы `Observable` .

RxJS возникла из парадигмы реактивного программирования. Эта парадигма ввела _наблюдаемую_ картину. В ней существует эта ключевая идея: один `Observable` излучает в то время как все его `Observer` s получить уведомление. `Observer` _подписываются_ на `Observable` чтобы получать уведомление. Это уведомление может означать несколько вещей.

Это может означать изменение данных или прибытие данных, как это обычно указывается в этой статье. Он может сигнализировать об изменении какой-либо части приложения, которая влияет на `Observer` s.

Эта _наблюдаемая_ модель также стремится отделить понятия. `Observable` должен функционировать без каких-либо `Observer` и наоборот. Обычно это означает, что они могут стоять автономно, а не полностью функционировать без друг друга.

Если вам интересно, вы можете узнать больше об основах реактивного программирования, прочитав [эту статью в Википедии](https://en.wikipedia.org/wiki/Reactive_programming) . В этом разделе рассказывается о том, что необходимо для остальной части статьи.

#### Наблюдаемые

Для того, чтобы быстро повторить, `Observable` можно _наблюдать_ с. Это позволяет `Observable` обеспечивать обратную связь с зависимостями на основе потока данных. В RxJS `Observable` - это собственная фабричная функция, используемая для создания объектов `Observable` . Их основной план состоит в следующем.

```typescript
import { Observable } from 'rxjs'; 
 
 const observable = Observable.create((source) => { 
  source.next(data); 
 }); 
```

`.next` передает данные при передаче события своим наблюдателям. An `Observable` эмитирует данные внутри своего `.create` обратного вызова с помощью `.next` . Он принимает один аргумент, представляющий данные для испускания. `Observable` еще не был внедрен в JavaScript. RxJS обеспечивает замену из своей библиотеки.

Следующий шаг - наблюдатели. Чтобы сообщить функции или объекту _наблюдать_ `Observable` , используется следующий синтаксис: `observable.subscribe(observer)` . Другой способ взглянуть на это - `producer.subscribe(consumer)` . Наблюдаемые _производят_ данные, вызывая `.next` . Затем потребители уведомляются при получении данных.

```typescript
import { Observable } from 'rxjs'; 
 
 const observable = Observable.create((source) => { 
  source.next("Hello"); 
  source.next("World!"); 
 }); 
 
 observable.subscribe((word) => console.log(word)); 
 // console output 
 /* 
 Hello 
 World! 
 */ 
```

Два вызова `.next` происходят из обратного вызова `Observable` `.create` (производитель данных). Это приводит к двум отдельным выходам консоли от наблюдателя (потребителя данных).

Два вызова `.next` представляют собой синхронный поток данных. Потоки концептуализируют данные как линейный поток в порядке. Он либо решает синхронно, либо асинхронно, в зависимости от доступности данных.

Если данные, содержащие поток, легко доступны, он выполняется синхронно. В противном случае поток решает асинхронно сверхурочно. Порядок данных в любом случае всегда один и тот же в зависимости от вызова `.next` пределах наблюдаемого.

`Observable` работает как очередь. Вызов `.next` на кусок данных подталкивает его к задней части очереди. После этого данные появляются с фронта.

`Observable` потоки данных имеют огромное значение. Они детерминированы в своем порядке и разумно зависят от доступности данных. Кроме того, любое количество наблюдателей может _наблюдать_ за источником данных `Observable` . Это означает, что данные могут производить один раз и выделять все в одной операции.

Функции обратного вызова - это не единственный способ использования данных. Наблюдатели могут цеплять друг в друга как производители и потребители.

```typescript
const observableI = Observable.create((source) => { 
  source.next("Hello World!"); 
 }); 
 
 const observableII = new Observable().subscribe((v) => console.log(v)); 
 
 observableI.subscribe(observableII); 
 // console output 
 /* 
 Hello World! 
 */ 
```

`.subscribe` находится в объекте `Observable` . Вы можете назвать его `Observable` как его источник (продюсер), а другой - как аргумент (потребитель). Данные могут проходить (излучать) через любое количество наблюдаемых.

#### Реактивные расширения для JavaScript (RxJS)

Потоковые данные хороши, но какова точка, если наблюдаемые не могут редактировать поток? Это где библиотека RxJS вступает в игру. Он предоставляет операторам, которые выполняют различные мутации в потоке данных.

Угловые рычаги используют эти операторы для преобразования входящих данных. Разработчики могут обрезать любые ненужные данные из входящего потока с использованием операторов RxJS. Это экономит память и устраняет необходимость в дополнительной логике преобразования.

RxJS предлагает отклонения от стандартного `Observable` например `Subject` , `Subscription` и `Observer` . Подумайте об этих отклонениях как о специальных ароматах традиционного `Observable` . Они не нужны, чтобы использовать библиотеку. Тем не менее, варианты, подобные `Subject` имеют невероятные прецеденты, превосходящие стандарт `Observable` .

Эта статья придерживается стандартного `Observable` . Все операторы потока данных из RxJS работают через `Observable` .

Многие операторы ядра RxJS были созданы из массивов JavaScript. Прототип объекта Array содержит много параллелей с библиотекой RxJS. Они иначе известны как «Дополнительно». Массивы представляют собой потоковые структуры, подобные наблюдаемым парам данных.

Чтобы лучше понять операторы RxJS, в этой статье мы кратко рассмотрим расширения массива JavaScript. Каждый из функций почти идентичен своему RxJS-аналогу. Разница - это просто формат данных (итерируемый массив против итеративного потока).

#### Дополнительно

Массивы содержат множество полезных методов. Эти методы называются Extray Array. Все они существуют на прототипе объекта Array. В приведенном ниже списке содержится пять дополнений с параллелями RxJS.

*   `.reduce`
*   `.filter`
*   `.map`
*   `.every`
*   `.forEach`

Для каждого примера массив выполняет итерацию над собой для получения конечного результата.

`.reduce` минимизирует массив в одно значение. `.filter` массив с булевой оценкой. `.map` преобразует массив по элементам. `.every` оценивает true или false для всего массива в зависимости от логического условия. `.forEach` выполняет `.forEach` через элементы массива.

Массирует потоки модели. Они находятся в порядке друг друга и повторяются один за другим. Наблюдает обтекание элементов данных своим наблюдателям аналогичным образом. Вот почему RxJS включает логическую копию каждого массива в своей библиотеке. Конечно, RxJS предоставляет больше возможностей своим операторам, чем Array Extras.

#### Основные операторы RxJS

Существует буквально целая библиотека для операторов RxJS. В этой статье рассматриваются те, которые перечислены ниже, которые вдохновили Extreme Array.

*   `.reduce`
*   `.filter`
*   `.map`
*   `.every`
*   `.forEach`

Ничего не изменилось из предыдущего списка. Ваше понимание Extray Array относится к операторам RxJS. Единственный улов для этого - это функция, называемая `.pipe` которая увидит много пользы в следующих нескольких примерах. `.pipe` цепи RxJS операторов вместе. Результаты предыдущего оператора переходят в следующий раунд до конечного оператора. Полученные данные затем исходят из наблюдаемого потока.

Обратите внимание на стандартный пример ниже. Используйте его для сравнения для воздействия каждого оператора на испускаемый поток данных.

```typescript
import { Observable, from } from 'rxjs'; 
 
 const stream: number[] = [1, 2, 3, 4, 5]; 
 
 const observable: Observable<number> = from(stream); 
 observable.subscribe((val: number) => console.log(val)); 
 
 // console output 
 /* 
 1 
 2 
 3 
 4 
 5 
 */ 
```

`.from` преобразует массив в объект `Observable` который вызывает `.next` для каждого элемента массива. Функция `.pipe` принимает любое количество аргументов в качестве операторов массива. Здесь каждый оператор реализуется. Операторы выполняют на упорядоченных данных в порядке их реализации в качестве аргументов для `.pipe` .

##### уменьшить

`.reduce` минимизирует поток данных в одно значение перед испусканием.

```typescript
import { reduce } from 'rxjs/operators'; 
 
 const stream: number[] = [1, 2, 3, 4, 5]; 
 
 const observable: Observable<number> = from(stream).pipe( 
  reduce((accum, val) => (accum + val)) 
 ); 
 observable.subscribe((val: number) => console.log(val)); 
 
 // console output 
 /* 
 15 
 */ 
```

##### Фильтр

`.filter` обрезает поток, исключая значения потока, которые не удовлетворяют его условиям.

```typescript
import { filter } from 'rxjs/operators'; 
 
 const stream: number[] = [1, 2, 3, 4, 5]; 
 
 const observable: Observable<number> = from(stream).pipe( 
  filter((val) => (val % 2 === 0)) // filters out odd numbers 
 ); 
 observable.subscribe((val: number) => console.log(val)); 
 
 // console output 
 /* 
 2 
 4 
 */ 
```

##### карта

`.map` и преобразует каждое текущее значение потока.

```typescript
const stream: number[] = [1, 2, 3, 4, 5]; 
 
 const observable: Observable<number> = from(stream).pipe( 
    map((val) => (val + 1)) 
 ); 
 observable.subscribe((val: number) => console.log(val)); 
 
 // console output 
 /* 
 2 
 3 
 4 
 5 
 6 
 */ 
```

##### Задача: Every и ForEach

Со знанием `.every` и `.forEach` как Array Extras, попробуйте реализовать их как операторов RxJS. Не стесняйтесь использовать предыдущие примеры для руководства. Немного практики проходит долгий путь после многих чтений!

#### HTTP в угловом

Этот раздел объединяет RxJS и Angular, чтобы показать, как они взаимодействуют. Как правило, услуга, предоставляемая Angular, обеспечит `Observable` . Поток данных `Observable` может затем мутировать с использованием операторов `.pipe` с `.pipe` .

Угловой предоставляет услугу `HttpClient` через `@angular/common/http` `HttpClient` `@angular/common/http` . `HttpClientModule` также из `@angular/common/http` и экспортирует службу `HttpClient` . Корневой модуль приложения должен импортировать `HttpClientModule` . Это делает `HttpClientModule` _инъекционным в_ любом месте приложения.

Служба `HttpClientModule` HTTP-запросы. Эти запросы являются асинхронными. Что делает их интересными для Angular, так это то, как обрабатывается запрос. `Observable` возвращается с каждым запросом. RxJS может убрать его оттуда.

В следующем примере используется открытый API, созданный [Typicode](https://jsonplaceholder.typicode.com) . API предоставляет массив из 100 элементов на асинхронный `GET` .

```typescript
// ./models/post.model.ts 
 
 export interface Post { 
  userId: number; 
  id: number; 
  title: string; 
  body: string; 
 } 
```

```typescript
// ./services/json.service.ts 
 
 import { HttpClient } from '@angular/common/http'; 
 import { Injectable } from '@angular/core'; 
 
 import { Observable, from } from 'rxjs'; 
 import { switchMap, map, filter, reduce } from 'rxjs/operators'; 
 
 import { Post } from '../models/post.model'; 
 
 @Injectable({ 
  providedIn: 'root' 
 }) 
 export class JsonService { 
  constructor(private http: HttpClient) { } 
 
  getPostsByUserId(id: number): Observable<any> { 
    const trim$ = (stream) => from(stream) 
      .pipe( 
        filter((post: Post) => +post.userId === +id), 
        map((post: Post) => ({ title: post.title, body: post.body })), 
        reduce((accum: Post[], post: Post) => accum.concat([post]), []) 
      ); 
 
    return this.http.get("https://jsonplaceholder.typicode.com/posts") 
      .pipe( 
        switchMap((value) => trim$(value)) 
      ); 
  } 
 } 
```

```typescript
// ./components/example/example.component.ts 
 
 import { Component } from '@angular/core'; 
 
 import { JsonService } from '../../services/json.service'; 
 import { Post } from '../../models/post.model'; 
 
 @Component({ 
  selector: 'app-example', 
  template: ` 
  <h1>Request User Posts</h1> 
  <span>User: </span><input #userInput> 
  <button (click)="requestForPosts(userInput.value)">REQUEST</button> 
  <hr> 
  <ul> 
    <div *ngIf="userPosts"> 
      <div *ngFor="let post of userPosts"> 
        <h3>{{ post.title }}</h3> 
        <p>{{ post.body }}</p> 
      </div> 
    </div> 
    <h3 *ngIf="!userPosts">No posts shown...</h3> 
  </ul> 
  ` 
 }) 
 export class ExampleComponent { 
  userPosts: Post[]; 
 
  constructor(private json: JsonService) { } 
 
  requestForPosts(id: number): void { 
    this.json.getPostsByUserId(id) 
      .subscribe((posts: Post[]) => { this.userPosts = posts.length > 0 ? posts : null; }); 
  } 
 } 
```

`json.service.ts` создает `Observable` от имени `component.example.ts` . Компонент может подписаться на возвращаемый `Observable` . Только одно значение излучает к тому времени, когда `Observable` разрешает поток данных.

Запрос `jsonplaceholder.typicode.com` дает один массив из 100 сообщений. Запрос через `HttpClient` дает `Observable` . Оператор `switchMap` возвращает другое `Observable` которое перезаписывает текущий поток. Переменная `trim$` хранит `Observable` как ее значение. Добавление `$` к переменной, используемой для хранения `Observable` s, является условным.

`from` конвертирует массив из `jsonplaceholder.typicode.com` в 100-значение, излучающее `Observable` . Затем операторы RxJS просеивают каждую часть данных в потоке. Они удаляют значения потока, не относящиеся к запросу. Обрезка данных происходит таким образом, что значения потока остаются ненужными. Окончательные результаты снова объединяются в единый массив, который испускает его наблюдателям.

В `component.example.ts` JsonService ссылается на метод, возвращающий только что описанный `Observable` . Этот метод вызывается при нажатии кнопки в шаблоне компонента. Поле ввода также в шаблоне предоставляет один аргумент `id` .

При нажатии кнопки JsonService возвращает `Observable` который испускает один массив. `.subscribe` вызывает на возвращаемом `Observable` . Затем компонент устанавливает значение `userPosts` равным испускаемому массиву.

Обнаружение углового изменения фиксирует изменение данных класса. Обновления шаблона и `*ngFor` обеспечивают, чтобы каждый элемент массива из `userPosts` отображал свой собственный элемент шаблона.

#### Вывод

RxJS обеспечивает ядро `Observable` вместе со своими операторами. Библиотека автоматически устанавливается из командной строки с помощью `ng new [name-of-app]` (Угловая CLI). Типы и операторы ядра RxJS загружаются соответственно в `rxjs` и `rxjs/operators` .

Даже если вы не используете CLI, такие сервисы, как `HttpClient` , все еще пригодны для использования. Служба возвращает `Promise` вместо `Observable` если RxJS недоступен. Объект `Promise` является родным для JavaScript в отличие от `Observable` . Вероятно, это изменится в следующем официальном выпуске JavaScript.

Тем не менее, в полной мере используйте RxJS! Любая итеративная структура может вместить `Observable` . С его помощью вся библиотека RxJS становится пригодной для использования. Его операторы эффективно преобразуют данные из потока в результаты. Кроме того, наблюдатели могут подписаться на результаты, увеличивая общую переносимость данных.

## источники

*   [Угловая команда. «Библиотека RxJS». _Google_ . Доступ к 5 июня 2018 года.](https://angular.io/guide/rx-library)
*   [Форбс, Эллиот. «Создание приложения реального времени с помощью Angular и Socket.io Tutorial». _TutorialEdge.net_ , 10 января 2017. Доступен 5 июня 2018 года.](https://tutorialedge.net/typescript/angular/angular-socket-io-tutorial)
*   [Команда RxJS. «Документация RxJS». _RxJS_ . Доступ к 5 июня 2018 года.](https://rxjs-dev.firebaseapp.com)
*   [Сукале, Райан. «Разница между субъектом Rxjs и наблюдаемым». _TutorialHorizon_ , 23 марта 2017. Доступен 5 июня 2018 года.](https://javascript.tutorialhorizon.com/2017/03/23/rxjs-subject-vs-observable)
*   [Сообщество Википедии. «Реактивное программирование». _Википедия_ , 2 июня 2018. Доступен 5 июня 2018 года.](https://en.wikipedia.org/wiki/Reactive_programming)

## Ресурсы

*   [Угловая документация](https://angular.io/guide)
*   [Угловая на GitHub](https://github.com/angular/angular)
*   [Угловая CLI](https://cli.angular.io)
*   [RxJS и угловой](https://angular.io/guide/rx-library)
*   [Реактивное программирование](https://en.wikipedia.org/wiki/Reactive_programming)
*   [Реактивные расширения для JavaScript](https://rxjs-dev.firebaseapp.com)