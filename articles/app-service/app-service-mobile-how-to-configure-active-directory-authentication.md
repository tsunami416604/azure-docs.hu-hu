---
title: "Az App Service szolgáltatások alkalmazás Azure Active Directory-hitelesítés konfigurálása"
description: "Tudnivalók az App Service szolgáltatások alkalmazás Azure Active Directory-hitelesítés konfigurálása."
author: mattchenderson
services: app-service
documentationcenter: 
manager: syntaxc4
editor: 
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 990fab9aeea71b8cf344b9a49a5ed438db6663c0
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-login"></a>App Service-alkalmazás Azure Active Directory bejelentkezési használandó konfigurálása
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure App Service szolgáltatások, Azure Active Directory egy hitelesítésszolgáltatót használandó.

## <a name="express"></a>Azure Active Directory konfigurálása gyorsbeállítások használatával
1. Az a [Azure-portálon], keresse meg az App Service alkalmazáshoz. Válassza ki a bal oldali navigációs **hitelesítési / engedélyezési**.
2. Ha **hitelesítési / engedélyezési** nem engedélyezve, jelölje be **a**.
3. Válassza ki **Azure Active Directory**, majd válassza ki **Express** alatt **üzemmód**.
4. Válassza ki **OK** az App Service alkalmazás regisztrálása az Azure Active Directoryban. Ezzel létrehoz egy új alkalmazás regisztrációja. Ha szeretne egy meglévő alkalmazás regisztrációja Válasszon helyette, kattintson a **válasszon ki egy meglévő alkalmazást** majd keresse meg a korábban létrehozott alkalmazás regisztrációs belül a bérlő nevét.
   Jelölje ki, majd kattintson az alkalmazás regisztráció kattintson **OK**. Kattintson a **OK** az Azure Active Directory beállításai oldal.
   Alapértelmezés szerint az App Service hitelesítést nyújt, de nem engedélyezett hozzáférés korlátozása a tartalom és API-k. Kell engedélyeznie a felhasználók az alkalmazás kódját.
5. (Választható) Csak az Azure Active Directory hitelesített felhasználók számára a helyhez való hozzáférésének korlátozásához, állítsa be **hitelesítetlen kérés esetén elvégzendő művelet** való **bejelentkezés Azure Active Directoryval**. Ehhez szükséges, hogy az összes kérelem hitelesítését, és minden nem hitelesített a kérelmet átirányítja az Azure Active Directory-hitelesítéshez.
6. Kattintson a **Save** (Mentés) gombra.

Most már készen áll az App Service-alkalmazás hitelesítéshez használt Azure Active Directoryban.

## <a name="advanced"></a>(Alternatív módszer) manuális konfigurálása az Azure Active Directory speciális beállításokkal
Másik lehetőségként konfigurációs lehetőségeket biztosít manuálisan. Ez az az előnyben részesített megoldás, ha az AAD-bérlőt használni kívánt eltér a tenant, amelyhez jelentkezzen be Azure. A konfigurálás befejezéséhez, először létre kell hoznia egy regisztrációs az Azure Active Directoryban, és meg kell adni az egyes a regisztrációs adatokat az App Service.

### <a name="register"></a>App Service-alkalmazás regisztrálása az Azure Active Directoryval
1. Jelentkezzen be a [Azure-portálon], és keresse meg az App Service alkalmazáshoz. Másolja az alkalmazás **URL-cím**. Az Azure Active Directory-alkalmazás regisztráció konfigurálásához használandó.
2. Navigáljon a **Active Directory**, majd jelölje be a **App regisztrációk**, kattintson a **új alkalmazás regisztrációja** elindítani az új alkalmazás regisztrációjának tetején. 
3. Az a **létrehozása** lapján adja meg a **neve** az alkalmazás regisztrációját, válassza ki a **Web App / API** írja be a a **bejelentkezési URL-cím** mezőbe illessze be a alkalmazás URL-CÍMÉT (1. lépés). Kattintson a **létrehozása**.
4. Néhány másodpercen belül meg kell jelennie az imént létrehozott új app regisztrációt.
5. Miután hozzáadta az alkalmazás regisztrálása, kattintson a regisztráció alkalmazás nevére, kattintson a **beállítások** tetején, majd kattintson a **tulajdonságai** 
6. A a **App ID URI** mezőbe illessze be az alkalmazás URL-cím (az 1. lépés), a is a **Kezdőlap** illessze be az alkalmazás URL-CÍMÉT (1. lépés), majd kattintson a **mentése**
7. Most kattintson a a **válasz URL-címek**, szerkesztheti a **válasz URL-CÍMEN**, illessze be az alkalmazás URL-CÍMÉT (1. lépés), módosítsa a protokollt, hogy ellenőrizze, hogy **https://** protokoll (nem http://), az URL-cím végén majd fűzött */.auth/login/aad/callback* (például `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Kattintson a **Save** (Mentés) gombra.   
8.  Ezen a ponton, másolja a **Alkalmazásazonosító** az alkalmazás. Legyen a későbbi használatra. Szüksége lesz az App Service-alkalmazás beállítása.
9. Zárja be a **regisztrált alkalmazás** lap. A a **App regisztrációk** lapján kattintson a a **végpontok** gombra az oldal tetején, majd másolja a **összevonási metaadat-dokumentum** URL-cím. 
10. Nyisson meg egy új böngészőablakot, és beillesztés, és keresse meg az XML-oldalra keresse meg az URL-címet. A dokumentum tetején van egy **EntityDescriptor** elem, kell lennie egy **entityid beállítást** attribútum az űrlap `https://sts.windows.net/` a bérlő (más néven "bérlő azonosítója") egy adott GUID követ. Másolja ezt az értéket – Ez a **kiállítójának URL-címe**. Az alkalmazás későbbi használatra konfigurál.

