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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460554"
---
## <a name="protect-your-access-keys"></a>A hozzáférési kulcsok védetté való ellátása

A Storage-fiók hozzáférési kulcsa a Storage-fiókhoz tartozó legfelső szintű jelszóhoz hasonló. Mindig ügyeljen arra, hogy megvédje a hozzáférési kulcsait. A kulcsok biztonságos kezelése és elforgatása Azure Key Vault használatával. Kerülje a hozzáférési kulcsok más felhasználók számára történő terjesztését, a merevlemezek kódolását, vagy a mások számára elérhető egyszerű szövegben való mentését. Ha úgy gondolja, hogy a kulcsok biztonsága megsérült, akkor forgassa el a kulcsokat.

Ha lehetséges, a Azure Active Directory (Azure AD) használatával engedélyezze a megosztott kulcs helyett a blob-és üzenetsor-tárolásra vonatkozó kérelmeket. Az Azure AD kiváló biztonságot és könnyű használatot biztosít a megosztott kulcson keresztül. További információ az adatok Azure AD-vel való hozzáférésének engedélyezéséről: az [Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](../articles/storage/common/storage-auth-aad.md).
