---
title: Az Azure karbantartási ütemezések (előzetes verzió) |} A Microsoft Docs
description: Karbantartási ütemezés lehetővé teszi a felhasználóknak szerte a szükséges ütemezett karbantartási események az Azure SQL Data warehouse szolgáltatást használja, vezethet be új funkciók, frissítések és javítások megtervezéséhez.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/07/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a6eedc0bac7aab69a9138f4f63d0d9d802e74dfc
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228378"
---
# <a name="change-a-maintenance-schedule"></a>Karbantartási ütemezés módosítása 

## <a name="portal"></a>Portál
Karbantartási ütemezés frissíthetők vagy bármikor módosítható. Azonban ha a kiválasztott példány jelenleg alatti aktív karbantartási a beállítások lesz mentve és a következő azonosított karbantartási időszak során csak aktívvá válik. [További](https://docs.microsoft.com/azure/service-health/resource-health-overview) egy aktív karbantartási események során az adatraktár figyelésével kapcsolatos. 

Előzetes verzióban elérhető azt fogja kell rákérdez arra, hogy válassza ki a két karbantartási időszakok egy 7 napos időszakban. Az egyes karbantartási időszakok lehet 3. és 8 óra, 3 jelenleg folyamatban van a legrövidebb elérhető beállítást munkán. Karbantartási fordulhat elő, egy meghatározott karbantartási időszakon belül bármikor, azonban ezek külső azonosított idő a windows előzetes értesítés nem történik meg, a program élmény lehet a kapcsolat megszakadása rövid, mert a szolgáltatás telepíti az új kódot az adatok az adatraktár. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Az elsődleges és másodlagos windows azonosítása

Az elsődleges és másodlagos windows kell azonosítania a napokat tartományba (vagyis elsődleges ablak (kedd – csütörtök), másodlagos ablak (szombat – vasárnap)

A következő lépéseket a portálon az adatraktárba telepített karbantartási ütemezés módosításához.
1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  Válassza ki az adattárház, amely frissíteni szeretné. A lap ekkor megnyílik az áttekintési panelen. 
3.  A karbantartási ütemezés beállításai lapon vagy a karbantartási ütemezés (előzetes verzió) összefoglaló hivatkozásra kattintva az áttekintési panelen vagy a karbantartási ütemezés lehetőséget a bal oldali erőforrás menüben keresztül elérhető lesz.  

    ![Áttekintés panel beállításai](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Az elsődleges karbantartási időszak, az oldal tetején a választógombok segítségével azonosíthatja az előnyben részesített naptartomány. Ez a beállítás határozza meg, ha az elsődleges ablak történik, a hét napja vagy a hétvégén. A kijelölt ennek megfelelően frissíteni fogja az alábbi értékeket a legördülő listából. Előzetes verzió használata során egyes régiókban előfordulhat, hogy egyelőre nem támogatják a rendelkezésre álló nap kapcsolók teljes készletét. Ezeket az értékeket az elkövetkező hónapokban frissíteni fogja.

   ![Karbantartási beállítások panel](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Válassza ki az előnyben részesített elsődleges és másodlagos karbantartási időszakok használata a nap kezdete és idő ablak legördülő menük az alábbi. A panel alján összefoglalás frissíteni fogja az ütemezés a legördülő listában kiválasztott értékek alapján.

#### <a name="dropdown-options"></a>Legördülő lista beállításai
- Nap: Előnyben részesített napi karbantartás végrehajtásához a kijelölt időszak alatt.
- Kezdő időpont: előnyben részesített karbantartási ablak indítási időpontja.
- Időtartomány: előnyben részesített az időtartomány időtartama.

  Miután kiválasztotta a kívánt karbantartási időszakokat, kattintson a Mentés gombra. Egy megerősítő üzenetet jelenik meg az új ütemezés nem aktív megerősítése. Egy ütemezést egy régióban, amely még nem támogatja a karbantartási ütemezés mentésekor, az alábbi üzenet fog megjelenni. A beállítások lesz mentve, és válik aktívvá, amikor a szolgáltatás elérhetővé válik a kiválasztott régióban.    

    ![A régió bejelentési nem aktív](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>További lépések
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) kapcsolatos naplóriasztási szabály vonatkozó Webhook-műveletek.
- [További](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure Service Health szolgáltatással kapcsolatos


