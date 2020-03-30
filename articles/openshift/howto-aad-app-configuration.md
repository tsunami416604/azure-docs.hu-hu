---
title: Azure Active Directory-integráció az Azure Red Hat OpenShift szolgáltatáshoz
description: Megtudhatja, hogy miként hozhat létre Azure AD-biztonsági csoportot és felhasználókat a Microsoft Azure Red Hat OpenShift-fürtalkalmazások teszteléséhez.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: a2eade6c5a9c826d28d435a09861ba58463ae8c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280533"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Active Directory-integráció az Azure Red Hat OpenShift szolgáltatáshoz

Ha még nem hozott létre egy Azure Active Directory (Azure AD) bérlőt, kövesse az [Azure Red Hat OpenShift azure-beli AD-bérlő létrehozása](howto-create-tenant.md) című útmutató utasításait, mielőtt folytatná ezeket az utasításokat.

A Microsoft Azure Red Hat OpenShift-nek engedélyekre van szüksége a fürt nevében végzett feladatok elvégzéséhez. Ha a szervezet még nem rendelkezik egy Azure AD-felhasználó, az Azure AD biztonsági csoport, vagy egy Azure AD-alkalmazás regisztrációja, hogy az egyszerű szolgáltatás, kövesse az alábbi utasításokat, hogy hozza létre őket.

## <a name="create-a-new-azure-active-directory-user"></a>Egy új Azure Active Directory-felhasználó létrehozása

