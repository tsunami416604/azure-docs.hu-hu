---
title: Hitelesítés Microsoft-Account – az Azure App Service konfigurálása
description: Ismerje meg az App Services-alkalmazás Microsoft Account hitelesítésének konfigurálása.
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
ms.openlocfilehash: e3da856efd7d44f15f9de27c9e38375d40dc211d
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410990"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>A Microsoft Account login használata App Service-alkalmazás konfigurálása
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a témakör bemutatja, hogyan konfigurálhatja az Azure App Service-ben a Microsoft Account használja, mint egy hitelesítési szolgáltatót. 

## <a name="register-microsoft-account"> </a>Az alkalmazás regisztrálásához és Microsoft-fiók
1. Jelentkezzen be a [Azure Portal], és keresse meg az alkalmazást. Másolás a **URL-cím**, amelyet később használhat az alkalmazás konfigurálása a Microsoft Account.
2. Keresse meg a [saját alkalmazások] a Microsoft Account Developer Center lapját, és jelentkezzen be Microsoft-fiókjával, ha szükséges.
3. Kattintson a **alkalmazás hozzáadása**, majd írja be az alkalmazás nevét, és kattintson a **létrehozás**.
4. Jegyezze fel a **Alkalmazásazonosító**, mivel később lesz szüksége. 
5. Kattintson a "Platformok," **hozzáadása Platform** , és válassza ki a "Webes".
6. "Átirányítási URI-k" területen adja meg a végpont az alkalmazáshoz, majd kattintson a **mentése**. 
   
   > [!NOTE]
   > Az átirányítási URI-ja URL-címét az elérési utat, kiegészítve az alkalmazás */.auth/login/microsoftaccount/callback*. Például: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Győződjön meg arról, hogy használja a HTTPS-sémát.
   
7. Kattintson a "A titkos Alkalmazáskulcsok," **új jelszó készítése**. Jegyezze fel az értéket, amely akkor jelenik meg. Ha elhagyja a lapot, akkor nem fog megjelenni újra.

    > [!IMPORTANT]
    > A jelszó, egy fontos biztonsági hitelesítő adat. Ne bárkivel megoszthatja a jelszót, és az ügyfélalkalmazások belül terjeszthet.
    
8. Kattintson a **Mentés** gombra.

## <a name="secrets"> </a>App Service-alkalmazás hozzáadása a Microsoft Account adatait
1. Térjen vissza a [Azure Portal]lépjen az alkalmazás, kattintson a **beállítások** > **hitelesítési / engedélyezési**.
2. Ha a hitelesítés / engedélyezés funkció nincs engedélyezve, módba **a**.
3. Kattintson a **Microsoft-fiók**. Illessze be az alkalmazás Azonosítóját és jelszavát értékeket, amelyek korábban beszerzett, és szükség esetén engedélyezze a bármely az alkalmazás által kért hatókörök. Ezután kattintson az **OK** gombra.
   
    ![][1]
   
    Alapértelmezés szerint az App Service-ben hitelesítést biztosít, de a webhely tartalmát és API-k nem korlátozza a hitelesített hozzáférést. Felhasználók engedélyeznie kell az alkalmazás kódjában.
4. (Nem kötelező) A hozzáférés korlátozása csak a Microsoft-fiók által hitelesített felhasználók a helyet, állítsa be **elvégzendő művelet, ha a kérés nincs hitelesítve** való **Microsoft Account**. Ehhez az szükséges, hogy minden kérelmet hitelesíteni, és az összes nem hitelesített kérelmek a rendszer átirányítja a hitelesítéshez a Microsoft-fiókjával.
5. Kattintson a **Save** (Mentés) gombra.

Most már készen áll a Microsoft Account használja a hitelesítéshez saját alkalmazásokban.

## <a name="related-content"> </a>Kapcsolódó tartalom
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Saját alkalmazások]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
