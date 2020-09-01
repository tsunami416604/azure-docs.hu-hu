---
title: Az Azure arc-kompatibilis kiszolgálók (előzetes verzió) ügynökének újdonságai
description: Ez a cikk kibocsátási megjegyzéseket tartalmaz az Azure arc használatára képes kiszolgálók (előzetes verzió) ügynökhöz. Számos összefoglaló probléma esetén további részletekre mutató hivatkozásokat talál.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 0f5060189d6f9cac620b49f414e0e27bed3e356e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236694"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>Az Azure arc-kompatibilis kiszolgálók (előzetes verzió) ügynökének újdonságai

Az Azure arc-kompatibilis kiszolgálók (előzetes verzió) csatlakoztatott gépi ügynök folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja a következő információkat:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások

## <a name="august-2020"></a>2020. augusztus

Verzió: 0,11

- Az Ubuntu 20,04 támogatása.

- Megbízhatósági fejlesztések a bővítmények központi telepítéséhez.

### <a name="known-issues"></a>Ismert problémák

Ha a Linux-ügynök régebbi verzióját használja, és a proxykiszolgáló használatára konfigurálta, akkor a frissítés után újra kell konfigurálnia a proxykiszolgáló-beállítást. Ehhez futtassa a parancsot `sudo azcmagent_proxy add http://proxyserver.local:83` .

## <a name="next-steps"></a>Következő lépések

Az arc-kompatibilis kiszolgálók (előzetes verzió) több hibrid gépen való kiértékelése vagy engedélyezése előtt tekintse át a [csatlakoztatott gép ügynökének áttekintése című témakört](agent-overview.md) a követelmények, az ügynök műszaki adatai és a telepítési módszerek megismeréséhez.