---
title: "Alkalmazások fejlesztése az Azure verem |} Microsoft Docs"
description: "Ismerje meg az Azure-veremben prototípusának alkalmazások fejlesztési szempontok"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 062c17173f87eec8e0eaa3f74323cbf8a8f48571
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="develop-for-azure-stack"></a>Fejlesztés az Azure Stackhez
Ismerkedés fejleszt alkalmazásokat, akkor is, ha nincs hozzáférése a Azure verem környezethez. Mivel Azure verem nyújt az adatközpontban található futtatása Microsoft Azure-szolgáltatások, hasonló eszközök és folyamatok használhatja fejlesztéséhez szemben Azure verem, mint az Azure-ral.  Előkészítése és a következő témakörök útmutatása bit az Azure verem környezet emulálni Azure használhatja ki:

* Azure-ban Azure Resource Manager-sablonok, amelyek is telepíthető Azure verem is létrehozhat.  Lásd: [sablon szempontok](azure-stack-develop-templates.md) hordozhatóság biztosításához a sablonokat az útmutatót.
* A szolgáltatás rendelkezésre állása és service versioning Azure és az Azure-verem között eltérés van. Használhatja a [Azure verem irányelvmodul](azure-stack-policy-module.md) korlátozhatja az Azure-szolgáltatások rendelkezésre állásának és erőforrás típusok elérhető Azure-készletben. Választható szolgáltatások korlátozási segítenek az alkalmazás Azure veremnek az elérhető szolgáltatások támaszkodnak.
* A [Azure verem gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates) gyakori forgatókönyv példák a sablonok fejlesztését, Azure és az Azure-verem is telepíthető.


