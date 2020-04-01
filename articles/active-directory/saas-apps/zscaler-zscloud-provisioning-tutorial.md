---
title: 'Oktatóanyag: A Zscaler ZSCloud konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt úgy, hogy automatikusan kiépítse és megszüntetze a felhasználói fiókokat a Zscaler ZSCloud szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: a752be80-d3ef-45d1-ac8f-4fb814c07b07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: af9d4aa15a4ff2784d2e1ca1334d9c24a8d12251
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77062676"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Oktatóanyag: A Zscaler ZSCloud konfigurálása automatikus felhasználói kiépítéshez

Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt (Azure AD) a felhasználók és/vagy csoportok zscaler ZSCloudba való automatikus kiépítéséhez és megszüntetéséhez.

> [!NOTE]
> Ez az oktatóanyag ismerteti az Azure AD felhasználói létesítési szolgáltatásra épülő összekötő. A szolgáltatás működésével és működésével kapcsolatos fontos részleteket, valamint a gyakori kérdésekre adott válaszokat a [Felhasználói kiépítés és a SaaS-alkalmazások létesítésének automatizálása az Azure Active Directoryval kapcsolatban.](../active-directory-saas-app-provisioning.md)


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett lépések végrehajtásához a következőkre van szükség:

* Egy Azure AD-bérlő.
* Zscaler ZSCloud-bérlő.
* A Zscaler ZSCloud felhasználói fiókja rendszergazdai engedélyekkel.

> [!NOTE]
> Az Azure AD-kiépítési integráció a Zscaler ZSCloud SCIM API-t, amely elérhető az Enterprise-fiókok.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Zscaler ZSCloud hozzáadása a galériából

Mielőtt konfigurálna zscaler ZSCloud automatikus felhasználói kiépítés az Azure AD-vel, hozzá kell adnia zscaler ZSCloud az Azure AD alkalmazáskatalógusa a felügyelt SaaS-alkalmazások listájához.

Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget:

