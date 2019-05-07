---
title: Egy Azure AD-alkalmazás regisztrációjának és felhasználó létrehozása az Azure Red Hat OpenShift |} A Microsoft Docs
description: Ismerje meg, hogyan hozzon létre egy egyszerű szolgáltatást, hozzon létre egy ügyfél titkos kulcs és a hitelesítési visszahívási URL-címe és egy új Active Directory-felhasználó létrehozása a Microsoft Azure Red Hat OpenShift fürtön lévő alkalmazások teszteléséhez.
author: tylermsft
ms.author: twhitney
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/06/2019
ms.openlocfilehash: de3f3c30848d26ea399bcccc29a6149a149f6a55
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078520"
---
# <a name="create-an-azure-ad-app-registration-and-user-for-azure-red-hat-openshift"></a>Egy Azure AD-alkalmazás regisztrációjának és felhasználó létrehozása az Azure Red Hat OpenShift

A Microsoft Azure Red Hat OpenShift engedélyre van szüksége a fürt nevében feladatok végrehajtásához. Ha a szervezete még nem rendelkezik egy Azure Active Directory (Azure AD) alkalmazásregisztráció az egyszerű szolgáltatás használatára, az alábbi lépésekkel hozzon létre egyet.

Ez a témakör arra a következtetésre jut, hozzon létre egy új utasításokat kell elérni az Azure Red Hat OpenShift fürtön futó alkalmazások Azure AD-felhasználót.

Ha még nem hozott létre az Azure AD-bérlő, kövesse az utasításokat [Azure Red Hat OpenShift az Azure AD-bérlő létrehozása](howto-create-tenant.md) ezeket az utasításokat a folytatás előtt.

## <a name="create-a-new-app-registration"></a>Hozzon létre egy új alkalmazás regisztrálása

Egy alkalmazás, amely az Azure ad-ben funkcióinak használatát szeretne először regisztrálni kell az Azure AD-bérlővel. A regisztrálási folyamat magában foglalja az Azure AD részletezi az alkalmazásadatokat, például az URL-címet, ahol az alkalmazás megtalálható, küldje a választ a felhasználó hitelesítése után az URL-cím, az URI-t, amely azonosítja az alkalmazást, és így tovább.

