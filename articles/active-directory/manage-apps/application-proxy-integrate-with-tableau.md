---
title: Azure Active Directory alkalmazásproxy és tabló | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az Azure Active Directory (Azure AD) alkalmazásproxy ttávoli hozzáférést a Tableau üzembe helyezéséhez.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1aa99e7e71ad78a62c1a9da303b2ecc8347ebeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65783846"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory alkalmazásproxy és tabló 

Az Azure Active Directory alkalmazásproxy és a Tableau partneri kapcsolatban áll, hogy könnyen használhassa az Alkalmazásproxyt a Tableau központi telepítéséhez. Ez a cikk bemutatja, hogyan konfigurálható ez a forgatókönyv.  

## <a name="prerequisites"></a>Előfeltételek 

A cikkben szereplő forgatókönyv feltételezi, hogy a következőket feltételezi:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) konfigurálva. 

- [Alkalmazásproxy-összekötő](application-proxy-add-on-premises-application.md) telepítve. 

 
## <a name="enabling-application-proxy-for-tableau"></a>Alkalmazásproxy engedélyezése a Tablóhoz 

Az alkalmazásproxy támogatja az OAuth 2.0 támogatási folyamatot, amely a Tableau megfelelő működéséhez szükséges. Ez azt jelenti, hogy az alkalmazás engedélyezéséhez már nincs szükség speciális lépésekre, kivéve az alábbi közzétételi lépések végrehajtásával történő konfigurálását.


## <a name="publish-your-applications-in-azure"></a>Az alkalmazások közzététele az Azure-ban 

A Tableau közzétételéhez közzé kell tennie egy alkalmazást az Azure Portalon.

A következők esetében:

- Az 1–8. [Publish applications using Azure AD Application Proxy](application-proxy-add-on-premises-application.md) 
- Az Alkalmazásproxy mezők Tabló értékeinek megkereséséről a Tableau dokumentációjában olvashat.  

**Az alkalmazás közzététele:** 


1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) alkalmazás-rendszergazdaként. 

2. Válassza az **Azure Active Directory > Enterprise alkalmazások lehetőséget.** 

3. Válassza a **Hozzáadás** lehetőséget a fűrészlap tetején. 

4. Válassza **a Helyszíni alkalmazás lehetőséget.** 

5. Töltse ki a szükséges mezőket az új alkalmazással kapcsolatos információkkal. A beállításokhoz az alábbi útmutatást kell használni: 

    - **Belső URL:** Ennek az alkalmazásnak rendelkeznie kell egy belső URL-címmel, amely maga a Tableau URL.Internal URL : This application should have a internal URL that is the Tableau URL. Például: `https://adventure-works.tableau.com`. 

    - **Előhitelesítési módszer**: Azure Active Directory (ajánlott, de nem szükséges). 

6. Válassza a **Hozzáadás** lehetőséget a fűrészlap tetején. Az alkalmazás hozzáadódik, és megnyílik a gyorsindítás menü. 

7. A gyorsindítás menüben válassza a **Felhasználó hozzárendelése tesztelésre**lehetőséget, és adjon hozzá legalább egy felhasználót az alkalmazáshoz. Győződjön meg arról, hogy a tesztfiók rendelkezik-e hozzáféréssel a helyszíni alkalmazáshoz. 

8. A Teszt felhasználói hozzárendelés mentéséhez válassza a **Hozzárendelés** lehetőséget. 

9. (Nem kötelező) Az alkalmazáskezelés lapon válassza az **Egyszeri bejelentkezés**lehetőséget. Válassza a legördülő menü **Integrált Windows-hitelesítés parancsát,** és töltse ki a szükséges mezőket a Tableau konfigurációja alapján. Kattintson a **Mentés** gombra. 

 

## <a name="testing"></a>Tesztelés 

Az alkalmazás készen áll a tesztelésre. Hozzáférés a Tabló közzétételéhez használt külső URL-címhez, és jelentkezzen be a két alkalmazáshoz rendelt felhasználóként.



## <a name="next-steps"></a>További lépések

Az Azure AD alkalmazásproxyról a [Helyszíni alkalmazások biztonságos távoli hozzáférésének biztosítása](application-proxy.md)című témakörben talál további információt.

