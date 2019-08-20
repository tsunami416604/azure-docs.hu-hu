---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: ef6b395aeff18a63f52f58e2477679b48a19b002
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624749"
---
1. A [Azure Portal](https://portal.azure.com)nyissa meg **Azure Active Directory** a bal oldali ablaktáblán, majd nyissa meg a **Tulajdonságok** ablaktáblát. Másolja ki a **Címtár-azonosítót** egy ideiglenes fájlba. Ezt az értéket fogja használni egy minta alkalmazás következő szakaszban való konfigurálásához.

    ![Azure Active Directory Directory-azonosító](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)

1. A [Azure Portal](https://portal.azure.com)nyissa meg **Azure Active Directory** a bal oldali ablaktáblán, majd nyissa meg a **Alkalmazásregisztrációk (örökölt)** ablaktáblát. Válassza az **új alkalmazás regisztrálása** gombot.

1. Adjon egy rövid nevet az alkalmazás regisztrálásához a **név** mezőben. Válassza az **alkalmazás típusa** **natívként**lehetőséget, majd az `https://microsoft.com`átirányítási **URI** -t. Kattintson a **Létrehozás** gombra.

    ![Létrehozás ablaktábla](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)

1.  Nyissa meg a regisztrált alkalmazást, és másolja az **Application ID** mező értékét egy ideiglenes fájlba. Ez az érték azonosítja a Azure Active Directory alkalmazást. Az alkalmazás-azonosító segítségével konfigurálja a minta alkalmazását a következő fejezetekben.

    ![Azure Active Directory alkalmazás azonosítója](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)

1. Nyissa meg az alkalmazás regisztráció ablaktábláját. Válassza a **Beállítások** > **szükséges engedélyek**lehetőséget, majd a következőket:

   a. A bal felső sarokban található **Hozzáadás** gombra kattintva nyissa meg az **API-hozzáférés hozzáadása** panelt.

   b. Válassza **az API kiválasztása** lehetőséget, és keressen az **Azure Digital ikrek**kifejezésre. Ha a keresés nem találja meg az API-t, keressen inkább az **Azure Smart Spaces** kifejezésre.

   c. Válassza ki az **Azure digitális Twins (Azure Smart Spaces szolgáltatás)** lehetőséget, és válassza a **kiválasztás**elemet.

   d. Válassza az **engedélyek kiválasztása**lehetőséget. Jelölje be az **olvasási/írási hozzáférés** delegált engedélyek jelölőnégyzetet, majd válassza a **kiválasztás**lehetőséget.

   e. Válassza a **kész** lehetőséget az **API-hozzáférés hozzáadása** panelen.

   f. A **szükséges engedélyek** ablaktáblán válassza az **engedélyek megadása** gombot, és fogadja el a megjelenő nyugtát. Ha ehhez az API-hoz nem kap engedélyt, forduljon a rendszergazdához.

      ![Szükséges engedélyek panel](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)

 
