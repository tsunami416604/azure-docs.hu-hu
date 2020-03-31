---
title: Azure Migrate-projekt eltávolítása
description: Bemutatja, hogyan hozhat létre egy Azure Migrate projektet, és hogyan adhat hozzá értékelési/áttelepítési eszközt.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512729"
---
# <a name="delete-an-azure-migrate-project"></a>Azure Migrate-projekt eltávolítása

Ez a cikk ismerteti, hogyan törölheti az [Azure Migrate](migrate-overview.md) projekt.


## <a name="before-you-start"></a>Előkészületek

Projekt törlése előtt:

- Amikor töröl egy projektet, a projekt és a felderített számítógép-metaadatok törlődnek.
- Ha csatolt egy Log Analytics-munkaterületet a Kiszolgálóértékelési eszközhöz függőségelemzésre, döntse el, hogy törölni kívánja-e a munkaterületet. 
    - A munkaterület nem törlődik automatikusan. Törölje manuálisan.
    - A törlés előtt ellenőrizze, hogy milyen munkaterületet használ. Ugyanaz a Log Analytics-munkaterület több forgatókönyvhöz is használható.
    - A projekt törlése előtt az **OMS-munkaterület**csoportban található egy hivatkozás az **Azure Migrate – Servers** > **Azure Migrate – Server Assessment**területén.
    - Ha a projekt törlése után törölni szeretne egy munkaterületet, keresse meg a munkaterületet a megfelelő erőforráscsoportban, és kövesse [az alábbi utasításokat.](../azure-monitor/platform/delete-workspace.md)


## <a name="delete-a-project"></a>Projekt törlése


1. Az Azure Portalon nyissa meg azt az erőforráscsoportot, amelyben a projekt et létrehozták.
2. Az erőforráscsoport lapon válassza a **Rejtett típusok megjelenítése**lehetőséget.
3. Jelölje ki a törölni kívánt projektet és a kapcsolódó erőforrásokat.
    - Az Azure Migrate projektek erőforrástípusa a **Microsoft.Migrate/migrateprojects**.
    - A következő szakaszban tekintse át a felderítés, értékelés és áttelepítés egy Azure Migrate projektben létrehozott erőforrásokat.
    - Ha az erőforráscsoport csak az Azure Migrate projektet tartalmazza, törölheti a teljes erőforráscsoportot.
    - Ha törölni szeretne egy projektet az Azure Migrate korábbi verziójából, a lépések megegyeznek. Ezeknek a projekteknek az erőforrástípusa az **Áttelepítési projekt.**


## <a name="created-resources"></a>Létrehozott erőforrások

Ezek a táblák összegezik a felderítéshez, értékeléshez és áttelepítéshez létrehozott erőforrásokat egy Azure Migrate projektben.

> [!NOTE]
> Törölje a key vault óvatosan, mert tartalmazhat biztonsági kulcsokat.

### <a name="vmwarephysical-server"></a>VMware/fizikai kiszolgáló

**Erőforrás** | **Típus**
--- | ---
"Készüléknév"kv | Key Vault
"Appliancename"oldal | Microsoft.OffAzure/VMware Webhelyek
"Projektnév" | Microsoft.Áttelepítése/áttelepítéseprojektek
"ProjectName" projekt | Microsoft.Migrate/assessmentProjektek
"ProjectName"rsvault | Recovery Services-tároló
"ProjectName"-MigrateVault-* | Recovery Services-tároló
migrateappligwsa* | Tárfiók
migrateapplilsa* | Tárfiók
migrateapplicsa* | Tárfiók
migrateapplikv* | Key Vault
migrateapplisbns16041 | Service Bus-névtér

### <a name="hyper-v-vm"></a>Hyper-V virtuális gép 

**Erőforrás** | **Típus**
--- | ---
"Projektnév" | Microsoft.Áttelepítése/áttelepítéseprojektek
"ProjectName" projekt | Microsoft.Migrate/assessmentProjektek
HiperV*kv | Key Vault
HyperV*Webhely | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Recovery Services-tároló


## <a name="next-steps"></a>További lépések

További információ további [értékelési](how-to-assess.md) és [áttelepítési](how-to-migrate.md) eszközök hozzáadásáról. 
