---
title: Azure Active Directory hitelesítés konfigurálása – Azure App Service
description: Megtudhatja, hogyan konfigurálhatja Azure Active Directory hitelesítést a App Service alkalmazáshoz.
author: cephalin
services: app-service
documentationcenter: ''
manager: gwallace
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service
ms.workload: web,mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 8b4b6549f9553773cc44c311f49befbb3eec9dc9
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233097"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>App Service alkalmazás konfigurálása Azure Active Directory bejelentkezés használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

> [!NOTE]
> Jelenleg a HRE v2 (beleértve az MSAL-t is) nem támogatott Azure App Service és Azure Functions esetén.
>

Ez a cikk bemutatja, hogyan konfigurálhatja a Azure App Servicet a Azure Active Directory hitelesítési szolgáltatóként való használatára.

Javasoljuk, hogy minden egyes App Service alkalmazást saját regisztrációval konfigurálja, így saját engedélyei és beleegyező engedélye van. Emellett érdemes lehet külön alkalmazás-regisztrációkat használni a különálló üzembe helyezési pontokhoz. Ezzel elkerüli az engedélyek megosztását a környezetek között, így a tesztelni kívánt új kód hibája nem befolyásolja a gyártást.

## <a name="express"> </a>Konfigurálás az expressz beállításokkal

