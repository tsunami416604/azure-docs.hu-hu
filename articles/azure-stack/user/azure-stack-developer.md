---
title: Alkalmazások fejlesztése az Azure Stackhez |} A Microsoft Docs
description: Fejlesztési szempontok prototípus-készítés alkalmazásokhoz az Azure Stackben
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: a6f89f9a7e5960e4749c14fc9a4adb648f6781f4
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054087"
---
# <a name="develop-for-azure-stack"></a>Fejlesztés az Azure Stackhez

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Kezdheti alkalmazások fejlesztésével, még akkor is, ha nem rendelkezik az Azure Stack környezettel való hozzáférést. Azure Stack biztosít a Microsoft Azure-szolgáltatások, amelyek a helyi adatközpontban futnak, mert segítségével hasonló eszközökkel és folyamatokkal szemben az Azure Stack fejleszthet, mint az Azure-ral. Az egyes előkészítése, és a következő témakörök útmutatása használhatja az Azure az Azure Stack-környezet emulációjához.

* Az Azure-ban létrehozhat az Azure Resource Manager-sablonok, amelyek az Azure Stack üzembe helyezhető. Lásd: [sablon szempontok](azure-stack-develop-templates.md) útmutatást annak biztosítása érdekében a hordozhatóság sablonok fejlesztéséhez.
* Az Azure és az Azure Stack közötti a szolgáltatás rendelkezésre állása és service versioning különbségek vannak. Használhatja a [Azure Stack-házirendmodul](azure-stack-policy-module.md) , hogy korlátozza azokat az Azure service rendelkezésre állását és az erőforrás elérhető az Azure Stackben. Szolgáltatások korlátozásra biztosítja, hogy az alkalmazások támaszkodjon az Azure Stackben elérhető szolgáltatások.
* A [Azure Stack gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates) gyakori forgatókönyv példák azt mutatják be, hogyan hozhat létre a sablonok, az Azure és az Azure Stackben is telepíthető.
