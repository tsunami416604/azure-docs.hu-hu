---
title: "Kezelheti az Azure Key Vault parancssori felülettel |} Microsoft Docs"
description: "Ez az oktatóanyag segítségével a Key Vault gyakori feladatok automatizálásához a CLI 2.0 használatával"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ambapat
ms.openlocfilehash: 5da9f5eceda71ac85259193e0f183c72813e1679
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-key-vault-using-cli-20"></a>Kezelheti a Key Vault 2.0 parancssori felület használatával
Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Bevezetés
Ez az oktatóanyag segít megismerni az Azure Key Vault szolgáltatást, amellyel megerősített tárolókat (kulcstartókat) hozhat létre, valamint kriptográfiai kulcsokat és titkos kódokat tárolhat az Azure-ban. Végigvezeti az Azure platformfüggetlen parancssori felület használatával hozzon létre egy tárolót, amely tartalmazza a kulcs vagy a jelszót, majd használható az Azure-alkalmazások a folyamat. Ezután bemutatja, hogyan alkalmazás használhatja adott kulcsot vagy jelszót.

**Az oktatóanyag áttekintésének várható időtartama:** 20 perc

> [!NOTE]
> Ez az oktatóanyag nem tartalmazza, hogy a lépések egyikét tartalmazza, amely bemutatja, hogyan az alkalmazások a key vault kulcsok vagy titkos kulcsok használata az Azure-alkalmazások írásával útmutatást.
>
> Ez az oktatóanyag az Azure CLI legújabb 2.0 használja.
>
>

Áttekintést az Azure Key Vaultról a [What is Azure Key Vault?](key-vault-whatis.md) (Mi az Azure Key Vault?) című cikkben talál.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez szüksége lesz:

