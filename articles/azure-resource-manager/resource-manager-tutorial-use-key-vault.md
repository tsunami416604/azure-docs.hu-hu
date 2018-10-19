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
ms.date: 10/10/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 46291fe425e5d0fd1c142960a250c5601bd8ad29
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078002"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Oktatóanyag: Az Azure Key Vault integrálása a Resource Manager-sablon üzembehelyezési folyamatába

Megtudhatja, hogyan kérhet le titkos értékeket az Azure Key Vaultból, és hogyan adhatja ezeket tovább paraméterekként a Resource Manager üzembe helyezése során. Az érték sosem lesz nyilvános, mert Ön csak a Key Vault-azonosítójára hivatkozik. További információért lásd azt a cikket, amely azzal foglalkozik, hogyan lehet [használni az Azure Key Vaultot biztonságos paraméterértékek megadásához az üzembe helyezés során](./resource-manager-keyvault-parameter.md).

Ebben az oktatóanyagban létre fog hozni egy virtuális gépet és néhány függő erőforrást ugyanazzal a sablonnal, amelyet az [Oktatóanyag: Függő erőforrásokkal ellátott Azure Resource Manager-sablonok létrehozása](./resource-manager-tutorial-create-templates-with-dependent-resources.md) témakörben is használt. A virtuális gép rendszergazdai jelszavát az Azure Key Vaultból kéri le a rendszer.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A Key Vault előkészítése
> * Gyorsindítási sablon megnyitása
> * A paraméterfájl szerkesztése
> * A sablon üzembe helyezése
> * Az üzembe helyezés ellenőrzése
> * Az erőforrások eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/) [Resource Manager Tools bővítménnyel](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="prepare-the-key-vault"></a>A Key Vault előkészítése

Ebben a szakaszban egy Key Vault-tárolót és egy titkos kódot fog létrehozni egy Azure Resource Manager-sablon használatával. Ez a sablon:

* Létrehoz egy Key Vault-tárolót egy engedélyezett **enabledForTemplateDeployment** tulajdonsággal. Ezt a tulajdonságot igaz értékre kell beállítani, mielőtt a sablon üzembe helyezési folyamata hozzáférhetne a Key Vault-tárolóban meghatározott titkos kódokhoz.
* Adjon hozzá egy titkos kódot a Key Vault-tárolóhoz.  Ez a titkos kód tárolja a virtuális gép rendszergazdájának jelszavát.

Ha Ön (mint a virtuálisgép-sablont üzembe helyező felhasználó) nem a Key Vault-tároló tulajdonosa vagy közreműködője, a tulajdonosnak vagy a közreműködőnek hozzáférést kell biztosítania az Ön számára a Key Vault Microsoft.KeyVault/vaults/deploy/action engedélyéhez. További információért lásd azt a cikket, amely azzal foglalkozik, hogyan lehet [használni az Azure Key Vaultot biztonságos paraméterértékek megadásához az üzembe helyezés során](./resource-manager-keyvault-parameter.md).

A sablonnak szüksége van az ÖN Azure AD-felhasználói objektumazonosítójára az engedélyek konfigurálásához. Az alábbi eljárás lekéri az objektumazonosítót (GUID), és a rendszergazdai jelszót is létrehozza. A szórásos jelszófeltöréses támadások megelőzése érdekében a rendszer által létrehozott jelszavak használatát javasoljuk.

1. Futtassa az alábbi Azure PowerShell- vagy Azure CLI-parancsot.  

    ```azurecli-interactive
    az ad user show --upn-or-object-id "<Your User Principle Name>" --query "objectId"
    openssl rand -base64 32
    ```
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Input your user principal name (email address) used to sign in to Azure"
    (Get-AzureADUser -ObjectId $upn).ObjectId
    openssl rand -base64 32
    ```
2. Jegyezze fel az objektumazonosítót és a létrehozott jelszót, mert később szüksége lesz rájuk.
3. Ellenőrizze, hogy a létrejött jelszó megfelel-e a virtuális gép jelszókövetelményeinek. Az egyes Azure-szolgáltatások különböző jelszókövetelményeket szabnak meg. A virtuális gépek jelszókövetelményeiért tekintse át [a virtuális gépek létrehozásakor érvényesülő jelszókövetelményeket](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) ismertető szakaszt.

Key Vault létrehozása:

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy Key Vault-tárolót és egy titkos Key Vault-kódot.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" /></a>

2. Válassza ki vagy adja meg a következő értékeket.  Az értékek megadása után ne válassza a **Vásárlás** lehetőséget.

    ![Resource Manager-sablon Key Vault-integrációjának üzembe helyezési portálja](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: adjon megy egyedi nevet. Jegyezze fel ezt a nevet, mivel ugyanezt az erőforráscsoportot fogja használni a virtuális gép üzembe helyezéséhez a következő szakaszban. Ha a Key Vault-tárolót és a virtuális gépet is ugyanabba az erőforráscsoportba helyezi, az oktatóanyag végén könnyebb törölni az erőforrást.
    * **Hely**: válasszon ki egy helyet.  Az alapértelmezett hely az **USA középső régiója**.
    * **Key Vault neve**: adjon megy egyedi nevet. 
    * **Bérlőazonosító**: a sablonfüggvény automatikusan lekéri a bérlőazonosítót.  Ne módosítsa az alapértelmezett értéket.
    * **AD felhasználói azonosító**: adja meg Azure AD-felhasználói objektumazonosítóját, amelyet az előző eljárásban kért le.
    * **Titkos kód neve**: Az alapértelmezett név az **mAdminPassword**. Ha itt módosítja a titkos kód nevét, a virtuális gép üzembe helyezésekor is frissítenie kell.
    * **Titkos kód értéke**: Adja meg a titkos kódot.  A titkos kód a virtuális gépbe való bejelentkezéshez használt jelszó. Javasolt az előző eljárásban létrehozott jelszót használni.
    * **Elfogadom a fenti feltételeket és kikötéseket**: Válassza ki.
3. Válassza a **Paraméterek szerkesztése** lehetőséget a képernyő felső részén a sablon megtekintéséhez.
4. Lépjen a JSON-fájl 28. sorához. Ez a Key Vault erőforrás-definíciója.
5. Lépjen a 35. sorhoz:

    ```json
    "enabledForTemplateDeployment": true,
    ```
    Az `enabledForTemplateDeployment` egy Key Vault-tulajdonság. Ezt a tulajdonságot igaz értékre kell beállítani, mielőtt az üzembe helyezés során lekérhetné a titkos kódokat a Key Vault-tárolóból. 
6. Lépjen a 89. sorhoz. Ez a Key Vault titkos kódjának definíciója.
7. Válassza az **Elvetés** lehetőséget a lap alján. Nem hajtott végre módosításokat.
8. Ellenőrizze, hogy megadott-e az előző képernyőképen látható minden értéket, majd kattintson a **Vásárlás** elemre a lap alján.
9. Válassza a harang ikont (értesítés) a lap tetején az **Értesítések** panel megnyitásához. Várjon, amíg az erőforrás üzembe helyezése sikeresen befejeződik.
8. Válassza az **Ugrás az erőforráscsoportra** lehetőséget az **Értesítések** panelen. 
9. A megnyitásához kattintson a Key Vault-tároló nevére.
10. A bal oldali panelen válassza a **Hozzáférési szabályzatok** lehetőséget. A neve (Active Directory) meg kell, hogy jelenjen a listán, különben nem rendelkezik a Key Vault-tároló eléréséhez szükséges engedéllyel.
11. Válassza a **Kattintson a speciális hozzáférési szabályzatok megtekintéséhez** lehetőséget. Figyelje meg, hogy ki van választva az a beállítás, amely **engedélyezi a hozzáférést az Azure Resource Managerhez a sablon üzembe helyezése során**. A Key Vault-integráció enélkül nem valósítható meg.

    ![Resource Manager-sablon Key Vault-integrációjának hozzáférési szabályzatai](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)    
12. A bal oldali panelen válassza a **Tulajdonságok** lehetőséget.
13. Készítsen másolatot az **erőforrás azonosítójáról**. Erre az azonosítóra szüksége lesz a virtuális gép üzembe helyezésekor.  Az erőforrás azonosítójának formátuma:

    ```
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
4. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.
5. Ismételje meg az 1–4. lépést a következő URL-cím megnyitásához, majd mentse a fájlt **azuredeploy.parameters.json** néven.

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
    Az **id** elemet cserélje le az előző eljárásban létrehozott Key Vault-tároló erőforrás-azonosítójára.  

    ![A Key Vault és a Resource Manager-sablon integrációja, a virtuális gép üzembehelyezési paraméterfájljai](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Adja meg a következők értékét:

    * **adminUsername**: a virtuális gép rendszergazdai fiókjának neve.
    * **dnsLabelPrefix**: a dnsLablePrefix neve.
4. Mentse a módosításokat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kövesse [a sablon üzembe helyezését](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) ismertető témakörben található utasításokat a sablon üzembe helyezéséhez. Az **azuredeploy.json** és az **azuredeploy.parameters.json** fájlt is fel kell töltenie a Cloud Shellbe, majd a következő PowerShell-szkripttel helyezze üzembe a sablont:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name of the Key Vault"
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
```

A sablon üzembe helyezésekor használja ugyanazt az erőforráscsoportot, amelyet a Key Vault-tároló is használ. Ez egyszerűbbé teszi az erőforrások törlését. Kettő helyett csak egy erőforráscsoportot kell majd törölnie.

## <a name="valid-the-deployment"></a>Az üzembe helyezés ellenőrzése

A virtuális gép sikeres üzembe helyezése után tesztelje a bejelentkezést a Key Vaultban tárolt jelszóval.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Válassza az **Erőforráscsoportok**/**SajátErőforráscsoportNeve>**/**simpleWinVM** lehetőséget.
3. Válassza a **csatlakozás** lehetőséget a lap tetején.
4. Válassza az **RDP-fájl letöltése** lehetőséget, majd kövesse az utasításokat a virtuális gépbe való bejelentkezéshez a Key Vaultban tárolt jelszóval.

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
