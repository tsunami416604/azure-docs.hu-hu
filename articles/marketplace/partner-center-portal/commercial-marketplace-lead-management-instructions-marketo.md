---
title: Vezetőkezelés konfigurálása a Marketo-ban | Azure Piactér
description: Konfigurálja a Marketo vezető felügyeletét az Azure piactéri ügyfelek számára.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: 35d57d117f6308863965ffd789c0e28bedd0f301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281510"
---
# <a name="configure-lead-management-in-marketo"></a>Érdeklődőkezelés konfigurálása a Marketo-ban

Ez a cikk bemutatja, hogyan állíthatja be a Marketo CRM rendszerét a piactéri ajánlatból származó értékesítési érdeklődők feldolgozásához.

## <a name="set-up-your-marketo-crm-system"></a>A Marketo CRM rendszer beállítása

1. Jelentkezzen be a Marketo-ba.
2. Válassza **a Design Studio**lehetőséget.
    ![Marketo Design Stúdió](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Válassza az **Új űrlap lehetőséget.**
    ![Marketo új forma](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Töltse ki a szükséges mezőket az Új űrlapon, majd válassza a **Létrehozás gombot.**
    ![A Marketo új űrlapot hoz létre](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  A Mező részletei területen válassza a **Befejezés**lehetőséget.
    ![Marketo befejezési forma](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Jóváhagyás és bezárás.

7. A *MarketplaceLeadBacked* lapon válassza a **Kód beágyazása**lehetőséget. 

    ![Beágyazási kód](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. A Marketo embed kódja a következő példához hasonló kódot jelenít meg.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Másolja a másoláshoz a Beágyazási kód képernyőn látható alábbi mezők értékeit. Ezeket az értékeket fogja használni az érdeklődők fogadására vonatkozó ajánlat konfigurálásához a következő lépésben. Használja a következő példát útmutatóként a Marketo embed kód példából szükséges azonosítók megszerzéséhez.

    - Kiszolgálóazonosító = **ys12**
    - Munchkin ID = **123-PQR-789**
    - Űrlap azonosítója = **1179**

    **Egy másik módja annak, hogy kitaláljuk, ezeket az értékeket**

    - A kiszolgálóazonosító a Marketo-példány URL-címében található, például "`serverID.marketo.com`".
    - Szerezd meg az előfizetés csámcsogó azonosítóját a "Munchkin fiókazonosító" mezőadmin>Munchkin menüjében, vagy a `https://{Munchkin ID}.mktorest.com`Marketo REST API-gazdaaldomainelső részéből: .
    - Az űrlapazonosító a 7.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Konfigurálja az ajánlatot, hogy érdeklődőket küldjön a Marketo-nak

Ha készen áll az ajánlat érdeklődőkezelési adatainak konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket: 

1. Nyissa meg az **ajánlat beállítási** oldalát.
1. Válassza a **Csatlakozás** lehetőséget az Érdeklődőkezelés szakaszban. 

    ![Érdeklődőkezelés - Csatlakozás](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. A Kapcsolat részletei előugró ablakban válassza a **Marketo** lehetőséget az érdeklődő célhelyéhez.

    ![Érdeklődő célhelyének kiválasztása](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Adja meg a **kiszolgálóazonosítót**, **a csámcsogó fiókazonosítót**és **az űrlapazonosítót.**

    >[!Note]
    >Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie, mielőtt érdeklődőket kapna az ajánlathoz. 

