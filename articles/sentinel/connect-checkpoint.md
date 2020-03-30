---
title: Ellenőrzőpont-adatok csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathatja az ellenőrzőpont adatait az Azure Sentinelhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588416"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Ellenőrzőpont csatlakoztatása az Azure Sentinelhez



Ez a cikk bemutatja, hogyan csatlakoztathatja a Check Point-készüléket az Azure Sentinelhez. A Check Point adatösszekötő lehetővé teszi, hogy könnyedén csatlakoztassa a Check Point naplók az Azure Sentinel, irányítópultok megtekintéséhez, egyéni riasztások létrehozása, és a vizsgálat javítása. A Check Point használatával az Azure Sentinel további betekintést nyújt a szervezet internethasználatába, és javítja a biztonsági üzemeltetési képességeit. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Ellenőrzőpont-naplók továbbítása a Syslog-ügynöknek

Állítsa be a Check Point-készüléket úgy, hogy a Syslog-üzeneteket CEF formátumban továbbítsa az Azure-munkaterületre a Syslog ügynökön keresztül.

1. Nyissa meg az [Ellenőrzőpont napló exportálása .](https://aka.ms/asi-syslog-checkpoint-forwarding)
1. Görgessen le az **alapszintű telepítéshez,** és kövesse az utasításokat a kapcsolat beállításához az alábbi irányelvek szerint:
   - Állítsa a **Syslog portot** **514-re** vagy az ügynökre beállított portra.
     - Cserélje le a CLI-ben lévő **nevet** és **a célkiszolgáló IP-címét** a Syslog ügynök nevére és IP-címére.
     - Állítsa a formátumot **CEF**formátumra.
1. Ha az R77.30 vagy R80.10 verziót használja, görgessen felfelé a **Telepítés ekhez,** és kövesse az utasításokat a log exportőr telepítéséhez az Ön verziójához.
1. Folytassa a [3.](connect-cef-verify.md)
 

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Check Point-készülékeket az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- [A kapcsolat ellenőrzése](connect-cef-verify.md).
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)


