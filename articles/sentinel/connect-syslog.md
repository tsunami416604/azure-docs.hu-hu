---
title: Syslog-adatbázis összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható a syslog-adatbázis az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: b2be563efa3c09cffaf14dec2b871f3881af1a7a
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240052"
---
# <a name="connect-your-external-solution-using-syslog"></a>Külső megoldás összekötése a syslog használatával

Bármely helyszíni berendezés csatlakoztatható, amely támogatja a syslog szolgáltatást az Azure Sentinel számára. Ezt egy, a készülék és az Azure Sentinel közötti linuxos gépen alapuló ügynök használatával végezheti el. Ha a linuxos gépe az Azure-ban található, a naplókat a készülékről vagy alkalmazásból egy dedikált, az Azure-ban létrehozott munkaterületre továbbíthatja, és összekapcsolhatók. Ha a Linux rendszerű számítógép nem az Azure-ban található, a naplókat a készülékről egy dedikált helyszíni virtuális gépre vagy gépre helyezheti át, amelyre telepíti a Linux-ügynököt. 

> [!NOTE]
> Ha a készülék támogatja a syslog CEF, a kapcsolat teljesebb, és ezt a beállítást kell választania, és követnie kell az [adatok CEF-ból való csatlakoztatásának](connect-common-event-format.md)utasításait.

## <a name="how-it-works"></a>Működés

Syslog-esemény naplózása protokoll, amely Linux közös. Alkalmazások küld üzeneteket, amelyek a helyi számítógépen tárolt vagy a Syslog-gyűjtő lett elküldve. A Linux rendszerhez készült Log Analytics-ügynök telepítésekor a helyi syslog démont úgy konfigurálja, hogy továbbítsa az üzeneteket az ügynöknek. Az ügynök ezután elküldi az üzenetet, hogy Azure Monitor, ahol létrejön egy megfelelő rekord.

További információ: [syslog-adatforrások Azure monitorban](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> Az ügynök több forrásból is gyűjthet naplókat, de a dedikált proxykiszolgálón kell telepítenie.

## <a name="connect-your-syslog-appliance"></a>A syslog-berendezés összekötése

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd válassza ki a **syslog** -összekötőt.

2. A **syslog** panelen válassza az **összekötő lap megnyitása**lehetőséget.

3. A Linux-ügynök telepítése:
    
    - Ha a linuxos virtuális gép az Azure-ban található, válassza az **ügynök letöltése és telepítése az Azure Linux virtuális gépen**lehetőséget. A **Virtual Machines (virtuális gépek** ) panelen válassza ki azokat a virtuális gépeket, amelyekre telepíteni szeretné az ügynököt, majd kattintson a **Kapcsolódás**elemre.
    - Ha a Linux rendszerű gép nem az Azure-ban található, válassza az **ügynök letöltése és telepítése a Linuxon nem Azure-beli gépen**lehetőséget. A **Direct Agent (közvetlen ügynök** ) panelen másolja a **letöltéshez és** a BEvezetéshez szükséges ügynököt a Linux rendszerhez, és futtassa azt a számítógépen. 
    
   > [!NOTE]
   > Győződjön meg arról, hogy a szervezet biztonsági házirendjének megfelelően konfigurálja a számítógépek biztonsági beállításait. Beállíthatja például, hogy a hálózati beállítások összhangban legyenek a szervezete hálózati biztonsági házirendjével, és a démon portjait és protokollait a biztonsági követelményekkel összhangba lehessen állítani.

4. Válassza **a munkaterület speciális beállítások konfiguráció megnyitása**lehetőséget.

5. A **Speciális beállítások** **panelen válassza ki az** > **adatsyslog**elemet. Ezután adja hozzá az összekötőhöz tartozó létesítményeket a gyűjtéshez.
    
    Adja hozzá azokat a létesítményeket, amelyeket a syslog-berendezés tartalmaz a naplójának fejlécében. Ezt a konfigurációt megtekintheti a syslog **-d** `/etc/rsyslog.d/security-config-omsagent.conf` -ben a mappában, és az **r-syslog** -ból `/etc/syslog-ng/security-config-omsagent.conf`.
    
    Ha rendellenes SSH bejelentkezési észlelést szeretne használni a gyűjtött adatokkal, adja hozzá az **Auth** és a **authpriv**. További részletekért tekintse meg a [következő szakaszt](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) .

6. Ha hozzáadta az összes figyelni kívánt létesítményt, és kiigazította az egyes súlyossági beállításokat, jelölje be az **alábbi konfiguráció alkalmazása a saját gépekre**jelölőnégyzetet.

7. Kattintson a **Mentés** gombra. 

8. Győződjön meg arról, hogy a syslog készülékén a megadott létesítményeket küldi el.

9. A syslog-naplók Azure Monitor vonatkozó sémájának használatához keresse meg a **syslog**kifejezést.

10. A syslog-üzenetek elemzéséhez használhatja a [függvények használata Azure monitor napló lekérdezésekben](../azure-monitor/log-query/functions.md) című témakörben leírt Kusto függvényt. Ezután új Log Analytics függvényként mentheti őket új adattípusként való használatra.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>A syslog-összekötő konfigurálása rendellenes SSH-bejelentkezés észleléséhez

> [!IMPORTANT]
> A rendellenes SSH bejelentkezési észlelés jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Sentinel gépi tanulást (ML) alkalmazhat a syslog-adatokra a rendellenes Secure Shell-(SSH-) bejelentkezési tevékenységek azonosításához. A forgatókönyvek a következők:

- Lehetetlen utazás – ha két sikeres bejelentkezési esemény következik be két olyan helyről, amely nem érhető el a két bejelentkezési esemény időkeretén belül.
- Váratlan hely – a sikeres bejelentkezési esemény előfordulási helye gyanús. Például a hely nem látható a közelmúltban.
 
Az észleléshez a syslog-adatösszekötő adott konfigurációja szükséges: 

1. Az előző eljárás 5. lépésében ellenőrizze, hogy az **Auth** és a **authpriv** is ki van-e választva a figyeléshez. Tartsa meg a súlyossági beállítások alapértelmezett beállításait, hogy azok mind ki legyenek választva. Példa:
    
    > [!div class="mx-imgBorder"]
    > ![A rendellenes SSH bejelentkezési észleléshez szükséges szolgáltatások](./media/connect-syslog/facilities-ssh-detection.png)

2. A syslog-adatok gyűjtéséhez elegendő idő szükséges. Ezután navigáljon az **Azure Sentinel-logs**elemhez, és másolja és illessze be a következő lekérdezést:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    Szükség esetén módosítsa az **időtartományt** , majd válassza a **Futtatás**lehetőséget.
    
    Ha az eredményül kapott darabszám nulla, erősítse meg az összekötő konfigurációját, és hogy a figyelt számítógépek sikeres bejelentkezési tevékenységet hajtanak végre a lekérdezéshez megadott időszakra vonatkozóan.
    
    Ha az eredményül kapott darabszám nagyobb nullánál, a syslog-adatok alkalmasak a rendellenes SSH-bejelentkezések észlelésére. Ezt az észlelést az **Analytics** >  -**szabály sablonjai** >  **(előzetes verzió) rendellenes SSH-bejelentkezések észlelésével**engedélyezheti.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan kapcsolódhat a syslog helyszíni készülékekhez az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
