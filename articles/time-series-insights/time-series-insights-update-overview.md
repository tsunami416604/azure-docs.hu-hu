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
ms.date: 11/28/2018
ms.openlocfilehash: 5400d8eeba1983d3137b5145c2f885faee036417
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52853074"
---
# <a name="azure-time-series-insights-overview"></a>Az Azure Time Series Insights áttekintése

Az Azure Time Series Insights (TSI) egy teljes körű Platform A – szolgáltatásként nyújtott betöltési, dolgozza fel, tárolása és lekérdezése rendkívül contextualized, a time series-optimalizált IoT-méretű adatokra. Mint ilyen az Azure TSI ideális az adatok feltárása ad hoc, valamint a működési elemzés. A TSI egy egyedi bővíthető, testre szabott, szolgáltatás, amely az, hogy megfelel-e a széles körű kell az ipari IoT-környezetét.

## <a name="what-is-iot-data"></a>Mi az IoT-adatot?

IoT-adatok az eszköz nagy szervezetek számára érhető el "ipari" adatok.
IoT-adatok gyakran azért magas strukturálatlan, mert azt egy széles-tartomány az eszközök, például a hőmérséklet, a mozgásban lévő, a páratartalmat viszonylag zajos mérések rögzítő küldte.

Ezenkívül ezek adatfolyamok gyakran jellemző jelentős hézagok, sérült üzenetek és olvasmányok false (hamis). Ezek a Streamek adatait kell törölni minden olyan elemző előtt. IoT-adat gyakran csak az első-(például CRM és ERP) vagy harmadik féltől származó további streamadat típusú bemenetek környezetben jelentéssel bíró adatforrásokat (például időjárási vagy helyet).

Ennek eredményeképpen az adatokat csak elhanyagolható töredékéért működési és üzleti célokat szolgál beolvasása. Az ilyen adatok üzleti jelentéskészítési és elemzési egységes, átfogó, jelenlegi és megfelelő információkat nyújt. Bekapcsolása folyamatban IoT gyűjtött adatokat döntéstámogató elemzésekké ezért szükséges néhány főbb képességei:

* Adatfeldolgozási törléséhez szűrése, interpolálja, átalakítása és adatok előkészítése elemzésre
* Keresse meg és megérteni az adatokat (a normalizálása, és az adatok contextualize) struktúra
* Feldolgozott (származtatott), valamint a nyers adatokat érdemes évtizedes hosszú / végtelen adatmegőrzési költséghatékony tárolás

