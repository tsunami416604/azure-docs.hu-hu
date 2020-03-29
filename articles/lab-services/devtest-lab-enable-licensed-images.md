---
title: Licencelt lemezkép engedélyezése a laborban az Azure DevTest Labs ben | Microsoft dokumentumok
description: Megtudhatja, hogy miként engedélyezheti a licencelt lemezképeket az Azure DevTest Labs ben az Azure Portal használatával
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 221390d2-8d3b-4e1f-b454-43d33f8072b7
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 11b6553fe8aceef0d3d15977998dd870c275128a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61294256"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Licencelt lemezkép engedélyezése a laborban az Azure DevTest Labsben

Az Azure DevTest Labs, a licencelt lemezkép olyan, amely tartalmazza a feltételeket – általában egy harmadik féltől –, amelyet el kell fogadni, mielőtt a rendszerkép elérhető a felhasználók számára a laborban. A következő szakaszok ismertetik, hogyan működik a licencelt lemezképek, hogy azok a virtuális gépek létrehozásához használható.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Annak meghatározása, hogy egy licencelt lemezkép elérhető-e a felhasználók számára
Az első lépés, amely lehetővé teszi a felhasználók számára, hogy virtuális gépeket hozzanak létre egy licencelt lemezképből, hogy győződjön meg arról, hogy a feltételeket elfogadták a licencelt lemezképhez. A következő lépések bemutatják, hogyan tekintheti meg egy licencelt lemezkép ajánlati állapotát, és ha szükséges, hogyan fogadhatja el annak feltételeit.

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. Válassza a **Minden szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.

1. A laborok listájából válassza ki a kívánt labort.  

1. A **bal**oldali panelen válassza a **Konfiguráció és házirendek**lehetőséget.

1. A bal oldali panelen, a **VIRTUÁLISGÉP-ALAPZAT**csoportban válassza a **Marketplace-képek lehetőséget.** 

    ![Marketplace-képek menüelem](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Megjelenik az összes elérhető piactéri lemezkép listája, beleértve az egyes **lemezképek ajánlati állapotát** is.

    ![Az egyes képek ajánlati állapotát megjelenítő piactéri képek listája](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    A licencelt kép egy 
    
    - **Elfogadott feltételek:** a licencelt lemezkép a felhasználók számára elérhető a virtuális gépek létrehozásához. 
    - **Feltételek felülvizsgálata szükséges:** a licencelt kép jelenleg nem érhető el a felhasználók számára. A licenc feltételeit el kell fogadni, mielőtt a tesztkörnyezet-felhasználók virtuális gépek létrehozására használhassák. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Licencelt lemezkép elérhetővé tétele a laborfelhasználók számára
Annak érdekében, hogy egy licencelt lemezkép elérhető legyen a tesztkörnyezet-felhasználók számára, a rendszergazdai engedélyekkel rendelkező tesztkörnyezet tulajdonosának először el kell fogadnia az adott licencelt lemezkép feltételeit. A licencelt lemezképhez társított előfizetés programozott központi telepítésének engedélyezése automatikusan elfogadja az adott lemezkép jogi feltételeit és adatvédelmi nyilatkozatait. [A Marketplace-lemezképek az Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) használatával további információkat nyújt a marketplace-lemezképek programozott központi telepítéséről.

A licencelt lemezképek programozott központi telepítését az alábbi lépésekkel engedélyezheti:

1. Az [Azure Portalon](https://go.microsoft.com/fwlink/p/?LinkID=525040)nyissa meg a **Marketplace-képek**listáját.

1. Azonosítsa azt a licencelt képet, amelyhez a felhasználóknak hozzáféréssel kell rendelkezniük, de feltételei nem lettek elfogadva. Például megjelenhet egy adatelemzési virtuális gép, amely a **Feltételek elfogadott** vagy a Feltételek szükséges ellenőrzésének állapotát jeleníti **meg.**

    ![A Programozott központi telepítés ablak konfigurálása](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Az adatelemzési virtuális gépek az Azure Virtuálisgép-lemezképek, amelyek előre telepítettek, konfigurálva és tesztelve számos népszerű eszközzel, amelyeket gyakran használnak adatelemzéshez, gépi tanuláshoz és AI-oktatáshoz. [Bevezetés az Azure Data Science Virtual Machine linuxos és Windows-alapú](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) nyújt nagy mennyiségű információt dsvms.
   >
   >

1. A kép **AJÁNLATÁLLAPOTA** oszlopában válassza a **Szükséges Feltételek felülvizsgálatlehetőséget.**

1. A Programozott központi telepítés konfigurálása ablakban válassza az **Engedélyezés**lehetőséget.

    ![A Programozott központi telepítés ablak konfigurálása](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Előfordulhat, hogy több előfizetés jelenik meg a Programozott központi telepítés konfigurálása ablakban. Győződjön meg arról, hogy csak a tervezett előfizetéshez engedélyezi a programozott központi telepítést.
   >
   >


1. Kattintson a **Mentés** gombra. 

    A marketplace-lemezképek listájában ez a kép mostantól **az elfogadott feltételeket** jeleníti meg, és a felhasználók számára elérhető virtuális gépek létrehozásához.

> [!NOTE]
> A felhasználók egyéni lemezképet hozhatnak létre licencelt lemezképből. További információt az [Egyéni lemezkép létrehozása VHD-fájlból](devtest-lab-create-template.md) című témakörben talál.
>
>


## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni képek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Egyéni lemezképek másolása az Azure DevTest Labs között](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [Egyéni rendszerkép létrehozása virtuális gépből](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Egyéni kép létrehozása VHD-fájlból](devtest-lab-create-template.md)
- [Virtuális gép hozzáadása a laborhoz](devtest-lab-add-vm.md)