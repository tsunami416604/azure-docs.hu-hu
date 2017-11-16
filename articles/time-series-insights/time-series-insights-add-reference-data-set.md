---
title: "A referencia-adatkészlet felvétele az Azure idő adatsorozat Insights környezetben |} Microsoft Docs"
description: "Ez a cikk ismerteti a referencia-adatkészlet felvétele az Azure idő adatsorozat Insights környezetben. Referenciaadatok akkor hasznos, csatlakoztatása révén az értékeket a forrásadatokban."
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 7cdcefbd0daec3b7bab59680afa1470624583c74
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Létrehozhat egy hivatkozási adatokat a idő adatsorozat Insights környezet az Azure portál használatával

Ez a cikk ismerteti a referencia-adatkészlet felvétele az Azure idő adatsorozat Insights környezetben. Referenciaadatok akkor hasznos, csatlakoztatása révén az értékeket a forrásadatokban.

Egy referencia-adatkészlet az eseményforrásbeli eseményekkel kibővített elemek gyűjteménye. A Time Series Insights bejövő forgalmat kezelő motorja a referencia-adatkészlet egy elemét csatolja egy eseményforrásbeli eseményhez. Ez a kibővített esemény ezután lekérdezhető. A csatolás a referencia-adatkészletben definiált kulcsokon alapul.

## <a name="add-a-reference-data-set"></a>A referencia-adatkészlet hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a meglévő idő adatsorozat Insights környezetben. Kattintson a **összes erőforrás** a menüben, az Azure portál bal oldalán. Válassza ki az Azure Time Series Insights-környezetet.

3. Az a **környezet topológia** elemcsoportban válasszon **hivatkozási adatkészletek**.

    ![A Time Series Insights referencia-adatkészlet létrehozása](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Válassza ki **+ Hozzáadás** hozzáadása egy új referencia-adatkészlet.

5. Adjon meg egy egyedi **referencia-adatkészlet neve**.

    ![A Time Series Insights referencia-adatkészlet létrehozása – részletesen](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

6. Adja meg a **kulcsnév** az üres mezőre és válassza ki a **típus**. Ilyen nevű és típusú segítségével válassza ki a megfelelő tulajdonságot az eseményeket az eseményforrás a referenciaadatok való csatlakozás a. 

   Ha megadta a kulcsnevet, például **DeviceId** és **karakterlánc**, majd a idő adatsorozat Insights érkező motor keresi a nevű tulajdonság. **DeviceId** típusú **Karakterlánc** meg minden esemény kialakításához, és csatlakozik a. Az eseményhez csatoláshoz egynél több kulcs is megadható. A kulcsnév egyeztetése megkülönbözteti a kis- és nagybetűket.

7. Kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>Következő lépések
* [Referencia-adatok kezelése](time-series-insights-manage-reference-data-csharp.md) programozott módon.
* Az alkalmazásprogramozási felület (API) teljes leírását a [Referencia-adatok API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api) dokumentum tartalmazza.
