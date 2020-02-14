---
title: IoT Edge-eszközökön futó Azure Stream Analytics
description: Edge-feladatok létrehozása az Azure Stream Analytics, és az Azure IoT Edge futtató eszközre telepítse őket.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 02/14/2020
ms.custom: seodec18
ms.openlocfilehash: 7e4a1cf43931b56cfdb3c31ffe870a07dbaa75af
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201755"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>IoT Edge-eszközökön futó Azure Stream Analytics
 
Az IoT Edge-eszközökön futó Azure Stream Analyticsszel (ASA) a fejlesztők közel valós idejű elemzési intelligenciát helyezhetnek üzembe az IoT-eszközökhöz közelebb, így teljes mértékben kihasználhatják az eszközök által létrehozott adatokban rejlő értéket. Az Azure Stream Analytics kis késésre, rugalmasságra, a sávszélesség hatékony használatára és megfelelőségre lett tervezve. A vállalatok mostantól az ipari műveletekhez közel helyezhetik üzembe a vezérlési logikát, és kiegészíthetik a felhőben végrehajtott Big Data-elemzéseket.  

A IoT Edge-eszközökön futó Azure Stream Analytics a [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) -keretrendszerben fut. A feladat az ASA létrehozása után telepítheti és kezelheti az IoT Hub használatával.

