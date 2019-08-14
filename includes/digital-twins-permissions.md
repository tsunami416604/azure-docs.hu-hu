---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: fc2e2fc05de66de6f428e6b8ca7c94f82003ba2a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012152"
---
>[!NOTE]
>Ez a szakasz az [új Azure ad-alkalmazás regisztrálására](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)vonatkozó utasításokat tartalmazza. Ha továbbra is örökölt natív alkalmazás-regisztrációt használ, akkor azt használhatja, ha ez támogatott. Emellett, ha valamilyen oknál fogva az alkalmazás-regisztráció új módja nem működik a telepítőben, megpróbálhat létrehozni egy örökölt natív HRE alkalmazást. További útmutatásért olvassa el az [Azure Digital Twins-alkalmazás regisztrálása Azure Active Directory örökölt](../articles/digital-twins/how-to-use-legacy-aad.md) című témakört. 

1. A [Azure Portal](https://portal.azure.com)nyissa meg **Azure Active Directory** a bal oldali ablaktáblán, majd nyissa meg a **Alkalmazásregisztrációk** ablaktáblát. Válassza az **új regisztráció** gombot.

    ![Alkalmazás regisztrálva](./media/digital-twins-permissions/aad-app-register.png)

1. Adjon egy rövid nevet az alkalmazás regisztrálásához a **név** mezőben. Az **átirányítási URI (nem kötelező)** szakaszban válassza a **nyilvános ügyfél (mobil & asztal)** lehetőséget a bal oldali legördülő menüben, majd a `https://microsoft.com` jobb oldalon adja meg a szövegmezőt. Kattintson a **Register** (Regisztrálás) elemre.

    ![Létrehozás ablaktábla](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Győződjön meg arról, hogy [az alkalmazás *natív alkalmazásként*van regisztrálva](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), nyissa meg az alkalmazás regisztrációjának **hitelesítés** paneljét, és görgessen le az ablaktáblán. Az **alapértelmezett ügyfél típusa** szakaszban válassza az **Igen** lehetőséget az **alkalmazás nyilvános ügyfélként való kezelésére**. 

    ![Alapértelmezett natív](./media/digital-twins-permissions/aad-app-default-native.png)

1.  Nyissa meg a regisztrált alkalmazás **Áttekintés** paneljét, és másolja az alábbi entitások értékeit egy ideiglenes fájlba. Ezeket az értékeket fogja használni a minta alkalmazás konfigurálásához a következő fejezetekben.

    - **Alkalmazás (ügyfél) azonosítója**
    - **Címtár (bérlő) azonosítója**

    ![Azure Active Directory alkalmazás azonosítója](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Nyissa meg az **API-engedélyek** panelt az alkalmazás regisztrálásához. Válassza **az engedély hozzáadása** gombot. A **kérelem API-engedélyek** ablaktáblán válassza a **saját szervezet által használt API** -k fület, majd keresse meg a következőt:
    
    1. **Azure digitális Twins**. Válassza ki az **Azure Digital Twins** API-t.

        ![Keresés az API-ban vagy az Azure Digital Twins-ban](./media/digital-twins-permissions/aad-aap-search-api-dt.png)

    1. Alternatív megoldásként keressen az **Azure intelligens tárhelyek**kifejezésre. Válassza ki az **Azure Smart Spaces szolgáltatás** API-ját.

        ![Azure intelligens Spaces-API keresése](./media/digital-twins-permissions/aad-app-search-api.png)

    > [!NOTE]
    > A keresés során megjelenő pontos név attól függően változhat, hogy melyik Azure-bérlőhöz tartozik.

1. A kiválasztott API az **Azure Digital Twins** néven jelenik meg ugyanabban a **kérelem API-engedélyek** ablaktáblán. Válassza az **olvasás (1)** legördülő listát, majd válassza az **olvasás. írás** jelölőnégyzetet. Kattintson az **engedélyek hozzáadása** gombra.

    ![API-engedélyek hozzáadása](./media/digital-twins-permissions/aad-app-req-permissions.png)

1. A szervezet beállításaitól függően előfordulhat, hogy további lépéseket kell tennie ahhoz, hogy rendszergazdai hozzáférést biztosítson ehhez az API-hoz. További információért forduljon a rendszergazdához. A rendszergazdai hozzáférés jóváhagyása után az **API-engedélyek** ablaktáblán a **rendszergazdai jóváhagyás szükséges** oszlop az API-khoz hasonlóan fog megjelenni:

    ![API-engedélyek hozzáadása](./media/digital-twins-permissions/aad-app-admin-consent.png)

