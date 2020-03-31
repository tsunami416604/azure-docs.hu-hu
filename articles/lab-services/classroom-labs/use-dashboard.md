---
title: Irányítópult használata tantermi laborhoz az Azure Lab Servicesben | Microsoft dokumentumok
description: Megtudhatja, hogyan használhatja az irányítópultot egy tantermi laborhoz az Azure Lab Servicesben.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 363e5705e9ccf4aa207ff7e5cafb615bc01bc7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538940"
---
# <a name="dashboard-for-classroom-labs"></a>Irányítópult tantermi laborokhoz
Ez a cikk az Azure Lab Services tantermi laborjának irányítópult-nézetét ismerteti. 

![Irányítópult](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Költségek és számlázáscsempe
Ez a csempe a következő költségbecslési részleteket tartalmazza:

| Beállítás | Érték | 
| ------- | ----- | 
| Kvótaórák | A felhasználó által az ütemezett órákon kívül a virtuális gép használható órák maximális száma. |
| Ütemezett órák | A laborban beállított ütemezés alapján felmerülő órák. Ez az érték csak akkor érhető el, ha az összes ütemezési eseményhez be van állítva egy kezdő/záró dátum. |
| Óra/felhasználó | A kvótaórák és az ütemezett órák összege. |
| Felhasználók maximális | A tesztkörnyezetben lévő felhasználók maximális száma az összes igényelt virtuális gép alapján. |
| Órák x felhasználók | Órák/felhasználó és a felhasználók számának szorzata. |
| Helyesbített kvóta | Az adott felhasználókhoz hozzáadott kvótaórák összege. |
| Összes óra * $/óra | Az óránkénti költség a kiválasztott virtuális gép mérete alapján. Ez alapján a rendszeres fizetés, ahogy megy ár. |
| Teljes becsült költség | Ez a tesztkörnyezet maximális ára az aktuális beállítások alapján. |

## <a name="template-tile"></a>Sablon csempe
A csempén a következő információk jelennek meg:

- A sablon létrehozásának dátuma 
- A sablon utolsó közzétételének dátuma 

Azt is rendelkezik egy linket, hogy keresse meg a **sablon** lapot, ahol [kezelheti a sablon virtuális gép](how-to-create-manage-template.md) az osztályhoz. 

## <a name="virtual-machine-pool-tile"></a>Virtuális gépkészlet csempéje

A csempén a következő információk jelennek meg:

- Diákokhoz (felhasználókhoz) rendelt virtuális gépek száma
- A diákokhoz még nem rendelt virtuális gépek száma

Azt is egy linket, hogy keresse meg a **virtuális gép készlet** lap, ahol kezelheti a virtuális gépek [a](how-to-set-virtual-machine-passwords.md) laborban. 

## <a name="users-tile"></a>Felhasználók csempe

A csempén a következő információk jelennek meg:

- Az osztályba regisztrált felhasználók száma
- A laborba felvett, de az osztályba nem regisztrált felhasználók száma 

Emellett egy hivatkozást is biztosít a **Felhasználók** lapra, ahol kezelheti a [tesztkörnyezet felhasználóit.](how-to-configure-student-usage.md) 

## <a name="schedules-tile"></a>Ütemezések csempe
A csempén láthatja a tesztkörnyezet aktuális ütemezett eseményeit. Egy hivatkozással is navigálhat az **Ütemezés** lapra, ahol [ütemezéseket hozhat létre és kezelhet.](how-to-create-schedules.md) A csempén csak két ütemezett esemény adatait és a labor ban fennmaradó ütemezett események számát jeleníti meg. 

![Ütemezett események](../media/use-dashboard/scheduled-events.png)

