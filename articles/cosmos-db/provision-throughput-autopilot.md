---
title: Hozzon létre Azure Cosmos-tárolókat és-adatbázisokat az átviteli sebességgel az Autopilot módban.
description: Ismerje meg az előnyöket, használati eseteket, valamint az Azure Cosmos-adatbázisok és-tárolók üzembe helyezését Autopilot módban.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 598dc6394e8be8b3372f4ed61a522454830a22d6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512274"
---
# <a name="create-azure-cosmos-containers-and-databases-with-provisioned-throughput-in-autopilot-mode-preview"></a>Azure Cosmos-tárolók és-adatbázisok létrehozása a kiosztott átviteli sebességgel az Autopilot módban (előzetes verzió)

Azure Cosmos DB lehetővé teszi az átviteli sebesség manuális vagy Autopilot módban való kiépítését a tárolókban. Ez a cikk az Autopilot mód előnyeit és használati eseteit ismerteti.

> [!NOTE]
> Az Autopilot mód jelenleg nyilvános előzetes verzióban érhető el.

Az átviteli sebesség manuális kiépítés mellett mostantól az Azure Cosmos-tárolókat is konfigurálhatja Autopilot módban. Az Autopilot módban konfigurált Azure Cosmos-tárolók és-adatbázisok **automatikusan és azonnal méretezhetik a kiépített átviteli sebességet az alkalmazás igényeinek megfelelően anélkül, hogy veszélyeztetné a SLA-kat.**

Többé nem kell manuálisan kezelnie a kiépített átviteli sebességet vagy a kezelői sebesség korlátozásával kapcsolatos problémákat. Az Autopilot módban konfigurált Azure Cosmos-tárolók azonnal méretezhetők a munkaterhelésre adott válasz nélkül, anélkül, hogy ez hatással lenne a munkaterhelés rendelkezésre állására, késésére, átviteli sebességére vagy teljesítményére. A magas kihasználtság alatt az Autopilot módban konfigurált Azure Cosmos-tárolók a folyamatban lévő műveletek befolyásolása nélkül méretezhetők vagy leállíthatók.

A tárolók és adatbázisok robotpilóta-módban való konfigurálásakor meg kell adnia a maximális átviteli sebességet, `Tmax` nem lehet túllépni. A tárolók ezután azonnal méretezhetők a számítási feladatok igénye alapján a `0.1*Tmax < T < Tmax` tartományon belül. Más szóval a tárolók és az adatbázisok a munkaterhelési igényeknek megfelelően méretezhetők, a beállított átviteli sebességtől számított 10%-os értéktől egészen a megadott maximális beállított értékig. Az Autopilot-adatbázison vagy a tárolón bármikor módosíthatja a maximális átviteli sebesség (Tmax) beállítást.

## <a name="benefits-of-autopilot-mode"></a>Az Autopilot mód előnyei

Az Autopilot módban konfigurált Azure Cosmos-tárolók a következő előnyöket nyújtják:

* **Egyszerű:** Az Autopilot módban lévő tárolók megszüntetik az összetettséget a kiépített átviteli sebesség (RUs) és a kapacitás manuális kezeléséhez a különböző tárolók esetében.

* **Skálázható:** Az Autopilot módban lévő tárolók zökkenőmentesen méretezhetők a kiosztott átviteli kapacitás igény szerint. Az ügyfélkapcsolatok, az alkalmazások és a meglévő SLA-kat nem érintik.

* **Költséghatékony:** Ha robotpilóta módban konfigurált Azure Cosmos-tárolókat használ, csak azokat az erőforrásokat kell fizetnie, amelyeket a számítási feladatoknak óránként kell használniuk.

* **Magasan elérhető:** Az Autopilot módban lévő Azure Cosmos-tárolók ugyanazt a globálisan elosztott, hibatűrő, magas rendelkezésre állású hátteret használják az adattartósság és a magas rendelkezésre állás érdekében.

## <a name="use-cases-of-autopilot-mode"></a>Az Autopilot mód használatának esetei

Az Autopilot módban konfigurált Azure Cosmos-tárolók használati esetei a következők:

* **Változó számítási feladatok:** Ha egy könnyű használatú alkalmazást futtat, és a maximális kihasználtsága 1 óra, akkor naponta többször, vagy évente többször is. Ilyenek például az emberi erőforrások, a költségvetések és az operatív jelentéskészítési alkalmazások. Ilyen esetekben az Autopilot módban konfigurált tárolók is használhatók, ezért nem kell manuálisan kiépíteni a csúcs-vagy az átlagos kapacitást.

* **Előre nem látható számítási feladatok:** Ha olyan munkaterheléseket futtat, amelyeken a nap folyamán adatbázis-használat van, de a tevékenységek is nehezen megbecsülhető. Ilyen például egy olyan forgalmi hely, amely az időjárás-előrejelzés változásakor meghaladó aktivitást lát. Az Autopilot üzemmódban konfigurált tárolók úgy módosítják a kapacitást, hogy megfeleljenek az alkalmazás maximális terhelésének, és a leskálázás a tevékenység túllépését eredményezi.

