---
title: Microsoft-fiók hitelesítésének konfigurálása – Azure App Service
description: Ismerje meg, hogyan konfigurálhatja a Microsoft-fiók hitelesítését a App Services alkalmazáshoz.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/08/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 729c235ee51a904714358221389703632fd8296c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033639"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>A App Service alkalmazás konfigurálása a Microsoft-fiók bejelentkezési használatára
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ebből a témakörből megtudhatja, hogyan konfigurálhatja a Azure App Servicet a Microsoft-fiók hitelesítési szolgáltatóként való használatára. 

## <a name="register-microsoft-account"> </a>Alkalmazás regisztrálása a Microsoft-fiókkal
1. Jelentkezzen be a [Azure Portal], és navigáljon az alkalmazáshoz. 

<!-- Copy your **URL**, which you will use later to configure your app with Microsoft Account. -->
1. Ha szükséges, navigáljon [**Alkalmazásregisztrációk**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade), és jelentkezzen be a Microsoft-fiók.

1. Kattintson az **új regisztráció**elemre, majd írja be az alkalmazás nevét.

1. Az **átirányítási URI**-k területen válassza a web `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`lehetőséget, majd írja be a következőt:. Cserélje le  *\<az App-domain-name > nevet* az alkalmazás tartománynevére.  Például: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. 

   > [!NOTE]
   > Használja a HTTPS-sémát az URL-címben.

1. Válassza a **regisztráció**lehetőséget. 

1. Másolja az **alkalmazás (ügyfél) azonosítóját**. Később szüksége lesz rá. 
   
7. Az új alkalmazás regisztrációjának bal oldali navigációs sávján válassza a **tanúsítványok & titkos kulcsok** > **új ügyfél titka**lehetőséget. Adja meg a leírást, válassza ki az érvényesség időtartamát, és válassza a **Hozzáadás**lehetőséget.

1. Másolja a **tanúsítványok & titkok** lapon megjelenő értéket. Ha elhagyja a lapot, nem jelenik meg újra.

    > [!IMPORTANT]
    > A jelszó egy fontos biztonsági hitelesítő adat. Ne ossza meg senkivel a jelszót, vagy küldje el azt egy ügyfélalkalmazáson belül.

## <a name="secrets"> </a>Microsoft-fiókadatok hozzáadása a app Service-alkalmazáshoz
1. A [Azure Portal]navigáljon az alkalmazáshoz. A bal oldali navigációs sávon kattintson a **hitelesítés/engedélyezés**elemre.

2. Ha a hitelesítés/engedélyezés funkció nincs engedélyezve, válassza **a be**lehetőséget.

3. A **hitelesítésszolgáltatók**területen válassza a **Microsoft-fiók**lehetőséget. Illessze be az alkalmazás (ügyfél) AZONOSÍTÓját és az ügyfél titkos kulcsát, amelyet korábban kapott, és opcionálisan engedélyezheti az alkalmazás által igényelt hatóköröket. Ezután kattintson az **OK** gombra.

    Alapértelmezés szerint a App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyezni kell a felhasználókat az alkalmazás kódjában.

4. Választható Ha korlátozni szeretné Microsoft-fiók felhasználók hozzáférését, állítsa be **a végrehajtandó műveletet, ha a kérés nincs hitelesítve** a Microsoft-fiókkal való **bejelentkezéshez**. Ehhez minden kérést hitelesíteni kell, és az összes nem hitelesített kérelem át lesz irányítva a hitelesítéshez Microsoft-fiók.

> [!CAUTION]
> A hozzáférés ezen a módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos, ha az alkalmazások nyilvánosan elérhető kezdőlapot szeretnének, például sok egyoldalas alkalmazásban. Ilyen alkalmazások esetén **engedélyezze a névtelen kérelmeket (nincs művelet)** előnyben részesített, ha az alkalmazás manuálisan indítja el a bejelentkezést, az [itt](overview-authentication-authorization.md#authentication-flow)leírtak szerint.

5. Kattintson a **Save** (Mentés) gombra.

Most már készen áll a Microsoft-fiók használatára a hitelesítéshez az alkalmazásban.

## <a name="related-content"> </a>Kapcsolódó tartalom
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
