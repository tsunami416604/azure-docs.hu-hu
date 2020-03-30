---
title: 'Oktatóanyag: A biztonsági rendszer konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és megszüntetje a felhasználói fiókokat a Harness szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 518d86fff04a23f1c1e63c44c53485b99f30637d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057838"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Oktatóanyag: A biztonsági rendszer automatikus felhasználói kiépítésének konfigurálása

Ebben a cikkben megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt (Azure AD) a felhasználók vagy csoportok automatikus kiépítésére és a további üzembe helyezhetők a Harness szolgáltatásba.

> [!NOTE]
> Ez a cikk egy összekötőt, amely az Azure AD felhasználói kiépítési szolgáltatás ra épül. A szolgáltatással kapcsolatos fontos tudnivalókért és a gyakori kérdésekre adott válaszokért olvassa el [a Felhasználói kiépítés és a SaaS-alkalmazások létesítésének automatizálása az Azure Active Directoryval című témakört.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg előzetes verzióban érhető el. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [A kábelköteg bérlője](https://harness.io/pricing/)
* *Rendszergazdai* engedélyekkel rendelkező felhasználói fiók a Biztonsági licencben

## <a name="assign-users-to-harness"></a>Felhasználók hozzárendelése a Biztonsági őchez

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói üzembe építés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak vagy csoportjainak kell hozzáférniük a Harnesshez. Ezután ezeket a felhasználókat vagy csoportokat a Hasznosítás hoz rendelheti hozzá a Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz című útmutató [utasításainak követésével.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-harness"></a>Fontos tippek a felhasználók harnesshoz való hozzárendeléséhez

* Azt javasoljuk, hogy rendeljen hozzá egy Azure AD-felhasználó a További hasznosítás az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót hozzárendel a Biztonsági asszejhez, a **Hozzárendelés** párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az *alapértelmezett hozzáférési* szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-harness-for-provisioning"></a>A Kábelköteg beállítása a kiépítéshez

1. Jelentkezzen be a [Biztonsági biztonsági szolgáltatás biztonsági szolgáltatásához,](https://app.harness.io/#/login)és lépjen a Folyamatos **biztonsági** > **hozzáférés-kezelés gombra.**

    ![Felügyeleti konzol kábelkötege](media/harness-provisioning-tutorial/admin.png)

1. Válassza az **API-kulcsok lehetőséget.**

    ![Az API-kulcsok kábelkötege](media/harness-provisioning-tutorial/apikeys.png)

1. Válassza **az API-kulcs hozzáadása lehetőséget.** 

    ![Az API-kulcs hozzáadása hivatkozás](media/harness-provisioning-tutorial/addkey.png)

1. Az **Api-kulcs hozzáadása** ablaktáblán tegye a következőket:

    ![Az Api-kulcs hozzáadása ablaktábla](media/harness-provisioning-tutorial/title.png)
   
   a. A **Név** mezőben adja meg a kulcs nevét.  
   b. Az **Örökölt engedélyek** legördülő listában válasszon egy beállítást. 
   
1. Válassza a **Küldés** lehetőséget.

1. Másolja a **kulcsot** későbbi használatra ebben az oktatóanyagban.

    ![Kábelköteg token létrehozása](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Harness hozzáadása a galériából

Mielőtt konfigurálja a Harness automatikus felhasználói kiépítés az Azure AD-vel, hozzá kell adnia a Harness az Azure AD-alkalmazáskatalógusa a felügyelt SaaS-alkalmazások listájához.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget.

    ![Az "Azure Active Directory" gomb](common/select-azuread.png)

1. Válassza **a Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**

    ![A "Minden alkalmazás" link](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az "Új alkalmazás" gomb](common/add-new-app.png)

1. A keresőmezőbe írja be a **Harness**, az eredménylistában a **Hám** elemet, majd az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

    ![Az eredménylista kihasználása](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Automatikus felhasználói kiépítés konfigurálása a Harness szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat vagy csoportokat hozzon létre, frissítsen és tiltson le a Biztonsági assziban az Azure AD-ben lévő felhasználói vagy csoport-hozzárendelések alapján.

> [!TIP]
> A [Harness egyszeri bejelentkezési útmutató](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)utasításait követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a Harness számára. Az automatikus felhasználói kiépítéstől függetlenül konfigurálhatja az egyszeri bejelentkezést, bár ez a két szolgáltatás kiegészíti egymást.

> [!NOTE]
> Ha többet szeretne megtudni a Harness SCIM-végpontról, tekintse meg a Harness [API Keys](https://docs.harness.io/article/smloyragsm-api-keys) cikket.

A Biztonsági beállítások azure-beli AD-hez való automatikus üzembeépítés konfigurálásához tegye a következőket:

1. Az [Azure portalon](https://portal.azure.com)válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

1. Az alkalmazások listájában válassza a **Harness**lehetőséget.

    ![Az alkalmazások listájában található Hám hivatkozás](common/all-applications.png)

1. Válassza **a Kiépítés lehetőséget.**

    ![A Kiépítés gomb](common/provisioning.png)

1. A **Kiépítési mód** legördülő listában válassza az **Automatikus**lehetőséget.

    ![A "Kiépítés mód" legördülő lista](common/provisioning-automatic.png)

1. A **Rendszergazdai hitelesítő adatok csoportban**tegye a következőket:

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)
 
   a. A **Bérlő URL-címe** mezőbe írja be a mezőt. **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`**  
   b. A **Titkos jogkivonat** mezőbe írja be az SCIM hitelesítési jogkivonat értékét, amelyet a "Kábelköteg beállítása kiépítéshez" szakasz 6.  
   c. Válassza **a Kapcsolat tesztelése** lehetőséget annak biztosításához, hogy az Azure AD csatlakozni tud-e a Harness-hez. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a biztonsági ügyfélfiók *rendszergazdai* engedélyekkel rendelkezik, majd próbálkozzon újra.

1. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, majd jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Az "Értesítési e-mail" mező](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **Leképezések**csoportban válassza **az Azure Active Directory-felhasználók szinkronizálása a hasznosítani**lehetőséget.

    ![Az "Azure Active Directory-felhasználók szinkronizálása a kábelezéshez" hivatkozás](media/harness-provisioning-tutorial/usermappings.png)

1. Az **Attribútum-leképezések csoportban**tekintse át az Azure AD-ről a Harness szolgáltatásba szinkronizált felhasználói attribútumokat. Az *egyeztetésként* kiválasztott attribútumok a Frissítési műveletek hez a Biztonsági licencben lévő felhasználói fiókok egyeztetésére szolgálnak. A módosítások véglegesítéséhez válassza a **Mentés** gombot.

    ![Az "Attribútumleképezések" felhasználó biztonsági őreinek biztonsági őrei](media/harness-provisioning-tutorial/userattributes.png)

1. A **Leképezések csoportban**válassza **az Azure Active Directory-csoportok szinkronizálása a kiaknázandó lehetőséget.**

    ![Az "Azure Active Directory-csoportok szinkronizálása a kiaknázatlanul" hivatkozás](media/harness-provisioning-tutorial/groupmappings.png)

1. Az **Attribútum-leképezések**csoportban tekintse át az Azure AD-ről a Harness szolgáltatásba szinkronizált csoportattribútumokat. Az *Egyező* tulajdonságokként kiválasztott attribútumok a Frissítési műveletek hez tartozó Biztonsági kábelköteg csoportjainak egyeztetésére szolgálnak. A módosítások véglegesítéséhez válassza a **Mentés** gombot.

    ![Az "Attribútumleképezések" biztonsági csoport biztonsági őrei ablaktábla](media/harness-provisioning-tutorial/groupattributes.png)

1. A hatókörszűrők konfigurálásáról az [Attribútumalapú alkalmazáskiépítés hatókörszűrőkkel című témakörben található.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

1. A **Beállítások csoportban**az Azure AD-kiépítési szolgáltatás a További beállítás engedélyezéséhez kapcsolja be a **Kiépítés állapota** kapcsolót be **kapcsolóra.**

    ![Az állapot kiépítése kapcsoló "Be" állásba van kapcsolva](common/provisioning-toggle-on.png)

1. A **Beállítások csoport**Hatókör **legördülő** listájában válassza ki, hogyan szeretné szinkronizálni a kiépíteni kívánt felhasználókat vagy csoportokat a Harness szolgáltatásba.

    ![Kiépítési hatókör](common/provisioning-scope.png)

1. Ha készen áll a kiépítésre, válassza a **Mentés gombot.**

    ![A kiépítés mentésgombja](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálást a felhasználók vagy csoportok kiépítés. A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbiek. Szinkronizálások fordulnak elő körülbelül 40 percenként, mindaddig, amíg az Azure AD kiépítési szolgáltatás fut. A folyamat figyeléséhez nyissa meg a **Szinkronizálás részletei szakaszt.** A kiépítési tevékenységjelentésre mutató hivatkozásokat is követhet, amely ismerteti az Azure AD-kiépítési szolgáltatás által a További biztonsági övben végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Jelentés az automatikus felhasználói fiók kiépítésről című témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
