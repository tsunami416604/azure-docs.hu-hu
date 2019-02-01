---
title: Diagnosztikai eszközt a StorSimple 8000-es eszköz hibáinak elhárítása |} A Microsoft Docs
description: A StorSimple eszköz módok és ismerteti a storsimple-höz készült Windows PowerShell használata az eszköz üzemmódjának módosítása.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 5cce4337e3ef95c6407d46d9b8b6401fe4f6600b
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497740"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>A StorSimple diagnosztikai eszközt használhatja a 8000-es sorozatú eszköz hibáinak elhárítása

## <a name="overview"></a>Áttekintés

A StorSimple diagnosztikai eszköz diagnosztizálja a rendszer, a teljesítmény, a hálózati és a hardver összetevő állapotát a StorSimple-eszköz kapcsolatos problémákat. A diagnosztikai eszköz használható a különböző forgatókönyvekben. Ilyen például, amikor munkaterhelés tervezési, a StorSimple eszköz üzembe helyezésének, a hálózati környezet felmérése és a egy működési eszköz teljesítményének meghatározására. Ez a cikk áttekintést a diagnosztikai eszköz, és ismerteti, hogyan használható az eszközt a StorSimple eszköz.

A diagnosztikai eszköz elsősorban a StorSimple 8000 sorozat a helyszíni eszközök (8100-as és 8600-as).

## <a name="run-diagnostics-tool"></a>Diagnosztikai eszköz futtatása

Ezt az eszközt a StorSimple eszköz a Windows PowerShell felületéről is futtathatók. Az eszköz helyi adapterének eléréséhez két módja van:

* [A PuTTY használata az eszköz soros konzoljához való kapcsolódáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Távoli elérése az eszköz a Windows PowerShell storsimple-höz készült](storsimple-8000-remote-connect.md).

Ez a cikk feltételezzük, hogy az eszköz soros konzoljához a PuTTY-n keresztül csatlakoztatta.

#### <a name="to-run-the-diagnostics-tool"></a>A diagnosztikai eszköz futtatása

