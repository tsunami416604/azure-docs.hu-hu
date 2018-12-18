---
title: 'Áttekintés: Az Azure Time Series Insights-előzetes verzió |} A Microsoft Docs'
description: Az Azure Time Series Insights előzetes verziója áttekintése.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: 847eddc78f8abc938e68e6fe383a773dadeaf779
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557835"
---
# <a name="azure-time-series-insights-preview-overview"></a>Az Azure Time Series Insights előzetes verziója – áttekintés

Az Azure Time Series Insights előzetes verziója olyan teljes körű platform--szolgáltatásként ajánlat. Betöltési, feldolgozásához, tárolásához és magas contextualized, time series-optimalizált IoT-méretű adatok lekérdezésére szolgál. A Time Series Insights ad hoc adatáttekintés és működési elemzés ideális. A Time Series Insights egy olyan ajánlat, hogy megfelel-e a széles körű kell ipari IoT központi telepítések egyedileg bővíthető és testre szabott szolgáltatás.

## <a name="video"></a>Videó

Ebben a videóban áttekintést az Azure Time Series Insights előzetes verziója, egy felhőalapú IoT-elemzési platform biztosítunk.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="define-iot-data"></a>IoT-adatok megadása

IoT-adatok az eszköz nagy szervezetek számára érhető el "ipari" adatok. IoT-adatot azért gyakran magas strukturálatlan, eszközök, amelyek viszonylag zajos mérések rögzítése küld. Ezeknek a méréseknek a hőmérséklet, a mozgásban lévő és a páratartalom tartalmazza. A data-adatfolyamok gyakran jelentős hézagok, sérült üzenetek és hamis értéket olvasmányok jellemzi. Ezekbe az adatfolyamokba adatokat kell törölni minden olyan elemző előtt. IoT-adatot legtöbbször jelentéssel bíró csak belső forrásból, például a CRM és ERP további streamadat típusú bemenetek kontextusában. Bemenetek külső adatforrások, például az időjárás vagy helyet is származhat.

Ennek következtében az adatok egy részét működési és üzleti célokat szolgál beolvasása. Az ilyen adatok üzleti jelentéskészítési és elemzési egységes, átfogó, jelenlegi és megfelelő információkat nyújt. Bekapcsolása folyamatban gyűjtött adatokat döntéstámogató elemzésekhez szükséges IoT:

* Adatok feldolgozása törléséhez szűrése, interpolálja, átalakítása és adatok elemzésre való előkészítését.
* Keresse meg és az adatokat, azaz ismertetése, normalizálása és struktúrát contextualize az adatokat.
* Költséghatékony tárolási megoldás hosszú vagy végtelen adatmegőrzési évtizednyi adatot dolgoztak fel feldolgozott vagy származtatott, az adatok és a nyers adatokat.

