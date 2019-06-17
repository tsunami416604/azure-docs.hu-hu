---
title: Az Azure diagnosztikai naplók áttekintése
description: További információ az Azure diagnosztikai naplók az Azure Monitor és hogyan használhatja őket egy Azure-erőforrás belül előforduló események megértéséhez.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 8902e29baa5802e3416bcda97ca59a5576d41829
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244865"
---
# <a name="overview-of-azure-diagnostic-logs"></a>Az Azure diagnosztikai naplók áttekintése

**Diagnosztikai naplók** adja meg a részletes, gyakori adatokat egy Azure-erőforrás a művelet. Az Azure Monitor lehetővé teszi a diagnosztikai naplók elérhető két típusát:

* **A bérlői naplók** – ezeket a naplókat a bérlői szintű szolgáltatások, amelyek az Azure-előfizetéssel, kívüliek biztosítja, mint például az Azure Active Directory-naplók.
* **Erőforrás-naplók** – Azure-szolgáltatások üzembe helyezése az Azure-előfizetéssel, például a hálózati biztonsági csoportok vagy a Storage-fiókok belüli erőforrások származnak ezek a naplók.

    ![Erőforrás diagnosztikai naplók és más típusú naplók](media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

Ezek a naplók a tartalom az Azure szolgáltatás- és erőforrás típusa szerint változó. Például a hálózati biztonsági csoport szabályainak számlálói, illetve a Key Vault-naplók két típusba diagnosztikai naplók.

Ezek a naplók különböznek a [tevékenységnapló](activity-logs-overview.md). A tevékenységnaplóban a műveletek a Resource Manager használatával, például egy virtuális gépet hoz létre, vagy a logikai alkalmazás törlése az előfizetés erőforrásain végzett betekintést nyújt. A tevékenységnapló előfizetés-szintű napló. Diagnosztikai naplók erőforrásszintű cybercrime végrehajtott műveletekkel belül az adott erőforráshoz, például a Key Vault titkos kulcs lekérése.

Ezek a naplók is a Vendég operációsrendszer-szintű diagnosztikai naplók különböznek. A vendég operációs rendszer a diagnosztikai naplók, ezek futhat virtuális gépen futó ügynök által gyűjtött vagy egyéb támogatott erőforrástípus. Diagnosztikai naplók erőforrásszintű nincs ügynök és a rögzítés erőforrás-specifikus adatok az Azure platform, igényelnek, miközben a Vendég operációsrendszer-szintű diagnosztikai naplók rögzítése az operációs rendszer és a egy virtuális gépen futó alkalmazások adatait.

Nem minden szolgáltatás támogatja a diagnosztikai naplók, az itt leírtak szerint. [Ez a cikk tartalmazza, mely szolgáltatásokat támogatja a diagnosztikai naplók szakasz listaelem](./../../azure-monitor/platform/diagnostic-logs-schema.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>Mire képes a diagnosztikai naplók
Az alábbiakban néhány, a diagnosztikai naplók segítségével teheti:

![Diagnosztikai naplók logikai elhelyezése](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Menti azokat egy [ **Tárfiók** ](../../azure-monitor/platform/archive-diagnostic-logs.md) naplózási vagy manuális ellenőrzést. A megőrzési ideje (nap) használatával is megadhat **erőforrás diagnosztikai beállításait**.
* [Azokat a Stream **az Event Hubs** ](diagnostic-logs-stream-event-hubs.md) egy külső szolgáltatás vagy az egyéni elemzési megoldás, mint a Power BI támogatunk.
* Elemezheti a [Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md), ahol az adatok írása közvetlenül az Azure monitornak és nincs szükség, először a tárolás az adatokat írni.  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Használhatja a storage-fiók vagy az Event Hubs-névtér, amely nem ugyanabban az előfizetésben, mint a naplókat kibocsátó. A beállítást konfiguráló felhasználónak rendelkeznie kell a megfelelő RBAC-hozzáférés mindkét előfizetéshez.

> [!NOTE]
>  Hálózati forgalmi naplók tárfiókba mögötti egy biztonságos virtuális hálózaton jelenleg nem archiválhatja.

## <a name="diagnostic-settings"></a>Diagnosztikai beállítások

Erőforrás-diagnosztikai naplók vannak konfigurálva az erőforrás diagnosztikai beállításait használja. Bérlő diagnosztikai naplók egy bérlő diagnosztikai beállítás használatával konfigurálhatók. **Diagnosztikai beállítások** szolgáltatás vezérlőelem:

* Ha a diagnosztikai naplók és mérőszámok érkeznek (Storage-fiók, az Event Hubs, illetve az Azure Monitor).
* Melyik naplókategóriák küldik, és hogy metrikaadatok is elküldi a rendszer.
* Mennyi ideig minden naplókategória fenn kell tartani a storage-fiókban.
    - Egy nulla napnyi adatmegőrzéshez azt jelenti, hogy naplókat tartják örökre. Ellenkező esetben az érték lehet minden olyan 1 és 365 közötti napok számát.
    - Ha a megőrzési házirend-beállításokat, de a naplók tárolása a Storage-fiók le van tiltva, (például, ha csak az Event Hubs és a Log Analytics lehetőség be van jelölve), az adatmegőrzési szabályzatok nem befolyásolják.
    - Adatmegőrzési házirendek, az alkalmazott napi, hogy naponta (UTC), naplók, amely mostantól a megőrzési ideje meghaladja a nap végén törli a házirendet. Például ha egy nap adatmegőrzési, ma a nap kezdetén az a napja előtt tegnap naplóinak törlődnének. A törlési folyamat kezdődik UTC szerint éjfélig, de vegye figyelembe, hogy a naplók a tárfiókból a törlendő akár 24 órát is igénybe vehet.

Ezek a beállítások konfigurált a portálon, az Azure PowerShell és CLI-parancsokkal, a diagnosztikai beállítások vagy a rendszer a [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Az Eseményközpont "Bejövő üzenetek" metrikája fel és ábrázolható egy üzenetsoronként. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Támogatott szolgáltatások, kategóriák és sémák a diagnosztikai naplók

[Ebben a cikkben](../../azure-monitor/platform/diagnostic-logs-schema.md) támogatott szolgáltatások, valamint naplókategóriák és ezek a szolgáltatások által használt sémák teljes listáját.

## <a name="next-steps"></a>További lépések

* [Az erőforrás-diagnosztikai naplók Stream **az Event Hubs**](diagnostic-logs-stream-event-hubs.md)
* [Módosítsa az erőforrás diagnosztikai beállításait az Azure Monitor REST API használatával](https://docs.microsoft.com/rest/api/monitor/)
* [Az Azure Monitor szolgáltatással az Azure storage-naplók elemzése](collect-azure-metrics-logs.md)
