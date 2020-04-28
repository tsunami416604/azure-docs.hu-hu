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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77471525"
---
Ha azt szeretné, hogy a felhasználók a hozzáférés megadását megelőzően egy második hitelesítési tényezőt kérjenek, konfigurálhatja az Azure Multi-Factor Authentication (MFA) szolgáltatását. Az MFA-t felhasználónként is konfigurálhatja, vagy az MFA-t [feltételes hozzáférés](../articles/active-directory/conditional-access/overview.md)használatával is kihasználhatja.

* Az MFA felhasználónként engedélyezhető díjmentesen. Ha felhasználónként engedélyezi az MFA-t, a rendszer a felhasználót az Azure AD-bérlőhöz kötött összes alkalmazás esetében kéri a második tényező hitelesítésére. Lásd: [1. lehetőség](#peruser) a lépésekhez.
* A feltételes hozzáférés lehetővé teszi, hogy finomabban szabályozható legyen a második tényező előléptetése. Lehetővé teszi az MFA hozzárendelését a VPN-hez, és kizár más, az Azure AD-bérlőhöz kötött alkalmazásokat. A lépésekért lásd a [2. lehetőséget](#conditional) .