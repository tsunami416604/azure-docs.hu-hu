---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 1f4cdf45fb643499f7d6ab717c6f36395ad292c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827710"
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