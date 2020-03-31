---
title: 'Oktatóanyag: A Salesforce konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez| Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Salesforce között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b8038896a11b65e835ce71f5fc34e85723cc91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060521"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Oktatóanyag: A Salesforce konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy a Salesforce és az Azure AD-ben a felhasználói fiókok automatikus kiépítéséhez és de-kiépítéséhez szükséges lépéseket jelenítse meg az Azure AD-ről a Salesforce-ra.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Egy Azure Active Directory-bérlő
* Egy Salesforce.com bérlő

> [!IMPORTANT]
> Ha Salesforce.com próbafiókot használ, akkor nem fogja tudni konfigurálni az automatikus felhasználói kiépítést. A próbaverziós fiókok nem rendelkeznek a szükséges API-hozzáféréssel, amíg meg nem vásárolják őket. Ezt a korlátozást egy ingyenes [fejlesztői fiókkal](https://developer.salesforce.com/signup) megkerülheti az oktatóanyag befejezéséhez.

Ha Salesforce sandbox környezetet használ, olvassa el a [Salesforce sandbox integrációs oktatóanyagát.](https://go.microsoft.com/fwLink/?LinkID=521879)

## <a name="assigning-users-to-salesforce"></a>Felhasználók hozzárendelése a Salesforce-hoz

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva van.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak vagy csoportjainak kell hozzáférniük a Salesforce alkalmazáshoz. Miután meghozta ezt a döntést, ezeket a felhasználókat hozzárendelheti a Salesforce alkalmazáshoz a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) című útmutató utasításainak megfelelően.

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Fontos tippek a felhasználók Salesforce-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve salesforce a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Salesforce-hoz rendel, érvényes felhasználói szerepkört kell választania. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítésnél

    > [!NOTE]
    > Ez az alkalmazás a kiépítési folyamat részeként importálja a salesforce-i profilokat, amelyeket az ügyfél az Azure AD-ben a felhasználók hozzárendelésekor szeretne kiválasztani. Kérjük, vegye figyelembe, hogy a Salesforce-ból importált profilok szerepkörként jelennek meg az Azure AD-ben.

## <a name="enable-automated-user-provisioning"></a>Automatikus felhasználói kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-nek a [Salesforce felhasználói fiók létesítési API-jával – v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)– való csatlakoztatásával, valamint a kiépítési szolgáltatás konfigurálásával a hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a Salesforce-ban az Azure AD-ben a felhasználói és csoport-hozzárendelés alapján.

> [!Tip]
> Az [Azure Portalon](https://portal.azure.com)található utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a Salesforce számára. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-account-provisioning"></a>A felhasználói fiókok automatikus kiépítésének konfigurálása

Ez a szakasz azt ismerteti, hogyan engedélyezheti az Active Directory felhasználói fiókok felhasználók számára történő kiépítését a Salesforce számára.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

2. Ha már konfigurálta a Salesforce-ot egyszeri bejelentkezéshez, keresse meg a Salesforce példányát a keresőmező használatával. Ellenkező esetben válassza a **Salesforce** **hozzáadása** és keresése lehetőséget az alkalmazásgyűjteményben. Válassza ki a Salesforce elemet a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

3. Válassza ki a Salesforce példányát, majd válassza a **Kiépítés** lapot.

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés](./media/salesforce-provisioning-tutorial/provisioning.png)

5. A **Rendszergazdai hitelesítő adatok csoportban** adja meg a következő konfigurációs beállításokat:

    a. A **Rendszergazdai felhasználónév** mezőbe írja be azt a Salesforce-fióknevet, amelyhez Salesforce.com **rendszergazdai** profil van rendelve.

    b. A **Rendszergazdai jelszó** mezőbe írja be a fiók jelszavát.

6. A Salesforce biztonsági jogkivonatának beszerzéséhez nyisson meg egy új lapot, és jelentkezzen be ugyanabba a Salesforce felügyeleti fiókba. A lap jobb felső sarkában kattintson a nevére, majd a **Beállítások parancsra.**

    ![Automatikus felhasználói kiépítés engedélyezése](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Automatikus felhasználói kiépítés engedélyezése")

7. A bal oldali navigációs ablakban kattintson a **Személyes adatok** elemre a kapcsolódó szakasz kibontásához, majd kattintson a Saját **biztonsági jogkivonat visszaállítása parancsra.**
  
    ![Automatikus felhasználói kiépítés engedélyezése](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Automatikus felhasználói kiépítés engedélyezése")

8. A **Biztonsági jogkivonat visszaállítása** lapon kattintson a **Biztonsági jogkivonat visszaállítása** gombra.

    ![Automatikus felhasználói kiépítés engedélyezése](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Automatikus felhasználói kiépítés engedélyezése")

9. Ellenőrizze az ehhez a rendszergazdai fiókhoz társított e-mail postafiókot. Keressen egy e-mailt Salesforce.com, amely tartalmazza az új biztonsági jogkivonatot.

10. Másolja a jogkivonatot, nyissa meg az Azure AD-ablakot, és illessze be a **titkos jogkivonat** mezőbe.

11. A **bérlői URL-címet** meg kell adni, ha a Salesforce példánya a Salesforce government cloud.The Tenant URL should be entered if the instance of Salesforce is on the Salesforce Government Cloud. Ellenkező esetben nem kötelező. Adja meg a bérlői URL-címet\<a "https:// példány\>.my.salesforce.com" formátumban, és cserélje le \<a példányt\> a Salesforce-példány nevére.

12. Az Azure Portalon kattintson a **Kapcsolat tesztelése** elemre, hogy az Azure AD csatlakozni tud-e a Salesforce alkalmazáshoz.

13. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia, és jelölje be az alábbi jelölőnégyzetet.

14. Kattintson a **Mentés gombra.**  

15. A Leképezések csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Salesforce-szal lehetőséget.**

16. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD-ről salesforce-ra szinkronizált felhasználói attribútumokat. Vegye figyelembe, hogy az **Egyező** tulajdonságokként kijelölt attribútumok a Salesforce felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

17. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Salesforce számára módosítsa a **Kiépítés állapotát** **be beállításra** a Beállítások szakaszban

18. Kattintson a **Mentés gombra.**

> [!NOTE]
> Miután a felhasználók kivannak építve a Salesforce alkalmazásban, a rendszergazdának konfigurálnia kell a nyelvspecifikus beállításokat. A nyelvi konfigurációval kapcsolatos további részletekért tekintse meg [ezt](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) a cikket.

Ezzel elindítja a Salesforce-hoz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását a Felhasználók és csoportok szakaszban. Vegye figyelembe, hogy a kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A Szinkronizálás **részletei** szakaszsegítségével figyelheti a folyamatot, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat, amelyek a salesforce-alkalmazás létesítési szolgáltatása által végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="common-issues"></a>Gyakori problémák
* Ha problémái vannak a Salesforce-hoz való hozzáférés engedélyezésével, győződjön meg a következőkről:
    * A használt hitelesítő adatok rendszergazdai hozzáféréssel rendelkeznek a Salesforce-hoz.
    * A Salesforce által használt verzió támogatja a Web Access t (pl. Developer, Enterprise, Sandbox és Unlimited kiadás a Salesforce-ban.)
    * A webes API-hozzáférés engedélyezve van a felhasználó számára.
* Az Azure AD-kiépítési szolgáltatás támogatja a kiépítési nyelvet, területi és timeZone egy felhasználó számára. Ezek az attribútumok az alapértelmezett attribútumleképezésekben találhatók, de nem rendelkeznek alapértelmezett forrásattribútummal. Győződjön meg arról, hogy az alapértelmezett forrásattribútumot választja, és hogy a forrásattribútum a SalesForce által várt formátumban van. Az angol (Egyesült Államok) (Egyesült Államok) beállításhoz például a (localeSidKey) en_US. Tekintse át az [itt](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) található útmutatást a megfelelő localeSidKey formátum meghatározásához. A languageLocaleKey formátumok [itt](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5)találhatók. A formátum helyességed mellett előfordulhat, hogy a nyelvet az [itt](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)leírtak szerint engedélyezni kell a felhasználók számára. 
* **A SalesforceLicenseLimitTúllép:** A felhasználó nem hozható létre a célalkalmazásban, mert ehhez a felhasználóhoz nincsenek elérhető licencek. Szerezzen be további licenceket a célalkalmazáshoz, vagy tekintse át a felhasználói hozzárendeléseket és az attribútumleképezési konfigurációt, hogy a megfelelő felhasználók a megfelelő attribútumokkal legyenek hozzárendelve.
* **SalesforceDuplicateUserName:** A felhasználó nem lehet kiépíteni, mert rendelkezik egy Salesforce.com "felhasználónév", amely egy másik Salesforce.com bérlődi duplikált.A Salesforce.com a "Felhasználónév" attribútum értékeinek egyedinek kell lenniük az összes Salesforce.com bérlőben.Alapértelmezés szerint a felhasználó userPrincipalName az Azure Active Directoryban lesz a "felhasználónév" Salesforce.com.Itt két lehetősége van.Az egyik lehetőség az, hogy megkeresse és nevezze át a felhasználót a másik Salesforce.com bérlő "Felhasználónév" duplikált "felhasználónév" azonosítójával, ha a másik bérlőt is felügyeli.A másik lehetőség az, hogy távolítsa el a hozzáférést az Azure Active Directory-felhasználó a Salesforce.com-bérlő, amellyel a címtár integrálva van. A következő szinkronizálási kísérletnél újra megpróbáljuk ezt a műveletet. 
* **SalesforceRequiredFieldMissing:** A Salesforce megköveteli, hogy bizonyos attribútumok jelen legyenek a felhasználón a felhasználó sikeres létrehozásához vagy frissítéséhez. Ennek a felhasználónak hiányzik az egyik szükséges attribútum. Győződjön meg arról, hogy az olyan attribútumok, például az e-mail és az alias, minden olyan felhasználóra fel vannak töltve, akiket ki szeretne építeni a Salesforce-ba. Az [attribútumalapú hatókörszűrőkkel](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)hatókörbe sodd a felhasználókat, akik nem rendelkeznek ezekkel az attribútumokkal. 
* A Salesforce-hoz való kiépítés alapértelmezett attribútumleképezése tartalmazza a SingleAppRoleAssignments kifejezést az appRoleAssignments leképezésére az Azure AD-ben a ProfileName-hez a Salesforce-ban. Győződjön meg arról, hogy a felhasználók nem rendelkeznek több alkalmazásszerepkör-hozzárendelések az Azure AD-ben, mivel az attribútum-leképezés csak támogatja egy szerepkör kiépítése. 
* A Salesforce megköveteli, hogy az e-mail-frissítéseket manuálisan hagyják jóvá a módosítás előtt. Ennek eredményeképpen több bejegyzés jelenhet meg a létesítési naplókban a felhasználó e-mail-címének frissítéséhez (amíg az e-mail módosítása nem lett jóváhagyva).


## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
