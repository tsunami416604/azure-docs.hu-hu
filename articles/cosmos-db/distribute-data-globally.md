---
title: Globális adatterjesztés az Azure Cosmos DB |} A Microsoft Docs
description: Ismerje meg a globális georeplikáció, több főkiszolgálós, feladatátvétel és data recovery global Database-adatbázisok az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás használatával.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 68ac603964593892dfcbc3488b4e6f2c91a0eb92
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238274"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Az Azure Cosmos DB globális terjesztési

A napjaink alkalmazásai számos futtathatja, vagy rendelkezik ambitions világra futtatásához. Ezek mindig működnek, és a felhasználók számára a világ minden pontján elérhető. A globális terjesztés, miközben nagy teljesítményű és magas rendelkezésre állást biztosít az alkalmazások által használt adatok kezelése rögzített probléma. A cosmos DB egy globálisan elosztott adatbázis-szolgáltatás, amely rendelkezik lett kihasználására tervezett egészen az alapoktól be a fenti kérdések megválaszolása.

A cosmos DB egy alapvető Azure-szolgáltatás, amely az összes elérhető [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/) alapértelmezés szerint. A Microsoft Azure-adatközpontokban világszerte több mint 50 régióban működik, és tovább bővíti az ügyfelek növekvő igényeknek. A Microsoft üzemelteti a Cosmos DB szolgáltatás 24/7, így Ön az alkalmazásokra koncentrálhat. Amikor létrehoz egy Cosmos DB-fiókot, döntse el szeretné helyezni a vásárlásom.

Cosmos DB-ügyfelek a globálisan elosztott és elérhető az 1-től bárhol 50 + Azure-régiók adatbázisaikat konfigurálhatja. Csökkentheti a késést, érdemes a helyezze közelebb a felhasználó tartózkodási helyén, így a régiók mellett dönt, és az, hogy melyik régiót kell futtatni attól függ, a globális elérni az alkalmazást, és hol találhatók a felhasználók. A cosmos DB transzparensen replikálja az Cosmos-fiókban lévő összes adatot a konfigurált régiók. A cosmos DB a Cosmos-adatbázis és a tárolók, egyetlen rendszerképet biztosít az, hogy az alkalmazás olvashat és írhat helyileg. A Cosmos DB használatával adja hozzá, vagy távolítsa el a bármikor a fiókjához társított régiók. Az alkalmazás nem kell szüneteltetve vagy újratelepítése, és továbbra is magas rendelkezésre állású kiszolgáló mindig köszönhetően, amely a szolgáltatás biztosít a többkiszolgálós képességekkel.

## <a name="key-benefits-of-global-distribution"></a>Fő előnyei a globális terjesztés

**Alkalmazásfejlesztés globális aktív-aktív, egyszerűen**: több főkiszolgálós képessége lehetővé teszi, minden régióban (mellett olvasható) írható, korlátlan rugalmas engedélyezése írás méretezhetősége, 99,999 %-os olvasási és írási, a világ minden tájáról, és garantált gyors olvasások és írások kevesebb mint 10 ezredmásodperc, 99 százalékon szolgálja ki.  

Az Azure Cosmos DB többkiszolgálós API-k, az alkalmazás mindig tudni fogja a legközelebbi régió, és kérelmet küld az adott régióban. Konfigurációs módosítások nélkül legközelebbi régiót azonosítja. Adjon hozzá vagy távolít el a régióban a Cosmos DB-fiókja, az alkalmazás nem kell újra el kell végezni, és továbbra is magas rendelkezésre állású legyen.

**Reagáló alkalmazásokat készíthet**: az alkalmazás könnyedén megtervezhetők úgy, a közel valós idejű olvasási és írási, egyszámjegyű ezredmásodperces késleltetéssel, szemben az összes régióban az adatbázis választotta.  Az Azure Cosmos DB belsőleg kezeli az adatreplikáció úgy, hogy garantálja a Cosmos-fiók választott konzisztenciaszinttől-régiók között.

Számos alkalmazásban a teljesítményt érintő továbbfejlesztés többrégiós (helyi) írási műveletek elvégzésének képességét a élvezheti. Egyes alkalmazások, például azokkal, amelyek igényelnek erős konzisztencia, a tölcsér összes írási művelet egyetlen régióban történő részesíti előnyben. A cosmos DB támogatja a konfigurációk, a egyrégiós és többrégiós.

**Magas rendelkezésre állású alkalmazásokat készíthet**: egy adatbázis több régióban üzemelő javítja az adatbázis rendelkezésre állását. Ha egy régió elérhetetlenné válik, más régiókban automatikusan fogja kezelni az alkalmazásokra irányuló kérések. Az Azure Cosmos DB kínál 99,999 %-os írási és olvasási többrégiós adatbázisok rendelkezésre állását.

**Üzletmenet-folytonossági során a regionális üzemkimaradások utáni helyreállításon**: Azure Cosmos DB támogatja a [automatikus feladatátvétel](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account) egy regionális kimaradás során. Ezen felül egy regionális kimaradás során Cosmos DB továbbra is a késés, a rendelkezésre állási, a konzisztencia és az átviteli SLA-k kezelése. Annak biztosítására, a teljes alkalmazás nem magas rendelkezésre állású, az Azure Cosmos DB manuális feladatátvétel API-t egy regionális kimaradás szimulálása kínál. Ez az API használatával rendszeres üzleti folytonossági gyakorlatok végrehajtása, és készen áll.

**Globális írási és olvasási méretezhetőség**: több főkiszolgálós képességgel rugalmasan skálázhatja olvasási és írási átviteli sebesség a világ minden tájáról. Több főkiszolgálós funkció biztosítja, hogy az átviteli sebességet, hogy az alkalmazás konfigurálása egy Azure Cosmos DB adatbázison, vagy egy tárolót az összes régióban elérhető, valamint által védett [pénzügyileg támogatott SLA-k](https://aka.ms/acdbsla).

**Többszörös, jól definiált konzisztenciamodellekkel**: Azure Cosmos DB replikációs protokoll, amelyet az öt jól definiált, gyakorlati kínál, és intuitív konzisztencia a konzisztencia és a teljesítmény egyértelmű magával az egyes modellek. Ezek konzisztenciamodell lehetővé teszi egyszerű megfelelő globálisan elosztott alkalmazásokat hozhat létre.

## <a id="Next Steps"></a>Következő lépések

További információ a globális terjesztés, az alábbi cikkeket:

* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
* [A többkiszolgálós ügyfelek konfigurálása](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Hogyan régiók hozzáadása/eltávolítása az adatbázisból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Egy egyéni ütközésfeloldási házirend SQL API-fiókok létrehozása](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)