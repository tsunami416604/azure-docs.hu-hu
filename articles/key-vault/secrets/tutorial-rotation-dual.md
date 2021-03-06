---
title: A két hitelesítő adatokkal rendelkező erőforrások rotációs oktatóanyaga
description: Ebből az oktatóanyagból megtudhatja, hogyan automatizálható a titkos kód elforgatása olyan erőforrásokhoz, amelyek két hitelesítési hitelesítő adatot használnak.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: 097b5c7d71076c11cdc30fce618f3a4ac4ef67a1
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655458"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>A titkos kulcs elforgatásának automatizálása két hitelesítési hitelesítő adattal rendelkező erőforrásokhoz

Az Azure-szolgáltatásokhoz való hitelesítés legjobb módja egy [felügyelt identitás](../general/authentication.md)használata, de vannak olyan helyzetek, amikor ez nem lehetséges. Ezekben az esetekben a rendszer hozzáférési kulcsokat vagy jelszavakat használ. Gyakran kell elforgatni a hozzáférési kulcsokat és a jelszavakat.

Ez az oktatóanyag bemutatja, hogyan automatizálható a titkok rendszeres elforgatása olyan adatbázisokhoz és szolgáltatásokhoz, amelyek két hitelesítési hitelesítő adatot használnak. Ez az oktatóanyag azt mutatja be, hogyan lehet elforgatni az Azure Storage-fiók kulcsait a Azure Key Vaultban titokként tárolva. Azure Event Grid értesítés által aktivált függvényt kell használnia. 

> [!NOTE]
> A Storage-fiókok kulcsai automatikusan kezelhetők Key Vaultban, ha megosztott hozzáférési aláírási jogkivonatokat biztosít a Storage-fiókhoz delegált hozzáféréshez. Vannak olyan szolgáltatások, amelyekhez hozzáférési kulcsok szükségesek a Storage-fiók kapcsolati karakterláncai számára. Ebben a forgatókönyvben ezt a megoldást ajánljuk.

Itt látható az oktatóanyagban ismertetett rotációs megoldás: 

![Az elforgatási megoldást bemutató diagram.](../media/secrets/rotation-dual/rotation-diagram.png)

Ebben a megoldásban Azure Key Vault a Storage-fiókhoz tartozó egyéni hozzáférési kulcsokat azonos titkos verzióként tárolja, a későbbi verziókban pedig az elsődleges és a másodlagos kulcs között váltakozik. Ha az egyik hozzáférési kulcsot a titkos kulcs legújabb verziójában tárolja a rendszer, akkor a rendszer újból létrehozza a másodlagos kulcsot, és hozzáadja a Key Vaulthoz a titok új verziójaként. A megoldás biztosítja az alkalmazás teljes rotációs ciklusát a legújabb újragenerált kulcsra való frissítéshez. 

1. Harminc nappal a titok lejárati dátuma előtt Key Vault közzéteszi a közeljövőben lejáró eseményt Event Grid.
1. Event Grid ellenőrzi az esemény-előfizetéseket, és HTTP POST használatával hívja meg az eseményre előfizetett Function app-végpontot.
1. A Function alkalmazás azonosítja a másodlagos kulcsot (nem a legújabbat), és meghívja a Storage-fiókot az újbóli létrehozásához.
1. A Function alkalmazás hozzáadja az új újragenerált kulcsot, hogy a titkos kulcs új verziója Azure Key Vault.

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault.
* Két Azure Storage-fiók.

Ezt a telepítési hivatkozást akkor használhatja, ha nem rendelkezik meglévő kulcstartóval és meglévő Storage-fiókokkal:

