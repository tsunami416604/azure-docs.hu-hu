---
title: Azure VMware-megoldás virtuális gépek életciklus-kezelése
description: Ismerje meg, hogyan kezelheti az Azure VMware megoldás virtuális gépei életciklusának minden aspektusát Microsoft Azure natív eszközökkel.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 10e721996b2e3186417a702d2d7473b36b11237f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575476"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Azure VMware-megoldás virtuális gépek életciklus-kezelése

Microsoft Azure natív eszközök lehetővé teszik a virtuális gépek (VM-EK) figyelését és kezelését az Azure-környezetben. Ugyanakkor lehetővé teszik a virtuális gépek figyelését és kezelését az Azure VMware megoldásban és a helyszíni virtuális gépeken. Ebben az áttekintésben áttekintjük az Azure-beli integrált monitorozási architektúrát, valamint azt, hogy a natív eszközeivel miként kezelhetők az Azure VMware-megoldás virtuális gépei a teljes életciklusuk során.

## <a name="benefits"></a>Előnyök

- Az Azure Native Services használatával a virtuális gépeket hibrid környezetben (Azure, Azure VMware megoldás és helyszíni) kezelheti.
- Az Azure, az Azure VMware megoldás és a helyszíni virtuális gépek integrált monitorozása és láthatósága.
- Az Azure Update Management a Azure Automationban a Windows és Linux rendszerű gépekhez is kezelheti az operációs rendszer frissítéseit. 
- A Azure Security Center komplex veszélyforrások elleni védelmet biztosít, többek között:
    - Fájlintegritás monitorozása
    - Nem biztonságos biztonsági riasztások
    - Operációs rendszer javításának felmérése
    - Biztonsági konfigurációs beállítások értékelése
    - Endpoint Protection-Értékelés 
- A Microsoft monitoring Agent (MMA) egyszerűen üzembe helyezhető az Azure ARC használatával az új virtuális gépekhez. 
- A Azure Monitor Log Analytics munkaterülete lehetővé teszi a naplók gyűjtését és a teljesítményszámláló gyűjtését az MMA vagy a Extensions használatával. Adatok és naplók összegyűjtése egyetlen pontra, és ezek az adatok különböző Azure-beli natív szolgáltatásokban jelennek meg. 
- A Azure Monitor előnyei a következők: 
    - Zökkenőmentes monitorozás 
    - Jobb infrastruktúra láthatósága 
    - Azonnali értesítések 
    - Automatikus feloldás 
    - Költséghatékonyság 

## <a name="integrated-azure-monitoring-architecture"></a>Integrált Azure monitorozási architektúra

Az alábbi ábrán az Azure VMware-megoldás virtuális gépei integrált figyelési architektúrája látható.

