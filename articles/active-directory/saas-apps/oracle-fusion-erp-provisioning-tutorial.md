---
title: 'Oktatóanyag: Az Oracle Fusion ERP konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat az Oracle Fusion ERP szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 08a82be5a11ba7b81cd1939fc5b386c161c43480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061176"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Oktatóanyag: Az Oracle Fusion ERP konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa az Oracle Fusion ERP és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket, hogy az Azure AD automatikusan kiépítse és dekonzisztsa a felhasználókat és/vagy csoportokat az Oracle Fusion ERP-be.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* Oracle [Fusion ERP-bérlő](https://www.oracle.com/applications/erp/).
* Az Oracle Fusion ERP-ben rendszergazdai engedélyekkel rendelkező felhasználói fiók.

## <a name="assign-users-to-oracle-fusion-erp"></a>Felhasználók hozzárendelése az Oracle Fusion ERP-hez 
Az Azure Active Directory egy hozzárendelések nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük az Oracle Fusion ERP-hez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti az Oracle Fusion ERP-hez az alábbi utasításokat követve:
 
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Fontos tippek a felhasználók Oracle Fusion ERP-hez való hozzárendeléséhez 

 * Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve az Oracle Fusion ERP az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót hozzárendel az Oracle Fusion ERP-hez, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Az Oracle Fusion ERP beállítása a kiépítéshez

Mielőtt konfigurálná az Oracle Fusion ERP-t az Azure AD-vel való automatikus felhasználói kiépítéshez, engedélyeznie kell az SCIM-kiépítést az Oracle Fusion ERP-n.

1. Bejelentkezés az [Oracle Fusion ERP Felügyeleti konzolba](https://cloud.oracle.com/sign-in)

2. Kattintson a navigátorra a bal felső sarokban. Az **Eszközök csoportban**válassza a **Biztonsági konzol**lehetőséget.

    ![Oracle Fusion ERP SCIM hozzáadása](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. Keresse meg a **Felhasználók lehetőséget.**
    
    ![Oracle Fusion ERP SCIM hozzáadása](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Mentse el annak a rendszergazdai felhasználói fióknak a felhasználónevét és jelszavát, amelyet az Oracle Fusion ERP admin konzolra való bejelentkezéshez használ. Ezeket az értékeket meg kell adni a **Rendszergazdafelhasználó-név** és **jelszó** mezőkben az Oracle Fusion ERP-alkalmazás kiépítés lapján az Azure Portalon.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Oracle Fusion ERP hozzáadása a galériából

Az Oracle Fusion ERP konfigurálásához az Azure AD-vel való automatikus felhasználói kiépítéshez hozzá kell adnia az Oracle Fusion ERP-t az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni az Oracle Fusion ERP-t az Azure AD alkalmazásgalériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Oracle Fusion ERP**kifejezést, és válassza az **Oracle Fusion ERP** lehetőséget az eredménypanelen.

    ![Oracle Fusion ERP az eredménylistában](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Automatikus felhasználói kiépítés konfigurálása az Oracle Fusion ERP szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le az Oracle Fusion ERP-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Az Oracle Fusion ERP szolgáltatáshoz az [Oracle Fusion ERP egyszeri bejelentkezési útmutatójában](oracle-fusion-erp-tutorial.md)található utasításokat követve is engedélyezheti az SAML-alapú egyszeri bejelentkezést. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

> [!NOTE]
> Ha többet szeretne megtudni az Oracle Fusion ERP SCIM-végpontjáról, olvassa el [a REST API for Common Features for Oracle Applications Cloud](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)című részt.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>A Fuze automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Oracle Fusion ERP**lehetőséget.

    ![Az Oracle Fusion ERP hivatkozása az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` csoportban adja meg a **bérlői URL-címet.** Írja be a rendszergazdai felhasználónevet és a korábban beolvasott jelszót a **Rendszergazdai felhasználónév** és **jelszó** mezőbe. Kattintson az Azure AD és az Oracle Fusion ERP közötti **kapcsolat tesztelésére.** 

    ![Oracle Fusion ERP SCIM hozzáadása](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása az Oracle Fusion ERP szolgáltatással**lehetőséget.

    ![Oracle Fusion ERP SCIM hozzáadása](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Tekintse át az Azure AD és az Oracle Fusion ERP között szinkronizált felhasználói attribútumokat az **Attribútum-leképezés** szakaszban. Az **Egyező** tulajdonságokként kiválasztott attribútumok az Oracle Fusion ERP felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Oracle Fusion ERP SCIM hozzáadása](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása az Oracle Fusion ERP szolgáltatással**lehetőséget.

    ![Oracle Fusion ERP csoport leképezései](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD és az Oracle Fusion ERP között szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kiválasztott attribútumok az Oracle Fusion ERP csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Oracle Fusion ERP-csoport attribútumai](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez az Oracle Fusion ERP, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni az Oracle Fusion ERP-be a **Beállítások** szakasz **Hatókör** területén a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és követheti a kiépítési tevékenységjelentésre mutató hivatkozásokat, amelyek az Azure AD-kiépítési szolgáltatás által az Oracle Fusion ERP-n végrehajtott összes műveletet ismertetik.

    Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* Az Oracle Fusion ERP csak az SCIM-végpontjuk alapszintű hitelesítését támogatja.
* Az Oracle Fusion ERP nem támogatja a csoportkiépítést.
* Az Oracle Fusion ERP szerepkörei az Azure AD-ben csoportokra vannak leképezve. Ahhoz, hogy szerepköröket rendeljen a felhasználókhoz az Oracle Fusion ERP-ben az Azure AD-ből, hozzá kell rendelnie a felhasználókat a kívánt Azure AD-csoportokhoz, amelyek az Oracle Fusion ERP szerepkörei után vannak elnevezve.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
