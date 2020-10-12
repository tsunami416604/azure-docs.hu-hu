---
author: baanders
description: fájl belefoglalása a szerepkör-hozzárendelés ellenőrzéséhez az Azure digitális Twins telepítésekor
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: fdb3bd034d93e623037be9fa0721a805924af5c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88864669"
---
A szerepkör-hozzárendelés sikeres beállításának egyik módja, ha megtekinti a [Azure Portal](https://portal.azure.com)Azure Digital Twins-példányának szerepkör-hozzárendeléseit. Nyissa meg az Azure Digital Twins-példányt a Azure Portalban (ezt megtekintheti az [Azure Digital Twins-példányok](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) oldalán, vagy megkeresheti a nevét a portál keresési sávján).

Ezután tekintse meg az összes hozzárendelt szerepkört a *hozzáférés-vezérlés (iam) > szerepkör-hozzárendelések*területen. A felhasználónak az *Azure Digital Twins-tulajdonos (előzetes verzió)* szerepkörrel kell megjelennie a listában. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Az Azure Digital Twins-példány szerepkör-hozzárendeléseinek megtekintése Azure Portal":::