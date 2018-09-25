---
title: Hibaelhárítás – QnAMaker
titlesuffix: Azure Cognitive Services
description: QnAMaker, a felhasználó az Azure-fiókban lévő üzemeltetett összetevőket foglalja magában. Hibakeresés megkövetelheti, hogy a felhasználók számára a QnAMaker Azure erőforrások kezeléséhez, vagy adja meg a QnAMaker támogatási csapatával a telepítés után további információt.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: ad3c01ebf37c8b544830b281144090694eeadfcd
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033727"
---
# <a name="qnamaker-troubleshooting"></a>QnAMaker hibáinak elhárítása
QnAMaker, a felhasználó az Azure-fiókban lévő üzemeltetett összetevőket foglalja magában. Hibakeresés megkövetelheti, hogy a felhasználók számára a QnAMaker Azure erőforrások kezeléséhez, vagy adja meg a QnAMaker támogatási csapatával a telepítés után további információt.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>QnAMaker modul legújabb frissítéseinek beszerzése
QnAMaker futásidejű része az Azure App Service üzembe helyezve, amikor Ön [QnAMaker szolgáltatás létrehozása](./set-up-qnamaker-service-azure.md) az Azure Portalon. A futtatókörnyezet rendszeres időközönként végrehajtott frissítéseket. A alkalmazni a legújabb frissítéseket a QnAMaker beállításai a alkalmazni, újra kell indítani az App Service-ben.
1. Keresse meg a QnAMaker szolgáltatást (erőforráscsoport) a [Azure Portalon](https://portal.azure.com)

    ![QnAMaker Azure-erőforráscsoport](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Kattintson a az App Service-ben, és nyissa meg az Áttekintés szakaszban

     ![QnAMaker App Service-ben](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Indítsa újra az App Service-ben. Néhány másodpercen belül teljesítéséhez. Vegye figyelembe, hogy az alárendelt alkalmazások robotokat készíthet a QnAMaker szolgáltatáson ebben az időszakban indítsa újra a végfelhasználók számára elérhető lesz.

    ![QnAMaker az App Service-újraindítás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>A QnAMaker szolgáltatás gazdagépneve beszerzése
QnAMaker szolgáltatás gazdagépneve hasznos hibakeresés céljából forduljon a támogatási QnAMaker vagy a uservoice-on. Az állomásnév az űrlap egy URL-címet: https://*{állomásnév}*. azurewebsites.NET webhelyet.
    
1. Keresse meg a QnAMaker szolgáltatást (erőforráscsoport) a [Azure Portalon](https://portal.azure.com)

    ![QnAMaker Azure-erőforráscsoport](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Kattintson az App Service

     ![QnAMaker App Service-ben](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Az állomásnév URL-cím áll rendelkezésre az Áttekintés szakaszban

    ![QnAMaker állomásnév](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnAMaker API használata](./upgrade-qnamaker-service.md)
