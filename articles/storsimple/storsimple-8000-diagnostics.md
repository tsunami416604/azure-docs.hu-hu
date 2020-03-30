---
title: Diagnosztikai eszköz a StorSimple 8000 eszköz hibaelhárításához | Microsoft dokumentumok
description: A StorSimple eszközmódok ismertetése, és bemutatja, hogyan használhatja a Windows PowerShell for StorSimple eszközmód módosítása.
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
ms.openlocfilehash: 48bd909eefbaea15cf6ca2427e106ad9bc0ffbb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298747"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>A StorSimple diagnosztikai eszközzel hárítsa el a 8000-es sorozatú eszközproblémákat

## <a name="overview"></a>Áttekintés

A StorSimple Diagnosztika eszköz diagnosztizálja a rendszer, a teljesítmény, a hálózat és a hardverösszetevők állapotával kapcsolatos problémákat egy StorSimple-eszköz esetében. A diagnosztikai eszköz különböző esetekben használható. Ezek a forgatókönyvek közé tartozik a számítási feladatok tervezése, egy StorSimple-eszköz üzembe helyezése, a hálózati környezet felmérése és egy működő eszköz teljesítményének meghatározása. Ez a cikk áttekintést nyújt a diagnosztikai eszközről, és ismerteti, hogyan használható az eszköz egy StorSimple eszközzel.

A diagnosztikai eszköz elsősorban a StorSimple 8000 sorozatú helyszíni eszközökhöz (8100 és 8600) készült.

## <a name="run-diagnostics-tool"></a>Diagnosztikai eszköz futtatása

Ez az eszköz a StorSimple-eszköz Windows PowerShell felületén keresztül futtatható. Az eszköz helyi felületét kétféleképpen érheti el:

* [A PuTTY segítségével csatlakozzon a készülék soros konzoljához](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Az eszköz távolról elérhető a Windows PowerShell for StorSimple rendszeren keresztül.](storsimple-8000-remote-connect.md)

Ebben a cikkben feltételezzük, hogy a PuTTY-n keresztül csatlakozott az eszköz soros konzoljához.

#### <a name="to-run-the-diagnostics-tool"></a>A diagnosztikai eszköz futtatása

Miután csatlakozott az eszköz Windows PowerShell-felületéhez, hajtsa végre a parancsmag futtatásához szükséges alábbi lépéseket.
1. Jelentkezzen be az eszköz soros konzoljára a [PuTTY használata az eszköz soros konzoljához való csatlakozáslépései](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)vel.

2. Írja be a következő parancsot:

    `Invoke-HcsDiagnostics`

    Ha a hatókör paraméter nincs megadva, a parancsmag végrehajtja az összes diagnosztikai tesztet. Ezek a tesztek magukban foglalják a rendszer, a hardverösszetevők állapotát, a hálózatot és a teljesítményt. 
    
    Ha csak egy adott tesztet szeretne futtatni, adja meg a hatókör paramétert. Ha például csak a hálózati tesztet szeretné futtatni, írja be a

    `Invoke-HcsDiagnostics -Scope Network`

3. További elemzés céljából jelölje ki és másolja a Kimenetet a PuTTY ablakból egy szöveges fájlba.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>A diagnosztikai eszköz használatának forgatókönyvei

A diagnosztikai eszközzel elháríthatja a rendszer hálózati, teljesítmény-, rendszer- és hardverállapotának elhárítását. Íme néhány lehetséges forgatókönyv:

* **Eszköz offline –** A StorSimple 8000 sorozatú eszköz offline állapotban van. Azonban a Windows PowerShell felület, úgy tűnik, hogy mindkét vezérlő működik.
    * Ezzel az eszközzel meghatározhatja a hálózati állapotot.
         
         > [!NOTE]
         > Ne használja ezt az eszközt az eszköz teljesítményének és hálózati beállításainak felmérésére a regisztráció előtt (vagy a beállítási varázslón keresztüli konfigurálás) előtt. A telepítővarázsló és a regisztráció során érvényes IP-cím van hozzárendelve az eszközhöz. Ezt a parancsmatot futtathatja egy nem regisztrált eszközön a hardver állapotés a rendszer. Használja a hatókör paramétert, például:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Állandó eszközproblémák** – Olyan eszközproblémákat tapasztal, amelyek látszólag továbbra is fennállnak. Például a regisztráció sikertelen. Előfordulhat, hogy az eszköz sikeres regisztrálása és működése után is előfordulhat, hogy eszközproblémákat tapasztal.
    * Ebben az esetben használja ezt az eszközt az előzetes hibaelhárításhoz, mielőtt a Microsoft támogatási szolgálatával naplózna egy szolgáltatási kérelmet. Javasoljuk, hogy futtassa ezt az eszközt, és rögzítse az eszköz kimenetét. Ezután ezt a kimenetet a támogatás a hibaelhárítás felgyorsítása érdekében.
    * Ha hardverösszetevő vagy fürthiba történt, jelentkezzen be egy támogatási kérelmet.

