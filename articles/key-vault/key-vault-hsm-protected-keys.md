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
ms.openlocfilehash: 00d2d38801929454110b41be88d133e3af232af7
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425748"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>HSM-védelemmel ellátott kulcsok importálása a Key Vaultba

Ha további garanciára van szüksége, amikor Azure Key Vault használ, a hardveres biztonsági modulokban (HSM) olyan kulcsokat importálhat vagy generálhat, amelyek soha nem hagyják el a HSM-határt. Ezt a forgatókönyvet gyakran a *saját kulcs*használatára vagy BYOK kell hivatkozni. A Azure Key Vault a HSM (FIPS 140-2 2. szint) nCipher nShield-családját használja a kulcsok elleni védelemhez.

Ez a funkció az Azure China 21Vianet esetében nem érhető el.

> [!NOTE]
> További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](key-vault-overview.md)  
> Az első lépéseket ismertető oktatóanyaghoz, amely magában foglalja a HSM-védelemmel ellátott kulcsok kulcstartójának létrehozását, lásd: [Mi az Azure Key Vault?](key-vault-overview.md)

## <a name="supported-hsms"></a>Támogatott HSM

A HSM-védelemmel ellátott kulcsok Key Vaultre való átvitele a használt HSM függően két különböző módszer használatával támogatott. Az alábbi táblázat segítségével meghatározhatja, hogy melyik módszert kell használni a HSM létrehozásához, majd átvinni a saját HSM-védelemmel ellátott kulcsait, hogy azok a Azure Key Vault használatával legyenek használatban. 

|HSM-szállító neve|Támogatott HSM-modellek|Támogatott HSM-Key átvitel módszer|
|---|---|---|
|Thales|<ul><li>SafeNet Luna HSM 7 termékcsalád a belső vezérlőprogram 7,3-es vagy újabb verziójával</li></ul>| [Új BYOK metódus használata (előzetes verzió)](hsm-protected-keys-vendor-agnostic-byok.md)|
|nCipher|<ul><li>HSM nShield családja</li></ul>|[Örökölt BYOK metódus használata](hsm-protected-keys-legacy.md)|


## <a name="next-steps"></a>Következő lépések

Kövesse [Key Vault ajánlott eljárásokat](key-vault-best-practices.md) a kulcsok biztonságának, tartósságának és figyelésének biztosításához.
