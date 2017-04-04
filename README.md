# JavaScript-
JavaScript Ответы на вопросы


# 2)Какие замыкания есть в JavaScript и как они используются?
Замыкание - одно из мощных выразительных средств javascript, которым часто пренебрегают, и даже не советуют употреблять.

Действительно, замыкания могут приводить к проблемам. Но на самом деле они очень удобны, просто нужно понимать, что реально происходит.
Если говорить просто, то замыкание - это внутренняя функция. Ведь javascript разрешает создавать функции по ходу выполнения скрипта. И эти функции имеют доступ к переменным внешней функции.

В этом примере создается внутренняя функция func, изнутри которой доступны как локальные переменные, так и переменные внешней функции outer:

function outer() {
	var outerVar;

	var func = function() {
		var innerVar
		...
		x = innerVar + outerVar
	}
	return func
}

Когда заканчивает работать функция outer, внутренняя функция func остается жить, ее можно запускать в другом месте кода.
Получается, что при запуске func используется переменная уже отработавшей функции outer, т.е самим фактом своего существования, func замыкает на себя переменные внешней функции (а точнее - всех внешних функций).

Наиболее часто замыкания применяются для назначения функций-обработчиков событий:

function addHideHandler(sourceId, targetId) {
	var sourceNode = document.getElementById(sourceId)
	var handler = function() {
		var targetNode = document.getElementById(targetId)
		targetNode.style.display = ‘none’
	}
	sourceNode.onclick = handler
}

Эта функция принимает два ID элементов HTML и ставит первому элементу обработчик onclick, который прячет второй элемент.

Т.е,

// при клике на элемент с ID="clickToHide"
// будет спрятан элемент с ID="info"
addHideHandler("clickToHide", "info")
Здесь динамически созданный обработчик события handler использует targetId из внешней функции для доступа к элементу.


Замыкание позволяет создать функцию суммирования, которая работает вот так:
sum(a)(b) = a+b

// например
sum(1)(3) = 4

Да, именно так: скобки - не опечатки.

А вот и сама функция sum:

function sum(a) {
  return function(b) {
    return a+b
  }
}

# 4)Что происходит, когда вы не объявить переменную в JavaScript?
При объявлении переменной это перемменая храниться в памяти и его можно использовать при нужном нам времени. А когда мы не объявляем переменную то она используеться один раз и нигде в памяти не храниться. 

# 5)В чем разница между == и ===?
В JavaScript есть два похожих оператора: == и ===. Если не знать их отличия, это может обернуться кучей ошибок. Так что решил раскрыть эту тему. Чем именно отличаются == и ===, как они работают, почему так происходит, и как избежать ошибок.

Оператор == сравнивает на равенство, а вот === — на идентичность. Плюс оператора === состоит в том, что он не приводит два значения к одному типу. Именно из-за этого он обычно и используется.

abc  == undefined;	// true, если abc = undefined | null
abc === undefined;	// true - только если abc = undefined!

abc  == false;	// true, если abc = false | 0 | '' | []
abc === false; // true, только если abc = false!

Ведь путать false и 0 (или '', или []) — вряд ли очень хорошо.

Разумеется:
5 === 5;	// true
true === true;	// true
'abc' === 'abc';	// true


А теперь интересный пример.
5  == 5;					// true
5 === 5;					// true
new Number(5)  == 5;				// true
new Number(5) === 5;				// false!


Почему так происходит? Да, любое число — это объект класса Number. Но можно представить число как цифру — некоторой константой. Она единожды объявлена, и всегда идентична сама себе. Но в то же время объявляя новый объект класса Number — он равен ей по значению, но не идентичен (так как это два совершенно разных объекта класса Number).

Arrays / Objects

А вот для массивов и объектов оба оператора работают одинаково, сравнивая на идентичность:
var a = {};
a  == {};	// false
a === {};	// false
a  == a;	// true
a === a;	// true


Для сравнения массивов и объектов можно написать специальную функцию:
function isEq(a, b){
	if(a == b) return true;
	for(var i in a){
		if(!isEq(a[i], b[i])) return false;
	}
	for(var i in b){
		if(!isEq(a[i], b[i])) return false;
	}
	return true;
}

