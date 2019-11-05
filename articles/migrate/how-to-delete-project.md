---
title: Azure Migrate projekt törlése
description: Útmutató Azure Migrate projekt létrehozásához és egy Assessment/Migration eszköz hozzáadásához.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512729"
---
# <a name="delete-an-azure-migrate-project"></a>Azure Migrate projekt törlése

Ez a cikk egy [Azure Migrate](migrate-overview.md) -projekt törlését ismerteti.


## <a name="before-you-start"></a>Előkészületek

Projekt törlése előtt:

- Ha töröl egy projektet, a projekt és a felderített gép metaadatai törlődnek.
- Ha csatolt egy Log Analytics munkaterületet a kiszolgáló-értékelési eszközhöz a függőségek elemzéséhez, döntse el, hogy törölni kívánja-e a munkaterületet. 
    - A munkaterület nem törlődik automatikusan. Törölje manuálisan.
    - Győződjön meg arról, hogy a törlés előtt milyen munkaterület van használatban. Ugyanaz a Log Analytics munkaterület több forgatókönyv esetén is használható.
    - A projekt törlése előtt megtalálhatja a munkaterületre mutató hivatkozást **Azure Migrate-** **servers > Azure Migrate-Server Assessment**( **OMS munkaterület**) területen.
    - Ha törölni szeretne egy munkaterületet a projekt törlése után, keresse meg a munkaterületet a megfelelő erőforráscsoporthoz, és kövesse az [alábbi utasításokat](../azure-monitor/platform/delete-workspace.md).


## <a name="delete-a-project"></a>Projekt törlése


1. A Azure Portal nyissa meg azt az erőforráscsoportot, amelyben a projekt létrejött.
2. Az erőforráscsoport lapon válassza a **rejtett típusok megjelenítése**elemet.
3. Válassza ki a projektet és a törölni kívánt erőforrásokat.
    - Azure Migrate projektek erőforrástípus a **Microsoft. migrálása/migrateprojects**.
    - A következő szakaszban tekintse át a felderítéshez, az értékeléshez és az áttelepítéshez létrehozott erőforrásokat egy Azure Migrate projektben.
    - Ha az erőforráscsoport csak a Azure Migrate projektet tartalmazza, akkor törölheti a teljes erőforráscsoportot.
    - Ha törölni szeretne egy projektet a Azure Migrate előző verziójáról, a lépések ugyanazok. Ezeknek a projekteknek az erőforrás-típusa **áttelepítési projekt**.


## <a name="created-resources"></a>Létrehozott erőforrások

Ezek a táblázatok összefoglalják a felderítéshez, értékeléshez és áttelepítéshez létrehozott erőforrásokat egy Azure Migrate projektben.

> [!NOTE]
> Törölje a Key vaultot körültekintően, mert biztonsági kulcsokat tartalmazhat.

### <a name="vmwarephysical-server"></a>VMware/fizikai kiszolgáló

**Erőforrás** | **Típus**
--- | ---
"Appliancename" kV | Key Vault
"Appliancename" webhely | Microsoft. OffAzure/VMwareSites
Projektnév | Microsoft. Migrálás/migrateprojects
"Projektnév" projekt | Microsoft. Migrálás/assessmentProjects
"Projektnév" rsvault | Recovery Services-tároló
"Projektnév"-MigrateVault-* | Recovery Services-tároló
migrateappligwsa* | Tárfiók
migrateapplilsa* | Tárfiók
migrateapplicsa* | Tárfiók
migrateapplikv* | Key Vault
migrateapplisbns16041 | Service Bus-névtér

### <a name="hyper-v-vm"></a>Hyper-V virtuális gép 

**Erőforrás** | **Típus**
--- | ---
Projektnév | Microsoft. Migrálás/migrateprojects
"Projektnév" projekt | Microsoft. Migrálás/assessmentProjects
HyperV * kV | Key Vault
HyperV * hely | Microsoft. OffAzure/HyperVSites
"Projektnév"-MigrateVault-* | Recovery Services-tároló


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan adhat hozzá további [értékelési](how-to-assess.md) és [áttelepítési](how-to-migrate.md) eszközöket. 
