---
title: Syslog-adatok csatlakoztatása az Azure Sentinelhez | Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathatja a Syslog-adatokat az Azure Sentinelhez.
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
ms.openlocfilehash: 73fd55fc24fd94dc88bba2f591c32480f77c7d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588076"
---
# <a name="connect-your-external-solution-using-syslog"></a>Csatlakoztassa külső megoldását a Syslog segítségével

A Syslog szolgáltatást támogató bármely helyszíni készüléket csatlakoztathatja az Azure Sentinelhez. Ez a készülék és az Azure Sentinel közötti Linux-gépen alapuló ügynök használatával történik. Ha a Linux-gép az Azure-ban van, streamelheti a naplókat a készülékről vagy alkalmazásból egy dedikált munkaterületre, amelyet az Azure-ban hoz létre, és csatlakoztathatja. Ha a Linux-gép nem az Azure-ban, streamelheti a naplókat a készülékről egy dedikált helyszíni virtuális gép vagy gép, amelyre telepíti a Linux-ügynök. 

> [!NOTE]
> Ha a készülék támogatja a Syslog CEF-et, a kapcsolat teljesebb, és válassza ezt a lehetőséget, és kövesse a [CEF adatainak csatlakoztatása](connect-common-event-format.md)című részben található utasításokat.

## <a name="how-it-works"></a>Működés

A Syslog egy olyan eseménynaplózási protokoll, amely a Linux közös. Az alkalmazások olyan üzeneteket küldenek, amelyek a helyi számítógépen tárolhatók, vagy egy Syslog gyűjtőnek kézbesíthetők. A Linuxos Log Analytics-ügynök telepítésekor a helyi Syslog démont konfigurálja az ügynöknek küldött üzenetek továbbítására. Az ügynök ezután elküldi az üzenetet az Azure Monitornak, ahol a megfelelő rekord jön létre.

További információ: [Syslog adatforrások az Azure Monitorban.](../azure-monitor/platform/data-sources-syslog.md)

> [!NOTE]
> - Az ügynök több forrásból is gyűjthet naplókat, de dedikált proxygépre kell telepíteni.
> - Ha ugyanazon a virtuális gépen is támogatni szeretné a CEF és a Syslog összekötőit, hajtsa végre az alábbi lépéseket az adatok másolásának elkerülése érdekében:
>    1. Kövesse az utasításokat [a CEF csatlakoztatása gombra.](connect-common-event-format.md)
>    2. A Syslog-adatok csatlakoztatásához nyissa meg a **Beállítások** > **munkaterület beállításai** > **Speciális beállítások** > **Data** > **Syslog** lehetőséget, és állítsa be a létesítményeket és azok prioritásait úgy, hogy ne ugyanazok a létesítmények és tulajdonságok, mint a CEF-konfigurációban használt. <br></br>Ha az Alábbiakban a **konfiguráció alkalmazása a gépeimre**lehetőséget választja, akkor ezeket a beállításokat alkalmazza a munkaterülethez csatlakoztatott összes virtuális gépre.


## <a name="connect-your-syslog-appliance"></a>Csatlakoztassa a Syslog készüléket

1. Az Azure Sentinelben válassza **az Adatösszekötők,** majd válassza ki a **Syslog-összekötőt.**

2. A **Syslog** panelen válassza az **Összekötő megnyitása lapot**.

3. Telepítse a Linux-ügynököt:
    
    - Ha a Linux-alapú virtuális gép az Azure-ban van, válassza **az Ügynök letöltése és telepítése az Azure Linux virtuális gépen**lehetőséget. A **Virtuális gépek** panelen jelölje ki az ügynök telepítéséhez szükséges virtuális gépeket, majd kattintson a **Csatlakozás gombra.**
    - Ha a Linux-gép nem az Azure-ban, válassza **az ügynök letöltése és telepítése linuxos nem Azure-gépen**lehetőséget. A **Direct agent** panelen másolja a **letöltési és fedélzeti ÜGYNÖK LINUX-ra** parancsát, és futtassa azt a számítógépen. 
    
   > [!NOTE]
   > Győződjön meg arról, hogy ezekhez a számítógépekhez a szervezet biztonsági házirendjének megfelelően konfigurálja a biztonsági beállításokat. Beállíthatja például, hogy a hálózati beállítások igazodjanak a szervezet hálózati biztonsági házirendjéhez, és módosíthatja a démon portjait és protokolljait, hogy megfeleljenek a biztonsági követelményeknek.

