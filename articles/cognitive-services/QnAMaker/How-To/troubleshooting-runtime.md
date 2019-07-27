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
ms.date: 01/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 94a3dfd9a3560321d7e2753ccd385fb1a5323107
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559925"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Hibaelhárítási tippek a QnA Maker szolgáltatást és a futásidejű támogatása
QnAMaker, a felhasználó az Azure-fiókban lévő üzemeltetett összetevőket foglalja magában. Hibakeresés megkövetelheti, hogy a felhasználók számára a QnAMaker Azure erőforrások kezeléséhez, vagy adja meg a QnAMaker támogatási csapatával a telepítés után további információt.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>QnAMaker modul legújabb frissítéseinek beszerzése
QnAMaker futásidejű része az Azure App Service üzembe helyezve, amikor Ön [QnAMaker szolgáltatás létrehozása](./set-up-qnamaker-service-azure.md) az Azure Portalon. A futtatókörnyezet rendszeres időközönként végrehajtott frissítéseket. A QnA Maker App Service az automatikus frissítési módban az április 2019-es hely kiterjesztésének kiadása után (5 + verzió). Ez már úgy van kialakítva, hogy a frissítések során nulla állásidőt lehessen felügyelni. A jelenlegi verzióját a következő címen tekintheti meg: https://www.qnamaker.ai/UserSettings. Ha a verzió régebbi, mint az 5. x verzió, újra kell indítania a App Service, hogy alkalmazza a legújabb frissítéseket a QnAMaker telepítésére.

1. Keresse meg a QnAMaker szolgáltatást (erőforráscsoport) a [Azure Portalon](https://portal.azure.com)

    ![QnAMaker Azure-erőforráscsoport](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Kattintson a az App Service-ben, és nyissa meg az Áttekintés szakaszban

     ![QnAMaker App Service-ben](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Indítsa újra az App Service-ben. Néhány másodpercen belül teljesítéséhez. Vegye figyelembe, hogy az alárendelt alkalmazások robotokat készíthet a QnAMaker szolgáltatáson ebben az időszakban indítsa újra a végfelhasználók számára elérhető lesz.

    ![QnAMaker az App Service-újraindítás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>A QnAMaker szolgáltatás gazdagépneve beszerzése
QnAMaker szolgáltatás gazdagépneve hasznos hibakeresés céljából forduljon a támogatási QnAMaker vagy a uservoice-on. Az állomásnév az űrlap egy URL-címet: https:// *{állomásnév}* . azurewebsites.NET webhelyet.
    
1. Keresse meg a QnAMaker szolgáltatást (erőforráscsoport) a [Azure Portalon](https://portal.azure.com)

    ![QnAMaker Azure-erőforráscsoportot az Azure Portalon](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Kattintson az App Service

     ![Válassza ki a QnAMaker App Service-ben](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Az állomásnév URL-cím áll rendelkezésre az Áttekintés szakaszban

    ![QnAMaker állomásnév](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A tudásbázissal kapcsolatos kérdések fejlesztése az aktív tanulással](./improve-knowledge-base.md)
