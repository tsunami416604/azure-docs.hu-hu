---
title: Gyakori kérdések az Azure NetApp Files SMB-teljesítményéről| Microsoft dokumentumok
description: Válaszok az Azure NetApp-fájlok SMB-teljesítményével kapcsolatos gyakori kérdésekre.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 24b3710861f0ee158619ae9103584dcdb181f3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460449"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Gyakori kérdések az Azure NetApp-fájlok SMB-teljesítményéről

Ez a cikk választ ad a gyakran ismételt kérdésekre az SMB azure NetApp-fájlok teljesítményével kapcsolatos gyakorlati tanácsokkal kapcsolatban.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Az SMB többcsatornás kapcsolat engedélyezve van az SMB-megosztásokban? 

Igen, az SMB többcsatornás alapértelmezés szerint engedélyezve van, a változás 2020 január elején történt. Minden SMB-megosztás már korábban már rendelkezett a meglévő SMB-kötetekkel, és az összes újonnan létrehozott köteten a szolgáltatás is engedélyezve lesz a létrehozás időpontjában. 

Az SMB többcsatornás funkció előnyeinek kihasználásához a szolgáltatás engedélyezése előtt létrejött minden SMB-kapcsolatot alaphelyzetbe kell állítani. Az alaphelyzetbe állításhoz leválaszthatja és újra csatlakoztathatja az SMB-megosztást.

## <a name="is-rss-supported"></a>Támogatott az RSS?

Igen, az Azure NetApp Files támogatja a fogadási oldali skálázást (RSS).

Ha az SMB többcsatornás kapcsolat engedélyezve van, az SMB3-ügyfél több TCP-kapcsolatot hoz létre az Azure NetApp Files SMB-kiszolgálóhoz egy olyan hálózati adapteren (NIC) keresztül, amely egyetlen RSS-képes. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Mely Windows-verziók támogatják az SMB Multichannel alkalmazást?

