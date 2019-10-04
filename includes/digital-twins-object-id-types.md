---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 12d4278171d43fdaf8613a1c91bdbffc269adc56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949054"
---
A `objectIdType` (vagy **objektumazonosító típusa**) a szerepkörhöz megadott identitás típusát jelöli. A `DeviceId` és a `UserDefinedFunctionId` típusokon kívül az objektumazonosítók típusai Azure Active Directory objektumok tulajdonságaihoz tartoznak.

A következő táblázat a támogatott objektumazonosító-típusokat tartalmazza az Azure Digital Ikrekben:

| Type | Leírás |
| --- | --- |
| UserId | Szerepkört rendel egy felhasználóhoz. |
| DeviceId | Szerepkört rendel egy eszközhöz. |
| DomainName | Szerepkört rendel egy tartománynévhez. A megadott tartománynévvel rendelkező felhasználók hozzáférési jogosultsággal rendelkeznek a megfelelő szerepkörhöz. |
| TenantId | Szerepkört rendel a bérlőhöz. Minden olyan felhasználó, aki a megadott Azure AD-bérlői AZONOSÍTÓhoz tartozik, hozzáférési jogosultságokkal rendelkezik a megfelelő szerepkörhöz. |
| ServicePrincipalId | Szerepkört rendel egy egyszerű szolgáltatásnév-objektumhoz. |
| UserDefinedFunctionId | Szerepkört rendel egy felhasználó által definiált függvényhez (UDF). |