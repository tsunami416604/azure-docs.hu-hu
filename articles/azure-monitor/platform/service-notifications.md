---
title: Mik az Azure-szolgáltatás állapotára vonatkozó értesítések?
description: Szolgáltatás állapotára vonatkozó értesítések lehetővé teszik a Microsoft Azure által közzétett service health üzenetek megtekintéséhez.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.component: logs
ms.openlocfilehash: 2dec2b1f9bdca8c83669b753d424204218f7a9ae
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190697"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure portal használatával

Szolgáltatás állapotára vonatkozó értesítések az Azure által közzétett, és az előfizetéshez tartozó erőforrásokra vonatkozó adatokat tartalmaznak. Ezek az értesítések egy alárendelt osztály tevékenység alkalmazásnapló-események, és a tevékenységnaplóban is található. Szolgáltatás állapotára vonatkozó értesítések tájékoztató vagy az osztály függően hasznos lehet.

Nincsenek a szolgáltatás állapotával kapcsolatos értesítésekre különböző osztályok:  

- **Szükséges művelet:** Azure előfordulhat, hogy figyelje meg, hogy szokatlan körülményt fordulhat elő, a fiókjában, és az Ön megoldásához. Az Azure milyen lépéseket kell tennie, vagy az Azure mérnöki vagy támogatási elérhetőségi vagy részletező értesítést küld.  
- **Személyes helyreállítási:** Olyan esemény történt, és mérnökök megerősítette, hogy nem szűnik meg a hatást. Az Azure mérnöki kell dolgoznia, hogy közvetlenül a szolgáltatások teljes egészségügyi visszaállítása.  
- **Incidens:** Egy eseményt, amely hatással van a szolgáltatás jelenleg érintő, egy vagy több erőforrást az előfizetésében.  
- **Karbantartás:** Egy tervezett karbantartási tevékenységről, ami hatással lehet egy vagy több erőforrást az előfizetésben.  
- **Adatok:** Lehetséges optimalizálási lehetőségek, amelyekkel javíthatja az erőforrás használja. 
- **Biztonság:** Sürgős biztonsággal kapcsolatos információkat a megoldásokat, amelyek az Azure-ban.

Minden egyes szolgáltatás állapotával kapcsolatos értesítés hatókörét és hatását az erőforrásokhoz való részleteket tartalmaz. Részletei a következők:

Tulajdonság neve | Leírás
-------- | -----------
csatornák | A következő értékek egyikét: **Rendszergazdai** vagy **művelet**.
correlationId | Általában egy GUID Azonosítót a karakterláncként. Ugyanaz a művelet általában tartozó események ossza meg az azonos korrelációs azonosító.
eventDataId | Az esemény egyedi azonosítója.
EventName | Az esemény címe.
szint | Egy esemény szintjét
erőforrás-szolgáltató neve | Az érintett erőforrás az erőforrás-szolgáltató neve.
resourceType| Erőforrás típusa, az érintett erőforrás.
a részállapot | Általában a megfelelő REST HTTP-állapotkódot hívja, de más egy substatus leíró karakterláncban is használható. Példa: OK (HTTP-állapotkód: 200-as), létrehozva (HTTP-állapotkód: 201-es), elfogadva (HTTP-állapotkód: 202), nincs tartalom (HTTP-állapotkód: 204), hibás kérelem (HTTP-állapotkód: 400), nem található (HTTP-állapotkód: 404-es), ütközés (HTTP-állapotkód: 409), belső kiszolgálóhiba (HTTP-állapotkód: 500-as), a szolgáltatás nem érhető el (HTTP-állapotkód: 503-as), és az átjáró időtúllépése (HTTP-állapotkód: 504).
eventTimestamp | Időbélyeg, ha az esemény jött létre az Azure-szolgáltatás az esemény végének a kérelem feldolgozása.
submissionTimestamp | Időbélyeg, amikor az eseményt vált elérhetővé a lekérdezéséhez.
subscriptionId | Az Azure-előfizetést, amelyben a rendszer ezt az eseményt naplózza.
status | A művelet állapotát leíró karakterlánc. Néhány gyakori értékek a következők: **Lépések**, **folyamatban**, **sikeres**, **nem sikerült**, **aktív**, és **megoldott**.
operationName | A művelet neve.
category | Ez a tulajdonság nem mindig **ServiceHealth**.
resourceId | Az érintett erőforrás erőforrás-Azonosítóját.
Properties.Title | A honosított címe Ehhez a kommunikációhoz. Angol az alapértelmezett érték.
Properties.Communication | A HTML-kód kommunikációt honosított részleteit. Angol az alapértelmezett érték.
Properties.incidentType | A következő értékek egyikét: **Beavatkozás szükséges**, **tájékoztató**, **incidens**, **karbantartási**, vagy **biztonsági**.
Properties.trackingId | Az incidens, amelyhez ez az esemény társítva. Ezzel az incidenshez kapcsolódó események összekapcsolását.
Properties.impactedServices | Az escape-karakterrel megjelölt JSON-blobját, amely ismerteti a szolgáltatások és régiók az incidens által érintett. A tulajdonság magában foglalja a szolgáltatások listáját, amelyek mindegyike rendelkezik egy **ServiceName**, és az érintett régiók listája, amelyek mindegyike rendelkezik egy **RegionName**.
Properties.defaultLanguageTitle | A kommunikáció, angol nyelven.
Properties.defaultLanguageContent | A kommunikáció egyszerű szöveges vagy HTML-kód angol nyelven.
Properties.Stage | A lehetséges értékei **incidens**, és **biztonsági** vannak **aktív,** **Megoldva** vagy **RCA**. A **beavatkozás szükséges** vagy **tájékoztató** az egyetlen érték **aktív.** A **karbantartási** vannak: **Aktív**, **tervezett**, **InProgress**, **meg lett szakítva**, **Újraütemezte**, **megoldott**, vagy **teljes**.
Properties.communicationId | A kommunikáció, amelyhez ez az esemény társítva.

