---
title: Hozzon létre Azure Cosmos-tárolókat és adatbázisokat robotpilóta módban.
description: Ismerje meg az előnyöket, használati eseteket, és hogyan építheti ki az Azure Cosmos-adatbázisokat és -tárolókat robotpilóta módban.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246655"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Azure Cosmos-tárolók és -adatbázisok létrehozása Autopilot módban (előzetes verzió)

Az Azure Cosmos DB segítségével manuális és Autopilot (automatikus) módban is kioszthatja a tárolók átviteli sebességét. Ez a cikk az Autopilot mód előnyeit és használati eseteit ismerteti.

> [!NOTE]
> Az Autopilot mód jelenleg nyilvános előzetes verzióban érhető el. Csak [új adatbázisokhoz és tárolókhoz engedélyezhet robotpilótát.](#create-a-database-or-a-container-with-autopilot-mode) Nem érhető el a meglévő tárolók és adatbázisok.

Az átviteli műveletek manuális kiépítése mellett mostantól konfigurálhatja az Azure Cosmos-tárolókat robotpilóta módban. A robotpilóta módban konfigurált tárolók és adatbázisok **automatikusan és azonnal skálázják a kiosztott átviteli teljesítményt az alkalmazás igényei alapján anélkül, hogy globálisan befolyásolná a számítási feladatok rendelkezésre állását, késését, átviteli képességét vagy teljesítményét.**

A tárolók és adatbázisok robotpilóta módban történő konfigurálásakor meg kell adnia a maximális átviteli értéket, `Tmax` amelyet nem szabad túllépni. A tárolók ezután méretezhetik az átviteli egyenátusukat, hogy `0.1*Tmax < T < Tmax`az . Más szóval, a tárolók és adatbázisok a számítási feladatok igényei nek megfelelően azonnal skálázhatók, a beállított maximális átviteli érték 10%-áig. A robotpilóta-adatbázis vagy`Tmax`-tároló maximális átviteli ( ) beállítását bármikor módosíthatja. A robotpilóta beállítás, a 400 RU/s minimális átviteli konténerenként vagy adatbázisonként már nem alkalmazható.

A robotpilóta előzetes verziója során a tárolón vagy az adatbázison megadott maximális átviteli teljesítmény esetén a rendszer lehetővé teszi a számított tárolási korláton belüli működést. Ha túllépi a tárolási korlátot, a maximális átviteli teljesítmény automatikusan magasabb értékre lesz módosítva. Ha az adatbázis szintű átviteli értéket robotpilóta módban használja, az adatbázisban engedélyezett tárolók száma a következőképpen kerül kiszámításra: `0.001*TMax`. Például ha 20 000 robotpilóta RU/s kiépítése, majd az adatbázis 20 tárolók.

## <a name="benefits-of-autopilot-mode"></a>A robotpilóta mód előnyei

Az Autopilot módban konfigurált Azure Cosmos-tárolók a következő előnyökkel járnak:

* **Egyszerű:** Robotpilóta módban lévő tárolók távolítsa el a kiosztott átviteli képesség (RUs) és a különböző tárolók kapacitásának manuális kezeléséhez összetettségét.

* **Méretezhető:** Robotpilóta módban lévő tárolók szükség szerint zökkenőmentesen méretezhetők a kiosztott átviteli kapacitással. Nincs fennakadás az ügyfélkapcsolatok, alkalmazások, és nem befolyásolják a meglévő SLA-k.

* **Költséghatékony:** Ha robotpilóta módban konfigurált tárolókat használ, csak az okat az erőforrásokat kell fizetnie, amelyeket a számítási feladatoknak óránként kell fizetniük.

* **Magas rendelkezésre állású:** A robotpilóta módban lévő tárolók ugyanazt a globálisan elosztott, hibatűrő, magas rendelkezésre állású háttérrendszert használják az adatok tartósságának és magas rendelkezésre állásának biztosítása érdekében.

## <a name="use-cases-of-autopilot-mode"></a>Robotpilóta-üzemmód használati esetei

A robotpilóta módban konfigurált Azure Cosmos-tárolók használati esetei a következők:

* **Változó munkaterhelések:** Ha fut egy enyhén használt alkalmazás csúcshasználat 1 óra több órát néhány alkalommal naponta, vagy évente többször. Ilyenek például az emberi erőforrásokra, a költségvetés-bekészítésre és az operatív jelentésekre vonatkozó alkalmazások. Az ilyen esetekben a robotpilóta módban konfigurált tárolók használhatók, és már nem kell manuálisan kiépíteni a csúcs- vagy átlagos kapacitást.

* **Kiszámíthatatlan munkaterhelések:** Ha olyan számítási feladatokat futtat, ahol a nap folyamán adatbázis-használat van, de a tevékenység olyan csúcsait is, amelyeket nehéz megjósolni. Egy példa egy forgalmi webhely, amely az időjárás-előrejelzés változása kor látja a tevékenység megugrását. Robotpilóta módban konfigurált tárolók állítsa be a kapacitást, hogy megfeleljen az alkalmazás csúcsterhelésének igényeinek, és csökkentse a kapacitást, amikor a tevékenység megugrása véget ér.

* **Új alkalmazások:** Ha egy új alkalmazást telepít, és nem biztos abban, hogy mennyi kiosztott átviteli -igény (azaz hány rúg) van szüksége. Robotpilóta módban konfigurált tárolókkal automatikusan skálázhatja az alkalmazás kapacitásigényeit és követelményeit.

* **Ritkán használt alkalmazások:** Ha olyan alkalmazással rendelkezik, amelyet naponta vagy héten vagy hónapon keresztül csak néhány órán keresztül használnak, például egy kis mennyiségű alkalmazás/web/blog webhely.

* **Fejlesztési és tesztelési adatbázisok:** Ha a fejlesztők munkaidőben használnak tárolókat, de éjszaka vagy hétvégén nincs rájuk szükségük. Robotpilóta módban konfigurált tárolókkal minimálisra csökkennek, ha nincsenek használatban.

* **Ütemezett éles számítási feladatok/lekérdezések:** Ha egy sor ütemezett kérelmek/műveletek/lekérdezések egyetlen tárolón, és ha vannak olyan tétlen időszakok, ahol szeretne futtatni egy abszolút alacsony átviteli sebességgel, most már megteheti, hogy könnyen. Amikor egy ütemezett lekérdezést/kérelmet egy robotpilóta módban konfigurált tárolóba küld, automatikusan felskálázódik, amennyire csak szükséges, és futtatja a műveletet.

A korábbi problémák megoldása nem csak a megvalósítássorán igényel hatalmas időt, hanem bonyolultkonfigurációt vagy kódot is bevezet, és gyakran manuális beavatkozást igényel azok kezeléséhez. Autopilot mód lehetővé teszi a fenti forgatókönyvek ki a dobozból, így nem kell aggódnia ezek a problémák többé.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Összehasonlítás – Kézi üzemmódban konfigurált tárolók és robotpilóta módban

|  | Manuális üzemmódban konfigurált tárolók  | Robotpilóta módban konfigurált tárolók |
|---------|---------|---------|
| **Kiosztott átviteli sebesség** | Manuálisan kiépítve. | Automatikusan és azonnal méretezve a számítási feladatok használati mintái alapján. |
| **A kérelmek/műveletek díjának korlátozása (429)**  | Előfordulhat, ha a felhasználás meghaladja a kiosztott kapacitást. | Nem fog megtörténni, ha a felhasznált átviteli sebesség a robotpilóta módban kiválasztott maximális átviteli sebességen belül van.   |
| **Kapacitástervezés** |  Meg kell tennie a kezdeti kapacitástervezés és a szükséges átviteli kapacitás biztosítása. |    Nem kell aggódnod a kapacitástervezés miatt. A rendszer automatikusan gondoskodik a kapacitástervezésről és a kapacitásmenedzsmentről. |
| **Díjszabás** | Manuálisan kiépített RU/s óránként. | Az egyírási régiós fiókok esetében óránként kell fizetnie a robotpilóta RU/s óránkénti díja használatával használt átviteli sebességért. <br/><br/>Több írási régióval rendelkező fiókok esetén a robotpilóta nem számít fel külön díjat. Az óránkénti átviteli sebességért ugyanazt a többfős RU/s/s óránkénti díjat használva kell fizetnie. |
| **A munkaterhelés-típusokhoz leginkább megfelelő** |  Kiszámítható és stabil munkaterhelések|   Kiszámíthatatlan és változó munkaterhelések  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Adatbázis vagy robotpilóta üzemmódú tároló létrehozása

Konfigurálhatja a robotpilóta az új adatbázisok vagy tárolók létrehozásakor őket az Azure Portalon keresztül. A következő lépésekkel hozzon létre egy új adatbázist vagy tárolót, engedélyezze a robotpilótát, és adja meg a maximális átviteli értéket (RU/s).

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) vagy az [Azure Cosmos DB-kezelőbe.](https://cosmos.azure.com/)

1. Nyissa meg az Azure Cosmos DB-fiókját, és nyissa meg az **Adatkezelő** lapot.

1. Válassza az **Új tároló lehetőséget.** Adja meg az adatbázis, a tároló és a partíciókulcs nevét. Az **Átviteli teljesítmény**csoportban válassza az **Autopilot** beállítást, és válassza ki azt a maximális átviteli értéket (RU/s), amelyet az adatbázis vagy a tároló nem léphet meg a robotpilóta beállítás használatakor.

   ![Tároló létrehozása és Az Autopilot átviteli igényének konfigurálása](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Válassza **az OK gombot.**

Hozzon létre egy megosztott átviteli adatbázis robotpilóta módban a **kiépítési adatbázis átviteli beállítás** kiválasztásával.

## <a name="throughput-and-storage-limits-for-autopilot"></a><a id="autopilot-limits"></a>Átviteli és tárolási korlátok robotpilótához

Az alábbi táblázat a robotpilóta módban a különböző beállítások maximális és tárolási korlátait mutatja be:

|Maximális átviteli korlát  |Maximális tárolási korlát  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20 000 RU/s  |  200 GB  |
|100 000 RU/s    |  1 TB   |
|500 000 RU/s    |  5 TB  |

## <a name="next-steps"></a>További lépések

* Tekintse át a [robotpilóta GYIK](autopilot-faq.md).
* További információ a [logikai partíciókról.](partition-data.md)
* Ismerje meg, hogyan [építheti ki az átviteli fazonát egy Azure Cosmos-tárolóban.](how-to-provision-container-throughput.md)
* Ismerje meg, hogyan [építheti ki az átviteli mertét egy Azure Cosmos-adatbázisban.](how-to-provision-database-throughput.md)
