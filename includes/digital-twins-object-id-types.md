---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 40ab53c941a7ac619ebb09d381a4ae0450f26e8b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179752"
---
A `objectIdType` (vagy **objektumtípus azonosítója**), amelyek szerepet kapnak identitást típusára utal. Kivéve a `DeviceId` és `UserDefinedFunctionId` típusai, objektumtípusok azonosító megfelelnek az Azure Active Directory-objektumok tulajdonságait.

Az alábbi táblázat tartalmazza a támogatott azonosító különböző az Azure digitális Twins:

| Típus | Leírás |
| --- | --- |
| Felhasználói azonosító | Egy szerepkört rendel egy felhasználói. |
| DeviceId | Egy szerepkört rendel egy eszközt. |
| Tartománynév | Egy szerepkört rendel egy tartomány nevét. Minden felhasználónak, a megadott tartomány nevét a hozzáférési jogosultságokat a megfelelő szerepkör van. |
| TenantId | Egy szerepkört rendel egy bérlőt. Minden felhasználónak, aki tagja a megadott Azure AD-bérlő azonosítója a hozzáférési jogosultságokat a megfelelő szerepkör van. |
| ServicePrincipalId | Egy szerepkört rendel egy szolgáltatásnév-objektum-azonosítót. |
| UserDefinedFunctionId | Egy szerepkört rendel egy felhasználói függvény (UDF). |