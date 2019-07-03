---
title: fájl belefoglalása
description: fájl belefoglalása
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 07/02/2019
ms.openlocfilehash: a463e3cf475909c34054717460dc10dbba4ad8f0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543957"
---
> [!IMPORTANT]
> * Az új **Azure Active Directory** > **alkalmazásregisztrációk** panel váltja fel a régebbi **Azure Active Directory** > **alkalmazás (örökölt) regisztrációk** 2019. május panelen.
> * Létrehozott vagy az örökölt panelen megjelenő alkalmazásregisztrációk automatikusan megjelenik az új panelen.
> * Átfogó információk az új Azure-alkalmazás regisztrációs felhasználói felületre történő áttelepítésről, olvassa el a [képzési útmutató Azure alkalmazásregisztrációk](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide) és [Azure Active Directory-Gyorssablonok](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Az a [az Azure portal](https://ms.portal.azure.com/)válassza **Azure Active Directory** > **alkalmazásregisztrációk** > **új regisztrációs**.

   [![Új alkalmazás regisztrálása az Azure Active Directoryban](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > Az új Azure Active Directory-alkalmazás regisztrációja panel lehetővé teszi, hogy a megjelenített alkalmazások szűrése kiválasztásával **tulajdonában lévő alkalmazások**.

    Az alkalmazás is szerepel a regisztrálást követően.

1. Az alkalmazás adjon meg egy nevet és a válassza **fiókok csak a szervezeti könyvtárban található** , adja meg a **támogatott fióktípusok** az API-t, előfordulhat, hogy hozzáférési. Válasszon egy érvényes URI-t, hogy a felhasználók után a hitelesítéshez, majd **regisztrálása**.

   [![Az Azure Active Directory-alkalmazás létrehozása](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Azure Active Directory-alkalmazás fontos információkat megjelenik a listán szereplő alkalmazások **áttekintése** panelen. Válassza ki az alkalmazást a **tulajdonában lévő alkalmazások**, majd **áttekintése**.

   [![Az Alkalmazásazonosító másolása](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Másolás a **Alkalmazásazonosítót (ügyfél)** ügyfélalkalmazás használata.

1. A **hitelesítési** panel fontos hitelesítési konfigurációs beállításait határozza meg. 

    1. **Átirányítási URI-k** meg kell egyeznie a címet, a hitelesítési kérelem által biztosított:

        * Egy helyi fejlesztési környezetben futtatott alkalmazások esetén válassza **(mobil és asztali) nyilvános ügyfél**. Állítsa be a **ügyféltípus alapértelmezett** Igen értékre.
        * Az Azure App Service-ben üzemeltetett egyoldalas alkalmazások esetén válassza **webes**.

    1. Az implicit engedélyezési folyamat engedélyezése ellenőrzésével **azonosító-jogkivonatokat**.

   [![Hozzon létre egy új titkos ügyfélkulcsot](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Kattintson a **Save** (Mentés) gombra.

1. Az Azure Active Directory-alkalmazás Azure TIme Series Insights társítja. Válassza ki **API-engedélyek** > **adjon hozzá egy engedélyt** > **API-k saját szervezete**. 

    [![Az Azure Active Directory-alkalmazás API-k társítása](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Típus `Azure Time Series Insights` sávon válassza ki a searchbe `Azure Time Series Insights`.

1. Ezután adja meg az adott alkalmazáshoz szükséges API-engedély típusa. Alapértelmezés szerint **delegált engedélyek** kiemelten jelenik meg. Válassza ki a választott egy engedély típusa **engedélyek hozzáadása**.

    [![Adja meg az adott alkalmazáshoz szükséges API-engedély típusa](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)