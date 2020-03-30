---
title: Cisco-adatok csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a Cisco-adatokat az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588399"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>A Cisco ASA csatlakoztatása az Azure Sentinelhez



Ez a cikk bemutatja, hogyan csatlakoztathatja a Cisco ASA-készüléket az Azure Sentinelhez. A Cisco ASA adatösszekötő lehetővé teszi, hogy könnyedén csatlakoztassa a Cisco ASA-naplókat az Azure Sentinelhez, irányítópultokat tekinthet meg, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Cisco ASA azure Sentinel en való használatával további betekintést nyerhet a szervezet internethasználatába, és javíthatja a biztonsági üzemeltetési képességeit. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Cisco ASA-naplók továbbítása a Syslog-ügynöknek

A Cisco ASA nem támogatja a CEF-et, így a naplók at Syslog néven küldi el a rendszer, és az Azure Sentinel-ügynök úgy tudja, hogyan elemezheti őket, mintha CEF-naplók lennének. Konfigurálja a Cisco ASA-t úgy, hogy a Syslog-üzeneteket a Syslog-ügynökön keresztül továbbítsa az Azure-munkaterületre:

1. Nyissa meg [a Syslog üzenetek küldése külső Syslog-kiszolgálóra](https://aka.ms/asi-syslog-cisco-forwarding)lehetőséget, és kövesse a kapcsolat beállításához adott utasításokat. A következő paramétereket használja, amikor a rendszer kéri:
    - Állítsa a **portot** 514-re vagy az ügynökben beállított portra.
    - Állítsa **syslog_ip** az ügynök IP-címére.

1. Ha a Megfelelő sémát szeretné használni a Log `CommonSecurityLog`Analytics szolgáltatásban a Cisco-eseményekhez, keresse meg a keresett fájlt.

1. Folytassa a [3.](connect-cef-verify.md)




## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Cisco ASA-készülékeket az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)


