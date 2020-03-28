---
title: Az Azure Active Directory önkiszolgáló jelszó-alaphelyzetbe állításának engedélyezése
description: Ebben az oktatóanyagban megtudhatja, hogyan engedélyezheti az Azure Active Directory önkiszolgáló jelszó-visszaállítást a felhasználók egy csoportja számára, és tesztelheti a jelszó-visszaállítási folyamatot.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71b9052f364dfbae205dd324ba69de9578ccc225
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77027677"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Oktatóanyag: Lehetővé teszi a felhasználók számára a fiók zárolásának feloldását vagy a jelszavak alaphelyzetbe állítását az Azure Active Directory önkiszolgáló jelszó-visszaállításával

Az Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi a felhasználók számára a jelszó módosítását vagy alaphelyzetbe állítását rendszergazda i vagy ügyfélszolgálati közreműködés nélkül. Ha egy felhasználó fiókja zárolva van, vagy elfelejti a jelszavát, követheti a figyelmeztetést, hogy feloldja a blokkolást, és visszatérjen a munkához. Ez a képesség csökkenti az ügyfélszolgálati hívásokat és a termelékenység csökkenését, ha a felhasználó nem tud bejelentkezni az eszközére vagy egy alkalmazásba.

> [!IMPORTANT]
> Ez a rövid útmutató bemutatja a rendszergazdának, hogyan engedélyezheti az önkiszolgáló jelszó-visszaállítást. Ha Ön olyan végfelhasználó, aki már regisztrált az önkiszolgáló jelszó-visszaállításra, https://aka.ms/ssprés vissza kell jutnia a fiókjába, nyissa meg a lehetőséget.
>
> Ha az informatikai csapat nem engedélyezte a saját jelszavának alaphelyzetbe állítását, további segítségért forduljon az ügyfélszolgálathoz.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Önkiszolgáló jelszó-visszaállítás engedélyezése az Azure AD-felhasználók egy csoportjában
> * Hitelesítési módszerek és regisztrációs beállítások konfigurálása
> * Az SSPR-folyamat tesztelése felhasználóként

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Egy működő Azure AD-bérlő, legalább próbaverziós licenccel.
    * Szükség esetén [hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* *Globális rendszergazdai* jogosultságokkal rendelkező fiók.
* Nem rendszergazdai felhasználó, olyan jelszóval, mint például *a tesztfelhasználó.* Tesztelje a végfelhasználói SSPR-élményt ebben a fiókban ebben az oktatóanyagban.
    * Ha felhasználót kell létrehoznia, olvassa [el a Rövid útmutató: Új felhasználók hozzáadása az Azure Active Directoryhoz című témakört.](../add-users-azure-active-directory.md)
* Olyan csoport, amelynek a nem rendszergazdai felhasználó tagja, például *az SSPR-Test-Group.* Ebben a csoportban ebben a csoportban engedélyezi az SSPR-t.
    * Ha csoportot kell létrehoznia, olvassa el, hogyan [hozhat létre csoportot, és hogyan vehet fel tagokat az Azure Active Directoryban.](../active-directory-groups-create-azure-portal.md)

## <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

Az Azure AD lehetővé teszi az SSPR engedélyezését a *Nincs*, *a Kiválasztott*vagy *az Összes* felhasználó számára. Ez a részletes képesség lehetővé teszi, hogy a felhasználók egy részhalmazát válassza ki az SSPR regisztrációs folyamat és a munkafolyamat teszteléséhez. Ha elégedett a folyamattal, és a felhasználók szélesebb körével tudja kommunikálni a követelményeket, további felhasználói csoportokat választhat ki az SSPR engedélyezéséhez. Vagy engedélyezheti az SSPR-t az Azure AD-bérlőben mindenki számára.

Ebben az oktatóanyagban konfigurálja az SSPR-t egy tesztcsoport felhasználóinak egy csoportjához. A következő példában az *SSPR-Test-Group* csoport használatos. Szükség szerint adja meg saját Azure AD-csoportját:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) *egy globális rendszergazdai* engedélyekkel rendelkező fiókhasználatával.
1. Keresse meg, és válassza az **Azure Active Directory**, majd válassza a **jelszó-visszaállítás** a menüben a bal oldalon.
1. A **Tulajdonságok** lapon az *Önkiszolgáló jelszó-visszaállítás engedélyezve*lehetőséget, válassza **a Csoport kiválasztása lehetőséget.**
1. Keresse meg és válassza ki az Azure AD-csoportot, például *az SSPR-Test-Group*csoportot, majd válassza *a Kijelölés lehetőséget.*

    [![](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png "Select a group in the Azure portal to enable for self-service password reset")](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

    Az SSPR szélesebb körű telepítésének részeként a beágyazott csoportok támogatottak. Győződjön meg arról, hogy a kiválasztott csoport(ok) felhasználói rendelkeznek a megfelelő licencekkel. Jelenleg nincs érvényesítési folyamat a licencelési követelmények.

1. Ha engedélyezni szeretné az SSPR-t a kiválasztott felhasználók számára, válassza a **Mentés gombot.**

## <a name="select-authentication-methods-and-registration-options"></a>Adja meg a hitelesítési módszereket és a regisztrációs beállításokat

Amikor a felhasználóknak fel kell oldaniuk a fiókjukat, vagy alaphelyzetbe kell állítaniuk a jelszavukat, a rendszer egy további megerősítési módszert kér tőlük. Ez a további hitelesítési tényező gondoskodik arról, hogy csak a jóváhagyott SSPR-események fejeződjenek be. A felhasználó által megadott regisztrációs adatok alapján kiválaszthatja, hogy mely hitelesítési módszereket szeretné engedélyezni.

1. A bal oldali menü **Hitelesítési módszerek** lapján állítsa be az *1-re* **való visszaállításhoz szükséges módszerek számát.**

    A biztonság növelése érdekében növelheti az SSPR-hez szükséges hitelesítési módszerek számát.

1. Válassza ki azengedélyezni **kívánt felhasználók számára elérhető módszereket.** Ebben az oktatóanyagban jelölje be a jelölőnégyzeteket a következő módszerek engedélyezéséhez:

    * *Mobilalkalmazás-értesítés*
    * *Mobilalkalmazás-kód*
    * *E-mail*
    * *Mobiltelefon*
    * *Irodai telefon*

1. A hitelesítési módszerek alkalmazásához válassza a **Mentés gombot.**

Ahhoz, hogy a felhasználók feloldhassák a fiókzárolást, vagy alaphelyzetbe állíthassák a jellegüket, regisztrálniuk kell kapcsolattartási adataikat. Ez a kapcsolattartási adatok az előző lépésekben konfigurált különböző hitelesítési módszerekhez használatosak.

A rendszergazda manuálisan is megadhatja ezeket a kapcsolattartási adatokat, vagy a felhasználók a regisztrációs portálon saját maguk adhatják meg az adatokat. Ebben az oktatóanyagban konfigurálja a felhasználókat, hogy a következő bejelentkezéskor kérjenek regisztrációt.

1. A **bal** oldali menü Regisztrációs lapján válassza az *Igen* lehetőséget a **Regisztráció megkövetelése a felhasználóknak a bejelentkezéskori regisztrációhoz**lehetőséget.
1. Fontos, hogy az elérhetőségi adatok naprakészek legyenek. Ha a kapcsolattartási adatok elavultak egy SSPR-esemény indításakor, előfordulhat, hogy a felhasználó nem tudja feloldani a fiókját, és nem tudja alaphelyzetbe állítani a jelszavát.

    **A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítő adataik ismételt megerősítését** értékét állítsa *180*-ra.
1. A regisztrációs beállítások alkalmazásához válassza a **Mentés gombot.**

## <a name="configure-notifications-and-customizations"></a>Értesítések és testreszabások konfigurálása

A fióktevékenységről való tájékoztatás érdekében beállíthatja, hogy az e-mail értesítések et sspr-események bekövetkeztekének esetén küldjék el. Ezek az értesítések a rendszeres felhasználói fiókokra és a rendszergazdai fiókokra egyaránt vonatkozhatnak. Rendszergazdai fiókok esetén ez az értesítés egy további tudatossági réteget biztosít, ha egy kiemelt rendszergazdai fiók jelszavát az SSPR használatával alaphelyzetbe állítják.

1. A bal oldali menü **Értesítések** lapján adja meg a következő beállításokat:

   * A **Felhasználók értesítése új jelszó kérésekor** beállítást állítsa *Igen* értékre.
   * A **Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérnek** beállítást állítsa *Igen* értékre.

1. Az értesítési beállítások alkalmazásához válassza a **Mentés gombot.**

Ha a felhasználóknak további segítségre van szükségük az SSPR-folyamattal kapcsolatban, testreszabhatja a "Forduljon a rendszergazdához" hivatkozást. Ez a hivatkozás az SSPR regisztrációs folyamatában használatos, és amikor a felhasználó feloldja a fiókját, vagy visszaállítja a jelszavát. Annak érdekében, hogy a felhasználók megkapják a szükséges támogatást, erősen ajánlott, hogy egy egyéni helpdesk e-mail vagy URL-t.

1. A **bal** oldali menü Testreszabás lapján állítsa az *Ügyfélszolgálat testreszabása hivatkozást* **Igen**értékre.
1. Az **Egyéni helpdesk e-mail vagy URL-cím** mezőben adjon meg egy e-mail címet vagy weblap URL-címét, ahol a felhasználók további segítséget kaphatnak a szervezettől, például*https://support.contoso.com/*
1. Az egyéni hivatkozás alkalmazásához válassza a **Mentés gombot.**

## <a name="test-self-service-password-reset"></a>Önkiszolgáló jelszó-visszaállítás tesztelése

Ha az SSPR engedélyezve van és konfigurálva van, tesztelje az SSPR-folyamatot egy olyan felhasználóval, aki az előző szakaszban kiválasztott csoport tagja, például a *Test-SSPR-Group*. A következő példában a *testuser* fiók használatos. Adja meg saját felhasználói fiókját, amely az oktatóanyag első szakaszában az SSPR-hez engedélyezett csoport része.

> [!NOTE]
> Az önkiszolgáló jelszó-visszaállítás tesztelésekénél használjon nem rendszergazdai fiókot. A rendszergazdák mindig engedélyezve vannak az önkiszolgáló jelszó-visszaállításhoz, és két hitelesítési módszert kell használniuk a jelszó alaphelyzetbe állításához.

1. A manuális regisztrációs folyamat megtekintéséhez nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és keresse meg a tallózását a tallóban. [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) A felhasználókat a következő bejelentkezéskor erre a regisztrációs portálra kell irányítani.
1. Jelentkezzen be egy nem rendszergazdai tesztfelhasználóval, például *a tesztfelhasználóval,* és regisztrálja a hitelesítési módszerek kapcsolattartási adatait.
1. Miután elkészült, válassza a **Jól néz ki** gombbal, és zárja be a böngészőablakot.
1. Nyisson meg egy új böngészőablakot InPrivate- vagy [https://aka.ms/sspr](https://aka.ms/sspr)inkognitómódban, és keresse meg a tallózást.
1. Adja meg a nem rendszergazdai tesztfelhasználói fiókadatait, például a *tesztfelhasználót*, a CAPTCHA karaktereit, majd válassza a **Tovább**gombot.

    ![Adja meg a felhasználói fiók adatait a jelszó visszaállításához](media/tutorial-enable-sspr/password-reset-page.png)

1. Kövesse az ellenőrzési lépéseket a jelszó alaphelyzetbe állításához. Ha elkészült, e-mailben értesítést kell kapnia a jelszó alaphelyzetbe állításáról.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A sorozat következő oktatóanyagában jelszó-visszaírást kell konfigurálni. Ez a funkció jelszómódosításokat ír az Azure AD SSPR-ből egy helyszíni AD-környezetbe. Ha folytatni szeretné ezt az oktatóanyag-sorozatot a jelszóvisszaírás konfigurálásához, ne tiltsa le most az SSPR-t.

Ha a továbbiakban nem szeretné használni az oktatóanyag részeként konfigurált SSPR-funkciókat, állítsa az SSPR-állapotot **Nincs** értékre az alábbi lépésekkel:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Keresse meg, és válassza az **Azure Active Directory**, majd válassza a **jelszó-visszaállítás** a menüben a bal oldalon.
1. A **Tulajdonságok** lap *Önkiszolgáló jelszó-visszaállítás engedélyezése*lehetőséget választotta a **Nincs**lehetőségre.
1. Az SSPR-módosítás alkalmazásához válassza a **Mentés gombot.**

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte az Azure AD önkiszolgáló jelszó-visszaállítást a felhasználók egy kiválasztott csoportjához. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Önkiszolgáló jelszó-visszaállítás engedélyezése az Azure AD-felhasználók egy csoportjában
> * Hitelesítési módszerek és regisztrációs beállítások konfigurálása
> * Az SSPR-folyamat tesztelése felhasználóként

> [!div class="nextstepaction"]
> [Az Azure Multi-Factor Authentication engedélyezése](tutorial-mfa-applications.md)
