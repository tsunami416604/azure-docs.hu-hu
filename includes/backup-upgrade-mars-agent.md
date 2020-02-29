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
ms.openlocfilehash: 227ec87081817b273b66684e31a0ebd0e11134a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197120"
---
## <a name="upgrade-the-mars-agent"></a>A MARS-ügynök frissítése

A 2.0.9083.0 alábbi Microsoft Azure Recovery Services (MARS) ügynökének verziói az Azure Access Control szolgáltatástól függenek. A MARS-ügynököt Azure Backup ügynöknek is nevezzük.

A 2018-es verzióban [a Microsoft elavulta az Azure Access Control szolgáltatást](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). 2018. március 19-én kezdődően a MARS-ügynök összes verziója a 2.0.9083.0 alatt biztonsági mentési hibákat fog tapasztalni. A biztonsági mentési hibák elkerülése vagy elhárítása érdekében [frissítse a Mars-ügynököt a legújabb verzióra](https://go.microsoft.com/fwlink/?linkid=229525). A MARS-ügynök frissítését igénylő kiszolgálók azonosításához kövesse a [biztonsági mentési blogban található lépéseket a Mars-ügynökök frissítéséhez](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/).

A MARS-ügynök a fájlok és mappák, valamint a rendszerállapot-információknak az Azure-ba történő biztonsági mentésére szolgál. A System Center DPM és Azure Backup Server a MARS-ügynök használatával készít biztonsági mentést az Azure-ba.
