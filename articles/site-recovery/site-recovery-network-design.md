---
title: "A vész-helyreállítási hálózati infrastruktúra megtervezése |} Microsoft Docs"
description: "Ez a cikk ismerteti az Azure Site Recovery hálózati kialakítási szempontjai"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: jwhit
editor: 
ms.assetid: ce787731-d930-4f00-a309-e2fbc2e1f53b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/20/2017
ms.author: pratshar
ms.openlocfilehash: 90ffd3dd1cf5068359afa1b60892cdee43ec0658
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="designing-your-network-for-disaster-recovery"></a>A vész-helyreállítási hálózat tervezése

Ez a cikk irányul, informatikai szakemberek igényeire felelősek újratervezni, megvalósítás, és támogatási üzleti folytonossági és vészhelyreállítási (BCDR) recovery-infrastruktúra és akik szeretnék kihasználni a Microsoft Azure webhely-helyreállítás (ASR) történő támogatása és javítása az Üzletfolytonossági szolgáltatásokba. Ez a cikk ismerteti, amelyek gyakorlati szempontok vész-helyreállítási helyen hálózati szerkezetének kialakítása, az Azure vagy más helyszíni hely. 

## <a name="overview"></a>Áttekintés
[Az Azure webhely-helyreállítás (ASR)](https://azure.microsoft.com/services/site-recovery/) koordinálja a védelmi és helyreállítási üzleti folytonossági (BCDR) vészhelyreállításra a virtualizált alkalmazások a Microsoft Azure-szolgáltatás. Ez a dokumentum célja, hogy az olvasó a folyamatot, amely a hálózat megtervezése, összpontosító újratervezni IP-címtartományok és -alhálózatok, a vész-helyreállítási helyen, a Site Recovery használata virtuális gépek (VM) replikálása esetén útmutató.

Ezenkívül ez a cikk bemutatja, hogyan helyreállítás lehetővé teszi, hogy újratervezni és végrehajtási egy többhelyes virtuális datacenter BCDR-szolgáltatásokat támogatja a tesztelési vagy katasztrófa időpontjában.

A világ, ahol mindenki vár 24/7 kapcsolatot akkor fontos a valaha is képes legyen az infrastruktúra és az alkalmazások, és fut.. Üzleti folytonossági és vészhelyreállítási (BCDR) helyreállítási célja összetevő visszaállítására, így a szervezet gyorsan folytathatja a normál működést. Nagy kihívást fejlesztése vész-helyreállítási stratégiák nem valószínű, pusztító események kezelésére. Különösen személyekbe valószínűtlen eseményeket, és adja meg a megfelelő intézkedéseket kiterjedt katasztrófa elleni védelem magas költség okozza a jövőben előrejelzésére rejlő nehézsége.

Kritikus fontosságú a BCDR tervezési, helyreállítási idő célkitűzés (RTO) és a helyreállítási pont célkitűzés (RPO) definiálni kell egy vész-helyreállítási terv részeként. Ha egy olyan vészhelyzet esetén az ügyfél az adatközpont, Azure Site Recovery, az ügyfelek is gyorsan segítségével (alacsony RTO) éri online állapotba kerüljön a replikált virtuális gép vagy a másodlagos adatközpont, vagy a Microsoft Azure minimális adatvesztéssel (kis RPO) található.

Feladatátvételi ASR, amely kezdetben átmásolja kijelölt virtuális gépeket az elsődleges adatközpont a másodlagos adatközpontba vagy az Azure-ba (attól függően, hogy a forgatókönyv), és rendszeresen frissülnek a replikák tette lehetővé. Infrastruktúrák tervezése, során hálózati terv, amely megakadályozhatja, hogy Ön értekezlet vállalat RTO és Helyreállításipont-célkitűzések lehetséges szűk kell tekinteni.  

Amikor a rendszergazdák a vész-helyreállítási megoldást telepítését tervez, az alábbiakhoz hasonló fontos kérdések saját készítésű egyik módját a virtuális gép lenne elérhető-e a feladatátvétel befejezése után. Automatikus rendszer-Helyreállítás a rendszergazda válassza ki a hálózatot, amelyhez a virtuális gépek volna csatlakoztatható a feladatátvételt követően. Ha az elsődleges hely Azure, vagy egy helyszíni hely a VMM-kiszolgáló felügyeli, majd a Hálózatleképezés használatával érhető el. További információ [hálózati leképezés az Azure az Azure-bA vész-Helyreállítási](site-recovery-network-mapping-azure-to-azure.md) és [Hálózatleképezés a VMM-ből](site-recovery-network-mapping.md)


A helyreállítási hely hálózati tervezésekor a rendszergazda két választási lehetőség:

* Egy másik IP-címtartomány használata a hálózati helyreállítási helyen. Ebben a forgatókönyvben a virtuális gép a feladatátvételt követően egy új IP-címet kap, és a DNS-frissítés végrehajtásához a rendszergazda rendelkezik. 
* Használja az ugyanazon IP-címtartomány a hálózatra a helyreállítási helyen. Bizonyos esetekben rendszergazdák jobban szeretik, hogy az IP-címek, amelyek rendelkeznek az elsődleges helyen a feladatátvétel után is megőrzi. Egy normál esetben a rendszergazda kellene az új helyének megadására az IP-címek az útvonalak frissítése. De a forgatókönyvben, amelyen üzembe van helyezve a felhőbe archivált alhálózat az elsődleges és a helyreállítási helyek között, az IP-címet a virtuális gépek megőrzése válik vonzó lehetőség. Egy alhálózat egy helyszíni hálózatból az Azure-hálózatot, vagy két Azure-hálózatok közötti Nyújtás nincs lehetőség.  

Amikor a rendszergazdák a vész-helyreállítási megoldást telepítését tervez, a szem előtt az alábbiakhoz hasonló fontos kérdések egyike hogyan az alkalmazások lesz elérhető a feladatátvétel befejezése után. Modern alkalmazások szinte mindig sem függ a hálózat bizonyos mértékben, így fizikai áthelyezésével a szolgáltatás egyik helyről egy másikra hálózati kihívást jelöli. Két módon fő, hogy a probléma a vész-helyreállítási megoldások foglalkozik. Az első módszer a fix IP-címek fenntartásához. Annak ellenére, hogy a szolgáltatások áthelyezése és az üzemeltetési kiszolgáló alatt álló különböző fizikai helyen találhatóak alkalmazásokat az új helyre az IP-címének konfigurációja, igénybe vehet. A második megközelítés teljesen módosítása az IP-cím a helyreállított helyhez való áttérés közben. Mindkét megközelítés tartozik megvalósítási változat alábbi összegzését.

A helyreállítási hely hálózati tervezésekor a rendszergazda két választási lehetőség:

## <a name="option-1-retain-ip-addresses"></a>1. lehetőség: Tartsa meg az IP-címek
A vész helyreállítási folyamat szempontjából rögzített IP-alapú címek úgy tűnik, hogy a legegyszerűbb módja, de számos lehetséges kihívást, amelyek a gyakorlatban teszik a legkevésbé népszerű megközelítés rendelkezik. Az Azure Site Recovery lehetővé teszi az IP-címek minden esetben megőrzi. Mielőtt egy úgy dönt, hogy IP megőrizni, megfelelő gondolat biztosítani kell feladatátvételi képességeit kifejezhető a korlátozást. Ossza meg velünk tekintse meg a tényezőket, amelyek segítséget nyújtanak a eldöntheti, hogy tartsa meg az IP-címek, vagy nem. Ez megvalósítható a két módon felhőbe archivált alhálózat vagy a teljes alhálózat feladatátvétel.

### <a name="stretched-subnet"></a>Felhőbe archivált alhálózati
Itt az alhálózat szeretné elérhetővé tenni egyidejűleg az elsődleges és a vész-Helyreállítási helyeken. Egyszerű Ez azt jelenti, hogy a kiszolgáló és az IP-(3. rétegbeli) konfigurációja áthelyezheti a második helyre, és a hálózati irányítja a forgalmat az új helyre automatikusan. Ez nagyon egyszerű server szempontjából foglalkozik, de számos kihívást rendelkezik:

* A 2. rétegbeli (adatrétege hivatkozás) szempontjából hálózatkezeléshez szükséges eszközök, melyekkel kezelhető a felhőbe archivált VLAN-OK ehhez, de ez lett kevesebb probléma, mivel most széles körben elérhető. A második és nehezebb probléma, amely lényegében a hibaérzékeny pontok kialakulását váljon a potenciális tartalék tartomány mindkét hely az időtartam VLAN nyújtsák. Ez nem egy valószínűtlen esetnek a bekövetkezése, azt, hogy egy szórási storm elindult, de nem sikerült elkülönített került sor. Azt utolsó problémáról vegyes vélemények talált, és amint láthatta számos sikeres megvalósítását, valamint "azt fogja soha nem a technológia itt".
* Felhőbe archivált alhálózati nincs lehetőség, ha használja a Microsoft Azure a vész-Helyreállítási helyként.

### <a name="subnet-failover"></a>Alhálózati feladatátvétel
A felhőbe archivált alhálózati megoldás nélkül kiterjesztené vele az alhálózat több helyre a fent leírt előnyeinek alhálózati feladatátvétel végrehajtásához lehetőség. Itt bármely adott alhálózaton lenne a jelen webhely 1 vagy 2. hely, de soha nem mindkét helyen egyszerre. Ahhoz, hogy a feladatátvétel esetén az IP-címtér kezelése, akkor lehet programozott módon rendezheti az útválasztó-infrastruktúra az alhálózatok áthelyezése egyik helyről egy másikra. Az alhálózatok és a társított áthelyezné feladatátvétel esetén a védett virtuális gépeket. A fő ezt a módszert hátránya, hogy helyezze át a teljes alhálózat, amely OK lehet, hogy hiba esetén, de ez hatással lehet a feladatátvevő granularitási ismertetett szempontok alapján.

Vizsgáljuk meg, hogyan tudja a virtuális gépek replikálása a helyreállítási hely megfelelő működése közben keresztüli a teljes alhálózat-e egy kitalált, Contoso nevű vállalati. Először a Contoso Mitől kezelhetik ezek alhálózatok, miközben a virtuális gépek replikálása két között a helyszíni helyek fog keresni, és majd ismertetjük, hogyan alhálózati feladatátvételi működik, ha [Azure használata a vész-helyreállítási helyként](#failover-to-azure).

#### <a name="failover-from-on-premises-to-azure"></a>Feladatátvétel a helyszíni az Azure-bA 
Az Azure webhely-helyreállítás (ASR) lehetővé teszi, hogy az Azure virtuális gépekhez a vész-helyreállítási helyként használandó.  

Vizsgáljuk meg az olyan forgatókönyvekben, ahol a Woodgrove Bank nevű fiktív vállalat rendelkezik-e az üzletági alkalmazásokat futtató helyszíni infrastruktúrát, és azokat az alkalmazásokat az Azure-on üzemeltető. A helyek (közötti S2S) virtuális magánhálózati (VPN) vagy ExpressRoute biztosít a Woodgrove Bank virtuális gépeket az Azure és a helyszíni kiszolgálók közötti kapcsolatot. S2S VPN-je lehetővé teszi, hogy a Woodgrove Bank virtuális hálózati kell tekinteni a Woodgrove Bank a helyszíni hálózat kiterjesztése az Azure-ban. Ez a kommunikáció S2S VPN engedélyezve van a Woodgrove Bank széle és az Azure-beli virtuális hálózat között. Most Woodgrove kívánja használni az automatikus rendszer-Helyreállítás a rendszert futtató elsődleges Azure-régió, egy másik Azure-régió, munkaterhelés replikálásához. Ez a beállítás megfelel a Woodgrove, amely egy gazdaságos vész-Helyreállítási lehetőséget szeretne, és képes tárolni az adatokat a nyilvános felhő környezeteiben igényeinek. Woodgrove az alkalmazások és konfigurációk, attól függően kódolt IP-címek kezelésére van, ezért a követelmény, hogy tartsa meg az IP-címet az alkalmazások az Azure-ban egy másik régióban Miután rendelkeznek.

Woodgrove határozott, hogy az IP-címeket adjon meg IP-címtartomány (172.16.1.0/24, 172.16.2.0/24) Azure-beli erőforrásaihoz.

A Woodgrove tudjanak a virtuális gépek replikálása Azure-ba, megtartja az IP-címek egy Azure virtuális hálózatot kell létrehozni. A helyszíni hálózat kiterjesztése kell, hogy az alkalmazások zökkenőmentesen is feladatátvétel a helyszíni helyről az Azure-bA. Azure-webhelyek, valamint a pont-pont VPN-kapcsolat hozzáadása az Azure-ban létrehozott virtuális hálózatok teszi lehetővé. A pont-pont kapcsolat beállításához, Azure-hálózat lehetővé teszi a forgalom irányítására a helyszíni helyre (Azure meghívja az helyi hálózati), csak akkor, ha az IP-címtartomány eltér a helyi IP-címtartomány, mert nem támogatja az Azure kiterjesztené vele az alhálózatokat.  Ez azt jelenti, hogy ha egy alhálózat 192.168.1.0/24 a helyszíni, nem adja hozzá a helyi hálózati 192.168.1.0/24 az Azure-hálózatot. Ez elvárható, hiszen az Azure nem tudja, hogy nincs aktív virtuális gépek nincsenek-e az alhálózatot, és csak a vész-Helyreállítási célokra létre az alhálózatot. Tudni megfelelően irányítható a hálózati forgalom kívül nem ütközhet a hálózat és a helyi hálózat alhálózatai Azure-hálózat.

![Alhálózati feladatátvétel előtt](./media/site-recovery-network-design/network-design7.png)

Feladatátvétel előtt

Woodgrove az üzleti követelmények teljesítése érdekében kell a következő munkafolyamatok végrehajtásához:

* Hozzon létre egy további hálózati, ossza meg velünk neki helyreállítási hálózati, ahol a átvevő virtuális gépek jönnek létre.
* Győződjön meg arról, hogy a virtuális gép IP-Címek őrizzen meg egy feladatátvétel után, lépjen a konfigurálása lapon, a virtuális gép tulajdonságai, adja meg az, hogy a virtuális gép rendelkezik-e a helyszíni azonos IP-cím, és kattintson a mentés. A virtuális gép feladatátvételt, ha az Azure Site Recovery rendeli a megadott IP a virtuális gép.

![Hálózat tulajdonságai](./media/site-recovery-network-design/network-design8.png)

Miután akkor váltódik ki, a feladatátvételt, és a virtuális gépek jönnek létre a kívánt IP-a helyreállítási hálózaton, a hálózati kapcsolat hozhatók létre a [Kapcsolatcsoporttal Vnet](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Ha szükséges, ez a művelet parancsfájlalapú lehet.  Azt az előző szakaszban bemutatott kapcsolatos alhálózati feladatátvétel esetén az Azure-ba, feladatátvétel esetén is megfelelően módosítani kell, hogy tükrözze a 192.168.1.0/24 most már át lett helyezve az Azure-bA rendelkezik útvonalak.

![Alhálózati feladatátvételt követően](./media/site-recovery-network-design/network-design9.png)

A feladatátvételt követően

Ha egy Azure hálózati nincs a fenti ábrán látható módon. Helyek közötti VPN-kapcsolat a "Helyreállítási hálózat" között "Elsődleges helyhez" a feladatátvételt követően is létrehozhat.  


#### <a name="failover-to-a-secondary-on-premises-site"></a>Feladatátvétel egy másodlagos helyszíni helyre
Ossza meg velünk tekintse meg a forgatókönyvhöz ahol azt szeretnénk, tartsa meg az IP-címe a virtuális gépek mindegyikének és a feladatátvételi a teljes alhálózat együtt. Az elsődleges hely rendelkezik alhálózati 192.168.1.0/24 futó alkalmazások. A feladatátvétel történik, ha ez az alhálózat részét képező összes virtuális gépet a helyreállítási hely feladatátvételt, és tartsa meg az IP-címét. Útvonalak érdekében, hogy az alhálózati 192.168.1.0/24 tartozó összes virtuális gépet a helyreállítási hely most áthelyezte megfelelően módosítani kell.

Az alábbi ábrán az elsődleges hely és a helyreállítási hely, harmadik és elsődleges hely és harmadik hely és helyreállítási hely között útvonalakat megfelelően módosítani kell.

Az alábbi képek a feladatátvétel előtt az alhálózatok jeleníti meg. Alhálózati 192.168.0.1/24 aktív a feladatátvétel előtt az elsődleges helyen, és a feladatátvétel után a helyreállítási hely aktiválódásakor

![Feladatátvétel előtt](./media/site-recovery-network-design/network-design2.png)

Feladatátvétel előtt

A következő ábrán látható a hálózatok és alhálózatok feladatátvételt követően.

![A feladatátvételt követően](./media/site-recovery-network-design/network-design3.png)

A feladatátvételt követően

A másodlagos hely van a helyszíni és a VMM-kiszolgáló segítségével kezelheti, majd egy adott virtuális gép védelmének engedélyezésekor ASR foglal le megfelelően a következő munkafolyamat hálózati erőforrások:

* Az ASR foglal le, a statikus IP-címkészletből definiálva a System Center VMM feltünteti a releváns hálózati IP-cím mindegyik hálózati interfész a virtuális gépen.
* Ha a rendszergazda a hálózat ugyanazon IP-címkészletet a helyreállítási helyen legyen, mint az IP-címkészlet a hálózat, az elsődleges hely határozza meg, a legyen, mint címre az IP-címet a replika virtuális gép automatikus rendszer-Helyreállítás szeretné kiosztani az azonos IP-cím lefoglalása közben a az elsődleges virtuális gép.  Az IP-cím van fenntartva a VMM-ben, de a Hyper-v gazdagép feladatátvételi IP nincs beállítva. Feladatátvételi IP-cím, a Hyper-v gazdagépet a feladatátvétel előtt van beállítva.


Az azonos IP-cím nem érhető el, ha az automatikus rendszer-Helyreállítás néhány elérhető IP-cím volna a megadott IP-címkészletből lefoglalni.

Miután a virtuális gép engedélyezve van a következő minta parancsfájl segítségével ellenőrizze az IP-cím nincs lefoglalva a virtuális gép védelmét. Az azonos IP-cím akkor állítja a feladatátvételi IP, és a feladatátvétel idején a virtuális Géphez rendelt:

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

> [!NOTE]
> A forgatókönyvben, ahol a virtuális gépek DHCP használatára az IP-címek kezelését teljesen kívül esik az automatikus rendszer-Helyreállítás irányítását. A rendszergazda számára, győződjön meg arról, hogy a DHCP-kiszolgáló az IP-címeket, a helyreállítási helyen szolgálhatnak a ugyanazzal a tartománnyal, amely az elsődleges hely.
>
>



## <a name="option-2-changing-ip-addresses"></a>2. lehetőség: IP-címek módosítása
Ez a megközelítés úgy tűnik, hogy a legjellemzőbb mi úgy találtuk, alapján. Minden virtuális gép, amely részt vesz a feladatátvételi IP-címének módosítása formájában vesz igénybe. A hátránya visszatérítési a bejövő hálózati "További", hogy az alkalmazást, amely korábban IPx jelenleg IPy igényel. Akkor is, ha IPX-et és IPy logikai nevek, általában módosítható, vagy a hálózaton, a kiürített kell DNS-bejegyzések és hálózati táblák gyorsítótárazott tételeit kell frissíteni vagy kiürített, ezért a leállás volt látható a attól függően, hogyan történt a DNS-infrastruktúra a telepítő. Ezek a problémák enyhíthetők alacsony TTL értékekkel intranetes alkalmazások esetén, és segítségével [Azure Traffic Manager az automatikus rendszer-Helyreállítás](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) az internet-alapú alkalmazások

### <a name="changing-the-ip-addresses---illustration"></a>Az IP-címek - illusztráció módosítása
Ossza meg velünk tekintse meg a forgatókönyv hol szeretne használni a különböző IP-címek az elsődleges és a helyreállítási helyen. Az alábbi példa azt is rendelkezik egy harmadik hely a ahol az alkalmazások üzemeltetett elsődleges vagy tartalék hely elérése.

![Különböző IP - feladatátvétel előtt](./media/site-recovery-network-design/network-design10.png)


A fenti kép néhány alhálózati 192.168.1.0/24 alhálózat az elsődleges helyen futtatott alkalmazások vannak, és a helyreállítási helyen lévő alhálózati 172.16.1.0/24 egy feladatátvétel után lesz konfigurálva. VPN-kapcsolatok és a hálózati útvonalak konfigurálva megfelelően, hogy az összes három helyen képesek elérni egymást.

Az alábbi, egy vagy több alkalmazást, miután a képként azok visszaáll a helyreállítási alhálózaton. Ebben az esetben azt nem korlátozza a teljes alhálózat feladatátvétele egy időben. Nincs szükség módosításra, hogy engedélyezzék a VPN- vagy hálózati útvonalak. Feladatátvétel és bizonyos DNS-frissítések győződjön meg arról, hogy alkalmazások továbbra is elérhető lesz. Ha a DNS dinamikus frissítések engedélyezésére van konfigurálva a virtuális gépek volna regisztrálják magukat az új IP-cím használatával, ha elindítják a feladatátvétel után.

![Különböző IP - feladatátvételt követően](./media/site-recovery-network-design/network-design11.png)


Hibás során a replika virtuális gép lehet szükség, amely nem azonos az elsődleges virtuális gép IP-címe IP-címet. Virtuális gépek frissíti a DNS-kiszolgálót használják indítása után. DNS-bejegyzések általában módosítani vagy a hálózaton a kiürített, pedig hálózati táblák a gyorsítótárazott bejegyzés frissítése vagy kiürített, így nem ritka állásidő szembesül, amíg ezek kerül sor. A probléma enyhíthetők:

* Intranetes alkalmazások alacsony TTL értéket használja.
* Az automatikus rendszer-Helyreállítás Azure Traffic Manager használ az internet-alapú alkalmazások.
* A helyreállítási terv belül a következő parancsfájl használatával (a parancsfájl nincs szükség esetén a dinamikus DNS-regisztráció van beállítva) egy időben történő frissítés biztosításához a DNS-kiszolgáló frissítése

        param(
        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

### <a name="changing-the-ip-addresses--dr-to-azure"></a>Az IP-címek – az Azure-bA DR módosítása
A [hálózati infrastruktúra telepítése a Microsoft Azure vész-helyreállítási helyként](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) blogbejegyzés ismerteti, hogyan szükséges Azure hálózati infrastruktúra beállításával, ha megtartja az IP-címek nem előfeltétele. Az alkalmazás leíró kezdődik, és keresse meg, hogyan állíthatja hálózati helyszíni, illetve a Azure és a majd áthaladva addig tart, a feladatátvételi tesztet, és a tervezett feladatátvétel végrehajtásához.

## <a name="next-steps"></a>Következő lépések
[Ismerje meg,](site-recovery-vmm-to-vmm.md#prepare-for-network-mapping) Site Recovery hogyan képezi le a forrás és cél hálózatok a használatakor a VMM-kiszolgáló az elsődleges hely kezeléséhez.
