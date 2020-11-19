---
title: Szabványos jelentések és adatforgalmi naplók Azure DDoS Protection
description: Ismerje meg, hogyan konfigurálhatja a jelentéseket és a folyamat naplóit.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 28e977ed68a3f288f9f86a0c2be02af4cbb26ba4
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886561"
---
# <a name="configure-ddos-attack-mitigation-reports-and-flow-logs"></a>DDoS-támadások mérséklési jelentéseinek és forgalomnaplóinak konfigurálása 

Azure DDoS Protection a standard részletes támadási elemzéseket és vizualizációkat biztosít a DDoS Attack Analytics használatával. A DDoS-támadásokkal szemben a virtuális hálózatokat védő ügyfelek részletesen megtekinthetik a támadási forgalmat, valamint a támadás enyhítésére tett lépéseket a támadásokkal kapcsolatos kockázatcsökkentő jelentésekben & a kockázatcsökkentő folyamat naplófájljait. A gazdag telemetria Azure Monitoron keresztül érhető el, beleértve a DDoS-támadások időtartama alatt részletes mérőszámokat is. A riasztás konfigurálható a DDoS Protection által elérhetővé tett Azure Monitor metrikák bármelyikéhez. A naplózás az [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection), a splunk (Azure Event Hubs), a OMS log Analytics és az Azure Storage szolgáltatással is integrálható a Azure monitor diagnosztikai felületen keresztül haladó elemzésekhez.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * A DDoS-támadások enyhítésével kapcsolatos jelentések megtekintése és konfigurálása
> * A DDoS elleni támadás-elhárítási folyamat naplófájljainak megtekintése és konfigurálása

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- Az oktatóanyag lépéseinek elvégzéséhez először létre kell hoznia egy [Azure DDoS standard Protection-csomagot](manage-ddos-protection.md).

## <a name="view-and-configure-ddos-attack-mitigation-reports"></a>A DDoS-támadások enyhítésével kapcsolatos jelentések megtekintése és konfigurálása

A támadás-elhárítási jelentések a Netflow protokoll azon adatait használják, amelyek összesítése az erőforrás támadásával kapcsolatos részletes információk biztosítására szolgál. Ha a nyilvános IP-cím a támadás alatt áll, a jelentés létrehozása azonnal elindul, amint a megoldás elindul. Az 5 percenként generált növekményes jelentés és a teljes kockázatcsökkentő időszakra vonatkozó utólagos jelentés. Így biztosíthatja, hogy a DDoS-támadás hosszabb ideig továbbra is fennmaradjon, és 5 percenként megtekintheti a kockázatcsökkentő jelentés legfrissebb pillanatképét, valamint egy teljes összegzést, amint a támadás feloldható. 

