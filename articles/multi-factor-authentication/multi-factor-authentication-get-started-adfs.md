---
title: "Kétlépcsős ellenőrzés és az AD FS – Azure MFA | Microsoft Docs"
description: "Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és az AD FS használatát."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 44fbba68-6cf9-46c1-a9df-736580b68ae3
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/23/2017
ms.author: kgremban
ms.translationtype: HT
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 28aede545c738137ff04257214e4a3f42792d85c
ms.contentlocale: hu-hu
ms.lasthandoff: 07/12/2017

---
<a id="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services" class="xliff"></a>

# Bevezetés az Azure Multi-Factor Authentication és az Active Directory összevonási szolgáltatások használatába
<center>![Felhő](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Ha a szervezete az AD FS használatával vonta össze a helyszíni Active Directoryt az Azure Active Directoryval, az Azure Multi-Factor Authentication két módon használható .

* A felhőerőforrások védelme az Azure Multi-Factor Authentication vagy az Active Directory összevonási szolgáltatások használatával
* A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló használatával

A következő táblázat foglalja össze az ellenőrzésbeli különbségeket az erőforrásoknak az Azure Multi-Factor Authentication használatával, illetve az AD FS használatával való védelme esetén

| Ellenőrzés – Böngészőalapú alkalmazások | Ellenőrzés – Nem böngészőalapú alkalmazások |
|:--- |:--- |:--- |
| Az Azure AD-erőforrások védelme az Azure Multi-Factor Authentication használatával |<li>Az ellenőrzés első lépése a helyszínen történik az AD FS használatával.</li> <li>A második lépés felhőalapú hitelesítést használó telefonalapú módszer.</li> |
| Az Azure AD-erőforrások védelme az Active Directory összevonási szolgáltatásokkal |<li>Az ellenőrzés első lépése a helyszínen történik az AD FS használatával.</li><li>A második lépés a helyszínen történik a jogcím betartásával.</li> |

Összevont felhasználók alkalmazásjelszavaival kapcsolatos figyelmeztetések:

* Az alkalmazásjelszavak ellenőrzése felhőalapú hitelesítéssel történik, így mellőzik az összevonásokat. Az összevonás csak alkalmazásjelszó beállításakor van aktív használatban.
* Az alkalmazásjelszavak nem tartják be a helyszíni ügyfél hozzáférés-vezérlési beállításait.
* Az alkalmazásjelszavak használata esetén nem érhető el a helyszíni hitelesítésnaplózás.
* A fiókok letiltása/törlése akár 3 óráig is eltarthat a címtár-szinkronizálással, ami késlelteti az alkalmazásjelszó letiltását/törlését a felhőalapú identitásban.

<a id="next-steps" class="xliff"></a>

## Következő lépések
Az Azure Multi-Factor Authentication vagy az Azure Multi-Factor Authentication-kiszolgáló AD FS-sel való beállításával kapcsolatosan további információt a következő cikkekben talál:

* [A felhőerőforrások védelme az Azure Multi-Factor Authentication és az AD FS használatával](multi-factor-authentication-get-started-adfs-cloud.md)
* [A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és a Windows Server 2012 R2 AD FS használatával](multi-factor-authentication-get-started-adfs-w2k12.md)
* [A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és az AD FS 2.0-s verziójának használatával](multi-factor-authentication-get-started-adfs-adfs2.md)

