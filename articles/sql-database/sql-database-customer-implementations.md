---
title: "Az Azure SQL Database felhasználói megvalósítások-technikai |} Microsoft Docs"
description: "További tudnivalók az Azure SQL Database üzleti problémák megoldására ügyfél implementatons vonatkozó technikai részleteket"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 00c8a713-f20c-4d6b-b8b7-0c1b9ba5f05b
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 03/03/2017
ms.author: carlrab
ms.openlocfilehash: 3da41adfa0865c0741c61b5d15c3ef1741eb6118
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="azure-sql-database-customer-implementation-technical-studies"></a>Az Azure SQL Database felhasználói megvalósítási műszaki tanulmányok

- [Daxko](sql-database-implementation-daxko.md): Daxko/CSI szoftver egy ellenőrző tapasztalt: alkalmasságra és újbóli központok az ügyfél alapja gyorsan, növekszik az környezetnek köszönhetően az átfogó vállalati-szoftveres megoldás, sikeres volt, de tartja az IT-infrastruktúra funkciókra van szüksége a hogy az ügyfelek alap növekvő lett ellenőrzi a vállalat informatikai részleg által. A vállalat egyre növekvő műveletek terhelést jelenthet, különösen a növekvő adatbázisok kezelése által lett korlátozott. Adott műveletek terhelés rosszabb, fejlesztői erőforrások a új kezdeményezések, például a vállalati szoftver új mobilitási szolgáltatásai be lett kivágja.

- [GEP](sql-database-implementation-gep.md): GEP biztosítja a szoftverek és -szolgáltatások, amelyek lehetővé teszik a vállalatok számára műveleteit, azokat a stratégiákat és pénzügyi teljesítményének gyakorolt hatásának maximalizálása a világ különböző beszerzési vezetők. Tanácsadási és felügyelt szolgáltatások a vállalat INTELLIGENS GEP®, egy felhőalapú, átfogó beszerzési szoftver platform által lehetőséget biztosít. Azonban GEP hibába ütközött az támogatja a megoldások, például saját GEP által INTELLIGENS a helyszíni adatközpontját próbál korlátozások: a szükséges beruházások voltak elsajátíthatják a használatát, és más országokban szabályozási követelmények volna végzett a szükséges beruházásokat tűnhet még több. Át a felhőbe, GEP informatikai erőforrások felszabadult lehetővé téve nyugodt kisebb IT-üzemeltetők és az új adatforrások érték az ügyfelek a világszerte több összpontosíthat.

- [SnelStart](sql-database-implementation-snelstart.md): SnelStart Hollandia népszerű pénzügyi - és üzleti-kezelési szoftver a kis - és közepes méretű vállalkozások számára (SMB) hoz. A személyzet 110 alkalmazottak, beleértve az informatikai részleg 35 által kiszolgált 55,000 ügyfeleinek. Asztali szoftverek áthelyezése egy szoftver,--szolgáltatás (SaaS) épülő Azure ajánlat, által az SnelStart végrehajtott beépített szolgáltatások többsége megszokott környezetben a C# segítségével, és a teljesítmény és méretezhetőség optimalizálása sem felett vagy alatt-átadásának rugalmas készleteket használó vállalatok által felügyeletének automatizálására. SnelStart Azure segítségével biztosítja a helyszíni és a felhő közötti áthelyezése ügyfelek rugalmasság.

- [Umbraco](sql-database-implementation-umbraco.md): egyszerűbbé teheti a felhasználói telepítés, Umbraco hozzáadott Umbraco,--szolgáltatás (UaaS): a szoftver,--szolgáltatás (SaaS) ajánlat, amely szükségtelenné teszi a helyszíni központi telepítések, a beépített skálázás, és eltávolítja a felügyeleti terhelést, mivel lehetővé teszi a fejlesztők számára a termék megoldás helyett innováció kezelésére. Umbraco el tudja-e az adott előnyök kihasználása érdekében a Microsoft Azure által kínált rugalmas platform,--szolgáltatás (PaaS) modell megtalálható.

- [Kvórum](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database): kvórum kulcsának adatbázis munkaterhelés megduplázódik, miközben az SQL Database 70 %-kal DTU csökkenti.

- [Kérelem kitöltése](https://customers.microsoft.com/story/quest): hívása nyújt az SQL Server Enterprise-bA egy cél szem előtt a Spotlight: adatok védelme, körül adatok áthelyezése és adatbázis-műveletek figyelését az elérhető legjobb eszközöket adhat adatbázis szakemberek számára. A Spotlight, Microsoft Azure és az Azure SQL Database SQL Server adatbázis-rendszergazdák is figyelése, észleli, diagnosztizálása és teszik lehetővé az SQL Server, a teljesítménnyel kapcsolatos problémák megoldásához, az asztalok ülne, vagy otthonról dolgozik.

- [A Microsoft Dynamics](https://customers.microsoft.com/story/dynamics365operationsproductteam): A rövid esettanulmány kiemeli az ajánlott eljárásokról és a Dynamics 365 műveletek termékcsapatának élmény ahhoz, hogy egy teljes körűen felügyelt szoftver biztosít az ügyfeleknek az Azure SQL Database áttelepítésének tanulságait a Szolgáltatásajánlat (SaaS). Azure SQL-adatbázist használja, a műveleti csapata Dynamics 365 felügyelete, és jelentősen kevesebb személyzettel a szolgáltatás működtetésére, és könnyedén méretezhető az out-of-az-box kezelhetőségi szolgáltatásokkal, például az adatbázis automatikus biztonsági mentés, adatbázis biztonsági másolatok megőrzésének , magas rendelkezésre állási és vészhelyreállítási helyreállítási funkciók. Az Azure SQL Database az alatt a felügyeleti teendők központjaként szolgáltatások folyamatos kiváló platform kölcsönbe ez kiépíteni az adatbázisokat trivial automatizálással képes együtt.

- [Microsoft OneDrive és SharePoint Online](https://customers.microsoft.com/story/microsoft-azure-sql-database-dicrete-manufacturing-united-states): A rövid esettanulmány mögött Microsoft OneDrive és SharePoint Online áthelyezés szövegegység közli az Azure SQL Database, és ismerteti, hogyan az áttelepítés engedélyezett rugalmas majdnem korlátlan kapacitás kezelése közben is jelentősen csökkenti a működési költségeket és infrastruktúra munkaterhelés.
