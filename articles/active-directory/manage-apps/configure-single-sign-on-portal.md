---
title: Egyszeri bejelentkezés konfigurálása – Azure Active Directory | Microsoft Docs
description: Ez az oktatóanyag az Azure Portalon konfigurál SAML-alapú egyszeri bejelentkezést egy alkalmazáshoz az Active Directory (Azure AD) használatával.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3d96799e69e2fdef3a4ffd1a436727e6a58da79
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565988"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>Oktatóanyag: Az Azure Active Directory a SAML-alapú egyszeri bejelentkezés az alkalmazás konfigurálása

Ez az oktatóanyag az [Azure Portalon](https://portal.azure.com) konfigurál SAML-alapú egyszeri bejelentkezést egy alkalmazáshoz az Active Directory (Azure AD) használatával. Ez az oktatóanyag során egy [alkalmazásspecifikus oktatóanyag](../saas-apps/tutorial-list.md) nem érhető el. 

Az oktatóanyag az Azure Portalt használja a következőkhöz:

> [!div class="checklist"]
> * Az SAML-alapú egyszeri bejelentkezési mód kiválasztása
> * Alkalmazásspecifikus tartomány és URL-címek konfigurálása
> * Felhasználói attribútumok konfigurálása
> * SAML aláíró tanúsítvány létrehozása
> * Felhasználók hozzárendelése az alkalmazáshoz
> * Az alkalmazás konfigurálása SAML-alapú egyszeri bejelentkezéshez
> * Az SAML-beállítások tesztelése

## <a name="before-you-begin"></a>Előkészületek

1. Ha az alkalmazás nincs hozzáadva az Azure AD-bérlővel, [a rövid útmutató: Vegye fel egy alkalmazást az Azure AD-bérlő](add-application-portal.md).

2. Az ismertetett információk, kérje meg az alkalmazás szállítójához [alapszintű SAML-beállítások konfigurálása](#configure-basic-saml-options).

3. Ebben az oktatóanyagban a lépéseket teszteléséhez használja a egy nem éles környezetben. Ha nem rendelkezik nem éles Azure AD-környezettel, szerezze be az [egy hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/).

4. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy felhőalapú alkalmazás, vagy egy alkalmazás az Azure AD-bérlő rendszergazdája.

## <a name="select-a-single-sign-on-mode"></a>Egyszeri bejelentkezési mód kiválasztása

Miután hozzáadott egy alkalmazást az Azure AD-bérlővel, készen áll az egyszeri bejelentkezés az alkalmazás konfigurálása.

Az egyszeri bejelentkezés beállításainak megnyitása:

1. Az a [az Azure portal](https://portal.azure.com), válassza a bal oldali navigációs panelen, **Azure Active Directory**. 

2. A **kezelés** a a **Azure Active Directory** navigációs panelt, amely akkor jelenik meg, válassza ki **vállalati alkalmazások**. Megjelenik egy véletlenszerűen vett minta az alkalmazások az Azure AD-bérlőben. 

3. Az a **alkalmazástípus** menüjében válassza **minden alkalmazás**, majd válassza ki **alkalmaz**.

4. Adja meg az alkalmazás nevét, amelyhez egyszeri bejelentkezést szeretne konfigurálni. Beírhatja például **GitHub-tesztelési** konfigurálnia az alkalmazást, akkor a [alkalmazás hozzáadása](add-application-portal.md) rövid.  

     ![Az alkalmazás keresősávba megjelenítő képernyőkép.](media/configure-single-sign-on-portal/azure-portal-application-search.png)

5. Válassza ki az alkalmazást, amelynek szeretné az egyszeri bejelentkezés konfigurálása.

6. Alatt a **kezelés** szakaszban jelölje be **egyszeri bejelentkezési**. 

7. Válassza ki **SAML** egyszeri bejelentkezés konfigurálásához. A **állítsa be egyszeri bejelentkezést az SAML - előzetes verzió** lap jelenik meg.

## <a name="configure-basic-saml-options"></a>Alapszintű SAML-beállítások konfigurálása

A tartomány és az URL-címek konfigurálása:

1. Lépjen kapcsolatba az alkalmazás forgalmazójával, és kérje el a következő beállítások adatait:

    | Konfigurációs beállítás | SP által kezdeményezve | Identitásszolgáltató által kezdeményezve | Leírás |
    |:--|:--|:--|:--|
    | Azonosító (entitásazonosító) | Néhány alkalmazáshoz szükséges | Néhány alkalmazáshoz szükséges | Egyedi módon azonosítja az alkalmazást, amelyhez az egyszeri bejelentkezést konfigurálja. Azure ad-ben az azonosító az alkalmazás SAML-jogkivonat célközönség paraméterként küld. Ellenőrizze, hogy az alkalmazás várható. Ez az érték az alkalmazás által megadott SAML-metaadatok entitásazonosítójaként is megjelenik.|
    | Válasz URL-cím | Optional | Szükséges | Megadja, hogy az alkalmazás hová várja az SAML-jogkivonatot. A válasz URL-címet más néven a tényfeldolgozó szolgáltatás (Assertion Consumer Service, ACS) URL-címének hívják. |
    | Bejelentkezési URL-cím | Szükséges | Ne adjon meg | Amikor egy felhasználó megnyitja ezt az URL-címet, a szolgáltató átirányítja az Azure AD-re a felhasználó hitelesítése és beléptetése érdekében. Az Azure AD az URL-cím használatával indítsa el az alkalmazást az Office 365 vagy az Azure AD hozzáférési Panel. Ha üres, az Azure AD egyszeri bejelentkezés elindításához, amikor egy felhasználó elindítja az alkalmazást az identitásszolgáltató támaszkodik.|
    | Továbbítási állapot | Optional | Optional | Megadja az alkalmazásnak, hogy hová irányítsa át a felhasználót a hitelesítés befejezése után. Az érték általában az alkalmazás érvényes URL-címet. Egyes alkalmazások használják, ez a mező eltérően. További információt az alkalmazás forgalmazójától kérhet.
    | Kijelentkezési URL | Optional | Optional | Az alkalmazásnak a SAML kijelentkezési válaszok elküldésére használatosak.


2. Az alapvető SAML-konfigurációs beállítások szerkesztéséhez válassza ki a **szerkesztése** (a Ceruza) ikonra a jobb felső sarkában a **alapszintű SAML-konfigurációja** szakaszban.

     ![Tanúsítványok konfigurálása](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

3. Az oldalon a megfelelő mezőkben adja meg az információkat, az 1. lépésben az alkalmazás gyártója által biztosított.

4. A lap tetején válassza **mentése**.

## <a name="configure-user-attributes-and-claims"></a>Felhasználói attribútumokról és jogcímekről konfigurálása 

Szabályozhatja, hogy milyen információ az Azure AD az alkalmazásnak az SAML-jogkivonatban küld, amikor egy felhasználó bejelentkezik. Ezt az információt a felhasználói attribútumok konfigurálásával szabályozható. Például konfigurálhatja a Azure AD küldjön a felhasználó nevét, e-mail és alkalmazott azonosítója, az alkalmazás a felhasználó bejelentkezésekor. 

Ezen attribútumok megadása kötelező vagy opcionális is lehet az egyszeri bejelentkezés megfelelő működése érdekében. További információt az [adott alkalmazásra vonatkozó oktatóanyagban](../saas-apps/tutorial-list.md) vagy az alkalmazás forgalmazójától kaphat.

1. Felhasználói attribútumokról és jogcímekről szerkesztéséhez válassza ki a **szerkesztése** (a Ceruza) ikonra a jobb felső sarkában a **felhasználói attribútumokról és jogcímekről** szakaszban.

   A **neve azonosító értékét** van beállítva, alapértelmezett értékkel *user.principalname*. A felhasználói azonosító egyedi módon azonosítja az alkalmazás egyes felhasználóit. Ha például az e-mail-cím a felhasználónév és az egyedi azonosító is egyben, állítsa be a *user.mail* értéket.

2. Módosíthatja a **neve azonosító értékét**, jelölje be a **szerkesztése** (a Ceruza) ikonra a **neve azonosító értékét** mező. A megfelelő módosításokat az azonosító formátuma és a forrás, igény szerint. Ha elkészült, mentse a módosításokat. Jogcímek testreszabása kapcsolatos további információkért lásd: a [vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása](../develop/active-directory-saml-claims-customization.md) útmutató cikk.

3. Jogcím hozzáadásához válassza **hozzáadása új jogcímet** az oldal tetején. Adja meg a **neve** , és válassza ki a megfelelő forrás. Ha a **attribútum** forrás, kell választania a **forrásattribútum** is használni szeretné. Ha a **fordítási** forrás, kell választania a **átalakítási** és **paraméter 1** használni kívánt.

4. Kattintson a **Mentés** gombra. Az új jogcímet a tábla jelenik meg.
 
## <a name="generate-a-saml-signing-certificate"></a>Hozzon létre egy SAML-aláíró tanúsítvány

Az Azure AD tanúsítvánnyal írja alá azokat az SAML-jogkivonatokat, amelyeket elküld az alkalmazásnak. 

1. Új tanúsítvány létrehozásához válassza a **szerkesztése** (a Ceruza) ikonra a jobb felső sarkában a **SAML-aláíró tanúsítvány** szakaszban.

2. Az a **SAML-aláíró tanúsítvány** szakaszban jelölje be **új tanúsítvány**.

3. Az új tanúsítvány sorban jelenik meg, állítsa be a **lejárati dátum**. Rendelkezésre álló konfigurációs lehetőségekkel kapcsolatos további információkért lásd: a [speciális tanúsítvány-aláírási beállítások](certificate-signing-options.md) cikk.

4. Válassza ki **mentése** felső részén a **SAML-aláíró tanúsítvány** szakaszban. 

## <a name="assign-users-to-the-application"></a>Felhasználók hozzárendelése az alkalmazáshoz

Célszerű az egyszeri bejelentkezés több felhasználóval vagy csoporttal tesztelni, mielőtt megvalósítaná az alkalmazás a szervezet számára.

> [!NOTE]
>
> Ezeket a lépéseket, hogy igénybe a **felhasználók és csoportok** konfigurációs szakaszhoz a portálon. Amikor elkészült, lépjen vissza a kell a **egyszeri bejelentkezési** szakasz az oktatóanyag elvégzéséhez.

Felhasználó vagy csoport hozzárendelése az alkalmazáshoz:

1. Ha még nincs megnyitva, nyissa meg az alkalmazás a portálon.
2. A bal oldali navigációs panelen az alkalmazáshoz, jelölje be a **felhasználók és csoportok**.
3. Válassza ki **felhasználó hozzáadása**.
4. Az a **hozzárendelés hozzáadása** szakaszban jelölje be **felhasználók és csoportok**.
5. Egy adott felhasználó megkereséséhez írja be a felhasználói nevet a **tag kiválasztása vagy egy külső felhasználó meghívása** mezőbe. Ezután válassza ki a felhasználó profilfényképének vagy embléma, és válassza a **kiválasztása**. 
6. Az a **hozzárendelés hozzáadása** szakaszban jelölje be **hozzárendelése**. Amikor végzett, a kijelölt felhasználók megjelennek a **felhasználók és csoportok** listája.

## <a name="set-up-the-application-to-use-azure-ad"></a>Azure AD használata az alkalmazás beállítása

Már majdnem kész.  Utolsó lépésként szeretne használni az Azure AD SAML identitás-szolgáltatóként az alkalmazás beállítása. 

1. Görgessen le a **beállítása <applicationName>**  szakaszban. A jelen oktatóanyag esetében ez a szakasz hívja **állítsa be a GitHub-tesztelési**. 
2. Ebben a szakaszban szereplő minden sornál másolja az értéket. Ezt követően illessze be az egyes értékek a megfelelő sort a **alapszintű SAML-konfigurációja** szakaszban. Például másolja a **bejelentkezési URL-cím** értéket a **állítsa be a GitHub-tesztelési** szakaszt, és illessze be azt a **bejelentkezési URL-** mezőbe a **alapszintű SAML-konfigurációja**  szakaszt, és így tovább.
3. Ha korábban beillesztett értékek a megfelelő mezőkbe, **mentése**.

## <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Készen áll a beállítások teszteléséhez.  

1. Nyissa meg az alkalmazás egyszeri bejelentkezési beállításait. 
2. Görgessen a **egyszeri bejelentkezésre az ellenőrzése <applicationName>**  szakaszban. A jelen oktatóanyag esetében ez a szakasz hívja **állítsa be a GitHub-tesztelési**.
3. Válassza ki **teszt**. Megjelennek a tesztelési beállítások.
4. Válassza ki **jelentkezzen be a jelenlegi felhasználói fiók**. Ez a vizsgálat Ha egyszeri bejelentkezést működik, a rendszergazda először megtekintheti, hogy

Ha hiba történt, hibaüzenet jelenik meg. Hajtsa végre a következő lépéseket:

1. Másolja ki és illessze be a részleteket a **hiba részleteit ismertető** mezőbe.

    ![Feloldási útmutatás beolvasása](media/configure-single-sign-on-portal/error-guidance.png)

2. Válassza ki **megoldási útmutató**. Az alapvető ok és megoldás útmutatást jelennek meg.  Ebben a példában a felhasználó az alkalmazás nem lett hozzárendelve.

3. Olvassa el a megoldási útmutató, és ezután, ha lehetséges, hárítsa el a problémát.

4. Futtassa ismét a tesztet, amíg sikeresnek nem bizonyul.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban az alkalmazás egyszeri bejelentkezési beállítások konfigurálva. A konfigurálás befejezése után felhasználót rendelt az alkalmazáshoz, és úgy konfigurálta az alkalmazást, hogy SAML-alapú egyszeri bejelentkezést használjon. Amikor mindezekkel végzett, ellenőrizte, hogy az SAML-bejelentkezés megfelelően működik-e.

A következőket hajtotta végre:
> [!div class="checklist"]
> * Kiválasztott az SAML-t egyszeri bejelentkezési módként
> * Kapcsolatba lépett az alkalmazás forgalmazójával a tartomány és az URL-címek konfigurálása érdekében
> * Felhasználói attribútumokat konfigurált
> * SAML aláíró tanúsítványt hozott létre
> * Manuálisan felhasználókat vagy csoportokat rendelt az alkalmazáshoz
> * Az alkalmazás SAML identitás-szolgáltatóként az Azure AD használatára konfigurálva
> * Tesztelte az SAML-alapú egyszeri bejelentkezést

Szeretné visszaállítani az alkalmazás a szervezet több felhasználó számára, használja a felhasználók automatikus átadása.

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan rendelhet hozzá felhasználókat automatikus átadással](configure-automatic-user-provisioning-portal.md)


