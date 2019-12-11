---
title: Üzletmenet-folytonossági terv – QnA Maker
titleSuffix: Azure Cognitive Services
description: Az üzletmenet-folytonossági terv elsődleges célja, hogy egy rugalmas Tudásbázis-végpontot hozzon létre, amely nem gondoskodik a bot vagy az alkalmazás elfogyasztásának leállásáról.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: a31076d17ebaf8020fe4b3f82594b98a7c3bde74
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977454"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Üzletmenet-folytonossági terv létrehozása a QnA Maker szolgáltatáshoz

Az üzletmenet-folytonossági terv elsődleges célja, hogy egy rugalmas Tudásbázis-végpontot hozzon létre, amely nem gondoskodik a bot vagy az alkalmazás elfogyasztásának leállásáról.

![QnA Maker BCP-terv](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A fentiekben képviselt magas szintű ötlet a következő:

1. Két párhuzamos QnA Maker- [szolgáltatás](../How-To/set-up-qnamaker-service-azure.md) beállítása az [Azure párosított régiókban](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Tartsa szinkronban az elsődleges és a másodlagos Azure Search-indexeket. A GitHub [-minta használatával](https://github.com/pchoudhari/QnAMakerBackupRestore) megtekintheti, hogyan állíthatja vissza az Azure indexeket.

3. A Application Insights biztonsági mentése [folyamatos exportálással](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Az elsődleges és másodlagos csomagok beállítása után a [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) segítségével konfigurálja a két végpontot, és állítson be útválasztási módszert.

5. Létre kell hoznia egy SSL (SSL) tanúsítványt a Traffic Manager-végponthoz. [Az SSL-tanúsítvány kötése](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) az App Servicesben.

6. Végül használja a Traffic Manager-végpontot a robotban vagy az alkalmazásban.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [QnA Maker üzemelő példány kapacitásának kiválasztása](../Tutorials/choosing-capacity-qnamaker-deployment.md)
