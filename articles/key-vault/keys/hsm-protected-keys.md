---
title: HSM-védelemmel ellátott kulcsok generálása és átvitele a Azure Key Vault-Azure Key Vaulthoz | Microsoft Docs
description: Ez a cikk segítséget nyújt a saját HSM-védelemmel ellátott kulcsok tervezéséhez, létrehozásához és átviteléhez a Azure Key Vault használatával. Más néven BYOK vagy saját kulcs használata.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: b624e521ee102f331100f2836d2e33fd8f2b3ec8
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193661"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>HSM által védett kulcsok importálása a Key Vaultba

Ha további garanciára van szüksége, amikor Azure Key Vault használ, a hardveres biztonsági modulokban (HSM) olyan kulcsokat importálhat vagy generálhat, amelyek soha nem hagyják el a HSM-határt. Ennek a megoldásnak a neve *saját kulcs használata*, angol betűszóval BYOK. A Azure Key Vault a HSM (FIPS 140-2 2. szint) nCipher nShield-családját használja a kulcsok elleni védelemhez.

Ez a funkció az Azure China 21Vianet esetében nem érhető el.

> [!NOTE]
> További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../general/overview.md)  
> Az első lépéseket ismertető oktatóanyaghoz, amely magában foglalja a HSM-védelemmel ellátott kulcsok kulcstartójának létrehozását, lásd: [Mi az Azure Key Vault?](../general/overview.md)

## <a name="supported-hsms"></a>Támogatott HSM

A HSM-védelemmel ellátott kulcsok Key Vaultre való átvitele a használt HSM függően két különböző módszer használatával támogatott. Az alábbi táblázat segítségével meghatározhatja, hogy melyik módszert kell használni a HSM létrehozásához, majd átvinni a saját HSM-védelemmel ellátott kulcsait, hogy azok a Azure Key Vault használatával legyenek használatban. 

|Szállító neve|Szállító típusa|Támogatott HSM-modellek|Támogatott HSM-Key átvitel módszer|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Gyártó<br/>HSM szolgáltatásként|<ul><li>HSM nShield családja</li><li>nShield szolgáltatásként</ul>|**1. módszer:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (erős igazolással a kulcs importálásához és a HSM ellenőrzéséhez)<br/>**2. módszer:** [új BYOK metódus használata](hsm-protected-keys-byok.md) |
|Thales|Gyártó|<ul><li>SafeNet Luna HSM 7 termékcsalád a belső vezérlőprogram 7,3-es vagy újabb verziójával</li></ul>| [Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Fortanix|HSM szolgáltatásként|<ul><li>A kulcskezelő szolgáltatás (SDKMS) önvédelme</li></ul>|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Marvell|Gyártó|Az összes LiquidSecurity-HSM a<ul><li>Belső vezérlőprogram verziója 2.0.4 vagy újabb</li><li>Belső vezérlőprogram 3,2-es vagy újabb verziója</li></ul>|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Enterprise Key Management System)|Több HSM-márka és-modell, beleértve a következőket is<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>[Részletekért lásd a Cryptopmathic-webhelyet](https://www.cryptomathic.com/azurebyok)|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>További lépések

* Kövesse [Key Vault ajánlott eljárásokat](../general/best-practices.md) a kulcsok biztonságának, tartósságának és figyelésének biztosításához.
* Az új BYOK metódus teljes leírását a [BYOK-specifikációban](https://docs.microsoft.com/azure/key-vault/keys/byok-specification) találja.