Az eszköz a Windows PowerShell felületéről való csatlakozás után a következő lépésekkel futtassa a parancsmagot.
1. Jelentkezzen be a lépéseket követve az eszköz soros konzoljához [a PuTTY használata az eszköz soros konzoljához való kapcsolódáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Írja be a következő parancsot:

    `Invoke-HcsDiagnostics`

    Ha a hatókör-paraméter nincs megadva, a parancsmag végrehajtja a diagnosztikai tesztek. Ezek a tesztek közé tartozik a rendszer, a hardver összetevő állapotát, a hálózati és a teljesítmény. 
    
    Csak egy adott teszt futtatásához adja meg a hatókör-paramétert. Csak a hálózati tesztek futtatásához például írja be a

    `Invoke-HcsDiagnostics -Scope Network`

3. Válassza ki, és másolja ki a kimenetet egy szövegfájlba további elemzés céljából a PuTTY ablakból.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Forgatókönyvek a diagnosztikai eszközt használhatja

Hálózat, a teljesítmény, a rendszer és a hardver állapotát, a rendszer a diagnosztikai eszközt használhatja. Az alábbiakban néhány lehetséges forgatókönyvek:

* **Az eszköz offline** – a StorSimple 8000 sorozatú eszköz offline állapotban. Azonban a Windows PowerShell interfészről úgy tűnik, hogy mindkét vezérlő rendszer üzembe helyezéséig.
    * Használhatja ezt az eszközt, majd vizsgálja meg a hálózati állapotot.
         
         > [!NOTE]
         > A teljesítmény- és hálózati beállításokat az eszközön a regisztráció (vagy a telepítő varázsló használatával történő konfigurációval) előtt mérje fel az eszköz nem használható. Egy érvényes IP-cím hozzá van rendelve az eszköz beállítása varázsló és a regisztráció során. Ezt a parancsmagot futtathatja egy eszközön, amely nincs regisztrálva, a hardver és a rendszer. Használja például a hatókör-paramétert:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Állandó eszközzel kapcsolatos problémák** -eszközökkel kapcsolatos problémákat, amelyek úgy tűnik, hogy fennállnak tapasztal. Például a regisztráció sikertelen. Sikerült is lehet tapasztal eszközzel kapcsolatos problémák után az eszköz sikeresen regisztrált és működési egy ideig.
    * Ebben az esetben előzetes hibaelhárítási előtt Support jelentkezzen egy szolgáltatási kérelmet az eszköz használható. Azt javasoljuk, hogy futtassa ezt az eszközt, és ez az eszköz kimenete rögzítése. Majd számára a hibaelhárítás támogatásához adja meg a kimenetet.
    * Ha bármely összetevő vagy a fürt hardverhibák, meg kell jelentkezzen be egy támogatási kérést.

* **Eszköz teljesítménye alacsony** – a StorSimple eszköz lassú.
    * Ebben az esetben futtassa ezt a parancsmagot a hatókör-paramétert állítsa be a teljesítmény. A kimenet elemzése. A felhőalapú olvasási és írási késése beolvasása. Használja a jelentett késési maximális elérhető célként, némi többletterhelést okoz, a belső adatfeldolgozási figyelembe vennie, és a számítási feladatok, a rendszer telepíteni. További információért ugorjon [használata a hálózati tesztek a eszközteljesítmény](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Diagnosztikai teszt- és minta kimenetek

### <a name="hardware-test"></a>Hardver ellenőrzése

Ez a vizsgálat meghatározza, hogy a hardverösszetevők, a USM belső vezérlőprogramot és a lemezfirmware a rendszeren futó állapotát.

* A hardverösszetevők jelentett olyan összetevőket, amelyek a teszt sikertelen, vagy nem találhatók a rendszerben.
* USM lemez és a belső vezérlőprogram belsővezérlőprogram-verziói a vezérlő 0, 1. vezérlő, jelentett, és a rendszerben a megosztott összetevőit. A hardverösszetevők teljes listáját Ugrás:

    * [Az elsődleges lemezház összetevői](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Az EBOD lemezház összetevői](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Ha a hardver ellenőrzése sikertelen összetevők jelentéseket [jelentkezzen be egy szolgáltatási kérelmet a Microsoft Support](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Hardver vizsgálat futtatása egy 8100-as eszközön mintakimenete

Íme egy StorSimple 8100 sorozatú eszköz egy mintakimenete. A 8100-as modell eszközön a EBOD ház nem szerepel. Ezért az EBOD-vezérlő összetevői nem jelennek meg.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Rendszer-teszt

Ez a teszt jelenti a rendszer-információkat, a frissítések érhetők el, a fürt információkat és a adatait az eszköz.

* A rendszer-információkat tartalmazza a modellt, sorozatszámát, időzóna, tartományvezérlő állapotát és a részletes szoftververzió a rendszeren futó. A kimeneteként jelentett különböző rendszer paraméterek megértéséhez, lépjen a [értelmezése során a rendszer-információkat](#appendix-interpreting-system-information).

* A frissítés rendelkezésre állási jelentések elérhetők-e a normál és a karbantartás mód és a csomaghoz kapcsolódó nevét. Ha `RegularUpdates` és `MaintenanceModeUpdates` vannak `false`, akkor ez azt jelenti, hogy a frissítések nem érhetők el. Az eszköz naprakész.
* Fürtinformációkat ismerteti a különböző logikai összetevőit a HCS fürtcsoportot és azok megfelelő állapotát. Ha egy kapcsolat nélküli fürtcsoport ebben a szakaszban a jelentés [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* A szolgáltatás információk közé tartozik a nevét és az eszközön futó összes HCS és a CIS szükségességét szolgáltatás állapota. Ez az információ hasznos lehet a a Microsoft Support az eszköz a probléma elhárításához.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>A rendszer vizsgálat futtatása egy 8100-as eszközön kimeneti példa

Itt látható egy mintakimenet, a rendszer vizsgálat futtatása egy 8100-as eszközön.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Hálózati tesztek

A hálózati adapterek, portok, DNS és NTP kapcsolat a kiszolgálóval, SSL tanúsítvány, tárfiók hitelesítő adatait, az Update-kiszolgálók elérhetőségének és webes proxy kapcsolat a StorSimple eszköz állapotát ellenőrzi.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Hálózati mintakimenete tesztelése, ha csak DATA0 engedélyezve van

Itt látható egy mintakimenet a 8100-as eszköz. A kimenetben láthatja, hogy:
* Csak a DATA 0 és adatok 1 hálózati adapter engedélyezve és konfigurálva.
* 2 – 5 adatok nem engedélyezettek a portálon.
* A DNS-kiszolgáló konfigurációjának érvényes, és az eszközök csatlakozhatnak a DNS-kiszolgálón keresztül.
* Az NTP-kiszolgáló csatlakozási emellett nem okoz gondot.
* 80-as és 443-as port meg nyitva. Azonban 9354-es port le van tiltva. Alapján a [hálózati rendszerkövetelmények](storsimple-system-requirements.md), meg kell nyitnia a portot, a service bus-kommunikációhoz.
* Az SSL-tanúsítvány érvényességét.
* Az eszköz csatlakozhat a tárfiókhoz: _myss8000storageacct_.
* Az Update-kiszolgálók elérhetőségének érvényességét.
* A webalkalmazás-proxy nincs konfigurálva ezen az eszközön.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Hálózati teszt DATA0 és adat1 engedélyezésekor. kimeneti példa

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Teljesítményteszt

Ez a vizsgálat a felhő teljesítmény keresztül az eszköz a felhőalapú olvasási és írási késése jelentések. Ezzel az eszközzel használható, amely akkor érhető el a StorSimple felhőalapú teljesítményének az alapértékeket. Az eszköz jelenti a legjobb teljesítményt (legkedvezőbb eset az olvasási és írási késés érdekében), kérheti, hogy a kapcsolat.

Az eszköz jelenti a legnagyobb elérhető teljesítmény, mivel használhatjuk a jelentett olvasási és írási késése célként a számítási feladatok üzembe helyezésekor.

A teszt szimulálja az eszközön a különböző kötettípusok társított méretű blobok. Normál rétegzett, és a gyors helyi kötetek biztonsági másolatait használhatja egy 64 KB-os blob mérete. Archív lehetőség be van jelölve a rétegzett kötetek használata blob mérete 512 KB. Ha az eszközön rétegzett és a helyileg rögzített kötetek konfigurálni, csak a teszt futtatása az adatok mérete 64 KB-os blobot megfelelő.

Az eszköz használatához hajtsa végre az alábbi lépéseket:

1.  Először is hozzon létre egy rétegzett kötetek és a rétegzett kötetek kombinációját archivált lehetőség be van jelölve. Ez a művelet biztosítja, hogy az eszközt futtatja a teszteket 64 KB-os és 512 KB méretű blobok.

2. Miután létrehozott és a kötetek konfigurálni, futtassa a parancsmagot. Típus:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Jegyezze fel az olvasási és írási késése, az eszköz által jelentett. Ez a teszt azt jelenti, hogy az eredmények végrehajtása több percig is eltarthat.

4. Ha a kapcsolat késési minden a várt tartományba alatt, majd az eszköz által jelentett az késleltetések használható maximális elérhető célként a számítási feladatok üzembe helyezésekor. Némi többletterhelést okoz, az adatfeldolgozás belső figyelembe vennie.

    Ha az olvasási és írási késése, a diagnosztikai eszköz által jelentett magas:

    1. A Storage Analytics konfigurálása a blob serviceshez, és elemezheti a kimenet az Azure storage-fiók az késleltetések megértéséhez. Részletes útmutatásért lépjen [engedélyezheti és konfigurálhatja a Storage Analytics](../storage/common/storage-enable-and-view-metrics.md). Ha ezeket a késésük is magas és a StorSimple diagnosztikai eszköz kapott számok hasonló, majd meg kell bejelentkeznie szolgáltatáskérés és az Azure storage.

    2. Ha alacsony a storage-fiók késéseket, a hálózati rendszergazdától a hálózaton lévő bármely késési problémák kivizsgálásában.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Vizsgálat futtatása egy 8100-as eszközön mintakimenete

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>A függelék: rendszeradatok értelmezése

Íme egy táblázat a különböző Windows PowerShell paraméterek a rendszer információkat leképezés. 

| PowerShell-paraméter    | Leírás  |
|-------------------------|------------------|
| Instance ID (Példányazonosító)             | Minden tartományvezérlő egy egyedi azonosítóval rendelkezik, vagy egy GUID Azonosítót társítva.|
| Name (Név)                    | Az eszköz üzembe helyezése során az Azure Portalon konfigurált eszköz rövid neve. Az alapértelmezett rövid név az eszköz sorozatszámát. |
| Modell                   | A StorSimple 8000 sorozatú eszköz modelljét. A modell a 8100-as vagy 8600-as lehet.|
| a sorozatszám            | Az eszköz sorozatszáma gyári van rendelve, és 15 karakter hosszú. Például 8600-SHX0991003G44HT jelöli:<br> 8600 – az eszköz modellje.<br>SHX – a gyártási hely.<br> 0991003 – egy bizonyos termékben van. <br> G44HT-hozhat létre egyedi sorozatszáma eggyel növeli az utolsó 5 számjegy. Ez egy szekvenciális beállítása nem lehet.|
| Időzóna                | Az eszköz időzónáját eszköz üzembe helyezése során konfigurált az Azure Portalon.|
| CurrentController       | A vezérlő, amely a Windows PowerShell felületét a StorSimple eszköz keresztül csatlakozik.|
| ActiveController        | A tartományvezérlővel, amely aktív az eszközön, és vezérli a hálózati és lemezkonfigurációját műveleteket. Ez lehet 0. vezérlő vagy a vezérlő 1.  |
| Controller0Status       | 0. vezérlő az eszköz állapotát. A vezérlő állapota lehet normál, helyreállítási módban, vagy nem érhető el.|
| Controller1Status       | Vezérlő 1 állapota az eszközön.  A vezérlő állapota lehet normál, helyreállítási módban, vagy nem érhető el.|
| SystemMode              | A StorSimple-eszközök általános állapotát. Az eszköz állapota lehet normál, karbantartási, vagy a leszerelt (felel meg az Azure Portalon inaktiválva).|
| FriendlySoftwareVersion | A rövid karakterlánc, amely megfelel az eszköz szoftververzióján. A rendszer a 4-es frissítést futtatja a rövid szoftververzió lenne a StorSimple 8000 Series Update 4.0-s.|
| HcsSoftwareVersion      | A HCS szoftver verziója fut az eszközön. Például az a StorSimple 8000 Series Update 4.0-s megfelelő HCS szoftververzió 6.3.9600.17820. |
| ApiVersion              | A Windows PowerShell API-ját a HCS eszköz szoftverének verziójával.|
| VhdVersion              | A gyári lemezképről, az eszköz a teljesített szoftverének verziójával. Az eszköz gyári alaphelyzetbe, majd futtatja a szoftververzió.|
| OSVersion               | A Windows Server operációs rendszer az eszközön futó szoftverének verziójával. A StorSimple-eszközön a Windows Server 2012 R2 megfelelő 6.3.9600 alapul.|
| CisAgentVersion         | A Cis-ügynök a StorSimple eszközön futó verzió. Ez az ügynök segít az Azure-ban futó StorSimple Manager szolgáltatással folytatott kommunikációhoz.|
| MdsAgentVersion         | Az a StorSimple eszközön futó Mds-ügynök megfelelő verziója. Ezt az ügynököt helyez át adatokat a megfigyelési és diagnosztikai szolgáltatás (MDS).|
| Lsisas2Version          | A megfelelő a LSI-illesztőprogramokat, a StorSimple eszköz verziója.|
| Kapacitás                | A teljes kapacitás (bájt) az eszköz.|
| RemoteManagementMode    | Azt jelzi, hogy az eszköz távolról felügyelhetik a Windows PowerShell felületéről. |
| FipsMode                | Azt jelzi, hogy engedélyezve van-e az Amerikai Egyesült Államok Szövetségi információk feldolgozása Standard (FIPS) mód az eszközön. A FIPS 140 szabvány határozza meg, bizalmas adatok védelme az USA szövetségi kormányzati számítógépes rendszerek által jóváhagyott titkosítási algoritmusokat. A 4-es vagy újabb frissítést futtató eszközök esetében FIPS-módban alapértelmezés szerint engedélyezve van. |

## <a name="next-steps"></a>További lépések

* Ismerje meg a [az Invoke-HcsDiagnostics parancsmag szintaxisát](https://technet.microsoft.com/library/mt795371.aspx).

* Ismerje meg, hogyan [üzembe helyezés hibáinak elhárítása](storsimple-troubleshoot-deployment.md) a StorSimple eszközön.
