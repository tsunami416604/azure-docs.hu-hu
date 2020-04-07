---
title: Érdeklődőkezelés konfigurálása a Salesforce számára | Azure Piactér
description: Konfigurálja a salesforce-on az Azure Marketplace-ügyfelek számára.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: 9b59181f7c1b24a7bc00ab579bbe67a70f499c92
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755375"
---
# <a name="configure-lead-management-for-salesforce"></a>Érdeklődőkezelés konfigurálása a Salesforce-hoz

Ez a cikk bemutatja, hogyan állíthatja be a Salesforce rendszert a kereskedelmi piactér-ajánlatból származó értékesítési érdeklődők feldolgozására.

> [!Note]
> A Marketplace nem támogatja az előre kitöltött listákat, például az **Ország** mező értéklistáját. A folytatás előtt győződjön meg arról, hogy nincsenek listák beállítva. Azt is megteheti, hogy konfigurálegy [HTTPS-végpontot](./commercial-marketplace-lead-management-instructions-https.md) vagy egy [Azure-táblát](./commercial-marketplace-lead-management-instructions-azure-table.md) az érdeklődők fogadására.

## <a name="set-up-your-salesforce-system"></a>A Salesforce rendszer beállítása

1. Jelentkezzen be a Salesforce-ba.
2. Ha a Salesforce világítási felületét használja.
    1. Válassza a **Beállítás** lehetőséget a Salesforce kezdőlapjáról.
    ![Salesforce beállítása](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. A Beállítás lapon navigáljon a bal oldali navigációs sávon a **Platform Tools->Szolgáltatásbeállítások->Marketing->Web-to-Lead oldalra.**

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Ha a Salesforce Classic élményt használja:
    1. Válassza a **Beállítás** lehetőséget a Salesforce kezdőlapjáról.
    ![Salesforce klasszikus beállítás](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. A Beállítás lapon navigáljon a bal oldali navigációs sávon a **Build ->Customize->Leads->web-to-lead (Build->>->web-to-lead**) elemre.

        ![Salesforce klasszikus web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

A többi utasítás ugyanaz, függetlenül attól, hogy melyik Salesforce-élményt használja.

4. A **Web-to-Lead beállítás lapon**kattintson a **Web-to-Lead űrlap létrehozása** gombra.
5. A **Web-to-Lead telepítőn**válassza **a Web-to-Lead űrlap létrehozása lehetőséget.**
    ![Salesforce – Web-to-Lead beállítás](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. A Create a **Web-to-Lead űrlap,** győződjön meg arról, `the Include reCAPTCHA in HTML` hogy a beállítás nincs bejelölve, és válassza a **Létrehozás lehetőséget.** 
    ![Salesforce – Webes űrlap létrehozása](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Megjelenik néhány HTML-szöveg. Keresse meg az "oid" szöveget, és másolja az **oid értéket** a HTML-szövegből (csak az idézőjelek közötti szöveget), és mentse. Ezt az értéket a közzétételi portál **Szervezeti azonosító** mezőjébe kell beilleszteni.
    ![Salesforce – Webes űrlap létrehozása](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Kijelölt **Kész**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Az ajánlat konfigurálása érdeklődők salesforce-nak való küldésére

Ha készen áll az ajánlat érdeklődőkezelési adatainak konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket:

1. Nyissa meg az **ajánlat beállítási** oldalát.
1. Válassza a **Csatlakozás** lehetőséget az Érdeklődőkezelés szakaszban.
    ![Érdeklődőkezelés - Csatlakozás](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. A Kapcsolat részletei előugró ablakban válassza a Salesforce for `oid` the Lead Destination (Érdeklődő **célhoz)** **lehetőséget,** és illessze be a létrehozott web-to-lead űrlapba a **Szervezet azonosító** mezőjének korábbi lépéseit követve.

1. Kattintson a **Mentés** gombra. 

    >[!Note]
    >Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie, mielőtt érdeklődőket kapna az ajánlathoz.

    ![Kapcsolat részletei - Érdeklődő célhelyének kiválasztása](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Kapcsolat részletei - Érdeklődő célhelyének kiválasztása](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)
