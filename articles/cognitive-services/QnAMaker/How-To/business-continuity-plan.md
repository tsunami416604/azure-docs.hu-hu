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
ms.openlocfilehash: 84f13f7e1d83f1ead00303b694b617d3ba1c8931
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876645"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Üzletmenet-folytonossági terv létrehozása a QnA Maker szolgáltatáshoz

Az üzletmenet-folytonossági terv elsődleges célja, hogy egy rugalmas Tudásbázis-végpontot hozzon létre, amely nem gondoskodik a bot vagy az alkalmazás elfogyasztásának leállásáról.

![QnA Maker BCP-terv](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A fentiekben képviselt magas szintű ötlet a következő:

1. Két párhuzamos QnA Maker- [szolgáltatás](../How-To/set-up-qnamaker-service-azure.md) beállítása az [Azure párosított régiókban](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Tartsa szinkronban az elsődleges és a másodlagos Azure Search-indexeket. A GitHub-minta [](https://github.com/pchoudhari/QnAMakerBackupRestore) használatával megtekintheti, hogyan állíthatja vissza az Azure indexeket.

3. A Application Insights biztonsági mentése [folyamatos exportálással](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Az elsődleges és másodlagos csomagok beállítása után a [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) segítségével konfigurálja a két végpontot, és állítson be útválasztási módszert.

5. Létre kell hoznia egy SSL-tanúsítványt a Traffic Manager-végponthoz. [Az SSL-tanúsítvány kötése](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl) az App Servicesben.

6. Végül használja a Traffic Manager-végpontot a robotban vagy az alkalmazásban.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker üzemelő példány kapacitásának kiválasztása](../Tutorials/choosing-capacity-qnamaker-deployment.md)
