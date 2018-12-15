---
title: Adja meg az üzembe helyezés utáni konfigurációk a bővítmények – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Resource Manager-sablon bővítmények biztosíthatja a telepítés utáni.
services: azure-resource-manager
documentationcenter: na
author: mumian
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: eb46966c3a28b3fa4c2b23668109b7c5d23a609b
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53414378"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Adja meg az üzembe helyezés utáni konfigurációk a bővítmények

A sablon a bővítmények olyan kisebb alkalmazásoknál, amelyek az üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosít az Azure-erőforrás. A legnépszerűbb egyik virtuálisgép-bővítmények lesz. Lásd: [virtuálisgép-bővítmények és szolgáltatások Windows](../virtual-machines/extensions/features-windows.md), és [virtuális gépi bővítmények és szolgáltatások Linux](../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Bővítmények

A meglévő bővítmények a következők:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/bővítmények](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Regisztrációra a Microsoft.HDInsight fürtök/bővítmények](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Kiszolgálók és adatbázisok/bővítmények Microsoft.Sql](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Hogy megtudja, az elérhető bővítmények, keresse meg a [sablonreferenciája](https://docs.microsoft.com/azure/templates/). A **szűrés cím szerint**, adja meg **bővítmény**.

Ezek a bővítmények használata című témakörben talál:

- [Oktatóanyag: Üzembe helyezése Azure Resource Manager-sablonokkal a virtuális gépi bővítmények](./resource-manager-tutorial-deploy-vm-extensions.md).
- [Oktatóanyag: Az Azure Resource Manager-sablonok SQL BACPAC-fájlok importálása](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Resource Manager-sablonok virtuálisgép-bővítmények telepítése](./resource-manager-tutorial-deploy-vm-extensions.md)
