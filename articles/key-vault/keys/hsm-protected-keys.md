---
title: HSM-védelemmel ellátott kulcsok létrehozása és átvitele az Azure Key Vaulthoz – Azure Key Vault | Microsoft dokumentumok
description: Ebből a cikkből segítséget tervezhet, hozhat létre, majd vihetát át saját HSM-védelemmel ellátott kulcsait az Azure Key Vault használatával. Más néven BYOK, vagy hogy a saját kulcsát.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 58cf3358a9e908070ce9003d05dd0b576b1d2d3f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429693"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>HSM-védelemmel ellátott kulcsok importálása a Key Vaultba

A további biztonság érdekében az Azure Key Vault használatakor importálhat vagy létrehozhat kulcsokat a hardveres biztonsági modulokban (HSM-ekben), amelyek soha nem hagyják el a HSM-határt. Ennek a megoldásnak a neve *saját kulcs használata*, angol betűszóval BYOK. Az Azure Key Vault nCipher nShield HSM-család (FIPS 140-2 Level 2 érvényesített) a kulcsok védelme érdekében.

Ez a funkció nem érhető el az Azure China 21Vianet.

> [!NOTE]
> Az Azure Key Vaultról a [Mi az Azure Key Vault?](../general/overview.md)  
> Az első lépések oktatóanyag, amely magában foglalja a HSM által védett kulcsok key vault létrehozása, [lásd: Mi az Azure Key Vault?](../general/overview.md)

## <a name="supported-hsms"></a>Támogatott HSMs

A HSM-védelemmel ellátott kulcsok key vaultba történő átvitele a hsm-ektől függően két különböző módszerrel támogatott. Az alábbi táblázat segítségével határozza meg, hogy a HSM-ek létrehozása milyen módszert használjon, majd vigye át a saját HSM-védelemmel ellátott kulcsokat az Azure Key Vault használatával. 

|Szállító neve|Szállító típusa|Támogatott HSM modellek|Támogatott HSM-kulcsátviteli módszer|
|---|---|---|---|
|nCipher|Gyártó|<ul><li>nShield hsm-család</li></ul>|[Örökölt BYOK-metódus használata](hsm-protected-keys-legacy.md)|
|Thales|Gyártó|<ul><li>SafeNet Luna HSM 7 család firmware verzió 7.3 vagy újabb</li></ul>| [Új BYOK metódus használata (előzetes verzió)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix között|HSM szolgáltatásként|<ul><li>Önvédő kulcskezelő szolgáltatás (SDKMS)</li></ul>|[Új BYOK metódus használata (előzetes verzió)](hsm-protected-keys-vendor-agnostic-byok.md)|


## <a name="next-steps"></a>További lépések

Kövesse [a Key Vault bevált módszereit](../general/best-practices.md) a kulcsok biztonságának, tartósságának és figyelésének biztosítása érdekében.
