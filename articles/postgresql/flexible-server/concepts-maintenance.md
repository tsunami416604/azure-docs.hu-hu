---
title: Ütemezett karbantartás – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL rugalmas kiszolgáló ütemezett karbantartási funkcióját ismerteti.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: ffee15776a48b6495f78b6becf81c620e1dc4d69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336309"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>Ütemezett karbantartás rugalmas Azure Database for PostgreSQL-kiszolgálón
 
Azure Database for PostgreSQL – a rugalmas kiszolgáló rendszeres karbantartást végez, hogy a felügyelt adatbázis biztonságos, stabil és naprakész legyen. A karbantartás során a kiszolgáló új funkciókat, frissítéseket és javításokat kap.
 
> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el
 
## <a name="selecting-a-maintenance-window"></a>Karbantartási időszak kiválasztása
 
A karbantartást a hét egy adott napján és az adott napon belül egy időablakban is ütemezhet. Vagy megadhatja, hogy a rendszer válasszon egy napot és egy időablakot az automatikus indításhoz. Mindkét esetben a rendszer öt nappal a karbantartás futtatása előtt riasztást küld. A rendszer azt is lehetővé teszi, hogy a karbantartás elindításakor és sikeres befejezésekor is jelezze.
 
Az ütemezett karbantartásra vonatkozó értesítések a következőket tehetik:
 
* Adott címre e-mailben
* E-mailben egy Azure Resource Manager szerepkörbe
* SMS-ben elküldve a mobileszközök számára
* Értesítésként leküldve egy Azure-alkalmazáshoz
* Kézbesítés hangüzenetként
 
A karbantartási ütemezés beállításainak megadásakor kiválaszthatja a hét valamelyik napját és egy időszakot. Ha nem határoz meg időpontot, a rendszer a kiszolgáló régiójának időzónája szerint 23:00 és 7:00 közötti időpontot választ. Az Azure-előfizetésében minden egyes rugalmas kiszolgálóhoz különböző ütemterveket adhat meg. 
 
> [!IMPORTANT]
> Általában legalább 30 nap telik el a kiszolgáló sikeres ütemezett karbantartási eseményei között.
>
> A kritikus fontosságú vészhelyzeti frissítések, például súlyos biztonsági rések esetén azonban előfordulhat, hogy kevesebb mint 5 nappal korábban kap értesítést a frissítésről. A kritikus fontosságú frissítés akkor is telepíthető a kiszolgálón, ha az elmúlt 30 napban sikeres ütemezett karbantartást hajtottak végre azon.

Bármikor frissítheti az ütemezési beállításokat. Ha a rugalmas kiszolgáló karbantartásra van ütemezve, és frissíti az ütemezési beállításokat, az aktuális esemény az ütemezett módon folytatódik, és az ütemezési beállítások módosítása a sikeres befejezést követően érvénybe lép. 

Ha a rendszer megszakította a karbantartási eseményt, vagy nem sikerül befejezni, a rendszer értesítést fog létrehozni a megszakított vagy sikertelen karbantartási eseményről. A rendszer a következő karbantartási kísérletet ütemezi az aktuális ütemezési beállításoknak megfelelően, és öt nappal korábban értesítést kap.
 
## <a name="next-steps"></a>Következő lépések
 
* Útmutató [a karbantartási ütemterv módosításához](how-to-maintenance-portal.md)
* Ismerje meg, hogyan [kaphat értesítéseket a közelgő karbantartásról](../../service-health/service-notifications.md) a Azure Service Health használatával
* Megtudhatja, hogyan [állíthat be riasztásokat a közelgő ütemezett karbantartási eseményekről](../../service-health/resource-health-alert-monitor-guide.md)
