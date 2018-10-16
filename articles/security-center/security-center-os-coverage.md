---
title: Funkciók és az Azure Security Center által támogatott platformok |} A Microsoft Docs
description: Ez a dokumentum az a funkciók és az Azure Security Center által támogatott platformok listáját tartalmazza.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 279818e6b43e53206deb9e33591f75ef381a8962
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319982"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Platformok és az Azure Security Center által támogatott funkciók

Biztonsági állapot monitorozása és javaslatok érhetők el a virtuális gépek (VM), mind a klasszikus és Resource Manager üzembe helyezési modellek és számítógépek használatával létrehozott.

> [!NOTE]
> Tudjon meg többet a [klasszikus és Resource Manager üzembe helyezési modellek](../azure-classic-rm.md) az Azure-erőforrásokhoz.
>
>

## <a name="supported-platforms"></a>Támogatott platformok 

Ez a szakasz felsorolja a platformokat, amelyeken az Azure Security Center-ügynök futtathatja, és, amelyről adatokat tudjon gyűjteni.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows-számítógépek és virtuális gépek által támogatott platformok
A támogatott Windows operációs rendszerek:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux rendszerű számítógépek és virtuális gépek által támogatott platformok
Támogatott Linux operációs rendszerek:

* Ubuntu verziók 12.04 LTS, 14.04 LTS, 16.04 LTS
* Debian verziók 6, 7, 8, 9
* CentOS verziók 5, 6, 7
* Red Hat Enterprise Linux (RHEL) verziók 5, 6, 7
* SUSE Linux Enterprise Server (SLES) verziók 11, 12
* Oracle Linux-verziók 5, 6, 7
* Amazon Linux 2012.09 2017-ig
* Openssl 1.1.0-s csak támogatott x86_64 platformokon (64 bites)

> [!NOTE]
> Virtuális gép működés elemzése még nem érhető el Linux operációs rendszerekhez.
>
>

## <a name="vms-and-cloud-services"></a>Virtuális gépek és Cloud Services
Egy cloud service-ben futó virtuális gépek is támogatottak. Csak a felhőszolgáltatások tárhelyek figyelt éles üzemben futó webes és feldolgozói szerepköröket. Felhőszolgáltatás kapcsolatos további információkért lásd: [Cloud Services – áttekintés](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Támogatott IaaS-szolgáltatások

> [!div class="mx-tableFixed"]
> 

|Kiszolgáló|Windows||Linux||
|----|----|----|----|----|
|Környezet|Azure|Nem Azure|Azure|Nem Azure|
|VMBA fenyegetésészlelési riasztások|✔|✔|✔ (a támogatott verziók)|✔|
|A hálózati alapú fenyegetésészlelés riasztásai|✔|X|✔|X|
|A Windows Defender ATP-integráció *|✔ (a támogatott verziók)|✔|X|X|
|Hiányzó javítások|✔|✔|✔|✔|
|Biztonsági konfigurációk|✔|✔|✔|✔|
|Kártevőirtó|✔|✔|X|X|
|Virtuális gépek igény szerinti elérése|✔|X|✔|X|
|Adaptív alkalmazásvezérlők|✔ (csak Azure)|X|X|X|
|FIM|✔|✔|✔|✔|
|Disk encryption (Lemeztitkosítás)|✔|X|✔|X|
|Harmadik féltől származó központi telepítési|✔|X|✔|X|
|NSG-k|✔|X|✔|X|
|Filess V1|✔|✔|X|X|
|Hálózati térkép létrehozásához|✔|X|✔|X|
|Az adaptív hálózati biztonságának megerősítése|✔|X|✔|X|

* Nyilvános előzetes verziója jelenleg támogatja ezeket a szolgáltatásokat.


## <a name="supported-paas-features"></a>Támogatott PaaS-szolgáltatások


|Szolgáltatás|Javaslatok|Fenyegetések észlelése|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL *|✔| ✔|
|MySQL *|✔| ✔|
|A BLOB storage fiókok *|✔| ✔|
|App Services|✔| ✔|
|Felhőszolgáltatások|✔| X|
|Redis Cache|✔| X|
|A Service fabric|✔| X|
|Azure Automation|✔| X|
|Data Lake |✔| X|
|Key Vault|✔| X|
|A Service bus|✔| X|
|Stream analytics|✔| X|
|Batch|✔| X|
|Logikai alkalmazások|✔| X|
|Virtuális hálózatok|✔| NA|
|Alhálózatok|✔| NA|
|Hálózati adapterek (NIC-k)|✔| ✔|
|NSG-k|✔| NA|
|Előfizetés|✔| ✔|

* Nyilvános előzetes verziója jelenleg támogatja ezeket a szolgáltatásokat.

## <a name="next-steps"></a>További lépések

- [Az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) – ismerje meg, hogyan tervezheti meg és a tervezési szempontokat az Azure Security Center elfogadása
- [Biztonsági riasztások típus szerint az Azure Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis) – további tudnivalók a virtuális gép működésének elemzése és az összeomlási memóriakép memória elemzése a Security Centerben
- [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center – gyakran ismételt kérdések) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
- [Az Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi
