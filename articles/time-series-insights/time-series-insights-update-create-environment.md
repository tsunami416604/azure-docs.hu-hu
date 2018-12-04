---
title: Az Azure Time Series Insights (előzetes verzió) környezet létrehozása |} A Microsoft Docs
description: Frissítés az Azure Time Series environment létrehozása
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: 7e256f032c67649a4a8b01311e70d6bc9307ae92
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852934"
---
# <a name="provisioning-and-managing-an-azure-time-series-insights-preview-environment"></a>Üzembe helyezése és kezelése az Azure Time Series Insights (előzetes verzió) környezet

Ez a dokumentum ismerteti, hogyan építhet ki és felügyelhet egy új Azure Time Series Insights (előzetes verzió) környezetet az Azure Portalon.

## <a name="overview"></a>Áttekintés

A Time Series Insights frissítést követő kiépítése rövid leírását:

* Az Azure Time Series Insights (TSI) frissítési környezet kiépítése.
* A létrehozási folyamat részeként meg kell adnia egy **Time Series azonosító**. Ez lehet akár **három** (3) kulcsokat. Tudjon meg többet [Time Series azonosítók kiválasztása](./time-series-insights-update-how-to-id.md).
* Amikor üzembe helyezi az Azure TSI frissítési környezet hoz létre a két Azure-erőforrások, a TSI frissítési környezet és a egy Azure Storage általános célú V1 fiók.  
* A jövőben új Azure-ügyfelek csak alapértelmezés szerint engedélyezett az Azure Storage-általános célú V2 fiók kiépítése, ezért támogatjuk, hogy a módosítás esetén.  
* Ne engedélyezze a ritka elérésű vagy archív tulajdonságai a storage-fiókot fog használni.
* Ezután igény szerint kapcsolódhat a frissítési környezet egy Time Series Insight támogatott eseményforrás (például az IoT Hub).
* Itt szeretne biztosítani a **időbélyeg azonosító** tulajdonságot, és adja meg egy egyedi felhasználói csoport, győződjön meg arról, hogy a környezet összes eseményt hozzáfér.
* Után kiépítést, majd igény szerint kezelheti hozzáférési házirendeket és más környezeti attribútumok, amelyek támogatják az üzleti követelményeinek.

## <a name="new-environment-creation"></a>Az új környezet létrehozása

1. Válassza ki `PAYG` származó a **Termékváltozat** legördülő listából. Ön lesz is adjon meg egy környezet neve, jelöl ki, melyik előfizetést és erőforráscsoportot szeretne létrehozni a környezetet a és válassza ki a környezetben található támogatott helyét.  

1. A tárfiók nevének kiválasztásával, és a egy replikációs választott kijelölő hozzon létre egy új Azure Storage-fiókot. Így automatikusan létrehoz egy új Azure Storage általános célú V1 fiókhoz a korábban kiválasztott (előzetes verzió) az Azure TSI-környezet ugyanabban a régióban.  

1. Bemeneti **Time Series azonosító** tulajdonság:

    > [!IMPORTANT]
    > A **Time Series azonosító** a kis-és nagybetűket nem módosítható és nem módosítható a beállítása után.

    További részletek a megfelelő kiválasztásakor **Time Series azonosító** információt [ajánlott eljárások a Time Series ID kiválasztására szolgáló](./time-series-insights-update-how-to-id.md).

    ![environment_details][1]

1. Igény szerint adhat hozzá egy esemény forrása:

    * Az Azure TSI támogatja az Azure IoT Hub és az Event Hubs közül. Csak akkor adhat hozzá egyetlen eseményforrás környezet létrehozáskor történik, míg további eseményforrás később is hozzáadhat. Érdemes annak érdekében, hogy az összes esemény láthatók a TSI egyedi fogyasztói csoport létrehozása. Válasszon egy meglévő fogyasztói csoportot, vagy hozzon létre egy új fogyasztói csoportot, az eseményforrás hozzáadása során.

    * Is megadhatja, hogy a megfelelő **időbélyeg** tulajdonság. Alapértelmezés szerint a TSI használja az üzenet sorba helyezésekor minden eseményforrás, amely nem feltétlenül megfelelő események kötegelési vagy feltöltése a korábbi adatok alapján. Ezért elengedhetetlen adjon meg a kis-és nagybetűket időbélyeg-tulajdonság az eseményforrás hozzáadása során.  

     ![environment_event_sources][2]

1. Tekintse át, és hozzon létre:

    ![environment_review][3]

    Győződjön meg arról, hogy minden rendben!

## <a name="management"></a>Kezelés

Akkor tudja felügyelni a TSI frissített környezet az Azure portal használatával. A TSI ismerő felhasználók közvetlenül a TSI-frissítés magabiztosan érzi, mivel nagy részét a rendszer veszi át verziói között.

Jelentősebb eltérések L1 TSI környezeti és a egy S1 vagy s2 szintű környezetben az Azure portal használatával felügyelni az alábbiak:

* A TSI-Azure portal *áttekintése* panelen:

  * Az Áttekintés panelen használatával az alábbiakat kivéve változatlan marad:

    * Kapacitás eltávolítva, mivel a fogalom esetében nem releváns L1 környezetekben.
    * A **Time Series azonosító** tulajdonsággal bővült. Ez a kiépítés ideje hozzá, és határozza meg, hogy az adatok particionálása egy nem módosítható tulajdonság.
    * Referencia-adatkészletekhez el lesznek távolítva.

* A TSI-Azure portal *konfigurálása* panelen:
  
  * Adatmegőrzési eltávolítva, mivel a megőrzési értékre lesz beállítva korlátlan.

    * További vezérlők hozzáadása Ez a jövőben várhatóan, de egyelőre nem korlátot állít be a.
    * Kapacitás, Számológép és tárolási kapacitása túllépte a viselkedést az összes eltávolítása.

* A TSI-Azure portal *referencia* adatok panel:

  * A teljes panel el lett távolítva, mert a referenciaadatok nem L1 környezet egyik összetevője.

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## <a name="next-steps"></a>További lépések

Olvassa el a TSI tárolási konfigurációt:

> [!div class="nextstepaction"]
> [Az Azure TSI (előzetes verzió) tárolási a bejövő és kimenő](./time-series-insights-update-storage-ingress.md)

További tudnivalók a Time Series modellek:

> [!div class="nextstepaction"]
> [Az Azure TSI (előzetes verzió) TSM](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/environment_details.png
[2]: media/v2-update-provision/environment_event_sources.png
[3]: media/v2-update-provision/environment_review.png