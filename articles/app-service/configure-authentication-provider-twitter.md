---
title: Twitter-hitelesítés konfigurálása – Azure App Service
description: Megtudhatja, hogyan konfigurálhatja a Twitter-hitelesítést a App Services alkalmazáshoz.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 728eacdcb0ee0d0bee878ff4764b1ca5e430c59c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088201"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>A App Service alkalmazás konfigurálása a Twitter-bejelentkezés használatára
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a témakör bemutatja, hogyan konfigurálhatja a Azure App Servicet a Twitter hitelesítési szolgáltatóként való használatához.

A jelen témakörben ismertetett eljárás végrehajtásához olyan Twitter-fiókkal kell rendelkeznie, amely ellenőrzött e-mail-címmel és telefonszámmal rendelkezik. Új Twitter-fiók létrehozásához nyissa meg a <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">Twitter.com</a>.

## <a name="register"> </a>Alkalmazás regisztrálása a Twitteren
1. Jelentkezzen be a [Azure Portal], és navigáljon az alkalmazáshoz. Másolja az **URL-címet**. Ezt fogja használni a Twitter-alkalmazás konfigurálásához.
2. Navigáljon a [Twitter-fejlesztők] webhelyre, jelentkezzen be a Twitter-fiókja hitelesítő adataival, és kattintson az **új alkalmazás létrehozása**elemre.
3. Írja be az új alkalmazás **nevét** és **leírását** . Illessze be az alkalmazás **URL-címét** a **webhely** értékéhez. Ezután a visszahívási **URL-címhez**illessze be a korábban átmásolt visszahívási **URL-címet** . Ez a Mobile App Gateway a */.auth/login/Twitter/callback*elérési úttal van hozzáfűzve. Például: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Győződjön meg arról, hogy a HTTPS-sémát használja.
4. A lap alján olvassa el és fogadja el a feltételeket. Ezután kattintson **a Twitter-alkalmazás létrehozása**elemre. Ez regisztrálja az alkalmazást, és megjeleníti az alkalmazás részleteit.
5. Kattintson a **Beállítások** lapra, jelölje be az **alkalmazás használatának engedélyezése**a Twitterrel való bejelentkezéshez lehetőséget, majd kattintson a **beállítások frissítése**elemre.
6. Válassza a **kulcsok és hozzáférési tokenek** fület. Jegyezze fel a **fogyasztói kulcs (API-kulcs)** és a **fogyasztói titok (API-titok)** értékeit.
   
   > [!NOTE]
   > A fogyasztói titok egy fontos biztonsági hitelesítő adat. Ezt a titkos kódot Ne ossza meg senkivel, vagy ossza meg az alkalmazásával.
   > 
   > 

## <a name="secrets"> </a>Twitter-információk hozzáadása az alkalmazáshoz
1. Térjen vissza a [Azure Portal], navigáljon az alkalmazáshoz. Kattintson a **Beállítások**, majd a **hitelesítés/engedélyezés**elemre.
2. Ha a hitelesítés/engedélyezés funkció nincs engedélyezve, kapcsolja be a kapcsolót **a**következőre:.
3. Kattintson a **Twitter**elemre. Illessze be az alkalmazás AZONOSÍTÓját és a korábban beszerzett alkalmazás titkos értékeit. Ezután kattintson az **OK** gombra.
   
   ![][1]
   
   Alapértelmezés szerint a App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyezni kell a felhasználókat az alkalmazás kódjában.
4. Választható Ha a webhelyhez való hozzáférést csak a Twitter által hitelesített felhasználókra szeretné korlátozni, állítsa be a **végrehajtandó műveletet, ha a kérés nincs hitelesítve** a **Twitteren**. Ehhez minden kérelmet hitelesíteni kell, és az összes nem hitelesített kérést a rendszer átirányítja a Twitterre a hitelesítéshez.

> [!CAUTION]
> A hozzáférés ezen a módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos, ha az alkalmazások nyilvánosan elérhető kezdőlapot szeretnének, például sok egyoldalas alkalmazásban. Ilyen alkalmazások esetén **engedélyezze a névtelen kérelmeket (nincs művelet)** előnyben részesített, ha az alkalmazás manuálisan indítja el a bejelentkezést, az [itt](overview-authentication-authorization.md#authentication-flow)leírtak szerint.

5. Kattintson a **Save** (Mentés) gombra.

Most már készen áll a Twitter használatára a hitelesítéshez az alkalmazásban.

## <a name="related-content"> </a>Related Content
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter-fejlesztők]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
