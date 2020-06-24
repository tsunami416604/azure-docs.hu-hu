---
title: Az Azure AD-alkalmazások jelszavas egyszeri bejelentkezésének konfigurálása | Microsoft Docs
description: Jelszavas egyszeri bejelentkezés (SSO) konfigurálása Azure AD vállalati alkalmazásokhoz a Microsoft Identity platformon (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/10/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 043adc309c3480865eb9aa7a7bff8d35e85bc78a
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763499"
---
# <a name="configure-password-single-sign-on"></a>Jelszó egyszeri bejelentkezésének konfigurálása

Ha [egy Gallery-alkalmazást](add-gallery-app.md) vagy egy [nem Gallery-webalkalmazást](add-non-gallery-app.md) AD hozzá az Azure ad vállalati alkalmazásaihoz, az egyik elérhető egyszeri bejelentkezési lehetőség a [jelszó-alapú egyszeri bejelentkezés](what-is-single-sign-on.md#password-based-sso). Ez a lehetőség bármely, HTML-alapú bejelentkezési oldallal rendelkező web esetében elérhető. A jelszó-alapú egyszeri bejelentkezés (más néven a jelszó-tároló) lehetővé teszi a felhasználók hozzáférésének és jelszavának kezelését az identitás-összevonást nem támogató webalkalmazásokhoz. Olyan helyzetekben is hasznos, amikor több felhasználónak egyetlen fiókot kell megosztania, például a szervezete közösségi Media app-fiókjaival. 

A jelszó-alapú egyszeri bejelentkezés nagyszerű lehetőséget biztosít az alkalmazások Azure AD-ba való integrálásának gyors megkezdésére, és lehetővé teszi a következőket:

-   Engedélyezze az **egyszeri bejelentkezést a felhasználók** számára az Azure ad-vel integrált alkalmazáshoz tartozó felhasználónevek és jelszavak biztonságos tárolásával és visszajátszásával

-   Olyan alkalmazások **támogatása, amelyeknek több bejelentkezési mezőre van szükségük** olyan alkalmazásokhoz, amelyek több, mint a Felhasználónév és a jelszó mezőt igényelnek a bejelentkezéshez

-   A felhasználónevek és jelszavak beviteli mezőinek **testreszabása** a felhasználók számára az alkalmazás- [hozzáférési panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) a hitelesítő adatok megadásakor

-   Saját felhasználónevek és jelszavak megadásának engedélyezése a **felhasználók** számára az [alkalmazás-hozzáférési panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) manuálisan begépelt összes meglévő alkalmazás-fiókhoz

-   A felhasználóhoz rendelt felhasználónevek és jelszavak megadásának engedélyezése az **üzleti csoport tagjai** számára az [önkiszolgáló alkalmazás-hozzáférési](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) szolgáltatás használatával

-   Annak engedélyezése, hogy a **rendszergazda** megadhatja a felhasználók vagy csoportok által az alkalmazásba való bejelentkezéskor használandó felhasználónevet és jelszót a hitelesítő adatok frissítése funkció használatával. 

## <a name="before-you-begin"></a>Előkészületek

Ha az alkalmazás nem lett hozzáadva az Azure AD-bérlőhöz, tekintse meg [a Gallery-alkalmazás hozzáadása](add-gallery-app.md) vagy [a nem Gallery-alkalmazás hozzáadása](add-non-gallery-app.md)című témakört.

## <a name="open-the-app-and-select-password-single-sign-on"></a>Nyissa meg az alkalmazást, és válassza a jelszó egyszeri bejelentkezés lehetőséget.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) Felhőbeli alkalmazás-rendszergazdaként vagy az Azure ad-bérlőhöz tartozó alkalmazás-rendszergazdaként.

2. Navigáljon **Azure Active Directory**  >  **vállalati alkalmazásokhoz**. Megjelenik az Azure AD-bérlőben található alkalmazások véletlenszerű mintája. 

3. Az **alkalmazás típusa** menüben válassza a **minden alkalmazás**lehetőséget, majd kattintson az **alkalmaz**gombra.

4. Adja meg az alkalmazás nevét a keresőmezőbe, majd válassza ki az alkalmazást az eredmények közül.

5. A **kezelés** szakaszban válassza az **egyszeri bejelentkezés**lehetőséget. 

6. Válassza a **jelszó-alapú**lehetőséget.

7. Adja meg az alkalmazás webalapú bejelentkezési oldalának URL-címét. A karakterláncnak a Felhasználónév beviteli mezőjét tartalmazó oldalnak kell lennie.

   ![Jelszó alapú egyszeri bejelentkezés](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Kattintson a **Mentés** gombra. Az Azure AD megpróbálja elemezni a bejelentkezési oldalt a Felhasználónév-bevitelhez és a jelszó beviteléhez. Ha a kísérlet sikeres, elkészült. 
 
> [!NOTE]
> A következő lépés a [felhasználók vagy csoportok társítása az alkalmazáshoz](methods-for-assigning-users-and-groups.md). A felhasználók és csoportok hozzárendelése után megadhatja a felhasználó nevében használt hitelesítő adatokat, amikor bejelentkeznek az alkalmazásba. Válassza a **felhasználók és csoportok**lehetőséget, jelölje be a felhasználó vagy csoport sorához tartozó jelölőnégyzetet, majd kattintson a **hitelesítő adatok frissítése**elemre. Ezután adja meg a felhasználó vagy csoport nevében használni kívánt felhasználónevet és jelszót. Ellenkező esetben a rendszer megkéri a felhasználókat, hogy indítsák el magukat a hitelesítő adatokat.
 

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

A bejelentkezési oldal rögzítése után felhasználókat és csoportokat rendelhet hozzá, és a hitelesítő adatokat ugyanúgy állíthatja be, mint a normál jelszó-SSO- [alkalmazások](what-is-single-sign-on.md)esetében.

> [!NOTE]
> Feltöltheti az alkalmazás csempe emblémáját az alkalmazás **Konfigurálás** lapján található **embléma feltöltése** gombbal.

## <a name="next-steps"></a>További lépések

- [Felhasználók vagy csoportok társítása az alkalmazáshoz](methods-for-assigning-users-and-groups.md)
- [A felhasználói fiókok automatikus üzembe helyezésének konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)
