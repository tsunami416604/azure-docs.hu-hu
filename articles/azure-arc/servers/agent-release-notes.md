---
title: Az Azure arc-kompatibilis kiszolgálók ügynökének újdonságai
description: Ebben a cikkben az Azure arc használatára képes kiszolgálók ügynökének kibocsátási megjegyzései szerepelnek. Számos összefoglaló probléma esetén további részletekre mutató hivatkozásokat talál.
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 3a01113d0d6416fb050f5c66191d5c420b7ac137
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505647"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Az Azure arc-kompatibilis kiszolgálók ügynökének újdonságai

Az Azure arc-kompatibilis kiszolgálókhoz csatlakoztatott gépi ügynök folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja a következő információkat:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások

## <a name="december-2020"></a>2020. december

### <a name="new-feature"></a>Új funkció

A Windows Server 2008 R2 támogatása

### <a name="fixed"></a>Rögzített méretű lemez

Megoldott probléma, amely megakadályozza, hogy a Linux egyéni parancsfájl-bővítménye sikeresen telepítse a alkalmazást.

## <a name="november-2020"></a>2020. november

### <a name="fixed"></a>Rögzített méretű lemez

Kijavítva a probléma, hogy a proxy konfigurációja elvész az RPM-alapú disztribúciók frissítése után.

## <a name="october-2020"></a>2020. október

### <a name="fixed"></a>Rögzített méretű lemez

- Rögzített proxy-parancsfájl a másodlagos GC-démoni egység fájlok helyének kezeléséhez.
- GuestConfig-ügynök megbízhatóságának változásai.
- US Gov Virginia régió GuestConfig-ügynökének támogatása.
- A GuestConfig-ügynök bővítménye a hibák esetén részletesebben részletezi a jelentés üzeneteit.

## <a name="september-2020"></a>2020. szeptember

Verzió: 1,0 (általánosan elérhető)

### <a name="plan-for-change"></a>Tervezett változtatás

- Az előzetes verziójú ügynökök (az 1,0-nál régebbi verziók) támogatását egy jövőbeli szolgáltatási frissítés fogja eltávolítani.
- Megszűnt a tartalék végpont támogatása `.azure-automation.net` . Ha rendelkezik proxyval, engedélyeznie kell a végpontot `*.his.arc.azure.com` .
- Ha a csatlakoztatott számítógép ügynöke az Azure-ban üzemeltetett virtuális gépre van telepítve, a virtuálisgép-bővítmények nem telepíthetők és nem módosíthatók az arc-kompatibilis kiszolgálók erőforrásból. Ezzel elkerülhető, hogy a virtuális gép **Microsoft. számítási** és **Microsoft. HybridCompute** erőforrása ne végezzen ütköző bővítményeket. Az összes bővítmény művelethez használja a számítógép **Microsoft. számítási** erőforrását.
- A vendég konfigurációs folyamat neve megváltozott, a *GCD* , a *Gcad* és a Linux rendszeren, valamint a *gcservice* a Windows *gcarcservice* .

### <a name="new-feature"></a>Új funkció

- További `azcmagent logs` lehetőség a támogatási információk gyűjtésére.
- `azcmagent license`A EULA megjelenítéséhez hozzáadott lehetőség.
- `azcmagent show --json`Lehetőség a kimeneti ügynök állapotának könnyen értelmezhető formátumban való hozzáadására.
- A kimenetben szereplő jelző hozzáadva `azcmagent show` azt jelzi, hogy a kiszolgáló az Azure-ban üzemeltetett virtuális gépen van-e.
- Hozzáadott `azcmagent disconnect --force-local-only` beállítás, amely lehetővé teszi a helyi ügynök állapotának alaphelyzetbe állítását, ha az Azure-szolgáltatás nem érhető el.
- Új `azcmagent connect --cloud` lehetőség a további felhők támogatásához. Ebben a kiadásban csak az Azure-t támogatja a szolgáltatás az ügynök kiadásának időpontjában.
- Az ügynök honosítva lett az Azure által támogatott nyelvekre.

### <a name="fixed"></a>Rögzített méretű lemez

- A kapcsolat-ellenőrzési funkcióinak fejlesztése.
- Javítottuk a proxykiszolgáló-beállításokkal kapcsolatos problémát a Linux-ügynök frissítésekor.
- Megoldott hibák, amikor a Windows Server 2012 R2 rendszert futtató kiszolgálóra próbálja meg telepíteni az ügynököt.
- A bővítmények telepítésének megbízhatóságának fejlesztése

## <a name="august-2020"></a>2020. augusztus

Verzió: 0,11

- Ez a kiadás korábban bejelentette az Ubuntu 20,04-es verziójának támogatását. Mivel egyes Azure-beli virtuálisgép-bővítmények nem támogatják az Ubuntu 20,04-et, az Ubuntu ezen verziójának támogatása megszűnik.

- Megbízhatósági fejlesztések a bővítmények központi telepítéséhez.

### <a name="known-issues"></a>Ismert problémák

Ha a Linux-ügynök egy régebbi verzióját használja, és a proxykiszolgáló használatára van konfigurálva, akkor a frissítés után újra kell konfigurálnia a proxykiszolgáló-beállítást. Ehhez futtassa a parancsot `sudo azcmagent_proxy add http://proxyserver.local:83` .

## <a name="next-steps"></a>Következő lépések

Az arc-kompatibilis kiszolgálók több hibrid gépen való kiértékelése vagy engedélyezése előtt tekintse át a [csatlakoztatott gép ügynökének áttekintése című témakört](agent-overview.md) a követelmények megismeréséhez, az ügynök műszaki részleteihez és a telepítési módszerekhez.