---
title: Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet licencelt kép engedélyezése |} A Microsoft Docs
description: Ismerje meg, hogyan lehet az Azure DevTest Labs szolgáltatásban az Azure portal használatával licencelt kép engedélyezése
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
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532745"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet licencelt kép engedélyezése

Az Azure DevTest Labs szolgáltatásban licencelt kép, amelyik tartalmazza a feltételek és kikötések – általában a harmadik fél – el kell fogadnia ahhoz a lemezképet a labor létrehozása a felhasználók számára érhető el. A következő szakaszok ismertetik, hogy azok használni létrehozott virtuális gépek számára elérhető licenccel rendelkező rendszerképek használata.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Licencelt kép elérhető-e a felhasználók számára meghatározása
Az első lépése, amellyel a felhasználók könnyedén hozhat létre virtuális gépeket a licencelt kép, győződjön meg arról, hogy a feltételek és kikötések lett elfogadva a licencelt kép. A következő lépések bemutatják, hogyan lehet licencelt kép ajánlat állapotának megtekintése és, ha szükséges, fogadja el a használati feltételeket.

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.

1. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.  

1. A bal oldali panel alatt **beállítások**válassza **Konfigurace a zásady**.

1. A bal oldali panel alatt **VIRTUÁLISGÉP-BÁZISOK**válassza **Piactérről származó rendszerképek**. 

    ![Marketplace-rendszerképek menüpont](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Az összes elérhető marketplace-rendszerképek listája látható, beleértve a **ajánlat állapota** az egyes lemezképek.

    ![Az egyes lemezképek ajánlat állapotát megjelenítő marketplace-rendszerképek listája](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Licencelt kép egy ajánlat állapotát jeleníti meg. 
    
    - **Feltételek elfogadva:** a licencelt kép hozhat létre virtuális gépeket a felhasználók számára érhető el. 
    - **Ellenőrzés szükséges feltételek:** a licencelt kép jelenleg nem áll rendelkezésre a felhasználók számára. A használati feltételeket a licenc el kell fogadnia ahhoz, labor felhasználók használhassa hozhat létre virtuális gépeket. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Licencelt kép elérhetővé tételére labor számára
Ahhoz, hogy licencelt kép labor-felhasználók számára érhető el, rendszergazdai jogosultságokkal rendelkezik a labor tulajdonosának kell először fogadja el a használati feltételeket az adott licencelt kép. Programozott üzembe helyezés az előfizetéshez társított automatikusan licencelt kép engedélyezése fogadja el a jogi feltételeket és adatvédelmi nyilatkozatából a rendszerképet. [Marketplace-rendszerképek használata az Azure Resource Managerrel](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) marketplace-rendszerképek a programozott telepítés további információkat tartalmaz.

Licencelt kép a programozott telepítés a következő lépésekkel engedélyezhető:

1. Az a [az Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), lépjen a listájának **Piactérről származó rendszerképek**.

1. Licencelt kép, amelyek esetében szeretne hozzáfér, hogy a felhasználók azonosításához, de amelynek feltételei nem fogadták el. Például előfordulhat, hogy megjelenik egy adatelemző virtuális gép, amely akár állapotát jeleníti meg a **feltételek elfogadva** vagy **ellenőrzés szükséges feltételek**.

    ![Programozott üzembe helyezés időkeret konfigurálása](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Adatelemzési virtuális gépek az Azure virtuálisgép-lemezképek, előre telepített, konfigurált és számos népszerű eszközök, amelyek gyakran használják az adatelemzés, a gépi tanulási és AI-betanítás teszteltük. [Bevezetés az Azure Data Science virtuális gép Linux és Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) nagy fokú Dsvm kapcsolatos információkat biztosít.
   >
   >

1. Az a **ajánlat állapota** oszlop a lemezképet, válassza ki a **ellenőrzés szükséges feltételek**.

1. Válassza a programozott telepítés konfigurálása ablak **engedélyezése**.

    ![Programozott üzembe helyezés időkeret konfigurálása](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Láthatja, hogy több előfizetést a programozott telepítés konfigurálása ablak szerepel. Ellenőrizze, hogy engedélyezi az importálni kívánt előfizetés csak a programozott telepítés.
   >
   >


1. Kattintson a **Mentés** gombra. 

    A marketplace-rendszerképek listája, amelyek kép most azt mutatja be **feltételek elfogadva** és hozhat létre virtuális gépeket a felhasználók számára érhető el.

> [!NOTE]
> Felhasználók létrehozhat egy egyéni rendszerkép licencelt kép. Lásd: [egy egyéni lemezkép készítése VHD-fájlból](devtest-lab-create-template.md) további információt.
>
>


## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni rendszerképek vagy képleteket?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Egyéni rendszerképek az Azure DevTest Labs közötti másolása](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [Egyéni lemezkép készítése egy virtuális Gépről](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Egy egyéni lemezkép készítése VHD-fájlból](devtest-lab-create-template.md)
- [A labor virtuális gép hozzáadása](devtest-lab-add-vm.md)