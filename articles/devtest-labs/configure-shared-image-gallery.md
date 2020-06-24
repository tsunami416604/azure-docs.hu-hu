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
ms.date: 06/19/2020
ms.author: spelluru
ms.openlocfilehash: 12a7b678130a2c199dd94af540bacffe8d4685f1
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125726"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Megosztott rendszerkép-katalógus konfigurálása az Azure DevTest Labsben
A DevTest Labs mostantól támogatja a [megosztott rendszerkép](../virtual-machines/windows/shared-image-galleries.md) -katalógus szolgáltatást. Lehetővé teszi, hogy a labor-felhasználók a laboratóriumi erőforrások létrehozásakor hozzáférjenek a lemezképekhez egy megosztott helyről. Emellett az egyéni felügyelt virtuálisgép-rendszerképekhez is felépítheti a struktúrát és a szervezetet. A megosztott rendszerkép-katalógus funkció a következőket támogatja:

- Lemezképek felügyelt globális replikálása
- Lemezképek verziószámozása és csoportosítása a könnyebb felügyelet érdekében
- A rendszerképeket a rendelkezésre állási zónákat támogató régiókban a zónák redundáns tárolási (ZRS) fiókjaival is elérhetővé teheti. A ZRS nagyobb rugalmasságot biztosít a zónabeli hibákkal szemben.
- Az előfizetések és a bérlők közötti megosztás szerepköralapú hozzáférés-vezérléssel (RBAC).

További információ: megosztott képkatalógus [dokumentációja](../virtual-machines/windows/shared-image-galleries.md). 
 
Ha nagy számú felügyelt lemezképet kell fenntartania, és a vállalaton belül elérhetővé szeretné tenni őket, a megosztott képtárat tárházként használhatja, amely megkönnyíti a képek frissítését és megosztását. A labor tulajdonosaként egy meglévő megosztott képtárat is csatolhat a laborhoz. A katalógus csatolása után a labor felhasználói a legújabb rendszerképekből hozhatnak létre gépeket. Ennek a funkciónak a legfőbb előnye, hogy a DevTest Labs mostantól kihasználhatja a képek megosztásának előnyeit a laborokban, az előfizetésekben és az egyes régiókban is. 

> [!NOTE]
> A megosztott képkatalógus szolgáltatással kapcsolatos költségek megismeréséhez tekintse meg a [megosztott képgyűjtemény számlázása](../virtual-machines/windows/shared-image-galleries.md#billing)című témakört.

## <a name="considerations"></a>Megfontolandó szempontok
- Egyszerre csak egy megosztott képtárat lehet csatlakoztatni a laborhoz. Ha másik gyűjteményt szeretne csatolni, le kell választania a meglévőt, és csatolnia kell egy másikat. 
- A DevTest Labs jelenleg nem támogatja lemezképek feltöltését a galérián a laboron keresztül. 
- Ha a virtuális gépet megosztott képkatalógus-rendszerkép használatával hozza létre, a DevTest Labs mindig a lemezkép legújabb közzétett verzióját használja. Ha azonban egy rendszerkép több verzióval rendelkezik, a felhasználó úgy is kiválaszthatja, hogy egy korábbi verzióból hozzon létre egy gépet. Ehhez nyissa meg a speciális beállítások lapot a virtuális gép létrehozásakor.  
- Habár a DevTest Labs automatikusan elvégzi a legjobb kísérletet annak biztosítására, hogy a megosztott képtárak replikálják a lemezképeket arra a régióra, amelyben a labor létezik, nem mindig lehetséges. Annak elkerülése érdekében, hogy a felhasználók ne hozzanak létre virtuális gépeket ezekből a lemezképekről, győződjön meg arról, hogy a lemezképek már replikálódnak a labor régiójába.

## <a name="use-azure-portal"></a>Az Azure Portal használata
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs menüben válassza a **minden szolgáltatás** lehetőséget.
1. Válassza a **DevTest Labs** elemet a listából.
1. A Labs listából válassza ki a **labort**.
1. A bal oldali menüben a **Beállítások** szakaszban válassza a **konfiguráció és szabályzatok** lehetőséget.
1. A bal oldali menüben válassza a **virtuális gépek alapjainak** **megosztott képgyűjtemények** lehetőséget.

    ![Megosztott képtárak menü](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Csatoljon egy meglévő megosztott képtárat a laborhoz. ehhez kattintson a **csatolás** gombra, és válassza ki a katalógust a legördülő menüben.

    ![Csatolás](./media/configure-shared-image-gallery/attach-options.png)
1. A képkatalógus csatolása után válassza ki azt a csatolt katalógushoz való ugráshoz. Konfigurálja a katalógust úgy, hogy **engedélyezze vagy tiltsa le** a virtuális gépek létrehozásához szükséges megosztott lemezképeket. A konfiguráláshoz válasszon ki egy képtárat a listából. 

    Alapértelmezés szerint a **virtuális gépek alapértékeiként használandó összes lemezkép** **Igen**értékre van állítva. Ez azt jelenti, hogy a csatlakoztatott megosztott lemezképek katalógusában elérhető összes lemezkép elérhetővé válik egy tesztkörnyezet-felhasználó számára új tesztkörnyezet létrehozásakor. Ha bizonyos lemezképekhez való hozzáférést korlátozni kell, módosítsa a **nem**értékre a **virtuális gép alapjaként használni** kívánt rendszerképeket, majd válassza ki azokat a lemezképeket, amelyeket engedélyezni szeretne a virtuális gépek létrehozásakor, majd kattintson a **Save (Mentés** ) gombra.

    :::image type="content" source="./media/configure-shared-image-gallery/enable-disable.png" alt-text="Képek engedélyezése vagy letiltása":::

    > [!NOTE]
    > A megosztott rendszerkép-katalógusban található általánosított és speciális rendszerképek is támogatottak. 
1. A labor felhasználói ezután létrehozhatnak egy virtuális gépet az elérhető lemezképek használatával, ha a **válassza ki a kiindulási** lapon a **+ Hozzáadás** és a kép keresése lehetőséget.

    ![Tesztkörnyezet felhasználói](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager sablon használata

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

A Resource Manager-sablonok teljes leírását lásd: ezek a Resource Manager-sablonok a nyilvános GitHub-tárházban: a [közös rendszerkép-katalógus konfigurálása a tesztkörnyezet létrehozásakor](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-rest-api"></a>A REST API használata

### <a name="get-a-list-of-labs"></a>A laborok listájának lekérése 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs?api-version= 2018-10-15-preview
```

### <a name="get-the-list-of-shared-image-galleries-associated-with-a-lab"></a>A laborhoz társított megosztott képtárak listájának beolvasása

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries?api-version= 2018-10-15-preview
   ```

### <a name="create-or-update-shared-image-gallery"></a>Megosztott Képtár létrehozása vagy frissítése

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

### <a name="list-images-in-a-shared-image-gallery"></a>Megosztott képtárban lévő rendszerképek listázása

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```



## <a name="next-steps"></a>További lépések
Tekintse meg a virtuális gép létrehozása a csatolt megosztott rendszerképből rendszerkép használatával című cikket a következő cikkekből: virtuális gép [létrehozása megosztott rendszerkép használatával a gyűjteményből](add-vm-use-shared-image.md)
