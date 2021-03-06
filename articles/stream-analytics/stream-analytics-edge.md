---
title: IoT Edge-eszközökön futó Azure Stream Analytics
description: Hozzon létre Edge-feladatokat a Azure Stream Analyticsban, és telepítse őket Azure IoT Edge rendszert futtató eszközökre.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 10/29/2020
ms.custom: seodec18
ms.openlocfilehash: cba81b8415f0f9cf7253e674e90ae09718b94d54
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130476"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>IoT Edge-eszközökön futó Azure Stream Analytics
 
Az IoT Edge-eszközökön futó Azure Stream Analyticsszel (ASA) a fejlesztők közel valós idejű elemzési intelligenciát helyezhetnek üzembe az IoT-eszközökhöz közelebb, így teljes mértékben kihasználhatják az eszközök által létrehozott adatokban rejlő értéket. Az Azure Stream Analytics kis késésre, rugalmasságra, a sávszélesség hatékony használatára és megfelelőségre lett tervezve. A vállalatok mostantól az ipari műveletekhez közel helyezhetik üzembe a vezérlési logikát, és kiegészíthetik a felhőben végrehajtott Big Data-elemzéseket.  

A IoT Edge-eszközökön futó Azure Stream Analytics a [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) -keretrendszerben fut. Ha a feladatot az ASA-ban hozták létre, IoT Hub használatával telepítheti és felügyelheti.

