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
ms.openlocfilehash: c90450fa4cc35b460198f5a351a965aee4ea4f4b
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636414"
---
# <a name="build-globally-distributed-applications-with-azure-cosmos-db"></a>Az Azure Cosmos DB globálisan elosztott alkalmazások készítése

A napjaink alkalmazásai számos kötelező több adatközpontban működtetni. Ezeket az alkalmazásokat, globálisan elosztott alkalmazások hivatkozunk. Ezek az alkalmazások mindig "be", és világszerte több felhasználó számára elérhetők. A globális terjesztés, alacsony késleltetésű, rugalmasan méretezhető átviteli sebesség és a világ különböző pontjain található magas rendelkezésre állású, miközben ezek az alkalmazások által használt adatok kezelése rögzített probléma. Az Azure Cosmos DB egy globálisan elosztott adatbázis-szolgáltatás, amely közel valós idejű, rugalmasan méretezhető az átviteli sebesség, jól definiált szemantikát nyújt, és a magas rendelkezésre állás a. Röviden Ha az alkalmazások igényeihez válaszidővel bárhol a világon, ha szüksége van ahhoz, hogy mindig online és átviteli sebesség és tárterület korlátlan és rugalmas méretezhetőséget van szüksége, érdemes lehet alkalmazásokat Azure Cosmos DB használatával.

Az Azure Cosmos DB egy alapvető Azure-szolgáltatás, amely az összes elérhető [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/) alapértelmezés szerint. A Microsoft Azure-beli adatközpontok a világ különböző pontjain régiókban 54 + működik, és tovább bővíti az ügyfelek növekvő igényeknek regionális jelenlétét. Amikor létrehoz egy Azure Cosmos-fiókot, döntse el szeretné helyezni a vásárlásom. A Microsoft működtet, az Azure Cosmos DB szolgáltatás 24/7, így Ön az alkalmazásokra koncentrálhat.

Beállíthatja, hogy az adatbázisok, globálisan elosztott, és elérhető legyen bármelyik Azure-régiót. Csökkentheti a késést, helyezze az adatok közelebb, ahol a felhasználók is. A szükséges régiók kiválasztása attól függ, hogy a globális jelenlétét, az alkalmazás- és hol találhatók a felhasználók számára. Az Azure Cosmos DB transzparensen replikálja az adatokat a fiókon belül a fiókjához társított összes régióba. A globálisan elosztott Azure Cosmos-adatbázis és a tárolók, amelyek az alkalmazás olvashat és írhat a helyi egyetlen rendszerképet biztosít. Az Azure Cosmos DB adja hozzá, vagy távolítsa el a bármikor a fiókjához társított régiók. Az alkalmazás nem kell szüneteltetve vagy újratelepítése hozzáadni vagy eltávolítani egy régiót. Továbbra is magas rendelkezésre állású folyamatosan miatt, amely a szolgáltatás biztosít a többkiszolgálós képességekkel.

## <a name="key-benefits-of-global-distribution"></a>Fő előnyei a globális terjesztés

**Globális aktív-aktív alkalmazásfejlesztés**: több főkiszolgálós képessége lehetővé teszi, minden régióban egy írási régió (mellett olvasható). A több főkiszolgálós funkciót is garanciák - korlátlan, rugalmas írási méretezhetőséget, 99,999 %-os írási és olvasási szerte a világon, és garantált olvasási/írási művelet az összes kiszolgált 99 százalékon kevesebb mint 10 ezredmásodperc.  

A az Azure Cosmos DB többkiszolgálós API-k, az alkalmazás kompatibilis a legközelebbi régió, és kéréseket küldhetnek adott régióban. Konfigurációs módosítások nélkül legközelebbi régiót azonosítja. Adja hozzá vagy távolít el a régióban az Azure Cosmos DB-fiókja, az alkalmazás nem kell újra el kell végezni, és továbbra is magas rendelkezésre állású legyen.

**Reagáló alkalmazásokat készíthet**: az alkalmazás könnyedén megtervezhetők úgy, a közel valós idejű olvasási és írási, szemben az adatbázis számára választott minden régióban egy számjegyű ezredmásodperces késleltetéssel.  Az Azure Cosmos DB belsőleg kezeli az adatreplikációt a régiók közötti, úgy, hogy az Azure Cosmos-fiók választott konzisztenciaszinttől garantált.

Számos alkalmazásban a teljesítményt érintő továbbfejlesztés többrégiós (helyi) írási műveletek elvégzésének képességét a élvezheti. Tölcsér összes írási művelet egyetlen régióban történő inkább néhány konzisztenciát igénylő alkalmazásokhoz. Ezek az alkalmazások támogatása érdekében az Azure Cosmos DB egyrégiós és többrégiós konfigurációk is támogatja.

**Magas rendelkezésre állású alkalmazásokat készíthet**: egy adatbázis több régióban üzemelő növeli az adatbázis rendelkezésre állását. Ha egy régió elérhetetlenné válik, más régiókban automatikusan fogja kezelni az alkalmazásokra irányuló kérések. Az Azure Cosmos DB kínál 99,999 %-os írási és olvasási többrégiós adatbázisok rendelkezésre állását.

**Üzletmenet-folytonossági során a regionális üzemkimaradások utáni helyreállításon**: Azure Cosmos DB támogatja a [automatikus feladatátvétel](how-to-manage-database-account.md#automatic-failover) egy regionális kimaradás során. Ezen felül egy regionális kimaradás során az Azure Cosmos DB továbbra is a késés, a rendelkezésre állási, a konzisztencia és az átviteli SLA-k kezelése. Azure Cosmos DB segítségével ellenőrizze a teljes alkalmazás-e magas rendelkezésre állású, kínál a manuális feladatátvételt API egy regionális kimaradás szimulálásához. Ez az API használatával rendszeres üzleti folytonossági gyakorlatokat is végezhet.

**Globális írási és olvasási méretezhetőség**: több főkiszolgálós képességgel rugalmasan skálázhatja olvasási és írási átviteli sebesség a világ minden tájáról. Több főkiszolgálós funkció biztosítja, hogy az átviteli sebességet, hogy az alkalmazás konfigurálása egy Azure Cosmos DB adatbázison, vagy egy tárolót az összes régióban elérhető, valamint által védett [pénzügyileg támogatott SLA-k](https://aka.ms/acdbsla).

**Többszörös, jól definiált konzisztenciamodellekkel**: Azure Cosmos DB replikációs protokoll, amelyet öt jól meghatározott, és intuitív konzisztenciamodelleket kínál. Minden egyes konzisztencia modellnek konzisztencia és a teljesítmény magával. Ezek konzisztenciamodell lehetővé teszi a globálisan elosztott alkalmazások létrehozása egyszerű.

## <a id="Next Steps"></a>Következő lépések

További információ a globális terjesztés, az alábbi cikkeket:

* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
* [A többkiszolgálós ügyfelek konfigurálása](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Hogyan régiók hozzáadása/eltávolítása az Azure Cosmos-fiókból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Egy egyéni ütközésfeloldási házirend SQL API-fiókok létrehozása](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)