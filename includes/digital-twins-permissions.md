---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 12/17/2018
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: e8027e16cc1f58fbadbb35ae241ab29010005586
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53584744"
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
