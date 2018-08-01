---
title: Az Azure Active Directory Application Proxy és a Tableau |} A Microsoft Docs
description: Ismerje meg, a Tableau-telepítés a távelérés biztosítása az Azure Active Directory (Azure AD) alkalmazásproxy használatával.  .
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7cace1af527c1c7c80bf0e23f7a88aa9ac9f9d03
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365022"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Az Azure Active Directory Application Proxy és a Tableau 

Az Azure Active Directory Application Proxy és a Tableau partnerkapcsolatra lépett, annak érdekében, hogy könnyen alkalmazásproxy használatával a Tableau-telepítés a távelérés biztosítása. Ez a cikk bemutatja, hogyan konfigurálhatja az ebben a forgatókönyvben.  

## <a name="prerequisites"></a>Előfeltételek 

Ebben a cikkben a forgatókönyv azt feltételezi, hogy:

- [A tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) konfigurálva. 

- Egy [Application Proxy connector](application-proxy-enable.md) telepítve. 

 

## <a name="enabling-application-proxy-for-tableau"></a>A Tableau alkalmazásproxy engedélyezése 

Ha szeretné az alkalmazásproxy használata a Tableau, szeretné-e egy e-mailek küldése [ aadapfeedback@microsoft.com ](mailto:aadapfeedback@microsoft.com) beolvasni ebben a forgatókönyvben engedélyezve van.
Az e-mailben:

-   Alkalmazásproxy engedélyezése használata a Tableau, a tulajdonos
-   A bérlő Azonosítóját tartalmazza törzsében.    

Kap egy megerősítő, ha az alkalmazás készen áll. A konfiguráció befejezéséhez a megerősítés várakozás közben.


 

## <a name="publish-your-applications-in-azure"></a>Az alkalmazások közzététele az Azure-ban 

Szeretné közzétenni a Tableau, szeretne közzétenni egy alkalmazást az Azure Portalon.

Esetén:

- Részletes utasítások lépéseket lásd a 1-8- [alkalmazások közzététele az Azure AD-alkalmazásproxy](application-proxy-publish-azure-portal.md). 
- A Tableau értékeinek megkeresése az alkalmazásproxy mezők információ a dokumentációban a Tableau.  

**Az alkalmazás közzétételéhez**: 


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként. 

2. Válassza ki **Azure Active Directory > Vállalati alkalmazások**. 

3. Válassza ki **Hozzáadás** a panel tetején. 

4. Válassza ki **helyszíni alkalmazás**. 

5. Töltse ki a kötelező mezőket az új alkalmazással kapcsolatos információkat. A következő útmutatást használhatja a beállításokat: 

    - **Belső URL-cím**: az alkalmazásnak tartalmaznia kell egy belső URL-CÍMÉT, amely a Tableau URL-cím magát. Például: `https://adventure-works.tableau.com`. 

    - **Az előhitelesítési módszer**: Azure Active Directory (ajánlott, de nem kötelező). 

6. Válassza ki **Hozzáadás** a panel tetején. Az alkalmazás kerül, és megnyílik a gyors üzembe helyezési menü. 

7. A gyors üzembe helyezési menüben válassza ki a **felhasználó hozzárendelése teszteléshez**, és legalább egy felhasználót az alkalmazáshoz. Ellenőrizze, hogy a teszt a helyszíni alkalmazás hozzáféréssel rendelkezik. 

8. Válassza ki **hozzárendelése** a teszt felhasználó-hozzárendelés mentése. 

9. (Nem kötelező) Válassza ki az alkalmazás-kezelési oldalán **egyszeri bejelentkezési**. Válasszon **integrált Windows-hitelesítés** a legördülő menüből, majd töltse ki a kötelező mezőket, a Tableau konfiguráció alapján. Kattintson a **Mentés** gombra. 

 

## <a name="testing"></a>Tesztelés 

Az alkalmazás teszteléséhez készen áll. A Tableau, közzétételéhez használt külső URL-címet, és jelentkezzen be egy felhasználó mindkét alkalmazás hozzárendelve.



## <a name="next-steps"></a>További lépések

Az Azure AD-alkalmazásproxy kapcsolatos további információkért lásd: [biztonságos távoli elérést biztosíthat a helyszíni alkalmazások](application-proxy.md).

