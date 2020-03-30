---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471525"
---
Ha azt szeretné, hogy a felhasználók a hozzáférés megadása előtt egy második hitelesítési tényezőt kérjenek, konfigurálhatja az Azure többtényezős hitelesítést (MFA). Az MFA-t beállíthatja felhasználónként, vagy használhatja az MFA-t [feltételes hozzáféréssel.](../articles/active-directory/conditional-access/overview.md)

* A felhasználónkénti mfa további költségek nélkül engedélyezhető. A felhasználónkénti MFA engedélyezésekor a felhasználó második tényezős hitelesítést kér az Azure AD-bérlőhöz kötött összes alkalmazással szemben. A lépéseket lásd az [1.](#peruser)
* A feltételes hozzáférés lehetővé teszi a második tényező előléptetésének finomabb szabályozását. Lehetővé teheti az MFA hozzárendelését csak VPN-hez, és kizárhatja az Azure AD-bérlőhöz kötött egyéb alkalmazásokat. A lépéseket lásd a [2.](#conditional)