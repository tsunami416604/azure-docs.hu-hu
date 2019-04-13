---
title: Összevonási tanúsítványok kezelése az Azure ad-ben |} A Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre az összevonási tanúsítványok lejáratának dátumát, és a közeljövőben lejáró tanúsítványok megújítása.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: celested
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c2d14a2aa6fc6b53260912b5bead2bd7c01e8d
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547844"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Összevont egyszeri bejelentkezés az Azure Active Directory-tanúsítványok kezelése

Ebben a cikkben bemutatjuk a gyakori kérdéseket és, hogy az Azure Active Directory (Azure AD) hoz létre, mint a szoftverszolgáltatások (SaaS) alkalmazások létrehozására összevont egyszeri bejelentkezés (SSO) a szoftver-tanúsítványokkal kapcsolatos információkat. Alkalmazások hozzáadása az Azure AD-alkalmazásgyűjtemény vagy egy katalógusban nem szereplő alkalmazást sablon használatával. Az alkalmazás konfigurálása az összevont egyszeri bejelentkezési beállítás használatával.

Ez a cikk csak az alkalmazásokat, amelyek révén az Azure AD egyszeri bejelentkezés használatára konfigurált fontos [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) összevonási.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatikusan létrehozott tanúsítvány katalógus és a katalógusban nem szereplő alkalmazásokhoz

Amikor egy új alkalmazás hozzáadása a katalógusból, és egy SAML-alapú bejelentkezés konfigurálása (kiválasztásával **egyszeri bejelentkezési** > **SAML** az alkalmazás áttekintése lapon), az Azure AD-hoz létre egy a tanúsítvány, amely a három évig érvényes. Az aktív tanúsítvány, mint a biztonsági tanúsítvány letöltése (**.cer**) fájl, lépjen vissza az oldal (**SAML-alapú bejelentkezés**), és válassza ki a letöltési hivatkozás a **SAML aláíró tanúsítvány** fejléc. A nyers (bináris) tanúsítványt vagy a Base64 (base 64 kódolású szöveges) tanúsítványt választhat. Katalógus alkalmazások esetében ez a szakasz is jeleníthet meg egy hivatkozást a tanúsítvány-ként való letöltéséhez összevonási metaadatainak XML (egy **.xml** fájl), attól függően, az alkalmazás követelményeinek.

