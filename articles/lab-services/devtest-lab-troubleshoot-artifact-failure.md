---
title: Az összetevők hibáinak diagnosztizálása egy Azure DevTest Labs virtuális gépen | Microsoft Docs
description: Ismerje meg, hogyan lehet elhárítani a Azure DevTest Labs az összetevők hibáit.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 8b7a72c03d41d54aa80505e781b6f6d32cd2a2c0
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991365"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Az összetevők hibáinak diagnosztizálása a laborban 
Miután létrehozott egy összetevőt, megtekintheti, hogy sikeres vagy sikertelen volt-e. A Azure DevTest Labs található összetevők naplói olyan információkat tartalmaznak, amelyek segítségével diagnosztizálhatja az összetevő meghibásodását. A Windows rendszerű virtuális gépek esetében több lehetőség közül választhat:

* A Azure Portal
* A virtuális gépen

> [!NOTE]
> Annak érdekében, hogy a hibák azonosítása és magyarázata sikeres legyen, fontos, hogy az összetevő megfelelő struktúrával rendelkezik. Az összetevők megfelelő létrehozásával kapcsolatos információkért lásd: egyéni összetevők [létrehozása](devtest-lab-artifact-author.md). Ha egy megfelelően strukturált összetevőre vonatkozó példát szeretne látni, tekintse meg a [teszt paraméter típusait](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) .

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Az összetevők hibáinak elhárítása a Azure Portal használatával

1. A Azure Portal az erőforrások listájában válassza ki a labort.
2. Válassza ki azt a Windows rendszerű virtuális gépet, amely tartalmazza a vizsgálni kívánt összetevőt.
3. A bal oldali panelen az **általános**területen válassza azösszetevők elemet. Megjelenik az adott virtuális géphez társított összetevők listája. Az összetevő neve és az összetevő állapota jelezve.

   ![Összetevő állapota](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Olyan összetevőt válasszon, amely egy **sikertelen** állapotot mutat. Megnyílik az összetevő. Megjelenik egy olyan bővítményi üzenet, amely tartalmazza az összetevő hibájának részleteit.

   ![Összetevő hibaüzenete](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Az összetevők hibáinak elhárítása a virtuális gépen

1. Jelentkezzen be a virtuális gépre, amely tartalmazza a diagnosztizálni kívánt összetevőt.
2. Nyissa meg\\a C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension*1,9*\Status, ahol a *1,9* az Azure egyéni szkript bővítmény verziószáma.

   ![Az állapot fájlja](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Nyissa meg az **állapot** fájlt.

A naplófájlok **Linux** rendszerű virtuális gépen való megtalálásával kapcsolatos utasításokért tekintse meg a következő cikket: [Az egyéni Azure script Extension 2. verziójának használata Linux rendszerű virtuális gépekkel](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Virtuális gép csatlakoztatása meglévő Active Directory tartományhoz egy Resource Manager-sablonnal a DevTest Labs szolgáltatásban](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [adhat hozzá git-tárházat](devtest-lab-add-artifact-repo.md)a laborhoz.

