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
ms.openlocfilehash: 4aa5e4ff46eeaa4e8d8c723f626dd1f1193fd12a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281608"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Az Azure Cosmos DB globális terjesztési

Globális skálázhatóság georeplikációval a napjaink alkalmazásai számos futtatni. Ezek az alkalmazások mindig, on, és világszerte több felhasználó számára hozzáférhető. A globális terjesztés, miközben nagy teljesítményű és magas rendelkezésre állást biztosít az alkalmazások által használt adatok kezelése rögzített probléma. Az Azure Cosmos DB egy globálisan elosztott adatbázis-szolgáltatás, amely a nagy teljesítményű és magas rendelkezésre állást biztosít a. Ebből kifolyólag miatt Azure Cosmos DB az alábbi valós idejű alkalmazások számára a leginkább megfelelő.

A cosmos DB egy alapvető Azure-szolgáltatás, amely az összes elérhető [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/) alapértelmezés szerint. A Microsoft Azure-beli adatközpontok a világ különböző pontjain régiókban 54 + működik, és tovább bővíti az ügyfelek növekvő igényeknek. Amikor létrehoz egy Cosmos DB-fiókot, döntse el szeretné helyezni a vásárlásom. A Microsoft üzemelteti a Cosmos DB szolgáltatás 24/7, így Ön az alkalmazásokra koncentrálhat.

Beállíthatja, hogy az adatbázisok, globálisan elosztott, és elérhető legyen, a több mint 50 Azure-régiók egyikében. Csökkentheti a késést, helyezze az adatok közelebbi helyen tárolhatók a felhasználói helyére. A szükséges régiók kiválasztása attól függ, hogy a globális jelenlétét, az alkalmazás- és hol találhatók a felhasználók számára. A cosmos DB transzparensen replikálja az adatokat a fiókon belül minden konfigurált régióban. A Cosmos-adatbázis és a tárolók, amelyekkel az alkalmazás olvashat és írhat helyileg egyetlen rendszerképet biztosít. A Cosmos DB használatával adja hozzá, vagy távolítsa el a bármikor a fiókjához társított régiók. Az alkalmazás nem kell szüneteltetve vagy újratelepítése hozzáadni vagy eltávolítani egy régiót. Továbbra is magas rendelkezésre állású folyamatosan miatt, amely a szolgáltatás biztosít a többkiszolgálós képességekkel.

## <a name="key-benefits-of-global-distribution"></a>Fő előnyei a globális terjesztés

**Globális aktív-aktív alkalmazásfejlesztés**: több főkiszolgálós képessége lehetővé teszi, minden régióban egy írási régió (mellett olvasható). A több főkiszolgálós funkciót is garanciák - korlátlan, rugalmas írási méretezhetőséget, 99,999 %-os írási és olvasási szerte a világon, és garantált olvasási/írási művelet az összes kiszolgált 99 százalékon kevesebb mint 10 ezredmásodperc.  

A az Azure Cosmos DB többkiszolgálós API-k, az alkalmazás kompatibilis a legközelebbi régió, és kéréseket küldhetnek adott régióban. Konfigurációs módosítások nélkül legközelebbi régiót azonosítja. Adjon hozzá vagy távolít el a régióban a Cosmos DB-fiókja, az alkalmazás nem kell újra el kell végezni, és továbbra is magas rendelkezésre állású legyen.

**Reagáló alkalmazásokat készíthet**: az alkalmazás könnyedén megtervezhetők úgy, a közel valós idejű olvasási és írási, szemben az adatbázis számára választott minden régióban egy számjegyű ezredmásodperces késleltetéssel.  Az Azure Cosmos DB belsőleg kezeli az adatreplikációt a régiók közötti, úgy, hogy a Cosmos-fiók választott konzisztenciaszinttől garantált.

Számos alkalmazásban a teljesítményt érintő továbbfejlesztés többrégiós (helyi) írási műveletek elvégzésének képességét a élvezheti. Tölcsér összes írási művelet egyetlen régióban történő inkább néhány konzisztenciát igénylő alkalmazásokhoz. Ezek az alkalmazása támogatásához, a Cosmos DB egyrégiós és többrégiós konfigurációk is támogatja.

**Magas rendelkezésre állású alkalmazásokat készíthet**: egy adatbázis több régióban üzemelő növeli az adatbázis rendelkezésre állását. Ha egy régió elérhetetlenné válik, más régiókban automatikusan fogja kezelni az alkalmazásokra irányuló kérések. Az Azure Cosmos DB kínál 99,999 %-os írási és olvasási többrégiós adatbázisok rendelkezésre állását.

**Üzletmenet-folytonossági során a regionális üzemkimaradások utáni helyreállításon**: Azure Cosmos DB támogatja a [automatikus feladatátvétel](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account) egy regionális kimaradás során. Ezen felül egy regionális kimaradás során Cosmos DB továbbra is a késés, a rendelkezésre állási, a konzisztencia és az átviteli SLA-k kezelése. Azure Cosmos DB segítségével ellenőrizze a teljes alkalmazás-e magas rendelkezésre állású, kínál a manuális feladatátvételt API egy regionális kimaradás szimulálásához. Ez az API használatával rendszeres üzleti folytonossági gyakorlatokat is végezhet.

**Globális írási és olvasási méretezhetőség**: több főkiszolgálós képességgel rugalmasan skálázhatja olvasási és írási átviteli sebesség a világ minden tájáról. Több főkiszolgálós funkció biztosítja, hogy az átviteli sebességet, hogy az alkalmazás konfigurálása egy Azure Cosmos DB adatbázison, vagy egy tárolót az összes régióban elérhető, valamint által védett [pénzügyileg támogatott SLA-k](https://aka.ms/acdbsla).

**Többszörös, jól definiált konzisztenciamodellekkel**: Azure Cosmos DB replikációs protokoll, amelyet öt jól meghatározott, és intuitív konzisztenciamodelleket kínál. Minden egyes konzisztencia modellnek konzisztencia és a teljesítmény magával. Ezek konzisztenciamodell lehetővé teszi a globálisan elosztott alkalmazások létrehozása egyszerű.

## <a id="Next Steps"></a>Következő lépések

További információ a globális terjesztés, az alábbi cikkeket:

* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
* [A többkiszolgálós ügyfelek konfigurálása](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Hogyan régiók hozzáadása/eltávolítása Cosmos-fiókjából](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Egy egyéni ütközésfeloldási házirend SQL API-fiókok létrehozása](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)