4. Válassza **a Munkaterület speciális beállításainak megnyitása lehetőséget.**

5. A **Speciális beállítások** panelen válassza a **Data** > **Syslog**lehetőséget. Ezután adja hozzá a csatlakozó összegyűjtéséhez szükséges létesítményeket.
    
    Adja hozzá a syslog készülék által a naplófejlécekben található létesítményeket. Ezt a konfigurációt a **Syslog-d Syslog-d** készülékében a mappában, `/etc/syslog-ng/security-config-omsagent.conf`az **r-Syslog alkalmazásban** pedig a `/etc/rsyslog.d/security-config-omsagent.conf` alkalmazásban láthatja.
    
    Ha rendellenes SSH bejelentkezési észlelést szeretne használni az összegyűjtött adatokkal, adja hozzá az **auth** és **authpriv**értéket. További részleteket a [következő részben](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) talál.

6. Ha hozzáadta az összes figyelni kívánt létesítményt, és mindegyikhez módosította a súlyossági beállításokat, jelölje be a **Konfiguráció alkalmazása a gépeimre jelölőnégyzetet.**

7. Kattintson a **Mentés** gombra. 

8. A syslog készüléken győződjön meg arról, hogy a megadott berendezéseket küldi.

9. Ha a megfelelő sémát szeretné használni az Azure Monitorban a syslog naplókhoz, keresse meg a **Syslog**kifejezést.

10. Használhatja a Kusto függvény leírt [funkciók használata az Azure Monitor naplólekérdezések](../azure-monitor/log-query/functions.md) a Syslog-üzenetek elemzéséhez. Ezután mentheti őket egy új Log Analytics-függvényként, hogy új adattípusként használhassa őket.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Konfigurálja a Syslog összekötőt a rendellenes SSH bejelentkezési észleléshez

> [!IMPORTANT]
> A rendellenes SSH bejelentkezési észlelés jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Az Azure Sentinel alkalmazhat gépi tanulás (ML) a syslog adatok at anomália biztonságos rendszerhéj (SSH) bejelentkezési tevékenység azonosításához. A forgatókönyvek a következők:

- Lehetetlen utazás – ha két sikeres bejelentkezési esemény két olyan helyről történik, amelyeket a két bejelentkezési esemény időkeretén belül lehetetlen elérni.
- Váratlan hely – az a hely, ahonnan sikeres bejelentkezési esemény történt, gyanús. Például a helyet nem látták a közelmúltban.
 
Ez az észlelés a Syslog adatösszekötő adott konfigurációját igényli: 

1. Az előző eljárás 5. **auth** **authpriv** Tartsa meg a súlyossági beállítások alapértelmezett beállításait, hogy mindegyik ki legyen jelölve. Példa:
    
    > [!div class="mx-imgBorder"]
    > ![A rendellenes SSH bejelentkezésészleléshez szükséges létesítmények](./media/connect-syslog/facilities-ssh-detection.png)

2. Hagyjon elegendő időt a syslog adatok gyűjtésére. Ezután keresse meg az **Azure Sentinel – Naplók ,** és másolja be a következő lekérdezést:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    Szükség esetén módosítsa az **időtartományt,** és válassza a **Futtatás**lehetőséget.
    
    Ha az eredményül kapott szám nulla, ellenőrizze az összekötő konfigurációját, és hogy a figyelt számítógépek sikeres bejelentkezési tevékenységgel rendelkeznek-e a lekérdezéshez megadott időszakban.
    
    Ha az eredményül kapott szám nagyobb, mint nulla, a syslog adatok alkalmasak rendellenes SSH bejelentkezésészlelése. Ezt az észlelést **az Analytics-szabály** >  **sablonokból (előzetes verzió) Rendellenes SSH bejelentkezési észlelési****sablonjaiból** > engedélyezi.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Helyszíni Syslog-készülékeket az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)

