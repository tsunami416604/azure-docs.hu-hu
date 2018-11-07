---
title: Az első virtuális gép létrehozása az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs
description: Ismerje meg az első virtuális gép létrehozása az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 93ce9feaf52282b9477d49eaf270d6d89dca7811
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232528"
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Az első virtuális gép létrehozása az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet

Ha kezdetben DevTest Labs eléréséhez, és létre szeretné hozni az első virtuális gép, akkor valószínűleg teszi ezt, használatával előre betöltött [alap Piactéri lemezképhez](devtest-lab-configure-marketplace-images.md). Később az Ön is választhat egy [egyéni lemezkép és a egy képlet](devtest-lab-add-vm.md) további virtuális gépek létrehozásakor. 

Ez az oktatóanyag végigvezeti az első virtuális gép hozzáadása a DevTest Labs szolgáltatásban létrehozott tesztkörnyezet az Azure portal használatával.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Az első virtuális gép hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet lépések
1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Tesztkörnyezetek listájában jelölje ki a tesztkörnyezetben, amelyben meg szeretné a virtuális gép létrehozásához.  
1. A laborgyakorlat **áttekintése** panelen válassza ki **+ Hozzáadás**.  

    ![Virtuális gép gomb hozzáadása](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Az a **vyberte bázi** panelen válassza ki a virtuális gép lemezkép-piactér.
1. A a **virtuális gép** panelen adjon meg egy nevet az új virtuális gép a **virtuális gép neve** szövegmezőben.

    ![Laborpanel VM](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. Adjon meg egy **felhasználónév** kap, amely rendszergazdai jogosultságokkal a virtuális gépen.  
1. Adjon meg egy jelszót a címkével ellátott szövegmezőben **adjon meg egy értéket**.
1. A **virtuálisgép-lemeztípust** határozza meg, melyik tárolólemez-típusba engedélyezett a virtuális gépek a tesztkörnyezetben.
1. Válassza ki **virtuálisgép-méret** válasszon ki egy, az előre definiált elemek, amelyek a Processzormagok, memória mérete és a merevlemez mérete, a virtuális gép létrehozásához.
1. Válassza ki **összetevők** és - összetevők – a listából válassza ki és konfigurálja az alaprendszerképet hozzáadni kívánt összetevők.
    **Megjegyzés:** Ha most ismerkedik a DevTest Labs, vagy konfigurálása összetevők, tekintse meg a [hozzáadása egy meglévő összetevő egy virtuális Géphez](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) szakaszt, és ezután térjen vissza ide, ha befejeződött.
1. Válassza ki **létrehozás** a megadott virtuális gép hozzáadása a tesztkörnyezetben.

   A laborpanel állapotát jeleníti meg a virtuális gép létrehozása – először mint **létrehozása**, majd, mint **futó** a virtuális gép elindítása után.

## <a name="next-steps"></a>További lépések
* A virtuális gép létrehozása után csatlakozhat a virtuális gép kiválasztásával **Connect** a virtuális gép paneljén.
* Tekintse meg [virtuális gép hozzáadása egy laborhoz](devtest-lab-add-vm.md) további virtuális gépek hozzáadása a laborkörnyezetben található részletesebb információ.
* Fedezze fel a [DevTest Labs Azure Resource Manager gyorsindítási sablon katalógus](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
