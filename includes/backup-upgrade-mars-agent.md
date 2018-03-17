---
title: "Az Azure Backup szolgáltatás ügynökének frissítése"
description: "Ezek az információk az Azure Backup szolgáltatás ügynökének miért és letöltéséről a frissítés ismerteti."
services: backup
cloud: 
suite: 
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
## <a name="upgrade-the-mars-agent"></a>A MARS-ügynökök frissítése
A Microsoft Azure Recovery szolgáltatáshoz (MARS) ügynök 2.0.9083.0 alábbi verzióit egy függőséget az Azure Access Control service (ACS) rendelkezik. 2018 az Azure lesz [az Azure Access Control service (ACS) érvényteleníthető](../articles/active-directory/develop/active-directory-acs-migration.md). 2018. március 19., kezdve a MARS agent 2.0.9083.0 alatti összes verziója a biztonsági mentési hibák fog tapasztalni. Hárítsa el a biztonsági mentési hibák, vagy elkerülése [frissítése a legújabb verzióra a MARS Agent](https://go.microsoft.com/fwlink/?linkid=229525). A MARS Agent frissítése szükséges kiszolgálók azonosításához, kövesse a [Azure Backup-ügynökök frissítése a biztonsági mentés blogbejegyzésben](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). A MARS agent használatos biztonsági mentéséhez a következő adattípusokat az Azure-bA:
- Fájlok 
- A rendszerállapot-adatok
- a System Center a DPM biztonsági mentése az Azure-bA
- Azure Backup Server (MABS)