* **Alacsony eszközteljesítmény** – A StorSimple eszköz lassú.
    * Ebben az esetben futtassa ezt a parancsmamot a teljesítményre beállított hatókörparaméterrel. Elemezze a kimenetet. A felhő olvasási és írási késése. Használja a jelentett késések maximális elérhető célként, tényező a belső adatfeldolgozás némi többletterhelést, majd telepítse a számítási feladatokat a rendszeren. További információt az [Eszköz teljesítményével kapcsolatos hibaelhárításhoz a hálózati teszt használata című](#network-test)témakörben talál.


## <a name="diagnostics-test-and-sample-outputs"></a>Diagnosztikai vizsgálat és mintakimenetek

### <a name="hardware-test"></a>Hardverteszt

Ez a teszt határozza meg a hardverösszetevők, az USM belső vezérlőprogramja és a rendszeren futó lemez belső vezérlőprogramja állapotát.

* A jelentett hardverösszetevők azok az összetevők, amelyek nem feleltmeg a teszten, vagy nem szerepelnek a rendszerben.
* Az USM belső vezérlőprogramjának és lemezbelső vezérlőprogramjának verziói a 0 vezérlő, az 1 vezérlő és a rendszer megosztott összetevőinek jelentése. A hardverösszetevők teljes listáját a következő lapban lehet megkeresni:

    * [Alkatrészek az elsődleges burkolatban](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Összetevők az EBOD házban](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Ha a hardverteszt sikertelen összetevőket jelent, [jelentkezzen be egy szolgáltatáskérelmet a Microsoft támogatási szolgálatával.](storsimple-8000-contact-microsoft-support.md)

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>A hardveres teszt mintakimenete 8100-as eszközön

Itt egy storSimple 8100-as eszköz mintakimenete látható. A 8100-as modelleszközben az EBOD-ház nincs jelen. Ezért az EBOD vezérlő összetevői nem jelentik.

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

### <a name="system-test"></a>Rendszervizsgálat

Ez a teszt jelenti a rendszeradatokat, a rendelkezésre álló frissítéseket, a fürtinformációkat és az eszköz szolgáltatásadatait.

* A rendszerinformáció tartalmazza a modellt, az eszköz sorozatszámát, az időzónát, a vezérlő állapotát és a rendszeren futó részletes szoftververziót. A kimenetként jelentett különböző rendszerparaméterek megértéséhez nyissa meg a Rendszeradatok értelmezése című [lapot.](#appendix-interpreting-system-information)

* A frissítés rendelkezésre állási jelentések, hogy a rendszeres és karbantartási módok állnak rendelkezésre, és a hozzájuk tartozó csomag nevét. Ha `RegularUpdates` `MaintenanceModeUpdates` és `false`vannak is, ez azt jelzi, hogy a frissítések nem érhetők el. A készülék naprakész.
* A fürtinformációk az összes HCS-fürtcsoport különböző logikai összetevőire és azok állapotára vonatkozó információkat tartalmazzák. Ha a jelentés nek ebben a szakaszában offline fürtcsoport jelenik meg, forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md)
* A szolgáltatás adatai tartalmazzák az eszközön futó összes HCS- és CiS-szolgáltatás nevét és állapotát. Ez az információ hasznos a Microsoft támogatási szolgálata számára az eszközprobléma elhárításában.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>A rendszer tesztfutásának mintakimenete 8100-as eszközön

Itt van egy minta kimeneta a rendszer teszt fut egy 8100 eszköz.

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

### <a name="network-test"></a>Hálózati teszt

Ez a teszt ellenőrzi a hálózati adapterek, portok, DNS- és NTP-kiszolgáló-kapcsolat, TLS/SSL-tanúsítvány, tárfiók hitelesítő adatai, a frissítési kiszolgálókhoz való kapcsolódás és a Webproxy-kapcsolat állapotát a StorSimple eszközön.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>A hálózati teszt mintakimenete, ha csak a DATA0 van engedélyezve

Itt van egy minta kimenet a 8100 eszköz. Láthatjuk a kimenet, hogy:
* Csak a DATA 0 és a DATA 1 hálózati csatolók vannak engedélyezve és konfigurálva.
* Data 2 - 5 nincs engedélyezve a portálon.
* A DNS-kiszolgáló konfigurációja érvényes, és az eszköz a DNS-kiszolgálón keresztül is csatlakozhat.
* Az NTP-kiszolgáló kapcsolata is rendben van.
* A 80-as és a 443-as port nyitva van. A 9354-es port azonban le van zárva. A [rendszerhálózati követelmények](storsimple-system-requirements.md)alapján meg kell nyitnia ezt a portot a szolgáltatásbusz-kommunikációhoz.
* A TLS/SSL tanúsítvány érvényes.
* Az eszköz csatlakozhat a tárfiókhoz: _myss8000storageacct_.
* A frissítési kiszolgálókkal való kapcsolat érvényes.
* A webproxy nincs konfigurálva ezen az eszközön.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>A hálózati teszt mintakimenete, ha a DATA0 és a DATA1 engedélyezve van

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

Ez a teszt a felhő teljesítményét jelenti a felhőolvasási és -írási késéseken keresztül az eszközre. Ez az eszköz a StorSimple-szel elérhető felhőteljesítmény alapkonfigurációjának létrehozásához használható. Az eszköz a kapcsolathoz beszerezhető maximális teljesítményt (az olvasási és írási késések legjobb esetét) jelenti.

Az eszköz jelenti a maximális elérhető teljesítményt, használhatjuk a jelentett írási és írási késések a számítási feladatok üzembe helyezésekor.

A teszt szimulálja a blob méretek társított különböző kötettípusok az eszközön. A helyileg rögzített kötetek rendszeres rétegzett és biztonsági mentései 64 KB-os blobméretet használnak. Rétegzett kötetek archiválási beállítással bevan jelölve 512 KB blob adatméretet használ. Ha az eszköz rétegzett és helyileg rögzített kötetek konfigurálva, csak a 64 KB blob adatméretnek megfelelő teszt fut.

Az eszköz használatához hajtsa végre az alábbi lépéseket:

1.  Először hozzon létre egy vegyes rétegzett kötetek és rétegzett kötetek archivált beállítás be van jelölve. Ez a művelet biztosítja, hogy az eszköz fut a tesztek mind a 64 KB és 512 KB blob méretek.

2. Futtassa a parancsmast a kötetek létrehozása és konfigurálása után. Típus:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Jegyezze fel az eszköz által jelentett írási és írási késéseket. Ez a teszt több percig is eltarthat, mielőtt jelenti az eredményeket.

4. Ha a kapcsolat késése imények vannak a várt tartományban, majd az eszköz által jelentett késések maximálisan elérhető célként használható a számítási feladatok üzembe helyezésekor. A belső adatfeldolgozás némi többletterhelése.

    Ha a diagnosztikai eszköz által jelentett olvasási/írási késések magasak:

    1. Konfigurálja a Storage Analytics blobszolgáltatásokhoz, és elemezze a kimenetet az Azure storage-fiók késéseinek megismeréséhez. A részletes útmutatásért nyissa meg a [Storage Analytics engedélyezését és konfigurálását.](../storage/common/storage-enable-and-view-metrics.md) Ha ezek a késések is magas, és összehasonlítható a StorSimple Diagnosztika eszköz től kapott számokkal, majd naplóznia kell egy szolgáltatáskérelmet az Azure Storage-szal.

    2. Ha a tárfiók késése alacsony, forduljon a hálózati rendszergazdához a hálózat esetleges késési problémáinak kivizsgálásához.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>A teljesítményteszt mintakimenete 8100-as eszközön

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

## <a name="appendix-interpreting-system-information"></a>Függelék: a rendszerinformációk értelmezése

Az alábbiakban egy táblázat ismerteti, hogy a rendszerinformációs térképben a különböző Windows PowerShell-paraméterek mire szolgálnak. 

| PowerShell-paraméter    | Leírás  |
|-------------------------|------------------|
| Instance ID (Példányazonosító)             | Minden vezérlőhöz tartozik egy egyedi azonosító vagy egy GUID.|
| Név                    | Az eszköz rövid neve az Azure Portalon keresztül konfigurálva az eszköz üzembe helyezése során. Az alapértelmezett rövid név az eszköz sorozatszáma. |
| Modell                   | A StorSimple 8000 sorozatú eszköz modellje. A modell lehet 8100 vagy 8600.|
| Sorozatszám            | Az eszköz sorozatszáma a gyárban van hozzárendelve, és 15 karakter hosszú. Például a 8600-SHX0991003G44HT a következőket jelzi:<br> 8600 – Ez az eszköz modell.<br>SHX - A gyártási helyén.<br> 0991003 - Egy adott termék. <br> G44HT- az utolsó 5 számjegy növekszik, hogy egyedi sorozatszámokat hozzon létre. Lehet, hogy ez nem egymást követő készlet.|
| TimeZone                | Az eszköz időzónája az Azure Portalon konfigurálva az eszköz üzembe helyezése során.|
| CurrentController       | A storSimple-eszköz Windows PowerShell-felületén keresztül csatlakoztatott vezérlő.|
| ActiveController        | Az eszközön aktív vezérlő, amely az összes hálózati és lemezműveletet vezérel. Ez lehet a Controller 0 vagy a Controller 1.  |
| Controller0Status       | A Controller 0 állapota az eszközön. A vezérlő állapota lehet normális, helyreállítási módban, vagy elérhetetlen.|
| Controller1Status       | Az eszköz Controller 1 állapotának állapota.  A vezérlő állapota lehet normális, helyreállítási módban, vagy elérhetetlen.|
| SystemMode mód              | A StorSimple-eszköz általános állapota. Az eszköz állapota lehet normál, karbantartási vagy leszerelt (megfelel az Azure Portalin inaktivált).|
| FriendlySoftwareVersion | Az eszköz szoftververziójának megfelelő barátságos karakterlánc. A 4-es frissítést futtató rendszer esetén a barátságos szoftververzió a StorSimple 8000 Series 4.0-s frissítése lenne.|
| HcsSoftwareVersion      | A HCS szoftver verziója fut a készüléken. A StorSimple 8000 Series 4.0-s verziójának megfelelő HCS szoftververzió például 6.3.9600.17820. |
| ApiVersion              | A HCS-eszköz Windows PowerShell API-jának szoftververziója.|
| VhdVersion (VhdVersion)              | A gyári lemezkép szoftververziója, amelyhez az eszközt szállították. Ha visszaállítja az eszközt a gyári alapértékekre, akkor ez a szoftververzió fut.|
| OSVersion               | Az eszközön futó Windows Server operációs rendszer szoftververziója. A StorSimple eszköz a 6.3.9600-nak megfelelő Windows Server 2012 R2 rendszeren alapul.|
| CisAgentVersion         | A StorSimple-eszközön futó CIS-ügynök verziója. Ez az ügynök segít kommunikálni az Azure-ban futó StorSimple Manager szolgáltatással.|
| MdsAgentVersion         | A StorSimple-eszközön futó Mds-ügynöknek megfelelő verzió. Ez az ügynök adatokat helyezi át a figyelési és diagnosztikai szolgáltatás (MDS).|
| Lsisas2Verzió          | A StorSimple-eszközön lévő LSI-illesztőprogramok megfelelő verzió.|
| Kapacitás                | Az eszköz teljes kapacitása bájtban.|
| RemoteManagementMode mód    | Azt jelzi, hogy az eszköz távolról kezelhető-e a Windows PowerShell-felületén keresztül. |
| FipsMode (FipsMode)                | Azt jelzi, hogy az Egyesült Államok Szövetségi Információfeldolgozási Szabvány (FIPS) üzemmódja engedélyezve van-e az eszközön. A FIPS 140 szabvány az Egyesült Államok szövetségi szövetségi számítógépes rendszerei által a bizalmas adatok védelmére jóváhagyott kriptográfiai algoritmusokat határozza meg. A 4- es vagy újabb frissítést futtató eszközök esetében a FIPS mód alapértelmezés szerint engedélyezve van. |

## <a name="next-steps"></a>További lépések

* Ismerje meg [az Invoke-HcsDiagnostics parancsmag szintaxisát.](https://technet.microsoft.com/library/mt795371.aspx)

* További információ a StorSimple-eszközön felmerülő [telepítési problémák elhárításáról.](storsimple-troubleshoot-deployment.md)
