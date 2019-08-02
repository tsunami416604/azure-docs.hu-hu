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
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 10b661f0c4b7dc45284b907e83df3c0372f97cab
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561547"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>A App Service alkalmazás konfigurálása a Microsoft-fiók bejelentkezési használatára
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ebből a témakörből megtudhatja, hogyan konfigurálhatja a Azure App Servicet a Microsoft-fiók hitelesítési szolgáltatóként való használatára. 

## <a name="register-microsoft-account"> </a>Alkalmazás regisztrálása a Microsoft-fiókkal
1. Jelentkezzen be a [Azure Portal], és navigáljon az alkalmazáshoz. Másolja az **URL-címet**, amelyet később az alkalmazás Microsoft-fiókkal való konfigurálásához használ.
2. Ha szükséges, navigáljon [**Alkalmazásregisztrációk**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade), és jelentkezzen be a Microsoft-fiók.
3. Kattintson **az alkalmazás hozzáadása**elemre, írja be az alkalmazás nevét, majd kattintson a **Létrehozás**gombra.
4. Jegyezze fel az **alkalmazás azonosítóját**, mert később szüksége lesz rá. 
5. A "platformok" alatt kattintson a **platform hozzáadása** elemre, és válassza a "web" lehetőséget.
6. Az "URI-k átirányítása" alatt adja meg az alkalmazás végpontját, majd kattintson a **Save (Mentés**) gombra. 
   
   > [!NOTE]
   > Az átirányítási URI az alkalmazás URL-címe, amely a */.auth/login/MicrosoftAccount/callback*elérési úttal van hozzáfűzve. Például: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Győződjön meg arról, hogy a HTTPS-sémát használja.
   
7. Az "alkalmazás titkai" területen kattintson az **új jelszó**előállítása elemre. Jegyezze fel a megjelenő értéket. Ha elhagyja a lapot, nem jelenik meg újra.

    > [!IMPORTANT]
    > A jelszó egy fontos biztonsági hitelesítő adat. Ne ossza meg senkivel a jelszót, vagy küldje el azt egy ügyfélalkalmazáson belül.
    
8. Kattintson a **Save** (Mentés) gombra

## <a name="secrets"> </a>Microsoft-fiókadatok hozzáadása a app Service-alkalmazáshoz
1. Térjen vissza a [Azure Portal], navigáljon az alkalmazáshoz, és kattintson a **Beállítások** > **hitelesítés/engedélyezés**elemre.
2. Ha a hitelesítés/engedélyezés funkció nincs engedélyezve, kapcsolja be **a**következőt:.
3. Kattintson a **Microsoft-fiók**lehetőségre. Illessze be a korábban beszerzett alkalmazás-azonosító és jelszó értékeket, és opcionálisan engedélyezze az alkalmazás által igényelt hatóköröket. Ezután kattintson az **OK** gombra.
   
    ![][1]
   
    Alapértelmezés szerint a App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyezni kell a felhasználókat az alkalmazás kódjában.
4. Választható Ha a webhelyhez való hozzáférést csak Microsoft-fiók által hitelesített felhasználóknak szeretné korlátozni, állítsa be a **végrehajtandó műveletet, ha a kérés nincs hitelesítve** a **Microsoft**-fiókkal. Ehhez minden kérést hitelesíteni kell, és az összes nem hitelesített kérelem át lesz irányítva a hitelesítéshez Microsoft-fiók.
5. Kattintson a **Save** (Mentés) gombra.

Most már készen áll a Microsoft-fiók használatára a hitelesítéshez az alkalmazásban.

## <a name="related-content"> </a>Kapcsolódó tartalom
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