1. A [Azure Portal]navigáljon a app Service alkalmazáshoz. A bal oldali navigációs sávon válassza a **hitelesítés/engedélyezés**lehetőséget.
2. Ha a **hitelesítés/engedélyezés** nincs engedélyezve, válassza **a be**lehetőséget.
3. Válassza a **Azure Active Directory**lehetőséget, majd válassza az **expressz** lehetőséget a **felügyeleti mód**alatt.
4. Kattintson az **OK** gombra, hogy regisztrálja a app Service alkalmazást a Azure Active Directoryban. Ez létrehoz egy új alkalmazás-regisztrációt. Ha ehelyett egy meglévő alkalmazás-regisztrációt szeretne választani, kattintson a **meglévő alkalmazás kiválasztása** lehetőségre, majd keressen rá egy korábban létrehozott alkalmazás-regisztráció nevére a bérlőn belül. A kiválasztásához kattintson az alkalmazás regisztrálása elemre, majd kattintson **az OK**gombra. Ezután kattintson az **OK gombra** a Azure Active Directory beállítások lapon.
Alapértelmezés szerint a App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyezni kell a felhasználókat az alkalmazás kódjában.
5. Választható Ha korlátozni szeretné az alkalmazáshoz való hozzáférést az Azure Active Directory által hitelesített felhasználók számára, akkor állítsa be **a műveletet, ha a kérelem nem hitelesítve van** a Azure Active Directoryval való **bejelentkezéshez**. Ehhez minden kérést hitelesíteni kell, és az összes nem hitelesített kérelem át lesz irányítva a hitelesítéshez Azure Active Directory.

    > [!NOTE]
    > A hozzáférés ezen a módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos, ha az alkalmazások nyilvánosan elérhető kezdőlapot szeretnének, például sok egyoldalas alkalmazásban. Ilyen alkalmazások esetén **engedélyezze a névtelen kérelmeket (nincs művelet)** előnyben részesített, ha az alkalmazás manuálisan indítja el a bejelentkezést, az [itt](overview-authentication-authorization.md#authentication-flow)leírtak szerint.
6. Kattintson a **Save** (Mentés) gombra.

## <a name="advanced"> </a>Konfigurálás speciális beállításokkal

A konfigurációs beállításokat manuálisan is megadhatja, ha a használni kívánt Azure Active Directory bérlő eltér az Azure-ba bejelentkezett bérlőtől. A konfiguráció befejezéséhez először létre kell hoznia egy regisztrációt Azure Active Directoryban, majd meg kell adnia néhány regisztrációs adatot a App Servicehoz.

### <a name="register"> </a>Alkalmazás-regisztráció létrehozása az Azure ad-ben a app Service-alkalmazáshoz

Az alkalmazások regisztrálásának manuális létrehozásakor jegyezze fel a három olyan információt, amelyre később szüksége lesz a App Service alkalmazás konfigurálásához: az ügyfél-azonosítót, a bérlői azonosítót és opcionálisan az ügyfél titkát és az alkalmazás-azonosító URI-t.

1. A [Azure Portal]navigáljon a app Service alkalmazáshoz, és jegyezze fel az alkalmazás **URL-címét**. Ezt fogja használni a Azure Active Directory alkalmazás regisztrálásának konfigurálásához.
1. A [Azure Portal]bal oldali menüjében válassza a **Active Directory** > **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget. 
1. Az **alkalmazás regisztrálása** lapon adja meg az alkalmazás regisztrációjának **nevét** .
1. Az **átirányítási URI**-ban válassza a **web** lehetőséget, és írja be a app Service alkalmazás URL `/.auth/login/aad/callback`-címét, és adja hozzá az elérési utat. Például: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Ezután kattintson a **Létrehozás** elemre.
1. Miután létrehozta az alkalmazás regisztrációját, másolja az **alkalmazás-(ügyfél-) azonosítót** és a **címtár-(bérlői) azonosítót** későbbi verzióra.
1. Válasszaa branding elemet. A **Kezdőlap URL-címe**mezőbe írja be a app Service alkalmazás URL-címét, majd válassza a **Mentés**lehetőséget.
1. Válasszon ki **egy API** > -**készletet**. Illessze be a App Service alkalmazás URL-címét, majd válassza a **Mentés**lehetőséget.

    > [!NOTE]
    > Ez az érték az alkalmazás regisztrációs **azonosítójának URI** azonosítója. Ha szeretné, hogy egy előtér-webalkalmazás hozzáférjen egy háttér-API-hoz, például ha szeretné, hogy a háttérrendszer explicit módon engedélyezze a hozzáférést az előtérhöz, akkor a < c2 App Service alkalmazás-erőforrásának konfigurálásakor az előtér- **alkalmazás azonosítójának URI-ja** szükséges. >.
1. Válassza a **Hatókör hozzáadása** lehetőséget. A **hatókör neve**mezőbe írja be a következőt: *user_impersonation*. A szövegmezőbe írja be a belefoglalni kívánt hatókör nevét és leírását, amelyet a felhasználók a beleegyező lapon láthatnak, például a *hozzáférés az alkalmazáshoz*. Ha elkészült, kattintson a **hatókör hozzáadása**lehetőségre.
1. Választható Az ügyfél titkos kulcsának létrehozásához válassza a **tanúsítványok & Secrets** > **új ügyfél titkos** > **hozzáadása**elemet. Másolja a lapon látható ügyfél titkos kulcs értékét. Ha elmozdul, nem jelenik meg újra.
1. Választható Több **Válasz URL-cím**hozzáadásához válassza a menü **hitelesítés** elemét.

### <a name="secrets"> </a>Azure Active Directory információk hozzáadása a app Service alkalmazáshoz

1. A [Azure Portal]navigáljon a app Service alkalmazáshoz. A bal oldali menüben válassza a **hitelesítés/engedélyezés**lehetőséget. Ha a hitelesítés/engedélyezés funkció nincs engedélyezve, válassza **a be**lehetőséget. 
1. Választható Alapértelmezés szerint a App Service hitelesítés lehetővé teszi a nem hitelesített hozzáférést az alkalmazáshoz. A felhasználói hitelesítés érvénybe léptetéséhez állítsa be a **műveletet, ha a kérelem nem hitelesítve van** a Azure Active Directoryba való **bejelentkezéshez**.
1. A hitelesítésszolgáltatók területen válassza a **Azure Active Directory**lehetőséget.
1. **Felügyeleti módban**válassza a **speciális** lehetőséget, és konfigurálja app Service hitelesítést az alábbi táblázatnak megfelelően:

    |Mező|Leírás|
    |-|-|
    |Ügyfél-azonosító| Használja az **alkalmazás regisztrációjának alkalmazás-(ügyfél-) azonosítóját** . |
    |Kiállító azonosítója| Használja `https://login.microsoftonline.com/<tenant-id>`és cserélje le  *\<a bérlő-azonosító >* az alkalmazás regisztrációjának **címtár-(bérlői) azonosítójával** . |
    |Ügyfél titka (nem kötelező)| Használja az alkalmazás regisztrációjában létrehozott ügyfél-titkos kulcsot.|
    |Jogkivonatok engedélyezett célrendszerei| Ha ez egy *háttérbeli* alkalmazás, és engedélyezni szeretné a hitelesítési jogkivonatokat egy előtér-alkalmazásból, adja hozzá az *ELŐTÉR* - **alkalmazás azonosítójának URI-ját** itt. |
1. Válassza **az OK**, majd a **Mentés**lehetőséget.

Most már készen áll a Azure Active Directory használatára a App Service alkalmazásban való hitelesítéshez.

## <a name="configure-a-native-client-application"></a>Natív ügyfélalkalmazás konfigurálása
Ha olyan ügyféloldali kódtár használatával kívánja végrehajtani a bejelentkezést, mint például a **Active Directory-hitelesítési tár**, akkor regisztrálhatja a natív ügyfeleket.

1. A [Azure Portal]bal oldali menüjében válassza a **Active Directory** > **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget. 
1. Az **alkalmazás regisztrálása** lapon adja meg az alkalmazás regisztrációjának **nevét** .
1. Az **átirányítási URI**-ban válassza a **nyilvános ügyfél (mobil & asztal)** lehetőséget, és írja be a app Service alkalmazás URL `/.auth/login/aad/callback`-címét, és adja hozzá az elérési utat. Például: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Ezután kattintson a **Létrehozás** elemre.

    > [!NOTE]
    > Windows-alkalmazások esetén a [csomag biztonsági azonosítóját](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) használja inkább URI-ként.
1. Az alkalmazás regisztrációjának létrehozása után másolja az **Application (ügyfél) azonosító**értékét.
1. A bal oldali menüben válassza az **API-engedélyek** > **Hozzáadás engedély** > **saját API**-k lehetőséget.
1. Válassza ki a korábban létrehozott App Service alkalmazás regisztrációját. Ha nem látja az alkalmazás regisztrációját, ellenőrizze, hogy az alkalmazás regisztrálása az [Azure ad](#register)-ban című cikk **user_impersonation** -hatókörét adta-e hozzá app Service alkalmazásához.
1. Válassza a **user_impersonation** lehetőséget, majd kattintson az **engedélyek hozzáadása**lehetőségre.

Ezzel konfigurált egy natív ügyfélalkalmazás, amely hozzáfér a App Service-alkalmazáshoz.

## <a name="related-content"> </a>Related Content

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
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[alternative method]:#advanced
