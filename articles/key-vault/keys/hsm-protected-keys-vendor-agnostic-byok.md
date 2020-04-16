---
title: HSM-védelemmel ellátott kulcsok létrehozása és átvitele az Azure Key Vaulthoz – Azure Key Vault | Microsoft dokumentumok
description: Ebből a cikkből segítséget nyújt saját HSM-védelemmel ellátott kulcsok megtervezéséhez, létrehozásához és átviteléhez az Azure Key Vault használatával. Más néven, hogy a saját kulcs (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 77568928e50fb4398aa786dbbd4a44b9277a8d5b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429706"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>HSM által védett kulcsok importálása a Key Vaultba (előzetes verzió)

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el, és csak az *USA keleti régióiban 2 EUAP* és *az USA középső régiójában érhető el.* 

Az Azure Key Vault használata kor további biztonság érdekében importálhat vagy létrehozhat egy kulcsot egy hardveres biztonsági modulban (HSM); a kulcs soha nem hagyja el a HSM határt. Ezt a forgatókönyvet gyakran saját kulcs (BYOK) *-ként* nevezik. Key Vault használja nCipher nShield család HSMs (FIPS 140-2 Level 2 érvényesített) a kulcsok védelmére.

Az ebben a cikkben található információk segítségével megtervezheti, létrehozhatja és átviheti saját HSM-védelemmel ellátott kulcsait az Azure Key Vault használatával.

> [!NOTE]
> Ez a funkció nem érhető el az Azure China 21Vianet. 
> 
> Ez az importálási módszer csak [a támogatott HSM-ek](#supported-hsms)esetében érhető el. 

További információért és a Key Vault használatának első használatáért (beleértve a HSM-védelemmel ellátott kulcsok key vaultjának létrehozását is) című oktatóanyagról a [Mi az Azure Key Vault?](../general/overview.md)

## <a name="overview"></a>Áttekintés

Íme egy áttekintés a folyamatról. A cikk későbbi részében ismertetett konkrét lépések találhatók.

* A Key Vaultban hozzon létre egy kulcsot (a továbbiakban: *kulcscserekulcs* (KEK)). A KEK-nek olyan RSA-HSM kulcsnak kell lennie, amely csak a `import` kulcsműveletet rendelkezik. Csak a Key Vault Premium Termékváltozat támogatja az RSA-HSM kulcsokat.
* Töltse le a KEK nyilvános kulcsot .pem fájlként.
* A KEK nyilvános kulcsátvitele egy helyszíni HSM-hez csatlakoztatott offline számítógépre.
* Az offline számítógépen a HSM-szállító által biztosított BYOK eszközzel hozzon létre egy BYOK-fájlt. 
* A célkulcs egy KEK-vel van titkosítva, amely mindaddig titkosítva marad, amíg át nem kerül a Key Vault HSM-be. Csak a kulcs titkosított verziója hagyja el a helyszíni HSM-et.
* A Key Vault HSM-en belül létrehozott KEK nem exportálható. HSM-ek kényszerítik azt a szabályt, hogy a Key Vault HSM-en kívül nincs kek-verzió.
* A KEK-nek ugyanabban a kulcstartóban kell lennie, ahol a célkulcsot importálja.
* Amikor a BYOK-fájlt feltölti a Key Vaultba, a Key Vault HSM a KEK személyes kulcsa segítségével visszafejti a célkulcs anyagát, és hsm-kulcsként importálja azt. Ez a művelet teljes mértékben a Key Vault HSM-en belül történik. A célkulcs mindig a HSM védelmi határán marad.

## <a name="prerequisites"></a>Előfeltételek

Az alábbi táblázat a BYOK Azure Key Vaultban való használatának előfeltételeit sorolja fel:

| Követelmény | További információ |
| --- | --- |
| Azure-előfizetés |Ha hozzon létre egy kulcstartót az Azure Key Vaultban, szüksége van egy Azure-előfizetésre. [Iratkozzon fel egy ingyenes próbaverzióra.](https://azure.microsoft.com/pricing/free-trial/) |
| A Key Vault prémium termékváltozat a HSM által védett kulcsok importálásához |Az Azure Key Vault szolgáltatási rétegeiről és képességeiről a [Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/)című témakörben talál további információt. |
| HSM a támogatott HSM-ek listájából, valamint egy BYOK eszköz és a HSM-szállító által biztosított utasítások | A HSM-hez engedéllyel kell rendelkeznie, és alapvető ismeretekkel kell rendelkeznie a HSM használatához. Lásd: [Támogatott HSMs](#supported-hsms). |
| Az Azure CLI 2.1.0-s vagy újabb verziója | Lásd: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli?view=azure-cli-latest)|

## <a name="supported-hsms"></a>Támogatott HSMs

|Szállító neve|Szállító típusa|Támogatott HSM modellek|További információ|
|---|---|---|---|
|Thales|Gyártó|SafeNet Luna HSM 7 család firmware verzióval 7.3 vagy újabb| [SafeNet Luna BYOK eszköz és dokumentáció](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix között|HSM szolgáltatásként|Önvédő kulcskezelő szolgáltatás (SDKMS)|[SDKMS-kulcsok exportálása a BYOK felhőszolgáltatóiba – Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|


> [!NOTE]
> Ha HSM-védelemmel ellátott kulcsokat szeretne importálni az nCipher nShield hsm-családból, használja az [örökölt BYOK eljárást.](hsm-protected-keys-legacy.md)

## <a name="supported-key-types"></a>Támogatott kulcstípusok

|Kulcs neve|Kulcs típusa|Kulcs mérete|Forrás|Leírás|
|---|---|---|---|---|
|Kulcscserekulcs (KEK)|RSA| 2 048 bites<br />3 072 bites<br />4 096 bites|Azure Key Vault HSM|Az Azure Key Vaultban létrehozott HSM-alapú RSA-kulcspár|
|Célkulcs|RSA|2 048 bites<br />3 072 bites<br />4 096 bites|Szállító IM|Az Azure Key Vault HSM-be átvihető kulcs|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>A kulcs létrehozása és átvitele a Key Vault HSM-be

Kulcs létrehozása és átvitele a Key Vault HSM-be:

* [1. lépés: KEK létrehozása](#step-1-generate-a-kek)
* [2. lépés: A KEK nyilvános kulcsletöltése](#step-2-download-the-kek-public-key)
* [3. lépés: A kulcs létrehozása és előkészítése az átvitelhez](#step-3-generate-and-prepare-your-key-for-transfer)
* [4. lépés: Kulcs átvitele az Azure Key Vaultba](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>1. lépés: KEK létrehozása

A KEK egy RSA-kulcs, amely egy Key Vault HSM-ben jön létre. A KEK az importálni kívánt kulcs (a *célkulcs)* titkosítására szolgál.

A KEK-nek:
- RSA-HSM kulcs (2048 bites; 3072 bites; vagy 4096 bites)
- Ugyanabban a kulcstartóban jön létre, ahol importálni kívánja a célkulcsot
- Az engedélyezett kulcsműveletekkel létrehozva`import`

> [!NOTE]
> A KEK-nek "import" (import' (import' néven kell rendelkeznie az egyetlen engedélyezett kulcsműveletnek. az "import" kölcsönösen kizárja az összes többi kulcsfontosságú műveletet.

Az [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) paranccsal hozzon létre `import`egy KEK-et, amelynek kulcsműveletei a. Rögzítse a következő`kid`parancsból visszaadott kulcsazonosítót ( ). (A `kid` [3. lépésértékét](#step-3-generate-and-prepare-your-key-for-transfer)fogja használni.)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>2. lépés: A KEK nyilvános kulcsletöltése

Az [az keyvault kulcsletöltéssel](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) töltse le a KEK nyilvános kulcsot egy .pem fájlba. Az importált célkulcs a KEK nyilvános kulcsával lesz titkosítva.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Vigye át a KEKforBYOK.publickey.pem fájlt az offline számítógépre. Szüksége lesz erre a fájlra a következő lépésben.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>3. lépés: A kulcs létrehozása és előkészítése az átvitelhez

A BYOK eszköz letöltéséhez és telepítéséhez olvassa el a HSM gyártójának dokumentációját. A célkulcs létrehozásához kövesse a HSM-szállító utasításait, majd hozzon létre egy kulcsátviteli csomagot (EGY BYOK-fájlt). A BYOK eszköz `kid` az [1.](#step-1-generate-a-kek) [Step 2](#step-2-download-the-kek-public-key)

Vigye át a BYOK-fájlt a csatlakoztatott számítógépre.

> [!NOTE] 
> Az RSA 1024 bites kulcsok importálása nem támogatott. Jelenleg az Elliptikus görbe (EK) kulcs importálása nem támogatott.
> 
> **Ismert probléma:** Az RSA 4K célkulcs SafeNet Luna HSM-ekből történő importálása csak a 7.4.0-s vagy újabb belső vezérlőprogrammal támogatott.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>4. lépés: Kulcs átvitele az Azure Key Vaultba

A kulcsimportálás befejezéséhez vigye át a kulcsátviteli csomagot (egy BYOK-fájlt) a leválasztott számítógépről az internethez csatlakoztatott számítógépre. Használja az [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) parancs feltöltheti a BYOK fájlt a Key Vault HSM.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Ha a feltöltés sikeres, az Azure CLI megjeleníti az importált kulcs tulajdonságait.

## <a name="next-steps"></a>További lépések

Most már használhatja ezt a HSM-védelemmel ellátott kulcsot a key vaultban. További információkért lásd [ezt az árat és a szolgáltatás összehasonlítását](https://azure.microsoft.com/pricing/details/key-vault/).



