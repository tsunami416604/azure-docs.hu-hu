---
title: Üzletmenet-folytonossági terv - QnA Maker
titleSuffix: Azure Cognitive Services
description: Az üzletmenet-folytonossági terv elsődleges célja, hogy hozzon létre egy rugalmas tudásbázis-végpont, amely biztosítja a robot vagy az alkalmazás fogyasztó számára állási idő.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 2100426923418104c31933bf9d1406a6441d3841
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410916"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Üzletmenet-folytonossági terv létrehozása a QnA Maker szolgáltatáshoz

Az üzletmenet-folytonossági terv elsődleges célja, hogy hozzon létre egy rugalmas tudásbázis-végpont, amely biztosítja a robot vagy az alkalmazás fogyasztó számára állási idő.

![QnA Maker bcp terv](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A fent szereplő magas szintű elképzelés a következő:

1. Két párhuzamos [QnA Maker-szolgáltatás](../How-To/set-up-qnamaker-service-azure.md) beállítása az [Azure párosított régióiban.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

2. Tartsa szinkronban az elsődleges és másodlagos Azure-keresési indexeket. A GitHub-minta [itt](https://github.com/pchoudhari/QnAMakerBackupRestore) megtudhatja, hogyan biztonsági másolatot készíteni az Azure-indexek biztonsági mentését.

3. Az Application Insights biztonsági másolatot készít [a folyamatos exportálás sal.](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)

4. Az elsődleges és másodlagos halmok beállítása után a [traffic manager](https://docs.microsoft.com/azure/traffic-manager/) segítségével konfigurálja a két végpontot, és állítson be egy útválasztási módszert.

5. Létre kell hoznia egy Transport Layer Security (TLS), korábbi nevén Secure Sockets Layer (SSL), tanúsítványt a traffic manager végpont. [Kösse meg a TLS/SSL tanúsítványt](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) az alkalmazásszolgáltatásokban.

6. Végül használja a traffic manager végpontot a robot ban vagy az alkalmazásban.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Capactiy kiválasztása](./improve-knowledge-base.md)
