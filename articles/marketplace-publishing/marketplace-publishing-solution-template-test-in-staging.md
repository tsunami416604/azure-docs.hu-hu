---
title: "A piactér sablon megoldás ajánlatát tesztelése |} Microsoft Docs"
description: "Megtudhatja, hogyan tesztelheti a megoldás sablon ajánlat az Azure piactéren."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: ef8f9b5e-b98c-49f3-913f-cdf772c14c12
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2015
ms.author: hascipio; v-divte
ms.openlocfilehash: da1fc4713fd1d832c7ba91226f72cbef63b241bc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="test-your-solution-template-offer-in-staging"></a>A megoldás sablon ajánlat tesztelése az átmeneti
Átmeneti azt jelenti, hogy a "védőfal", ahol tesztelése és funkciókat ellenőrzése előtt az üzemi környezetben privát ajánlatát telepítése. Az ajánlat volna egy felhasználói, akik már telepítették az átmeneti jelenik meg. Az ajánlat igazolja a kell előkészítésre továbbít.

Az ajánlat elő van készítve, miután tekintheti meg és tesztelni az ajánlat a [Azure Portal](https://portal.azure.com/).

A lefokozásra szolgáló átmeneti leküldéses és tesztelik azt a következő lépések a [Azure Portal](https://portal.azure.com/):

1. Lépjen a [közzétételi Portáljára](https://publish.windowsazure.com) > **Solution Templates** lapon > az ajánlat > **közzététel** > **átmenetileküldése**.
2. Azure-előfizetések, amelyekkel előzetes megtekintéséhez és tesztelni az ajánlatot listáját tartalmazzák.
3. Jelentkezzen be az Azure betekintő portálon, amelyet az előző lépésben használt előfizetés-azonosító segítségével.
4. Az Azure betekintő portál, az alábbiakban leírt pontokon vizsgálati legalább egy ciklikus végrehajtására:
   * Győződjön meg arról, hogy tartalom marketing jeleníti meg helyesen az Azure piactéren.
   * A topológia-végpontok közötti üzembe helyezése.
   * Hajtsa végre a teljesítmény tesztelése, és magas terhelés tesztelése.
   * Győződjön meg arról, hogy a topológia megfelelő-e az ajánlott eljárásokat.

## <a name="next-steps"></a>Következő lépések
Ha elégedett az eredményeket, majd továbbléphet a végső ajánlat közzétételi fázis **4. lépés**: [központi telepítése az ajánlatot a piactér](marketplace-publishing-push-to-production.md). Ellenkező esetben módosítsa az ajánlatot, és kérjen tanúsítási újra.

> [!NOTE]
> Marketing a tartalmi változások, a hitelesítésszolgáltató szükség.
> 
> 

Lásd: [első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md) való összes közzétevői feladatok.

