---
title: A Azure NetApp Files SMB-teljesítményével kapcsolatos gyakori kérdések | Microsoft Docs
description: Válaszok a Azure NetApp Files SMB-teljesítményével kapcsolatos gyakori kérdésekre.
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
ms.date: 01/14/2020
ms.author: b-juche
ms.openlocfilehash: 6391a3eeead6a52371c11011a65f4b4de7260156
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046421"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>A Azure NetApp Files SMB-teljesítményével kapcsolatos gyakori kérdések

Ez a cikk a Azure NetApp Files SMB-teljesítményre vonatkozó ajánlott eljárásaival kapcsolatos gyakori kérdéseket (GYIK) válaszol.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Engedélyezve van-e a többcsatornás SMB az SMB-megosztásokban? 

Igen, a többcsatornás SMB alapértelmezés szerint engedélyezve van, a módosítás a 2020 január elején történik. A meglévő SMB-kötetek előtti összes SMB-megosztás engedélyezve lett, és az újonnan létrehozott kötetek a létrehozáskor is engedélyezve lesznek a szolgáltatással. 

A többcsatornás SMB funkció használatának kihasználása érdekében a funkció engedélyezése előtt létrehozott SMB-kapcsolatokat vissza kell állítani. A visszaállításhoz leválaszthatja és újracsatlakoztathatja az SMB-megosztást.

## <a name="is-rss-supported"></a>Támogatott az RSS?

Igen, Azure NetApp Files támogatja a fogadó oldali skálázást (RSS).

Ha engedélyezve van a többcsatornás SMB, a SMB3-ügyfél több TCP-kapcsolatot létesít a Azure NetApp Files SMB-kiszolgálóval egy olyan hálózati adapteren (NIC) keresztül, amely egyetlen RSS-kompatibilis. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Mely Windows-verziók támogatják a többcsatornás SMB-t?

A Windows támogatja a többcsatornás SMB használatát, mivel a Windows 2012 lehetővé teszi a legjobb teljesítmény elérését.  A részletekért lásd: [többcsatornás SMB telepítése](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) és [a többcsatornás SMB alapjai](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) . 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Támogatja az Azure-beli virtuális gépek az RSS-t?

Ha szeretné megtudni, hogy az Azure-beli virtuális gépek hálózati adapterei támogatják-e az RSS-t, futtassa a parancsot `Get-SmbClientNetworkInterface` a következőképpen, és ellenőrizze a mezőt `RSS Capable` 

