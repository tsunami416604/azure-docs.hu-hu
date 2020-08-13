---
title: 'Gyors útmutató: biztonsági javaslatok vizsgálata'
description: Vizsgálja meg a biztonsági javaslatokat a IoT biztonsági szolgáltatás Azure Security Centerával.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: mlottner
ms.openlocfilehash: aa241a9108be32f88357065732c6f283d312be6c
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2020
ms.locfileid: "88144211"
---
# <a name="quickstart-investigate-security-recommendations"></a>Gyors útmutató: biztonsági javaslatok vizsgálata


A IoT Azure Security Center általi időben történő elemzése és enyhítése a legjobb módszer a biztonsági testhelyzet javítására és a támadási felület csökkentésére a IoT-megoldásban.

Ebben a rövid útmutatóban megvizsgáljuk az egyes IoT biztonsági javaslatokban elérhető információkat, és megtudhatjuk, hogyan részletezheti és használhatja az egyes javaslatok és a kapcsolódó eszközök részleteit a kockázatok csökkentése érdekében.

Tegyük meg az első lépéseket!

## <a name="investigate-new-recommendations"></a>Új javaslatok vizsgálata

A IoT Hub javaslatok listája megjeleníti a IoT Hub összesített biztonsági javaslatait.

1.  A Azure Portalban nyissa meg **IoT Hub**az   új javaslatokkal vizsgálni kívánt IoT hub.

1.  A **Biztonság**   menüben válassza a **javaslatok**elemet. A IoT Hub összes biztonsági javaslata megjelenik, és az **új**   jelzővel kapcsolatos javaslatok az elmúlt 24 órában is megjelölhetik az ajánlásokat. 

    [![Biztonsági javaslatok vizsgálata az ASC-mel a IoT-hez](media/quickstart/investigate-security-recommendations-inline.png)](media/quickstart/investigate-security-recommendations-expanded.png#lightbox)


1.  Válasszon ki és nyisson meg egy javaslatot a listából, és nyissa meg a javaslat részleteit, és tekintse meg a részletes információkat.

## <a name="security-recommendation-details"></a>Biztonsági javaslat részletei

Nyissa meg az egyes összesített javaslatokat, hogy megjelenjenek a részletes ajánlások leírása, szervizelési lépések, eszköz azonosítója minden olyan eszközhöz, amely egy javaslatot váltott ki. Emellett az ajánlás súlyosságát és a közvetlen vizsgálathoz való hozzáférést is megjeleníti Log Analytics használatával.

1.  Válasszon ki és nyisson meg egy biztonsági javaslatot a **IoT hub**   \>  **biztonsági**   \>  **javaslatok**   listából.

1.  Tekintse át az ajánlás **leírását**, **súlyosságát**, az **eszköz részletes adatait**   az összesítési időszakban. 

1.  Az ajánlási javaslatok áttekintése után a **manuális szervizelési lépés**   utasításait követve javítsa ki az ajánlást okozó problémát. 

    [![Biztonsági javaslatok szervizelése az ASC-mel a IoT-hez](media/quickstart/remediate-security-recommendations-inline.png)](media/quickstart/remediate-security-recommendations-expanded.png#lightbox)


1.  Az adott eszközre vonatkozó javaslat részleteinek megismeréséhez válassza ki a kívánt eszközt a részletezés oldalon.

    [Az ![ eszközökre vonatkozó konkrét biztonsági javaslatok kivizsgálása a IoT](media/quickstart/explore-security-recommendation-detail-inline.png)](media/quickstart/explore-security-recommendation-detail-expanded.png#lightbox)


1.  Ha további vizsgálatra van szükség, a hivatkozás használatával **vizsgálja meg log Analytics ajánlását**   . 


## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan hozhat létre egyéni riasztásokat...

> [!div class="nextstepaction"]
> [Egyéni riasztások létrehozása](quickstart-create-custom-alerts.md)
