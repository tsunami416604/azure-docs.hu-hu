---
title: 'Oktatóanyag: a Salesforce konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Salesforce között.
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
ms.openlocfilehash: 64de004a1d9b3aa011c447fdded51658582586b0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "68825778"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Salesforce konfigurálása

Ennek az oktatóanyagnak a célja, hogy megmutassa a Salesforce és az Azure AD végrehajtásához szükséges lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a Salesforce.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Azure Active Directory-bérlő
* Salesforce.com-bérlő

> [!IMPORTANT]
> Ha Salesforce.com próbaverziós fiókot használ, akkor nem fogja tudni beállítani az automatikus felhasználó-kiépítés beállítását. A próbaverziós fiókokhoz nincs engedélyezve a szükséges API-hozzáférés, amíg meg nem vásárolják őket. Ezt a korlátozást egy ingyenes [fejlesztői fiók](https://developer.salesforce.com/signup) használatával érheti el az oktatóanyag elvégzéséhez.

Ha Salesforce sandbox-környezetet használ, tekintse meg a [Salesforce homokozó integrációs oktatóanyagát](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Felhasználók kiosztása a Salesforce

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra vagy csoportokra van szükség az Azure AD-ben a Salesforce alkalmazás eléréséhez. A döntés elvégzése után ezeket a felhasználókat hozzárendelheti a Salesforce-alkalmazáshoz a [felhasználó vagy csoport társítása vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) című részben leírtak szerint.

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Fontos Tippek a felhasználók Salesforce való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Salesforce a létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha a felhasználó Salesforce rendel hozzá, ki kell választania egy érvényes felhasználói szerepkört. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során

    > [!NOTE]
    > Ez az alkalmazás a Salesforce származó profilokat importálja a létesítési folyamat részeként, amelyet az ügyfél kiválaszthat a felhasználók Azure AD-ben való hozzárendeléséhez. Vegye figyelembe, hogy a Salesforce importált profilok szerepkörökként jelennek meg az Azure AD-ben.

## <a name="enable-automated-user-provisioning"></a>Automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-nek a Salesforce felhasználói fiók létesítési API-hoz való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán az Azure AD-ben a felhasználó-és Salesforce alapján a felhasználói fiókok létrehozásához, frissítéséhez és letiltásához.

> [!Tip]
> Dönthet úgy is, hogy engedélyezte az SAML-alapú egyszeri bejelentkezést a Salesforce, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="configure-automatic-user-account-provisioning"></a>a felhasználói fiókok automatikus üzembe helyezésének konfigurálása

Ennek a szakasznak a célja annak ismertetése, hogyan engedélyezhető Active Directory felhasználói fiókok Salesforce való kiépítés.

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

2. Ha már konfigurálta a Salesforce az egyszeri bejelentkezéshez, keresse meg a Salesforce-példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **Salesforce** az alkalmazás-gyűjteményben. Válassza a Salesforce lehetőséget a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

3. Válassza ki a Salesforce példányát, majd válassza a **kiépítés** lapot.

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![kiépítési](./media/salesforce-provisioning-tutorial/provisioning.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a következő konfigurációs beállításokat:

    a. A **rendszergazda felhasználóneve** szövegmezőbe írja be a Salesforce-fiók nevét, amely a Salesforce.com hozzárendelt **rendszergazda** profillal rendelkezik.

    b. A **rendszergazdai jelszó** szövegmezőbe írja be a fiókhoz tartozó jelszót.

6. A Salesforce biztonsági jogkivonat beszerzéséhez nyisson meg egy új lapot, és jelentkezzen be ugyanahhoz a Salesforce-rendszergazdai fiókba. A lap jobb felső sarkában kattintson a nevére, majd a **Beállítások**elemre.

    ![Automatikus felhasználó-kiépítés engedélyezése](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Automatikus felhasználó-kiépítés engedélyezése")

7. A bal oldali navigációs panelen kattintson a **személyes adatok** elemre a kapcsolódó szakasz kibontásához, majd kattintson a **biztonsági jogkivonat alaphelyzetbe állítása**elemre.
  
    ![Automatikus felhasználó-kiépítés engedélyezése](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Automatikus felhasználó-kiépítés engedélyezése")

8. A **biztonsági jogkivonat alaphelyzetbe állítása** lapon kattintson a **biztonsági jogkivonat alaphelyzetbe állítása** gombra.

    ![Automatikus felhasználó-kiépítés engedélyezése](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Automatikus felhasználó-kiépítés engedélyezése")

9. Keresse meg a rendszergazdai fiókhoz társított e-mailek mappáját. Keressen egy e-mailt az új biztonsági jogkivonatot tartalmazó Salesforce.com.

10. Másolja ki a tokent, nyissa meg az Azure AD-ablakot, és illessze be a **titkos jogkivonat** mezőbe.

11. A **bérlői URL-címet** meg kell adni, ha a Salesforce példánya a Salesforce Government felhőben van. Ellenkező esetben nem kötelező. Adja meg a bérlői URL-címet a "https://\<your-instance \>. my.salesforce.com" formátumban, a \<your példány \> helyett a Salesforce-példány nevét.

12. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a Salesforce-alkalmazáshoz.

13. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az alábbi jelölőnégyzetet.

14. Kattintson a **Mentés gombra.**  

15. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Salesforce lehetőséget.**

16. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből az Salesforce-be szinkronizált felhasználói attribútumokat. Vegye figyelembe, hogy az **egyező** tulajdonságokként kiválasztott attribútumok a Salesforce felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a Save (Mentés) gombra.

17. Az Azure AD-kiépítési szolgáltatás Salesforce való engedélyezéséhez módosítsa a **kiépítési állapotot** a következőre a beállítások **szakaszban:**

18. Kattintson a **Mentés gombra.**

> [!NOTE]
> A felhasználók a Salesforce alkalmazásban való kiépítés után a rendszergazdának konfigurálnia kell a nyelvspecifikus beállításait. A nyelvi konfigurációval kapcsolatos további információkért tekintse meg [ezt](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) a cikket.

Ezzel elindítja a felhasználók és csoportok szakaszban Salesforce rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a Salesforce alkalmazásban végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../manage-apps/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