Egy tipikus IoT-adatfolyam az alábbi képen látható.

  ![IoT-adatfolyam][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Az Azure Time Series Insights az ipari IoT

Az aktuális IoT fekvő változatos. Ügyfeleink a gyártási, a gépjárműipar, energia, segédprogramok, okosépületek és tanácsadási iparágak kiterjednek. Forgatókönyvek között megtalálható az ad hoc adatfeltárás, ahol az adatok alakját az ismeretlen. Funkciók működési elemzés is között a működési hatékonyság növelésén sematikus, vagy explicit módon modellezett adatok. Ezekben az esetekben általában egymás mellett, és támogatja a különböző használati helyzetekhez. Platform-képességeket, ipari IoT a vállalatok és a digitális revolution sikeréhez kulcsfontosságú, hogy a következők:

- Többrétegű tárolás, online és offline is. 
- Évtizednyi alatt az idősorozat-adatok tárolásának képessége. 
- Lehetővé teszi az explicit módon modellezheti és az operatív adatelemzési eszköz-alapú lekérdezések optimalizálása.

A Time Series Insights egy átfogó, teljes körű platform--szolgáltatásként IoT-adatok feltárása és az operational insights szánt. A Time Series Insights egy teljes körűen felügyelt felhőszolgáltatás, IoT-méretű idősoros adatainak elemzésére kínál.

Nyers adatok egy séma nélküli, memórián belüli tárolóban tárolhatja. Ezután elvégezheti, interaktív ad hoc lekérdezéseket egy elosztott lekérdezési motor és az API segítségével. Ellenőrizze a gazdag felhasználói élmény használatával jeleníthet meg több milliárd eseményt másodpercek alatt. Tudjon meg többet a [adatok feltárása képességek](./time-series-insights-overview.md).

A Time Series Insights jelenleg előzetes verzióban az operational insights-képességeket is kínál. Interaktív adatáttekintés és operatív adatelemzési használhatja a Time Series Insights származtassa tovább kamatoztatását IoT-eszközök begyűjtött adatokat. Az előzetes ajánlat támogatja:

* Egy skálázható, teljesítmény és költség optimalizált idősorozat-adatok tárolására. A felhőalapú IoT-megoldás is trend évnyi idősorozat-adatok másodpercek alatt.
* A szemantikai modell támogatása, amely leírja a tartomány és a származtatott, és nem származtatott jelekkel, eszközök és eszközökről érkező társított metaadatokat.
* Továbbfejlesztett felhasználói felületet nyújt, amely az eszközintelligencia-alapú adatelemzési információk ötvözi a gazdag, ad-hoc adatelemzést. Ez a kombináció meghajtók, üzleti és működési intelligenciával.
* Fejlett gépi tanulási és elemzési eszközök integrációja. Eszközök közé tartozik az Azure Databricks, az Apache Spark, Azure Machine Learning, a Jupyter notebookok és Power bi-ban. Ezek az eszközök segítségével idősorozat-adatok ügyfélfeladatainak idő kiszolgálókon és a működési hatékonyságot.

Együtt, operational insights és az adatok feltárása az adatfeldolgozási, a storage és a lekérdezés egy egyszerű használatalapú díjszabási modell érhető el. A változó üzleti igények szerint ez a számlázási modell esetén használhatók.

A magas szintű adatfolyam-diagram megjeleníti azokat a frissítéseket.

  ![Főbb képességek][2]

A Time Series Insights főbb ipari IoT funkcióját bevezetésével fő előnyei a következők biztosít.

| | |
| ---| ---|
| **Az IoT-méretű idősoros adatainak többrétegű tárolás** | Egy közös adatfeldolgozási folyamat adatok, és adatokat tárolhatja az interaktív lekérdezések meleg storage-ban. Akkor is adatot tárolhat nagy mennyiségű adatot a ritka elérésű tárolási. Nagy teljesítményű eszköz-alapú előnyeit [lekérdezések](./time-series-insights-update-tsq.md). |
| **Nyers telemetriát contextualize és elemzéseket az eszközintelligencia-alapú Idősorozat-modell** | A leíró a nyers telemetriaadatok contextualize [Idősorozat-modell](./time-series-insights-update-tsm.md). Az eszköz alapú lekérdezések magas teljesítmény és költség optimalizált gazdag operatív adatelemzési származik. |
| **Más megoldások zökkenőmentes és folyamatos integráció** |  A Time Series Insights adatai [tárolt](./time-series-insights-update-storage-ingress.md) a nyílt forráskódú Apache Parquet-fájlokat. Ez az integráció az adatok megoldásait, első vagy harmadik féltől származó,-e egyszerű végpontok közötti forgatókönyvek esetén. Ilyen például, amikor üzleti intelligencia, a fejlett gépi tanulási és a prediktív elemzések. |
| **Közel valós idejű adatok feltárása** | A [Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md) felhasználói élményt biztosít a betöltési folyamat révén minden adatstreamelési vizualizációját. Hamarosan az eseményforrás a csatlakozás után megtekintheti, ismerje meg, és események adatainak lekérdezéséhez. Így ellenőrizheti, hogy e eszköz elvárt bocsát ki adatokat. Egy IoT-eszköz állapotát, hatékonyságot és általános hatékonyságát is figyelheti. |
| **Kiváltó elemzések és rendellenességészlelés észlelése** | A [Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md) tartson, és mentse a többlépéses, alapvető okok elemzése nézeteket és szempontjából egyaránt támogatja. Az Azure Stream Analytics együttesen a Time Series Insights segítségével közel valós időben észlelheti a riasztások és a rendellenességeket. |
| **A Time Series Insights-platformra épülő egyéni alkalmazásokat** | Time Series Insights támogatja a [JavaScript SDK](./tutorial-explore-js-client-lib.md). Az SDK-t biztosít a gazdag vezérlők és egyszerűen hozzáférhet a lekérdezéseket. Az SDK használatával egyéni IoT alkalmazásokat a Time Series Insights a saját üzleti igényeinek. Emellett használhatja a Time Series Insights [lekérdezési API-k](./time-series-insights-update-tsq.md) közvetlenül az egyéni IoT-alkalmazások meghajtó adatokat. |

## <a name="next-steps"></a>További lépések

Ismerkedés az Azure Time Series Insights előzetes verziója:

> [!div class="nextstepaction"]
> [A rövid útmutató elolvasása](./time-series-insights-update-quickstart.md)

További információ a használati esetek:

> [!div class="nextstepaction"]
> [Az Azure Time Series Insights előzetes verzió használati esetek](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png