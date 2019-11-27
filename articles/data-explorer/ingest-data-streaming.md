---
title: Adatfolyamok betöltésének használata az Azure-ba való adatbevitelhez Adatkezelő
description: Ismerje meg, hogyan tölthetők be (betöltési) az Azure Adatkezelő az adatfolyam-feldolgozás használatával.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4f9804ed0e7d6c83a4f6fc732f836fcecce1c2e7
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74548339"
---
# <a name="streaming-ingestion-preview"></a>Folyamatos átvitel (előzetes verzió)

Az adatfolyamok betöltése olyan forgatókönyvek esetén fordul elő, amelyek kis késést igényelnek, és a betöltési idő kevesebb, mint 10 másodperc a különböző mennyiségi adatok esetében. Számos tábla működési feldolgozásának optimalizálására használatos egy vagy több adatbázisban, ahol az egyes táblákba irányuló adatstream viszonylag kicsi (néhány rekord másodpercenként), de a teljes adatfeldolgozási kötet magas (több ezer rekord másodpercenként).

Az adatfolyamok betöltése helyett a klasszikus (tömeges) betöltést használja, ha az adatmennyiség másodpercenként 1 MB-onként nő. A különböző betöltési módszerekkel kapcsolatos további információkért olvassa el az [adatfeldolgozás áttekintése című témakört](/azure/data-explorer/ingest-data-overview) .

> [!NOTE]
> A streaming betöltés nem támogatja a következő funkciókat:
> * [Adatbázis-kurzorok](/azure/kusto/management/databasecursor).
> * [Adatleképezés](/azure/kusto/management/mappings). Csak az [előre létrehozott](/azure/kusto/management/tables#create-ingestion-mapping) adatleképezés támogatott. 

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Jelentkezzen be a [webes felhasználói felületre](https://dataexplorer.azure.com/).
* [Azure adatkezelő-fürt és-adatbázis](create-cluster-database-portal.md) létrehozása

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Folyamatos átvitel engedélyezése a fürtön

1. A Azure Portal nyissa meg az Azure Adatkezelő-fürtöt. A **Beállítások**területen válassza a **konfigurációk**lehetőséget. 
1. A **konfigurációk** ablaktáblán válassza **a** be lehetőséget a **streaming**betöltésének engedélyezéséhez.
1. Kattintson a **Mentés** gombra.
 
    ![folyamatos átvitel](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. A [webes felhasználói felületen](https://dataexplorer.azure.com/)adja meg az adatfolyam-betöltési [szabályzatot](/azure/kusto/concepts/streamingingestionpolicy) olyan táblázat (ok) ra vagy adatbázis (ok) ra, amely a folyamatos átviteli adatot fogja fogadni. 

    > [!NOTE]
    > * Ha a házirend az adatbázis szintjén van meghatározva, a rendszer az adatbázisban lévő összes táblát engedélyezi a folyamatos átvitelhez.
    > * Az alkalmazott házirend csak az újonnan betöltött adatmennyiségre és az adatbázis más tábláira hivatkozhat.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Adatfolyamok betöltésének használata a fürtön tárolt adatbevitelhez

Két támogatott adatfolyam-betöltési típus létezik:

* Az [Event hub](/azure/data-explorer/ingest-data-event-hub) adatforrásként használatos
* Az egyéni betöltéshez olyan alkalmazást kell írnia, amely az Azure Adatkezelő ügyféloldali kódtárainak egyikét használja. Lásd: streaming betöltési [minta](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) egy minta alkalmazáshoz.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Válassza ki a megfelelő adatfolyam-betöltési típust

|   |Event Hubs-eseményközpontok  |Egyéni betöltés  |
|---------|---------|---------|
|Adatfeldolgozási kezdeményezés és a lekérdezéshez rendelkezésre álló adatmennyiség közötti késleltetés   |    hosszabb késleltetés     |   rövidebb késleltetés      |
|Fejlesztési terhelés    |   gyors és egyszerű telepítés, nincs fejlesztési terhelés    |   magas fejlesztési terhelés az alkalmazás számára a hibák kezeléséhez és az adatkonzisztencia biztosításához     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Adatfolyam-betöltés letiltása a fürtön

> [!WARNING]
> A streaming betöltésének letiltása néhány órát is igénybe vehet.

1. A streaming betöltési [szabályzatának](/azure/kusto/concepts/streamingingestionpolicy) eldobása az összes kapcsolódó táblából és adatbázisból. A streaming betöltési házirendjének eltávolítása elindítja a betöltési adatátvitelt a kezdeti tárterületről az oszlopos tárolóban lévő állandó tárolóba (egységekben vagy szegmensekben). Az adatáthelyezés a kezdeti tárolóban tárolt adatmennyiségtől és a fürt PROCESSZORának és memóriahasználat mennyiségétől függően néhány másodperctől akár néhány óráig is tarthat.
1. A Azure Portal nyissa meg az Azure Adatkezelő-fürtöt. A **Beállítások**területen válassza a **konfigurációk**lehetőséget. 
1. A **konfigurációk** ablaktáblán válassza ki a **ki** lehetőséget a **streaming**betöltésének letiltásához.
1. Kattintson a **Mentés** gombra.

    ![Folyamatos átvitel](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Korlátozások

* A streaming betöltési teljesítménye és a kapacitása nagyobb a virtuális gépek és a fürtök méretével. Az egyidejű betöltések legfeljebb 6 betöltésre korlátozódnak. Például 16 Magos SKU esetében, például a D14 és a L16 esetében a maximálisan támogatott terhelés 96 egyidejű betöltés. 2 Magos SKU esetében, mint például a D11, a maximálisan támogatott terhelés 12 egyidejű betöltés.
* A betöltési kérések adatméretre vonatkozó korlátozása 4 MB.
* A sémák frissítései, például a táblák és a betöltési leképezések létrehozása és módosítása akár 5 percet is igénybe vehet a streaming betöltési szolgáltatás számára.
* Az adatfolyamok betöltésének engedélyezése egy fürtön, még akkor is, ha az adatok nem a folyamatos átvitelen keresztül kerülnek betöltésre, a a fürt helyi SSD-lemezének részét képezi a betöltési adatok átviteléhez, és csökkenti a gyors gyorsítótár számára elérhető tárterületet

## <a name="next-steps"></a>Következő lépések

* [Az Azure Adatkezelő lekérdezése](web-query-data.md)
