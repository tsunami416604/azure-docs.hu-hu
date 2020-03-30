---
title: Az Azure biztonsági mentési ügynök frissítése
description: Ez az információ ismerteti, hogy miért kell frissíteni az Azure backup ügynök, és hol kell letölteni a frissítést.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673191"
---
## <a name="upgrade-the-mars-agent"></a>A MARS-ügynök frissítése

A 2.0.9083.0 alatti Microsoft Azure Recovery Services (MARS) ügynök verziói az Azure Access Control szolgáltatástól függenek. A MARS-ügynök is nevezik az Azure backup ügynök.

2018-ban a Microsoft [elavultak az Azure Access Control szolgáltatással.](../articles/active-directory/azuread-dev/active-directory-acs-migration.md) 2018. március 19-től a MARS-ügynök 2.0.9083.0 alatti összes verziója biztonsági mentési hibákat fog tapasztalni. A biztonsági mentési hibák elkerülése vagy megoldása érdekében [frissítse marsügynökét a legújabb verzióra.](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent) A MARS-ügynök frissítését igénylő kiszolgálók azonosításához kövesse [a Microsoft Azure Recovery Services (MARS) ügynök frissítése](../articles/backup/upgrade-mars-agent.md)című részben leírt lépéseket.

A MARS-ügynök fájlok és mappák, valamint rendszerállapot-adatok Azure-ba történő biztonsági biztonsági másolatot. A System Center DPM és az Azure Backup Server a MARS-ügynök segítségével készítsen biztonsági másolatot az Azure-ba.
