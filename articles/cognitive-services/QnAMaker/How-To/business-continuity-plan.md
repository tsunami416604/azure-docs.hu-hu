---
title: Üzletmenet folytonosságát biztosító terve – QnA Maker
titleSuffix: Azure Cognitive Services
description: Az üzletmenet folytonosságát biztosító terve elsődleges célja, hogy hozzon létre egy rugalmas Tudásbázis végpontot, amely idő leállás nélkül a robot vagy az azt használó alkalmazás biztosítja.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: 66d2b4667adaac6fa5d6bba2a5a78af3a0c2c255
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221113"
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
