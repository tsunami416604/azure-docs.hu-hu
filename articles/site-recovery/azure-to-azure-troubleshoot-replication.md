---
title: Az Azure Site Recovery hibaelhárítása az Azure – Azure replikációval kapcsolatos problémákat és hibákat |} A Microsoft Docs
description: Vész-helyreállítási Azure-beli virtuális gépek replikálása hibáinak és problémáinak elhárítása
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: asgang
ms.openlocfilehash: 22ea3d955fe2910dc99ab4015165008da899d48e
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52312850"
---
# <a name="troubleshoot-azure-to-azure-vm-ongoing-replication-issues"></a>Azure – Azure virtuális gép folyamatban lévő replikáció hibáinak elhárítása

Ez a cikk az Azure Site Recoveryben replikálása és helyreállítása Azure-beli virtuális gépek egyik régióból egy másik régióba gyakori problémákat ismerteti, és azok megoldását ismerteti. Támogatott konfigurációk kapcsolatos további információkért lásd: a [támogatási mátrixa Azure virtuális gépek replikálása](site-recovery-support-matrix-azure-to-azure.md).


## <a name="recovery-points-not-getting-generated"></a>Nem első létrehozott helyreállítási pontok

HIBAÜZENET: Nincs összeomlási elérhető alkalmazáskonzisztens helyreállítási pont a virtuális gép az elmúlt 60 perc.</br>
HIBA AZONOSÍTÓJA: 153007 </br>

Az Azure Site Recovery folyamatosan a forrásrégióban replikálja az adatokat a vészhelyreállítási régióban, és 5 percenként összeomlás-konzisztens pontot hoz létre. Ha a Site Recovery nem tudja létrehozni a helyreállítási pontok 60 percig, majd a felhasználó értesítést. Az alábbiakban, amely ezt a hibát eredményezhet az okok:

**1. ok: [magas adatváltozási sebessége a forrás virtuális gépen](#high-data-change-rate-on-the-source-virtal-machine)**    
**2. ok: [hálózati kapcsolati hiba ](#Network-connectivity-issue)**

## <a name="causes-and-solutions"></a>Okait és megoldásait

### <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Magas adatváltozási sebessége a forrás virtuális gép
Az Azure Site Recovery egy esemény küldése, ha a forrás virtuális gép adatváltozási sebessége meghaladja a támogatott határértékeket. Ellenőrizze, hogy a probléma csak nagy forgalom miatt, lépjen a replikált elemek > virtuális gép > kattintson az "események – elmúlt 72 órában".
Az alábbi képernyőképen látható módon megjelenik a "adatok módosítási gyakorisága meghaladja a támogatott határértékeket"

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Ha az esemény kattint, látnia kell a pontos lemez adatait az alábbi képernyőképen látható módon

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


#### <a name="azure-site-recovery-limits"></a>Az Azure Site Recovery korlátai
Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza. Ezek a korlátok a saját tesztjeinken alapulnak, de nem fedhetik le az alkalmazások minden lehetséges I/O-kombinációját. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak. Azt is vegye figyelembe, hogy két korlátozva van, érdemes lehet lemezenként data churn, és egy virtuális gép adatainak adatváltozás.
Például, ha megnézzük a prémium szintű P20 lemez az alábbi táblázat, a Site Recovery lemezenként az öt ilyen lemezek virtuális gépenkénti miatt a 25 MB/s összes adatforgalom virtuális gépenként legfeljebb 5 MB/s forgalom kezelésére is.

**Replikáció tárolási célja** | **Forráslemez átlagos I/O-mérete** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB  | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |  Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |10 MB/s | Lemezenként 842 GB

### <a name="solution"></a>Megoldás
Tisztában vagyunk kell azzal, hogy rendelkezik-e az Azure Site Recovery data sebességhatárok lemez típusának módosítása. Az, ha a probléma ismétlődő vagy rövid ideig, fontos, hogy az adatváltozási sebesség minta az érintett virtuális gép található.
Található a módosult adatok aránya az érintett virtuális gép. Nyissa meg a forrás virtuális gép > metrikákat a figyelés és a metrikák hozzáadása a lent látható módon.

![high_data_change_rate](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Kattintson a "Hozzáadás metrika", és adja hozzá a "Operációsrendszer-lemez írási sebessége bájt/mp" és "Adatlemez írási bájt/mp".
2. Figyelheti a megnövekedett, a képernyőképen látható módon.
3. Megjelenik az összes írási műveletet operációsrendszer-lemez és az összes adatlemezt együttesen történik. Most ezeket a metrikákat nem kiderülhet, per disk szintadatait, de a teljes adatmennyiség adatváltozási minta egy jól jelzi.

Azokban az esetekben, például a fent egy alkalmanként adatokat hirtelen és adatváltozási sebessége meghaladja a 10 MB/s (a prémium szintű) és 2 MB/s (Standard) egy kis ideig, és tartalmaz, replikációs fog legérdekesebb részeit. Azonban ha a forgalom a támogatott korlátot túlmutató a legtöbbször, majd érdemes egyikét az alábbi beállítást, ha lehetséges:

**1. lehetőség:** okozza a magas adatváltozási sebesség lemezének kizárása: </br>
Jelenleg zárhatók ki a lemezt használó [Site Recovery Powershell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine)

**2. lehetőség:** a katasztrófa utáni helyreállítás tárolási lemez szint módosítása: </br>
Ez a beállítás csak akkor lehetséges, ha a lemez adatváltozásának kevesebb mint 10 MB/s. Lehetővé tegyük fel, hogy egy virtuális Gépet a P10 lemez egy nagyobb, mint a 8 MB/s, de kevesebb mint 10 MB/s adatváltozása esetén adódnak. Ha az ügyfél védelem során P30 lemez használható a céloldali tárolóra, majd a probléma megoldhatók.

### <a name="Network-connectivity-issue"></a>Hálózati kapcsolati hiba

#### <a name="network-latency-to-cache-storage-account-"></a>Hálózati késés gyorsítótárfiókba:
 A Site Recovery a replikált adatokat küld a gyorsítótárfiókot, és a probléma akkor fordulhat elő, ha feltöltéséhez a virtuális gépről a gyorsítótárfiókba lassabb a 4 MB 3 mp. Ellenőrizze, hogy van-e bármilyen probléma késés használatával kapcsolatos [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) feltölteni az adatokat a virtuális gépről a gyorsítótárfiókba.<br>
Ha nagy a késés, ellenőrizze, ha egy hálózati virtuális berendezések segítségével szabályozhatja a virtuális gépek kimenő hálózati forgalmát. A berendezés előfordulhat, hogy leszabályozza, ha a replikációs forgalmat az nva-n keresztül halad. Azt javasoljuk, hogy hoz létre egy hálózati végpontot a virtuális hálózat "Tároló" úgy, hogy a replikálás forgalma nem halad, az nva-n. Tekintse meg [hálózati virtuális berendezés konfigurációjának](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)

#### <a name="network-connectivity"></a>Hálózati kapcsolat
A Site Recovery replikációja, a kimenő kapcsolat az adott URL-címek vagy IP-címtartományok szükség a virtuális gépről. Ha a virtuális gép tűzfal mögött található, vagy használja a hálózati biztonsági csoport (NSG) szabályai kimenő kapcsolat szabályozásához, előfordulhat, hogy között ezek a problémák egyike.</br>
Tekintse meg [kimenő kapcsolatok esetében a Site Recovery URL-címeinek](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) , győződjön meg arról, hogy minden URL-címek vannak csatlakoztatva 