![Integrált Azure monitorozási architektúra](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="integrating-and-deploying-azure-native-services"></a>Az Azure Native Services integrálása és üzembe helyezése

Az **Azure arc** kiterjeszti az Azure-felügyeletet bármely infrastruktúrára, beleértve az Azure VMware-megoldást, a helyszíni vagy más felhőalapú platformokat. Az Azure ARC üzembe helyezhető egy Azure Kubernetes Service-(ak-) fürt telepítésével az Azure VMware-megoldás környezetében. További információ: [Azure arc-kompatibilis Kubernetes-fürt összekapcsolása](../azure-arc/kubernetes/connect-cluster.md).

Az Azure VMware megoldás virtuális gépei az MMA-n keresztül figyelhetők (más néven Log Analytics ügynök vagy OMS Linux-ügynök). Az MMA-t automatikusan telepítheti, ha a virtuális gépek az ARC VM életciklus-munkafolyamatain keresztül vannak kiépítve. Az MMA telepíthető virtuális gépek sablonból történő telepítésekor is a vCenter-ben. újra, az ARC-munkafolyamatokon keresztül kiépített virtuális gépekkel. Az összes kiépített Azure VMware-megoldás virtuális gépe az MMA-ban telepíthető és naplókat küldhet az Azure Log Analytics-munkaterületre. További információ: [log Analytics Agent áttekintése](../azure-monitor/platform/log-analytics-agent.md).

**Log Analytics munkaterület** lehetővé teszi a naplók gyűjtését és a teljesítményszámláló gyűjtését az MMA vagy a Extensions használatával. Log Analytics munkaterület létrehozásához tekintse meg a [log Analytics munkaterület létrehozása a Azure Portalben](../azure-monitor/learn/quick-create-workspace.md)című témakört.
- Windows rendszerű virtuális gépek a log Analytics-munkaterülethez való hozzáadásával kapcsolatban lásd: [log Analytics ügynök telepítése Windows rendszerű számítógépekre](../azure-monitor/platform/agent-windows.md).
- Linux rendszerű virtuális gépek a log Analytics-munkaterülethez való hozzáadásával kapcsolatban lásd: [log Analytics ügynök telepítése Linux rendszerű számítógépekre](../azure-monitor/platform/agent-linux.md).

A Azure Automation **Azure Update Management** egy hibrid környezetben kezeli a Windows-és Linux-alapú gépek operációs rendszerének frissítéseit. Figyeli a megfelelőség javítását, és továbbítja a javítási szórási riasztásokat a szervizelés Azure Monitor. Az Azure Update Managementnak csatlakoznia kell a Log Analytics munkaterülethez a tárolt adatmennyiségek használatával a virtuális gépek frissítéseinek állapotának felmérése érdekében.
- Log Analytics Azure Update Managementhoz való hozzáadásához először [létre kell hoznia egy Azure Automation-fiókot](../automation/automation-create-standalone-account.md).
- Log Analytics munkaterület automatizálási fiókjával való összekapcsolásához tekintse meg a [log Analytics munkaterület és az Automation-fiók](../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)című témakört.
- Az Azure-Update Management a virtuális gépek számára történő engedélyezéséről az [Automation-fiókból történő Update Management engedélyezése](../automation/update-management/update-mgmt-enable-automation-account.md)című témakörben olvashat.
- Miután hozzáadta a virtuális gépeket az Azure Update Managementhoz, [telepíthet frissítéseket a virtuális gépeken, és áttekintheti az eredményeket](../automation/update-management/update-mgmt-deploy-updates.md). 

A **Azure Security Center** komplex veszélyforrások elleni védelmet biztosít a felhőben és a helyszínen futó hibrid számítási feladatokhoz. Felméri az Azure VMware-alapú virtuális gépek biztonsági réseit, és szükség esetén riasztásokat hoz létre. Ezek a biztonsági riasztások továbbítható a Azure Monitor a megoldáshoz.
- Azure Security Center nem igényel telepítést. További információkért tekintse meg a [virtuális gépek támogatott szolgáltatásainak](../security-center/security-center-services.md)listáját.
- Ha Azure VMware-alapú virtuális gépeket és nem Azure-beli virtuális gépeket szeretne felvenni Azure Security Centerba, tekintse meg a következő témakört: [Windows rendszerű számítógépek előkészítése Azure Security Center](../security-center/quick-onboard-windows-computer.md) és [Azure Security Center a Linux](../security-center/quick-onboard-linux-computer.md)
- A virtuális gépek hozzáadása után Azure Security Center elemzi az erőforrások biztonsági állapotát a lehetséges sebezhetőségek azonosítása érdekében. Emellett javaslatokat is biztosít az Áttekintés lapon. További információ: [biztonsági javaslatok a Azure Security Center](../security-center/security-center-recommendations.md).
- A Azure Security Centerban biztonsági házirendeket is meghatározhat. A biztonsági szabályzatok konfigurálásával kapcsolatos információkért lásd: [a biztonsági házirendek használata](../security-center/tutorial-security-policy.md).

A **Azure monitor** egy átfogó megoldás a Felhőbeli és a helyszíni környezetek telemetria gyűjtésére, elemzésére és működésének elvégzésére. Nem igényel telepítést.
- Azure Monitor lehetővé teszi a különböző forrásokból származó adatok figyelését és elemzését. További információ: [Azure monitor figyelési adatainak forrásai](../azure-monitor/platform/data-sources.md).
- Különböző típusú adatokat is gyűjthet az elemzéshez, a vizualizációhoz és a riasztásokhoz. További információ: [Azure monitor adatplatform](../azure-monitor/platform/data-platform.md).
- Ha Azure Monitort szeretne konfigurálni a Log Analytics munkaterülettel, tekintse meg a [log Analytics munkaterület konfigurálása Azure monitor for VMshoz](../azure-monitor/insights/vminsights-configure-workspace.md)című témakört.
- Riasztási szabályokat hozhat létre a környezetében felmerülő problémák azonosításához, például az erőforrások magas kihasználásához, a hiányzó javításokhoz, a kevés lemezterülethez és a virtuális gépek szívveréséhez. Az észlelt eseményekhez automatikus választ is beállíthat, ha riasztást küld az IT Service Management-(ITSM-) eszközökre. A riasztások észleléséről értesítő értesítés is elküldhető e-mailben. Ilyen szabályok létrehozásához tekintse meg a következőt:
    - [Metrikai riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../azure-monitor/platform/alerts-metric.md).
    - [Naplózási riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../azure-monitor/platform/alerts-log.md).
    - Az automatizált műveletek és értesítések beállítására szolgáló [műveleti szabályok](../azure-monitor/platform/alerts-action-rules.md) .
    - [Az Azure összekapcsolásával ITSM eszközöket it-szolgáltatásmenedzsmenti csatoló használatával](../azure-monitor/platform/itsmc-overview.md).

Az **Azure platform Service (Pásti)** a Felhőbeli fejlesztési és üzembe helyezési környezet, amely lehetővé teszi a felhőalapú alkalmazások továbbítását. Például integrálhatja Azure SQL Databaseeit az Azure VMware-megoldás virtuális gépei segítségével. Az SQL-riasztások ezután összekapcsolhatók az Azure VMware megoldás VM-riasztásokkal. Tegyük fel például, hogy az alkalmazás SQL Database szakasza az Azure Pásti-n belül van, és ugyanazon alkalmazás webalkalmazási szintje fut az Azure VMware megoldás virtuális gépei között. Az adatbázis-riasztások így korrelálnak a webalkalmazási riasztásokkal. A hibaelhárítás az Azure, az Azure VMware megoldás és a helyszíni virtuális gépek egyetlen, integrált láthatóságával egyszerűsíthető.
