---
title: Az Azure Site Recovery folyamatkiszolgáló hibaelhárítása
description: Ez a cikk ismerteti az Azure Site Recovery-folyamat kiszolgálóval kapcsolatos problémák elhárítása
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.author: raynew
ms.openlocfilehash: 6e31308800f72d60381f1e4ecd540482ba263851
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969364"
---
# <a name="troubleshoot-the-process-server"></a>A folyamatkiszolgáló hibaelhárítása

A [Site Recovery](site-recovery-overview.md) folyamatkiszolgáló szolgál, ha a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-bA vészhelyreállítás beállítása. Ez a cikk ismerteti a problémák a folyamatkiszolgáló, beleértve a replikáció és a csatlakozási hibák elhárításáról.

[További](vmware-physical-azure-config-process-server-overview.md) a folyamatkiszolgáló kapcsolatban.

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené a hibaelhárítás:

1. Győződjön meg arról, hogy megismerte, hogyan [folyamatkiszolgálók figyelése](vmware-physical-azure-monitor-process-server.md).
2. Tekintse át az alábbi ajánlott eljárásokat.
3. Támaszkodjon [kapacitás kihasználását](site-recovery-plan-capacity-vmware.md#capacity-considerations), és használata méretezési útmutató a [konfigurációs kiszolgáló](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) vagy [önálló folyamatkiszolgálók](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Folyamatkiszolgáló telepítésének ajánlott eljárásai

A folyamatkiszolgálók optimális teljesítményét hogy már összesített általános ajánlott eljárásokat számos.

**Ajánlott eljárás** | **Részletek**
--- |---
**Használat** | Ellenőrizze, hogy a konfigurációs kiszolgáló vagy különálló folyamatkiszolgálót csak szolgálnak az adott célra. Ne futtassa bármi más a gépet.
**IP-cím** | Győződjön meg arról, hogy a folyamatkiszolgáló statikus IPv4-címmel rendelkezik, és nem rendelkezik konfigurált NAT.
**Vezérlő memória és CPU-használat** |70 %-os alatt tartsa Processzor-és memóriahasználatát.
**Szabad terület biztosítása** | Szabad lemezterület a gyorsítótárban lemezterület a folyamatkiszolgáló hivatkozik. Replikációs adatok a gyorsítótárban tárolt előtt fel lesz töltve az Azure-bA.<br/><br/> Tartsa meg a fenti 25 % szabad terület. 20 % alá kerül, ha a rendszer szabályozza a replikációs a folyamatkiszolgáló társított replikált gépek esetében.

## <a name="check-process-server-health"></a>Folyamat kiszolgáló állapotának ellenőrzése

Hibaelhárítás első lépéseként állapotát és a folyamatkiszolgáló állapotának ellenőrzéséhez. Ehhez tekintse át az összes riasztást, ellenőrizze, hogy a szükséges szolgáltatások futnak, és győződjön meg arról, hogy nincs-e a szívverés a folyamatkiszolgálóról. Ezeket a lépéseket foglalja össze a következő ábra a módosításukhoz szükséges eljárások segítséget nyújtanak a lépésekkel.

![Folyamat kiszolgáló állapotának hibaelhárítása](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>1\. lépés: Kiszolgáló állapotriasztások folyamat hibaelhárítása

A folyamatkiszolgáló állít elő, hogy a health-riasztások száma. A riasztások és a javasolt műveleteket, az alábbi táblázat foglalja össze.

**Riasztás típusa** | **Hiba történt** | **Hibaelhárítás**
--- | --- | --- 
![Kifogástalan][green] | None  | A folyamatkiszolgáló csatlakoztatva és működik megfelelően.
![Figyelmeztetés][yellow] | A megadott szolgáltatások nem futnak. | 1. Ellenőrizze, hogy a szolgáltatások futnak-e.<br/> 2. Ha szolgáltatásokat az elvárt módon futnak, hajtsa végre az alatt látható utasításokat követve [kapcsolatot és a replikációs hibaelhárítás](#check-connectivity-and-replication).
![Figyelmeztetés][yellow]  | Processzor kihasználtsága > 80 % az elmúlt 15 percben. | 1. Ne adjon hozzá új gépek.<br/>2. Ellenőrizze, hogy a virtuális gépek használatával a folyamatkiszolgáló igazodnak-e [korlátok definiált](site-recovery-plan-capacity-vmware.md#capacity-considerations), és érdemes beállítani egy [további folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md).<br/>3. Kövesse a lenti útmutatást [kapcsolatot és a replikációs hibaelhárítás](#check-connectivity-and-replication).
![Kritikus][red] |  Processzor kihasználtsága > 95 % az elmúlt 15 percben. | 1. Ne adjon hozzá új gépek.<br/>2. Ellenőrizze, hogy a virtuális gépek használatával a folyamatkiszolgáló igazodnak-e [korlátok definiált](site-recovery-plan-capacity-vmware.md#capacity-considerations), és érdemes beállítani egy [további folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md).<br/>3. Kövesse a lenti útmutatást [kapcsolatot és a replikációs hibaelhárítás](#check-connectivity-and-replication).<br/> 4. Ha a probléma nem szűnik meg, futtassa a [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) VMware/fizikai kiszolgáló replikációjához.
![Figyelmeztetés][yellow] | Memória használati > 80 % az elmúlt 15 percben. |  1. Ne adjon hozzá új gépek.<br/>2. Ellenőrizze, hogy a virtuális gépek használatával a folyamatkiszolgáló igazodnak-e [korlátok definiált](site-recovery-plan-capacity-vmware.md#capacity-considerations), és érdemes beállítani egy [további folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md).<br/>3. Kövesse a figyelmeztetéssel kapcsolatos utasításokat.<br/> 4. Ha a probléma nem szűnik meg, kövesse a lenti útmutatást [kapcsolatot és a replikációs hibaelhárítás](#check-connectivity-and-replication).
![Kritikus][red] | Memória használati > 95 % az elmúlt 15 percben. | 1. Ne új gépek felvétele és beállítása a mérlegeli- [további folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md).<br/> 2. Kövesse a figyelmeztetéssel kapcsolatos utasításokat.<br/> 3. 4. Ha a probléma továbbra is fennáll, kövesse a lenti útmutatást [kapcsolatot és a replikációs hibaelhárítás](#check-connectivity-and-replication).<br/> 4. Ha a probléma nem szűnik meg, futtassa a [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) VMware/fizikai kiszolgáló replikációs problémák esetén.
![Figyelmeztetés][yellow] | Gyorsítótár mappa szabad terület < 30 % a az elmúlt 15 percben. | 1. Ne adja hozzá az új gépeket, és érdemes beállítani egy [további folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md).<br/>2. Ellenőrizze, hogy a virtuális gépek használatával a folyamatkiszolgáló igazodnak-e [irányelvek](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Kövesse a lenti útmutatást [kapcsolatot és a replikációs hibaelhárítás](#check-connectivity-and-replication).
![Kritikus][red] |  Szabad terület < 25 % az elmúlt 15 percben | 1. Kövesse az utasításokat a figyelmeztetés a problémához társított.<br/> 2. 3. Kövesse a lenti útmutatást [kapcsolatot és a replikációs hibaelhárítás](#check-connectivity-and-replication).<br/> 3. Ha a probléma nem szűnik meg, futtassa a [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) VMware/fizikai kiszolgáló replikációjához.
![Kritikus][red] | Nem érkezett szívverés a folyamatkiszolgálóról 15 percig vagy tovább. A tmansvs szolgáltatás nem kommunikál a konfigurációs kiszolgálón. | 1.) ellenőrizze, hogy a folyamatkiszolgáló működik-e.<br/> 2. Ellenőrizze, hogy a tmassvc a folyamatkiszolgálón fut-e.<br/> 3. Kövesse a lenti útmutatást [kapcsolatot és a replikációs hibaelhárítás](#check-connectivity-and-replication).


![tábla kulcsa](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>2\. lépés: Ellenőrizze a folyamatkiszolgáló szolgáltatásai

Szolgáltatások, amelyek a folyamatkiszolgáló futnia kell az alábbi táblázat foglalja össze. A szolgáltatások, attól függően, hogyan helyezünk üzembe a folyamatkiszolgáló kisebb különbségek vannak. 

Kivéve a Microsoft Azure Recovery Services Agent (obengine) szolgáltatásokhoz, ellenőrizze, hogy a lefokozáskor értéke **automatikus** vagy **automatikus (Késleltetett indítás)** .
 
**Üzembe helyezés** | **Futó szolgáltatások**
--- | ---
**Folyamatkiszolgáló a konfigurációs kiszolgálón** | ProcessServer; ProcessServerMonitor; cxprocessserver; Az InMage PushInstall; Napló feltöltési szolgáltatás (LogUpload); Az InMage Scout alkalmazásszolgáltatás; A Microsoft Azure Recovery Services Agent (obengine); Az InMage Scout VX Agent – Sentinel/Outpost (svagents); tmansvc; Webes közzétételi szolgáltatás (W3SVC); MySQL; A Microsoft Azure Site Recovery szolgáltatás (dra)
**Önálló kiszolgálóként üzemelő folyamatkiszolgálót** | ProcessServer; ProcessServerMonitor; cxprocessserver; Az InMage PushInstall; Napló feltöltési szolgáltatás (LogUpload); Az InMage Scout alkalmazásszolgáltatás; A Microsoft Azure Recovery Services Agent (obengine); Az InMage Scout VX Agent – Sentinel/Outpost (svagents); tmansvc.
**Üzembe helyezett Azure-ban feladat-visszavételi folyamatkiszolgáló** | ProcessServer; ProcessServerMonitor; cxprocessserver; Az InMage PushInstall; Napló feltöltési szolgáltatás (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>3\. lépés: Ellenőrizze a folyamat kiszolgálói szívverés

Ha nem érkezett szívverés a folyamatkiszolgálóról (hibakód: 806), tegye a következőket:

1. Győződjön meg arról, hogy a folyamatkiszolgáló virtuális gép működik-e.
2. Ellenőrizze ezeket a naplókat, a hibákat.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Ellenőrizze a kapcsolatot és a replikáció

 Kezdeti és folyamatos replikálási hibák gyakran forrásgépek és a folyamatkiszolgáló között, vagy a folyamatkiszolgáló és az Azure közötti kapcsolati problémák okozzák. Ezeket a lépéseket foglalja össze a következő ábra a módosításukhoz szükséges eljárások segítséget nyújtanak a lépésekkel.

![Kapcsolat és a replikáció hibaelhárítása](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>4\. lépés: Ellenőrizze a forrásgépen idő szinkronizálása

Győződjön meg arról, hogy a rendszer dátum/idő a replikált gép szinkronizálva-e. [További információ](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>5\. lépés: Ellenőrizze a víruskereső szoftvert a forrásgépen

Ellenőrizze, hogy a replikált gép nincs víruskereső szoftver blokkolja a Site Recovery. Ha kizárja a Site Recovery a víruskereső programok van szüksége, tekintse át a [Ez a cikk](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>6\. lépés: Ellenőrizze a kapcsolatot a forrásgépen


1. Telepítse a [Telnet-ügyfél](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) a forrásgépen, ha szeretné. Ne használja a pingelés.
2. A forrásgép pingelni a Telnet-HTTPS-port a folyamatkiszolgálón. Alapértelmezés szerint a 9443-as HTTPS-port a replikációs forgalmat a.

    `telnet <process server IP address> <port>`

3. Győződjön meg arról, hogy a kapcsolat létrejött.


**Kapcsolatok** | **Részletek** | **Művelet**
--- | --- | ---
**A sikeres** | A Telnet üres képernyőt jeleníti meg, és a folyamatkiszolgáló érhető el. | Nincs szükség műveletre.
**Sikertelen** | Nem lehet csatlakoztatni | Győződjön meg arról, hogy a 9443-as porton bejövő engedélyezett-e a folyamatkiszolgáló. Például ha a szegélyhálózaton vagy demilitarizált. Ellenőrizze a kapcsolatot újra.
**Részben sikeres** | A kapcsolódás, de a forrásgép jelenti, hogy a folyamatkiszolgáló nem érhető el. | Folytassa a következő hibaelhárítási eljárást.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>7\. lépés: Amikor folyamatkiszolgálót nem érhető el – hibaelhárítás

Ha a folyamatkiszolgáló nem a forrásgépről, 78186 hiba jelenik meg. Ha nem képes kezelni, a probléma lesz alkalmazáskonzisztens egyaránt, és összeomlás-konzisztens helyreállítási pontok nem várt módon létrehozott.

Végezzen hibaelhárítást, úgy, hogy a forrásgép is elérni a folyamatkiszolgáló IP-címét, és az cxpsclient eszközt futtatja a végpontok közötti kapcsolat a forrásgép.


### <a name="check-the-ip-connection-on-the-process-server"></a>A folyamatkiszolgáló IP-kapcsolat ellenőrzése

A telnet sikeres, de a forrásgép jelenti, hogy a folyamatkiszolgáló nem érhető el, ha ellenőrizze, hogy elérheti a folyamatkiszolgáló IP-címét.

1. Egy webböngészőben és próbálja meg elérni IP-cím https://<PS_IP>:<PS_Data_Port>/.
2. Ez az ellenőrzés azt mutatja, hogy HTTPS-tanúsítvány hibát, ha ez normális. Ha a hiba figyelmen kívül hagyásához, megtekintheti az egy 400 – Hibás kérés. Ez azt jelenti, hogy a kiszolgáló a böngésző kérés nem teljesíthető, és, hogy a szabványos HTTPS-kapcsolat nem okoz gondot.
3. Ha ez az ellenőrzés nem működik, majd jegyezze fel a böngésző hibaüzenet. Például egy 407 hiba proxyhitelesítés problémáját jelzi.

### <a name="check-the-connection-with-cxpsclient"></a>Ellenőrizze a kapcsolatot a cxpsclient

Emellett futtathatja a cxpsclient eszközzel ellenőrizze a végpontok közötti kapcsolat.

1. Futtassa az eszközt az alábbiak szerint:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. A folyamatkiszolgáló tekintse meg a generált naplókat, a mappákon belül:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Ellenőrizze a forrás virtuális gépek naplóinak, a feltöltési hibák (78028 hiba)

Adatfeltöltés blokkolva forrásgépek, a folyamat szolgáltatáshoz probléma mindkét nem hoz létre összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontokat eredményezhet. 

1. A hálózati feltöltési hibák elhárításához megtekintheti a naplóban jelzett hibákat:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. Használja a többi ebben a cikkben ismertetett eljárások segítségével adatok feltöltéssel kapcsolatos problémák megoldásához.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>8\. lépés: Ellenőrizze, hogy a folyamatkiszolgáló küld-e adatokat

Ellenőrizze, hogy a folyamatkiszolgáló van aktívan küld-e adatokat az Azure-bA.

  1. A folyamatkiszolgáló nyissa meg a Feladatkezelőt (nyomja meg a Ctrl + Shift + Esc).
  2. Válassza ki a **teljesítmény** lap > **nyissa meg az erőforrás-figyelő**.
  3. A **erőforrás-figyelő** lapon válassza ki a **hálózati** fülre. A **hálózati tevékenységgel rendelkező folyamat**, ellenőrizze, hogy cbengine.exe aktívan küld egy nagy vNotolume az adatok.

       ![A hálózati tevékenységgel rendelkező folyamat kötetek](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Ha cbengine.exe nagy mennyiségű adatot nem küld, hajtsa végre az alábbi szakaszokban található.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>9\. lépés: Az Azure blob storage-folyamat kiszolgálói kapcsolat ellenőrzése

1. Válassza ki az erőforrás-figyelő **cbengine.exe**.
2. A **TCP-kapcsolatok**, ellenőrizze, hogy van-e kapcsolat a folyamatkiszolgáló és az Azure storage.

  ![Cbengine.exe és az Azure Blob storage URL-cím közötti kapcsolat](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Ellenőrizze a szolgáltatásokat

Ha nincs kapcsolat a folyamatkiszolgáló és az Azure blob storage URL-címe van, ellenőrizze, hogy a szolgáltatások futnak-e.

1. Válassza ki a Vezérlőpult **szolgáltatások**.
2. Győződjön meg arról, hogy futnak-e a következő szolgáltatásokat:

    - cxprocessserver
    - Az InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure Recovery Services Agent
    - Microsoft Azure Site Recovery szolgáltatás
    - tmansvc

3. Indítsa el, vagy bármely szolgáltatás nem fut, indítsa újra.
4. Győződjön meg arról, hogy a folyamatkiszolgáló csatlakoztatva és elérhető. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>10\. lépés: Ellenőrizze a folyamat kiszolgálói kapcsolatot az Azure nyilvános IP-cím

1. A folyamatkiszolgáló a **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**, nyissa meg a legújabb CBEngineCurr.errlog fájlt.
2. A fájlban keresse meg **443-as**, vagy a karakterlánc **kapcsolódási kísérlet sikertelen**.

  ![Hiba jelentkezik be a Temp mappa](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Ha problémákat, az Azure nyilvános IP-címet a fájlban található CBEngineCurr.currLog 443-as port használatával:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. A parancssorban a folyamatkiszolgálón a Telnet használatával az Azure nyilvános IP-címet pingelje.
6. Ha nem sikerül, kövesse a következő eljárással.

## <a name="step-11-check-process-server-firewall-settings"></a>11\. lépés: Ellenőrizze a folyamat kiszolgálói tűzfal beállításaiban. 

Ellenőrizze, hogy a folyamatkiszolgáló IP cím-alapú tűzfala blokkolja.

1. Az IP-címeken alapuló tűzfalszabályok szabályok:

    a) teljes listájának letöltése [Microsoft Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653).

    (b) adja hozzá az IP-címtartományok a tűzfal konfigurációját, annak érdekében, hogy a tűzfal engedélyezi-e a kommunikációt az Azure-ba (és az alapértelmezett HTTPS-port, 443-as porton).

    az előfizetés az Azure-régióban, valamint Azure USA nyugati régiójában (hozzáférés-vezérléshez és identitáskezeléshez kezelés használt) az engedélyezés c) IP-címtartományok.

2. Adja hozzá az URL-címeket, a tűzfal-konfiguráció az alábbi táblázatban felsorolt URL-alapú tűzfalak.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>12\. lépés: Folyamat kiszolgáló proxy beállításainak ellenőrzése 

1. Ha proxykiszolgálót használ, győződjön meg arról, hogy a proxykiszolgáló nevét feloldja a DNS-kiszolgáló. Ellenőrizze a beállításkulcs a konfigurációs kiszolgáló beállításakor megadott értékét **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure hely Recovery\ProxySettings**.
2. Győződjön meg arról, hogy ugyanazokat a beállításokat az Azure Site Recovery-ügynök által használ is küldhet adatokat.

    a) keressen a **a Microsoft Azure Backup**.

    (b) nyílt **a Microsoft Azure Backup**, és válassza ki **művelet** > **tulajdonságainak módosítása**.

    c) az a **proxykonfiguráció** lapon, a proxykiszolgáló címét megegyezőnek kell lennie a proxykiszolgáló címét, a beállításjegyzék-beállításainak látható. Ha nem, akkor ugyanazt a címet módosítsa azt.

## <a name="step-13-check-bandwidth"></a>13\. lépés: Ellenőrizze a sávszélesség

Növelje a sávszélesség a folyamatkiszolgáló és az Azure között, és ellenőrizze, hogy a probléma továbbra is fennáll-e.


## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége, tegye közzé a kérdését a [Azure Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png