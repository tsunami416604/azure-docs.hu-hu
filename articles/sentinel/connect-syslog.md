---
title: Syslog-adatbázis összekötése az Azure Sentinel előzetes verziójával | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható a syslog-adatbázis az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: fef9fa128d2ebb84fb82579f254735fdb9aa7ee2
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881065"
---
# <a name="connect-your-external-solution-using-syslog"></a>Külső megoldás összekötése a syslog használatával

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bármely helyszíni berendezés csatlakoztatható, amely támogatja a syslog szolgáltatást az Azure Sentinel számára. Ezt egy, a készülék és az Azure Sentinel közötti linuxos gépen alapuló ügynök használatával végezheti el. Ha a linuxos gépe az Azure-ban található, a naplókat a készülékről vagy alkalmazásból egy dedikált, az Azure-ban létrehozott munkaterületre továbbíthatja, és összekapcsolhatók. Ha a Linux rendszerű számítógép nem az Azure-ban található, a naplókat a készülékről egy dedikált helyszíni virtuális gépre vagy gépre helyezheti át, amelyre telepíti a Linux-ügynököt. 

> [!NOTE]
> Ha a készülék támogatja a syslog CEF, a kapcsolat teljesebb, és ezt a beállítást kell választania, és követnie kell az [adatok CEF-ból való csatlakoztatásának](connect-common-event-format.md)utasításait.

## <a name="how-it-works"></a>Működés

Syslog-esemény naplózása protokoll, amely Linux közös. Alkalmazások küld üzeneteket, amelyek a helyi számítógépen tárolt vagy a Syslog-gyűjtő lett elküldve. A Linux rendszerhez készült Log Analytics-ügynök telepítésekor a helyi syslog démont úgy konfigurálja, hogy továbbítsa az üzeneteket az ügynöknek. Az ügynök ezután elküldi az üzenetet, hogy Azure Monitor, ahol létrejön egy megfelelő rekord.

További információ: [syslog-](../azure-monitor/platform/data-sources-syslog.md)adatforrások Azure monitorban.

> [!NOTE]
> Az ügynök több forrásból is gyűjthet naplókat, de a dedikált proxykiszolgálón kell telepítenie.

## <a name="connect-your-syslog-appliance"></a>A syslog-berendezés összekötése

1. Az Azure Sentinel-portálon válassza  az adatösszekötők lehetőséget, és válassza ki a **syslog** -sort a táblázatban, és a syslog ablaktáblán kattintson a jobb oldalon található **összekötő lap megnyitása**lehetőségre.
2. Ha a linuxos gépe az Azure-ban található, válassza az **ügynök letöltése és telepítése az Azure Linux rendszerű virtuális gépen**lehetőséget. A virtuális gépek ablakban válassza ki azokat a gépeket, amelyekre telepíteni kívánja az ügynököt, majd kattintson a felül található **Kapcsolódás** lehetőségre.
1. Ha a Linux rendszerű gép nem az Azure-ban található, válassza az **ügynök letöltése és telepítése a Linuxon nem Azure-beli gépen**lehetőséget. A **közvetlen ügynök** ablakban másolja a parancsot az **ügynök letöltése és** előkészítése a Linux rendszerhez, és futtassa azt a gépen. 
1. A syslog-összekötő beállítása ablakban a **naplók konfigurálása** területen kövesse az alábbi utasításokat:
    1. Kattintson a hivatkozásra a **munkaterület speciális beállítások konfigurációjának megnyitásához**. 
    1. Válassza **az**adatelemet, majd a **syslog**elemet.
    1. Ezután a táblázatban adja meg, hogy mely létesítményeket szeretné összegyűjteni a syslog. Adja hozzá vagy válassza ki azokat a létesítményeket, amelyeket a syslog-berendezés tartalmaz a naplójának fejlécében. Ezt a konfigurációt a syslog-d-ben a következő mappában tekintheti meg:/etc/rsyslog.d/Security-config-omsagent.conf, és r-syslog a/etc/syslog-ng/Security-config-omsagent.conf. alatt 
       > [!NOTE]
       > Ha bejelöli azt a jelölőnégyzetet, amely az **alábbi konfigurációt alkalmazza a saját gépekre**, akkor ez a konfiguráció a munkaterülethez csatlakozó összes linuxos gépen érvényes lesz. Ezt a konfigurációt a syslog-gépen a következő helyen tekintheti meg: 
1. Kattintson **a megnyomva gombra a konfiguráció**panel megnyitásához.
1. Válassza **az adatelem** , majd a **syslog**lehetőséget.
   - Győződjön meg arról, hogy a syslog által küldött összes létesítmény szerepel a táblázatban. Állítsa be a súlyosságot minden olyan létesítmény esetében, amelyet figyelni fog. Kattintson az **Alkalmaz** gombra.
1. Győződjön meg arról, hogy a syslog-gépen a létesítményeket küldi el. 

1. A syslog-naplók Log Analytics vonatkozó sémájának használatához keresse meg a **syslog**kifejezést.
1. A következő témakörben leírt Kusto függvénnyel elemezheti a syslog-üzeneteket, majd új Log Analytics-függvényként mentheti őket, és a függvényt új adattípusként használhatja: [functions in Azure monitor log lekérdezésekben](../azure-monitor/log-query/functions.md) .




## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan kapcsolódhat a syslog helyszíni készülékekhez az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
