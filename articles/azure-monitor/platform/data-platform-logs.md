---
title: Naplók a Azure Monitorban | Microsoft Docs
description: Ismerteti a Azure Monitor naplóit, amelyeket a rendszer a figyelési adatelemzések speciális elemzésekor használ.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 09/09/2020
ms.author: bwren
ms.openlocfilehash: e08c649b9a1d7e8b909a413ee435fce30a8d7e48
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032772"
---
# <a name="azure-monitor-logs-overview"></a>Azure Monitor naplók áttekintése
Azure Monitor a naplók a Azure Monitor egyik funkciója, amely különböző forrásokból gyűjti és rendezi a naplózási adatokat, és az elemzéshez kifinomult lekérdezési nyelv használatával teszi elérhetővé. A különböző forrásokból származó adatok összevonhatók egyetlen munkaterületre, és együttesen elemezhetők az ilyen feladatok elvégzéséhez, a riasztások és a vizualizációk kiértékeléséhez.

## <a name="relationship-to-azure-monitor-metrics"></a>Azure Monitor metrikákkal való kapcsolat
Azure Monitor a metrikák egy idősorozat-adatbázisban tárolják a numerikus adatokat, így az adatok egyszerűbbek, mint a Azure Monitor naplók, és képesek a közel valós idejű forgatókönyvek támogatására, amelyek különösen hasznosak lehetnek a riasztások és a problémák gyors észlelése érdekében. A metrikák azonban csak egy adott struktúrában tárolhatják a numerikus adatokat, míg a naplók számos különböző adattípust tárolhatnak a saját struktúrájuk használatával. A naplók adatain összetett elemzéseket is végrehajthat, ha olyan naplózási lekérdezéseket használ, amelyek nem használhatók a metrikák adatok elemzéséhez.

A numerikus adatokat gyakran adatforrásokból küldik el a metrikák mellett. Az adatok naplókba való begyűjtésére és megőrzésére azonban további díjat számítunk fel, így metrikus adatokat is hozzáadhat a naplózott lekérdezésekhez, és elemezheti azokat a többi megfigyelési adattal.

## <a name="relationship-to-azure-data-explorer"></a>Kapcsolat az Azure Adatkezelő
Azure Monitor naplók az Azure Adatkezelőon alapulnak. Egy Log Analytics munkaterület nagyjából egy Adatkezelő Azure-beli adatbázisnak megfelelő, a táblák szerkezete azonos, és mindkettő ugyanazt a Kusto lekérdezési nyelvet (KQL) használja. Az Log Analytics használata a Azure Portal Azure Monitor lekérdezésekkel való együttműködéshez hasonló az Azure Adatkezelő webes felhasználói felületén keresztüli élményhez. Az [Azure adatkezelő-lekérdezésekben log Analytics munkaterületről is tartalmazhat adatok](/azure/data-explorer/query-monitor-data). 


## <a name="structure-of-data"></a>Adatszerkezet
Azure Monitor naplók által gyűjtött adatokat egy [log Analytics munkaterületen](./design-logs-deployment.md) tárolja a rendszer, amely több táblát tartalmaz, amelyek egy adott forrásból származó adatokat tárolnak. A munkaterület meghatározza az adatok földrajzi helyét, a hozzáférési jogokat, amelyek meghatározzák, hogy mely felhasználók férhetnek hozzá az adatokhoz, valamint a konfigurációs beállításokat, például a díjszabási szintet és az adatmegőrzést. Használhat egyetlen munkaterületet az összes megfigyelési adathoz, vagy több munkaterületet is létrehozhat a követelményektől függően. A több munkaterület létrehozásával kapcsolatos megfontolásokat lásd: [Azure monitor naplók tervezése](design-logs-deployment.md) .

Az egyes munkaterületek több táblázatot is tartalmaznak, amelyek több sornyi adatsort tartalmazó különálló oszlopba vannak rendezve. Az egyes táblákat az egyes oszlopok egyedi halmaza határozza meg, amelyeket az adatforrás által biztosított adatsorok közösen használnak. 

[![Azure Monitor naplók szerkezete](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


A Application Insightsről származó adatok naplózása is Azure Monitor naplókban történik, de az alkalmazás konfigurációjától függően eltérő módon tárolódik. A munkaterület-alapú alkalmazások esetén a rendszer az adathalmazokat egy szabványos táblázatban tárolja Log Analytics munkaterületen, amely az olyan adatmennyiségek tárolására szolgál, mint az alkalmazások, a kivételek és a lapok nézetei. Több alkalmazás is használhatja ugyanazt a munkaterületet. Klasszikus alkalmazások esetében az adatLog Analytics munkaterület nem tárolja. Ugyanazt a lekérdezési nyelvet használja, és a Azure Portal azonos Log Analytics eszközzel hozza létre és futtatja a lekérdezéseket. A klasszikus alkalmazások adatait azonban külön tárolja a rendszer. Az általános szerkezete megegyezik a munkaterület-alapú alkalmazásokkal, bár a tábla-és oszlopnevek eltérőek. Lásd: [munkaterület-alapú erőforrás-változások](../app/apm-tables.md) a kettő részletes összehasonlításához.


> [!NOTE]
> Továbbra is visszamenőleges kompatibilitást biztosítunk a Application Insights klasszikus erőforrás-lekérdezésekhez, a munkafüzetekhez és a napló alapú riasztásokhoz a Application Insights felhasználói felületén belül. Az [Új munkaterület-alapú tábla struktúrájának/sémájának](../app/apm-tables.md) lekérdezéséhez vagy megtekintéséhez először navigáljon a log Analytics munkaterületre. Az előzetes verzióban a **naplók** kiválasztása a Application Insights ablaktáblán elérhetővé teszi a klasszikus Application Insights lekérdezési élményt. További részletekért lásd a [lekérdezési hatókört](../log-query/scope.md) .


[![Azure Monitor a naplók struktúráját Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="log-queries"></a>Naplólekérdezések
Az adatok beolvasása egy Log Analytics munkaterületről egy olyan [naplózási lekérdezéssel](../log-query/log-query-overview.md) , amely írásvédett kérelem az adatok feldolgozására és az eredmények visszaküldésére. A [Kusto lekérdezési nyelve (KQL)](/azure/data-explorer/kusto/query/)az Azure adatkezelő által használt lekérdezési nyelvet írja be. Használja a Log Analytics, amely a Azure Portal egyik eszköze, amellyel szerkesztheti és futtathatja a naplók lekérdezéseit, és interaktív módon elemezheti az eredményeket. Ezután a létrehozott lekérdezéseket használhatja a Azure Monitor egyéb funkcióinak támogatásához, például a log lekérdezési riasztások és a munkafüzetek számára.


## <a name="sources-of-data-for-azure-monitor-logs"></a>Adatforrások Azure Monitor naplókhoz
Azure Monitor a különböző forrásokból származó naplózási adatokat gyűjti, beleértve a Azure Monitor és a virtuális gépeken futó ügynökök erőforrásait is. Nézze meg, [mi figyeli a Azure monitor?](../monitor-reference.md) az adatforrások teljes listáját, amelyek egy log Analytics munkaterületre küldenek információt.



## <a name="next-steps"></a>Következő lépések

- Tudnivalók a Log Analytics-munkaterületről származó adatok lekérdezéséhez és elemzéséhez szükséges [naplókról](../log-query/log-query-overview.md) .
- [A Azure monitor metrikáinak](data-platform-metrics.md)megismerése.
- Ismerje meg az Azure különböző erőforrásaihoz [elérhető figyelési információkat](data-sources.md) .

