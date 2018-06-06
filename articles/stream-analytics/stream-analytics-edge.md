---
title: Az Azure Stream Analytics IoT oldal (előzetes verzió)
description: Peremhálózati feladatok létrehozása az Azure Stream Analytics, és telepítheti őket az eszközök fut. Azure IoT peremhálózati.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/16/2017
ms.openlocfilehash: 5ce0420dde5bf232fe8067a3b14814f14380602e
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802527"
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>Az Azure Stream Analytics IoT oldal (előzetes verzió)

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető, és nem javasolt éles használatra.
 
Az Azure Stream Analytics (ASA) IoT oldal lehetővé teszi a fejlesztők számára, hogy azok oldhatja fel a teljes mértékben kihasználhatja a eszköz által létrehozott adatok közel valós idejű elemzési eszközintelligencia közelebb telepíteni az IoT-eszközök. Az Azure Stream Analytics a kis késleltetésű, a rugalmasság, a sávszélesség, és a megfelelőség hatékony felhasználása szolgál. Vállalatok most ellenőrzési logika megközelíti a ipari műveletek telepítheti és kiegészíti a Big Data-elemzések a felhőben történik.  

Az Azure Stream Analytics IoT oldal belül fut a [Azure IoT peremhálózati](https://azure.microsoft.com/campaigns/iot-edge/) keretrendszer. Ha a feladat ASA jön létre, telepítheti és IoT-központ használatával ASA feladatok kezelése. Ez a funkció előzetes verzióban érhető el. Ha bármilyen kérdése vagy visszajelzést szeretne küldeni, használhatja [felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) a termékért felelős csoport kapcsolódni. 

## <a name="scenarios"></a>Forgatókönyvek
![Magas szintű diagramját](media/stream-analytics-edge/ASAedge_highlevel.png)

* **Kis késleltetésű parancs és a vezérlő**: például biztonsági rendszerek gyártási kell válaszolnia a működési adatok egészen kis késleltetésű. Az IoT-oldal ASA elemezheti érzékelő adatokat majdnem valós időben, és parancsok kibocsátani, ha Ön a gépek leállítása vagy aktiváltak riasztásokat rendellenességek észlelését.
*   **A felhő kapcsolat korlátozott**: küldetési kritikus rendszerek esetében, például a távoli adatbányászati berendezések, csatlakoztatott hajók vagy part állapotkategóriák vizsgálatát, elemzése, és reagálni azokra adatokat, akkor is, ha a felhő egy időszakos kell. Az ASA a folyamatos átviteli logika függetlenül a hálózati kapcsolat fut, és választhat, hogy mi küld a felhő további feldolgozás és a tárolási.
* **Korlátozott sávszélesség**: jet motorok által létrehozott adatok mennyiségét, vagy csatlakoztatott autók annyira nagy, hogy kell-e előre feldolgozott mielőtt elküldené a felhő vagy szűrt adatokat. ASA használva kiszűrhetik vagy összesíti az adatokat, amelyek a felhőbe kell küldeni.
* **Megfelelőségi**: előírásoknak való megfelelés szükség lehet néhány adat helyileg anonimizált adatokon alapul, vagy előtt küldi el a felhő összesíteni.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Peremhálózati Azure Stream Analytics-feladatok
### <a name="what-is-an-edge-job"></a>Mi az, hogy egy "peremhálózati" feladatot?

ASA peremhálózati feladatokat futtató moduljainak [Azure IoT peremhálózati futásidejű](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Azok a két részből áll:
1.  Felhő része, amely felelős az feladatdefiníció: felhasználó definiál bemeneti, kimeneti, lekérdezés és más beállítások (üzemen kívüli események, stb.) a felhőben.
2.  Az ASA helyileg futó IoT biztonsági modulon. Az ASA összetett esemény feldolgozása motor tartalmazza, és a feladat definíciójához megkapja a felhőből. 

ASA IoT-központ használatával peremhálózati feladatok telepíteni őket. További információ a [IoT peremhálózati telepítés látható itt](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Edge-feladat](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>Telepítési utasításokat
A következő táblázat ismerteti a magas szintű lépéseket. További részletek a következő szakaszok szerepelnek.
|      |Lépés   | Hely     | Megjegyzések   |
| ---   | ---   | ---       |  ---      |
| 1   | **A tároló létrehozása**   | Azure Portal       | A tároló használt menteni a feladat definíciójához ha azok elérhetők az IoT-eszközök által. <br>  A meglévő tárolót is felhasználhatja.     |
| 2   | **ASA peremhálózati feladat létrehozása**   | Azure Portal      |  Hozzon létre egy új feladatot, jelölje be **peremhálózati** , **üzemeltetési környezet**. <br> Ezek a feladatok létrehozott vagy kezelt a felhőből, és futtassa a saját IoT peremhálózati eszközön.     |
| 3   | **Az IoT-Edge tesztkörnyezetben, a forráseszköz(ök) beállítása**   | Eszköz(ök)      | Az utasítások [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) vagy [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Az IoT-Edge eszköz(ök) ASA telepített**   | Azure Portal      |  A korábban létrehozott tároló ASA feladatdefiníció exportálja.       |
Követésével [részletes oktatóanyag](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) központi telepítése az első ASA munkahelye IoT oldal. A következő videó segítenek megérteni a a folyamat egy Stream Analytics-feladat az IoT-peremhálózati eszközön:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>A tároló létrehozása
Egy tároló kell exportálni a ASA lefordított lekérdezés és a feladat konfigurációját. Kép: a ASA Docker konfigurálása az adott lekérdezéssel szolgál. 
1. Hajtsa végre a [ezeket az utasításokat](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) storage-fiók létrehozása az Azure portálról. Beállíthatja, hogy az összes alapértelmezett beállítások ASA ehhez a fiókhoz használandó.
2. Az újonnan létrehozott tárfiók hozzon létre egy blob storage tárolót:
    1. Kattintson a **Blobok**, majd **+ tároló**. 
    2. Adjon meg egy nevet, és láthatóan tartja a tárolóban **titkos**.

#### <a name="create-an-asa-edge-job"></a>ASA peremhálózati feladat létrehozása
> [!Note]
> Ez az oktatóanyag összpontosít ASA feladat létrehozása az Azure portál használatával. Emellett [ASA peremhálózati feladat létrehozása a Visual Studio beépülő modul használatával](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Az Azure-portálon hozzon létre egy új "Stream Analytics-feladat". [Hozzon létre egy új ASA feladatot a közvetlen hivatkozás](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. A létrehozási képernyőn válassza ki a **peremhálózati** , **üzemeltetési környezet** (lásd az alábbi képen) ![feladat létrehozása](media/stream-analytics-edge/ASAEdge_create.png)
3. Feladatdefiníció
    1. **Adja meg a bemeneti Stream(s)**. Adja meg a feladat egy vagy több bemeneti adatfolyamot.
    2. Adja meg a referenciaadatok (nem kötelező).
    3. **Adja meg a kimeneti Stream(s)**. Adja meg a feladat egy vagy több kimeneti adatfolyamokat. 
    4. **Adja meg a lekérdezés**. Adja meg az ASA lekérdezést a beágyazott szerkesztővel a felhőben. A fordítóprogram automatikusan ellenőrzi a szintaxis ASA peremhálózati engedélyezve. A lekérdezés azt is tesztelni, mintaadatokat feltöltésével. 
4. A tárolási tároló adatainak beállításához a **IoT szélétől** menü.
5. Választható beállításai
    1. **Esemény rendelési**. Konfigurálhatja a soron házirendet a portálon. Dokumentáció áll rendelkezésre [Itt](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Területi beállítás**. A internalization formátum megadása.



> [!Note]
> A központi telepítés létrehozásakor ASA tárolót exportálja a feladat definíciójához. A feladat definíciójához változatlan marad, a telepítés idejére. Ennek következtében ha azt szeretné, a peremhálózati futó feladat frissítése, szüksége ASA a feladat szerkesztése, majd hozza létre az új központi telepítést az IoT-központ.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Az IoT-Edge tesztkörnyezetben, a forráseszköz(ök) beállítása
Peremhálózati feladatok Azure IoT Edge-es eszközökön is telepíthető.
Ehhez kövesse az alábbi lépéseket kell:
- Létrehoz egy Iot-központot.
- Docker- és IoT peremhálózati futásidejű telepíti a peremhálózati eszközön.
- Állítsa be az eszközöket, mint a **IoT peremeszközök** az IoT-központot.

Ezeket a lépéseket az IoT-Edge dokumentációjában ismertetett [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) vagy [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Az IoT-Edge eszköz(ök) ASA központi telepítése
##### <a name="add-asa-to-your-deployment"></a>A központi telepítés ASA hozzáadása
- Az Azure portálon, nyissa meg az IoT-központot, navigáljon a **IoT peremhálózati** és az eszközön szeretné megcélozni a telepítés gombra.
- Válassza ki **modulok beállítása**, majd jelölje be **+ Hozzáadás** válassza **Azure Stream Analytics modul**.
- Válassza ki az előfizetés és a ASA peremhálózati feladatot, amely létrehozta. Kattintson a Mentés gombra.
![ASA modul hozzá lesz adva a központi telepítésben](media/stream-analytics-edge/set_module.png)


> [!Note]
> Ezzel a lépéssel ASA mappát hoz létre a tároló elnevezett "EdgeJobs" (Ha még nem létezik). Az egyes központi telepítések egy új almappát a "EdgeJobs" mappában jön létre.
> A feladat peremeszközök telepítéséhez ASA a feladat csomagdefiníciós fájl egy közös hozzáférésű jogosultságkód (SAS) hoz létre. A SAS-kulcs biztonságosan továbbítani az IoT peremhálózati eszköz a két eszközökre. Ennek a kulcsnak lejárati három év napja, ahol létrehozták a rendszer.


Az IoT peremhálózati központi telepítések kapcsolatos további információkért lásd: [ezen a lapon](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Útvonalak beállítása
Az IoT-Edge megoldást deklaratív módon irányítja az üzenetek modulokat, valamint modulok és az IoT-központ között. A teljes szintaxissal leírt [Itt](https://docs.microsoft.com/azure/iot-edge/module-composition).
A be- és kimenetekkel a ASA feladatban létrehozott nevei végpontként használható irányításához.  

###### <a name="example"></a>Példa
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
Ez a példa bemutatja a forgatókönyvhöz az alábbi képen leírt útvonalak. Tartalmaz egy néven edge-feladat "**ASA**", nevű bemeneti "**hőmérséklet**"és egy kimeneti nevű"**riasztás**".
![Útválasztás – példa](media/stream-analytics-edge/RoutingExample.png)

Ez a példa meghatározza, hogy a következő útvonalakat:
- Minden üzenetet kapott a **tempSensor** nevű modul küld **ASA** nevű bemeneti **hőmérséklet**,
- Összes kimenetének **ASA** modul küldött csatolva ehhez az eszközhöz (előtt$), az IoT-központ
- Összes kimenetének **ASA** modul küldött a **vezérlő** végpont esetén a **tempSensor**.


## <a name="technical-information"></a>Technikai információ
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>A felhő feladatok képest peremhálózati feladatok aktuális korlátozásai
A cél, hogy él feladatok és a felhő feladatok paritása rendelkezik. A legtöbb SQL lekérdezési nyelv funkciók már használhatók.
Azonban a következő funkciók még nem támogatottak a peremhálózati feladatok:
* Felhasználói függvény (UDF) és a felhasználó által definiált összesítések (UDA).
* Az Azure ML funkciók.
* Több mint 14 aggregátumok használata egyetlen lépésben.
* Az AVRO formátum a bemeneti/kimeneti. Jelenleg csak a fürt megosztott kötetei szolgáltatás és a JSON támogatott.
* A következő SQL-operátorok:
    * AnomalyDetection
    * A földrajzi kezelők:
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * A PARTÍCIÓ ÁLTAL
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Futásidejű és a hardverkövetelményeket
IoT peremhálózati ASA futtatásához is futtató eszközök kell [Azure IoT peremhálózati](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA és Azure IoT Edge használata **Docker** tárolók több állomás operációs rendszerek (Windows, Linux) futó hordozható megoldást nyújt.

Az IoT-oldal ASA Windows és Linux képek, mind a x86-64, vagy az Azure Resource Manager architektúrák futó elérhetővé tegyen. 


### <a name="input-and-output"></a>Bemeneti és kimeneti
#### <a name="input-and-output-streams"></a>Bemeneti és kimeneti folyamok
ASA peremhálózati feladatok bemenetekhez és kimenetekhez érheti el más IoT peremhálózati eszközön futó modulok. A kezdő és a modulokról kapcsolódni a központi telepítéskor az útválasztási konfigurációja állíthatja be. További információ a leírt [IoT peremhálózati modul összeállítás dokumentációjában](https://docs.microsoft.com/azure/iot-edge/module-composition).

Bemenetekhez és kimenetekhez, a fürt megosztott kötetei szolgáltatás és a JSON formátum támogatott.

Az egyes bemeneti és kimeneti adatfolyamba hoz létre a ASA feladat, a telepített modul megfelelő végpont jön létre. Ezeket a végpontokat az útvonalakat a központi telepítés is használható.

Jelenleg a, az egyetlen támogatott adatfolyam-bemenet, és az adatfolyam kimeneti típusok a következők biztonsági központ. Támogatja a bemeneti fájl hivatkozástípus hivatkozik. Más kimenetek elérhető a felhő feladatok után. Például egy Stream Analytics-feladat az Edge üzemeltetett kimenetet küld a peremhálózati Hub, amely is elküldheti kimeneti IoT-központot. A Power BI vagy egy másik kimeneti típust az IoT Hub és a kimeneti bemenete egy második a felhőben üzemeltetett Azure Stream Analytics-feladat használatával.



##### <a name="reference-data"></a>Referenciaadat
Referenciaadatok (más néven keresési tábla) olyan véges adat, amely statikus vagy lassú jellegű módosítása. Keresés végrehajtásához, vagy az adatfolyam a kivizsgált használható. Annak hivatkozás adatok az Azure Stream Analytics-feladat, általában fogja használni a [hivatkozás adatok csatlakozás](https://msdn.microsoft.com/library/azure/dn949258.aspx) a lekérdezésben. További információkért lásd: a [ASA dokumentációjában az referenciaadatok](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data).

Az Iot-oldal ASA referenciaadatok használatához kövesse az alábbi lépéseket: 
1. Hozzon létre egy új bemeneti a feladat
2. Válasszon **referenciaadatok** , a **forrástípus**.
3. Állítsa be a fájl elérési útját. A fájl elérési útnak kell lennie egy **abszolút** az eszközön a fájl elérési útját ![hivatkozhat adatok létrehozása](media/stream-analytics-edge/ReferenceData.png)
4. Engedélyezése **megosztott meghajtók** a Docker-konfigurációja, és győződjön meg arról, hogy a meghajtó engedélyezett-e a központi telepítés elindítása előtt.

További információkért lásd: [Docker dokumentációja a Windows itt](https://docs.docker.com/docker-for-windows/#shared-drives).

> [!Note]
> Jelenleg csak a helyi referenciaadatok esetén támogatott.




## <a name="license-and-third-party-notices"></a>Licenc és a külső értesítéseket
* [Az IoT peremhálózati preview licencet az Azure Stream Analytics](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Külső értesítés az Azure Stream Analytics a IoT peremhálózati Preview-ban](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Segítségkérés
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>További lépések

* [További információ az Azure Iot oldal](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Az IoT-Edge oktatóanyag ASA](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [A csapat a felmérés használatával küldhet visszajelzést](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
* [Visual Studio eszközök használatával Stream Analytics peremhálózati feladatok fejlesztése](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
