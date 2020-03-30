---
title: 'Oktatóanyag: Az Elium konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiirtsa a felhasználói fiókokat az Elium szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a3e1c936d46e0d9b0ad3b98dafeda7df0b4a23db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058464"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Oktatóanyag: Az Elium konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag bemutatja, hogyan konfigurálhatja az Elium és az Azure Active Directory (Azure AD) automatikus kiépítése és de-provision felhasználók vagy csoportok Elium.

> [!NOTE]
> Ez az oktatóanyag egy összekötőt, amely az Azure AD felhasználói kiépítési szolgáltatás ra épül. A szolgáltatás működésével és működésével kapcsolatos fontos részletekről, valamint a gyakori kérdésekről [a Felhasználói kiépítés és a SaaS-alkalmazások létesítésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg előzetes verzióban érhető el. Az Azure-funkciók előzetes verzióban történő használatának általános feltételeit a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben tekintheti meg.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Egy Elium bérlő](https://www.elium.com/pricing/)
* Elium felhasználói fiókja, rendszergazdai engedélyekkel

## <a name="assigning-users-to-elium"></a>Felhasználók hozzárendelése az Eliumhoz

Az Azure AD egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és csoportjainak kell hozzáférniük az Eliumhoz. Ezután rendelje hozzá ezeket a felhasználókat és csoportokat az Eliumhoz a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)című lépéseit követve.

## <a name="important-tips-for-assigning-users-to-elium"></a>Fontos tippek a felhasználók Eliumhoz való hozzárendeléséhez 

Azt javasoljuk, hogy rendeljen hozzá egy Azure AD-felhasználó elium az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és csoportok rendelhetők hozzá.

Amikor egy felhasználót az Elium hoz rendel, a hozzárendelés párbeszédpanelen ki kell választania egy érvényes, alkalmazásspecifikus szerepkört (ha van ilyen). Az alapértelmezett **hozzáférési** szerepkört használó felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-elium-for-provisioning"></a>Az Elium beállítása kiépítéshez

Az Elium konfigurálása az Azure AD automatikus felhasználói kiépítéséhez, engedélyeznie kell a System for Cross-domain Identity Management (SCIM) kiépítését az Elium on Elium.Before configuring Elium for automatic user provisioning with Azure AD, you must enable System for Cross-domain Identity Management (SCIM) provisioning on Elium. Kövesse az alábbi lépéseket:

1. Jelentkezzen be az Elium szolgáltatásba, és lépjen a **Saját profilbeállítások** > **lapra.**

    ![Beállítások menüpont az Eliumban](media/Elium-provisioning-tutorial/setting.png)

1. A bal alsó sarokban a **SPECIÁLIS csoportban**válassza a **Biztonság**lehetőséget.

    ![Biztonsági link Eliumban](media/Elium-provisioning-tutorial/security.png)

1. Másolja a **bérlői URL-címet** és **a titkos jogkivonat-értékeket.** Ezeket az értékeket később fogja használni, a megfelelő mezőkben az Elium-alkalmazás **kiépítés** lapján az Azure Portalon.

    ![Bérlői URL- és titkos jogkivonat-mezők az Eliumban](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Elium hozzáadása a galériából

Az Elium konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez az Azure AD alkalmazásgyűjteményből is hozzá kell adnia az Eliumot a felügyelt szoftverszolgáltatásként (SaaS) alkalmazások listájához. Kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory menüelem](common/select-azuread.png)

