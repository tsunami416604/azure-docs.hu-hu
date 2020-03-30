---
title: Megosztott képgaléria konfigurálása az Azure DevTest Labs ben | Microsoft dokumentumok
description: Megtudhatja, hogyan konfigurálhat megosztott képgalériát az Azure DevTest Labs ben
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 9593d60f76802cd515ca85616bce028cf3aa0d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589317"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Megosztott rendszerkép-katalógus konfigurálása az Azure DevTest Labsben
A DevTest Labs mostantól támogatja a [Megosztott képtár](../virtual-machines/windows/shared-image-galleries.md) funkciót. Lehetővé teszi, hogy a tesztkörnyezet felhasználói egy megosztott helyről férjenek hozzá a lemezképekhez, miközben laborerőforrásokat hoznak létre. Emellett az egyénileg felügyelt virtuálisgép-lemezképek köréalakíthatja a struktúrát és a szervezetet. A Megosztott képtár szolgáltatás a következőket támogatja:

- Képek felügyelt globális replikációja
- A képek verziószámozása és csoportosítása a könnyebb kezelés érdekében
- A zone redundáns tárolás (ZRS) fiókokkal magas szintű elérhetővé teheti a lemezképeket a rendelkezésre állási zónákat támogató régiókban. A ZRS jobb rugalmasságot kínál a zónahibákkal szemben.
- Megosztás előfizetések között, és még a bérlők között, szerepköralapú hozzáférés-vezérlés (RBAC) használatával.

További információt a [Megosztott képtár dokumentációjában](../virtual-machines/windows/shared-image-galleries.md)talál. 
 
Ha nagy számú felügyelt lemezképet kell karbantartania, és szeretné elérhetővé tenni őket a vállalategész területén, használhat egy megosztott képgalériát tárházként, amely megkönnyíti a képek frissítését és megosztását. A tesztkörnyezet tulajdonosaként egy meglévő megosztott képgalériát csatolhat a tesztkörnyezethez. A galéria csatlakoztatása után a tesztkörnyezet-felhasználók gépeket hozhatnak létre ezekből a legújabb képekből. A funkció egyik fő előnye, hogy a DevTest Labs most már kihasználhatja a képek megosztása a laborok között, az előfizetések között és a régiók között. 

> [!NOTE]
> A Megosztott képtár szolgáltatással kapcsolatos költségekről a [Megosztott képtár számlázása](../virtual-machines/windows/shared-image-galleries.md#billing)című témakörben olvashat.

## <a name="considerations"></a>Megfontolandó szempontok
- Egyszerre csak egy megosztott képgalériát csatolhat egy tesztkörnyezethez. Ha szeretne csatolni egy másik galéria, akkor le kell választania a meglévőt, és csatolja egy másik. 
- DevTest Labs jelenleg csak a megosztott képgaléria általános képeket támogatja.
- DevTest Labs jelenleg nem támogatja a képek feltöltését a gyűjteménya laboron keresztül. 
- A devtest labs mindig a lemezkép legújabb közzétett verzióját használja egy megosztott képgaléria-lemezkép használatával. Ha azonban egy lemezkép több verzióval rendelkezik, a felhasználó úgy is választhat, hogy egy korábbi verzióból hozzon létre egy gépet a Speciális beállítások lapon a virtuális gép létrehozása során.  
- Bár a DevTest Labs automatikusan a legjobb kísérletet teszi annak biztosítására, hogy a megosztott képgaléria replikálja a képeket arra a régióra, ahol a labor létezik, ez nem mindig lehetséges. Annak elkerülése érdekében, hogy a felhasználók nak problémái vannak a virtuális gépek létrehozásával ezekből a lemezképekből, győződjön meg arról, hogy a rendszerképek már replikálódnak a labor régiójába."

## <a name="use-azure-portal"></a>Az Azure Portal használata
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza a bal oldali navigációs menü **Minden szolgáltatás lehetőséget.**
1. Válassza a **DevTest Labs** elemet a listából.
1. A laborok listájából válassza ki a **labort.**
1. A bal oldali menü **Beállítások** szakaszában válassza a **Konfiguráció és házirendek** lehetőséget.
1. A bal oldali menü **Virtuálisgép-alapokkal** csoportban válassza a **Megosztott képgalériák** lehetőséget.

    ![Megosztott képgalériák menü](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Csatoljon egy meglévő megosztott képgalériát a laborhoz a **Csatolás gombra** kattintva, és válassza ki a galériát a legördülő menüben.

    ![Csatolás](./media/configure-shared-image-gallery/attach-options.png)
1. Nyissa meg a csatolt katalógust, és konfigurálja a katalógust a virtuális gép létrehozásához megosztott képek **engedélyezéséhez vagy letiltásához.** A beállításhoz jelöljön ki egy képgyűjteményt a listából. 

    Alapértelmezés szerint **az összes lemezkép használatba veendő engedélyezése, mivel a virtuálisgép-alapzat** **beállítása Igen**. Ez azt jelenti, hogy a csatolt megosztott képtárban elérhető összes kép elérhető lesz a labor felhasználó számára egy új tesztkörnyezet virtuális gép létrehozásakor. Ha bizonyos képekhez való hozzáférést korlátozni kell, módosítsa az **Összes lemezkép virtuálisgép-bázisként való használatának engedélyezése** **lehetőséget ,** és jelölje ki azokat a képeket, amelyeket a virtuális gépek létrehozásakor engedélyezni szeretne, majd kattintson a **Mentés gombra.**

    ![Engedélyezés vagy letiltás](./media/configure-shared-image-gallery/enable-disable.png)
1. A Labor-felhasználók ezután létrehozhatnak egy virtuális gépet az engedélyezett képek használatával, ha a **+Hozzáadás** gombra kattintanak, és megkeresik a képet az **alaplapon.**

    ![Labor felhasználók](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Az Azure Resource Manager sablon használata

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Megosztott képtár csatolása a laborhoz
Ha egy Azure Resource Manager-sablont használ egy megosztott képtár csatolásához a laborhoz, hozzá kell adnia azt az Erőforrás-kezelő sablon erőforrások szakasza alatt, ahogy az a következő példában látható:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

Egy teljes Erőforrás-kezelő sablon például lásd az alábbi Erőforrás-kezelő sablon minták nyilvános GitHub-tárházban: [Konfigurálása egy megosztott képtár létrehozása közben egy tesztkörnyezet.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)

## <a name="use-api"></a>AZ API használata

### <a name="shared-image-galleries---create-or-update"></a>Megosztott képgalériák – létrehozás vagy frissítés

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="shared-image-galleries-images---list"></a>Megosztott képgalériák képek - Lista 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>További lépések
Tekintse meg a virtuális gép létrehozásáról szóló alábbi cikkeket a csatolt megosztott képtárból származó kép használatával: [Virtuális gép létrehozása a katalógusból megosztott képpel](add-vm-use-shared-image.md)
