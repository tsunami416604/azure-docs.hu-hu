---
title: Az Azure Backup-ügynök frissítése
description: Ez az információ azt ismerteti, miért az Azure Backup ügynököt, valamint a helyét, a frissítés letöltéséhez.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: 2a969240da6e16f5a5ba76605a6efa2a96f9e4dd
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43285643"
---
## <a name="upgrade-the-mars-agent"></a>A MARS-ügynök frissítése

A Microsoft Azure Recovery Service-(MARS-) ügynök 2.0.9083.0 alábbi verziói az Azure Access Control service (ACS) egy függőségi rendelkezik. A MARS-ügynök van más néven az Azure Backup ügynököt. 2018, az Azure-ban [az Azure Access Control service (ACS) elavult](../articles/active-directory/develop/active-directory-acs-migration.md). 2018. március 19., kezdve a MARS-ügynök 2.0.9083.0 alatti összes verzióját a biztonsági mentési hibák fog tapasztalni. Elkerülése, illetve a biztonsági mentési hibák elhárításához [a MARS-ügynök frissítése a legújabb verzióra](https://go.microsoft.com/fwlink/?linkid=229525). Egy MARS-ügynök frissítése igénylő kiszolgálók azonosításához, kövesse a [MARS-ügynök frissítéséhez szükséges biztonsági mentés blog](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). A MARS-ügynök segítségével biztonsági mentése a fájlok és mappák és a rendszerállapot-adatok az Azure-bA. A System Center DPM és az Azure Backup Server adatainak biztonsági mentése az Azure-bA a MARS-ügynök használatával.
