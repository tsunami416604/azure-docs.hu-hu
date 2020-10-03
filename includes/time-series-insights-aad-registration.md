---
title: fájlbefoglalás
description: fájlbefoglalás
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 7de4dc21391f7dbd817c56ce51606a808cf9e3c4
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665811"
---
1. A [Azure Portal](https://ms.portal.azure.com/)válassza a **Azure Active Directory**  >  **Alkalmazásregisztrációk**  >  **új regisztráció**lehetőséget.

   [![Új alkalmazás regisztrálása Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    A regisztrálás után az alkalmazás itt jelenik meg.

1. Adjon nevet az alkalmazásnak, és válassza ki a **fiókokat ebben a szervezeti könyvtárban csak** az API-hoz hozzáférő **támogatott fióktípus** megadásához. Ha [nyilvános ügyfélprogramot](https://docs.microsoft.com/azure/active-directory/develop/msal-client-application-configuration#redirect-uri)hoz létre, adjon hozzá egy érvényes átirányítási URI-t, majd **regisztráljon**.

   [![Az alkalmazás létrehozása Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Fontos Azure Active Directory az alkalmazásadatok a felsorolt alkalmazás **Áttekintés** paneljén jelennek meg. Válassza ki az alkalmazást a **tulajdonában lévő alkalmazások**területen, majd az **Áttekintés**lehetőséget.

   [![Az alkalmazás AZONOSÍTÓjának másolása](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Másolja az **alkalmazás (ügyfél) azonosítóját** az ügyfélalkalmazás használatára.

1. A **hitelesítés** panelen a fontos hitelesítési konfigurációs beállítások adhatók meg.

    1. Adja hozzá az **átirányítási URI-ket** , és konfigurálja a **hozzáférési jogkivonatokat** a **+ platform hozzáadása**lehetőség kiválasztásával.

    1. Döntse el, hogy az alkalmazás **nyilvános** -e, vagy sem, ha az **Igen** vagy a **nem**lehetőséget választja.

    1. Ellenőrizze, hogy mely fiókok és bérlők támogatottak.

    [![Implicit engedélyezés konfigurálása](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. Miután kiválasztotta a megfelelő platformot, konfigurálja az **átirányítási URI-ket** és a **hozzáférési jogkivonatokat** a felhasználói felület jobb oldalán lévő oldalsó panelen.

    1. Az **átirányítási URI-azonosítóknak** meg kell egyezniük a hitelesítési kérelem által megadott címtől:

        * Helyi fejlesztési környezetben üzemeltetett alkalmazásokhoz válassza a **nyilvános ügyfél (mobil & asztali)** lehetőséget. Ügyeljen arra, hogy a **nyilvános ügyfelet** állítsa **Igen**értékre.
        * Azure App Serviceon üzemeltetett egylapos alkalmazások esetében válassza a **web**lehetőséget.

    1. Döntse el, hogy megfelelő-e a **kijelentkezési URL-cím** .

    1. Engedélyezze az implicit engedélyezési folyamatot a **hozzáférési jogkivonatok** vagy **azonosító tokenek**ellenőrzésével.

    [![Átirányítási URI-k létrehozása](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Kattintson a **Konfigurálás**, majd a **Mentés**elemre.

1. Válassza a **tanúsítványok & Secrets** , majd az **új ügyfél titka** elemet egy olyan alkalmazás jelszavának létrehozásához, amelyet az ügyfélalkalmazás a személyazonosságának igazolására használhat.

   [![Új ügyfél titkos kulcsának létrehozása](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Ekkor megjelenik az ügyfél titkos jelszava. Másolja a kulcsot a kedvenc szövegszerkesztőbe.

   > [!NOTE]
   > Lehetőség van egy tanúsítvány importálására. A fokozott biztonság érdekében ajánlott a tanúsítvány használata. A tanúsítvány használatához válassza a **tanúsítvány feltöltése**lehetőséget.

1. A Azure Active Directory alkalmazás Azure Time Series Insightsének hozzárendelése. Válassza az **API-engedélyek**lehetőséget a  >  **Add a permission**  >  **szervezetem által használt API**-k hozzáadása lehetőségre.

    [![API hozzárendelése a Azure Active Directory-alkalmazáshoz](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Írja be `Azure Time Series Insights` a keresősáv kifejezést, majd válassza a elemet `Azure Time Series Insights` .

1. Ezután adja meg az alkalmazás által igényelt API-engedélyt. Alapértelmezés szerint a **delegált engedélyek** ki lesznek emelve. Válassza ki az engedély típusát, majd válassza az **engedélyek hozzáadása**lehetőséget.

    [![Adja meg az alkalmazás által igényelt API-engedély típusát](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
