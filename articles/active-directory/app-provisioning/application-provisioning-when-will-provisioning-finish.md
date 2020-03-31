---
title: Az Azure AD Gallery alkalmazásba való felhasználói kiépítés órákat vagy többet vesz igénybe
description: Hogyan derítheti ki, hogy miért tarthat tovább az alkalmazás kiépítése a vártnál?
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22d19733fbeb162fba55dd732c10e552879c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522645"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Az Azure AD Gallery-alkalmazásba való felhasználói kiépítés órákat vagy többet vesz igénybe

Amikor először engedélyezi az automatikus kiépítés egy alkalmazás, a kezdeti ciklus is eltarthat bárhol 20 perctől több óráig, méretétől függően az Azure AD-címtár és a felhasználók száma a kiépítés hatókörében. 

A kezdeti ciklus utáni későbbi szinkronizálások gyorsabbak lesznek, mivel a létesítési szolgáltatás olyan vízjeleket tárol, amelyek a kezdeti ciklus után mindkét rendszer állapotát jelölik, javítva a későbbi szinkronizálások teljesítményét.

## <a name="how-to-improve-provisioning-performance"></a>A kiépítési teljesítmény javítása

Ha a kezdeti ciklus több mint néhány órát vesz igénybe, van egy dolog, amit tehetünk, hogy javítsa a teljesítményt:

-   **Felhasználói hatókörszűrők.** A hatókörszűrők lehetővé teszik, hogy finomhangolja azokat az adatokat, amelyeket a létesítési szolgáltatás kibont az Azure AD-ből, adott attribútumértékek alapján kiszűrve a felhasználókat. A szűrők hatókörének kezeléséről további információt az [Attribútumalapú alkalmazáskiépítés hatókörszűrőkkel című témakörben talál.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

## <a name="next-steps"></a>További lépések
[A felhasználói kiépítés és a SaaS-alkalmazásokba való megszüntetés automatizálása az Azure Active Directoryval](user-provisioning.md)

