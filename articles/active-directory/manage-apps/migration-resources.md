---
title: Alkalmazások Azure Active Directoryba való áttelepítéséhez szükséges erőforrások | Microsoft dokumentumok
description: Az alkalmazások elérésének és hitelesítésének az Azure Active Directoryba (Azure AD) történő áttelepítéséhez szükséges erőforrások.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: mimart
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: b30469858a5dd83f7f5f707f74466302b3000510
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968724"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Alkalmazások Azure Active Directoryba való áttelepítéséhez szükséges erőforrások

Az alkalmazások elérésének és hitelesítésének az Azure Active Directoryba (Azure AD) történő áttelepítéséhez szükséges erőforrások. Vegye ki ezthttps://aka.ms/AppsMigrationFeedback) a rövid felmérést ( visszajelzést adhat az alkalmazások Azure AD-be való áttelepítésének élményéről (beleértve az áttelepítésblokkolókat, az eszközök/ útmutatás okait, vagy a helyszíni IDP megőrzésének okait). 

| Erőforrás  | Leírás  |
|:-----------|:-------------|
|[Az alkalmazások áttelepítése az Azure AD-be](https://aka.ms/migrateapps/whitepaper) | Ez a tanulmány bemutatja a migráció előnyeit, és ismerteti, hogyan tervezhet áttelepítési négy világosan körvonalazott fázisban: felfedezés, besorolás, áttelepítés és folyamatos felügyelet. Végigvezeti, hogyan gondolkodhat a folyamatról, és hogyan bonthatja le a projektet könnyen fogyasztható darabokra. A dokumentum ban olyan fontos forrásokra mutató hivatkozások találhatók, amelyek segítenek az út során. |
|[Megoldásútmutató: Alkalmazások áttelepítése az Active Directory összevonási szolgáltatásokból (AD FS) az Azure AD-be](https://aka.ms/migrateapps/adfssolutionguide) | Ez a megoldásútmutató végigvezeti az áttelepítési tanulmány magasabb szinten leírt alkalmazásáttelepítési projekt tervezésének és végrehajtásának ugyanazon négy fázisán. Ebben az útmutatóban megtudhatja, hogyan alkalmazhatja ezeket a fázisokat arra a konkrét célra, hogy egy alkalmazást az Azure Directory-összevont szolgáltatásokból (AD FS) áthelyezzenek az Azure AD-be.|
| [Eszköz: Active Directory összevonási szolgáltatások áttelepítési készenléti parancsfájlja](https://aka.ms/migrateapps/adfstools) | Ez egy parancsfájl futtatható a helyszíni Active Directory összevonási szolgáltatások (AD FS) kiszolgálón, hogy meghatározza az alkalmazások készen áll az Azure AD-be való áttelepítéshez.|
| [Központi telepítési terv: Áttelepítés az AD FS-ről a jelszókivonat-szinkronizálásra](https://aka.ms/ADFSTOPHSDPDownload) | A jelszókivonat-szinkronizálással a rendszer szinkronizálja a felhasználói jelszavak kivonatait a helyszíni Active Directoryból az Azure AD-be. Ez lehetővé teszi, hogy az Azure AD hitelesítse a felhasználókat anélkül, hogy a helyszíni Active Directoryval kommunikálna.| 
| [Telepítési terv: Áttelepítés az AD FS-ről az átmenő hitelesítésre](https://aka.ms/ADFSTOPTADPDownload)|Az Azure AD átmenő hitelesítése segít a felhasználóknak, hogy ugyanazzal a jelszóval jelentkezzenek be a helyszíni és a felhőalapú alkalmazásokba. Ez a funkció jobb élményt nyújt a felhasználóknak, mivel eggyel kevesebb jelszóval rendelkeznek. Emellett csökkenti az informatikai ügyfélszolgálati költségeket, mivel a felhasználók kevésbé valószínű, hogy elfelejtik, hogyan kell bejelentkezni, ha csak egy jelszó megjegyzését kell megjegyezniük. Az Azure AD-vel való bejelentkezéskor a szolgáltatás közvetlenül a helyszíni Active Directoryban tárolt adatok alapján érvényesíti a felhasználói jelszavakat.|
| [Központi telepítési terv: Egyszeri bejelentkezés engedélyezése egy SaaS-alkalmazásba az Azure AD-vel](https://aka.ms/SSODPDownload) | Egyszeri bejelentkezés (SSO) segítségével hozzáférhet az összes alkalmazás és erőforrás, amire szüksége van az üzleti, miközben csak egyszer jelentkezik be, egyetlen felhasználói fiók használatával. Ha például egy felhasználó bejelentkezett, a felhasználó a Microsoft Office-ból a SalesForce-ba, a Box-ba léphet anélkül, hogy másodszor is hitelesítené (például beírja a jelszót). 
| [Központi telepítési terv: Alkalmazások kiterjesztése az Azure AD-re alkalmazásproxyval](https://aka.ms/AppProxyDPDownload)| Az alkalmazotti laptopok és egyéb eszközök helyszíni alkalmazásokhoz való hozzáférés biztosítása hagyományosan virtuális magánhálózatokat (VPN) vagy demilitarizált zónákat (DMZs) érintett. Ezeket a megoldásokat azonban nem csupán nagyon nehéz és komplex dolog védelemmel ellátni, hanem a beállításuk és az üzemeltetésük is nagyon költséges. Az Azure AD alkalmazásproxy megkönnyíti a helyszíni alkalmazások elérését. |
| [Üzembehelyezési tervek](../fundamentals/active-directory-deployment-plans.md) | További telepítési terveket találhat olyan funkciók üzembe helyezéséhez, mint a többtényezős hitelesítés, a feltételes hozzáférés, a felhasználói kiépítés, a zökkenőmentes egyszeri bejelentkezés, az önkiszolgáló jelszó-visszaállítás stb. |


