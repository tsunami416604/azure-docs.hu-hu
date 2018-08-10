---
title: A megoldás sablon ajánlatot a piactéren tesztelése |} A Microsoft Docs
description: Megtudhatja, hogyan tesztelheti a megoldás sablon ajánlat az Azure Marketplace-en.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: ef8f9b5e-b98c-49f3-913f-cdf772c14c12
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2015
ms.author: hascipio; v-divte
ms.openlocfilehash: da1fc4713fd1d832c7ba91226f72cbef63b241bc
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714825"
---
# <a name="test-your-solution-template-offer-in-staging"></a>A megoldás sablon ajánlat tesztelése az átmeneti állapotában
Átmeneti azt jelenti, hogy az ajánlatot egy saját "védőfal" mögött, ahol tesztelheti, és ellenőrizze a működését, mielőtt leküldené azt az éles környezetben üzembe helyezéséhez. Az ajánlat átmeneti üzembe van helyezve a fejlesztőktől volna jelenik meg. Az ajánlat átmeneti környezetbe kell leküldeni minősítéssel kell rendelkezniük.

Az ajánlat elő van készítve, miután tekintheti meg és tesztelje az ajánlatot a [az Azure Portal](https://portal.azure.com/).

Az ajánlat átmeneti üzembe helyezéséhez és teszteléséhez az alábbi lépésekkel a [az Azure Portal](https://portal.azure.com/):

1. Nyissa meg a [közzétételi portál](https://publish.windowsazure.com) > **Megoldássablonok** lap > az ajánlat > **közzététel** > **átmenetileküldése**.
2. Adja meg a használatával tekintse meg és tesztelje ajánlatát Azure-előfizetések listáját.
3. Jelentkezzen be az Azure betekintő portálon, amely az előző lépésben használt előfizetés-azonosítója használatával.
4. Az Azure betekintő portálon az alábbi pontokban vizsgálati legalább egy ciklikus végzi:
   * Győződjön meg arról, hogy marketing-tartalom megjelenik-e megfelelően az Azure piactéren.
   * Teljes körű a topológia üzembe helyezése.
   * Teljesítménytesztelés és terhelési tesztelés végrehajtása.
   * Győződjön meg arról, hogy a topológia betartja az ajánlott eljárásokat.

## <a name="next-steps"></a>További lépések
Ha elégedett az eredménnyel, akkor továbbléphet a végső az ajánlat közzétételi fázis **4. lépés**: [üzembe helyezése az ajánlatot a piactéren](marketplace-publishing-push-to-production.md). Ellenkező esetben hajtsa végre a módosításokat az ajánlatban, és kérjen minősítést újra.

> [!NOTE]
> A tartalmi változások marketing, a hitelesítésszolgáltató nem kötelező.
> 
> 

Lásd: [első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md) egy útmutató, amellyel az összes közzétevői feladatok számára.

