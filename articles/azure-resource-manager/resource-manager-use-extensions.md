---
title: Telepítés utáni konfiguráció bővítmények használatával
description: Ismerje meg, hogyan használhatók a Azure Resource Manager-sablonok bővítmények a telepítés utáni konfigurációk biztosításához.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: c7896efb453cac478202efedb268c4bc838aef3a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150372"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Telepítés utáni konfigurációk megadása bővítmények használatával

A sablon-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-erőforrásokon. A legnépszerűbb az egyik a virtuálisgép-bővítmények. Lásd: a [Windows rendszerhez készült virtuálisgép-bővítmények és-szolgáltatások](../virtual-machines/extensions/features-windows.md), valamint [a linuxos virtuálisgép-bővítmények és-funkciók](../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Bővítmények

A meglévő bővítmények a következők:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft. HDInsight-fürtök/-bővítmények](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Microsoft.Sql servers/databases/extensions](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Az elérhető bővítmények kereséséhez keresse meg a [sablon hivatkozását](https://docs.microsoft.com/azure/templates/). A **szűrés cím szerint**mezőben adja meg a **bővítményt**.

A bővítmények használatáról további információt a következő témakörben talál:

- [Oktatóanyag: virtuálisgép-bővítmények üzembe helyezése Azure Resource Manager-sablonokkal](./resource-manager-tutorial-deploy-vm-extensions.md).
- [Oktatóanyag: SQL BACPAC-fájlok importálása Azure Resource Manager-sablonokkal](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: virtuálisgép-bővítmények üzembe helyezése Azure Resource Manager-sablonokkal](./resource-manager-tutorial-deploy-vm-extensions.md)
