---
title: Függő erőforrásokkal ellátott Azure Resource Manager-sablonok létrehozása | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre több erőforrással ellátott Azure Resource Manager-sablont, valamint hogyan helyezheti üzembe azt az Azure Portal használatával.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 22d7da30602bd83c7194cd7e016a0d4a2b9093ed
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310063"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Oktatóanyag: Függő erőforrások létrehozása Azure Resource Manager-sablonok

Ismerje meg, hogyan hozhat létre az Azure Resource Manager-sablon üzembe helyezése több erőforrást, és konfigurálja a telepítési sorrendet. A sablon létrehozását követően az üzembe helyezés az Azure Portal Cloud Shelljének használatával történik.

Az oktatóanyag során egy tárfiókot, egy virtuális gépet, egy virtuális hálózatot és néhány egyéb függő erőforrást fog létrehozni. Bizonyos erőforrások nem helyezhetők üzembe addig, amíg egy másik erőforrás létre nem lett hozva. Nem hozhat létre például virtuális gépet addig, amíg annak tárfiókja és hálózati adaptere létre nem lett hozva. Ezt a kapcsolatot úgy definiáljuk, hogy egy adott erőforrás más erőforrásokkal áll függőségi viszonyban. A Resource Manager kiértékeli az erőforrások közötti függőségeket, majd azokat függőségi sorrendben üzembe helyezi. Ha az erőforrások között nincs függőségi viszony, akkor a Resource Manager párhuzamosan helyezi üzembe azokat. További információkat [az erőforrások Azure Resource Manager-sablonokban történő üzembehelyezési sorrendjének meghatározását](./resource-group-define-dependencies.md) ismertető témakörben talál.

![Resource manager sablon tőle függő erőforrások telepítési sorrend diagram](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon vizsgálata
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/) a Resource Manager Tools bővítménnyel.  Lásd [a bővítmény telepítését](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites) ismertető részt.
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd: [oktatóanyag: Integrálhatja az Azure Key Vault Resource Manager-sablon üzembe helyezési](./resource-manager-tutorial-use-key-vault.md). Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure-beli gyorsindítási sablonok a Resource Manager-sablonok adattáraként szolgálnak. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.

## <a name="explore-the-template"></a>A sablon vizsgálata

Amikor ebben a szakaszban a sablont vizsgálja, próbálja megválaszolni a következő kérdéseket:

* Hány Azure-erőforrás van meghatározva ebben a sablonban?
* Az egyik erőforrás egy Azure-tárfiók.  A definíció hasonlít a legutóbbi oktatóanyagban használtra?
* Hogyan tudja megkeresni a sablonban meghatározott erőforrásokhoz a sablonreferenciákat?
* Hogyan tudja megkeresni az erőforrások függőségeit?

1. A Visual Studio Code-ban csukja össze az elemeket mindaddig, amíg csak az első szintű és a **resources** (erőforrások) alatt lévő második szintű elemek lesznek láthatók:

    ![Visual Studio Code – Azure Resource Manager-sablonok](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    A sablon öt erőforrást határoz meg:

    * `Microsoft.Storage/storageAccounts`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Érdemes megismerkedni a sablon alapvető működésével, mielőtt megkezdi annak testreszabását.

2. Bontsa ki az első erőforrást. Ez egy tárfiók. Hasonlítsa össze az erőforrás-definíciót a [sablonreferenciával](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).

    ![Visual Studio Code – Azure Resource Manager-sablonok, tárfiók-definíciók](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Bontsa ki a második erőforrást. Az erőforrástípus `Microsoft.Network/publicIPAddresses`. Hasonlítsa össze az erőforrás-definíciót a [sablonreferenciával](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).

    ![Visual Studio Code – Azure Resource Manager-sablonok, nyilvános IP-cím definíciója](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Bontsa ki a negyedik erőforrást. Az erőforrástípus `Microsoft.Network/networkInterfaces`:  

    ![Visual Studio Code – Azure Resource Manager-sablonok – dependsOn](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    A dependsOn elem lehetővé teszi, hogy egy adott erőforrást egy vagy több erőforrástól függőként lehessen meghatározni. Az erőforrás két másik erőforrástól függ:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

5. Bontsa ki az ötödik erőforrást. Ez az erőforrás egy virtuális gép. Ez az erőforrás a következő két másik erőforrástól függ:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

A következő ábrán az erőforrások és az ehhez a sablonhoz tartozó függőségi adatok láthatók:

![Visual Studio Code – Azure Resource Manager-sablonok – függőségi ábra](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

A függőségek megadásával a Resource Manager már hatékonyan képes üzembe helyezni a megoldást. Mivel nincs közöttük függőség, a tárfiók, a nyilvános IP-cím és a virtuális hálózat párhuzamosan lesz létrehozva. A nyilvános IP-cím és a virtuális hálózat után a hálózati adapter üzembe helyezése következik. A Resource Manager azt követően helyezi üzembe helyezve, hogy az összes többi erőforrás üzembe helyezése megtörtént.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A sablonok üzembe helyezésének számos módszere létezik.  Ebben az oktatóanyagban az Azure Portal Cloud Shelljét fogja használni.

1. Jelentkezzen be a [Cloud Shellbe](https://shell.azure.com). 
2. Válassza a Cloud Shell bal felső sarkában található **PowerShell** elemet, majd a **Megerősítés** lehetőséget.  Ebben az oktatóanyagban a PowerShellt fogja használni.
3. A Cloud Shellben kattintson a **Fájl feltöltése** lehetőségre:

    ![Azure Portal – Cloud Shell – Fájl feltöltése](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Válassza ki az oktatóanyag korábbi részében mentett sablont. Alapértelmezés szerint a fájl neve a következő: **azuredeploy.json**.  Ha ilyen néven már létezik fájl, a rendszer értesítés nélkül felülírja a régit.

    Lehetősége van a **ls $HOME** parancsot, és a **$HOME/azuredeploy.json macskakép** paranccsal ellenőrizheti, hogy a fájlok areis feltöltése sikeresen befejeződött. 

5. Futtassa az alábbi PowerShell-parancsokat a Cloud Shellben. A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Lásd: [Előfeltételek](#prerequisites).

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"
    ```

8. Az alábbi PowerShell-parancs futtatásával megjelenítheti az újonnan létrehozott virtuális gépet:

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    Get-AzVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    A sablonban szereplő virtuálisgépnév (**SimpleWinVM**) nem módosítható.

9. RDP-kapcsolattal csatlakozzon a virtuális géphez, hogy ellenőrizze, sikeresen létrejött-e a virtuális gép.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott és üzembe helyezett egy virtuális gép, egy virtuális hálózat és a függő erőforrások létrehozására szolgáló sablont. Az Azure-erőforrások feltételek alapján való üzembe helyezésével kapcsolatban lásd:

> [!div class="nextstepaction"]
> [Feltételek használata](./resource-manager-tutorial-use-conditions.md)
