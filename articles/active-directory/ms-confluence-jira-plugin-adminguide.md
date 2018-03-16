---
title: "A Microsoft Azure Active Directory egyszeri bejelentkezés beépülő modul felügyeleti útmutató |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Microsoft Azure Active Directory egyszeri bejelentkezésre vonatkozó JIRA között."
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
ms.openlocfilehash: af949d1db8af37a534a16364f9f0763479c436e4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-admin-guide"></a>A Microsoft Azure Active Directory egyszeri bejelentkezés beépülő modul felügyeleti útmutató

## <a name="table-of-contents"></a>Tartalomjegyzék

1. **[OVERVIEW](#overview)**
2. **[HOGYAN MŰKÖDIK?](#how-it-works)**
3. **[CÉLKÖZÖNSÉG](#audience)**
4. **[ELŐFELTÉTELEK](#assumptions)**
5. **[PREREQUISITES](#prerequisites)**
6. **[JIRA ÉS VALÓ ÖSSZEFOLYÁS FELETT TÁMOGATOTT VERZIÓI](#supported-versions-of-jira-and-confluence)**
7. **[TELEPÍTÉS](#installation)**
8. **[BEÉPÜLŐ MODUL KONFIGURÁCIÓ](#plugin-configuration)**
9. **[A MEZŐ MAGYARÁZAT A BŐVÍTMÉNY KÖVETKEZŐ KONFIGURÁCIÓS KÉPERNYŐJÉN:](#field-explanation-for-add---on-configuration-screen:)**
10. **[HIBAELHÁRÍTÁS](#troubleshooting)**

## <a name="overview"></a>Áttekintés

Ezek a bővítmények engedélyezi a Microsoft Azure AD ügyfeleknek a szervezet felhasználónevüket és jelszavukat bejelentkezési azonosítóhoz használja a Atlassian Jira be, és való összefolyás felett Server termékek alapján. Végrehajtja az SAML 2.0 SSO-alapú.

## <a name="how-it-works"></a>Működés

Ha a felhasználók szeretnék bejelentkezni Atlassian Jira vagy való összefolyás felett, akkor jelenik meg a **az Azure AD bejelentkezési** gombra a bejelentkezési lapon. Ezek kattintson rá, ha azok szükségesek az Azure AD-szervezet bejelentkezési oldal bejelentkezéshez.

Ha a felhasználók hitelesítése, össze kell tudni bejelentkezni az alkalmazásba. Ha már hitelesített szervezet Azonosítóját és jelszavát, majd közvetlenül jelentkeznek az alkalmazásba. Emellett vegye figyelembe, hogy a bejelentkezés JIRA és való összefolyás felett használható. Ha felhasználók vannak bejelentkezve JIRA alkalmazásba, és való összefolyás felett is meg nyitva a böngészőablakban, szükségük egyszer bejelentkeznek, és nem kell megadnia a hitelesítő adatok újra egyéb alkalmazás. A felhasználók a Atlassian termék myapps Azure fiókon keresztül is elérhető, és azok kell bejelentkeznie a hitelesítő adatok nélkül.

> [!NOTE]
> A felhasználók átadása nem végezhető el ez a bővítmény használatával.

## <a name="audience"></a>Célközönség

JIRA és való összefolyás felett rendszergazdák, akik tervezi, hogy a beépülő modul használatával engedélyezhető az SSO-t az Azure AD.

## <a name="assumptions"></a>Előfeltételek

* A példány JIRA/való összefolyás felett HTTPS-kompatibilis
* Felhasználók már létre vannak hozva a JIRA/való összefolyás felett
* Felhasználók rendelkeznek az JIRA/való összefolyás felett hozzárendelt szerepkör
* Rendszergazdák férhetnek hozzá a beépülő modul konfigurálásához szükséges adatokhoz
* JIRA/való összefolyás felett érhetők el, valamint a vállalati hálózaton kívül
* Adja hozzá a működéséről csak helyi JIRA és verziójával való összefolyás felett

## <a name="prerequisites"></a>Előfeltételek

Megjegyzés: a következő előfeltételek előtt folytatni azokat, amelyek a bővítmény telepítést:

* A Windows 64 bites verzióján telepített JIRA/való összefolyás felett
* JIRA/való összefolyás felett verziók az alábbiak HTTPS-kompatibilis
* Megjegyzés: a támogatott verziójú beépülő modul az alábbi "Támogatott verziói" részben.
* JIRA/való összefolyás felett interneten érhető el.
* Rendszergazdai hitelesítő adatokat a JIRA/való összefolyás felett
* Az Azure AD rendszergazdai hitelesítő adatokat
* WebSudo le kell tiltani a JIRA és való összefolyás felett

## <a name="supported-versions-of-jira-and-confluence"></a>JIRA és való összefolyás felett támogatott verziói

Mostantól JIRA és való összefolyás felett következő verziói támogatottak:

* JIRA Core és a szoftver: 6.0 való 7.2.0
* JIRA Service Desk: 3.0 to 3.2
* Való összefolyás felett: 5.0 5.10

## <a name="installation"></a>Telepítés

Felügyelet az alábbiakban említett a beépülő modul telepítése lépéseket kell követniük:

1. Rendszergazdaként jelentkezzen be a JIRA/való összefolyás felett példányát
    
2. Ugrás a JIRA/való összefolyás felett felügyeleti, majd kattintson a bővítmények.
    
3. A Atlassian piactéren, keressen **Microsoft SAML SSO beépülő modul**
 
4. A bővítmény megfelelő verzióját a keresési jelenik meg
 
5. Válassza ki a beépülő modul, és UPM azonos telepíti.
 
6. Megjelenik a beépülő modul telepítése után telepítette bővítmények szakasz kezelése bővítmény szakasz
 
7. Be kell állítania a beépülő modul az használata előtt.
 
8. Kattintson a beépülő modul, és megjelenik a Konfigurálás gombra.
 
9. Egyes elemekre kattintva adja meg a konfigurációs bemenetek
    
## <a name="plugin-configuration"></a>Beépülő modul konfiguráció

Következő kép bemutatja a bővítmény konfigurálására szolgáló képernyőn JIRA és való összefolyás felett
    
![bővítmények konfigurálása](./media/ms-confluence-jira-plugin-adminguide/jira.png)

### <a name="field-explanation-for-add-on-configuration-screen"></a>A mező magyarázat a bővítmény következő konfigurációs képernyőjén:

1.   Metaadatainak URL-címe: Az összevonási metaadatok lekérni az Azure AD URL-címe
 
2.   Azonosító: A kérelem forrását érvényesítéséhez használt Azure ad. Ez leképezi eleme az Azure ad-ben. Ez az automatikus, https://<domain:port>/ beépülő modul által származtatott
 
3.   Válasz URL-címe: Használata válasz URL-címet a SAML bejelentkezési kezdeményezése a kiállító terjesztési hely. Ez leképezi a válasz URL-CÍMEN elem az Azure ad-ben. Ez az automatikus, https://<domain:port>/plugins/servlet/saml/auth beépülő modul által származtatott
 
4.   Bejelentkezési URL-címen: Használata bejelentkezési URL-cím a IDP kezdeményezheti a SAML-bejelentkezés. Ez leképezi a bejelentkezés elem az Azure ad-ben. Ez az automatikus, https://<domain:port>/plugins/servlet/saml/auth beépülő modul által származtatott
 
5.   IdP Entitásazonosító: Az entitás azonosítója, amely a kiállító terjesztési hely használja. Ez a telepítéskor metaadatainak URL-CÍMÉT megoldásakor.
 
6.   Bejelentkezési URL-címe: A bejelentkezési URL-CÍMÉT a kiállító terjesztési hely. Ez a telepítéskor az Azure AD, ha a probléma fennáll metaadatainak URL-CÍMÉT.
 
7.   Kijelentkezési URL-cím: A kijelentkezési URL-CÍMÉT a kiállító terjesztési hely. Ez a telepítéskor az Azure AD, ha a probléma fennáll metaadatainak URL-CÍMÉT.
 
8.   X.509 tanúsítvány: A kiállító terjesztési hely X.509-tanúsítvány. Ez a telepítéskor az Azure AD, ha a probléma fennáll metaadatainak URL-CÍMÉT.
 
9.   Bejelentkezési gomb neve: A bejelentkezési gomb neve a szervezet által megjelenítéséhez. Ezt a szöveget a felhasználók számára, a Bejelentkezés gombra, a bejelentkezési képernyőn látható.
 
10.   SAML-alapú felhasználói azonosító helyek: Ahol a felhasználói azonosítót várt a SAML-válasz. Vagy lehet NameID vagy egy egyéni attribútum nevében. Ez az azonosító nem lehet a JIRA/való összefolyás felett felhasználói azonosítóját.
 
11.   Az attribútumnév: a felhasználói azonosítót várt attribútum neve.
 
12.   Enable otthoni Hitelesítőtartományának feltárási: Jelölje be ezt a jelzőt, ha a vállalat az AD FS-alapú bejelentkezés használatával.
 
13.   Tartománynév: Adja meg a tartomány nevét az AD FS-alapú bejelentkezés esetén
 
14.   Engedélyezze a kimenő egyszeri bejelentkezéshez: az office ellenőrizze, hogy az Azure AD, ha a JIRA/való összefolyás felett kijelentkezik a felhasználói jelentkezzen ki szeretné.

### <a name="troubleshooting"></a>Hibaelhárítás

* Ha több tanúsítványok hiba
    
    * Bejelentkezés az Azure AD és az alkalmazás ellen több tanúsítványok eltávolítása. Győződjön meg arról, hogy csak egy tanúsítvány jelen van-e.

* Tanúsítvány lejár, az Azure ad-ben.
    
    * A tanúsítvány automatikus helyettesítő bővítmények kezeli. Amikor egy tanúsítvány lejárt, új tanúsítványt aktív kell megjelölni, és nem használt tanúsítvány törölni kell. Ha a felhasználó megpróbál bejelentkezni a JIRA ebben a forgatókönyvben, a bővítmény beolvassa az új tanúsítványt, és mentse a beépülő modul.

* WebSudo (tiltsa le a biztonságos rendszergazdai munkamenet) letiltása
    
    * JIRA: Biztonságos rendszergazdai alapértelmezés szerint engedélyezve vannak a munkamenetek (Ez azt jelenti, hogy jelszó megerősítésének felügyeleti funkciók elérése előtt). Ha szeretné letiltani ezt a JIRA példányban, ez a funkció a következő sort a jira-config.properties fájlban való megadásával letilthatja: "ira.websudo.is.disabled = true"
    
    * Való összefolyás felett: Kövesse a megjelenő lépéseket, mint a következő URL-címe https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html

* Metaadatok URL-cím szerint kell feltöltenie elvileg mezők nem kerül beolvasása
    
    * Ellenőrizze, hogy az URL-cím helyes-e. Ha elvégezte a megfelelő bérlői és az alkalmazásazonosító ellenőrzése
    
    * Kattintson a böngésző URL-CÍMÉT, és tekintse meg, ha az összevonási metaadatok XML részesül.

* Belső kiszolgálóhiba:
    
    * Nyissa meg a telepítési naplók könyvtárában található naplók keresztül. Ha a hiba, ha a felhasználó próbál bejelentkezni az Azure AD SSO használatával kap, a támogatási információkat a jelen dokumentum alább megoszthatja a naplókat.

* Felhasználói azonosító nem található a hiba, ha a felhasználó megpróbál bejelentkezni
    
    * Felhasználó nem jön létre a JIRA/való összefolyás felett, és így azonos.

* Nem található a hiba az Azure AD-alkalmazás
    
    * Tekintse meg, ha a megfelelő URL-cím van leképezve az alkalmazást az Azure ad-ben.

* Támogatás részletei: kapcsolatba velünk a: [Azure AD SSO integrációs csoport](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 24-48 üzleti órán belül válaszolunk.
    
    * Az Azure portál csatornán keresztül a Microsoft támogatási jegy is is növelheti.