---
title: Kezelheti az Azure Key Vault parancssori felülettel |} Microsoft Docs
description: Ez az oktatóanyag segítségével a Key Vault gyakori feladatok automatizálásához a CLI 2.0 használatával
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
ms.date: 04/19/2018
ms.author: barclayn
ms.openlocfilehash: 95e35ed1f26a861ab934570fae613dda95fcb537
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796132"
---
# <a name="manage-key-vault-using-cli-20"></a>Kezelheti a Key Vault 2.0 parancssori felület használatával

Ez a cikk bemutatja, hogyan adhat az Azure Key Vault használatával az Azure CLI 2.0 használatának megkezdéséhez. Információkat tekintheti meg:
- Megerősített tárolókat (kulcstartókat) létrehozása az Azure-ban
- Hogyan tárolhatja, és kezelheti a titkosítási kulcsok és titkos az Azure-ban. 
- A folyamat az Azure platformfüggetlen parancssori felület használatával, amely tartalmazza a kulcs vagy a jelszót, majd használható az Azure-alkalmazások létrehozását. 
- Egy alkalmazás majd használatát adott kulcsot vagy jelszót.

Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).

**Az oktatóanyag áttekintésének várható időtartama:** 20 perc

> [!NOTE]
> Ez az oktatóanyag nem tartalmazza, hogy a lépések egyikét tartalmazza, amely bemutatja, hogyan az alkalmazások a key vault kulcsok vagy titkos kulcsok használata az Azure-alkalmazások írásával útmutatást.
>