* **Új alkalmazások:** Ha új alkalmazást telepít, és nem biztos benne, hogy mekkora mennyiségű kiosztott átviteli sebességre van szükség. Az Autopilot módban konfigurált tárolók automatikusan méretezhetők az alkalmazás kapacitási igényeire és követelményeire.

* **Ritkán használt alkalmazások:** Ha olyan alkalmazást használ, amely naponta, hetente vagy havonta többször is használatban van, például egy kis mennyiségű alkalmazás/Web/Blog webhelyen.

* **Fejlesztési és tesztelési adatbázisok:** A fejlesztők a munkaidő alatt használják az Azure Cosmos-fiókokat, de nem szükséges éjszakára vagy hétvégére használni őket. Az Autopilot módban konfigurált tárolók esetében a használaton kívüli minimálisra méretezhetők.

* **Ütemezett üzemi munkaterhelések/lekérdezések:** Ha egy adott tárolón ütemezett kérelmek/műveletek/lekérdezések sorozata van, és ha vannak olyan tétlen időszakok, amikor egy abszolút alacsony átviteli sebességen szeretne futni, mostantól könnyedén elvégezhető. Ha egy ütemezett lekérdezés/kérelem egy Autopilot módban konfigurált tárolóhoz van elküldve, a rendszer a szükséges mértékben automatikusan felskálázást végez, és futtatja a műveletet.

Az előző problémák megoldásához nem csupán nagy mennyiségű időt kell igénybe venni a megvalósításban, de összetettséget is bevezetnek a konfigurációban vagy a kódban, és gyakran kézi beavatkozásra van szükségük a megoldáshoz. Az Autopilot mód lehetővé teszi, hogy a fenti forgatókönyvek a dobozból is elérhetők legyenek, így többé nem kell aggódnia ezekkel a problémákról.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Összehasonlítás – kézi üzemmódban konfigurált tárolók és Autopilot mód

|  | Manuális módban konfigurált tárolók  | Robotpilóta-módban konfigurált tárolók |
|---------|---------|---------|
| **Kiosztott átviteli sebesség** | Manuálisan kiépítve | Proaktívan és reaktívan méretezhető a munkaterhelés-használati minták alapján. |
| **Kérelmek/műveletek korlátozása (429)**  | Előfordulhat, hogy a felhasználás meghaladja a kiosztott kapacitást. | Nem fog történni.  |
| **Kapacitástervezés** |  Meg kell tennie a kezdeti kapacitás megtervezését és a szükséges átviteli sebesség kiépítését. |    Nem kell aggódnia a kapacitás megtervezése miatt. A rendszer automatikusan gondoskodik a kapacitás megtervezéséről és a kapacitások kezeléséről. |
| **Díjszabás** | Manuálisan kiépített RU/s óránként. | Az egyszeri írási régió fiókjai esetében óradíjat használ a robotpilóta (RU/s) óránkénti díjszabása alapján. <br/><br/>A több írási régióval rendelkező fiókok esetében nem számítunk fel külön díjat a robotpilóta számára. Az óránkénti átviteli sebességért kell fizetnie, ugyanazzal a több főkiszolgálós RU/s-díj használatával. |
| **Legmegfelelőbb a számítási feladatok típusaihoz** |  Kiszámítható és stabil számítási feladatok|   Kiszámíthatatlan és változó számítási feladatok  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Adatbázis vagy tároló létrehozása robotpilóta-móddal

Az Autopilot-t konfigurálhatja adatbázisok vagy tárolók létrehozásához. Használja az alábbi lépéseket egy új adatbázis vagy tároló számára, engedélyezze az Autopilot-t, és határozza meg a maximális átviteli sebességet.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) vagy az [Azure Cosmos Explorerben.](https://cosmos.azure.com/)

1. Navigáljon az Azure Cosmos-fiókjához, és nyissa meg a **adatkezelő** lapot.

1. Válassza az **új adatbázis**lehetőséget, adja meg az adatbázis nevét. Az **Autopilot** beállításnál válassza az **engedélyezve** lehetőséget, és adja meg azt a maximális átviteli sebességet, amelyet az adatbázis nem tud túllépni az Autopilot beállítás használatakor.

   ![Adatbázis létrehozása Autopilot módban](./media/provision-throughput-autopilot/create-database-autopilot-mode.png)

1. Kattintson az **OK** gombra.

A hasonló lépéseket követve létrehozhat egy kiépített átviteli sebességű tárolót is az Autopilot módban.

## <a name="next-steps"></a>További lépések

* További információ a [logikai partíciókhoz](partition-data.md).
* Útmutató az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiépítéséhez.
* Útmutató az [átviteli sebesség Azure Cosmos-adatbázison](how-to-provision-database-throughput.md)való kiépítéséhez.