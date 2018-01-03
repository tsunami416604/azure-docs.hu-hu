---
title: "Egy Azure DevTest Labs szolgáltatásban virtuális gép összetevő hibáinak diagnosztizálásához |} Microsoft Docs"
description: "Útmutató: Azure DevTest Labs összetevő hibáinak elhárítása."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: v-craic
ms.openlocfilehash: 6c03ce8f91ec688e32e379f1284767db9a45920c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>A tesztkörnyezetben összetevő hibák diagnosztizálása 
Miután létrehozott egy összetevő, ellenőrizheti látható, hogy sikeres volt, vagy nem sikerült. Összetevő-naplók Azure DevTest Labs segítségével diagnosztizálhatja összetevő hibát információkat tartalmaznak. A beállítások megtekintéséhez a összetevő naplózási adatok egy Windows virtuális gép több lehetősége van:

* Az Azure-portálon
* A virtuális gépen

> [!NOTE]
> Győződjön meg arról, hogy hibák helyesen azonosítani és ismertetése, fontos, hogy az összetevő a megfelelő struktúrája. Hogyan megfelelően készítse összetevő kapcsolatos információkért lásd: [egyéni összetevők létrehozása](devtest-lab-artifact-author.md). A megfelelően felépített összetevő például megtekintéséhez tekintse meg a [paramétertípusok tesztelése](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) összetevő.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Összetevő hibák elhárítása az Azure portál használatával

1. Válassza ki a labor erőforrásait, az Azure-portálon.
2. Válassza ki a Windows virtuális Gépet, amely tartalmazza az összetevő, amelyet meg szeretne vizsgálni.
3. A bal oldali panel alatt **általános**, jelölje be **összetevők**. Ezt a virtuális Gépet társított összetevők listája jelenik meg. Neve az összetevő és az összetevő állapotát jelzi.

   ![Összetevő-állapot](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Válassza ki, amely összetevő egy **sikertelen** állapotát. Az összetevő megnyitása. Egy bővítmény üzenet, amely tartalmazza az összetevő a hibával kapcsolatos részletekért jelenik meg.

   ![Összetevő hibaüzenet](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>A virtuális gépekről származó összetevő hibák elhárítása

1. Jelentkezzen be a virtuális Gépet, amely tartalmazza a diagnosztizálása kívánt összetevő.
2. Ugrás a C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, ahol *1.9* Azure egyéni parancsprogramok futtatására szolgáló bővítmény verziószáma.

   ![A állapotfájl](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Nyissa meg a **állapot** fájlt.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Meglévő Active Directory-tartományban a DevTest Labs szolgáltatásban a Resource Manager-sablon használatával egy virtuális gép csatlakoztatása](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [Git-tárház hozzáadása egy laborhoz](devtest-lab-add-artifact-repo.md).

