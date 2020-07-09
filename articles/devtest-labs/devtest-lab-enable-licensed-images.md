---
title: Licencelt rendszerkép engedélyezése a laborban Azure DevTest Labs | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti a licencelt rendszerképek használatát Azure DevTest Labs a Azure Portal
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6a4023a0107acf5aa4cabc2178d17f7be40301f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85481017"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Licencelt rendszerkép engedélyezése a laborban Azure DevTest Labs

Azure DevTest Labs egy licenccel rendelkező rendszerkép olyan, amely feltételeket és kikötéseket tartalmaz – jellemzően egy harmadik féltől –, amelyet el kell fogadni ahhoz, hogy a rendszerkép elérhető legyen a tesztkörnyezet felhasználói számára. A következő szakaszok azt ismertetik, hogyan használhatók a licencelt lemezképek, hogy elérhetők legyenek a virtuális gépek létrehozásához.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Annak meghatározása, hogy elérhető-e egy licenccel rendelkező rendszerkép a felhasználók számára
Az első lépés, amely lehetővé teszi, hogy a felhasználók virtuális gépeket hozzanak létre egy licencelt rendszerképből, győződjön meg arról, hogy a feltételek és kikötések el lettek fogadva a licencelt rendszerképhez. A következő lépések bemutatják, hogyan tekintheti meg egy licencelt rendszerkép ajánlati állapotát, és ha szükséges, elfogadhatja a használati feltételeit.

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.

1. A laborok listájából válassza ki a kívánt labort.  

1. A bal oldali panel **Beállítások**területén válassza a **konfiguráció és házirendek**elemet.

1. A **virtuális gépek alapjainak**bal oldali paneljén válassza a **piactér lemezképek**lehetőséget. 

    ![Piactéri rendszerképek menüelem](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Megjelenik az összes elérhető Piactéri lemezkép listája, beleértve az egyes lemezképek **ajánlati állapotát** is.

    ![Az egyes lemezképek ajánlati állapotát megjelenítő piactér-lemezképek listája](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    A licencelt rendszerkép az ajánlat állapotát jeleníti meg 
    
    - **Feltételek elfogadva:** a licenccel rendelkező felhasználók a virtuális gépek létrehozásához használhatók. 
    - A **feltételek áttekintése szükséges:** a licencelt rendszerkép jelenleg nem érhető el a felhasználók számára. A licenc használati feltételeit el kell fogadni ahhoz, hogy a labor felhasználók virtuális gépeket hozzanak létre. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Licencelt rendszerkép elérhetővé tétele a labor felhasználói számára
Annak biztosítása érdekében, hogy a labor felhasználói számára elérhető legyen egy licenccel rendelkező rendszerkép, a rendszergazdai jogosultsággal rendelkező labor tulajdonosának először el kell fogadnia az adott licencelt rendszerkép használati feltételeit. A programozott központi telepítés engedélyezése a licencelt rendszerképhez társított előfizetéshez automatikusan elfogadja az adott képhez tartozó jogi feltételeket és adatvédelmi nyilatkozatokat. A [Piactéri rendszerképek használata a Azure Resource Manageron](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) további információkat nyújt a Piactéri rendszerképek programozott üzembe helyezéséről.

Az alábbi lépéseket követve engedélyezheti a programozott központi telepítést egy licencelt rendszerképhez:

1. A [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)lépjen a **piactér lemezképek**listájára.

1. Azonosítson egy licencelt rendszerképet, amelyhez a felhasználóknak hozzáférésre van szükségük, de a feltételek nem lettek elfogadva. Előfordulhat például, hogy egy olyan Data Science Virtual Machine jelenik meg, amely az **elfogadva** vagy a **feltételek felülvizsgálatához szükséges**állapotot jeleníti meg.

    ![Programozott telepítés ablakának konfigurálása](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Az adatelemzési virtuális gépek Azure-beli virtuálisgép-lemezképek, előre telepített, konfigurált és tesztelt számos olyan népszerű eszközzel, amelyet általában az adatelemzés, a gépi tanulás és a mesterséges intelligenciával kapcsolatos képzésekhez használnak. A [Linux és a Windows rendszerhez készült Azure Data Science Virtual Machine bemutatása](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) nagy mennyiségű információt nyújt a dsvm-ről.
   >
   >

1. A rendszerkép **ajánlat állapota** oszlopában válassza a **feltételek felülvizsgálat szükséges**elemet.

1. A programozott telepítés konfigurálása ablakban válassza az **Engedélyezés**lehetőséget.

    ![Programozott telepítés ablakának konfigurálása](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Előfordulhat, hogy több előfizetést is láthat a programozott telepítés konfigurálása ablakban. Győződjön meg arról, hogy csak a kívánt előfizetéshez engedélyezi a programozott üzembe helyezést.
   >
   >


1. Kattintson a **Mentés** gombra. 

    A Piactéri lemezképek listájában a kép mostantól az **elfogadott kifejezéseket** jeleníti meg, és elérhetővé válik a felhasználók számára virtuális gépek létrehozásához.

> [!NOTE]
> A felhasználók létrehozhatnak egyéni rendszerképet egy licencelt rendszerképből. További információ: [Egyéni rendszerkép létrehozása VHD-fájlból](devtest-lab-create-template.md) .
>
>


## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Egyéni rendszerképek másolása Azure DevTest Labs között](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [Egyéni rendszerkép létrehozása virtuális gépből](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Egyéni rendszerkép létrehozása VHD-fájlból](devtest-lab-create-template.md)
- [Virtuális gép hozzáadása a laborhoz](devtest-lab-add-vm.md)