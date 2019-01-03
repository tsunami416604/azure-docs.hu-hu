---
title: IoT-megoldásgyorsítók referenciaarchitektúra – Azure |} A Microsoft Docs
description: További információ az Azure IoT-megoldás megoldásgyorsítók referencia architektúra. A meglévő megoldásgyorsítók kihasználva Ez a referenciaarchitektúra. A referenciaarchitektúra használhatja saját egyéni IoT-megoldások készítése során is.
author: dominicbetts
ms.author: dobett
ms.date: 12/04/2018
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: philmea
ms.openlocfilehash: 5fb615ff042cc5cdd3b6b8e42e4d91b161ebf445
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602375"
---
# <a name="introduction-to-the-azure-iot-reference-architecture"></a>Az Azure IoT-referenciaarchitektúra bemutatása

Ez a cikk a [Azure IoT-referenciaarchitektúra](https://aka.ms/iotrefarchitecture) , és bemutatja a példákkal [Azure IoT-megoldásgyorsítók](about-iot-accelerators.md) javaslatait kövesse.

A nyílt forráskódú [távoli megfigyelés](iot-accelerators-remote-monitoring-sample-walkthrough.md) és [Okosgyár](iot-accelerators-connected-factory-sample-walkthrough.md) megoldásgyorsítók kövesse a referencia-architektúra javaslatok számos. A megoldásgyorsítók tanulási eszközöket, vagy a saját IoT-megoldás kiindulási pontként használható.

## <a name="overview"></a>Áttekintés

A referenciaarchitektúra egy IoT-megoldások, például a technológiai elvek, az összeállítás, az Azure IoT-szolgáltatások és az eszközök-elemeket ismerteti. Az architektúra megvalósítása technológiákkal kapcsolatos javaslatok is teszi.

A legmagasabb szintű IoT-megoldások, mint áll tekintheti meg:

* Létrehozó és küldő telemetriai adatokat – például a mértékek és események dolog. A távoli figyelési megoldásgyorsító az eszközök, például tehergépkocsik vagy elevator dolgot a telemetriai adatokat küldő.
* Az elemzések a dolog által küldött telemetriai adatokból jönnek létre. A távoli figyelési megoldásgyorsító egy szabályt hoz létre egy elemzést. Szabály például azonosíthatja, amikor a hőmérséklet-motor a eléri a küszöbértéket.
* Műveletek, az elemzéseket, egy üzleti vagy a folyamat javítása érdekében. A távoli figyelési megoldásgyorsító az e-mail-művelet értesítheti lehetséges problémát az operátornak a motorral.

A [Azure IoT-referenciaarchitektúra](https://aka.ms/iotrefarchitecture) élő dokumentum frissül, amint a technológia haladásával.

## <a name="core-subsystems"></a>Core alrendszerek

A referenciaarchitektúra azonosítja az alapvető alrendszerek az alábbi ábrán látható:

![Core alrendszerek](media/iot-accelerators-architecture-overview/CoreSubsystems.png)

A következő szakaszok ismertetik, hogyan képezze le a távoli figyelési megoldásgyorsító alkotóelemeinek a core alrendszerekhez.

### <a name="iot-devices"></a>IoT-eszközök

IoT-megoldások lehetővé kell tennie az eszköz szinte bármilyen típusú és a egy felhőátjáróhoz közötti biztonságos, hatékony és hatékony kommunikáció. Eszközök, amelyek több száz szilárd összetevők és érzékelők összetett gyártósorokon és egyszerű hőmérséklet-érzékelők közötti üzleti eszközök lesznek.

A helyszíni átjáró, vagy a peremhálózati eszköz, az egy speciális eszköz-berendezés vagy működő v:, általános célú szoftver

* Kommunikációs engedélyező protokoll átalakítás kezeléséhez.
* Helyi eszköz verziókövetési rendszert és a telemetria-feldolgozási hub eszköz. Edge-eszköz a felhőbe való kommunikáció nélkül képes feldolgozni helyileg eszközök telemetriai adatokat. Edge-eszköz is szűrheti, vagy a telemetriai adatok mennyiségének csökkentésére az eszköz összesített telemetriai töltődnek fel a felhőbe.

A távoli figyelési megoldásban az eszközök csatlakoztatása az IoT hubhoz, és a telemetriát a feldolgozáshoz. A távoli figyelési megoldás azt is lehetővé teszi, hogy az operátorok kezelheti az eszközöket a feladatok vagy az automatikus kezelés. Az Azure IoT eszközoldali SDK-k segítségével az eszközök megvalósításához.

A távoli figyelési megoldás telepítheti és kezelheti az IoT Edge-eszközökön. Feldolgozó peremére segít csökkenteni a felhőben és a válaszok eszköz események felgyorsítására küldött telemetriai adatok mennyisége.

### <a name="cloud-gateway"></a>Átjáró

Egy átjáró lehetővé teszi a kommunikációt, eszközök és az edge-eszközökön. Ezeket az eszközöket lehet számos távoli helyeken.

Az átjáró kezeli az eszköz közötti kommunikáció, beleértve a kapcsolatok kezeléséhez, a kommunikációs útvonal, hitelesítési és engedélyezési védelméről. Az átjáró is érvénybe lépteti a kapcsolat és átviteli kvótái, és összegyűjti a telemetriai adatok, Számlázás, diagnosztika és más figyelési feladatok használható.

A távoli figyelési megoldás üzembe helyez egy IoT hubot, hogy telemetriát küldjön az eszközökre egy biztonságos végpontot biztosítanak. Az IoT hub is:

* Egy eszköz ügyfélidentitás-tárolóval, kezelheti az eszközök a megoldáshoz történő csatlakoztatásáról tartalmazza.
* Lehetővé teszi, hogy a parancsok küldését az eszközök a megoldáshoz. Ha például egy szelepből nyomás felszabadítása megnyitásához.
* Lehetővé teszi, hogy tömeges eszköz kezelése. Ha például az eszközök belső vezérlőprogramjának frissítéséhez.

### <a name="stream-processing"></a>Stream-feldolgozás

A megoldás fogadnak telemetriát, fontos tudni, hogyan változhat, a telemetria-feldolgozási folyamat. A forgatókönyvtől függően adatfelderítési rekordok különböző szakaszaiban is áthaladhat:

* Tárolás, például a memórián belüli gyorsítótár, üzenetsorok ideiglenes és állandó archívumok.

* Elemzés futtatása bemeneti keresztül feltételkészleteket, telemetriai adatokat, és eltérő kimeneti rekordokat hozhat létre. Az Avro kódolású bemeneti telemetriai például JSON-kódolású kimeneti telemetriát adhat vissza.

* Eredeti bemeneti telemetriai és elemzési kimeneti bejegyzések általában tárolja és elérhetővé való megjelenítéshez. A bemeneti telemetriai és a kimeneti műveleteket, mint például az e-mailek, incidens jegyek és eszközparancsok is indíthat.

Útválasztás, a telemetriai adatokat egy vagy több tárolási végpontok, elemzési folyamatok és műveletek csatolva is. Megoldás lehet, hogy kombinálja a szakaszok eltérőek, és feldolgozni azokat az egyidejű párhuzamos feladatok.

A távoli figyelési megoldás [Azure Stream Analytics](/azure/stream-analytics/) a streamfeldolgozáshoz. A megoldásban a rules engine Stream Analytics-lekérdezések használatával hozzon létre riasztásokat és műveleteket. Például a megoldás segítségével lekérdezés azonosíthatja, ha az átlaghőmérséklet egy teherautó tárolási térben a több mint öt perc alatt 36 fok alá esik.

### <a name="storage"></a>Storage

IoT-megoldásokat hozhat létre nagy mennyiségű adatot, ami általában a idősorozat-adatok. Ezek az adatok mappájába kell menteni, ahol használat vizualizációt és a jelentéskészítés. A megoldás is lehetséges, hogy kell érniük később elemzés vagy a további feldolgozás céljából. Szokás gyakrabban és ritkábban adattárak ossza fel az adatokat. A meleg adattár közel valós idejű adateléréshez legutóbbi adatait tartalmazza. A ritkán használt adatokat tároló általában előzményadatokat tárolja.

A távoli figyelési megoldás [Azure Time Series Insights](/azure/time-series-insights/) a meleg adattár és a Cosmos DB, a ritkán használt adatok tárolását.

### <a name="ui-and-reporting-tools"></a>Felhasználói felület és jelentéskészítési eszközökkel

A megoldás felhasználói Felületet biztosítja:

* A hozzáférést és vizualizációs eszköz az adat- és elemzési eredmények.
* A beállításjegyzék segítségével eszközök felderítése.
* Eszközök irányítása és vezérlése.
* Eszköz-üzembehelyezési munkafolyamatokat.
* Értesítések és riasztások.
* Integráció az élő, interaktív irányítópultok nagyszámú eszköz adatainak megjelenítéséhez.  
* Földrajzi hely és a geo-t támogató szolgáltatások.

A távoli figyelési megoldás magában foglalja egy webes felhasználói felületen, hogy ezt a funkciót. A webes felhasználói felület tartalmazza:

* Interaktív térkép eszközök helyének megjelenítéséhez.
* A Time Series Insights explorer lekérdezése és elemezheti a telemetriát a hozzáférést.

### <a name="business-integration"></a>Vállalati integráció

A vállalati integrációs réteg kezeli az IoT-megoldások az üzleti rendszerekben, például a CRM, ERP és az üzletági alkalmazások integrációja. Ilyenek például a számlázási szolgáltatás ügyfél-támogatási, és a pótalkatrészek adja meg.

A távoli figyelési megoldás szabályokat használ, e-mailek küldése, ha egy feltétel teljesül. A megoldás értesítheti például az operátornak, ha a hőmérséklet egy teherautó 36 fok alá esik.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedhetett az Azure IoT-referenciaarchitektúra és látott néhány példát, hogyan a távoli figyelési megoldásgyorsító követi a javaslatait. A következő lépés az, hogy olvassa el a [a Microsoft Azure IoT-Referenciaarchitektúra](https://aka.ms/iotrefarchitecture).