---
title: Ellenőrzési ponthoz tartozó adatkapcsolatok összekötése az Azure Sentinel szolgáltatással Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a ellenőrzési pontok az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588416"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Ellenőrzési pont kapcsolódása az Azure Sentinelhez



Ez a cikk azt ismerteti, hogyan csatlakoztatható a ellenőrzési pont berendezése az Azure Sentinelhez. A ellenőrzési pont adatösszekötője lehetővé teszi, hogy könnyedén összekösse a ellenőrzési pontok naplóit az Azure Sentinelrel, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. Az Azure Sentinel-beli ellenőrzési pont használatával több betekintést nyerhet a szervezet internetes használatára, és fokozza a biztonsági üzemeltetési képességeit. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>A bejelentkezéshez szükséges naplók továbbítása a syslog-ügynökhöz

Konfigurálja a ellenőrzési pont készülékét, hogy a syslog-ügynökön keresztül továbbítsa a syslog-üzeneteket CEF formátumban az Azure-munkaterületre.

1. Ugrás a következőre: [ellenőrzési pont naplójának exportálása](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Görgessen le az **alapszintű üzembe helyezéshez** , és kövesse az utasításokat a kapcsolódás beállításához az alábbi irányelvek alapján:
   - Állítsa a **syslog portot** **514** -re vagy az ügynökön beállított portra.
     - Cserélje le a **név** és a **cél-kiszolgáló IP-címet** a parancssori felületre a syslog-ügynök nevével és IP-címével.
     - Állítsa a formátumot **CEF**értékre.
1. Ha R 77.30 vagy R 80.10 verziót használ, görgessen a **telepítésekhez** , és kövesse az utasításokat, és telepítsen egy log exportőrt a verzióra.
1. Folytassa a [3. lépéssel: a kapcsolat ellenőrzése](connect-cef-verify.md).
 

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a ellenőrzési pontok berendezései az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- A [kapcsolat ellenőrzése](connect-cef-verify.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.