### <a name="secrets"></a>App Service-alkalmazás Azure Active Directory hozzáadása információk
1. Vissza a [Azure-portálon], keresse meg az App Service alkalmazáshoz. Kattintson a **hitelesítési/engedélyezési**. Ha a hitelesítési/engedélyezési szolgáltatás nincs engedélyezve, kapcsolja be a kapcsoló **a**. Kattintson a **Azure Active Directory**, a hitelesítési szolgáltatók, az alkalmazás konfigurálásához. (Választható) Alapértelmezés szerint az App Service hitelesítést nyújt, de nem engedélyezett hozzáférés korlátozása a tartalom és API-k. Kell engedélyeznie a felhasználók az alkalmazás kódját. Állítsa be **hitelesítetlen kérés esetén elvégzendő művelet** való **bejelentkezés Azure Active Directoryval**. Ez a beállítás megköveteli, hogy az összes kérelem hitelesítését, és minden nem hitelesített a kérelmet átirányítja az Azure Active Directory-hitelesítéshez.
2. a Active Directory-hitelesítés konfiguráció kattintson **speciális** alatt **üzemmód**. Illessze be az Alkalmazásazonosító ügyfél-azonosító (a 8. lépés), és illessze be a entityid beállítást (a 10. lépés) azokat a kiállító URL-címével. Ezután kattintson az **OK** gombra.
3. Az Active Directory-hitelesítés konfiguráció lapján kattintson a **mentése**.

Most már készen áll az App Service-alkalmazás hitelesítéshez használt Azure Active Directoryban.

## <a name="optional-configure-a-native-client-application"></a>(Választható) Natív ügyfélalkalmazás konfigurálása
Az Azure Active Directory segítségével is natív ügyfelek regisztrálása nagyobb mértékben vezérelheti a leképezési engedélyeket biztosító. Ez szükséges, ha a szalagtárat használó például bejelentkezések végrehajtandó a **Active Directory Authentication Library**.

1. Navigáljon a **Azure Active Directory** a a [Azure-portálon].
2. Válassza ki a bal oldali navigációs **App regisztrációk**. Kattintson a **új alkalmazás regisztrációjának** tetején.
4. Az a **létrehozása** lapján adja meg a **neve** az alkalmazás regisztrációjához. Válassza ki **natív** a **alkalmazástípus**.
5. Az a **átirányítási URI-** mezőbe írja be a webhely */.auth/login/done* végpont, a HTTPS protokollt használ. Ez az érték a következőképpen kell kinéznie *https://contoso.azurewebsites.net/.auth/login/done*. Ha inkább használja a Windows alkalmazás létrehozása a [SID csomag](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) az URI-azonosítóként.
5. Kattintson a **Create** (Létrehozás) gombra.
6. Miután hozzáadta az alkalmazás regisztrálása, válassza ki azt a megnyitásához. Keresés a **Alkalmazásazonosító** és jegyezze fel ezt az értéket.
7. Kattintson a **összes beállítás** > **szükséges engedélyek** > **Hozzáadás** > **API kiválasztása**.
8. Írja be a nevét, keresse meg azt, majd jelölje ki, majd kattintson a korábban regisztrált App Service alkalmazás **válasszon**. 
9. Válassza ki **hozzáférés \<alkalmazás_neve >**. Kattintson a **válasszon**. Ezután kattintson a **Done** (Kész) gombra.

Ezzel beállította egy webalkalmazást az App Service alkalmazáshoz hozzáférő.

## <a name="related-content"></a>Kapcsolódó tartalom
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure-portálon]: https://portal.azure.com/
[alternative method]:#advanced
