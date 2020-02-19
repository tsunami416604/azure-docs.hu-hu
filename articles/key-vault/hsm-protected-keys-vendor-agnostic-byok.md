---
title: HSM-védelemmel ellátott kulcsok generálása és átvitele a Azure Key Vault-Azure Key Vaulthoz | Microsoft Docs
description: Ez a cikk segítséget nyújt a saját HSM-védelemmel ellátott kulcsok tervezéséhez, létrehozásához és átviteléhez a Azure Key Vault használatával. Más néven BYOK vagy saját kulcs használata.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 2f5269587d222be9a1628b72c1f3f0dc1b105f3c
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461743"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>HSM-védelemmel ellátott kulcsok importálása a Key Vaultba (előzetes verzió)

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el, és csak az **USA 2. keleti – euap** és az **USA középső** régiójában elérhető – euap régióban érhető el. 

A Azure Key Vault használatakor a rendszer a hardveres biztonsági modulokban (HSM) található kulcsokat importálhatja vagy generálhatja, amelyek soha nem hagyják el a HSM határt. Ezt a forgatókönyvet gyakran a *saját kulcs*használatára vagy BYOK kell hivatkozni. A Azure Key Vault a HSM (FIPS 140-2 2. szint) nCipher nShield-családját használja a kulcsok elleni védelemhez.

A témakörben található információk segítségével megtervezheti, létrehozhatja és átviheti a saját HSM-védelemmel ellátott kulcsait, hogy azok a Azure Key Vault használatával legyenek használatban.

> [!NOTE]
> Ez a funkció az Azure China 21Vianet esetében nem érhető el. 
> 
> Ez az importálási módszer csak a [támogatott HSM](#supported-hsms)esetében érhető el. 

További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](key-vault-overview.md)  Az első lépéseket ismertető oktatóanyaghoz, amely magában foglalja a HSM-védelemmel ellátott kulcsok kulcstartójának létrehozását, lásd: [Mi az Azure Key Vault?](key-vault-overview.md)

## <a name="overview"></a>Áttekintés

* Kulcs (más néven Key Exchange-kulcs vagy KEK) létrehozása a Key vaultban. Ennek egy RSA-HSM-kulcsnak kell lennie, amely csak az "import" lehet, az egyetlen kulcsfontosságú művelet. Csak a Key Vault Premium SKU támogatja az RSA-HSM-kulcsokat.
* A KEK nyilvános kulcsának letöltése. PEM-fájlként
* KEK nyilvános kulcsának átvitele a helyszíni HSM-hez csatlakoztatott offline munkaállomásra.
* A kapcsolat nélküli munkaállomáson használja a HSM-gyártó által biztosított BYOK eszközt egy BYOK-fájl létrehozásához. 
* A célként megadott kulcs egy KEK-mel van titkosítva, amely titkosítva marad, amíg át nem kerül a Azure Key Vault HSM. Csak a kulcs titkosított verziója hagyhatja a helyszíni HSM-et.
* Az Azure Key Vault HSM-ben generált KEK, és nem exportálható. A HSM azt kényszeríti, hogy nem lehet a KEK tiszta verziója a Key Vault HSM kívül.
* A KEK-nek ugyanabban a kulcstartóban kell lennie, ahol a célként megadott kulcsot importálni kívánja.
* Ha a BYOK-fájlt feltöltötte Key Vaultre, Key Vault HSM a KEK titkos kulcsával visszafejti a célként megadott kulcs anyagát, és azt HSM-kulcsként importálja. Ez a művelet teljes egészében bekövetkezik Key Vault HSM, és a célként megadott kulcs mindig a HSM védelmi határain belül marad.

## <a name="prerequisites"></a>Előfeltételek

Az alábbi táblázatban megtekintheti a saját kulcs (BYOK) használatának előfeltételeit a Azure Key Vaulthoz.

