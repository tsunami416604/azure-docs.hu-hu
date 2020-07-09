---
title: Telepítés utáni konfiguráció bővítmények használatával
description: Ismerje meg, hogyan használhatók a Azure Resource Manager-sablonok bővítmények a telepítés utáni konfigurációk biztosításához.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: be55138a2aa6dc0552c7556438ffd43705687c87
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86055046"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Telepítés utáni konfigurációk megadása bővítmények használatával

A sablon-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-erőforrásokon. A legnépszerűbb az egyik a virtuálisgép-bővítmények. Lásd: a [Windows rendszerhez készült virtuálisgép-bővítmények és-szolgáltatások](../../virtual-machines/extensions/features-windows.md), valamint [a linuxos virtuálisgép-bővítmények és-funkciók](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Bővítmények

A meglévő bővítmények a következők:

- [Microsoft. számítás/virtualMachines/bővítmények](/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft. számítási virtualMachineScaleSets/bővítmények](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft. HDInsight-fürtök/-bővítmények](/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft. SQL-kiszolgálók/adatbázisok/bővítmények](/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft. Web/Sites/siteextensions](/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Az elérhető bővítmények kereséséhez keresse meg a [sablon hivatkozását](/azure/templates/). A **szűrés cím szerint**mezőben adja meg a **bővítményt**.

A bővítmények használatáról további információt a következő témakörben talál:

- [Oktatóanyag: virtuálisgép-bővítmények üzembe helyezése Azure Resource Manager-sablonokkal](template-tutorial-deploy-vm-extensions.md).
- [Oktatóanyag: SQL BACPAC-fájlok importálása Azure Resource Manager-sablonokkal](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Virtuális gépi bővítmények üzembe helyezése Azure Resource Manager-sablonokkal](template-tutorial-deploy-vm-extensions.md)
