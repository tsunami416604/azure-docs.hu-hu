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
ms.openlocfilehash: 60752cf1b3c05ab7817083e70310ba7b40227dec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129967"
---
1. Kattintson egy társviszony-létesítési kapcsolat ra szeretné engedélyezni a társviszony-létesítési szolgáltatás, majd kattintson a **...**  >  **Kapcsolat szerkesztése** gomb.
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési kapcsolat szerkesztése](../media/setup-direct-modify-editconnection.png)
1. A ***Társviszony-létesítési szolgáltatás használata***csoportban kattintson az **Engedélyezve** és a **Mentés gombra.**
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítés engedélyezése társviszony-létesítési szolgáltatás](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Az áttekintő képernyőn láthatja a központi telepítés részleteit. Miután a telepítés befejeződött, kattintson a **Go to resource**.
    > [!div class="mx-imgBorder"]
    > ![A telepítés befejeződött](../media/setup-direct-modify-overview-deployment-complete.png)

1. Ekkor megjelenik a **Beállítások regisztrált előtagok**területen. Kattintson a **Regisztrált előtag hozzáadása gombra.**
    > [!div class="mx-imgBorder"]
    > ![Regisztrált előtagok és kapcsolatok](../media/setup-direct-modify-add-registered-prefix.png)
1. Előtag regisztrálása **egy név** és egy **előtag** kiválasztásával, majd kattintson a **Mentés** gombra.
    > [!div class="mx-imgBorder"]
    >  ![Előtag regisztrálása](../media/setup-direct-modify-register-a-prefix.png) 

1. Az előtag létrehozása után megjelenik a regisztrált előtagok listájában. További részletekért kattintson az előtag **nevére.**
    > [!div class="mx-imgBorder"]
    > ![Regisztrált előtagok és kapcsolatok](../media/setup-direct-modify-registered-prefixes.png)
1. A regisztrált előtag lapon az egyes **előtagok előtagkulcsának** teljes részletei jelennek meg. Ezt a kulcsot meg kell adni annak az ügyfélnek, aki ezt az előtagot a szolgáltatójától osztotta ki. Az ügyfél ezután regisztrálhatja előtagot az előfizetésében ezzel a kulccsal.
    > [!div class="mx-imgBorder"]
    > ![Előtag előtaggal](../media/setup-direct-modify-registered-prefix-detail.png)