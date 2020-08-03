---
author: baanders
description: fájl belefoglalása a szerepkör-hozzárendelés ellenőrzéséhez az Azure digitális Twins telepítésekor
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e651b02bf72ced58b6cba637a68ace3258514176
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405586"
---
A szerepkör-hozzárendelés sikeres beállításának egyik módja, ha megtekinti a [Azure Portal](https://portal.azure.com)Azure Digital Twins-példányának szerepkör-hozzárendeléseit. Nyissa meg az [Azure Digital Twins-példányok](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) portáljának oldalát (ezt a hivatkozást használhatja, vagy megkeresheti a portál keresési sávjában), és kiválaszthatja az ellenőriznie kívánt példány nevét. 

Ezután tekintse meg az összes hozzárendelt szerepkört a *hozzáférés-vezérlés (iam) > szerepkör-hozzárendelések*területen. A felhasználónak az *Azure Digital Twins-tulajdonos (előzetes verzió)* szerepkörrel kell megjelennie a listában. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Az Azure Digital Twins-példány szerepkör-hozzárendeléseinek megtekintése Azure Portal":::