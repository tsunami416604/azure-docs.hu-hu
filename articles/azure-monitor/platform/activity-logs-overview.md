---
title: Az Azure-tevékenység naplójának áttekintése
description: Ismerje meg az Azure-beli tevékenység naplóját, és azt, hogy miként használható az Azure-előfizetésében előforduló események megismerésére.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ee3a1fef379e2950172dddc389b30e0a363127ae
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262126"
---
# <a name="overview-of-azure-activity-log"></a>Az Azure Activity log áttekintése

Az **Azure-tevékenység naplója** betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekre. Ez számos adattartományt tartalmaz, Azure Resource Manager operatív adatokból Service Health események frissítéseire. A műveletnapló korábbi nevén _naplózási naplók_ vagy _operatív naplók_voltak, mivel a felügyeleti kategória az előfizetésekhez tartozó vezérlési sík eseményeket jelenti. 

A tevékenység naplójának használatával meghatározhatja a _mi_, _ki_és _Mikor_ az előfizetésben lévő erőforrásokra vonatkozó írási műveletek (Put, post, DELETE) esetében. A művelet és az egyéb releváns tulajdonságok állapotát is ismernie is. 

A műveletnapló nem tartalmaz olvasási (GET) műveleteket vagy műveleteket a klasszikus/RDFE modellt használó erőforrásokhoz.

## <a name="comparison-to-resource-logs"></a>Erőforrás-naplók összehasonlítása
Minden egyes Azure-előfizetéshez egyetlen tevékenységi napló van. Adatokkal szolgál az erőforráson kívülről (a "vezérlési síkon") végzett műveletekről. Az erőforrás- [naplókat](resource-logs-overview.md) egy erőforrás bocsátja ki, és információt nyújt az adott erőforrás működéséről (az "adatsíkon"). Az erőforrás-naplók összegyűjtéséhez létre kell hoznia egy diagnosztikai beállítást az egyes erőforrásokhoz.

