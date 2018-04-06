---
title: Az Azure Active Directory-alapú Egyszeri beépülő modul rendszergazdai útmutatója |} Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Jira/való összefolyás felett.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: d34ff6021816c73fb064a3ce73b7fcf3ae22dbd1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="admin-guide-for-the-azure-active-directory-sso-plug-in"></a>Az Azure Active Directory-alapú Egyszeri beépülő modul rendszergazdai útmutatója

## <a name="overview"></a>Áttekintés

Az Azure Active Directory (Azure AD) egyszeri bejelentkezés (SSO) beépülő modul segítségével a Microsoft Azure AD felhasználók Atlassian Jira és való összefolyás felett Server szolgáltatáson alapuló termékek bejelentkezés használni a munkahelyi vagy iskolai fiókkal. Végrehajtja az SAML 2.0-alapú egyszeri Bejelentkezést.

## <a name="how-it-works"></a>Működés

Ha a felhasználó szeretné jelentkezzen be a Atlassian Jira vagy való összefolyás felett alkalmazás, akkor jelenik meg a **az Azure AD bejelentkezési** gombra a bejelentkezési lapon. Amikor kiválasztanak, még azok szükséges jelentkezzen be az Azure AD szervezet bejelentkezési oldal (Ez azt jelenti, hogy a munkahelyi vagy iskolai fiókkal) használatával.

A felhasználók hitelesítése után vannak, össze kell használva jelentkezhet be az alkalmazásba. Ha már hitelesített azonosítója és a munkahelyi vagy iskolai fiók jelszavát, majd azokat közvetlenül jelentkezzen be az alkalmazást. 

Bejelentkezési works Jira és való összefolyás felett. Ha felhasználó jelentkezik be a Jira alkalmazáshoz, és való összefolyás felett böngészőablakban nyílik meg, nincs szükség a hitelesítő adatokat kér a másik alkalmazásra. 

Felhasználók a munkahelyi vagy iskolai fiókkal az saját alkalmazásokkal Atlassian termék is elérhető. Azok alá kell írni hitelesítő adatok nélkül.

> [!NOTE]
> A felhasználók átadása a beépülő modul segítségével nem történik meg.

## <a name="audience"></a>Célközönség

Egyszeri bejelentkezés engedélyezése az Azure AD használatával Jira és való összefolyás felett rendszergazdák használhatja a beépülő modult.

## <a name="assumptions"></a>Előfeltételek

* Jira és való összefolyás felett példányai is HTTPS-kompatibilis.
* Felhasználók Jira vagy való összefolyás felett már létre vannak hozva.
* Felhasználók Jira vagy való összefolyás felett szerepkörrel rendelkeznek.
* Rendszergazdák hozzáférhetnek a beépülő modul konfigurálásához szükséges adatokat.
* Jira vagy való összefolyás felett érhető el, valamint a vállalati hálózaton kívülről.
* A beépülő modul működését csak a helyszíni verzióval, Jira és való összefolyás felett.

## <a name="prerequisites"></a>Előfeltételek

A beépülő modul telepítése előtt, vegye figyelembe a következőket:

* Jira és való összefolyás felett telepítve vannak a Windows 64 bites verziója.
* Jira és való összefolyás felett verziója nem a HTTPS-kompatibilis.
* Jira és való összefolyás felett az interneten érhető el.
* Rendszergazdai hitelesítő adatok Jira és való összefolyás felett.
* Rendszergazdai hitelesítő adatokat az Azure AD vannak érvényben.
* WebSudo Jira és való összefolyás felett le van tiltva.

## <a name="supported-versions-of-jira-and-confluence"></a>Jira és való összefolyás felett támogatott verziói

A beépülő modul Jira és való összefolyás felett a következő verzióit támogatja:

* Jira Core és a szoftver: 6.0 való 7.2.0
* Jira ügyfélszolgálatához: 3.0 3.2
* Való összefolyás felett: 5.0 5.10

## <a name="installation"></a>Telepítés

A beépülő modul telepítéséhez kövesse az alábbi lépéseket:

1. A Jira vagy való összefolyás felett példányához, rendszergazdaként jelentkezzen be
    
2. A Jira/való összefolyás felett felügyeleti konzolon, és válassza ki **bővítmények**.
    
3. A Atlassian piactérről keressen **Microsoft SAML SSO beépülő modul**.
 
   A megfelelő verzióját a beépülő modul a találatok között jelenik meg.
 
5. Válassza ki a beépülő modult, és az univerzális beépülő modul Manager (UPM) telepíti azt.
 
A beépülő modul telepítése után megjelenik a **felhasználói telepített bővítmények** szakasza **bővítmények kezelése**.
    
## <a name="plug-in-configuration"></a>Beépülő modul konfiguráció

A beépülő modul használata előtt konfigurálnia kell azt. Válassza ki a beépülő modul, válassza ki a **konfigurálása** gombra, és adja meg a konfigurációs adatait.

Az alábbi ábrán a következő konfigurációs képernyőjén Jira és való összefolyás felett is:
    
![Beépülő modul konfigurációs képernyő](./media/ms-confluence-jira-plugin-adminguide/jira.png)

*   **Metaadatainak URL-CÍMÉT**: az összevonási metaadatok lekérni az Azure AD az URL-címet.
 
*   **Azonosítók**: az URL-címet, hogy az Azure AD használatával ellenőrizze a kérelem forrását. Azt hozzárendeli a **azonosító** elem az Azure ad-ben. A beépülő modul automatikusan-oldaltípusból lett-e az URL-címet https://*< tartomány: port >*/.
 
