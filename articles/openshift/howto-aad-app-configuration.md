---
title: Azure Active Directory integráció az Azure Red Hat OpenShift
description: Ismerje meg, hogyan hozhat létre Azure AD-beli biztonsági csoportot és felhasználót a Microsoft Azure Red Hat OpenShift-fürtön futó alkalmazások teszteléséhez.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 6e2437fadb743706d4f4215bbcbab8616817de5f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381477"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Active Directory integráció az Azure Red Hat OpenShift

Ha még nem hozott létre Azure Active Directory (Azure AD) bérlőt, kövesse az Azure [ad-bérlő létrehozása az Azure Red Hat OpenShift](howto-create-tenant.md) című témakör utasításait, mielőtt folytatná ezeket az utasításokat.

Microsoft Azure Red Hat OpenShift engedélyre van szüksége a fürt nevében elvégzendő feladatok végrehajtásához. Ha a szervezet még nem rendelkezik Azure AD-felhasználóval, Azure AD-biztonsági csoporttal vagy egy Azure AD-alkalmazás regisztrálásával, amelyet az egyszerű szolgáltatásként szeretne használni, kövesse ezeket az utasításokat a létrehozásához.

## <a name="create-a-new-azure-active-directory-user"></a>Egy új Azure Active Directory-felhasználó létrehozása

