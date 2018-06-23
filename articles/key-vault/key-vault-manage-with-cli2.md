---
title: Kezelheti az Azure Key Vault parancssori felülettel |} Microsoft Docs
description: Ez a cikk segítségével a Key Vault gyakori feladatok automatizálásához a CLI 2.0 használatával
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: ''
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: barclayn
ms.openlocfilehash: 8c2501b5e89e81709de074c0b0c93b317ecebd7b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316594"
---
# <a name="manage-key-vault-using-cli-20"></a>Kezelheti a Key Vault 2.0 parancssori felület használatával

Ez a cikk bemutatja, hogyan adhat az Azure Key Vault használatával az Azure CLI 2.0 használatának megkezdéséhez. Információkat tekintheti meg:
- Megerősített tárolókat (kulcstartókat) létrehozása az Azure-ban
- Hogyan tárolhatja, és kezelheti a titkosítási kulcsok és titkos az Azure-ban. 
- Az Azure parancssori felület használatával hozzon létre egy tárolót.
- A kulcs vagy a jelszót, majd használható az Azure-alkalmazások létrehozása. 
- Egy alkalmazás használatát a létrehozott kulcsot vagy jelszót.

Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).


> [!NOTE]
> Ez a cikk útmutatást, hogy a lépések egyikét tartalmazza, amely bemutatja, hogyan az alkalmazások a key vault kulcsok vagy titkos kulcsok használata az Azure-alkalmazások írásával nem tartalmazza.
>

