---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 5afcc8e4524a0e8353766ba239d5ab9161b29d86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67509074"
---
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** majd válassza az **Értesítési központok lehetőséget** a **Mobil** szakaszban. A szolgáltatás neve melletti csillagikont választva adja hozzá a szolgáltatást a bal oldali menü **KEDVENCEK** szakaszához. Miután hozzáadja **az értesítési központokat** a **KEDVENCEKhez,** jelölje ki azt a bal oldali menüben.

      ![Azure Portal – válassza az Értesítési központok lehetőséget](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Az **Értesítési központok** lapon válassza a **Hozzáadás** lehetőséget az eszköztáron.

      ![Értesítési központok – Eszköztár hozzáadása gomb](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Az **Értesítési központ** lapon tegye a következő lépéseket:

    1. Írjon be egy nevet az **Értesítési központban**.  

    1. Írjon be egy nevet az **Új névtér létrehozása mezőbe.** A névtér egy vagy több elosztót tartalmaz.

    1. Válasszon egy értéket a **Hely** legördülő listából. Ez az érték határozza meg azt a helyet, ahol a hubot létre kívánja hozni.

    1. Jelöljön ki egy meglévő erőforráscsoportot az **Erőforráscsoport**ban, vagy hozzon létre nevet egy új erőforráscsoportnak.

    1. Kattintson a **Létrehozás** gombra.

        ![Azure Portal – értesítési központ tulajdonságainak megadása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Válassza **az Értesítések** (a csengő ikon) lehetőséget, majd az Ugrás az **erőforrásra**lehetőséget. A listát az **Értesítési központok** lapon is frissítheti, és kiválaszthatja a központot.

      ![Azure Portal – Értesítések -> Erőforrás megnyitása](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. A listáról válassza a **Hozzáférési szabályzatok** lehetőséget. Ne feledje, hogy a két kapcsolati karakterlánc elérhető az Ön számára. A leküldéses értesítések kezeléséhez később szüksége lesz rájuk.

      >[!IMPORTANT]
      >*Ne* használja a **DefaultFullSharedAccessSignature** házirendet az alkalmazásban. Ezt csak a hátsó dinatoban kell használni.
      >

      ![Azure Portal – értesítési központ kapcsolati sztringjei](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
