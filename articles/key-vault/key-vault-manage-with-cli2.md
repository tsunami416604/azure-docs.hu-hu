---
title: Az Azure Key Vault parancssori felület használata kezelheti |} A Microsoft Docs
description: Ez a cikk segítségével automatizálhatja a gyakori feladatok a Key Vault az Azure CLI-vel
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
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: barclayn
ms.openlocfilehash: ba39ba2d4206fa86ed32bddf0538a2e997a79339
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220567"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Az Azure CLI használatával a Key Vault felügyelete 

Ez a cikk bemutatja, hogyan kezdeni a munkát az Azure Key Vault az Azure CLI használatával.  Az információkat tekintheti meg:

- Hogyan hozhat létre, amellyel megerősített tárolókat (kulcstartókat) az Azure-ban
- Egy kulcs, a titkos kulcsot vagy a tanúsítvány hozzáadása a key vaulthoz
- Egy alkalmazás regisztrálása az Azure Active Directoryval
- Az alkalmazás egy kulcs vagy titkos kód használatának engedélyezése
- A beállítás a key vault speciális hozzáférési szabályzatok
- Hardveres biztonsági modulokban (HSM) használata
- A kulcstartó és a hozzá tartozó kulcsok és titkos kódok törlése
- Vegyes az Azure platformfüggetlen parancssori felületi parancsokkal


Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Ez a cikk nem tartalmaz útmutatást, hogy a lépések egyikét tartalmazza, amely bemutatja, hogyan az alkalmazások számára a kulcsok vagy titkos kulcsok a key vault használata az Azure-alkalmazás írása.
>

Az Azure Key Vault áttekintését lásd: [Mi az Azure Key Vault?](key-vault-whatis.md)
Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk az Azure CLI-parancsok használatához a következő elemeket kell rendelkeznie:

* Egy Microsoft Azure-előfizetésre. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial).
* Az Azure parancssori felület 2.0-s vagy újabb verziója. Telepítse a legújabb verzióját, lásd: [az Azure CLI telepítése](/cli/azure/install-azure-cli).
* A kulcs vagy jelszó ebben a cikkben létrehozott használandó konfigurálni kívánt alkalmazás. Egy mintaalkalmazás elérhető a [Microsoft letöltőközpontból](http://www.microsoft.com/download/details.aspx?id=45343). Útmutatásért lásd: a csomagban foglalt fontos fájlt.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Segítség kérése az Azure többplatformos parancssori felület

Ez a cikk azt feltételezi, hogy ismeri a parancssori felületen (Bash, terminál, parancssor használatával).

A--help vagy a -h paramétert a konkrét parancsok súgójának megtekintéséhez használható. Azt is megteheti az Azure súgó [parancs] [kapcsolók] formátumot túl is használható. Ha kétségei vannak parancs szükséges paraméterek kapcsolatban, tekintse meg érdekében. Az alábbi parancsokat az összes például ugyanazokat az információkat adja vissza:

```azurecli
az account set --help
az account set -h
```

A következő cikkekben megismerkedhet az Azure Resource Manager az Azure platformfüggetlen parancssori felülete is olvashatja:

* [Az Azure parancssori felület telepítése](/cli/azure/install-azure-cli)
* [Azure CLI használatának első lépései](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Hogyan hozhat létre, amellyel megerősített tárolókat (kulcstartókat) az Azure-ban

Tárolók tárolókat hardveres biztonsági modulok által támogatott biztonságosak. A tárolók a titkos alkalmazáskulcsok tárolásának központosításával csökkentik a biztonsági információk véletlen elvesztésének kockázatát. A kulcstartók a bennük tárolt tartalomhoz való hozzáférést vezérlik és naplózzák is. Az Azure Key Vault kezelni tudja a Transport Layer Security- (TLS-) tanúsítványok kérelmezését és megújítását, biztosítva a tanúsítvány életciklusának megbízható kezelési szolgáltatásához szükséges funkciókat. Az alábbi lépésekkel létrehozhat egy tárolót.

### <a name="connect-to-your-subscriptions"></a>Csatlakozás az előfizetésekhez

Jelentkezzen be interaktívan, használja a következő parancsot:

```azurecli
az login
```
Bejelentkezés szervezeti fiókkal, is át kell adnia a felhasználónevét és jelszavát.

```azurecli
az login -u username@domain.com -p password
```

Ha több előfizetéssel rendelkezik, és meg kell adnia, hogy melyiket kívánja használni, írja be a következő, a fiókhoz tartozó előfizetések megtekintéséhez:

```azurecli
az account list
```

Előfizetés az előfizetési paraméter adható meg.

```azurecli
az account set --subscription <subscription name or ID>
```

Az Azure többplatformos parancssori felület konfigurálásával kapcsolatos további információkért lásd: [Azure CLI telepítése](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Új erőforráscsoport létrehozása

Azure Resource Manager használatakor az összes kapcsolódó erőforrás egy erőforráscsoportba tartozó hozhatók létre. Egy meglévő erőforráscsoportot is létrehozhat kulcstartót. Ha egy új erőforráscsoportot használni szeretne, létrehozhat egy újat.

```azurecli
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

Az első paraméter az erőforráscsoport nevét, és a második paraméter az a hely. Minden lehetséges helyek típusa listájának lekérése:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>A Key Vault erőforrás-szolgáltató regisztrálása

 Ön hibaüzenet jelenhet meg: "az előfizetés nincs regisztrálva"Microsoft.KeyVault"névtér" Amikor megpróbál létrehozni egy új kulcstartóba. Üzenet jelenik meg, hogy a Key Vault erőforrás-szolgáltató regisztrálva van az előfizetésben. Ez a műveletet minden egyes előfizetés esetén csak egyszer kell elvégezni.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Használja a `az keyvault create` parancs használatával hozzon létre egy kulcstartót. Ez a szkript három kötelező paraméterrel rendelkezik: egy erőforráscsoport-nevet, egy kulcstároló nevét és a földrajzi helyet.

Az új tároló létrehozása nevű **ContosoKeyVault**, az erőforráscsoportban lévő **ContosoResourceGroup**az adataihoz, a **Kelet-Ázsia** helyét, írja be: 

```azurecli
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

Ez a parancs kimenetét mutatja a létrehozott kulcstartó tulajdonságait. A két legfontosabb tulajdonság:

* **név**: a példában a neve a következő ContosoKeyVault. Más Key Vault parancsok ezt a nevet fogja használni.
* **vaultUri**: a példában az URI-ja https://contosokeyvault.vault.azure.net. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Azure-fiókja most már engedéllyel rendelkezik arra, hogy bármilyen műveletet végezzen ezen a kulcstartón. Kezdődően még senki más nem engedélyezett.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Egy kulcs, a titkos kulcsot vagy a tanúsítvány hozzáadása a key vaulthoz

Ha azt szeretné, hogy az Azure Key Vault létrehozása egy szoftveres védelemmel ellátott kulcs az Ön számára, használja a `az key create` parancsot.

```azurecli
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```

Ha rendelkezik egy meglévő kulcs egy .pem fájlra, feltöltheti az Azure Key Vaultba. Kiválaszthatja, hogy a szoftver vagy a HSM-kulcs védelme. A kulcs importálása a .pem-fájlt, és védje a szoftver a következő használatával:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'Pa$$w0rd' --protection software
```

A kulcs, amelyet létrehozott, vagy az Azure Key Vaultba feltöltött, az URI használatával hivatkozhat. Használat **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** , mindig letöltheti a legfrissebb verziót. Használat https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] lekérni ezt a verziót. Például: **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Adja hozzá a tárolóhoz, amely egy SQLPassword nevű jelszót, és a Pa$ $w0rd az Azure Key vault-Kulcstartók értékkel rendelkezik, amely egy titkos kulcsot. 

```azurecli
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

Az URI használatával hivatkozhat az ezt a jelszót. Használat **https://ContosoVault.vault.azure.net/secrets/SQLPassword** mindig lekérni az aktuális verzióra, és https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] lekérni ezt a verziót. Például: **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Tanúsítvány importálása a használatával egy .pem- vagy .pfx-tárolóba.

```azurecli
az keyvault certificate import --vault-name 'ContosoKeyVault' --file 'c:\cert\cert.pfx' --name 'ContosoCert' --password 'Pa$$w0rd'
```

Most megtekintheti a kulcsot, a titkos kulcsot vagy a létrehozott tanúsítványt:

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

## <a name="registering-an-application-with-azure-active-directory"></a>Egy alkalmazás regisztrálása az Azure Active Directoryval

Ezt a lépést általában egy fejlesztő végzi egy másik számítógépről. Azt nem egyedi az Azure Key Vaultba, de meg adva, figyelése. Végezze el az alkalmazás regisztrációját, a fiók, a tároló és az alkalmazás kell lennie az Azure könyvtárába.

A kulcstartót használó alkalmazásoknak az Azure Active Directoryból származó jogkivonat használatával kell hitelesítést végezniük.  Az alkalmazás tulajdonosának regisztrálnia kell azt az Azure Active Directory először. A regisztrációt követően az alkalmazás tulajdonosa az alábbi értékeket kapja:

- Egy **Alkalmazásazonosító** (más néven az AAD-Ügyfélazonosítójának vagy alkalmazásazonosító alapján)
- egy **hitelesítési kulcsot** (más néven közös titkos kódot). 

Az alkalmazásnak mindkét értékkel rendelkeznie kell ahhoz, hogy Azure Active Directory-jogkivonatot kapjon. Hogyan van konfigurálva az alkalmazások jogkivonat beszerzéséhez az alkalmazás függ. A [Key Vault-mintaalkalmazás](https://www.microsoft.com/download/details.aspx?id=45343) esetében az alkalmazás tulajdonosa adja meg ezeket az értékeket az app.config fájlban.

Az alkalmazások az Azure Active Directoryval való regisztrálásának részletes lépéseit tekintse át a című cikkekben [alkalmazások integrálása az Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md), [a portálon hozzon létre egy Azure Active Directory-alkalmazás és egyszerű szolgáltatás erőforrások eléréséhez](../azure-resource-manager/resource-group-create-service-principal-portal.md), és [egy Azure-beli szolgáltatásnév létrehozása az Azure CLI-vel](/cli/azure/create-an-azure-service-principal-azure-cli).

Alkalmazás regisztrálása az Azure Active Directoryban:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password 'Pa$$w0rd' --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Az alkalmazás egy kulcs vagy titkos kód használatának engedélyezése

Engedélyezze az alkalmazás számára a kulcs vagy titkos kód, a tároló eléréséhez, használja a `az keyvault set-policy` parancsot.

Például ha a tároló neve ContosoKeyVault, az alkalmazás rendelkezik egy appID-8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, és engedélyezze az alkalmazás számára a tároló kulcsait visszafejtse és szeretne, használja a következő parancsot:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Engedélyezze az ugyanazon alkalmazás a tároló titkos kulcsainak olvasásához, írja be a következő parancsot:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a> A beállítás a key vault speciális hozzáférési szabályzatok

Használat [az keyvault update](/cli/azure/keyvault#az-keyvault-update) a kulcstartó speciális szabályzatok engedélyezéséhez. 

 Központi telepítés engedélyezése a Key Vault: lehetővé teszi, hogy a virtuális gépek a tárolóból titkos kódként tárolt tanúsítványok lekérése.
 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-deployment 'true'
 ``` 

A Key Vault engedélyezése lemeztitkosítás: megadása kötelező, ha a tároló az Azure Disk encryption használatával.

 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-disk-encryption 'true'
 ```  

A Key Vault engedélyezése a sablon üzembe helyezéshez: Resource Manager lehetővé teszi, hogy a titkos kódok lekérése a tárolóból.
 ```azurecli 
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-template-deployment 'true'
 ```

## <a name="working-with-hardware-security-modules-hsms"></a>Hardveres biztonsági modulokban (HSM) használata

Hozzáadott garantálja hogy is kulcsok importálását vagy létrehozását a hardveres biztonsági modulokban (HSM) a HSM határait betartó. A hardveres biztonsági modulok a 2. szintű FIPS 140-2 szerint vannak érvényesítve. Ha ez a követelmény nem vonatkozik Önre, ugorja át ezt a szakaszt, és folytassa a [Kulcsartó és a hozzá tartozó kulcsok és titkos kódok törlése](#delete-the-key-vault-and-associated-keys-and-secrets) szakasszal.

Ezek a HSM által védett kulcsok létrehozásához, egy tároló-előfizetése, amely támogatja a HSM-védelemmel ellátott kulcsokkal kell rendelkeznie.

A kulcstartó létrehozásakor adja hozzá az "sku" paraméter:

```azurecli
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

Ehhez a tárolóhoz szoftveresen védett (korábban bemutatva) és HSM által védett kulcsokat is hozzáadhat. Egy HSM által védett kulcs létrehozásához állítsa be a cél paramétert "HSM"-re:

```azurecli
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

Az alábbi parancs segítségével kulcsot importálhat egy .pem-fájlt a számítógépen. Ez a parancs a hardveres Key Vault szolgáltatás biztonsági moduljaiba importálja a kulcsot:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```

A következő parancs egy „saját kulcs használata” (BYOK-) csomagot importál. Ezzel a helyi HSM-ben hozhatja létre a kulcsot, majd helyezheti át a Key Vault szolgáltatás HSM-jeire anélkül, hogy a kulcs elhagyná a HSM határait:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```

Részletes útmutatást a BYOK-csomag létrehozásához, lásd: [HSM-Protected kulcsok használata az Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>A kulcstartó és a hozzá tartozó kulcsok és titkos kódok törlése

Ha már nincs szüksége a key vault és a kulcsok vagy titkos adatokat, a key vault használatával törölheti az `az keyvault delete` parancsot:

```azurecli
az keyvault delete --name 'ContosoKeyVault'
```

Lehetősége van a teljes Azure-erőforráscsoport törlésére is, amely magában foglalja a kulcstartót és a csoport összes erőforrását:

```azurecli
az group delete --name 'ContosoResourceGroup'
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Vegyes az Azure platformfüggetlen parancssori felületi parancsokkal

Egyéb parancsok, amelyek hasznosak lehetnek az Azure Key Vault kezeléséhez.

Ez a parancs egy táblázatos jeleníti meg az összes kulcsot és a kiválasztott tulajdonságokat tartalmazza:

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

Ez a parancs a megadott kulcs tulajdonságainak teljes listáját jeleníti meg:

```azurecli
az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Ez a parancs táblázatos megjeleníti a titkos kód nevét és a megadott tulajdonságokat tartalmazza:

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

Íme egy példa egy adott kulcs eltávolítására:

```azurecli
az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Íme egy példa egy adott titkos kód eltávolítására:

```azurecli
az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'
```

## <a name="next-steps"></a>További lépések

- Befejeződött az Azure CLI-referenciáját a key vault parancsok, lásd: [Key Vault parancssori felület referenciája](/cli/azure/keyvault).

- Programozási hivatkozások lásd [az Azure Key Vault fejlesztői útmutató](key-vault-developers-guide.md)

- Az Azure Key Vaultban, és HSM-EK további információkért lásd: [HSM-Protected kulcsok használata az Azure Key Vault](key-vault-hsm-protected-keys.md).
