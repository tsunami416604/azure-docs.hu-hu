---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 11/13/2018
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: 216e2db82d5a07bd8e4cae8b9f357ac7dcee330a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626398"
---
1. Az a [az Azure portal](https://portal.azure.com), nyissa meg **Azure Active Directory** a bal oldali ablaktáblán, és ezután nyissa meg a **tulajdonságok** ablaktáblán. Másolja ki a **Címtár-azonosítót** egy ideiglenes fájlba. A mintaalkalmazás konfigurálását a következő szakaszban ezt az értéket fogja használni.

    ![Az Azure Active Directory címtár-azonosító](./media/digital-twins-permissions/aad-app-reg-tenant.png)

1. Nyissa meg a **alkalmazásregisztrációk** ablaktáblán, és válassza ki a **új alkalmazásregisztráció** gombra.

    ![Alkalmazás regisztrációk panel](./media/digital-twins-permissions/aad-app-reg-start.png)

1. Adjon egy rövid nevet a az alkalmazás regisztrációját a **neve** mezőbe. Válasszon **alkalmazástípus** , **natív**, és **átirányítási URI-t** , `https://microsoft.com`. Kattintson a **Létrehozás** gombra.

    ![Hozzon létre panel](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Nyissa meg a regisztrált alkalmazást, és másolja az értékét a **Alkalmazásazonosító** mezőt egy ideiglenes fájlba. Ez az érték azonosítja az Azure Active Directory-alkalmazást. A mintaalkalmazás konfigurálását az alábbi szakaszok az Alkalmazásazonosítót fogja használni.

    ![Az Azure Active Directory-alkalmazás azonosítója](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Az alkalmazás regisztrációs panel megnyitásához. Válassza ki **beállítások** > **szükséges engedélyek**, majd:

   a. Válassza ki **Hozzáadás** megnyitásához a bal felső a **API-hozzáférés hozzáadása** ablaktáblán.

   b. Válassza ki **API kiválasztása** és keressen rá a **Azure digitális Twins**. Ha a keresés nem találja meg az API-t, keressen inkább az **Azure Smart Spaces** kifejezésre.

   c. Válassza ki a **Azure digitális Twins (Azure intelligens tárolóhelyek szolgáltatása)** lehetőséget, majd válassza a **kiválasztása**.

   d. Válasszon **engedélyek kiválasztása**. Válassza ki a **olvasási/írási hozzáférést** delegált engedélyeket jelölőnégyzetet, majd válassza a **kiválasztása**.

   e. Válassza ki **kész** a a **API-hozzáférés hozzáadása** ablaktáblán.

   f. Az a **szükséges engedélyek** panelen válassza a **engedélyeket** gombra, és fogadja el a visszaigazolás, amely akkor jelenik meg.

      ![Szükséges engedélyek panelen](./media/digital-twins-permissions/aad-app-req-permissions.png)
