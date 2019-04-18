---
title: 'Oktatóanyag: A G Suite konfigurálása a felhasználók automatikus átadása az Azure Active Directoryban |} A Microsoft Docs'
description: Ismerje meg, hogyan automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből a G Suite-hoz.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea1f4d4a6b60961515826a1ba7409bf149b318e8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281301"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Oktatóanyag: A G Suite konfigurálása a felhasználók automatikus átadása

Ez az oktatóanyag célja bemutatják, hogyan automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure Active Directory (Azure AD) a G Suite-hoz.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a G Suite, a következőkre van szükség:

- Azure AD-előfizetés
- A G Suite egyszeri bejelentkezés engedélyezve van az előfizetés
- A Google Apps előfizetést vagy a Google Cloud Platform-előfizetés.

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-g-suite"></a>Felhasználók hozzárendelése a G Suite-hoz

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus fióklétesítés kontextusában csak a felhasználók és csoportok rendelt "" az Azure AD-alkalmazáshoz való szinkronizálódnak.

Beállítása, és a kiépítési szolgáltatás engedélyezése előtt kell dönthet arról, hogy mely felhasználók vagy csoportok Azure AD-ben van szükség az alkalmazásához való hozzáférést. Miután végrehajtotta ezt a döntést, hozzárendelheti ezeket a felhasználókat az alkalmazás útmutatásait követve [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazást](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Javasoljuk, hogy egyetlen Azure AD-felhasználó rendelhető G Suite-hoz, az üzembe helyezési konfiguráció tesztelése. További felhasználók és csoportok később is hozzárendelhet.
> 
> Amikor egy felhasználó rendel a G Suite, válassza ki a **felhasználói** vagy **csoport** szerepkör-hozzárendelés párbeszédpanel. A **alapértelmezett hozzáférési** szerepkör üzembe helyezés nem működik.

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-csatlakozás a felhasználói fiók kiépítése a API G Suite folyamatán. Emellett segít az eszközkiépítési szolgáltatás létrehozása, frissítése és tiltsa le a hozzárendelt felhasználói fiókok a felhasználó és csoport-hozzárendelést az Azure ad-ben alapján a G Suite konfigurálása.

>[!TIP]
>Előfordulhat, hogy meg utasításait követve SAML-alapú egyszeri bejelentkezés a csomag, a G engedélyezése a [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="configure-automatic-user-account-provisioning"></a>Automatikus felhasználói fiók üzembe helyezésének konfigurálása

> [!NOTE]
> Egy másik kivitelezhető lehetőség, automatizálhatja a felhasználókiépítés és a G Suite [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). GADS látja el a helyszíni Active Directory-identitások G Suite-hoz. Ezzel szemben az ebben az oktatóanyagban a megoldás látja el az Azure Active Directory (felhő) felhasználók és a levelezési csoportok a G Suite-hoz. 

1. Jelentkezzen be a [Google Apps felügyeleti konzol](https://admin.google.com/) a rendszergazdai fiók, és válassza ki a **biztonsági**. Ha a hivatkozás nem jelenik meg, akkor előfordulhat, hogy rejtve alatt a **további vezérlők** menü a képernyő alján.

    ![Válassza ki a biztonsági.][10]

1. Az a **biztonsági** lapon jelölje be **API-referencia**.

    ![Válassza ki az API-referencia.][15]

1. Válassza ki **engedélyezése API-hozzáférés**.

    ![Válassza ki az API-referencia.][16]

   > [!IMPORTANT]
   > Minden felhasználó, amelyet meg kíván kiépíteni a G Suite-hoz, a felhasználónév az Azure Active Directoryban *kell* időpontjától egyéni tartományhoz. Például a felhasználói neveket, hogy néz ki bob@contoso.onmicrosoft.com nem fogadja el a G Suite. Másrészről bob@contoso.com elfogadható. A tulajdonságok módosítása az Azure ad-ben módosíthatja egy meglévő felhasználó tartománya. Egyéni tartomány beállítása az Azure Active Directory és a G Suite a következő lépések útmutatást bővítettük.

1. Ha egy egyéni tartománynév még az Azure Active Directoryban még nincs hozzáadva, majd tegye a következőket:
  
    a. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs ablaktáblán válassza ki a **Active Directory**. A könyvtár listában válassza ki a címtárat.

    b. Válassza ki **tartománynév** a bal oldali navigációs panelen, és válassza ki a **Hozzáadás**.

    ![Domain](./media/google-apps-provisioning-tutorial/domain_1.png)

    ![Tartomány hozzáadása](./media/google-apps-provisioning-tutorial/domain_2.png)

    c. Írja be a tartomány nevét, a **tartománynév** mező. Ezt a tartománynevet szeretne használni a G Suite tartományi névvel kell lennie. Válassza ki a **hozzáadni Domain** gombra.

    ![Tartománynév](./media/google-apps-provisioning-tutorial/domain_3.png)

    d. Válassza ki **tovább** az ellenőrzési lap megnyitásához. Ez a tartomány tulajdonjogának ellenőrzéséhez módosítsa a tartomány DNS-rekordok az ezen a lapon lévő értékeknek megfelelően. Választhatja azt is ellenőrizze a **MX-rekordok** vagy **txt típusú rekordok**, attól függően, válassza a a **rekordtípus** lehetőséget.

    Hogyan lehet ellenőrizni a tartománynevek az Azure ad-vel átfogóbb utasításokért lásd: [saját tartománynév hozzáadása az Azure ad-ben](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).

    ![Domain](./media/google-apps-provisioning-tutorial/domain_4.png)

    e. Ismételje meg a fenti lépéseket minden olyan tartományban, amelyet hozzá kíván adni a könyvtárhoz.

    > [!NOTE]
    > A felhasználók átadása, az egyéni tartomány meg kell egyeznie a forrás az Azure AD tartománynevét. Ha nem egyeznek, előfordulhat, a probléma megoldásához attribútum leképezés testreszabás implementálásával.

1. Most, hogy az összes tartományt, az Azure AD ellenőrizte, ellenőriznie kell őket újra a Google Apps. Minden egyes tartományhoz, amely még nincs regisztrálva a Google a következő lépéseket:

    a. Az a [Google Apps felügyeleti konzol](https://admin.google.com/)válassza **tartományok**.

    ![Select Domains][20]

    b. Válassza ki **adjon hozzá egy tartományhoz vagy egy tartomány alias**.

    ![Új tartomány hozzáadása][21]

    c. Válassza ki **egy másik tartomány hozzáadása**, majd írja be a hozzáadni kívánt tartomány nevét be.

    ![Írja be a tartomány neve][22]

    d. Válassza ki **Folytatás és a tartomány tulajdonjogának ellenőrzéséhez**. Ezután kövesse a lépéseket, győződjön meg arról, hogy Ön a tulajdonosa a tartomány nevét. Ellenőrizze a tartományt, a Google átfogó útmutatást lásd: [a Google Apps hely tulajdonosának ellenőrzését](https://support.google.com/webmasters/answer/35179).

    e. Ismételje meg a fenti lépéseket minden további tartományt, amelyet meg kíván hozzáadni a Google Apps.

    > [!WARNING]
    > Ha módosítja az elsődleges tartomány G Suite-bérlője számára, és ha már beállította az egyszeri bejelentkezés az Azure ad-vel, Önnek kell ismételje meg a #3. lépés a 2. lépés: Egyszeri bejelentkezés engedélyezése.

1. Az a [Google Apps felügyeleti konzol](https://admin.google.com/)válassza **rendszergazdai szerepkörök**.

    ![Select Google Apps][26]

1. Határozza meg, hogy mely felhasználók átadásának kezeléséhez használni kívánt rendszergazdai fiókkal. Az a **rendszergazdai szerepkör** -fiók szerkesztése a **jogosultságokkal** szerepkör számára. Ne feledje engedélyezni az összes **rendszergazdai API jogosultságokat** , hogy a fiók használható a kiépítéshez.

    ![Select Google Apps][27]

    > [!NOTE]
    > Ha éles környezetben, az ajánlott eljárás, ha egy rendszergazdai fiók a G Suite kifejezetten az ebben a lépésben. Ezek a fiókok rendszergazda szerepkörrel társítva, amely rendelkezik a szükséges API-jogosultságokkal kell rendelkeznie.

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory** > **vállalati alkalmazások** > **mindenalkalmazás** szakaszban.

1. Ha már konfigurálta a G Suite az egyszeri bejelentkezés, keresse meg a G Suite-példányát a keresőmező használatával. Ellenkező esetben válassza **Hozzáadás**, és keressen **G Suite** vagy **Google Apps** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki az alkalmazást, és ezután adja hozzá az alkalmazások listáját.

1. Válassza ki a G Suite-példányát, és válassza a **kiépítési** fülre.

1. Állítsa be a **Kiépítési mód** való **automatikus**. 

    ![Kiépítés](./media/google-apps-provisioning-tutorial/provisioning.png)

1. Alatt a **rendszergazdai hitelesítő adataival** szakaszban jelölje be **engedélyezés**. Egy Google-engedélyezési párbeszédpanel egy új böngészőablakban nyílik meg.

1. Győződjön meg arról, hogy szeretné-e el a G Suite-bérlő Azure Active Directory engedélyt. Válassza ki az **Elfogadás** lehetőséget.

    ![Ellenőrizze az engedélyeket.][28]

1. Az Azure Portalon válassza ki a **kapcsolat tesztelése** annak érdekében, hogy az Azure AD az alkalmazás képes csatlakozni. Ha a kapcsolódás sikertelen, győződjön meg arról, hogy a G Suite-fiókjától csapat rendszergazdai engedélyekkel rendelkező. Ismételje meg a **engedélyezés** lépés újra.

1. Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mező. Ezután válassza ki a jelölőnégyzetet.

1. Válassza ki **mentéséhez.**

1. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Google Apps**.

1. Az a **attribútumleképezések** területen tekintse át a G Suite-hoz az Azure ad-ből szinkronizált felhasználói attribútumok. Az attribútumok, amelyek **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, a G Suite a frissítési műveleteket. Válassza ki **mentése** véglegesíti a módosításokat.

1. Az Azure AD létesítési szolgáltatás G Suite engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a **beállítások**.

1. Kattintson a **Mentés** gombra.

Ez a folyamat elindítja a kezdeti szinkronizálás, a felhasználók és csoportok, a felhasználók és csoportok szakaszban G Suite-hoz rendelt. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, miközben fut a szolgáltatás több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** előrehaladásának figyeléséhez, és kövesse a hivatkozásokat tevékenységeket tartalmazó naplók kiépítés szakaszt. Ezek a naplók a kiépítési szolgáltatást a az alkalmazás által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](google-apps-tutorial.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
