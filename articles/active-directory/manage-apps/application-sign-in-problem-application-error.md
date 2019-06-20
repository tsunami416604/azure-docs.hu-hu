---
title: Hibaüzenet jelenik meg az alkalmazás lapjának bejelentkezés után |} A Microsoft Docs
description: Ha az alkalmazás hibaüzenetet ad vissza az Azure AD bejelentkezési problémák megoldása hogyan.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41ec1f510b028a2ffe2554bfcbd77bc439c4e79
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272947"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Alkalmazáslap egy hibaüzenetet jelenít meg, a felhasználó bejelentkezése után az

Ebben a forgatókönyvben az Azure Active Directory (Azure AD) a felhasználó jelentkezik be. Azonban az alkalmazás hibaüzenetet jelenít meg, és nem teszi lehetővé a felhasználó a bejelentkezési folyamat befejezéséhez. A probléma oka, hogy az alkalmazás nem fogadja el a választ, amely a kiállított Azure ad-ben.

Miért érdemes az alkalmazás nem fogadja el az Azure ad-ből a válasz több lehetséges oka is van. Ha a hibaüzenet nem egyértelműen azonosítható, mi az a válaszból hiányzik, próbálja meg a következőt:

-   Ha az alkalmazás az Azure AD katalógusából származó, győződjön meg arról, hogy követte a lépéseket a [az Azure ad-ben az SAML-alapú egyszeri bejelentkezés alkalmazások hibakeresése](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Egy hasonló eszközzel [Fiddler](https://www.telerik.com/fiddler) a SAML-kérelem, válasz és jogkivonat-rögzítéséhez.

-   Az alkalmazás gyártójával, a SAML-válasz küldése, és kérje meg, mi hiányzik.

## <a name="attributes-are-missing-from-the-saml-response"></a>Attribútumok hiányoznak az SAML-válasz

Attribútum hozzáadása az Azure AD választ kap az Azure AD-konfigurációban, kövesse az alábbi lépéseket:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy globális rendszergazdai vagy társadminisztrátorként.

2. A bal oldali navigációs ablak tetején válassza **minden szolgáltatás** megnyitásához az Azure AD-bővítményben.

3. Típus **Azure Active Directory** a szűrőt a keresési mezőbe, és válassza ki a **Azure Active Directory**.

4. Válassza ki **vállalati alkalmazások** az Azure ad-ben navigációs ablaktáblán.

5. Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista**. Állítsa be a **megjelenítése** beállítást az "Összes alkalmazás."

6. Válassza ki az alkalmazást, amelyet szeretne konfigurálni az egyszeri bejelentkezés.

7. Miután betölti az alkalmazást, válassza ki **egyszeri bejelentkezési** a navigációs ablaktáblán.

8. Az a **felhasználói attribútumok** szakaszban jelölje be **megtekintése és egyéb felhasználói attribútumok szerkesztése**. Itt módosíthatja a melyik attribútumok legyenek az alkalmazás SAML-jogkivonatban küld, amikor bejelentkeznek.

   Attribútum hozzáadása:

   1. Válassza ki **attribútum hozzáadása**. Adja meg a **neve**, és válassza ki a **érték** a legördülő listából.

   1.  Kattintson a **Mentés** gombra. Látni fogja a táblázatban az új attribútumot.

9. Mentse a konfigurációt.

   A felhasználó bejelentkezik az alkalmazásba, amikor legközelebb az Azure AD elküldi az új attribútumot, a SAML-válasz.

## <a name="the-app-doesnt-identify-the-user"></a>Az alkalmazás nem azonosítani a felhasználót

Az alkalmazás a bejelentkezés meghiúsul, mert a SAML-válasz hiányzik egy attribútum, például egy szerepkör. Vagy, sikertelen lesz, mivel az alkalmazás más formátumú vagy értéket vár. a **NameID** (felhasználóazonosító) attribútum.

Ha használ [Azure ad-ben automatizált felhasználókiépítése](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) szeretne létrehozni, karbantartását, és távolítsa el a felhasználókat az alkalmazásban, győződjön meg arról, hogy a felhasználó az SaaS-alkalmazás lett kiépítve. További információkért lásd: [egyetlen felhasználó sem lett létrehozva az Azure AD katalógusából származó alkalmazásba való](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Attribútum hozzáadása az Azure AD-alkalmazások konfigurálása

A felhasználói azonosító értékének módosításához kövesse az alábbi lépéseket:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy globális rendszergazdai vagy társadminisztrátorként.

2. Válassza ki **minden szolgáltatás** az Azure AD-bővítmény megnyitásához a bal oldali navigációs ablak tetején.

3. Típus **Azure Active Directory** a szűrőt a keresési mezőbe, és válassza ki a **Azure Active Directory**.

4. Válassza ki **vállalati alkalmazások** az Azure ad-ben navigációs ablaktáblán.

5. Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista**. Állítsa be a **megjelenítése** beállítást az "Összes alkalmazás."

6. Válassza ki az alkalmazást, amely az egyszeri bejelentkezéshez konfigurálandó.

7. Miután betölti az alkalmazást, válassza ki **egyszeri bejelentkezési** a navigációs ablaktáblán.

8. A **felhasználói attribútumok**, válassza ki, hogy a felhasználó egyedi azonosítója a **felhasználóazonosító** legördülő listából.

## <a name="change-the-nameid-format"></a>Módosítsa a NameID-formátum

Ha az alkalmazás egy másik formátumú vár a **NameID** (felhasználói azonosító) attribútummal, lásd: [szerkesztési nameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization.md#editing-nameid) nameid-formátumához módosításához.

Az Azure AD kiválasztása formátumát a **NameID** (felhasználóazonosító) attribútum alapján a kiválasztott érték, vagy a SAML AuthRequest az alkalmazás által kért formátumban. További információkért lásd: a "NameIDPolicy" szakaszában [egyszeri bejelentkezéses SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Az alkalmazás vár módszert más aláírás a SAML-válasz

Az SAML-jogkivonat mely részeit írja alá digitálisan az Azure AD módosításához kövesse az alábbi lépéseket:

1. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és jelentkezzen be egy globális rendszergazdai vagy társadminisztrátorként.

2. Válassza ki **minden szolgáltatás** az Azure AD-bővítmény megnyitásához a bal oldali navigációs ablak tetején.

3. Típus **Azure Active Directory** a szűrőt a keresési mezőbe, és válassza ki a **Azure Active Directory**.

4. Válassza ki **vállalati alkalmazások** az Azure ad-ben navigációs ablaktáblán.

5. Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazásra, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista**. Állítsa be a **megjelenítése** beállítást az "Összes alkalmazás."

6. Válassza ki az alkalmazást, amelyet szeretne konfigurálni az egyszeri bejelentkezés.

7. Miután betölti az alkalmazást, válassza ki **egyszeri bejelentkezési** a navigációs ablaktáblán.

8. A **SAML-aláíró tanúsítvány**válassza **speciális tanúsítvány-aláírási beállítások megjelenítése**.

9. Válassza ki a **aláírási beállítás** , amelyeket az alkalmazás vár, ezek a lehetőségek közül:

   * **SAML-válasz aláírása**
   * **SAML-válasz és -előfeltétel aláírása**
   * **SAML-előfeltétel aláírása**

   Amikor legközelebb a felhasználó bejelentkezik az alkalmazásba, az Azure AD alá fogja írni a kiválasztott SAML-válasz részét.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Az alkalmazás vár az SHA-1 algoritmust

Alapértelmezés szerint az Azure AD SAML-jogkivonatban aláírja a legtöbb biztonságos algoritmus használatával. Azt javasoljuk, hogy nem módosítja az aláíró algoritmus *SHA-1* , kivéve, ha az alkalmazásnak szüksége van az SHA-1.

Az aláíró algoritmus módosításához kövesse az alábbi lépéseket:

1. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és jelentkezzen be egy globális rendszergazdai vagy társadminisztrátorként.

2. Válassza ki **minden szolgáltatás** az Azure AD-bővítmény megnyitásához a bal oldali navigációs ablak tetején.

3. Típus **Azure Active Directory** a szűrőt a keresési mezőbe, és válassza ki a **Azure Active Directory**.

4. Válassza ki **vállalati alkalmazások** az Azure ad-ben navigációs ablaktáblán.

5. Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazásra, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista**. Állítsa be a **megjelenítése** beállítást az "Összes alkalmazás."

6. Válassza ki az alkalmazást, amelyet szeretne konfigurálni az egyszeri bejelentkezés.

7. Miután betölti az alkalmazást, válassza ki **egyszeri bejelentkezési** az alkalmazás a bal oldali navigációs panelről.

8. A **SAML-aláíró tanúsítvány**válassza **speciális tanúsítvány-aláírási beállítások megjelenítése**.

9. Válassza ki **SHA-1** , a **aláíró algoritmus**.

   A felhasználó bejelentkezik az alkalmazásba, amikor legközelebb az Azure AD fog használatával írja alá az SAML-jogkivonat az SHA-1 algoritmust.

## <a name="next-steps"></a>További lépések
[Az Azure ad-ben az SAML-alapú egyszeri bejelentkezés alkalmazások hibakeresése](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
