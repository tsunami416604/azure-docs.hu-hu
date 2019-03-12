---
title: Az Azure DevTest Labs szolgáltatásban virtuális gépként az összetevők hibáinak diagnosztizálása |} A Microsoft Docs
description: Az Azure DevTest Labs szolgáltatásban az összetevők hibáinak elhárítását ismereti.
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: d5e03b7645fcacaeba4ccf147d48886cefd33ffb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531441"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>A lab-ben az összetevők hibáinak diagnosztizálása 
Miután létrehozott egy összetevő, akkor ellenőrizheti, hogy akár sikeres, vagy sikertelen volt. Azure DevTest Labs-összetevő naplók segítségével diagnosztizálhatja az összetevő meghibásodása információkat tartalmaznak. Van egy néhány további lehetőség egy Windows virtuális gép összetevő naplózási adatok megtekintése:

* Az Azure Portalon
* A virtuális gépen

> [!NOTE]
> Győződjön meg arról, hogy hibák helyesen azonosítani és ismertetése, fontos, hogy rendelkezik-e a megfelelő struktúra az összetevőben. Megfelelően hozhat létre egy összetevő kapcsolatos információkért lásd: [egyéni összetevők létrehozása](devtest-lab-artifact-author.md). Egy példa a megfelelően felépített összetevő megtekintéséhez tekintse meg a [paramétertípusok tesztelése](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) összetevő.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Az összetevők hibáinak elhárítása az Azure portal használatával

1. Az Azure Portalon, az erőforrások listájában válassza ki a labor.
2. Válassza ki a Windows virtuális gép, amely tartalmazza a vizsgálni kívánt hitelesítendő entitások azonosítása.
3. A bal oldali panel alatt **általános**, jelölje be **összetevők**. A virtuális gép társított összetevők listája jelenik meg. Neve az összetevő és az összetevő állapotát jelzi.

   ![Összetevő-állapot](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Válasszon egy összetevő, amely megjeleníti a **sikertelen** állapotát. A lehívandó összetevő nyílik meg. Egy bővítmény üzenet, amely tartalmazza a lehívandó összetevő a hibával kapcsolatos részleteket jelenik meg.

   ![Összetevő hibaüzenet](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>A virtuális gépen az összetevők hibáinak elhárítása

1. Jelentkezzen be a virtuális gép, amely tartalmazza az összetevőben diagnosztizálása szeretné.
2. Lépjen a C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, ahol *1.9* az Azure egyéni szkriptek futtatására szolgáló bővítmény verziószám.

   ![Az állapot-fájl](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Nyissa meg a **állapot** fájlt.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Meglévő Active Directory-tartomány egy Resource Manager-sablon használatával a DevTest Labs szolgáltatásban létrehozott virtuális gépek csatlakoztatása](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [Git-adattár hozzáadása egy laborhoz](devtest-lab-add-artifact-repo.md).

