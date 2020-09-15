---
title: HSM által védett kulcsok &ának generálása – Azure Key Vault
description: Megtudhatja, hogyan tervezheti meg, generálhatja és továbbíthatja a saját HSM-védelemmel ellátott kulcsait a Azure Key Vault használatával való használatra. Más néven BYOK vagy saját kulcs használata.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 33249da6772d146de3e5e7351bc82c203674ddff
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532137"
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
|Thales|Gyártó|<ul><li>Luna HSM 7 család belső vezérlőprogram 7,3-es vagy újabb verziójával</li></ul>| [Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Fortanix|Gyártó<br/>HSM szolgáltatásként|<ul><li>A kulcskezelő szolgáltatás (SDKMS) önvédelme</li><li>Equinix SmartKey</li></ul>|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Marvell|Gyártó|Az összes LiquidSecurity-HSM a<ul><li>Belső vezérlőprogram verziója 2.0.4 vagy újabb</li><li>Belső vezérlőprogram 3,2-es vagy újabb verziója</li></ul>|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Enterprise Key Management System)|Több HSM-márka és-modell, beleértve a következőket is<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>[Részletekért lásd a Cryptomathic-webhelyet](https://www.cryptomathic.com/azurebyok)|[Új BYOK metódus használata](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>Következő lépések

* Kövesse [Key Vault ajánlott eljárásokat](../general/best-practices.md) a kulcsok biztonságának, tartósságának és figyelésének biztosításához.
* Az új BYOK metódus teljes leírását a [BYOK-specifikációban](https://docs.microsoft.com/azure/key-vault/keys/byok-specification) találja.
