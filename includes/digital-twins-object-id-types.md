---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 94baeb3d459b700cc95d88fb82f995957640aab6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012159"
---
A `objectIdType` (vagy az **objektumazonosító típusa**) a szerepkörhöz megadott identitás típusát jelöli. A és `DeviceId` `UserDefinedFunctionId` a típustól eltekintve az objektumazonosítók típusai Azure Active Directory objektumok tulajdonságaira vonatkoznak.

A következő táblázat a támogatott objektumazonosító-típusokat tartalmazza az Azure Digital Ikrekben:

| Type | Leírás |
| --- | --- |
| UserId | Szerepkört rendel egy felhasználóhoz. |
| DeviceId | Szerepkört rendel egy eszközhöz. |
| DomainName | Szerepkört rendel egy tartománynévhez. A megadott tartománynévvel rendelkező felhasználók hozzáférési jogosultsággal rendelkeznek a megfelelő szerepkörhöz. |
| TenantId | Szerepkört rendel a bérlőhöz. Minden olyan felhasználó, aki a megadott Azure AD-bérlői AZONOSÍTÓhoz tartozik, hozzáférési jogosultságokkal rendelkezik a megfelelő szerepkörhöz. |
| ServicePrincipalId | Szerepkört rendel egy egyszerű szolgáltatásnév-objektumhoz. |
| UserDefinedFunctionId | Szerepkört rendel egy felhasználó által definiált függvényhez (UDF). |