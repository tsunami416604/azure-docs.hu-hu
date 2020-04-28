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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76268232"
---
A `objectIdType` (vagy az **objektumazonosító típusa**) a szerepkörhöz megadott identitás típusát jelöli. A és `UserDefinedFunctionId` a `DeviceId` típustól eltekintve az objektumazonosítók típusai Azure Active Directory objektumok tulajdonságaira vonatkoznak.

A következő táblázat a támogatott objektumazonosító-típusokat tartalmazza az Azure Digital Ikrekben:

| Típus | Leírás |
| --- | --- |
| UserId (Felhasználóazonosító) | Szerepkört rendel egy felhasználóhoz. |
| DeviceId | Szerepkört rendel egy eszközhöz. |
| DomainName | Szerepkört rendel egy tartománynévhez. A megadott tartománynévvel rendelkező felhasználók hozzáférési jogosultsággal rendelkeznek a megfelelő szerepkörhöz. |
| TenantId | Szerepkört rendel a bérlőhöz. Minden olyan felhasználó, aki a megadott Azure AD-bérlői AZONOSÍTÓhoz tartozik, hozzáférési jogosultságokkal rendelkezik a megfelelő szerepkörhöz. |
| ServicePrincipalId | Szerepkört rendel egy egyszerű szolgáltatásnév-objektumhoz. |
| UserDefinedFunctionId | Szerepkört rendel egy felhasználó által definiált függvényhez (UDF). |