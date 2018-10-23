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
ms.date: 10/09/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 50f1c81f08787181de2fe3a9f6fb97a96a2bd882
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114312"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Oktatóanyag: Függő erőforrásokkal ellátott Azure Resource Manager-sablonok létrehozása

Ismerje meg, hogyan hozhat létre több erőforrás üzembe helyezésére alkalmas Azure Resource Manager-sablont.  A sablon létrehozását követően az üzembe helyezés az Azure Portal Cloud Shelljének használatával történik.

Az oktatóanyag során egy tárfiókot, egy virtuális gépet, egy virtuális hálózatot és néhány egyéb függő erőforrást fog létrehozni. Bizonyos erőforrások nem helyezhetők üzembe addig, amíg egy másik erőforrás létre nem lett hozva. Nem hozhat létre például virtuális gépet addig, amíg annak tárfiókja és hálózati adaptere létre nem lett hozva. Ezt a kapcsolatot úgy definiáljuk, hogy egy adott erőforrás más erőforrásokkal áll függőségi viszonyban. A Resource Manager kiértékeli az erőforrások közötti függőségeket, majd azokat függőségi sorrendben üzembe helyezi. Ha az erőforrások között nincs függőségi viszony, akkor a Resource Manager párhuzamosan helyezi üzembe azokat. További információkat [az erőforrások Azure Resource Manager-sablonokban történő üzembehelyezési sorrendjének meghatározását](./resource-group-define-dependencies.md) ismertető témakörben talál.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A Key Vault előkészítése
> * Gyorsindítási sablon megnyitása
> * A sablon vizsgálata
> * A paraméterfájl szerkesztése
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/) a Resource Manager Tools bővítménnyel.  Lásd [a bővítmény telepítését](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites) ismertető részt.

## <a name="prepare-key-vault"></a>A Key Vault előkészítése

A szórásos jelszófeltöréses támadások megelőzése érdekében javasoljuk a rendszer által automatikusan létrehozott jelszavak használatát a virtuális gép rendszergazda fiókjához, valamint a Key Vault használatát a jelszó tárolására. Az alábbi eljárás létrehoz egy Key Vault-tárolót és egy titkos kódot a jelszó tárolására. Ezen felül konfigurálja a sablonok üzembe helyezéséhez szükséges engedélyeket a Key Vaultban tárolt titkos kódhoz való hozzáféréshez. Ha a Key Vault másik Azure-előfizetés alatt van, további hozzáférési szabályzatokra is szükség lesz. További információért lásd azt a cikket, amely azzal foglalkozik, hogyan lehet [használni az Azure Key Vaultot biztonságos paraméterértékek megadásához az üzembe helyezés során](./resource-manager-keyvault-parameter.md).

