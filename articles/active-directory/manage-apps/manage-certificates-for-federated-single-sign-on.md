---
title: Összevonási tanúsítványok kezelése az Azure AD-ben | Microsoft dokumentumok
description: Megtudhatja, hogy miként szabhatja testre az összevonási tanúsítványok lejárati dátumát, és hogyan újíthatja meg a hamarosan lejáró tanúsítványokat.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de60dc5095ce4ab4d0219a388c445b08f544e1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159029"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Az összevont egyszeri bejelentkezés tanúsítványainak kezelése az Azure Active Directoryban

Ebben a cikkben az Azure Active Directory (Azure AD) által létrehozott tanúsítványokkal kapcsolatos gyakori kérdéseket és információkat ismerteti, amelyek a szoftver szolgáltatásként (SaaS) alkalmazások összevont egyszeri bejelentkezését (SSO) hozzák létre. Alkalmazások hozzáadása az Azure AD alkalmazásgyűjteményből vagy egy nem katalógus alkalmazássablon használatával. Konfigurálja az alkalmazást az összevont SSO beállítás használatával.

Ez a cikk csak az Azure AD [SSO-nak a security assertion markup language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) összevonáson keresztüli használatára konfigurált alkalmazásokra vonatkozik.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatikusan létrehozott tanúsítvány katalógus- és nem katalógusalkalmazásokhoz

Amikor új alkalmazást ad hozzá a katalógusból, és konfigurálja az SAML-alapú bejelentkezést (az egyszeri > **saml** kiválasztásával az alkalmazás áttekintése lapon), az Azure AD létrehoz egy tanúsítványt az alkalmazás, amely három évig érvényes. **Single sign-on** Ha az aktív tanúsítványt biztonsági tanúsítvány (**.cer**) fájlként szeretné letölteni, térjen vissza arra a lapra (**SAML-alapú bejelentkezés**), és válasszon egy letöltési hivatkozást az **SAML aláíró tanúsítvány** fejlécében. Választhat a nyers (bináris) tanúsítvány vagy a Base64 (base 64 kódolású szöveg) tanúsítvány között. A katalógusalkalmazások esetében ez a szakasz az alkalmazás követelményétől függően egy hivatkozást is megjeleníthet a tanúsítvány összevonási metaadat-XML -ként **(.xml** fájl) való letöltésére.

