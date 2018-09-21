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
ms.date: 09/19/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 3be22e7f8e69ded8ccc8956cc7fd7c6d71fe5fa1
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2018
ms.locfileid: "46497733"
---
# <a name="develop-for-azure-stack"></a>Fejlesztés az Azure Stackhez

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Kezdheti alkalmazások fejlesztésével, még akkor is, ha nem rendelkezik az Azure Stack környezettel való hozzáférést. Azure Stack biztosít a Microsoft Azure-szolgáltatások, amelyek a helyi adatközpontban futnak, mert segítségével hasonló eszközökkel és folyamatokkal szemben az Azure Stack fejleszthet, mint az Azure-ral. 

## <a name="development-considerations"></a>Fejlesztési szempontok

Az egyes előkészítése, és a következő témakörök útmutatása használhatja az Azure az Azure Stack-környezet emulációjához.

* Az Azure-ban létrehozhat az Azure Resource Manager-sablonok, amelyek az Azure Stack üzembe helyezhető. Lásd: [sablon szempontok](azure-stack-develop-templates.md) útmutatást annak biztosítása érdekében a hordozhatóság sablonok fejlesztéséhez.
* Az Azure és az Azure Stack közötti a szolgáltatás rendelkezésre állása és service versioning különbségek vannak. Használhatja a [Azure Stack-házirendmodul](azure-stack-policy-module.md) , hogy korlátozza azokat az Azure service rendelkezésre állását és az erőforrás elérhető az Azure Stackben. Szolgáltatások korlátozásra biztosítja, hogy az alkalmazások támaszkodjon az Azure Stackben elérhető szolgáltatások.
* A [Azure Stack gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates) gyakori forgatókönyv példák azt mutatják be, hogyan hozhat létre a sablonok, az Azure és az Azure Stackben is telepíthető.

## <a name="next-steps"></a>További lépések

Azure Stack fejlesztői kapcsolatos további információkért lásd a következő cikkeket:

- [Ajánlott eljárások az Azure Resource Manager sablon](azure-stack-develop-templates.md)
- [Az Azure Stack gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates)