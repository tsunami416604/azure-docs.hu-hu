---
title: Kettős hitelesítő adatok rotációs oktatóanyaga
description: Ebből az oktatóanyagból megtudhatja, hogyan automatizálható egy titkos kód elforgatása a kettős hitelesítő adatokat használó erőforrásokhoz.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: 9ab8a35808f94c04c1d57cd18a8d45b5a59c5160
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486976"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-dual-credential-authentication"></a>Egy titok elforgatásának automatizálása kettős hitelesítő adatok hitelesítését használó erőforrásokhoz

Az Azure-szolgáltatásokhoz való hitelesítés legjobb módja egy [felügyelt identitás](../general/managed-identity.md)használata, de vannak olyan helyzetek, amikor ez nem lehetséges. Ezekben az esetekben a rendszer hozzáférési kulcsokat vagy jelszavakat használ. A hozzáférési kulcsokat és jelszavakat gyakran el kell forgatni.

Ez az oktatóanyag bemutatja, hogyan automatizálható az adatbázisok és a kettős hitelesítő adatokat használó szolgáltatások titkainak rendszeres elforgatása. Ebben az oktatóanyagban az Azure Storage-fiók kulcsait elforgatja Azure Key Vault a titkokat Azure Event Grid értesítés által aktivált függvény használatával. :

> [!NOTE]
> A Storage-fiókok kulcsai automatikusan kezelhetők Key Vault a Storage-fiók delegált hozzáférésének közös hozzáférési aláírási jogkivonatokkal való ellátásával. Vannak olyan szolgáltatások, amelyekhez hozzáférési kulccsal rendelkező Storage-fiók kapcsolati sztringre van szükség, és ez a megoldás ajánlott

![Rotációs megoldás diagramja](../media/secrets/rotation-dual/rotation-diagram.png)

A fenti megoldásban Azure Key Vault tárolja a Storage-fiók egyedi hozzáférési kulcsait az elsődleges és másodlagos kulcs közötti, az azt követő verziókban található azonos titkos kulccsal rendelkező verziókként. Mivel az egyik hozzáférési kulcsot a titkos kulcs legújabb verziója tárolja, a rendszer újragenerálta az alternatív kulcsot, és hozzáadja azt a titok új és legújabb verziójának Key Vault. Ez a megoldás teljes rotációs ciklust biztosít az alkalmazások számára a legújabb újragenerált kulcsra való frissítéshez. 

1. a titkos kód lejárati dátuma előtt 30 nappal Key Vault közzéteszi a "közeljövőben" eseményt Event Grid.
1. Event Grid ellenőrzi az esemény-előfizetéseket, és HTTP POST használatával hívja meg az eseményre előfizetett Function app-végpontot.
1. A Function alkalmazás azonosítja a másodlagos kulcsot (a legutóbbi kivételével), és meghívja a Storage-fiókot az újbóli létrehozásához.
1. A Function alkalmazás új újragenerált kulcsot hoz létre a titkos kulcs új verziójának Azure Key Vaultéhez.

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure Key Vault
* Két Azure Storage-fiók

Ha nem rendelkezik meglévő Key Vault-és Storage-fiókkal, akkor az alábbi telepítési hivatkozás használható:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

1. Az **erőforráscsoport**területen válassza az **új létrehozása**lehetőséget. Nevezze el a csoport **akvrotation** , és kattintson **az OK**gombra.
1. Válassza a **felülvizsgálat + létrehozás**lehetőséget.
1. **Létrehozás** kiválasztása

    ![Erőforráscsoport létrehozása](../media/secrets/rotation-dual/dual-rotation-1.png)

Most már rendelkezik egy kulcstartóval és két Storage-fiókkal. A telepítőt az Azure CLI-ben ellenőrizheti a következő parancs futtatásával:

```azurecli
az resource list -o table -g akvrotation
```

Az eredmény a következő kimenettel fog kinézni:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>Storage-fiók kulcsának rotációs függvényének létrehozása és üzembe helyezése

Ezután hozzon létre egy, a rendszer által felügyelt identitással rendelkező Function alkalmazást a többi szükséges összetevőn kívül, és telepítse a Storage-fiók kulcsának rotációs funkcióit

A Function app rotációs funkciója megköveteli ezeket az összetevőket és konfigurációkat:
- Egy Azure App Service terv
- A Function app trigger felügyeletéhez szükséges Storage-fiók
- Hozzáférési szabályzat a titkok eléréséhez Key Vault
- A Storage-fiók kulcs-kezelője szolgáltatás szerepkörének hozzárendelésével elérheti az alkalmazást a Storage-fiók hozzáférési kulcsainak eléréséhez
- Storage-fiók kulcsának rotációs funkciója az Event triggerrel és a http-eseményindítóval (igény szerinti rotáció)
- EventGrid esemény-előfizetés a **SecretNearExpiry** eseményhez

1. Válassza ki az Azure-sablon központi telepítési hivatkozását:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

1. Az **erőforráscsoport** listában válassza a **akvrotation**lehetőséget.
1. A **Storage-fiók neve**mezőbe írja be a Storage-fiók nevét a forgatni kívánt elérési kulcsokkal.
1. A **Key Vault neve**mezőbe írja be a Key Vault nevét
1. A **Függvényalkalmazás neve**mezőbe írja be a Function alkalmazás nevét.
1. A **titok neve**mezőbe írja be a titkos kulcs nevét, ahol a hozzáférési kulcsok tárolása történik
1. A tárház **URL-címében**írja be a következőt: függvény kód GitHub helye ( **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** )
1. Válassza a **felülvizsgálat + létrehozás**lehetőséget.
1. **Létrehozás** kiválasztása

   ![Felülvizsgálat + létrehozás](../media/secrets/rotation-dual/dual-rotation-2.png)

