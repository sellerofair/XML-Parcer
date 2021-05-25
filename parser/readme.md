# Пакет для парсинга XML
## enums (перечисления)
### Stage
**Набор возможных событий при парсинге XML**
В процессе парсинга в зависимости от обрабатываемого символа и текущего события, парсер может изменить событие, либо оставить текущее состояние и продолжить парсинг. Возможны следующие события:
* WAITTAG - ожидание открытия тега (символа '<');
* TAG - чтение тега;
* **OPENTAG** - прочитан открывающий тег;
* **CLOSETAG** - прочитан закрывающий тег;
* **SINGLETAG** - прочитан одиночный тег;
* WAITCONTENT - ожидание содержания внутри элемента;
* **CONTENT** - прочитано содержание элемента;
* WAITKEY - ожидание ключа атрибута;
* KEY - чтение ключа атрибута;
* WAITEQUAL - ожидание символа '=';
* WAITVALUE - ожидание значения атрибута;
* VALUE - чтение значения атрибута;
* PROLOG - чтение пролога;
* COMMENT - чтение (пропуск) комментария;

**!!! Поля не должны изменяться при использовании класса.**

**Жирным** выделены события, возвращаемые при использовании парсера в цикле `for (let stage of parser)`, где
`stage` - переменная, используемая в текущем цикле, хранящая текущее событие,
`parser` предварительно созданный экземпляр класса `Parser`,
а также при использовании метода `next()`.

Остальные события используются только внутри класса `Parser` и при правильном использовании пакета не возникают снаружи.

## errors (ошибки)
### XmlStatementError
**Ошибки, связанные с неверным синтаксисом XML**
Возникают при передаче в `Parser` строки, которая не соответствует формату XML. Содержат сообщение, уточняющее причину и место возникновения ошибки.
### StageInstatiationError
**Ошибки, связанные с неверным синтаксисом XML**
**Исключение для предотвращения создания экземпляра Stage**
При правильном использовании создавать экзаемпляр класса `Stage` не нужно. Все поля событий статические.

## parser (основной модуль)
### LinkedString(text: string)
**Объект-обертка для строки**
Нужен для исключения двойного хранения строки с данными. В противном случае, строка продублируется внутри созданного экземпляра класса `Parser`.
### Parser(linkedString: LinkedString)
**Класс парсера**
Экземпляр парсера сканирует текст на наличие XML тегов и возвращает событие, либо строку в текущей позиции. В конструктор передается `LinkedString`.
Все поля класса приватные. Доступ осуществляется через методы `getSmth()`.
Перемещение по блокам осуществляется с помощью метода `next()` или в цикле `for .. of`.
#### getTagName()
Метод.
Возвращает имя тега.
#### getAttributes()
Метод.
Возвращает массив атрибутов.
#### getContent()
Метод.
Возвращает содержимое.
#### next()
Метод. Может бросать `XmlStatementError`.
Возвращает следующее событие (Stage) в цикле `for .. of`.

# Пример использования.
```javascript
// str - входная строка текста.
let str = `
<A B="C">
    <D E = "F"/>
</A>`
// Создаем обертку для входящей строки. По возможности должна передаваться строка без промежуточных переменных.
const linkSrt = new LinkedString(str);
// Создаем экземпляр парсера.
let parser = new Parser(linkSrt);

for (let stage of parser) {
    console.log(stage);
    if (stage === Stage.OPENTAG) {
        console.log(parser.getTagName());
    }
```
**LOG:**
*Symbol(OPENTAG)*
*A*
*Symbol(SINGLETAG)*
*Symbol(CLOSETAG)*