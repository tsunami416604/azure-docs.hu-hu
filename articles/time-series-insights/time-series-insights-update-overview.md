---
title: Az Azure Time Series Insights áttekintése |} A Microsoft Docs
description: Az Azure Time Series Insights áttekintése
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 12/05/2018
ms.openlocfilehash: 9843a01ed3c96b362e17718e9035c378da6c3cf2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083393"
---
# <a name="azure-time-series-insights-overview"></a>Az Azure Time Series Insights áttekintése

Az Azure Time Series Insights (TSI) egy teljes körű Platform A – szolgáltatásként nyújtott betöltési, dolgozza fel, tárolása és lekérdezése rendkívül contextualized, a time series-optimalizált, amely ideális adatok feltárása ad hoc, valamint a működési elemzés IoT-méretű adatokra. Az Azure TSI szolgáltatás egyedileg bővíthető és testre szabott ajánlat, hogy megfelel-e a széles körű kell ipari IoT központi telepítések.

## <a name="video"></a>Videó

Ebben a videóban áttekintést az Azure Time Series Insights (előzetes verzió) egy felhőalapú IoT-elemzési platform biztosítunk.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="defining-iot-data"></a>IoT-adatok meghatározása

IoT-adatok az eszköz nagy szervezetek számára érhető el "ipari" adatok. IoT-adatok gyakran azért magas strukturálatlan, mert azt egy széles-tartomány az eszközök, például a hőmérséklet, a mozgásban lévő, a páratartalmat viszonylag zajos mérések rögzítő küldte. Ezenkívül ezek adatfolyamok gyakran jellemző jelentős hézagok, sérült üzenetek és olvasmányok false (hamis). Ezek a Streamek adatait kell törölni minden olyan elemző előtt. IoT-adatot a gyakran csak abban a környezetben, például a CRM és ERP belső származó további streamadat típusú bemenetek jelentéssel bíró) vagy külső adatforrásokból (például időjárási vagy helyet).

Ennek eredményeképpen az adatokat csak elhanyagolható töredékéért működési és üzleti célokat szolgál beolvasása. Az ilyen adatok üzleti jelentéskészítési és elemzési egységes, átfogó, jelenlegi és megfelelő információkat nyújt. Bekapcsolása folyamatban IoT gyűjtött adatokat döntéstámogató elemzésekké megköveteli, néhány főbb képességei:

* Adatfeldolgozási törléséhez szűrése, interpolálja, átalakítása és adatok előkészítése elemzésre
* Keresse meg és megérteni az adatokat (a normalizálása, és az adatok contextualize) struktúra
* Feldolgozott (származtatott), valamint a nyers adatokat érdemes évtizedes hosszú / végtelen adatmegőrzési költséghatékony tárolás