[![Az Azure-ba felcímkézett hivatkozás.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget. Nevezze el a csoport **akvrotation** , majd kattintson **az OK gombra**.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

    ![Az erőforráscsoport létrehozásának módját bemutató képernyőkép.](../media/secrets/rotation-dual/dual-rotation-1.png)

Most már rendelkezik egy Key vaulttal és két Storage-fiókkal. A telepítőt az Azure CLI-ben ellenőrizheti a következő parancs futtatásával:

```azurecli
az resource list -o table -g akvrotation
```

Az eredmény az alábbihoz hasonló kimenetet fog kinézni:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>A Key rotációs függvény létrehozása és üzembe helyezése

Ezután létre kell hoznia egy, a rendszer által felügyelt identitással rendelkező Function alkalmazást az egyéb szükséges összetevőkön kívül. A Storage-fiók kulcsainak rotációs függvényét is telepítenie kell.

A Function app rotációs funkciója a következő összetevőket és konfigurációkat igényli:
- Egy Azure App Service terv
- A Function app-eseményindítók kezelésére szolgáló Storage-fiók
- Hozzáférési szabályzat a titkok eléréséhez Key Vault
- A Function alkalmazáshoz rendelt Storage-fiók kulcs-kezelő szolgáltatásának szerepköre, hogy hozzáférhessen a Storage-fiók hozzáférési kulcsaihoz
- Egy Key rotációs függvény eseményvezérelt eseményindítóval és HTTP-eseményindítóval (igény szerinti rotáció)
- Event Grid esemény-előfizetés a **SecretNearExpiry** eseményhez

1. Válassza ki az Azure-sablon központi telepítési hivatkozását: 

   [![Azure-sablonok üzembe helyezési hivatkozása.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. Az **erőforráscsoport** listában válassza a **akvrotation** lehetőséget.
1. A **Storage-fiók RG** mezőjébe írja be annak az erőforráscsoportnak a nevét, amelyben a Storage-fiók található. Tartsa meg az alapértelmezett **[resourceGroup (). name]** értéket, ha a Storage-fiókja már ugyanabban az erőforráscsoportban található, ahol a Key rotációs funkciót telepíteni fogja.
1. A **Storage-fiók neve** mezőbe írja be annak a Storage-fióknak a nevét, amely a forgatni kívánt hozzáférési kulcsokat tartalmazza.
1. A **Key Vault RG** mezőben adja meg az erőforráscsoport nevét, amelyben a kulcstartó található. Tartsa meg az alapértelmezett **[resourceGroup (). name]** értéket, ha a kulcstartó már létezik ugyanabban az erőforráscsoportban, ahol a Key rotációs funkciót üzembe helyezi.
1. A **Key Vault neve** mezőbe írja be a Key Vault nevét.
1. A **Függvényalkalmazás neve** mezőbe írja be a Function alkalmazás nevét.
1. A **titkos kulcs neve** mezőbe írja be annak a titoknak a nevét, ahová a hozzáférési kulcsokat tárolni fogja.
1. A tárház **URL-címe** mezőbe írja be a függvény kódjának GitHub-helyét: **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

   ![Képernyőkép, amely bemutatja, hogyan hozhatja létre az első Storage-fiókot.](../media/secrets/rotation-dual/dual-rotation-2.png)

Az előző lépések elvégzése után egy Storage-fiók, egy kiszolgálófarm, egy Function-alkalmazás és egy Application Insights fog rendelkezni. Az üzembe helyezés befejezésekor a következő oldal jelenik meg: képernyőfelvétel, ![ amely az üzembe helyezés befejezését mutatja.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Ha hiba lép fel, az **újratelepítése** lehetőség kiválasztásával befejezheti az összetevők központi telepítését.


A [githubon](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)megtalálhatja a rotációs függvény üzembe helyezési sablonjait és kódját is.

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Adja hozzá a Storage-fiók hozzáférési kulcsait Key Vault

Először állítsa be a hozzáférési szabályzatot, és adja meg a felhasználók számára a **titkos kulcsok kezelésére** vonatkozó engedélyeket:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Most már létrehozhat egy új titkot egy Storage-fiók elérési kulcsával az értékeként. Szüksége lesz a Storage-fiók erőforrás-AZONOSÍTÓra, a titkos kulcs érvényességi idejére és a kulcs AZONOSÍTÓra, hogy a titkos kulcshoz legyen hozzáadva, így a rotációs függvény újra létrehozhatja a kulcsot a Storage-fiókban.

Határozza meg a Storage-fiók erőforrás-AZONOSÍTÓját. Ezt az értéket a `id` tulajdonságban találja.
```azurecli
az storage account show -n akvrotationstorage
```

Sorolja fel a Storage-fiók hozzáférési kulcsait, hogy megkapják a kulcs értékeit:

```azurecli
az storage account keys list -n akvrotationstorage 
```

Futtassa ezt a parancsot a (z) és a következő lekért értékeivel `key1Value` `storageAccountResourceId` :

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Ha rövid lejárati dátummal hoz létre egy titkos kulcsot, a `SecretNearExpiry` rendszer néhány percen belül közzétesz egy eseményt. Ez az esemény ezután aktiválja a függvényt a titok elforgatásához.

Ellenőrizze, hogy a hozzáférési kulcsok újragenerálása a Storage-fiók kulcsának és a Key Vault titoknak a beolvasásával és összehasonlításával ellenőrizhető-e.

Ezzel a paranccsal kérheti le a titkos adatokat:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Figyelje meg, hogy `CredentialId` a rendszer frissíti a másikat, `keyName` és `value` újragenerálta a ![ következőt: képernyőkép, amely az első Storage-fiókhoz tartozó z kulcstartó titkos Megjelenítés parancs kimenetét jeleníti meg.](../media/secrets/rotation-dual/dual-rotation-4.png)

Az értékek összehasonlításához a hozzáférési kulcsok beolvasása:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Képernyőkép, amely az első Storage-fiókhoz tartozó z Storage-fiók kulcsainak listáját jeleníti meg.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Storage-fiókok hozzáadása a rotációhoz

Ugyanezt a függvényt újra felhasználva több Storage-fiókhoz is elforgathatja a kulcsokat. 

A Storage-fiók kulcsainak egy meglévő függvényhez való hozzáadásához a következők szükségesek:
- A Storage-fiók kulcsát kezelő szolgáltatási szerepkör, amely a Function alkalmazáshoz van rendelve, hogy hozzáférhessen a Storage-fiók hozzáférési kulcsaihoz.
- Event Grid esemény-előfizetés a **SecretNearExpiry** eseményhez.

1. Válassza ki az Azure-sablon központi telepítési hivatkozását: 

   [![Azure-sablonok üzembe helyezési hivatkozása.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. Az **erőforráscsoport** listában válassza a **akvrotation** lehetőséget.
1. A **Storage-fiók neve** mezőbe írja be annak a Storage-fióknak a nevét, amely a forgatni kívánt hozzáférési kulcsokat tartalmazza.
1. A **Key Vault neve** mezőbe írja be a Key Vault nevét.
1. A **Függvényalkalmazás neve** mezőbe írja be a Function alkalmazás nevét.
1. A **titkos kulcs neve** mezőbe írja be annak a titoknak a nevét, ahová a hozzáférési kulcsokat tárolni fogja.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

   ![Képernyőkép, amely bemutatja, hogyan hozhat létre egy további Storage-fiókot.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Adjon hozzá egy másik Storage-fiókhoz való hozzáférési kulcsot Key Vault

Határozza meg a Storage-fiók erőforrás-AZONOSÍTÓját. Ezt az értéket a `id` tulajdonságban találja.
```azurecli
az storage account show -n akvrotationstorage2
```

Sorolja fel a Storage-fiók hozzáférési kulcsait, hogy a key2 értéket kapja:

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Futtassa ezt a parancsot a (z) és a következő lekért értékeivel `key2Value` `storageAccountResourceId` :

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Ezzel a paranccsal kérheti le a titkos adatokat:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Figyelje meg, hogy `CredentialId` a rendszer frissíti a másikat, `keyName` és `value` újragenerálta a ![ következőt: képernyőkép, amely a második Storage-fiókhoz tartozó z kulcstartó titkos Megjelenítés parancs kimenetét jeleníti meg.](../media/secrets/rotation-dual/dual-rotation-8.png)

Az értékek összehasonlításához a hozzáférési kulcsok beolvasása:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Képernyőkép, amely a második Storage-fiókhoz tartozó z Storage-fiók kulcsainak listáját jeleníti meg.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-dual-credential-rotation-functions"></a>Key Vault kettős hitelesítő adatok elforgatási funkciói

- [Storage-fiók](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis Cache](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="next-steps"></a>Következő lépések
- Áttekintés: [Key Vault figyelése Azure Event Grid](../general/event-grid-overview.md)
- Útmutató: az [első függvény létrehozása a Azure Portalban](../../azure-functions/functions-create-first-azure-function.md)
- Útmutató: [e-mailek fogadása Key Vault titkos kód módosításakor](../general/event-grid-logicapps.md)
- Hivatkozás: [Azure Event Grid Azure Key Vaulthoz tartozó esemény-séma](../../event-grid/event-schema-key-vault.md)
