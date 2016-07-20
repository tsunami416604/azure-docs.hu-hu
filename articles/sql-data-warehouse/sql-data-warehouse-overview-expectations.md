<properties
   pageTitle="Az SQL Data Warehouse előzetes verziójával kapcsolatos elvárások | Microsoft Azure"
   description="Az SQL Data Warehouse nyilvános előzetes verziójának képességeivel és a szolgáltatás általános elérhetőségével kapcsolatos céljaink összegzése."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/05/2016"
   ms.author="nicw;barbkess;sonyama"/>


# Az SQL Data Warehouse előzetes verziójával kapcsolatos elvárások

Ez a cikk az SQL Data Warehouse előzetes verzió képességeit, valamint a szolgáltatás általános elérhetőségével (GA) kapcsolatos céljainkat ismerteti. Ezeket az információkat a nyilvános előzetes verzió képességeinek fejlesztéseivel párhuzamosan folyamatosan frissítjük.

Az SQL Data Warehouse-szal kapcsolatos céljaink:

- Kiszámítható teljesítmény és lineáris méretezhetőség akár több petabájtnyi adatmennyiségig
- Magas megbízhatóság minden adatraktár-művelethez
- Relációs és nem relációs adatok gyors feldolgozása

Folyamatosan dolgozunk azon, hogy elérjük ezeket az SQL Data Warehouse előzetes verziójával kapcsolatos célokat.

## Kiszámítható és méretezhető teljesítmény

Az SQL Data Warehouse bevezeti az adattárházegységet (DWU), amellyel mérhetők az adatraktárhoz elérhető számítási erőforrások (CPU-k, memória, tárolási I/O). A DWU-k számának növelése növeli az erőforrásokat. Ahogy a DWU-k száma növekszik, az SQL Data Warehouse több elosztott erőforráson párhuzamosan futtat műveleteket (pl. adatok betöltését vagy lekérdezését). Ezzel csökkenthető a késleltetés és javítható a teljesítmény.

Minden adatraktárnak 2 alapvető teljesítmény-mérőszáma van:

- Betöltési sebesség. Az adatraktárba másodpercenként betölthető rekordok száma. Kifejezetten mérjük a PolyBase által az Azure Blob Storage-ból egy fürtözött oszloptárindexszel rendelkező táblába importálható rekordok számát.
- Vizsgálati sebesség. Az adatraktárból egymás után lekérhető rekordok száma másodpercenként. Kifejezetten mérjük a fürtözött oszloptárindexeken végzett lekérdezések által visszaadott rekordok számát.

Mérünk néhány fontos teljesítményfejlesztést, és hamarosan megosztjuk a várt sebességeket. Az előzetes verzió alatt folyamatos fejlesztéseket végzünk (pl. a tömörítésen és a gyorsítótárazáson) a sebesség növelése és a kiszámítható méretezés biztosítása érdekében.  

## Adatvédelem

Az SQL Data Warehouse minden adatot az Azure Storage-ban tárol helyileg redundáns tárolással. A rendszer több szinkron másolatot tart az adatokról a helyi adatközpontban, így transzparens adatvédelmet garantál helyi hibák esetén. 

## Biztonsági másolatok

Az SQL Data Warehouse legalább 8 óránként biztonsági mentést készít az Azure Storage-pillanatfelvételekkel. Ezeket a pillanatfelvételeket a rendszer 7 napig tartja meg. Az adatok így az előző 7 nap legalább 21 időpontjára visszaállíthatók a legutolsó pillanatfelvétel idejével bezárólag. Az adatokat a PowerShell vagy a REST API-k segítségével állíthatja vissza egy pillanatfelvételből.

## A lekérdezések megbízhatósága

Az SQL Data Warehouse egy nagymértékben párhuzamos feldolgozási (MPP) architektúrára épül. Az SQL Data Warehouse automatikusan észleli és áttelepíti a számítási és vezérlési csomópontok hibáit. Előfordulhat azonban, hogy egy művelet (pl. adatok betöltése vagy lekérdezése) meghiúsul egy csomóponthiba vagy egy áttelepítés miatt. Folyamatosan fejlesztjük az előzetes verziót, hogy a műveletek a csomóponthibák ellenére is sikeresen végrehajthatók legyenek.


## Frissítések és állásidő

Az SQL Data Warehouse-t időnként frissítjük, hogy új szolgáltatásokkal bővítsük és kritikus javításokat telepítsünk.  Ezek a frissítések zavart okozhatnak, és jelenleg a frissítések nem kiszámítható ütemezés szerint zajlanak.  Ha úgy találja, hogy ez a folyamat túl zavaró, javasoljuk [egy támogatási jegy létrehozását][], hogy segíthessünk a folyamat megkerülésében.


## További lépések

[Bevezetés][] a nyilvános előzetes verzió használatába.

<!--Image references-->

<!--Article references-->
[egy támogatási jegy létrehozását]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Bevezetés]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Jun16_HO2-->


