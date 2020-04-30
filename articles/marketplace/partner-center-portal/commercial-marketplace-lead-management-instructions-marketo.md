---
title: Az érdeklődők felügyeletének konfigurálása a Marketo-ben | Azure piactér
description: Az Azure Marketplace-ügyfelek Marketo vezető felügyeletének konfigurálása.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: b142e0ab1aaa242157e207ceecf958be51bb1721
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133657"
---
# <a name="configure-lead-management-in-marketo"></a>Az érdeklődők felügyeletének konfigurálása a Marketo-ben

Ez a cikk azt ismerteti, hogyan állíthat be Marketo CRM-rendszert a kereskedelmi Piactéri ajánlatból származó értékesítési érdeklődők feldolgozásához.

## <a name="set-up-your-marketo-crm-system"></a>A Marketo CRM rendszer beállítása

1. Jelentkezzen be a Marketo.

1. Válassza a **Design Studio**elemet.

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Válassza az **új űrlap**lehetőséget.

    ![Új Marketo űrlap](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Töltse ki a kötelező mezőket az **új űrlap** párbeszédpanelen, majd válassza a **Létrehozás**lehetőséget.

    ![Új űrlap létrehozása a Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  A **mező részletei** lapon válassza a **Befejezés**lehetőséget.

    ![Marketo befejezési űrlapja](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Jóváhagyás és bezárás.

1. A **MarketplaceLeadBackend** lapon válassza a **beágyazási kód**lehetőséget. 

    ![Marketo beágyazási kód](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. A Marketo beágyazási kód az alábbi példához hasonló kódot jelenít meg.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Másolja a beágyazási kód űrlapon látható következő mezők értékeit. Ezeknek az értékeknek a használatával konfigurálhatja, hogy az ajánlat a következő lépésben fogadja az érdeklődőket. Használja a következő példát útmutatóként a szükséges azonosítók beolvasásához a Marketo beágyazási kódjának példája alapján.

    - Kiszolgáló azonosítója = **ys12**
    - Munchkin-azonosító = **123-PQR-789**
    - Űrlap azonosítója = **1179**

    A következő értékek kiszűrésének másik módja:

    - A kiszolgáló-azonosító a Marketo-példány URL-címében található, például `serverID.marketo.com`:.
    - Az előfizetés Munchkin-azonosítójának beszerzéséhez nyissa meg a **rendszergazda** > **Munchkin** menüt a **Munchkin-fiók azonosítója** mezőben, vagy a Marketo első részén REST API gazdagép altartománya: `https://{Munchkin ID}.mktorest.com`.
    - Az űrlap-azonosító a 7. lépésben létrehozott beágyazási kód űrlap azonosítója, amely az érdeklődőket a piactérről irányítja át.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Az ajánlat beállítása, hogy érdeklődőket küldjön a Marketo

Ha készen áll az ajánlathoz tartozó érdeklődő-felügyeleti információk konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket. 

1. Nyissa meg az ajánlat **telepítési** lapját.

1. Válassza a **kapcsolat** lehetőséget az **érdeklődő felügyelete** szakaszban. 

    ![Érdeklődői felügyelet szakasz csatlakozási gombja](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. A **kapcsolat részletei** előugró ablakban válassza a **Marketo** lehetőséget az **érdeklődő célhelyéhez**.

    ![Érdeklődő célhelyének kiválasztása](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Adja meg a **kiszolgáló azonosítóját**, a **Munchkin**és az **űrlap azonosítóját**.

    > [!NOTE]
    > Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt. 

1. A **kapcsolattartási e-mail**cím alatt adja meg a vállalat azon tagjainak e-mail-címeit, akiknek új érdeklődő fogadása esetén e-mail-értesítéseket kell kapniuk. A pontosvesszővel elválasztva több e-mail-címet is megadhat.

1. Kattintson az **OK** gombra.

   Az **Érvényesítés** gombra kattintva győződjön meg arról, hogy sikeresen csatlakozott egy érdeklődői célhoz. Ha a művelet sikeres, a vezető célhelyen egy teszt vezet.

   ![Kapcsolat részletei előugró ablak](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
