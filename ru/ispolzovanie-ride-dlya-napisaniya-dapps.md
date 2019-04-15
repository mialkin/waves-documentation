# Валидация транзакции с помощью функцииsigVerify, без использования скрипта аккаунта

Перед тем как попасть в блок, каждая транзакция в блокчейне Waves проходит проверку на валидность.В функционал блокчейна Waves вшита функция sigVerify, которая выполняет проверку каждой транзакции на валидность:

```
sigVerify(signature, tx.bodyBytes(), pubKey)
```

где

**signature**— это цифровая подпись транзакции \(перед отправкой транзакции в блокчейн, владелец аккаунта, с которого транзакция отправляется, подписывает эту транзакцию своим закрытым ключом\),

**tx.bodyBytes\(\)**— это массив байт сериализованной транзакции,

**pubKey**— публичный ключ аккаунта, с которого отправляется транзакция.

Если функция sigVerify возвращает true, то транзакция попадает в блок, если функция возвращает false— транзакция отбрасывается блокчейном. Функция sigVerify проверяет только одну вещь— она проверяет то, что транзакция, отправляемая с некоторого аккаунта, была на самом деле отправлена владельцем этого аккаунта. Зная цифровую подпись транзакции и публичный ключ владельца аккаунта, принадлежность транзакции аккаунту легко проверить \(см. страницу шифрование публичным ключом\). И цифровая подпись, и публичный ключ прикладываются к транзакции.

# Валидация транзакции скриптом аккаунта {#id-Объяснениевалидациитранзакциискриптомаккаунта-Валидациятранзакциискриптомаккаунта}

В определенный момент в блокчейне Waves возникла необходимость создавать более сложные проверки транзакций на валидность. Например, было бы неплохо, чтобы транзакцию можно было бы подписывать не одним ключом, а, допустим, сразу несколькими ключами. Пример: Коля и Ваня являются партнерами, и они договорились, что транзакции с их общего аккаунта будут валидными, если они подписаны либо Колей, либо Ваней, либо Колей и Ваней одновременно.

Реализовать сложную логику проверки транзакций на валидность можно с помощью скрипта, написанного на языке RIDE.

Программист пишет на языке RIDE скрипт, компилирует его, и привязывает скомпилированный скрипт к аккаунту, с которого транзакция отправляется.

# Привязывание скрипта к аккаунту {#id-Объяснениевалидациитранзакциискриптомаккаунта-Привязываниескриптакаккаунту}