* Egy Microsoft Azure-előfizetésre. Ha még nem rendelkezik ilyennel, akkor regisztrálhatnak az egy [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial).
* Parancssori felület 2.0-s vagy újabb verziója. Telepítse a legújabb verziót, és csatlakozzon az Azure-előfizetéssel, [telepítése és konfigurálása az Azure platformfüggetlen parancssori felület 2.0](/cli/azure/install-azure-cli).
* Egy, az útmutató során létrehozott kulcs vagy jelszó használatához konfigurált alkalmazásra. Egy mintaalkalmazás elérhető a [Microsoft letöltőközpontból](http://www.microsoft.com/download/details.aspx?id=45343). Útmutatásért tekintse meg a kísérő információs fájlt.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Segítség az Azure platformfüggetlen parancssori felülettel
Ez az oktatóanyag feltételezi, hogy ismeri a parancssori felülettel (Bash, terminál, parancssor)

A – Súgó vagy -h paraméter használható parancsok súgójának megtekintéséhez. Alternatív megoldásként az azure súgó [parancs] [kapcsolók] formátumban is használható ugyanazokat az információkat adja vissza. A következő parancsokat minden például ugyanazokat az információkat adja vissza:

```
az account set --help
az account set -h
```

A parancs által igényelt paraméterekről kétségei vannak, tekintse meg a használatával – súgó, – h vagy az [parancs] segítségével.

Az alábbi útmutatókkal megismerkedhet az Azure Resource Manager az Azure platformfüggetlen parancssori felülettel is olvasható:

* [Az Azure parancssori felület telepítése](/cli/azure/install-azure-cli)
* [Ismerkedés az Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Csatlakozás az előfizetésekhez
Jelentkezzen be egy szervezeti fiókjával, használja a következő parancsot:

```
az login -u username@domain.com -p password
```

vagy ha azt szeretné, írja be a interaktívan bejelentkezni

```
az login
```

Ha több előfizetése van, és meg szeretné szabni, hogy melyiket használja az Azure Key Vaulthoz, írja be az alábbi parancsot a fiókhoz tartozó előfizetések megtekintéséhez:

```
az account list
```

Ezt követően írja be az alábbi parancsot a kívánt előfizetés kiválasztásához:

```
az account set --subscription <subscription name or ID>
```

Azure platformfüggetlen parancssori felületre konfigurálásával kapcsolatos további információkért lásd: [Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Új erőforráscsoport létrehozása
Azure Resource Manager használatakor minden kapcsolódó erőforrás egy erőforráscsoportban jönnek létre. Ebben az oktatóanyagban létrehozunk egy új erőforráscsoportot "ContosoResourceGroup".

```
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

Az első paraméter az erőforráscsoport neve, a második paraméter pedig a hely. A helyen, a parancs használata `az account list-locations` ebben a példában egy másik elérési utat megadása azonosításához. Ha további tájékoztatásra van szüksége, írja be: `az account list-locations -h`.

## <a name="register-the-key-vault-resource-provider"></a>A Key Vault erőforrás-szolgáltató regisztrálása
Győződjön meg arról, hogy Key Vault erőforrás-szolgáltató regisztrálva van az előfizetésben:

```
az provider register -n Microsoft.KeyVault
```

Ez csak azért van szükség, egyszer előfizetésenként.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Használja a `az keyvault create` parancs futtatásával hozzon létre egy kulcstartót. Ez a parancsfájl három kötelező paraméterrel rendelkezik: erőforráscsoport-nevet, a kulcstároló neve és a földrajzi helyet.

Például ha használja a tároló neve ContosoKeyVault, az erőforráscsoport neve ContosoResourceGroup és helyét, Kelet-Ázsia, írja be:
```
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

Ez a parancs kimenetét mutatja az újonnan létrehozott kulcstartó tulajdonságait. A két legfontosabb tulajdonság:

* **név**: a példában ez az ContosoKeyVault. Más Key Vault parancsok ezt a nevet fogja használni.
* **vaultUri**: a példában ez az https://contosokeyvault.vault.azure.net. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Azure-fiókja most már engedéllyel rendelkezik arra, hogy bármilyen műveletet végezzen ezen a kulcstartón. Egyelőre senki másnak nincs erre engedélye.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Kulcs vagy titkos kód hozzáadása a kulcstartóhoz
Ha azt szeretné, hogy az Azure Key Vault szoftveres védelemmel ellátott kulcs létrehozásához, használja a `az key create` parancsot, és írja be a következőt:
```
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```
Azonban ha egy meglévő kulcs egy helyi fájlba, amelyet szeretne feltölteni az Azure Key Vault softkey.pem nevű fájlba menti a .pem fájl található, írja be a következőt a kulcs importálása a. PEM-fájl, a kulcs védetté szoftvereket a Key Vault szolgáltatásban:
```
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```
A kulcs létrehozott vagy az Azure Key Vaultba feltöltött, az URI használatával hivatkozhat. Használjon **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** mindig letöltheti a legfrissebb verziót, és használata **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** lekérni ezt a verziót.

Adja hozzá a titkos kulcs a tárolóba, amely egy SQLPassword nevű jelszót, és, hogy a Pa$ $w0rd az Azure Key Vaultba értékkel rendelkezik, írja be a következőt:
```
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```
Az Azure Key Vaulthoz hozzáadott jelszóra ez után az URI használatával hivatkozhat. A **https://ContosoVault.vault.azure.net/secrets/SQLPassword** oldalról mindig letöltheti a legfrissebb verziót, a **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** oldalról pedig ezt a verziót töltheti le.

Tekintse meg a kulcs vagy titkos kulcsot, amelyet most hozott létre:

* A kulcs megtekintéséhez írja be az alábbi parancsot:`az keyvault key list --vault-name 'ContosoKeyVault'`
* A titkos kód megtekintéséhez írja be az alábbi parancsot:`az keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Alkalmazás regisztrálása az Azure Active Directory szolgáltatásban
Ezt a lépést általában egy fejlesztő végzi egy másik számítógépről. Nem csak az Azure Key Vault, de meg adva, feltétlenül teljességében.

> [!IMPORTANT]
> Az oktatóprogram sikeres befejezéséhez a fiókjának, a tárolónak és az ebben a lépésben regisztrálandó alkalmazásnak ugyanabban az Azure-címtárban kell lenniük.
>
>

A kulcstartót használó alkalmazásoknak az Azure Active Directoryból származó jogkivonat használatával kell hitelesítést végezniük. Ehhez az alkalmazás tulajdonosának először regisztrálnia kell az alkalmazást az Azure Active Directory szolgáltatásban. A regisztrációt követően az alkalmazás tulajdonosa az alábbi értékeket kapja:

* Egy **alkalmazásazonosítót** (más néven ügyfélazonosítót) és egy **hitelesítési kulcsot** (más néven közös titkos kódot). Az alkalmazás e mindkét ezeket az értékeket az Azure Active Directory, a szolgáltatáshitelesítést egy token. Az értékek megadásának módja az adott alkalmazástól függ. A Key Vault mintaalkalmazás esetében az alkalmazás tulajdonosa adja meg ezeket az értékeket az app.config fájlban.

Az alkalmazás regisztrálása az Azure Active Directory szolgáltatásban:

1. Jelentkezzen be az Azure portálra.
2. Kattintson a bal oldali **Azure Active Directory**, majd válassza ki a könyvtárat, amelyben regisztrálni fogja az alkalmazást. <br> <br> 

> [!Note] 
> Ki kell választania az ugyanabban a könyvtárban, amely a kulcstartót létrehozó Azure-előfizetést tartalmazza. Ha nem tudja, hogy melyik ez a címtár, kattintson a **Settings** (Beállítások) lehetőségre, keresse meg a kulcstartót létrehozó előfizetést, és jegyezze fel az utolsó oszlopban megjelenített nevét.

3. Kattintson az **APPLICATIONS** (ALKALMAZÁSOK) elemre. Ha az alkalmazás nem érhetőek el a címtárban, ezen a lapon csak jelennek-e a **hozzáadhat egy alkalmazást** hivatkozásra. Kattintson a hivatkozásra, vagy másik lehetőségként kattinthat a **hozzáadása** a parancssávon.
4. Az **ADD APPLICATION** (ALKALMAZÁS HOZZÁADÁSA) varázsló **What do you want to do?** (Mit kíván tenni?) lapján kattintson a **Add an application my organization is developing** (A vállalatom által fejlesztett alkalmazás hozzáadása) lehetőségre.
5. Az a **adja meg azt az alkalmazás** lapon adja meg az alkalmazás nevét, és válassza **WEB APPLICATION AND/OR WEB API** (alapértelmezett). Kattintson a Tovább ikonra.
6. Az **App properties** (Alkalmazás tulajdonságai) oldalon adja meg a webalkalmazás bejelentkezési URL-címét és az alkalmazásazonosító URI-ját a **SIGN-ON URL**, illetve az **APP ID URI** mezőkben. Ha az alkalmazás nem rendelkezik ezekkel az értékekkel, ehhez a lépéshez nem létező értékeket is megadhat (például http://test1.contoso.com mindkét mezőhöz). Nem számít, hogy létezik-e az adott weboldal. A lényeg az, hogy az alkalmazásazonosító URI-jai eltérőek legyenek a címtár mindegyik alkalmazásában. A címtár ezzel a karakterlánccal azonosítja az alkalmazást.
7. Kattintson a teljes ikonra a varázslóban a módosítások mentéséhez.
8. Kattintson a gyors üzembe helyezés lap **KONFIGURÁLÁSA**.
9. Görgessen a **keys** (kulcsok) szakaszhoz, adja meg az időtartamot, majd kattintson a **SAVE** (MENTÉS) gombra. A lap ekkor frissül, és kijelzi a kulcs értékét. Az alkalmazásban ezt a kulcsértéket, valamint **CLIENT ID** (ÜGYFÉLAZONOSÍTÓ) értéket kell beállítani. (A beállítás menete alkalmazásonként eltérő lehet.)
10. Másolja ki a lapról az ügyfélazonosító értékét, mert ezzel fogja beállítani a tároló engedélyeit a következő lépésben.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>A kulcs vagy titkos kód használatának engedélyezése az alkalmazás számára
Engedélyezi az alkalmazásnak a kulcs vagy a tárolóban lévő titkos kulcs eléréséhez, használja a `az keyvault set-policy` parancsot.

Például ha a tároló neve ContosoKeyVault és a regisztrálni kívánt alkalmazás Ügyfélazonosítója 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed rendelkezik, és az alkalmazás a tároló kulcsait visszafejtse és engedélyezni szeretné, majd futtassa a következő:
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Ha engedélyezni szeretné, hogy az alkalmazás megnyithassa a tárolóban lévő titkos kódokat, futtassa az alábbi parancsot:
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```
## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Hardveres biztonsági modul (HSM) használata
A nagyobb biztonság érdekében hardveres biztonsági modulokkal importálhat vagy hozhat létre a HSM határait mindig betartó kulcsokat. A hardveres biztonsági modulok a 2. szintű FIPS 140-2 szerint vannak érvényesítve. Ha ez a követelmény nem vonatkozik Önre, ugorja át ezt a szakaszt, és folytassa a [Kulcsartó és a hozzá tartozó kulcsok és titkos kódok törlése](#delete-the-key-vault-and-associated-keys-and-secrets) szakasszal.

A HSM által védett kulcsok létrehozásához HSM által védett kulcsokat támogató tárolóra előfizetéssel kell rendelkeznie.

A keyvault létrehozásakor adja hozzá az "sku" paramétert:

```
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```
Ehhez a tárolóhoz szoftveresen védett (korábban bemutatva) és HSM által védett kulcsokat is hozzáadhat. Egy HSM által védett kulcs létrehozásához állítsa be a cél paramétert "HSM":

```
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

A következő paranccsal kulcsot importálhat .pem fájlt a számítógépen. Ez a parancs a hardveres Key Vault szolgáltatás biztonsági moduljaiba importálja a kulcsot:

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```
A következő parancs egy „saját kulcs használata” (BYOK-) csomagot importál. Ezzel a helyi HSM-ben hozhatja létre a kulcsot, majd helyezheti át a Key Vault szolgáltatás HSM-jeire anélkül, hogy a kulcs elhagyná a HSM határait:

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```
Leírja, hogyan a BYOK-csomag létrehozásához további: [HSM-Protected kulcsok használata az Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Kulcstartó és a hozzá tartozó kulcsok és titkos kódok törlése
Ha már nincs szüksége a kulcstartót és a kulcs vagy titkos kódra, a key vault használatával törölheti a `az keyvault delete` parancs:

```
az keyvault delete --name 'ContosoKeyVault'
```

Lehetősége van a teljes Azure-erőforráscsoport törlésére is, amely magában foglalja a kulcstartót és a csoport összes erőforrását:

```
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Egyéb Azure platformfüggetlen parancssori felület parancsai
Egyéb parancsok, akkor lehet hasznos, ha az Azure Key Vault kezeléséhez.

Ez a parancs táblázatos formában jeleníti meg az összes kulcsot és a kijelölt tulajdonságok:

az keyvault kulcslista--tároló-neve "ContosoKeyVault"

Ez a parancs a megadott kulcs tulajdonságainak teljes listáját jeleníti meg:

az keyvault kulcs megjelenítése--tároló-neve "ContosoKeyVault"--"ContosoFirstKey" neve

Ez a parancs táblázatos formában jeleníti meg az összes titkos kód nevét és a kijelölt tulajdonságokat:

az keyvault titkos lista--tároló-neve "ContosoKeyVault"

Itt látható egy példa egy adott kulcs eltávolítására:

az keyvault kulcs törlése--tároló-neve "ContosoKeyVault"--"ContosoFirstKey" neve

Itt látható egy példa egy adott titkos kód eltávolítása:

az keyvault titkos kulcs törlése--tároló-neve "ContosoKeyVault"--"SQLPassword" neve


## <a name="next-steps"></a>Következő lépések
A kulcstároló parancsok teljes Azure parancssori felület referenciáért lásd: [Key Vault CLI-hivatkozás](/cli/azure/keyvault)

Programozási hivatkozások: [Az Azure Key Vault fejlesztői útmutatója](key-vault-developers-guide.md).
