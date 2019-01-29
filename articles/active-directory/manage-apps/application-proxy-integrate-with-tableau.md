---
title: Az Azure Active Directory Application Proxy és a Tableau |} A Microsoft Docs
description: Ismerje meg, a Tableau-telepítés a távelérés biztosítása az Azure Active Directory (Azure AD) alkalmazásproxy használatával.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: b775d9377b95c3c63e3c38cd477ce47209a6f419
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55149969"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Az Azure Active Directory Application Proxy és a Tableau 

Az Azure Active Directory Application Proxy és a Tableau partnerkapcsolatra lépett, annak érdekében, hogy könnyen alkalmazásproxy használatával a Tableau-telepítés a távelérés biztosítása. Ez a cikk bemutatja, hogyan konfigurálhatja az ebben a forgatókönyvben.  

## <a name="prerequisites"></a>Előfeltételek 

Ebben a cikkben a forgatókönyv azt feltételezi, hogy:

- [A tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) konfigurálva. 

- Egy [Application Proxy connector](application-proxy-add-on-premises-application.md) telepítve. 

 
## <a name="enabling-application-proxy-for-tableau"></a>A Tableau alkalmazásproxy engedélyezése 

Az alkalmazásproxy támogatja az OAuth 2.0 engedélyezési folyamatát, azért szükség, a Tableau megfelelően működjön. Ez azt jelenti, hogy már nem léteznek bármely különleges lépések szükségesek az alkalmazáshoz, konfigurálni kell a következő közzétételi lépéseket követve eltérő.


## <a name="publish-your-applications-in-azure"></a>Az alkalmazások közzététele az Azure-ban 

Szeretné közzétenni a Tableau, szeretne közzétenni egy alkalmazást az Azure Portalon.

Esetén:

- Részletes utasítások lépéseket lásd a 1-8- [alkalmazások közzététele az Azure AD-alkalmazásproxy](application-proxy-add-on-premises-application.md). 
- A Tableau értékeinek megkeresése az alkalmazásproxy mezők információ a dokumentációban a Tableau.  

**Az alkalmazás közzétételéhez**: 


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként. 

2. Válassza ki **Azure Active Directory > Vállalati alkalmazások**. 

3. Válassza ki **Hozzáadás** a panel tetején. 

4. Válassza ki **helyszíni alkalmazás**. 

5. Töltse ki a kötelező mezőket az új alkalmazással kapcsolatos információkat. A következő útmutatást használhatja a beállításokat: 

    - **Belső URL-cím**: Az alkalmazásnak tartalmaznia kell egy belső URL-CÍMÉT, amely a Tableau URL-cím magát. Például: `https://adventure-works.tableau.com`. 

    - **Az előhitelesítési módszer**: Az Azure Active Directory (ajánlott, de nem kötelező). 

6. Válassza ki **Hozzáadás** a panel tetején. Az alkalmazás kerül, és megnyílik a gyors üzembe helyezési menü. 

7. A gyors üzembe helyezési menüben válassza ki a **felhasználó hozzárendelése teszteléshez**, és legalább egy felhasználót az alkalmazáshoz. Ellenőrizze, hogy a teszt a helyszíni alkalmazás hozzáféréssel rendelkezik. 

8. Válassza ki **hozzárendelése** a teszt felhasználó-hozzárendelés mentése. 

9. (Nem kötelező) Válassza ki az alkalmazás-kezelési oldalán **egyszeri bejelentkezési**. Válasszon **integrált Windows-hitelesítés** a legördülő menüből, majd töltse ki a kötelező mezőket, a Tableau konfiguráció alapján. Kattintson a **Mentés** gombra. 

 

## <a name="testing"></a>Tesztelés 

Az alkalmazás teszteléséhez készen áll. A Tableau, közzétételéhez használt külső URL-címet, és jelentkezzen be egy felhasználó mindkét alkalmazás hozzárendelve.



## <a name="next-steps"></a>További lépések

Az Azure AD-alkalmazásproxy kapcsolatos további információkért lásd: [biztonságos távoli elérést biztosíthat a helyszíni alkalmazások](application-proxy.md).

