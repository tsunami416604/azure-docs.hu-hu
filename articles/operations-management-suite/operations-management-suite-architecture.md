---
title: "Az Operations Management Suite (OMS) architektúrája | Microsoft Docs"
description: "A Microsoft Operations Management Suite (OMS) a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a helyszíni és a felhőalapú infrastruktúra kezelésében és védelmében.  Ez a cikk azonosítja az OMS különböző szolgáltatásait, és a részletes tartalmukra mutató hivatkozásokat tartalmaz."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 40e41686-7e35-4d85-bbe8-edbcb295a534
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 76f69946724b5297b1f9a1f715819c69c4a4a51d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="oms-architecture"></a>OMS-architektúra
Az [Operations Management Suite (OMS)](https://azure.microsoft.com/documentation/services/operations-management-suite/) felhőalapú szolgáltatások gyűjteménye a helyszíni és a felhőalapú környezet kezeléséhez.  Ez a cikk az OMS különböző helyszíni és felhőalapú összetevőit és az összetevők magas szintű felhőszámítási architektúráját ismerteti.  További részleteket az egyes szolgáltatásokhoz tartozó dokumentáció tartalmaz.

## <a name="log-analytics"></a>Log Analytics
A [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) által gyűjtött minden adat az OMS-tárházba kerül, amelyet az Azure üzemeltet.  A csatlakoztatott források által előállított adatokat a rendszer az OMS-tárházba gyűjti.  Jelenleg háromféle csatlakoztatott forrás támogatott.

* Közvetlenül az OMS-hez csatlakoztatott [Windows](../log-analytics/log-analytics-windows-agents.md) vagy [Linux](../log-analytics/log-analytics-linux-agents.md) rendszerű számítógépre telepített ügynök.
* A [Log Analytics-hez csatlakoztatott](../log-analytics/log-analytics-om-agents.md) System Center Operations Manager (SCOM) felügyeleti csoport.  Az SCOM-ügynökök továbbra is a felügyeleti kiszolgálókkal kommunikálnak, amelyek az eseményeket és a teljesítményadatokat a Log Analytics-nek továbbítják.
* [Azure-tárfiók,](../log-analytics/log-analytics-azure-storage.md) amely [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)-adatokat gyűjt feldolgozói szerepkörből, webes szerepkörből vagy virtuális gépről az Azure-ban.

A Log Analytics által a csatakoztatott forrásokból gyűjtött adatokat, többek között az eseménynaplókat és teljesítményszámlálókat az adatforrások határozzák meg.  A megoldások funkciókkal bővítik az OMS-t, és könnyen hozzáadhatók a munkaterülethez az [OMS megoldástárából](../log-analytics/log-analytics-add-solutions.md).  Egyes megoldások közvetlen kapcsolatot igényelhetnek az SCOM-ügynöktől a Log Analytics szolgáltatáshoz, míg másokhoz szükség lehet további ügynök telepítésére.

A Log Analytics olyan webalapú portállal rendelkezik, amelyet OMS-erőforrások kezelésére, OMS-megoldások hozzáadására és konfigurálására, valamint az OMS-tárház adatainak megtekintésére és elemzésére használhat.

![A Log Analytics magas szintű architektúrája](media/operations-management-suite-architecture/log-analytics.png)

## <a name="azure-automation"></a>Azure Automation
Az [Azure Automation runbookjainak](http://azure.microsoft.com/documentation/services/automation) végrehajtása az Azure-felhőben történik, és képes hozzáférni az Azure-ban, más felhőszolgáltatásokban lévő vagy a nyilvános internetről elérhető erőforrásokhoz.  A helyi adatközpont helyszíni gépeit is kijelölheti [hibrid runbook-feldolgozó](../automation/automation-hybrid-runbook-worker.md) használatával, hogy a runbookok elérhessenek helyi erőforrásokat.

Az Azure Automationben tárolt [DSC-konfigurációk](../automation/automation-dsc-overview.md) azonnal alkalmazhatók az Azure virtuális gépekre.  Egyéb fizikai és virtuális gépek az Azure Automation DSC lekérési kiszolgálóról kérhetnek konfigurációkat.

Az Azure Automation olyan OMS-megoldással rendelkezik, amely statisztikákat jelenít meg, valamint hivatkozásokat az Azure-portál indításához bármely művelethez.

![Az Azure Automation magas szintű architektúrája](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Azure Backup
Az [Azure Backup](http://azure.microsoft.com/documentation/services/backup) védett adatainak tárolása egy meghatározott földrajzi régióban elhelyezkedő biztonságimásolat-tárolóban történik.  Az adatok ugyanazon a régión belül replikálódnak, és a tároló típusától függően a nagyobb redundancia érdekében egy másik régióban is replikálódhatnak.

Az Azure Backup három alapvető alkalmazási helyzetben használható.

* Windows rendszerű gép Azure Backup-ügynökkel.  Ez lehetővé teszik bármely Windows-kiszolgáló fájljainak és mappáinak biztonsági mentését közvetlenül az Azure-beli biztonsági mentési tárba.  
* System Center Data Protection Manager (DPM) vagy Microsoft Azure Backup Server. Ez lehetővé teszi a DPM vagy a Microsoft Azure Backup Server használatát az alkalmazások, például az SQL és a SharePoint munkaterhelései mellett fájlok és mappák biztonsági másolatának elkészítésére helyi tárba, majd ezek replikálását az Azure-beli biztonsági mentési tárba.
* Azure Virtual Machine Extensions.  Lehetővé teszi az Azure-beli virtuális gépek biztonsági másolatának elkészítését az Azure-beli biztonsági mentési tárba.

Az Azure Backup olyan OMS-megoldással rendelkezik, amely statisztikákat jelenít meg, valamint hivatkozásokat az Azure-portál indításához bármely művelethez.

![Az Azure Backup magas szintű architektúrája](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Az [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) koordinálja a virtuális gépek és a fizikai kiszolgálók replikálását, feladatátvételét és feladat-visszavételét. A replikációs adatok cseréje a Hyper-V-gazdagépek, a VMware-hipervizorok és az elsődleges és a másodlagos adatközpontokban elhelyezkedő fizikai kiszolgáló között, vagy az adatközpont és az Azure Storage között történik.  A Site Recovery a metaadatokat meghatározott földrajzi Azure-régióban elhelyezkedő tárolókban tárolja. A Site Recovery szolgáltatás nem tárol replikált adatokat.

Az Azure Site Recovery három alapvető replikációs helyzetben használható.

**Hyper-V virtuális gépek replikálása**

* Ha a Hyper-V virtuális gépek felügyelete VMM-felhőkben történik, replikálást végezhet másodlagos adatközpontba vagy az Azure Storage-ba.  A replikálás az Azure-ba biztonságos internetkapcsolaton keresztül történik.  A replikálás a másodlagos adatközpontba a helyi hálózaton keresztül történik.
* Ha a Hyper-V virtuális gépet nem a VMM felügyeli, csak az Azure Storage-ba végezhet replikálást.  A replikálás az Azure-ba biztonságos internetkapcsolaton keresztül történik.

**VMWare virtuális gépek replikálása**

* A VMware virtuális gépeket VMware-t futtató másodlagos adatközpontba vagy az Azure Storage-ba replikálhatja.  A replikálás az Azure-ba történhet helyek közötti VPN-en, illetve Azure ExpressRoute-on keresztül vagy biztonságos internetkapcsolaton át. A másodlagos adatközpontba a replikálás az InMage Scout adatcsatornáján keresztül történik.

**A fizikai Windows- és Linux-kiszolgálók replikálása** 

* A fizikai kiszolgálókat replikálhatja másodlagos adatközpontokba vagy az Azure Storage-ba. A replikálás az Azure-ba történhet helyek közötti VPN-en, illetve Azure ExpressRoute-on keresztül vagy biztonságos internetkapcsolaton át. A másodlagos adatközpontba a replikálás az InMage Scout adatcsatornáján keresztül történik.  Az Azure Site Recovery olyan OMS-megoldással rendelkezik, amely megjelenít néhány statisztikát, de az Azure portált kell használnia minden művelethez.

![Az Azure Site Recovery magas szintű architektúrája](media/operations-management-suite-architecture/site-recovery.png)

## <a name="next-steps"></a>Következő lépések
* További tudnivalók a [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) szolgáltatásról.
* További tudnivalók az [Azure Automation](https://azure.microsoft.com/documentation/services/automation) szolgáltatásról.
* További tudnivalók az [Azure Backup](http://azure.microsoft.com/documentation/services/backup) szolgáltatásról.
* További tudnivalók az [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) szolgáltatásról.

