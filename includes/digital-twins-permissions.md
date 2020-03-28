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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77029008"
---
>[!NOTE]
>Ez a szakasz az [Azure AD alkalmazás regisztrációjával](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)kapcsolatos utasításokat tartalmazza.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az **Azure Active Directoryt** a kibontható bal oldali menüből, majd nyissa meg az **alkalmazásregisztrációk** ablaktáblát. 

    [![Az Azure Active Directory ablaktábla kijelölése](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Válassza a **+ Új regisztráció** gombot.

    [![Az Új regisztráció gomb kiválasztása](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Adjon rövid nevet az alkalmazás regisztrációjának a **Név** mezőben. 

    1. Az **URI átirányítása (nem kötelező)** csoportban írja be `https://microsoft.com` a szövegmezőbe.     

    1. Ellenőrizze, hogy az Azure Active Directory-alkalmazás mely fiókokat és bérlőket támogatja.

    1. Kattintson a **Register** (Regisztrálás) elemre.

    [![Ablaktábla létrehozása](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. A **Hitelesítés** panel fontos hitelesítési konfigurációs beállításokat ad meg. 

    1. Adja hozzá **az átirányítási URI-kat,** és konfigurálja **a hozzáférési jogkivonatokat** **a + Platform hozzáadása**lehetőség kiválasztásával .

    1. Válassza az **Igen** lehetőséget, ha azt szeretné, hogy az alkalmazás **nyilvános ügyfél.**

    1. Ellenőrizze, hogy az Azure Active Directory-alkalmazás mely fiókokat és bérlőket támogatja.

    [![Nyilvános ügyfél konfigurációs beállítása](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. A megfelelő platform kiválasztása után konfigurálja az **átirányítási URI-kat** és a **hozzáférési jogkivonatokat** a felhasználói felület jobb oldalán lévő oldalsó panelen.

    1. **Az átirányítási URI-knak** meg kell egyezniük a hitelesítési kérelem által megadott címmel:

        * Helyi fejlesztői környezetben üzemeltetett alkalmazások esetén válassza a **Nyilvános ügyfél (mobil & asztali)** lehetőséget. Győződjön meg arról, hogy **a nyilvános ügyfelet** **Igen**beállításra állítja.
        * Az Azure App Service szolgáltatásban üzemeltetett egyoldalas alkalmazások esetén válassza a **Web**lehetőséget.

    1. Határozza meg, hogy a **kijelentkezési URL-cím** megfelelő-e.

    1. Engedélyezze az implicit támogatási **folyamatot az Access-jogkivonatok** vagy **-azonosítójogtok**ellenőrzésével.
                
    [![Átirányítási URI-k konfigurálása](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    Kattintson **a Konfigurálás**gombra, majd a **Mentés gombra.**

1.  Nyissa meg a regisztrált alkalmazás **Áttekintő** ablaktábláját, és másolja a következő entitások értékeit egy ideiglenes fájlba. Ezeket az értékeket fogja használni a mintaalkalmazás konfigurálásához a következő szakaszokban.

    - **Alkalmazás (ügyfél) azonosítója**
    - **Címtár (bérlő) azonosítója**

    [![Az Azure Active Directory alkalmazásazonosítója](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Nyissa meg az **API-engedélyek** ablaktáblát az alkalmazás regisztrációjához. Válassza **a + Engedély hozzáadása** gombot. Az **API-engedélyek kérése** ablaktáblán jelölje ki a **szervezet által használt API-kat,** majd keresse meg az alábbi adatok egyikét:
    
    1. `Azure Digital Twins`. Válassza ki az **Azure Digital Twins** API-t.

        [![KERESÉS API vagy Azure Digital Twins](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Másik lehetőségként `Azure Smart Spaces Service`keressen rá a gombra. Válassza ki az **Azure Smart Spaces service** API-t.

        [![Keresés api for Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Az Azure AD API-név és -azonosító, amely megjelenik, a bérlőtől függ:
    > * A tesztbérlői és `Azure Digital Twins`ügyfélfiókok nak keresniük kell a keresett mezőt.
    > * Más Microsoft-fiókoknak `Azure Smart Spaces Service`is keresniük kell a keresett mezőben.

1. Bármelyik API **azure-beli digitális twins** ként jelenik meg ugyanabban a **Request API-engedélyek** ablaktáblában, miután kiválasztotta. Jelölje be az **Olvasás** legördülő menüt, majd jelölje be az **Olvasás.Írás** jelölőnégyzetet. Válassza az **Engedélyek hozzáadása** gombot.

    [![API-engedélyek hozzáadása](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. A szervezet beállításaitól függően előfordulhat, hogy további lépéseket kell tennie az API-hoz való rendszergazdai hozzáférés engedélyezéséhez. További információért forduljon a rendszergazdához. A rendszergazdai hozzáférés jóváhagyása után az **API-engedélyek** ablaktáblájának **Rendszergazdai hozzájárulás szükséges** oszlopa megjeleníti az engedélyeket. 

    [![Rendszergazdai hozzájárulás jóváhagyása](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Ellenőrizze, hogy **megjelenik-e az Azure Digital Twins.**
