---
title: IoT Edge-eszközökön futó Azure Stream Analytics
description: Hozzon létre peremhálózati feladatokat az Azure Stream Analytics szolgáltatásban, és telepítse őket az Azure IoT Edge-et futtató eszközökre.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 8bb1bd018866bda9270b78507f0462b6c4d4ea17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475892"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>IoT Edge-eszközökön futó Azure Stream Analytics
 
Az IoT Edge-eszközökön futó Azure Stream Analyticsszel (ASA) a fejlesztők közel valós idejű elemzési intelligenciát helyezhetnek üzembe az IoT-eszközökhöz közelebb, így teljes mértékben kihasználhatják az eszközök által létrehozott adatokban rejlő értéket. Az Azure Stream Analytics kis késésre, rugalmasságra, a sávszélesség hatékony használatára és megfelelőségre lett tervezve. A vállalatok mostantól az ipari műveletekhez közel helyezhetik üzembe a vezérlési logikát, és kiegészíthetik a felhőben végrehajtott Big Data-elemzéseket.  

Az Azure Stream Analytics az IoT Edge-en az [Azure IoT Edge keretrendszeren](https://azure.microsoft.com/campaigns/iot-edge/) belül fut. Miután a feladat létrejött az ASA-ban, üzembe helyezheti és kezelheti az IoT Hub használatával.

## <a name="scenarios"></a>Forgatókönyvek
![Az IoT Edge magas szintű diagramja](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Kis késleltetésű parancs és vezérlés:** A gyártási biztonsági rendszereknek például rendkívül alacsony késleltetéssel kell reagálniuk a működési adatokra. Az IOT Edge ASA segítségével közel valós időben elemezheti az érzékelőadatokat, és parancsokat adhat ki, amikor anomáliákat észlel egy számítógép leállításához vagy riasztások at aktivál.
*   **Korlátozott kapcsolat a felhővel:** A kritikus fontosságú rendszereknek, például a távoli bányászati berendezéseknek, a csatlakoztatott hajóknak vagy a tengeri fúrásnak elemezniük kell az adatokat, és reagálniuk kell az adatokra, még akkor is, ha a felhőbeli kapcsolat időszakos. Az ASA-val a streamelési logika a hálózati kapcsolattól függetlenül fut, és kiválaszthatja, hogy mit küld a felhőbe további feldolgozásra vagy tárolásra.
* **Korlátozott sávszélesség**: A sugárhajtóművek vagy a csatlakoztatott autók által előállított adatok mennyisége olyan nagy lehet, hogy az adatokat szűrni vagy előre feldolgozni kell, mielőtt elküldenék azokat a felhőbe. Az ASA használatával szűrheti vagy összesítheti a felhőbe küldendő adatokat.
* **Megfelelőség**: Előfordulhat, hogy a szabályozási megfelelőség bizonyos adatok helyian anonimizálást vagy összesítést igényel a felhőbe való küldés előtt.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Peremhálózati feladatok az Azure Stream Analytics szolgáltatásban
### <a name="what-is-an-edge-job"></a>Mi az az "él" munka?

Az ASA Edge-feladatok [az Azure IoT Edge-eszközökre](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)telepített tárolókban futnak. Ezek két részből állnak:
1.  A feladatdefinícióért felelős felhőrész: a felhasználók bemeneteket, kimeneteket, lekérdezéseket és egyéb beállításokat definiálnak (sorrenden kívüli események stb.) a felhőben.
2.  Az IoT-eszközökön futó modul. Tartalmazza az ASA-motort, és a felhőből kapja a feladatdefiníciót. 

Az ASA az IoT Hub segítségével telepíti a peremhálózati feladatokat az eszközökre. Az [IoT Edge üzembe helyezéséről itt olvashat bővebben.](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)

![Azure Stream Analytics Edge-feladat](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Telepítési utasítások
A magas szintű lépéseket az alábbi táblázat ismerteti. További részletek a következő szakaszokban találhatók.

|      |Lépés   | Megjegyzések   |
| ---   | ---   |  ---      |
| 1   | **Tárolótároló létrehozása**   | Tárolótárolók segítségével mentheti a feladatdefiníciót, ahol az IoT-eszközök által elérhető. <br>  Bármely meglévő tárolótároló t felhasználhat.     |
| 2   | **ASA peremfeladat létrehozása**   |  Hozzon létre egy új feladatot, válassza **az Edge** **hosting környezetben**lehetőséget. <br> Ezek a feladatok a felhőből jönnek létre/kezelhetők, és a saját IoT Edge-eszközökön futnak.     |
| 3   | **Az IoT Edge-környezet beállítása az eszköz(ek)en**   | Utasítások a [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) vagy [linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **ASA üzembe helyezése az IoT Edge-eszköz(ök)**   |  Az ASA-feladatdefiníció exportálása a korábban létrehozott tárolóba történik.       |

Kövesse [ezt a lépésről-lépésre oktatóanyag](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) üzembe helyezéséhez az első ASA-feladat az IoT Edge-en. Az alábbi videó segít megérteni a Stream Analytics-feladat IoT peremhálózati eszközön való futtatásának folyamatát:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Tárolótároló létrehozása
Az ASA lefordított lekérdezésének és a feladatkonfigurációexportáláshoz tárolási tárolóra van szükség. Az ASA Docker-rendszerkép konfigurálására szolgál az adott lekérdezéssel. 
1. Kövesse ezeket az [utasításokat,](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) hogy hozzon létre egy tárfiókot az Azure Portalon. Megtarthatja az összes alapértelmezett beállítást, hogy ezt a fiókot az ASA-val használja.
2. Az újonnan létrehozott tárfiókban hozzon létre egy blob storage-tárolót:
    1. Kattintson a **Blobs**, majd **+ Container**. 
    2. Írjon be egy nevet, és tartsa a tárolót **privátként.**

#### <a name="create-an-asa-edge-job"></a>ASA Edge-feladat létrehozása
> [!Note]
> Ez az oktatóanyag az ASA-munkahelyteremtésre összpontosít az Azure Portal használatával. A [Visual Studio beépülő modul segítségével ASA Edge-feladatot](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs) is létrehozhat

1. Az Azure Portalon hozzon létre egy új "Stream Analytics-feladatot". [Közvetlen link, hogy hozzon létre egy új ASA munkát itt](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. A létrehozási képernyőn válassza az **Edge** **hosting környezet** lehetőséget (lásd az alábbi képet)

   ![Stream Analytics-feladat létrehozása az Edge-en](media/stream-analytics-edge/create-asa-edge-job.png)
3. Feladat definíciója
    1. **Adja meg a bemeneti adatfolyam(oka)t.** Adjon meg egy vagy több bemeneti adatfolyamot a feladathoz.
    2. Hivatkozási adatok meghatározása (nem kötelező).
    3. **Kimeneti adatfolyam(ok) definiálása**. Definiáljon egy vagy több kimeneti adatfolyamot a feladathoz. 
    4. **Lekérdezés definiálása**. Definiálja az ASA-lekérdezést a felhőben a szövegközi szerkesztő használatával. A fordító automatikusan ellenőrzi az ASA edge számára engedélyezett szintaxist. A lekérdezést mintaadatok feltöltésével is tesztelheti. 

4. Állítsa be a tároló adatait az **IoT Edge beállítások** menüben.

5. Választható beállítások megadása
    1. **Esemény rendelése**. A portálon beállíthatja a nem sorrendben lévő házirendet. A dokumentáció [itt](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)érhető el.
    2. **Területi beállítás**. Állítsa be a internalizálási formátumot.



> [!Note]
> Központi telepítés létrehozásakor az ASA exportálja a feladatdefiníciót egy tárolótárolóba. Ez a feladat-definíció ugyanaz marad a központi telepítés időtartama alatt. Ennek következtében, ha szeretné frissíteni a peremhálózaton futó feladat, a feladat az ASA-ban, majd hozzon létre egy új üzembe helyezést az IoT Hubban.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Az IoT Edge-környezet beállítása az eszköz(ök)
Peremhálózati feladatok azure IoT Edge-et futtató eszközökön telepíthetők.
Ehhez az alábbi lépéseket kell végrehajtania:
- Hozzon létre egy iot hubot.
- Telepítse a Docker és az IoT Edge futásidejű a peremhálózati eszközökre.
- Állítsa be az eszközöket **IoT Edge-eszközök** ként az IoT Hubban.

Ezeket a lépéseket a [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) vagy [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)IoT Edge dokumentációjában ismertetik.  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Asa üzembe helyezése az IoT Edge-eszköz(ök)
##### <a name="add-asa-to-your-deployment"></a>Asa hozzáadása a telepítéshez
- Az Azure Portalon nyissa meg az IoT Hubot, keresse meg az **IoT Edge-et,** és kattintson a központi telepítéshez megcélozni kívánt eszközre.
- Válassza **a Modulok beállítása**lehetőséget, majd a + **Add** and choose Azure Stream Analytics Module (Bővítmény és az Azure Stream **Analytics modul) lehetőséget.**
- Válassza ki az előfizetést és a létrehozott ASA Edge-feladatot. Kattintson a Mentés gombra.
![ASA-modul hozzáadása a központi telepítéshez](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Ebben a lépésben az ASA létrehoz egy "EdgeJobs" nevű mappát a tárolótárolóban (ha még nem létezik). Minden központi telepítéshez egy új almappa jön létre az "EdgeJobs" mappában.
> Amikor telepíti a feladatot az IoT Edge-eszközökre, az ASA létrehoz egy megosztott hozzáférésű aláírást (SAS) a feladatdefiníciós fájlhoz. A SAS-kulcs biztonságosan továbbítódik az IoT Edge-eszközök ikereszköz használatával. Ennek a kulcsnak a lejárta három évvel a létrehozása napjától. IoT Edge-feladat frissítésekor a SAS megváltozik, de a rendszerkép verziója nem változik. A **frissítés**után kövesse a központi telepítési munkafolyamatot, és egy frissítési értesítés van bejelentkezve az eszközön.


Az IoT Edge-telepítésekről ezen [a lapon](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)talál további információt.


##### <a name="configure-routes"></a>Útvonalak konfigurálása
Az IoT Edge lehetővé teszi az üzenetek deklaratív továbbítását a modulok, valamint a modulok és az IoT Hub között. A teljes szintaxis itt [található.](https://docs.microsoft.com/azure/iot-edge/module-composition)
Az ASA-feladatban létrehozott bemenetek és kimenetek nevei az útválasztás végpontjaiként használhatók.  

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
Ez a példa az alábbi képen ismertetett forgatókönyv útvonalait mutatja be. Tartalmaz egy edge feladat az úgynevezett "**ASA**", egy bemeneti nevű "**hőmérséklet**" és egy kimeneti nevű "**riasztás**".
![Diagram példa az üzenetútválasztásra](media/stream-analytics-edge/edge-message-routing-example.png)

Ez a példa a következő útvonalakat határozza meg:
- Minden üzenetet a **tempSensor** küld a modul nevű **ASA** a bemeneti nevű **hőmérséklet,**
- Az **ASA** modul összes kimenete az ehhez az eszközhöz kapcsolt IoT Hubba kerül ($upstream),
- Az **ASA** modul összes kimenete a tempSensor **vezérlővégpontjára** **kerül.**


## <a name="technical-information"></a>Műszaki információk
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Az IoT Edge-feladatok jelenlegi korlátai a felhőalapú feladatokhoz képest
A cél az IoT Edge-feladatok és a felhőbeli feladatok közötti paritás. A legtöbb SQL-lekérdezési nyelvi szolgáltatások támogatottak, amely lehetővé teszi, hogy ugyanazt a logikát mind a felhő és az IoT Edge futtatásához.
A peremhálózati feladatok hoz a következő funkciók at még nem támogatják:
* Felhasználó által definiált függvények (UDF) JavaScript ben. UDF érhetők el [c# IoT Edge feladatok](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (előzetes verzió).
* Felhasználó által definiált összesítések (UDA).
* Azure ML-függvények.
* Több mint 14 aggregátum használata egyetlen lépésben.
* AVRO formátum bemeneti/kimeneti. Jelenleg csak a CSV és a JSON támogatott.
* A következő SQL operátorok:
    * PARTÍCIÓ
    * GetMetadataPropertyValue
* Késői érkezésre vonatkozó irányelvek

### <a name="runtime-and-hardware-requirements"></a>Futásidejű és hardverkövetelmények
Az ASA ioT Edge-en való futtatásához olyan eszközökre van szüksége, amelyek futtathatják [az Azure IoT Edge-et.](https://azure.microsoft.com/campaigns/iot-edge/) 

Az ASA és az Azure IoT Edge **Docker-tárolók** használatával hordozható megoldást biztosít, amely több gazdaoperációs rendszeren (Windows, Linux) fut.

Az IoT Edge ASA-ja Elérhető Windows és Linux rendszerképekként, x86-64 vagy ARM (Advanced RISC Machines) architektúrákon is. 


### <a name="input-and-output"></a>Bemenet és kimenet
#### <a name="input-and-output-streams"></a>Bemeneti és kimeneti adatfolyamok
Az ASA Edge-feladatok az IoT Edge-eszközökön futó más modulok bemeneteit és kimeneteit is lejuthatnak. Adott modulokból való csatlakozáshoz és azokhoz az útválasztási konfigurációt a telepítés kor állíthatja be. További információt [az IoT Edge modul összeállításának dokumentációjában](https://docs.microsoft.com/azure/iot-edge/module-composition)ismertetünk.

A CSV és jSON formátumok mind a bemenetek, mind a kimenetek esetében támogatottak.

Az ASA-feladatban létrehozott minden egyes bemeneti és kimeneti streamhez egy megfelelő végpont jön létre az üzembe helyezett modulon. Ezek a végpontok a központi telepítés útvonalain használhatók.

Jelenleg az egyetlen támogatott stream bemeneti és stream kimeneti típusok Edge Hub. A hivatkozási bemenet támogatja a referenciafájltípust. Más kimenetek érhető el egy felhőalapú feladat downstream használatával. Például egy Stream Analytics-feladat az Edge-ben üzemeltetett küld kimenetet Edge Hub, amely ezután kimenetet küldhet az IoT Hub. Használhatja a második felhőben üzemeltetett Azure Stream Analytics-feladat az IoT Hub bemeneti és kimeneti Power BI vagy más kimeneti típus.



##### <a name="reference-data"></a>Referenciaadatok
A referenciaadatok (más néven lookup table) egy véges adatkészlet, amely statikus vagy lassú módosítás iményjellegű. Arra használják, hogy végezzen egy lookup, vagy korrelál az adatfolyam. A referenciaadatok az Azure Stream Analytics-feladatban, általában egy [referenciaadatok JOIN](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) a lekérdezésben. További információt a [Stream Analytics-ben található keresgélő megtekintések referenciaadatainak használata című témakörben talál.](stream-analytics-use-reference-data.md)

Csak a helyi referenciaadatok támogatottak. Amikor egy feladat telepítve van az IoT Edge-eszközre, betölti a referenciaadatokat a felhasználó által definiált fájl elérési útjáról.

Feladat létrehozása hivatkozási adatokkal az Edge-en:

1. Hozzon létre egy új bemenetet a feladathoz.

2. Forrástípusként válassza a **Hivatkozási adatok** **lehetőséget.**

3. Készítse elő a referencia-adatfájlt az eszközön. Windows-tároló esetén helyezze a referencia-adatfájlt a helyi meghajtóra, és ossza meg a helyi meghajtót a Docker-tárolóval. Linux-tároló esetén hozzon létre egy Docker-kötetet, és feltölti az adatfájlt a kötetre.

4. Állítsa be a fájl elérési útját. Windows host operációs rendszer és Windows tároló `E:\<PathToFile>\v1.csv`esetén használja az abszolút elérési utat: . Windows host operációs rendszer és Linux tároló, illetve Linux operációs rendszer `<VolumeName>/file1.txt`és Linux tároló esetén használja a kötetelérési útját: .

![Új referenciaadat-bevitel az Azure Stream Analytics-feladathoz az IoT Edge-en](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Az IoT Edge-frissítés referenciaadatait egy központi telepítés váltja ki. Aktiválásután az ASA modul a futó feladat leállítása nélkül választja ki a frissített adatokat.

A referenciaadatok kétféleképpen frissíthetők:
* Frissítse a referenciaadatok elérési útját az ASA-feladatban az Azure Portalról.
* Frissítse az IoT Edge központi telepítését.

## <a name="license-and-third-party-notices"></a>Licenc és harmadik fél értesítései
* [Az Azure Stream Analytics az IoT Edge-licencen.](https://go.microsoft.com/fwlink/?linkid=862827) 
* [Az Azure Stream Analytics külső értesítése az IoT Edge-en.](https://go.microsoft.com/fwlink/?linkid=862828)

## <a name="azure-stream-analytics-module-image-information"></a>Az Azure Stream Analytics modul képadatai 

Ezt a verzióinformációt utoljára a 2019-06-27-es számon frissítettük:

- Kép: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-amd64`
   - alapkép: microsoft/dotnet:2.1.6-runtime-alpine3.7
   - Platform:
      - építészet: amd64
      - os: linux
  
- Kép: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-arm32v7`
   - alapkép: microsoft/dotnet:2.1.6-runtime-bionic-arm32v7
   - Platform:
      - építészet: kar
      - os: linux
  
- Kép: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-windows-amd64`
   - alapkép: microsoft/dotnet:2.1.6-runtime-nanoserver-1809
   - Platform:
      - építészet: amd64
      - os: ablakok
      
      
## <a name="get-help"></a>Segítségkérés
További segítségért próbálja ki az [Azure Stream Analytics fórumot.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések

* [További információ az Azure Iot Edge-ről](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA az IoT Edge oktatóanyagán](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Stream Analytics Edge-feladatok fejlesztése a Visual Studio eszközeivel](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [CI/CD megvalósítása a Stream Analytics szolgáltatáshoz API-k használatával](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