Привязывание скомпилированного скрипта к аккаунту производится с помощью специальной [Set Script-транзакции](https://docs.wavesplatform.com/en/waves-api-and-sdk/waves-node-rest-api/transactions.html#section-510c9bfa44d0430520a12c9113896f51).Данную транзакцию можно создать, например, с помощью [библиотечной функции](https://docs.wavesplatform.com/en/waves-api-and-sdk/client-libraries.html). Функция, создающая транзакцию, принимает на вход массив байт скомпилированного скрипта, и адрес аккаунта,с которого отправляется транзакция.

После привязывания скрипта к аккаунту, _любая _транзакция, которая будет отправляться с данного аккаунта, будет валидироваться данным скриптом.

При создании нового аккаунта, к этому аккаунту никакой скрипт еще не привязан. Поэтому все транзакции, которые отправляются с нового аккаунта, валидируются только функцией sigVerify, как описано выше.

После того, как к аккаунту привязывается скрипт, все отправляемый с данного аккаунта транзакции проверяются уже этим скриптом, а не функцией sigVerify. Тем не менее, внутри скрипта аккаунта разработчики часто используют функцию sigVerify.

К 1 аккаунту можно привязать только 1 скрипт.

# Перепривязывание скрипта к аккаунту {#id-Объяснениевалидациитранзакциискриптомаккаунта-Перепривязываниескриптакаккаунту}

Если владелец аккаунта, к которому привязан скрипт, захочет поменять логику, запрограммированную внутри скрипта, он должен будет сделать следующее:

1. Написать новую версию скрипта аккаунта, с обновленной логикой
2. Скомпилировать новый скрипт
3. Скомпилированный новый скрипт привязать к аккаунту

# Удаление привязки скрипта к аккаунту {#id-Объяснениевалидациитранзакциискриптомаккаунта-Удалениепривязкискриптакаккаунту}

С любого аккаунта, который имеет привязку к скрипту, данную привязку можно снять. После снятия привязки все транзакции, отправляемые с данного аккаунта, вновь будут валидироваться лишь функцией sigVerify.

# Пример простейшего скрипта аккаунта {#id-Объяснениевалидациитранзакциискриптомаккаунта-Примерпростейшегоскриптааккаунта}

В качестве IDE для написания скриптов на языке RIDE мы используем Waves IDE, доступное по адресу [http://ide.wavesplatform.com](http://ide.wavesplatform.com/).

Чтобы создать новый скрипт, кликнем на кнопку "New" и выберем пункт "Account Script".![](/assets/1.png)



Откроется новая вкладка, в которой мы сможем писать наш RIDE-скрипт.![](/assets/2.png)  


Самый простой скрипт аккаунта может состоять из одного лишь слова "true":

```
true
```

Такой скрипт, после привязывания его к аккаунту, будет разрешать все отправляемые с данного аккаунта транзакции. Если бы вместо "true" мы написали "false", то с аккаунта нельзя было бы отправить ни одну транзакцию. Ни в коем случае не привязывайте к аккаунту, на котором лежат ваши деньги, скрипт, который разрешает все отправляемые с вашего аккаунта транзакции! Если вы это сделает, то любой желающий сможет перевести с вашего аккаунта все ваши деньги на свой аккаунт.

# Работа с объектом tx внутри скрипта аккаунта

У скрипта аккаунта есть контекст, содержащий некоторые полезные переменные. Одной из таких переменных является переменная tx, которая содержит в себе информацию о вилидируемой скриптом транзакции. В блокчейне Waves существует [несколько типов](https://docs.wavesplatform.com/en/waves-api-and-sdk/waves-node-rest-api/transactions.html#section-510c9bfa44d0430520a12c9113896f51) транзакций. Внутри скрипта аккаунта мы должны иметь возможность определять тип транзакции,  с которой мы имеем дело. Определение \(а точнее приведение\) типа осуществляется с помощью оператора match:

```
match (tx)
{
    case t: TransferTransaction =>
        t.amount < 100
    case t: IssueTransaction =>
        true
    case _ =>
        false
}
```

В примере выше мы говорим, что если отправляемая с аккаунта транзакция является трансфер-транзакцией, и сумма перевода составляет менее 100 токенов, то мы разрешаем такую транзакцию. Также мы разрешаем любую транзакцию типа Issue Transaction. Все остальные типы транзакций отправлять с аккаунта мы запрещаем.

# Общая структура DApp {#id-Объяснениевалидациитранзакциискриптомаккаунта-ОбщаяструктураDApp}

Основные ограничения:

* Максимальная сложность: 10000.
* Максимальный размер скрипта:
  32 Кбайт.

DApp состоит из следующих элементов \(порядок элементов необходимо сохранять\):

1. Директивы

Т.к. компилятор работает с любым видом скриптов нашей системы, ему необходима дополнительная информация для работы. Поэтому во всех скриптах желательно использовать директивы, иначе будут использованы значения по умолчанию \(...\), которые могут не подходить в конкретном случае.

Для DApp набор директив следующий:

```
{-# STDLIB_VERSION 3 #-}
{-# SCRIPT_TYPE ACCOUNT #-}
{-# CONTENT_TYPE DAPP #-}
```

STDLIB\_VERSION 3 — версия библиотеки стандартных функций

SCRIPT\_TYPE ACCOUNT — директива указывающая, к чему привязан скрипт \(ассету или аккаунту\)

CONTENT\_TYPE DAPP — директива, задающая тип скрипта \(скрипт старого вида, либо dApp\)

2.  Контекст скрипта

Контекст скрипта — это контекст, в котором могут быть определены переменные \(immutable variables\) и определены функции, которые будут доступны в пределах всего DApp.

```
let someConstant = 42
func doSomething() = {
    1+1
}
```

3. Объявление вызываемых функций

Здесь мы можем объявлять функции, которые будут вызываться с помощью InvokeScriptTransaction. Такие функции помечаются аннотацией @Callable\(contextObj\), в скобках после которой указывается \(произвольное\) имя объекта контекста. Объект контекста содержит поля:

* caller - адрес аккаунта, который вызвал данную функцию
* callerPublicKey - публичный ключ аккаунта, который вызвал данную функцию
* payment - платёж, который приложен к данному вызову функции \(см. способ вызова функции -
  InvokeScriptTransaction
  \). Платёж может быть пустым \(UNIT\).

Вызываемая функция может использовать функции и значения из контекста скрипта \(пункт 2\) или из своего контекста \(т.е. не может использовать другие вызываемые функции и функцию валидации\).

```
@Callable(contextObj)
func foo() = {
   if (contextObj.caller == this)
   then
       ScriptResult(
            WriteSet([DataEntry("someDataKey", 42)]),
            TransferSet([ScriptTransfer(contextObj.caller, 100500, unit)])
        )
   else
       throw("Only owner can use this function.")
}
```

Ограничения для вызываемых функций:

* Имя функции не может превышать 255 символов.

* Каждая из функций может принимать не больше 22 аргументов.
* Допустимые типы аргументов: Int, String, Boolean, ByteVector

Результат выполнения один из:  ScriptResult, WriteSet, TransferSet.

WriteSet - в результате записывает список DataEntry\(ключ, значение\)в аккаунт. 

Ограничения**:**  
Максимальная длина списка: 100.  
Максимальный размер одного ключа: 100 символов.  


TransferSet - в результате создаёт список платежей ScriptTransfer, которые будут применены после вызова функции.

Ограничения**:**  
Максимальная длина списка: 10.

ScriptResult - содержит WriteSet и TransferSet.

```
@Callable(contextObj)
 
func foo() = {
    val a = 0
 
    if (a == 1)
    then ScriptResult(
            WriteSet([DataEntry("someDataKey", 42)]),
            TransferSet([ScriptTransfer(contextObj.caller, 100500, unit)])
        )
 
    else if (a == 2)
    then WriteSet([DataEntry("someDataKey", 42)]),
    else
        TransferSet([ScriptTransfer(contextObj.caller, 100500, unit)])
 
}
```

4. Функция валидации

Аналог скрипта на аккаунте. Т.е. данная функция проверяет **исходящие **с данного аккаунта транзакции - разрешает их выполнение или нет.

Такая функция помечается аннотацией @Verifier\(tx\), в скобках после которой указывается \(произвольное\) имя объекта контекста. Объект контекста - это текущая транзакция, которую в данный момент проверяет функция.

Результат выполнения:true, false, ошибка.

Если в DApp нет функции валидации, то выполняется алгоритм валидации по умолчанию - как на не заскриптованом аккаунте \(выполняется проверка подписи\)

\#Данная функция пропускает только TransferTransaction, т.е. любая другая транзакция с данного аккаунта не будет выполнена.

```
@Verifier(tx)
func verify() = {
    match tx {
        case ttx:TransferTransaction => sigVerify(ttx.bodyBytes, ttx.proofs[0], ttx.senderPublicKey)
        case _ => false
    }
}
```

**Вызов DApp функции \(InvokeScriptTransaction\):**



| **Параметр ** | **Описание** |
| :--- | :--- |
| **type**Int  **dappAddress**Address  **payment**OPTION\[AttachedPayment\]**fee** Int **feeAssetId**OPTION\[ByteVector\]   **call:    - function**String**    - args**LIST\[UNION\(Boolean,ByteVector,Int,String\)\]  **id** ByteVector **timestamp** Int **version** Int  **sender**Address **senderPublicKey** ByteVector **proofs** LIST ByteVector  **chainId**Byte | тип транзакции\(16 для InvokeScript\) адрес, по которому вызывается функция  отправляемый платеж \(размер, ассет\) размер вознаграждения идентификатор ассета вознаграждения\(null для Waves\)  имя вызываемой функциисписок передаваемых аргументов  идентификатор транзакции время выполнения транзакции версия транзакции\(на текущий момент 1\) адрес вызывающего аккаунта публичный ключ вызывающего аккаунта набор подписей, подтверждающих подлинность транзакции идентификатор сети блокчейна ‘T’ - тестовая‘W’ - продовая |

  
 Пример:

```
{
 
  "type": 16,
  "version": 1,
  "senderPublicKey": "2GEvUnpNpve2rSAs51c2HMTkaCYW9QRgwR16Z2HGJZgC",
  "dappAddress": "3FYR1f5YydHXF8dtfRJRyX3PoDCoT7a36Kq",
  "call": {
    "function": "deposit",
    "args": [{type:"integer", value: 600000000}]
  },
  "payment": [
    {
      "amount": 200000000,
      "assetId": null
    }
  ],
  "fee": 1000000,
  "feeAssetId": null,
  "timestamp": 1555073997308,
  "chainId": 68,
  "proofs": [
      "42Tf6VSVi3Cq6yHK1ENcVtyQbt9Ap8fcu57gYoZWChJTPPz52qRDM5NThuhFDVB4qE2gPZonuvjEJVtWHVYyNvJC"
  ],
  "id": "2fcMC9ihuLAcGNsbiSLDgz8dekq2JkrtjihroUiyNYCp"
}
```



Пример dApp:

```
# Директивы
{-# STDLIB_VERSION 3 #-}
{-# CONTENT_TYPE DAPP #-}
{-# SCRIPT_TYPE ACCOUNT #-}
 
# Блок Контекста скрипта
# ПУСТОЙ
 
# Вызываемая функция - принимает платежи с других аккаунтов и записывает на их счёт (в данные аккаунта, на котором установлен DApp)
@Callable(i)        # Объект контекста с именем i
func deposit() = {
   let pmt = extract(i.payment)
   if (isDefined(pmt.assetId)) then throw("can hodl waves only at the moment")
   else {
        let currentKey = toBase58String(i.caller.bytes)
        let currentAmount = match getInteger(this, currentKey) {
            case a:Int => a
            case _ => 0
        }
        let newAmount = currentAmount + pmt.amount
        WriteSet([DataEntry(currentKey, newAmount)])
   }
}
 
# Вызываемая функция - снимает со счёта валюту и переводит владельцу, если у нега на счету что-то есть
@Callable(i)                    # Объект контекста с именем i
func withdraw(amount: Int) = {
        let currentKey = toBase58String(i.caller.bytes)
        let currentAmount = match getInteger(this, currentKey) {
            case a:Int => a
            case _ => 0
        }
        let newAmount = currentAmount - amount
     if (amount < 0)
            then throw("Can't withdraw negative amount")
    else if (newAmount < 0)
            then throw("Not enough balance")
            else ScriptResult(
                    WriteSet([DataEntry(currentKey, newAmount)]),
                    TransferSet([ScriptTransfer(i.caller, amount, unit)])
                )
    }
 
 
# Функция валидации исходящих транзакций - данная реализация полностью соответствует реализации по умолчанию
@Verifier(tx)
func verify() = {
    sigVerify(tx.bodyBytes, tx.proofs[0], tx.senderPublicKey)
}
```

# Ограничения @Callable-функции {#id-Объяснениевалидациитранзакциискриптомаккаунта-Ограничения@Callable-функции}

**Максимальная длина названия функции:**255 символов

**Ограничения входных аргументов:  
**Допустимые типы: Int, String, Boolean, ByteVector**  
**Максимальное кол-во входных аргументов: 22

**Возвращаемое значение**- одно из:

**WriteSet ** — список из ключей и значений \(словарь\), определяющий какие данные сохранятся в аккаунте после успешного вызова контракта. Определяет информацию, которая будет добавлена к состоянию аккаунта.

**Ограничения:**  
Максимальная длина списка: 100.  
Максимальный размер одного ключа: 100 байт.  
Максимальный размер списка: 5 Кбайт.

**TransferSet**- список из ключей и значений \(словарь\), определяющий какие платежи будут совершены после успешного вызова контракта.

**Ограничения:**  
Максимальная длина списка: 10.

**ScriptResult**- структура данных, состоящая из WriteSet и TransferSet 

**Ограничения скрипта.  
**Максимальная сложность: 10000.  
Максимальный размер контракта:32 Кбайт.




