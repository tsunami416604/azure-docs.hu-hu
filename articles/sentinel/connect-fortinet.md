---
title: Fortinet-adatok csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakozhat fortinet adatok at Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aabc95fcb8d3b32b89bb83f4f6892c3d40b03417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588195"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>A Fortinet csatlakoztatása az Azure Sentinelhez



Ez a cikk bemutatja, hogyan csatlakoztathatja a Fortinet-készüléket az Azure Sentinelhez. A Fortinet-adatösszekötő lehetővé teszi, hogy könnyedén csatlakoztassa a Fortinet-naplókat az Azure Sentinelhez irányítópultok megtekintéséhez, egyéni riasztások létrehozásához és a vizsgálat javításához. A Fortinet használata az Azure Sentinelen további betekintést nyújt a szervezet internethasználatába, és javítja a biztonsági üzemeltetési képességeit. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Fortinet-naplók továbbítása a Syslog-ügynöknek

Konfigurálja a Fortinet-et úgy, hogy a Syslog-üzeneteket CEF formátumban továbbítsa az Azure-munkaterületre a Syslog ügynökön keresztül.

1. Nyissa ki a CLI-t a Fortinet készüléken, és futtassa a következő parancsokat:

        config log syslogd setting
        set format cef
        set port 514
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Cserélje le a kiszolgáló **IP-címét** az ügynök IP-címére.
    - Állítsa a **syslog portot** **514-re** vagy az ügynök portkészletét.
    - A CEF formátum korai FortiOS-verziókban való engedélyezéséhez előfordulhat, hogy futtatnia kell a **csv disable**parancskészletet.
 
   > [!NOTE] 
   > További információt a [Fortinet dokumentumtárban](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)talál. Válassza ki a saját verzióját, és használja a **kézikönyvet** és a **naplóüzenet-útmutatót**.

1. Ha a megfelelő sémát szeretné használni az Azure Monitor Log `CommonSecurityLog`Analytics szolgáltatásában a Fortinet-eseményekhez, keresse meg a keresett fájlt.

1. Folytassa a [3.](connect-cef-verify.md)


## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan csatlakoztathatja a Fortinet-készülékeket az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)


