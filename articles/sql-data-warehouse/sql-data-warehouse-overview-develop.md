<properties
   pageTitle="SQL 資料倉儲的設計決策和程式碼撰寫技術開發 | Microsoft Azure"
   description="SQL 資料倉儲的開發概念、設計決策、建議和程式碼撰寫技巧。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL 資料倉儲的設計決策和程式碼撰寫技術

若要進一步瞭解 SQL 資料倉儲的重要的設計決策、建議和程式碼撰寫技術，請參閱這些開發文章。

## 主要的設計決策
下列文章的重點在於使用 SQL 資料倉儲開發分散式資料倉儲時，必須了瞭解的一些重要概念和設計決策：

- [連線][]
- [並行][]
- [交易][]
- [使用者定義的結構描述][]
- [資料表設計][]
- [雜湊散發索引鍵][]
- [資料表資料分割][]
- [CTAS][]
- [統計資料][]

## 開發建議和程式碼撰寫技術
這些文章會強調特定的程式碼撰寫技術、秘訣和建議，用於開發您的 SQL 資料倉儲：

- [預存程序][]
- [標籤][]
- [檢視][]
- [暫存資料表][]
- [動態 SQL][]
- [迴圈][]
- [重新命名的物件][]
- [樞紐分析資料][]
- [群組依據選項][]
- [變數指派][]

## 後續步驟
一旦您已開發文章仔細閱讀 [TRANSACT-SQL 參考][] 如需有關支援的語法，SQL 資料倉儲的頁面。

<!--Image references-->

<!--Article references-->
[concurrency]: sql-data-warehouse-develop-concurrency.md
[connections]: sql-data-warehouse-develop-connections.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[dynamic SQL]: sql-data-warehouse-develop-dynamic-sql.md
[group by options]: sql-data-warehouse-develop-group-by-options.md
[hash distribution keys]: sql-data-warehouse-develop-hash-distribution-key.md
[labels]: sql-data-warehouse-develop-label.md
[looping]: sql-data-warehouse-develop-loops.md
[pivoting data]: sql-data-warehouse-develop-pivot-unpivot.md
[renaming objects]: sql-data-warehouse-develop-rename.md
[statistics]: sql-data-warehouse-develop-statistics.md
[stored procedures]: sql-data-warehouse-develop-stored-procedures.md
[table design]: sql-data-warehouse-develop-table-design.md
[table partitions]: sql-data-warehouse-develop-table-partitions.md
[temporary tables]: sql-data-warehouse-develop-temporary-tables.md
[transactions]: sql-data-warehouse-develop-transactions.md
[user-defined schemas]: sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: sql-data-warehouse-develop-variable-assignment.md
[views]: sql-data-warehouse-develop-views.md

[Transact-SQL reference]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!--Other Web references-->

