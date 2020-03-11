---
title: Az Azure AD-alkalmazásproxy és Qlik Sense |} A Microsoft Docs
description: Kapcsolja be az alkalmazásproxyt, hogy az Azure Portalon, és az összekötők telepítése a fordított proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2edf63da0fd09f829f936b54eb088c34dfe029a1
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79036999"
---
# <a name="application-proxy-and-qlik-sense"></a>Az alkalmazásproxy és Qlik Sense 
Az Azure Active Directory alkalmazásproxy és Qlik Sense platformtechnológiát együtt, ellenőrizze, hogy könnyen tud alkalmazásproxy használatával a Qlik Sense-telepítés a távelérés biztosítása.  

## <a name="prerequisites"></a>Előfeltételek 
Ez a forgatókönyv további része feltételezi, a következőket:
 
- A konfigurált [Qlik értelme](https://community.qlik.com/docs/DOC-19822). 
- [Alkalmazásproxy-összekötő telepítése](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Az alkalmazások közzététele az Azure-ban 
A közzétételhez QlikSense kell két alkalmazás közzététele az Azure-ban.  

### <a name="application-1"></a>#1. alkalmazás: 
Kövesse az alábbi lépéseket az alkalmazás közzétételéhez. Az 1-8-es lépések részletesebb ismertetése: [alkalmazások közzététele az Azure ad Application proxy használatával](application-proxy-add-on-premises-application.md). 


1. Jelentkezzen be globális rendszergazdaként az Azure Portalon. 
2. Válassza **Azure Active Directory** > **vállalati alkalmazások**lehetőséget. 
3. A panel tetején kattintson a **Hozzáadás** gombra. 
4. Válassza **a helyszíni alkalmazás**lehetőséget. 
5. Töltse ki a kötelező mezőket az új alkalmazással kapcsolatos információkat. A következő útmutatást használhatja a beállításokat: 
   - **Belső URL-cím**: az alkalmazásnak rendelkeznie kell egy belső URL-címmel, amely maga a QlikSense URL-cím. Például: **https&#58;//demo.qlikemm.com:4244** 
   - **Előhitelesítési módszer**: Azure Active Directory (ajánlott, de nem kötelező) 
1. Kattintson a panel alján található **Hozzáadás** gombra. Az alkalmazás kerül, és megnyílik a gyors üzembe helyezési menü. 
2. A gyors üzembe helyezés menüben válassza a **felhasználó kiosztása teszteléshez**lehetőséget, és adjon hozzá legalább egy felhasználót az alkalmazáshoz. Ellenőrizze, hogy a teszt a helyszíni alkalmazás hozzáféréssel rendelkezik. 
3. Válassza a **hozzárendelés** lehetőséget a felhasználói teszt hozzárendelésének mentéséhez. 
4. (Nem kötelező) Az alkalmazás felügyeleti panelen válassza ki az egyszeri bejelentkezés. A legördülő menüben válassza a **Kerberos által korlátozott delegálás** lehetőséget, és töltse ki a kötelező mezőket a Qlik-konfiguráció alapján. Kattintson a **Mentés** gombra. 

### <a name="application-2"></a>#2. alkalmazás: 
Kövesse a lépéseket, mint az alkalmazás 1, a következő kivételekkel: 

**#5. lépés**: a belső URL-címnek most a QlikSense URL-címnek kell lennie az alkalmazás által használt hitelesítési porton. Az alapértelmezett érték a HTTPS **4244** , a **4248** a QlikSense pedig az április 2018 előtti verziókban. A QlikSense-kiadások alapértelmezett értéke a 2018. április **443** a https és a **80** a http-hez.  Pl. **:&#58;https//demo.qlik.com:4244**</br></br>
**#10. lépés:** Ne állítson be egyszeri bejelentkezést, és hagyja **letiltani az egyszeri bejelentkezést**
 
 
## <a name="testing"></a>Tesztelés 
Az alkalmazás teszteléséhez készen áll. A külső URL-cím, amellyel QlikSense közzététele az alkalmazás 1 és bejelentkezés egy felhasználó mindkét alkalmazáshoz rendelt eléréséhez.  

## <a name="additional-references"></a>További referenciák
További információ a Qlik való közzétételről az alkalmazásproxy használatával kapcsolatban: a Qlik közösségi cikkeinek követése: 
- [Azure AD integrált Windows-hitelesítéssel a Kerberos által korlátozott delegálással a Qlik Sense használatával](https://community.qlik.com/docs/DOC-20183)
- [Qlik-integráció az Azure AD Application Proxy](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Következő lépések

- [Alkalmazások közzététele az Application proxyval](application-proxy-add-on-premises-application.md)
- [Alkalmazásproxy-összekötők használata](application-proxy-connector-groups.md)