## <a name="scenarios"></a>Forgatókönyvek
![IoT Edge összeállítás áttekintő jellegű diagramja](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Kis késleltetésű parancs és vezérlés**: például a gyártási biztonsági rendszereknek rendkívül alacsony késéssel kell válaszolniuk az operatív adatszolgáltatásokra. Az ASA az IoT Edge-ben az adatok közel valós időben, és adja ki a parancsokat, ha egy gép leállítása vagy riasztások aktiválása a rendellenességek észlelését, érzékelő elemezheti.
*   **Korlátozott kapcsolódás a felhőhöz: a**kritikus fontosságú rendszerek, például a távoli adatbányászati berendezések, a csatlakoztatott hajók vagy a offshore fúrások esetében az adatelemzést és az adatkezelést akkor is meg kell vizsgálni, ha a Felhőbeli kapcsolat időszakos. Az ASA a streaming logika futtat függetlenül a hálózati kapcsolatot, és Ön kiválaszthatja, mit küld a felhő további feldolgozás vagy tárolás.
* **Korlátozott sávszélesség**: a Jet-motorok vagy a csatlakoztatott autók által előállított adatok mennyisége olyan nagy lehet, hogy az adatokat szűrni kell, vagy előre fel kell dolgozni a felhőbe való küldés előtt. ASA használatával szűrheti vagy összesítheti az adatokat, amelyek a felhőbe kell küldeni.
* **Megfelelőség**: a szabályozás megfelelősége miatt előfordulhat, hogy bizonyos adatokat helyileg kell névtelenül vagy összesíteni, mielőtt elküldi őket a felhőbe.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Az Azure Stream Analytics Edge-feladatok
### <a name="what-is-an-edge-job"></a>Mi az "él" feladatot?

Az ASA Edge-feladatok a [Azure IoT Edge eszközökön](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)üzembe helyezett tárolókban futnak. Ezek a következők két részből áll:
1.  Feladatdefiníció felelős felhő tartozik: felhasználók definiálása bemeneti, kimeneti, lekérdezési és egyéb beállítások (üzemen kívüli események, stb.) a felhőben.
2.  A modul futtatása az IoT-eszközökön. Az ASA-motort tartalmaz, és a feladat definíciója fogad a felhő. 

ASA az IoT Hub telepítéséhez edge-feladatok (ök) höz használ. [IoT Edge üzembe helyezéssel](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)kapcsolatos további információkért tekintse meg itt.

![Az Azure Stream Analytics Edge-feladat](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Telepítési utasítások
A magas szintű lépéseket a következő táblázat ismerteti. További információkat is megtudhat az alábbi szakaszokban található.

|      |Lépés   | Megjegyzések   |
| ---   | ---   |  ---      |
| 1   | **Storage-tároló létrehozása**   | Storage-tárolók segítségével a feladatdefiníció mentése ha azok elérhetők az IoT-eszközök által. <br>  Használhat bármely meglévő storage-tárolóba.     |
| 2   | **ASA Edge-feladatok létrehozása**   |  Hozzon létre egy új feladatot, és válassza az **Edge** lehetőséget **üzemeltetési környezetként**. <br> Ezek a feladatok létrehozott és kezelt a felhőből, és futtassa a saját IoT Edge-eszközökön.     |
| 3   | **Az eszköz (ek) IoT Edge környezetének beállítása**   | Windows vagy [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) [rendszerre](https://docs.microsoft.com/azure/iot-edge/quickstart) vonatkozó utasítások.          |
| 4   | **Az ASA üzembe helyezése IoT Edge eszközön (k)**   |  A korábban létrehozott tároló ASA-feladat definíciója exportálja.       |

[Ezt a részletes oktatóanyagot](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) követve üzembe helyezheti az első ASA-feladatot IoT Edgeon. Az alábbi videó segítenek megérteni a folyamat egy Stream Analytics-feladat futtatása az IoT edge-eszközön:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Storage-tároló létrehozása
Egy storage-tárolóba szükség ahhoz, hogy exportálja az ASA összeállított lekérdezést, és a feladat konfigurációját. A lekérdezés konfigurálása az ASA Docker-rendszerkép szolgál. 
1. Az [alábbi utasításokat](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) követve hozzon létre egy Storage-fiókot a Azure Portal. Beállíthatja, hogy a fiók használatához az ASA az összes alapértelmezett beállításokat.
2. Az újonnan létrehozott tárfiók hozzon létre egy blob storage-tároló:
    1. Kattintson a **Blobok**, majd a **+ tároló**elemre. 
    2. Adjon meg egy nevet, és tartsa **magánjellegűként**a tárolót.

#### <a name="create-an-asa-edge-job"></a>ASA Edge-feladatok létrehozása
> [!Note]
> Ebben az oktatóanyagban az ASA-feladat létrehozása az Azure portal használatával foglalkozik. Az [ASA Edge-feladatok létrehozásához használhatja a Visual Studio beépülő modult](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs) is

1. Az Azure Portalról hozzon létre egy új "Stream Analytics-feladat". [Közvetlen hivatkozás új ASA-feladatok létrehozásához itt](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. A létrehozási képernyőn válassza a **peremhálózat** **üzemeltetési környezetként** (lásd a következő ábrát)

   ![Stream Analytics-feladat létrehozása az Edge-ben](media/stream-analytics-edge/create-asa-edge-job.png)
3. Feladat definíciója
    1. **Adja meg a bemeneti stream (eke) t**. Adja meg a feladat egy vagy több bemeneti streamekhez.
    2. Adja meg a referenciaadatok (nem kötelező).
    3. **Kimeneti adatfolyamok definiálása**. Adja meg a feladat egy vagy több kimeneti adatfolyamokat. 
    4. **Lekérdezés definiálása**. Adja meg az ASA-lekérdezést a felhőben, a beágyazott-szerkesztő használatával. A fordítóprogram automatikusan ellenőrzi a szintaxist, az ASA edge engedélyezve van. A lekérdezés teszteléséhez a mintaadatok feltöltése. 

4. Adja meg a Storage-tároló adatait a **IoT Edge beállítások** menüjében.

5. Nem kötelező beállítások megadása
    1. **Események rendezése**. A portálon konfigurálhatja a soron kívüli out házirend. A dokumentáció [itt](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)érhető el.
    2. **Területi beállítás**. Állítsa be a internalization formátumban.



> [!Note]
> Központi telepítés létrehozásakor a ASA exportálja a feladat definíciója egy storage-tárolóba. Ezt a feladatdefiníciót változatlan marad, a központi telepítés időtartam alatt. Következtében ha azt szeretné, frissítheti a feladatokat az Edge-ben futó, szüksége a feladat az ASA szerkesztése és az IoT Hub hozhatók létre új központi telepítést.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Az eszközt az IoT Edge környezet beállítása
Edge-feladatok is üzembe helyezhetők az Azure IoT Edge-es eszközökön.
Ehhez kövesse az alábbi lépéseket kell:
- Hozzon létre egy Iot hubot.
- Telepítse a Docker és az IoT Edge-futtatókörnyezet a peremhálózati eszközökre.
- Állítsa be az eszközöket **IoT Edge eszközként** a IoT hub.

Ezek a lépések a [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) vagy [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)IoT Edge dokumentációjában olvashatók.  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Az IoT Edge (ök) höz ASA központi telepítése
##### <a name="add-asa-to-your-deployment"></a>Az üzemelő példány ASA hozzáadása
- A Azure Portal nyissa meg a IoT Hubt, navigáljon a **IoT Edge** elemre, és kattintson arra az eszközre, amelyet meg szeretne célozni ehhez a központi telepítéshez.
- Válassza a **modulok beállítása**, majd a **+ Hozzáadás** lehetőséget, és válassza **Azure stream Analytics modult**.
- Válassza ki az előfizetést és az ASA Edge-feladat, amelyet Ön hozott létre. Kattintson a Mentés gombra.
![ASA-modul hozzáadása a telepítéshez](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Ezzel a lépéssel ASA mappát hoz létre a "EdgeJobs" a tároló neve (Ha még nem létezik már). Minden egyes üzemelő példányhoz egy új almappát a "EdgeJobs" mappában jön létre.
> Ha IoT Edge-eszközökre helyezi üzembe a feladatot, az ASA létrehoz egy közös hozzáférési aláírást (SAS) a feladatdefiníció fájlhoz. A SAS-kulcsát biztonságosan átkerülnek a ikereszközök használata az IoT Edge-eszközökön. Ez a kulcs lejáratának napjától létrehozása a három év meghatározva. Amikor frissít egy IoT Edge feladatot, az SAS megváltoztatja, de a rendszerkép verziószáma nem változik. A **frissítés**után kövesse az üzembe helyezési munkafolyamatot, és a rendszer egy frissítési értesítést naplóz az eszközön.


IoT Edge központi telepítésekkel kapcsolatos további információkért tekintse meg [ezt a lapot](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Útvonalak beállítása
IoT Edge lehetővé teszi a deklaratív irányíthatja a modulok között, és modulok és az IoT Hub közötti üzenetek. A teljes szintaxis [itt](https://docs.microsoft.com/azure/iot-edge/module-composition)van leírva.
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
Ez a példa bemutatja a forgatókönyvhöz az alábbi ábrán bemutatott az útvonalakat. Egy "**ASA**" nevű peremhálózati feladatot tartalmaz, egy "**hőmérséklet**" nevű bemenettel és egy "**riasztás**" nevű kimenettel.
![diagram – példa az üzenetek útválasztására](media/stream-analytics-edge/edge-message-routing-example.png)

Ebben a példában a következő útvonalakat határozza meg:
- A rendszer a **tempSensor** származó összes üzenetet elküldi az **ASA** nevű modulnak a megadott **hőmérsékleti**értékre.
- Az **ASA** -modul összes kimenetét az eszközhöz kapcsolódó IoT hub küldi a rendszer ($upstream),
- A rendszer az **ASA** -modul összes kimenetét elküldi a **tempSensor** **vezérlő** végpontjának.


## <a name="technical-information"></a>Technikai információ
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Felhőalapú feladat képest az IoT Edge-feladatok aktuális korlátozások
A cél, hogy a paritásos IoT Edge-feladatok és a felhőbeli feladatok között. A legtöbb SQL-lekérdezés nyelvi funkciója támogatott, ami lehetővé teszi, hogy ugyanazt a logikát futtassa a felhőben és a IoT Edge is.
Azonban a következő funkciók még nem támogatottak az edge-feladatok:
* Felhasználó által definiált függvények (UDF) a JavaScript. Az UDF a [ C# IoT Edge feladatok](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (előzetes verzió) számára érhető el.
* Felhasználó által definiált összesítések (UDA).
* Az Azure ML-függvényeket.
* Legfeljebb 14 összesítést használó egyetlen lépésben.
* Bemeneti/kimeneti AVRO formátum. Jelenleg csak a fürt megosztott kötetei szolgáltatás és a JSON támogatott.
* A következő SQL-operátorok:
    * A PARTÍCIÓ SZERINT
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Modul és a hardverkövetelményeket
Az ASA IoT Edge futtatásához olyan eszközökre van szükség, amelyeken futtathatók a [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

Az ASA és a Azure IoT Edge **Docker** -tárolók használatával biztosítanak egy olyan hordozható megoldást, amely több gazdagépen futó operációs rendszeren (Windows, Linux) fut.

Az ASA on IoT Edge Windows-és Linux-rendszerképekként is elérhető, amely x86-64 vagy ARM (Advanced RISC machines) architektúrán fut. 


### <a name="input-and-output"></a>Bemenet és kimenet
#### <a name="input-and-output-streams"></a>Bemeneti és kimeneti adatfolyam
ASA Edge-feladatok az IoT Edge-eszközökön futó más modulok kérheti bemeneteit és kimeneteit. Csatlakozhat a kezdő és a modulokról, beállíthatja az útválasztási konfigurációja üzembe helyezéskor. További információ a [IoT Edge modul-összeállítás dokumentációjában](https://docs.microsoft.com/azure/iot-edge/module-composition)olvasható.

A bemenetek és kimenetek CSV- és JSON formátumok használata támogatott.

Minden bemeneti és kimeneti adatfolyamba hoz létre az ASA-feladat, a kapcsolódó végpont a telepített modul jön létre. Ezeket a végpontokat az útvonalakat a központi telepítés is használható.

Jelenleg a, az egyetlen támogatott vstup datového proudu és a stream kimenettípusok Edge hubot. Támogatja a bemeneti fájl hivatkozástípus hivatkozhat. Más kimenetek elérhető, felhőalapú feladat használatával aktiválásához. Például egy Edge-ben futó Stream Analytics-feladat kimeneti küld, amely ezután küldhetnek kimenetet az IoT hubhoz Edge hubot. Egy második a felhőben Azure Stream Analytics-feladat használhatja a Power bi-ban vagy egy másik kimeneti típus az IoT Hub és a kimeneti bemenetet.



##### <a name="reference-data"></a>Referenciaadat
Referenciaadatok (más néven egy keresési táblázat) egy olyan véges adatokat, amely statikus vagy lassan jellegű módosítása. A keresés végrehajtásához vagy korrelációját, ha az adatfolyamban szolgál. Ha a Azure Stream Analytics-feladatokban szeretné használni a hivatkozásokat, a lekérdezésben általában egy [hivatkozási adatokhoz való csatlakozást](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) fog használni. További információ: a [stream Analyticsban található keresések hivatkozási adatainak használata](stream-analytics-use-reference-data.md).

Csak a helyi referenciaadatok használata támogatott. Amikor egy feladat IoT Edge-eszköz üzembe, referenciaadatok betölti a felhasználó által megadott elérési útról.

Létrehoz egy feladatot az Edge-ben a referenciaadatok:

1. Hozzon létre egy új bemeneti a feladatnak.

2. Válassza ki a **forrás típusaként** **szolgáló hivatkozási** értéket.

3. Rendelkezik egy referencia-adatfájl készen áll az eszközön. Az egy Windows-tárolók helyezze a referencia-adatfájl a helyi meghajtón, és megoszthatja a helyi meghajtón a Docker-tárolót. Linux-tárolók hozzon létre egy Docker-kötetet, és töltse fel az adatok fájlt a köteten.

4. Állítsa be a fájl elérési útját. A Windows-gazdagép operációs rendszer és a Windows-tároló esetében használja az abszolút elérési utat: `E:\<PathToFile>\v1.csv`. Windows-gazdagép operációs rendszer-és Linux-tárolóhoz, illetve Linux operációs rendszerhez és Linux-tárolóhoz használja a következő kötet elérési útját: `<VolumeName>/file1.txt`.

![Az Azure Stream Analytics-feladat az IoT Edge-ben új referenciaadat-bemenetek](./media/stream-analytics-edge/Reference-Data-New-Input.png)

A referenciaadatok IoT Edge-frissítés központi telepítés által aktiválódik. Aktivált, miután az ASA-modul a futó feladat leállítása nélkül választja ki a frissített adatokat.

A referenciaadatok frissítése két módja van:
* Frissítés referencia adatelérési útvonalán az ASA-feladat az Azure Portalról.
* Az IoT Edge üzemelő példány frissítése.

## <a name="license-and-third-party-notices"></a>Licenc és a harmadik felekkel kapcsolatos közlemények
* [IoT Edge-eszközökön futó Azure stream Analytics licenc](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Harmadik féltől származó értesítés a IoT Edge-eszközökön futó Azure stream Analytics](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Azure Stream Analytics modul képének adatai 

A verzióra vonatkozó információk utolsó frissítése 2019-06-27:

- Rendszerkép: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-amd64`
   - alaprendszerkép: Microsoft/DotNet: 2.1.6-Runtime-Alpine 3.7
   - platform
      - architektúra: amd64
      - operációs rendszer: Linux
  
- Rendszerkép: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-arm32v7`
   - alaprendszerkép: Microsoft/DotNet: 2.1.6-Runtime-Bionic-arm32v7
   - platform
      - architektúra: ARM
      - operációs rendszer: Linux
  
- Rendszerkép: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-windows-amd64`
   - alaprendszerkép: Microsoft/DotNet: 2.1.6-Runtime-nanoserver-1809
   - platform
      - architektúra: amd64
      - operációs rendszer: Windows
      
      
## <a name="get-help"></a>Segítségkérés
További segítségért próbálja ki a [Azure stream Analytics fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Következő lépések

* [További információ az Azure IOT Edge-ről](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA IoT Edge oktatóanyag](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Stream Analytics Edge-feladatok fejlesztése a Visual Studio Tools használatával](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [CI/CD implementálása a Stream Analytics API-k használatával](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
