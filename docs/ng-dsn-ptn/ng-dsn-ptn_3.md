# 第三章：经典模式

TypeScript 是一种面向对象的编程语言，因此我们可以利用几十年关于面向对象架构的知识。在本章中，我们将探索一些最有用的面向对象设计模式，并学习如何在 Angular 中应用它们。

Angular 本身就是一个面向对象的框架，它强制你以某种方式进行大部分开发。例如，你需要有组件、服务、管道等。强制这些构建块对你有助于构建良好的架构，就像 Zend 框架对 PHP 或 Ruby on Rails 对 Ruby 所做的那样。当然，框架的存在是为了让你的生活更轻松，加快开发时间。

虽然 Angular 的设计方式远远超出了平均水平，但我们总是可以做得更好。我并不是说我在本章中提出的是最终设计，或者你将能够用它来解决从面包店网页到火星一号任务的仪表板的任何问题——不幸的是，这样的设计并不存在——但它肯定会丰富你的工具库。

在本章中，我们将看到以下经典模式：

+   组件

+   单例

+   观察者

# 组件

在这本书的前三章中，我们看到了大量的 Angular 组件。Angular `Component`是 Angular 应用程序的主要构建块之一，例如`services`，`pipes`等。作为提醒，TypeScript 类使用以下注解成为 Angular 组件：

```ts
import { Component } from '@angular/core'; 

@Component({ 
  selector: 'app-root', 
  templateUrl: './app.component.html', 
  styleUrls: ['./app.component.css'] 
}) 
export class AppComponent { 
  title = 'app'; 
} 
```

在这里，`AppComponent`类通过`selector`，`templateUrl`和`styleUrls` Angular 组件的行为得到了增强。

# 单例模式

用于前端应用程序的另一个方便的模式是单例模式。单例模式确保你的程序中只存在一个给定对象的实例。此外，它提供了对对象的全局访问点。

实际上看起来是这样的：

```ts
export class MySingleton{ 

    //The constructor is private so we  
    //can't do `let singleton:MySingleton = new MySingleton();` 
    private static instance:MySingleton = null; 

    private constructor(){ 

    } 

    public static getInstance():MySingleton{ 
        if(MySingleton.instance == null){ 
            MySingleton.instance = new MySingleton(); 
        }
```

```ts
 return MySingleton.instance; 
    } 
} 
 let singleton:MySingleton = MySingleton.getInstance();
```

我们有一个具有`private static instance:MySingleton`属性的类。然后，我们有一个私有构造函数，使以下操作失败：

```ts
let singleton:MySingleton = new MySingleton(); 
```

请注意，它失败是因为你的 TypeScript 转译器对可见性提出了抱怨。然而，如果你将`MySingleton`类转译为 JavaScript 并将其导入到另一个 TypeScript 项目中，你将能够使用*new*运算符，因为转译后的 TypeScript 没有任何可见性。

这种相当简单的单例模式实现的问题在于并发。确实，如果两个进程同时调用`getInstance():MySingleton`，那么程序中将会有两个`MySingleton`的实例。为了确保这种情况不会发生，我们可以使用一种称为早期实例化的技术：

```ts
export

 class MySingleton
 {
   private static instance : MySingleton = new MySingleton();
 private constructor()
  {
 }

 } singleton: MySingleton = MySingleton.getInstance();
```

虽然你可以在 TypeScript 中实现你的单例，但你也可以利用 Angular 创建单例的方式：服务！确实，在 Angular 中，服务只被实例化一次，并且被注入到任何需要它的组件中。下面是一个通过本书之前看到的`NgModule`进行服务和注入的示例：

```ts

 import { Injectable } from '@angular/core'; 

@Injectable() 
export class ApiService { 

  private static increment:number = 0; 

  public constructor(){ 
    ApiService.increment++; 
  } 

  public toString() :string { 
    return "Current instance: " + ApiService.increment; 
  } 

} 
 // ./app.component.ts

 import { Component } from '@angular/core'; 
import { ApiService } from './api.service'; 

@Component({ 
  selector: 'app-root', 
  templateUrl: './app.component.html', 
  styleUrls: ['./app.component.css'] 
}) 
export class AppComponent { 
  title = 'app'; 

  public constructor(api:ApiService){ 
    console.log(api); 
  } 
} 

 // ./other/other.component.ts

 import { Component, OnInit } from '@angular/core'; 
import { ApiService } from './../api.service'; 

@Component({ 
  selector: 'app-other', 
  templateUrl: './other.component.html', 
  styleUrls: ['./other.component.css'] 
}) 
export class OtherComponent implements OnInit { 

  public constructor(api:ApiService){ 
    console.log(api); 
  } 

  ngOnInit() { 
  } 

} 

 //app.module.ts

 import { BrowserModule } from '@angular/platform-browser'; 
import { NgModule } from '@angular/core'; 
import { MySingleton } from './singleton'; 

import { AppComponent } from './app.component'; 
import { OtherComponent } from './other/other.component'; 

import { ApiService } from './api.service'; 

@NgModule({ 
  declarations: [ 
    AppComponent, 
    OtherComponent 
  ], 
  imports: [ 
    BrowserModule 
  ], 
  providers: [ApiService], 
  bootstrap: [AppComponent] 
}) 
export class AppModule { 

} 
```

在上述代码中，我们有以下内容：

+   `APIService`显示了`@Injectable()`注解，使其可以被注入。此外，`APIService`有一个`increment:number`属性，每次创建新实例时都会递增。由于`increment:number`是静态的，它将准确告诉我们程序中有多少个实例。最后，`APIService`有一个`toString:string`方法，返回当前实例编号。

+   `AppComponent`是一个经典组件，它接收了`APIService`的注入。

+   `OtherComponent`是另一个经典组件，它接收了`APIService`的注入。

+   `/app.module.ts`包含了`NgModule`。在`NgModule`中，这里显示的大部分声明已经在本书中讨论过。新颖之处来自于`providers: [APIService]`部分。在这里，我们为`APIService`本身声明了一个提供者。由于`APIService`并没有做什么太疯狂的事情，它本身就足够了，并且可以通过引用类来提供。而更复杂的服务，例如它们自己需要注入的服务，需要定制的提供者。

现在，如果我们导航到这两个组件，结果将如下：

```ts
Current instance: 1
Current instance: 1
```

这证明只创建了一个实例，并且相同的实例已被注入到两个组件中。因此，我们有一个单例。然而，这个单例虽然方便，但并不是真正安全的。你为什么这样问？嗯，`APIService`也可以在组件级别提供，就像这样：

```ts
import { Component } from '@angular/core'; 
import { ApiService } from './api.service'; 

@Component({ 
  selector: 'app-root', 
  templateUrl: './app.component.html', 
  styleUrls: ['./app.component.css'] 
}) 
export class AppComponent { 
  title = 'app'; 

  public constructor(api:ApiService){ 
    console.log(api); 
  } 
} 
 // ./other.component.ts

 @Component({ 
  selector: 'app-root', 
  templateUrl: './app.component.html', 
  styleUrls: ['./app.component.css'] 
  providers: [APIService],
 })
 export class OtherComponent implements OnInit { 

  public constructor(api:ApiService){ 
    console.log(api); 
  } 

  ngOnInit() { 
  } 

} 
```

在这种情况下，将创建两个单独的实例，导致以下输出：

```ts
Current instance: 1
Current instance: 2
```

因此，使用 Angular 服务，你无法强制使用单例模式，与其普通的 TypeScript 对应相反。此外，普通的 TypeScript 比 Angular 服务快上一个数量级，因为我们完全跳过了注入过程。确切的数字严重依赖于你的机器的 CPU/RAM。

在单例的情况下，唯一剩下的问题是何时使用它或哪种实现效果最好。单例只强制在程序中给定类的一个实例。因此，它非常适合与后端的任何通信或任何硬件访问。例如，在与后端的通信的情况下，可能希望只有一个`APIService`处理 API 密钥、API 限制和整个板块的`csrf`令牌，而无需确保我们在所有组件、模型等中传递相同的服务实例。在硬件访问的情况下，您可能希望确保您只打开一个与用户的网络摄像头或麦克风的连接，以便在完成后可以正确释放它们。

在性能方面，以下是每种实现的结果，以毫秒为单位。我运行了每个版本 100 次，排除了异常值（最好和最差的 5%），并对剩下的 90 次调用进行了平均，如下表所示：

| **单例懒加载** | **单例早期加载** | **服务注入** |
| --- | --- | --- |
| 196 毫秒 | 183 毫秒 | 186 毫秒 |

我运行的代码如下：

```ts
import { Component } from '@angular/core';

 import {MySingleton} from './singleton';
 import { SingletonService } from './singleton.service';

 @Component({
 selector: 'app-root',
 templateUrl: './app.component.html',
 styleUrls: ['./app.component.css']
 })
 export class AppComponent {
 title = 'app works!';

 constructor(private singleton:SingletonService){
 singleton.doStuff();
 }
 //OR
 constructor(){
 MySingleton.getInstance().doStuff();
 }
 }
```

对于服务注入的实验，我不得不在`app.module.ts`中添加以下行：`providers: [SingletonService]`。

令我惊讶的是，两种方法的结果相差不大。早期实例化的单例实现仅比更实用的服务注入好 2%。懒加载的单例排名第三，用时 196 毫秒（比早期实例化的单例差 7%，比服务注入差 5%）。

# 工厂方法

假设我们有一个带有两个私有变量`lastName:string`和`firstName:string`的`User`类。此外，这个简单的类提供了`hello`方法，打印出`"Hi I am", this.firstName, this.lastName`：

```ts
class User{
 constructor(private lastName:string, private firstName:string){
 }
 hello(){
 console.log("Hi I am", this.firstName, this.lastName);
 }
 }
```

现在，考虑到我们通过 JSON API 接收用户。它很可能看起来像这样：

```ts
[{"lastName":"Nayrolles","firstName":"Mathieu"}...]. 
```

通过以下代码片段，我们可以创建一个`User`：

```ts
let userFromJSONAPI: User = JSON.parse('[{"lastName":"Nayrolles","firstName":"Mathieu"}]')[0];
```

到目前为止，TypeScript 编译器没有抱怨，并且执行顺利。这是因为`parse`方法返回`any`（例如，Java 对象的 TypeScript 等价物）。当然，我们可以将`any`转换为`User`。然而，`userFromJSONAPI.hello();`将产生以下结果：

```ts
json.ts:19
 userFromJSONAPI.hello();
 ^
 TypeError: userFromUJSONAPI.hello is not a function
 at Object.<anonymous> (json.ts:19:18)
 at Module._compile (module.js:541:32)
 at Object.loader (/usr/lib/node_modules/ts-node/src/ts-node.ts:225:14)
 at Module.load (module.js:458:32)
 at tryModuleLoad (module.js:417:12)
 at Function.Module._load (module.js:409:3)
 at Function.Module.runMain (module.js:575:10)
 at Object.<anonymous> (/usr/lib/node_modules/ts-node/src/bin/ts-node.ts:110:12)
 at Module._compile (module.js:541:32)
 at Object.Module._extensions..js (module.js:550:10)
```

为什么？好吧，赋值的左侧被定义为`User`，但当我们将其转译为 JavaScript 时，它将被抹去。

在 TypeScript 中进行类型安全的方式如下：

```ts
let validUser = JSON.parse('[{"lastName":"Nayrolles","firstName":"Mathieu"}]')
 .map((json: any):User => {
 return new User(json.lastName, json.firstName);
 })[0];
```

有趣的是，函数的类型也不会帮助你。在这两种情况下，它都会显示`object`而不是`User`，因为 JavaScript 中并不存在用户的概念。

虽然直接的类型安全方法可以工作，但它并不是非常可扩展或可重用的。事实上，地图回调方法必须在接收 JSON 用户的任何地方重复。最方便的方法是通过`Factory`模式来做。工厂用于创建对象，而不会将实例化逻辑暴露给客户端。

如果我们要创建一个用户的工厂，它会是这样的：

```ts

 export class POTOFactory{

 /**
 * Builds an User from json response
 * @param  {any}  jsonUser
 * @return {User} 
 */
 static buildUser(jsonUser: any): User {

 return new User(
 jsonUser.firstName,
 jsonUser.lastName
 );
 }

 }
```

在这里，我们有一个名为`buildUser`的`static`方法，它接收一个 JSON 对象，并从 JSON 对象中获取所有必需的值，以调用一个假设的`User`构造函数。这个方法是静态的，就像工厂的所有方法一样。事实上，在工厂中我们不需要保存任何状态或实例绑定的变量；我们只是将用户的创建封装起来。请注意，你的工厂可能会与你的 POTO 的其余部分共享。

# 观察者

允许一个名为主题的对象跟踪其他对象（称为观察者）对主题状态感兴趣的可观察模式。当主题状态改变时，它会通知观察者。这背后的机制非常简单。

让我们来看一下在纯 TypeScript 中（没有任何 Angular 2 或任何框架，只是 TypeScript）实现的观察者/主题实现。首先，我定义了一个`Observer`接口，任何具体的实现都必须实现：

```ts
export interface Observer{ notify(); }
```

这个接口只定义了`notify()`方法。当被观察对象的状态改变时，主题（观察者观察的对象）将调用这个方法。然后，我有一个这个接口的实现，名为`HumanObserver`：

```ts
export class HumanObserver implements Observer{ 
 constructor(private name:string){}

 notify(){

 console.log(this.name, 'Notified');
 } } 
```

