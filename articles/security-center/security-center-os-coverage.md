---
title: A Azure Security Center által támogatott szolgáltatások és platformok | Microsoft Docs
description: Ez a dokumentum a Azure Security Center által támogatott szolgáltatások és platformok listáját tartalmazza.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/03/2019
ms.author: v-mohabe
ms.openlocfilehash: 02d993d760338356fa29ee58a03215e14d6583f1
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295603"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>A Azure Security Center által támogatott platformok és szolgáltatások

A biztonsági állapot figyelése és javaslatai elérhetők a klasszikus és a Resource Manager-alapú üzemi modellekkel és számítógépekkel létrehozott virtuális gépekhez (VM).

> [!NOTE]
> További információ az Azure [-erőforrások klasszikus és Resource Manager-alapú üzembe helyezési modelljeiről](../azure-classic-rm.md) .
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Az adatgyűjtési ügynököt támogató platformok 

Ez a szakasz felsorolja azokat a platformokat, amelyeken a Azure Security Center ügynök futtatható, és amelyekről adatokat gyűjthet.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Támogatott platformok Windows rendszerű számítógépekhez és virtuális gépekhez
A következő Windows operációs rendszerek támogatottak:

* A Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> A Windows Defender ATP szolgáltatással való integráció csak a Windows Server 2012 R2 és a Windows Server 2016 rendszert támogatja.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Támogatott platformok Linux rendszerű számítógépekhez és virtuális gépekhez

A következő Linux operációs rendszerek támogatottak:

> [!NOTE]
> Mivel a támogatott Linux operációs rendszerek listája folyamatosan változik, ha szeretné, kattintson [ide](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) , hogy megtekintse a támogatott verziók legfrissebb listáját abban az esetben, ha a témakör legutóbbi közzététele óta módosult.

64 bites
* CentOS 6 és 7
* Amazon Linux 2017,09
* Oracle Linux 6 és 7
* Red Hat Enterprise Linux 6. és 7. kiszolgáló
* Debian GNU/Linux 8 és 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS és 18,04 LTS
* SUSE Linux Enterprise Server 12

32 bites
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 és 9
* Ubuntu Linux 14,04 LTS és 16,04 LTS

## <a name="vms-and-cloud-services"></a>Virtuális gépek és Cloud Services
A Cloud Service-ben futó virtuális gépek is támogatottak. Csak az üzemi tárolóhelyeken futó Cloud Services-alapú webes és feldolgozói szerepköröket figyeli a rendszer. További információ a Cloud Services szolgáltatásról: [Az Azure Cloud Services áttekintése](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Támogatott IaaS funkciók

> [!div class="mx-tableFixed"]
> 

|Kiszolgáló|Windows|||Linux|||Díjszabás|
|----|----|----|----|----|----|----|----|
|**Környezet**|**Azure**||**Non-Azure**|**Azure**||**Non-Azure**||
||**Virtuális gép**|**Virtuálisgép-méretezési csoport**||**Virtuális gép**|**Virtuálisgép-méretezési csoport**|
|VMBA-fenyegetések észlelésével kapcsolatos riasztások|✔|✔|✔|✔ (támogatott verziókon)|✔ (támogatott verziókon)|✔|Javaslatok (ingyenes) veszélyforrások észlelése (standard)|
|Hálózati veszélyforrások észlelésével kapcsolatos riasztások|✔|✔|X|✔|✔|X|Standard|
|A Windows Defender ATP-integráció|✔ (támogatott verziókon)|✔ (támogatott verziókon)|✔|X|X|X|Standard|
|Hiányzó javítások|✔|✔|✔|✔|✔|✔|Free|
|Biztonsági konfigurációk|✔|✔|✔|✔|✔|✔|Free|
|Endpoint Protection-Értékelés|✔|✔|✔|X|X|X|Free|
|JIT VM-hozzáférés|✔|X|X|✔|X|X|Standard|
|Adaptív alkalmazásvezérlők|✔|X|✔|✔|X|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|Lemez titkosításának felmérése|✔|✔|X|✔|✔|X|Free|
|Külső gyártótól származó telepítés|✔|X|X|✔|X|X|Free|
|NSG-Értékelés|✔|✔|X|✔|✔|X|Free|
|Fájlok nem észlelhető veszélyforrások észlelése|✔|✔|✔|X|X|X|Standard|
|Hálózati Térkép|✔|✔|X|✔|✔|X|Standard|
|Adaptív hálózati vezérlők|✔|✔|X|✔|✔|X|Standard|
|Szabályozási megfelelőségi irányítópult & jelentések|✔|✔|✔|✔|✔|✔|Standard|
|Javaslatok és veszélyforrások észlelése a Docker által üzemeltetett IaaS-tárolókban|X|X|X|✔|✔|✔|Standard|

### <a name="supported-endpoint-protection-solutions"></a>Támogatott Endpoint Protection-megoldások

A következő táblázat a következőkhöz nyújt mátrixot:
 - Azt határozza meg, hogy használható-e a Azure Security Center az egyes megoldások telepítéséhez.
 - Melyik Endpoint Protection-megoldás Security Center képes felderíteni. Ha a rendszer felderíti az egyik Endpoint Protection-megoldást, Security Center nem javasolja, hogy telepítsen egyet.

További információ az egyes védelemekhez kapcsolódó javaslatok létrehozásáról: [Endpoint Protection Értékelés és javaslatok](security-center-endpoint-protection.md).

| Endpoint Protection (Végpontok védelme)| Platformok | Security Center telepítése | Security Center felderítése |
|------|------|-----|-----|
| Windows Defender (Microsoft-kártevőirtó)| Windows Server 2016| Nincs, az operációs rendszerbe van beépítve| Igen |
| System Center Endpoint Protection (Microsoft-kártevőirtó) | Windows Server 2012 R2, 2012, 2008 R2 (lásd az alábbi megjegyzést) | Bővítmény útján | Igen |
| Trend Micro – Összes verzió | Windows Server termékcsalád  | Nem | Igen |
| Symantec v12.1.1100+| Windows Server termékcsalád  | Nem | Igen |
| McAfee v10+ | Windows Server termékcsalád  | Nem | Igen |
| Kaspersky| Windows Server termékcsalád  | Nem | Nem  |
| Sophos| Windows Server termékcsalád  | Nem | Nem  |

> [!NOTE]
> - A System Center Endpoint Protection (SCEP) Windows Server 2008 R2 rendszerű virtuális gépen való észleléséhez a PowerShell 3,0 (vagy egy felső verzió) után telepíteni kell a SCEP.

## <a name="supported-paas-features"></a>Támogatott Pásti-funkciók


|Szolgáltatás|Javaslatok (ingyenes)|Fenyegetések észlelése (standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure Blob Storage-fiókok|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|Virtuális hálózatok|✔| NA|
|Alhálózatok|✔| NA|
|Hálózati adapterek (NIC-k)|✔| NA|
|NSG-k|✔| NA|
|Subscription|✔ **| ✔|
|Batch|✔| NA|
|Service Fabric|✔| NA|
|Automation-fiók|✔| NA|
|Terheléselosztó|✔| NA|
|Keresés|✔| NA|
|Service Bus|✔| NA|
|Stream Analytics|✔| NA|
|Eseményközpont|✔| NA|
|Logikai alkalmazások|✔| NA|
|Tárfiók|✔| NA|
|Redis|✔| NA|
|A "Lake Analytics"|✔| NA|
|Key Vault|✔| NA|




\*Ezek a funkciók jelenleg nyilvános előzetes verzióban támogatottak.

\*\*A HRE-javaslatok csak a standard előfizetésekhez érhetők el



## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan tervezheti meg [és értelmezheti a Azure Security Center](security-center-planning-and-operations-guide.md)elfogadásához szükséges tervezési szempontokat.
- További információ a [virtuális gépek viselkedési elemzéséről és az összeomlási memóriakép memóriájának elemzéséről Security Centerban](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- [Gyakori kérdések a Azure Security Center használatáról](security-center-faq.md).
- [Blogbejegyzések az Azure biztonsági és megfelelőségi](https://blogs.msdn.com/b/azuresecurity/)funkcióiról.
