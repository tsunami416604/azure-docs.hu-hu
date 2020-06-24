---
title: Az alkalmazások Azure Active Directoryba való áttelepítéséhez szükséges erőforrások | Microsoft Docs
description: Az alkalmazások hozzáférésének és hitelesítésének Azure Active Directory (Azure AD) szolgáltatásba való áttelepíteni kívánt erőforrások.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fe21b6509080e88c63d9c02c8116f3882fb64dd
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763108"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Az alkalmazások Azure Active Directoryba való áttelepítéséhez szükséges erőforrások

Az alkalmazások hozzáférésének és hitelesítésének Azure Active Directory (Azure AD) szolgáltatásba való áttelepíteni kívánt erőforrások. Végezze el ezt a rövid kérdőívet ( https://aka.ms/AppsMigrationFeedback) hogy visszajelzést kapjon az alkalmazások Azure ad-be történő áttelepítésével kapcsolatban (beleértve az áttelepítésre szolgáló blokkolók, az eszközök és a útmutatás szükségességét, vagy a helyszíni identitásszolgáltató megőrzésének okait). 

| Erőforrás  | Leírás  |
|:-----------|:-------------|
|[Alkalmazások áttelepítése az Azure AD-be](https://aka.ms/migrateapps/whitepaper) | Ez a tanulmány az áttelepítés előnyeit mutatja be, és leírja, hogyan tervezze meg az áttelepítést négy jól tagolt fázisban: felderítés, besorolás, áttelepítés és folyamatos felügyelet. Útmutatást kaphat arról, hogyan gondolhatja át a folyamatot, és hogyan bontják le a projektet könnyen felhasználható darabokra. A dokumentum teljes egészében olyan fontos erőforrásokra mutató hivatkozásokat talál, amelyek segítenek az Ön számára. |
|[Megoldási útmutató: alkalmazások áttelepítése Active Directory összevonási szolgáltatások (AD FS)ról (AD FS) az Azure AD-be](https://aka.ms/migrateapps/adfssolutionguide) | Ez a megoldási útmutató végigvezeti az áttelepítési tanulmány magasabb szintjén ismertetett alkalmazás-áttelepítési projekt tervezésének és végrehajtásának négy fázisán. Ebből az útmutatóból megtudhatja, hogyan alkalmazhatja ezeket a fázisokat arra a célra, amely az alkalmazások Azure Directory összevont szolgáltatásokból (AD FS) az Azure AD-ba való áthelyezésének konkrét céljára vonatkozik.|
| [Eszköz: Active Directory összevonási szolgáltatások (AD FS) Migration Readiness script](https://aka.ms/migrateapps/adfstools) | Ez egy parancsfájl, amely a helyszíni Active Directory összevonási szolgáltatások (AD FS) (AD FS) kiszolgálón futtatható az Azure AD-ba való áttelepítéshez használható alkalmazások készültségének meghatározásához.|
| [Központi telepítési terv: áttelepítés AD FSról jelszó-kivonatolási szinkronizálásra](https://aka.ms/ADFSTOPHSDPDownload) | A jelszó-kivonatolási szinkronizálással a felhasználói jelszavak kivonatait a helyszíni Active Directoryról az Azure AD-be szinkronizálja a rendszer. Ez lehetővé teszi, hogy az Azure AD hitelesítse a felhasználókat a helyszíni Active Directory való interakció nélkül.| 
| [Központi telepítési terv: áttelepítés AD FSról átmenő hitelesítésre](https://aka.ms/ADFSTOPTADPDownload)|Az Azure AD átmenő hitelesítés használatával a felhasználók ugyanazzal a jelszóval jelentkezhetnek be a helyszíni és a felhőalapú alkalmazásokba. Ez a funkció jobb felhasználói élményt nyújt a felhasználóknak, hiszen kevesebb jelszót kell megjegyeznünk. Emellett csökkenti az informatikai támogatási szolgálat költségeit, mivel a felhasználók kevésbé valószínű, hogy hogyan jelentkezhetnek be, amikor csak egy jelszót kell megjegyezni. Az Azure AD-vel való bejelentkezéskor a szolgáltatás közvetlenül a helyszíni Active Directoryban tárolt adatok alapján érvényesíti a felhasználói jelszavakat.|
| [Üzembe helyezési terv: egyszeri bejelentkezés engedélyezése SaaS-alkalmazásba az Azure AD-vel](https://aka.ms/SSODPDownload) | Az egyszeri bejelentkezés (SSO) segítséget nyújt az összes olyan alkalmazás és erőforrás eléréséhez, amelyet üzleti tevékenységhez szükséges, és csak egyszer kell bejelentkeznie egyetlen felhasználói fiók használatával. Ha például egy felhasználó bejelentkezett, a felhasználó áttérhet a Microsoft Officeról a SalesForce-be a hitelesítés nélkül (például egy jelszó beírása). 
| [Üzembe helyezési terv: alkalmazások kiterjesztése az Azure AD-be alkalmazásproxy használatával](https://aka.ms/AppProxyDPDownload)| Az alkalmazottaktól származó laptopok és más eszközök hozzáférésének biztosítása a helyszíni alkalmazásokhoz hagyományosan a virtuális magánhálózatok (VPN) vagy a vagy demilitarizált zónák (DMZ-EK) használatával érhető el. Ezeket a megoldásokat azonban nem csupán nagyon nehéz és komplex dolog védelemmel ellátni, hanem a beállításuk és az üzemeltetésük is nagyon költséges. Az Azure AD Application Proxy megkönnyíti a helyszíni alkalmazások elérését. |
| [Üzembe helyezési tervek](../fundamentals/active-directory-deployment-plans.md) | További központi telepítési terveket találhat olyan funkciók üzembe helyezéséhez, mint a többtényezős hitelesítés, a feltételes hozzáférés, a felhasználók kiépítése, a zökkenőmentes egyszeri bejelentkezés, az önkiszolgáló jelszó-visszaállítás és egyebek. |


