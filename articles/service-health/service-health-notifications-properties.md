---
title: Mik azok az Azure-szolgáltatás állapotára vonatkozó értesítések?
description: A szolgáltatásállapot-értesítések lehetővé teszik a Microsoft Azure által közzétett szolgáltatásállapot-üzenetek megtekintését.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: e40538ac98bbc7b79311d4fb0da7568d56a84e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77653968"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon

A szolgáltatásállapot-értesítéseket az Azure teszi közzé, és az előfizetése alatti erőforrásokra vonatkozó információkat tartalmaznak. Ezek az értesítések a tevékenységnapló-események alosztályai, és a tevékenységnaplóban is megtalálhatók. A szolgáltatásállapot-értesítések az osztálytól függően lehetnek tájékoztató vagy perelhető kérhetőek.

A szolgáltatásállapot-értesítéseknek különböző osztályai vannak:  

- **Szükséges művelet:** Előfordulhat, hogy az Azure valami szokatlan történik a fiókjában, és önnel együttműködve orvosolja ezt. Az Azure értesítést küld Önnek, amely részletezi a végrehajtandó műveleteket, vagy azt, hogy miként léphet kapcsolatba az Azure mérnöki vagy támogatási tevékenységével.  
- **Esemény:** Egy esemény, amely hatással van a szolgáltatás jelenleg hatással van egy vagy több erőforrás az előfizetésben.  
- **Karbantartás:** Tervezett karbantartási tevékenység, amely hatással lehet egy vagy több erőforrás az előfizetés ben.  
- **Információ:** Lehetséges optimalizálások, amelyek segíthetnek az erőforrás-használat javításában. 
- **Biztonság:** Sürgős, biztonsággal kapcsolatos információk az Azure-ban futó megoldásokkal kapcsolatban.

Minden szolgáltatás állapotértesítés e hatókörés az erőforrásokra gyakorolt hatás részleteit tartalmazza. Részletek a következők:

Tulajdonság neve | Leírás
-------- | -----------
Csatornák | A következő értékek egyike: **Rendszergazda** vagy **Operation**.
correlationId | Általában a GUID a karakterlánc formátumban. Az azonos művelethez tartozó események általában azonos korrelációsazonosítóval rendelkeznek.
eventDataId | Egy esemény egyedi azonosítója.
eventName | Egy esemény címe.
szint | Az esemény szintje
resourceProviderName | Az érintett erőforrás erőforrás-szolgáltatójának neve.
resourceType| Az érintett erőforrás erőforrásának típusa.
alállapot | Általában a megfelelő REST-hívás HTTP-állapotkódja, de tartalmazhat más karakterláncokat is, amelyek egy alállapotot írnak le. Például: OK (HTTP állapotkód: 200), Létrehozva (HTTP-állapotkód: 201), Elfogadva (HTTP-állapotkód: 202), Nincs tartalom (HTTP-állapotkód: 204), Rossz kérés (HTTP-állapotkód: 400), Nem található (HTTP állapotkód: 404), Ütközés (HTTP-állapotkód: 409), Belső kiszolgáló Hiba (HTTP-állapotkód: 500), Szolgáltatás nem érhető el (HTTP-állapotkód: 503) és átjáró időkiírása (HTTP-állapotkód: 504).
eventTimestamp | Időbélyeg, amikor az eseményt az Azure szolgáltatás hozta létre az eseménynek megfelelő kérés feldolgozása.
beküldésIdőbélyeg | Időbélyeg, amikor az esemény elérhetővé vált a lekérdezéshez.
subscriptionId | Az Az Azure-előfizetés, amelyben ez az esemény naplózásra került.
status | A művelet állapotát leíró karakterlánc. Néhány gyakori érték: **Elindítva,** **Folyamatban,** **Sikeresen**, **Sikertelen**, **Aktív**és **Megoldva.**
operationName | A művelet neve.
category | Ez a tulajdonság mindig **ServiceHealth**.
resourceId | Az érintett erőforrás erőforrásazonosítója.
Tulajdonságok.cím | A kommunikáció honosított címe. Az alapértelmezett beállítás az angol.
Tulajdonságok.kommunikáció | A HTML-jelöléssel való kommunikáció honosított részletei. Az alapértelmezett beállítás az angol.
Tulajdonságok.incidenstípus | A következő értékek egyike: **ActionRequired**, **Informational**, **Incident**, **Maintenance**vagy **Security**.
Properties.trackingId | Az az incidens, amelyhez az esemény társítva van. Ezzel korrelálhat az incidenshez kapcsolódó eseményekkel.
Properties.impactedServices | Egy szökött JSON blob, amely leírja az incidens által érintett szolgáltatásokat és régiókat. A tulajdonság tartalmazza a szolgáltatások listáját, amelyek mindegyike rendelkezik **ServiceName**, és egy listát az érintett régiók, amelyek mindegyike rendelkezik **regionname**.
Properties.defaultLanguageTitle | A kommunikáció angolul.
Properties.defaultLanguageContent | A kommunikáció angol nyelven HTML-jelölésként vagy egyszerű szövegként.
Tulajdonságok.szakasz | Az Incidens és a Biztonság lehetséges értékei **aktívak,** **megoldottak** vagy **RCA.The**possible values for **Incident**, and **Security** are Active, Resolved or RCA . **ActionRequired** vagy **Informational** esetén az egyetlen érték **aktív.** **Karbantartás esetén** ezek a következők: **Aktív,** **Tervezett**, **Folyamatban,** **Visszavont,** **Átütemezett**, **Feloldva**vagy **Kész.**
Tulajdonságok.communicationId | Az a kommunikáció, amelyhez ez az esemény társítva van.

### <a name="details-on-service-health-level-information"></a>A szolgáltatási szintű információk részletei

**Szükséges művelet** (properties.incidentType == ActionRequired)
- Információs – Rendszergazdai műveletre van szükség a meglévő szolgáltatásokra gyakorolt hatás megelőzése érdekében.
    
**Karbantartás** (properties.incidentType == Karbantartás)
- Figyelmeztetés - Vészhelyzeti karbantartás
- Tájékoztató - Szabványos tervezett karbantartás

**Információ** (properties.incidentType == Információ)
- Tájékoztató – Előfordulhat, hogy a rendszergazda a meglévő szolgáltatásokra gyakorolt hatás megelőzése érdekében szükséges.

**Biztonság** (properties.incidentType == Biztonság)
- Figyelmeztetés – Biztonsági tanácsadó, amely hatással van a meglévő szolgáltatásokra, és rendszergazdai beavatkozást igényelhet.
- Információs – A meglévő szolgáltatásokat érintő biztonsági tanácsadó.

**Szolgáltatásproblémák** (properties.incidentType == Incidens)
- Hiba – A több szolgáltatás több régióban való elérésével kapcsolatos széles körű problémák az ügyfelek széles körét érintik.
- Figyelmeztetés – Az adott szolgáltatásokhoz és/vagy régiókhoz való hozzáféréssel kapcsolatos problémák hatással vannak az ügyfelek egy részhalmazára.
- Információs – a felügyeleti műveleteket és/vagy a késést befolyásoló problémák, amelyek nem befolyásolják a szolgáltatás rendelkezésre állását.
