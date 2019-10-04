---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 880c2a811df7b4d41760676d4d6e0153e9384f38
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949759"
---
>[!NOTE]
>Ez a szakasz az [új Azure ad-alkalmazás regisztrálására](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)vonatkozó utasításokat tartalmazza. Ha továbbra is örökölt natív alkalmazás-regisztrációt használ, akkor azt használhatja, ha ez támogatott. Emellett, ha valamilyen oknál fogva az alkalmazás-regisztráció új módja nem működik a telepítőben, megpróbálhat létrehozni egy örökölt natív HRE alkalmazást. További útmutatásért olvassa el az [Azure Digital Twins-alkalmazás regisztrálása Azure Active Directory örökölt](../articles/digital-twins/how-to-use-legacy-aad.md) című témakört. 

1. A [Azure Portal](https://portal.azure.com)nyissa meg **Azure Active Directory** a bal oldali ablaktáblán, majd nyissa meg a **Alkalmazásregisztrációk** ablaktáblát. Válassza az **új regisztráció** gombot.

    [@no__t – 1App regisztrálva](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Adjon egy rövid nevet az alkalmazás regisztrálásához a **név** mezőben. Az **átirányítási URI (nem kötelező)** szakaszban válassza a **nyilvános ügyfél (mobil & asztal)** lehetőséget a bal oldali legördülő menüben, majd írja be a `https://microsoft.com` értéket a jobb oldali szövegmezőbe. Kattintson a **Register** (Regisztrálás) elemre.

    [@no__t – 1Create panel](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Győződjön meg arról, hogy [az alkalmazás *natív alkalmazásként*van regisztrálva](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), nyissa meg az alkalmazás regisztrációjának **hitelesítés** paneljét, és görgessen le az ablaktáblán. Az **alapértelmezett ügyfél típusa** szakaszban válassza az **Igen** lehetőséget az **alkalmazás nyilvános ügyfélként való kezelésére**. 

    [@no__t – natív 1Default](./media/digital-twins-permissions/aad-app-default-native.png)](./media/digital-twins-permissions/aad-app-default-native.png#lightbox)

1.  Nyissa meg a regisztrált alkalmazás **Áttekintés** paneljét, és másolja az alábbi entitások értékeit egy ideiglenes fájlba. Ezeket az értékeket fogja használni a minta alkalmazás konfigurálásához a következő fejezetekben.

    - **Alkalmazás (ügyfél) azonosítója**
    - **Címtár (bérlő) azonosítója**

    [@no__t – 1Azure Active Directory alkalmazás azonosítója](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Nyissa meg az **API-engedélyek** panelt az alkalmazás regisztrálásához. Válassza **az engedély hozzáadása** gombot. A **kérelem API-engedélyek** ablaktáblán válassza a **saját szervezet által használt API** -k fület, majd keresse meg a következőt:
    
    1. `Azure Digital Twins`. Válassza ki az **Azure Digital Twins** API-t.

        [![Search API vagy Azure digitális Twins](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Vagy keressen rá a `Azure Smart Spaces Service` kifejezésre. Válassza ki az **Azure Smart Spaces szolgáltatás** API-ját.

        [@no__t – 1Search API az Azure intelligens tárhelyekhez](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > A megjelenő Azure AD API-név és-azonosító a bérlőtől függ:
    > * A bérlői és az ügyfél-fiókok teszteléséhez `Azure Digital Twins` értéket kell keresni.
    > * Más Microsoft-fiókoknak a `Azure Smart Spaces Service` kifejezésre kell keresniük.

1. A kiválasztott API az **Azure Digital Twins** néven jelenik meg ugyanabban a **kérelem API-engedélyek** ablaktáblán. Válassza az **olvasás (1)** legördülő listát, majd válassza az **olvasás. írás** jelölőnégyzetet. Kattintson az **engedélyek hozzáadása** gombra.

    [![API-engedélyek hozzáadása](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. A szervezet beállításaitól függően előfordulhat, hogy további lépéseket kell tennie ahhoz, hogy rendszergazdai hozzáférést biztosítson ehhez az API-hoz. További információért forduljon a rendszergazdához. A rendszergazdai hozzáférés jóváhagyása után az **API-engedélyek** ablaktáblán a **rendszergazdai jóváhagyás szükséges** oszlop az API-khoz hasonlóan fog megjelenni:

    [![API-engedélyek hozzáadása](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