1. Az a [az Azure portal](https://portal.azure.com), győződjön meg arról, hogy a bérlő a felhasználónév felső területen jelenik meg, a portál jobb:

    ![Képernyőkép a portal a bérlő szerepel a jobb felső sarokban][tenantcallout] Ha rossz bérlőhöz jelenik meg, kattintson a jobb felső sarokban a felhasználónevére, majd kattintson a **címtár váltása**, és válassza ki a megfelelő bérlő a a **összes Könyvtárak** listája.

2. Nyissa meg a [regisztrációk panelére](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) kattintson **új alkalmazásregisztráció**.
3. Az a **létrehozás** panelen adjon meg egy rövid nevet az alkalmazásobjektum.
4. Ügyeljen arra, hogy **alkalmazástípus** értékre van állítva *Web app és az API*.
5. Hozzon létre egy **bejelentkezési URL-** használatával a következő mintának:

    ```
    https://<cluster-name>.<azure-region>.cloudapp.azure.com
    ```

    . . . ahol `<cluster-name>` tervezett az Azure Red Hat OpenShift fürt neve és `<azure-region>` van a [az Azure Red Hat OpenShift fürtöt az Azure-régióban](supported-resources.md#azure-regions). Például, ha a fürt nevét, hogy lehet `contoso`, és létrehozni a a `eastus` régió, a teljesen minősített tartománynevét (FQDN), amely kell adnia a **bejelentkezési URL-** lenne `https://contoso.eastus.cloudapp.azure.com`

    > [!IMPORTANT]
    > A fürt neve csak kisbetűket, és a teljes tartománynév URL-cím egyedinek kell lennie.
    > Győződjön meg arról, a fürt megfelelően eltérő nevet választani.

    Jegyezze fel a fürt nevét és a bejelentkezés URL-cím – kell őket később a fürtön futó alkalmazások elérése. Fogunk hivatkozni a fürt nevét, `CLUSTER_NAME`, és a bejelentkezési URL-cím `FQDN` a a [hozzon létre egy Azure Red Hat OpenShift fürtöt](tutorial-create-cluster.md) oktatóanyag.

6. Győződjön meg, hogy a **bejelentkezési URL-** értékét ellenőrzi az egy zöld pipa. (Nyomja le a Tab billentyű fókusz közül a *bejelentkezési URL-* mezőben, majd futtassa az ellenőrzésen.)
7. Kattintson a **létrehozás** gombra az Azure AD alkalmazás-objektum létrehozásához.
8. Az a **regisztrált alkalmazás** oldal jelenik meg, másolja le a **Alkalmazásazonosító**. Ezt az értéket hivatkozni fogunk `APPID` a a [hozzon létre egy Azure Red Hat OpenShift fürtöt](tutorial-create-cluster.md) oktatóanyag.

    ![Képernyőkép az alkalmazás azonosítója szövegmező][appidimage]
    
További információ az Azure-alkalmazás objektumának: [alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Részletekért hozzon létre egy új Azure AD-alkalmazást, lásd: [alkalmazás regisztrálása az Azure Active Directory 1.0-s verziójú végpontján az](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

### <a name="create-a-client-secret"></a>Ügyfél titkos kulcs létrehozása

Most már készen áll egy ügyfélkulcs hitelesítéséhez az Azure Active Directory-alkalmazás létrehozásához.

1. Az a **regisztrált alkalmazás** lapon **beállítások** , nyissa meg a regisztrált alkalmazás beállításait.
2. Az a **beállítások** ablaktáblán megjelenő **kulcsok**.  A **kulcsok** ablaktáblán jelenik meg.
![Képernyőkép a létrehozása a portál fő lapján][createkeyimage]
3. Adjon meg egy kulcsot **leírás**.
4. Beállítható egy érték **lejárat**, például *2 évben*.
5. Kattintson a **mentése** és jelenik meg a kulcs értékét.
6. Másolja le a kulcs értékét. Ezt az értéket hivatkozni fogunk `SECRET` a a [hozzon létre egy Azure Red Hat OpenShift fürtöt](tutorial-create-cluster.md) oktatóanyag.

### <a name="create-a-reply-url"></a>A válasz URL-cím létrehozása

Azure AD elküldi az alkalmazás objektum *válasz URL-cím* megadásához használja az alkalmazás engedélyezésekor a visszahívás. Webes API-t vagy webes alkalmazás esetén a válasz URL-címe a hely, ahol az Azure AD elküldi a hitelesítési választ, beleértve egy jogkivonatot, ha a hitelesítés sikeres volt.

Még a legkisebb eltérés (záró perjellel hiányzik, eltérő kis-és nagybetűhasználatot) miatt sikertelen a jogkivonat-kiállítási műveletet, és nem lehet bejelentkezni.

1. Lépjen vissza a **beállítások** ablaktáblán (kattintson **beállítások** diagramnézethez a portál tetején található), és kattintson a **válasz URL-címek** a jobb oldali.  A **válasz URL-címek** ablaktáblán jelenik meg.
2. A lista első válasz URL-cím lesz a `FQDN` 6. lépés értékét [hozzon létre egy új alkalmazásregisztráció](#create-a-new-app-registration). Szerkesztheti, és a hozzáfűző `/oauth2callback/Azure%20AD`.  Ha például a válasz URL-cím már hasonlóan kell kinéznie `https://mycluster.eastus.cloudapp.azure.com/oauth2callback/Azure%20AD`
3. Kattintson a **mentése** menteni a válasz URL-cím.

## <a name="create-a-new-active-directory-user"></a>Hozzon létre egy új Active Directory-felhasználót

Új felhasználó létrehozása az Active Directory használatával jelentkezzen be az alkalmazás futtatásához az Azure Red Hat OpenShift-fürtön.

1. Nyissa meg a [felhasználók – minden felhasználó](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) panelen.
2. Kattintson a **+ új felhasználó**. A **felhasználói** ablaktáblán jelenik meg.
3. Adjon meg egy **neve** , amelyet a felhasználó számára.
4. Hozzon létre egy **felhasználónév** a létrehozott bérlő neve alapján `.onmicrosoft.com` hozzáfűzi a végén. Például: `yourUserName@yourTenantName.onmicrosoft.com`. Jegyezze fel ezt a felhasználói nevet. Szüksége lesz rá a bejelentkezéshez használni az alkalmazást a fürtön.
5. Kattintson a **címtárbeli szerepkör** , és válassza ki **globális rendszergazdai** majd **Ok** a panel alján.
6. Közepén a **felhasználói** ablaktáblán kattintson a **jelszó megjelenítése** , és jegyezze fel az ideiglenes jelszót. Miután először bejelentkezik, a program felszólítja állítsa alaphelyzetbe.
7. A panel alján kattintson **létrehozás** a felhasználó létrehozásához.

## <a name="resources"></a>További források

* [Alkalmazások és az Azure Active Directory egyszerű szolgáltatási objektumok](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [Rövid útmutató: Alkalmazás regisztrálása az az Azure Active Directory 1.0-s verziójú végpontján](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

[appidimage]: ./media/howto-create-tenant/get-app-id.png
[createkeyimage]: ./media/howto-create-tenant/create-key.png
[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="next-steps"></a>További lépések

Ha az összes teljesítette az [Azure Red Hat OpenShift előfeltételei](howto-setup-environment.md), készen áll az első saját fürt létrehozása!

Próbálja ki az oktatóanyag:
> [!div class="nextstepaction"]
> [Az Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)
