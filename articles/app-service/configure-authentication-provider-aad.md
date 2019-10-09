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
ms.custom: fasttrack-edit
ms.openlocfilehash: ac73b549546c353dce4c40005b7742577e03d26c
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176983"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>App Service alkalmazás konfigurálása az Azure AD-bejelentkezés használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja a Azure App Servicet a Azure Active Directory (Azure AD) hitelesítési szolgáltatóként való használatára.

> [!NOTE]
> Jelenleg a Azure App Service és Azure Functions csak az Azure AD 1.0-s verziójában támogatott. Ezek a Microsoft [Identity platform 2.0-s verziójában](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-overview)nem támogatottak, beleértve a Microsoft hitelesítési kódtárait (MSAL).

Az alkalmazás és a hitelesítés beállításakor kövesse az alábbi ajánlott eljárásokat:

- Adja meg az egyes App Service alkalmazások saját engedélyeit és hozzájárulásukat.
- Konfigurálja az egyes App Service alkalmazásait a saját regisztrálásával.
- Kerülje a környezetek közötti engedélyek megosztását külön alkalmazás-regisztrációk használatával külön üzembe helyezési pontokhoz. Az új kód tesztelésekor ez a gyakorlat segít megakadályozni az éles alkalmazást érintő problémákat.

## <a name="express"> </a>Konfigurálás az expressz beállításokkal

1. A [Azure Portal]nyissa meg a app Service alkalmazást.
1. Válassza a **beállítások** > **hitelesítés/engedélyezés** lehetőséget a bal oldali ablaktáblán, és győződjön meg arról, hogy a **app Service hitelesítés** **be van kapcsolva**.
1. Válassza a **Azure Active Directory**lehetőséget, majd válassza az **expressz** lehetőséget a **felügyeleti mód**alatt.
1. Kattintson az **OK** gombra, hogy regisztrálja a app Service alkalmazást a Azure Active Directoryban. Létrejön egy új alkalmazás regisztrálása.

   Ha inkább egy meglévő alkalmazás-regisztrációt szeretne választani:

   1. Válassza a **meglévő alkalmazás kiválasztása** lehetőséget, majd keressen rá egy korábban létrehozott alkalmazás-regisztráció nevére a bérlőn belül.
   1. Válassza ki az alkalmazás regisztrációját, majd kattintson **az OK gombra**.
   1. Ezután kattintson az **OK gombra** a Azure Active Directory beállítások lapon.

   Alapértelmezés szerint a App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyezni kell a felhasználókat az alkalmazás kódjában.
1. Választható Ha csak az Azure Active Directory által hitelesített felhasználók számára kívánja korlátozni az alkalmazás-hozzáférést, állítsa be **a végrehajtandó műveletet, ha a kérés nincs hitelesítve** a **Azure Active Directoryval való bejelentkezéshez**. Ha beállítja ezt a funkciót, az alkalmazásnak minden kérelmet hitelesítenie kell. Emellett átirányítja az összes nem hitelesített Azure Active Directory a hitelesítéshez.

    > [!CAUTION]
    > A hozzáférés ily módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos olyan alkalmazások esetében, amelyek nyilvánosan elérhető kezdőlaptal rendelkeznek, mint sok egyoldalas alkalmazásban. Ilyen alkalmazások esetén **engedélyezze a névtelen kérelmeket (nincs művelet)** előnyben részesített, ha az alkalmazás manuálisan indítja el a bejelentkezést. További információ: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow).
1. Kattintson a **Mentés** gombra.

## <a name="advanced"> </a>Konfigurálás speciális beállításokkal

Az Alkalmazásbeállítások manuálisan is konfigurálhatók, ha olyan Azure AD-bérlőt szeretne használni, amely különbözik az Azure-ba való bejelentkezéshez használt egyiktől. Az egyéni konfiguráció végrehajtásához a következőket kell tennie:

1. Hozzon létre egy regisztrációt az Azure AD-ben.
1. Adja meg a App Service regisztrációs adatait.

### <a name="register"> </a>Alkalmazás-regisztráció létrehozása az Azure ad-ben a app Service-alkalmazáshoz

A App Service alkalmazás konfigurálásakor a következő információkra lesz szüksége:

- Ügyfél-azonosító
- Bérlőazonosító
- Ügyfél titka (nem kötelező)
- Alkalmazás-azonosító URI-ja

Hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portal] , és lépjen a app Service alkalmazáshoz. Jegyezze fel az alkalmazás **URL-címét**. Ezzel konfigurálja a Azure Active Directory alkalmazás regisztrációját.
1. Válassza a **Azure Active Directory** > **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget.
1. Az **alkalmazás regisztrálása** lapon adja meg az alkalmazás regisztrációjának **nevét** .
1. Az **átirányítási URI**-ban válassza a **web** lehetőséget, és adja meg az App Service alkalmazás URL-címét, és fűzze hozzá a `/.auth/login/aad/callback` elérési utat. Például: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Kattintson a **Létrehozás** gombra.
1. Az alkalmazás regisztrációjának létrehozása után másolja az **alkalmazás (ügyfél) azonosítóját** és a **címtár (bérlő) azonosítóját** később.
1. Válassza a **branding**elemet. A **Kezdőlap URL-címe**mezőben adja meg app Service alkalmazásának URL-címét, majd válassza a **Mentés**lehetőséget.
1. Válasszon ki **egy API**- > **készletet**. Illessze be a App Service alkalmazás URL-címét, majd válassza a **Mentés**lehetőséget.

   > [!NOTE]
   > Ez az érték az alkalmazás regisztrációs **azonosítójának URI** azonosítója. Ha a webalkalmazásnak hozzáférést kell biztosítania egy Felhőbeli API-hoz, akkor a felhő App Service erőforrásának konfigurálásakor a webalkalmazás **alkalmazás-azonosító URI-ja** szükséges. Ezt például akkor használhatja, ha azt szeretné, hogy a Cloud Service explicit módon engedélyezze a hozzáférést a webalkalmazáshoz.

