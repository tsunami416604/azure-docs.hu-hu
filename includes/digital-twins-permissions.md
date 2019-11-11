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
ms.openlocfilehash: 3ad4a3391a8d0174a0a031920f40a47464753767
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903940"
---
>[!NOTE]
>Ez a szakasz az [új Azure ad-alkalmazás regisztrálására](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)vonatkozó utasításokat tartalmazza. Ha továbbra is örökölt natív alkalmazás-regisztrációt használ, akkor azt használhatja, ha ez támogatott. Emellett, ha valamilyen oknál fogva az alkalmazás-regisztráció új módja nem működik a telepítőben, megpróbálhat létrehozni egy örökölt natív HRE alkalmazást. További útmutatásért olvassa el az [Azure Digital Twins-alkalmazás regisztrálása Azure Active Directory örökölt](../articles/digital-twins/how-to-use-legacy-aad.md) című témakört. 

1. A [Azure Portal](https://portal.azure.com)nyissa meg **Azure Active Directory** a bal oldali menüben, majd nyissa meg a **Alkalmazásregisztrációk** ablaktáblát. Válassza az **új regisztráció** gombot.

    [![alkalmazás regisztrálva](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Adjon egy rövid nevet az alkalmazás regisztrálásához a **név** mezőben. Az **átirányítási URI (nem kötelező)** szakaszban válassza a bal oldali legördülő menüben a **nyilvános ügyfél/natív (mobil & asztal)** lehetőséget, majd a jobb oldali szövegmezőbe írja be a `https://microsoft.com`. Kattintson a **Register** (Regisztrálás) elemre.

    [![létrehozás panel](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Győződjön meg arról, hogy [az alkalmazás *natív alkalmazásként*van regisztrálva](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), nyissa meg az alkalmazás regisztrációjának **hitelesítés** paneljét, és görgessen le az ablaktáblán. Az **alapértelmezett ügyfél típusa** szakaszban válassza az **Igen** lehetőséget az **alkalmazás nyilvános ügyfélként**való kezeléséhez, és kattintson a **Mentés**gombra.

    [Alapértelmezett natív ![](./media/digital-twins-permissions/aad-app-default-native.png)](./media/digital-twins-permissions/aad-app-default-native.png#lightbox)

1.  Nyissa meg a regisztrált alkalmazás **Áttekintés** paneljét, és másolja a következő entitások értékeit egy ideiglenes fájlba. Ezeket az értékeket fogja használni a minta alkalmazás konfigurálásához a következő fejezetekben.

    - **Alkalmazás (ügyfél) azonosítója**
    - **Címtár (bérlő) azonosítója**

    [![Azure Active Directory alkalmazás azonosítója](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Nyissa meg az **API-engedélyek** panelt az alkalmazás regisztrálásához. Válassza **az engedély hozzáadása** gombot. A **kérelem API-engedélyek** ablaktáblán válassza ki a **saját szervezet által használt API-kat** , majd keressen rá a következők egyikére:
    
    1. `Azure Digital Twins`. Válassza ki az **Azure Digital Twins** API-t.

        [![Search API vagy Azure digitális Twins](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Másik lehetőségként keressen rá `Azure Smart Spaces Service`. Válassza ki az **Azure Smart Spaces szolgáltatás** API-ját.

        [![Search API az Azure intelligens Spaces szolgáltatáshoz](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > A megjelenő Azure AD API-név és-azonosító a bérlőtől függ:
    > * A bérlői és az vevői fiókok tesztelésével `Azure Digital Twins`kereshet.
    > * Más Microsoft-fiókoknak `Azure Smart Spaces Service`kell keresniük.

1. A kiválasztott API-k az **Azure digitális Twins** szolgáltatásként jelennek meg ugyanabban a **kérési API-engedélyek** ablaktáblán. Válassza a **READ (olvasás** ) legördülő lehetőséget, majd jelölje be az **olvasás. írás** jelölőnégyzetet. Kattintson az **engedélyek hozzáadása** gombra.

    [![API-engedélyek hozzáadása](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. A szervezet beállításaitól függően előfordulhat, hogy további lépéseket kell tennie ahhoz, hogy rendszergazdai hozzáférést biztosítson ehhez az API-hoz. További információért forduljon a rendszergazdához. A rendszergazdai hozzáférés jóváhagyása után az **API-engedélyek** ablaktábla **rendszergazdai jóváhagyás szükséges** oszlopa a következőhöz hasonlóan fog kinézni az API-khoz:

    [![API-engedélyek hozzáadása](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

