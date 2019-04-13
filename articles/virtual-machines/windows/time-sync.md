---
title: Szinkronizálási idő Windows virtuális gépek az Azure-ban |} A Microsoft Docs
description: Idő szinkronizálása a Windows virtuális gépekhez.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 1a2e75dcffe32c6f1aeaba8646b96bbc1500ffdf
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546913"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Idő szinkronizálása a Windows virtuális gépek az Azure-ban

Időszinkronizálás fontos a biztonság és eseménykorreláció. Egyes esetekben használatos az elosztott tranzakciók végrehajtására. Szinkronizáció idő pontossága a több számítógépes rendszereken érhető el. Szinkronizálási hatással lehet más dolgokra, beleértve az újraindítások és az idő forrás- és a számítógép beolvasása az idő közötti hálózati forgalmat. 

Az Azure mostantól a Windows Server 2016 rendszert futtató infrastruktúra alapját. A Windows Server 2016 továbbfejlesztett algoritmusok pontos idő és a helyi órája UTC szinkronizálását a feltétel rendelkezik.  A Windows Server 2016 szintén fejlődött a VMICTimeSync szolgáltatás, amely szabályozza, hogy hogyan virtuális gépek szinkronizál az állomás pontos ideje. Fejlesztések közé tartoznak a pontosabb kezdeti ideje a virtuális gép start vagy a virtuális gép visszaállítási megszakítási késési javítási Windows idő (W32time) a megadott minták. 