A [Azure Portalban](https://portal.azure.com)győződjön meg arról, hogy a bérlő a portál jobb felső részén a Felhasználónév alatt jelenik meg:

![a portált a](./media/howto-create-tenant/tenant-callout.png) jobb felső sarokban felsorolt Bérlővel, ha rossz bérlő jelenik meg, kattintson a jobb felső sarokban található felhasználónévre, majd kattintson a **váltás könyvtár**elemre, és válassza ki a megfelelő bérlőt a **minden könyvtár** listából.

Hozzon létre egy új Azure Active Directory globális rendszergazda felhasználót az Azure Red Hat OpenShift-fürtbe való bejelentkezéshez.

1. Lépjen a [felhasználók – minden felhasználó](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) panelre.
2. Kattintson az **+ új felhasználó** lehetőségre a **felhasználó** ablaktábla megnyitásához.
3. Adja meg a felhasználó **nevét** .
4. Hozzon létre egy **felhasználónevet** a létrehozott bérlő neve alapján, `.onmicrosoft.com` hozzáfűzéssel a végén. Például: `yourUserName@yourTenantName.onmicrosoft.com`. Jegyezze fel ezt a felhasználónevet. Szüksége lesz rá a fürtbe való bejelentkezéshez.
5. A címtárbeli szerepkör panel megnyitásához kattintson a **címtárbeli szerepkör** elemre, majd válassza a **globális rendszergazda** lehetőséget, majd kattintson az **OK** gombra a panel alján.
6. A **felhasználó** ablaktáblán kattintson a **jelszó megjelenítése** lehetőségre, és jegyezze fel az ideiglenes jelszót. Az első bejelentkezés után a rendszer felszólítja, hogy állítsa alaphelyzetbe.
7. A panel alján kattintson a **Létrehozás** gombra a felhasználó létrehozásához.

## <a name="create-an-azure-ad-security-group"></a>Azure AD biztonsági csoport létrehozása

A fürt rendszergazdai hozzáférésének megadásához az Azure AD biztonsági csoportba tartozó tagságok az "OSA-Customer-adminok" OpenShift-csoportba vannak szinkronizálva. Ha nincs megadva, a rendszer nem engedélyezi a fürt rendszergazdai hozzáférését.

1. Nyissa meg a [Azure Active Directory csoportok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) panelt.
2. Kattintson az **+ új csoport**lehetőségre.
3. Adja meg a csoport nevét és leírását.
4. Adja meg a **csoport típusát** a **Biztonság**beállításnál.
5. Adja meg a **tagsági típust** a **hozzárendeléshez**.

    Adja hozzá az előző lépésben létrehozott Azure AD-felhasználót a biztonsági csoporthoz.

6. Kattintson a **tagok** elemre a **Tagok kiválasztása** panel megnyitásához.
7. A tagok listában válassza ki a fent létrehozott Azure AD-felhasználót.
8. A portál alján kattintson a **kiválasztás** , majd a **Létrehozás** elemre a biztonsági csoport létrehozásához.

    Jegyezze fel a csoport AZONOSÍTÓjának értékét.

9. A csoport létrehozásakor megjelenik az összes csoport listájában. Kattintson az új csoportra.
10. A megjelenő lapon másolja le az **objektum azonosítóját**. Erre az értékre az [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md) című oktatóanyagban `GROUPID` fogunk hivatkozni.

> [!IMPORTANT]
> Ha a csoportot az OSA-Customer-adminok OpenShift csoporttal szeretné szinkronizálni, hozza létre a fürtöt az Azure CLI használatával. A Azure Portal jelenleg nem rendelkezik a csoport beállításához szükséges mezővel.

## <a name="create-an-azure-ad-app-registration"></a>Azure AD-alkalmazás regisztrálásának létrehozása

A fürt létrehozásának részeként automatikusan létrehozhat egy Azure Active Directory (Azure AD-beli) alkalmazás-regisztrációs ügyfelet, ha kihagyja a `--aad-client-app-id` jelzőt a `az openshift create` parancshoz. Ebből az oktatóanyagból megtudhatja, hogyan hozhatja létre a teljesség érdekében az Azure AD-alkalmazás regisztrációját.

Ha a szervezet még nem rendelkezik olyan Azure Active Directory (Azure AD-beli) alkalmazás-regisztrációval, amelyet egyszerű szolgáltatásnévként szeretne használni, akkor az alábbi utasításokat követve hozhat létre egyet.

1. Nyissa meg a [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) panelt, és kattintson az **+ új regisztráció**elemre.
2. Az **alkalmazás regisztrálása** ablaktáblán adja meg az alkalmazás regisztrációjának nevét.
3. Győződjön meg arról, hogy az **ebben a szervezeti könyvtárban csak** a **támogatott fióktípus** vannak kiválasztva. Ez a legbiztonságosabb megoldás.
4. Később egy átirányítási URI-t adunk hozzá, ha ismerjük a fürt URI-JÁT. Az Azure AD-alkalmazás regisztrációjának létrehozásához kattintson a **regisztráció** gombra.
5. A megjelenő oldalon másolja le az **alkalmazás (ügyfél) azonosítóját**. Erre az értékre az [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md) című oktatóanyagban `APPID` fogunk hivatkozni.

![Képernyőfelvétel az App Object lapról](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Ügyfél titkos kulcsának létrehozása

Ügyfél-titkos kulcs létrehozása az alkalmazás Azure Active Directory való hitelesítéséhez.

1. Az alkalmazás-regisztrációk lap **kezelés** szakaszában kattintson a **tanúsítványok & titkok**elemre.
2. A **tanúsítványok & titkok** ablaktáblán kattintson az **+ új ügyfél titka**elemre.  Megjelenik az **ügyfél titkos kulcsának hozzáadása** panel.
3. Adja meg a **leírást**.
4. A beállítás **lejárati** ideje a kívánt időtartam, például **2 év**.
5. Kattintson a **Hozzáadás** gombra, és a kulcs értéke megjelenik az oldal **ügyfél-titkok** szakaszában.
6. Másolja le a kulcs értékét. Erre az értékre az [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md) című oktatóanyagban `SECRET` fogunk hivatkozni.

![Képernyőkép a tanúsítványok és titkok panelről](./media/howto-create-tenant/create-key.png)

További információ az Azure Application Objects szolgáltatásról: [alkalmazás-és szolgáltatásnév-objektumok Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

További információ az új Azure AD-alkalmazások létrehozásáról: [Alkalmazások regisztrálása a Azure Active Directory v 1.0-s végponttal](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="add-api-permissions"></a>API-engedélyek hozzáadása

1. A **kezelés** szakaszban kattintson az **API-engedélyek**elemre.
2. Kattintson az **engedély hozzáadása** lehetőségre, és válassza ki **Azure Active Directory gráfot** , majd **delegált engedélyeket**. 
3. Bontsa ki a **felhasználó** elemet az alábbi listán, és engedélyezze a **User. Read** engedélyt. Ha a **User. Read** beállítás alapértelmezés szerint engedélyezve van, győződjön meg arról, hogy az **Azure Active Directory Graph** engedély **felhasználó. Read**, *nem* az **Microsoft Graph** engedély **felhasználó. Read**.
4. Görgessen felfelé, és válassza ki az **alkalmazás engedélyeit**.
5. Bontsa ki az alábbi lista **könyvtárát** , és engedélyezze a **Directory. ReadAll**
6. A módosítások elfogadásához kattintson az **engedélyek hozzáadása** lehetőségre.
7. Az API-engedélyek panelnek ekkor meg kell jelennie a *User. Read* és a *Directory. ReadAll*. Vegye figyelembe, hogy a **rendszergazda beleegyezett a szükséges** oszlopba a *Directory. ReadAll*mellett.
8. Ha Ön az *Azure-előfizetés rendszergazdája*, kattintson az alábbi  ***előfizetés neveként* a rendszergazdai jóváhagyás megadása** lehetőségre. Ha nem Ön az *Azure-előfizetés rendszergazdája*, kérje meg a rendszergazdától a hozzájárulásukat.
![képernyőkép az API-engedélyek panelről. User. Read és Directory. ReadAll engedélyek hozzáadva, rendszergazdai engedély szükséges a Directory. ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> A fürt-rendszergazdák csoport szinkronizálása csak a beleegyező engedély megadása után fog működni. Ekkor megjelenik egy pipa nevű zöld kör, és egy "megadott *előfizetés neve*" üzenet jelenik meg a *rendszergazdai engedély szükséges* oszlopban.

A rendszergazdák és egyéb szerepkörök kezelésével kapcsolatos részletekért lásd: [Azure-előfizetési rendszergazdák hozzáadása vagy módosítása](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).

## <a name="resources"></a>További források

* [Alkalmazások és egyszerű szolgáltatások objektumai a Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Gyors útmutató: alkalmazás regisztrálása a Azure Active Directory v 1.0 végponttal](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>Következő lépések

Ha teljesítette az összes [Azure Red Hat OpenShift előfeltételét](howto-setup-environment.md), készen áll az első fürt létrehozására.

Próbálja ki az oktatóanyagot:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)