![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

Nyissa meg a **Vállalati alkalmazások,** és válassza a **Minden alkalmazás**lehetőséget:

![Vállalati alkalmazások](common/enterprise-applications.png)

Alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget az ablak tetején:

![Új alkalmazás kiválasztása](common/add-new-app.png)

A keresőmezőbe írja be a **Zscaler ZSCloud értéket.** Az eredmények között válassza a **Zscaler ZSCloud** lehetőséget, majd a **Hozzáadás**lehetőséget.

![Eredmények listája](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Felhasználók hozzárendelése a Zscaler ZSCloud-hoz

Az Azure AD-felhasználóknak hozzáférést kell biztosítani a kiválasztott alkalmazásokhoz, mielőtt használhatnák őket. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói üzembe építés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Zscaler ZSCloudhoz. Miután úgy döntött, hogy ezeket a felhasználókat és csoportokat hozzárendelheti a Zscaler ZSCloudhoz, kövesse a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz című utasításait.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Fontos tippek a felhasználók Zscaler ZSCloudhoz való hozzárendeléséhez

* Azt javasoljuk, hogy először rendeljen hozzá egy Azure AD-felhasználózt a Zscaler ZSCloudhoz az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználókat és csoportokat rendelhet hozzá.

* Amikor hozzárendel egy felhasználót a Zscaler ZSCloudhoz, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés iadpárbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-automatic-user-provisioning"></a>Automatikus felhasználói kiépítés beállítása

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépéseit a Zscaler ZSCloud felhasználói és csoport-hozzárendelései alapján a Zscaler ZSCloudban, az Azure AD felhasználói és csoport-hozzárendelések alapján történő konfigurálása.

> [!TIP]
> Előfordulhat, hogy a Zscaler ZSCloud saml-alapú egyszeri bejelentkezést is engedélyezni szeretne. Ha igen, kövesse a [Zscaler ZSCloud egyszeri bejelentkezési oktatóanyag](zscaler-zsCloud-tutorial.md)utasításait. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, de a két funkció kiegészíti egymást.

1. Jelentkezzen be az [Azure portalra,](https://portal.azure.com) és válassza a **Vállalati alkalmazások** > **minden alkalmazás** > **Zscaler ZSCloud**:

    ![Vállalati alkalmazások](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Zscaler ZSCloud**lehetőséget:

    ![Alkalmazások listája](common/all-applications.png)

3. Válassza a **Kiépítés** lapot:

    ![Zscaler ZSCloud kiépítés](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Állítsa a **létesítési módot** **automatikusra:**

    ![A kiépítési mód beállítása](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Zscaler ZSCloud-fiók **bérlői URL-címét** és **titkos jogkivonatát** a következő lépésben leírtak szerint.

6. A **bérlői URL és** a **titkos jogkivonat**lekéréséhez nyissa meg a **Zscaler** > ZSCloud portál felügyeleti**hitelesítési beállításait,** és válassza az **SAML** lehetőséget a **Hitelesítés típusa csoportban:**

    ![Zscaler ZSCloud hitelesítési beállítások](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Az **SAML konfigurálása** lehetőséget választ az **SAML konfigurálása** ablak megnyitásához:

    ![SAML ablak konfigurálása](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    Válassza **az Scim-alapú kiépítés engedélyezése** lehetőséget, és másolja az alap **URL-címet** és a **tulajdonosi tokent,** majd mentse a beállításokat. Az Azure Portalon illessze be az **alap URL-címet** a **bérlői URL-címbe** és a **tulajdonosi jogkivonatot** a **titkos jogkivonatmezőbe.**

7. Miután megadta az értékeket a **bérlői URL-cím** és a **titkos jogkivonat** mezőkben, válassza a **Kapcsolat tesztelése** lehetőséget, hogy az Azure AD képes-e csatlakozni a Zscaler ZSCloudhoz. Ha a kapcsolat nem sikerül, ellenőrizze, hogy a Zscaler ZSCloud-fiók rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![A kapcsolat tesztelése](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, amelynek meg kell kapnia a létesítési hibaértesítéseket. Válassza **az E-mail értesítés küldése hiba esetén**lehetőséget:

    ![Értesítési e-mail beállítása](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések** szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása a ZscalerZSCloud szolgáltatással**lehetőséget:

    ![Az Azure AD-felhasználók szinkronizálása](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Tekintse át az Azure AD és a Zscaler ZSCloud között szinkronizált felhasználói attribútumokat az **Attribútum-leképezések** szakaszban. Az **egyező** tulajdonságokként kiválasztott attribútumok a Zscaler ZSCloud felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez válassza a **Mentés** gombot.

    ![Attribútumleképezések](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a ZscalerZSCloud szolgáltatással**lehetőséget:

    ![Azure AD-csoportok szinkronizálása](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Tekintse át az Azure AD és a Zscaler ZSCloud között szinkronizált csoportattribútumokat az **Attribútum-leképezések** szakaszban. Az **egyező** tulajdonságokként kiválasztott attribútumok a Zscaler ZSCloud csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez válassza a **Mentés** gombot.

    ![Attribútumleképezések](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagának utasításait.](./../active-directory-saas-scoping-filters.md)

15. Az Azure AD-kiépítési szolgáltatás zscaler ZSCloud engedélyezéséhez módosítsa a **Kiépítési állapot** **ot Be** állapotra a **Beállítások** szakaszban:

    ![Kiépítés állapota](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Zscaler ZSCloud ba, válassza ki a kívánt értékeket a Beállítások szakasz **Hatókör** **csoportjában:**

    ![Hatókör-értékek](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Ha készen áll a kiépítésre, válassza a **Mentés lehetőséget:**

    ![Mentés kiválasztása](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Ez a művelet elindítja a Beállítások szakasz Hatókör **e** területen definiált összes felhasználójának és csoportjának kezdeti **szinkronizálását.** A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, mindaddig, amíg az Azure AD-kiépítési szolgáltatás fut. A **folyamatot** a Szinkronizálás részletei szakaszban figyelheti. A zscaler ZSCloud-on az Azure AD-kiépítési szolgáltatás által végrehajtott összes műveletet ismerteti.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../active-directory-saas-provisioning-reporting.md)olvashat.

## <a name="additional-resources"></a>További források

* [A felhasználói fiókok vállalati alkalmazásokhoz való kiépítésének kezelése](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png
