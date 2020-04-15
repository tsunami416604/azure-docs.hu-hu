---
title: Rövid útmutató – Azure Analysis Services-kiszolgálói erőforrás létrehozása az Azure Resource Manager-sablon használatával
description: Rövid útmutató, amely bemutatja, hogyan azure Analysis Services-kiszolgálói erőforrás egy Azure Resource Manager sablon használatával.
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: analysis-services
ms.topic: quickstart
ms.date: 04/14/2020
ms.custom: subject-armqs
ms.openlocfilehash: d292500c5e26d3c07ff2402964166b3928cc7e44
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384256"
---
# <a name="quickstart-create-a-server---azure-resource-manager-template"></a>Rövid útmutató: Kiszolgáló létrehozása – Azure Resource Manager-sablon

Ez a rövid útmutató ismerteti, hogyan hozhat létre egy Analysis Services-kiszolgálói erőforrást az Azure-előfizetésében egy Resource Manager-sablon használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p/).
* **Azure Active Directory**: Előfizetésének egy Azure Active Directory-bérlőhöz kell tartoznia. Emellett be kell jelentkeznie az Azure-ba az adott Azure Active Directoryban található fiókkal. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).

## <a name="create-a-server"></a>A kiszolgáló létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://azure.microsoft.com/resources/templates/101-analysis-services-create/)

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

A sablon egyetlen [Microsoft.AnalysisServices/servers](https://docs.microsoft.com/azure/templates/microsoft.analysisservices/2017-08-01/servers) erőforrást határoz meg tűzfalszabállyal. 

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza ki a következő Üzembe helyezés az Azure-ba hivatkozást az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon az Analysis Services kiszolgálói erőforrás létrehozására, valamint a szükséges és választható tulajdonságok megadására szolgál.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json"><img src="./media/analysis-services-create-template/deploy-azure.png" alt="deploy to azure"/></a>

2. Válassza ki vagy adja meg a következő értékeket.

    Eltérő rendelkezés hiányában használja az alapértelmezett értékeket.

    * **Előfizetés**: Válasszon egy Azure-előfizetést.
    * **Erőforráscsoport**: Kattintson **az Új létrehozása**gombra, majd adjon meg egy egyedi nevet az új erőforráscsoportnak.
    * **Hely**: Az erőforráscsoportban létrehozott erőforrások alapértelmezett helyének kiválasztása.
    * **Kiszolgáló neve**: Adja meg a kiszolgálói erőforrás nevét. 
    * **Hely**: Figyelmen kívül hagyva az Analysis Services számára. A hely a Kiszolgáló helye helyen van megadva.
    * **Kiszolgáló helye**: Adja meg az Analysis Services kiszolgáló helyét. Ez gyakran ugyanaz a régió, mint az erőforráscsoporthoz megadott alapértelmezett hely, de nem kötelező. Például **az USA északi középső régiója**. A támogatott régiókról az [Analysis Services rendelkezésre állása régiónként](analysis-services-overview.md#availability-by-region)című témakörben található.
    * **Sku neve**: Adja meg a létrehozásához az Analysis Services kiszolgáló sku nevét. Válasszon: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. A Termékváltozat elérhetősége a régiótól függ. S0 vagy D1 ajánlott az értékeléshez és a teszteléshez.
    * **Kapacitás**: Adja meg a lekérdezésreplika kibővített példányainak teljes számát. Egynél több példány horizontális felskálázása csak bizonyos régiókban támogatott.
    * **Tűzfal beállításai**: Adja meg a kiszolgálóhoz definiálandó bejövő tűzfalszabályokat. Ha nincs megadva, a tűzfal le van tiltva.
    * **Biztonsági mentési blobtároló uri:** Adja meg a SAS URI egy privát Azure Blob Storage tároló olvasási, írási és lista engedélyeket. Csak akkor kötelező, ha a [Biztonsági másolat/visszaállítás](analysis-services-backup.md)segédprogramot kívánja használni.
    * **Elfogadom a fenti feltételeket és kikötéseket**: Válassza ki.

3. Válassza a **Beszerzés** lehetőséget. A kiszolgáló sikeres telepítése után értesítést kap:

   ![Erőforrás-kezelő sablon, portálértesítés telepítése](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Az Azure Portalon vagy az Azure PowerShellben ellenőrizze az erőforráscsoport és a kiszolgálói erőforrás létrehozása.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, használja az Azure Portalon, az Azure CLI-t vagy az Azure PowerShellt az erőforráscsoport és a kiszolgálói erőforrás törléséhez.

# <a name="cli"></a>[parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Azure Resource Manager-sablont használt egy új erőforráscsoport és egy Azure Analysis Services-kiszolgálói erőforrás létrehozásához. Miután létrehozott egy kiszolgálói erőforrást a sablon használatával, vegye figyelembe az alábbiakat:
- [Gyors útmutató: Kiszolgáló létrehozása – PowerShell](analysis-services-create-powershell.md)
- [Mintamodell hozzáadása a portálról](analysis-services-create-sample-model.md)
- [A kiszolgáló rendszergazdájának és felhasználói szerepköreinek konfigurálása](tutorials/analysis-services-tutorial-roles.md)