1. Jelentkezzen be az [Azure Cloud Shellbe](https://shell.azure.com).
2. Váltson arra környezetre a bal felső sarokban, amelyet használni szeretne. Ez a **PowerShell** vagy a **Bash** lehet.
3. Futtassa az alábbi Azure PowerShell- vagy Azure CLI-parancsot.  

    ```azurecli-interactive
    keyVaultName='<your-unique-vault-name>'
    resourceGroupName='<your-resource-group-name>'
    location='Central US'
    userPrincipalName='<your-email-address-associated-with-your-subscription>'
    
    # Create a resource group
    az group create --name $resourceGroupName --location $location
    
    # Create a Key Vault
    keyVault=$(az keyvault create \
      --name $keyVaultName \
      --resource-group $resourceGroupName \
      --location $location \
      --enabled-for-template-deployment true)
    keyVaultId=$(echo $keyVault | jq -r '.id')
    az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

    # Create a secret
    password=$(openssl rand -base64 32)
    az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: $keyVaultId."
    ```

    ```azurepowershell-interactive
    $keyVaultName = "<your-unique-vault-name>"
    $resourceGroupName="<your-resource-group-name>"
    $location='Central US'
    $userPrincipalName="<your-email-address-associated-with-your-subscription>"
    
    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
    # Create a Key Vault
    $keyVault = New-AzureRmKeyVault `
      -VaultName $keyVaultName `
      -resourceGroupName $resourceGroupName `
      -Location $location `
      -EnabledForTemplateDeployment
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
      
    # Create a secret
    $password = openssl rand -base64 32
    
    $secretValue = ConvertTo-SecureString $password -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretValue
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: " $keyVault.ResourceID
    ```
4. Jegyezze fel a kimeneti értékeket. Az oktatóanyag későbbi részében még szüksége lesz rájuk.

> [!NOTE]
> Az egyes Azure-szolgáltatások különböző jelszókövetelményeket szabnak meg. Az Azure-beli virtuális gépek követelményei például megtalálhatók a virtuális gépek létrehozásakor érvényesülő jelszókövetelményeket ismertető cikkben.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure-beli gyorsindítási sablonok a Resource Manager-sablonok adattáraként szolgálnak. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.
5. Ismételje meg az 1–4. lépést a **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json** megnyitásához, majd mentse a fájlt **azuredeploy.parameters.json** néven.

## <a name="explore-the-template"></a>A sablon vizsgálata

Amikor ebben a szakaszban a sablont vizsgálja, próbálja megválaszolni a következő kérdéseket:

- Hány Azure-erőforrás van meghatározva ebben a sablonban?
- Az egyik erőforrás egy Azure-tárfiók.  A definíció hasonlít a legutóbbi oktatóanyagban használtra?
- Hogyan tudja megkeresni a sablonban meghatározott erőforrásokhoz a sablonreferenciákat?
- Hogyan tudja megkeresni az erőforrások függőségeit?

1. A Visual Studio Code-ban csukja össze az elemeket mindaddig, amíg csak az első szintű és a **resources** (erőforrások) alatt lévő második szintű elemek lesznek láthatók:

    ![Visual Studio Code – Azure Resource Manager-sablonok](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    A sablon öt erőforrást határoz meg.
2. Bontsa ki az első erőforrást. Ez egy tárfiók. A definíciónak az előző oktatóanyag elején használttal azonosnak kell lennie.

    ![Visual Studio Code – Azure Resource Manager-sablonok, tárfiók-definíciók](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Bontsa ki a második erőforrást. Az erőforrástípus **Microsoft.Network/publicIPAddresses**. A sablonreferencia megkereséséhez lépjen a [sablonreferenciához](https://docs.microsoft.com/azure/templates/), majd a **Filter by title (Szűrés cím alapján)** mezőben adja meg a **nyilvános IP-címet** vagy a **nyilvános IP-címeket**. Hasonlítsa össze az erőforrás-definíciót a sablonreferenciával.

    ![Visual Studio Code – Azure Resource Manager-sablonok, nyilvános IP-cím definíciója](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. A sablonban meghatározott további erőforrások sablonreferenciáinak megkereséséhez ismételje meg az utolsó lépést.  Hasonlítsa össze az erőforrás-definíciókat a referenciákkal.
5. Bontsa ki a negyedik erőforrást:

    ![Visual Studio Code – Azure Resource Manager-sablonok – dependsOn](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    A dependsOn elem lehetővé teszi, hogy egy adott erőforrást egy vagy több erőforrástól függőként lehessen meghatározni. Ebben a példában ez az erőforrás a networkInterface.  Ez az erőforrás a következő két másik erőforrástól függ:

    * publicIPAddress
    * virtualNetwork

6. Bontsa ki az ötödik erőforrást. Ez az erőforrás egy virtuális gép. Ez az erőforrás a következő két másik erőforrástól függ:

    * storageAccount
    * networkInterface

A következő ábrán az erőforrások és az ehhez a sablonhoz tartozó függőségi adatok láthatók:

![Visual Studio Code – Azure Resource Manager-sablonok – függőségi ábra](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

A függőségek megadásával a Resource Manager már hatékonyan képes üzembe helyezni a megoldást. Mivel nincs közöttük függőség, a tárfiók, a nyilvános IP-cím és a virtuális hálózat párhuzamosan lesz létrehozva. A nyilvános IP-cím és a virtuális hálózat után a hálózati adapter üzembe helyezése következik. A Resource Manager azt követően helyezi üzembe helyezve, hogy az összes többi erőforrás üzembe helyezése megtörtént.

## <a name="edit-the-parameters-file"></a>A paraméterfájl szerkesztése

A sablonfájlt nem kell módosítania. Módosítania kell azonban a paraméterfájlt, hogy le tudja kérni a rendszergazdai jelszót a Key Vaultból.

1. Ha még nincs megnyitva, nyissa meg az **azuredeploy.parameters.json** fájlt a Visual Studio Code-ban.
2. Frissítse az **adminPassword** paramétert a következőre:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```
    Az **id** elemet cserélje le az előző eljárásban létrehozott Key Vault-tároló erőforrás-azonosítójára. Ez az egyik kimenet. 

    ![A Key Vault és a Resource Manager-sablon integrációja, a virtuális gép üzembehelyezési paraméterfájljai](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Adja meg a következők értékét:

    - **adminUsername**: a virtuális gép rendszergazdai fiókjának neve.
    - **dnsLabelPrefix**: a dnsLabelPrefix neve.
4. Mentse a módosításokat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablonok üzembe helyezésének számos módszere létezik.  Ebben az oktatóanyagban az Azure Portal Cloud Shelljét fogja használni.

1. Jelentkezzen be a [Cloud Shellbe](https://shell.azure.com). Azt is megteheti, hogy bejelentkezik az [Azure Portalra](https://portal.azure.com), majd kiválasztja a jobb felső sarokból a **Cloud Shellt** (lásd az alábbi ábrát):

    ![Azure Portal – Cloud Shell](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
2. Válassza a Cloud Shell bal felső sarkában található **PowerShell** elemet, majd a **Megerősítés** lehetőséget.  Ebben az oktatóanyagban a PowerShellt fogja használni.
3. A Cloud Shellben kattintson a **Fájl feltöltése** lehetőségre:

    ![Azure Portal – Cloud Shell – Fájl feltöltése](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Válassza ki az oktatóanyag korábbi részében mentett fájlokat. Az alapértelmezett nevek: **azuredeploy.json** és **azuredeploy.paraemters.json**.  Ha ilyen néven már léteznek fájlok, akkor a rendszer értesítés nélkül felülírja a régi fájlokat.
5. A fájl sikeres feltöltésének ellenőrzéséhez futtassa az alábbi parancsot a Cloud Shellben. 

    ```bash
    ls
    ```

    ![Azure Portal – Cloud Shell – Fájlok listázása](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    A képernyőképen a következő fájlnév látható: azuredeploy.json.

6. A JSON-fájl tartalmának ellenőrzéséhez futtassa az alábbi parancsot a Cloud Shellben:

    ```bash
    cat azuredeploy.json
    cat azuredeploy.parameters.json
    ```
7. Futtassa az alábbi PowerShell-parancsokat a Cloud Shellben. A mintaszkript a Key Vaulthoz létrehozott erőforráscsoportot használja, így ugyanis könnyebb lesz törölni az erőforrásokat.

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $deploymentName = "<Enter a deployment name>"

    New-AzureRmResourceGroupDeployment -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json `
        -TemplateparameterFile azuredeploy.parameters.json
    ```
8. Az alábbi PowerShell-parancs futtatásával megjelenítheti az újonnan létrehozott virtuális gépet:

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    A sablonban szereplő virtuálisgépnév (**SimpleWinVM**) nem módosítható.

9. Jelentkezzen be a virtuális gépre a rendszergazdai hitelesítő adatok teszteléséhez. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

A jelen oktatóanyagban létrehoz és üzembe helyez egy virtuális gép, egy virtuális hálózat és a függő erőforrások létrehozására szolgáló sablont. Az Azure-erőforrások feltételek alapján való üzembe helyezésével kapcsolatban lásd:


> [!div class="nextstepaction"]
> [Feltételek használata](./resource-manager-tutorial-use-conditions.md)

