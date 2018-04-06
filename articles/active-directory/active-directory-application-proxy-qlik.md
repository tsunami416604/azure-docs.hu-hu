---
title: Az Azure AD alkalmazás-Proxy és Qlik logika |} Microsoft Docs
description: Alkalmazásproxy bekapcsolása az Azure portálon, és a fordított proxyhoz tartozó az összekötők telepítése.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/03/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: e10fefdd3bd46aeb90fd2cfc82d4fee3b17d867b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="application-proxy-and-qlik-sense"></a>Proxy és Qlik logika 
Az Azure Active Directory Alkalmazásproxyjával és Qlik logika közösen együtt, hogy könnyen alkalmazásproxy használatával adja meg a távoli elérés az Qlik logika üzembe helyezéshez.  

## <a name="prerequisites"></a>Előfeltételek 
Ez a forgatókönyv további része azt feltételezi, hogy Ön a következőket:
 
- Konfigurált [Qlik logika](https://community.qlik.com/docs/DOC-19822). 
- [Az alkalmazásproxy-összekötő telepítése](active-directory-application-proxy-enable.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Az alkalmazások közzététele az Azure-ban 
QlikSense közzététele, akkor két alkalmazások közzététele az Azure-ban.  

### <a name="application-1"></a>#1. alkalmazás: 
Kövesse az alábbi lépéseket az alkalmazás közzétételére. A részletes lépéseket bemutató 1-8, tekintse meg a még [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](application-proxy-publish-azure-portal.md). 


1. Jelentkezzen be globális rendszergazdaként az Azure-portálon. 
2. Válassza ki **Azure Active Directory** > **vállalati alkalmazások**. 
3. Válassza ki **Hozzáadás** a panel tetején. 
4. Válassza ki **helyszíni alkalmazás**. 
5.       Töltse ki a kötelező mezőket az új alkalmazás adatait tartalmazó. A következő útmutatást használhatja a beállításokat: 
    - **Belső URL-cím**: az alkalmazás egy belső URL-CÍMÉT, amely a QlikSense URL-cím kell rendelkeznie. Például **https&#58;//demo.qlikemm.com:4244** 
    - **Előhitelesítési módszer**: Azure Active Directoryban (de nem ajánlott) 
1.       Válassza ki **Hozzáadás** a panel alján. Az alkalmazás kerül, és a quick start menü megnyitása. 
2.       A quick start menüben válassza ki a **rendelje hozzá a felhasználót tesztelési**, és legalább egy felhasználói hozzáadni az alkalmazáshoz. Győződjön meg arról, hogy a teszt fiókjának van hozzáférési joga a helyszíni alkalmazások. 
3.       Válassza ki **hozzárendelése** menteni a teszt felhasználó hozzárendelése. 
4.       (Választható) Az alkalmazás-felügyelet panel válassza az egyszeri bejelentkezés. Válasszon **Kerberos által korlátozott delegálás** a legördülő menüből, és kitöltése során a kötelező mezőket a Qlik konfiguráció alapján. Kattintson a **Mentés** gombra. 

### <a name="application-2"></a>#2. alkalmazás: 
Kövesse a lépéseket, mint az alkalmazás 1, a következő kivételekkel: 

**#5. lépés**: belső URL-cím kell a QlikSense a hitelesítési portot, az alkalmazás által használt URL-cím. Az alapértelmezett érték **4244** HTTPS és a HTTP 4248. Például: **https&#58;//demo.qlik.com:4244**</br></br> 
**#10. lépés:** nem beállítania egyszeri Bejelentkezést, és hagyja a **egyszeri bejelentkezés le van tiltva**
 
 
## <a name="testing"></a>Tesztelés 
Az alkalmazás tesztelése készen áll. Hozzáférési QlikSense közzétételéhez az alkalmazás 1 és bejelentkezési mindkét alkalmazáshoz rendelt felhasználó használt külső URL-CÍMÉT.  

## <a name="next-steps"></a>További lépések

- [Alkalmazások közzététele az alkalmazásproxy](application-proxy-publish-azure-portal.md)
- [Alkalmazásproxy összekötők használata](active-directory-application-proxy-connectors-azure-portal.md).
