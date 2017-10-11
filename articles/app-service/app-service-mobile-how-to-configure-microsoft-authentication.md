---
title: "Az App Service szolgáltatások alkalmazás Microsoft Account hitelesítés konfigurálása"
description: "Megtudhatja, hogyan konfigurálja az App Service szolgáltatások alkalmazás Microsoft Account hitelesítést."
author: mattchenderson
services: app-service
documentationcenter: 
manager: syntaxc4
editor: 
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 67386b03ae4cc683fe00e11e8dad19d1442eff09
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Az App Service alkalmazás használatához Microsoft Account bejelentkezés konfigurálása
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a témakör bemutatja, hogyan konfigurálhatja az Azure App Service-nek Microsoft Account használja, mint egy hitelesítésszolgáltatót. 

## <a name="register-microsoft-account"></a>Regisztrálja az alkalmazást a Microsoft-fiók
1. Jelentkezzen be a [Azure-portálon], és keresse meg az alkalmazás. Másolás a **URL-cím**, amelyet később az alkalmazás konfigurálása a Microsoft Account használhat.
2. Keresse meg a [saját alkalmazások] a Microsoft Account Developer Center weblapjára, és jelentkezzen be Microsoft-fiókjával, ha szükséges.
3. Kattintson a **hozzáadhat egy alkalmazást**, majd írja be az alkalmazás nevét, majd kattintson **alkalmazás létrehozása**.
4. Jegyezze fel a **Alkalmazásazonosító**, mert később szüksége lesz az. 
5. Kattintson a "Platformok," **hozzáadása Platform** válassza ki a "Web".
6. A "Átirányítási URI-k" adja meg a végpont az alkalmazáshoz, majd kattintson az **mentése**. 
   
   > [!NOTE]
   > Az átirányítási URI megadása az alkalmazás fűzhető hozzá az elérési út az URL-cím */.auth/login/microsoftaccount/callback*. Például: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Győződjön meg arról, hogy a HTTPS protokollt használ.
   
7. Kattintson a "Titkos kulcsok alkalmazás," **új jelszó létrehozása**. Jegyezze fel az érték, amely akkor jelenik meg. Ha elhagyja a lapot, akkor nem jelenik meg újra.

    > [!IMPORTANT]
    > A jelszó nem egy fontos biztonsági hitelesítő adatok. Ne ossza meg senkivel a jelszót, vagy eloszthatják azt egy ügyfél-alkalmazással.

## <a name="secrets"></a>Az App Service alkalmazás azon Microsoft-fiók hozzáadása
1. Vissza a [Azure-portálon], keresse meg az alkalmazás, kattintson a **beállítások** > **hitelesítési / engedélyezési**.
2. Ha a hitelesítési / engedélyezési funkció nincs engedélyezve, kapcsolja **a**.
3. Kattintson a **Microsoft-fiók**. Illessze be az alkalmazás Azonosítóját és jelszavát értékek, amelyek korábban beszerzett, és opcionálisan engedélyezése az alkalmazás által igényelt összes hatókörök. Ezután kattintson az **OK** gombra.
   
    ![][1]
   
    Alapértelmezés szerint az App Service hitelesítést nyújt, de nem engedélyezett hozzáférés korlátozása a tartalom és API-k. Kell engedélyeznie a felhasználók az alkalmazás kódját.
4. (Választható) Csak a Microsoft-fiók által hitelesített felhasználók a helyhez való hozzáférésének korlátozásához, állítsa be **hitelesítetlen kérés esetén elvégzendő művelet** való **Microsoft Account**. Ehhez szükséges összes kérelmet hitelesíteni, és minden nem hitelesített kérelmek Microsoft-fiók hitelesítési van átirányítva.
5. Kattintson a **Save** (Mentés) gombra.

Most már készen áll a Microsoft Account használja a hitelesítéshez, az alkalmazásban.

## <a name="related-content"></a>Kapcsolódó tartalom
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[saját alkalmazások]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-portálon]: https://portal.azure.com/
