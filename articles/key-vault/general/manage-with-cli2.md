---
title: Az Azure Key Vault kezelése a CLI használatával - Azure Key Vault | Microsoft dokumentumok
description: Ezzel a cikkből automatizálhatja a Key Vault gyakori feladatait az Azure CLI használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 127f0cdfc8cecf9789a68210f4b7ce1927333cc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422584"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Key Vault kezelése az Azure CLI használatával 

Ez a cikk ismerteti, hogyan kezdheti meg a munkát az Azure Key Vault az Azure CLI használatával.  A következő információk jelennek meg:

- Megerősített tároló (tároló) létrehozása az Azure-ban
- Kulcs, titkos kulcs vagy tanúsítvány hozzáadása a kulcstartóhoz
- Alkalmazás regisztrálása az Azure Active Directoryval
- Alkalmazás engedélyezése kulcs vagy titkos kulcs használatára
- A kulcstartó speciális hozzáférési házirendjeinek beállítása
- Hardveres biztonsági modulok (HSM-ek)
- A kulcstartó és a hozzárendelt kulcsok és titkos kulcsok törlése
- Egyéb Azure cross-platform parancssori felületparancsok


Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Ez a cikk nem tartalmazza az Azure-alkalmazás írásával kapcsolatos utasításokat, amelyet az egyik lépés tartalmaz, amely bemutatja, hogyan engedélyezheti az alkalmazás egy kulcs vagy titkos kulcs használatát a key vaultban.
>

Az Azure Key Vault áttekintését a [Mi az Azure Key Vault?](overview.md)) című témakörben találja. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure CLI-parancsok ebben a cikkben való használatához a következő elemekkel kell rendelkeznie:

* Egy Microsoft Azure-előfizetésre. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial).
* Az Azure parancssori illesztő felülete 2.0-s vagy újabb verziója. A legújabb verzió telepítéséhez [olvassa el az Azure CLI telepítése](/cli/azure/install-azure-cli)című témakört.
* Olyan alkalmazás, amely a cikkben létrehozott kulcs vagy jelszó használatára lesz konfigurálva. Egy mintaalkalmazás elérhető a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=45343). További információt a mellékelt Readme fájlban talál.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Segítség az Azure cross-platform parancssori felületéhez

Ez a cikk feltételezi, hogy ismeri a parancssori felületet (Bash, Terminal, Command prompt).

A --help vagy -h paraméter az egyes parancsok súgójának megtekintésére használható. Az Azure súgó [parancs] [beállítások] formátuma is használható. Ha kétségei vannak a parancs által szükséges paraméterekkel kapcsolatban, olvassa el a súgót. A következő parancsok például ugyanazokat az adatokat adják vissza:

```azurecli
az account set --help
az account set -h
```

Az Azure Cross-Platform parancssori felületen az alábbi cikkekben ismerkedhet meg az Azure Resource Manager használatával:

* [Telepítse az Azure CLI-t](/cli/azure/install-azure-cli)
* [Az Azure CLI használatának első lépései](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Megerősített tároló (tároló) létrehozása az Azure-ban

A tárolók at hardveres biztonsági modulok biztosítják. A tárolók a titkos alkalmazáskulcsok tárolásának központosításával csökkentik a biztonsági információk véletlen elvesztésének kockázatát. A kulcstartók a bennük tárolt tartalomhoz való hozzáférést vezérlik és naplózzák is. Az Azure Key Vault kezelni tudja a Transport Layer Security- (TLS-) tanúsítványok kérelmezését és megújítását, biztosítva a tanúsítvány életciklusának megbízható kezelési szolgáltatásához szükséges funkciókat. A következő lépésekben hozzon létre egy trezort.

### <a name="connect-to-your-subscriptions"></a>Csatlakozás az előfizetésekhez

Az interaktív bejelentkezéshez használja a következő parancsot:

```azurecli
az login
```
Ha szervezeti fiókkal szeretne bejelentkezni, adja meg felhasználónevét és jelszavát.

```azurecli
az login -u username@domain.com -p password
```

Ha egynél több előfizetéssel rendelkezik, és meg kell adnia, hogy melyiket használja, írja be a következőt a fiók előfizetései megtekintéséhez:

```azurecli
az account list
```

Adjon meg egy előfizetést az előfizetési paraméterrel.

```azurecli
az account set --subscription <subscription name or ID>
```

Az Azure cross-platform parancssori felületének konfigurálásáról az [Azure CLI telepítése című](/cli/azure/install-azure-cli)témakörben talál további információt.

### <a name="create-a-new-resource-group"></a>Új erőforráscsoport létrehozása

Az Azure Resource Manager használatakor az összes kapcsolódó erőforrás egy erőforráscsoporton belül jön létre. Létrehozhat egy key vault egy meglévő erőforráscsoportban. Ha új erőforráscsoportot szeretne használni, létrehozhat egy újat.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

Az első paraméter az erőforráscsoport neve, a második paraméter pedig a hely. Az összes lehetséges hely típuslistájának beírásához:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>A Key Vault-erőforrás-szolgáltató regisztrálása

 Új kulcstartó létrehozásakor a következő hibaüzenet jelenhet meg: "Az előfizetés nincs regisztrálva a "Microsoft.KeyVault" névtér használatához. Ha ez az üzenet jelenik meg, győződjön meg arról, hogy a Key Vault-erőforrás-szolgáltató regisztrálva van az előfizetésben. Ez a műveletet minden egyes előfizetés esetén csak egyszer kell elvégezni.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A `az keyvault create` parancs segítségével hozzon létre egy key vault. Ez a parancsfájl három kötelező paraméterrel rendelkezik: egy erőforráscsoport nevét, egy key vault nevét és a földrajzi helyet.

**ContosoKeyVault**nevű új tároló létrehozásához a **ContosoResourceGroup**erőforráscsoportban, amely a **kelet-ázsiai** helyen található, írja be a következőt: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

A parancs kimenete a létrehozott key vault tulajdonságait jeleníti meg. A két legfontosabb tulajdonság:

* **name**: A példában a neve ContosoKeyVault. Ezt a nevet fogja használni a többi Key Vault-parancshoz.
* **vaultUri**: A példában https://contosokeyvault.vault.azure.netaz URI . A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Azure-fiókja most már engedéllyel rendelkezik arra, hogy bármilyen műveletet végezzen ezen a kulcstartón. Eddig senki más nem engedélyezett.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Kulcs, titkos kulcs vagy tanúsítvány hozzáadása a kulcstartóhoz

Ha azt szeretné, hogy az Azure Key Vault hozzon `az key create` létre egy szoftveráltal védett kulcsot, használja a parancsot.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Ha rendelkezik egy .pem fájlban lévő kulccsal, feltöltheti azt az Azure Key Vaultba. A kulcsot szoftverrel vagy HSM-mel is megvédheti. Ez a példa importálja a kulcsot a .pem fájlból, és szoftverrel védi a "hVFkk965BuUv" jelszóval:

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Most már hivatkozhat a kulcsot, amely et létrehozott vagy feltöltött az Azure Key Vault, az URI használatával. Használja, **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** hogy mindig az aktuális verziót. A https://[keyvault-name]vault.azure.net/keys/[keyname]/[key-unique-id] használatával lejuthat erre a konkrét verzióra. **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**Például. 

Adjon hozzá egy titkos kulcsot a tárolóba, amely egy SQLPassword nevű jelszó, és amely "hVFkk965BuUv" értékkel rendelkezik az Azure Key Vaults-hoz. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Hivatkozzon erre a jelszóra az URI használatával. Az **https://ContosoVault.vault.azure.net/secrets/SQLPassword** aktuális verzió és a https://[keyvault-name]vault.azure.net/secret/[titkos név]/[titkos-unique-id] mindig lehívható az adott verzió lekérti. **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**Például.

Importáljon egy tanúsítványt a tárolóba .pem vagy .pfx használatával.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Tekintsük meg a létrehozott kulcsot, titkos kulcsot vagy tanúsítványt:

* A kulcsok megtekintéséhez írja be a következőt: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* A titkos kulcsok megtekintéséhez írja be a következőt: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* A tanúsítványok megtekintéséhez írja be a következőt: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Alkalmazás regisztrálása az Azure Active Directoryval

Ezt a lépést általában egy fejlesztő végzi egy másik számítógépről. Nem az Azure Key Vaultra vonatkozik, de a tudatosság érdekében itt található. Az alkalmazás regisztrációjának befejezéséhez a fióknak, a tárolónak és az alkalmazásnak ugyanabban az Azure-címtárban kell lennie.

A kulcstartót használó alkalmazásoknak az Azure Active Directoryból származó jogkivonat használatával kell hitelesítést végezniük.  Az alkalmazás tulajdonosának először regisztrálnia kell azt az Azure Active Directoryban. A regisztrációt követően az alkalmazás tulajdonosa az alábbi értékeket kapja:

- **Alkalmazásazonosító** (más néven AAD-ügyfélazonosító vagy alkalmazásazonosító)
- egy **hitelesítési kulcsot** (más néven közös titkos kódot). 

Az alkalmazásnak mindkét értékkel rendelkeznie kell ahhoz, hogy Azure Active Directory-jogkivonatot kapjon. Hogyan van beállítva egy alkalmazás jogkivonat lekéréséhez függ az alkalmazás. A [Key Vault-mintaalkalmazás](https://www.microsoft.com/download/details.aspx?id=45343) esetében az alkalmazás tulajdonosa adja meg ezeket az értékeket az app.config fájlban.

Az alkalmazások Azure Active Directoryval történő regisztrálásának részletes lépéseit tekintse át az [Alkalmazások integrálása](../../active-directory/develop/active-directory-integrating-applications.md)az Azure Active Directoryval című cikkekben, [a portál használatával hozzon létre egy Azure Active Directory-alkalmazást és egyszerű szolgáltatást, amely képes hozzáférni az erőforrásokhoz,](../../active-directory/develop/howto-create-service-principal-portal.md)és [hozzon létre egy egyszerű Azure-szolgáltatást az Azure CLI-vel.](/cli/azure/create-an-azure-service-principal-azure-cli)

Alkalmazás regisztrálása az Azure Active Directoryban:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Alkalmazás engedélyezése kulcs vagy titkos kulcs használatára

Ha engedélyezni szeretné az alkalmazást a kulcs vagy `az keyvault set-policy` titkos kulcs eléréséhez a tárolóban, használja a parancsot.

Ha például a tároló neve ContosoKeyVault, az alkalmazás 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed alkalmazásazonosítóval rendelkezik, és engedélyezni szeretné az alkalmazás számára a kulcsvisszafejtést és a kulcsokkal való bejelentkezést a tárolóban, kövesse a következő parancsot:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Ha engedélyezni szeretné ugyanazt az alkalmazást a titkos kulcsok olvasásához a tárolóban, írja be a következő parancsot:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a>A kulcstartó speciális hozzáférési házirendjeinek beállítása

Használja [az keyvault frissítés](/cli/azure/keyvault#az-keyvault-update) speciális házirendek a key vault engedélyezéséhez.

 Key Vault engedélyezése központi telepítéshez: Lehetővé teszi a virtuális gépek lekérni a tárolóból titkos kulcsként tárolt tanúsítványokat.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Key Vault engedélyezése lemeztitkosításhoz: Az Azure Disk titkosításához a tároló használatakor szükséges.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Key Vault engedélyezése a sablon üzembe helyezéséhez: Lehetővé teszi, hogy az Erőforrás-kezelő titkokat olvasson le a tárolóból.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Hardveres biztonsági modulok (HSM-ek)

A további biztonság érdekében importálhat vagy létrehozhat kulcsokat olyan hardveres biztonsági modulokból (HSM-ek), amelyek soha nem hagyják el a HSM-határt. A hardveres biztonsági modulok a 2. szintű FIPS 140-2 szerint vannak érvényesítve. Ha ez a követelmény nem vonatkozik Önre, ugorja át ezt a szakaszt, és folytassa a Kulcsartó és a hozzá tartozó kulcsok és titkos kódok törlése szakasszal.

HSM által védett kulcsok létrehozásához HSM által védett kulcsokat támogató tárolóra való előfizetéssel kell rendelkeznie.

A keyvault létrehozásakor adja hozzá a "sku" paramétert:

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Ehhez a tárolóhoz szoftveresen védett (korábban bemutatva) és HSM által védett kulcsokat is hozzáadhat. HSM-védelemmel ellátott kulcs létrehozásához állítsa a Cél paramétert "HSM" (HSM' ) beállításra:

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

A következő paranccsal .pem fájlból importálhat kulcsot a számítógépen. Ez a parancs a hardveres Key Vault szolgáltatás biztonsági moduljaiba importálja a kulcsot:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

A következő parancs importál ja a "hozd el a saját kulcsodat" (BYOK) csomagot. Ezzel a helyi HSM-ben hozhatja létre a kulcsot, majd helyezheti át a Key Vault szolgáltatás HSM-jeire anélkül, hogy a kulcs elhagyná a HSM határait:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

A BYOK-csomag létrehozásáról a [HSM-védett kulcsok használata az Azure Key Vault szolgáltatással](../keys/hsm-protected-keys.md)című témakörben talál további útmutatást.

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>A kulcstartó és a hozzárendelt kulcsok és titkos kulcsok törlése

Ha már nincs szüksége a key vaultra és annak kulcsaira `az keyvault delete` vagy titkos kulcsaira, a következő paranccsal törölheti a kulcstartót:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Lehetősége van a teljes Azure-erőforráscsoport törlésére is, amely magában foglalja a kulcstartót és a csoport összes erőforrását:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Egyéb Azure cross-platform parancssori felületparancsok

Az Azure Key Vault kezeléséhez hasznosnak találhat egyéb parancsok.

Ez a parancs az összes billentyű és kijelölt tulajdonság táblázatos megjelenítését sorolja fel:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Ez a parancs a megadott kulcs tulajdonságainak teljes listáját jeleníti meg:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Ez a parancs az összes titkos név és kijelölt tulajdonság táblázatos megjelenítését sorolja fel:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Íme egy példa egy adott kulcs eltávolítására:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Íme egy példa egy adott titok eltávolítására:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>További lépések

- A key vault-parancsokhoz való teljes Azure CLI-hivatkozásról a [Key Vault CLI-hivatkozása.](/cli/azure/keyvault)

- A programozási referenciákat [az Azure Key Vault fejlesztői útmutatójában](developers-guide.md) talál.

- Az Azure Key Vault ról és a HSM-ekről a [HSM-védelemmel ellátott kulcsok használata az Azure Key Vault szolgáltatással](../keys/hsm-protected-keys.md)című témakörben talál.
