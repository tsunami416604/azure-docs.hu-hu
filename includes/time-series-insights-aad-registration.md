---
title: fájl belefoglalása
description: fájl belefoglalása
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 09/24/2019
ms.openlocfilehash: ccfbd16f4db770558f1bc0284860a5f8d9fb8b68
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266942"
---
> [!IMPORTANT]
> * Az új **Azure Active Directory** > **Alkalmazásregisztrációk** panel az örökölt **Azure Active Directory** > **Alkalmazásregisztrációk (örökölt)** panelt váltja fel 2019.
> * Az örökölt panelen létrehozott vagy megjelenő Alkalmazásregisztrációk automatikusan megjelenik az új panelen.
> * Az új Azure-alkalmazás regisztrációs felületére való áttelepítéssel kapcsolatos részletes információkért olvassa el az [azure Alkalmazásregisztrációk képzési útmutatót](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide) , és [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)a gyors üzembe helyezést.

1. A [Azure Portal](https://ms.portal.azure.com/)válassza a **Azure Active Directory** > **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget.

   [![Új alkalmazás regisztrálása Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > Az új Azure Active Directory alkalmazás regisztrációs panelje lehetővé teszi a megjelenített alkalmazások szűrését a **saját alkalmazások**kiválasztásával.

    A regisztrálás után az alkalmazás itt jelenik meg.

1. Adjon nevet az alkalmazásnak, és válassza ki a **fiókokat ebben a szervezeti könyvtárban csak** az API-hoz hozzáférő **támogatott fióktípus** megadásához. Válasszon egy érvényes URI-t a felhasználók a hitelesítés után való átirányításához, majd **regisztrálja**őket.

   [![Az alkalmazás létrehozása Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Fontos Azure Active Directory az alkalmazásadatok a felsorolt alkalmazás **Áttekintés** paneljén jelennek meg. Válassza ki az alkalmazást a **tulajdonában lévő alkalmazások**területen, majd az **Áttekintés**lehetőséget.

   [![Az alkalmazás AZONOSÍTÓjának másolása](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Másolja az **alkalmazás (ügyfél) azonosítóját** az ügyfélalkalmazás használatára.

1. A **hitelesítés** panelen a fontos hitelesítési konfigurációs beállítások adhatók meg. 

    1. Az **átirányítási URI-azonosítóknak** meg kell egyezniük a hitelesítési kérelem által megadott címtől:

        * Helyi fejlesztési környezetben üzemeltetett alkalmazásokhoz válassza a **nyilvános ügyfél (mobil & asztali)** lehetőséget. Ügyeljen arra, hogy az **alapértelmezett ügyfél típusát** állítsa Igen értékre.
        * Azure App Serviceon üzemeltetett egylapos alkalmazások esetében válassza a **web**lehetőséget.

    1. Engedélyezze az implicit engedélyezési folyamatot az **azonosító tokenek**ellenőrzésével.

   [![Új ügyfél titkos kulcsának létrehozása](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Kattintson a **Save** (Mentés) gombra.

1. Válassza a **tanúsítványok & Secrets** , majd az **új ügyfél titka** elemet egy olyan alkalmazás jelszavának létrehozásához, amelyet az ügyfél a személyazonosságának bizonyítására használhat fel.

   [![Új ügyfél titkos kulcsának létrehozása](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Ekkor megjelenik az ügyfél titkos jelszava. Másolja a kulcsot a kedvenc szövegszerkesztőbe.

   > [!NOTE]
   > Lehetőség van egy tanúsítvány importálására. A fokozott biztonság érdekében ajánlott a tanúsítvány használata. A tanúsítvány használatához válassza a **tanúsítvány feltöltése**lehetőséget.

1. A Azure Active Directory alkalmazásának Azure TIme Series-beli bepillantást kell rendelnie. Válassza **az API-engedélyek** > lehetőséget a**szervezetem által használt API**-k**hozzáadása** > lehetőségre. 

    [![API hozzárendelése a Azure Active Directory-alkalmazáshoz](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Írja `Azure Time Series Insights` be a keresősáv kifejezést, majd `Azure Time Series Insights`válassza a elemet.

1. Ezután adja meg az alkalmazás által igényelt API-engedélyt. Alapértelmezés szerint a **delegált engedélyek** ki lesznek emelve. Válassza ki az engedély típusát, majd válassza az **engedélyek hozzáadása**lehetőséget.

    [![Adja meg az alkalmazás által igényelt API-engedély típusát](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)