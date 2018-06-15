---
title: QnAMaker hibaelhárítása |} Microsoft Docs
titleSuffix: Azure
description: A QnAMaker futásidejű hibaelhárítása
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 06/04/2018
ms.author: saneppal
ms.openlocfilehash: 23847c81a39ea392b6e64575406c9dc338b852de
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "35349930"
---
# <a name="qnamaker-troubleshooting"></a>QnAMaker hibaelhárítása
A felhasználó Azure-fiók tárolt összetevők QnAMaker foglalja magában. Hibakeresés előfordulhat, hogy a felhasználók a QnAMaker Azure-erőforrások kezelhetők, vagy adjon meg további információt a telepítő QnAMaker támogatási csoport.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Hogyan QnAMaker futásidejű legújabb frissítéseinek beszerzése
QnAMaker futásidejű része az Azure App Service telepítése, amikor Ön [QnAMaker szolgáltatás létrehozása](./set-up-qnamaker-service-azure.md) Azure-portálon. A futtatókörnyezet rendszeresen végrehajtott frissítéseket. A QnAMaker telepítési alkalmazni a legújabb frissítések alkalmazásához újra kell indítani az App Service.
1. Keresse meg a QnAMaker szolgáltatás (erőforráscsoport) a [Azure-portálon](https://portal.azure.com)

    ![QnAMaker Azure erőforráscsoport](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Az App Service kattintson, majd nyissa meg a áttekintés szakaszban

     ![QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Indítsa újra az App service. Akkor kell végeznie néhány másodpercen belül. Vegye figyelembe, hogy az alárendelt alkalmazások botok QnAMaker szolgáltatás létrehozása a újraindítás időszak lesz elérhető a végfelhasználók számára.

    ![QnAMaker App Service-újraindítás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Hogyan kérhet az QnAMaker szolgáltatás állomásneve
QnAMaker szolgáltatás állomásnév esetén hasznos hibakeresés céljából UserVoice vagy QnAMaker támogatási szolgálatához. Az állomásnév megadása egy URL-címet a következő formában: https://*{állomásnév}*. azurewebsites.net.
    
1. Keresse meg a QnAMaker szolgáltatás (erőforráscsoport) a [Azure-portálon](https://portal.azure.com)

    ![QnAMaker Azure erőforráscsoport](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Kattintson az App Service

     ![QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Az állomásnév URL-cím érhető el az-Áttekintés szakaszban

    ![QnAMaker állomásnév](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnAMaker API használata](./upgrade-qnamaker-service.md)