Az Azure Key Vault áttekintését lásd: [Mi az Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk az Azure parancssori felület parancsait használatához rendelkeznie kell a következő elemek:

* Egy Microsoft Azure-előfizetésre. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial).
* Parancssori felület 2.0-s vagy újabb verziója. Telepítse a legújabb verziót, lásd: [telepítése és konfigurálása az Azure platformfüggetlen parancssori felület 2.0](/cli/azure/install-azure-cli).
* A kulcs vagy jelszó ebben a cikkben konfigurálni kívánt alkalmazás. Egy mintaalkalmazás elérhető a [Microsoft letöltőközpontból](http://www.microsoft.com/download/details.aspx?id=45343). Útmutatásért lásd: a beágyazott információs fájl.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Segítség az Azure platformfüggetlen parancssori felülettel
Ez a cikk feltételezi, hogy ismeri a parancssori felület (Bash, terminál, parancssor).

A – Súgó vagy -h paraméter használható parancsok súgójának megtekintéséhez. Alternatív megoldásként az Azure súgó [parancs] [kapcsolók] formátumban túl is használható. A parancs által igényelt paraméterekről kétségei vannak, tekintse meg a segítségével. A következő parancsokat minden például ugyanazokat az információkat adja vissza:

```azurecli-interactive
az account set --help
az account set -h
```

A következő cikkekben ismerkedhet meg az Azure Resource Manager az Azure platformfüggetlen parancssori felülettel is olvasható:

* [Az Azure parancssori felület telepítése](/cli/azure/install-azure-cli)
* [Ismerkedés az Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Csatlakozás az előfizetésekhez

A bejelentkezéshez interaktív módon, a következő paranccsal:

```azurecli
az login
```
Olyan szervezeti fiókkal bejelentkezni átadhatók a felhasználónevét és jelszavát.

```azurecli
az login -u username@domain.com -p password
```

Ha egynél több előfizetéssel rendelkezik, és meg kell adnia kell használni, írja be a fiókhoz tartozó előfizetések megjelenítéséhez a következőket:

```azurecli
az account list
```

Az előfizetés paraméterrel meg az előfizetést.

```azurecli
az account set --subscription <subscription name or ID>
```

Azure platformfüggetlen parancssori felületre konfigurálásával kapcsolatos további információkért lásd: [Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Új erőforráscsoport létrehozása
Azure Resource Manager használatakor minden kapcsolódó erőforrás egy erőforráscsoportban jönnek létre. Egy meglévő erőforráscsoportot is létrehozhat egy kulcstartót. Ha egy új erőforráscsoportot használni kívánt, létrehozhat egy újat.

```azurecli
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

Az első paraméter az erőforráscsoport neve, a második paraméter pedig a hely. Az összes lehetséges listájának helyek írja be:

```azurecli
az account list-locations
``` 

## <a name="register-the-key-vault-resource-provider"></a>A Key Vault erőforrás-szolgáltató regisztrálása
 Előfordulhat, hogy a hibát látja "az előfizetés nem regisztrált"Microsoft.KeyVault"névtér" Amikor megpróbál létrehozni egy új kulcstartó. Ha az üzenet jelenik meg, győződjön meg arról, hogy Key Vault erőforrás-szolgáltató regisztrálva van az előfizetésben. Ez a műveletet minden egyes előfizetés esetén csak egyszer kell elvégezni.

```azurecli
az provider register -n Microsoft.KeyVault
```


## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Használja a `az keyvault create` parancs futtatásával hozzon létre egy kulcstartót. Ez a parancsfájl három kötelező paraméterrel rendelkezik: erőforráscsoport-nevet, a kulcstároló neve és a földrajzi helyet.

Egy új tárolót létrehozni a névvel ellátott **ContosoKeyVault**, erőforráscsoportban **ContosoResourceGroup**szereplő, a **Kelet-Ázsia** helyét, írja be: 

```azurecli
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

Ez a parancs kimenetét mutatja a korábban létrehozott kulcstartó tulajdonságait. A két legfontosabb tulajdonság:

* **név**: A példában a értéke ContosoKeyVault. Más Key Vault parancsok ezt a nevet fogja használni.
* **vaultUri**: a példában az URI: https://contosokeyvault.vault.azure.net. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Azure-fiókja most már engedéllyel rendelkezik arra, hogy bármilyen műveletet végezzen ezen a kulcstartón. Től még senki más engedélyezett.

## <a name="add-a-key-secret-or-certificate-to-the-key-vault"></a>A kulcs, a titkos kulcs vagy a tanúsítvány felvétele a key vault

Ha azt szeretné, hogy az Azure Key Vault szoftveres védelemmel ellátott kulcs létrehozásához, használja a `az key create` parancsot.

```azurecli
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```

Ha egy meglévő kulcs a .pem fájl található, az Azure Key Vaultba is feltölthet. Kiválaszthatja, hogy a kulcs szoftveres vagy hardveres biztonsági MODULT a védelméhez. A kulcs importálása a .pem fájl, és a szoftverek védelmét, a következő használatával:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'Pa$$w0rd' --protection software
```

A kulcs létrehozott vagy az Azure Key Vaultba feltöltött, az URI használatával hivatkozhat. Használjon **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** mindig lekérni az aktuális verzióját. Használjon https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] az adott verzióval. Például: **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Adja hozzá a titkos kulcs a tárolóba, amely egy SQLPassword nevű jelszót, és, hogy a Pa$ $w0rd az Azure Key tárolók értékkel rendelkezik. 

```azurecli
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

Az URI használatával hivatkozhat a ezt a jelszót. Használjon **https://ContosoVault.vault.azure.net/secrets/SQLPassword** mindig lekérni az aktuális verzióra, és https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] az adott verzióval. Például: **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Tanúsítvány importálása a .pem vagy .pfx-tárolóba.

```azurecli
az keyvault certificate import --vault-name 'ContosoKeyVault' --file 'c:\cert\cert.pfx' --name 'ContosoCert' --password 'Pa$$w0rd'
```

Tekintse meg a kulcs, a titkos kulcs, vagy a létrehozott tanúsítvány:

* A kulcsok megtekintéséhez írja be: 

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

* A titkos kulcsok megtekintéséhez írja be: 

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

* Tanúsítványok megtekintéséhez írja be: 

```azurecli
az keyvault certificate list --vault-name 'ContosoKeyVault'
```

## <a name="register-an-application-with-azure-active-directory"></a>Alkalmazás regisztrálása az Azure Active Directory szolgáltatásban
Ezt a lépést általában egy fejlesztő végzi egy másik számítógépről. Nem adott, az Azure Key Vaultba, de meg adva, ezekre a műveletekre. Az alkalmazás a Regisztrálás, a fiókját, a tároló és az alkalmazás kell lennie az Azure könyvtárába.

A kulcstartót használó alkalmazásoknak az Azure Active Directoryból származó jogkivonat használatával kell hitelesítést végezniük.  Az alkalmazás tulajdonosa regisztrálnia kell azt az Azure Active Directoryban először. A regisztrációt követően az alkalmazás tulajdonosa az alábbi értékeket kapja:

- Egy **Alkalmazásazonosító** (más néven az ügyfél-Azonosítót az AAD vagy appID)
- egy **hitelesítési kulcsot** (más néven közös titkos kódot). 

Az alkalmazásnak mindkét értékkel rendelkeznie kell ahhoz, hogy Azure Active Directory-jogkivonatot kapjon. Hogyan van konfigurálva az alkalmazás a szolgáltatáshitelesítést egy token az alkalmazás függ. A [Key Vault-mintaalkalmazás](https://www.microsoft.com/download/details.aspx?id=45343) esetében az alkalmazás tulajdonosa adja meg ezeket az értékeket az app.config fájlban.

Lépésenkénti útmutató az alkalmazás regisztrálása az Azure Active Directoryval tekintse át a című cikkekben [alkalmazások integrálása az Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md), [használata portálon hozzon létre egy Azure Active Directory-alkalmazást és egy egyszerű szolgáltatást, amely erőforrások eléréséhez](../azure-resource-manager/resource-group-create-service-principal-portal.md), és [hozzon létre egy Azure szolgáltatás egyszerű Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli).

Alkalmazás regisztrálása az Azure Active Directoryban:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password 'Pa$$w0rd' --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>A kulcs vagy titkos kód használatának engedélyezése az alkalmazás számára

Engedélyezi az alkalmazásnak a kulcs vagy a tárolóban lévő titkos kulcs eléréséhez, használja a `az keyvault set-policy` parancsot.

Például ha a tároló neve ContosoKeyVault, az alkalmazás rendelkezik egy appID azonosítója 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, és az alkalmazás a tároló kulcsait visszafejtse és engedélyezni szeretné, a következő paranccsal:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Engedélyezi a az alkalmazás megnyithassa a tárolóban, írja be a következő parancsot:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a> Set kulcstároló hozzáférési házirendek speciális 
Használjon [az keyvault frissítés](/cli/azure/keyvault#az-keyvault-update) esetében a key vault speciális szabályzatok engedélyezéséhez. 

 Központi telepítés engedélyezése a Key Vault: lehetővé teszi, hogy a tanúsítványokat tárolja a tárolóból titkos kulcsok beolvasása a virtuális gépek.
 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-deployment 'true'
 ``` 

Key Vault engedélyezni az adatok titkosítása: megadása kötelező, ha a tárolót használja az Azure Disk encryption.

 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-disk-encryption 'true'
 ```  

Engedélyezze a Key Vault a sablon-üzembehelyezés: Resource Manager lehetővé teszi, hogy a titkos kulcsok beolvasása a tárolóból.
 ```azurecli 
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-template-deployment 'true'
 ```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Hardveres biztonsági modul (HSM) használata

A nagyobb importálhat vagy kulcsok generálása a hardveres biztonsági modulokkal (HSM), amely sosem hagyják el a HSM határait. A hardveres biztonsági modulok a 2. szintű FIPS 140-2 szerint vannak érvényesítve. Ha ez a követelmény nem vonatkozik Önre, ugorja át ezt a szakaszt, és folytassa a [Kulcsartó és a hozzá tartozó kulcsok és titkos kódok törlése](#delete-the-key-vault-and-associated-keys-and-secrets) szakasszal.

A HSM által védett kulcsok létrehozásához HSM által védett kulcsokat támogató tárolóra előfizetéssel kell rendelkeznie.

A keyvault létrehozásakor adja hozzá az "sku" paramétert:

```azurecli
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

Ehhez a tárolóhoz szoftveresen védett (korábban bemutatva) és HSM által védett kulcsokat is hozzáadhat. Egy HSM által védett kulcs létrehozásához állítsa be a cél paramétert "HSM":

```azurecli
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

A következő paranccsal kulcsot importálhat .pem fájlt a számítógépen. Ez a parancs a hardveres Key Vault szolgáltatás biztonsági moduljaiba importálja a kulcsot:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```

A következő parancs egy „saját kulcs használata” (BYOK-) csomagot importál. Ezzel a helyi HSM-ben hozhatja létre a kulcsot, majd helyezheti át a Key Vault szolgáltatás HSM-jeire anélkül, hogy a kulcs elhagyná a HSM határait:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```

Leírja, hogyan a BYOK-csomag létrehozásához további: [HSM-Protected kulcsok használata az Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Kulcstartó és a hozzá tartozó kulcsok és titkos kódok törlése

Ha már nincs szüksége a kulcstartó és a kulcsok vagy titkos kulcsok, a key vault használatával törölheti a `az keyvault delete` parancs:

```azurecli
az keyvault delete --name 'ContosoKeyVault'
```

Lehetősége van a teljes Azure-erőforráscsoport törlésére is, amely magában foglalja a kulcstartót és a csoport összes erőforrását:

```azurecli
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Egyéb Azure platformfüggetlen parancssori felület parancsai

Egyéb parancsok, amelyek hasznosak lehetnek az Azure Key Vault kezeléséhez.

Ez a parancs táblázatos formában jeleníti meg az összes kulcsot és a kijelölt tulajdonságok:

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

Ez a parancs a megadott kulcs tulajdonságainak teljes listáját jeleníti meg:

```azurecli
az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Ez a parancs táblázatos formában jeleníti meg az összes titkos kód nevét és a kijelölt tulajdonságokat:

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

Itt látható egy példa egy adott kulcs eltávolítására:

```azurecli
az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Itt látható egy példa egy adott titkos kód eltávolítása:

```azurecli
az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'
```

## <a name="next-steps"></a>További lépések

- A kulcstároló parancsok teljes Azure parancssori felület referenciáért lásd: [Key Vault CLI hivatkozás](/cli/azure/keyvault).

- Programozási hivatkozások: [Az Azure Key Vault fejlesztői útmutatója](key-vault-developers-guide.md).

- Az Azure Key Vault és a hardveres biztonsági modulok kapcsolatos tudnivalókat lásd: [HSM-Protected kulcsok használata az Azure Key Vault](key-vault-hsm-protected-keys.md).
