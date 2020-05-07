---
title: Azure Cosmos-tárolók és-adatbázisok létrehozása az autoscale kiépített átviteli sebességében.
description: Ismerje meg az előnyöket, a használati eseteket, valamint az Azure Cosmos-adatbázisok és-tárolók üzembe helyezését az autoscale kiépített átviteli sebességben.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 81a13dcb7955a7d46f485416bf9b7e4e7be4d9ac
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791714"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-provisioned-throughput"></a>Azure Cosmos-tárolók és-adatbázisok létrehozása az autoscale kiépített átviteli sebességgel

Azure Cosmos DB lehetővé teszi a tárolók konfigurálását a standard (manuális) kiosztott átviteli sebesség vagy az automatikus skálázási átviteli sebesség használatával. Ez a cikk az autoscale előnyeit és használati eseteit ismerteti.

> [!NOTE]
> A csak [Új adatbázisok és tárolók esetében engedélyezhető az autoskálázás](#create-db-container-autoscale) . A meglévő tárolók és adatbázisok esetében nem érhető el.

Az átviteli sebesség szabványos kiosztása mellett mostantól az Azure Cosmos-tárolókat is konfigurálhatja az automatikusan kiosztott átviteli sebességgel. Az Automatikus méretezéssel kiépített átviteli sebességben konfigurált tárolók és adatbázisok **automatikusan és azonnal méretezhetővé teszik az alkalmazás által igényelt kiépített átviteli sebességet anélkül, hogy ez befolyásolná a számítási feladat rendelkezésre állását, késését, átviteli sebességét vagy teljesítményét.**

Ha a tárolókat és az adatbázisokat az autoscale-ben konfigurálja, meg `Tmax` kell adnia a maximális átviteli sebességet, amely nem haladja meg a korlátot. A tárolók ezután méretezhetik az átviteli `0.1*Tmax < T < Tmax`sebességet. Más szóval, a tárolók és az adatbázisok a számítási feladatok igénye alapján azonnal méretezhetők, a maximális átviteli sebesség értékének 10%-ában, amelyet a beállított maximális átviteli sebességre konfiguráltak. Az autoskálázás konfigurálása után bármikor módosíthatja egy adatbázis vagy tároló maximális`Tmax`átviteli sebességét (). Az autoscale kapcsolóval az 400 RU/s minimális átviteli sebesség a tároló vagy az adatbázis esetében már nem alkalmazható.

A tárolón vagy az adatbázison megadott maximális átviteli sebesség esetében a rendszer a számított tárolási korláton belül lehetővé teszi a működést. Ha túllépi a tárolási korlátot, a maximális átviteli sebesség automatikusan magasabb értékre van igazítva. Ha az adatbázis-szint átviteli sebességét az autoskálázással használja, az adatbázisban engedélyezett tárolók számát a `0.001*TMax`következőképpen számítjuk ki:. Ha például kiépíti az 20 000-es autoscale RU/s-t, akkor az adatbázis 20 tárolóval rendelkezhet.

## <a name="benefits-of-autoscale-provisioned-throughput"></a><a id="autoscale-benefits"></a>Az autoscale kiosztott átviteli sebességének előnyei

Az autoscale-sel konfigurált Azure Cosmos-tárolók a következő előnyöket nyújtják:

* **Egyszerű:** A tárolók az autoscale használatával eltávolítják a bonyolultságot a kiépített átviteli sebesség (RUs) és a kapacitás manuális kezeléséhez a különböző tárolók esetében.

* **Skálázható:** A tárolók és az autoscale zökkenőmentesen méretezhető a kiosztott átviteli kapacitás igény szerint. Az ügyfélkapcsolatok, az alkalmazások és a meglévő SLA-kat nem érintik.

* **Költséghatékony:** Ha az autoscale-sel konfigurált tárolókat használ, csak azokat az erőforrásokat kell fizetnie, amelyeket a számítási feladatoknak óránként kell használniuk.

* **Magasan elérhető:** A tárolók az autoscale használatával ugyanazt a globálisan elosztott, hibatűrő, magas rendelkezésre állású hátteret használják az adattartósság és a magas rendelkezésre állás biztosítása érdekében.

## <a name="use-cases-of-autoscale-provisioned-throughput"></a><a id="autoscale-usecases"></a>Az autoscale kiépített átviteli sebesség használatának esetei

Az olyan Azure Cosmos-tárolók használati esetei, amelyek az autoscale használatával vannak konfigurálva:

* **Változó számítási feladatok:** Ha egy könnyű használatú alkalmazást futtat, és a maximális kihasználtság 1 óra, akkor naponta többször vagy évente többször is elvégezheti az órák számát. Ilyenek például az emberi erőforrások, a költségvetések és az operatív jelentéskészítési alkalmazások. Ilyen esetekben az autoscale használatával konfigurált tárolók használhatók, és a továbbiakban nem kell manuálisan kiépíteni a maximális vagy az átlagos kapacitást.

* **Előre nem látható számítási feladatok:** Ha olyan munkaterheléseket futtat, amelyeken a nap folyamán adatbázis-használat van, de a tevékenységek is nehezen megbecsülhető. Ilyen például egy olyan forgalmi hely, amely az időjárás-előrejelzés változásakor meghaladó aktivitást lát. Az automatikus skálázással konfigurált tárolók úgy módosítják a kapacitást, hogy megfeleljenek az alkalmazás maximális terhelésének, és a leskálázás a tevékenység túllépésének megnövekedésével.

* **Új alkalmazások:** Ha új alkalmazást telepít, és nem biztos benne, hogy mekkora mennyiségű kiosztott átviteli sebességre van szükség. Az automatikus skálázással konfigurált tárolók automatikusan méretezhetők az alkalmazás kapacitási igényeire és követelményeire.

* **Ritkán használt alkalmazások:** Ha olyan alkalmazást használ, amely naponta, hetente vagy havonta többször is használatban van, például egy kis mennyiségű alkalmazás/Web/Blog webhelyen.

* **Fejlesztési és tesztelési adatbázisok:** Ha a fejlesztők a munkaidőben tárolókat használnak, de nem szükségesek éjszakára vagy hétvégére. Az autoscale használatával konfigurált tárolókkal a rendszer a használaton kívüli minimumra kicsinyíti le őket.

* **Ütemezett üzemi munkaterhelések/lekérdezések:** Ha egy adott tárolón ütemezett kérelmek/műveletek/lekérdezések sorozata van, és ha vannak olyan tétlen időszakok, amikor egy abszolút alacsony átviteli sebességen szeretne futni, mostantól könnyedén elvégezhető. Ha egy ütemezett lekérdezés/kérelem automatikus skálázással konfigurált tárolóhoz van elküldve, a rendszer a szükséges mértékben automatikusan felskálázást végez, és futtatja a műveletet.

Az előző problémák megoldásához nem csupán nagy mennyiségű időt kell igénybe venni a megvalósításban, de összetettséget is bevezetnek a konfigurációban vagy a kódban, és gyakran kézi beavatkozásra van szükségük a megoldáshoz. Az autoscale funkció lehetővé teszi a fenti forgatókönyvek használatát, így többé nem kell aggódnia ezekkel a problémákról.

## <a name="comparison--standard-manual-vs-autoscale-provisioned-throughput"></a>Összehasonlítás – standard (manuális) és automatikus méretezés kiépített átviteli sebessége

|  | Standard kiosztott átviteli sebességgel konfigurált tárolók  | Az autoscale kiépített átviteli sebességgel konfigurált tárolók |
|---------|---------|---------|
| **Kiosztott átviteli sebesség** | Manuálisan kiépítve. | Automatikusan és azonnal méretezhető a munkaterhelés-használati minták alapján. |
| **Kérelmek/műveletek korlátozása (429)**  | Előfordulhat, hogy a felhasználás meghaladja a kiosztott kapacitást. | Nem történik meg, ha a felhasznált átviteli sebesség az automatikusan kiválasztott maximális átviteli sebességen belül van.   |
| **Kapacitástervezés** |  Meg kell tennie a kezdeti kapacitás megtervezését és a szükséges átviteli sebesség kiépítését. |    Nem kell aggódnia a kapacitás megtervezése miatt. A rendszer automatikusan gondoskodik a kapacitás megtervezéséről és a kapacitások kezeléséről. |
| **Díjszabás** | Manuálisan kiépített RU/s óránként. | Az egyszeri írási régió fiókjai esetében az óránkénti átviteli sebesség (RU/s) óránkénti díjszabása alapján kell fizetnie. <br/><br/>Több írási régióval rendelkező fiókok esetében az autoskálázás díjmentes. Az óránkénti átviteli sebességért kell fizetnie, ugyanazzal a több főkiszolgálós RU/s-díj használatával. |
| **Legmegfelelőbb a számítási feladatok típusaihoz** |  Kiszámítható és stabil számítási feladatok|   Kiszámíthatatlan és változó számítási feladatok  |

## <a name="create-a-database-or-a-container-with-autoscale"></a><a id="create-db-container-autoscale"></a>Adatbázis vagy tároló létrehozása az autoscale paranccsal

Az új adatbázisok vagy tárolók számára az Azure Portal használatával konfigurálhatja az autoskálázást. A következő lépésekkel hozzon létre egy új adatbázist vagy tárolót, engedélyezze az autoskálázást, és határozza meg a maximális átviteli sebességet (RU/s).

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) vagy a [Azure Cosmos db Explorerben.](https://cosmos.azure.com/)

1. Navigáljon a Azure Cosmos DB-fiókjához, és nyissa meg a **adatkezelő** lapot.

1. Válassza az **új tároló elemet.** Adja meg az adatbázis, a tároló és a partíciós kulcs nevét. Az **átviteli sebesség**területen válassza az **autoskálázás** lehetőséget, és válassza ki azt a maximális átviteli sebességet (ru/s), amelyet az adatbázis vagy a tároló nem léphet túl az autoskálázási beállítás használatakor.

   ![Tároló létrehozása és az autoskálázási teljesítmény konfigurálása](./media/provision-throughput-autoscale/create-container-autoscale-mode.png)

1. Kattintson az **OK** gombra.

Az **adatbázis-átviteli sebesség** kiosztása lehetőség kiválasztásával létrehozhat egy megosztott átviteli sebességű adatbázist az autoscale használatával.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>Átviteli sebesség és tárolási korlátok az autoskálázáshoz

Az alábbi táblázat az autoskálázás különböző lehetőségeinek maximális teljes és tárolási korlátait mutatja be:

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
