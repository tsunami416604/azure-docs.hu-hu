---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: cb43c8b8c952d8db6cf450a7015c22c85e7fe4b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76268232"
---
A `objectIdType` (vagy **objektumazonosító típusa)** a szerepkörhöz adott identitás típusára utal. A és `UserDefinedFunctionId` `DeviceId` a típusokon kívül az objektumazonosító-típusok az Azure Active Directory-objektumok tulajdonságainak felelnek meg.

Az alábbi táblázat az Azure Digital Twins támogatott objektumazonosító-típusait tartalmazza:

| Típus | Leírás |
| --- | --- |
| UserId (Felhasználóazonosító) | Szerepkört rendel egy felhasználóhoz. |
| DeviceId | Szerepkört rendel egy eszközhöz. |
| DomainName | Szerepkört rendel egy tartománynévhez. Minden megadott tartománynévvel rendelkező felhasználó rendelkezik a megfelelő szerepkör hozzáférési jogosultságaival. |
| TenantId | Szerepkörhozzárendelése a bérlőhöz. Minden felhasználó, aki a megadott Azure AD-bérlőazonosítóhoz tartozik, rendelkezik a megfelelő szerepkör hozzáférési jogokkal. |
| ServicePrincipalId | Szerepkörhozzárendelés egy egyszerű szolgáltatásobjektum-azonosítóhoz. |
| UserDefinedFunctionId azonosító | Szerepkörhozzárendelés egy felhasználó által definiált függvényhez (UDF). |