| Követelmény | További információ |
| --- | --- |
| Előfizetés az Azure-ba |Azure Key Vault létrehozásához Azure-előfizetésre van szükség: [regisztráljon az ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/) |
| Key Vault (prémium SKU) a HSM-védelemmel ellátott kulcsok importálásához |A Azure Key Vault szolgáltatási szintjeivel és képességeivel kapcsolatos további információkért tekintse meg a [Azure Key Vault díjszabási](https://azure.microsoft.com/pricing/details/key-vault/) webhelyét. |
| Egy HSM a támogatott HSM-listából, valamint a BYOK eszközzel és a HSM-gyártó által biztosított utasításokkal | Hozzá kell férnie egy hardveres biztonsági modulhoz és a HSM alapvető működési ismereteihez. Lásd: [támogatott HSM](#supported-hsms). |
| Azure CLI-verzió 2.1.0 vagy újabb verziója | További információért lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli?view=azure-cli-latest) .|

## <a name="supported-hsms"></a>Támogatott HSM

|HSM-szállító neve|Támogatott HSM-modellek|További részletek|
|---|---|---|
|Thales|SafeNet Luna HSM 7 termékcsalád a belső vezérlőprogram 7,3-es vagy újabb verziójával| [SafeNet Luna BYOK eszköz és dokumentáció](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|


> [!NOTE]
> HSM-védelemmel ellátott kulcsok importálása a nCipher nShield-HSM-családból [örökölt BYOK eljárás használata](hsm-protected-keys-legacy.md)


## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>A kulcs előállítása és átvitele Azure Key Vault HSM-be

Az alábbi lépéseket követve hozhatja elő és viheti át a kulcsot egy Azure Key Vault HSM-be:

* [1. lépés: KEK létrehozása](#step-1-generate-a-kek)
* [2. lépés: a KEK nyilvános kulcsának letöltése](#step-2-download-kek-public-key)
* [3. lépés: a kulcs előállítása és előkészítése az átvitelhez](#step-3-generate-and-prepare-your-key-for-transfer)
* [4. lépés: a kulcs átvitele Azure Key Vaultre](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>1\. lépés: KEK létrehozása

A KEK (kulcscsere-kulcs) a Key Vault HSM-ben generált RSA-kulcs. Ezzel a kulccsal lehet titkosítani az importálandó kulcsot (célként megadott kulcs).

A KEK a következőket kell tennie:
1. egy **RSA-HSM-** kulcs (2048 bites vagy 3072 bites vagy 4096 bites)
2. ugyanabban a kulcstartóban lett létrehozva, amelyben importálni kívánja a célként megadott kulcsot
3. létrehozva az **importálásra** beállított engedélyezett kulcs-műveletekkel

Az az kulcstartó [kulcs létrehozása](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) paranccsal hozzon létre KEK-et az importálásra beállított fő műveletekkel. Jegyezze fel az alábbi parancs által visszaadott "Kid" azonosítójú kulcsot. A [3. lépésben](#step-3-generate-and-prepare-your-key-for-transfer)szüksége lesz rá.


```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-kek-public-key"></a>2\. lépés: a KEK nyilvános kulcsának letöltése

Az az [kulcstartó kulcs letöltése](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) paranccsal töltse le a KEK nyilvános kulcsát egy. PEM-fájlba. Az importált célként megadott kulcs a KEK nyilvános kulcsával van titkosítva.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Vigye át a KEKforBYOK. PUBLICKEY. PEM fájlt a kapcsolat nélküli munkaállomásra. A következő lépésben szüksége lesz erre a fájlra.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>3\. lépés: a kulcs előállítása és előkészítése az átvitelhez

A BYOK eszköz letöltéséhez és telepítéséhez tekintse meg a HSM gyártójának dokumentációját. A HSM-gyártó utasításait követve hozzon létre egy célként megadott kulcsot, majd hozzon létre egy kulcs-átviteli csomagot (egy BYOK-fájlt). A BYOK eszköz a [2](#step-2-download-kek-public-key) . lépésben letöltött [1](#step-1-generate-a-kek) . és KEKforBYOK. PUBLICKEY. PEM fájl kulcs-azonosítóját fogja használni egy BYOK-fájlban lévő titkosított célként generált kulcs létrehozásához.

Vigye át a BYOK-fájlt a csatlakoztatott munkaállomásra.

> [!NOTE] 
> A célként megadott kulcsnak 2048 bites vagy 3072 bites vagy 4096 bites RSA-kulcsnak kell lennie. Az elliptikus görbe kulcsok importálása jelenleg nem támogatott.
> <br/><strong>Ismert probléma:</strong> A SafeNet Luna HSM nem tudja importálni az RSA 4K-beli célként megadott kulcsot. A probléma megoldásakor a rendszer frissíti a dokumentumot.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>4\. lépés: a kulcs átvitele Azure Key Vaultre

Ebben az utolsó lépésben vigye át a kulcs átvitele csomagot (egy BYOK-fájlt) a leválasztott munkaállomásról az internethez kapcsolódó munkaállomásra, majd az az kulcstartó [kulcs importálása](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) paranccsal töltse fel a BYOK-fájlt a Azure Key Vault HSM-be a kulcs importálásának befejezéséhez.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Ha a feltöltés sikeres, megjelenik az imént importált kulcs tulajdonságai.

## <a name="next-steps"></a>Következő lépések

Ezt a HSM-védelemmel ellátott kulcsot már használhatja a kulcstartóban. További információkért tekintse meg ezt az árat és a szolgáltatás [összehasonlítását](https://azure.microsoft.com/pricing/details/key-vault/).
