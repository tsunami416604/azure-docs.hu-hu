---
title: Twitter-hitelesítés – az Azure App Service konfigurálása
description: Ismerje meg, hogyan konfigurálhatja az App Services-alkalmazás Twitter-hitelesítést.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 51a2ac93fd2d863855c820ba147418c5397c2a89
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53260327"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Twitter-bejelentkezés használatához App Service-alkalmazás konfigurálása
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a témakör bemutatja, hogyan konfigurálhatja az Azure App Service egy hitelesítésszolgáltató Twitter használandó.

Ebben a témakörben az eljárás végrehajtásához egy hitelesített e-mail címét és telefonszámát számot tartalmazó Twitter-fiók kell rendelkeznie. Új Twitter-fiók létrehozásához lépjen a <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Az alkalmazás regisztrálása a Twitterrel
1. Jelentkezzen be a [Azure Portal], és keresse meg az alkalmazást. Másolás a **URL-cím**. A Twitter-alkalmazás konfigurálása akkor fogja használni.
2. Keresse meg a [Twitter-fejlesztők számára] webhelyét, jelentkezzen be a Twitter-fiókja hitelesítő adataival, majd kattintson a **új alkalmazás létrehozása**.
3. Írja be a **neve** és a egy **leírás** az új alkalmazás. Illessze be az alkalmazás **URL-cím** számára a **webhely** értéket. Ezután a **visszahívási URL-Címének**, illessze be a **visszahívási URL-Címének** korábban vágólapra másolt. Ez az a Mobile App gateway hozzáfűzi az az elérési út */.auth/login/twitter/callback*. Például: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Győződjön meg arról, hogy használja a HTTPS-sémát.
4. A lap alsó olvassa el és fogadja el a feltételeket. Kattintson a **Twitter-alkalmazás létrehozása**. Ez regisztrálja az alkalmazás megjelenik az alkalmazás részletei.
5. Kattintson a **beállítások** lapon jelölje **telepítésének engedélyezése az alkalmazás segítségével jelentkezzen be Twitter**, majd kattintson a **beállítások**.
6. Válassza ki a **kulcsok és hozzáférési tokenek** fülre. Jegyezze fel a értékeit **fogyasztói kulcs (API-kulcs)** és **fogyasztói titkos kulcs (API titkos kódot)**.
   
   > [!NOTE]
   > A fogyasztói titkos kulcs egy fontos biztonsági hitelesítő adat. Ne a titkos kulcs bárkivel megoszthatja vagy osztja el az alkalmazást.
   > 
   > 

## <a name="secrets"> </a>Twitter-információk hozzáadása az alkalmazáshoz
1. Térjen vissza a [Azure Portal], keresse meg az alkalmazást. Kattintson a **beállítások**, majd **hitelesítési / engedélyezési**.
2. Ha a hitelesítés / engedélyezés funkció nincs engedélyezve, kapcsolja be a kapcsolót **a**.
3. Kattintson a **Twitter**. Illessze be az alkalmazás azonosítója és az alkalmazás titkos kulcsát a értékek, amelyek korábban beszerzett. Ezután kattintson az **OK** gombra.
   
   ![][1]
   
   Alapértelmezés szerint az App Service-ben hitelesítést biztosít, de a webhely tartalmát és API-k nem korlátozza a hitelesített hozzáférést. Felhasználók engedélyeznie kell az alkalmazás kódjában.
4. (Nem kötelező) A hozzáférés korlátozása csak a Twitter által hitelesített felhasználók a helyet, állítsa be **elvégzendő művelet, ha a kérés nincs hitelesítve** való **Twitter**. Ehhez az szükséges, hogy minden kérelmet hitelesíteni, és az összes nem hitelesített kérelmek Twitter-rendszer átirányítja a hitelesítéshez.
5. Kattintson a **Save** (Mentés) gombra.

Most már készen áll a Twitteren az alkalmazásban hitelesítéshez használandó.

## <a name="related-content"> </a>Kapcsolódó tartalom
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter-fejlesztők számára]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
