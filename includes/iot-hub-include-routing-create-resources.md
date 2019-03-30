---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 710bb8cba7fbbe4bc9b9fdc52b0767c96f97fe72
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631090"
---
## <a name="create-base-resources"></a>Alap-erőforrások létrehozása

Az üzenet-útválasztással is konfigurálhat, birtokában hozhat létre egy IoT hubot, a storage-fiók és a egy Service Bus-üzenetsorba. Ezek az erőforrások hozható létre az elérhető négy cikkekben esetében ez az oktatóanyag 1. rész: az Azure CLI, Azure PowerShell-lel, az Azure Portalon vagy Azure Resource Manager-sablon.

Ugyanazt az erőforráscsoportot és helyet használja minden erőforráshoz. Majd végén, eltávolíthatja az erőforrásokat egy lépésben az erőforráscsoport törlésével.

A következő szakaszok ismertetik a lépéseket kell végrehajtania.

1. Hozzon létre egy [erőforráscsoportot](../articles/azure-resource-manager/resource-group-overview.md).

2. Hozzon létre egy IoT Hubot az S1 szinten. Adjon hozzá egy fogyasztói csoportot az IoT Hubhoz. A fogyasztói csoportot az Azure Stream Analytics használja az adatok lekérésekor.

   > [!NOTE]
   > Az oktatóanyag elvégzéséhez a fizetős csomagra kell használnia egy Iot hubot. Az ingyenes szinten csak lehetővé teszi egy végpont beállítása, és ehhez az oktatóanyaghoz több végpontot.
   > 

3. Hozzon létre egy standard szintű V1-tárfiókot Standard_LRS-replikációval.

4. Hozzon létre egy Service Bus-névteret és üzenetsort.

5. Hozzon létre egy eszközidentitást ahhoz a szimulált eszközhöz, amely üzeneteket küld a központnak. Mentse a kulcsot a tesztelési fázishoz. (Resource Manager-sablonnal hoz létre, ha ez történik a sablon üzembe helyezése után.)