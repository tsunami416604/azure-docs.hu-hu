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
ms.date: 02/03/2020
ms.openlocfilehash: 5be6e7937a6e1f710b8e2576a9058963413fb6c2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984569"
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

    1. Adja hozzá az **átirányítási URI-ket** , és konfigurálja a **hozzáférési jogkivonatokat** a **+ platform hozzáadása**lehetőség kiválasztásával.

    1. Döntse el, hogy az alkalmazás **nyilvános** -e, vagy sem, ha az **Igen** vagy a **nem**lehetőséget választja.

    1. Ellenőrizze, hogy mely fiókok és bérlők támogatottak.

    [![az implicit engedélyezés konfigurálása](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. Miután kiválasztotta a megfelelő platformot, konfigurálja az **átirányítási URI-ket** és a **hozzáférési jogkivonatokat** a felhasználói felület jobb oldalán lévő oldalsó panelen.

    1. Az **átirányítási URI-azonosítóknak** meg kell egyezniük a hitelesítési kérelem által megadott címtől:

        * Helyi fejlesztési környezetben üzemeltetett alkalmazásokhoz válassza a **nyilvános ügyfél (mobil & asztali)** lehetőséget. Ügyeljen arra, hogy a **nyilvános ügyfelet** állítsa **Igen**értékre.
        * Azure App Serviceon üzemeltetett egylapos alkalmazások esetében válassza a **web**lehetőséget.

    1. Döntse el, hogy megfelelő-e a **kijelentkezési URL-cím** .

    1. Engedélyezze az implicit engedélyezési folyamatot a **hozzáférési jogkivonatok** vagy **azonosító tokenek**ellenőrzésével.

    [átirányítási URI-k létrehozása ![](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Kattintson a **Konfigurálás**, majd a **Mentés**elemre.

1. Válassza a **tanúsítványok & Secrets** , majd az **új ügyfél titka** elemet egy olyan alkalmazás jelszavának létrehozásához, amelyet az ügyfélalkalmazás a személyazonosságának igazolására használhat.

   [új ügyfél-titkos ![létrehozása](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Ekkor megjelenik az ügyfél titkos jelszava. Másolja a kulcsot a kedvenc szövegszerkesztőbe.

   > [!NOTE]
   > Lehetőség van egy tanúsítvány importálására. A fokozott biztonság érdekében ajánlott a tanúsítvány használata. A tanúsítvány használatához válassza a **tanúsítvány feltöltése**lehetőséget.

1. A Azure Active Directory alkalmazásának Azure TIme Series-beli bepillantást kell rendelnie. Válassza az **API-engedélyek** lehetőséget > a **szervezet által használt**engedély > API-k **hozzáadása** elemet. 

    [![API hozzárendelése a Azure Active Directory alkalmazáshoz](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Írja be `Azure Time Series Insights` a keresőmezőbe, majd válassza a `Azure Time Series Insights`lehetőséget.

1. Ezután adja meg az alkalmazás által igényelt API-engedélyt. Alapértelmezés szerint a **delegált engedélyek** ki lesznek emelve. Válassza ki az engedély típusát, majd válassza az **engedélyek hozzáadása**lehetőséget.

    [![adja meg az alkalmazás által igényelt API-engedély típusát](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