1. Válassza a **Hatókör hozzáadása** lehetőséget.
   1. A **hatókör neve**mezőbe írja be a *user_impersonation*nevet.
   1. A szövegmezőben adja meg a belefoglalni kívánt hatókör nevét és leírását, amelyet a felhasználók a beleegyező lapon láthatnak. Írja be például a következőt: *hozzáférés az alkalmazáshoz*. 
   1. Válassza a **hatókör hozzáadása**elemet.
1. Választható Az ügyfél titkos kulcsának létrehozásához válassza a **tanúsítványok & secrets** > **új ügyfél titkos**@no__t – 3**Hozzáadás**elemet. Másolja a lapon látható ügyfél titkos kulcs értékét. Nem jelenik meg újra.
1. Választható Több **Válasz URL-cím**hozzáadásához válassza a **hitelesítés**lehetőséget.

### <a name="secrets"> </a>Azure Active Directory információk hozzáadása a app Service alkalmazáshoz

1. A [Azure Portal]nyissa meg a app Service alkalmazást. 
1. Válassza a **beállítások > hitelesítés/engedélyezés** lehetőséget a bal oldali ablaktáblán, és győződjön meg arról, hogy a **app Service hitelesítés** **be van kapcsolva**.
1. Választható Alapértelmezés szerint a App Service hitelesítés lehetővé teszi a nem hitelesített hozzáférést az alkalmazáshoz. A felhasználói hitelesítés érvénybe léptetéséhez állítsa be a **műveletet, ha a kérelem nem hitelesítve van** a **Azure Active Directoryba való bejelentkezéshez**.
1. A hitelesítésszolgáltatók területen válassza a **Azure Active Directory**lehetőséget.
1. **Felügyeleti módban**válassza a **speciális** lehetőséget, és konfigurálja app Service hitelesítést az alábbi táblázatnak megfelelően:

    |Mező|Leírás|
    |-|-|
    |Ügyfél-azonosító| Használja az **alkalmazás regisztrációjának alkalmazás-(ügyfél-) azonosítóját** . |
    |Kiállító azonosítója| Használja a `https://login.microsoftonline.com/<tenant-id>` értéket, és cserélje le a *\<tenant-id >* az alkalmazás regisztrációjának **könyvtár (bérlő) azonosítójával** . |
    |Ügyfél titka (nem kötelező)| Használja az alkalmazás regisztrációjában létrehozott ügyfél-titkos kulcsot.|
    |Engedélyezett jogkivonat-célközönségek| Ha ez egy Felhőbeli vagy kiszolgálóalkalmazás-alkalmazás, és engedélyezni szeretné a hitelesítési jogkivonatokat egy webalkalmazásból, adja hozzá a webalkalmazás **alkalmazás-azonosító URI-ját** itt. |

    > [!NOTE]
    > A konfigurált **ügyfél** -azonosító *mindig* implicit módon engedélyezett célközönségnek minősül, függetlenül attól, hogy az **engedélyezett jogkivonat-célközönségek**hogyan lettek konfigurálva.
1. Válassza **az OK**, majd a **Mentés**lehetőséget.

Most már készen áll a Azure Active Directory használatára a App Service alkalmazásban való hitelesítéshez.

## <a name="configure-a-native-client-application"></a>Natív ügyfélalkalmazás konfigurálása

A natív ügyfelek regisztrálása lehetővé teszi a hitelesítés használatát egy ügyféloldali kódtár, például a **Active Directory-hitelesítési tár**használatával.

1. A [Azure Portal]válassza a **Active Directory** > **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget.
1. Az **alkalmazás regisztrálása** lapon adja meg az alkalmazás regisztrációjának **nevét** .
1. Az **átirányítási URI**-ban válassza a **nyilvános ügyfél (mobil & asztal)** lehetőséget, és adja meg a app Service alkalmazás URL-címét, és fűzze hozzá a `/.auth/login/aad/callback` elérési utat. Például: `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Kattintson a **Létrehozás** gombra.

    > [!NOTE]
    > Windows-alkalmazások esetén a [csomag biztonsági azonosítóját](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) használja inkább URI-ként.
1. Az alkalmazás regisztrációjának létrehozása után másolja az **Application (ügyfél) azonosító**értékét.
1. Válassza az **API-engedélyek** > **engedély hozzáadása** > **saját API**-k elemet.
1. Válassza ki a korábban létrehozott App Service alkalmazás regisztrációját. Ha nem látja az alkalmazás regisztrációját, győződjön meg arról, hogy az alkalmazás [regisztrálása az Azure ad](#register)-ben című témakör **user_impersonation** -hatókörét adta hozzá app Service alkalmazásához.
1. Válassza a **user_impersonation**lehetőséget, majd kattintson az **engedélyek hozzáadása**lehetőségre.

Ezzel konfigurált egy natív ügyfélalkalmazás, amely hozzáfér a App Service-alkalmazáshoz.

## <a name="related-content"></a>Következő lépések

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
