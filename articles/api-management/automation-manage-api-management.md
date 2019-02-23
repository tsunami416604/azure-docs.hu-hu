---
title: Kezelheti az Azure API Management az Azure Automationnel
description: Ismerje meg hogyan az Azure Automation szolgáltatást is használható az Azure API Management kezeléséhez.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 642353218960e2e995d717260ae82b6dbd7c2612
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727060"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Kezelés az Azure API Management az Azure Automationnel
Ez az útmutató bemutatja, hogyan használható az Azure API Management kezelésének egyszerűsítéséhez, és az Azure Automation szolgáltatást.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
[Az Azure Automation](https://azure.microsoft.com/services/automation/) leegyszerűsíti a felhőfelügyelet a folyamatok automatizálása révén az Azure-szolgáltatások. Az Azure Automationnel, manuális, ismétlődő, hosszú futású, és sok hibalehetőséget rejtő feladat automatizálható a megbízhatóság, hatékonyságot és a szervezet teljesítések idejét.

Az Azure Automation segítségével egy rendkívül megbízható, magas rendelkezésre állású munkafolyamat-végző összetevőjével, amely igény szerint méretezhető. Az Azure Automation szolgáltatás folyamatokat is lehet kezdődjön manuálisan, 3. rendszerekben, vagy rendszeres időközönként, hogy a feladatok fordulhat elő, pontosan akkor, ha szükséges.

Csökkentheti üzemeltetési terheit és szabadítson fel informatikai és a fejlesztési és üzemeltetési csapatának, amely üzleti értéket hozzáadja a felhőfelügyeleti feladatokat automatikusan Azure Automation által futtatandó váltással munkára.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Hogyan segít az Azure Automation kezelheti az Azure API Management?
Az API Management segítségével kezelhetők az Azure Automationben a [Windows PowerShell-parancsmagok az Azure API Management API](https://docs.microsoft.com/powershell/module/az.apimanagement). Az Azure Automation PowerShell-munkafolyamat parancsfájlokat hajtsa végre az API Management-feladatok a parancsmagok használatával írhat. Ezek a parancsmagok az Azure Automation parancsmagjaival más Azure-szolgáltatások, az összetett feladatok automatizálása Azure-szolgáltatások és a 3. fél rendszerek párosítása is történik.

Íme néhány példa az API Management a PowerShell-lel:

* [Az API Management az Azure PowerShell-minták](https://docs.microsoft.com/azure/api-management/powershell-samples)

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az alapokat, az Azure Automationben, és hogyan használható az Azure API Management kezeléséhez, kövesse az alábbi hivatkozások további.

* Tekintse meg az Azure Automation [kezdeti lépéseket ismertető oktatóanyag](../automation/automation-first-runbook-graphical.md).