这个实现利用了 TypeScript 属性构造函数，其中你可以在构造函数内部定义类的属性。这种表示法与以下表示法完全等效，但更短：

```ts
private name:string; constructor(name:string){

 this.name = name;
}
```

在定义了`Observer`接口和`HumanObserver`之后，我们可以继续进行主题。我定义了一个管理观察者的主题类。这个类有三个方法：`attachObserver`，`detachObserver`和`notifyObservers`：

```ts
export class Subject{ private observers:Observer[] = [];

/**
* Adding an observer to the list of observers
*/
attachObserver(observer:Observer):void{

 this.observers.push(observer);
}

/**
* Detaching an observer
*/
detachObserver(observer:Observer):void{

 let index:number = this.observers.indexOf(observer);

 if(index > -1){

 this.observers.splice(index, 1);
 }else{

 throw "Unknown observer";
 }
}

/**
* Notify all the observers in this.observers
*/
protected notifyObservers(){

 for (var i = 0; i < this.observers.length; ++i) {

 this.observers[i].notify();
 }
} } 
```

`attachObserver`方法将新的观察者推入`observers`属性中，而`detachObserver`则将它们移除。

主题实现通常以 attach/detach，subscribe/unsubscribe 或 add/delete 前缀的形式出现。

最后一个方法是`notifyObservers`，它遍历观察者并调用它们的通知方法。允许我们展示可观察机制的最后一个类是 IMDB，它扩展了`subject`。它将在添加电影时通知观察者：

```ts
export class IMDB extends Subject{

 private movies:string[] = [];

 public addMovie(movie:string){

 this.movies.push(movie);
 this.notifyObservers();
 }
 }
```

为了使这些部分彼此通信，我们必须：创建一个`Subject`，创建一个`Observer`，将`Observer`附加到`Subject`，并通过`addMovie`方法改变主题的状态。

更具体地说，以下是先前列表的实现：

```ts
let imdb:IMDB = new IMDB();
 let mathieu:HumanObserver = new HumanObserver("Mathieu");
 imbd.attachObserver(mathieu);
 imbd.addMovie("Jaws");
```

为了加快我们的开发过程，我们将安装`ts-node`。这个 node 包将把 TypeScript 文件转译成 JavaScript，并解决这些文件之间的依赖关系。

输出是`Mathieu Notified`。我们可以尝试分离`mathieu`并添加另一部电影：

```ts
imdb.detachObserver(mathieu);
 imdb.addMovie("Die Hard");
```

输出仍然是`Mathieu Notified`，这发生在我们添加`Jaws`电影之后。第二部电影（`Die Hard`）的添加并不会触发控制台打印`Mathieu Notified`，因为它已经被分离。

# 带参数的 TypeScript 可观察对象

因此，这是观察者模式的一个基本实现。然而，它并不完全成熟，因为`HumanObserver`只知道它观察的主题中的某些东西发生了变化。因此，它必须遍历它观察的所有主题，并检查它们的先前状态与当前状态，以确定发生了什么变化以及在哪里发生了变化。更好的方法是修改`Observer`的`notify`，使其包含更多信息。例如，我们可以添加可选参数如下：

```ts
export interface Observer{

 notify(value?:any, subject?:Subject);
 }

 export class HumanObserver implements Observer{

 constructor(private name:string){}

 notify(value?:any, subject?:Subject){

 console.log(this.name, 'received', value, 'from', subject);
 }
 }
```

`notify()`方法现在接受一个可选的值参数，用于描述`subject`对象的新状态。我们还可以接收到`Subject`对象本身的引用。这在观察者观察多个主题时非常有用。在这种情况下，我们需要能够区分它们。因此，我们必须稍微更改 Subject 和 IMDB，以便它们使用新的通知：

```ts
export class Subject{

 private observers:Observer[] = [];

 attachObserver(oberver:Observer):void{

 this.obervers.push(oberver);
 }

 detachObserver(observer:Observer):void{
 let index:number = this.obervers.indexOf(observer);
 if(index > -1){
 this.observers.splice(index, 1);

 }else{

 throw "Unknown observer";
 }
 }

 protected notifyObservers(value?:any){

 for (var i = 0; i < this.obervers.length; ++i) {

 this.observers[i].notify(value, this);
 }
 }
 }

 export class IMDB extends Subject{

 private movies:string[] = [];

 public addMovie(movie:string){

 this.movies.push(movie);
 this.notifyObservers(movie);
 }
 }
```

最后，输出如下：

```ts
 Mathieu received Jaws from IMDB {

 observers: [ HumanObserver { name: 'Mathieu' } ],
 movies: [ 'Jaws' ] }
```

这比`Mathieu Notified`更具表现力。现在，当我们使用观察者模式进行异步编程时，我们真正的意思是要求某些东西，并且在其处理过程中不想等待做任何事情。相反，我们订阅响应事件以在响应到来时得到通知。在接下来的章节中，我们将使用相同的模式和机制与 Angular 一起使用。

# 观察 HTTP 响应

