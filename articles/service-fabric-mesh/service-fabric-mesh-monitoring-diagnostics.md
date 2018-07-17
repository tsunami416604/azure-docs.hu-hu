---
title: Monitorozás és diagnosztika az Azure Service Fabric-háló alkalmazásokban |} A Microsoft Docs
description: További tudnivalók figyelése és diagnosztizálása a Service Fabric háló az Azure-ban az alkalmazás.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 79e5622dd73e53854204675b435e99d187a3ab26
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076419"
---
# <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika
Az Azure Service Fabric háló egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a mikroszolgáltatás-alapú alkalmazások üzembe helyezése nélkül kezelése virtuális gépek, tároló, vagy a hálózat. Monitorozás és diagnosztika a Service Fabric-háló kategorizálva be a diagnosztikai adatok három fő típusa:

- Protokoly aplikací – ezek a naplók a tárolóalapú alkalmazásokat, hogyan van kialakítva az alkalmazás alapján vannak meghatározva (pl. docker-naplók)
- Platform-események – a háló releváns a tároló működés, platformról események jelenleg beleértve a tároló aktiválási, Inaktiválási és felmondás.
- Tárolómetrikák - erőforrás kihasználtságát és a teljesítmény-mérőszámok a tárolókhoz (docker statisztika)

Ez a cikk ismerteti a monitorozási és diagnosztikai lehetőségei a legújabb előzetes verziót.

## <a name="application-logs"></a>Alkalmazásnaplók

A docker-naplók megtekintéséhez a az üzembe helyezett tárolókkal, egy tárolóban történik. A Service Fabric-háló alkalmazásmodell az egyes tárolók csomag egy kódot az alkalmazásban. A kód csomaggal társított naplók megtekintéséhez használja a következő parancsot:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> A "az háló servicereplica" paranccsal kérje le a replika nevét. Másodpéldány nevének növekvő számok 0.* a

Megjelenése a szavazóalkalmazás VotingWeb.Code tárolótól naplóinak megtekintése az itt látható:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="next-steps"></a>További lépések
Service Fabric-háló kapcsolatos további információkért olvassa el a áttekintése:
- [Service Fabric-háló áttekintése](service-fabric-mesh-overview.md)
