---
title: Címkék hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs
description: Ismerje meg az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet egy címke hozzáadása
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 38ec93afd27ac75a97a28fef59bd10fafdf963c9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247041"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Címkék hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet

Egyéni címkék létrehozása, és alkalmazhatja azokat a DevTest Labs erőforrások logikailag kategorizálhatja az erőforrásokat. Később gyorsan és egyszerűen tekintse meg az előfizetés adott címkével rendelkező erőforrásokat. A címkék hasznosak, meg kell rendszereznie erőforrásait számlázás vagy felügyelet céljából.

A címkék által támogatott erőforrásai között szerepeljen

* Virtuális gépek számítási
* Hálózati adapterek (NIC-k)
* IP-címek
* Terheléselosztók
* Tárfiókok
* Felügyelt lemezek

Alkalmazhat mikor címkék, [labor létrehozása](devtest-lab-create-lab.md) azokat később konfigurációja és beállításai a Címkék panelen keresztül felügyelheti.

Minden címke épül fel egy **neve**/**érték** pár. Például előfordulhat, hogy egy címkét hoz létre a nevet *costcenter* , amelynek az értéke *34543*. Egy címke, mint például ez hasznos lehet, hogy később azonosíthatja labor-erőforrásokat, amelyek a szervezet adott területhez számlázható. Válassza ki a neveket és értékeket, amelyek bírnak, hogyan szeretne az előfizetés rendszerezése kap.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Egy meglévő tesztkörnyezetben címkék felügyeletének lépései

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Ha szükséges, válassza ki a **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából. Előfordulhat, hogy a tesztkörnyezet már látható az irányítópult alatt **összes erőforrás**.
1. Tesztkörnyezetek listájában jelölje ki a labor létrehozása, amelyben meg szeretné felvenni vagy címkék kezelése.  
1. A laborgyakorlat **áttekintése** területen válassza **Konfigurace a zásady**.  

    ![Konfigurace a zásady gomb](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. A bal oldali alatt **kezelés**válassza **címkék**.
1. A jelen labor új címke létrehozásához írja be a **neve**/**érték** párosítását, és válassza ki **mentése**. Egy meglévő címke is kijelölhet a listában megtekintéséhez vagy a címkével társított erőforrások kezeléséhez.

    ![Címkék kezelése](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>A címkék korlátozások ismertetése

Az alábbi korlátozások érvényesek a címkékre:

* Minden egyes erőforrás vagy erőforráscsoport legfeljebb 15 címkenév/érték párral rendelkezhet. A korlátozás csak a közvetlenül az erőforráscsoportra vagy erőforrásra alkalmazott címkékre érvényes. Az erőforráscsoportok sok olyan erőforrást tartalmazhatnak, amelyek mindegyike 15 címkenév/érték párral rendelkezik. 
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.

[Az Azure-erőforrások rendszerezése címkék használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) címkék használatával az Azure-ban, beleértve a PowerShell vagy az Azure CLI használatával címkék kezelése nagyobb részletesen ismerteti.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
* Testreszabott házirendek használatával alkalmazhat korlátozások és konvenciói előfizetését. Egy Ön által meghatározott szabályzat szükség lehet, hogy az összes erőforrásnak rendelkeznie kell egy értéket egy adott címkét. További információkért lásd: [szabályzatok és ütemezések beállítása](devtest-lab-set-lab-policy.md).
* Fedezze fel a [DevTest Labs Azure Resource Manager gyorsindítási sablon katalógus](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
