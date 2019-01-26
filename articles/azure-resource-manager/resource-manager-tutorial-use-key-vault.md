---
title: Az Azure Key Vault integrálása a Resource Manager-sablon üzembehelyezési folyamatába | Microsoft Docs
description: Megtudhatja, hogyan használható az Azure Key Vault biztonságos paraméterértékek megadására a Resource Manager-sablon üzembe helyezése során
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/25/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 4b8e7f429cbe9ff8e71432ac8038c8ad15114711
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080906"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Oktatóanyag: Integrálhatja az Azure Key Vault Resource Manager-sablon telepítése

Ismerje meg, hogyan lehet beolvasni a titkos kulcsok Azure Key vault és a titkos kulcsok paraméterként átadni a Resource Manager üzembe helyezése során. Az érték sosem hagyja el, mert csak hivatkozhat a key vault azonosítója. További információért lásd azt a cikket, amely azzal foglalkozik, hogyan lehet [használni az Azure Key Vaultot biztonságos paraméterértékek megadásához az üzembe helyezés során](./resource-manager-keyvault-parameter.md).

Az [erőforrások üzembehelyezési sorrendjének beállítását](./resource-manager-tutorial-create-templates-with-dependent-resources.md) ismertető oktatóanyagban egy virtuális gépet, egy virtuális hálózatot és egyéb függő erőforrásokat fog létrehozni. Ebben az oktatóanyagban testre szabhatja a sablont a virtuális gép rendszergazdai jelszavának lekérése egy kulcstartót.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Key vault előkészítése
> * Gyorsindítási sablon megnyitása
> * A paraméterfájl szerkesztése
> * A sablon üzembe helyezése
> * Az üzembe helyezés ellenőrzése
> * Az erőforrások eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/) [Resource Manager Tools bővítménnyel](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Ellenőrizze, hogy a létrejött jelszó megfelel-e a virtuális gép jelszókövetelményeinek. Az egyes Azure-szolgáltatások különböző jelszókövetelményeket szabnak meg. A virtuális gépek jelszókövetelményeiért tekintse át [a virtuális gépek létrehozásakor érvényesülő jelszókövetelményeket](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) ismertető szakaszt.

## <a name="prepare-a-key-vault"></a>Key vault előkészítése

Ebben a szakaszban egy Resource Manager-sablon létrehozásához a key vault és a egy titkos kulcsot használja. Ez a sablon:

* A key vault létrehozása a `enabledForTemplateDeployment` tulajdonság lehetővé teszi. Ez a tulajdonság igaznak kell lennie, a sablon üzembe helyezési folyamat férjenek hozzá a meghatározott a kulcstartó titkos kulcsait.
* Titkos kód hozzáadása a kulcstartóhoz.  Ez a titkos kód tárolja a virtuális gép rendszergazdájának jelszavát.

Ha (mint a felhasználót, hogy a virtuálisgép-sablon üzembe helyezése) nem a tulajdonos vagy a közreműködői a key vault, a tulajdonos vagy közreműködő a key vault hozzáférést kell biztosítania, az a Microsoft.KeyVault/vaults/deploy/action engedéllyel a kulcstartó. További információért lásd azt a cikket, amely azzal foglalkozik, hogyan lehet [használni az Azure Key Vaultot biztonságos paraméterértékek megadásához az üzembe helyezés során](./resource-manager-keyvault-parameter.md).

A sablonnak szüksége van az ÖN Azure AD-felhasználói objektumazonosítójára az engedélyek konfigurálásához. Az alábbi eljárást az objektum Azonosítóját (GUID) beolvasása.

1. Futtassa az alábbi Azure PowerShell- vagy Azure CLI-parancsot.  

    ```azurecli-interactive
    echo "Enter your email address that is associated with your Azure subscription):" &&
    read upn &&
    az ad user show --upn-or-object-id $upn --query "objectId" &&
    ```
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Input your user principal name (email address) used to sign in to Azure"
    (Get-AzADUser -UserPrincipalName $upn).Id
    ```
2. Jegyezze fel az objektumazonosítót. Az oktatóanyag későbbi részében szüksége lesz.

A key vault létrehozása:

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy kulcstartót és a egy titkos kulcsot.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Válassza ki vagy adja meg a következő értékeket.  Az értékek megadása után ne válassza a **Vásárlás** lehetőséget.

    ![Resource Manager-sablon Key Vault-integrációjának üzembe helyezési portálja](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: adjon megy egyedi nevet. Jegyezze fel ezt a nevet, mivel ugyanezt az erőforráscsoportot fogja használni a virtuális gép üzembe helyezéséhez a következő szakaszban. Ugyanabban az erőforráscsoportban helyezi el a key vaulttal és a virtuális gép is megkönnyíti az oktatóanyag végén található az erőforrás megtisztítása.
    * **Hely**: válasszon ki egy helyet.  Az alapértelmezett hely az **USA középső régiója**.
    * **Key Vault neve**: adjon megy egyedi nevet. 
    * **Bérlőazonosító**: a sablonfüggvény automatikusan lekéri a bérlőazonosítót.  Ne módosítsa az alapértelmezett értéket.
    * **AD felhasználói azonosító**: adja meg Azure AD-felhasználói objektumazonosítóját, amelyet az előző eljárásban kért le.
    * **Název tajného kódu**: Alapértelmezés szerint ez **vmAdminPassword**. Ha itt módosítja a titkos kód nevét, a virtuális gép üzembe helyezésekor is frissítenie kell.
    * **Titkos érték**: Adja meg a titkos kód.  A titkos kód a virtuális gépbe való bejelentkezéshez használt jelszó. Javasolt az előző eljárásban létrehozott jelszót használni.
    * **Elfogadom a fenti feltételek és kikötések állapot**: Kiválasztás.
3. Válassza a **Paraméterek szerkesztése** lehetőséget a képernyő felső részén a sablon megtekintéséhez.
4. Lépjen a JSON-fájl 28. sorához. Ez a key vault erőforrás-definícióban.
5. Lépjen a 35. sorhoz:

    ```json
    "enabledForTemplateDeployment": true,
    ```
    Az `enabledForTemplateDeployment` egy Key Vault-tulajdonság. Ez a tulajdonság true kell, mielőtt lehet lekérdezni a titkos kulcsok a key vault üzembe helyezése során.
6. Lépjen a 89. sorhoz. Ez a Key Vault titkos kódjának definíciója.
7. Válassza az **Elvetés** lehetőséget a lap alján. Nem hajtott végre módosításokat.
8. Ellenőrizze, hogy megadott-e az előző képernyőképen látható minden értéket, majd kattintson a **Vásárlás** elemre a lap alján.
9. Válassza a harang ikont (értesítés) a lap tetején az **Értesítések** panel megnyitásához. Várjon, amíg az erőforrás üzembe helyezése sikeresen befejeződik.
10. Válassza az **Ugrás az erőforráscsoportra** lehetőséget az **Értesítések** panelen. 
11. Válassza ki a kulcstartó nevét a megnyitásához.
12. Válassza ki **titkok** a bal oldali ablaktáblán. **vmAdminPassword** ott szerepelnie.
13. A bal oldali panelen válassza a **Hozzáférési szabályzatok** lehetőséget. A neve (Active Directory) meg kell, hogy jelenjen a listán, különben nem rendelkezik a Key Vault-tároló eléréséhez szükséges engedéllyel.
14. Válassza a **Kattintson a speciális hozzáférési szabályzatok megtekintéséhez** lehetőséget. Figyelje meg, hogy ki van választva az a beállítás, amely **engedélyezi a hozzáférést az Azure Resource Managerhez a sablon üzembe helyezése során**. Ez a beállítás akkor egy másik feltétel, hogy a Key Vault-integráció működjön.

    ![Resource Manager-sablon Key Vault-integrációjának hozzáférési szabályzatai](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)
15. A bal oldali panelen válassza a **Tulajdonságok** lehetőséget.
16. Készítsen másolatot az **erőforrás azonosítójáról**. Erre az azonosítóra szüksége lesz a virtuális gép üzembe helyezésekor.  Az erőforrás azonosítójának formátuma:

    ```json
    /subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
    ```

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure-beli gyorsindítási sablonok a Resource Manager-sablonok adattáraként szolgálnak. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt. Ez ugyanaz a forgatókönyv, amelyet az [Oktatóanyag: Függő erőforrásokkal ellátott Azure Resource Manager-sablonok létrehozása](./resource-manager-tutorial-create-templates-with-dependent-resources.md) részben használt.
4. A sablon öt erőforrást határoz meg:

    * `Microsoft.Storage/storageAccounts`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Érdemes megismerkedni a sablon alapvető működésével, mielőtt megkezdi annak testreszabását.
5. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.
6. Ismételje meg az 1–4. lépést a következő URL-cím megnyitásához, majd mentse a fájlt **azuredeploy.parameters.json** néven.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>A paraméterfájl szerkesztése

A sablonfájlt nem kell módosítania.

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
    Cserélje le a **azonosító** az utolsó eljárás során létrehozott a key vault erőforrás-azonosító.  

    ![A Key Vault és a Resource Manager-sablon integrációja, a virtuális gép üzembehelyezési paraméterfájljai](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Adja meg a következők értékét:

    * **adminUsername**: a virtuális gép rendszergazdai fiókjának neve.
    * **dnsLabelPrefix**: a dnsLabelPrefix neve.
4. Mentse a módosításokat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kövesse [a sablon üzembe helyezését](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) ismertető témakörben található utasításokat a sablon üzembe helyezéséhez. Az **azuredeploy.json** és az **azuredeploy.parameters.json** fájlt is fel kell töltenie a Cloud Shellbe, majd a következő PowerShell-szkripttel helyezze üzembe a sablont:

```azurepowershell
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmResourceGroupDeployment -Name $deploymentName `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

A sablon központi telepítése esetén használja a key vault ugyanabban az erőforráscsoportban. Ez egyszerűbbé teszi az erőforrások törlését. Kettő helyett csak egy erőforráscsoportot kell majd törölnie.

## <a name="valid-the-deployment"></a>Az üzembe helyezés ellenőrzése

Miután sikeresen telepítette a virtuális gép, tesztelje a bejelentkezést, a key vaultban tárolt jelszó segítségével.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Válassza az **Erőforráscsoportok**/**SajátErőforráscsoportNeve>**/**simpleWinVM** lehetőséget.
3. Válassza a **csatlakozás** lehetőséget a lap tetején.
4. Válassza ki **RDP-fájl letöltése** és kövesse az utasításokat követve jelentkezzen be a virtuális géppel a key vaultban tárolt jelszó segítségével.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban lekért egy titkos kódot az Azure Key Vaultból, amelyet felhasznált a sablon üzembe helyezése során.  Ha meg szeretné tudni, hogyan hozhat létre csatolt sablonokat, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Csatolt sablonok létrehozása](./resource-manager-tutorial-create-linked-templates.md)
