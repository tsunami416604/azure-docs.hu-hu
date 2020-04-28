---
title: Azure Cosmos-tárolók és-adatbázisok létrehozása az autoskálázási módban.
description: Ismerje meg az előnyeit, használati eseteit, valamint az Azure Cosmos-adatbázisok és-tárolók kiépítését az autoscale módban.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 49fea2cc9a48d5afc794d6932208b61252bea424
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196490"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autoscale-mode"></a>Azure Cosmos-tárolók és-adatbázisok létrehozása az autoskálázási módban

A Azure Cosmos DB lehetővé teszi, hogy a tárolókban manuális vagy automatikus skálázási módban is kiépítse az átviteli sebességet. Ez a cikk az autoskálázási mód előnyeit és használati eseteit ismerteti.

> [!NOTE]
> A csak [Új adatbázisok és tárolók esetében engedélyezhető az autoskálázás](#create-db-container-autoscale) . A meglévő tárolók és adatbázisok esetében nem érhető el.

Az átviteli sebesség manuális kiosztása mellett mostantól konfigurálhatja az Azure Cosmos-tárolókat az automatikus skálázási módban is. Az automatikus skálázási módban konfigurált tárolók és adatbázisok **automatikusan és azonnal méretezhetik az alkalmazás által igényelt kiépített átviteli sebességet anélkül, hogy ez befolyásolná a munkaterhelés rendelkezésre állását, késését, átviteli sebességét vagy teljesítményét globálisan.**

A tárolók és adatbázisok autoskálázási módban való konfigurálásakor meg kell adnia a maximális `Tmax` átviteli sebességet, amely nem haladja meg a korlátot. A tárolók ezután méretezhetik az átviteli `0.1*Tmax < T < Tmax`sebességet. Más szóval, a tárolók és az adatbázisok a számítási feladatok igénye alapján azonnal méretezhetők, a maximális átviteli sebesség értékének 10%-ában, amelyet a beállított maximális átviteli sebességre konfiguráltak. Az autoskálázás konfigurálása után bármikor módosíthatja egy adatbázis vagy tároló maximális`Tmax`átviteli sebességét (). Az autoscale kapcsolóval az 400 RU/s minimális átviteli sebesség a tároló vagy az adatbázis esetében már nem alkalmazható.

A tárolón vagy az adatbázison megadott maximális átviteli sebesség esetében a rendszer a számított tárolási korláton belül lehetővé teszi a működést. Ha túllépi a tárolási korlátot, a maximális átviteli sebesség automatikusan magasabb értékre van igazítva. Ha az adatbázis-szint átviteli sebességét az autoskálázási móddal használja, az adatbázisban engedélyezett tárolók számát a `0.001*TMax`következőképpen számítjuk ki:. Ha például kiépíti az 20 000-es autoscale RU/s-t, akkor az adatbázis 20 tárolóval rendelkezhet.

## <a name="benefits-of-autoscale-mode"></a><a id="autoscale-benefits"></a>Az autoskálázási mód előnyei

Az autoskálázási módban konfigurált Azure Cosmos-tárolók a következő előnyöket nyújtják:

* **Egyszerű:** Az autoskálázási módban lévő tárolók megszüntetik az összetettséget a kiépített átviteli sebesség (RUs) és a kapacitás manuális kezeléséhez a különböző tárolók esetében.

* **Skálázható:** Az autoscale módban lévő tárolók zökkenőmentesen méretezhetik a kiosztott átviteli kapacitást igény szerint. Az ügyfélkapcsolatok, az alkalmazások és a meglévő SLA-kat nem érintik.

* **Költséghatékony:** Ha autoskálázási módban konfigurált tárolókat használ, csak azokat az erőforrásokat kell fizetnie, amelyeket a számítási feladatoknak óránként kell használniuk.

* **Magasan elérhető:** Az autoscale módban lévő tárolók ugyanazt a globálisan elosztott, hibatűrő, magas rendelkezésre állású hátteret használják az adattartósság és a magas rendelkezésre állás biztosítása érdekében.

## <a name="use-cases-of-autoscale-mode"></a><a id="autoscale-usecases"></a>Az autoskálázási mód használata

Az Azure Cosmos-tárolók az autoskálázási módban konfigurált használati esetei a következők:

* **Változó számítási feladatok:** Ha egy könnyű használatú alkalmazást futtat, és a maximális kihasználtság 1 óra, akkor naponta többször vagy évente többször is elvégezheti az órák számát. Ilyenek például az emberi erőforrások, a költségvetések és az operatív jelentéskészítési alkalmazások. Ilyen esetekben az autoskálázási módban konfigurált tárolók használhatók, és már nem kell manuálisan kiépíteni a csúcs-vagy az átlagos kapacitást.

* **Előre nem látható számítási feladatok:** Ha olyan munkaterheléseket futtat, amelyeken a nap folyamán adatbázis-használat van, de a tevékenységek is nehezen megbecsülhető. Ilyen például egy olyan forgalmi hely, amely az időjárás-előrejelzés változásakor meghaladó aktivitást lát. Az automatikus skálázási módban konfigurált tárolók úgy módosítják a kapacitást, hogy megfeleljenek az alkalmazás maximális terhelésének, és a leskálázást a tevékenységek túllépése esetén.

* **Új alkalmazások:** Ha új alkalmazást telepít, és nem biztos benne, hogy mekkora mennyiségű kiosztott átviteli sebességre van szükség. Az automatikus skálázási módban konfigurált tárolók automatikusan méretezhetők az alkalmazás kapacitási igényeire és követelményeire.

* **Ritkán használt alkalmazások:** Ha olyan alkalmazást használ, amely naponta, hetente vagy havonta többször is használatban van, például egy kis mennyiségű alkalmazás/Web/Blog webhelyen.

* **Fejlesztési és tesztelési adatbázisok:** Ha a fejlesztők a munkaidőben tárolókat használnak, de nem szükségesek éjszakára vagy hétvégére. Az autoskálázási módban konfigurált tárolók esetén a rendszer a használaton kívüli minimálisra kicsinyíti le őket.

* **Ütemezett üzemi munkaterhelések/lekérdezések:** Ha egy adott tárolón ütemezett kérelmek/műveletek/lekérdezések sorozata van, és ha vannak olyan tétlen időszakok, amikor egy abszolút alacsony átviteli sebességen szeretne futni, mostantól könnyedén elvégezhető. Ha egy ütemezett lekérdezés/kérelem egy automatikus skálázási módban konfigurált tárolóhoz van elküldve, a rendszer a szükséges mértékben automatikusan felskálázást végez, és futtatja a műveletet.

Az előző problémák megoldásához nem csupán nagy mennyiségű időt kell igénybe venni a megvalósításban, de összetettséget is bevezetnek a konfigurációban vagy a kódban, és gyakran kézi beavatkozásra van szükségük a megoldáshoz. Az autoskálázási mód lehetővé teszi, hogy a fenti forgatókönyvek a dobozból legyenek, így többé nem kell aggódnia ezekkel a problémákról.

## <a name="comparison--containers-configured-in-manual-mode-vs-autoscale-mode"></a>Összehasonlítás – kézi üzemmódban konfigurált tárolók és automatikus skálázási mód

|  | Manuális módban konfigurált tárolók  | Az autoskálázási módban konfigurált tárolók |
|---------|---------|---------|
| **Kiosztott átviteli sebesség** | Manuálisan kiépítve. | Automatikusan és azonnal méretezhető a munkaterhelés-használati minták alapján. |
| **Kérelmek/műveletek korlátozása (429)**  | Előfordulhat, hogy a felhasználás meghaladja a kiosztott kapacitást. | Nem történik meg, ha a felhasznált átviteli sebesség az automatikusan kihasználható maximális átviteli sebességen belül van.   |
| **Kapacitástervezés** |  Meg kell tennie a kezdeti kapacitás megtervezését és a szükséges átviteli sebesség kiépítését. |    Nem kell aggódnia a kapacitás megtervezése miatt. A rendszer automatikusan gondoskodik a kapacitás megtervezéséről és a kapacitások kezeléséről. |
| **Díjszabás** | Manuálisan kiépített RU/s óránként. | Az egyszeri írási régió fiókjai esetében az óránkénti átviteli sebesség (RU/s) óránkénti díjszabása alapján kell fizetnie. <br/><br/>Több írási régióval rendelkező fiókok esetében az autoskálázás díjmentes. Az óránkénti átviteli sebességért kell fizetnie, ugyanazzal a több főkiszolgálós RU/s-díj használatával. |
| **Legmegfelelőbb a számítási feladatok típusaihoz** |  Kiszámítható és stabil számítási feladatok|   Kiszámíthatatlan és változó számítási feladatok  |

## <a name="create-a-database-or-a-container-with-autoscale-mode"></a><a id="create-db-container-autoscale"></a>Adatbázis vagy tároló létrehozása az autoscale módban

Az új adatbázisok vagy tárolók számára az Azure Portal használatával konfigurálhatja az autoskálázást. A következő lépésekkel hozzon létre egy új adatbázist vagy tárolót, engedélyezze az autoskálázást, és határozza meg a maximális átviteli sebességet (RU/s).

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) vagy a [Azure Cosmos db Explorerben.](https://cosmos.azure.com/)

1. Navigáljon a Azure Cosmos DB-fiókjához, és nyissa meg a **adatkezelő** lapot.

1. Válassza az **új tároló elemet.** Adja meg az adatbázis, a tároló és a partíciós kulcs nevét. Az **átviteli sebesség**területen válassza az **autoskálázás** lehetőséget, és válassza ki azt a maximális átviteli sebességet (ru/s), amelyet az adatbázis vagy a tároló nem léphet túl az autoskálázási beállítás használatakor.

   ![Tároló létrehozása és az autoskálázási teljesítmény konfigurálása](./media/provision-throughput-autoscale/create-container-autoscale-mode.png)

1. Kattintson az **OK** gombra.

Az **adatbázis-átviteli sebesség** kiosztása lehetőség kiválasztásával létrehozhat egy megosztott átviteli sebességű adatbázist az autoskálázási móddal.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>Átviteli sebesség és tárolási korlátok az autoskálázáshoz

Az alábbi táblázat a maximális és a tárolási korlátot mutatja be az autoskálázási módban lévő különböző beállítások esetében:

|Maximális átviteli sebesség korlátja  |Maximális tárolási korlát  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20 000 RU/s  |  200 GB  |
|100 000 RU/s    |  1 TB   |
|500 000 RU/s    |  5 TB  |

## <a name="next-steps"></a>További lépések

* Tekintse át az [autoscale – gyakori kérdések](autoscale-faq.md)című szakaszt.
* További információ a [logikai partíciókhoz](partition-data.md).
* Útmutató az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiépítéséhez.
* Útmutató az [átviteli sebesség Azure Cosmos-adatbázison](how-to-provision-database-throughput.md)való kiépítéséhez.
