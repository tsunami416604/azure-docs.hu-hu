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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76984569"
---
1. Az [Azure Portalon](https://ms.portal.azure.com/)válassza az **Azure Active Directory** > **alkalmazás regisztrációk** > **Új regisztráció**.

   [![Új alkalmazásregisztráció az Azure Active Directoryban](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Az alkalmazás a regisztráció után itt jelenik meg.

1. Adjon nevet az alkalmazásnak, és válassza **a Fiókok lehetőséget ebben a szervezeti címtárban csak** az API-hoz hozzáférő támogatott **fióktípusok** megadásához. Válasszon egy érvényes URI-t, ahamelya felhasználókat a hitelesítés után átirányít, majd **regisztráljon.**

   [![Az alkalmazás létrehozása az Azure Active Directoryban](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Fontos Az Azure Active Directory alkalmazás adatai jelennek meg a felsorolt alkalmazás **áttekintése** panelen. Válassza ki az alkalmazást **a Saját alkalmazások**területen, majd az Áttekintés **lehetőséget.**

   [![Az alkalmazásazonosító másolása](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Másolja az **alkalmazás (ügyfél) azonosítóját** az ügyfélalkalmazásban való használatra.

1. A **Hitelesítés** panel fontos hitelesítési konfigurációs beállításokat ad meg. 

    1. Adja hozzá **az átirányítási URI-kat,** és konfigurálja **a hozzáférési jogkivonatokat** **a + Platform hozzáadása**lehetőség kiválasztásával .

    1. Az **Igen** vagy a **Nem**lehetőség kiválasztásával állapítsa meg, hogy az alkalmazás **nyilvános ügyfél-e** vagy sem.

    1. Ellenőrizze, hogy mely fiókok és bérlők támogatottak.

    [![Implicit támogatás konfigurálása](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. A megfelelő platform kiválasztása után konfigurálja az **átirányítási URI-kat** és a **hozzáférési jogkivonatokat** a felhasználói felület jobb oldalán lévő oldalsó panelen.

    1. **Az átirányítási URI-knak** meg kell egyezniük a hitelesítési kérelem által megadott címmel:

        * Helyi fejlesztői környezetben üzemeltetett alkalmazások esetén válassza a **Nyilvános ügyfél (mobil & asztali)** lehetőséget. Győződjön meg arról, hogy **a nyilvános ügyfelet** **Igen**beállításra állítja.
        * Az Azure App Service szolgáltatásban üzemeltetett egyoldalas alkalmazások esetén válassza a **Web**lehetőséget.

    1. Határozza meg, hogy a **kijelentkezési URL-cím** megfelelő-e.

    1. Engedélyezze az implicit támogatási **folyamatot az Access-jogkivonatok** vagy **-azonosítójogtok**ellenőrzésével.

    [![Átirányítási URI-k létrehozása](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Kattintson **a Konfigurálás**gombra, majd a **Mentés gombra.**

1. Válassza **a tanúsítványok & titkos kulcsokat,** majd az új **ügyféltitok** egy alkalmazásjelszót hozzon létre, amelyet az ügyfélalkalmazás az identitásának igazolására használhat.

   [![Új ügyféltitok létrehozása](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Ekkor megjelenik az ügyféltitkos jelszava. Másolja a kulcsot a kedvenc szövegszerkesztőbe.

   > [!NOTE]
   > Ehelyett importálhat egy tanúsítványt. A fokozott biztonság érdekében tanúsítvány használata ajánlott. Tanúsítvány használatához válassza a **Tanúsítvány feltöltése**lehetőséget.

1. Társítsa az Azure Active Directory-alkalmazást, az Azure TIme Series Insights-ot. **API-engedélyek** > **kiválasztása: Adjon hozzá egy** > **engedélyAPI-t, amelyet a szervezet használ.** 

    [![API társítása az Azure Active Directory-alkalmazáshoz](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Írja `Azure Time Series Insights` be a keresősávba, majd válassza a lehetőséget. `Azure Time Series Insights`

1. Ezután adja meg az alkalmazás által igényelt API-engedélyek et. Alapértelmezés szerint a **delegált engedélyek** ki lesznek emelve. Válasszon majd egy engedélytípust, és válassza **az Engedélyek hozzáadása**lehetőséget.

    [![Adja meg, hogy milyen API-engedélyre van szükség az alkalmazásnak](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
