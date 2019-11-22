---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/20/2019
ms.custom: include file
ms.openlocfilehash: e46041a33c12b3fcb40e5a04de11108471e59855
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307261"
---
A `objectIdType` (vagy **objektumazonosító típusa**) a szerepkörhöz megadott identitás típusát jelöli. A `DeviceId` és `UserDefinedFunctionId` típusokon kívül az objektumazonosítók típusai Azure Active Directory objektumok tulajdonságaihoz tartoznak.

A következő táblázat a támogatott objektumazonosító-típusokat tartalmazza az Azure Digital Ikrekben:

| Típus | Leírás |
| --- | --- |
| UserId | Szerepkört rendel egy felhasználóhoz. |
| DeviceId | Szerepkört rendel egy eszközhöz. |
| DomainName | Szerepkört rendel egy tartománynévhez. A megadott tartománynévvel rendelkező felhasználók hozzáférési jogosultsággal rendelkeznek a megfelelő szerepkörhöz. |
| TenantId | Szerepkört rendel a bérlőhöz. Minden olyan felhasználó, aki a megadott Azure AD-bérlői AZONOSÍTÓhoz tartozik, hozzáférési jogosultságokkal rendelkezik a megfelelő szerepkörhöz. |
| ServicePrincipalId | Szerepkört rendel egy egyszerű szolgáltatásnév-objektumhoz. |
| UserDefinedFunctionId | Szerepkört rendel egy felhasználó által definiált függvényhez (UDF). |