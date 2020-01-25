---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/23/2020
ms.custom: include file
ms.openlocfilehash: a1576e4a97af5de0b936c662de636aae542a19b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76749011"
---
>[!NOTE]
>Ez a szakasz az [Azure ad-alkalmazás regisztrálására](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)vonatkozó utasításokat tartalmazza.

1. A [Azure Portal](https://portal.azure.com)nyissa meg **Azure Active Directory** a bal oldali menüben, majd nyissa meg a **Alkalmazásregisztrációk** ablaktáblát. 

    [![válassza a Azure Active Directory panelt](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Válassza az **+ új regisztráció** gombot.

    [![válassza az új regisztráció gombot](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Adjon egy rövid nevet az alkalmazás regisztrálásához a **név** mezőben. Az **átirányítási URI (nem kötelező)** szakaszban válassza a bal oldali legördülő menüben a **nyilvános ügyfél/natív (mobil & asztal)** lehetőséget, majd a jobb oldali szövegmezőbe írja be a `https://microsoft.com`. Kattintson a **Register** (Regisztrálás) elemre.

    [![létrehozás panel](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Győződjön meg arról, hogy [az alkalmazás **nyilvános ügyfélként**van regisztrálva](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), nyissa meg az alkalmazás regisztrációjának **hitelesítés** paneljét, majd görgessen le az ablaktáblán. Az **alapértelmezett ügyfél típusa** szakaszban válassza az **Igen** lehetőséget az **alkalmazás nyilvános ügyfélként**való kezeléséhez, és kattintson a **Mentés**gombra.

    1. Az **átirányítási URI-azonosítóknak** meg kell egyezniük a hitelesítési kérelem által megadott címtől:

        * Helyi fejlesztési környezetben üzemeltetett alkalmazásokhoz válassza a **nyilvános ügyfél (mobil & asztali)** lehetőséget. Ügyeljen arra, hogy az **alapértelmezett ügyfél típusát** állítsa Igen értékre.
        * Azure App Serviceon üzemeltetett egylapos alkalmazások esetében válassza a **web**lehetőséget.

        Válassza a **nyilvános ügyfél (mobil & asztali)** lehetőséget, és adja meg `http://localhost:8080/`.

        [![átirányítási URI-k konfigurálása](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    1. A **hozzáférési jogkivonatok** beállításával konfigurálja a **oauth2AllowImplicitFlow** beállítást úgy, hogy `true` az erőforrás **jegyzékfájljának** JSON-fájljában.

        [![nyilvános ügyfél-konfigurációs beállítás](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1.  Nyissa meg a regisztrált alkalmazás **Áttekintés** paneljét, és másolja a következő entitások értékeit egy ideiglenes fájlba. Ezeket az értékeket fogja használni a minta alkalmazás konfigurálásához a következő fejezetekben.

    - **Alkalmazás (ügyfél) azonosítója**
    - **Címtár (bérlő) azonosítója**

    [![Azure Active Directory alkalmazás azonosítója](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Nyissa meg az **API-engedélyek** panelt az alkalmazás regisztrálásához. Válassza **az + engedély hozzáadása** gombot. A **kérelem API-engedélyek** ablaktáblán válassza ki a **saját szervezet által használt API-kat** , majd keressen rá a következők egyikére:
    
    1. `Azure Digital Twins` kérdésre adott válaszban foglalt lépéseket. Válassza ki az **Azure Digital Twins** API-t.

        [![Search API vagy Azure digitális Twins](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Másik lehetőségként keressen rá `Azure Smart Spaces Service`. Válassza ki az **Azure Smart Spaces szolgáltatás** API-ját.

        [![Search API az Azure intelligens Spaces szolgáltatáshoz](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > A megjelenő Azure AD API-név és-azonosító a bérlőtől függ:
    > * A bérlői és az vevői fiókok tesztelésével `Azure Digital Twins`kereshet.
    > * Más Microsoft-fiókoknak `Azure Smart Spaces Service`kell keresniük.

1. Az API-k az **Azure Digital Twins** néven jelennek meg ugyanabban a **kérési API-engedélyek** ablaktáblán, ha ki van választva. Válassza a **READ (olvasás** ) legördülő lehetőséget, majd jelölje be az **olvasás. írás** jelölőnégyzetet. Kattintson az **engedélyek hozzáadása** gombra.

    [![API-engedélyek hozzáadása](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. A szervezet beállításaitól függően előfordulhat, hogy további lépéseket kell tennie ahhoz, hogy rendszergazdai hozzáférést biztosítson ehhez az API-hoz. További információért forduljon a rendszergazdához. A rendszergazdai hozzáférés jóváhagyása után az **API-engedélyek** ablaktábla **rendszergazdai jóváhagyás szükséges** oszlopa megjeleníti az Ön engedélyeit. 

    [![rendszergazdai jóváhagyás](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Ellenőrizze, hogy az **Azure digitális Twins** megjelenik-e.
