---
title: A Azure Backup-ügynök frissítése
description: Ez az információ azt ismerteti, hogy miért érdemes frissíteni a Azure Backup ügynököt, és hova kell letölteni a frissítést.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: c22fbcd07286ddffedd8fc2fdc12017b9338d7f7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161841"
---
## <a name="upgrade-the-mars-agent"></a>A MARS-ügynök frissítése

A (z) 2.0.9083.0 alatti Microsoft Azure Recovery Service-(MARS-) ügynök verziója az Azure Access Control szolgáltatással (ACS) függ. A MARS-ügynököt Azure Backup ügynöknek is nevezzük. 2018-ben [Az Azure elavult az azure Access Control Service (ACS) szolgáltatásban](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). 2018. március 19-én kezdődően a MARS-ügynök összes verziója a 2.0.9083.0 alatt biztonsági mentési hibákat fog tapasztalni. A biztonsági mentési hibák elkerülése vagy elhárítása érdekében [frissítse a Mars-ügynököt a legújabb verzióra](https://go.microsoft.com/fwlink/?linkid=229525). A MARS-ügynök frissítését igénylő kiszolgálók azonosításához kövesse a [biztonsági mentési blogban található lépéseket a Mars-ügynökök frissítéséhez](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). A MARS-ügynök a fájlok és mappák, valamint a rendszerállapot-információknak az Azure-ba történő biztonsági mentésére szolgál. A System Center DPM és Azure Backup Server a MARS-ügynök használatával készít biztonsági mentést az Azure-ba.
