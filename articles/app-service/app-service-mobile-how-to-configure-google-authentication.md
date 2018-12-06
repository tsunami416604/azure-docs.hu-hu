---
title: Az App Services-alkalmazás Google-hitelesítés konfigurálása
description: Ismerje meg, hogyan konfigurálja az App Services-alkalmazás hitelesítés Google-fiókkal.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 35e02defb8b28c60c70c8b50c28bba8499392377
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961537"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Google-bejelentkezés használatához App Service-alkalmazás konfigurálása
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a témakör bemutatja, hogyan konfigurálhatja az Azure App Service, Google adatokként egy hitelesítési szolgáltatót.

Ebben a témakörben az eljárás végrehajtásához egy hitelesített e-mail-címmel rendelkező Google-fiókkal kell rendelkeznie. Új Google-fiók létrehozásához látogassa meg az [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) webhelyet.

## <a name="register"> </a>Regisztrálja az alkalmazást a Google-lel
1. Jelentkezzen be a [Azure Portal], és keresse meg az alkalmazást. Másolás a **URL-cím**, amely, amelyet később az Google-alkalmazás konfigurálása.
2. Keresse meg a [Google API-k](https://go.microsoft.com/fwlink/p/?LinkId=268303) webhelyét, jelentkezzen be a Google hitelesítő adatait, kattintson a **projekt létrehozása**, adja meg egy **projektnév**, majd kattintson  **Hozzon létre**.
3. A projekt létrehozása után kattintson rá. A projekt irányítópultján kattintson **nyissa meg az API-k áttekintése**.
4. Válassza ki **API-k engedélyezése és a szolgáltatások**. Keresse meg **Google + API**, és válassza ki azt. Kattintson a **engedélyezése**.
5. A bal oldali navigációs **hitelesítő adatok** > **OAuth-hozzájárulási képernyő**, majd válassza ki a **E-mail-cím**, adjon meg egy **terméknév**, és kattintson a **mentése**.
6. Az a **hitelesítő adatok** lapra, majd **hitelesítő adatok létrehozása** > **OAuth-Ügyfélazonosító**.
7. Az "Ügyfél-azonosító létrehozása" képernyőn válassza ki a **webes alkalmazás**.
8. Illessze be az App Service **URL-cím** be korábban másolt **JavaScript engedélyezett eredetek**, majd illessze be az átirányítási URI-t az **jogosult átirányítási URI-t**. Az átirányítási URI-ja URL-címét az elérési utat, kiegészítve az alkalmazás */.auth/login/google/callback*. Például: `https://contoso.azurewebsites.net/.auth/login/google/callback`. Győződjön meg arról, hogy használja a HTTPS-sémát. Ezt követően kattintson a **Create** (Létrehozás) gombra.
9. A következő képernyőn jegyezze fel az ügyfél-Azonosítóját és ügyfélkulcsát értékeit.

    > [!IMPORTANT]
    > Az ügyfél titkos kulcsát egy fontos biztonsági hitelesítő adat. Ne a titkos kulcs bárkivel megoszthatja, és egy ügyfélalkalmazás belül terjeszthet.


## <a name="secrets"> </a>Google-információk hozzáadása az alkalmazáshoz
1. Térjen vissza a [Azure Portal], keresse meg az alkalmazást. Kattintson a **beállítások**, majd **hitelesítési / engedélyezési**.
2. Ha a hitelesítés / engedélyezés funkció nincs engedélyezve, kapcsolja be a kapcsolót **a**.
3. Kattintson a **Google**. Illessze be az Alkalmazásazonosítót és titkos Alkalmazáskulcs értékeket, amelyek korábban beszerzett, és szükség esetén engedélyezze a bármely az alkalmazás által kért hatókörök. Ezután kattintson az **OK** gombra.
   
   ![][1]
   
   Alapértelmezés szerint az App Service-ben hitelesítést biztosít, de a webhely tartalmát és API-k nem korlátozza a hitelesített hozzáférést. Felhasználók engedélyeznie kell az alkalmazás kódjában.
4. (Nem kötelező) A hozzáférés korlátozása csak a Google által hitelesített felhasználók a helyet, állítsa be **elvégzendő művelet, ha a kérés nincs hitelesítve** való **Google**. Ehhez az szükséges, hogy minden kérelmet hitelesíteni, és az összes nem hitelesített kérelmek a rendszer átirányítja a Google-hitelesítéshez.
5. Kattintson a **Save** (Mentés) gombra.

Most már készen áll a Google használnak a hitelesítéshez saját alkalmazásokban.

## <a name="related-content"> </a>Kapcsolódó tartalom
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

