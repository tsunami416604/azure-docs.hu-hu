---
title: Üzletmenet folytonosságát biztosító terve – QnA Maker
titleSuffix: Azure Cognitive Services
description: Az üzletmenet folytonosságát biztosító terve elsődleges célja, hogy hozzon létre egy rugalmas Tudásbázis végpontot, amely idő leállás nélkül a robot vagy az azt használó alkalmazás biztosítja.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 01/14/2018
ms.author: tulasim
ms.openlocfilehash: 28d2e9ce16106a1995702bd908825d9aa75f06bd
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263866"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>A QnA Maker szolgáltatás üzleti folytonossági terv létrehozása

Az üzletmenet folytonosságát biztosító terve elsődleges célja, hogy hozzon létre egy rugalmas Tudásbázis végpontot, amely idő leállás nélkül a robot vagy az azt használó alkalmazás biztosítja.

![A QnA Maker bcp terv](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A magas szintű idea fent jelölt a következőképpen történik:

1. Állítsa be a két párhuzamos [QnA Maker szolgáltatás](../How-To/set-up-qnamaker-service-azure.md) a [Azure párosított régióiról](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Szinkronizálja az elsődleges és másodlagos az Azure search-indexeket. A GitHub-minta használatát [Itt](https://github.com/pchoudhari/QnAMakerBackupRestore) hogyan indexek az Azure backup-visszaállítás.

3. Készítsen biztonsági másolatot az Application Insights használatával [a folyamatos exportálás](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Ha az elsődleges és másodlagos vermek hoztak létre, az [a traffic manager](https://docs.microsoft.com/azure/traffic-manager/) konfigurálja a végpontokat, és a egy útválasztási módszer beállítása.

5. Hozzon létre egy SSL-tanúsítványt a traffic manager-végpontot kell. [Az SSL-tanúsítvány kötése](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl) az App services szolgáltatásban.

6. Végül a robot vagy alkalmazás a traffic manager-végpontot.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Válassza ki a kapacitás a QnA Maker telepítés](../Tutorials/choosing-capacity-qnamaker-deployment.md)
