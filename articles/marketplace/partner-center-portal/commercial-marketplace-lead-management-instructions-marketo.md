---
title: Az érdeklődők felügyeletének konfigurálása a Marketo-ben | Azure piactér
description: Az Azure Marketplace-ügyfelek Marketo vezető felügyeletének konfigurálása.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 8d13e8c3aeabf6d3fdea80ffddbae47b80adc139
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812139"
---
# <a name="configure-lead-management-in-marketo"></a>Az érdeklődők felügyeletének konfigurálása a Marketo-ben

Ez a cikk azt ismerteti, hogyan állíthat be Marketo CRM-rendszert a Piactéri ajánlatból származó értékesítési érdeklődők feldolgozásához.

## <a name="set-up-your-marketo-crm-system"></a>A Marketo CRM rendszer beállítása

1. Jelentkezzen be a Marketo.
2. Válassza a **Design Studio**elemet.
    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Válassza az **új űrlap**lehetőséget.
    ![Marketo új űrlap](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Töltse ki a kötelező mezőket az új űrlapon, majd válassza a **Létrehozás**lehetőséget.
    ![Marketo új űrlap létrehozása](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  A mező részletei lapon válassza a **Befejezés**lehetőséget.
    ![Marketo befejezési formája](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Jóváhagyás és bezárás.

7. A *MarketplaceLeadBacked* lapon válassza a **beágyazási kód**lehetőséget. 

    ![Beágyazási kód](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. A Marketo beágyazási kód az alábbi példához hasonló kódot jelenít meg.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Másolja ki a beágyazási kód űrlapon látható alábbi mezők értékeit. Ezeknek az értékeknek a használatával konfigurálhatja, hogy az ajánlat a következő lépésben fogadja az érdeklődőket. Használja a következő példát útmutatóként a szükséges azonosítók beolvasásához a Marketo beágyazási kódjának példája alapján.

    - Kiszolgáló azonosítója = **ys12**
    - Munchkin-azonosító = **123-PQR-789**
    - Űrlap azonosítója = **1179**

    **Ezen értékek megállapításának másik módja**

    - A kiszolgáló-azonosító a Marketo-példány URL-címében található, például: "`serverID.marketo.com`".
    - Az előfizetés csámcsogó AZONOSÍTÓjának beszerzéséhez nyissa meg a rendszergazda > Munchkin menüt a "Munchkin-fiók azonosítója" mezőben, vagy a Marketo első részében REST API gazdagép altartománya: `https://{Munchkin ID}.mktorest.com`.
    - Az űrlap-azonosító a 7. lépésben létrehozott beágyazási kód űrlap azonosítója, amely az érdeklődőket a piactérről irányítja át.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Az ajánlat beállítása, hogy érdeklődőket küldjön a Marketo

Ha készen áll az ajánlathoz tartozó érdeklődői felügyeleti információk konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket: 

1. Navigáljon az ajánlat **telepítési** lapjára.
1. Válassza a **kapcsolat** lehetőséget az érdeklődő felügyelete szakaszban. 

    ![Vezető felügyelet – kapcsolat](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. A kapcsolat részletei előugró ablakban válassza a **Marketo** lehetőséget az érdeklődő célhelyéhez.

    ![Érdeklődő célhelyének kiválasztása](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Adja meg a **kiszolgáló azonosítóját**, a **csámcsogó fiók azonosítóját**és az **űrlap azonosítóját**.

    >[!Note]
    >Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt. 

