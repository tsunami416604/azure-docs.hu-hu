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
ms.openlocfilehash: a36e15a56a5a4c8a637120ca730ae1da764d376d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422899"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Gyakori hibakódok a Azure Key Vault

Az alábbi táblázatban felsorolt hibakódokat egy Azure Key Vault-művelet is visszaküldheti

| Hibakód | Felhasználói üzenet |
|--|--|
| VaultAlreadyExists |  A megadott névvel rendelkező új kulcstartó létrehozásának kísérlete meghiúsult, mert a név már használatban van. Ha nemrég törölt egy ilyen nevű kulcstartót, akkor lehet, hogy a törlésre kész állapotban van. [Itt](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault) ellenőrizheti, hogy létezik-e a Soft Deleted állapotban |
| VaultNameNotValid |  A tár nevének 24 karakterből kell állnia, alfanumerikusnak kell lennie, és betűvel kell kezdődnie |
| AccessDenied |  Előfordulhat, hogy a művelet végrehajtásához a hozzáférési házirendben hiányoznak engedélyek. |
| ForbiddenByFirewall |  Az ügyfél címe nincs engedélyezve, és a hívó nem megbízható szolgáltatás. |
| ConflictError |  Több műveletet is kér ugyanazon az elemen.  |
| RegionNotSupported |  Ez az erőforrás nem támogatja a megadott Azure-régiót. |
| SkuNotSupported |  A megadott SKU-típus nem támogatott ehhez az erőforráshoz. |
| ResourceNotFound |  A megadott Azure-erőforrás nem található. |
| CertificateExpired |  A tanúsítvány lejárati dátumának és érvényességi idejének megadásához. |


## <a name="next-steps"></a>További lépések

- Lásd: [Azure Key Vault fejlesztői útmutató](developers-guide.md)
- További információ a [Key Vault hitelesítéséről](authentication.md)