Az előző lépések elvégzése után egy Storage-fiók, egy kiszolgálófarm, egy Function alkalmazás és egy Application-szolgáltatás is rendelkezésre áll. Az üzembe helyezés befejezése után az alábbi képernyő jelenik meg: az ![ üzembe helyezés befejeződött](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Ha bármilyen hiba merül fel, kattintson az újbóli **üzembe helyezés** elemre a fennmaradó összetevők telepítésének befejezéséhez.


A központi telepítési sablonok és a rotációs függvények kódja a [githubon](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)érhető el.

## <a name="add-storage-account-access-key-to-key-vault"></a>Adja hozzá a Storage-fiók hozzáférési kulcsát Key Vault

Először állítsa be a hozzáférési szabályzatot, és adja meg a felhasználók számára a *titkos kulcsok kezelésére* vonatkozó engedélyeket:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Most létrehozhat egy új titkot egy Storage-fiók elérési kulcsával értékként. Szüksége lesz a Storage-fiók erőforrás-AZONOSÍTÓra, a titkos kulcs érvényességi időtartamára, valamint a Secret értékre felvenni kívánt kulcs-AZONOSÍTÓra, így a rotációs függvény újra létrehozhatja a kulcsot a Storage-fiókban.

A Storage-fiók erőforrás-AZONOSÍTÓjának beolvasása. Az érték a tulajdonság alatt található. `id`
```azurecli
az storage account show -n akvrotationstorage
```

A Storage-fiók hozzáférési kulcsainak listázása a kulcsok értékének lekéréséhez

```azurecli
az storage account keys list -n akvrotationstorage 
```

A **key1Value** és a **storageAccountResourceId** lekért értékeinek feltöltése

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Egy rövid lejárati dátummal rendelkező titkos kulcs létrehozása `SecretNearExpiry` néhány percen belül közzé fog tenni egy eseményt, amely viszont aktiválja a függvényt a titok elforgatására.

A hozzáférési kulcsok újragenerálása a Storage-fiók kulcsainak beolvasása és a Key Vault titkos kulcs összevetésével ellenőrizhető.

Az alábbi paranccsal megjelenítheti a titkos adatokat:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Figyelje meg, hogy a frissítve lett, `CredentialId` `keyName` és `value` a rendszer újragenerálta a ![ titkos diasort](../media/secrets/rotation-dual/dual-rotation-4.png)

Hozzáférési kulcsok beolvasása az érték érvényesítéséhez
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Hozzáférési kulcsok listája](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>További Storage-fiókok hozzáadása a forgatáshoz

Ugyanaz a Function alkalmazás több Storage-fiók elforgatására is felhasználható. 

A meglévő függvények elforgatásához további Storage-fiókok kulcsainak hozzáadására van szükség:
- A Storage-fiók kulcs-kezelője szolgáltatás szerepkörének hozzárendelésével elérheti az alkalmazást a Storage-fiók hozzáférési kulcsainak eléréséhez
- EventGrid esemény-előfizetés a **SecretNearExpiry** eseményhez

1. Válassza ki az Azure-sablon központi telepítési hivatkozását:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

1. Az **erőforráscsoport** listában válassza a **akvrotation**lehetőséget.
1. A **Storage-fiók neve**mezőbe írja be a Storage-fiók nevét a forgatni kívánt elérési kulcsokkal.
1. A **Key Vault neve**mezőbe írja be a Key Vault nevét
1. A **Függvényalkalmazás neve**mezőbe írja be a Function alkalmazás nevét.
1. A **titok neve**mezőbe írja be a titkos kulcs nevét, ahol a hozzáférési kulcsok tárolása történik
1. Válassza a **felülvizsgálat + létrehozás**lehetőséget.
1. **Létrehozás** kiválasztása

   ![Felülvizsgálat + létrehozás](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Adjon hozzá egy másik Storage-fiókhoz való hozzáférési kulcsot Key Vault

A Storage-fiók erőforrás-AZONOSÍTÓjának beolvasása. Az érték a tulajdonság alatt található. `id`
```azurecli
az storage account show -n akvrotationstorage2
```

A Storage-fiók hozzáférési kulcsainak listázása a key2 értékének lekéréséhez

```azurecli
az storage account keys list -n akvrotationstorage2 
```

A **key2Value** és a **storageAccountResourceId** lekért értékeinek feltöltése

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Titkos adatok megjelenítése az alábbi paranccsal:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Figyelje meg, hogy a frissítve lett, `CredentialId` `keyName` és `value` a rendszer újragenerálta a ![ titkos diasort](../media/secrets/rotation-dual/dual-rotation-8.png)

Hozzáférési kulcsok beolvasása az érték érvényesítéséhez
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Hozzáférési kulcsok listája](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>Elérhető Key Vault kettős hitelesítő adatok elforgatási funkciói

- [Storage-fiók](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis Cache](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>Tudjon meg többet
- Áttekintés: [Key Vault figyelése Azure Event Grid (előzetes verzió)](../general/event-grid-overview.md)
- Útmutató: az [első függvény létrehozása a Azure Portalban](../../azure-functions/functions-create-first-azure-function.md)
- Útmutató: [e-mailek fogadása a Key Vault titkos változásairól](../general/event-grid-logicapps.md)
- [Azure Key Vault Azure Event Gridi esemény sémája (előzetes verzió)](../../event-grid/event-schema-key-vault.md)
