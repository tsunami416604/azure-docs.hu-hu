---
title: Jelszó alapú egyszeri bejelentkezés konfigurálása az Azure AD-alkalmazásokhoz
description: Jelszó-alapú egyszeri bejelentkezés (SSO) konfigurálása Azure AD-alkalmazásokhoz a Microsoft Identity platformon (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: c3f9f96c6429d4925c60a56cd450a9c2ee7dde24
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87419954"
---
# <a name="configure-password-based-single-sign-on"></a>Jelszó alapú egyszeri bejelentkezés konfigurálása

Az alkalmazások felügyeletének rövid útmutató [sorozatában](view-applications-portal.md) megtudhatta, hogyan használhatja az Azure ad-t az alkalmazás identitás-szolgáltatója (identitásszolgáltató) használatával. A rövid útmutatóban SAML-alapú egyszeri bejelentkezést állíthat be. Az SAML mellett lehetőség van a jelszó-alapú egyszeri bejelentkezésre is. Ez a cikk részletesebben ismerteti az egyszeri bejelentkezéshez használt jelszó-alapú beállítást. 

Ez a lehetőség bármely olyan webhelyhez elérhető, amelynek HTML-bejelentkezési lapja van. A jelszó-alapú egyszeri bejelentkezés (más néven a jelszó-tároló) lehetővé teszi a felhasználók hozzáférésének és jelszavának kezelését az identitás-összevonást nem támogató webalkalmazásokhoz. Olyan helyzetekben is hasznos, amikor több felhasználónak egyetlen fiókot kell megosztania, például a szervezete közösségi Media app-fiókjaival. 

A jelszó-alapú egyszeri bejelentkezés nagyszerű lehetőséget biztosít az alkalmazások Azure AD-ba való integrálásának gyors megkezdésére, és lehetővé teszi a következőket:

- Engedélyezze az egyszeri bejelentkezést a felhasználók számára az Azure AD-vel integrált alkalmazáshoz tartozó felhasználónevek és jelszavak biztonságos tárolásával és visszajátszásával

- Olyan alkalmazások támogatása, amelyeknek több bejelentkezési mezőre van szükségük olyan alkalmazásokhoz, amelyek több, mint a Felhasználónév és a jelszó mezőt igényelnek a bejelentkezéshez

- A felhasználónevek és jelszavak beviteli mezőinek testreszabása a felhasználók számára az alkalmazás- [hozzáférési panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) a hitelesítő adatok megadásakor

- Lehetővé teheti a felhasználók számára, hogy saját felhasználóneveket és jelszavukat adjanak meg a meglévő, manuálisan begépelt alkalmazás-fiókokhoz.

- A felhasználóhoz rendelt felhasználónevek és jelszavak megadásának engedélyezése az üzleti csoport tagjai számára az [önkiszolgáló alkalmazás-hozzáférési](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) szolgáltatás használatával

-   Annak engedélyezése, hogy a rendszergazda megadhatja a felhasználók vagy csoportok által az alkalmazásba való bejelentkezéskor használandó felhasználónevet és jelszót a hitelesítő adatok frissítése funkció használatával. 

## <a name="before-you-begin"></a>Előkészületek

Ha az Azure AD-t identitás-szolgáltatóként használja, és az egyszeri bejelentkezés (SSO) beállítása egyszerű vagy összetett lehet a használt alkalmazástól függően. Néhány alkalmazás csak néhány művelettel állítható be. Másoknak részletes konfigurációra van szükségük. A gyors üzembe helyezéshez járjon végig az alkalmazás-felügyeleti útmutató [sorozatán](view-applications-portal.md) . Ha a hozzáadott alkalmazás egyszerű, akkor valószínűleg nem kell elolvasnia ezt a cikket. Ha a felvenni kívánt alkalmazásnak egyéni konfigurációra van szüksége, és jelszó-alapú egyszeri bejelentkezést kell használnia, akkor ez a cikk Önnek szól.

> [!IMPORTANT] 
> Vannak olyan helyzetek, amikor az **egyszeri bejelentkezési** lehetőség nem lesz a **vállalati alkalmazásokban**lévő alkalmazás navigációjában. 
>
> Ha az alkalmazás **Alkalmazásregisztrációk** használatával lett regisztrálva, akkor az egyszeri bejelentkezés funkció alapértelmezés szerint a OIDC OAuth használatára van beállítva. Ebben az esetben az **egyszeri bejelentkezési** lehetőség nem jelenik meg a **vállalati alkalmazások**alatt a navigációban. Ha a **Alkalmazásregisztrációk** használatával adja hozzá az egyéni alkalmazást, a jegyzékfájlban konfigurálhatja a beállításokat. További információ a jegyzékfájlról: [Azure Active Directory app manifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest). Az SSO-szabványokkal kapcsolatos további tudnivalókért tekintse meg a [hitelesítés és engedélyezés a Microsoft Identity platform használatával](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform)című témakört. 
>
> Egyéb forgatókönyvek, amelyekben az **egyszeri bejelentkezés** hiányzik a navigálásból, ha egy alkalmazás egy másik bérlőn fut, vagy ha a fiókja nem rendelkezik a szükséges engedélyekkel (globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás-rendszergazda vagy az egyszerű szolgáltatásnév tulajdonosa). Az engedélyek olyan eseteket is okozhatnak, ahol megnyithatja az **egyszeri bejelentkezést** , de nem fogja tudni menteni. További információ az Azure AD rendszergazdai szerepköreiről: ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Alapszintű konfiguráció

A rövid útmutató [sorozatában](view-applications-portal.md)megtanulta, hogyan adhat hozzá egy alkalmazást a bérlőhöz, hogy az Azure ad tudja, hogy az alkalmazás identitás-szolgáltatója (identitásszolgáltató) legyen használatban. Egyes alkalmazások már előre konfigurálva vannak, és megjelennek az Azure AD-galériában. A többi alkalmazás nem szerepel a katalógusban, és létre kell hoznia egy általános alkalmazást, és manuálisan kell konfigurálnia. Az alkalmazástól függően előfordulhat, hogy a jelszó-alapú egyszeri bejelentkezés beállítás nem érhető el. Ha nem jelenik meg a jelszó-alapú beállítási lista az alkalmazás egyszeri bejelentkezési oldalán, akkor nem érhető el.

A jelszó-alapú egyszeri bejelentkezés konfigurációs lapja egyszerű. Csak az alkalmazás által használt bejelentkezési oldal URL-címét tartalmazza. A karakterláncnak a Felhasználónév beviteli mezőjét tartalmazó oldalnak kell lennie.

Az URL-cím megadása után válassza a **Mentés**lehetőséget. Az Azure AD elemzi a bejelentkezési oldal HTML-jét a Felhasználónév és jelszó beviteli mezőiben. Ha a kísérlet sikeres, elkészült.
 
A következő lépés a [felhasználók vagy csoportok társítása az alkalmazáshoz](methods-for-assigning-users-and-groups.md). A felhasználók és csoportok hozzárendelése után megadhatja a felhasználó nevében használt hitelesítő adatokat, amikor bejelentkeznek az alkalmazásba. Válassza a **felhasználók és csoportok**lehetőséget, jelölje be a felhasználó vagy csoport sorához tartozó jelölőnégyzetet, majd válassza a **hitelesítő adatok frissítése**lehetőséget. Ezután adja meg a felhasználó vagy csoport nevében használni kívánt felhasználónevet és jelszót. Ellenkező esetben a rendszer megkéri a felhasználókat, hogy indítsák el magukat a hitelesítő adatokat.
 

## <a name="manual-configuration"></a>Manuális konfigurálás

Ha az Azure AD elemzési kísérlete sikertelen, manuálisan is konfigurálhatja a bejelentkezést.

1. A ** \<application name> konfiguráció**területen válassza **a \<application name> jelszó egyszeri bejelentkezés beállításainak megadása** lehetőséget a **bejelentkezési** lap megjelenítéséhez. 

2. Jelölje be **a bejelentkezési mezők manuális észlelése**jelölőnégyzetet. A bejelentkezési mezők manuális észlelését ismertető további utasítások jelennek meg.

   ![Jelszó alapú egyszeri bejelentkezés manuális konfigurálása](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Jelölje be **a bejelentkezési mezők rögzítése**jelölőnégyzetet. Egy új lapon megnyílik egy rögzítési állapot lap, amely azt mutatja, hogy az üzenet **metaadatainak rögzítése jelenleg folyamatban van**.

4. Ha megjelenik a **hozzáférési panel bővítmény kötelező** mezője egy új lapon, válassza a **Telepítés most** lehetőséget a **saját alkalmazások biztonságos bejelentkezési bővítményének** telepítéséhez. (A böngésző kiterjesztéséhez a Microsoft Edge, a Chrome vagy a Firefox szükséges.) Ezután telepítse, indítsa el és engedélyezze a bővítményt, és frissítse a rögzítés állapota lapot.

   Ekkor megnyílik egy másik lap, amely megjeleníti a megadott URL-címet.
5. A megadott URL-címmel rendelkező lapon ugorjon végig a bejelentkezési folyamaton. Adja meg a Felhasználónév és a jelszó mezőket, majd próbáljon meg bejelentkezni. (Nem kell megadnia a megfelelő jelszót.)

   A rendszer felszólítja a rögzített bejelentkezési mezők mentésére.
6. Válassza az **OK** lehetőséget. A böngésző bővítménye frissíti a rögzítés állapota lapot, és az üzenet **metaadatai frissültek az alkalmazáshoz**. A böngésző lap bezárul.

7. Az Azure AD **configure bejelentkezési** oldalán kattintson az **OK gombra, és sikerült bejelentkezni az alkalmazásba**.

8. Válassza az **OK** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók vagy csoportok társítása az alkalmazáshoz](methods-for-assigning-users-and-groups.md)
- [A felhasználói fiókok automatikus üzembe helyezésének konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)
