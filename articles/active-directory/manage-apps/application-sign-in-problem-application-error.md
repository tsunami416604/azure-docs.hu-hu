---
title: Bejelentkezés után hibaüzenet jelenik meg az alkalmazáslapon | Microsoft dokumentumok
description: Hogyan oldhatja meg az Azure AD bejelentkezéssel kapcsolatos problémákat, amikor az alkalmazás hibaüzenetet ad vissza.
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
ms.openlocfilehash: 9b8d20b31e96973a492355f0515d0532deea0ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185491"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Egy alkalmazáslap hibaüzenetet jelenít meg, miután a felhasználó bejelentkezett

Ebben a forgatókönyvben az Azure Active Directory (Azure AD) bejelentkezik a felhasználó. De az alkalmazás hibaüzenetet jelenít meg, és nem hagyja, hogy a felhasználó befejezze a bejelentkezési folyamatot. A probléma az, hogy az alkalmazás nem fogadta el az Azure AD által kiadott választ.

Számos oka lehet annak, hogy az alkalmazás nem fogadta el az Azure AD válaszát. Ha a hibaüzenet nem azonosítja egyértelműen, hogy mi hiányzik a válaszból, próbálkozzon a következőkkel:

-   Ha az alkalmazás az Azure AD-gyűjtemény, ellenőrizze, hogy követte-e az [SAML-alapú egyszeri bejelentkezés az Azure AD-ben alkalmazásokba](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)című lépéseit.

