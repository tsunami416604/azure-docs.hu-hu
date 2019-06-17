---
title: Egy megosztott képgyűjtemény konfigurálása az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, egy megosztott képgyűjtemény konfigurálása az Azure DevTest Labs szolgáltatásban
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
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: de857498aeb51c9b3711c90338d983e85b61cb70
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065436"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Megosztott rendszerkép-katalógus konfigurálása az Azure DevTest Labsben
DevTest Labs szolgáltatásban mostantól támogatja a [megosztott Képkatalógus](../virtual-machines/windows/shared-image-galleries.md) funkció. A funkcióval a tesztkörnyezet-felhasználók megosztott helyen tárolt rendszerképekhez férhetnek hozzá, amikor tesztkörnyezet-erőforrásokat hoznak létre. Emellett azt is lehetővé teszi, hogy az egyénileg felügyelt VM-rendszerképekhez struktúrát és szervezetet hozzon létre. A megosztott Képkatalógus funkció támogatja:

- Rendszerképek felügyelt globális replikációja
- Verziókövetés és rendszerképek csoportosítása az egyszerűbb kezelés érdekében
- A lemezképek magas rendelkezésre állása zónaredundáns tárolási (ZRS-) fiókokkal biztosítható a rendelkezésre állási zónákat támogató régiókban. A ZRS ellenállóbbá teszi a lemezképeket a zónán belüli hibákkal szemben.
- Megosztás előfizetések és bérlők között szerepköralapú hozzáférés-vezérlés (RBAC) használatával.

További információkért lásd: [megosztott Képkatalógus dokumentáció](../virtual-machines/windows/shared-image-galleries.md). 
 
Ha nagy számú felügyelt lemezképet kell kezelnie, és szeretné azokat elérhetővé tenni a teljes cég számára, használhat megosztott rendszerkép-katalógust és tárházat, amelynek használatával egyszerűen frissíthetők és oszthatók meg a rendszerképek. A tesztkörnyezet tulajdonosaként egy már meglévő megosztott rendszerkép-katalógust a tesztkörnyezethez is csatolhat. A katalógus csatolása után a tesztkörnyezet felhasználói virtuális gépeket hozhatnak létre ezekből a legfrissebb rendszerképekből. Ennek a funkciónak az egyik legfontosabb előnye, hogy a DevTest-laborok így több tesztkörnyezetre, több előfizetésre és több régióra kiterjedően használhatják a megosztott rendszerképeket. 

## <a name="considerations"></a>Megfontolandó szempontok
- Egyszerre csak egy laborhoz csak csatolhat egy megosztott lemezkép-katalógusában. Ha egy másik gyűjtemény csatlakoztatni szeretne, válassza le a meglévőt, és egy másik új kell. 
- DevTest Labs szolgáltatásban jelenleg nem támogatja feltöltését lemezképeket – a labor létrehozása a katalógusban. 
- Egy megosztott kép katalóguslemezt használó virtuális gép létrehozásakor a DevTest Labs mindig a lemezkép közzétett legújabb verzióját használja.
- Bár a DevTest Labs automatikusan lehetővé teszi a lehető legjobb módon győződjön meg arról, megosztott lemezkép-katalógusában a régiót, amelyben a labor található rendszerképek replikálja, nem mindig lehetséges. A felhasználók problémákba ütköznek-hoz létre virtuális gépeket a lemezképek elkerülése érdekében győződjön meg arról a labor régió már replikálva vannak a lemezképeket."

