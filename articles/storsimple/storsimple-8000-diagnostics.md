---
title: Diagnosztikai eszköz, a StorSimple 8000 eszköz hibáinak elhárítása |} Microsoft Docs
description: A StorSimple eszköz módok és használatához a Windows PowerShell-lel módosíthatja eszköz módját ismerteti.
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
ms.openlocfilehash: 7199009553eb7aae31db3f913fe4de87e03d74ba
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
ms.locfileid: "27744112"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>A StorSimple diagnosztikai eszköz segítségével 8000 sorozat eszközök kapcsolatos problémák elhárítása

## <a name="overview"></a>Áttekintés

A StorSimple diagnosztikai eszköz diagnosztizálja a rendszer, a teljesítmény, a hálózati és a hardver összetevő állapota a StorSimple eszköz kapcsolatos problémákat. A diagnosztikai eszköz használható a különböző forgatókönyvekben. Ilyen például, alkalmazások és szolgáltatások tervezése, a StorSimple eszköz üzembe helyezése, a hálózati környezet értékeléséhez és az operatív eszközök teljesítményének meghatározására. Ez a cikk áttekintést a diagnosztikai eszköz, és ismerteti, hogyan használható az eszközt a StorSimple eszköz.

A diagnosztikai eszköz elsődlegesen a StorSimple 8000 series a helyszíni eszközök (8100 és 8600).

## <a name="run-diagnostics-tool"></a>Diagnosztikai eszköz futtatása

Ezt az eszközt a StorSimple eszköz a Windows PowerShell felületén keresztül is futtatható. A helyi kapcsolat az eszköz eléréséhez két módja van:

* [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Távoli eléréséhez a Windows PowerShell eszközt a StorSimple](storsimple-8000-remote-connect.md).

Ebben a cikkben azt feltételezzük, hogy az eszköz soros konzoljához PuTTY keresztül csatlakoztatott.

#### <a name="to-run-the-diagnostics-tool"></a>A diagnosztikai eszköz futtatása

