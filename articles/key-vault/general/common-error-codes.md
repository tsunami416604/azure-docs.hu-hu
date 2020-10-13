---
title: Gyakori hibakódok a Azure Key Vaulthoz | Microsoft Docs
description: Gyakori hibakódok a Azure Key Vault
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: 25c79229a09db912903fba825f0d48f571880745
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876936"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Gyakori hibakódok a Azure Key Vault

Az alábbi táblázatban felsorolt hibakódokat egy Azure Key Vault-művelet is visszaküldheti

| Hibakód | Felhasználói üzenet |
|--|--|
| VaultAlreadyExists |  A megadott kulcstartó már létezik (helyreállított állapotban vagy egy másik előfizetésben). |
| VaultNameNotValid |  A tár nevének 24 karakterből kell állnia, alfanumerikusnak kell lennie, és betűvel kell kezdődnie |
| AccessDenied |  Előfordulhat, hogy a művelet végrehajtásához a hozzáférési házirendben hiányoznak engedélyek. |
| ForbiddenByFirewall |  Az ügyfél címe nincs engedélyezve, és a hívó nem megbízható szolgáltatás. |
| ConflictError |  Több műveletet is kér ugyanazon az elemen.  |
| RegionNotSupported |  Ez az erőforrás nem támogatja a megadott Azure-régiót. |
| SkuNotSupported |  A megadott SKU-típus nem támogatott ehhez az erőforráshoz. |
| ResourceNotFound |  A megadott Azure-erőforrás nem található. |
| CertificateExpired |  A tanúsítvány lejárati dátumának és érvényességi idejének megadásához. |


## <a name="next-steps"></a>Következő lépések

- Lásd: [Azure Key Vault fejlesztői útmutató](developers-guide.md)
- További információ a [Key Vault hitelesítéséről](authentication.md)
