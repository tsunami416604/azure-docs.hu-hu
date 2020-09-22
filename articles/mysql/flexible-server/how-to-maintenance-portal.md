---
title: Azure Database for MySQL – rugalmas kiszolgáló (előzetes verzió) – ütemezett karbantartás – Azure Portal
description: Megtudhatja, hogyan konfigurálhat egy Azure Database for MySQL rugalmas kiszolgáló ütemezett karbantartási beállításait a Azure Portal.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 6c3ad5fff6958bce72f876e3ff4701cf081abe60
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940485"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-mysql--flexible-server"></a>Azure Database for MySQL – rugalmas kiszolgáló ütemezett karbantartási beállításainak kezelése
 
Az Azure-előfizetésben található minden egyes rugalmas kiszolgálóhoz karbantartási beállításokat adhat meg. A beállítások közé tartozik a karbantartási ütemterv és az értesítési beállítások a közelgő és befejezett karbantartási eseményekhez.

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló előzetes verzióban érhető el.
 
## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for MySQL rugalmas kiszolgáló](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>Karbantartási ütemterv beállításainak megadása
 
1. A MySQL-kiszolgáló lapon, a **Beállítások** fejléc alatt válassza a **karbantartás** lehetőséget az ütemezett karbantartási beállítások megnyitásához.
2. Az alapértelmezett (rendszer által felügyelt) ütemterv a hét egy véletlenszerű napja, a karbantartási időszak pedig 60, a helyi kiszolgáló ideje pedig 23:00 és 07:00 között van. Ha testre szeretné szabni ezt az ütemtervet, válassza az **Egyéni ütemterv**lehetőséget. Ezután kiválaszthatja a hét előnyben részesített napját, valamint egy 60 perces időszakot a karbantartási kezdési időponthoz.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>Értesítés az ütemezett karbantartási eseményekről
 
A Azure Service Health használatával [megtekintheti](/azure/service-health/service-notifications.md) a rugalmas kiszolgálón a közelgő és ütemezett karbantartással kapcsolatos értesítéseket. A riasztásokat a Azure Service Healthban is [beállíthatja](/azure/service-health/resource-health-alert-monitor-guide.md) , hogy értesítéseket kapjon a karbantartási eseményekről.
 
## <a name="next-steps"></a>Következő lépések  
 
* Ismerje meg [Azure Database for MySQL – rugalmas kiszolgáló ütemezett karbantartását](concepts-maintenance.md)
* Sovány [Azure Service Health](/azure/service-health/overview.md)
