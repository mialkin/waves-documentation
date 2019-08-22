# SetScriptTransaction

Структура [транзакции установки скрипта](/blockchain/transaction-type/set-script-transaction.md).

## Конструктор

``` ride
SetScriptTransaction(script: ByteVector|Unit, id: ByteVector, fee: Int, timestamp: Int, version: Int, sender: Address, senderPublicKey: ByteVector, bodyBytes: ByteVector, proofs: List[ByteVector])
```

## Поля структуры

| # | Название | Тип данных | Описание |
| :--- | :--- | :--- | :--- |
| 1 | script | [ByteVector](/ride/data-types/byte-vector.md)&#124;[Unit](/ride/data-types/unit.md) | [Скрипт аккаунта](/ride/script/script-types/account-script.md) |
| 2 | id | [ByteVector](/ride/data-types/byte-vector.md) | [ID транзакции](/blockchain/transaction/transaction-id.md) |
| 3 | fee | [Int](/ride/data-types/int.md) | [Комиссия за транзакцию](/blockchain/transaction-fee.md) |
| 4 | timestamp | [Int](/ride/data-types/int.md) | [Временная метка транзакции](/blockchain/transaction/transaction-timestamp.md) |
| 5 | version | [Int](/ride/data-types/int.md) | Версия [структуры данных](/blockchain/transaction-data-structure.md) транзакции |
| 6 | sender | [Address](/ride/structures/common-structures/address.md) | [Адрес](/blockchain/address.md) отправителя транзакции |
| 7 | senderPublicKey | [ByteVector](/ride/data-types/byte-vector.md) | Публичный ключ отправителя транзакции |
| 8 | bodyBytes | [ByteVector](/ride/data-types/byte-vector.md) | [Байты тела транзакции](/blockchain/transaction/transaction-body-bytes.md) |
| 9 | proofs | [List](/ride/data-types/list.md)[[ByteVector](/ride/data-types/byte-vector.md)] | Список [подтверждений](/blockchain/transaction-proof.md) |