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
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38750330"
---
## <a name="upgrade-the-mars-agent"></a>A MARS-ügynök frissítése
A Microsoft Azure Recovery Service-(MARS-) ügynök 2.0.9083.0 alábbi verziói az Azure Access Control service (ACS) egy függőségi rendelkezik. 2018-ban az Azure lesz [kivezetjük az Azure Access Control service (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). 2018. március 19., kezdve a MARS-ügynök 2.0.9083.0 alatti összes verzióját a biztonsági mentési hibák fog tapasztalni. Elkerülése, illetve a biztonsági mentési hibák elhárításához [a MARS-ügynök frissítése a legújabb verzióra](https://go.microsoft.com/fwlink/?linkid=229525). Egy MARS-ügynök frissítése igénylő kiszolgálók azonosításához, kövesse a [az Azure Backup-ügynökök frissítése a biztonsági mentés blog](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). A MARS-ügynök segítségével biztonsági másolatot a következő adattípusokat:
- Fájlok 
- A rendszerállapot-adatok
- a System Center DPM biztonsági másolatának az Azure-bA
- Azure Backup Server (MABS)
