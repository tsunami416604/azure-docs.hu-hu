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
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 3c8bf69b745f5dba8c08556908df4d4ae5b5769f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521919"
---
# <a name="supported-platforms"></a>Támogatott platformok 

Ez a lap az Azure Security Center által támogatott platformokat és környezeteket jeleníti meg.

## <a name="combinations-of-environments"></a>Környezetkombinációk<a name="vm-server"></a>

Az Azure Security Center különböző típusú hibrid környezetekben támogatja a virtuális gépeket és kiszolgálókat:

* Csak az Azure
* Azure és helyszíni
* Az Azure és más felhők
* Azure, egyéb felhők és helyszíni

Egy Azure-előfizetésen aktivált Azure-környezet esetén az Azure Security Center automatikusan felderíti az előfizetésen belül üzembe helyezett IaaS-erőforrásokat.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Security Center a [Log Analytics-ügynöktől](../azure-monitor/platform/agents-overview.md#log-analytics-agent)függ. Győződjön meg arról, hogy a gépek en futnak az ügynök támogatott operációs rendszereinek egyikén, az alábbi oldalakon leírtak szerint:

* [Log Analytics ügynök Windows által támogatott operációs rendszerekhez](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Log Analytics ügynök Linux által támogatott operációs rendszerekhez](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

Győződjön meg arról is, hogy a Log Analytics-ügynök [megfelelően van konfigurálva az adatok biztonsági központba küldéséhez](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Ha többet szeretne tudni a Windows és Linux rendszeren elérhető Security Center-szolgáltatásokról, olvassa el [a Szolgáltatások lefedettsége a gépekhez című témakört.](security-center-services.md)

## <a name="managed-virtual-machine-services"></a>Felügyelt virtuálisgép-szolgáltatások<a name="virtual-machine"></a>

Virtuális gépek is jönnek létre egy ügyfél-előfizetés részeként néhány Azure által felügyelt szolgáltatások is, például az Azure Kubernetes (AKS), Az Azure Databricks és így tovább. A Security Center felderíti ezeket a virtuális gépeket is, és a Log Analytics-ügynök telepíthető és konfigurálható, ha egy támogatott operációs rendszer érhető el.

## <a name="cloud-services"></a>Felhőszolgáltatások<a name="cloud-services"></a>

A felhőalapú szolgáltatásban futó virtuális gépek is támogatottak. Csak a felhőszolgáltatások webes és feldolgozói szerepkörök, amelyek éles tárolóhelyeken futnak, figyelt. A felhőszolgáltatásokról az [Azure Cloud Services áttekintése című témakörben](../cloud-services/cloud-services-choose-me.md)olvashat bővebben.

Az Azure Stackben található virtuális gépek védelme is támogatott. A Security Center Azure Stackkel való integrációjáról az [Azure Stack virtuális gépei nek a Security Centerbe című témakörben](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)talál további információt.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [gyűjti a Security Center az adatokat a Log Analytics ügynök használatával.](security-center-enable-data-collection.md)
- Ismerje meg, hogyan [kezeli és védi a Security Center az adatokat.](security-center-data-security.md)
- Ismerje meg, hogyan [tervezheti meg és ismerheti meg az Azure Security Center bevezetésének tervezési szempontjait.](security-center-planning-and-operations-guide.md)