## <a name="scenarios"></a>Forgatókönyvek
![IoT Edge magas szintű diagramja](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Kis késleltetésű parancs és vezérlés** : például a gyártási biztonsági rendszereknek rendkívül alacsony késéssel kell válaszolniuk az operatív adatszolgáltatásokra. Az ASA on IoT Edge segítségével közel valós időben elemezheti az érzékelőket, és parancsokat adhat ki, amikor a gép leállítása vagy a riasztások kiváltása miatt rendellenességeket észlel.
*   **Korlátozott kapcsolódás a felhőhöz: a** kritikus fontosságú rendszerek, például a távoli adatbányászati berendezések, a csatlakoztatott hajók vagy a offshore fúrások esetében az adatelemzést és az adatkezelést akkor is meg kell vizsgálni, ha a Felhőbeli kapcsolat időszakos. Az ASA esetében a folyamatos átviteli logikája a hálózati kapcsolattól függetlenül fut, és kiválaszthatja, hogy mit küld a felhőbe további feldolgozásra vagy tárolásra.
* **Korlátozott sávszélesség** : a Jet-motorok vagy a csatlakoztatott autók által előállított adatok mennyisége olyan nagy lehet, hogy az adatokat szűrni kell, vagy előre fel kell dolgozni a felhőbe való küldés előtt. Az ASA használatával szűrheti vagy összesítheti a felhőbe küldendő adatokat.
* **Megfelelőség** : a szabályozás megfelelősége miatt előfordulhat, hogy bizonyos adatokat helyileg kell névtelenül vagy összesíteni, mielőtt elküldi őket a felhőbe.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-feladatok a Azure Stream Analyticsban
### <a name="what-is-an-edge-job"></a>Mi az "Edge"-feladatok?

Az ASA Edge-feladatok a [Azure IoT Edge eszközökön](../iot-edge/about-iot-edge.md)üzembe helyezett tárolókban futnak. Ezek két részből állnak:
1.  A feladattípusért felelős Felhőbeli rész: a felhasználók bemeneteket, kimeneteket, lekérdezéseket és egyéb beállításokat határoznak meg a felhőben.
2.  Egy modul, amely a IoT-eszközökön fut. Ez tartalmazza az ASA-motort, és a feladattípust fogadja a felhőből. 

Az ASA a IoT Hub használatával helyezi üzembe az Edge-feladatokat az eszköz (ek) re. [IoT Edge üzembe helyezéssel](../iot-edge/module-deployment-monitoring.md)kapcsolatos további információkért tekintse meg itt.

![Azure Stream Analytics Edge-feladatok](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Telepítési utasítások
A magas szintű lépéseket az alábbi táblázat ismerteti. További részleteket a következő szakaszokban talál.

| Lépés | Megjegyzések |
| --- | --- |
| **Tároló létrehozása** | A tárolók a IoT-eszközök által elérhető feladatdefiníció mentésére szolgálnak. <br>  A meglévő tárolókat újra felhasználhatja. |
| **ASA Edge-feladatok létrehozása** | Hozzon létre egy új feladatot, és válassza az **Edge** lehetőséget **üzemeltetési környezetként** . <br> Ezek a feladatok a felhőből jönnek létre/kezelhetők, és a saját IoT Edge eszközein futnak. |
| **Az eszköz (ek) IoT Edge környezetének beállítása** | Windows vagy [Linux](../iot-edge/quickstart-linux.md) [rendszerre](../iot-edge/quickstart.md) vonatkozó utasítások.|
| **Az ASA üzembe helyezése IoT Edge eszközön (k)** | Az ASA-feladatdefiníció a korábban létrehozott Storage-tárolóba lett exportálva. |

[Ezt a részletes oktatóanyagot](../iot-edge/tutorial-deploy-stream-analytics.md) követve üzembe helyezheti az első ASA-feladatot IoT Edgeon. A következő videó segít megérteni a Stream Analytics feladatok futtatásának folyamatát egy IoT Edge-eszközön:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Tároló létrehozása
Az ASA lefordított lekérdezés és a feladatok konfigurációjának exportálásához tárolóra van szükség. Az ASA Docker-rendszerkép az adott lekérdezéssel való konfigurálására szolgál. 
1. Az [alábbi utasításokat](../storage/common/storage-account-create.md) követve hozzon létre egy Storage-fiókot a Azure Portal. Az összes alapértelmezett beállítást megtarthatja a fiók ASA-vel való használatához.
2. Az újonnan létrehozott Storage-fiókban hozzon létre egy blob Storage-tárolót:
    1. Kattintson a **Blobok** , majd a **+ tároló** elemre. 
    2. Adjon meg egy nevet, és tartsa **magánjellegűként** a tárolót.

#### <a name="create-an-asa-edge-job"></a>ASA Edge-feladatok létrehozása
> [!Note]
> Ez az oktatóanyag az ASA-feladatok Azure Portal használatával történő létrehozására koncentrál. Az [ASA Edge-feladatok létrehozásához használhatja a Visual Studio beépülő modult](./stream-analytics-tools-for-visual-studio-edge-jobs.md) is

1. A Azure Portal hozzon létre egy új "Stream Analytics feladatot". [Közvetlen hivatkozás új ASA-feladatok létrehozásához itt](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. A létrehozási képernyőn válassza a **peremhálózat** **üzemeltetési környezetként** (lásd a következő ábrát)

   ![Stream Analytics-feladatok létrehozása az Edge-ben](media/stream-analytics-edge/create-asa-edge-job.png)
3. Feladatdefiníció
    1. **Adja meg a bemeneti stream (eke) t** . Definiáljon egy vagy több bemeneti streamet a feladathoz.
    2. Adja meg a hivatkozási adattípusokat (nem kötelező).
    3. **Kimeneti adatfolyamok definiálása** . Adjon meg egy vagy több kimeneti adatfolyamot a feladatokhoz. 
    4. **Lekérdezés definiálása** . Adja meg az ASA-lekérdezést a felhőben a beágyazott szerkesztő használatával. A fordító automatikusan ellenőrzi az ASA Edge-hez engedélyezett szintaxist. A lekérdezést a mintaadatok feltöltésével is tesztelheti. 

4. Adja meg a Storage-tároló adatait a **IoT Edge beállítások** menüjében.

5. Választható beállítások megadása
    1. **Események rendezése** . A nem megrendelési szabályzatokat a portálon konfigurálhatja. A dokumentáció [itt](/stream-analytics-query/time-skew-policies-azure-stream-analytics)érhető el.
    2. **Területi beállítás** . Állítsa be a internalizálása formátumát.



> [!Note]
> Egy központi telepítés létrehozásakor az ASA a feladatdefiníció egy tárolóba exportálja. Ez a feladatdefiníció a központi telepítés időtartama alatt változatlan marad. Ennek következményeként, ha egy, a peremhálózati gépen futó feladatot szeretne frissíteni, a feladatot az ASA-ben kell szerkesztenie, majd létre kell hoznia egy új központi telepítést IoT Hubban.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>A IoT Edge-környezet beállítása az eszköz (ek) re
Az Edge-feladatok Azure IoT Edge rendszert futtató eszközökön helyezhetők üzembe.
Ehhez a következő lépéseket kell követnie:
- Hozzon létre egy IOT hubot.
- Telepítse a Docker és a IoT Edge Runtime eszközt a peremhálózati eszközökön.
- Állítsa be az eszközöket **IoT Edge eszközként** a IoT hub.

Ezek a lépések a [Windows](../iot-edge/quickstart.md) vagy [Linux](../iot-edge/quickstart-linux.md)IoT Edge dokumentációjában olvashatók.  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Üzembe helyezési ASA a IoT Edge eszközön (k)
##### <a name="add-asa-to-your-deployment"></a>ASA hozzáadása az üzemelő példányhoz
- A Azure Portal nyissa meg a IoT Hubt, navigáljon a **IoT Edge** elemre, és kattintson arra az eszközre, amelyet meg szeretne célozni ehhez a központi telepítéshez.
- Válassza a **modulok beállítása** , majd a **+ Hozzáadás** lehetőséget, és válassza **Azure stream Analytics modult** .
- Válassza ki az előfizetést és a létrehozott ASA Edge-feladatot. Kattintson a Mentés gombra.
![ASA-modul hozzáadása az üzemelő példányban](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Ebben a lépésben az ASA létrehoz egy "EdgeJobs" nevű mappát a tárolóban (ha még nem létezik). Az egyes központi telepítések esetében új almappa jön létre a "EdgeJobs" mappában.
> Ha IoT Edge-eszközökre helyezi üzembe a feladatot, az ASA létrehoz egy közös hozzáférési aláírást (SAS) a feladatdefiníció fájlhoz. Az SAS-kulcs biztonságosan továbbítva van a IoT Edge eszközöknek a Twin eszköz használatával. A kulcs lejárta a létrehozás napjától számított három év. Amikor frissít egy IoT Edge feladatot, az SAS megváltoztatja, de a rendszerkép verziószáma nem változik. A **frissítés** után kövesse az üzembe helyezési munkafolyamatot, és a rendszer egy frissítési értesítést naplóz az eszközön.


IoT Edge központi telepítésekkel kapcsolatos további információkért tekintse meg [ezt a lapot](../iot-edge/module-deployment-monitoring.md).


##### <a name="configure-routes"></a>Útvonalak konfigurálása
A IoT Edge lehetővé teszi, hogy az üzeneteket a modulok között, illetve a modulok és a IoT Hub között lehessen átirányítani. A teljes szintaxis [itt](../iot-edge/module-composition.md)van leírva.
Az ASA-feladatban létrehozott bemenetek és kimenetek nevei végpontként használhatók az útválasztáshoz.  

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
Ez a példa az alábbi képen bemutatott forgatókönyv útvonalait mutatja be. Egy " **ASA** " nevű peremhálózati feladatot tartalmaz, egy " **hőmérséklet** " nevű bemenettel és egy " **riasztás** " nevű kimenettel.
![Diagram – példa az üzenet-útválasztásra](media/stream-analytics-edge/edge-message-routing-example.png)

Ez a példa a következő útvonalakat határozza meg:
- A rendszer a **tempSensor** származó összes üzenetet elküldi az **ASA** nevű modulnak a megadott **hőmérsékleti** értékre.
- Az **ASA** -modul összes kimenetét az eszközhöz kapcsolódó IoT hub küldi a rendszer ($upstream),
- A rendszer az **ASA** -modul összes kimenetét elküldi a **tempSensor** **vezérlő** végpontjának.


## <a name="technical-information"></a>Technikai információk
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>A Felhőbeli feladatokhoz képest IoT Edge feladatok jelenlegi korlátai
A cél a IoT Edge feladatok és a Felhőbeli feladatok közötti paritás. A legtöbb SQL-lekérdezés nyelvi funkciója támogatott, ami lehetővé teszi, hogy ugyanazt a logikát futtassa a felhőben és a IoT Edge is.
Az Edge-feladatok esetében azonban a következő funkciók még nem támogatottak:
* Felhasználó által definiált függvények (UDF) a JavaScriptben. Az UDF a C# nyelven érhető el [IoT Edge feladatokhoz](./stream-analytics-edge-csharp-udf.md) (előzetes verzió).
* Felhasználó által definiált összesítések (UDA).
* Azure ML függvények.
* Több mint 14 összesítés használata egyetlen lépésben.
* A bemeneti/kimeneti AVRO formátuma. Jelenleg csak a CSV és a JSON támogatott.
* A következő SQL-operátorok:
    * PARTICIONÁLÁS
    * GetMetadataPropertyValue
* Késői érkezési szabályzat

### <a name="runtime-and-hardware-requirements"></a>Futtatókörnyezet és hardverkövetelmények
Az ASA IoT Edge futtatásához olyan eszközökre van szükség, amelyeken futtathatók a [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

Az ASA és a Azure IoT Edge **Docker** -tárolók használatával biztosítanak egy olyan hordozható megoldást, amely több gazdagépen futó operációs rendszeren (Windows, Linux) fut.

Az ASA on IoT Edge Windows-és Linux-rendszerképekként is elérhető, amely x86-64 vagy ARM (Advanced RISC machines) architektúrán fut. 


### <a name="input-and-output"></a>Bemenet és kimenet
#### <a name="input-and-output-streams"></a>Bemeneti és kimeneti adatfolyamok
Az ASA Edge-feladatok a IoT Edge eszközökön futó más modulok bemeneteit és kimeneteit is lekérhetik. A és az adott modulokhoz való kapcsolódáshoz beállíthatja az útválasztási konfigurációt a központi telepítés ideje alatt. További információ a [IoT Edge modul-összeállítás dokumentációjában](../iot-edge/module-composition.md)olvasható.

A bemenetek és kimenetek esetében a CSV és a JSON formátum is támogatott.

Az ASA-feladatban létrehozott összes bemeneti és kimeneti adatfolyamhoz létrejön egy megfelelő végpont az üzembe helyezett modulon. Ezek a végpontok használhatók az üzemelő példány útvonalán.

Jelenleg az egyetlen támogatott stream-bemeneti és stream-kimeneti típus a peremhálózati hub. A hivatkozás bemenete támogatja a hivatkozási fájl típusát. Más kimenetek is elérhetők egy felhőalapú feladatokkal. Az Edge-ben futtatott Stream Analytics-feladatok például a kimenetet a peremhálózati hubhoz küldik, amely ezután kimenetet küld a IoT Hubnak. Használhat egy második felhőben üzemeltetett Azure Stream Analytics feladatot IoT Hubból és kimenetből Power BI vagy más kimeneti típusba való bevitelsel.



##### <a name="reference-data"></a>Hivatkozási érték
A hivatkozási adathalmaz (más néven keresési táblázat) egy olyan véges adatkészlet, amely statikus vagy lassú a természetben. A lekérdezés végrehajtásához vagy az adatfolyamhoz való korrelációhoz használható. Ha a Azure Stream Analytics-feladatokban szeretné használni a hivatkozásokat, a lekérdezésben általában egy [hivatkozási adatokhoz való csatlakozást](/stream-analytics-query/reference-data-join-azure-stream-analytics) fog használni. További információ: a [stream Analyticsban található keresések hivatkozási adatainak használata](stream-analytics-use-reference-data.md).

Csak a helyi hivatkozási adatértékek támogatottak. Ha egy feladatot központilag telepítenek IoT Edge eszközre, a felhasználó által megadott fájl elérési útjából betölti a hivatkozási adatait.

Az Edge-adatokkal rendelkező feladatok létrehozása:

1. Hozzon létre egy új bemenetet a feladathoz.

2. Válassza ki a **forrás típusaként** **szolgáló hivatkozási** értéket.

3. Az eszközön készen áll egy hivatkozási adatfájl. Windows-tároló esetén helyezze a hivatkozási adatfájlt a helyi meghajtón, és ossza meg a helyi meghajtót a Docker-tárolóval. Linux-tároló esetén hozzon létre egy Docker-kötetet, és töltse fel az adatfájlt a kötetre.

4. Adja meg a fájl elérési útját. A Windows-gazdagép operációs rendszer és a Windows-tároló esetében használja az abszolút elérési utat: `E:\<PathToFile>\v1.csv` . Windows-gazdagép operációs rendszer-és Linux-tárolóhoz, illetve Linux operációs rendszerhez és Linux-tárolóhoz használja a következő kötet elérési útját: `<VolumeName>/file1.txt` .

![Új, Azure Stream Analytics feladatra vonatkozó adatbevitel IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

A IoT Edge frissítésre vonatkozó hivatkozásokat egy központi telepítés indítja el. Az aktiválást követően az ASA-modul a futó feladatok leállítása nélkül kiválasztja a frissített adatok körét.

A referenciák frissítése kétféleképpen lehetséges:
* Frissítse a hivatkozási adatok elérési útját az ASA-feladataiban Azure Portalról.
* Frissítse a IoT Edge üzemelő példányt.

## <a name="license-and-third-party-notices"></a>Licencek és harmadik felekkel kapcsolatos közlemények
* [IoT Edge-eszközökön futó Azure stream Analytics licenc](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Harmadik féltől származó értesítés a IoT Edge-eszközökön futó Azure stream Analytics](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Azure Stream Analytics modul képének adatai 

A verzióra vonatkozó információk utolsó frissítése 2019-06-27:

- Kép: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - alaprendszerkép: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - platform
      - architektúra: amd64
      - operációs rendszer: Linux
 
- Kép: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - alaprendszerkép: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - platform
      - architektúra: ARM
      - operációs rendszer: Linux
 
- Kép: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - alaprendszerkép: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - platform
      - architektúra: arm64
      - operációs rendszer: Linux
      
      
## <a name="get-help"></a>Segítség kérése
További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics vonatkozó kérdés oldalát](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Következő lépések

* [További információ az Azure IOT Edge-ről](../iot-edge/about-iot-edge.md)
* [ASA IoT Edge oktatóanyag](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Stream Analytics Edge-feladatok fejlesztése a Visual Studio Tools használatával](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [CI/CD implementálása a Stream Analytics API-k használatával](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/