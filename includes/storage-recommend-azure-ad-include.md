---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 825a9f5668cc80f1306d74623db2ea54614ba4a3
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985388"
---
> [!TIP]
> Az Azure Storage a Azure Active Directory (Azure AD) használatával támogatja a blob-és üzenetsor-tárolásra vonatkozó kérések engedélyezését. Az Azure AD által visszaadott OAuth 2,0 tokent használó felhasználók vagy alkalmazások engedélyezése a jobb biztonságot és a könnyű használatot teszi lehetővé a megosztott kulcsos hitelesítésnél. Az Azure AD-ben nem szükséges a fiókhoz tartozó hozzáférési kulcs tárolása a kóddal és a potenciális biztonsági rések kockázatával.
>
> Az Azure Storage Emellett támogatja a felhasználói delegálás megosztott hozzáférésének aláírását (SAS) a blob Storage-hoz. A felhasználói delegálás SAS-je Azure AD-beli hitelesítő adatokkal van aláírva. Ha az alkalmazás kialakításához közös hozzáférésű aláírásokra van szükség a blob Storage-hoz való hozzáféréshez, az Azure AD-beli hitelesítő adatok használatával hozzon létre egy felhasználói delegálási SAS-t a felettes
>
> A Microsoft azt javasolja, hogy ha lehetséges, az Azure AD-t használja az Azure Storage-alkalmazásokkal. További információ: az [Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](../articles/storage/common/storage-auth-aad.md).
