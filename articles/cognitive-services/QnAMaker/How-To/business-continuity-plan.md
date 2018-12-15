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
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 10d3809de590a79b6efa86e3d55fbbe535ea13b6
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413115"
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
