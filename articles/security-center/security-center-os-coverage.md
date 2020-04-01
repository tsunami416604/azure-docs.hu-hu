---
title: Az Azure Security Center által támogatott platformok | Microsoft dokumentumok
description: Ez a dokumentum az Azure Security Center által támogatott platformok listáját tartalmazza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: f9b948714f72ba02a100d9941721f073953bf22a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473242"
---
# <a name="supported-platforms"></a>Támogatott platformok 

## <a name="virtual-machines--servers"></a>Virtuális gépek / szerverek<a name="vm-server"></a>

A Security Center különböző típusú hibrid környezetekben támogatja a virtuális gépeket/ kiszolgálókat:

* Csak az Azure
* Azure és helyszíni
* Az Azure és más felhők
* Azure, egyéb felhők és helyszíni

Egy Azure-előfizetésen aktivált Azure-környezet esetén az Azure Security Center automatikusan felderíti az előfizetésen belül üzembe helyezett IaaS-erőforrásokat.

> [!NOTE]
> A biztonsági funkciók teljes készletének fogadásához telepítve és [megfelelően konfigurálva](security-center-enable-data-collection.md#manual-agent)kell lennie a [Log Analytics Ügynöknek,](../azure-monitor/platform/agents-overview.md#log-analytics-agent)amelyet az Azure Security Center használ.

A következő szakaszok azokat a támogatott kiszolgálói operációs rendszereket sorolják fel, amelyeken az Azure Security Center által használt [Log Analytics-ügynök](../azure-monitor/platform/agents-overview.md#log-analytics-agent)futtatható.

### <a name="windows-server-operating-systems"></a>Windows server operációs rendszerek<a name="os-windows"></a>

|Operációs rendszer|Az Azure Security Center által támogatott|A Microsoft Defender ATP-vel való integráció támogatása|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Ha többet szeretne megtudni a Windows operációs rendszerek fent felsorolt támogatott szolgáltatásairól, olvassa el a [Virtuálisgép/kiszolgáló által támogatott szolgáltatások című témakört.](security-center-services.md#vm-server-features)

### <a name="windows-operating-systems"></a>Windows operációs rendszerek<a name="os-windows (non-server)"></a>

Az Azure Security Center integrálható az Azure-szolgáltatásokkal a Windows-alapú virtuális gépek figyeléséhez és védelméhez.

### <a name="linux-operating-systems"></a>Linux operációs rendszerek<a name="os-linux"></a>

64 bites

* CentOS 6 és 7
* Amazon Linux 2017.09
* Oracle Linux 6 és Oracle Linux 7
* Red Hat Enterprise Linux Server 6 és 7
* Debian GNU/Linux 8 és 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS és 18.04 LTS
* SUSE Linux Enterprise Server 12

32 bites
* CentOS, 6.
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 és 9
* Ubuntu Linux 14.04 LTS és 16.04 LTS

> [!NOTE]
> Mivel a támogatott Linux operációs rendszerek listája folyamatosan változik, ha úgy tetszik, kattintson [ide](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) a támogatott verziók legfrissebb listájának megtekintéséhez, abban az esetben, ha a téma legutóbbi közzététele óta változások történtek.

Ha többet szeretne megtudni a Linux operációs rendszerek által támogatott funkciókról, lásd a fent felsorolt a [Virtuális gép / kiszolgáló által támogatott funkciókat.](security-center-services.md#vm-server-features)

### <a name="managed-virtual-machine-services"></a>Felügyelt virtuálisgép-szolgáltatások<a name="virtual-machine"></a>

Virtuális gépek is jönnek létre egy ügyfél-előfizetés részeként néhány Azure felügyelt szolgáltatások is, például az Azure Kubernetes (AKS), Az Azure Databricks és így tovább. Ezeket a virtuális gépeket az Azure Security Center is felfedezi, és a Log analytics ügynök telepíthető és konfigurálható a támogatott [Windows/Linux operációs rendszerek](#os-windows)szerint , a fent felsorolt.

### <a name="cloud-services"></a>Felhőszolgáltatások<a name="cloud-services"></a>

A felhőalapú szolgáltatásban futó virtuális gépek is támogatottak. Csak a felhőszolgáltatások webes és feldolgozói szerepkörök, amelyek éles tárolóhelyeken futnak, figyelt. A felhőszolgáltatásokról az [Azure Cloud Services áttekintése című témakörben](../cloud-services/cloud-services-choose-me.md)olvashat bővebben.

Az Azure Stackben található virtuális gépek védelme is támogatott. A Security Center Azure Stackkel való integrációjáról az [Azure Stack virtuális gépei nek a Security Centerbe című témakörben](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)talál további információt.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [gyűjti a Security Center az adatokat és a Log Analytics-ügynököt.](security-center-enable-data-collection.md)
- Ismerje meg, hogyan [kezeli és védi a Security Center az adatokat.](security-center-data-security.md)
- Ismerje meg, hogyan [tervezheti meg és ismerheti meg az Azure Security Center bevezetésének tervezési szempontjait.](security-center-planning-and-operations-guide.md)
- Ismerje meg [a különböző felhőkörnyezetekben elérhető funkciókat.](security-center-services.md)
- További információ a [Windows- és Linux-gépek fenyegetésvédelmi](threat-protection.md#windows-machines)szolgáltatásáról az Azure Security Centerben.