>[!NOTE]
>A Windows időszolgáltatás gyors áttekintést, vessen egy pillantást, ez [videó összefoglaló jellegű áttekintést nyújt](https://aka.ms/WS2016TimeVideo).
>
> További információkért lásd: [Windows Server 2016 – pontos idő](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Áttekintés

A számítógép órája pontosságának van kivett a hogyan zárja be a számítógép óráját, hogy az idő egyezményes világidő (UTC) standard. (UTC) egy második 300 évben ki csak lehet pontos atomi órájával multinacionális mintát határozza meg. De UTC olvasása közvetlenül speciális hardvert igényel. Ehelyett időkiszolgálók szinkronizálva lesznek az UTC és más számítógépek méretezhetőségét és háttértárat érhető el. Minden számítógép szinkronizálási szolgáltatás fut, hogy tudja, milyen idő-kiszolgálók által használt, és rendszeresen ellenőrzi, hogy ha a számítógép órája javításra szoruló idő tartozik, és beállítja az időt, szükség esetén. 

Azure gazdagép belső hierarchiabeli 1 Microsoft tulajdonú eszközökről, a GPS-antennák azok időt vesz igénybe a Microsoft idő kiszolgálók vannak szinkronizálva. Az Azure Virtual machines vagy függőségi viszonyban lehet átadni a pontos időt a gazdagép (*idő gazdagép*) be a virtuális gép vagy a virtuális gép közvetlenül lekérheti a idő egy kiszolgálót vagy mindkettőt. 

A gazdagép virtuálisgép-interakció is befolyásolhatja az óra. Során [karbantartás megőrzése memória](maintenance-and-updates.md#maintenance-not-requiring-a-reboot), virtuális gépek vannak függesztve, akár 30 másodpercig. Például karbantartás megkezdése előtt a virtuális gép órája jeleníti meg a 10:00:00-kor és 28 másodperc tart. Után folytatja a virtuális Gépet, az óra, a virtuális gép továbbra is megjeleníti 10:00:00-kor, amelyek 28 másodperc ki. Ennek, a VMICTimeSync szolgáltatás figyeli a gazdagép és a módosítások utasításokat található a virtuális gépek esetében történő kompenzálás mi történik.

A VMICTimeSync szolgáltatás minta vagy a szinkronizálás üzemmódban működik, és csak befolyásolják az óra előre. Minta módban, amelyhez szükség van a W32time futnia kell, a VMICTimeSync szolgáltatást a gazdagép 5 másodpercenként lekérdezi és W32time mintákat biztosít. Körülbelül 30 másodpercenként, a W32time szolgáltatás legújabb idő mintát vesz igénybe, és befolyásolhatja a Vendég óra használja. Szinkronizálási mód aktiválódik, ha Vendég folytatódik, vagy ha a Vendég óra drifts mögött a gazdagép óra több mint 5 másodperc. Azokban az esetekben, ahol a W32time szolgáltatás megfelelően fut az utóbbi esetben szabad bekövetkeznie.

Nélkül idő szinkronizálási működő, a virtuális gép órája hibák lenne összeadódhatnak. Ha csak egy virtuális Gépet, a hatás nem jelentős, kivéve, ha a munkaterhelés rendkívül pontos időmérő igényel. De a legtöbb esetben azt kell több, összekapcsolt virtuális gépeket, amelyek idő segítségével nyomon követheti a tranzakciók és az időt kell lennie a teljes telepítés során. Ha más virtuális gépek között eltelt idő, sikerült jelenik meg a következő hatásai vannak:

- Hitelesítés sikertelen lesz. Biztonsági protokollok, például a Kerberos vagy a tanúsítvány-függő technológia támaszkodik a rendszeridő konzisztens a rendszerek között. 
- Legyen nagyon nehéz döntse el, mi történhetett rendszerekben, hogy a naplók (vagy egyéb adat) nem fogadom el lehetőséget időben. Az ugyanahhoz az eseményhez jelenne meg, különböző időpontokban, nehéz és korrelációs történt.
- Ha óra ki kapcsolva, a számlázás helytelenül sikerült kiszámítani.

A legjobb eredmények elérése érdekében a Windows központi telepítések, a vendég operációs rendszer, amely biztosítja a legújabb fejlesztései időszinkronizálás használható a Windows Server 2016 használatával végezheti el.

## <a name="configuration-options"></a>Beállítási lehetőségek

A szinkronizálás konfigurálása az Azure-ban üzemeltetett Windows rendszerű virtuális gépek három lehetőség áll rendelkezésre:

- Állomás pontos idejével és time.windows.com. Ez az Azure Marketplace-rendszerképek a használt alapértelmezett konfigurációja.
- Csak állomásnév.
- Egy másik, külső időkiszolgálóval használhatja, vagy állomás pontos idejével használata nélkül.


### <a name="use-the-default"></a>Használja az alapértelmezett

Alapértelmezés szerint a Windows OS VM-rendszerképek a két forrásból származó szinkronizálása w32time vannak konfigurálva: 

- Az NTP szolgáltató, amely információkat olvas be time.windows.com.
- A VMICTimeSync szolgáltatás, a gazdagép kommunikációhoz használt időt, hogy a virtuális gépeket, és javíthatja a megadottakat, miután a virtuális gép karbantartás miatt szünetel. Azure gazdagépek hierarchiabeli 1 a Microsoft által birtokolt eszközök használatával – pontos idő tartani.

a W32Time inkább az idő-szolgáltató a következő sorrendben prioritású: hierarchiabeli szintjét, legfelső szintű késleltetés, legfelső szintű diszperziós, idő eltolása. A legtöbb esetben w32time inkább time.windows.com a gazdagépre, mert a time.windows.com alacsonyabb hierarchiabeli jelentések. 

Tartományhoz csatlakoztatott gépeket magát a tartományt hozza létre a szinkronizálási időhierarchiában, de az erdő gyökértartományában továbbra is kell valahol az időt vesz igénybe, és az alábbi szempontokat még mindig tartsa igaz.


### <a name="host-only"></a>Csak állomásnév 

Mivel a time.windows.com egy nyilvános NTP-kiszolgálót, vele time szinkronizálása igényel az interneten keresztül küldi a forgalmat, különböző csomag késések negatív hatással lehet az idő szinkronizálása minőségét. Time.windows.com eltávolítása átvált a gazdagép csak szinkronizálás néha javítja az idő szinkronizálása az eredményeket.

Váltás csak állomásnév idő szinkronizálása teszi értelemben vett idő szinkronizálása tapasztal problémák az alapértelmezett konfigurációt használja. Próbálja ki a gazdagép csak szinkronizálás megtekintéséhez Ha, amelyek az idő szinkronizálása a virtuális Gépen. 

### <a name="external-time-server"></a>Külső időkiszolgálóval

Ha adott időpont-szinkronizálás követelményei, is lehetőség van a külső idő kiszolgálók használatával. Külső idő kiszolgálók biztosíthat az adott időpontot, amely a tesztelési helyzetekhez, ideje egységességének biztosítása üzemeltetett-Microsoft adatközpontok vagy kezelési azt másodperc, speciális módon gépekkel hasznos lehet.

Külső kiszolgálók VMICTimeSync szolgáltatás és az alapértelmezett konfiguráció hasonló eredményeket VMICTimeProvider kombinálhatók. 

## <a name="check-your-configuration"></a>Ellenőrizze a konfigurációt


Ellenőrizze, hogy a az idő NTP szolgáltató explicit az NTP-kiszolgálókra (NTP) vagy a tartomány idő szinkronizálása (NT5DS) használatára van konfigurálva.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Ha a virtuális gép által használt NTP, a következő kimenet jelenik meg:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Milyen időkiszolgálóval megtekintéséhez az NTP idő szolgáltatót használ, egy rendszergazda jogú parancssorból írja be a:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Ha a virtuális gép nem használja az alapértelmezett, a kimeneti néznek ki:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Megtekintheti, milyen idő szolgáltató jelenleg használatban van.

```
w32tm /query /source
```


A kimenet láthatóvá teheti, és mi jelentené itt látható:
    
- **time.Windows.com** – a alapértelmezett konfigurálásban w32time kap időt a time.windows.com. Az idő szinkronizálása minőségi internet kapcsolódniuk kell hozzá függ, és késések csomag által érintett. Ez a szokásos kimenete az alapértelmezett beállítás.
- **Virtuális gép IC idő szinkronizálási szolgáltató** – a virtuális Gépet a gazdagépről idő szinkronizálása folyamatban van. Ez általában akkor az az eredmény, ha Ön Feliratkozás a gazdagép csak szinkronizálás vagy az NTP-kiszolgáló nem érhető el abban a pillanatban. 
- *A tartomány kiszolgáló* – az aktuális számítógép egy tartományhoz tartozik, és a tartomány az időhierarchiában szinkronizálási határozza meg.
- *Egyéb kiszolgálón* -w32time explicit módon konfigurálták, hogy az idő lekérése, a másik kiszolgáló. Idő szinkronizálása minőségi attól függ, hogy ezen idő server minőségét.
- **Helyi CMOS óra** – óra nincs szinkronizálva. Ez a kimenet kaphat, ha w32time még nem volt elég ideje elindításához újraindítás után, vagy ha az összes beállított idő adatforrás nem érhető el.


## <a name="opt-in-for-host-only-time-sync"></a>Feliratkozás a gazdagép csak idő szinkronizálása

Az Azure folyamatosan dolgozik növelése az idő szinkronizálása a gazdagépeken és tud garantálni, hogy az idő szinkronizálása infrastruktúra található, a Microsoft tulajdonában lévő adatközpontokban. Ha a szinkronizálás az alapértelmezett beállítás, amely time.windows.com használja elsődleges idő forrásaként problémái, segítségével a következő parancsok szeretné csak állomásnév idő szinkronizálása.

A VMIC szolgáltató megjelölése engedélyezve van. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Jelölje meg az NTP-szolgáltatót, mert le van tiltva.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Indítsa újra a w32time szolgáltatás.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012 és R2 virtuális gépek 

A Windows Server 2012 és Windows Server 2012 R2 rendelkezik az idő szinkronizálása más alapértelmezett beállításait. A W32Time szolgáltatás alapértelmezés szerint úgy, hogy a szolgáltatás alacsony többletterhelést keresztül szeretnék pontos idő van beállítva. 

Ha szeretné helyezni az újabb alapértelmezett beállításokat, amelyeknek pontos idő használata a Windows Server 2012 és 2012 R2 központi telepítések, a következő beállításokat is alkalmazhat.

A w32time lekérdezésének frissítése, és frissítési időközök Windows Server 2016-beállításoknak megfelelően.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

A w32time tudják használni a új lekérdezési időköz a NtpServers megjelölni használja őket. Kiszolgálók vannak feliratozva 0x1 jelzőbit maszkkal rendelkező, ha, amely akkor bírálja felül ezt a mechanizmust, és w32time SpecialPollInterval inkább használna. Ügyeljen arra, hogy megadott NTP-kiszolgálók vagy használja 0x8 jelző vagy nem jelző minden:

Ellenőrizze, hogy milyen jelzők használ a használt NTP-kiszolgálók.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>További lépések

Az alábbiakban további információt az idő szinkronizálása mutató hivatkozásokat:

- [Windows időszolgáltatás eszközei és beállításai](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [A Windows Server 2016-ban fejlesztései ](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [A Windows Server 2016 – pontos idő](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Határ konfigurálása a Windows időszolgáltatás nagy pontosságú környezetek támogatása](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)


