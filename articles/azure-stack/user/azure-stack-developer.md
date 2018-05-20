---
title: Alkalmazások fejlesztése az Azure verem |} Microsoft Docs
description: Azure-veremben prototípusának alkalmazások fejlesztési szempontjai
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a1b5a90ca40ce2b19186220344b22ec0ae77e34b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="develop-for-azure-stack"></a>Fejlesztés az Azure Stackhez

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Ismerkedés fejleszt alkalmazásokat, akkor is, ha nincs hozzáférése a Azure verem környezethez. Mivel Azure verem nyújt az adatközpontban található futtatása Microsoft Azure-szolgáltatások, hasonló eszközök és folyamatok használhatja fejlesztéséhez szemben Azure verem, mint az Azure-ral. Bizonyos előkészítés, és az útmutató használatával a következő témakörökben található Azure verem környezet emulálni Azure használhatja.

* Azure-ban Azure Resource Manager-sablonok, amelyek központilag telepíthető Azure verem is létrehozhat. Lásd: [sablon szempontok](azure-stack-develop-templates.md) sablonok hordozhatóság biztosításához az útmutatót.
* A szolgáltatás rendelkezésre állása és service versioning Azure és az Azure-verem közötti különbségek vannak. Használhatja a [Azure verem irányelvmodul](azure-stack-policy-module.md) korlátozhatja az Azure-szolgáltatások rendelkezésre állásának és erőforrás típusok elérhető Azure-készletben. Szolgáltatások korlátozási biztosítja, hogy az alkalmazások az Azure veremnek az elérhető szolgáltatások támaszkodnak.
* A [Azure verem gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates) példák gyakori forgatókönyv, amelyek bemutatják, hogyan fejleszthet Azure és az Azure-verem telepített sablonok.
