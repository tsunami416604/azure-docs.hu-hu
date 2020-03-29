---
title: Adatok globális terjesztése az Azure Cosmos DB-vel
description: Ismerje meg a bolygóméretű georeplikációt, a többfőkiszolgálós, a feladatátvételt és az adathelyreállítást az Azure Cosmos DB globális adatbázisaival, amely egy globálisan elosztott, többmodelles adatbázis-szolgáltatás.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 84cd201cd758293082a61a87528332c7d8c58811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264305"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Globális adatterjesztés az Azure Cosmos DB-vel – áttekintés

A mai alkalmazásoknak rendkívül érzékenynek és mindig online kell lenniük. Az alacsony késés és a magas rendelkezésre állás elérése érdekében ezen alkalmazások példányait olyan adatközpontokban kell telepíteni, amelyek közel állnak a felhasználókhoz. Ezek az alkalmazások általában több adatközpontban vannak telepítve, és globálisan elosztottnak nevezzük őket. A globálisan elosztott alkalmazásoknak globálisan elosztott adatbázisra van szükségük, amely transzparens módon tudja replikálni az adatokat a világ bármely pontján, hogy az alkalmazások a felhasználóikhoz közel lévő adatpéldányt használhassák. 

Az Azure Cosmos DB egy globálisan elosztott adatbázis-szolgáltatás, amelyet úgy terveztek, hogy alacsony késést, az átviteli feszültség rugalmas méretezhetőségét, az adatok konzisztenciáját és a magas rendelkezésre állást biztosító jól meghatározott szemantika. Röviden, ha az alkalmazásnak garantált gyors válaszidőre van szüksége a világ bármely pontján, ha mindig online állapotban kell lennie, és korlátlan és rugalmas átviteli sebességre és tárhelyre van szüksége, az alkalmazást az Azure Cosmos DB-re kell építenie.

Beállíthatja, hogy az adatbázisok globálisan terjeszthetőés elérhető bármely Azure-régióban. A késés csökkentéséhez helyezze az adatokat a felhasználók tartózkodási helyéhez. A szükséges régiók kiválasztása az alkalmazás globális elérésétől és a felhasználók tartózkodási helyétől függ. Cosmos DB transzparens módon replikálja az adatokat a Cosmos-fiókhoz társított összes régióban. A globálisan elosztott Azure Cosmos-adatbázis és tárolók egyetlen rendszerképét biztosítja, amelyeket az alkalmazás helyileg tud olvasni és írni. 

Az Azure Cosmos DB segítségével bármikor hozzáadhatja vagy eltávolíthatja a fiókjához társított régiókat. Az alkalmazás nem kell szüneteltetni, vagy újratelepíteni egy régió hozzáadása vagy eltávolítása. Továbbra is mindig magas rendelkezésre állású, mert a szolgáltatás natívan nyújt többhelyű képességek miatt.

![Magas rendelkezésre állású telepítési topológia](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>A globális elosztás legfontosabb előnyei

**Globális aktív alkalmazások at hozhat létre.** Az új többfős replikációs protokolljával minden régió támogatja az írásokat és az olvasásokat. A többfős képesség lehetővé teszi:

- Korlátlan rugalmas írási és olvasási méretezhetőség. 
- 99.999% olvasási és írási elérhetőség a világ minden tájáról.
- Garantált olvasási és írási kézbesítési szolgált kevesebb, mint 10 ezredmásodperc alatt a 99 percentilis.

Az Azure Cosmos DB több-homing API-k használatával az alkalmazás tisztában van a legközelebbi régióban, és az adott régióba küldhet kéréseket. A legközelebbi régió konfigurációs módosítások nélkül lesz azonosítva. Az Azure Cosmos-fiókhoz való régiók hozzáadása és eltávolítása során az alkalmazást nem kell újratelepíteni vagy szüneteltetni, továbbra is mindig magas rendelkezésre állású.

**Rendkívül gyorsan reagáló alkalmazásokat hozhat létre.** Az alkalmazás közel valós idejű olvasási és írási műveleteket hajthat végre az adatbázishoz kiválasztott összes régión. Az Azure Cosmos DB belsőleg kezeli az adatok replikációját a régiók között a kiválasztott szint konzisztenciaszintű garanciáival.

**Magas rendelkezésre állású alkalmazásokat hozhat létre.** Az adatbázisok világszerte több régióban történő futtatása növeli az adatbázis rendelkezésre állását. Ha egy régió nem érhető el, más régiók automatikusan kezelik az alkalmazáskérelmeket. Az Azure Cosmos DB 99,999%-os olvasási és írási rendelkezésre állást kínál többrégiós adatbázisokhoz.

**A regionális leállások során tartsa fenn az üzletmenet folytonosságát.** Az Azure Cosmos DB támogatja az [automatikus feladatátvételt](how-to-manage-database-account.md#automatic-failover) egy regionális kimaradás során. Egy regionális kimaradás során az Azure Cosmos DB továbbra is fenntartja a késés, a rendelkezésre állás, a konzisztencia és az átviteli átviteli SlA-k. Annak érdekében, hogy a teljes alkalmazás magas rendelkezésre állású, cosmos DB kínál manuális feladatátvételi API-t szimulálni egy regionális kimaradás. Ezzel az API-val rendszeres üzletmenet-folytonossági gyakorlatokat végezhet.

**Globálisan méretezhetők az olvasási és írási átviteli.** Engedélyezheti, hogy minden régió írható és rugalmasan méretezhető olvasási és írási műveletek a világ minden tájáról. Az alkalmazás által konfigurált átviteli egy Azure Cosmos-adatbázis vagy egy tároló garantáltan az Azure Cosmos-fiókhoz társított összes régióban lesz kézbesítve. A kiosztott átviteli átbocsátást [pénzügyileg támogatott SLA-k garantálják](https://aka.ms/acdbsla).

**Számos jól definiált konzisztenciamodell közül választhat.** Az Azure Cosmos DB replikációs protokoll öt jól definiált, praktikus és intuitív konzisztenciamodellt kínál. Minden modell rendelkezik egy kompromisszumot a konzisztencia és a teljesítmény között. Ezekkel a konzisztenciamodellekkel könnyedén hozhat létre globálisan elosztott alkalmazásokat.

## <a name="next-steps"></a><a id="Next Steps"></a>További lépések

Tudjon meg többet a globális forgalmazásról az alábbi cikkekben:

* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
* [Többfőkiszolgáló konfigurálása az alkalmazásokban](how-to-multi-master.md)
* [Ügyfelek konfigurálása többhomoláshoz](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Régiók hozzáadása vagy eltávolítása az Azure Cosmos DB-fiókjából](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Egyéni ütközésfeloldási házirend létrehozása SQL API-fiókokhoz](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programozható konzisztenciamodellek a Cosmos DB-ben](consistency-levels.md)
* [Válassza ki az alkalmazásnak megfelelő konzisztenciaszintet](consistency-levels-choosing.md)
* [Konzisztenciaszintek az Azure Cosmos DB API-k között](consistency-levels-across-apis.md)
* [Rendelkezésre állási és teljesítménykompromisszumok a különböző konzisztenciaszintekhez](consistency-levels-tradeoffs.md)

