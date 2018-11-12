---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 09/19/2018
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: 1887efd741f4779a5186707d60b27ca66fc3c06f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283983"
---
1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs paneljén nyissa meg az **Azure Active Directory** elemet, majd a **Tulajdonságok** panelt. Másolja ki a **Címtár-azonosítót** egy ideiglenes fájlba. Ezt az értéket a mintaalkalmazás konfigurálásához használhatja majd a következő szakaszban.

    ![Azure Active Directory címtár-azonosító](./media/digital-twins-permissions/aad-app-reg-tenant.png)

1. Nyissa meg az **Alkalmazásregisztrációk** panelt, majd kattintson az **Új alkalmazás regisztrálása** elemre.
    
    ![Azure Active Directory-alkalmazásregisztráció – új](./media/digital-twins-permissions/aad-app-reg-start.png)

1. A **Név** mezőben adjon meg egy rövid nevet az alkalmazás számára. Az **Alkalmazás típusa** mezőben válassza a **_Natív_**, az **Átirányítási URI** mezőben pedig a **_https://microsoft.com_** értéket. Kattintson a **Create** (Létrehozás) gombra.

    ![Azure Active Directory-alkalmazásregisztráció – létrehozás](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Nyissa meg a regisztrált alkalmazást, és másolja az **Alkalmazásazonosító** mező értékét egy ideiglenes fájlba – ez az érték azonosítja az Azure Active Directory-alkalmazást. Az Alkalmazásazonosítót a mintaalkalmazás konfigurálásához használhatja majd a következő szakaszokban.

    ![Azure Active Directory-alkalmazásazonosító](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Nyiss meg az alkalmazásregisztráció paneljét, és kattintson a **Beállítások** > **Szükséges engedélyek** elemre:
    - Kattintson a bal oldalon felül a **Hozzáadás** elemre az **API-hozzáférés hozzáadása** panel megnyitásához.
    - Kattintson az **API kiválasztása** lehetőségre, és keressen az **Azure Digital Twins** kifejezésre. Ha a keresés nem találja meg az API-t, keressen inkább az **Azure Smart Spaces** kifejezésre.
    - Válassza az **Azure Digital Twins (Azure Smart Spaces Service)** lehetőséget, és kattintson a **Kiválasztás** gombra.
    - Kattintson az **Engedélyek kiválasztása** elemre. Jelölje be az **Olvasási/írási hozzáférés** delegált engedélyek mezőt, és kattintson a **Kiválasztás** gombra.
    - Kattintson a **Kész** gombra az **API-hozzáférés hozzáadása** panelen.
    - A **Szükséges engedélyek** panelen kattintson az **Engedélyek megadása** gombra, és fogadja el a megjelenő megerősítő üzenetet.

       ![Azure Active Directory-alkalmazásregisztráció – API hozzáadása](./media/digital-twins-permissions/aad-app-req-permissions.png)
