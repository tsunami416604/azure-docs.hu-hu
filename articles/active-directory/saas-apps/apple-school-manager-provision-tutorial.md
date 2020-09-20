---
title: 'Oktatóanyag: az Apple School Manager konfigurálása a felhasználók automatikus kiosztásához a Azure Active Directory segítségével | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből az Apple School Managerbe.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: a6543c56f7170f674a227421575ba7f842fb29a8
ms.sourcegitcommit: 4ce82b6df65ebd81157b6168d3aa4e7323355022
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90761492"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>Oktatóanyag: az Apple School Manager konfigurálása automatikus felhasználók kiosztásához



Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket az Apple School Managerben és a Azure Active Directory (Azure AD) is végre kell hajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat az [Apple School Managernek](https://school.apple.com/) az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása az Apple School Managerben
> * Felhasználók eltávolítása az Apple School Managerben, ha már nincs szükség hozzáférésre
> * Adott felhasználói attribútumok szinkronizálása az Azure AD és az Apple School Manager között

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Egy Apple School Manager-fiók a rendszergazda, a Site Manager vagy a People Manager szerepkörrel.

> [!NOTE]
> A jogkivonat-átvitelt az Azure AD-be, és a sikeres kapcsolat létesítését 4 naptári napon belül kell végrehajtani, vagy a folyamatot újra kell indítani.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és az Apple School Manager között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>2. lépés Az Apple School Manager konfigurálása az Azure AD-vel való kiépítés támogatására

1. Az Apple School Managerben jelentkezzen be egy olyan fiókkal, amely a rendszergazda, a Site Manager vagy a People Manager szerepkörrel rendelkezik.
2. Kattintson az oldalsáv alján található beállítások elemre, kattintson a szervezeti beállítások alatti adatforrás lehetőségre, majd kattintson a kapcsolódás adatforráshoz lehetőségre.
3. Kattintson a Kapcsolódás a SCIM mellett lehetőségre, figyelmesen olvassa el a figyelmeztetést, kattintson a másolás, majd a Bezárás gombra.
[A Kapcsolódás a SCIM ablakhoz, amely egy jogkivonatot és egy másolási gombot biztosít.] Hagyja nyitva ezt az ablakot, és másolja a bérlői URL-címet az Apple Business Managerből az Azure AD-be, amely a következő: https://federation.apple.com/feeds/school/scim

    ![Apple School Manager](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> A titkos token nem osztható meg senkivel az Azure AD-rendszergazda kivételével.

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>3. lépés Az Apple School Manager hozzáadása az Azure AD Application Galleryből

Az Apple School Manager hozzáadása az Azure AD-alkalmazás-katalógusból az Apple School Managerbe való kiépítés kezelésének megkezdéséhez. Ha korábban már telepítette az Apple School Managert az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Amikor felhasználókat rendel az Apple School Managerhez, ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>5. lépés Az automatikus felhasználó üzembe helyezésének beállítása az Apple School Managerben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Apple School Manager**elemet.

    ![Az Apple School Manager az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **scim 2,0 alap URL-címet és a hozzáférési jogkivonat** azon értékeit, amelyeket az Apple School managerből lekért a **bérlői URL-címben** és a **titkos jogkivonatban** . Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni az Apple School Managerhez. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy az Apple School Manager-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Ha a művelet sikeres, az Apple School Manager a SCIM-kapcsolatokat aktívként jeleníti meg. Ez a folyamat akár 60 másodpercig is eltarthat, hogy az Apple School Manager a legújabb kapcsolódási állapotot tükrözze.

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása az Apple School Managerbe**lehetőséget.

9. Tekintse át az Azure AD-ból az Apple School Managerbe szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Apple School Managerben a frissítési műveletekhez használt felhasználói fiókokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

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

11. Az Azure AD-kiépítési szolgáltatás az Apple School Managerben való engedélyezéséhez módosítsa a **kiépítési állapotot** **a következőre** a beállítások szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg az Apple School Manager számára kiépíteni kívánt felhasználókat és/vagy csoportokat, ha a **Settings (beállítások** ) szakaszban szeretné kiválasztani a kívánt értékeket a **hatókörben** .

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
* [Az Apple School Manager SCIM-követelményeinek áttekintése](URL=https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [A személy AZONOSÍTÓjának használata az Apple School Managerben](URL=https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [Felhasználók importálása az Apple School Managerbe a SCIM használatával](URL=https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [SCIM felhasználói fiókok ütközésének feloldása az Apple School Managerben](URL=https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [Az Apple School Managerben megjelenő Azure AD-fiókok törlése](URL=https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [SCIM-tevékenység megtekintése az Apple School Managerben](URL=https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [Meglévő SCIM-jogkivonat és-kapcsolatok kezelése az Apple School Managerben](URL=https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* [A scim-kapcsolat leválasztása az Apple School Managerben](URL=https://support.apple.com/guide/apple-school-manager/apd609be3a61)[meglévő scim-jogkivonat és-kapcsolatok kezelése az Apple School Managerben](URL=https://support.apple.com/guide/apple-school-manager/apdc9a8236e9)
* [Az SCIM-kapcsolatok hibaelhárítása az Apple School Managerben](URL=https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
