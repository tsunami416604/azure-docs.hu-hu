---
title: Azure Active Directory hitelesítés konfigurálása – Azure App Service
description: Megtudhatja, hogyan konfigurálhatja Azure Active Directory hitelesítést a App Services alkalmazáshoz.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 41084c3532e29b3a52c121d48226c5a45857d5dc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088235"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>App Service alkalmazás konfigurálása Azure Active Directory bejelentkezés használatára

> [!NOTE]
> Jelenleg a HRE v2 (beleértve az MSAL-t is) nem támogatott az Azure App Services és Azure Functions esetén. Tekintse át a frissítéseket.
>

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure App Servicest Azure Active Directory hitelesítési szolgáltatóként való használatára.

## <a name="express"> </a>Konfigurálás az expressz beállításokkal

1. A [Azure Portal]navigáljon a app Service alkalmazáshoz. A bal oldali navigációs sávon válassza a **hitelesítés/engedélyezés**lehetőséget.
2. Ha a **hitelesítés/engedélyezés** nincs engedélyezve, válassza **a be**lehetőséget.
3. Válassza a **Azure Active Directory**lehetőséget, majd válassza az **expressz** lehetőséget a **felügyeleti mód**alatt.
4. Kattintson az **OK** gombra, hogy regisztrálja a app Service alkalmazást a Azure Active Directoryban. Ez létrehoz egy új alkalmazás-regisztrációt. Ha ehelyett egy meglévő alkalmazás-regisztrációt szeretne választani, kattintson a **meglévő alkalmazás kiválasztása** lehetőségre, majd keressen rá egy korábban létrehozott alkalmazás-regisztráció nevére a bérlőn belül.
   A kiválasztásához kattintson az alkalmazás regisztrálása elemre, majd kattintson **az OK**gombra. Ezután kattintson az **OK gombra** a Azure Active Directory beállítások lapon.
   Alapértelmezés szerint a App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyezni kell a felhasználókat az alkalmazás kódjában.
5. Választható Ha korlátozni szeretné a webhelyhez való hozzáférést a Azure Active Directory által hitelesített felhasználók számára, akkor állítsa be **a műveletet, ha a kérelem nem hitelesítve van** a Azure Active Directoryval való **bejelentkezéshez**. Ehhez minden kérést hitelesíteni kell, és az összes nem hitelesített kérelem át lesz irányítva a hitelesítéshez Azure Active Directory.