Miután csatlakozott az eszköz a Windows PowerShell felületén, hajtsa végre az alábbi lépéseket a parancsmag futtatásához.
1. Jelentkezzen be az eszköz soros konzoljához lépéseit követve [a PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Írja be a következő parancsot:

    `Invoke-HcsDiagnostics`

    Ha a hatókör-paraméter nincs megadva, a parancsmag a diagnosztikai tesztek hajtja végre. Ezek a tesztek közé tartozik a rendszer, a hardver összetevő állapotát, a hálózati és a teljesítményt. 
    
    Csak egy adott teszt futtatásához, adja meg a hatókör-paramétert. Például csak a hálózati tesztek futtatásához írja be

    `Invoke-HcsDiagnostics -Scope Network`

3. Jelölje ki, és a kimeneti másolja a PuTTY ablakból egy szövegfájlba további elemzés céljából.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>A diagnosztikai eszköz forgatókönyvek

A diagnosztikai eszköz segítségével hibaelhárítása a rendszer a hálózaton, a teljesítmény, a rendszer és a hardver állapotát. Az alábbiakban néhány lehetséges forgatókönyv szerint:

* **Az eszköz offline** -a StorSimple 8000 series eszköz offline állapotban. Azonban a Windows PowerShell felületén, úgy tűnik, hogy mindkét vezérlőhöz megfelelően működik.
    * Az eszköz segítségével ellenőrizze a hálózati állapotot.
         
         > [!NOTE]
         > Az eszköz nem használható fel annak ellenőrzéséhez, teljesítmény- és hálózati beállításokat a regisztrációs (vagy keresztül telepítővarázsló konfigurálása) előtt egy eszközön. Egy érvényes IP-cím hozzá van rendelve az eszköz telepítővarázslóját, és a regisztráció során. Olyan eszköz, amely nincs regisztrálva, a hardver és a rendszer ezt a parancsmagot futtathatja. Használja például a hatókör-paramétert:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Állandó eszközökkel kapcsolatos problémákat** -eszközökkel kapcsolatos problémákat, amelyek az adatok megőrzéséhez tapasztalja. Például regisztrációja sikertelen. Sikerült is lehet tapasztal eszközökkel kapcsolatos problémákat Miután az eszköz sikeresen regisztrált és működési egy ideig.
    * Ebben az esetben ezzel az eszközzel előzetes hibaelhárítási, mielőtt bejelentkezik egy szolgáltatási kérelem Microsoft Support. Azt javasoljuk, hogy az eszköz futtatásához, és ez az eszköz kimenetét rögzíti. Ezután az hibaelhárítás elősegítésére támogatáshoz adja meg a kimenetet.
    * Minden összetevő vagy a fürt hardverhibák esetén be kell jelentkezni egy támogatási kérést.

* **Kevés a Teljesítmény eszköz** -a StorSimple eszköz lassú.
    * Ebben az esetben futtassa ezt a parancsmagot a teljesítmény beállítása hatókör-paramétert. Vizsgálja meg a kimenetet. A felhő írható-olvasható késések beolvasása. A jelentésben szereplő késleltetések maximális elérhető célként használni, számításba a némi többletterhelést okoz, a belső az adatok feldolgozásához, és telepíteni a feladatait, a rendszer. További információkért látogasson el [a hálózati tesztek segítségével eszköz elhárítása](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Diagnosztika teszt- és minta kimenet

### <a name="hardware-test"></a>Hardver ellenőrzése

Ez a vizsgálat meghatározza a hardverösszetevők, a legpontosabb Beállításhoz belső vezérlőprogram és a lemez belső vezérlőprogramját a rendszeren futó állapotát.

* A jelentett hardverösszetevőket összetevőket, a teszt sikertelen, vagy nincsenek jelen a rendszerben.
* A legpontosabb Beállításhoz belső vezérlőprogram és lemez belsővezérlőprogram-verziók a vezérlő 0, a vezérlő 1 jelzett, és a rendszer a megosztott összetevőit. A teljes listáját, és hardverösszetevők Ugrás:

    * [Elsődleges szolgáltatással összetevők](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Összetevők EBOD szolgáltatással](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Ha a hardver teszt eredményeként összetevő, [jelentkezzen be Microsoft Support szolgáltatáskérés](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Minta kimenet hardveren végzett ellenőrzéshez egy 8100-eszközön fut

Íme egy minta kimenet egy StorSimple 8100 eszközről. A EBOD ház nincs jelen a 8100-as modell eszközhöz. Emiatt a EBOD vezérlő összetevők nem jelennek meg.

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

Ez a vizsgálat a rendszer-információkat, a rendelkezésre álló frissítések, a fürt információkat és a szolgáltatás adatait az eszköz jelentéseket.

* A rendszer-információkat tartalmazza a modell, sorozatszámát, időzóna, tartományvezérlő állapotát, és a rendszeren futó részletes szoftververzió. A kimeneti jelentett különböző rendszer paraméterek megértéséhez, keresse fel [Rendszerinformáció értelmezése](#appendix-interpreting-system-information).

* A frissítés rendelkezésre állási jelenti, hogy elérhetők-e a normál és a karbantartási mód és a csomaghoz kapcsolódó nevek. Ha `RegularUpdates` és `MaintenanceModeUpdates` vannak `false`, ez azt jelzi, hogy a frissítések nem érhetők el. Az eszköz naprakész állapotban.
* A fürt adatokat a a HCS fürt összes csoport és a hozzájuk megfelelő állapotok logikai összetevők információt tartalmaz. Ha megjelenik egy kapcsolat nélküli fürtcsoport ebben a szakaszban a jelentés [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* A szolgáltatás információk közé tartozik, a neveket és az eszközön futó összes HCS és a CIS szükségességét Services állapotok. Ez az információ akkor hasznos, for a Microsoft Support az eszköz a probléma elhárításához.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Minta kimenet egy 8100-eszközön fut, a rendszer vizsgálat

Íme egy minta kimenet, a rendszer vizsgálat futtatása egy 8100-as eszközön.

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
hcs_management_servic         Online HCS Cluster Group
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

A hálózati adapterek, portok, a DNS és NTP kapcsolat a kiszolgálóval, SSL tanúsítvány, tárfiók hitelesítő adatait, a frissítési kiszolgálók és webes proxy kapcsolatot a StorSimple eszköz állapotát ellenőrzi.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Minta kimenet hálózat csak DATA0 engedélyezésekor a rendszer tesztelése

Íme egy minta kimenet a 8100-eszköz. A kimenet látható, amely:
* Csak DATA 0 DATA 1 hálózati adapter engedélyezve és konfigurálva.
* 2 – 5 adatok nincsenek engedélyezve a portálon.
* Érvénytelen, a DNS-kiszolgáló konfigurációját, és az eszközök csatlakozhatnak a DNS-kiszolgálón keresztül.
* Az NTP-kiszolgáló kapcsolatát is rendben.
* 80-as és 443-as port nyitva. Azonban 9354-es port blokkolva van. Alapján a [hálózati rendszerkövetelmények](storsimple-system-requirements.md), meg kell nyitni ezt a portot, a service bus való kommunikációhoz.
* Az SSL-tanúsítvány érvénytelen.
* Az eszközök csatlakozhatnak a tárfiók: _myss8000storageacct_.
* Az Update-kiszolgálókon kapcsolat érvénytelen.
* A webalkalmazás-proxy nincs beállítva ezen az eszközön.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Minta kimenet hálózati vizsgálat DATA0 és adat1 engedélyezésekor.

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

Ez a vizsgálat a jelentés a felhő teljesítmény keresztül a felhő írható-olvasható késések fordulnak elő az eszközhöz. Ez az eszköz segítségével érhető el a StorSimple felhő teljesítménybeli meghatározásához. Az eszköz a maximális teljesítmény (ajánlott eset az olvasási és írási késések), amely a kapcsolat kaphat jelenti.

Az eszköz jelenti a legnagyobb elérhető teljesítmény, azt használatával a jelentésben szereplő késleltetések írható-olvasható célként a munkaterhelések telepítésekor.

A vizsgálat a blob mérete, a másik kötetre típusok, az eszközön társított szimulálja. Rendszeres rétegzett, és a helyileg rögzített kötetek biztonsági mentések használja a 64 KB-os blob mérete. Rétegzett kötetek archív beállítás be van jelölve a 512 KB blob adatok mérete. Ha az eszköznek rétegzett és helyileg rögzített kötetek konfigurált, csak a megfelelő adatok mérete futtatása 64 KB-os blob vizsgálat.

Az eszköz használatához hajtsa végre az alábbi lépéseket:

1.  Először hozzon létre a rétegzett kötetek és a rétegzett kötetek archivált beállítás be van jelölve. Ez a művelet biztosítja, hogy az eszköz a teszteket 64 KB-os és 512 KB blob mérete.

2. Miután létrehozott és a kötetek konfigurált, futtassa a parancsmagot. Típus:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Jegyezze fel az eszköz által jelentett írható-olvasható késések fordulnak elő. Ez a vizsgálat futtatása előtt a rendszer jelzi, hogy az eredmények több percig is eltarthat.

4. Ha a kapcsolat a várt tartományon a vannak, majd a késések fordulnak elő a eszköz által használható maximális elérhető célként a munkaterhelések telepítésekor. Figyelembe a belső adatkezelési némi többletterhelést okoz.

    Ha az írható-olvasható késések fordulnak elő a diagnosztikai eszköz által jelentett magas:

    1. Tárolási analitika konfigurálása a blob-szolgáltatásokhoz, és elemezheti az az Azure storage-fiók késések megértése. A részletes utasításokat lásd [engedélyezheti és konfigurálhatja a tárolási analitika](../storage/common/storage-enable-and-view-metrics.md). Ha ezek késések is magas és a StorSimple diagnosztikai eszköz kapott számok hasonló, majd kell bejelentkeznie egy szolgáltatási kérelmet az Azure storage szolgáltatással.

    2. Ha a tárolási fiók késések alacsony, a hálózati rendszergazdától vizsgálja meg a hálózati késés hibát.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Minta kimenet teljesítmény vizsgálat futtatása egy 8100-eszközön

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

Ez a táblázat a különböző Windows PowerShell paraméterek a rendszer adatokat leképezés:. 

| PowerShell-paraméter    | Leírás  |
|-------------------------|------------------|
| Instance ID (Példányazonosító)             | Minden tartományvezérlő egyedi azonosítóval rendelkezik, vagy a vele társított egy GUID Azonosítót.|
| Name (Név)                    | Az eszköz üzembe helyezése során az Azure portálon keresztül konfigurált eszköz rövid neve. Az alapértelmezett felhasználóbarát név az eszköz sorozatszámát. |
| Modell                   | A StorSimple 8000 series eszköz modelljét. A modell 8100 vagy 8600 lehet.|
| Sorozatszám            | Az eszköz sorozatszámát gyári hozzá van rendelve, és 15 karakter hosszú. Például 8600-SHX0991003G44HT jelzi:<br> 8600 – az eszköz típusa van.<br>SHX – a gyártási hely.<br> 0991003 - termékekkel is. <br> G44HT – az utolsó 5 számjegy eggyel növekszik, egyedi sorozatszámokat létrehozásához. Ez nem lehet egy soros készlet.|
| Időzóna                | Az eszköz időzóna eszköz üzembe helyezése során az Azure portálon konfigurált módon.|
| CurrentController       | A tartományvezérlő a StorSimple eszközt a Windows PowerShell felületén keresztül csatlakoznak.|
| ActiveController        | A vezérlő aktív, az eszközön, és a hálózati és lemez műveletek vezérli. Ez lehet vezérlő 0 vagy 1 vezérlő.  |
| Controller0Status       | A vezérlő 0, az eszköz állapota. Lehet, hogy a vezérlő állapotát normál, helyreállítási módban, vagy nem érhető el.|
| Controller1Status       | A vezérlő 1 állapotát az eszköz.  Lehet, hogy a vezérlő állapotát normál, helyreállítási módban, vagy nem érhető el.|
| SystemMode              | A StorSimple eszköz általános állapota. Lehet, hogy az eszköz állapotát normál, karbantartási, vagy leszerelt (felel meg az Azure portálon inaktiválása).|
| FriendlySoftwareVersion | A rövid karakterlánc, amely megfelel az eszköz szoftverének verziójával. Az operációs rendszert futtató Update 4 a rövid szoftververzió lenne a StorSimple 8000 Series Update 4.0-s verzióját.|
| HcsSoftwareVersion      | Az eszközön futó HCS szoftverének verziójával. A StorSimple 8000 Series Update 4.0 megfelelő HCS szoftververzió például 6.3.9600.17820. |
| ApiVersion              | A Windows PowerShell API a HCS eszköz szoftverének verziójával.|
| VhdVersion              | A gyári lemezképről, az eszköz a teljesített szoftverének verziójával. Ha az eszköz visszaállítása a gyári beállítások, majd azt a szoftver verzióját futtatja.|
| OSVersion               | A szoftver az eszközön futó Windows Server operációs rendszer verziója. A StorSimple eszközt a Windows Server 2012 R2 6.3.9600 megfelelő épül.|
| CisAgentVersion         | A StorSimple eszköz futó a Cis ügynök verzióját. Ez az ügynök segít kommunikálni az Azure-beli StorSimple Manager szolgáltatással.|
| MdsAgentVersion         | A megfelelő az Mds-ügynököt a StorSimple eszközön futó verziója. Ez az ügynök mozgatja az adatokat a megfigyelési és diagnosztikai szolgáltatás (MDS).|
| Lsisas2Version          | A verzió a LSI illesztőprogramokat, a StorSimple eszköz megfelelő.|
| Kapacitás                | Az eszköz bájtban teljes kapacitását.|
| RemoteManagementMode    | Azt jelzi, hogy az eszköz távolról felügyelhetik a Windows PowerShell felületén keresztül. |
| FipsMode                | Azt jelzi, hogy az Amerikai Egyesült Államokban Federal Information Processing Standard (FIPS) mód engedélyezve van-e az eszközön. A FIPS 140 szabvány határozza meg a bizalmas adatok védelmének amerikai szövetségi kormányzati számítógépes rendszerek által jóváhagyott titkosítási algoritmusokat. 4 vagy újabb frissítés rendszerű eszközöket FIPS-módban alapértelmezés szerint engedélyezve van. |

## <a name="next-steps"></a>További lépések

* Ismerje meg a [az Invoke-HcsDiagnostics parancsmag szintaxisa](https://technet.microsoft.com/library/mt795371.aspx).

* További tudnivalók a [telepítési problémák elhárításához](storsimple-troubleshoot-deployment.md) a StorSimple eszköz.
