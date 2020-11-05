---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/09/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 62d466e81309765540bcbd52714733b97d241ebc
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93353917"
---
Egy böngészőből lépjen az Azure Portalra, majd jelentkezzen be az Azure-fiókjával.

1. Navigáljon a virtuális WAN lapra. A portálon kattintson az **+Erőforrás létrehozása** gombra. Írja be a **virtuális WAN** kifejezést a keresőmezőbe, majd válassza az **ENTER billentyűt**.
1. Válassza ki a **virtuális WAN** elemet az eredmények közül. A virtuális WAN lapon válassza a **Létrehozás** lehetőséget a WAN létrehozása lap megnyitásához.
1. A **WAN létrehozása** lap **alapok** lapján töltse ki a következő mezőket:

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="A képernyőfelvétel a WAN létrehozása panelt jeleníti meg, ahol az alapok lap van kiválasztva.":::

   * **Előfizetés** – Válassza ki a használni kívánt előfizetést.
   * **Erőforráscsoport** – új létrehozása vagy meglévő használata.
   * **Erőforráscsoport helye** – válasszon ki egy erőforrás-helyet a legördülő listából. A WAN egy globális erőforrás, és nem egy adott régióhoz tartozik. Azonban ki kell választania egy régiót a létrehozott WAN-erőforrás kezeléséhez és megkereséséhez.
   * **Név** – írja be a WAN-híváshoz használni kívánt nevet.
   * **Típus** – alapszintű vagy standard. Válassza a **standard** lehetőséget. Ha az alapszintű VWAN lehetőséget választja, akkor az alapszintű VWANs csak olyan alapszintű hubokat tartalmazhat, amelyek a kapcsolat típusát a helyek közötti helyre korlátozzák.
1. Miután befejezte a mezők kitöltését, válassza a **felülvizsgálat + létrehozás** lehetőséget.
1. Az ellenőrzés után válassza a **Létrehozás** lehetőséget a virtuális WAN létrehozásához.
