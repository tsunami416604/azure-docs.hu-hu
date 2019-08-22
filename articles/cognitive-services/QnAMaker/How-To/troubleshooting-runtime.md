---
title: Hibaelhárítás – QnAMaker
titleSuffix: Azure Cognitive Services
description: QnAMaker, a felhasználó az Azure-fiókban lévő üzemeltetett összetevőket foglalja magában. Hibakeresés megkövetelheti, hogy a felhasználók számára a QnAMaker Azure erőforrások kezeléséhez, vagy adja meg a QnAMaker támogatási csapatával a telepítés után további információt.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b58a08c4cfa97356be4064456b2e9ec5cceccfbc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876341"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Hibaelhárítási tippek a QnA Maker szolgáltatást és a futásidejű támogatása

A QnAMaker a felhasználó Azure-előfizetésében tárolt erőforrásokból áll. A hibakereséshez a felhasználóknak meg kell adniuk az Azure-QnAMaker erőforrásaikat, vagy további információkat kell megadniuk a QnAMaker támogató csapatnak a telepítéséről.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>QnAMaker modul legújabb frissítéseinek beszerzése

Az QnAMaker Runtime a [QnAMaker szolgáltatás](./set-up-qnamaker-service-azure.md) Azure Portal-ban való létrehozásakor üzembe helyezett Azure app Service része. A futtatókörnyezet rendszeres időközönként végrehajtott frissítéseket. A QnA Maker App Service az április 2019-es hely kiterjesztésének kiadása után automatikus frissítési módban van. Ez már úgy van kialakítva, hogy a frissítések során nulla állásidőt lehessen felügyelni. 

A jelenlegi verzióját a következő címen tekintheti meg: https://www.qnamaker.ai/UserSettings. Ha a verzió régebbi, mint az 5. x verzió, újra kell indítania a App Service a legújabb frissítések alkalmazásához.

1. Keresse meg a QnAMaker szolgáltatást (erőforráscsoport) a [Azure Portalon](https://portal.azure.com)

    ![QnAMaker Azure-erőforráscsoport](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Kattintson a az App Service-ben, és nyissa meg az Áttekintés szakaszban

     ![QnAMaker App Service-ben](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Indítsa újra az App Service-ben. Néhány másodpercen belül teljesítéséhez. Vegye figyelembe, hogy a QnAMaker szolgáltatást használó függő alkalmazások vagy robotok ezen újraindítási időszakban nem lesznek elérhetők a végfelhasználók számára.

    ![QnAMaker az App Service-újraindítás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>A QnAMaker szolgáltatás gazdagépneve beszerzése
QnAMaker szolgáltatás gazdagépneve hasznos hibakeresés céljából forduljon a támogatási QnAMaker vagy a uservoice-on. Az állomásnév a következő formátumú URL-cím: https:// *{hostname}* . azurewebsites.net.
    
1. Keresse meg a QnAMaker szolgáltatást (erőforráscsoport) a [Azure Portalon](https://portal.azure.com)

    ![QnAMaker Azure-erőforráscsoportot az Azure Portalon](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Válassza ki a QnA Maker erőforráshoz társított App Service. A nevek általában azonosak.

     ![Válassza ki a QnAMaker App Service-ben](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Az állomásnév URL-cím áll rendelkezésre az Áttekintés szakaszban

    ![QnAMaker állomásnév](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A tudásbázissal kapcsolatos kérdések fejlesztése az aktív tanulással](./improve-knowledge-base.md)
