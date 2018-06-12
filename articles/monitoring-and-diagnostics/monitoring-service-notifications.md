---
title: Mik az Azure szolgáltatás állapotával kapcsolatos értesítésekre?
description: Szolgáltatás állapotával kapcsolatos értesítésekre engedélyezi, hogy a Microsoft Azure által közzétett szolgáltatás állapotának üzenet megtekintése.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2017
ms.author: dukek
ms.component: activitylog
ms.openlocfilehash: d6a87b17041c4ce6cf41da863354ef5a2a37141c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264442"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Az Azure portál használatával megtekintheti a szolgáltatás állapotával kapcsolatos értesítésekre

Szolgáltatás állapotával kapcsolatos értesítésekre Azure teszi közzé, és az előfizetéshez tartozó erőforrásokra vonatkozó információt. Ezek az értesítések egy alárendelt osztályát tevékenység alkalmazásnapló-események, és a műveletnaplóban is található. Szolgáltatás állapotával kapcsolatos értesítésekre tájékoztató vagy hajtható végre, attól függően, hogy az osztály is lehet.

Számos különböző osztályú szolgáltatás állapotával kapcsolatos értesítésekre.  

- **Beavatkozás szükséges:** Azure előfordulhat, hogy figyelje meg, valami szokatlan fordulhat elő, a fiókjában, és Önnek ennek orvoslása érdekében. Azure vagy és részletesen leírja, milyen lépéseket kell tennie, vagy az Azure mérnöki vagy támogatási elérhetőségi, értesítést küld.  
- **Támogatott helyreállítási:** olyan esemény történt, és a mérnökök megerősítette, hogy továbbra is problémát hatása. Az Azure mérnöki kell dolgoznia közvetlenül visszaállítására a szolgáltatások teljes állapotába.  
- **Incidens:** egy eseményt, amely hatással van a szolgáltatás jelenleg érinti az előfizetésében szereplő erőforrások közül legalább egyet.  
- **Karbantartás:** egy tervezett karbantartási tevékenységet, amelyek hatással lehetnek az előfizetéshez tartozó erőforrások közül legalább egyet.  
- **Információ:** segíthet lehetséges optimalizálásokat javítása az erőforrások felhasználását. 
- **Biztonsági:** a megoldások Azure rendszeren futó sürgős biztonsági kapcsolatos információ.

Minden szolgáltatás állapotának értesítés tartalmazza a hatókör és az erőforrások hatással van. Részletek a következők:

Tulajdonság neve | Leírás
-------- | -----------
csatornák | A következő értékek egyikét: **Admin** vagy **művelet**.
correlationId | Általában egy GUID Azonosítót a karakterlánc formátuma. Ugyanaz a művelet általában tartozó események az azonos correlationId megosztani.
eventDataId | Az esemény egyedi azonosítója.
EventName | Egy esemény címe.
szint | Egy esemény szintje
resourceProviderName | Az erőforrás-szolgáltató az érintett erőforrás neve.
resourceType| Az erőforrás az érintett erőforrás típusát.
a részállapot | Általában a megfelelő REST HTTP-állapotkód: hívja, de más egy részállapot leíró karakterláncok is használható. Például: OK (HTTP-állapotkód:: 200), létrehozott (HTTP-állapotkód:: 201-es), fogadja el (HTTP-állapotkód:: 202), nem a tartalom (HTTP-állapotkód: 204), hibás kérés (HTTP-állapotkód:: 400), nem található (HTTP-állapotkód: 404-es), ütközés (HTTP-állapotkód:: 409), belső kiszolgáló Hiba (HTTP-állapotkód: 500), a Service nem érhető el (HTTP-állapotkód: 503-as), és az átjáró időtúllépése (HTTP-állapotkód: 504).
eventTimestamp | Az esemény az esemény megfelelő a kérés feldolgozása az Azure-szolgáltatás által kiváltott idejét jelző időbélyegző.
submissionTimestamp | Az esemény váltak elérhetővé lekérdezése idejét jelző időbélyegző.
subscriptionId | Az Azure-előfizetés, amelyben ez az esemény naplózásának.
status | A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: **elindítva**, **folyamatban lévő**, **sikeres**, **sikertelen**, **aktív**, és **Feloldva**.
operationName | A művelet neve.
category | Ez a tulajdonság akkor mindig **ServiceHealth**.
resourceId | Az erőforrás-azonosító az érintett erőforrás.
Properties.Title | Ez a kommunikáció honosított címét. Angol az alapértelmezett beállítás.
Properties.Communication | A HTML-kódot szolgáltatással való kommunikációhoz honosított részleteit. Angol az alapértelmezett beállítás.
Properties.incidentType | A következő értékek egyikét: **ActionRequired**, **információk**, **incidens**, **karbantartási**, vagy **biztonsági**.
Properties.trackingId | Az incidens, amelyhez ez az esemény társítva. Ezzel az incidensekhez kapcsolódó eseményeket összefüggéseket.
Properties.impactedServices | Az escape-karakterrel megjelölt JSON-blob, amely leírja a szolgáltatások és az incidens által érintett területek. A tulajdonság felsorolja a szolgáltatások, amelyek mindegyikének egy **szolgáltatásnév**, és az érintett régiók listáját, amelyek mindegyike rendelkezik egy **RegionName**.
Properties.defaultLanguageTitle | A kommunikáció, angol nyelven.
Properties.defaultLanguageContent | A kommunikáció vagy a HTML-kódot, vagy az egyszerű szöveg angol nyelven.
Properties.Stage | A lehetséges értékei **incidens**, és **biztonsági** vannak **aktív,** **Megoldva** vagy **RCA**. A **ActionRequired** vagy **információk** az egyetlen érték **aktív.** A **karbantartási** vannak: **aktív**, **tervezett**, **esetbejegyzések**, **visszavonva**, **Újraütemezte**, **feloldva**, vagy **teljes**.
Properties.communicationId | A kommunikáció, amelyhez ez az esemény társítva.