1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

     ![Az Azure AD Enterprise alkalmazások panelje](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget az ablaktábla tetején.

    ![Új alkalmazáshivatkozás](common/add-new-app.png)

1. A keresőmezőbe írja be az **Elium**, válassza **az Elium** elemet az eredménylistában, majd kattintson a **Hozzáadás gombra** az alkalmazás hozzáadásához.

    ![Galéria keresőmezője](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Automatikus felhasználói kiépítés konfigurálása az Elium szolgáltatásba

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználókat és csoportokat hozzon létre, frissítsen és tiltson le az Eliumban, az Azure AD-ben lévő felhasználói és csoport-hozzárendelések alapján.

> [!TIP]
> Az Elium egyszeri bejelentkezését is engedélyezheti a Security Assertion Markup Language (SAML) alapján, ha követi az [Elium egyszeri bejelentkezési útmutató](Elium-tutorial.md)utasításait. Az automatikus felhasználói kiépítéstől függetlenül konfigurálhatja az egyszeri bejelentkezést, bár a két szolgáltatás kiegészíti egymást.

Az Elium automatikus felhasználói kiépítésének konfigurálásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com), válassza a **Vállalati alkalmazások**lehetőséget, majd válassza az **Összes alkalmazás**lehetőséget.

    ![Az Azure AD Enterprise alkalmazások panelje](common/enterprise-applications.png)

1. Az alkalmazások listájában válassza az **Elium**lehetőséget.

    ![Alkalmazások listája a Vállalati alkalmazások panelen](common/all-applications.png)

1. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap a Vállalati alkalmazások panelen](common/provisioning.png)

1. Állítsa a **létesítési módot** **Automatikus**ra.

    ![A létesítési mód automatikus beállítása](common/provisioning-automatic.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban írja be ** \<a tenantURL\>/scim/v2** címet a **bérlői URL-cím** mezőbe. (A **tenantURL** az Elium felügyeleti konzolról korábban beolvasott érték.) Írja be az Elium **titkos token** értékét is a **Titkos jogkivonat** mezőbe. Végül válassza **a Kapcsolat tesztelése** lehetőséget annak ellenőrzéséhez, hogy az Azure AD csatlakozhat-e az Eliumhoz. Ha a kapcsolat nem sikerül, ellenőrizze, hogy az Elium-fiók rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Bérlői URL- és titkosjogkivonat-mezők a rendszergazdai hitelesítő adatokban](common/provisioning-testconnection-tenanturltoken.png)

1. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, aki vagy amely megkapja a kiépítési hibaértesítéseket. Ezután jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **Leképezések** szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása Elium szolgáltatásba**lehetőséget.

    ![Az Azure AD-felhasználók Eliumhoz való hozzárendelésére szolgáló hivatkozás szinkronizálása](media/Elium-provisioning-tutorial/usermapping.png)

1. Tekintse át az Azure AD és Elium között szinkronizált felhasználói attribútumokat az **Attribútumleképezések** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok az Elium felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez válassza a **Mentés** gombot.

    ![Attribútumleképezések az Azure AD és az Elium között](media/Elium-provisioning-tutorial/userattribute.png)

1. A hatókörszűrők konfigurálásához kövesse a [Hatókörszűrő oktatóanyagának utasításait.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

1. Az Elium Azure AD-kiépítési szolgáltatás engedélyezéséhez módosítsa a **Kiépítés állapotát** **be a** **Beállítások** szakaszban.

    ![Az állapot kiépítése bevan kapcsolva](common/provisioning-toggle-on.png)

1. Adja meg az eliumba kiépíteni kívánt felhasználókat és csoportokat a **Beállítások** szakasz **Hatókör** legördülő listájában kívánt értékek kiválasztásával.

    ![Hatókör kiépítése lista](common/provisioning-scope.png)

1. Ha készen áll a kiépítésre, válassza a **Mentés gombot.**

    ![Mentés gomb a konfiguráció kiépítéséhez](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz Hatókör **szakaszában** definiált összes felhasználó és csoport kezdeti **szinkronizálását.** Ez a kezdeti szinkronizálási folyamat hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. A kiépítéshez szükséges időről a [Mennyi ideig tart a felhasználók kiépítése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Az **Aktuális állapot** szakaszban figyelheti a folyamatot, és követheti a létesítési tevékenységjelentésre mutató hivatkozásokat. A létesítési tevékenység jelentés ismerteti az Azure AD-kiépítési szolgáltatás az Elium által végrehajtott összes műveletet. További információ: [A felhasználói kiépítés állapotának ellenőrzése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Az Azure AD kiépítési naplók olvasásához olvassa el [az Automatikus felhasználói fiók kiépítésről szóló jelentéskészítés című témakört.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókkiépítési kezelésének kezelése.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