![SAML aktív aláíró tanúsítvány letöltési beállítások](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Egy aktív vagy inaktív tanúsítvány kiválasztásával is letöltheti a **SAML-aláíró tanúsítvány** címsor **szerkesztése** ikon (a Ceruza), mely megjeleníti a **SAML aláíró tanúsítvány** lapot. Kattintson a három pontra (**...** ) mellett a kívánt tanúsítványt, töltse le, és válassza a tanúsítvány formátumot szeretne. Lehetősége van további adatvédelemmel ellátott levelezési (PEM) formátumú a tanúsítvány letöltéséhez. Ez a formátum akkor Base64 azonos, de egy **.pem** fájlnév-kiterjesztésű, amely a Windows, a tanúsítvány formátuma nem ismerhető fel.

![SAML-aláíró tanúsítvány letöltési beállítások (aktív és inaktív)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Testre szabhatja az összevonási tanúsítványok lejáratának dátumát és vihetők át az új tanúsítvány

Alapértelmezés szerint az Azure úgy konfigurálja a tanúsítványt, létrehozásakor automatikusan SAML egyszeri bejelentkezés konfigurálása során három év után lejár. Követően mentse a dátumot a tanúsítvány nem módosítható, mert a rendelkezésére:

1. Hozzon létre egy új tanúsítványt a kívánt dátumot.
2. Mentse az új tanúsítványt.
3. Töltse le az új tanúsítvány formátuma helytelen.
4. Töltse fel az új tanúsítványt az alkalmazáshoz.
5. Győződjön meg arról, az új tanúsítványt az Azure Active Directory portál aktív.

A következő két szakasz segítséget nyújt a következő lépésekkel.

### <a name="create-a-new-certificate"></a>Új tanúsítvány létrehozása

Először hozzon létre, és mentse az új tanúsítvány eltérő lejárati dátummal rendelkező:

1. Jelentkezzen be a [Azure Active Directory portálon](https://aad.portal.azure.com/). A **Azure Active Directory felügyeleti központ** lap jelenik meg.

2. A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet. A fiók a vállalati alkalmazások listája jelenik meg.

3. Válassza ki az érintett alkalmazás. Az alkalmazás áttekintő oldala jelenik meg.

4. Az alkalmazás áttekintés oldal bal oldali panelen válassza ki a **egyszeri bejelentkezési**.

5. Ha a **egyszeri bejelentkezési módszer** lap, válasszon **SAML**.

6. Az a **állítsa be egyszeri bejelentkezést az SAML - előzetes verzió** lapon, keresse meg a **SAML-aláíró tanúsítvány** címsor, és válassza ki a **szerkesztése** ikon (a Ceruza). A **SAML-aláíró tanúsítvány** lap, amely állapotát jeleníti meg (**aktív** vagy **inaktív**), lejárati dátumát, és minden egyes tanúsítvány ujjlenyomata (kivonat karakterláncot).

7. Válassza ki **új tanúsítvány**. Új sor jelenik meg a listáját, ahol a lejárati dátum az aktuális dátum után pontosan három év alapértelmezett értéke. (A módosítások még nem lett mentve, így továbbra is módosíthatja a lejárati dátumot.)

8. Az új tanúsítvány sorban a lejárati dátum oszlop fölé, és válassza ki a **dátum kiválasztása** (Naptár) ikonra. Naptár vezérlőelem jelenik meg, az új sor aktuális lejárati dátum a hónap napjait megjelenítése.

9. Új dátum beállításához használja a naptárvezérlőt. Minden dátum az aktuális dátumot és az aktuális dátum után három év között állíthatja be.

10. Kattintson a **Mentés** gombra. Az új tanúsítványt most állapottal jelenik meg **inaktív**, a lejárati dátum, amikor a kiválasztott, és a egy ujjlenyomatot.

11. Válassza ki a **X** térjen vissza a **állítsa be egyszeri bejelentkezést az SAML - előzetes verzió** lapot.

### <a name="upload-and-activate-a-certificate"></a>Töltse fel, és a egy tanúsítvány aktiválása

Ezután töltse le az új tanúsítványt a megfelelő formátumban, töltse fel az alkalmazáshoz, és aktívvá válik az Azure Active Directoryban:

1. Az alkalmazás további SAML-bejelentkezés a konfigurálási útmutató megtekintéséhez vagy:
   - Válassza a **beállítási útmutató** hivatkozásra kattintva egy külön böngészőablakot vagy-lapon, vagy
   - Ugrás a **beállítása** fejlécre, majd válassza **részletesen megtekintheti** egy oldalsávon megtekintéséhez.

2. Az útmutatóban vegye figyelembe a kódolási formátum, a tanúsítvány feltöltése szükséges.

3. Kövesse az utasításokat a [katalógus és a katalógusban nem szereplő alkalmazások automatikusan létrehozott tanúsítvány](#auto-generated-certificate-for-gallery-and-non-gallery-applications) korábbi szakaszában. Ebben a lépésben letölti a tanúsítványt a megadott kódolási formátum, az alkalmazás számára szükséges a feltöltéshez.

4. Ha szeretné az új tanúsítvány hozzá, lépjen vissza a **SAML-aláíró tanúsítvány** lapon, és az újonnan mentett tanúsítványt sorban kattintson a három pontra (**...** ), és válassza ki **tanúsítvány aktívvá**. Az új tanúsítvány állapotra vált **aktív**, és a egy állapotát módosításait a korábban aktív tanúsítvány **inaktív**.

5. Az alkalmazás SAML bejelentkezési konfigurációs utasításokat, megjelenik a korábban, feltöltheti az SAML-aláíró tanúsítvány a megfelelő kódolási formátum a következő továbbra is.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>E-mail értesítési címek a tanúsítvány lejárata

Az Azure AD elküldi egy e-mail-értesítés 60, 30 és 7 nap a SAML-tanúsítvány lejárta előtt. Ha értesítéseket szeretne kapni a több mint egy e-mail-címet adhat meg. Azt szeretné, hogy az értesítések küldését e-mail-címek megadása:

1. Az a **SAML-aláíró tanúsítvány** lapon kattintson a **értesítési e-mail-címek** fejléc. Alapértelmezés szerint ezt a címsort használja a rendszergazda, aki felvette az alkalmazás csak az e-mail-címe.

2. Alább a végső e-mail-címét írja be az e-mail-címet kell a tanúsítvány lejárati közzé az állapotfrissítéseket, és nyomja le az ENTER billentyűt.

3. Ismételje az előző lépést minden hozzáadni kívánt e-mail-cím.

4. Az összes törölni kívánt e-mail-cím, válassza ki a **törlése** ikon (a szemetes) az e-mail-cím mellett.

5. Kattintson a **Mentés** gombra.

Az értesítési e-mailt a aadnotification@microsoft.com. Az e-mailt a levélszemét helyet fogja elkerülése érdekében adja hozzá az ügyfelek e-mailt.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Újítsa meg egy tanúsítvány hamarosan lejár

Ha egy tanúsítvány hamarosan lejár, meg lehet újítani eredményezi, jelentős állásidő nélkül a felhasználók számára ez az eljárás használatával. A lejáró tanúsítvány megújítása:

1. Kövesse az utasításokat a [hozzon létre egy új tanúsítványt](#create-a-new-certificate) szakasz korábbi, a meglévő tanúsítvány, amely átfedésben van egy dátumot használata. A dátumnak a tanúsítvány lejárati által okozott állásidők mennyiségét korlátozza.

2. Ha az alkalmazás is automatikusan lecserélhetők egy tanúsítványt, állítsa be az új tanúsítvány aktív az alábbi lépéseket:
   1. Lépjen vissza a **SAML-aláíró tanúsítvány** lapot.
   2. Az újonnan mentett tanúsítványt sorban kattintson a három pontra (**...** ), majd **tanúsítvány aktívvá**.
   3. A következő két lépés kihagyható.

3. Ha az alkalmazás csak képes kezelni egy tanúsítvány egy időben, válasszon végrehajtani a következő lépés egy állásidő időköz. (Ellenkező esetben, ha az alkalmazás nem automatikusan felveszi az új tanúsítványt, de több mint egy aláíró tanúsítványt képes kezelni, végezheti a következő lépés bármikor.)

4. A régi tanúsítvány lejárta előtt kövesse az utasításokat a [és a tanúsítvány aktiválásához](#upload-and-activate-a-certificate) korábbi szakaszában.

5. Jelentkezzen be az alkalmazás győződjön meg arról, hogy a tanúsítvány megfelelően működik-e.

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](../saas-apps/tutorial-list.md)
* [Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
* [Egyszeri bejelentkezés az Azure Active Directory-alkalmazások](what-is-single-sign-on.md)
* [SAML-alapú egyszeri bejelentkezés az Azure Active Directory-alkalmazások hibakeresése](../develop/howto-v1-debug-saml-sso-issues.md)