> [!CAUTION]
> A hozzáférés ezen a módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos, ha az alkalmazások nyilvánosan elérhető kezdőlapot szeretnének, például sok egyoldalas alkalmazásban. Ilyen alkalmazások esetén **engedélyezze a névtelen kérelmeket (nincs művelet)** előnyben részesített, ha az alkalmazás manuálisan indítja el a bejelentkezést, az [itt](overview-authentication-authorization.md#authentication-flow)leírtak szerint.

6. Kattintson a **Save** (Mentés) gombra.

## <a name="advanced"> </a>Konfigurálás speciális beállításokkal

A konfigurációs beállításokat manuálisan is megadhatja. Ez az előnyben részesített megoldás, ha a használni kívánt Azure Active Directory bérlő eltér az Azure-ba bejelentkezett bérlőtől. A konfiguráció befejezéséhez először létre kell hoznia egy regisztrációt Azure Active Directoryban, majd meg kell adnia néhány regisztrációs adatot a App Servicehoz.

### <a name="register"> </a>App Service alkalmazás regisztrálása a Azure Active Directory

1. Jelentkezzen be a [Azure Portal], és navigáljon a app Service alkalmazáshoz. Másolja az alkalmazás **URL-címét**. Ezt a Azure Active Directory alkalmazás regisztrációjának konfigurálásához fogja használni.
2. Navigáljon **Active Directory**, majd válassza ki a **Alkalmazásregisztrációk**, majd kattintson az **új alkalmazás regisztrálása** lehetőségre a lap tetején egy új alkalmazás regisztrálásának elindításához. 
3. A **Létrehozás** lapon adja meg az alkalmazás regisztrációjának **nevét** , válassza ki a **webalkalmazás/API** típusát a **bejelentkezési URL-cím** mezőben illessze be az alkalmazás URL-címét (az 1. lépésből). Ezután kattintson idea létrehozáshoz.
4. Néhány másodpercen belül megjelenik az imént létrehozott új alkalmazás-regisztráció.
5. Az alkalmazás regisztrációjának hozzáadása után kattintson az alkalmazás regisztrációjának nevére, kattintson a felül található **Beállítások** elemre, majd a **Tulajdonságok** elemre. 
6. Az alkalmazás- **azonosító URI-ja** mezőbe illessze be az alkalmazás URL-címét (az 1. lépésből), továbbá a **Kezdőlap URL** -címében ILLESSZE be az alkalmazás URL-címét (az 1. lépésből), majd kattintson a **Mentés** gombra.
7. Most kattintson a **Válasz URL**-címekre, szerkessze a **Válasz URL**-címét, illessze be az alkalmazás URL-címét (az 1. lépésből), majd fűzze hozzá az URL- `https://contoso.azurewebsites.net/.auth/login/aad/callback`cím végéhez, */.auth/login/aad/callback* (például). Kattintson a **Save** (Mentés) gombra.

   > [!NOTE]
   > A további **Válasz URL-címek**hozzáadásával ugyanazt az alkalmazást használhatja több tartományhoz. Győződjön meg arról, hogy az egyes App Service-példányokat saját regisztrációval modellezi, így saját engedélyekkel és jóváhagyással rendelkezik. Érdemes lehet külön alkalmazás-regisztrációkat használni a különálló hely tárolóhelyekhez. Ezzel elkerülhető, hogy az engedélyek ne legyenek megosztva a környezetek között, így a tesztelt új kódban lévő hibák nem befolyásolják a gyártást.
    
8. Ezen a ponton másolja át az **alkalmazás azonosítóját** . Tartsa a későbbi használat érdekében. Szüksége lesz rá a App Service alkalmazás konfigurálásához.
9. A **regisztrált alkalmazás** oldalának lezárása. A **Alkalmazásregisztrációk** lapon kattintson a felül található **végpontok** gombra, majd másolja a **WS-Federation bejelentkezési végpont** URL-címét, de távolítsa el `/wsfed` a végződést az URL-címről. A végeredménynek a következőhöz `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000`hasonlóan kell kinéznie:. A tartomány neve eltérő lehet a szuverén felhőben. Ez a kiállítói URL-ként fog szolgálni később.

### <a name="secrets"> </a>Azure Active Directory információk hozzáadása a app Service alkalmazáshoz

1. A [Azure Portal]ismét navigáljon a app Service alkalmazáshoz. Kattintson a **hitelesítés/engedélyezés**elemre. Ha a hitelesítés/engedélyezés funkció nincs engedélyezve, kapcsolja be a kapcsolót **a**következőre:. Az alkalmazás konfigurálásához kattintson **Azure Active Directory**, a hitelesítésszolgáltatók területen.

    Választható Alapértelmezés szerint a App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyezni kell a felhasználókat az alkalmazás kódjában. Állítsa be a végrehajtandó **műveletet, ha a kérelem** nincs hitelesítve a Azure Active Directoryba való **bejelentkezéshez**. Ehhez a beállításhoz az összes kérelem hitelesítése szükséges, és az összes nem hitelesített kérelem át lesz irányítva Azure Active Directory hitelesítésre.
2. A Active Directory hitelesítés konfigurációjában kattintson a **speciális** **felügyeleti mód**alatt a speciális elemre. Illessze be az alkalmazás AZONOSÍTÓját az ügyfél-azonosító mezőbe (a 8. lépésből), majd illessze be az URL-címet (a 9. lépésből) a kiállító URL-címébe. Ezután kattintson az **OK** gombra.
3. A Active Directory hitelesítés konfigurációja lapon kattintson a **Mentés**gombra.

Most már készen áll a Azure Active Directory használatára a App Service alkalmazásban való hitelesítéshez.

## <a name="configure-a-native-client-application"></a>Natív ügyfélalkalmazás konfigurálása
A natív ügyfelek regisztrálása lehetővé teszi az engedélyek leképezésének nagyobb mértékű szabályozását. Erre akkor van szükség, ha olyan ügyféloldali kódtár használatával kívánja végrehajtani a bejelentkezést, mint például a **Active Directory-hitelesítési tár**.

1. Navigáljon **Azure Active Directory** a [Azure Portal].
2. A bal oldali navigációs sávon válassza a **Alkalmazásregisztrációk**lehetőséget. Kattintson a felül található **új alkalmazás regisztrálása** elemre.
4. A **Létrehozás** lapon adja meg az alkalmazás regisztrációjának **nevét** . Válassza a **natív** lehetőséget az **alkalmazás típusa**területen.
5. Az **átirányítási URI** mezőben adja meg a hely */.auth/login/Done* -végpontját a https-séma használatával. Az értéknek a *https://contoso.azurewebsites.net/.auth/login/done* következőhöz hasonlónak kell lennie:. Windows-alkalmazás létrehozásakor Ehelyett használja a [csomag biztonsági azonosítóját](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) URI-ként.
5. Kattintson a **Create** (Létrehozás) gombra.
6. Miután hozzáadta az alkalmazás regisztrációját, válassza ki azt a megnyitásához. Keresse meg az **alkalmazás azonosítóját** , és jegyezze fel ezt az értéket.
7. Kattintson a **minden beállítás** > **szükséges engedélyek** > **Hozzáadás** > **API kiválasztása**elemre.
8. Írja be annak a App Service alkalmazásnak a nevét, amelyet korábban regisztrált, majd válassza ki, majd kattintson a **kiválasztás**gombra.
9. Válassza **a \<hozzáférés APP_NAME >** elemet. Ezután kattintson a **Kiválasztás** elemre. Ezután kattintson a **Done** (Kész) gombra.

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
