---
title: Azure Active Directory önkiszolgáló jelszó-visszaállítás engedélyezése
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti Azure Active Directory önkiszolgáló jelszó-visszaállítást egy felhasználói csoport számára, és tesztelheti a jelszó-visszaállítási folyamatot.
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027677"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Oktatóanyag: a felhasználók számára lehetővé teszi a fiók zárolásának feloldását vagy a jelszavak alaphelyzetbe állítását Azure Active Directory önkiszolgáló jelszó-visszaállítás használatával

A Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi a felhasználók számára a jelszavuk módosítását vagy alaphelyzetbe állítását, rendszergazdai vagy ügyfélszolgálati beavatkozás nélkül. Ha a felhasználó fiókja zárolva van, vagy elfelejti a jelszavát, akkor az utasításokat követve letilthatja önmagát, és visszatérhet a munkához. Ez a funkció csökkenti az ügyfélszolgálati hívásokat és a termelékenység elvesztését, ha a felhasználó nem tud bejelentkezni az eszközére vagy alkalmazására.

> [!IMPORTANT]
> Ez a rövid útmutató egy rendszergazdát mutat be, amely lehetővé teszi az önkiszolgáló jelszó-visszaállítást. Ha a végfelhasználó már regisztrálva van az önkiszolgáló jelszó-visszaállításhoz, és vissza kell kérnie a fiókját, lépjen a https://aka.ms/sspr.
>
> Ha az informatikai csapat nem engedélyezte a saját jelszavának alaphelyzetbe állítását, további segítségért forduljon az ügyfélszolgálathoz.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Önkiszolgáló jelszó-visszaállítás engedélyezése Azure AD-felhasználók egy csoportjára
> * Hitelesítési módszerek és regisztrációs beállítások konfigurálása
> * A SSPR folyamat tesztelése felhasználóként

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Egy működő Azure AD-bérlő, legalább próbaverziós licenccel.
    * Ha szükséges, [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *Globális rendszergazdai* jogosultságokkal rendelkező fiók.
* Egy nem rendszergazda felhasználó, aki ismeri a jelszót, például *tesztfelhasználó*. Ebben az oktatóanyagban tesztelheti a végfelhasználói SSPR felhasználói élményét.
    * Ha létre kell hoznia egy felhasználót, tekintse meg a rövid útmutató [: új felhasználók hozzáadása a Azure Active Directoryhoz](../add-users-azure-active-directory.md)című témakört.
* Olyan csoport, amelynek a nem rendszergazda felhasználó tagja, például *SSPR-test-Group*. Ebben az oktatóanyagban engedélyezheti a csoport SSPR.
    * Ha csoportot kell létrehoznia, tekintse meg [a csoport létrehozása és a tagok hozzáadása a Azure Active Directory-ben](../active-directory-groups-create-azure-portal.md)című témakört.

## <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

Az Azure AD lehetővé teszi, hogy a *nincs*, a *kiválasztott*vagy *az összes* felhasználó számára engedélyezze a SSPR. Ez a részletes képesség lehetővé teszi, hogy a felhasználók egy részhalmazát kiválassza a SSPR-regisztrációs folyamat és a munkafolyamat teszteléséhez. Ha már jól ismeri a folyamatot, és a felhasználók szélesebb körével tud kommunikálni a követelményekkel, további felhasználói csoportokat is kijelölhet a SSPR engedélyezéséhez. Az Azure AD-bérlőben mindenki számára is engedélyezheti a SSPR.

Ebben az oktatóanyagban egy SSPR tartozó felhasználók készletét konfigurálja. A következő példában az *SSPR-test-Group* csoport van használatban. Igény szerint adja meg saját Azure AD-csoportját:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) *globális rendszergazdai* jogosultságokkal rendelkező fiókkal.
1. Keresse meg és válassza ki a **Azure Active Directory**, majd a bal oldali menüben válassza a **jelszó alaphelyzetbe állítása** lehetőséget.
1. A **Tulajdonságok** lapon, az *önkiszolgáló jelszó-visszaállítás engedélyezése*lehetőségnél válassza a **csoport kiválasztása** lehetőséget.
1. Keresse meg és válassza ki az Azure AD-csoportot, például *SSPR-test-Group*, majd válassza a *kiválasztás*lehetőséget.

    [![](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png "Select a group in the Azure portal to enable for self-service password reset")](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

    A SSPR szélesebb körű üzembe helyezésének részeként beágyazott csoportok is támogatottak. Győződjön meg arról, hogy a kiválasztott csoport (ok) hoz tartozó felhasználók rendelkeznek a megfelelő licencekkel. Ezen licencelési követelmények jelenleg nem rendelkeznek érvényesítési folyamattal.

1. A SSPR engedélyezéséhez válassza a **Mentés**lehetőséget.

## <a name="select-authentication-methods-and-registration-options"></a>Hitelesítési módszerek és regisztrációs beállítások kiválasztása

Ha a felhasználóknak fel kell oldaniuk a fiókját, vagy alaphelyzetbe kell állítaniuk a jelszavukat, a rendszer további megerősítő módszert kér. Ez a további hitelesítési tényező gondoskodik arról, hogy csak a jóváhagyott SSPR-események legyenek végrehajtva. Kiválaszthatja, hogy mely hitelesítési módszerek legyenek engedélyezve, a felhasználó által biztosított regisztrációs információk alapján.

1. A bal oldali menüben a **hitelesítési módszerek** lapon állítsa be az *1*értékre való **visszaállításhoz szükséges metódusok számát** .

    A biztonság növelése érdekében növelheti a SSPR szükséges hitelesítési módszerek számát.

1. Válassza ki **azokat a módszereket,** amelyeket a szervezetnek engedélyezni kíván a felhasználók számára. Ebben az oktatóanyagban a következő módszerekkel engedélyezheti a jelölőnégyzeteket:

    * *Mobile apps – értesítés*
    * *Mobile App Code*
    * *E-mail*
    * *Mobiltelefon*
    * *Irodai telefon*

1. A hitelesítési módszerek alkalmazásához válassza a **Mentés**lehetőséget.

Ahhoz, hogy a felhasználók fel tudják oldani a fiókját vagy a jelszó alaphelyzetbe állítását, regisztrálniuk kell a kapcsolattartási adataikat. A kapcsolattartási adatok az előző lépésekben konfigurált különböző hitelesítési módszerekhez használatosak.

A rendszergazda manuálisan megadhatja a kapcsolattartási adatokat, vagy a felhasználók a regisztrációs portálon is megadhatják az információkat. Ebben az oktatóanyagban konfigurálja a felhasználókat, hogy a következő bejelentkezéskor megkérdezzenek a regisztrációra.

1. A bal oldali menüben, a **regisztráció** lapon válassza az *Igen* lehetőséget, ha a **felhasználóknak regisztrálniuk kell a bejelentkezéskor**.
1. Fontos, hogy a kapcsolattartási adatok naprakészek maradnak. Ha a kapcsolattartási adatok elavultak egy SSPR esemény elindításakor, előfordulhat, hogy a felhasználó nem tudja feloldani a fiókját, vagy visszaállítja a jelszavát.

    **A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítő adataik ismételt megerősítését** értékét állítsa *180*-ra.
1. A regisztrációs beállítások alkalmazásához válassza a **Mentés**lehetőséget.

## <a name="configure-notifications-and-customizations"></a>Értesítések és testreszabások konfigurálása

Ha szeretné, hogy a felhasználók tájékoztassanak a fiók tevékenységéről, beállíthatja, hogy a rendszer e-mail-értesítéseket küldjön, amikor SSPR esemény történik. Ezek az értesítések a normál felhasználói fiókokra és a rendszergazdai fiókokra is kiterjednek. A rendszergazdai fiókok esetében ez az értesítés további figyelmet nyújt, ha a jogosultsági szintű rendszergazdai fiók jelszava alaphelyzetbe áll a SSPR használatával.

1. Az **értesítések** lap bal oldali menüjében adja meg a következő beállításokat:

   * A **Felhasználók értesítése új jelszó kérésekor** beállítást állítsa *Igen* értékre.
   * A **Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérnek** beállítást állítsa *Igen* értékre.

1. Az értesítési beállítások alkalmazásához válassza a **Mentés**lehetőséget.

Ha a felhasználóknak további segítségre van szüksége a SSPR folyamathoz, testreszabhatja a "Kapcsolatfelvétel a rendszergazdával" hivatkozást. Ez a hivatkozás a SSPR regisztrációs folyamatában használatos, és amikor a felhasználó feloldja a fiókját, vagy visszaállítja a jelszavát. Annak biztosításához, hogy a felhasználók megkapják a szükséges támogatást, kifejezetten ajánlott egyéni segélyszolgálat-e-mailt vagy URL-címet megadni.

1. A **Testreszabás** lapon a bal oldali menüben válassza az *ügyfélszolgálati kapcsolat testreszabása* **Igen**lehetőséget.
1. Az **Egyéni segélyszolgálat e-mail-címe vagy URL-** címe mezőben adjon meg egy e-mail-címet vagy egy weblap URL-címét, ahol a felhasználók további segítséget kérhetnek a szervezettől, például *https://support.contoso.com/*
1. Az egyéni hivatkozás alkalmazásához válassza a **Mentés**lehetőséget.

## <a name="test-self-service-password-reset"></a>Önkiszolgáló jelszó-visszaállítás tesztelése

Ha a SSPR engedélyezve és konfigurálva van, tesztelje a SSPR folyamatot egy olyan felhasználóval, aki az előző szakaszban kiválasztott csoport részét képezi, például *test-SSPR-Group*. A következő példában a rendszer a *tesztfelhasználó* fiókot használja. Adja meg saját felhasználói fiókját, amely az oktatóanyag első szakaszában a SSPR számára engedélyezett csoport részét képezi.

> [!NOTE]
> Amikor teszteli az önkiszolgáló jelszó-visszaállítást, használjon egy nem rendszergazdai fiókot. A rendszergazdák mindig engedélyezve vannak az önkiszolgáló jelszó-visszaállításhoz, és a jelszó alaphelyzetbe állításához két hitelesítési módszert kell használniuk.

1. A manuális regisztrációs folyamat megtekintéséhez nyisson meg egy új böngészőablakot InPrivate-vagy inkognitóban-módban, és keresse meg [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup). A felhasználókat a következő bejelentkezéskor a regisztrációs portálra kell irányítani.
1. Jelentkezzen be egy nem rendszergazdai jogosultsággal rendelkező felhasználóval, például a *tesztfelhasználó*, és regisztrálja a hitelesítési módszereket a kapcsolattartási adatokhoz.
1. Ha elkészült, jelölje be a jól látható gombra kattintva **, majd a** böngészőablak bezárásához.
1. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://aka.ms/sspr](https://aka.ms/sspr).
1. Adja meg a nem rendszergazdai felhasználói fiók adatait, például a *tesztfelhasználó*, a CAPTCHA karaktereit, majd kattintson a **tovább**gombra.

    ![Adja meg a felhasználói fiók adatait a jelszó alaphelyzetbe állításához](media/tutorial-enable-sspr/password-reset-page.png)

1. A jelszó alaphelyzetbe állításához kövesse az ellenőrzési lépéseket. Ha elkészült, kapnia kell egy e-mailt arról, hogy a jelszó alaphelyzetbe állt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a sorozatban a következő oktatóanyagban konfigurálhatja a jelszó visszaírási. Ez a szolgáltatás az Azure AD-SSPR visszaadott jelszavakat egy helyszíni AD-környezetbe írja. Ha továbbra is ezt az oktatóanyag-sorozatot szeretné használni a jelszó-visszaírási konfigurálásához, ne tiltsa le a SSPR.

Ha már nem szeretné használni az oktatóanyag részeként konfigurált SSPR funkciót, állítsa a SSPR állapotot a **none** értékre a következő lépések végrehajtásával:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Keresse meg és válassza ki a **Azure Active Directory**, majd a bal oldali menüben válassza a **jelszó alaphelyzetbe állítása** lehetőséget.
1. A **Tulajdonságok** lapon, az *önkiszolgáló jelszó-visszaállítás engedélyezése*lehetőségnél válassza a **nincs**lehetőséget.
1. A SSPR módosításának alkalmazásához válassza a **Mentés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte az Azure AD önkiszolgáló jelszó-visszaállítását egy kiválasztott felhasználói csoport számára. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Önkiszolgáló jelszó-visszaállítás engedélyezése Azure AD-felhasználók egy csoportjára
> * Hitelesítési módszerek és regisztrációs beállítások konfigurálása
> * A SSPR folyamat tesztelése felhasználóként

> [!div class="nextstepaction"]
> [Azure-Multi-Factor Authentication engedélyezése](tutorial-mfa-applications.md)
