---
title: Azure Active Directory-hitelesítés – az Azure App Service konfigurálása
description: Ismerje meg, hogyan konfigurálja az App Services-alkalmazás Azure Active Directory-hitelesítést.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: d0c39462bc046b13a2756d37c089ba0e68c90452
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456639"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Az App Service-alkalmazások a Azure Active Directory-bejelentkezés konfigurálása

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure App Services, Azure Active Directory-hitelesítési szolgáltató használandó.

## <a name="express"> </a>Konfigurálása kifejezett beállításokkal

1. Az a [Azure Portal], keresse meg az App Service-alkalmazást. A bal oldali navigációs sávján válassza **hitelesítési / engedélyezési**.
2. Ha **hitelesítési / engedélyezési** nincs engedélyezve van, jelölje be **a**.
3. Válassza ki **Azure Active Directory**, majd válassza ki **Express** alatt **felügyeleti mód**.
4. Válassza ki **OK** az App Service-alkalmazás regisztrálása az Azure Active Directoryban. Ez létrehoz egy új alkalmazás regisztrálása. Ha ehelyett válassza egy meglévő alkalmazásregisztráció szeretne, kattintson a **válasszon ki egy meglévő alkalmazást** majd keresse meg a korábban létrehozott alkalmazás regisztrációját a bérlőn belül nevét.
   Kattintson az alkalmazás regisztrációját a kiválasztásához, és kattintson a **OK**. Kattintson a **OK** a az Azure Active Directory-beállítások lapon.
   Alapértelmezés szerint az App Service-ben hitelesítést biztosít, de a webhely tartalmát és API-k nem korlátozza a hitelesített hozzáférést. Felhasználók engedélyeznie kell az alkalmazás kódjában.
5. (Nem kötelező) A hozzáférés korlátozása csak az Azure Active Directory által hitelesített felhasználók a helyet, állítsa be **elvégzendő művelet, ha a kérés nincs hitelesítve** való **bejelentkezés az Azure Active Directory**. Ehhez az szükséges, hogy minden kérelmet hitelesíteni, és az összes nem hitelesített kérelmek a rendszer átirányítja az Azure Active Directory-hitelesítéshez.
6. Kattintson a **Save** (Mentés) gombra.

Most már készen áll az App Service-alkalmazást a hitelesítéshez használt Azure Active Directoryban.

## <a name="advanced"> </a>Speciális beállítások konfigurálása

Konfigurációs beállítások is megadhatja manuálisan. Ez a az előnyben részesített megoldás, ha a használni kívánt Azure Active Directory-bérlő eltér a bérlőhöz, amellyel bejelentkezik az Azure. A konfiguráció befejezéséhez, először létre kell hoznia egy regisztrációs az Azure Active Directoryban, és meg kell adni a regisztrációs adatokat az egyes App Service-ben.

### <a name="register"> </a>Az App Service-alkalmazás regisztrálása az Azure Active Directory

