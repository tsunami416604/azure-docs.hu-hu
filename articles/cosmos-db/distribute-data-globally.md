---
title: Globális adatterjesztés az Azure Cosmos DB-hez
description: Ismerje meg a globális georeplikáció, több főkiszolgálós, feladatátvétel és data recovery global Database-adatbázisok az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás használatával.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 70ead36e20861026e08e864f438071948c526844
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294416"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Globális terjesztés, az Azure Cosmos DB - áttekintés

A napjaink alkalmazásai válaszidejű, és mindig online szükségesek. Alacsony késéssel és magas rendelkezésre állás elérése érdekében ezeket az alkalmazásokat példányát kell üzembe helyezni a felhasználók közelében lévő adatközpontokban. Ezek az alkalmazások általában több adatközpontban telepített, és globálisan elosztott nevezzük. Globálisan elosztott alkalmazások egy globálisan elosztott adatbázis, amely átláthatóan képes replikálni az adatokat az alkalmazások az adatokat, amely a felhasználók közel van a művelethez használandó a világ bármely pontján található kell. 

Az Azure Cosmos DB egy globálisan elosztott adatbázis-szolgáltatás, amely közel valós idejű, rugalmasan méretezhető az átviteli sebesség, jól definiált szemantikát nyújt, és a magas rendelkezésre állású tervezték, hogy a. Ha az alkalmazás kell gyors garantált válaszidő bárhol a világon, ha ez szükséges, hogy mindig online, és szüksége van az átviteli sebesség és tárterület korlátlan és rugalmas méretezhetőséget, röviden, kell az Azure Cosmos DB-alkalmazás létrehozása.

Beállíthatja, hogy az adatbázisok, globálisan elosztott, és elérhető legyen bármelyik Azure-régiót. Csökkentheti a késést, helyezze el az adatok hol találhatók a felhasználók közel. A szükséges régiók kiválasztása attól függ, hogy a globális jelenlétét, az alkalmazás- és hol találhatók a felhasználók számára. A cosmos DB transzparensen replikálja az adatokat a Cosmos-fiókjához társított összes régióba. A globálisan elosztott Azure Cosmos-adatbázis és a tárolók, amelyek az alkalmazás olvashat és írhat a helyi egyetlen rendszerképet biztosít. 

Az Azure Cosmos DB adja hozzá, vagy távolítsa el a bármikor a fiókjához társított régiók. Az alkalmazás szüneteltetve vagy újratelepítése hozzáadni vagy eltávolítani egy régió nem szükséges. Továbbra is magas rendelkezésre állású folyamatosan miatt, amely a szolgáltatást natív módon biztosít a többkiszolgálós képességekkel.

![Magas rendelkezésre állású telepítés topológiája](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Fő előnyei a globális terjesztés

**Globális aktív-aktív alkalmazásokat készíthet.** Az új több főkiszolgálós replikációs protokoll minden régióban írások és olvasások is támogatja. A több főkiszolgálós funkció is lehetővé teszi:

- Korlátlan számú rugalmas írhat, és olvassa el a méretezhetőséget. 
- 99,999 %-os írási és olvasási a világ minden tájáról.
- Garantált olvasási és írási 99 százalékon kevesebb mint 10 ezredmásodperc szolgálja ki.

Használatával az Azure Cosmos DB többkiszolgálós API-k, az alkalmazás kompatibilis a legközelebbi régió és kéréseket küldhet adott régióban. A legközelebbi régió azonosított konfigurációs módosítások nélkül. Adjon hozzá vagy távolít el a régióban, és az Azure Cosmos-fiókjából, az alkalmazás nem kell helyeznie, vagy fel van függesztve, továbbra is fennáll, mindig rendelkezésre állású legyen.

**Reagáló alkalmazásokat készíthet.** Az alkalmazás végezhet közel valós idejű olvasási és az adatbázis kiválasztott minden régióban írására. Az Azure Cosmos DB belsőleg kezeli az adatreplikációt a kiválasztott szint szintű biztosítaná a régiók között.

**Magas rendelkezésre állású alkalmazásokat készíthet.** A rendelkezésre állási adatbázis egy adatbázis világszerte több régióban működő nő. Ha egy régió elérhetetlenné válik, más régiókban automatikusan kezelik az alkalmazásokra irányuló kérések. Az Azure Cosmos DB kínál 99,999 %-os írási és olvasási többrégiós adatbázisok rendelkezésre állását.

**Üzletmenet-folytonossági karbantartása során a regionális üzemkimaradások utáni helyreállításon.** Az Azure Cosmos DB támogatja a [automatikus feladatátvétel](how-to-manage-database-account.md#automatic-failover) egy regionális kimaradás során. Egy regionális kimaradás során az Azure Cosmos DB SLA-k a késés, a rendelkezésre állási, a konzisztencia és az átviteli sebesség fenntartása érdekében továbbra is. Annak érdekében, ügyeljen arra, hogy a teljes alkalmazás magas rendelkezésre állású, a Cosmos DB kínál a manuális feladatátvételt egy regionális kimaradás szimulálásához API. Ez az API használatával is végrehajthasson rendszeres üzleti folytonossági gyakorlatokat.

**Méretezés és globálisan olvasási átviteli sebességet.** Minden régióhoz, írható és olvasási és írási rugalmasan skálázhatja a világ minden tájáról engedélyezheti. Az átviteli sebességet, amely az alkalmazás egy Azure Cosmos-adatbázis vagy egy tároló konfigurálja garantáltan kézbesítéséhez az Azure Cosmos-fiókhoz társított összes régióban. A kiosztott átviteli sebesség által van garantált [pénzügyileg támogatott SLA-k](https://aka.ms/acdbsla).

**Több jól definiált konzisztenciamodellekkel közül választhat.** Az Azure Cosmos DB replikációs protokoll öt jól meghatározott, és intuitív konzisztenciamodelleket kínál. Minden modell a konzisztencia és a teljesítmény magával rendelkezik. Ezek konzisztenciamodell használatával hozhat létre globálisan elosztott alkalmazások létrehozását.

## <a id="Next Steps"></a>Következő lépések

További információ a globális terjesztés, az alábbi cikkeket:

* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
* [Az alkalmazások több főkiszolgálós konfigurálása](how-to-multi-master.md)
* [Ügynökönkénti ügyfelek konfigurálása](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Régiók hozzáadása vagy eltávolítása az Azure Cosmos DB-fiókból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Hozzon létre egy egyéni ütközésfeloldási házirend SQL API-fiókok](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)