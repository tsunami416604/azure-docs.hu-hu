---
title: Zscaler-adatok csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathatja a Zscaler-adatokat az Azure Sentinelhez.
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
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587991"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>A Zscaler internet-hozzáférés csatlakoztatása az Azure Sentinelhez

> [!IMPORTANT]
> A Zscaler adatösszekötő az Azure Sentinel jelenleg nyilvános előzetes verzióban.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Ez a cikk bemutatja, hogyan csatlakoztathatja a Zscaler Internet Access készüléket az Azure Sentinelhez. A Zscaler adatösszekötő lehetővé teszi, hogy könnyedén csatlakoztassa a Zscaler Internet Access (ZIA) naplók at Azure Sentinel, irányítópultok megtekintéséhez, egyéni riasztások létrehozása, és a vizsgálat javítása. A Zscaler azure Sentinel használatával további betekintést nyújt a szervezet internethasználatába, és javítja a biztonsági üzemeltetési képességeit. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>A Zscaler beállítása CEF-üzenetek küldésére

1. A Zscaler-berendezésen be kell állítania ezeket az értékeket, hogy a készülék elküldi a szükséges naplókat a szükséges formátumban az Azure Sentinel Syslog ügynök, a Log Analytics-ügynök alapján. Ezeket a paramétereket a készülékben, mindaddig, amíg azokat is módosíthatja a Syslog démon az Azure Sentinel ügynök.
    - Protokoll = TCP
    - Port = 514
    - Formátum = CEF
    - IP-cím - győződjön meg róla, hogy a CEF üzeneteket az erre a célra kijelölt virtuális gép IP-címére küldje.
 További információt a [Zscaler és az Azure Sentinel telepítési útmutatójában talál.](https://aka.ms/ZscalerCEFInstructions)
 
   > [!NOTE]
   > Ez a megoldás támogatja a Syslog RFC 3164 vagy RFC 5424-et.


1. Ha a megfelelő sémát szeretné használni a Log `CommonSecurityLog`Analytics szolgáltatásban a CEF-eseményekhez, keresse meg a keresett főt.
1. Folytassa a [3.](connect-cef-verify.md)


## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Zscaler Internet Access-t az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)