*   **Válasz URL-cím**: A válasz URL-CÍMEN az identitásszolgáltató (IdP), amely az SAML-bejelentkezés kezdeményezi. Azt hozzárendeli a **válasz URL-CÍMEN** elem az Azure ad-ben. A beépülő modul automatikusan-oldaltípusból lett-e az URL-címet https://*< tartomány: port >*/plugins/servlet/saml/auth.
 
*   **URL-cím bejelentkezési**: A bejelentkezési URL-címet a kiállító terjesztési hely, amely az SAML-bejelentkezés kezdeményezi. Azt hozzárendeli a **bejelentkezés** elem az Azure ad-ben. A beépülő modul automatikusan-oldaltípusból lett-e az URL-címet https://*< tartomány: port >*/plugins/servlet/saml/auth.
 
*   **IdP Entitásazonosító**: az entitás azonosítója, amely a kiállító terjesztési hely használja. Ebben a mezőben fel van töltve, a metaadatok URL-cím megoldásakor.
 
*   **Bejelentkezési URL-cím**: A bejelentkezési URL-CÍMÉT a kiállító terjesztési hely. Ebben a mezőben van feltöltve, az Azure AD, ha a metaadatok URL-cím oldva.
 
*   **Kijelentkezési URL-cím**: a kiállító terjesztési hely a kijelentkezési URL-CÍMÉT. Ebben a mezőben van feltöltve, az Azure AD, ha a metaadatok URL-cím oldva.
 
*   **X.509 tanúsítvány**: az IdP X.509-tanúsítvány. Ebben a mezőben van feltöltve, az Azure AD, ha a metaadatok URL-cím oldva.
 
*   **Bejelentkezési gomb neve**: a Bejelentkezés gombra, hogy a szervezet által a bejelentkezési oldalon jeleníteni a felhasználóknak a nevét.
 
*   **SAML-alapú felhasználói azonosító helyek**: a Jira vagy való összefolyás felett felhasználói Azonosítót várt a SAML-válasz helyét. Azok a **NameID** vagy egy egyéni attribútum nevében.
 
*   **Attribútum név**: az a felhasználói Azonosítót várt attribútum neve.
 
*   **Engedélyezze a Hitelesítőtartomány**: A kijelölést, ha a vállalat használ Active Directory összevonási szolgáltatások (AD FS) - alapú bejelentkezési - a.
 
*   **Tartománynév**: A tartománynév, ha a bejelentkezés az AD FS-alapú.
 
*   **Egyetlen Signout engedélyezése**: Ha azt szeretné, hogy az Azure AD, ha a felhasználó kijelentkezik Jira vagy való összefolyás felett Kijelentkezés a kijelölés.

## <a name="troubleshooting"></a>Hibaelhárítás

* **Több tanúsítvány hibákat kap**: az Azure AD bejelentkezés, és távolítsa el a több tanúsítványokat, amelyek az alkalmazás ellen. Győződjön meg arról, hogy csak egy tanúsítványra jelen.

* **Egy tanúsítvány érvényessége hamarosan lejár az Azure AD**: bővítmények gondoskodunk a tanúsítvány automatikus kapcsolódó kulcsváltást. Ha a tanúsítvány lejár, egy új tanúsítvány aktív kell megjelölni, és törölni kell a nem használt tanúsítványok. Amikor egy felhasználó megpróbál bejelentkezni Jira ebben a forgatókönyvben a beépülő modul fetches, és menti az új tanúsítványt.

* **Le szeretné tiltani a WebSudo (tiltsa le a biztonságos rendszergazdai munkamenet)**:
    
  * Jira biztonságos rendszergazdai munkamenetek (Ez azt jelenti, hogy jelszó megerősítésének felügyeleti funkciók elérése előtt) alapértelmezés szerint engedélyezve vannak. Ha szeretné eltávolítani ezt a lehetőséget a Jira példányban, adja meg a következő sort a jira-config.properties fájlban: `ira.websudo.is.disabled = true`
    
  * A való összefolyás felett, kövesse a lépéseket a [való összefolyás felett támogatási webhelyén](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **A metaadatok URL-cím szerint kell feltöltenie elvileg mezők nem kerül első**:
    
  * Ellenőrizze, hogy az URL-cím helyes-e. Ellenőrizze, hogy ha elvégezte a helyes-e a bérlők és az alkalmazás azonosítóját.
    
  * Adja meg az URL-címet egy böngészőben, és tekintse meg, ha akkor kap az összevonási metaadatok XML.

* **Belső hiba történt az**: tekintse át a a naplózási könyvtár a telepítés. Amikor a felhasználó próbál jelentkezzen be Azure AD SSO használatával kap a hiba, ha a naplók megoszthatja a támogatási csapatával.

* **Van egy "Felhasználói azonosító nem található" hiba történt, amikor a felhasználó megpróbál bejelentkezni**: a felhasználói azonosító létrehozása Jira vagy való összefolyás felett.

* **Az "Alkalmazás nem található" hiba van az Azure AD**: tekintse meg, ha a megfelelő URL-cím van leképezve az alkalmazást az Azure ad-ben.

* **Ha támogatásra van szüksége**: érheti el a [Azure AD SSO integrációs csoport](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). A csapat változásokhoz 24-48 üzleti órában.
    
  Az Azure portál csatornán keresztül a Microsoft támogatási jegy is is növelheti.