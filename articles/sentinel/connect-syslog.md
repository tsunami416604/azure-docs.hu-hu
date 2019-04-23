---
title: Syslog-adatot csatlakozhat az Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: Ismerje meg, hogyan kell csatlakozni a Syslog-adatot az Azure-Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 9144f20eae5a925ad88054e972c99ae024bcefe3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795729"
---
# <a name="connect-your-external-solution-using-syslog"></a>A Syslog használatával külső megoldás csatlakoztatása

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bármely helyszíni berendezés, amely támogatja az Azure-Sentinel Syslog kapcsolódhat. Ez a készüléket és a Sentinel-Azure közötti Linux gépen alapuló ügynök használatával történik. Ha a Linux-gép az Azure-ban, streamelheti a készülék vagy az alkalmazás létrehozása az Azure-ban, és csatlakoztassa egy dedikált munkaterület a naplókat. Ha a Linux-gép nem az Azure-ban, streamelheti a naplókat az a készülék egy dedikált a helyszíni virtuális gép vagy gép, amelyre a Linuxhoz készült ügynök telepítése. 

> [!NOTE]
> Ha a készülék támogatja a Syslog CEF, a kapcsolat teljes körű és kell ezt a lehetőséget, és kövesse a [adatok összekapcsolása a CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Működés

Syslog-kapcsolat Linux-ügynök használatával valósítható meg. Alapértelmezés szerint a Linux-ügynök fogadja az eseményeket a Syslog démonból UDP-n keresztül, de azokban az esetekben, ahol Linuxos gépek várhatóan nagy mennyiségű, a Syslog-események gyűjtése, például amikor egy Linux-ügynök kapja az események más eszközökről, úgy módosul, hogy a konfiguráció a Syslog-démont és az ügynök közötti TCP átviteli használja.

## <a name="connect-your-syslog-appliance"></a>Csatlakozás a Syslog-berendezés

1. Az Azure-Sentinel-portálon válassza **adatösszekötők** , és válassza a **Syslog** csempére.
2. Ha a Linux-gép nem Azure-ban, töltse le és telepítse az Azure-Sentinel **Linux-ügynök** a készüléken. 
1. Ha dolgozik, az Azure-ban, válasszon vagy hozzon létre egy virtuális Gépet, amely dedikált Syslog-üzenetek fogadása az Azure Sentinel-munkaterületen belül. Válassza ki a virtuális gép Azure Sentinel-munkaterületek **Connect** a bal oldali ablaktábla tetején.
3. Kattintson a **konfigurálásáról a csatlakoztatni kívánt** vissza a a Syslog-összekötő telepítése. 
4. Kattintson a **itt nyomja meg a konfigurációs panel megnyitásához**.
1. Válassza ki **adatok** , majd **Syslog**.
   - Győződjön meg arról, hogy küld minden létesítmény szerint Syslog a táblában. Az egyes létesítmény kívánja figyelni, és állítsa a súlyosság. Kattintson az **Alkalmaz** gombra.
1. A Syslog-gépen ellenőrizze, hogy ezekben a létesítményekben küld-e. 

3. A megfelelő sémát használ a Log Analytics a Syslog-naplók, keresse meg **Syslog**.




## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni, hogy a Syslog helyszíni berendezések Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