Немножко неаккуратно, два цикла, да и про hasOwnProperty забыли; ну да сойдёт.

This <-

Есть ещё один подводный камень. Это передача в this.
(function(){
	this  == 5;	// true
	this === 5;	// false
}).call(5);


Вот такой вот момент. Стоит о нём не забывать.

Итого...

Ну а теперь представим, что мы пишем свой суперфреймворк, активно юзаем там оператор === вместо == просто потому что он красивее, и некто находит у нас несколько багов.
func(new Number(5));
(function(){
	func(this);
}).call(5);


Кажется, что такие примеры нежизнеспособны? Пожалуйста!

jQuery:
$.each([1, 2, 3, 4, 5], function(){
	func(this);
});


Ну или захотелось расширить цифру.
var Five = new Number(5);
Five.a = 2; // захотелось расширить, а просто 5 не расширяется
// здесь как-то используем...
func(Five);


# 6) Что типы данных поддерживаются в JavaScript?
В Яваскрипте существуют следуюшие типы данный (typeof):
1-Sting "Hello"
2-Number  1, 2, 3, 4 и.т.д
3-Boulean (true/false).
4-Null Значение null не относится ни к одному из типов выше, а образует свой отдельный тип, состоящий из единственного значения null:
5-Undefined Значение undefined, как и null, образует свой собственный тип, состоящий из одного этого значения. Оно имеет смысл «значение не присвоено».
Если переменная объявлена, но в неё ничего не записано, то её значение как раз и есть undefined:

var x;
alert( x ); // выведет "undefined"
6-Object Объявляются объекты при помощи фигурных скобок {...}, например:
var user = { name: "Вася" };

typeof undefined // "undefined"

typeof 0 // "number"

typeof true // "boolean"

typeof "foo" // "string"

typeof {} // "object"

typeof null // "object"  (1)

typeof function(){} // "function"  (2)


# 7)Что бы «1» + 2 + 3 и 1 + 2 + «3» вычисляться, соответственно?
document.write('1' + 2 + 3) = 123
document.write(1 + 2 + "3") = 33

# 8) Объяснить понятие ненавязчивого JavaScript?
Ненавязчивый JavaScript (англ. unobtrusive JavaScript) является подходом к web-программированию на языке JavaScript. Термин был введён в 2002 году Стюартом Лэнгриджем[1]. Под принципами ненавязчивого Javascript обычно понимаются следующие:

отделение функциональности веб-страницы («уровень поведения») от структуры, содержания и представления Web-страницы;
наработанные методы по избежанию проблем традиционного программирования на JavaScript (таких как зависимость от браузера и недостаток масштабируемости);
техники «постепенного улучшения» (англ. Progressive enhancement) для поддержки пользовательских агентов, которые могут не в полной мере поддерживать функциональность JavaScript.

# 9)В чем разница между значением нуля и неопределенным значением?
Понимание null и undefined
В JavaScript null — это литерал и ключевое слово языка, которое представляет собой отсутствие какого-либо объекта. Другими словами, null указывает «в никуда». В свою очередь, хоть и похожий по смыслу undefined, олицетворяет отсутствие значения как такового. Оба абсолютно неизменны, не имеют свойств и методов и не способны их иметь. Фактически, попытка обратиться к какому-нибудь свойству, или присвоить его, приведёт к ошибке  TypeError. Оба этих примитива, как намекают их имена, совершенно лишены значений.

Это самое отсутствие значения приводит к тому, что они считаются ложными, в том смысле, что они приводятся к false если используются в качестве условия, например, в конструкции if. А если сравнить null и undefined с другими ложными значениями при помощи оператора нестрогого сравнения (==), то окажется, что они не равны ничему, кроме самих себя:

null == 0; // false
undefined == ""; // false
null == false; // false
undefined == false; // false
null == undefined; // true
Несмотря на эти сходства, null и undefined не эквивалентны. Каждый из них является представителем своего типа: undefined — представляет тип Undefined, а null, соответственно — тип Null. Это легко доказать, сравнив их при помощи оператора строгого сравнения (===), который принимает в расчёт не только значения, но и типы данных:

undefined === null; // false
Это важное различие, и оно не случайно, ведь эти примитивы служат для разных целей. Чтобы их различать, вы можете считать undefined неожиданным отсутствием значения, а null — умышленным отсутствием значения.

Получение undefined
Есть множество способов получить значение undefined в коде. Обычно это происходит при попытке получить значение там, где значения нет. В этом случае JavaScript, будучи динамическим, слабо типизированным языком, не покажет ошибку, а выдаст значение по умолчанию, undefined.

Любая объявленная переменная, которой при создании не присвоено никакого значения, имеет значение undefined:

var foo; // по умолчанию undefined
Значение undefined также получается при попытке обратиться к несуществующему свойству объекту или элементу массива:

var array = [1, 2, 3];
var foo = array.foo; // свойство foo не существует, возвращается undefined
var item = array[5]; // в массиве нет элемента 5, возвращается undefined
Если в функции нет оператора return, она возвращает undefined:

var value = (function(){})(); // возвращает undefined
Если функции не был передан какой-либо аргумент, он становится undefined:

(function(undefined){
    // параметр равен undefined
})();
Помимо всего вышеперечисленного, для получения undefined может использоваться оператор  void. Некоторые библиотеки, вроде Underscore пользуются этим для надежной проверки типов, потому как void нельзя переопределить, и он всегда возвращает undefined:

function isUndefined(obj){
    return obj === void 0;
}
Наконец, undefined — это предопределённая глобальная переменная (а не ключевое слово, как  null), которая равна undefined:

'undefined' in window; // true
Начиная с пятой версии ECMAScript эта переменная доступна только для чтения, а, вот, в предыдущих версиях её было возможно переопределить.

Применение null
В первую очередь null отличается своим применением, и в отличие от undefined, null больше используется для присваивания значения. Как раз из-за этого оператор typeof для  null возвращает «object». Изначально это объяснялось тем, что null использовался (и используется) как пустая ссылка там, где ожидается объект, что-то вроде заглушки. Такое поведение typeof было позже признано багом, и, хотя было предложено это поведение исправить, пока что, в целях обратной совместимости, всё остается как есть.

Вот, почему окружение JavaScript не выставляет никаких значений в null, и это делается только программно. В документации на MDN написано следующее:

В различных API null часто возвращается в тех местах, где ожидается объект, но такой объект подобрать нельзя.

Это правдиво для DOM, который не зависит от языка и никак не описывается в документации ECMAScript. Из-за того, что используется внешний API, попытка получить отсутствующий элемент возвращает null, а не undefined.

Вообще, если нужно присвоить «не-значение» переменной или свойству, передать его в функцию, или вернуть из функции, то null — это почти всегда лучший вариант. Упрощённо: JavaScript использует undefined, а программисты должны использовать null.

Другой способ применения null — явное «зануливание» переменной (object = null), когда ссылка на объект больше не требуется. Кстати, это считается хорошей практикой. Присваивая  null, вы фактически удаляете ссылку на объект, и если на него нет других ссылок, он отправляется к сборщику мусора, таким образом возвращая доступную память.

Копнём глубже
Причина того, что null и undefined эдакие чёрные дыры, кроется не только в их поведении, но ещё и в том, как они обрабатываются внутри окружения JavaScript. Они не обладают теми характеристиками, которые обычно присущи другим примитивам и встроенным объектам.

Начиная с ES5 метод Object.prototype.toString, ставший стандартом де-факто для проверки типов, стал полезен в этом отношении и для null с undefined:

Object.prototype.toString.call(null); // [object Null]
Object.prototype.toString.call(undefined); // [object Undefined]
Однако, на самом деле у null и undefined этот метод не возвращает внутреннее свойство  [[Class]]. По документации он работает следующим образом:

Если значение this равно undefined, вернуть "[object Undefined]".
Если значение this равно null, вернуть "[object Null]".
Пусть O равно результату вызова ToObject с this, переданным как аргумент.
Пусть class равно внутреннему свойству [[Class]] объекта O.
Вернуть значение String, которое является результатом сложения трёх строк  "[object ", class, и "]".
Этот метод просто возвращает заготовленную строку, если обнаруживает null или undefined, просто чтобы унифицировать функциональность с другими объектами. Такое поведение встречается сплошь и рядом во всей документации, большая часть методов содержат простую проверку, и если встретился null или  undefined, возвращают значение сразу. Фактически, нигде не написано, что у них содержатся какие-либо внутренние свойства, обычно имеющиеся у каждого нативного объекта. Это как если бы они вообще не были объектами. Интересно, эти примитивы в окружении JavaScript как-то явно и особо обрабатываются? Может быть, кто-то более знакомый с имплементацией мог бы подсказать.

# 10)Какие условные операторы будут поддерживать JavaScript?
&& / II / !=
if(true/false)

# 11)Что такое NaN?
Особое значение Not-A-Number, которое указывает, что арифметическое выражение возвратило значение, не являющееся числом.
Описание, примеры

Это значение заменяет собой ошибки вычислений.

Для проверки используется функция isNaN,

Пример: Деление на ноль:
var n = 1/0   // NaN

// проверим
alert(NaN == NaN) // false
alert(isNaN(n)) // true

# 12)Объясните значение ключевого слова «this» в функции JavaScript.
Ключевое слово this в javascript работает своеобразно, не так, как в других языках.

В отличие от PHP, Java, C++ и т.п, значение this в javascript не привязывается статически ни к какому объекту, а зависит от контекста вызова.

Разберем все 4 возможных случая.

1.Режим конструктора
Если функция вызывается через new как конструктор объекта, то this ставится на создаваемый объект:

function Animal(name) {
    this.name = name
}
function this javascript
В результате получается объект, инициализированный конструктором, с заполненным свойством name.

2.Метод объекта

Если функция запущена как свойство объекта, то в this будет ссылка на этот объект.

obj.func(параметры)
// или
obj['func'](параметры)
При этом совершенно неважно, откуда эта функция взялась. Важно лишь, какой перед func стоит объект.
Пример:
Создадим два любых объекта:
vasya = { 
	name: "Василий"
}
dima = { 
	name: "Дмитрий"
}

Определим никак не связанную с ними функцию say:
say = function() {
    alert("Привет, я "+this.name)
}
Присвоим функцию свойству sayHi для обоих объектов:

vasya.sayHi = say
dima.sayHi = say
И теперь тестовый запуск:
vasya.sayHi()    // =>  "Привет, я Василий"

dima.sayHi()    // => "Привет, я Дмитрий"
// или так
dima['sayHi']()
В обоих случаях запускается одна и та же функция say, которая получает в this объект до точки.

3.Apply/Call

Функцию можно вызвать, используя методы call и apply.

func.apply(obj, [параметры])
func.call(obj, параметр)
При таком вызове this будет установлен в obj.

Оба метода работают одинаково, но для call аргументы подставляются в вызов, а для apply - передается массив.
function sum(a,b) {
	this.c = a + b
}

var obj = {}

sum.call(obj, 1,2)
// или
sum.apply(obj, [1,2])

alert(obj.c)   // => 3
Вызов через apply удобен тем, что можно самостоятельно сформировать массив аргументов или модифицировать существующий.
Простейший вызов
say()
При таком вызове this ставится равным глобальному объекту (в браузере это window). Обычно, если функция использует this, то она не подразумевает запуск в таком виде, так что в 90% случаев такое значение this - результат ошибки в программе, глюк.

# 13)В чем разница между undefined и not defined в JavaScript?
Underfind это озночает не видет значеня или видет пустое значение а not defined озночает что выражение н
е найдено и не существует.
# 14)Что такое функция Подъемный в JavaScript?
В JavaScript существует четыре основных способа появления идентификатора в области видимости:
Внутренние механизмы языка: например, во всех областях видимости доступны this и arguments.
Формальные параметры: у функций могут быть именованные формальные параметры, область видимости которых ограничена телом функции.
Объявления функций: объявленные в виде function foo() {}.
Объявления переменных: например, var foo;.

# 16)Что такое функциональное программирование?
Функциона́льное программи́рование — раздел дискретной математики и парадигма программирования, в которой процесс вычисления трактуется как вычисление значений функций в математическом понимании последних (в отличие от функций как подпрограмм в процедурном программировании).

