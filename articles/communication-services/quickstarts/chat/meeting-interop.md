---
title: Ismerkedés a Teams együttműködésével az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services quickstart
description: Ebből a rövid útmutatóból megtudhatja, hogyan csatlakozhat egy csapathoz az Azure kommunikációs csevegési ügyféloldali kódtár használatával
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ea66e4295e8228aa382aa29a46fcca8147dcbc98
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578123"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Gyors útmutató: csatlakozás a csevegési alkalmazáshoz egy csapat-értekezleten

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Ismerkedés az Azure kommunikációs szolgáltatásokkal a csevegési megoldás Microsoft Teams-hez való csatlakoztatásával a JavaScript ügyféloldali kódtár használatával. 

## <a name="prerequisites"></a>Előfeltételek 

1.  [Csapatok üzembe helyezése](https://docs.microsoft.com/deployoffice/teams-install). 
2. Egy működő [csevegési alkalmazás](./get-started.md). 

## <a name="enable-teams-interoperability"></a>Csoportok együttműködésének engedélyezése 

A kommunikációs szolgáltatások felhasználója, amely egy vendégként működő csapathoz csatlakozik, csak akkor férhet hozzá az értekezlet csevegéséhez, ha csatlakoztak a Teams Meeting híváshoz. Tekintse meg a [csapatok együttműködési](../voice-video-calling/get-started-teams-interop.md) dokumentációját, amelyből megtudhatja, hogyan adhat hozzá kommunikációs szolgáltatásokat használó felhasználókat egy csapat-értekezlethez.

A csapatok együttműködési funkciója jelenleg privát előzetes verzióban érhető el. Ha engedélyezni szeretné ezt a funkciót a kommunikációs szolgáltatások erőforrásához, küldjön e-mailt a következő címre acsfeedback@microsoft.com : 
1. A kommunikációs szolgáltatások erőforrását tartalmazó Azure-előfizetés előfizetés-azonosítója. 
2. Csapatának bérlői azonosítója. Ennek a legegyszerűbb módja a csapatra mutató hivatkozás beszerzése és megosztása. 

A funkció használatához mindkét entitás tulajdonos szervezetének tagjának kell lennie. 

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>További lépések

További információért tekintse át a következő cikkeket:

- Tekintse meg a [csevegési hős mintáját](../../samples/chat-hero-sample.md)
- További információ a [csevegés működéséről](../../concepts/chat/concepts.md)