Az Azure Key Vault áttekintését lásd: [Mi az Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez szüksége lesz:

* Egy Microsoft Azure-előfizetésre. Ha még nem rendelkezik ilyennel, akkor regisztrálhatnak az egy [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial).
* Parancssori felület 2.0-s vagy újabb verziója. Telepítse a legújabb verziót, és csatlakozzon az Azure-előfizetéssel, [telepítése és konfigurálása az Azure platformfüggetlen parancssori felület 2.0](/cli/azure/install-azure-cli).
* Egy, az útmutató során létrehozott kulcs vagy jelszó használatához konfigurált alkalmazásra. Egy mintaalkalmazás elérhető a [Microsoft letöltőközpontból](http://www.microsoft.com/download/details.aspx?id=45343). Útmutatásért tekintse meg a kísérő információs fájlt.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Segítség az Azure platformfüggetlen parancssori felülettel
Ez az oktatóanyag feltételezi, hogy ismeri a parancssori felülettel (Bash, terminál, parancssor)

A – Súgó vagy -h paraméter használható parancsok súgójának megtekintéséhez. Alternatív megoldásként az azure súgó [parancs] [kapcsolók] formátumban is használható ugyanazokat az információkat adja vissza. A következő parancsokat minden például ugyanazokat az információkat adja vissza:

```azurecli-interactive
az account set --help
az account set -h
```

A parancs által igényelt paraméterekről kétségei vannak, tekintse meg a használatával – súgó, – h vagy az [parancs] segítségével.

Az alábbi útmutatókkal megismerkedhet az Azure Resource Manager az Azure platformfüggetlen parancssori felülettel is olvasható:

* [Az Azure parancssori felület telepítése](/cli/azure/install-azure-cli)
* [Ismerkedés az Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Csatlakozás az előfizetésekhez
Jelentkezzen be egy szervezeti fiókjával, használja a következő parancsot:

```azurecli
az login -u username@domain.com -p password
```

vagy ha azt szeretné, írja be a interaktívan bejelentkezni

```azurecli
az login
```

Ha több előfizetése van, és meg szeretné szabni, hogy melyiket használja az Azure Key Vaulthoz, írja be az alábbi parancsot a fiókhoz tartozó előfizetések megtekintéséhez:

```azurecli
az account list
```

Ezt követően írja be az alábbi parancsot a kívánt előfizetés kiválasztásához:

```azurecli
az account set --subscription <subscription name or ID>
```

Azure platformfüggetlen parancssori felületre konfigurálásával kapcsolatos további információkért lásd: [Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Új erőforráscsoport létrehozása
Azure Resource Manager használatakor minden kapcsolódó erőforrás egy erőforráscsoportban jönnek létre. Ebben az oktatóanyagban létrehozunk egy új erőforráscsoportot "ContosoResourceGroup".

```azurecli
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

Az első paraméter az erőforráscsoport neve, a második paraméter pedig a hely. Az összes lehetséges listájának helyek írja be:

```azurecli
az account list-locations
``` 

Ha további tájékoztatásra van szüksége, írja be: 

```azurecli
az account list-locations -h
```

## <a name="register-the-key-vault-resource-provider"></a>A Key Vault erőforrás-szolgáltató regisztrálása
Amikor megpróbál létrehozni egy új kulcstartó jelenhet meg a hiba "az előfizetés nem regisztrált"Microsoft.KeyVault"névtér". Ha az üzenet jelenik meg, győződjön meg arról, hogy a Key Vault erőforrás-szolgáltató regisztrálva van az előfizetésben:

```azurecli
az provider register -n Microsoft.KeyVault
```

>[!NOTE]
Ez csak azért van szükség, egyszer előfizetésenként.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Használja a `az keyvault create` parancs futtatásával hozzon létre egy kulcstartót. Ez a parancsfájl három kötelező paraméterrel rendelkezik: erőforráscsoport-nevet, a kulcstároló neve és a földrajzi helyet.

Példa:

- Ha a tároló neve **ContosoKeyVault**
- Az erőforráscsoport neve **ContosoResourceGroup**
- Helyét **Kelet-Ázsia**

Írja be:

```azurecli
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

Ez a parancs kimenetét mutatja az újonnan létrehozott kulcstartó tulajdonságait. A két legfontosabb tulajdonság:

* **név**: a példában ez az ContosoKeyVault. Más Key Vault parancsok ezt a nevet fogja használni.
* **vaultUri**: Ez a példa https://contosokeyvault.vault.azure.net. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Azure-fiókja most már engedéllyel rendelkezik arra, hogy bármilyen műveletet végezzen ezen a kulcstartón. Egyelőre senki másnak nincs erre engedélye.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Kulcs vagy titkos kód hozzáadása a kulcstartóhoz

Ha azt szeretné, hogy az Azure Key Vault szoftveres védelemmel ellátott kulcs létrehozásához, használja a `az key create` parancsot, és írja be a következőt:

```azurecli
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```

Azonban ha egy meglévő kulcs egy helyi fájlba, amelyet szeretne feltölteni az Azure Key Vault softkey.pem nevű fájlba menti a .pem fájl található, írja be a következőt a kulcs importálása a. PEM-fájl, a kulcs védetté szoftvereket a Key Vault szolgáltatásban:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```

A kulcs létrehozott vagy az Azure Key Vaultba feltöltött, az URI használatával hivatkozhat. Használjon **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** mindig letöltheti a legfrissebb verziót, és használata **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** lekérni ezt a verziót.

Adja hozzá a titkos kulcs a tárolóba, amely egy SQLPassword nevű jelszót, és, hogy a Pa$ $w0rd az Azure Key Vaultba értékkel rendelkezik, írja be a következőt:

```azurecli
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

Az Azure Key Vaulthoz hozzáadott jelszóra ez után az URI használatával hivatkozhat. Használjon **https://ContosoVault.vault.azure.net/secrets/SQLPassword** mindig letöltheti a legfrissebb verziót, és használata **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** lekérni ezt a verziót.

Tekintse meg a kulcs vagy titkos kulcsot, amelyet most hozott létre:

* A kulcs megtekintéséhez írja be az alábbi parancsot: 

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

* A titkos kód megtekintéséhez írja be az alábbi parancsot: 

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

## <a name="register-an-application-with-azure-active-directory"></a>Alkalmazás regisztrálása az Azure Active Directory szolgáltatásban
Ezt a lépést általában egy fejlesztő végzi egy másik számítógépről. Nem csak az Azure Key Vault, de meg adva, ezekre a műveletekre.

> [!IMPORTANT]
> Az oktatóprogram sikeres befejezéséhez a fiókjának, a tárolónak és az ebben a lépésben regisztrálandó alkalmazásnak ugyanabban az Azure-címtárban kell lenniük.
>
>

A kulcstartót használó alkalmazásoknak az Azure Active Directoryból származó jogkivonat használatával kell hitelesítést végezniük. Ehhez az alkalmazás tulajdonosának először regisztrálnia kell az alkalmazást az Azure Active Directory szolgáltatásban. A regisztrációt követően az alkalmazás tulajdonosa az alábbi értékeket kapja:

- egy **alkalmazásazonosítót**, 
- egy **hitelesítési kulcsot** (más néven közös titkos kódot). 

Az alkalmazásnak mindkét értékkel rendelkeznie kell ahhoz, hogy Azure Active Directory-jogkivonatot kapjon. Az értékek megadásának módja az adott alkalmazástól függ. A [Key Vault-mintaalkalmazás](https://www.microsoft.com/download/details.aspx?id=45343) esetében az alkalmazás tulajdonosa adja meg ezeket az értékeket az app.config fájlban.

Lépésenkénti útmutató az alkalmazás regisztrálása az Azure Active Directoryval tekintse át a című cikk [alkalmazások integrálása az Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) vagy [használata portálon hozzon létre egy Azure Active Directory-alkalmazást és egy egyszerű szolgáltatást, amely erőforrások eléréséhez](../azure-resource-manager/resource-group-create-service-principal-portal.md) az alkalmazás regisztrálása az Azure Active Directoryban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon kattintson az **Alkalmazásregisztrációk** elemre. Ha nem lát alkalmazásregisztrációkat, kattintson a **további szolgáltatások** elemre.  
[!NOTE]
Azt a címtárat kell kiválasztania, amely a kulcstartót létrehozó Azure-előfizetést tartalmazza. 
3. Kattintson az **Új alkalmazásregisztráció** elemre.
4. A **Létrehozás** panelen adjon meg egy nevet az alkalmazásnak, majd válassza a **WEB APPLICATION AND/OR WEB API** (WEBALKALMAZÁS ÉS/VAGY WEBES API) (az alapértelmezett beállítás) lehetőséget, és adja meg a webalkalmazás **SIGN-ON URL** (Bejelentkezési URL) címét. Ha ezt az információt jelenleg nem rendelkezik, hogy az ehhez a lépéshez (megadhatja például http://test1.contoso.com ). Nem számít, hogy ezek a webhelyek léteznek-e. 

    ![Új alkalmazásregisztráció](./media/key-vault-manage-with-cli2/new-application-registration.png)
    >[!WARNING]
    Ügyeljen arra, hogy a **WEB APPLICATION AND/OR WEB API** (WEBALKALMAZÁS ÉS/VAGY WEBES API) lehetőség legyen kiválasztva, különben a **kulcsok** lehetőség nem jelenik meg a beállítások között.

5. Kattintson a **Létrehozás** gombra.
6. Az alkalmazásregisztráció befejezését követően megjelenik a regisztrált alkalmazások listája. Keresse meg az imént regisztrált alkalmazást, és kattintson rá.
7. Kattintson a **Regisztrált alkalmazás** panelre, és másolja az **alkalmazásazonosítót**.
8. Kattintson az **Összes beállítás** elemre.
9. A **Beállítások** panelen kattintson az **kulcsok** elemre.
9. A **Kulcs leírása** mezőben írja be a leírást, válassza ki az időtartamot, majd kattintson **MENTÉS** gombra. A lap ekkor frissül, és kijelzi a kulcs értékét. 
10. A következő lépésben az **alkalmazásazonosító** és a **kulcs** adataival fogja beállítani a tároló engedélyeit.


## <a name="authorize-the-application-to-use-the-key-or-secret"></a>A kulcs vagy titkos kód használatának engedélyezése az alkalmazás számára

Engedélyezi az alkalmazásnak a kulcs vagy a tárolóban lévő titkos kulcs eléréséhez, használja a `az keyvault set-policy` parancsot.

Például ha a tároló neve ContosoKeyVault és a regisztrálni kívánt alkalmazás Ügyfélazonosítója 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed rendelkezik, és az alkalmazás a tároló kulcsait visszafejtse és engedélyezni szeretné, majd futtassa a következő:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Ha engedélyezni szeretné, hogy az alkalmazás megnyithassa a tárolóban lévő titkos kódokat, futtassa az alábbi parancsot:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Hardveres biztonsági modul (HSM) használata

A nagyobb biztonság érdekében hardveres biztonsági modulokkal importálhat vagy hozhat létre a HSM határait mindig betartó kulcsokat. A hardveres biztonsági modulok a 2. szintű FIPS 140-2 szerint vannak érvényesítve. Ha ez a követelmény nem vonatkozik Önre, ugorja át ezt a szakaszt, és folytassa a [Kulcsartó és a hozzá tartozó kulcsok és titkos kódok törlése](#delete-the-key-vault-and-associated-keys-and-secrets) szakasszal.

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

Ha már nincs szüksége a kulcstartót és a kulcs vagy titkos kódra, a key vault használatával törölheti a `az keyvault delete` parancs:

```azurecli
az keyvault delete --name 'ContosoKeyVault'
```

Lehetősége van a teljes Azure-erőforráscsoport törlésére is, amely magában foglalja a kulcstartót és a csoport összes erőforrását:

```azurecli
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Egyéb Azure platformfüggetlen parancssori felület parancsai

Egyéb parancsok, akkor lehet hasznos, ha az Azure Key Vault kezeléséhez.

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
