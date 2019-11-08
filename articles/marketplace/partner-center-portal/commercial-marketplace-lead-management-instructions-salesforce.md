---
title: A Salesforce vezető felügyeletének konfigurálása | Azure piactér
description: Az Azure Marketplace-ügyfelek Salesforce az érdeklődők felügyeletének konfigurálása.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: bdd3bb50fc69083c73eb01a84bf0fb0db82a8a65
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812154"
---
# <a name="configure-lead-management-for-salesforce"></a>A Salesforce vezető felügyeletének konfigurálása

Ez a cikk azt ismerteti, hogyan lehet beállítani a Salesforce rendszerét értékesítési érdeklődők feldolgozására a Piactéri ajánlatból.

## <a name="set-up-your-salesforce-system"></a>A Salesforce-rendszerek beállítása

1. Jelentkezzen be a Salesforce.
2. Ha a Salesforce-világítási élményt használja.
    1. Válassza a **telepítő** elemet a Salesforce kezdőlapján.
    ![Salesforce beállítása](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. A telepítés lapon navigáljon a **platform Tools-> funkció beállításai-> marketing-> web-to-Lead elemre**.
    ![Salesforce web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Ha a klasszikus Salesforce-élményt használja:
    1. Válassza a **telepítő** elemet a Salesforce kezdőlapján.
    ![Salesforce klasszikus telepítő](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. A telepítés lapon navigáljon a bal oldali navigációs sávon a **Build-> testreszabása-> leads-> web-to-Lead**elemre.
    ![Salesforce klasszikus webes és vezető](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

A többi utasítás ugyanaz, függetlenül attól, hogy milyen Salesforce-élményt használ.

4. A **webes telepítés lapon**válassza a **webes létrehozás az űrlapon** gombot.
5. A **web – vezető beállításnál**válassza a **webes létrehozási űrlap létrehozása**lehetőséget.
    ![Salesforce-web-to-Lead Setup](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. A **webes létrehozási űrlap létrehozása lapon**győződjön meg arról, hogy `the Include reCAPTCHA in HTML` beállítás nincs bejelölve, és válassza a **Létrehozás**lehetőséget. 
    ![Salesforce – webes létrehozási űrlap létrehozása](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Néhány HTML-szöveg jelenik meg. Keressen rá az "OID" szövegre, és másolja az **OID-értéket** a HTML-szövegből (csak az idézőjelek között lévő szöveg), és mentse. Ezt az értéket a közzétételi portál **szervezeti azonosító** mezőjébe illeszti be.
    ![Salesforce – webes létrehozási űrlap létrehozása](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. A kijelölt **Befejezés**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Az ajánlat beállítása, hogy érdeklődőket küldjön a Salesforce

Ha készen áll az ajánlathoz tartozó érdeklődői felügyeleti információk konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket:

1. Navigáljon az ajánlat **telepítési** lapjára.
1. Válassza a **kapcsolat** lehetőséget az érdeklődő felügyelete szakaszban.
    ![vezető felügyelet – kapcsolat](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. A kapcsolat részletei előugró ablakban válassza a **Salesforce** lehetőséget az **érdeklődő célhelyéhez** , és illessze be a `oid`t a webes – érdeklődő űrlapra, amelyet a **szervezeti azonosító** mezőben a korábbi lépések alapján hozott létre.

1. Kattintson a **Mentés** gombra. 

    >[!Note]
    >Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt.

    ![Kapcsolat részletei – válasszon egy érdeklődő célhelyet](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Kapcsolat részletei – válasszon egy érdeklődő célhelyet](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)