![Tevékenységek naplói az erőforrás-naplókhoz képest](media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Az Azure-tevékenység naplója elsősorban a Azure Resource Managerban bekövetkező tevékenységek esetében fordul elő. A klasszikus/RDFE modellt használó erőforrásokat nem követi nyomon. Néhány klasszikus erőforrástípus rendelkezik egy proxy erőforrás-szolgáltatóval Azure Resource Managerban (például: Microsoft. ClassicCompute). Ha egy klasszikus erőforrástípust használ a Azure Resource Manager ezen proxy erőforrás-szolgáltatók használatával, a műveletek megjelennek a tevékenység naplójában. Ha a Azure Resource Manager proxyn kívüli klasszikus erőforrástípus is működik, a műveletek csak a műveleti naplóban lesznek rögzítve. A műveleti naplót a portál egy külön szakasza is megkeresheti.

## <a name="activity-log-retention"></a>Tevékenység naplójának megőrzése
A létrehozást követően a rendszer nem módosítja vagy törli a tevékenység naplójának bejegyzéseit. Emellett a felületen vagy programozott módon nem módosítható. A műveletnapló eseményei 90 napig tárolódnak. Ha hosszabb ideig szeretné tárolni az adatokat, [gyűjtsön Azure monitor](activity-log-collect.md) vagy [exportálja a tárolóba vagy Event Hubsba](activity-log-export.md).

## <a name="view-the-activity-log"></a>A műveletnapló megtekintése
A Azure Portal **figyelő** menüjében tekintse meg a tevékenység naplóját az összes erőforráshoz. Egy adott erőforráshoz tartozó tevékenység naplójának megtekintése az erőforrás menüjének **tevékenység napló** lehetőségével. A műveletnapló rekordjait a PowerShell, a CLI vagy a REST API is beolvashatja.  Lásd: [Azure-Tevékenységnaplók eseményeinek megtekintése és beolvasása](activity-log-view.md).

![Műveletnapló megtekintése](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Tevékenységek naplójának összegyűjtése Azure Monitor
Gyűjtsön a Log Analytics munkaAzure Monitor területre a tevékenység naplójában, hogy elemezze más figyelési adatokkal, és az adatokat 90 napnál hosszabb ideig őrizze meg. Lásd: [Az Azure-Tevékenységnaplók összegyűjtése és elemzése log Analytics munkaterületen Azure monitor](activity-log-collect.md).

![Lekérdezési tevékenység naplója](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Tevékenységnapló exportálása
Exportálja a tevékenység naplóját az Azure Storage-ba archiválásra, vagy továbbítsa azt egy Event hub-ba egy harmadik féltől származó szolgáltatás vagy egyéni elemzési megoldás általi betöltéshez. Lásd: [Az Azure-tevékenység naplójának exportálása](activity-log-export.md). A Power BI a [**Power bi Content Pack csomag**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)használatával is elemezheti a tevékenységek naplózási eseményeit.

## <a name="alert-on-activity-log"></a>Riasztás a tevékenység naplójában
Riasztást hozhat létre, ha adott eseményeket hoz létre a tevékenység naplójában egy [tevékenység naplójának riasztásával](activity-log-alerts.md). Ha a tevékenység naplója egy Log Analytics munkaterülethez csatlakozik, akkor a [napló lekérdezésével](alerts-log-query.md) is létrehozhat riasztást, de a lekérdezési riasztások naplózása is megtörténik. A tevékenység naplójának riasztásai díjmentesek.

## <a name="categories-in-the-activity-log"></a>Kategóriák a tevékenység naplójában
A tevékenység naplójának minden eseménye egy adott kategóriával rendelkezik, amelyet az alábbi táblázat ismertet. Részletes információ a sémák szerializálása e kategóriák közül, lásd: [Azure-tevékenységnapló eseménysémája](activity-log-schema.md). 

| Category | Leírás |
|:---|:---|
| Rendszergazdai | A Resource Manageren keresztül végrehajtott összes létrehozási, frissítési, törlési és műveleti művelet rekordját tartalmazza. Ilyenek például a _virtuális gépek létrehozása_ és a _hálózati biztonsági csoport törlése_.<br><br>Egy felhasználó vagy alkalmazás által a Resource Managerrel végrehajtott összes művelet egy adott erőforrástípus műveletének megfelelően van modellezve. Ha a művelet típusa _írás_, _Törlés_vagy _művelet_, akkor a művelet kezdési és sikerességi rekordjait is rögzíti a rendszer a felügyeleti kategóriában. A rendszergazdai események az előfizetés szerepköralapú hozzáférés-vezérlésének változásait is tartalmazzák. |
| Service Health | Az Azure-ban történt összes szolgáltatás-egészségügyi incidens rekordját tartalmazza. Az _USA keleti régiójában SQL Azure Service Health eseménynek például állásidőt tapasztalhat_. <br><br>Az események Service Health hat fajta: _Beavatkozás szükséges_, _segítség a helyreállításhoz_, _incidenshez_, _karbantartáshoz_, _információhoz_vagy _biztonsághoz_. Ezek az események csak akkor jönnek létre, ha van olyan erőforrása az előfizetésben, amelyet az esemény érint.
| Resource Health | Az Azure-erőforrásokra vonatkozó összes erőforrás-állapottal kapcsolatos esemény rekordját tartalmazza. Resource Health eseményre például a _virtuális gép állapota nem érhető el értékre módosult_.<br><br>Resource Health események a négy állapot egyikét jelenthetik: _Elérhető_, nem _elérhető_, _csökkentett teljesítményű_és _ismeretlen_. Emellett Resource Health eseményeket úgy is kategorizálhatja, hogy _platform kezdeményezett_ vagy _felhasználó által kezdeményezett_. |
| Riasztás | Az Azure-riasztások aktiválási rekordját tartalmazza. Egy riasztási esemény például a _MyVM CPU%-a az elmúlt 5 percben 80_.|
| Automatikus méretezés | Az adott előfizetésben definiált bármely, az autoskálázási motor működésével kapcsolatos események rekordját tartalmazza. Az autoskálázási eseményre például _nem sikerült a vertikális Felskálázási művelet_. |
| Ajánlás | A Azure Advisor ajánlásainak eseményeit tartalmazza. |
| Biztonság | A Azure Security Center által generált riasztások rekordját tartalmazza. A biztonsági eseményekre példaként a rendszer _gyanús kettős kiterjesztésű fájlt futtat_. |
| Szabályzat | A Azure Policy által végrehajtott összes hatás művelet műveleteit tartalmazza. Példák a házirendi eseményekre: _naplózás_ és _Megtagadás_. A házirend által végrehajtott összes művelet az erőforráson végzett műveletként van modellezve. |


## <a name="next-steps"></a>További lépések

* [Log-profil létrehozása az Azure-tevékenység naplójának exportálásához](activity-log-export.md)
* [Az Azure-tevékenység naplójának továbbítása Event Hubs](activity-logs-stream-event-hubs.md)
* [Az Azure-tevékenység naplójának archiválása a Storage-ba](archive-activity-log.md)

