---
title: Windows-virtuális gépek időszinkronizálása az Azure-ban
description: Időszinkronizálás A Windows virtuális gépekhez.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: dd2ae2159c43da6a049d67cae739f111eba682c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74534454"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Windows-virtuális gépek időszinkronizálása az Azure-ban

Az időszinkronizálás fontos a biztonság és az eseménykorreláció érdekében. Néha elosztott tranzakciók megvalósításához használják. Több számítógépes rendszer közötti időpontosság szinkronizálással érhető el. A szinkronizálást több dolog is befolyásolhatja, például az újraindítások és a hálózati forgalom az időforrás és a számítógép időbetöltése között. 

Az Azure-t most a Windows Server 2016 rendszert futtató infrastruktúra támogatja. A Windows Server 2016 továbbfejlesztett algoritmusokat használt az idő és a helyi óra állapotának javítására az UTC-vel való szinkronizáláshoz.  A Windows Server 2016 javította a VMICTimeSync szolgáltatást is, amely a virtuális gépek és a gazdagép szinkronizálásának pontos idejét szabályozza. A fejlesztések közé tartozik a virtuális gép indításakor a kezdeti idő pontosabb időpontja vagy a virtuális gép visszaállítása és a Windows idő (W32time) számára biztosított minták késésének megszakítása. 


