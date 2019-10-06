---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 10/03/2019
ms.custom: include file
ms.openlocfilehash: 7e0396c032a9f3dc26b82648604624446d6ad191
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978629"
---
## <a name="azure-active-directory-legacy-registration"></a>Azure Active Directory örökölt regisztráció

1. A [Azure Portal](https://portal.azure.com)nyissa meg **Azure Active Directory** a bal oldali ablaktáblán, majd nyissa meg a **Tulajdonságok** ablaktáblát. Másolja ki a **Címtár-azonosítót** egy ideiglenes fájlba. Ezt az értéket fogja használni egy minta alkalmazás következő szakaszban való konfigurálásához.

    [@no__t – 1Azure Active Directory Directory-azonosító](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png#lightbox)

1. A [Azure Portal](https://portal.azure.com)nyissa meg **Azure Active Directory** a bal oldali ablaktáblán, majd nyissa meg a **Alkalmazásregisztrációk (örökölt)** ablaktáblát. Válassza az **új alkalmazás regisztrálása** gombot.

1. Adjon egy rövid nevet az alkalmazás regisztrálásához a **név** mezőben. Válassza az **alkalmazás típusa** **natívként**lehetőséget, majd **átirányítási URI** -t `https://microsoft.com`-ként. Kattintson a **Létrehozás** gombra.

    [@no__t – 1Create panel](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)](./media/digital-twins-permissions-legacy/aad-app-reg-create.png#lightbox)

1.  Nyissa meg a regisztrált alkalmazást, és másolja az **Application ID** mező értékét egy ideiglenes fájlba. Ez az érték azonosítja a Azure Active Directory alkalmazást. Az alkalmazás-azonosító segítségével konfigurálja a minta alkalmazását a következő fejezetekben.

    [@no__t – 1Azure Active Directory alkalmazás azonosítója](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png#lightbox)

1. Nyissa meg az alkalmazás regisztráció ablaktábláját. Válassza a **beállítások** > **szükséges engedélyek**elemet, majd a következőket:

   a. A bal felső sarokban található **Hozzáadás** gombra kattintva nyissa meg az **API-hozzáférés hozzáadása** panelt.

   b. Válassza **az API kiválasztása** lehetőséget, és keressen az **Azure Digital ikrek**kifejezésre. Ha a keresés nem találja meg az API-t, keressen inkább az **Azure Smart Spaces** kifejezésre.

   c. Válassza ki az **Azure digitális Twins (Azure Smart Spaces szolgáltatás)** lehetőséget, és válassza a **kiválasztás**elemet.

   d. Válassza az **engedélyek kiválasztása**lehetőséget. Jelölje be az **olvasási/írási hozzáférés** delegált engedélyek jelölőnégyzetet, majd válassza a **kiválasztás**lehetőséget.

   e. Válassza a **kész** lehetőséget az **API-hozzáférés hozzáadása** panelen.

   f. A **szükséges engedélyek** ablaktáblán válassza az **engedélyek megadása** gombot, és fogadja el a megjelenő nyugtát. Ha ehhez az API-hoz nem kap engedélyt, forduljon a rendszergazdához.

      [@no__t – 1Required engedélyek panel](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png#lightbox)

 
