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
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53795885"
---
A `objectIdType` (vagy **objektumtípus azonosítója**), amelyek szerepet kapnak identitást típusára utal. Kivéve a `DeviceId` és `UserDefinedFunctionId` típusai, objektumtípusok azonosító megfelelnek az Azure Active Directory-objektumok tulajdonságait.

Az alábbi táblázat tartalmazza a támogatott azonosító különböző az Azure digitális Twins:

| Típus | Leírás |
| --- | --- |
| Felhasználói azonosító | Egy szerepkört rendel egy felhasználói. |
| Eszközazonosító | Egy szerepkört rendel egy eszközt. |
| Tartománynév | Egy szerepkört rendel egy tartomány nevét. Minden felhasználónak, a megadott tartomány nevét a hozzáférési jogosultságokat a megfelelő szerepkör van. |
| TenantId | Egy szerepkört rendel egy bérlőt. Minden felhasználónak, aki tagja a megadott Azure AD-bérlő azonosítója a hozzáférési jogosultságokat a megfelelő szerepkör van. |
| servicePrincipalId | Egy szerepkört rendel egy szolgáltatásnév-objektum-azonosítót. |
| UserDefinedFunctionId | Egy szerepkört rendel egy felhasználói függvény (UDF). |