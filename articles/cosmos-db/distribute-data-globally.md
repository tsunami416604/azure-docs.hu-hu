---
title: Globális adatterjesztés az Azure Cosmos DB-hez
description: Ismerje meg a globális georeplikáció, több főkiszolgálós, feladatátvétel és data recovery global Database-adatbázisok az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás használatával.
services: cosmos-db
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.openlocfilehash: 2c217a1a89d3b573bfe2297a263bf55849b5f6e1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843852"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Az Azure Cosmos DB globális terjesztési

A napjaink alkalmazásai válaszidejű, és mindig online szükségesek. Alacsony késéssel és magas rendelkezésre állás elérése érdekében ezeket az alkalmazásokat példányát kell üzembe helyezni a felhasználók közelében lévő adatközpontokban. Ezek az alkalmazások általában több adatközpontban telepített, és globálisan elosztott nevezzük. Globálisan elosztott alkalmazások egy globálisan elosztott adatbázis, amely átláthatóan képes replikálni az adatokat az alkalmazások az adatokat, amely a felhasználók közel van a művelethez használandó a világ bármely pontján található kell. 

Az Azure Cosmos DB egy globálisan elosztott adatbázis-szolgáltatás, amely közel valós idejű, rugalmasan méretezhető az átviteli sebesség, jól definiált szemantikát nyújt, és a magas rendelkezésre állású tervezték, hogy a. Röviden Ha az alkalmazás kell gyors garantált válaszidő bárhol a világon, ha ez szükséges, hogy mindig online, és korlátlan és rugalmas méretezhetőséget átviteli sebesség és tárterület van szüksége, fontolja meg alkalmazások Azure Cosmos DB használatával.

Beállíthatja, hogy az adatbázisok, globálisan elosztott, és elérhető legyen bármelyik Azure-régiót. Csökkentheti a késést, helyezze el az adatok közelebb, ahol a felhasználók is. A szükséges régiók kiválasztása attól függ, hogy a globális jelenlétét, az alkalmazás- és hol találhatók a felhasználók számára. Az Azure Cosmos DB transzparensen replikálja az adatokat a fiókon belül a fiókjához társított összes régióba. A globálisan elosztott Azure Cosmos-adatbázis és a tárolók, amelyek az alkalmazás olvashat és írhat a helyi egyetlen rendszerképet biztosít. 

Az Azure Cosmos DB adja hozzá, vagy távolítsa el a bármikor a fiókjához társított régiók. Az alkalmazás szüneteltetve vagy újratelepítése hozzáadni vagy eltávolítani egy régió nem szükséges. Továbbra is magas rendelkezésre állású folyamatosan miatt a többhelyűségével képességeket, amelyek a szolgáltatás biztosítja.

## <a name="key-benefits-of-global-distribution"></a>Fő előnyei a globális terjesztés

**Globális aktív-aktív alkalmazásokat készíthet.** Több főkiszolgálós szolgáltatással minden régióban egy írási régióba. Emellett akkor is olvasható. A több főkiszolgálós funkciót is biztosítja:

- Korlátlan, rugalmas írás méretezhetősége. 
- 99,999 %-os írási és olvasási a világ minden tájáról.
- Garantált olvasási és írási 99 százalékon kevesebb mint 10 ezredmásodperc szolgálja ki.

Az Azure Cosmos DB többhelyűség API-k használatával az alkalmazás kompatibilis a legközelebbi régió. Majd kéréseket küldhetnek adott régióban. A legközelebbi régió azonosított konfigurációs módosítások nélkül. Adja hozzá vagy távolít el a régióban az Azure Cosmos DB-fiókja, az alkalmazás újbóli üzembe helyezéséhez nem szükséges. Az alkalmazás továbbra is magas rendelkezésre állású legyen.

**Reagáló alkalmazásokat készíthet.** Az alkalmazás is könnyedén úgy, hogy közel valós idejű olvasások és írások. Az adatbázis számára is választott minden régióban ellen egyszámjegyű ezredmásodperces késéseket képes használni. Az Azure Cosmos DB belsőleg kezeli az adatreplikációt a régiók között. Ennek eredményeképpen a konzisztencia szintjét, az Azure Cosmos DB-fiók kijelölt garantált.

Számos alkalmazás kihasználhatják a teljesítményt érintő továbbfejlesztés, amely lehetővé teszi az többrégiós (helyi) írási műveletek végrehajtásához származnak. Tölcsér összes írási művelet egyetlen régióban történő inkább néhány konzisztenciát igénylő alkalmazásokhoz. Ezekhez az alkalmazásokhoz az Azure Cosmos DB támogatja az egyrégiós és többrégiós konfigurációkat.

**Magas rendelkezésre állású alkalmazásokat készíthet.** Egy adatbázis több régióban üzemelő növeli az adatbázis rendelkezésre állását. Ha egy régió elérhetetlenné válik, más régiókban automatikusan kezelik az alkalmazásokra irányuló kérések. Az Azure Cosmos DB kínál 99,999 %-os írási és olvasási többrégiós adatbázisok rendelkezésre állását.

**Üzletmenet-folytonossági karbantartása során a regionális üzemkimaradások utáni helyreállításon.** Az Azure Cosmos DB támogatja a [automatikus feladatátvétel](how-to-manage-database-account.md#automatic-failover) egy regionális kimaradás során. Egy regionális kimaradás során az Azure Cosmos DB SLA-k a késés, a rendelkezésre állási, a konzisztencia és az átviteli sebesség fenntartása érdekében továbbra is. Annak érdekében, ügyeljen arra, hogy a teljes alkalmazás magas rendelkezésre állású, az Azure Cosmos DB manuális feladatátvételt egy regionális kimaradás szimulálásához API kínál. Ez az API használatával is végrehajthasson rendszeres üzleti folytonossági gyakorlatokat.

**Méretezés és globálisan olvasási átviteli sebességet.** Több főkiszolgálós szolgáltatással rugalmasan skálázhatja olvasása és írása az átviteli sebesség a világ minden tájáról. A több főkiszolgálós funkció garantálja, hogy az átviteli sebességet, hogy az alkalmazás konfigurálása egy Azure Cosmos DB adatbázison, vagy egy tároló kézbesíti a rendszer minden régióban. Az átviteli sebességet is védi [pénzügyileg támogatott SLA-k](https://aka.ms/acdbsla).

**Több jól definiált konzisztenciamodellekkel közül választhat.** Az Azure Cosmos DB replikációs protokoll öt jól meghatározott, és intuitív konzisztenciamodelleket kínál. Minden modell a konzisztencia és a teljesítmény magával rendelkezik. Ezek konzisztenciamodell használatával hozhat létre globálisan elosztott alkalmazások létrehozását.

## <a id="Next Steps"></a>Következő lépések

További információ a globális terjesztés, az alábbi cikkeket:

* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
* [Ügynökönkénti ügyfelek konfigurálása](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Régiók hozzáadása vagy eltávolítása az Azure Cosmos DB-fiókból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Hozzon létre egy egyéni ütközésfeloldási házirend SQL API-fiókok](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)