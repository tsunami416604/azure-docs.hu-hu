---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: e18d0a6a01a86f844edc213fc95003cf4f4b46c9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164821"
---
* A Távoli asztali kapcsolat funkcióval létesítsen kapcsolatot a folyamatkiszolgáló virtuális géppel.
* A cspsconfigtool.exe fájlt az asztalon található parancsikonra kattintva futtathatja. (Az eszköz automatikusan elindul, ha ez az első alkalommal jelentkezik be a folyamatkiszolgáló).
  - A konfigurációs kiszolgáló teljes neve (FQDN) vagy IP-címe.
  - Az a port, amelyen a konfigurációs kiszolgáló figyel. Az értéknek 443-nak kell lennie.
  - A kapcsolat hozzáférési kódja, hogy kapcsolódni lehessen a konfigurációs kiszolgálóhoz.
  - A folyamatkiszolgálóhoz konfigurált adatátviteli port. Hagyja változatlanul az alapértelmezett értéket, kivéve, ha egy másik portszámra módosította a saját környezetében.

    ![A folyamatkiszolgáló regisztrálása](./media/site-recovery-vmware-register-process-server/register-ps.png)
* A konfiguráció mentéséhez és a Folyamatkiszolgáló regisztrálásához kattintson a Mentés gombra.
