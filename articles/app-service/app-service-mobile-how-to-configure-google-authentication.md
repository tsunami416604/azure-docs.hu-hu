---
title: Google hitelesítési alkalmazásszolgáltatások alkalmazás konfigurálása
description: Tudnivalók az App Service szolgáltatások alkalmazás Google-hitelesítés konfigurálása.
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
ms.openlocfilehash: f89ff3a030f1da75bca538eefaf2496e9be8e97b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233819"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Az App Service alkalmazás használhatja a Google-bejelentkezés konfigurálása
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a témakör bemutatja, hogyan konfigurálhatja az Azure App Service egy hitelesítésszolgáltatót Google használandó.

Ebben a témakörben az eljárás végrehajtásához egy hitelesített e-mail-címmel rendelkező Google fiókkal kell rendelkeznie. Új Google-fiók létrehozásához látogassa meg az [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302) webhelyet.

## <a name="register"> </a>Az alkalmazás regisztrálása a Google
1. Jelentkezzen be a [Azure Portal], és keresse meg az alkalmazás. Másolás a **URL-cím**, amely később a Google alkalmazás konfigurálása.
2. Keresse meg a [Google API-k](http://go.microsoft.com/fwlink/p/?LinkId=268303) webhelyét, jelentkezzen be Google fiók hitelesítő adatait, kattintson **projekt létrehozása**, adjon meg egy **projekt neve**, majd kattintson a  **Hozzon létre**.
3. A projekt létrehozása után válassza ki azt. A projekt irányítópultján kattintson **Ugrás az áttekintéshez API-k**.
4. Válassza ki **API-k engedélyezése és a szolgáltatások**. Keresse meg **Google + API**, és válassza ki azt. Kattintson a **engedélyezése**.
5. A bal oldali navigációs **hitelesítő adatok** > **OAuth-hozzájárulás képernyő**, majd jelölje be a **E-mail cím**, adjon meg egy **termékneve**, és kattintson a **mentése**.
6. Az a **hitelesítő adatok** lapra, majd **hitelesítő adatok létrehozása** > **OAuth-Ügyfélazonosító**.
7. Az "Ügyfél-azonosító létrehozása" képernyőn válassza ki a **webes alkalmazás**.
8. Illessze be az App Service **URL-cím** másolt korábban be **jogosult JavaScript-források**, majd illessze be az átirányítási az URI **jogosult átirányítási URI-**. Az átirányítási URI megadása az alkalmazás fűzhető hozzá az elérési út az URL-cím */.auth/login/google/callback*. Például: `https://contoso.azurewebsites.net/.auth/login/google/callback`. Győződjön meg arról, hogy a HTTPS protokollt használ. Ezt követően kattintson a **Create** (Létrehozás) gombra.
9. A következő képernyőn jegyezze fel az ügyfél-azonosító és a titkos ügyfélkódot értékeit.

    > [!IMPORTANT]
    > A titkos ügyfélkulcs egy fontos biztonsági hitelesítő adatok. Ne ossza meg senkivel ezt a titkos kulcsot, és eloszthatják azt egy ügyfél-alkalmazással.


## <a name="secrets"> </a>Google-adatokat hozzáadni az alkalmazáshoz
1. Vissza a [Azure Portal], keresse meg az alkalmazás. Kattintson a **beállítások**, majd **hitelesítési / engedélyezési**.
2. Ha a hitelesítési / engedélyezési funkció nincs engedélyezve, kapcsolja be a kapcsoló **a**.
3. Kattintson a **Google**. Illessze be az alkalmazás Azonosítóját és az alkalmazás titkos kulcs értékek, amelyek korábban beszerzett, és opcionálisan engedélyezése az alkalmazás által igényelt összes hatókörök. Ezután kattintson az **OK** gombra.
   
   ![][1]
   
   Alapértelmezés szerint az App Service hitelesítést nyújt, de nem engedélyezett hozzáférés korlátozása a tartalom és API-k. Kell engedélyeznie a felhasználók az alkalmazás kódját.
4. (Választható) Csak a Google által hitelesített felhasználók a helyhez való hozzáférésének korlátozásához, állítsa be **hitelesítetlen kérés esetén elvégzendő művelet** való **Google**. Ehhez szükséges összes kérelmet hitelesíteni, és minden nem hitelesített kérelmek Google hitelesítésre van átirányítva.
5. Kattintson a **Save** (Mentés) gombra.

Most már készen áll a Google az alkalmazáson belüli hitelesítéshez használandó.

## <a name="related-content"> </a>Kapcsolódó tartalom
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

