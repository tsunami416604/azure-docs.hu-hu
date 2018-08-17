---
title: Egyszeri bejelentkezés konfigurálása – Azure Active Directory | Microsoft Docs
description: Ez az oktatóanyag az Azure Portalon konfigurál SAML-alapú egyszeri bejelentkezést egy alkalmazáshoz az Active Directory (Azure AD) használatával.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 08/09/2018
ms.author: barbkess
ms.reviewer: arvinh,luleon
ms.openlocfilehash: b0180f162996c5fc4647071feaf02d42320b7c9a
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036380"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>Oktatóanyag: SAML-alapú egyszeri bejelentkezés konfigurálása egy alkalmazáshoz az Azure Active Directory használatával

Ez az oktatóanyag az [Azure Portalon](https://portal.azure.com) konfigurál SAML-alapú egyszeri bejelentkezést egy alkalmazáshoz az Active Directory (Azure AD) használatával. Ezt az oktatóanyagot olyan alkalmazások konfigurálásához használja, amelyek nem rendelkeznek [alkalmazásspecifikus oktatóanyaggal](../saas-apps/tutorial-list.md). 


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

1. Ha az alkalmazás még nincs hozzáadva az Azure AD-bérlőhöz, lásd: [Rövid útmutató: Alkalmazás hozzáadása az Azure Active Directory-bérlőhöz](add-application-portal.md).

2. Kérje el az alkalmazás forgalmazójától a [tartomány és az URL-címek konfigurálásával](#configure-domain-and-urls) kapcsolatos szakaszban található adatokat.

3. Javasoljuk, hogy az oktatóanyag lépéseinek teszteléséhez ne éles környezetet használjon. Ha nem rendelkezik nem éles Azure AD-környezettel, szerezze be az [egy hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/).

4. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure AD-bérlő globális rendszergazdájaként, felhőalkalmazás- vagy alkalmazás-rendszergazdaként.

## <a name="select-a-single-sign-on-mode"></a>Egyszeri bejelentkezési mód kiválasztása

Miután hozzáadta az alkalmazást az Azure AD-bérlőhöz, készen áll az egyszeri bejelentkezés konfigurálására.

Az egyszeri bejelentkezés beállításainak megnyitása:

1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs paneljén kattintson az **Azure Active Directory** elemre. 

2. Az **Azure Active Directory** panelen kattintson a **Vállalati alkalmazások** elemre. Megnyílik a **Minden alkalmazás** panel, amely az Azure AD-bérlőn található alkalmazások véletlenszerű mintáját mutatja. 

3. Az **Alkalmazás típusa** menüben válassza a **Minden alkalmazás** lehetőséget, majd kattintson az **Alkalmaz** gombra.

4. Adja meg az alkalmazás nevét, amelyhez egyszeri bejelentkezést szeretne konfigurálni. Válassza ki a saját alkalmazását, vagy használja az [alkalmazás hozzáadásával](add-application-portal.md) kapcsolatos rövid útmutatóban hozzáadott GitHub-tesztalkalmazást.

5. Kattintson az **Egyszeri bejelentkezés** elemre. Az **Egyszeri bejelentkezési mód** területen az **SAML-alapú bejelentkezés** jelenik meg alapértelmezett beállításként. 

    ![Beállítási lehetőségek](media/configure-single-sign-on-portal/config-options.png)

6. Kattintson a panel tetején lévő **Mentés** elemre. 

## <a name="configure-domain-and-urls"></a>Tartomány és URL-címek konfigurálása

A tartomány és az URL-címek konfigurálása:

1. Lépjen kapcsolatba az alkalmazás forgalmazójával, és kérje el a következő beállítások adatait:

    | Konfigurációs beállítás | SP által kezdeményezve | Identitásszolgáltató által kezdeményezve | Leírás |
    |:--|:--|:--|:--|
    | Bejelentkezési URL-cím | Kötelező | Ne adja meg | Amikor egy felhasználó megnyitja ezt az URL-címet, a szolgáltató átirányítja az Azure AD-re a felhasználó hitelesítése és beléptetése érdekében. Az Azure AD az URL-címmel indítja el az alkalmazást az Office 365-ről és az Azure AD hozzáférési panelről. Ha nincs megadva, az Azure AD identitásszolgáltató által kezdeményezett egyszeri bejelentkezést hajt végre, amikor egy felhasználó elindítja az alkalmazást az Office 365-ből, az Azure AD hozzáférési panelről vagy az Azure AD egyszeri bejelentkezési URL-címről.|
    | Azonosító (entitásazonosító) | Néhány alkalmazáshoz szükséges | Néhány alkalmazáshoz szükséges | Egyedi módon azonosítja az alkalmazást, amelyhez az egyszeri bejelentkezést konfigurálja. Az Azure AD az SAML-jogkivonat Audience (célközönség) paramétereként küldi vissza az azonosítót az alkalmazásnak, amelyet az alkalmazásnak érvényesítenie kell. Ez az érték az alkalmazás által megadott SAML-metaadatok entitásazonosítójaként is megjelenik.|
    | Válasz URL-cím | Nem kötelező megadni | Kötelező | Megadja, hogy az alkalmazás hová várja az SAML-jogkivonatot. A válasz URL-címet más néven a tényfeldolgozó szolgáltatás (Assertion Consumer Service, ACS) URL-címének hívják. |
    | Továbbítási állapot | Nem kötelező megadni | Nem kötelező megadni | Megadja az alkalmazásnak, hogy hová irányítsa át a felhasználót a hitelesítés befejezése után. Az érték általában az alkalmazás érvényes URL-címe, de néhány alkalmazás másképpen használja ezt a mezőt. További információt az alkalmazás forgalmazójától kérhet.

2. Adja meg az információkat. Az összes beállítás megtekintéséhez kattintson a **Speciális URL-beállítások megjelenítése** elemre.

    ![Beállítási lehetőségek](media/configure-single-sign-on-portal/config-urls.png)

3. Kattintson a panel tetején lévő **Mentés** gombra.

4. Ebben a szakaszban van egy **SAML-beállítások tesztelése** gomb. Ezt a tesztet az oktatóanyag későbbi részében fogja futtatni, az [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) szakaszban.

## <a name="configure-user-attributes"></a>Felhasználói attribútumok konfigurálása

A felhasználói attribútumokkal szabályozhatja, hogy az Azure AD milyen információkat küldjön az alkalmazásnak. Az Azure AD elküldheti például az alkalmazásnak a felhasználó nevét, e-mail-címét és alkalmazotti azonosítóját. Az Azure AD a felhasználói attribútumokat SAML-jogkivonatban küldi el az alkalmazásnak minden egyes alkalommal, amikor egy felhasználó bejelentkezik. 

Ezen attribútumok megadása kötelező vagy opcionális is lehet az egyszeri bejelentkezés megfelelő működése érdekében. További információt az [adott alkalmazásra vonatkozó oktatóanyagban](../saas-apps/tutorial-list.md) vagy az alkalmazás forgalmazójától kaphat.

1. Az összes lehetőség megtekintéséhez kattintson az **Egyéb felhasználói attribútumok megtekintése és szerkesztése** elemre.

    ![Felhasználói attribútumok konfigurálása](media/configure-single-sign-on-portal/config-user-attributes.png)

2. Adja meg a **felhasználói azonosítót**.

    A felhasználói azonosító egyedi módon azonosítja az alkalmazás egyes felhasználóit. Ha például az e-mail-cím a felhasználónév és az egyedi azonosító is egyben, állítsa be a *user.mail* értéket.

3. Az SAML-jogkivonat további attribútumaiért kattintson az **Egyéb felhasználói attribútumok megtekintése és szerkesztése** elemre.

4. Ha attribútumot szeretne adni az **SAML-jogkivonat attribútumaihoz**, kattintson az **Attribútum hozzáadása** lehetőségre. Írja be a **nevet**, és válassza ki a menüből az **értéket**.

5. Kattintson a **Mentés** gombra. Megjelenik az új attribútum a táblán.
 
## <a name="create-a-saml-signing-certificate"></a>SAML aláíró tanúsítvány létrehozása

Az Azure AD tanúsítvánnyal írja alá azokat az SAML-jogkivonatokat, amelyeket elküld az alkalmazásnak. 

1. Az összes lehetőség megtekintéséhez kattintson a **Speciális tanúsítvány-aláírási beállításokat megjelenítő** elemre.

    ![Tanúsítványok konfigurálása](media/configure-single-sign-on-portal/config-certificate.png)

2. Tanúsítvány konfigurálásához kattintson az **Új tanúsítvány létrehozása** elemre.

3. Az **Új tanúsítvány létrehozása** panelen állítsa be a lejárat dátumát, és kattintson a **Mentés** gombra.

4. Kattintson az **új tanúsítványt aktívvá tévő** elemre.

5. További tudnivalókért lásd a [speciális tanúsítvány-aláírási lehetőségekkel](certificate-signing-options.md) foglalkozó szakaszt.

6. Az eddig elvégzett módosítások megőrzéséhez kattintson az **Egyszeri bejelentkezés** panel tetején lévő **Mentés** elemre. 

## <a name="assign-users-to-the-application"></a>Felhasználók hozzárendelése az alkalmazáshoz

A Microsoft azt javasolja, hogy mielőtt az alkalmazást bevezetné a cégen vagy intézményen belül, több felhasználóval vagy csoporttal tesztelje az egyszeri bejelentkezést.

Felhasználó vagy csoport hozzárendelése az alkalmazáshoz:

1. Ha még nincs megnyitva, nyissa meg az alkalmazást a portálon.
2. Az alkalmazás bal oldali panelén kattintson a **Felhasználók és csoportok** lehetőségre.
3. Kattintson a **Felhasználó hozzáadása** parancsra.
4. A **Hozzárendelés felvétele** panelen kattintson a **Felhasználók és csoportok** elemre.
5. Egy adott felhasználó megkereséséhez írja be a felhasználó nevét a **Kiválasztás** mezőbe, jelölje be a felhasználó profilképe vagy emblémája melletti jelölőnégyzetet, és kattintson a **Kiválasztás** gombra. 
6. Keresse meg a jelenlegi felhasználónevet, és válassza ki. Igény szerint több felhasználót is kiválaszthat.
7. A **Hozzárendelés felvétele** panelen kattintson a **Hozzárendelés** gombra. Amikor végzett, a kiválasztott felhasználók megjelennek a **Felhasználók és csoportok** listában.

## <a name="configure-the-application-to-use-azure-ad"></a>Az alkalmazás konfigurálása az Azure AD használatára

Már majdnem kész.  Végső lépésként úgy kell konfigurálnia az alkalmazást, hogy az Azure AD-t használja SAML-identitásszolgáltatóként. 

1. Görgessen le az alkalmazás **Egyszeri bejelentkezés** panelének végére. 

    ![Az alkalmazás konfigurálása](media/configure-single-sign-on-portal/configure-app.png)

2. Kattintson a portálon az **Alkalmazás konfigurálása** elemre, és kövesse az utasításokat.
3. Manuálisan hozzon létre felhasználói fiókokat az alkalmazásban az egyszeri bejelentkezés teszteléséhez. Hozza létre [az előző szakaszban](#assign-users-to-the-application) az alkalmazáshoz rendelt felhasználói fiókokat.   Amikor készen áll az alkalmazás bevezetésére a cégen belül, azt javasoljuk, hogy automatikus felhasználó-átadással automatikusan hozzon létre felhasználói fiókokat az alkalmazásban.

## <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Készen áll a beállítások tesztelésére.  

1. Nyissa meg az alkalmazás egyszeri bejelentkezési beállításait. 
2. Görgessen a **Tartomány és URL-címek konfigurálása** szakaszra.
2. Kattintson az **SAML-beállítások tesztelése** elemre. Megjelennek a tesztelési beállítások.

    ![Az egyszeri bejelentkezés tesztelésének beállításai](media/configure-single-sign-on-portal/test-single-sign-on.png) 

3. Kattintson a **Bejelentkezés az aktuális felhasználóként** elemre. Így letesztelheti, hogy az egyszeri bejelentkezés működik-e Önnek, mint rendszergazdának.
4. Hiba esetén megjelenik egy hibaüzenet. Másolja ki és illessze be a részleteket a **hiba részleteit ismertető** mezőbe.

    ![Feloldási útmutatás beolvasása](media/configure-single-sign-on-portal/error-guidance.png)

5. Kattintson a **megoldási útmutatást kérő** lehetőségre. Megjelenik a hiba oka és a megoldásra vonatkozó útmutatás.  Ebben a példában a felhasználó nem volt az alkalmazáshoz rendelve.

    ![Hiba javítása](media/configure-single-sign-on-portal/fix-error.png)

6. Olvassa el a megoldásra vonatkozó útmutatást, és ha a hiba orvosolható, kattintson a **Javítás** gombra.

7. Futtassa ismét a tesztet, amíg sikeresnek nem bizonyul.



## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban az Azure Portalon konfigurált egy alkalmazást az Azure AD-val végzett egyszeri bejelentkezéshez. Megtalálta az egyszeri bejelentkezés konfigurációs oldalát, és konfigurálta az egyszeri bejelentkezés beállításait. A konfigurálás befejezése után felhasználót rendelt az alkalmazáshoz, és úgy konfigurálta az alkalmazást, hogy SAML-alapú egyszeri bejelentkezést használjon. Amikor mindezekkel végzett, ellenőrizte, hogy az SAML-bejelentkezés megfelelően működik-e.

A következőket hajtotta végre:
> [!div class="checklist"]
> * Kiválasztott az SAML-t egyszeri bejelentkezési módként
> * Kapcsolatba lépett az alkalmazás forgalmazójával a tartomány és az URL-címek konfigurálása érdekében
> * Felhasználói attribútumokat konfigurált
> * SAML aláíró tanúsítványt hozott létre
> * Manuálisan felhasználókat vagy csoportokat rendelt az alkalmazáshoz
> * Konfigurálta az alkalmazást az egyszeri bejelentkezéshez
> * Tesztelte az SAML-alapú egyszeri bejelentkezést

Ha az alkalmazást a cégen belül több felhasználóra is ki szeretné terjeszteni, az automatikus átadás használatát javasoljuk.

> [!div class="nextstepaction"]
>[Megtudhatja, hogyan rendelhet hozzá felhasználókat automatikus átadással](configure-automatic-user-provisioning-portal.md)