Tipikus IoT-adatfolyam a következőképpen ábrázolható:

  ![IoT-adatfolyam][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Az Azure Time Series Insights az ipari IoT

Az aktuális IoT fekvő változatos. Ez magában foglalja az ügyfeleknek a gyártási, autó, olaj és gáz, power & segédprogramot, okosépületek átfedés és tanácsadási. Forgatókönyvek között megtalálható az ad hoc adatfeltárás, ahol az adatok alakját a működési hatékonyság növelésén sematikus (explicit módon modellezett) adatok ismeretlen, valamint működési elemzés. Ezekben az esetekben általában léteznek egymás mellett, és támogatja a különböző használati helyzetekhez. A sikeres kulcs platform képességek, például az egyre többrétegű tárolás (meleg és hideg), a évtizedes alatt az idősorozat-adatok tárolására, valamint az explicit módon modellezheti, és az operatív adatelemzési eszköz-alapú lekérdezések optimalizálása ipari IoT a vállalatok és a digitális revolution.

Az Azure TSI az IoT-adatok feltárása és is az operational insights átfogó teljes körű Platform, A szolgáltatás ajánlatot. A Time Series Insights egy teljes körűen felügyelt felhőszolgáltatás, IoT-méretű idősoros adatainak elemzésére kínál.

Ügyfelek is nyers adatok tárolása egy séma nélküli, a memóriában tárolja, és interaktív ad hoc lekérdezéseket egy elosztott lekérdezési motor és API-t, valamint kihasználja a gazdag felhasználói élmény milliárd jelenítenek meg másodpercben. Tudjon meg többet a [adatok feltárása képességek](./time-series-insights-overview.md).

A TSI jelenleg nyilvános előzetes verzióban az operational insights-képességeket is kínál. Interaktív adatfeltárás és operatív adatelemzési, együtt a Time Series Insights használatát teszi lehetővé tovább kamatoztatását IoT-eszközök gyűjtött adatokat nyer. Pontosabban a nyilvános előzetes ajánlat az alábbi főbb képességeket támogatja:

* Egy skálázható, teljesítmény- és költség optimalizált idő sorozat adattár, amely lehetővé teszi, hogy a felhőalapú IoT-megoldás trend évnyi idősorozat-adatok másodpercek alatt.
* A szemantikai modell támogatása a tartomány és a származtatott, és nem származtatott jelekkel, eszközök és eszközökről érkező társított metaadatokat.
* Továbbfejlesztett felhasználói felületet nyújt, amely az eszközintelligencia-alapú adatelemzési információk ötvözi a gazdag, ad-hoc adatelemzést a üzleti és működési intelligenciával
* Az integrációt a fejlett gépi tanulási és elemzési eszközökkel, mint például az Azure Databricks, az Apache Spark, Azure Machine Learning, a Jupyter notebooks, a Power bi-ban, hogy az ügyfelek idősorozat-adatok ügyfélfeladatainak idő kiszolgálókon és a működési hatékonyságot stb.

Egy egyszerű használatalapú díjszabási modellje az adatok feldolgozása, a storage és a lekérdezés, a változó üzleti igényeinek megfelelően sokkal jobban skálázható modell ügyfelek helyezzék együtt operational insights és az adatok feltárására érhető el.

Az alábbi, a magas szintű adatok folyamatábrája, és a frissített funkciók:

  ![Főbb képességek][2]

Kulcs ipari IoT képességek bevezetésével az Azure TSI fő előnyei a következők biztosít.

* Az IoT-méretű idősoros adatainak többrétegű tárolás

  * Egy közös adatfeldolgozási folyamat adatok, és az ügyfelek is tárolja az adatokat az interaktív lekérdezések meleg tárolási és/vagy ritka elérésű tárolási nagy mennyiségű adat tárolására, és kihasználhatja a nagy teljesítményű, eszköz-alapú lekérdezések képes.

  * Tárolási rétegek dinamikus útválasztást hamarosan elérhető lesz.

* A nyers telemetriát contextualizing és eszköz-alapú insights származtatás idősorozat-modell

  * Ügyfelek contextualize nyers telemetriaadatok, a leíró idősorozat-modell és gazdag operatív adatelemzési az eszköz alapú lekérdezések magas teljesítmény és költség optimalizált származtassa.

* Zökkenőmentes együttműködés más data-megoldások
  
  * Mivel az adatokat az Azure Time Series Insightsban nyílt forráskódú Apache Parquet-fájlokat tárolja, ügyfelek egyszerűen integrálható más megoldások (első vagy harmadik féltől származó) üzleti intelligencia, fejlett gépi tanulási, beleértve a végpontok közötti forgatókönyvek prediktív elemzés, és így tovább.

* Közel valós idejű adatok feltárása

  * Az Azure Time Series Insights Explorer felhasználói élményt biztosít Vizualizáció minden adat a betöltési folyamat keresztül. Hamarosan az eseményforrás a csatlakozás után ügyfelek megtekintheti, ismerje meg, és e kibocsátó a várt adatokat, és egy IoT-eszköz állapotát, hatékonyságot és általános hatékonyságát figyelési ellenőrzésével kapcsolatos esemény adatokat kérdezhet le.

* Kiváltó elemzések és rendellenességészlelés észlelése

  * Az Azure Time Series Insights Explorer támogatja a mintákat és viselkedési szabályzattal, és mentse a több lépésből álló alapvető okok elemzése szempontjából nézeteket. Az Azure Stream Analytics együttesen ügyfelek használhatják a Time Series Insights riasztásokat és a rendellenességeket közel valós idejű észlelését.

* A Time Series Insights platform egyéni alkalmazásokat hozhat létre

  * Az Azure Time Series Insights JavaScript SDK-t támogatja. a részletes vezérlők és egyszerűen hozzáférhet a lekérdezéseket, amelyekkel az ügyfelek igényeinek megfelelően az egyes vállalatok egyéni IoT-alkalmazások a Time Series Insights-platformra épülő.
  * Ügyfelek is használhatja a Time Series Insights lekérdezési API-k közvetlenül a meghajtó adatainak egyéni IoT-alkalmazásokba.

## <a name="next-steps"></a>További lépések

Készen áll az Azure TSI privát előzetes verzió használatának első lépései:

> [!div class="nextstepaction"]
> [A rövid útmutató elolvasása](./time-series-insights-update-quickstart.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png