### <a name="details-on-service-health-level-information"></a>A szolgáltatási szint állapotinformációkat részletei
  <ul>
    <li><b>Beavatkozás szükséges</b> (properties.incidentType == ActionRequired) <dl>
            <dt>Tájékoztató</dt>
            <dd>Rendszergazdai beavatkozás szükséges ahhoz, hogy a hatása a meglévő szolgáltatások</dd>
        </dl>
    </li>
    <li><b>Karbantartási</b> (properties.incidentType karbantartási ==) <dl>
            <dt>Figyelmeztetés</dt>
            <dd>karbantartási<dd>
            <dt>Tájékoztató</dt>
            <dd>Standard tervezett karbantartás</dd>
        </dl>
    </li>
    <li><b>Információ</b> (properties.incidentType információk ==) <dl>
            <dt>Tájékoztató</dt>
            <dd>Rendszergazda lehet kell ahhoz, hogy a hatása a meglévő szolgáltatások</dd>
        </dl>
    </li>
    <li><b>Biztonsági</b> (properties.incidentType biztonsági ==) <dl>
            <dt>Hiba történt</dt>
            <dd>Több szolgáltatás használata a különféle régiókban széles körű problémák vannak érintő ügyfelek széles körét.</dd>
            <dt>Figyelmeztetés</dt>
            <dd>Adott szolgáltatások és/vagy meghatározott régióiba elérése problémák vannak érintő felhasználók egy részhalmazát.</dd>
            <dt>Tájékoztató</dt>
            <dd>Felügyeleti műveletek és/vagy késését, nem a szolgáltatás elérhetőségét érintő érintő problémák.</dd>
        </dl>
    </li>
    <li><b>Problémák szolgáltatás</b> (properties.incidentType incidens ==) <dl>
            <dt>Hiba történt</dt>
            <dd>Több szolgáltatás használata a különféle régiókban széles körű problémák vannak érintő ügyfelek széles körét.</dd>
            <dt>Figyelmeztetés</dt>
            <dd>Adott szolgáltatások és/vagy meghatározott régióiba elérése problémák vannak érintő felhasználók egy részhalmazát.</dd>
            <dt>Tájékoztató</dt>
            <dd>Felügyeleti műveletek és/vagy késését, nem a szolgáltatás elérhetőségét érintő érintő problémák.</dd>
        </dl>
    </li>
  </ul>

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>A szolgáltatás állapotával kapcsolatos értesítésekre tekintse meg az Azure-portálon
1.  Az a [Azure-portálon](https://portal.azure.com), jelölje be **figyelő**.

    ![Képernyőfelvétel az Azure portál menüjében, a kijelölt figyelő](./media/monitoring-service-notifications/home-monitor.png)

    Az Azure figyelő összegyűjti az összes figyelési beállítások és adatok egyetlen összevont nézetben. A panel az első alkalommal a **Tevékenységnapló** területet megjelenítve nyílik meg.

3.  Válassza ki **riasztások**.

    ![Képernyőkép a figyelő tevékenységnapló, a kiválasztott riasztásokat](./media/monitoring-service-notifications/service-health-summary.png)
4. Válassza ki **+ Hozzáadás figyelmeztetés a napló**, és riasztást beállítása annak érdekében, hogy a jövőbeni értesítések értesítést kap. További információkért lásd: [napló riasztások tevékenység létrehozása a szolgáltatás értesítések](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="next-steps"></a>További lépések
Fogadási [riasztási értesítések, amikor az állapotfigyelő szolgáltatáshoz értesítést](monitoring-activity-log-alerts-on-service-notifications.md) van közzétéve.  
További információ [napló tevékenységriasztásokat](monitoring-activity-log-alerts.md).
