---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 11abd52681d7c9962af4e5bf0728f97b256223c1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122900"
---
## <a name="create-a-personalizer-azure-resource"></a>Személyre szabó Azure-erőforrás létrehozása

Hozzon létre egy erőforrást a Personalizer számára az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI-n](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) keresztül a helyi gépen. További lehetőségek:

* Kap egy [próbakulcs](https://azure.microsoft.com/try/cognitive-services) érvényes részére 7 napok részére szabad. A regisztráció után elérhető lesz az [Azure-webhelyen.](https://azure.microsoft.com/try/cognitive-services/my-apis/)
* Tekintse meg az erőforrást az [Azure Portalon.](https://portal.azure.com/)

Miután bekapott egy kulcsot a próba-előfizetésvagy erőforrás, hozzon létre két [környezeti változó:](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)

* `PERSONALIZER_RESOURCE_KEY`az erőforráskulcshoz.
* `PERSONALIZER_RESOURCE_ENDPOINT`az erőforrás-végponthoz.

Az Azure Portalon a kulcs- és végpontértékek is elérhetők a **rövid útmutató** lapról.
