---
title: A Salesforce vezető felügyeletének konfigurálása | Azure piactér
description: Az Azure Marketplace-ügyfelek Salesforce az érdeklődők felügyeletének konfigurálása.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: e0fbb09370e198772b4fc485b3c0fe8a56da4226
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133637"
---
# <a name="configure-lead-management-for-salesforce"></a>A Salesforce vezető felügyeletének konfigurálása

Ez a cikk azt ismerteti, hogyan állíthatja be a Salesforce rendszerét, hogy feldolgozza a kereskedelmi Marketplace-ajánlatból származó értékesítési érdeklődőket.

> [!NOTE]
> Az Azure Marketplace nem támogatja az előre feltöltött listákat, például az **ország** mező értékeinek listáját. A folytatás előtt győződjön meg arról, hogy nincsenek beállítva felsorolások. Másik lehetőségként beállíthat egy [https-végpontot](./commercial-marketplace-lead-management-instructions-https.md) vagy egy [Azure-táblázatot](./commercial-marketplace-lead-management-instructions-azure-table.md) az érdeklődők fogadására.

## <a name="set-up-your-salesforce-system"></a>A Salesforce-rendszerek beállítása

1. Jelentkezzen be a Salesforce.
1. Ha a Salesforce világítási élményt használja:
    1. Válassza a **telepítő** elemet a Salesforce kezdőlapján.

       ![Salesforce-telepítő](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. A **telepítés** lapon válassza a **platform eszközök** > **szolgáltatás beállítások** > **marketing** > **web – vezető**lehetőséget.

        ![Salesforce web – Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

1. Ha a klasszikus Salesforce-élményt használja:

    1. Válassza a **telepítő** elemet a Salesforce kezdőlapján.

       ![Klasszikus Salesforce-telepítő](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. A **telepítés** lapon válassza a **Létrehozás** > **Testreszabás** > webes**érdeklődők** > **számára**lehetőséget.

        ![Salesforce klasszikus webes – vezető](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

A többi utasítás ugyanaz, függetlenül attól, hogy milyen Salesforce-élményt használ.

1. A **webes telepítés** lapon válassza a **webes létrehozás az űrlapon** gombot.
1. A **web – vezető beállításnál**válassza a **webes létrehozási űrlap létrehozása**lehetőséget.

    ![Salesforce-alapú webes telepítés](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. A **webes létrehozás űrlap létrehozása**lapon győződjön meg arról, hogy a beállítás `Include reCAPTCHA in HTML` törölve van, majd válassza a **Létrehozás**lehetőséget.

    ![Webes Salesforce létrehozásához](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Néhány HTML-szöveg jelenik meg. Keressen rá az "OID" szövegre, és másolja az **"OID" értéket** a HTML-szövegből (csak az idézőjelek között lévő szöveg), majd mentse azt. Ezt az értéket a közzétételi portál **szervezeti azonosító** mezőjébe illeszti be.

    ![A Salesforce létrehoz egy webes – vezető űrlapot a HTML "OID" érték megjelenítéséhez](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Válassza a **kész**lehetőséget.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Az ajánlat beállítása, hogy érdeklődőket küldjön a Salesforce

Ha készen áll az ajánlathoz tartozó érdeklődő-felügyeleti információk konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket.

1. Nyissa meg az ajánlat **telepítési** lapját.
1. Válassza a **kapcsolat** lehetőséget az **érdeklődő felügyelete** szakaszban.

    ![Érdeklődői felügyelet szakasz csatlakozási gombja](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. A **kapcsolat részletei** előugró ablakban válassza a **Salesforce** lehetőséget a **vezető célhelyhez** , és illessze be `oid` az értéket a létrehozott webes és érdeklődő űrlapból a **szervezeti azonosító** mezőbe.

1. A **kapcsolattartási e-mail**cím alatt adja meg a vállalat azon tagjainak e-mail-címeit, akiknek új érdeklődő fogadása esetén e-mail-értesítéseket kell kapniuk. A pontosvesszővel elválasztva több e-mailt is megadhat.

1. Kattintson az **OK** gombra.

Az **Érvényesítés** gombra kattintva győződjön meg arról, hogy sikeresen csatlakozott egy érdeklődői célhoz. Ha a művelet sikeres, a vezető célhelyen egy teszt vezet.

>[!NOTE]
>Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt.

![Kapcsolat részletei előugró ablak: válasszon egy érdeklődő célhelyet](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Kapcsolat részletei – előugró ablak – a kapcsolattartási e-mail mező ellenőrzése](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
