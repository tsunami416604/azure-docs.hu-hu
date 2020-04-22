---
title: fájl belefoglalása
titleSuffix: Azure
description: fájl belefoglalása
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687048"
---
1. Válassza ki az Azure-társviszony-létesítési szolgáltatáshoz engedélyezni kívánt társviszony-létesítési kapcsolatot. Ezután válassza ki **...**  >  **Kapcsolat szerkesztése**.
    > [!div class="mx-imgBorder"]
    > ![Társviszony- Kapcsolat – Kapcsolat szerkesztése](../media/setup-direct-modify-editconnection.png)
1. A **Társviszony-létesítési szolgáltatás használata**csoportban válassza az **Engedélyezve** lehetőséget, majd a **Mentés**lehetőséget.
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési szolgáltatás engedélyezése](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Az Áttekintés képernyőn **láthatja** a központi telepítés részleteit. A telepítés befejezése után válassza az **Ugrás az erőforrásra**lehetőséget.
    > [!div class="mx-imgBorder"]
    > ![A telepítés befejeződött](../media/setup-direct-modify-overview-deployment-complete.png)

1. A **Regisztrált előtagok** ablaktáblán válassza **a Regisztrált előtag hozzáadása**lehetőséget.
    > [!div class="mx-imgBorder"]
    > ![Regisztrált előtag hozzáadása](../media/setup-direct-modify-add-registered-prefix.png)
1. Előtag regisztrálása a Név és **előtag** **kiválasztásával,** majd a **Mentés**lehetőség kiválasztásával.
    > [!div class="mx-imgBorder"]
    >  ![Előtag regisztrálása](../media/setup-direct-modify-register-a-prefix.png) 

1. Az előtag létrehozása után megjelenik a **regisztrált előtagok**listájában. További részletek megtekintéséhez válassza az előtag **nevét.**
    > [!div class="mx-imgBorder"]
    > ![Regisztrált előtagok és kapcsolatok](../media/setup-direct-modify-registered-prefixes.png)
1. A regisztrált előtag lapon látható a teljes részleteket, amelyek tartalmazzák az **előtag kulcs** minden előtag. Ezt a kulcsot meg kell adni a szolgáltatói isp-től lefoglalt ügyfélnek. Az ügyfél ezután regisztrálhatja előtagot az előfizetésében ezzel a kulccsal.
    > [!div class="mx-imgBorder"]
    > ![Előtag előtaggal](../media/setup-direct-modify-registered-prefix-detail.png)