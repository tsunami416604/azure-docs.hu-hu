---
title: Az Azure site recovery folyamatkiszolgálójának hibaelhárítása
description: Ez a cikk az Azure Site Recovery folyamatkiszolgálóval kapcsolatos problémák elhárítását ismerteti
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256860"
---
# <a name="troubleshoot-the-process-server"></a>A folyamatkiszolgáló hibáinak elhárítása

A [Site Recovery](site-recovery-overview.md) folyamatkiszolgáló akkor használatos, amikor beállítja a vészhelyreállítást az Azure-ban a helyszíni VMware virtuális gépek és a fizikai kiszolgálók. Ez a cikk a folyamatkiszolgálóval kapcsolatos problémák elhárítását ismerteti, beleértve a replikációs és kapcsolódási problémákat.

[További információ](vmware-physical-azure-config-process-server-overview.md) a folyamatkiszolgálóról.

## <a name="before-you-start"></a>Előkészületek

A hibaelhárítás megkezdése előtt:

1. Győződjön meg róla, hogy megértette, hogyan kell [figyelni a folyamatszerverek](vmware-physical-azure-monitor-process-server.md).
2. Tekintse át az alábbi gyakorlati tanácsokat.
3. Ügyeljen arra, hogy kövesse a [kapacitással kapcsolatos szempontokat,](site-recovery-plan-capacity-vmware.md#capacity-considerations)és használjon méretezési útmutatót a [konfigurációs kiszolgálóhoz](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) vagy az [önálló folyamatkiszolgálókhoz.](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)

## <a name="best-practices-for-process-server-deployment"></a>Gyakorlati tanácsok a folyamatkiszolgáló telepítéséhez

A folyamatkiszolgálók optimális teljesítménye érdekében számos általános ajánlott eljárást foglaltunk össze.

**Legjobb gyakorlat** | **Részletek**
--- |---
**Használat** | Győződjön meg arról, hogy a konfigurációs kiszolgáló/önálló folyamatkiszolgáló csak a kívánt célra használatos. Ne futtasson semmi mást a gépen.
**IP-cím** | Győződjön meg arról, hogy a folyamatkiszolgáló rendelkezik statikus IPv4-címmel, és nincs konfigurálva a NAT.
**Memória/CPU-használat szabályozása** |Tartsa a CPU és a memória használat alatt 70%.
**Szabad terület biztosítása** | A szabad terület a folyamatkiszolgáló gyorsítótárlemezterületére vonatkozik. A replikációs adatok a gyorsítótárban tárolódnak, mielőtt feltöltenék az Azure-ba.<br/><br/> Tartsa a szabad területet 25% felett. Ha 20% alá csökken, a replikáció a folyamatkiszolgálóhoz társított replikált gépek reprodukált akta lesz.

## <a name="check-process-server-health"></a>A folyamatkiszolgáló állapotának ellenőrzése

A hibaelhárítás első lépése a folyamatkiszolgáló állapotának és állapotának ellenőrzése. Ehhez tekintse át az összes riasztást, ellenőrizze, hogy a szükséges szolgáltatások futnak-e, és ellenőrizze, hogy van-e szívverés a folyamatkiszolgálótól. Ezeket a lépéseket az alábbi ábra foglalja össze, amelyet a lépések végrehajtásához szükséges eljárások követnek.

![Folyamatkiszolgáló állapotának elhárítása](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>1. lépés: Folyamatkiszolgáló állapotára vonatkozó riasztások kal elhárítása

A folyamatkiszolgáló számos állapotriasztást hoz létre. Ezeket a riasztásokat és az ajánlott műveleteket az alábbi táblázat foglalja össze.

**Riasztástípus** | **Hiba** | **Elhárítása**
--- | --- | --- 
![Kifogástalan][green] | None  | A folyamatkiszolgáló csatlakoztatva van és kifogástalan állapotú.
![Figyelmeztetés][yellow] | A megadott szolgáltatások nem futnak. | 1. Ellenőrizze, hogy futnak-e a szolgáltatások.<br/> 2. Ha a szolgáltatások a várt módon futnak, kövesse az alábbi utasításokat a [kapcsolódási és replikációs problémák elhárításához.](#check-connectivity-and-replication)
![Figyelmeztetés][yellow]  | A PROCESSZOR-kihasználtság 80%-kal > az elmúlt 15 percben. | 1. Ne adjon hozzá új gépeket.<br/>2. Ellenőrizze, hogy a folyamatkiszolgálót használó virtuális gépek száma [igazodik-e](site-recovery-plan-capacity-vmware.md#capacity-considerations)a megadott korlátokhoz, és fontolja meg egy [további folyamatkiszolgáló beállítását.](vmware-azure-set-up-process-server-scale.md)<br/>3. Kövesse az alábbi utasításokat a [kapcsolódási és replikációs problémák elhárításához](#check-connectivity-and-replication).
![Kritikus][red] |  A PROCESSZOR kihasználtsága > 95% az elmúlt 15 percben. | 1. Ne adjon hozzá új gépeket.<br/>2. Ellenőrizze, hogy a folyamatkiszolgálót használó virtuális gépek száma [igazodik-e](site-recovery-plan-capacity-vmware.md#capacity-considerations)a megadott korlátokhoz, és fontolja meg egy [további folyamatkiszolgáló beállítását.](vmware-azure-set-up-process-server-scale.md)<br/>3. Kövesse az alábbi utasításokat a [kapcsolódási és replikációs problémák elhárításához](#check-connectivity-and-replication).<br/> 4. Ha a probléma továbbra is fennáll, futtassa a [Telepítéstervező](https://aka.ms/asr-v2a-deployment-planner) t a VMware/fizikai kiszolgáló replikációjához.
![Figyelmeztetés][yellow] | A memóriahasználat 80%-> az elmúlt 15 percben. |  1. Ne adjon hozzá új gépeket.<br/>2. Ellenőrizze, hogy a folyamatkiszolgálót használó virtuális gépek száma [igazodik-e](site-recovery-plan-capacity-vmware.md#capacity-considerations)a megadott korlátokhoz, és fontolja meg egy [további folyamatkiszolgáló beállítását.](vmware-azure-set-up-process-server-scale.md)<br/>3. Kövesse a figyelmeztetéssel kapcsolatos utasításokat.<br/> 4. Ha a probléma továbbra is fennáll, kövesse az alábbi utasításokat a [kapcsolódási és replikációs problémák elhárításához](#check-connectivity-and-replication).
![Kritikus][red] | A memóriahasználat > 95% az elmúlt 15 percben. | 1. Ne adjon hozzá új gépeket, és fontolja meg egy [további folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md)létrehozását .<br/> 2. Kövesse a figyelmeztetéssel kapcsolatos utasításokat.<br/> 3. 4. Ha a probléma továbbra is fennáll, kövesse az alábbi utasításokat a [kapcsolódási és replikációs problémák elhárításához.](#check-connectivity-and-replication)<br/> 4. Ha a probléma továbbra is fennáll, futtassa a [Telepítéstervező](https://aka.ms/asr-v2a-deployment-planner) t a VMware/fizikai kiszolgáló replikációs problémáihoz.
![Figyelmeztetés][yellow] | A gyorsítótármappa szabad területe az elmúlt 15 percben 30%-<. | 1. Ne adjon hozzá új gépeket, és fontolja meg egy [további folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md)létrehozását.<br/>2. Ellenőrizze, hogy a folyamatkiszolgálót használó virtuális gépek száma igazodik-e az [irányelvekhez.](site-recovery-plan-capacity-vmware.md#capacity-considerations)<br/> 3. Kövesse az alábbi utasításokat a [kapcsolódási és replikációs problémák elhárításához](#check-connectivity-and-replication).
![Kritikus][red] |  Szabad hely < 25% az elmúlt 15 percben | 1. Kövesse a jelen probléma figyelmeztetéséhez kapcsolódó utasításokat.<br/> 2. 3. A [kapcsolódási és replikációs problémák elhárításához](#check-connectivity-and-replication)kövesse az alábbi utasításokat.<br/> 3. Ha a probléma továbbra is fennáll, futtassa a [Telepítéstervező](https://aka.ms/asr-v2a-deployment-planner) t a VMware/fizikai kiszolgáló replikációjához.
![Kritikus][red] | A folyamatkiszolgáló 15 percig vagy tovább nem szívritmusban érkezik. A tmansvs szolgáltatás nem kommunikál a konfigurációs kiszolgálóval. | 1) Ellenőrizze, hogy a folyamat szerver működik.<br/> 2. Ellenőrizze, hogy a tmassvc fut-e a folyamatkiszolgálón.<br/> 3. Kövesse az alábbi utasításokat a [kapcsolódási és replikációs problémák elhárításához](#check-connectivity-and-replication).


![Táblakulcs](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>2. lépés: Folyamatkiszolgáló-szolgáltatások ellenőrzése

A folyamatkiszolgálón futtatandó szolgáltatásokat az alábbi táblázat foglalja össze. A folyamatkiszolgáló telepítésétől függően a szolgáltatások között kisebb különbségek vannak. 

A Microsoft Azure Recovery Services Agent (obengine) kivételével minden szolgáltatás esetében ellenőrizze, hogy a StartType beállítása **Automatikus** vagy **Automatikus (Késleltetett indítás)** értékre van-e állítva.
 
**Környezet** | **Szolgáltatások működtetése**
--- | ---
**Folyamatkiszolgáló a konfigurációs kiszolgálón** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Naplófeltöltési szolgáltatás (LogUpload); InMage Scout alkalmazásszolgáltatás; Microsoft Azure helyreállítási szolgáltatások ügynöke (obengine); InMage Scout VX Agent-Sentinel/Előőrs (svagents); tmansvc; Webes közzétételi szolgáltatás (W3SVC); MySQL; Microsoft Azure webhely-helyreállítási szolgáltatás (dra)
**Önálló kiszolgálóként futó kiszolgáló feldolgozása** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Naplófeltöltési szolgáltatás (LogUpload); InMage Scout alkalmazásszolgáltatás; Microsoft Azure helyreállítási szolgáltatások ügynöke (obengine); InMage Scout VX Agent-Sentinel/Előőrs (svagents); tmansvc.
**Az Azure-ban üzembe helyezett folyamatkiszolgáló feladat-visszavételhez** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Naplófeltöltési szolgáltatás (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>3. lépés: Ellenőrizze a folyamatkiszolgáló szívverését

Ha a folyamatkiszolgáló nem jelenik meg szívverés (806-os hibakód), tegye a következőket:

1. Ellenőrizze, hogy a folyamatkiszolgáló virtuális gépe működik-e.
2. Ellenőrizze, hogy vannak-e hibák a naplókban.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>A kapcsolat és a replikáció ellenőrzése

 A kezdeti és a folyamatban lévő replikációs hibákat gyakran a forrásgépek és a folyamatkiszolgáló, illetve a folyamatkiszolgáló és az Azure közötti kapcsolódási problémák okozzák. Ezeket a lépéseket az alábbi ábra foglalja össze, amelyet a lépések végrehajtásához szükséges eljárások követnek.

![Kapcsolódás és replikáció – problémamegoldás](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>4. lépés: Az idő szinkronizálásának ellenőrzése a forrásgépen

Győződjön meg arról, hogy a replikált gép rendszerdátuma/ideje szinkronban van. [További információ](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>5. lépés: Ellenőrizze anti-vírus szoftver forrásgép

Ellenőrizze, hogy a replikált számítógépen nincs-e víruskereső szoftver blokkolja-e a Site Recovery webhelyhelyreállítását. Ha ki kell zárnia a Site Recovery webhelyet a víruskereső programokból, olvassa el [ezt a cikket.](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)

## <a name="step-6-check-connectivity-from-source-machine"></a>6. lépés: Ellenőrizze a kapcsolatot a forrásgépről


1. Szükség esetén telepítse a [Telnet-ügyfelet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) a forrásszámítógépre. Ne használja a Ping.Don't use Ping.
2. A forrásgépről pingelje a folyamatkiszolgálót a HTTPS-porton a Telnet szolgáltatással. Alapértelmezés szerint a 9443 a replikációs forgalom HTTPS-portja.

    `telnet <process server IP address> <port>`

3. Ellenőrizze, hogy a kapcsolat sikeres-e.


**Kapcsolat** | **Részletek** | **Művelet**
--- | --- | ---
**Sikeres** | A Telnet üres képernyőt jelenít meg, és a folyamatkiszolgáló elérhető. | Nincs szükség további intézkedésre.
**Sikertelen** | Nem lehet csatlakozni | Győződjön meg arról, hogy a 9443-as bejövő port engedélyezett a folyamatkiszolgálón. Ha például peremhálózattal vagy árnyékolt alhálózattal rendelkezik. Ellenőrizze újra a kapcsolatot.
**Részben sikeres** | Csatlakozhat, de a forrásgép jelenti, hogy a folyamatkiszolgáló nem érhető el. | Folytassa a következő hibaelhárítási eljárással.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>7. lépés: Elérhetetlen folyamatkiszolgáló hibáinak elhárítása

Ha a folyamatkiszolgáló nem érhető el a forrásgépről, a 78186-os hiba jelenik meg. Ha nem oldja meg a problémát, a probléma eredményeképpen mind az alkalmazáskonzisztens, mind az összeomlás-konzisztens helyreállítási pontok nem a várt módon jönnek létre.

Hibaelhárítás: ellenőrizze, hogy a forrásgép el tudja-e érni a folyamatkiszolgáló IP-címét, és futtassa a cxpsclient eszközt a forrásszámítógépen a végpontok között lévő kapcsolat ellenőrzéséhez.


### <a name="check-the-ip-connection-on-the-process-server"></a>Az IP-kapcsolat ellenőrzése a folyamatkiszolgálón

Ha a telnet sikeres, de a forrásgép arról számol be, hogy a folyamatkiszolgáló nem érhető el, ellenőrizze, hogy el tudja-e érni a folyamatkiszolgáló IP-címét.

1. Webböngészőben próbálja meg elérni a https://<PS_IP>:<PS_Data_Port>/ IP-címet.
2. Ha ez az ellenőrzés HTTPS-tanúsítványhibát jelez, az normális. Ha figyelmen kívül hagyja a hibát, meg kell jelennie egy 400 - Rossz kérés. Ez azt jelenti, hogy a kiszolgáló nem tudja kiszolgálni a böngészőkérést, és hogy a szabványos HTTPS-kapcsolat rendben van.
3. Ha ez az ellenőrzés nem működik, jegyezze fel a böngésző hibaüzenetét. Például egy 407-es hiba jelzi a proxyhitelesítéssel kapcsolatos problémát.

### <a name="check-the-connection-with-cxpsclient"></a>Ellenőrizze a kapcsolatot cxpsclient

Ezenkívül a cxpsclient eszköz futtatásával ellenőrizheti a végpontok közötti kapcsolatot.

1. Futtassa az eszközt az alábbiak szerint:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. A folyamatkiszolgálón ellenőrizze a létrehozott naplókat ezekben a mappákban:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>A forrásvirtuális gép naplóinak ellenőrzése feltöltési hibák miatt (78028-as hiba)

A forrásgépekről a folyamatszolgáltatásba letiltott adatfeltöltések problémája összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontok létrejöttét eredményezheti. 

1. A hálózati feltöltési hibák elhárításához keresse meg a naplóhibáit:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. A cikkben található további eljárások segítségével megoldhatja az adatok feltöltésével kapcsolatos problémákat.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>8. lépés: Ellenőrizze, hogy a folyamatkiszolgáló adatokat nyom-e le

Ellenőrizze, hogy a folyamatkiszolgáló aktívan lekérdezi-e az adatokat az Azure-ba.

  1. A folyamatkiszolgálón nyissa meg a Feladatkezelőt (nyomja le a Ctrl+Shift+Esc billentyűkombinációt).
  2. Válassza a **Teljesítmény** lapot > **Az Erőforrásfigyelő megnyitása**.
  3. Az **Erőforrás-figyelő** lapon válassza a **Hálózat** lapot. A **Hálózati tevékenységgel folyamatok csoportban**ellenőrizze, hogy a cbengine.exe aktívan küld-e nagy mennyiségű adatot.

       ![Hálózati tevékenységgel rendelkező folyamatok alatti kötetek](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Ha a cbengine.exe nem küld nagy mennyiségű adatot, hajtsa végre a következő szakaszokban ismertetett lépéseket.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>9. lépés: Ellenőrizze a folyamatkiszolgáló és az Azure blob storage kapcsolatát

1. Az Erőforrás-figyelőben válassza a **cbengine.exe**lehetőséget.
2. A **TCP-kapcsolatok**csoportban ellenőrizze, hogy van-e kapcsolat a folyamatkiszolgáló és az Azure storage között.

  ![A cbengine.exe és az Azure Blob storage URL-címe közötti kapcsolat](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Szolgáltatások ellenőrzése

Ha nincs kapcsolat a folyamatkiszolgáló és az Azure blob storage URL-cím, ellenőrizze, hogy a szolgáltatások futnak.

1. A Vezérlőpulton válassza a **Szolgáltatások**lehetőséget.
2. Ellenőrizze, hogy futnak-e a következő szolgáltatások:

    - cxprocessserver
    - InMage Scout VX ügynök - Sentinel / Előőrs
    - Microsoft Azure helyreállítási szolgáltatások ügynöke
    - Microsoft Azure webhely-helyreállítási szolgáltatás
    - tmansvc között

3. Indítsa el vagy indítsa újra a nem futó szolgáltatást.
4. Ellenőrizze, hogy a folyamatkiszolgáló csatlakoztatva van-e és elérhető-e. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>10. lépés: ellenőrizze a folyamatkiszolgáló kapcsolatát az Azure nyilvános IP-címével

1. A folyamatkiszolgáló **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**mappájában nyissa meg a legújabb CBEngineCurr.errlog fájlt.
2. A fájlban keressen a **443**-as t vagy a **karakterlánc-csatlakozási kísérletet.**

  ![Hibanaplók a Temp mappában](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Ha problémákat lát, az Azure nyilvános IP-címét a CBEngineCurr.currLog fájlban, a 443-as port használatával található:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. A folyamatkiszolgáló parancssorában a Telnet segítségével pingelheti az Azure nyilvános IP-címét.
6. Ha nem tud csatlakozni, kövesse a következő eljárást.

## <a name="step-11-check-process-server-firewall-settings"></a>11. lépés: Ellenőrizze a folyamatkiszolgáló tűzfalbeállításait. 

Ellenőrizze, hogy a folyamatkiszolgáló IP-címalapú tűzfala blokkolja-e a hozzáférést.

1. IP-címalapú tűzfalszabályok esetén:

    a) Töltse le a [Microsoft Azure adatközpont IP-tartományainak](https://www.microsoft.com/download/details.aspx?id=41653)teljes listáját.

    b) Adja hozzá az IP-címtartományokat a tűzfal konfigurációjához, hogy a tűzfal lehetővé tegye az Azure-ral (és az alapértelmezett HTTPS-porttal,443) való kommunikációt.

    c) IP-címtartományok engedélyezése az előfizetés Azure-régiójához és az Azure West USA régióhoz (hozzáférés-vezérléshez és identitáskezeléshez).

2. URL-alapú tűzfalak esetén adja hozzá az alábbi táblázatban felsorolt URL-címeket a tűzfal konfigurációjához.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>12. lépés: A folyamatkiszolgáló proxybeállításainak ellenőrzése 

1. Ha proxykiszolgálót használ, győződjön meg arról, hogy a DNS-kiszolgáló feloldja a proxykiszolgáló nevét. Ellenőrizze a konfigurációs kiszolgáló beállításkulcsban való beállításakor megadott értéket **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**mappában.
2. Győződjön meg arról, hogy ugyanazokat a beállításokat használja az Azure Site Recovery ügynök adatok küldéséhez.

    a) Keressen a **Microsoft Azure Biztonsági másolat keresése**elemre.

    b) Nyissa meg a **Microsoft Azure Biztonsági másolatot,** és válassza a **Műveletmódosítás** > **tulajdonságai lehetőséget.**

    c) A **Proxy konfiguráció jalapján** a proxycímnek meg kell egyeznie a beállításjegyzék beállításaiban megjelenő proxycímmel. Ha nem, módosítsa ugyanarra a címre.

## <a name="step-13-check-bandwidth"></a>13. lépés: A sávszélesség ellenőrzése

Növelje a folyamatkiszolgáló és az Azure közötti sávszélességet, majd ellenőrizze, hogy a probléma továbbra is fennáll-e.


## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége, tegye fel kérdését az [Azure Site Recovery fórumon.](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
