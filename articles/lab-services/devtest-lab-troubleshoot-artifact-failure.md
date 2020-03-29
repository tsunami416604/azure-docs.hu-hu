---
title: Műtermék-hibák diagnosztizálása egy Azure DevTest Labs virtuális gépen
description: A DevTest Labs olyan információkat biztosít, amelyek segítségével diagnosztizálhatja a műtermék-hibákat. Ez a cikk bemutatja, hogyan háríthatók el a műtermékek hibái.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 7229f1ee4061eb38b7c6da09df21102ab302ab42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760317"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Műtermék-hibák diagnosztizálása a laborban 
Miután létrehozott egy műtárgyat, ellenőrizheti, hogy sikerült-e vagy sem. Az Azure DevTest Labs må±termÃ©k-k må. Néhány lehetősége van a Windows virtuális gépek műterméknapló-adatainak megtekintésére:

* Az Azure Portalon
* A virtuális gép

> [!NOTE]
> Annak érdekében, hogy a hibák megfelelőazonosítása és magyarázata, fontos, hogy a műtermék megfelelő szerkezettel rendelkezik. Az összetevők helyes felépítéséről az [Egyéni összetevők létrehozása című](devtest-lab-artifact-author.md)témakörben talál további információt. Ha egy megfelelően strukturált összetevőt szeretne látni, tekintse meg a [Test paramétertípusok](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) összetevő.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Műtermék-hibák elhárítása az Azure Portal használatával

1. Az Azure Portalon az erőforrások listájában válassza ki a labor.
2. Válassza ki a Windows virtuális gép, amely tartalmazza a műtermék, amely meg szeretné vizsgálni.
3. A bal oldali panel **ÁLTALÁNOS**területén válassza **az Eltérések**lehetőséget. Megjelenik az adott virtuális géphez társított összetevők listája. A műtermék neve és a műtárgy állapota jelzésre került.

   ![Összetevő állapota](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Jelöljön ki egy **sikertelen** állapotot megjelenítő műterméket. Megnyílik az ereklye. Megjelenik egy bővítményüzenet, amely a műtermék hibájának részleteit tartalmazza.

   ![Műtermék hibaüzenet](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>A műtermék-hibák elhárítása a virtuális gépen belülről

1. Jelentkezzen be a diagnosztizált összetevőt tartalmazó virtuális gépbe.
2. Nyissa meg a C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status webhelyet, ahol az *1.9* az Azure Custom Script Extension verziószáma.

   ![Az állapotfájl](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Nyissa meg az **állapotfájlt.**

A **naplófájlok Linux os** virtuális gépen történő megkeresésével kapcsolatos tudnivalókat a következő cikkben találja: [Az Azure Custom Script Extension 2 verziója Linux-alapú virtuális gépekkel](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Virtuális gép csatlakoztatása meglévő Active Directory-tartományhoz a DevTest Labs Erőforrás-kezelő sablonjának használatával](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>További lépések
* További információ [agtár hozzáadásáról a laborhoz.](devtest-lab-add-artifact-repo.md)