>[!NOTE]
>A Windows időszolgáltatás gyors áttekintéséhez tekintse meg ezt a [magas szintű áttekintő videót.](https://aka.ms/WS2016TimeVideo)
>
> További információt a [Windows Server 2016 pontos ideje](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)című témakörben talál. 

## <a name="overview"></a>Áttekintés

A számítógép órájának pontosságát annak a felmérnivalója, hogy a számítógép órája milyen közel van az egyezményes világidő (UTC) időszabványhoz. Az UTC-t a pontos atomórák ból álló multinacionális minta határozza meg, amely 300 év alatt csak egy másodperccel kapcsolható ki. De az UTC olvasása közvetlenül speciális hardvert igényel. Ehelyett az időkiszolgálók atutc-ra vannak szinkronizálva, és más számítógépekről érhetők el, hogy méretezhetőséget és robusztusságot biztosítsanak. Minden számítógépen van idő szinkronizálási szolgáltatás fut, amely tudja, hogy mikor szerverek használata, és rendszeresen ellenőrzi, ha a számítógép óráját kell javítani, és beállítja az időt, ha szükséges. 

Az Azure-állomások szinkronizálva vannak a Microsoft belső időkiszolgálóival, amelyek a Microsoft tulajdonában lévő Stratum 1 eszközökről, GPS-antennákból veszik igénybe az idejüket. Virtuális gépek az Azure-ban vagy függ a gazdagép adja át a pontos időt *(állomás idő*) a virtuális gép, vagy a virtuális gép közvetlenül időt kap egy időkiszolgáló, vagy a kettő kombinációja. 

A virtuális gép és a gazdagép közötti interakciók hatással lehetnek az órára is. A [memória megőrzése során](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)a virtuális gépek legfeljebb 30 másodpercre szünetelnek. Például a karbantartás megkezdése előtt a virtuális gép órája 10:00:00 am-t jelenít meg, és 28 másodpercig tart. Miután a virtuális gép folytatódik, a virtuális gép órája továbbra is 10:00:00 am,ami 28 másodperc szünet. Ennek kijavításához a VMICTimeSync szolgáltatás figyeli, hogy mi történik az állomáson, és kéri a módosításokat a virtuális gépeken, hogy kompenzálja.

A VMICTimeSync szolgáltatás minta- vagy szinkronizálási módban működik, és csak az órát befolyásolja előre. Minta módban, amely megköveteli a W32time futtatását, a VMICTimeSync szolgáltatás 5 másodpercenként lekérdezi az állomást, és időmintákat biztosít a W32time számára. Körülbelül 30 másodpercenként, a W32time szolgáltatás veszi a legújabb idő mintát, és használja, hogy befolyásolja a vendég óra. A szinkronizálási mód akkor aktiválódik, ha egy vendég folytatja, vagy ha a vendég órája 5 másodpercnél tovább van a házigazda órája mögött. Azokban az esetekben, amikor a W32time szolgáltatás megfelelően működik, az utóbbi esetben soha nem történhet meg.

Idő szinkronizálás a munka nélkül, az óra a virtuális gép felhalmozódna hibákat. Ha csak egy virtuális gép van, a hatás nem lehet jelentős, kivéve, ha a számítási feladatok rendkívül pontos időmérést igényel. De a legtöbb esetben több, összekapcsolt virtuális gépünk van, amelyek a tranzakciók nyomon követésére időt használnak, és az időnek konzisztensnek kell lennie a teljes központi telepítés során. Ha a virtuális gépek közötti idő eltérő, a következő hatások jelennek meg:

- A hitelesítés sikertelen lesz. Az olyan biztonsági protokollok, mint a Kerberos vagy a tanúsítványfüggő technológia, az idő konzisztensek a rendszerek között. 
- Nagyon nehéz kitalálni, hogy mi történt a rendszerben, ha a naplók (vagy más adatok) nem értenek egyet az időben. Ugyanaz az esemény úgy nézne ki, mintha különböző időpontokban történt volna, ami megnehezíti a korrelációt.
- Ha az óra ki van kapcsolva, a számlázás helytelenül számítható ki.

A Windows-telepítésekhez a legjobb eredményeket a Windows Server 2016-os operációs rendszer vendég operációs rendszerként való használatával érheti el, amely biztosítja az időszinkronizálás legújabb fejlesztéseit.

## <a name="configuration-options"></a>Beállítási lehetőségek

Az Azure-ban üzemeltetett Windows virtuális gépek időszinkronizálásának konfigurálása három lehetőség közül választhat:

- Host idő és time.windows.com. Ez az Azure Marketplace-lemezképek alapértelmezett konfigurációja.
- Csak gazdagép.
- Használjon egy másik külső időkiszolgálót az állomásidő használatával vagy anélkül.


### <a name="use-the-default"></a>Az alapértelmezett beállítás használata

Alapértelmezés szerint a Windows operációs rendszer virtuálisgép-lemezképei a w32time rendszerhez vannak konfigurálva, hogy két forrásból szinkronizáljanak: 

- Az NTPClient-szolgáltató, amely adatokat kap time.windows.com.
- A VMICTimeSync szolgáltatás, amely az állomásidő kommunikálására szolgál a virtuális gépekkel, és a karbantartás felfüggesztése után javításokat tesz le. Az Azure-állomások a Microsoft tulajdonában lévő Stratum 1 eszközöket használják a pontos idő megtartásához.

w32time inkább az időszolgáltató a következő prioritási sorrendben: réteg szint, gyökér késleltetés, gyökér diszperzió, idő eltolás. A legtöbb esetben a w32time inkább a gazdagéptime.windows.com részesíti előnyben, mivel time.windows.com alacsonyabb rétegről számol be. 

Tartományhoz csatlakozó gépek esetén maga a tartomány időszinkronizálási hierarchiát hoz létre, de az erdőgyökérnek még mindig időt kell szakítania valahonnan, és a következő szempontok továbbra is teljesülnek.


### <a name="host-only"></a>Csak állomás 

Mivel time.windows.com nyilvános NTP-kiszolgáló, a szinkronizálási idő az interneten keresztül imitáló forgalmat igényel, a változó csomagkésések negatívan befolyásolhatják az időszinkronizálás minőségét. Ha eltávolítja time.windows.com a csak állomásszinkronizálásra való váltással, az néha javíthatja az időszinkronizálás eredményeit.

Ha az alapértelmezett konfigurációval időszinkronizálási problémákat tapasztal, célszerű a csak állomásidő-szinkronizálásra váltani. Próbálja ki a csak állomás szinkronizálási, hogy ha ez javítaná az idő szinkronizálása a virtuális gépen. 

### <a name="external-time-server"></a>Külső időkiszolgáló

Ha konkrét időszinkronizálási követelményekkel rendelkezik, akkor a külső időkiszolgálók is igénybe vehetik. A külső időkiszolgálók meghatározott időt biztosíthatnak, ami hasznos lehet a tesztforgatókönyvekhez, biztosítva az idő egységét a nem microsoftos adatközpontokban üzemeltetett gépekkel, vagy a szökőmásodpercek speciális módon történő kezelését.

A külső kiszolgálókat kombinálhatja a VMICTimeSync szolgáltatással és a VMICTimeProvider szolgáltatással, hogy az alapértelmezett konfigurációhoz hasonló eredményeket nyújtson. 

## <a name="check-your-configuration"></a>A konfiguráció ellenőrzése


Ellenőrizze, hogy az NTP-ügyfél időszolgáltatója explicit NTP-kiszolgálók (NTP) vagy tartományidő-szinkronizálás (NT5DS) használatára van-e konfigurálva.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Ha a virtuális gép NTP-t használ, a következő kimenet jelenik meg:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Az NtpClient időszolgáltató által használt időkiszolgáló megtekintéséhez rendszergazdai jogú parancssor-típussal:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Ha a virtuális gép az alapértelmezett, a kimenet így fog kinézni:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


A jelenleg használt időszolgáltató megtekintése.

```
w32tm /query /source
```


Itt van a kimenet, amit lehetett látni, és mit jelentene:
    
- **time.windows.com** - az alapértelmezett konfigurációban a w32time időt kap time.windows.com. Az időszinkronizálás minősége az internetkapcsolattól függ, és a csomagkésések befolyásolják. Ez az alapértelmezett beállítás szokásos kimenete.
- **Virtuális gép IC időszinkronizálási szolgáltató** – a virtuális gép szinkronizálási idő a gazdagép. Ez általában akkor az eredmény, ha csak állomásidő-szinkronizálást engedélyez, vagy ha az NtpServer jelenleg nem érhető el. 
- *A tartománykiszolgáló* – az aktuális számítógép egy tartományban van, és a tartomány határozza meg az időszinkronizálási hierarchiát.
- *Néhány másik szerver* - w32time kifejezetten úgy volt beállítva, hogy az idő, hogy egy másik szerverre. Az időszinkronizálás minősége ettől az időkiszolgálótól függ.
- **Helyi CMOS óra** - óra nincs szinkronizálva. Ezt a kimenetet akkor kaphatja meg, ha a w32time-nak nem volt elég ideje az újraindítás után elindítani, vagy ha az összes beállított időforrás nem érhető el.


## <a name="opt-in-for-host-only-time-sync"></a>Csak állomásidő-szinkronizálás letiltása

Az Azure folyamatosan dolgozik az időszinkronizálás javításán a gazdagépeken, és garantálja, hogy a szinkronizálási infrastruktúra minden alkalommal a Microsoft tulajdonában lévő adatközpontokban található. Ha időszinkronizálási problémái vannak azzal az alapértelmezett beállítással, amely a time.windows.com elsődleges időforrásként kívánja használni, a következő parancsokkal engedélyezheti a csak állomásidő-szinkronizálást.

Jelölje meg a VMIC-szolgáltatót engedélyezve. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Jelölje meg az NTP-ügyfélszolgáltatót letiltottként.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Indítsa újra a w32time szolgáltatást.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012 és R2 virtuális gépek 

A Windows Server 2012 és a Windows Server 2012 R2 alapértelmezett beállításai eltérőek az időszinkronizáláshoz. A w32time alapértelmezés szerint úgy van konfigurálva, hogy a szolgáltatás alacsony terhelése a pontos időhöz képest alacsonyabb. 

Ha a Windows Server 2012 és 2012 R2 központi telepítéseit a pontos időt előnyben részesítő újabb alapértelmezett értékek használatára szeretné áthelyezni, a következő beállításokat alkalmazhatja.

Frissítse a w32time lekérdezési és frissítési időközöket a Windows Server 2016 beállításainak megfelelően.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Ahhoz, hogy a w32time használni tudja az új lekérdezési időközöket, az NtpServers-kiszolgálókat használatként kell megjelölni. Ha a kiszolgálók at cx1 bitflag maszkkal vannak ellátva, az felülírná ezt a mechanizmust, és a w32time a SpecialPollInterval-ot használná. Győződjön meg arról, hogy a megadott NTP-kiszolgálók vagy 0x8-as jelzőt használnak, vagy egyáltalán nincs jelző:

Ellenőrizze, hogy milyen jelzőket használ a használt NTP-kiszolgálókhoz.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>További lépések

Az alábbiakban az időszinkronizálással kapcsolatos további részletekre mutató hivatkozásoktalálhatók:

- [A Windows időszolgáltatás eszközei és beállításai](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [A Windows Server 2016 fejlesztései](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [A Windows Server 2016 pontos ideje](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Támogatási határ a Windows időszolgáltatás nagy pontosságú környezetekhez való konfigurálásához](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)


