---
title: Azure Stream Analytics az IoT Edge segítségével
description: Edge-feladatok létrehozása az Azure Stream Analytics, és az Azure IoT Edge futtató eszközre telepítse őket.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: c64bf11a5e0d95e2896bb717d4069f9b0d7ea721
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569994"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics az IoT Edge segítségével
 
Az Azure Stream Analytics (ASA) az IoT Edge-ben lehetővé teszi a fejlesztők számára, hogy IoT-eszközökre telepíti központilag közel valós idejű elemzési intelligenciát bárhol is tartózkodjanak, hogy azok oldhatja fel a teljes mértékben kihasználhatók a eszköz által létrehozott adatokat. Az Azure Stream Analytics kis késésre, rugalmasságra, a sávszélesség hatékony használatára és megfelelőségre lett tervezve. A vállalatok mostantól közel az ipari műveletek ellenőrzési logika üzembe, és kiegészíti a felhőben végzett Big Data-elemzés.  

Az Azure Stream Analytics az IoT Edge-en belül futtatja a [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) keretrendszer. A feladat az ASA létrehozása után telepítheti és kezelheti az IoT Hub használatával.

## <a name="scenarios"></a>Forgatókönyvek
![IoT Edge összeállítás áttekintő jellegű diagramja](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Közel valós idejű parancs és vezérlés**: Például a biztonsági rendszerek gyártási válaszolnia kell a működési adatok ultramagas alacsony késleltetéssel. Az ASA az IoT Edge-ben az adatok közel valós időben, és adja ki a parancsokat, ha egy gép leállítása vagy riasztások aktiválása a rendellenességek észlelését, érzékelő elemezheti.
*   **A felhővel való kapcsolat korlátozott**: Mission kritikus rendszerek, például a távoli adatbányászati berendezések, csatlakoztatott hajó vagy offshore részletes elemzések kibontásáról, elemezheti és reagálni, hogy az adatok akkor is felhőkapcsolatra időszakos kell. Az ASA a streaming logika futtat függetlenül a hálózati kapcsolatot, és Ön kiválaszthatja, mit küld a felhő további feldolgozás vagy tárolás.
* **Korlátozott sávszélesség**: Hajtóművek által előállított adatok mennyisége, vagy lehet, hogy a csatlakoztatott autók rendkívül nagy méretűek, hogy kell-e előre feldolgozott, mielőtt elküldené a felhőben vagy szűrt adatok. ASA használatával szűrheti vagy összesítheti az adatokat, amelyek a felhőbe kell küldeni.
* **Megfelelőségi**: A jogszabályoknak való megfelelőség helyileg anonimizált vagy összesített előtt a felhőbe küldött adatokra lehet szükség.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Az Azure Stream Analytics Edge-feladatok
### <a name="what-is-an-edge-job"></a>Mi az "él" feladatot?

ASA Edge-feladatok futtatása üzembe helyezett tárolók [Azure IoT Edge-eszközök](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Ezek a következők két részből áll:
1.  Feladatdefiníció felelős felhő tartozik: felhasználók definiálása bemeneti, kimeneti, lekérdezési és egyéb beállítások (üzemen kívüli események, stb.) a felhőben.
2.  A modul futtatása az IoT-eszközökön. Az ASA-motort tartalmaz, és a feladat definíciója fogad a felhő. 

ASA az IoT Hub telepítéséhez edge-feladatok (ök) höz használ. További információ a [üzemelő IoT Edge-példány látható itt](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Az Azure Stream Analytics Edge-feladat](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Telepítési utasítások
A magas szintű lépéseket a következő táblázat ismerteti. További információkat is megtudhat az alábbi szakaszokban található.
|      |Lépés   | Megjegyzések   |
| ---   | ---   |  ---      |
| 1   | **Storage-tároló létrehozása**   | Storage-tárolók segítségével a feladatdefiníció mentése ha azok elérhetők az IoT-eszközök által. <br>  Használhat bármely meglévő storage-tárolóba.     |
| 2   | **Edge ASA-feladatok létrehozása**   |  Hozzon létre egy új feladatot, válassza ki **Edge** , **üzemeltetési környezet**. <br> Ezek a feladatok létrehozott és kezelt a felhőből, és futtassa a saját IoT Edge-eszközökön.     |
| 3   | **A az eszközt az IoT Edge-környezet beállítása**   | Utasítások [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) vagy [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Az IoT Edge-eszközt az ASA telepítése**   |  A korábban létrehozott tároló ASA-feladat definíciója exportálja.       |
Követheti [részletes oktatóanyag](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) üzembe helyezéséhez az első ASA-feladat az IoT Edge-ben. Az alábbi videó segítenek megérteni a folyamat egy Stream Analytics-feladat futtatása az IoT edge-eszközön:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Storage-tároló létrehozása
Egy storage-tárolóba szükség ahhoz, hogy exportálja az ASA összeállított lekérdezést, és a feladat konfigurációját. A lekérdezés konfigurálása az ASA Docker-rendszerkép szolgál. 
1. Hajtsa végre a [ezek az utasítások](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) a storage-fiók létrehozása az Azure Portalról. Beállíthatja, hogy a fiók használatához az ASA az összes alapértelmezett beállításokat.
2. Az újonnan létrehozott tárfiók hozzon létre egy blob storage-tároló:
    1. Kattintson a **Blobok**, majd **+ tároló**. 
    2. Adjon meg egy nevet, és tartsa meg a tárolót, **privát**.

#### <a name="create-an-asa-edge-job"></a>ASA Edge-feladatok létrehozása
> [!Note]
> Ebben az oktatóanyagban az ASA-feladat létrehozása az Azure portal használatával foglalkozik. Emellett [ASA Edge-feladatok létrehozása a Visual Studio beépülő modul használatával](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Az Azure Portalról hozzon létre egy új "Stream Analytics-feladat". [Hozzon létre egy új ASA feladatot a közvetlen hivatkozás](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. A létrehozás képernyőn válassza ki a **Edge** , **üzemeltetési környezet** (lásd a következő képen látható)

   ![Stream Analytics-feladat létrehozása az Edge-ben](media/stream-analytics-edge/create-asa-edge-job.png)
3. Feladat definíciója
    1. **Adja meg a bemeneti Stream(s)**. Adja meg a feladat egy vagy több bemeneti streamekhez.
    2. Adja meg a referenciaadatok (nem kötelező).
    3. **Adja meg a kimeneti Stream(s)**. Adja meg a feladat egy vagy több kimeneti adatfolyamokat. 
    4. **Lekérdezés meghatározása**. Adja meg az ASA-lekérdezést a felhőben, a beágyazott-szerkesztő használatával. A fordítóprogram automatikusan ellenőrzi a szintaxist, az ASA edge engedélyezve van. A lekérdezés teszteléséhez a mintaadatok feltöltése. 

4. Állítsa be a storage-tároló információkat a **IoT Edge-beállítások** menü.

5. Nem kötelező beállítások megadása
    1. **Események rendezése**. A portálon konfigurálhatja a soron kívüli out házirend. Dokumentáció áll rendelkezésre [Itt](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Területi beállítás**. Állítsa be a internalization formátumban.



> [!Note]
> Központi telepítés létrehozásakor a ASA exportálja a feladat definíciója egy storage-tárolóba. Ezt a feladatdefiníciót változatlan marad, a központi telepítés időtartam alatt. Következtében ha azt szeretné, frissítheti a feladatokat az Edge-ben futó, szüksége a feladat az ASA szerkesztése és az IoT Hub hozhatók létre új központi telepítést.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Az eszközt az IoT Edge környezet beállítása
Edge-feladatok is üzembe helyezhetők az Azure IoT Edge-es eszközökön.
Ehhez kövesse az alábbi lépéseket kell:
- Hozzon létre egy Iot hubot.
- Telepítse a Docker és az IoT Edge-futtatókörnyezet a peremhálózati eszközökre.
- Állítsa be az eszközöket, mint **IoT Edge-eszközök** az IoT hubon.

Az IoT Edge dokumentációja ismerteti ezeket a lépéseket [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) vagy [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Az IoT Edge (ök) höz ASA központi telepítése
##### <a name="add-asa-to-your-deployment"></a>Az üzemelő példány ASA hozzáadása
- Az Azure Portalon nyissa meg az IoT Hub, lépjen a **IoT Edge** , majd kattintson a, amelyekre az üzembe helyezés kívánt eszközt.
- Válassza ki **modulok beállítása**, majd **+ Hozzáadás** válassza **Azure Stream Analytics modul**.
- Válassza ki az előfizetést és az ASA Edge-feladat, amelyet Ön hozott létre. Kattintson a Mentés gombra.
![A központi telepítésben ASA-modul hozzáadása](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Ezzel a lépéssel ASA mappát hoz létre a "EdgeJobs" a tároló neve (Ha még nem létezik már). Minden egyes üzemelő példányhoz egy új almappát a "EdgeJobs" mappában jön létre.
> Annak érdekében, hogy a feladat üzembe a peremhálózati eszközökre, az ASA egy közös hozzáférésű jogosultságkód (SAS) a feladat definícióját fájlt hoz létre. A SAS-kulcsát biztonságosan átkerülnek a ikereszközök használata az IoT Edge-eszközökön. Ez a kulcs lejáratának napjától létrehozása a három év meghatározva.


IoT Edge-telepítések kapcsolatos további információkért lásd: [ezt oldal](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Útvonalak beállítása
IoT Edge lehetővé teszi a deklaratív irányíthatja a modulok között, és modulok és az IoT Hub közötti üzenetek. A teljes szintaxis leírása [Itt](https://docs.microsoft.com/azure/iot-edge/module-composition).
Lépések bemeneteit és kimeneteit létrehozott az ASA-feladat neve útválasztáshoz használható Végpontokként.  

###### <a name="example"></a>Példa

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
Ez a példa bemutatja a forgatókönyvhöz az alábbi ábrán bemutatott az útvonalakat. Edge-feladatok nevű tartalmaz "**ASA**", nevű bemeneti "**hőmérséklet**"és a egy nevű kimeneti"**riasztás**".
![Példa diagram üzenet-útválasztása](media/stream-analytics-edge/edge-message-routing-example.png)

Ebben a példában a következő útvonalakat határozza meg:
- Az összes üzenetet a **tempSensor** nevű modul küldendő **ASA** a nevű bemeneti **hőmérséklet**,
- Az összes kimenetének **ASA** modul érkeznek az eszközön (felső$), csatolt IoT hubhoz
- Az összes kimenetének **ASA** modul érkeznek a **vezérlő** végpontját a **tempSensor**.


## <a name="technical-information"></a>Technikai információ
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Felhőalapú feladat képest az IoT Edge-feladatok aktuális korlátozások
A cél, hogy a paritásos IoT Edge-feladatok és a felhőbeli feladatok között. A legtöbb SQL-lekérdezési nyelvi funkciók már használhatók.
Azonban a következő funkciók még nem támogatottak az edge-feladatok:
* Felhasználó által definiált függvények (UDF) a JavaScript. Az UDF érhetők el a [ C# IoT Edge-feladatok](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (előzetes verzió).
* Felhasználó által definiált összesítések (UDA).
* Az Azure Machine Learning-függvények
* Legfeljebb 14 összesítést használó egyetlen lépésben.
* Bemeneti/kimeneti AVRO formátum. Jelenleg csak a fürt megosztott kötetei szolgáltatás és a JSON támogatott.
* A következő SQL-operátorok:
    * Térinformatikai kezelők:
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * A PARTÍCIÓ SZERINT
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Modul és a hardverkövetelményeket
Az ASA (IoT Edge) futtatásához, eszközök, amelyek futtathatók kell [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA és az Azure IoT Edge használata **Docker** tárolókat a hordozható megoldást kínál, amely több gazdagép operációs rendszert (Windows, Linux) futtat.

Az IoT Edge-ben ASA vált elérhetővé, Windows és Linux-rendszerképeket, x86-64-vagy ARM (Advanced RISC gépek) architektúrák. 


### <a name="input-and-output"></a>Bemenet és kimenet
#### <a name="input-and-output-streams"></a>Bemeneti és kimeneti adatfolyam
ASA Edge-feladatok az IoT Edge-eszközökön futó más modulok kérheti bemeneteit és kimeneteit. Csatlakozhat a kezdő és a modulokról, beállíthatja az útválasztási konfigurációja üzembe helyezéskor. További információ a leírt [az IoT Edge-modul összeállítás dokumentációja](https://docs.microsoft.com/azure/iot-edge/module-composition).

A bemenetek és kimenetek CSV- és JSON formátumok használata támogatott.

Minden bemeneti és kimeneti adatfolyamba hoz létre az ASA-feladat, a kapcsolódó végpont a telepített modul jön létre. Ezeket a végpontokat az útvonalakat a központi telepítés is használható.

Jelenleg a, az egyetlen támogatott vstup datového proudu és a stream kimenettípusok Edge hubot. Támogatja a bemeneti fájl hivatkozástípus hivatkozhat. Más kimenetek elérhető, felhőalapú feladat használatával aktiválásához. Például egy Edge-ben futó Stream Analytics-feladat kimeneti küld, amely ezután küldhetnek kimenetet az IoT hubhoz Edge hubot. Egy második a felhőben Azure Stream Analytics-feladat használhatja a Power bi-ban vagy egy másik kimeneti típus az IoT Hub és a kimeneti bemenetet.



##### <a name="reference-data"></a>Referenciaadat
Referenciaadatok (más néven egy keresési táblázat) egy olyan véges adatokat, amely statikus vagy lassan jellegű módosítása. A keresés végrehajtásához vagy korrelációját, ha az adatfolyamban szolgál. Győződjön meg arról, hogy az Azure Stream Analytics-feladat a referenciaadatok, az általában használhat egy [referencia-adatok CSATLAKOZZON](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) a lekérdezésben. További információkért lásd: a [a referenciaadatok a Stream Analytics keresések](stream-analytics-use-reference-data.md).

Csak a helyi referenciaadatok használata támogatott. Amikor egy feladat IoT Edge-eszköz üzembe, referenciaadatok betölti a felhasználó által megadott elérési útról.

Létrehoz egy feladatot az Edge-ben a referenciaadatok:

1. Hozzon létre egy új bemeneti a feladatnak.

2. Válasszon **referenciaadatok** , a **forrástípus**.

3. Rendelkezik egy referencia-adatfájl készen áll az eszközön. Az egy Windows-tárolók helyezze a referencia-adatfájl a helyi meghajtón, és megoszthatja a helyi meghajtón a Docker-tárolót. Linux-tárolók hozzon létre egy Docker-kötetet, és töltse fel az adatok fájlt a köteten.

4. Állítsa be a fájl elérési útját. A Windows gazda operációs rendszer és Windows-tárolót használja az abszolút elérési út: `E:\<PathToFile>\v1.csv`. Egy Windows-gazda operációs rendszer- és Linux-tárolót vagy a Linux operációs rendszer és a Linux-tárolót, használja a mennyiségi programban az elérési út: `<VolumeName>/file1.txt`.

![Az Azure Stream Analytics-feladat az IoT Edge-ben új referenciaadat-bemenetek](./media/stream-analytics-edge/Reference-Data-New-Input.png)

A referenciaadatok IoT Edge-frissítés központi telepítés által aktiválódik. Aktivált, miután az ASA-modul a futó feladat leállítása nélkül választja ki a frissített adatokat.

A referenciaadatok frissítése két módja van:
* Frissítés referencia adatelérési útvonalán az ASA-feladat az Azure Portalról.
* Az IoT Edge üzemelő példány frissítése.

## <a name="license-and-third-party-notices"></a>Licenc és a harmadik felekkel kapcsolatos közlemények
* [Az Azure Stream Analytics az IoT Edge-ben licenc](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Harmadik felekre vonatkozó megjegyzés, az Azure Stream Analytics az IoT Edge-ben](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Segítségkérés
További segítségre van szüksége, próbálja meg a [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>További lépések

* [További információ az Azure Iot Edge-ben](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Az IoT Edge-oktatóanyag ASA](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [A Visual Studio-eszközökkel Stream Analytics Edge-feladatok fejlesztése](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [CI/CD megvalósításához a Stream Analytics API-k használatával](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