![RSS-támogatás az Azure-beli virtuális géphez](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files támogatja az SMB Direct szolgáltatást?

Nem, Azure NetApp Files nem támogatja a közvetlen SMB-t. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Milyen előnyökkel jár a többcsatornás SMB? 

A többcsatornás SMB szolgáltatás lehetővé teszi, hogy az SMB3-ügyfél kapcsolatok készletet hozzon létre egyetlen hálózati kártyán (NIC) vagy több hálózati adapteren keresztül, valamint hogy egyetlen SMB-munkamenetre vonatkozó kérelmeket küldjön. Ezzel szemben a tervezés, a SMB1 és a SMB2 megköveteli, hogy az ügyfél egy kapcsolatot hozzon létre, és az adott munkamenethez tartozó összes SMB-forgalmat elküldje a kapcsolaton keresztül. Ez az egyetlen kapcsolat korlátozza a protokollok teljes teljesítményét, amely egyetlen ügyfélről is elérhető.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Több hálózati adaptert konfigurálok az SMB-ügyfélhez?

Nem. Az SMB-ügyfél megfelel az SMB-kiszolgáló által visszaadott hálózati adapterek számának.  Minden tárolási kötet egy és csak egy tárolási végpontból érhető el.  Ez azt jelenti, hogy a rendszer csak egy hálózati adaptert használ az adott SMB-kapcsolathoz.  

Az alábbi `Get-SmbClientNetworkInterace` kimenete azt mutatja, hogy a virtuális gépnek két hálózati adaptere van – 15 és 12.  Ahogy az az alábbi parancsban is látható, `Get-SmbMultichannelConnection`, bár két RSS-kompatibilis hálózati adapter van, akkor a rendszer csak a 12-es felületet használja az SMB-megosztással kapcsolatban. a 15. illesztőfelület nincs használatban.

![RSS-kompatibilis hálózati adapterek](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Támogatott a hálózati adapterek összevonása az Azure-ban?

A hálózati adapterek összevonása az Azure-ban nem támogatott. Bár az Azure Virtual Machines szolgáltatásban több hálózati adapter is támogatott, a fizikai szerkezet helyett logikai értéknek kell lennie. Ezért nem biztosítanak hibatűrést.  Emellett az Azure-beli virtuális gépek számára elérhető sávszélesség a gép számára is kiszámítható, nem pedig egyedi hálózati adapterre.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Mi a teljesítmény, mint a többcsatornás SMB?

Az alábbi tesztek és grafikonok a többcsatornás SMB hatékonyságát mutatják be az Egypéldányos munkaterhelések esetében.

### <a name="random-io"></a>Véletlenszerű I/O  

Ha a többcsatornás SMB le van tiltva az ügyfélen, a FIO és a 40-GiB munkakészletek használatával a Pure 8 – KiB olvasási és írási tesztek elvégzése is megtörtént.  Az SMB-megosztást leválasztották az egyes tesztek között, és a `1`,`4`,`8`,`16`és `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`RSS-kapcsolati beállításain keresztül az SMB-ügyfélkapcsolatok számát. A tesztek azt mutatják, hogy az `4` alapértelmezett beállítása elegendő az I/O-igényes munkaterhelésekhez. a `8` és `16`nak való növekedés nem volt hatással. 

A parancs `netstat -na | findstr 445` bizonyította, hogy további kapcsolatok lettek létrehozva, `1`től `4`ig, `8`ig és `16`ig.  Minden teszt során négy CPU-magot teljes mértékben használtak az SMB számára, ahogy azt a perfmon `Per Processor Network Activity Cycles` statisztikája megerősítette (ez a cikk nem tartalmazza.)

![Véletlenszerű I/O-tesztek](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Az Azure-beli virtuális gép nem befolyásolja az SMB-(vagy NFS-) tárolási I/O-korlátozásokat.  Ahogy az alábbi ábrán is látható, a D16-példány típusa korlátozott 32 000 a gyorsítótárazott tárolás IOPS és 25 600 a nem gyorsítótárazott tárolási IOPS esetében.  A fenti gráf azonban jelentősen több I/O-t mutat az SMB protokollon keresztül.

![Véletlenszerű I/O-összehasonlítás](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Szekvenciális IO 

A fent ismertetett véletlenszerű I/O-tesztekhez hasonló tesztek 64 – KiB szekvenciális I/O-vel lettek elvégezve. Bár az ügyfél-kapcsolati kapcsolatok száma az RSS-hálózati adapteren (a 4. után) nem volt észrevehető hatással a véletlenszerű I/O-műveletekre, ugyanez nem vonatkozik a szekvenciális I/O-ra. Ahogy az alábbi ábrán is látható, az egyes növekmények az olvasási sebesség megfelelő növekedésével vannak társítva. Az írási átviteli sebesség az Azure által az egyes példányok típusának és méretének megfelelő hálózati sávszélességre vonatkozó korlátozások miatt megmaradt. 

![Szekvenciális I/O-tesztek](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Az Azure minden egyes virtuális gép típusára/méretére korlátozza a hálózati díjszabást. A ráta korlátja csak a kimenő forgalomra érvényes. A virtuális gépen lévő hálózati adapterek száma nem befolyásolja a számítógép számára elérhető sávszélesség teljes mennyiségét.  Az D16-példány típusa például egy 8000 Mbps (1 000 MiB/s) hálózati korláttal rendelkezik.  Ahogy a fenti szekvenciális gráf mutatja, a korlát befolyásolja a kimenő forgalmat (írásokat), de a többcsatornás olvasásokat nem.

![Szekvenciális I/O-összehasonlítás](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-advanced-networking-recommended"></a>Ajánlott-e a speciális hálózatkezelés?

A maximális teljesítmény érdekében ajánlott a speciális hálózatkezelés konfigurálása, ahol lehetséges. Tartsa szem előtt az alábbi szempontokat:  

* A Azure Portal alapértelmezés szerint engedélyezi a speciális hálózatkezelést a szolgáltatást támogató virtuális gépek számára.  Más üzembe helyezési módszerek, például a Ansible és a hasonló konfigurációs eszközök azonban nem.  Nem sikerült engedélyezni a speciális hálózatkezelést, hogy akadály a gép teljesítményét.  
* Ha a speciális hálózatkezelés nincs engedélyezve a virtuális gép hálózati adapterén, mert a példány típusa vagy mérete nem támogatott, akkor továbbra is le lesz tiltva a nagyobb példányok esetében. Ezekben az esetekben manuális beavatkozásra lesz szüksége.

## <a name="are-jumbo-frames-supported"></a>A Jumbo-keretek támogatottak?

Az Azure-beli virtuális gépek nem támogatják a Jumbo-kereteket.

## <a name="is-smb-signing-supported"></a>Támogatott-e az SMB-aláírás? 

Az SMB protokoll a fájl-és nyomtatómegosztás, valamint más hálózati műveletek, például a távoli Windows-felügyelet alapját biztosítja. Ha meg szeretné akadályozni, hogy az SMB-csomagokat az átvitel során módosító, nem a közepes támadásokkal szemben, az SMB protokoll támogatja az SMB-csomagok digitális aláírását. 

Az SMB-aláírás a Azure NetApp Files által támogatott összes SMB protokoll-verzió esetében támogatott. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Milyen hatással van az SMB-aláírás teljesítményére?  

Az SMB-aláírás káros hatással van az SMB teljesítményére. A teljesítmény romlásának egyéb lehetséges okai között az egyes csomagok digitális aláírása további ügyféloldali PROCESSZORt használ, mivel az alábbi Perfmon-kimenet látható. Ebben az esetben a 0. alap a felelős az SMB-hez, beleértve az SMB-aláírást is.  Az előző szakaszban szereplő, nem többcsatornás szekvenciális olvasási adatátviteli számokkal való összehasonlítás azt mutatja, hogy az SMB-aláírás csökkenti a teljes átviteli sebességet a 875MiB/s-ről körülbelül 250MiB/s értékre. 

![SMB-aláírás teljesítményére gyakorolt hatás](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Következő lépések  

- [Gyakori kérdések a Azure NetApp Files](azure-netapp-files-faqs.md)
- Tekintse [meg a Azure NetApp Files: felügyelt vállalati FÁJLMEGOSZTÁS SMB](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) számítási feladatokhoz az SMB-fájlmegosztás és a Azure NetApp Files használatával című témakört.