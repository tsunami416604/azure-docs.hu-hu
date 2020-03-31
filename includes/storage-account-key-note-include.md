---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460554"
---
## <a name="protect-your-access-keys"></a>A hozzáférési kulcsok védelme

A tárfiók hozzáférési kulcsai hasonlóak a tárfiók gyökérjelszavához. Mindig legyen óvatos a hozzáférési kulcsok védelmével. Az Azure Key Vault használatával biztonságosan kezelheti és forgathatja a kulcsokat. Ne ossza meg a hozzáférési kulcsokat más felhasználók számára, ne kódolja őket, és ne mentse őket bárhová olyan egyszerű szövegben, amely mások számára elérhető. Forgassa el a billentyűit, ha úgy gondolja, hogy biztonságban voltak.

Ha lehetséges, használja az Azure Active Directory (Azure AD) a kérelmek engedélyezése blob és várólista-tároló helyett megosztott kulcs. Az Azure AD kiváló biztonságot és egyszerű használatot biztosít a megosztott kulcson keresztül. Az Azure AD-vel való hozzáférés engedélyezéséről az [Azure-blobokhoz és várólistákhoz való hozzáférés engedélyezése](../articles/storage/common/storage-auth-aad.md)az Azure Active Directory használatával című témakörben talál további információt.