1. Jelentkezzen be a [Azure Portal], és keresse meg az App Service-alkalmazást. Másolja az alkalmazás **URL-cím**. Fogja használni az Azure Active Directory alkalmazásregisztráció konfigurálásához.
2. Keresse meg **Active Directory**, majd válassza a **alkalmazásregisztrációk**, majd kattintson a **új alkalmazásregisztráció** elindítani egy új alkalmazásregisztráció tetején. 
3. Az a **létrehozás** lap, adja meg egy **neve** válassza ki az alkalmazás regisztrációját a **webalkalmazás / API** írja be a a **bejelentkezési URL-** mezőbe illessze be a alkalmazás URL-CÍMÉT (1. lépés). Kattintson a **létrehozás**.
4. Néhány másodpercen belül megtekintheti az imént létrehozott új alkalmazásregisztráció.
5. Miután hozzáadta az alkalmazás regisztrációját, az alkalmazás regisztrációs nevére, kattintson a **beállítások** tetején, majd kattintson a **tulajdonságai** 
6. Az a **Alkalmazásazonosító URI-t** mezőbe illessze be az alkalmazás URL-cím (az 1. lépésből), a a **kezdőlap URL-címe** illessze be az alkalmazás URL-cím (az 1. lépésből), majd kattintson a **mentése**
7. Ezután kattintson a a **válasz URL-címek**, szerkesztheti a **válasz URL-cím**, illessze be az alkalmazás URL-cím (az 1. lépésből), majd az URL-cím végéhez fűzött */.auth/login/aad/callback* (a a példában `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Kattintson a **Save** (Mentés) gombra.   
8.  Ezen a ponton másolja a **Alkalmazásazonosító** az alkalmazáshoz. Legyen a későbbi használatra. Szüksége lesz rá az App Service-alkalmazás konfigurálása.
9. Zárja be a **regisztrált alkalmazás** lapot. A a **alkalmazásregisztrációk** lapon a **végpontok** gomb tetején, majd másolja a **WS-FEDERATION SIGN-ON ENDPOINT** URL-címet, de távolítsa el a `/wsfed` befejezési az URL-címből. A végeredmény hasonlóan kell kinéznie `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000`. A tartománynév szuverén felhő eltérő lehet. Ez erre a célra a kiállító URL-címe későbbi használatra.

### <a name="secrets"> </a>Az App Service-alkalmazást az Azure Active Directory-információk hozzáadása

1. Térjen vissza a [Azure Portal], keresse meg az App Service-alkalmazást. Kattintson a **hitelesítés/engedélyezés**. Ha a hitelesítés/engedélyezés funkció nem érhető el, kapcsolja be a kapcsolót **a**. Kattintson a **Azure Active Directory**, a hitelesítési szolgáltatók, az alkalmazás konfigurálása.

    (Nem kötelező) Alapértelmezés szerint az App Service-ben hitelesítést biztosít, de a webhely tartalmát és API-k nem korlátozza a hitelesített hozzáférést. Felhasználók engedélyeznie kell az alkalmazás kódjában. Állítsa be **elvégzendő művelet, ha a kérés nincs hitelesítve** való **bejelentkezés az Azure Active Directory**. Ez a beállítás megköveteli, hogy minden kérelmet hitelesíteni, és az összes nem hitelesített kérelmek a rendszer átirányítja az Azure Active Directory-hitelesítéshez.
2. Kattintson az Active Directory-hitelesítést a konfigurációban **speciális** alatt **felügyeleti mód**. Illessze be az Alkalmazásazonosítót ügyfél-azonosító (a 8. lépés), és illessze be az URL-cím (a 9. lépés) a kibocsátó URL-cím értékét. Ezután kattintson az **OK** gombra.
3. Az Active Directory-hitelesítés konfiguráció lapján kattintson a **mentése**.

Most már készen áll az App Service-alkalmazást a hitelesítéshez használt Azure Active Directoryban.

## <a name="configure-a-native-client-application"></a>Natív ügyfélalkalmazás konfigurálása
Regisztrálhat natív ügyfelek számára, amellyel az engedélyek hozzárendelése nagyobb mértékben vezérelheti. Ez szükséges, ha szeretné végrehajtani, például egy ügyféloldali kódtár használatával bejelentkezések a **Active Directory Authentication Library**.

1. Navigáljon a **Azure Active Directory** a a [Azure Portal].
2. A bal oldali navigációs sávján válassza **alkalmazásregisztrációk**. Kattintson a **új alkalmazásregisztráció** tetején.
4. Az a **létrehozás** lap, adja meg egy **neve** az alkalmazás regisztrációját. Válassza ki **natív** a **alkalmazástípus**.
5. Az a **átirányítási URI-t** mezőbe írja be a webhely */.auth/login/done* végpontról, a HTTPS-sémát. Ez az érték legyen hasonló *https://contoso.azurewebsites.net/.auth/login/done*. Ha inkább használja egy Windows-alkalmazás létrehozása a [csomag biztonsági azonosítója](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) , az URI-t.
5. Kattintson a **Create** (Létrehozás) gombra.
6. Miután hozzáadta az alkalmazás regisztrációját, ezt választva nyithatja meg azt. Keresse meg a **Alkalmazásazonosító** , és jegyezze fel ezt az értéket.
7. Kattintson a **minden beállítás** > **szükséges engedélyek** > **Hozzáadás** > **API kiválasztása**.
8. Adja meg az App Service-alkalmazás, amely korábban regisztrált a keresési funkciót, majd jelölje ki azt, és kattintson a **kiválasztása**.
9. Válassza ki **hozzáférés \<app_name >**. Ezután kattintson a **Kiválasztás** elemre. Ezután kattintson a **Done** (Kész) gombra.

Most már konfigurálta a natív ügyfélalkalmazás az App Service-alkalmazás eléréséhez.

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
