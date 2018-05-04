---
title: Az Azure Active Directory-Proxy és a Tableau |} Microsoft Docs
description: 'Útmutató: Azure Active Directory (Azure AD) alkalmazásproxy használatával adja meg a távelérés a Tableau üzembe helyezésére.  .'
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 480e1cd72bcb42d7f39a92fc49e7c3d66ecf05f8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Az Azure Active Directory-Proxy és a Tableau 

Hogy könnyen alkalmazásproxy használatával adja meg a Tableau környezet a távoli hozzáférést az Azure Active Directory Alkalmazásproxyjával és a Tableau közösen. Ez a cikk azt ismerteti, hogyan konfigurálhatja az ebben a forgatókönyvben.  

## <a name="prerequisites"></a>Előfeltételek 

Ez a cikk a forgatókönyv azt feltételezi, hogy rendelkezik:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#reverse-proxy-server) konfigurálva. 

- Egy [Application Proxy connector](active-directory-application-proxy-enable.md) telepítve. 

 

## <a name="enabling-application-proxy-for-tableau"></a>A Tableau alkalmazásproxy engedélyezése 

Ha használandó alkalmazásproxy a Tableau, szeretné-e egy e-mailek küldése [ aadapfeedback@microsoft.com ](mailto:aadapfeedback@microsoft.com) engedélyezve van ez a forgatókönyv segítségével.
Az e-mailben:

-   Alkalmazásproxy engedélyezése a Tableau használják tulajdonos
-   A bérlő azonosítója szerepeljenek a szervezet    

Amikor készen áll az alkalmazás használatára kap egy megerősítő üzenet. Általában ez vesz igénybe a hét. A konfiguráció azonban befejezheti a megerősített várakozás közben.


 

## <a name="publish-your-applications-in-azure"></a>Az alkalmazások közzététele az Azure-ban 

A Tableau való közzétételéhez szeretné közzétenni egy alkalmazást az Azure portálon.

Esetén:

- Részletes utasítások a lépések 1-8, lásd: [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](application-proxy-publish-azure-portal.md). 
- Információ a Tableau értékek keresése a következő alkalmazás Proxy mezők a dokumentáció Tableau.  

**Az alkalmazás közzététele**: 


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként. 

2. Válassza ki **Azure Active Directory > Vállalati alkalmazások**. 

3. Válassza ki **Hozzáadás** a panel tetején. 

4. Válassza ki **helyszíni alkalmazás**. 

5. Töltse ki a kötelező mezőket az új alkalmazás adatait tartalmazó. A következő útmutatást használhatja a beállításokat: 

    - **Belső URL-cím**: az alkalmazás egy belső URL-CÍMÉT, amely a Tableau URL-cím kell rendelkeznie. Például: `https://adventure-works.tableau.com`. 

    - **Előhitelesítési módszer**: Azure Active Directoryban (ajánlott, de nem kötelező). 

6. Válassza ki **Hozzáadás** a panel tetején. Az alkalmazás kerül, és a quick start menü megnyitása. 

7. A quick start menüben válassza ki a **rendelje hozzá a felhasználót tesztelési**, és legalább egy felhasználói hozzáadni az alkalmazáshoz. Győződjön meg arról, hogy a teszt fiókjának van hozzáférési joga a helyszíni alkalmazások. 

8. Válassza ki **hozzárendelése** menteni a teszt felhasználó hozzárendelése. 

9. (Választható) Az alkalmazás kezelése lapon válassza az **egyszeri bejelentkezés**. Válasszon **integrált Windows-hitelesítés** a legördülő menüből, és kitöltése során a kötelező mezőket a Tableau konfiguráció alapján. Kattintson a **Mentés** gombra. 

 

## <a name="testing"></a>Tesztelés 

Az alkalmazás tesztelése készen áll. A Tableau, közzétételéhez használt külső URL hozzáférés, és jelentkezzen be egy felhasználó lehet hozzárendelve mindkét alkalmazáshoz.



## <a name="next-steps"></a>További lépések

Az Azure AD-alkalmazásproxy kapcsolatos további információkért lásd: [hogyan biztosíthat biztonságos távoli hozzáférést a helyszíni alkalmazások](active-directory-application-proxy-get-started.md).