-   Az olyan eszközök használatával, mint a [Fiddler](https://www.telerik.com/fiddler) az SAML-kérelem, válasz és jogkivonat rögzítéséhez.

-   Küldje el az SAML választ az alkalmazás forgalmazójának, és kérdezze meg, mi hiányzik.

## <a name="attributes-are-missing-from-the-saml-response"></a>Az attribútumok hiányoznak az SAML válaszból

Ha hozzá szeretne adni egy attribútumot az Azure AD-konfigurációban, amelyet az Azure AD-válaszban fog elküldeni, kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be globális rendszergazdaként vagy társrendszergazdaként.

2. A bal oldali navigációs ablak tetején válassza a **Minden szolgáltatás** lehetőséget az Azure AD-bővítmény megnyitásához.

3. Írja be az **Azure Active Directory** kifejezést a szűrő keresőmezőjébe, majd válassza az Azure Active **Directory**lehetőséget.

4. Válassza ki a **vállalati alkalmazások** az Azure AD navigációs ablaktáblán.

5. Válassza **a Minden alkalmazás lehetőséget** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista**tetején. Állítsa a **Megjelenítés** beállítást "Minden alkalmazás" beállításra.

6. Válassza ki azt az alkalmazást, amelyet egyszeri bejelentkezéshez szeretne konfigurálni.

7. Az alkalmazás betöltése után válassza **az Egyszeri bejelentkezés** lehetőséget a navigációs ablakban.

8. A **Felhasználói attribútumok** csoportban jelölje be **az Összes többi felhasználói attribútum megtekintése és szerkesztése jelölőnégyzetet.** Itt módosíthatja, hogy mely attribútumokat küldje el az alkalmazásnak az SAML-jogkivonatban, amikor a felhasználók bejelentkeznek.

   Attribútum hozzáadása:

   1. Válassza **az Attribútum hozzáadása lehetőséget.** Írja be a Név ( **Név**) értéket, és válassza ki az **Értéket** a legördülő listából.

   1.  Kattintson a **Mentés** gombra. Az új attribútum megjelenik a táblázatban.

9. Mentse a konfigurációt.

   A következő alkalommal, amikor a felhasználó bejelentkezik az alkalmazásba, az Azure AD elküldi az új attribútumot az SAML válasz.

## <a name="the-app-doesnt-identify-the-user"></a>Az alkalmazás nem azonosítja a felhasználót

Az alkalmazásba való bejelentkezés sikertelen, mert az SAML-válaszból hiányzik egy attribútum, például egy szerepkör. Vagy sikertelen, mert az alkalmazás más formátumot vagy értéket vár a **NameID** (Felhasználói azonosító) attribútumhoz.

Ha [az Azure AD automatikus felhasználói kiépítést](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) használ a felhasználók létrehozásához, karbantartásához és eltávolításához az alkalmazásban, ellenőrizze, hogy a felhasználó ki van-e építve a SaaS-alkalmazásba. További információ: [Nincs felhasználó van kiépítve egy Azure AD Gallery-alkalmazás.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Attribútum hozzáadása az Azure AD alkalmazás konfigurációjához

A felhasználói azonosító értékének módosításához hajtsa végre az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be globális rendszergazdaként vagy társrendszergazdaként.

2. Válassza a navigációs ablak tetején a bal oldali összes **szolgáltatás** lehetőséget az Azure AD-bővítmény megnyitásához.

3. Írja be az **Azure Active Directory** kifejezést a szűrő keresőmezőjébe, majd válassza az Azure Active **Directory**lehetőséget.

4. Válassza ki a **vállalati alkalmazások** az Azure AD navigációs ablaktáblán.

5. Válassza **a Minden alkalmazás lehetőséget** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista**tetején. Állítsa a **Megjelenítés** beállítást "Minden alkalmazás" beállításra.

6. Válassza ki azt az alkalmazást, amelyet az SSO-hoz szeretne konfigurálni.

7. Az alkalmazás betöltése után válassza **az Egyszeri bejelentkezés** lehetőséget a navigációs ablakban.

8. A **Felhasználói attribútumok**csoportban válassza ki a felhasználó egyedi azonosítóját a **Felhasználói azonosító** legördülő listából.

## <a name="change-the-nameid-format"></a>A NameID formátum módosítása

Ha az alkalmazás a **NameID** (Felhasználói azonosító) attribútum egy másik formátumát várja, a NameID formátumának módosításához olvassa el a [NameID-formátum szerkesztése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) című témakört.

Az Azure AD kiválasztja a **NameID** attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML AuthRequest-ben kért formátum alapján. További információt az [egyszeri bejelentkezési SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)"NameIDPolicy" című szakaszában talál.

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Az alkalmazás más aláírási módszert vár az SAML-válaszhoz

Ha módosítani szeretné, hogy az AZURE AD mely részeit írja alá digitálisan az Azure AD, kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt,](https://portal.azure.com/) és jelentkezzen be globális rendszergazdaként vagy társrendszergazdaként.

2. Válassza a navigációs ablak tetején a bal oldali összes **szolgáltatás** lehetőséget az Azure AD-bővítmény megnyitásához.

3. Írja be az **Azure Active Directory** kifejezést a szűrő keresőmezőjébe, majd válassza az Azure Active **Directory**lehetőséget.

4. Válassza ki a **vállalati alkalmazások** az Azure AD navigációs ablaktáblán.

5. Válassza **a Minden alkalmazás lehetőséget** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista**tetején. Állítsa a **Megjelenítés** beállítást "Minden alkalmazás" beállításra.

6. Válassza ki azt az alkalmazást, amelyet egyszeri bejelentkezéshez szeretne konfigurálni.

7. Az alkalmazás betöltése után válassza **az Egyszeri bejelentkezés** lehetőséget a navigációs ablakban.

8. Az **SAML aláíró tanúsítvány csoportban**válassza **a Speciális tanúsítványaláírási beállítások megjelenítése**lehetőséget.

9. Válassza ki azt az **aláírási lehetőséget,** amelyet az alkalmazás az alábbi lehetőségek közül vár:

   * **SAML-válasz aláírása**
   * **SAML-válasz és állítás aláírása**
   * **SAML-állítás aláírása**

   A következő alkalommal, amikor a felhasználó bejelentkezik az alkalmazásba, az Azure AD aláírja a kiválasztott SAML-válasz részét.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Az alkalmazás az SHA-1 aláírási algoritmust várja

Alapértelmezés szerint az Azure AD aláírja az SAML-jogkivonatot a legbiztonságosabb algoritmus használatával. Azt javasoljuk, hogy ne módosítsa az aláírási algoritmus *TA-1,* kivéve, ha az alkalmazás igényel SHA-1.

Az aláíró algoritmus módosításához hajtsa végre az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt,](https://portal.azure.com/) és jelentkezzen be globális rendszergazdaként vagy társrendszergazdaként.

2. Válassza a navigációs ablak tetején a bal oldali összes **szolgáltatás** lehetőséget az Azure AD-bővítmény megnyitásához.

3. Írja be az **Azure Active Directory** kifejezést a szűrő keresőmezőjébe, majd válassza az Azure Active **Directory**lehetőséget.

4. Válassza ki a **vállalati alkalmazások** az Azure AD navigációs ablaktáblán.

5. Válassza **a Minden alkalmazás lehetőséget** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista**tetején. Állítsa a **Megjelenítés** beállítást "Minden alkalmazás" beállításra.

6. Válassza ki azt az alkalmazást, amelyet egyszeri bejelentkezéshez szeretne konfigurálni.

7. Az alkalmazás betöltése után válassza az **Egyszeri bejelentkezés** lehetőséget az alkalmazás bal oldalán található navigációs ablaktáblán.

8. Az **SAML aláíró tanúsítvány csoportban**válassza **a Speciális tanúsítványaláírási beállítások megjelenítése**lehetőséget.

9. Az **SHA-1** lehetőséget **választja aláírási algoritmusként.**

   A következő alkalommal, amikor a felhasználó bejelentkezik az alkalmazásba, az Azure AD aláírja az SAML-jogkivonatot az SHA-1 algoritmus használatával.

## <a name="next-steps"></a>További lépések
[Az SAML-alapú egyszeri bejelentkezés hibakeresése az Azure AD-ben lévő alkalmazásokba.](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