![SAML aktív aláíró tanúsítvány letöltési beállításai](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Aktív vagy inaktív tanúsítványt is letölthet, ha kiválasztja az **SAML aláíró tanúsítvány** fejlécének **Szerkesztésikonját** (ceruzát), amely az **SAML aláíró tanúsítvány** lapot jeleníti meg. Jelölje ki a letölteni kívánt tanúsítvány melletti három pontot (**...**), majd válassza ki a kívánt tanúsítványformátumot. A tanúsítványt további lehetőségként töltheti le pem formátumban. Ez a formátum megegyezik a Base64 formátummal, de **.pem** fájlnévkiterjesztéssel, amelyet a Windows nem ismer tanúsítványformátumként.

![SAML aláíró tanúsítvány letöltési beállításai (aktív és inaktív)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Az összevonási tanúsítvány lejárati dátumának testreszabása és átgörgetése új tanúsítványba

Alapértelmezés szerint az Azure konfigurálja a tanúsítvány lejár három év után, amikor automatikusan létre saml egyszeri bejelentkezési konfiguráció során jön létre. Mivel a mentés után nem módosíthatja a tanúsítvány dátumát, a következőket kell a következőkre váltania:

1. Hozzon létre egy új tanúsítványt a kívánt dátummal.
1. Mentse az új tanúsítványt.
1. Töltse le az új tanúsítványt a megfelelő formátumban.
1. Töltse fel az új tanúsítványt az alkalmazásba.
1. Tegye aktívvá az új tanúsítványt az Azure Active Directory portálon.

Az alábbi két szakasz segítséget nyújt a lépések végrehajtásához.

### <a name="create-a-new-certificate"></a>Új tanúsítvány létrehozása

Először hozzon létre és mentsen új tanúsítványt egy másik lejárati dátummal:

1. Jelentkezzen be az [Azure Active Directory portálra.](https://aad.portal.azure.com/) Megjelenik **az Azure Active Directory felügyeleti központ** lapja.
1. A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet. Megjelenik a fiókjában lévő vállalati alkalmazások listája.
1. Válassza ki az érintett alkalmazást. Megjelenik az alkalmazás áttekintő lapja.
1. Az alkalmazás áttekintése lap bal oldali ablaktáblájában válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Ha megjelenik **az Egyetlen bejelentkezési módszer kiválasztása** lap, válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML - Preview** lapon keresse meg az **SAML aláíró tanúsítvány** fejlécét, és válassza a **Szerkesztés** ikont (egy ceruzát). Megjelenik **az SAML aláíró tanúsítvány** lap, amely megjeleníti az állapotát (**aktív** vagy **inaktív**), lejárati dátumát és az ujjlenyomatot (kivonatoló karakterláncot).
1. Válassza az **Új tanúsítvány lehetőséget.** Egy új sor jelenik meg a tanúsítványlista alatt, ahol a lejárati dátum alapértelmezés szerint pontosan három évvel az aktuális dátum után. (A módosítások még nem lettek mentve, így továbbra is módosíthatja a lejárati dátumot.)
1. Az új tanúsítványsorban mutasson a lejárati dátum oszlopra, és válassza a **Dátum kiválasztása** ikont (naptár). Megjelenik egy naptárvezérlő, amely az új sor aktuális lejárati dátumának hónapnapjait jeleníti meg.
1. Új dátum beállításához használja a naptár vezérlőt. Az aktuális dátum és az aktuális dátum után három év között tetszőleges dátumot állíthat be.
1. Kattintson a **Mentés** gombra. Az új tanúsítvány most **inaktív**állapotú , a kiválasztott lejárati dátummal és egy ujjlenyomattal jelenik meg.
1. Válassza ki az X-et, ha vissza szeretne térni az **Egyszeri bejelentkezés beállítása SAML - Preview** oldalra. **X**

### <a name="upload-and-activate-a-certificate"></a>Tanúsítvány feltöltése és aktiválása

Ezután töltse le az új tanúsítványt a megfelelő formátumban, töltse fel az alkalmazásba, és tegye aktívvá az Azure Active Directoryban:

1. Tekintse meg az alkalmazás további SAML bejelentkezési konfigurációs utasításait a következő kén:

   - A **konfigurációs útmutató** hivatkozásának kiválasztása külön böngészőablakban vagy lapon való megtekintéshez, vagy
   - Lépjen a **beállítási** fejlécre, és válassza **a Nézet lépésenkénti utasításokat** az oldalsávban való megtekintéshez.

1. Az utasításokban jegyezze fel a tanúsítvány feltöltéséhez szükséges kódolási formátumot.
1. Kövesse az [automatikusan létrehozott tanúsítvány](#auto-generated-certificate-for-gallery-and-non-gallery-applications) a katalógus és a nem galéria alkalmazások szakaszban korábban. Ez a lépés az alkalmazás által feltöltéshez szükséges kódolási formátumban tölti le a tanúsítványt.
1. Ha át szeretne váltani az új tanúsítványra, lépjen vissza az **SAML aláíró tanúsítvány** lapra, és az újonnan mentett tanúsítványsorban jelölje ki a három pontot (**...**), és válassza a **Tanúsítvány aktívsá szolgáltatása**lehetőséget. Az új tanúsítvány állapota **Aktív**, a korábban aktív tanúsítvány pedig **Inaktív**állapotra változik .
1. Kövesse az alkalmazás SAML bejelentkezési konfigurációs utasításait, amelyeket korábban már megírt, hogy feltölthesse az SAML aláíró tanúsítványt a megfelelő kódolási formátumban.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>E-mail értesítési címek hozzáadása a tanúsítvány lejáratához

Az Azure AD küld egy e-mail értesítést 60, 30 és 7 nappal az SAML tanúsítvány lejárta előtt. Az értesítések fogadásához több e-mail címet is megadhat. Az értesítések elküldéséhez az e-mail cím(ek)megadásához:

1. Az **SAML aláíró tanúsítvány** lapon lépjen az **értesítési e-mail címek** fejlécére. Alapértelmezés szerint ez a fejléc csak az alkalmazást hozzáadó rendszergazda e-mail címét használja.
1. A végső e-mail cím alatt írja be azt az e-mail címet, amelynek meg kell kapnia a tanúsítvány lejárati értesítését, majd nyomja le az Enter billentyűt.
1. Ismételje meg az előző lépést minden hozzáadni kívánt e-mail címnél.
1. Minden törölni kívánt e-mail címhez válassza a **Törlés** ikont (szemetesgyűjtőt) az e-mail cím mellett.
1. Kattintson a **Mentés** gombra.

Az értesítést a rendszertől aadnotification@microsoft.comkapja meg. Annak érdekében, hogy az e-mail ne kerüljön a levélszemét helyére, adja hozzá ezt az e-mailt a névjegyalbumhoz.

## <a name="renew-a-certificate-that-will-soon-expire"></a>A hamarosan lejáró tanúsítvány megújítása

Ha egy tanúsítvány hamarosan lejár, megújíthatja azt egy olyan eljárással, amely nem eredményez jelentős leállást a felhasználók számára. Lejáró tanúsítvány megújítása:

1. Kövesse az utasításokat a [Létrehozása új tanúsítvány](#create-a-new-certificate) szakaszban korábban, egy dátum, amely átfedésben van a meglévő tanúsítványt. Ez a dátum korlátozza a tanúsítvány lejárata által okozott állásidő mennyiségét.
1. Ha az alkalmazás automatikusan át tud állítani egy tanúsítványt, állítsa az új tanúsítványt aktívra az alábbi lépésekkel:
   1. Lépjen vissza az **SAML aláíró tanúsítvány** lapra.
   1. Az újonnan mentett tanúsítványsorban jelölje ki a három pontot (**...**), majd válassza **a Tanúsítvány aktívvá váltatása**lehetőséget.
   1. Hagyja ki a következő két lépést.

1. Ha az alkalmazás egyszerre csak egy tanúsítványt tud kezelni, válasszon egy állásidőintervallumot a következő lépés végrehajtásához. (Ellenkező esetben, ha az alkalmazás nem veszi fel automatikusan az új tanúsítványt, de egynél több aláíró tanúsítványt is képes kezelni, a következő lépést bármikor végrehajthatja.)
1. A régi tanúsítvány lejárta előtt kövesse a Feltöltés című szakasz utasításait, [és aktiválja](#upload-and-activate-a-certificate) korábban a tanúsítványszakaszt.
1. Jelentkezzen be az alkalmazásba, és győződjön meg arról, hogy a tanúsítvány megfelelően működik.

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](../saas-apps/tutorial-list.md)
- [Alkalmazáskezelés az Azure Active Directory használatával](what-is-application-management.md)
- [Egyszeri bejelentkezés az alkalmazásokba az Azure Active Directoryban](what-is-single-sign-on.md)
- [Saml-alapú egyszeri bejelentkezés hibakeresése az Azure Active Directoryban lévő alkalmazásokba](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
