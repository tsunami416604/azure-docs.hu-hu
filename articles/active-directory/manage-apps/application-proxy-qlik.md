---
title: Azure AD alkalmazásproxy és Qlik Sense| Microsoft dokumentumok
description: Kapcsolja be az alkalmazásproxyt az Azure Portalon, és telepítse a fordított proxy összekötőit.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036999"
---
# <a name="application-proxy-and-qlik-sense"></a>Alkalmazás proxy és Qlik Sense 
Az Azure Active Directory alkalmazásproxy és a Qlik Sense együttműködve biztosítja, hogy az Alkalmazásproxy segítségével távoli hozzáférést biztosíthasson a Qlik Sense központi telepítéséhez.  

## <a name="prerequisites"></a>Előfeltételek 
A forgatókönyv további része feltételezi, hogy a következőket tette:
 
- Konfigurált [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- [Alkalmazásproxy-összekötő telepítése](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Az alkalmazások közzététele az Azure-ban 
A QlikSense közzétételéhez két alkalmazást kell közzétennie az Azure-ban.  

### <a name="application-1"></a>Alkalmazás #1: 
Az alkalmazás közzétételéhez kövesse az alábbi lépéseket. Az 1–8. [Publish applications using Azure AD Application Proxy](application-proxy-add-on-premises-application.md) 


1. Jelentkezzen be az Azure Portalra globális rendszergazdaként. 
2. Válassza az **Azure Active Directory** > **Enterprise alkalmazások lehetőséget.** 
3. Válassza a **Hozzáadás** lehetőséget a fűrészlap tetején. 
4. Válassza **a Helyszíni alkalmazás lehetőséget.** 
5. Töltse ki a szükséges mezőket az új alkalmazással kapcsolatos információkkal. A beállításokhoz az alábbi útmutatást kell használni: 
   - **Belső URL**: Ennek az alkalmazásnak rendelkeznie kell egy belső URL-lel, amely maga a QlikSense URL. Például **https&#58;//demo.qlikemm.com:4244** 
   - **Előhitelesítési módszer**: Azure Active Directory (ajánlott, de nem szükséges) 
1. Válassza a **Hozzáadás** lehetőséget a fűrészlap alján. Az alkalmazás hozzáadódik, és megnyílik a gyorsindítás menü. 
2. A gyorsindítás menüben válassza a **Felhasználó hozzárendelése tesztelésre**lehetőséget, és adjon hozzá legalább egy felhasználót az alkalmazáshoz. Győződjön meg arról, hogy a tesztfiók rendelkezik-e hozzáféréssel a helyszíni alkalmazáshoz. 
3. A Teszt felhasználói hozzárendelés mentéséhez válassza a **Hozzárendelés** lehetőséget. 
4. (Nem kötelező) Az alkalmazáskezelési panelen válassza az Egyszeri bejelentkezés lehetőséget. Válassza a legördülő menü **Kerberos korlátozott delegálása** parancsát, és töltse ki a qlik konfiguráción alapuló szükséges mezőket. Kattintson a **Mentés** gombra. 

### <a name="application-2"></a>Jelentkezési #2: 
Kövesse ugyanazokat a lépéseket, mint az Alkalmazás #1 esetében, a következő kivételekkel: 

**#5 lépés:** A belső URL-t most a QlikSense URL-t kell használnia az alkalmazás által használt hitelesítési porttal. Az alapértelmezett érték **4244** HTTPS esetén, http esetén **pedig 4248** a QlikSense-kiadások esetében 2018 áprilisa előtt. A QlikSense kiadások alapértelmezett beállítása 2018 áprilisa után **443** HTTPS és **80** HTTP esetén.  Pl.: **https&#58;//demo.qlik.com:4244**</br></br>
**#10. lépés:** Ne állítsa be az Egyszeri bejelentkezést, és hagyja **letiltva** az egyszeri bejelentkezést
 
 
## <a name="testing"></a>Tesztelés 
Az alkalmazás készen áll a tesztelésre. Hozzáférés a külső URL-t használt közzé QlikSense az Application #1, és jelentkezzen be, mint a felhasználó rendelt mindkét alkalmazás.  

## <a name="additional-references"></a>További referenciák
A Qlik Sense alkalmazásproxyval történő közzétételéről a Qlik közösségi cikkeket követve tájékozódhat: 
- [Azure AD integrált Windows-hitelesítéssel, Kerberos-alapú delegálással a Qlik Sense segítségével](https://community.qlik.com/docs/DOC-20183)
- [Qlik Sense integráció az Azure AD alkalmazásproxyval](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>További lépések

- [Alkalmazások közzététele az alkalmazásproxy használatával](application-proxy-add-on-premises-application.md)
- [Alkalmazásproxy-összekötők használata](application-proxy-connector-groups.md)