1. A portál bal oldalán válassza a **minden szolgáltatás** lehetőséget.
2. A **szűrő** mezőbe írja be a *figyelőt* . Ha a **figyelő** megjelenik az eredmények között, válassza ki.
3. A **Beállítások** területen válassza a **diagnosztikai beállítások** elemet.
4. Válassza ki azt az **előfizetést** és **erőforráscsoportot** , amely tartalmazza a NAPLÓZNI kívánt nyilvános IP-címet.
5. Válassza ki a **nyilvános IP-címet** az **erőforrástípus mezőben**, majd válassza ki azt a nyilvános IP-címet, amelyre vonatkozóan naplózni kívánja a metrikákat.
6. **A DDoSMitigationReports-napló bekapcsolásához válassza a diagnosztika bekapcsolása** lehetőséget, majd az alábbi lehetőségek közül választhat:

    - **Archiválás egy Storage-fiókba**: az adatok egy Azure Storage-fiókba íródnak. Ha többet szeretne megtudni erről a lehetőségről, olvassa el az [erőforrás-naplók archiválása](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört.
    - **Stream az Event hub**-ba: lehetővé teszi, hogy a naplók egy Azure Event hub használatával vegyenek fel naplókat. Az Event hubok lehetővé teszik az integrációt a splunk vagy más SIEM-rendszerekkel. Ha többet szeretne megtudni erről a lehetőségről, tekintse meg a [stream-erőforrás naplófájljait az Event hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-ban.
    - **Küldés log Analyticsba**: a naplók beírása a Azure monitor szolgáltatásba. Ha többet szeretne megtudni erről a lehetőségről, tekintse meg a [naplók összegyűjtése Azure monitor naplókban való használatát](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ismertető témakört.

A növekményes & a támadás utáni kockázatcsökkentő jelentések mind a következő mezőket tartalmazzák
- Támadási vektorok
- Forgalmi statisztika
- Eldobott csomagok oka
- Érintett protokollok
- Az első 10 forrás ország vagy régió
- A 10 legjobb forrás ASN

## <a name="view-and-configure-ddos-attack-mitigation-flow-logs"></a>A DDoS elleni támadás-elhárítási folyamat naplófájljainak megtekintése és konfigurálása
A támadás-elhárítási folyamat naplói lehetővé teszik, hogy a közel valós időben egy aktív DDoS-támadás során áttekintse az eldobott forgalmat, a továbbított forgalmat és az egyéb érdekes datapoints. Az adatok állandó streamjét betöltheti az Azure Sentinelbe vagy a harmadik féltől származó SIEM-rendszerbe az Event hub használatával a közel valós idejű monitorozáshoz, a lehetséges műveletek elvégzéséhez és a védelmi műveletek igényének kielégítéséhez.

1. A portál bal oldalán válassza a **minden szolgáltatás** lehetőséget.
2. A **szűrő** mezőbe írja be a *figyelőt* . Ha a **figyelő** megjelenik az eredmények között, válassza ki.
3. A **Beállítások** területen válassza a **diagnosztikai beállítások** elemet.
4. Válassza ki azt az **előfizetést** és **erőforráscsoportot** , amely tartalmazza a NAPLÓZNI kívánt nyilvános IP-címet.
5. Válassza ki a **nyilvános IP-címet** az **erőforrástípus mezőben**, majd válassza ki azt a nyilvános IP-címet, amelyre vonatkozóan naplózni kívánja a metrikákat.
6. **A DDoSMitigationFlowLogs-napló bekapcsolásához válassza a diagnosztika bekapcsolása** lehetőséget, majd az alábbi lehetőségek közül választhat:

    - **Archiválás egy Storage-fiókba**: az adatok egy Azure Storage-fiókba íródnak. Ha többet szeretne megtudni erről a lehetőségről, olvassa el az [erőforrás-naplók archiválása](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört.
    - **Stream az Event hub**-ba: lehetővé teszi, hogy a naplók egy Azure Event hub használatával vegyenek fel naplókat. Az Event hubok lehetővé teszik az integrációt a splunk vagy más SIEM-rendszerekkel. Ha többet szeretne megtudni erről a lehetőségről, tekintse meg a [stream-erőforrás naplófájljait az Event hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-ban.
    - **Küldés log Analyticsba**: a naplók beírása a Azure monitor szolgáltatásba. Ha többet szeretne megtudni erről a lehetőségről, tekintse meg a [naplók összegyűjtése Azure monitor naplókban való használatát](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ismertető témakört.

Ez a [sablon](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Enable%20Diagnostic%20Logging/Azure%20Policy) létrehoz egy Azure Policy definíciót a diagnosztikai naplózás engedélyezéséhez.

### <a name="azure-sentinel-data-connector"></a>Az Azure Sentinel adatösszekötője

Az Azure Sentinel szolgáltatáshoz kapcsolódhat, megtekintheti és elemezheti az adatait a munkafüzetekben, egyéni riasztásokat hozhat létre, és beépítheti azokat a vizsgálati folyamatokban. Az Azure Sentinelhez való kapcsolódáshoz lásd: [Kapcsolódás az Azure sentinelhez](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection). 

![Azure Sentinel DDoS-összekötő](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS Protection munkafüzet

Ha meg szeretné tekinteni a folyamat naplófájljainak adatait az Azure Analytics irányítópultján, importálhatja a minta-irányítópultot a következőből: https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook

A flow-naplók a következő mezőket fogják tartalmazni: 
- Forrás IP-címe
- Cél IP-címe
- Forrásport 
- Célport 
- Protokoll típusa 
- A mérséklés során végrehajtott művelet

![DDoS Protection munkafüzet](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

A támadási elemzés csak akkor működik, ha DDoS Protection standard engedélyezve van a nyilvános IP-cím virtuális hálózatán. 

## <a name="sample-log-outputs"></a>Mintául szolgáló naplók kimenetei

A következő képernyőképek példák a naplók kimenetére:

### <a name="ddosmitigationflowlogs"></a>DDoSMitigationFlowLogs

![DDoS Protection DDoSMitigationFlowLogs](./media/ddos-attack-telemetry/ddos-mitigation-flow-logs.png)

### <a name="ddosprotectionnotifications"></a>DDoSProtectionNotifications

![DDoS Protection DDoSProtectionNotifications](./media/ddos-attack-telemetry/ddos-protection-notifications.png)

### <a name="ddosmitigationreports"></a>DDoSMitigationReports

![DDoS Protection DDoSMitigationReports](./media/ddos-attack-telemetry/ddos-mitigation-reports.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

- A DDoS-támadások enyhítésével kapcsolatos jelentések megtekintése és konfigurálása
- A DDoS elleni támadás-elhárítási folyamat naplófájljainak megtekintése és konfigurálása

A DDoS-támadások teszteléséről és szimulálásáról a szimulációs tesztelési útmutatóban talál további információt:

> [!div class="nextstepaction"]
> [Tesztelés szimulációk használatával](test-through-simulations.md)