## <a name="use-azure-portal"></a>Az Azure Portal használata
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki **minden szolgáltatás** a bal oldali navigációs menüben.
1. Válassza a **DevTest Labs** elemet a listából.
1. Tesztkörnyezetek listájában válassza ki a **labor**.
1. Válassza ki **Konfigurace a zásady** a a **beállítások** szakaszban a bal oldali menüben.
1. Válassza ki **megosztott kép katalógusok** alatt **virtuálisgép-bázisok** a bal oldali menüben.

    ![Megosztott kép katalógusok menü](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. A labor kattintva egy meglévő megosztott lemezkép-katalógus csatolása a **Attach** gombra, és a gyűjtemény kiválasztásával a legördülő listában.

    ![Csatolás](./media/configure-shared-image-gallery/attach-options.png)
1. Nyissa meg a csatlakoztatott katalógus, és konfigurálja a katalógust úgy, hogy **engedélyezése vagy letiltása** megosztott képeket a virtuális gép létrehozásához.

    ![Engedélyezése vagy letiltása](./media/configure-shared-image-gallery/enable-disable.png)
1. Labor felhasználók ezután hozhat létre egy virtuális gépet a engedélyezett képekkel kattintva **+ Hozzáadás** és lévő lemezkép keresése a **válassza ki az alapszintű** lap.

    ![Tesztkörnyezet-felhasználók](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Az Azure Resource Manager sablonjainak használata

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>A labor egy megosztott lemezkép-katalógus csatolása
Csatlakoztassa a megosztott lemezkép-katalógusában a labor használata az Azure Resource Manager-sablon, hozzá kell azt a Resource Manager-sablon erőforrás szakaszába alatt az alábbi példában látható módon:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "[parameters('newLabName')]",
    "location": "[resourceGroup (). location]",
    "resources": [
    {
        "apiVersion": "2018-10-15-preview",
        "name": "[variables('labVirtualNetworkName')]",
        "type": "virtualNetworks",
        "dependsOn": [
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    },
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"[parameters('existingSharedGalleryId')]",
            "allowAllImages": "Enabled"
        },
        "dependsOn":[
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    }
    ]
} 

```

Egy teljes Resource Manager-sablon példát tekintse meg ezeket a nyilvános GitHub-adattár Resource Manager-sablonminták: [Labor létrehozása közben egy megosztott képgyűjtemény konfigurálása](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>Hozzon létre egy Virtuálisgép-lemezkép használatával a megosztott lemezkép-katalógusban
Ha egy megosztott kép katalóguslemezt használó virtuális gép létrehozása Azure Resource Manager-sablonnal használ, használja a következő mintát:

```json

"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs/virtualMachines",
    "name": "[variables('resourceName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "sharedImageId": "[parameters('existingSharedImageId')]",
        "size": "[parameters('newVMSize')]",
        "isAuthenticationWithSshKey": false,
        "userName": "[parameters('userName')]",
        "sshKey": "",
        "password": "[parameters('password')]",
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "labSubnetName": "[variables('labSubnetName')]"
    }
}
],

```

További tudnivalókért tekintse meg a Resource Manager-sablonminták a nyilvános GitHub.
[Hozzon létre egy megosztott kép katalóguslemezt használó virtuális gép](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage).

## <a name="use-api"></a>API-val

- API verzió 2018-10-15-preview használja.
- Csatlakoztassa a katalógusban, a kérelem küldése, az alábbi kódrészletben látható módon:
    
    ``` 
    PUT [Lab Resource Id]/SharedGalleries/[newGalleryName]
    Body: 
    {
        “properties”:{
            “galleryId”: “[Shared Image Gallery resource Id]”,
            “allowAllImages”:”Enabled”
        }
    }
    ```
- Az összes rendszerkép megtekintéséhez a megosztott rendszerkép-katalógusában, listázhatja az összes megosztott rendszerképekre, valamint az erőforrás-azonosítók szerint

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- Hozzon létre egy virtuális gép megosztott rendszerképekből, hajtsa végre a PUT, a virtuális gépek és a virtuális gép tulajdonságai, adja át az előző hívásából származó megosztott rendszerképet Azonosítót. A tulajdonságai. SharedImageId


## <a name="next-steps"></a>További lépések
Az összetevők a következő cikkekben talál:

- [Adja meg a kötelező összetevők a tesztkörnyezethez](devtest-lab-mandatory-artifacts.md)
- [Egyéni összetevők létrehozása](devtest-lab-artifact-author.md)
- [Az összetevőtárban hozzáadása egy laborhoz](devtest-lab-artifact-author.md)
- [Az összetevők hibáinak diagnosztizálása](devtest-lab-troubleshoot-artifact-failure.md)
