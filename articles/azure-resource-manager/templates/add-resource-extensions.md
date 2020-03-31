---
title: A telepítés utáni konfiguráció bővítmények használatával
description: Ismerje meg, hogyan használhatja az Azure Resource Manager sablonbővítményeit a telepítés utáni konfigurációk biztosításához.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: b3c4110c8761b3e8daf324d65ac7fa1dcbcdf61f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023497"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>A telepítés utáni konfigurációk biztosítása bővítmények használatával

A sablonbővítmények olyan kis alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-erőforrásokon. A legnépszerűbb a virtuális gép kiterjesztések. Lásd: [Virtuálisgép-bővítmények és -szolgáltatások a Windows rendszerhez,](../../virtual-machines/extensions/features-windows.md)valamint a Virtual machine extensions and features for Linux című [témakört.](../../virtual-machines/extensions/features-linux.md)

## <a name="extensions"></a>Bővítmények

A meglévő bővítmények a következők:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight-fürtök/-bővítmények](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft.Sql kiszolgálók/adatbázisok/bővítmények](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

A rendelkezésre álló bővítmények ről keresse meg a [sablonhivatkozást.](https://docs.microsoft.com/azure/templates/) A **Szűrés cím szerint**mezőbe írja be a **bővítményt.**

A bővítmények használatáról az alábbi témakörökben olvashat:

- [Oktatóanyag: Virtuálisgép-bővítmények üzembe helyezése az Azure Resource Manager-sablonokkal.](template-tutorial-deploy-vm-extensions.md)
- [Oktatóanyag: SQL BACPAC-fájlok importálása Azure Resource Manager-sablonokkal](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Virtuális gépi bővítmények üzembe helyezése Azure Resource Manager-sablonokkal](template-tutorial-deploy-vm-extensions.md)
