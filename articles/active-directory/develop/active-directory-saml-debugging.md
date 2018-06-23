---
title: SAML-alapú egyszeri bejelentkezés – Azure Active Directory hibakeresése |} Microsoft Docs
description: A hibakeresési SAML-alapú egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban.
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/15/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin, dastrock, smalser
ms.openlocfilehash: d0c006b21e00693fe6c8b35237d4ce55f67c0f75
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320596"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Hibakeresési SAML-alapú egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban

Megtudhatja, hogyan keresse meg és hárítsa el [egyszeri bejelentkezés](../manage-apps/what-is-single-sign-on.md) támogató alkalmazások az Azure Active Directory (Azure AD) problémái [Security Assertion Markup Language (SAML) 2.0-s](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Előkészületek
Javasoljuk, hogy telepíti a [saját alkalmazások biztonságos bejelentkezési bővítmény](../active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension). A bővítmény megkönnyíti a SAML-kérelmet és az egyszeri bejelentkezést a problémák elhárításához szükséges SAML válasz információk összegyűjtéséhez. Abban az esetben, ha a kiterjesztés nem tudja telepíteni, a Ez a cikk bemutatja, hogyan és a-kiterjesztés telepítése anélkül problémák megoldásához.

Töltse le és telepítse a saját alkalmazások biztonságos bejelentkezési bővítmény, használja az alábbi hivatkozások egyikét.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Peremhálózati](https://go.microsoft.com/fwlink/?linkid=845176)
- [A Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>SAML-alapú egyszeri bejelentkezés tesztelése

Tesztelési SAML-alapú egyszeri bejelentkezést, aad-ben és a cél alkalmazás között:

1.  Jelentkezzen be a [Azure-portálon](https://portal.azure.com) a globális rendszergazdaként vagy más felügyeleti alkalmazások kezelésére jogosult.
2.  A bal oldali panelen kattintson **Azure Active Directory**, és kattintson a **vállalati alkalmazások**. 
3.  Vállalati alkalmazások listájának megtekintéséhez kattintson az alkalmazás, amely egyszeri bejelentkezést vizsgálni kívánt, majd kattintson a bal oldali lehetőségei közül **egyszeri bejelentkezés**.
4.  Nyissa meg a SAML-alapú egyszeri bejelentkezés tesztelési élmény, a a **tartomány és az URL-címek** szakaszban kattintson **teszt SAML beállítás**. Ha a teszt SAML beállítás gomb szürkén jelenik meg, akkor először írja ki és mentse el a szükséges attribútumokat.
5.  Az a **egyszeri bejelentkezés tesztelése** panelen, a munkahelyi hitelesítő adatait használja a célalkalmazásnak történő bejelentkezéshez. Az aktuális felhasználó vagy egy másik felhasználó bejelentkezhet. Jelentkezzen be másik felhasználóként, egy értesítés rákérdez hitelesítéséhez.

Ha Ön sikeresen bejelentkezett, a rendszer megfelelt a vizsgálat. Ebben az esetben az Azure AD egy SAML válasz jogkivonatot ki az alkalmazáshoz. Az alkalmazás sikeresen szolgáltatásba a SAML-jogkivonat használ.

Ha hiba történt a bejelentkezési oldal vállalati vagy az alkalmazás oldal, használja a következő szakaszok egyikét a hiba megoldása érdekében.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Oldja meg a bejelentkezési hiba a vállalati bejelentkezési oldal

Amikor megpróbál bejelentkezni hiba jelenhet meg a vállalati bejelentkezési lapon. 

![Bejelentkezési hiba](media/active-directory-saml-debugging/error.png)

Debug hibára, a hibaüzenet és a SAML-kérelmet van szükség. A személyes alkalmazások biztonságos bejelentkezési bővítmény automatikusan gyűjti össze az adatokat, és a megoldási útmutató jeleníti meg az Azure AD. 

A bejelentkezési hiba lépett fel a MyApps biztonságos megoldásához bejelentkezési bővítmény telepítve:

1.  Ha hiba lép fel, a bővítmény irányítja az Azure Ad vissza a **egyszeri bejelentkezés tesztelése** panelen. 
2.  Az a **egyszeri bejelentkezés tesztelése** panelen kattintson a **töltse le a SAML-kérelmet**. 
3.  Útmutatás alapján a hiba és a SAML-kérelmet szereplő értékek adott feloldási kell megjelennie. Olvassa el a útmutatást.

A hiba megoldásához MyApps biztonságos bejelentkezési bővítmény telepítése nélkül:

1. Másolja az hibaüzenet jelenik meg a lap jobb alsó sarkában. A hibaüzenet tartalmazza:
    - A CorrelationID és időbélyegző. Ezeket az értékeket hoz létre egy támogatási esetet a Microsofttal, mivel a probléma azonosításához, és adja meg a problémát egy pontos névfeloldást a mérnökök segítségükkel esetén fontos.
    - A probléma okának nyilatkozatot.
2.  Az Azure AD vissza, és keresse a **egyszeri bejelentkezés tesztelése** panelen.
3.  A fenti szövegmezőben **megoldási útmutató beolvasása**, illessze be a hibaüzenetet.
3.  Kattintson a **megoldási útmutató beolvasása** megjelenítéséhez a probléma megoldásához szükséges lépéseket. Útmutatás a SAML-kérelmet vagy a SAML-válasz információra lehet szükség. Ha nem használja a biztonságos MyApps bejelentkezési bővítményt, szükség lehet egy eszköz, mint [Fiddler](http://www.telerik.com/fiddler) a SAML-kérelem-válasz beolvasása.
4.  Ellenőrizze a SAML-kérelmet a célkiszolgáló megfelel-e a SAML egyetlen bejelentkezési URL-címe az Azure Active Directory kapott
5.  A SAML-kérelmet a kibocsátó ellenőrizze az alkalmazás Azure Active Directoryban konfigurált ugyanezzel az azonosítóval. Az Azure AD a kibocsátó használja a címtárban található alkalmazás.
6.  Ellenőrizze, hogy AssertionConsumerServiceURL, ahol az alkalmazás fogadni az SAML-jogkivonat az Azure Active Directory vár. Ezt az értéket konfigurálhatja az Azure Active Directoryban, de ez nem kötelező része a SAML-kérelmet.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Oldja meg az alkalmazás oldalon a bejelentkezési hiba

Előfordulhat, hogy jelentkezzen be sikeresen, és majd tekintse meg a hiba az alkalmazás lapján. Ez akkor fordul elő, amikor az Azure AD egy jogkivonatot ki az alkalmazáshoz, de az alkalmazás nem fogadja el a választ.   

A hiba elhárításához:

1. Ha az alkalmazás az Azure AD-katalógusában, ellenőrizze az alkalmazás integrálja az Azure AD az összes lépésekkel. Az integráció utasításokat az alkalmazás megkereséséhez lásd: a [SaaS-alkalmazás integrációs bemutatók felsorolása](../saas-apps/tutorial-list.md).
2. A SAML-válasz lekérése.
    - Ha az alkalmazások a biztonságos bejelentkezés bővítmény telepítve van, az a **egyszeri bejelentkezés tesztelése** panelen kattintson a **töltse le a SAML-válasz**.
    - Ha nincs telepítve a bővítmény, használjon egy eszköz például [Fiddler](http://www.telerik.com/fiddler) SAML-válasz beolvasása. 
3. Figyelje meg ezeknek az elemeknek a SAML-válasz jogkivonat:
    - Felhasználói NameID érték és formátumának egyedi azonosítója
    - A token által kiadott jogcímeket
    - A token aláírásához használt tanúsítványt. Tekintse át a SAML-válasz kapcsolatos információkért lásd: [egyszeri bejelentkezés SAML protokoll](active-directory-single-sign-on-protocol-reference.md).
4. A SAML-válasz további információkért lásd: [egyszeri bejelentkezés SAML protokoll](active-directory-single-sign-on-protocol-reference.md).
5. Most, hogy tekintse át a SAML-válasz, tekintse meg a [hiba történt a bejelentkezés után az alkalmazás oldalon](../application-sign-in-problem-application-error.md) kapcsolatos útmutatás a probléma megoldása. 
6. Ha még nem jelentkezhetnek be sikeresen, kérje meg az alkalmazás gyártójának Mi hiányzik a SAML-válasz.


## <a name="next-steps"></a>További lépések
Most, hogy az egyszeri bejelentkezés az alkalmazás működik, sikerült [automatizálhatja a felhasználó kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz való](../active-directory-saas-app-provisioning.md), vagy [feltételes hozzáférés – első lépések](../active-directory-conditional-access-azure-portal-get-started.md).