A Windows a Windows 2012 óta támogatja az SMB multicsatornát a legjobb teljesítmény érdekében.  [A részleteket lásd: SMB többcsatornás üzembe helyezése](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) és az [SMB többcsatornás adatcsatornái.](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Támogatja az Azure virtuális gépem az RSS-t?

Ha meg szeretné tudni, hogy az Azure virtuális `Get-SmbClientNetworkInterface` gép hálózati adapterei `RSS Capable`támogatják-e az RSS-t, futtassa a parancsot az alábbiak szerint, és ellenőrizze a mezőt: 

![RSS-támogatás az Azure virtuális géphez](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Támogatja az Azure NetApp-fájlok az SMB Direct szolgáltatást?

Nem, az Azure NetApp Files nem támogatja az SMB Direct szolgáltatást. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Mi az SMB Multichannel előnye? 

Az SMB többcsatornás szolgáltatás lehetővé teszi, hogy az SMB3-ügyfél egyetlen hálózati kártya (NIC) vagy több hálózati adapteren keresztül létesítsen kapcsolatokat, és egyetlen SMB-munkamenetre vonatkozó kérelmek küldésére használja őket. Ezzel szemben az SMB1 és az SMB2 rendszer megköveteli az ügyféltől, hogy egyetlen kapcsolatot hozzon létre, és az adott munkamenetre vonatkozó összes SMB-forgalmat elküldje a kapcsolaton keresztül. Ez az egyetlen kapcsolat korlátozza az egyetlen ügyféláltal elérhető protokolláltalános teljesítményt.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Több hálózati adaptert kell konfigurálnom az ügyfelemen az SMB-hez?

Nem. Az SMB-ügyfél megegyezik az SMB-kiszolgáló által visszaadott hálózati adapterszámmal.  Minden tárolókötet egyetlen tárolóvégpontról érhető el.  Ez azt jelenti, hogy egy adott SMB-kapcsolathoz csak egy hálózati adapter lesz használva.  

Ahogy az `Get-SmbClientNetworkInterace` alábbi kimenet mutatja, a virtuális gép két hálózati adapter - 15 és 12.  Amint az a `Get-SmbMultichannelConnection`parancs ban látható , annak ellenére, hogy két RSS-kompatibilis NICS van, csak a 12-es interfészt használják az SMB-megosztással kapcsolatban; 15-ös interfész nincs használatban.

![RSS-kompatibilis NICS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Támogatja a hálózati adapterek összeegyezését az Azure-ban?

A hálózati adapterek összeegyezése nem támogatott az Azure-ban. Bár az Azure virtuális gépeken több hálózati csatoló is támogatott, logikai, nem pedig fizikai konstrukciót képviselnek. Mint ilyen, nem nyújtanak hibatűrést.  Emellett az Azure virtuális gép számára elérhető sávszélesség et magához a géphez, és nem az egyes hálózati adapterekhez számítja ki a rendszer.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Milyen a teljesítmény az SMB Multichannel esetében?

A következő tesztek és grafikonok bemutatják az SMB többcsatornás egypéldányos számítási feladatok erejét.

### <a name="random-io"></a>Véletlenszerű I/O  

Az SMB multichannel levan tiltva az ügyfélen, tiszta 8-KiB olvasási és írási teszteket végeztek fio és egy 40-GiB munkakészlet használatával.  Az SMB-megosztás lelett választva az egyes tesztek között, az SMB-ügyfélkapcsolati`8``16`szám `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`növekményei pedig `1`rss hálózati kapcsolatbeállításai szerint ,`4`, , , . A tesztek azt mutatják, hogy az `4` alapértelmezett beállítás elegendő az I/O-intenzív munkaterhelések esetében; növekménye, `8` és `16` nem volt hatása. 

A `netstat -na | findstr 445` parancs bebizonyította, hogy további kapcsolatokat `1` hoztak `4` `8` létre `16`növekményekkel a és a között.  Minden teszt során négy CPU-magot használtak ki teljes mértékben `Per Processor Network Activity Cycles` az SMB-hez, amit a perfmon-statisztika is megerősített (ez a cikk nem tartalmazza).)

![Véletlenszerű I/O-tesztek](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Az Azure virtuális gép nem befolyásolja az SMB (sem Az NFS) tárolási I/O-korlátokat.  Az alábbiakban látható, a D16-példány típusa korlátozott sebessége 32 000 a gyorsítótárazott tárolási IOPS és 25.600 a nem gyorsítótárazott tárolási IOPS.  A fenti grafikon azonban jelentősen több I/O-t mutat az SMB-n keresztül.

![Véletlen I/O összehasonlítás](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Szekvenciális I/O 

A fent leírt véletlenszerű I/O-vizsgálatokhoz hasonló vizsgálatokat végeztek 64-KiB szekvenciális I/O-val. Bár az ügyfélkapcsolatok számának növekedése rss hálózati adapterenként 4' után nem volt észrevehető hatással a véletlenszerű I/O-ra, ugyanez nem vonatkozik a szekvenciális I/O-ra. Ahogy a következő grafikon mutatja, minden növekedés az olvasási átviteli szint megfelelő növekedésével jár. Az írási átviteli igény az Azure által az egyes példánytípusokra/-méretre vonatkozó hálózati sávszélesség-korlátozások miatt állandó maradt. 

![Szekvenciális I/O-vizsgálatok](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Az Azure minden virtuálisgép-típusra/-méretre korlátozza a hálózati sebességkorlátot. A díjkorlát csak a kimenő forgalomra van kiszabva. A virtuális gépen lévő hálózati adapterek száma nincs hatással a gép rendelkezésére álló sávszélesség teljes mennyiségére.  A D16-példány típusának például 8000 Mbps (1000 MiB/s) hálózati korlátja van.  Ahogy a fenti szekvenciális grafikon mutatja, a korlát hatással van a kimenő forgalomra (írás), de a többcsatornás olvasásokra nem.

![Szekvenciális I/O-összehasonlítás](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>Ajánlott a gyorsított hálózatkezelés?

A maximális teljesítmény érdekében ajánlott a [gyorsított hálózatkonfigurálás](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) lehetőség szerint konfigurálni. Tartsa szem előtt a következő szempontokat:  

* Az Azure Portal alapértelmezés szerint lehetővé teszi a gyorsított hálózatkezelést a funkciót támogató virtuális gépek számára.  Előfordulhat azonban, hogy más központi telepítési módszerek, például az Ansible és a hasonló konfigurációs eszközök nem.  A gyorsított hálózatkezelés engedélyezésének elmulasztása megbéníthatja a gép teljesítményét.  
* Ha az Accelerated Networking nincs engedélyezve a virtuális gép hálózati felületén, mert egy példány típusa vagy mérete nem támogatja, akkor a nagyobb példánytípusokkal le tiltása marad. Ezekben az esetekben manuális beavatkozásra lesz szüksége.

## <a name="are-jumbo-frames-supported"></a>Támogatottak a jumbo keretek?

Jumbo keretek nem támogatottak az Azure virtuális gépek.

## <a name="is-smb-signing-supported"></a>Támogatott az SMB-aláírás? 

Az SMB protokoll képezi a fájl- és nyomtatómegosztás, valamint más hálózati műveletek, például a távoli Windows-felügyelet alapját. Az SMB protokoll támogatja az SMB-csomagok digitális aláírását, hogy megelőzze a köztes műveletek et módosító, nem kerültsorba kerülő támadásokat. 

Az SMB-aláírás az Azure NetApp Files által támogatott összes SMB protokollverzióban támogatott. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Milyen hatással van az SMB-aláírás teljesítményére?  

Az SMB-aláírás káros hatással van az SMB teljesítményére. A teljesítménycsökkenés egyéb lehetséges okai között az egyes csomagok digitális aláírása további ügyféloldali PROCESSZORt fogyaszt, amint azt az alábbi perfmon kimenet mutatja. Ebben az esetben a Core 0 felelősnek tűnik az SMB-ért, beleértve az SMB-aláírást is.  Az előző szakaszban a nem többcsatornás szekvenciális olvasási átviteli sebességszámokkal való összehasonlítás azt mutatja, hogy az SMB-aláírás 875MiB/s-ről körülbelül 250MiB/s-re csökkenti a teljes átviteli csatornát. 

![Az SMB-aláírás teljesítményére gyakorolt hatása](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>További lépések  

- [Gyakori kérdések az Azure NetApp-fájlokról](azure-netapp-files-faqs.md)
- Tekintse meg az [Azure NetApp-fájlok: Felügyelt vállalati fájlmegosztások SMB-számítási feladatokhoz](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) az SMB-fájlmegosztások Azure NetApp-fájlokkal való használatáról.
