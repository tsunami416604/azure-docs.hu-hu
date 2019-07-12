---
title: Az Azure Red Hat OpenShift az Azure Active Directory-integráció |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy Azure AD biztonsági csoport és a felhasználó a Microsoft Azure Red Hat OpenShift fürtön lévő alkalmazások teszteléséhez.
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2019
ms.openlocfilehash: 00609905d09f8d414660c21805c6efca5eb30843
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669387"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Az Azure Red Hat OpenShift az Azure Active Directory-integráció

Ha még nem hozott létre egy Azure Active Directory (Azure AD) bérlő, kövesse az utasításokat [Azure Red Hat OpenShift az Azure AD-bérlő létrehozása](howto-create-tenant.md) ezeket az utasításokat a folytatás előtt.

A Microsoft Azure Red Hat OpenShift engedélyre van szüksége a fürt nevében feladatok végrehajtásához. Ha a szervezete még nem rendelkezik Azure AD-felhasználót, az Azure AD biztonsági csoportot vagy egy Azure AD-alkalmazás regisztrációjának használja, mint az egyszerű szolgáltatás, az alábbi lépésekkel hozhatja létre őket.

## <a name="create-a-new-azure-active-directory-user"></a>Egy új Azure Active Directory-felhasználó létrehozása

Az a [az Azure portal](https://portal.azure.com), győződjön meg arról, hogy a bérlő a felhasználónév felső területen jelenik meg, a portál jobb:

![Képernyőkép a portal a bérlő szerepel a jobb felső sarokban](./media/howto-create-tenant/tenant-callout.png) Ha rossz bérlőhöz megjelenik, kattintson a jobb felső sarokban a felhasználónevére, majd kattintson **címtár váltása**, és válassza ki a megfelelő bérlő, a **összes Könyvtárak** listája.

Hozzon létre egy új Azure Active Directory globális rendszergazdai felhasználót, jelentkezzen be az Azure Red Hat OpenShift fürt.

1. Nyissa meg a [felhasználók – minden felhasználó](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) panelen.
2. Kattintson a **+ új felhasználó** megnyitásához a **felhasználói** ablaktáblán.
3. Adjon meg egy **neve** ehhez a felhasználóhoz.
4. Hozzon létre egy **felhasználónév** alapján hozott létre, a bérlő nevével rendelkező `.onmicrosoft.com` hozzáfűzi a végén. Például: `yourUserName@yourTenantName.onmicrosoft.com`. Jegyezze fel ezt a felhasználói nevet. Szüksége lesz rá a fürthöz való bejelentkezéshez.
5. Kattintson a **címtárbeli szerepkör** nyissa meg a címtár szerepkörrel ablaktáblát, és válassza ki **globális rendszergazdai** majd **Ok** a panel alján.
6. Az a **felhasználói** ablaktáblán kattintson a **jelszó megjelenítése** , és jegyezze fel az ideiglenes jelszót. Miután először bejelentkezik, a program felszólítja állítsa alaphelyzetbe.
7. A panel alján kattintson **létrehozás** a felhasználó létrehozásához.

## <a name="create-an-azure-ad-security-group"></a>Az Azure AD biztonsági csoport létrehozása

A fürt rendszergazdai hozzáférést a az Azure AD biztonsági csoportok tagságát szinkronizálja az OpenShift csoport "osa-ügyfél-Adminisztrátorok". Ha nincs megadva, a fürt rendszergazdai hozzáférés nélkül kapnak.

1. Nyissa meg a [Azure Active Directory-csoportok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) panelen.
2. Kattintson a **+ új csoport**.
3. Adja meg a csoport nevét és leírását.
4. Állítsa be **csoporttípust** való **biztonsági**.
5. Állítsa be **tagságtípusának** való **hozzárendelt**.

    Adja hozzá a biztonsági csoporthoz az előző lépésben létrehozott Azure AD-felhasználót.

6. Kattintson a **tagok** megnyitásához a **tagok kiválasztása** ablaktáblán.
7. A tagok listában válassza ki a fent létrehozott Azure AD-felhasználót.
8. A portál alján kattintson a **kiválasztása** , majd **létrehozás** a biztonsági csoport létrehozásához.

    Jegyezze fel a csoport azonosító értéke.

9. A csoport létrehozását követően láthatja az összes csoportot a listában. Kattintson az új csoport.
10. A megjelenő lapon másolja le a **Objektumazonosító**. Ezt az értéket hivatkozni fogunk `GROUPID` a a [hozzon létre egy Azure Red Hat OpenShift fürtöt](tutorial-create-cluster.md) oktatóanyag.

## <a name="create-an-azure-ad-app-registration"></a>Hozzon létre egy Azure AD alkalmazás regisztrálása

Automatikusan létrehozhat egy Azure Active Directory (Azure AD-) alkalmazás regisztrációs ügyfél felsorolhatja a fürt létrehozásának részeként a `--aad-client-app-id` jelzőt a `az openshift create` parancsot. Ez az oktatóanyag bemutatja, hogyan hozhat létre az Azure AD-alkalmazás regisztrációjának lépés teljességet szolgálja.

Ha a szervezete még nem rendelkezik egy Azure Active Directory (Azure AD) alkalmazásregisztráció adatokként egy egyszerű szolgáltatást, az alábbi lépésekkel hozzon létre egyet.

1. Nyissa meg a [regisztrációk panelére](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) kattintson **+ új regisztrációs**.
2. Az a **alkalmazás regisztrálása** panelen adjon meg egy nevet az alkalmazás regisztrációja.
3. Ügyeljen arra, hogy a **támogatott fióktípusok** , amely **fiókok csak a szervezeti könyvtárban található** van kiválasztva. Ez a legbiztonságosabb lehetőség.
4. Hozzáadjuk átirányítási URI-t később, miután tudjuk, hogy a fürt URI azonosítója. Kattintson a **regisztrálása** gombra az Azure AD-alkalmazás regisztrációjának létrehozásához.
5. A megjelenő lapon másolja le a **Alkalmazásazonosítót (ügyfél)** . Ezt az értéket hivatkozni fogunk `APPID` a a [hozzon létre egy Azure Red Hat OpenShift fürtöt](tutorial-create-cluster.md) oktatóanyag.

![Alkalmazás objektum oldalát bemutató képernyőkép](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Ügyfél titkos kulcs létrehozása

Hozzon létre egy ügyfél titkos kulcsot az Azure Active Directory-alkalmazás hitelesítéséhez.

1. A a **kezelése** szakaszban kattintson az alkalmazás regisztrációs oldal **tanúsítványok és titkos kulcsok**.
2. Az a **tanúsítványok és titkos kulcsok** ablaktáblán kattintson a **+ új titkos ügyfélkulcsot**.  A **ügyfélkódot hozzáadása** ablaktáblán jelenik meg.
3. Adjon meg egy **leírás**.
4. Állítsa be **lejárat** igény szerint például időtartama **2 évben**.
5. Kattintson a **Hozzáadás** jelenik meg a kulcs értékét, és a **ügyfél titkos kódok** lap részében.
6. Másolja le a kulcs értékét. Ezt az értéket hivatkozni fogunk `SECRET` a a [hozzon létre egy Azure Red Hat OpenShift fürtöt](tutorial-create-cluster.md) oktatóanyag.

![A tanúsítványok és titkos kulcsok panel képernyőképe](./media/howto-create-tenant/create-key.png)

Az Azure-alkalmazás objektumának kapcsolatos további információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Részletekért hozzon létre egy új Azure AD-alkalmazást, lásd: [alkalmazás regisztrálása az Azure Active Directory 1.0-s verziójú végpontján az](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="add-api-permissions"></a>API-engedélyek hozzáadása

1. Az a **kezelés** szakaszban kattintson **API-engedélyek**.
2. Kattintson a **adjon hozzá engedélyt** válassza **Azure Active Directory Graph** majd **delegált engedélyek**
3. Bontsa ki a **felhasználói** az alábbi listában, és ellenőrizze, hogy a **User.Read** engedélyezve van.
4. Görgessen fel, és válassza ki **Alkalmazásengedélyek**.
5. Bontsa ki a **Directory** a lentebbi listában, majd engedélyezze a **Directory.ReadAll**
6. Kattintson a **engedélyek hozzáadása** a módosítások elfogadásához.
7. Az API-engedélyek panelen most meg kell jelennie mindkét *User.Read* és *Directory.ReadAll*. Vegye figyelembe a figyelmeztetést, **rendszergazdai jóváhagyás szükséges** oszlop melletti *Directory.ReadAll*.
8. Ha Ön a *Azure előfizetés-rendszergazda*, kattintson a **adja meg a rendszergazdai jóváhagyás *Előfizetésnevet***  alatt. Ha nem a *Azure előfizetés-rendszergazda*, a jóváhagyási kérjen a rendszergazdától.
![Az API-engedélyek panel képernyőképe. Hozzáadott User.Read és Directory.ReadAll engedélyek, Directory.ReadAll szükséges rendszergazdai jóváhagyás](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> A fürt a Rendszergazdák csoport szinkronizálási csak hozzájárulás megadása után fog működni. Látni fogja a zöld körön be van jelölve, egy üzenet "nyújtott *Előfizetésnevet*" az a *rendszergazdai jóváhagyás szükséges* oszlop.

A rendszergazdák és más szerepkörök kezelése a részletekért lásd: [hozzáadása vagy módosítása az Azure-előfizetés rendszergazdái](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).

## <a name="resources"></a>További források

* [Alkalmazások és az Azure Active Directory egyszerű szolgáltatási objektumok](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Rövid útmutató: Alkalmazás regisztrálása az az Azure Active Directory 1.0-s verziójú végpontján](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>További lépések

Ha az összes teljesítette az [Azure Red Hat OpenShift előfeltételei](howto-setup-environment.md), készen áll az első saját fürt létrehozása!

Próbálja ki az oktatóanyag:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)
