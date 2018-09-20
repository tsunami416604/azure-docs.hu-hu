---
title: Erőforrások áttelepítése az Azure Active Directory-alkalmazások |} A Microsoft Docs
description: Forrásanyagok az Azure Active Directory (Azure AD-) alkalmazás-hozzáférés és a hitelesítési át.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2018
ms.author: barbkess
ms.reviewer: baselden
ms.openlocfilehash: 750bf1905a3ca352e181dcd5b7fcecdfc8d04f76
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465503"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Erőforrások áttelepítése az Azure Active Directory-alkalmazások

Forrásanyagok az Azure Active Directory (Azure AD-) alkalmazás-hozzáférés és a hitelesítési át. 

| Erőforrás  | Leírás  |
|:-----------|:-------------|
|[Az alkalmazások az Azure AD-be való migrálása](https://aka.ms/migrateapps/whitepaper) | Ez a tanulmány bemutatja a migrálás előnyeit, és ismerteti, hogyan lehet egyértelműen vázolt tételéhez négy fázisban áttelepítés tervezése: felderítés, besorolás, áttelepítési és folyamatos felügyeletét. Gondolja át a folyamat és a projekt könnyen feldolgozható darabokra felosztania rendszer végigvezeti. A dokumentum segítséget nyújt a vizualizáción fontos erőforrásokra mutató hivatkozásokat is. |
|[Megoldási útmutató: az Active Directory összevonási szolgáltatások (AD FS) áttelepítése alkalmazásokat az Azure ad-hez](https://aka.ms/migrateapps/adfssolutionguide) | Ez a megoldási útmutató végigvezeti tervezési és a egy magasabb szintű az áttelepítés tanulmányban leírtak áttelepítési projektet végrehajtása azonos négy fázisa. Ebben az útmutatóban megismerheti, hogyan a alkalmazni ezeket a fázisokat adott célja az alkalmazások áthelyezése az Azure Directory összevont szolgáltatások (AD FS) az Azure AD fog.|
| [Eszköz: Az Active Directory összevonási szolgáltatások áttelepítési készültségi parancsfájl](https://aka.ms/migrateapps/adfstools) | Ez a parancsfájl futtatása meghatározni a migrálás az Azure AD-alkalmazások készen áll-e a helyszíni Active Directory összevonási szolgáltatások (AD FS) kiszolgálón.|
| [Telepítési terv: áttelepítés az AD FS-ről Jelszókivonat-szinkronizálás](https://aka.ms/ADFSTOPHSDPDownload) | A Jelszókivonat-szinkronizálás a felhasználói jelszavak kivonatait vannak szinkronizálva a helyszíni Active Directoryból az Azure AD. Ez lehetővé teszi az Azure AD-felhasználók hitelesítése anélkül, hogy a helyszíni Active Directoryval való interakcióhoz.| 
| [Telepítési terv: áttelepítés az AD FS-ről átmenő hitelesítéshez](https://aka.ms/ADFSTOPTADPDownload)|Az Azure AD átmenő hitelesítésének segít a felhasználóknak, jelentkezzen be a helyszíni és felhőalapú alkalmazások is ugyanazt a jelszót. Ez a szolgáltatás a felhasználók a jobb felhasználói élményt biztosít, mivel kevesebb ne feledje, hogy egyetlen jelszóval rendelkeznek. Mivel a felhasználók kevésbé valószínű, hogy hogyan jelentkezhet be, amikor csak szükségük van egy jelszó megjegyzése elfelejtette is kevesebb informatikai ügyfélszolgálati kiadásokat. Az Azure AD-vel való bejelentkezéskor a szolgáltatás közvetlenül a helyszíni Active Directoryban tárolt adatok alapján érvényesíti a felhasználói jelszavakat.|
| [Telepítési terv: engedélyezi az egyszeri bejelentkezés SaaS-alkalmazásokhoz az Azure AD-vel](https://aka.ms/SSODPDownload) | Egyszeri bejelentkezés (SSO) segítségével fér hozzá az alkalmazások és erőforrások kell tennie az üzleti, miközben csak egyszer, egyetlen felhasználói fiókkal jelentkezik be. Például egy felhasználó jelentkezett be, miután a felhasználó áthelyezheti a Microsoft Office, SalesForce, Box másodszor (például írja be a jelszót) hitelesítése nélkül. 
| [Telepítési terv: alkalmazások kiterjesztése az Azure AD-alkalmazásproxyval](https://aka.ms/AppProxyDPDownload)| Az alkalmazott laptopok hozzáférést és egyéb eszközöket a helyszíni alkalmazások hagyományosan van szó, virtuális magánhálózatok (VPN) vagy demilitarizált zóna (DMZ). Ezeket a megoldásokat azonban nem csupán nagyon nehéz és komplex dolog védelemmel ellátni, hanem a beállításuk és az üzemeltetésük is nagyon költséges. Az Azure AD-alkalmazásproxy megkönnyíti a helyszíni alkalmazásokat. |
| [Központi telepítési csomagok](../fundamentals/active-directory-deployment-plans.md) | Több központi telepítési csomagok központi telepítése a multi-factor authentication, feltételes hozzáférés, felhasználókiépítés, zökkenőmentes egyszeri bejelentkezési, önkiszolgáló jelszó-visszaállítás, és más funkciókat keresése! |


