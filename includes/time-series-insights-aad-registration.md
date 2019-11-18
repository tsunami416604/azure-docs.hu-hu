---
title: fájl belefoglalása
description: fájl belefoglalása
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 11/14/2019
ms.openlocfilehash: fa6921f8c4309f17cbd63e242a5416e7e81bb6c3
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133612"
---
1. A [Azure Portal](https://ms.portal.azure.com/)válassza a **Azure Active Directory** > **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget.

   [Új alkalmazás-regisztráció ![Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    A regisztrálás után az alkalmazás itt jelenik meg.

1. Adjon nevet az alkalmazásnak, és válassza ki a **fiókokat ebben a szervezeti könyvtárban csak** az API-hoz hozzáférő **támogatott fióktípus** megadásához. Válasszon egy érvényes URI-t a felhasználók a hitelesítés után való átirányításához, majd **regisztrálja**őket.

   [![hozza létre az alkalmazást a Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Fontos Azure Active Directory az alkalmazásadatok a felsorolt alkalmazás **Áttekintés** paneljén jelennek meg. Válassza ki az alkalmazást a **tulajdonában lévő alkalmazások**területen, majd az **Áttekintés**lehetőséget.

   [![az alkalmazás AZONOSÍTÓjának másolása](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Másolja az **alkalmazás (ügyfél) azonosítóját** az ügyfélalkalmazás használatára.

1. A **hitelesítés** panelen a fontos hitelesítési konfigurációs beállítások adhatók meg. 

    1. Az **átirányítási URI-azonosítóknak** meg kell egyezniük a hitelesítési kérelem által megadott címtől:

        * Helyi fejlesztési környezetben üzemeltetett alkalmazásokhoz válassza a **nyilvános ügyfél (mobil & asztali)** lehetőséget. Ügyeljen arra, hogy az **alapértelmezett ügyfél típusát** állítsa Igen értékre.
        * Azure App Serviceon üzemeltetett egylapos alkalmazások esetében válassza a **web**lehetőséget.

    1. Engedélyezze az implicit engedélyezési folyamatot a **hozzáférési jogkivonatok** vagy **azonosító tokenek**ellenőrzésével.

   [új ügyfél-titkos ![létrehozása](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Kattintson a **Save** (Mentés) gombra.

1. Válassza a **tanúsítványok & Secrets** , majd az **új ügyfél titka** elemet egy olyan alkalmazás jelszavának létrehozásához, amelyet az ügyfél a személyazonosságának bizonyítására használhat fel.

   [új ügyfél-titkos ![létrehozása](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Ekkor megjelenik az ügyfél titkos jelszava. Másolja a kulcsot a kedvenc szövegszerkesztőbe.

   > [!NOTE]
   > Lehetőség van egy tanúsítvány importálására. A fokozott biztonság érdekében ajánlott a tanúsítvány használata. A tanúsítvány használatához válassza a **tanúsítvány feltöltése**lehetőséget.

1. A Azure Active Directory alkalmazásának Azure TIme Series-beli bepillantást kell rendelnie. Válassza az **API-engedélyek** lehetőséget > a **szervezet által használt**engedély > API-k **hozzáadása** elemet. 

    [![API hozzárendelése a Azure Active Directory alkalmazáshoz](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Írja be `Azure Time Series Insights` a keresőmezőbe, majd válassza a `Azure Time Series Insights`lehetőséget.

1. Ezután adja meg az alkalmazás által igényelt API-engedélyt. Alapértelmezés szerint a **delegált engedélyek** ki lesznek emelve. Válassza ki az engedély típusát, majd válassza az **engedélyek hozzáadása**lehetőséget.

    [![adja meg az alkalmazás által igényelt API-engedély típusát](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)