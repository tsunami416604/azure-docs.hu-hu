---
title: Megosztott képgyűjtemény konfigurálása a Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat megosztott képtárat Azure DevTest Labs
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
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: 80610168e0d293b65626da71ee349f25e456576b
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774570"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Megosztott rendszerkép-katalógus konfigurálása az Azure DevTest Labsben
A DevTest Labs mostantól támogatja a [megosztott rendszerkép](../virtual-machines/windows/shared-image-galleries.md) -katalógus szolgáltatást. A funkcióval a tesztkörnyezet-felhasználók megosztott helyen tárolt rendszerképekhez férhetnek hozzá, amikor tesztkörnyezet-erőforrásokat hoznak létre. Emellett azt is lehetővé teszi, hogy az egyénileg felügyelt VM-rendszerképekhez struktúrát és szervezetet hozzon létre. A megosztott rendszerkép-katalógus funkció a következőket támogatja:

- Rendszerképek felügyelt globális replikációja
- Verziókövetés és rendszerképek csoportosítása az egyszerűbb kezelés érdekében
- A lemezképek magas rendelkezésre állása zónaredundáns tárolási (ZRS-) fiókokkal biztosítható a rendelkezésre állási zónákat támogató régiókban. A ZRS ellenállóbbá teszi a lemezképeket a zónán belüli hibákkal szemben.
- Megosztás előfizetések és bérlők között szerepköralapú hozzáférés-vezérlés (RBAC) használatával.

További információ: [megosztott](../virtual-machines/windows/shared-image-galleries.md)képkatalógus dokumentációja. 
 
Ha nagy számú felügyelt lemezképet kell kezelnie, és szeretné azokat elérhetővé tenni a teljes cég számára, használhat megosztott rendszerkép-katalógust és tárházat, amelynek használatával egyszerűen frissíthetők és oszthatók meg a rendszerképek. A tesztkörnyezet tulajdonosaként egy már meglévő megosztott rendszerkép-katalógust a tesztkörnyezethez is csatolhat. A katalógus csatolása után a tesztkörnyezet felhasználói virtuális gépeket hozhatnak létre ezekből a legfrissebb rendszerképekből. Ennek a funkciónak az egyik legfontosabb előnye, hogy a DevTest-laborok így több tesztkörnyezetre, több előfizetésre és több régióra kiterjedően használhatják a megosztott rendszerképeket. 

## <a name="considerations"></a>Megfontolandó szempontok
- Egyszerre csak egy megosztott képtárat lehet csatlakoztatni a laborhoz. Ha másik gyűjteményt szeretne csatolni, le kell választania a meglévőt, és csatolnia kell egy másikat. 
- A DevTest Labs jelenleg nem támogatja lemezképek feltöltését a galérián a laboron keresztül. 
- Ha a virtuális gépet megosztott képkatalógus-rendszerkép használatával hozza létre, a DevTest Labs mindig a lemezkép legújabb közzétett verzióját használja.
- Habár a DevTest Labs automatikusan elvégzi a legjobb kísérletet annak biztosítására, hogy a megosztott képtárak replikálják a lemezképeket arra a régióra, amelyben a labor létezik, nem mindig lehetséges. Annak elkerülése érdekében, hogy a felhasználók ne hozzanak létre virtuális gépeket ezekből a lemezképekről, győződjön meg arról, hogy a lemezképek már replikálódnak a labor régiójába.

## <a name="use-azure-portal"></a>Az Azure Portal használata
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs menüben válassza a **minden szolgáltatás** lehetőséget.
1. Válassza a **DevTest Labs** elemet a listából.
1. A Labs listából válassza ki a labort.
1. A bal oldali menüben a **Beállítások** szakaszban válassza a **konfiguráció és szabályzatok** lehetőséget.
1. A bal oldali menüben válassza a **virtuális gépek alapjainak** **megosztott** képgyűjtemények lehetőséget.

    ![Megosztott képtárak menü](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Csatoljon egy meglévő megosztott képtárat a laborhoz. ehhez kattintson a **csatolás** gombra, és válassza ki a katalógust a legördülő menüben.

    ![Csatolás](./media/configure-shared-image-gallery/attach-options.png)
1. Nyissa meg a csatolt katalógust, és konfigurálja a katalógust, hogy **engedélyezze vagy tiltsa le** a virtuális gépek létrehozásához szükséges megosztott lemezképeket. A konfiguráláshoz válasszon ki egy képtárat a listából. 

    Alapértelmezés szerint a **virtuális gépek alapértékeiként használandó összes lemezkép** **Igen**értékre van állítva. Ez azt jelenti, hogy a csatlakoztatott megosztott lemezképek katalógusában elérhető összes lemezkép elérhetővé válik egy tesztkörnyezet-felhasználó számára új tesztkörnyezet létrehozásakor. Ha bizonyos lemezképekhez való hozzáférést korlátozni kell, módosítsa a **nem**értékre a **virtuális gép alapjaként használni** kívánt rendszerképeket, majd válassza ki azokat a lemezképeket, amelyeket engedélyezni szeretne a virtuális gépek létrehozásakor, majd kattintson a **Save (Mentés** ) gombra.

    ![Engedélyezés vagy Letiltás](./media/configure-shared-image-gallery/enable-disable.png)
1. A labor felhasználói ezután létrehozhatnak egy virtuális gépet az elérhető lemezképek használatával, ha a **válassza ki** a kiindulási lapon a **+ Hozzáadás** és a kép keresése lehetőséget.

    ![Tesztkörnyezet-felhasználók](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Az Azure Resource Manager sablonjainak használata

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Megosztott képgyűjtemény csatolása a laborhoz
Ha Azure Resource Manager sablonnal csatlakoztat egy megosztott képtárat a laborhoz, azt a Resource Manager-sablon erőforrások szakaszában kell hozzáadnia, az alábbi példában látható módon:

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

A Resource Manager-sablonok teljes példáját a nyilvános GitHub-tárházban tekintheti meg: [Konfiguráljon egy megosztott képtárat labor létrehozásakor](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-api"></a>API használata

### <a name="shared-image-galleries---create-or-update"></a>Megosztott képtárak – létrehozás vagy frissítés

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

### <a name="shared-image-galleries-images---list"></a>Megosztott képtárak képei – lista 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>További lépések
Tekintse meg a virtuális gép létrehozásával kapcsolatos alábbi cikkeket a csatolt megosztott rendszerkép-katalógus rendszerképének használatával: [Virtuális gép létrehozása megosztott rendszerkép használatával a katalógusból](add-vm-use-shared-image.md)