### <a name="details-on-service-health-level-information"></a>A Szolgáltatásállapot-szintű adatok részletei

**Szükséges művelet** (properties.incidentType == beavatkozás szükséges)
    - Tájékoztató - rendszergazda, további lépésekre van szükség a meglévő szolgáltatások gyakorolt hatás megakadályozása
    
**Karbantartási** (properties.incidentType karbantartási ==)
    - Figyelmeztetés – karbantartási
    - Tájékoztató – standard tervezett karbantartás

**Információk** (properties.incidentType információk ==)
    - Tájékoztató - rendszergazda szükség lehet a meglévő szolgáltatások gyakorolt hatás megakadályozása

**Biztonsági** (properties.incidentType biztonsági ==)
    - Hiba – eléréséhez több szolgáltatás több régióban, széles körű problémák vannak hatással a ügyfelek széles körét.
    - Figyelmeztetés – problémák adott szolgáltatások elérésére és/vagy adott régióban vannak hatással az ügyfelek egy részhalmazát.
    - Tájékoztató - felügyeleti műveletek és/vagy a késés, érintő problémákat nem negatív hatással a szolgáltatás rendelkezésre állása.

**Szolgáltatási problémák** (properties.incidentType incidens ==)
    - Hiba – eléréséhez több szolgáltatás több régióban, széles körű problémák vannak hatással a ügyfelek széles körét.
    - Figyelmeztetés – problémák adott szolgáltatások elérésére és/vagy adott régióban vannak hatással az ügyfelek egy részhalmazát.
    - Tájékoztató - felügyeleti műveletek és/vagy a késés, érintő problémákat nem negatív hatással a szolgáltatás rendelkezésre állása.


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>A szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon
1.  Az a [az Azure portal](https://portal.azure.com)válassza **figyelő**.

    ![Képernyőkép az Azure portál menüjében a kijelölt figyelő](./media/service-notifications/home-monitor.png)

    Az Azure Monitor összegyűjti az összes figyelési beállítást és adatok egyetlen, összevont nézetben. A panel az első alkalommal a **Tevékenységnapló** területet megjelenítve nyílik meg.

3.  Válassza ki **riasztások**.

    ![Képernyőkép a Monitor tevékenységnapló-, a riasztások a kiválasztott](./media/service-notifications/service-health-summary.png)
4. Válassza ki **+ tevékenységnapló-riasztás hozzáadása**, és állítsa be a riasztást annak biztosítása érdekében a jövőbeli szolgáltatási értesítésekhez értesítést kap. További információkért lásd: [tevékenységnapló-riasztások létrehozása a szolgáltatási értesítések](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>További lépések
Kap [riasztási értesítéseket, amikor a szolgáltatás állapotával kapcsolatos értesítés](../../azure-monitor/platform/alerts-activity-log-service-notifications.md) tesznek közzé.  
Tudjon meg többet [tevékenységnapló-riasztások](../../azure-monitor/platform/activity-log-alerts.md).