Tipikus IoT-adatfolyam a következőképpen ábrázolható:

  ![IoT-adatfolyam][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Az Azure Time Series Insights az ipari IoT

Az aktuális IoT fekvő változatos. Ügyfeleink a gyártási, a gépjárműipar átfedés, energia, segédprogramok, okosépületek és tanácsadási iparágak tartalmazza. Forgatókönyvek között megtalálható az ad hoc adatfeltárás, ahol az adatok alakját a működési hatékonyság növelésén sematikus (explicit módon modellezett) adatok ismeretlen, valamint működési elemzés. Ezekben az esetekben általában léteznek egymás mellett, és támogatja a különböző használati helyzetekhez. A sikeres kulcs platform képességek, például az egyre többrétegű tárolás (meleg és hideg), a évtizedes alatt az idősorozat-adatok tárolására, valamint az explicit módon modellezheti, és az operatív adatelemzési eszköz-alapú lekérdezések optimalizálása ipari IoT a vállalatok és a digitális revolution.

Az Azure TSI egy átfogó teljes körű Platform, A szolgáltatás ajánlatot is IoT-adatok feltárása, valamint az operational insights. A TSI elemzése IoT-méretű idősoros adatainak teljes körűen felügyelt felhőszolgáltatást kínál.

Ügyfelek nyers adatokat tárolhat egy séma nélküli, a memória, tároló. Ügyfelek hajthat végre interaktív ad hoc lekérdezéseket egy elosztott lekérdezési motor keresztül, és API-t kihasználva a gazdag felhasználói élmény a milliárd jelenítenek meg másodpercben. Tudjon meg többet a [adatok feltárása képességek](./time-series-insights-overview.md).

A TSI jelenleg előzetes verzióban az operational insights-képességeket is kínál. Interaktív adatfeltárás és operatív adatelemzési TSI használatát teszi lehetővé tovább kamatoztatását IoT-eszközök gyűjtött adatokat nyer. Pontosabban az előzetes ajánlat támogatja az alábbi fő lehetőségeket:

* Egy skálázható, teljesítmény és költség optimalizált idő sorozat adattár, amely lehetővé teszi, hogy a felhőalapú IoT-megoldás trend évnyi idősorozat-adatok másodpercek alatt.
* Szemantikaimodell-támogatás, amellyel ismertetheti az eszközök származtatott és nem származtatott jeleihez társított tartományokat és metaadatokat.
* Továbbfejlesztett felhasználói felületet nyújt, amely az eszközintelligencia-alapú adatelemzési információk ötvözi a gazdag, ad-hoc adatelemzést a üzleti és működési intelligenciával
* Az integrációt a fejlett gépi tanulási és elemzési eszközökkel, mint például az Azure Databricks, az Apache Spark, Azure Machine Learning, a Jupyter notebooks, a Power bi-ban, hogy az ügyfelek idősorozat-adatok ügyfélfeladatainak idő kiszolgálókon és a működési hatékonyságot stb.

Egy egyszerű használatalapú díjszabási modellje az adatok feldolgozása, a storage és a lekérdezés, a változó üzleti igényeinek megfelelően sokkal jobban skálázható modell ügyfelek helyezzék együtt operational insights és az adatok feltárására érhető el.

Az alábbi, a magas szintű adatok folyamatábrája, és a frissített funkciók:

  ![Főbb képességek][2]

Kulcs ipari IoT képességek bevezetésével az Azure TSI elérhetővé fő előnyei a következők:

| | |
| ---| ---|
| **Az IoT-méretű idősoros adatainak többrétegű tárolás** | Egy közös adatfeldolgozási folyamat adatok, és rendelkező ügyfeleink az képessége tároló adatainak meleg tárolási interaktív lekérdezések és/vagy ritka elérésű tárolási nagy mennyiségű adat tárolásához. Ügyfelek kihasználhatják a nagy teljesítményű, eszköz-alapú, [lekérdezések](./time-series-insights-update-tsq.md). |
| **A nyers telemetriát contextualizing és eszköz-alapú insights származtatás Idősorozat-modell** | Ügyfeleink a leíró a nyers telemetriaadatok is contextualize [Idősorozat-modell](./time-series-insights-update-tsm.md) és az eszköz alapú lekérdezések magas teljesítmény és költség optimalizált gazdag operatív adatelemzési származtassa. |
| **Zökkenőmentes együttműködés más data-megoldások** | Mivel az adatok az Azure TSI [tárolt](./time-series-insights-update-storage-ingress.md) nyílt forráskódú Apache Parquet-fájlokat, az ügyfelek egyszerűen integrálható más megoldások (első vagy harmadik féltől származó) teljes körű forgatókönyvek, beleértve az üzleti intelligencia, a speciális a gép tanulás, prediktív elemzés, stb. |
| **Közel valós idejű adatok feltárása** | [Az Azure TSI explorer](./time-series-insights-update-explorer.md) felhasználói élményt biztosít a betöltési folyamat révén minden adatstreamelési vizualizációját. Hamarosan az eseményforrás a csatlakozás után ügyfelek megtekintheti, ismerje meg, és e kibocsátó a várt adatokat, és egy IoT-eszköz állapotát, hatékonyságot és általános hatékonyságát figyelési ellenőrzésével kapcsolatos esemény adatokat kérdezhet le. |
| **Kiváltó elemzések és rendellenességészlelés észlelése** | [Az Azure TSI explorer](./time-series-insights-update-explorer.md) támogatja a viselkedési szabályzattal, és mentse a több lépésből álló, kiváltó okokat, elemzési és szempontjából egyaránt nézeteket. Az Azure Stream Analytics együttesen ügyfelek használhatják az Azure TSI a riasztások és közel valós idejű rendellenességek észlelése. |
| **A TSI-platform-on egyéni alkalmazásokat készíthet** | Az Azure TSI támogatja a [JavaScript SDK](./tutorial-explore-js-client-lib.md). Az SDK-t biztosít a gazdag vezérlők és egyszerűen hozzáférhet a lekérdezéseket. Az SDK lehetővé teszi, hogy az ügyfelek számára, hogy az adott üzleti igényeinek megfelelően egyéni IoT-alkalmazások épülő Azure TSI. Ügyfelek is használhatják az Azure TSI [lekérdezési API-k](./time-series-insights-update-tsq.md) közvetlenül az egyéni IoT-alkalmazások meghajtó adatokat. |

## <a name="next-steps"></a>További lépések

Készen áll az Azure TSI (előzetes verzió) használatának első lépései:

> [!div class="nextstepaction"]
> [A rövid útmutató elolvasása](./time-series-insights-update-quickstart.md)

További információ a használati esetek:

> [!div class="nextstepaction"]
> [Az Azure TSI alkalmazási helyzetek](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png