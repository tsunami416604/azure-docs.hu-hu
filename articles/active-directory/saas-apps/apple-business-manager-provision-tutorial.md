---
title: 'Oktatóanyag: az Apple Business Manager konfigurálása a felhasználók automatikus kiosztásához a Azure Active Directory segítségével | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből az Apple Business Managerbe.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4ad30031-9904-4ac3-a4d2-e8c28d44f319
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: c4ac4a17e577ea69f4359e1e9b321e5fcc839697
ms.sourcegitcommit: 4ce82b6df65ebd81157b6168d3aa4e7323355022
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90761541"
---
# <a name="tutorial-configure-apple-business-manager-for-automatic-user-provisioning"></a>Oktatóanyag: az Apple Business Manager konfigurálása automatikus felhasználók kiosztásához



Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket az Apple Business Managerben és a Azure Active Directory (Azure AD) is végre kell hajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat az [Apple Business Managernek](https://business.apple.com/) az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása az Apple Business Managerben
> * Felhasználók eltávolítása az Apple Business Managerben, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és az Apple Business Manager között

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure ad-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda).
* Egy Apple Business Manager-fiók a rendszergazda vagy a People Manager szerepkörrel.

> [!NOTE]
> A jogkivonat-átvitelt az Azure AD-be, és a sikeres kapcsolat létesítését 4 naptári napon belül kell végrehajtani, vagy a folyamatot újra kell indítani.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és az Apple Business Manager között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-apple-business-manager-to-support-provisioning-with-azure-ad"></a>2. lépés Az Apple Business Manager konfigurálása az Azure AD-vel való kiépítés támogatására

1. Az Apple Business Managerben jelentkezzen be egy olyan fiókkal, amely a rendszergazda vagy a People Manager szerepkörrel rendelkezik.
2. Kattintson az oldalsáv alján található beállítások elemre, kattintson a szervezeti beállítások alatti adatforrás lehetőségre, majd kattintson a kapcsolódás adatforráshoz lehetőségre.
3. Kattintson a Kapcsolódás a SCIM mellett lehetőségre, figyelmesen olvassa el a figyelmeztetést, kattintson a másolás, majd a Bezárás gombra.
[A Kapcsolódás a SCIM ablakhoz, amely egy jogkivonatot és egy másolási gombot biztosít.] Hagyja nyitva ezt az ablakot, és másolja a bérlői URL-címet az Apple Business Managerből az Azure AD-be, amely a következő: https://federation.apple.com/feeds/business/scim

    ![Apple Business Manager](media/applebusinessmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> A titkos token nem osztható meg senkivel az Azure AD-rendszergazda kivételével.

## <a name="step-3-add-apple-business-manager-from-the-azure-ad-application-gallery"></a>3. lépés Az Apple Business Manager hozzáadása az Azure AD Application Galleryből

Az Apple Business Manager az Azure AD Application Galleryből való hozzáadásával megkezdheti a kiépítés kezelését az Apple Business Manager alkalmazásban. Ha korábban már telepítette az Apple Business Managert az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Amikor felhasználókat rendel az Apple Business Managerhez, ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-business-manager"></a>5. lépés Az automatikus felhasználó üzembe helyezésének beállítása az Apple Business Managerben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Apple Business Manager**elemet.

    ![Az Apple Business Manager az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **scim 2,0 alap URL-címet és a hozzáférési jogkivonat** azon értékeit, amelyeket az Apple Business managerből lekért a **bérlői URL-címben** és a **titkos jogkivonatban** . Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak az Apple Business Managerhez. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy az Apple Business Manager-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Ha a kapcsolatok sikeresek, az Apple Business Manager a SCIM-kapcsolatokat aktívként jeleníti meg. Ez a folyamat akár 60 másodpercig is eltarthat, hogy az Apple Business Manager a legújabb kapcsolódási állapotot tükrözze.

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása az Apple Business Managerbe**lehetőséget.

9. Tekintse át az Azure AD-ból az Apple Business Managerbe szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Apple Business Managerben a frissítési műveletekhez használt felhasználói fiókokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |Active|Logikai|
   |userName (Felhasználónév)|Sztring|
   |név. givenName|Sztring|
   |név. familyName|Sztring|
   |név. givenName|Sztring|
   |externalId|Sztring|
   |területi beállítás|Sztring|
   |timezone|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|

10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Az Azure AD-kiépítési szolgáltatás az Apple Business Managerben való engedélyezéséhez módosítsa a **kiépítési állapotot** **a beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg az Apple Business Manager számára kiépíteni kívánt felhasználókat és/vagy csoportokat, ha a **Beállítások** szakasz **hatókör** részén kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
2. Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Az Apple Business Manager SCIM kapcsolatos követelmények áttekintése](URL=https://support.apple.com/guide/apple-business-manager/apdd88331cd6)
* [A személy AZONOSÍTÓjának használata az Apple Business Managerben](URL=https://support.apple.com/guide/apple-business-manager/apd69e1e48e9)
* [Felhasználók importálása az Apple Business Managerbe a SCIM használatával](URL=https://support.apple.com/guide/apple-business-manager/apd3ec7b95ad)
* [SCIM felhasználói fiókok ütközésének feloldása az Apple Business Managerben](URL=https://support.apple.com/guide/apple-business-manager/apd313013d12)
* [Az Apple Business Managerben megjelenő Azure AD-fiókok törlése](URL=https://support.apple.com/guide/apple-business-manager/apdaa5798fbe)
* [SCIM-tevékenység megtekintése az Apple Business Managerben](URL=https://support.apple.com/guide/apple-business-manager/apd1bfd8dfde)
* [Meglévő SCIM-jogkivonat és-kapcsolatok kezelése az Apple Business Managerben](URL=https://support.apple.com/guide/apple-business-manager/apdc9a823611)
* [A scim-kapcsolat leválasztása az Apple Business Managerben](URL=https://support.apple.com/guide/apple-business-manager/apd609be3a61) *  [Meglévő scim-jogkivonat és-kapcsolatok kezelése az Apple Business Managerben](URL=https://support.apple.com/guide/apple-business-manager/apdc9a8236e9)
* [Az SCIM-kapcsolatok hibaelhárítása az Apple Business Managerben](URL=https://support.apple.com/guide/apple-business-manager/apd403a0f3bd)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)