Az [Azure Portalon](https://portal.azure.com)győződjön meg arról, hogy a bérlő a felhasználónév alatt jelenik meg a portál jobb felső részén:

![Képernyőkép a jobb felső sarokban lévő bérlőről: Ha nem a megfelelő](./media/howto-create-tenant/tenant-callout.png) bérlő jelenik meg, kattintson a felhasználónevére a jobb felső sarokban, majd kattintson a **Címtár váltása** **parancsra,** és válassza ki a megfelelő bérlőt a Minden könyvtár listájából.

Hozzon létre egy új Azure Active Directory globális rendszergazdai felhasználót, aki bejelentkezik az Azure Red Hat OpenShift fürtbe.

1. Nyissa meg a [Felhasználók – Minden felhasználó](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) panelt.
2. Kattintson **az +Új felhasználó** gombra a **Felhasználó** ablaktábla megnyitásához.
3. Adja meg a felhasználó **nevét.**
4. Hozzon létre egy **felhasználónevet** a létrehozott bérlő `.onmicrosoft.com` neve alapján, a végén hozzáfűzve. Például: `yourUserName@yourTenantName.onmicrosoft.com`. Írja le ezt a felhasználónevet. Szüksége lesz rá, hogy jelentkezzen be a fürtbe.
5. Kattintson **a Címtárszerepkör** gombra a címtárszerep-ablaktábla megnyitásához, válassza a **Globális rendszergazda** lehetőséget, majd kattintson az ablaktábla alján az **Ok** gombra.
6. A **Felhasználó** ablaktáblán kattintson a **Jelszó megjelenítése** elemre, és rögzítse az ideiglenes jelszót. Miután első alkalommal bejelentkezett, a rendszer kéri, hogy állítsa alaphelyzetbe.
7. Az ablaktábla alján kattintson a **Létrehozás** gombra a felhasználó létrehozásához.

## <a name="create-an-azure-ad-security-group"></a>Azure AD biztonsági csoport létrehozása

Fürtfelügyeleti hozzáférés biztosításához az Azure AD biztonsági csoport tagságai szinkronizálva vannak az "osa-customer-admins" OpenShift csoportba. Ha nincs megadva, a fürt felügyeleti hozzáférése nem lesz megadva.

1. Nyissa meg az [Azure Active Directory-csoportok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) panelt.
2. Kattintson **az +Új csoport gombra.**
3. Adja meg a csoport nevét és leírását.
4. Állítsa **a Csoport típusát** **Biztonság**beállításra.
5. Állítsa **a tagság típusát** **Hozzárendeltre.**

    Adja hozzá a korábbi lépésben létrehozott Azure AD-felhasználót ehhez a biztonsági csoporthoz.

6. Kattintson a **Tagok** gombra a **Tagok kiválasztása** ablaktábla megnyitásához.
7. A tagok listájában válassza ki a fent létrehozott Azure AD-felhasználót.
8. A portál alján kattintson a **Kijelölés gombra,** majd a **Létrehozás gombra** a biztonsági csoport létrehozásához.

    Írja le a csoportazonosító értékét.

9. A csoport létrehozásakor megjelenik az összes csoport listájában. Kattintson az új csoportra.
10. A megjelenő lapon másolja lefelé az **Objektumazonosítót**. Ezt az értéket az `GROUPID` [Azure Red Hat OpenShift fürt létrehozása](tutorial-create-cluster.md) oktatóanyagban fogjuk hivatkozni.

> [!IMPORTANT]
> Ha szinkronizálni szeretné ezt a csoportot az osa-customer-admins OpenShift csoporttal, hozza létre a fürtöt az Azure CLI használatával. Az Azure Portalon jelenleg nincs mező a csoport beállításához.

## <a name="create-an-azure-ad-app-registration"></a>Azure AD-alkalmazás regisztrációjának létrehozása

Automatikusan létrehozhat egy Azure Active Directory (Azure AD) alkalmazás regisztrációs ügyfél a `--aad-client-app-id` fürt `az openshift create` létrehozásának részeként a jelző kihagyásával a parancs. Ez az oktatóanyag bemutatja, hogyan hozhat létre az Azure AD alkalmazás regisztrációját a teljesség érdekében.

Ha a szervezet még nem rendelkezik egy Azure Active Directory (Azure AD) alkalmazás regisztrációját, hogy egyszerű szolgáltatásként használható, kövesse az alábbi utasításokat hozzon létre egyet.

1. Nyissa meg az [Alkalmazásregisztrációk panelt,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) és kattintson **az +Új regisztráció gombra.**
2. Az **Alkalmazás regisztrálása** ablaktáblában adja meg az alkalmazásregisztráció nevét.
3. Győződjön meg arról, hogy a **Támogatott fióktípusok** alatt a **Fiókok ebben a szervezeti címtárban csak** akkor van kiválasztva. Ez a legbiztonságosabb választás.
4. Később hozzáadunk egy átirányítási URI-t, amint megtudjuk a fürt URI-ját. Kattintson a **Regisztráció** gombra az Azure AD-alkalmazás regisztrációjának létrehozásához.
5. A megjelenő lapon másolja lefelé az **alkalmazás (ügyfél) azonosítóját.** Ezt az értéket az `APPID` [Azure Red Hat OpenShift fürt létrehozása](tutorial-create-cluster.md) oktatóanyagban fogjuk hivatkozni.

![Képernyőkép az alkalmazásobjektum lapról](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Ügyféltitok létrehozása

Hozzon létre egy ügyféltitkot az alkalmazás Azure Active Directoryba való hitelesítéséhez.

1. Az alkalmazásregisztrációk lap **Kezelés** szakaszában kattintson a **Tanúsítványok & titkos kulcsok**elemre.
2. A **Tanúsítványok & titkos kulcsok** ablaktáblán kattintson az **+Új ügyféltitok gombra.**  Megjelenik **az Ügyféltitkos hozzáadása** ablaktábla.
3. Adja meg **a leírást.**
4. Állítsa **lejár,** hogy a kívánt időtartamot, például **2 év**.
5. Kattintson a **Hozzáadás** gombra, és a kulcsérték megjelenik a lap **Ügyféltitkos kulcsok** szakaszában.
6. Másolja lefelé a kulcs értékét. Ezt az értéket az `SECRET` [Azure Red Hat OpenShift fürt létrehozása](tutorial-create-cluster.md) oktatóanyagban fogjuk hivatkozni.

![Képernyőkép a tanúsítványok és titkos kulcsok ablaktábláról](./media/howto-create-tenant/create-key.png)

Az Azure Application Objects alkalmazásobjektumokkal kapcsolatos további információkért [lásd: Alkalmazás- és egyszerű szolgáltatásobjektumok az Azure Active Directoryban.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

Az új Azure AD-alkalmazás létrehozásáról az [Alkalmazás regisztrálása az Azure Active Directory 1.0-s végpontjával](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)című témakörben talál.

## <a name="add-api-permissions"></a>API-engedélyek hozzáadása

[//]: # (Ne váltson Microsoft Graph programra. Nem működik a Microsoft Graph programmal.)
1. A **Kezelés szakaszban** kattintson az **API-engedélyek elemre.**
2. Kattintson **a Engedély hozzáadása** elemre, majd válassza az Azure Active Directory **Graph** lehetőséget, majd **a Delegált engedélyeket.**
> [!NOTE]
> Győződjön meg arról, hogy az "Azure Active Directory Graph" lehetőséget választotta, és nem a "Microsoft Graph" csempét.

3. **Bontsa** ki a Felhasználó elemet az alábbi listán, és engedélyezze a **User.Read** engedélyt. Ha **a User.Read** alapértelmezés szerint engedélyezve van, győződjön meg arról, hogy az **Azure Active Directory Graph** permission **User.Read.**
4. Görgessen felfelé, és válassza **az Alkalmazásengedélyek lehetőséget.**
5. Bontsa ki a **Könyvtárat** az alábbi listában, és engedélyezze a **Directory.ReadAll**.
6. A módosítások elfogadásához kattintson az **Engedélyek hozzáadása** gombra.
7. Az API-engedélyek panelen most antól a *User.Read* és *a Directory.ReadAll*fájl is megjelenik. Kérjük, vegye figyelembe a figyelmeztetés **admin hozzájárulás szükséges** oszlop mellett *Directory.ReadAll*.
8. Ha Ön az *Azure-előfizetés rendszergazdája,* kattintson a **Rendszergazdai hozzájárulás megadása *az előfizetés neve* ** az alábbi. Ha nem Ön az *Azure-előfizetés rendszergazdája,* kérje a rendszergazda hozzájárulását.

![Képernyőkép az API-engedélyek panelről. User.Read és Directory.ReadMinden hozzáadott engedélyek, rendszergazdai hozzájárulás szükséges Directory.ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> A fürtrendszergazdák csoportjának szinkronizálása csak a hozzájárulás megadása után fog működni. A *Rendszergazda hozzájárulása szükséges* oszlopban egy zöld kör jelenik meg, amelyen pipa és "Előfizetésneve biztosított" üzenet látható. *Subscription Name*

A rendszergazdák és egyéb szerepkörök kezeléséről az [Azure-előfizetés-rendszergazdák hozzáadása és módosítása](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)című témakörben talál részleteket.

## <a name="resources"></a>Források

* [Alkalmazások és egyszerű szolgáltatásobjektumok az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Rövid útmutató: Alkalmazás regisztrálása az Azure Active Directory 1.0-s verziójú végpontján](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>További lépések

Ha teljesítette az [Azure Red Hat OpenShift összes előfeltételét,](howto-setup-environment.md)készen áll az első fürt létrehozására!

Próbálja ki a bemutató:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)
