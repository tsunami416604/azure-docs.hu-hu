---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
ms.custom: include file
ms.openlocfilehash: cfe3eb4c0ac1378b7c519b3b34094945612d8508
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029008"
---
>[!NOTE]
>Ez a szakasz az [Azure ad-alkalmazás regisztrálására](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)vonatkozó utasításokat tartalmazza.

1. A [Azure Portal](https://portal.azure.com)nyissa meg **Azure Active Directory** a bal oldali menüben, majd nyissa meg a **Alkalmazásregisztrációk** ablaktáblát. 

    [![válassza a Azure Active Directory panelt](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Válassza az **+ új regisztráció** gombot.

    [![válassza az új regisztráció gombot](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Adjon egy rövid nevet az alkalmazás regisztrálásához a **név** mezőben. 

    1. Az **átirányítási URI (nem kötelező)** szakaszban adja meg `https://microsoft.com` a szövegmezőben.     

    1. Ellenőrizze, hogy a Azure Active Directory-alkalmazás támogatja-e a fiókokat és a bérlőket.

    1. Kattintson a **Register** (Regisztrálás) elemre.

    [![létrehozás panel](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. A **hitelesítés** panelen a fontos hitelesítési konfigurációs beállítások adhatók meg. 

    1. Adja hozzá az **átirányítási URI-ket** , és konfigurálja a **hozzáférési jogkivonatokat** a **+ platform hozzáadása**lehetőség kiválasztásával.

    1. Válassza az **Igen** lehetőséget annak megadásához, hogy az alkalmazás **nyilvános ügyfél**legyen.

    1. Ellenőrizze, hogy a Azure Active Directory-alkalmazás támogatja-e a fiókokat és a bérlőket.

    [![nyilvános ügyfél-konfigurációs beállítás](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. Miután kiválasztotta a megfelelő platformot, konfigurálja az **átirányítási URI-ket** és a **hozzáférési jogkivonatokat** a felhasználói felület jobb oldalán lévő oldalsó panelen.

    1. Az **átirányítási URI-azonosítóknak** meg kell egyezniük a hitelesítési kérelem által megadott címtől:

        * Helyi fejlesztési környezetben üzemeltetett alkalmazásokhoz válassza a **nyilvános ügyfél (mobil & asztali)** lehetőséget. Ügyeljen arra, hogy a **nyilvános ügyfelet** állítsa **Igen**értékre.
        * Azure App Serviceon üzemeltetett egylapos alkalmazások esetében válassza a **web**lehetőséget.

    1. Döntse el, hogy megfelelő-e a **kijelentkezési URL-cím** .

    1. Engedélyezze az implicit engedélyezési folyamatot a **hozzáférési jogkivonatok** vagy **azonosító tokenek**ellenőrzésével.
                
    [![átirányítási URI-k konfigurálása](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    Kattintson a **Konfigurálás**, majd a **Mentés**elemre.

1.  Nyissa meg a regisztrált alkalmazás **Áttekintés** paneljét, és másolja a következő entitások értékeit egy ideiglenes fájlba. Ezeket az értékeket fogja használni a minta alkalmazás konfigurálásához a következő fejezetekben.

    - **Alkalmazás (ügyfél) azonosítója**
    - **Címtár (bérlő) azonosítója**

    [![Azure Active Directory alkalmazás azonosítója](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Nyissa meg az **API-engedélyek** panelt az alkalmazás regisztrálásához. Válassza **az + engedély hozzáadása** gombot. A **kérelem API-engedélyek** ablaktáblán válassza ki a **saját szervezet által használt API-kat** , majd keressen rá a következők egyikére:
    
    1. `Azure Digital Twins`. Válassza ki az **Azure Digital Twins** API-t.

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
