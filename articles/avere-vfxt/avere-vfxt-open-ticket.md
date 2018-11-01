---
title: Hogyan kérhet támogatást az Avere vFXT az Azure-hoz
description: Azure-támogatási jegyek Avere vFXT kapcsolatos megnyitása ismertetése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d621511cbb6983f8ad57ea8305823039475f40d0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634100"
---
# <a name="get-help-with-your-system"></a>Segítség a rendszer a

Ha az Azure-hoz a Avere vFXT segítségre van szüksége, az alábbiakban kérhet támogatást a különböző módokon:

* **Avere vFXT probléma** – használja az Azure Portalon leírtak szerint, nyisson meg egy támogatási jegyet a Avere vFXT [alább](#open-a-support-ticket-for-your-avere-vfxt).
* **Kvóta** – Ha rendelkezik egy kvótával kapcsolatos problémák [a kvóta növelésére](#request-a-quota-increase)
* **Dokumentáció és példák** – Ha problémákat tapasztal a dokumentáció vagy példákat talál a problémát, és használja az oldal alján a **visszajelzés** meglévő problémák és a egy új egy if fájl szakasz szükséges.  

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Nyisson meg egy támogatási jegyet a Avere vFXT

Telepítését vagy használatát Avere vFXT során problémák merülnek fel, ha az Azure Portalon keresztül segítséget kérhet.  

Kövesse az alábbi lépéseket, győződjön meg arról, hogy a támogatási jegy a fürtről egy erőforráshoz van megjelölve. A jegy címkézés segítségével irányítja a megfelelő támogatási erőforrás. 

1. A [ https://portal.azure.com ](https://portal.azure.com)válassza **erőforráscsoportok**.

   !["Erőforráscsoportok" bekarikázott az Azure portal bal oldali menü képernyőképe](media/avere-vfxt-ticket-rg.png)

1. Tallózással keresse meg az erőforráscsoport, amely tartalmazza a vFXT fürt, ahol a probléma lépett fel, és kattintson az egyik a Avere virtuális gépeket.

    ![az egy adott virtuális gép bekarikázott az Azure portal resource csoport "Áttekintés" panel képernyőképe](media/avere-vfxt-ticket-vm.png)

1. A virtuális gép oldalon görgessen le a bal oldali panel aljára, és kattintson a **új támogatási kérelem**.

    ![Képernyőkép az Azure portal virtuális gép oldalán az előző képernyőképen látható a virtuális gép számára. A bal oldali menüben az alsó és a "Új támogatási kérelem" görgetése közben bekarikázott van.](media/avere-vfxt-ticket-request.png)

1. A támogatási kérelem egyik lapján kattintson a **minden szolgáltatás** és keresse meg a **tárolási** kiválasztása **Avere vFXT**.

    ![képernyőfelvétel az új támogatási kérelem képernyő az "alapvető beállítások" fejléc és a egy kör körül a "Szolgáltatás" elemet az Azure Portalon. Az "Összes szolgáltatások" gombot, és a legördülő menüből a mező értéke "Avere vFXT"](media/avere-vfxt-ticket-service.png)

1. Két a lapon választhatja ki a probléma típusa és a probléma megoldásához leginkább megfelelő kategóriát. Adjon hozzá egy rövid címet és egy leírást, amely tartalmazza az idő, a probléma lépett fel. 

   ![képernyőfelvétel az új támogatási kérelem képernyő a fejléc "Hiba", amely kell elvégezni, sok mezőből áll](media/avere-vfxt-ticket-problem.png)

1. A három lap, adja meg a kapcsolattartási adatokat, és kattintson a **létrehozás**. Az e-mail cím megerősítése és a jegyet számnak küld, és a támogatási személyzet tagja felveszi Önnel a kapcsolatot.

## <a name="request-a-quota-increase"></a>A kvóta növelésére

Olvasási [a vFXT fürt kvóta](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) megtudhatja, milyen összetevők szükségesek a Avere vFXT telepítése az Azure-hoz. Is [a kvóta növelésére](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) az Azure Portalról.