---
title: A Palo Alto Networks adatainak csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathatja a Palo Alto Networks adatait az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a79b7a1448e1decb377aa0072261df068c366567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588127"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>A Palo Alto networks csatlakoztatása az Azure Sentinelhez



Ez a cikk bemutatja, hogyan csatlakoztathatja a Palo Alto Networks készüléket az Azure Sentinelhez. A Palo Alto Networks adatösszekötő lehetővé teszi, hogy könnyedén csatlakoztassa a Palo Alto Networks naplók az Azure Sentinel, irányítópultok megtekintéséhez, egyéni riasztások létrehozása, és a vizsgálat javítása. A Palo Alto Networks azure Sentinel használatával további betekintést nyújt a szervezet internethasználatába, és javítja a biztonsági üzemeltetési képességeit. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Forward Palo Alto Networks bejelentkezik a Syslog ügynök

Konfigurálja a Palo Alto Networks szolgáltatást úgy, hogy a Syslog-üzeneteket CEF formátumban továbbítsa az Azure-munkaterületre a Syslog ügynökön keresztül:
1.  Látogasson el a [Közös eseményformátum (CEF) konfigurációs útmutatóira,](https://docs.paloaltonetworks.com/resources/cef) és töltse le a készülék típusához tartozó pdf-et. Kövesse az útmutatóban található utasításokat a Palo Alto Networks készülék beállításához a CEF események összegyűjtéséhez. 

1.  A [Syslog figyeléskonfigurálása](https://aka.ms/asi-syslog-paloalto-forwarding) és a 2.

    1. Győződjön meg arról, hogy a **Syslog kiszolgáló formátumát** **BSD**formátumra állította.

       > [!NOTE]
       > A PDF-dokumentum másolási/beillesztési műveletei megváltoztathatják a szöveget, és véletlenszerű karaktereket szúrhatnak be. Ennek elkerülése érdekében másolja a szöveget egy szerkesztőbe, és távolítsa el azokat a karaktereket, amelyek a beillesztés előtt megszakíthatják a naplóformátumot, amint azt a példában is látható.
 
        ![CEF szövegmásolási probléma](./media/connect-cef/paloalto-text-prob1.png)

1. Ha a Megfelelő sémát szeretné használni a Log Analytics szolgáltatásban a Palo Alto Networks eseményekhez, keresse meg a **CommonSecurityLog (CommonSecurityLog)** kifejezést.

1. Folytassa a [3.](connect-cef-verify.md)




## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Palo Alto Networks készülékeit az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)