在本节中，我们将构建一个 JSON API，根据搜索参数返回电影。我们不仅仅是等待 HTTP 查询完成，而是利用观察者设计模式的力量，让用户知道我们正在等待，并且如果需要，执行其他进程。首先，我们需要一个数据源来构建我们的类似 IMDB 的应用程序。构建和部署一个能够解释 HTTP 查询并相应发送结果的服务器端应用程序现在相对简单。然而，这超出了本书的范围。相反，我们将获取托管在[`bit.ly/mastering-angular2-marvel`](http://bit.ly/mastering-angular2-marvel)的静态 JSON 文件。该文件包含漫威电影宇宙的一些最新电影。它包含一个描述 14 部电影的 JSON 数组作为 JSON 对象。这是第一部电影：

```ts
 {
 "movie_id" : 1,
 "title" : "The Incredible Hulk",
 "phase" : "Phase One: Avengers Assembled",
 "category_name" : "Action",
 "release_year" : 2005,
 "running_time" : 135,
 "rating_name" : "PG-13",
 "disc_format_name" : "Blu-ray",
 "number_discs" : 1,
 "viewing_format_name" : "Widescreen",
 "aspect_ratio_name" : " 2.35:1",
 "status" : 1,
 "release_date" : "June 8, 2008",
 "budget" : "150,000,000",
 "gross" : "263,400,000",
 "time_stamp" : "2018-06-08"
 },
```

您可以找到类似 IMDB 的应用程序提供的标准信息，例如发行年份，播放时间等。我们的目标是设计一个异步的 JSON API，使每个字段都可以搜索。

由于我们正在获取一个静态的 JSON 文件（我们不会插入、更新或删除任何元素），可接受的 API 调用如下：

```ts
IMDBAPI.fetchOneById(1);
 IMDBAPI.fetchByFields(MovieFields.release_date, 2015);
```

第一个调用只是获取`movie_id = 1`的电影；第二个调用是一个更通用的调用，可以在任何字段中工作。为了防止 API 使用者请求我们电影中不存在的字段，我们使用在`Movie`类内部定义的枚举器限制字段值。现在，这里的重要部分是这些调用的实际返回。实际上，它们将触发一个可观察机制，在这种机制中，调用者将附加到一个可观察的 HTTP 调用。然后，当 HTTP 调用完成并根据查询参数过滤结果时，被调用者将通知调用者有关响应。因此，调用者不必等待被调用者（`IMDBAPI`），因为他们将在请求完成时收到通知。

# 实施

让我们深入实现。首先，我们需要使用 Angular CLI 创建一个新的 Angular 项目：

```ts
mkdir angular-observable
 ng init
 ng serve
```

接下来，我们需要一个模型来表示电影概念。我们将使用`ng g class` models/Movie 命令行生成这个类。然后，我们可以添加一个构造函数，定义`Movie`模型的所有私有字段，这与我们为 getter 和 setter 所做的相同。

```ts
export class Movie {

 public constructor(
 private _movie_id:number,
 private _title: string,
 private _phase: string,
 private _category_name: string,
 private _release_year: number,
 private _running_time: number,
 private _rating_name: string,
 private _disc_format_name: string,
 private _number_discs: number,
 private _viewing_format_name: string,
 private _aspect_ratio_name: string,
 private _status: string,
 private _release_date: string,
 private _budget: number,
 private _gross: number,
 private _time_stamp:Date){
 }

 public toString = () : string => {

 return `Movie (movie_id: ${this._movie_id},
 title: ${this._title},
 phase: ${this._phase},
 category_name: ${this._category_name},
 release_year: ${this._release_year},
 running_time: ${this._running_time},
 rating_name: ${this._rating_name},
 disc_format_name: ${this._disc_format_name},
 number_discs: ${this._number_discs},
 viewing_format_name: ${this._viewing_format_name},
 aspect_ratio_name: ${this._aspect_ratio_name},
 status: ${this._status},
 release_date: ${this._release_date},
 budget: ${this._budget},
 gross: ${this._gross},
 time_stamp: ${this._time_stamp})`;

 }
 //GETTER
 //SETTER
 }

 export enum MovieFields{
 movie_id,
 title,
 phase,
 category_name,
 release_year,
 running_time,
 rating_name,
 disc_format_name,
 number_discs,
 viewing_format_name,
 aspect_ratio_name,
 status,
 release_date,
 budget,
 gross,
 time_stamp
 }
```

在这里，电影 JSON 定义的每个字段都使用构造函数属性声明映射到`Movie`类的私有成员

of TypeScript. We also override the `toString` method so that it prints every field. In the `toString` method, we take advantage of multi-line strings provided by the backtick (```ts) and the `${}` syntax that allows the concatenation of strings and different variables. Then, we have an enumerator called `MovieFields` that will allow us to restrict the searchable field.

Moving on, we need to generate the `IMDBAPI` class. As the `IMDBAPI` class will be potentially used everywhere in our program, we will make it a service. The advantage is that services can be injected into any component or directive. Moreover, we can choose if we want Angular 2 to create an instance of the `IMDBAPI` per injection or always inject the same instance. If the provider for the `IMDBAPI` is created at the application level, then the same instance of the `IMDBAPI` will be served to anyone requesting it. At the component level, however, a new instance of the `IMDBAPI` will be created and served to the component each time the said component is instantiated. In our case, it makes more sense to have only one instance of the `IMDBAPI`, as it will not have any particular states that are susceptible to change from component to component. Let's generate the `IMDBAPI` service (`ng g s` `services/IMDBAPI`) and implement the two methods we defined earlier:

```

IMDBAPI.fetchOneById(1);

IMDBAPI.fetchByFields(MovieFields.release_date, 2015);

```ts

Here's the IMDAPI service with the `fetchOneById` method:

```

import { Injectable } from '@angular/core';

import { Http }  from '@angular/http';

import { Movie, MovieFields } from '../models/movie';

import { Observable } from 'rxjs/Rx';

import 'rxjs/Rx';

@Injectable()

export class IMDBAPIService {

private moviesUrl:string = "app/marvel-cinematic-universe.json";

constructor(private http: Http) { }

/**

* 返回一个匹配 id 的 Movie 的 Observable

* @param  {number}           id

* @return {Observable<Movie>}

*/

public fetchOneById(id:number):Observable<Movie>{

console.log('fetchOneById', id);

返回 this.http.get(this.moviesUrl)

/**

* Transforms the result of the HTTP get, which is observable

* into one observable by item.

*/

.flatMap(res => res.json().movies)

/**

* Filters movies by their movie_id

*/

.filter((movie:any)=>{

console.log("filter", movie);

return (movie.movie_id === id)

})

/**

* 将 JSON 电影项映射到 Movie 模型

*/

.map((movie:any) => {

console.log("map", movie);

return new Movie(

movie.movie_id,

movie.title,

movie.phase,

movie.category_name,

movie.release_year,

movie.running_time,

movie.rating_name,

movie.disc_format_name,

movie.number_discs,

movie.viewing_format_name,

movie.aspect_ratio_name,

movie.status,

movie.release_date,

movie.budget,

movie.gross,

movie.time_stamp

);

});

}

}

```ts

# Understanding the implementation

Let's break it down chunk by chunk. First, the declaration of the service is pretty standard:

```

import { Injectable } from '@angular/core';

import { Http } from '@angular/http';

import { Movie, MovieFields } from '../models/movie';

import { Observable } from 'rxjs/Rx';

import 'rxjs/Rx';

@Injectable()

export class IMDBAPIService {

private moviesUrl:string = "app/marvel-cinematic-universe.json";

constructor(private http: Http) { }

```ts

Services are injectable. Consequently, we need to import and add the `@Injectable` annotation. We also import `Http`, `Movie`, `MovieFields`, `Observable`, and the operators of `Rxjs`. **RxJS** stands for **reactive extensions for JavaScript**. It is an API to perform observer, iterator, and functional programming. When it comes to asynchronism in Angular 2, you rely on RxJS for the most part.

One important thing to note is that we use RxJS 5.0, which is a complete rewrite, based on the same concept of RxJS 4.0.

`IMDBAPIService` also has a reference to the path of our JSON file and a constructor to receive an injection of the HTTP service. On the implementation of the `fetchOneById` method, we can see four distinct operations chained to each other: `get`, `flatMap`, `filter`, and `map`. `Get` returns an observable on the body of the HTTP request. `flatMap` transforms the `get Observable` by applying a function that you specify for each item emitted by the source `Observable`, where that function returns an `Observable` that emits items. `FlatMap` then merges the emissions of these resultant`Observables`, emitting these merged results as its sequence. In our case, it means that we will apply the next two operations (filter and map) on all the items received from the HTTP get. The filter checks if the ID of the current movie is the one we are looking to Map transform the JSON representation of a movie into the typeScript representation of a movie (such as the `Movie` class).

This last operation, while counter-intuitive, is mandatory. Indeed, one could think that the JSON representation and the TypeScript representation are identical as they own the same fields. However, the TypeScript representation, as well as its properties, define functions such as `toString`, the getters, and the setters. Removing the map would return an `Object` instance containing all the fields of`Movie` without being one. Also, a typecast will not help you there. Indeed, the TypeScript transpiler will allow you to cast an `Object` into a `Movie`, but it still won't have the methods defined in the `Movie` class as the concept of static typing disappears when the TypeScript is transpiled into JavaScript. The following would  fail to transpile at execution time:

```

movie.movie_id(25) TypeError: movie.movie_id is not a function at Object.<anonymous>

movie: Movie = JSON.parse(`{

"movie_id" : 1,

"title" : "Iron Man",

"phase" : "Phase One: Avengers Assembled",

"category_name" : "Action",

"release_year" : 2015,

"running_time" : 126,

"rating_name" : "PG-13",

"disc_format_name" : "Blu-ray",

"number_discs" : 1,

"viewing_format_name" : "Widescreen",

"aspect_ratio_name" : " 2.35:1",

"status" : 1,

"release_date" : "May 2, 2008",

"budget" : "140,000,000",

"gross" : "318,298,180",

"time_stamp" : "2015-05-03"

}`);

Console.log(movie.movie_id(25));

```ts

Now, if we want to use our `IMDB` service, further modifications of the code that was generated by the Angular CLI is required. First, we need to modify `main.ts` so that it looks like this:

```

import{ bootstrap } from '@angular/platform-browser-dynamic';

import{ enableProdMode } from '@angular/core';

import{ AngularObservableAppComponent, environment } from './app/';

import{ IMDBAPIService } from './app/services/imdbapi.service';

import { HTTP_PROVIDERS } from '@angular/http';

if(environment.production) {

enableProdMode();

}

```ts

```

bootstrap(AngularObservableAppComponent,

[IMDBAPIService , HTTP_PROVIDERS]

);

```ts

The lines in bold represent what has been added. We import our `IMDBService` and the `HTTP_PROVIDERS`. Both providers are declared at the application level, meaning that the instance that will be injected into the controller or directive will always be the same.

Then, we modify the `angular-observable.component.ts` file that was generated and add the following:

```

import { Component } from '@angular/core';

import { IMDBAPIService } from './services/imdbapi.service';

import { Movie } from './models/movie';

@Component({

moduleId: module.id,

selector: 'angular-observable-app',

templateUrl: 'angular-observable.component.html',

styleUrls: ['angular-observable.component.css']

})

export class AngularObservableAppComponent {

title = 'angular-observable works!';

private movies:Movie[] = [];

private error:boolean = false;

private finished:boolean = false;

constructor(private IMDBAPI:IMDBAPIService){

this.IMDBAPI.fetchOneById(1).subscribe(

value => {this.movies.push(value); console.log("Component",value)},

error => this.error = true,

() => this.finished =true

)

}

}

```ts

We have added several properties to `AngularObservableAppComponent`: `movies`, `error`, and `finished`. The first property is an array of `Movie` that will store the result of our queries, and the second and the third properties flag for `error` and `termination`. In the constructor, we have an injection of `IMDBAPIService`, and we subscribe to the result of the `fetchOneById` method. The `subscribe` method expects three callbacks:

*   **Observer:** Receives the value yield by the observed method. It is the RxJS equivalent of the notifying method we saw earlier in this chapter.
*   **Error** (**Optional**): Triggered in the case that the observed object yields an error.
*   **Complete** (**Optional**): Triggered on completion. 

Finally, we can modify the `angular-ob``servable.component.html` file to map the`movie` property of the `AngularObservableAppComponent` array:

```

<h1>

{{title}}

</h1>

<ul>

<li *ngFor = "let movie of movies">{{movie}}</li>

</ul>

```ts

We can see that the first movie item has been correctly inserted into our `ul`/`li` HTML structure. What's really interesting about this code is the order in which things execute. Analyzing the log helps us to grasp the true power of asynchronism in Angular with RxJS. Here's what the console looks like after the execution of our code:

```

javascript fetchOneById 1 :4200/app/services/imdbapi.service.js:30 过滤对象 :4200/app/services/imdbapi.service.js:34 映射对象 :4200/app/angular-observable.component.js:21 组件 电影宽高比名称："2.35:1"_ 预算："140,000,000"_ 类别名称："动作"_ 光盘格式名称："蓝光"_ 总票房："318,298,180"_ 电影 ID：1_ 光盘数量：1_ 阶段："第一阶段：复仇者联盟"_ 评级名称："PG-13"_ 上映日期："2008 年 5 月 2 日"_ 上映年份：2015_ 片长：126_ 状态：1_ 时间戳："2015-05-03"_ 标题："钢铁侠"_ 观看格式名称："宽屏"宽高比名称：(...)预算：(...)类别名称：(...)光盘格式名称：(...)总票房：(...)电影 ID：(...)光盘数量：(...)阶段：(...)评级名称：(...)上映日期：(...)上映年份：(...)片长：(...)状态：(...)时间戳：(...)标题：(...)toString：()观看格式名称：(...)__proto__：对象 :4200/app/services/imdbapi.service.js:30 过滤对象 :4200/app/services/imdbapi.service.js:30 过滤对象 :4200/app/services/imdbapi.service.js:30 过滤对象 :4200/app/services/imdbapi.service.js:30 过滤对象 :4200/app/services/imdbapi.service.js:30 过滤对象 :4200/app/services/imdbapi.service.js:30 过滤对象 :4200/app/services/imdbapi.service.js:30 过滤对象 :4200/app/services/imdbapi.service.js:30 过滤对象 :4200/app/services/imdbapi.service.js:30 过滤对象 :4200/app/services/imdbapi.service.js:30 过滤对象 :4200/app/services/imdbapi.service.js:30 过滤对象 :4200/app/services/imdbapi.service.js:30 过滤对象 :4200/app/services/imdbapi.service.js:30 过滤对象

```ts

As you can see, `AngularObservableAppComponent` was notified that a movie matching the query was found before the filter function analyzed all the items. As a reminder, the order of operations inside `fetchOneById` by ID was: `get`, `flatMap`, `filter`, and `map`, and we have a logging statement in the `filter` and `map` method as well. So, here, the `filter` operation analyzes the first item, which happens to be the one we look for (`movie_id===1`), and forwards it to the map operations that transform it into a `Movie`. This `Movie` is sent right away to`AngularObservableAppComponent`. We can clearly see that the received object in the `AngularObservableAppComponent` component is from the `Movie` type as the console gives us our overriding of the `toString` method. Then, the filter operation continues with the rest of the items. None of them match. Consequently, we do not have any more notifications. Let's test this further with a second method, `IMDBAPI.fetchByField`:

```

public fetchByField(field:MovieFields, value:any){

控制台.log('fetchByField', field, value);

返回 this.http.get（this.moviesUrl）

.flatMap(res => res.json().movies)

/**

* 根据字段过滤电影

*/

.filter((电影:any) =>{

控制台.log("过滤"，电影);

返回（电影[MovieFields[field]] ===值）

})

/**

* 将 JSON 电影项目映射到电影模型

*/

.map((电影:any) => {

控制台.log（"映射"，电影）;

返回新的电影(

电影电影 ID，

电影标题，

电影.phase，

电影类别名称，

电影上映年份，

电影片长，

电影评级名称，

电影光盘格式名称，

电影光盘数量，

电影观看格式名称，

电影宽高比名称，

电影状态，

电影上映日期，

电影预算，

电影总票房，

电影时间戳

);

}); }

```ts

For the `fetchByField` method, we use the same mechanisms as the `fetchById`. Unsurprisingly, the operations stay the same: `get`, `flatMap`, `filter`, and `map`. The only change is in the filter operation, where we now have to filter on a field that's received as a parameter:

```

返回（电影[MovieFields[field]] ===值）。

```ts

This statement can be a bit overwhelming to the TypeScript or JavaScript newcomer. First, the `MovieFields[field]` part is explained by the fact that `enum` will be transpiled into the following JavaScript function:

```

(function(MovieFields) {

电影字段[电影字段["电影 ID"] = 0] = "电影 ID";

电影字段[电影字段["标题"] = 1] = "标题";

电影字段[电影字段["phase"] = 2] = "phase";

电影字段[电影字段["category_name"] = 3] = "category_name";

电影字段[电影字段["release_year"] = 4] = "release_year";

电影字段[电影字段["running_time"] = 5] = "running_time";

电影字段[电影字段["rating_name"] = 6] = "rating_name";

电影字段[电影字段["disc_format_name"] = 7] ="disc_format_name";

电影字段[电影字段["number_discs"] = 8] = "number_discs";

电影字段[电影字段["viewing_format_name"] = 9] = "viewing_format_name";

电影字段[电影字段["aspect_ratio_name"] = 10] =  "aspect_ratio_name";

电影字段[电影字段["status"] = 11] = "status";

电影字段[电影字段["release_date"] = 12] = "release_date";

电影字段[电影字段["budget"] = 13] = "budget";

电影字段[电影字段["gross"] = 14] = "gross";

电影字段[电影字段["time_stamp"] = 15] = "time_stamp";

})(exports.MovieFields || (exports.MovieFields =  {}));

var 电影字段 = exports.MovieFields;

```ts

Consequently, the value of `MovieFields.release_year` is, in fact, 4, and `MovieFields` is a static array. Consequently, requesting the fourth index of the `MovieFields` array gives me the string `release_year is`. So, `movie[MovieFields[field]]` is interpreted as a `movie["release_year is"]` in our current example.

Now, we have five matches instead of one. Upon analysis of the console, we can see that the notifications still come as soon as a suitable object is found and not when they have all been filtered:

```

fetchByField 4 2015

imdbapi.service.js:43 过滤对象 {movie_id: 1, title: "钢铁侠", phase: "第一阶段：复仇者集结", category_name: "动作", release_year: 2015...}

imdbapi.service.js:47 映射对象 {movie_id: 1, title: "钢铁侠", phase: "第一阶段：复仇者集结", category_name: "动作", release_year: 2015...}

angular-observable.component.js:22 组件电影 {_movie_id: 1, _title: "钢铁侠", _phase: "第一阶段：复仇者集结", _category_name: "动作", _release_year: 2015...}

imdbapi.service.js:43 过滤对象 {movie_id: 2, title: "无敌浩克", phase: "第一阶段：复仇者集结", category_name: "动作", release_year: 2008...}

imdbapi.service.js:43 过滤对象 {movie_id: 3, title: "钢铁侠 2", phase: "第一阶段：复仇者集结", category_name: "动作", release_year: 2015...}

imdbapi.service.js:47 映射对象 {movie_id: 3 =, title: "钢铁侠 2", phase: "第一阶段：复仇者集结", category_name: "动作", release_year: 2015...}

angular-observable.component.js:22 组件电影{_movie_id: 3, _title: "钢铁侠 2", _phase: "第一阶段：复仇者集结", _category_name: "动作", _release_year:2015...}

imdbapi.service.js:43 过滤对象 {movie_id: 4, title: "雷神", phase: "第一阶段：复仇者集结", category_name: "动作", release_year:2011...}

imdbapi.service.js:43 过滤对象 {movie_id: 5, title: "美国队长", phase: "第一阶段：复仇者集结", category_name: "动作", release_year: 2011...}

imdbapi.service.js:43 过滤对象 {电影 ID: 6, 标题: "复仇者联盟", 阶段: "第一阶段：复仇者集结", 类别名称: "科幻", 发行年份: 2012...}

imdbapi.service.js:43 过滤对象 {电影 ID: 7, 标题: "钢铁侠 3", 阶段: "第二阶段", 类别名称: "动作", 发行年份: 2015...}

imdbapi.service.js:47 映射对象 {电影 ID: 7, 标题: "钢铁侠 3", 阶段: "第二阶段", 类别名称: "动作", 发行年份: 2015...}

angular-observable.component.js:22 组件电影 {_movie_id: 7, _title: "钢铁侠 3", _phase: "第二阶段", _category_name: "动作", _release_year: 2015...}

imdbapi.service.js:43 过滤对象 {电影 ID: 8, 标题: "雷神 2：黑暗世界", 阶段: "第二阶段", 类别名称: "科幻", 发行年份: 2013...}

imdbapi.service.js:43 过滤对象 {电影 ID: 9, 标题: "美国队长 2：冬日战士", 阶段: "第二阶段", 类别名称: "动作", 发行年份: 2014...}

imdbapi.service.js:43 过滤对象 {电影 ID: 10, 标题: "银河护卫队", 阶段: "第二阶段", 类别名称: "科幻", 发行年份: 2014...}

imdbapi.service.js:43 过滤对象 {电影 ID: 11, 标题: "复仇者联盟：奥创纪元", 阶段: "第二阶段", 类别名称: "科幻", 发行年份: 2015...}

imdbapi.service.js:47 映射对象 {电影 ID: 11, 标题: "复仇者联盟：奥创纪元", 阶段: "第二阶段", 类别名称: "科幻", 发行年份: 2015...}

angular-observable.component.js:22 组件电影 {_movie_id: 11, _title: "复仇者联盟：奥创纪元", _phase: "第二阶段", _category_name: "科幻", _release_year: 2015...}

imdbapi.service.js:43 过滤对象 {电影 ID: 12, 标题: "蚁人", 阶段: "第二阶段", 类别名称: "科幻", 发行年份: 2015...}

imdbapi.service.js:47 映射对象 {电影 ID: 12, 标题: "蚁人", 阶段: "第二阶段", 类别名称: "科幻", 发行年份: 2015...}

angular-observable.component.js:22 组件电影 {_movie_id: 12, _title: "蚁人", _phase: "第二阶段", _category_name: "科幻", _release_year: 2015...}

imdbapi.service.js:43 过滤对象 {电影 ID: 13, 标题: "美国队长 3：内战", 阶段: "第三阶段", 类别名称: "科幻", 发行年份: 2016...}

imdbapi.service.js:43 过滤对象 {电影 ID: 14, 标题: "奇异博士", 阶段: "第二阶段", 类别名称: "科幻", 发行年份: 2016...}

```ts

Now, the other strength of this design pattern is the ability to unsubscribe yourself. To do so, you only have to acquire a reference to your subscription and call the `unsubscribe()` method, as follows:

```

constructor(private IMDBAPI:IMDBAPIService{ let imdbSubscription = this.IMDBAPI.fetchByField(MovieFields.release_year, 2015).subscribe(

value=> {

this.movies.push(value);

console.log("Component", value)

如果(this.movies.length > 2){

imdbSubscription.unsubscribe();

}

},

error => this.error = true,

() => this.finished = true

);

}

```ts

Here, we unsubscribe after the third notification. To add to all this, the observable object will even detect that nobody's observing it anymore and will stop whatever it was doing. Indeed, the previous code with `unsubscribe` produces:

```

fetchByField 4 2015

imdbapi.service.js:43 过滤对象 {movie_id: 1, title: "Iron Man", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}

imdbapi.service.js:49 映射对象 {movie_id: 1, title: "Iron Man", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}

angular-observable.component.js:24 组件电影 {_movie_id: 1, _title: "Iron Man", _phase: "Phase One: Avengers Assembled", _category_name: "Action", _release_year: 2015...}

imdbapi.service.js:43 过滤对象 {movie_id: 2, title: "The Incredible Hulk", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2008...}

imdbapi.service.js:43 过滤对象 { movie_id: 3, title: "Iron Man 2", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}

imdbapi.service.js:49 映射对象 {movie_id: 3, title: "Iron Man 2", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}

angular-observable.component.js:24 组件电影 {_movie_id: 3, _title: "Iron Man 2", _phase: "Phase One: Avengers Assembled", _category_name: "Action",_release_year: 2015...}

imdbapi.service.js:43 过滤对象 {movie_id: 4, title: "Thor", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2011...}

imdbapi.service.js:43 过滤对象 {movie_id: 5, title: "Captain America", phase: "Phase One: Avengers Assembled", category_name: "Action",release_year: 2011...}

imdbapi.service.js:43 过滤对象 {movie_id: 6, title: "Avengers, The", phase: "Phase One: Avengers Assembled", category_name: "Science Fiction", release_year: 2012...}

imdbapi.service.js:43 过滤对象 {movie_id: 7, title: "Iron Man 3", phase: "Phase Two", category_name: "Action", release_year: 2015...}

imdbapi.service.js:49 映射对象 {movie_id: 7, title: "Iron Man 3", phase: "Phase Two", category_name: "Action", release_year: 2015...}

angular-observable.component.js:24 组件电影 {_movie_id: 7, _title: "Iron Man 3", _phase: "Phase Two", _category_name: "Action", _release_year: 2015...}

```ts

Everything stops after the third notification.

# Promises

The promise is another useful asynchronous concept that has been provided by Angular 2\. It promises to provide the same feature as `Observer`: process something and, asynchronously, notify the caller that an answer is available. So, why bother having two concepts that do the same thing? Well, the verbosity of `Observer` allows one thing that the `Promise`does not: unsubscribe. Consequently, if you never plan on using the unsubscribe capacity of the observer pattern, you are better off using `Promises`, which are, in my opinion, more intuitive in their writing and understanding. To emphasize the differences between observers and promises, we will take the same example as before—fetching movies from a JSON API. `AngularObservableAppComponent` will make an asynchronous call to`IMDBAPIService` and, upon the answer, will update the HTML view.

Here's the `fetchOneById` method using `Promise` instead of `Observable`:

```

/**

* 返回一个匹配 id 的电影的 Promise

*@param {number} id

*@return {Promise<Movie>}

*/

public fetchOneById(id:number) : Promise <Movie>{

console.log('fecthOneById', id);

return this.http.get(this.moviesUrl)

/**

* 将 HTTP 获取的结果转换为可观察的

* 转换为单个项目的可观察对象。

*/

.flatMap(res => res.json().movies)

/**

* 通过电影 ID 筛选电影

*/

.filter((movie:any) =>{

console.log("过滤", 电影);

返回 (电影.movie_id === id)

})

.toPromise()

/**

* 将 JSON 电影项目映射到电影模型

*/

.then((movie:any) => {

console.log("映射", 电影);

返回新电影(

电影 ID，

电影标题，

电影阶段，

电影类别名称，

电影发行年份，

电影时长，

电影评级名称，

电影光盘格式名称，

电影光盘数量，

电影观看格式名称，

电影宽高比名称，

电影状态，

电影发行日期，

电影预算，

电影总票房，

电影时间戳

) });

}

```ts

As shown by this code, we went from `flatMap`, `filter`, `map`, to `flatMap`, `filter`, to `Promise`, `then`. The new operations, `toPromise` and `then`, are creating a `Promise` object that will contain the result of the `filter`operation and, on completion of the `filter` operation, the `then` operation will be executed. The `then` operation can be thought of as a map; it does the same thing. To use this code, we also have to change the way we call `IMDBAPIService` in `AngularObservableAppComponent` to the following:

```

this.IMDBAPI.fetchOneById(1).then(

值=> {

this.movies.push(value);

console.log("组件", 值)

},

错误 => this.error = true

);

```ts

Once again, we can see a `then` operation that will be executed when the promise from `IMDBAPIService.FetchOneById` has completed. The `then` operation accepts two callbacks: `onCompletion` and `onError`. The second callback, `onError`, is optional. Now, the `onCompletion` callback will only be executed once, when the Promise has completed, as shown in the console:

```

imdbapi.service.js:30 过滤对象 {movie_id: 2, title: "无敌浩克", phase: "第一阶段：复仇者联盟", category_name: "动作", release_year: 2008...}

imdbapi.service.js:30 过滤对象 {movie_id: 3, title: "钢铁侠 2", phase : "第一阶段：复仇者联盟", category_name: "动作", release_year: 2015...}

imdbapi.service.js:30 过滤对象 {movie_id: 4, title: "雷神", phase: "第一阶段：复仇者联盟", category_name: "动作", release_year: 2011...}

imdbapi.service.js:30 过滤对象 {movie_id: 5, title: "美国队长", phase: "第一阶段：复仇者联盟", category_name: "动作", release_year: 2011...}

imdbapi.service.js:30 过滤对象 {movie_id: 6, title: "复仇者联盟", phase: "第一阶段：复仇者联盟", category_name:"科幻", release_year: 2012...}

imdbapi.service.js:30 过滤对象 {movie_id: 7, title: "钢铁侠 3", phase: "第二阶段", category_name: "动作", release_year: 2015...}

imdbapi.service.js:30 过滤对象 {movie_id: 8, title: "雷神 2：黑暗世界", phase: "第二阶段", category_name: "科幻", release_year: 2013...}

imdbapi.service.js:30 过滤对象 {movie_id: 9, title: "美国队长：冬日战士", phase: "第二阶段", category_name: "动作",release_year: 2014...}

imdbapi.service.js:30 过滤对象 {movie_id: 10, title: "银河护卫队", phase: "第二阶段", category_name: "科幻", release_year: 2014...}

imdbapi.service.js:30 过滤对象 { movie_id: 11, title: "复仇者联盟：奥创纪元", phase: "第二阶段", category_name: "科幻", release_year: 2015...}

imdbapi.service.js:30 filter Object {movie_id: 12, title: "Ant-Man", phase: "Phase Two", category_name: "Science Fiction", release_year: 2015...}

imdbapi.service.js:30 filter Object {movie_id: 13, title: "Captain America: Civil War", phase: "Phase Three", category_name: "Science Fiction", release_year: 2016...}

imdbapi.service.js:30 filter Object {movie_id: 14, title: "Doctor Strange", phase: "Phase Two", category_name: "Science Fiction", release_year: 2016...}

imdbapi.service.js:35 map Object {movie_id: 1, title: "Iron Man", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}

angular-observable.component.js:23 Component Movie {_movie_id: 1, _title: "Iron Man", _phase: "Phase One: Avengers Assembled", _category_name: "Action", _release_year: 2015...}

```ts

While the modification of `IMDBAPIService` was minimal for the `fetchOneById` method, we will have to change `fetchByField` more significantly. Indeed, the `onComplete` callback will only be executed once, so we need to return an array of `Movie` and not only one `Movie`. Here's the implementation of the `fetchByField` method:

```

public fetchByField(field: MovieFields, value: any) :Promise<Movie[]>{

console.log('fetchByField', field, value);

return this.http.get(this.moviesUrl)

.map(res => res.json().movies.filter(

(movie)=>{

return (movie[MovieFields[field]] === value)

})

)

.toPromise()

/**

* Map the JSON movie items to the Movie Model

*/

.then((jsonMovies:any[]) => {

console.log("map",jsonMovies);

let movies:Movie[] = [];

for (var i = 0; i < jsonMovies.length; i++) {

movies.push(

new Movie(

jsonMovies[i].movie_id,

jsonMovies[i].title,

jsonMovies[i].phase,

jsonMovies[i].category_name,

jsonMovies[i].release_year,

jsonMovies[i].running_time,

jsonMovies[i].rating_name,

jsonMovies[i].disc_format_name,

jsonMovies[i].number_discs,

jsonMovies[i].viewing_format_name,

jsonMovies[i].aspect_ratio_name,

jsonMovies[i].status,

jsonMovies[i].release_date,

jsonMovies[i].budget,

jsonMovies[i].gross,

jsonMovies[i].time_stamp

)

)

}

return movies;

});

}

```ts

To implement this, I trade `flatMap` for a classical map as the first operation. In the map, I directly acquire the reference to the JSON `movie` array and apply the filed filter. The result is transformed into a promise and processed in `then`. The `then` operation receives an array of JSON `movies` and transforms it into an array of `Movie`. This produces an array of `Movie` which is returned, as the promised result, to the caller. The call in  `AngularObservableAppComponent` is also a bit different, as we now expect an array:

```

this.IMDBAPI.fetchByField(MovieFields.release_year, 2015).then(

value => {

this.movies = value;

console.log("Component", value)

},

error => this.error = true

)

```ts

Another way to use `Promise` is through the fork/join paradigm. Indeed, it is possible to launch many processes (fork) and wait for all the promises to complete before sending the aggregated result to the caller (join). It is therefore relatively easy to supercharge the `fetchByField` method, as it can run in many fields with logic or. Here are the three very short methods we need to implement to logic or:

```

/**

* Private member storing pending promises

*/

private promises:Promise<Movie[]>[] = [];

/**

* Register one promise for field/value. Returns this

* for chaining i.e.

*

* byField(Y, X)

* .or(...)

* .fetch()

*

* @param {MovieFields} field

* @param {any}  value

* @return {IMDBAPIService}

*/

public byField(field:MovieFields, value:any):IMDBAPIService{

this.promises.push(this.fetchByField(field, value));

return this;

}

/**

* Convenient method to make the calls more readable, i.e.

*

* byField(Y, X)

* .or(...)

* .fetch()

*

* instead of

*

* byField(Y, X)

* .byField(...)

* .fetch()

*

* @param {MovieFields} field

* @param {any}  value

* @return {IMDBAPIService}

*/ public or(field:MovieFields, value:any):IMDBAPIService{

return this.byField(field, value);

}

/**

* Join all the promises and return the aggregated result.

*

*@return {Promise<Movie[]>}

*/

public fetch():Promise<Movie[]>{

return Promise.all(this.promises).then((results:any) => {

//result is an array of movie arrays. One array per

//promise. We need to flatten it.

return [].concat.apply([], results);

});

}

```ts

Here, I provide two convenient methods, `field` and `or`, that take a `MovieField` and a value as an argument and create a new promise. They both return `this` for chaining. The `fetch` method joins all the promises together and merges their respective results. In `AngularObservableAppComponent`, we now have the following:

```

this.IMDBAPI.byField(MovieFields.release_year, 2015)

.or(MovieFields.release_year, 2014)

.or(MovieFields.phase, "Phase Two")

.fetch()

.then (

value => {

this.movies = value;

console.log("Component", value)

},

error => this.error = true

);

```

这很容易阅读和理解，同时保持了 Angular 2 的所有异步能力。

# 总结

在本章中，我们学习了如何使用一些最有用的经典模式：组件、单例和观察者。我们学会了如何在纯 TypeScript 中以及使用 Angular 2 构建块来实现。本章的代码可以在这里找到：[`github.com/MathieuNls/Angular-Design-Patterns-and-Best-Practices/tree/master/chap4`](https://github.com/MathieuNls/Angular-Design-Patterns-and-Best-Practices/tree/master/chap4)。

在下一章中，我们将专注于模式，旨在简化和组织我们的 Angular 2 应用程序中的导航。
