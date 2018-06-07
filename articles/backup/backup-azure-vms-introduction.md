---
title: A virtuális gép az Azure biztonsági mentési infrastruktúra tervezése
description: Az Azure virtuális gépek biztonsági megtervezésekor fontos tudnivalók találhatók
services: backup
author: markgalioto
manager: carmonm
keywords: virtuális gépek biztonsági mentése, a virtuális gépek biztonsági mentése
ms.service: backup
ms.topic: conceptual
ms.date: 3/23/2018
ms.author: markgal
ms.openlocfilehash: 92122e7dc62e0f402bcddff099984e6e2c605fae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606086"
---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Virtuális gép biztonsági infrastruktúrájának megtervezése az Azure-ban
Ez a cikk ismerteti a teljesítmény- és erőforrás-javaslatok a virtuális gép biztonsági mentési infrastruktúra tervezéséhez nyújtanak segítséget. A biztonsági mentési szolgáltatás; fő szempontjait is meghatározza lehet, hogy ezeket az jellemzőket fontos meghatározni, hogy az architektúra a kapacitástervezés és ütemezés. Ha megismerte [a környezet előkészítése](backup-azure-arm-vms-prepare.md), tervezés, mint a megkezdése előtt [biztonsági mentése a virtuális gépek](backup-azure-arm-vms.md). Ha az Azure virtuális gépek több információra van szüksége, tekintse meg a [Virtual Machines – dokumentáció](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="how-does-azure-back-up-virtual-machines"></a>Hogyan működik az Azure virtuális gépek biztonsági mentése?
Ha az Azure Backup szolgáltatás indít el a biztonsági mentési feladatot a megadott időpontban, a szolgáltatás eseményindítók időpontban pillanatképének elkészítéséhez tartalék mellék. Az Azure Backup szolgáltatás használ a _VMSnapshot_ Windows, a bővítmény és a _VMSnapshotLinux_ Linux bővítményt. A bővítmény telepítve van a virtuális gép első biztonsági mentés során. A bővítmény telepítéséhez, a virtuális Gépen kell futnia. Ha a virtuális gép nem fut, a Backup szolgáltatás az alapul szolgáló tárolóról készít pillanatképet (mivel nem történik alkalmazásírás, amikor a virtuális gép le van állítva).

Amikor Windows rendszerű virtuális gépekről készít pillanatképet, a Backup szolgáltatás együttműködik a Kötet árnyékmásolata szolgáltatással (VSS), hogy egységes pillanatképet készítsen a virtuális gép lemezeiről. Ha biztonsági mentést készít a Linux virtuális gépek, szkripteket saját egyéni konzisztencia biztosításához, ha a virtuális gép pillanatkép létrehozása van folyamatban. Meghívja a parancsfájlokat a részletek a cikk későbbi részében találhatók.

Amikor az Azure Backup szolgáltatás elkészítette a pillanatképet, az adatok átkerülnek a tárolóba. A maximális hatékonyság érdekében a szolgáltatás csak azokat az adatblokkokat azonosítja és továbbítja, amelyek az előző biztonsági mentés óta változtak.

![Azure virtuális gép biztonsági mentési architektúra](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Ha az adatátvitel befejeződött, a rendszer eltávolítja a pillanatképet, és létrehoz egy helyreállítási pontot.

> [!NOTE]
> 1. A biztonsági mentési folyamat során Azure biztonsági mentés nem tartalmazza a ideiglenes lemezt a virtuális géphez csatolt. További információkért tekintse meg a blog [ideiglenes tárolási](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).
> 2. Az Azure biztonsági mentés vesz egy tárolási szintű pillanatképet, és továbbítja a tároló, ne változtassa meg a tárfiók kulcsait a biztonsági mentési feladat befejezéséig.
> 3. Prémium szintű virtuális gépekhez Azure biztonsági mentés másolja a pillanatkép a tárfiók. Gondoskodjon arról, hogy a biztonsági mentési szolgáltatás elegendő IOPS az adatok átviteléhez a tároló azt. A tárolási további példányának fel van töltve, a virtuális gép lefoglalt méret szerint. 
>

### <a name="data-consistency"></a>Adatkonzisztencia
Biztonsági mentése és visszaállítása a kritikus fontosságú adatok bonyolítja van arra, hogy üzleti szempontból fontos adatokhoz kell biztonsági mentése során az alkalmazások az adatokat előállító üzleti futnak. Orvoslása érdekében Azure biztonsági mentés támogatja az alkalmazáskonzisztens biztonsági mentés a Windows és a Linux virtuális gépek
#### <a name="windows-vm"></a>Windows rendszerű virtuális gép
Azure biztonsági mentés VSS teljes biztonsági mentés a Windows virtuális gépeken időt vesz igénybe. (további információk [VSS teljes biztonsági mentés](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Ahhoz, hogy a VSS másolásos biztonsági mentéshez, a következő beállításkulcsot kell állítani a virtuális Gépen.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```

#### <a name="linux-vms"></a>Linux rendszerű virtuális gépek
Azure biztonsági mentés egy parancsfájl-kezelési keretrendszert biztosít. Linux virtuális gépek biztonsági mentéséről alkalmazás konzisztencia érdekében hozzon létre egyéni előtti és utáni parancsfájlok, amelyek vezérlik a biztonsági mentési munkafolyamat és a környezetben. Azure biztonsági mentés előtti parancsfájl elindítja a virtuális gép pillanatképének készítése előtt, és meghívja az utólagos parancsfájl a virtuális gép pillanatkép-feladat befejeződése után. További részletekért lásd: [alkalmazás konzisztens VM a biztonsági mentéseket parancsfájl előtti és utáni parancsfájl](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).
> [!NOTE]
> Azure biztonsági mentés csak hív meg, az ügyfél-írt előtti és utáni parancsfájlokat. Ha a előtti parancsfájl és utáni parancsfájlok végrehajtása sikertelen, Azure biztonsági mentés állapotúként jelöli meg a helyreállítási pont alkalmazás konzisztens. Azonban az ügyfél akkor végső soron felelős alkalmazás konzisztenciájának, amikor egyéni parancsfájlok használatával.
>


Ez a táblázat ismerteti az konzisztencia típusát és a feltételeket, ezek alapján során felmerülő Azure virtuális gép biztonsági mentési és helyreállítási eljárásokat.

| Konzisztencia | VSS-alapú | MAGYARÁZAT és részletek |
| --- | --- | --- |
| Alkalmazáskonzisztencia |Igen, a Windows rendszerhez|Alkalmazás konzisztencia ideális munkaterhelésekhez, biztosítja, hogy:<ol><li> A virtuális gép *elindul a*. <li>Nincs *sérülés nem*. <li>Nincs *adatvesztés nélküli*.<li> Az adatok minden az alkalmazáshoz, amely az adatokat, használja az alkalmazás biztonsági mentés – VSS vagy előkészítő/utólagos parancsfájl használatával bevonásával konzisztens.</ol> <li>*Windows virtuális gépek*-legtöbb Microsoft-munkaterhelések rendelkeznek VSS-író, amely kapcsolódik az adatok konzisztenciájának munkaterhelés-specifikus műveleteket hajthatja végre. Például a Microsoft SQL Server, amely biztosítja, hogy az írási műveleteket ad ki az adatbázis és a tranzakciós naplófájlt megfelelően történik-e VSS-író tartozik. Az Azure Windows virtuális gép biztonsági mentések alkalmazáskonzisztens helyreállítási pont létrehozása a tartalék mellék kell meghívni a VSS munkafolyamat és befejeződését, mielőtt a virtuális gép pillanatképének készítése. Az Azure virtuális gép pillanatképének pontosak, az összes Azure virtuális gép alkalmazás VSS-író elvégzése után is. (További a [VSS alapjait](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) és mély alaposabban részleteit [működésének](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *Linux virtuális gépek*-ügyfelek végrehajtható [egyéni előtti parancsfájl és az alkalmazás konzisztencia biztosításához utáni parancsfájl](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Fájlrendszer-konzisztenciával |Igen – Windows-alapú számítógépekhez |Két esetben, ahol a helyreállítási pont lehet *fájlrendszer konzisztens*:<ul><li>Linux virtuális gépek Azure nélkül előtti-script/követő-script, vagy ha előtti-script/követő-script nem sikerült biztonsági mentést. <li>VSS-hiba történt a Windows Azure virtuális gépek biztonsági mentését.</li></ul> Mindkét esetben a legjobb végezheti el, hogy ellenőrizze, hogy: <ol><li> A virtuális gép *elindul a*. <li>Nincs *sérülés nem*.<li>Nincs *adatvesztés nélküli*.</ol> Alkalmazásokat kell megvalósítani a saját "javítás felfelé" mechanizmus a visszaállított adatok. |
| Összeomlás-konzisztencia |Nem |Ez a helyzet megegyezik egy virtuális gép "összeomlási" (keresztül letölthető vagy rögzített reset) tapasztal. Összeomlási konzisztencia általában akkor fordul elő, le, amikor virtuális gépet az Azure biztonsági mentés idején. Összeomlás-konzisztens helyreállítási pontot nyújt az adathordozón tárolt--vagy az operációs rendszer vagy az alkalmazás szempontjából nem garantálja az adatok konzisztenciájának körül. Csak a biztonsági mentés idején a lemezen már meglévő adatok rögzítése és biztonsági mentése. <br/> <br/> Amíg vannak általában nem garantálja, az operációs rendszer indítása követ lemez ellenőrzése eljárás, például a chkdsk eszközhöz, bármely sérülést hibák elhárításához. Bármely a memóriában levő vagy nem átadott a lemezre írás elvesznek. Az alkalmazás általában követi a saját hitelesítési mechanizmus, abban az esetben adatok visszaállítás azért van szükség. <br><br>Tegyük fel bejegyzéseit az adatbázisban nem található a tranzakciós napló-e az adatbázisszoftver konfigurálásáért visszaállítja a újra, amíg az adatok összhangban. Adatok (például átnyúló kötetek) több virtuális lemez van elosztva, összeomlás-konzisztens helyreállítási pontot az adatok helyességét garanciát nem biztosít. |

## <a name="performance-and-resource-utilization"></a>Teljesítmény- és erőforrás-használat
Telepített helyszíni biztonsági mentési szoftver, például meg kell terveznie a kapacitás és az erőforrás-használat igényeinek biztonsági mentésekor az Azure virtuális gépeken. A [Azure Storage korlátozza](../azure-subscription-service-limits.md#storage-limits) meghatározhatja, hogyan kell a struktúra maximális teljesítménye érdekében minimális hatással van a munkafolyamatot futtató Virtuálisgép-telepítések.

A következő Azure Storage-korlátok különös biztonsági mentés teljesítményét tervezése során:

* Maximális kilépő / storage-fiók
* A tárfiók egy teljes kérelmek gyakorisága

### <a name="storage-account-limits"></a>Tárfiókok korlátai
Biztonsági mentési adatokat másolni a tárfiókból, hozzáadja a bemeneti/kimeneti műveletek száma másodpercenként (IOPS) és a kimenő forgalom (vagy átviteli) metrikákat a tárfiók. Ugyanakkor a virtuális gépek is fel iops-érték és a teljesítményt. A cél, hogy ellenőrizze a biztonsági mentési és a virtuális gép forgalom nem lépik túl a tárfiókok korlátai.

### <a name="number-of-disks"></a>Lemezek száma
A biztonsági mentési folyamat megkísérli a lehető leggyorsabban végezze el a biztonsági mentési feladatot. Ennek során, akkor használhatja a lehető legtöbb erőforrást. Azonban minden i/o-műveletek korlátozza a *egyetlen BLOB tároló átviteli*, 60 MB másodpercenként legfeljebb tartalmaz. Az a sebesség maximalizálása érdekében tett kísérlet, a biztonsági mentési folyamat megpróbálja készítsen biztonsági másolatot a virtuális gép lemezeinek mindegyikének *párhuzamosan*. Ha egy virtuális gép négy lemezzel rendelkezik, a szolgáltatás megkísérli készítsen biztonsági másolatot az összes négy lemez párhuzamosan. A **lemezek száma** a tárolási fiók biztonsági mentési forgalom meghatározásának egyik legfontosabb szempontja biztonsági mentése, folyamatban.

### <a name="backup-schedule"></a>Biztonsági mentés ütemezése
Egy további tényező, amely hatással van a teljesítmény a **biztonsági mentés ütemezése**. Így minden virtuális gép biztonsági mentése egy időben konfigurálhatja a házirendeket, ha beütemeztek egy forgalom a probléma megoldásáig. A biztonsági mentési folyamat megpróbálja készítsen biztonsági másolatot az összes lemez párhuzamosan. A biztonsági mentési adatforgalom csökkentése a tárfiókból, készítsen biztonsági másolatot különböző virtuális gépek különböző időpontban a nap-átlapolás nélkül.

## <a name="capacity-planning"></a>Kapacitástervezés
Üzembe az előző tényezőket, meg kell terveznie az a tárolási fiók igényei. Töltse le a [virtuális gép biztonsági mentési kapacitástervezési Excel-táblázat](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) a lemez és a biztonsági mentés ütemezése lehetőségei hatásának megjelenítéséhez.

### <a name="backup-throughput"></a>Biztonsági mentési átviteli sebesség
Az egyes lemezek biztonsági mentés alatt Azure biztonsági mentési olvassa be a lemezen a blokkok és csak a módosított adatokat (növekményes biztonsági mentés) tárolja. A következő táblázat a biztonsági másolat szolgáltatás átlagos átviteli értékek. A következő adatokat használja, megbecsülheti biztonsági mentése egy adott ekkora lemezt szükséges időt.

| Biztonsági mentési művelet | Hányad átviteli sebesség |
| --- | --- |
| Kezdeti biztonsági mentés |160 MB/s |
| A növekményes biztonsági mentés (DR) |640 Mbps <br><br> Átviteli sebesség jelentősen csökken, ha a lemez közötti megvédheti a módosított adatokat (igénylő biztonsági mentése).|

## <a name="total-vm-backup-time"></a>Virtuális gép teljes biztonsági mentés ideje
A biztonsági mentési legtöbbször ennek olvasását és az adatok másolásának telik, amíg más műveletek hozzájárul a teljes ideje, készítsen biztonsági másolatot a virtuális gép szükséges.

* Szükséges idő [telepítse vagy frissítse a tartalék mellék](backup-azure-arm-vms.md).
* Pillanatkép ideje, ami időn belül elindítani egy pillanatkép. A pillanatképek megközelíti az ütemezett biztonsági mentéskor aktiválódnak.
* Várakozási ideje. A biztonsági mentési szolgáltatás biztonsági mentések több ügyfél feldolgozása, mert pillanatkép biztonsági mentési adatok másolása a biztonsági mentési vagy a Recovery Services-tároló esetleg nem azonnal elindítani. A maximális idejét betöltése, és a várakozási is stretch legfeljebb nyolc óra feldolgozott biztonsági másolatok száma miatt. Azonban a teljes virtuális gép biztonsági mentési ideje 24 óránál kevesebb napi biztonsági mentési házirendek. <br>
**Ez a tároló érvényes csak növekményes biztonsági mentések és nem az első biztonsági mentés. Első biztonsági mentés ideje arányos, és az adatok méretétől függően 24 óránál hosszabb lehet, és az idő biztonsági mentés használatban van.**
* Adatátviteli idő, a biztonsági mentési szolgáltatás számára, a korábbi biztonsági mentés a növekményes változásokat számítási és a változások átvitele tárolási tároló szükséges idő.

### <a name="why-am-i-observing-longer12-hours-backup-time"></a>Miért vagyok I betartásával longer(>12 hours) biztonsági mentés ideje?
Biztonsági mentés két fázisból áll: pillanatképek készítése, és a pillanatképek átvitele a tárolóban. A biztonsági mentési szolgáltatás optimalizálja a tároláshoz. A pillanatkép-adatok átviteléhez a tárolóba, a szolgáltatás csak a növekményes változásokat átviszi az előző pillanatképet.  Annak megállapításához, a növekményes módosításokat, a szolgáltatás kiszámítja a blokkok ellenőrzőösszeg. Ha a blokk módosul, a blokk egy blokkot, a tároló küldendő azonosítja. A szolgáltatás csukja majd további mindegyik azonosított blokkok, minimálisra csökkentése érdekében az adatok átvitele lehetőségek keres. Minden megváltozott blokkokat kiértékelése, után a szolgáltatás egyesíti a módosításokat, és elküldi azokat a tárolóban. Néhány örökölt alkalmazás kicsi, töredezett írások optimálisak nem tárolására. A pillanatkép tartalmazza a sok kisméretű, töredezett írási műveleteket, ha a szolgáltatás fordít további időt, az alkalmazások által írt adatok feldolgozására. Az ajánlott alkalmazás írási kitiltás az Azure, a virtuális Gépen belül futó alkalmazások, legalább 8 KB-os van. Ha az alkalmazás egy legfeljebb 8 KB-os blokkméretet, a biztonsági mentés teljesítményét történik. Az alkalmazás biztonsági mentési teljesítményének javítása érdekében hangolása kapcsolatban lásd: [alkalmazások az Azure storage optimális teljesítményének hangolása](../virtual-machines/windows/premium-storage-performance.md). Bár a biztonsági mentés teljesítményét foglalkozó prémium szintű storage példák használ, a útmutatást esetén szabványos tárolólemezek alkalmazható.

## <a name="total-restore-time"></a>Teljes helyreállítás időpontjára
A visszaállítási művelet áll két fő sub feladat: adatok másolása a kiválasztott ügyfél tárfiókja vissza a tárolóból, és a virtuális gép létrehozása. Az adatok másolásának vissza a tárolóból, a biztonsági másolatokat tároló belső az Azure-ban, és az ügyfél tárfiókja tároló függ. Adat másolása szükséges idő függ:
* Várakozási ideje - dolgozza fel az szolgáltatás feladatok visszaállítása több ügyfél egyszerre, mivel visszaállítási kérések használata várólista be.
* Az adatmásolási idő - adatokat az ügyfél tárfiókja másolja a tárolóból. Visszaállítás idő függ az IOPS és átviteli Azure Backup szolgáltatás lekérdezi a kijelölt ügyfél tárfiók. A visszaállítás során a másolási idejének csökkentése érdekében válasszon egy nem betöltött más alkalmazás írások és olvasások tárfiókot.

## <a name="best-practices"></a>Ajánlott eljárások
Javasoljuk, hogy ezeket a gyakorlatokat, virtuális gépek biztonsági mentéseinek konfigurálása közben követően:

* Nem ütemezhető be a 10-nél több klasszikus virtuális gépek biztonsági mentése egyszerre ugyanazon felhőalapú szolgáltatásából. Ha azt szeretné, a biztonsági mentés több virtuális gép ugyanazon a felhőalapú szolgáltatás, a biztonsági mentés kezdési idejének eltolása a egy órával.
* Nem ütemezhető be a 40-nél több virtuális gépek biztonsági mentése egy időben.
* Virtuális gép biztonsági mentések ütemezése során csúcsidőn kívül. Ily módon a biztonsági mentési szolgáltatás IOPS használ az ügyfél tárfiókja adatokat továbbít a tárolóban.
* Győződjön meg arról, hogy a házirend érvényben van-e elosztva a eltérő tárfiókokból virtuális gépeken. Javasoljuk, hogy legfeljebb 20 teljes lemezek egyetlen tárfiókból védi a ugyanazt biztonsági mentés ütemezése. Ha nagyobb, mint 20 lemezek tárfiókokban, a virtuális gépek elosztva több házirendet a szükséges IOPS lekérni a biztonsági mentési folyamat átviteli szakasza során.
* Ne állítson vissza egy virtuális Gépen futó prémium szintű storage ugyanazt a tárfiókot. Ha a visszaállítási művelet folyamat megegyezik a biztonsági mentési művelet, csökkenti a rendelkezésre álló IOPS a biztonsági mentéshez.
* A prémium szintű virtuális gép biztonsági mentése a virtuális gép biztonsági mentési csomagjának V1 javasoljuk, hogy a teljes tárolóhelynek fiók csak 50 %-át osszon ki, hogy az Azure Backup szolgáltatás másolhatja a pillanatkép storage-fiók és átviteli adatokat erről a helyről másolt tárfiókban a tárolóba.
* Győződjön meg arról, hogy a Linux virtuális gépeken python verzió engedélyezett a biztonsági mentés 2.7

## <a name="data-encryption"></a>Adattitkosítás
Azure biztonsági mentés titkosítja az adatokat a biztonsági mentési folyamatának részeként. Azonban titkosítsa az adatokat a virtuális Gépen belül, és a védett adatok biztonsági mentése zökkenőmentesen (tudjon meg többet az [titkosított adatok biztonsági mentését](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>Védett példányok költségének kiszámítása
Azure Backup használatával biztonsági mentése Azure virtuális gépek vannak vetve [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/). Védett példányok kiszámítása alapul a *tényleges* a virtuális gép, amely a virtuális gép – az átmeneti tárolás kivételével az összes adat mérete.

Virtuális gépek biztonsági mentéséről árképzési nem alapul a virtuális géphez csatolt adatok lemezek támogatott maximális méretét. Árképzési a tényleges adatlemeze tárolt adatok alapján. Hasonlóképpen a biztonsági másolatok tárolásának számlázási tárolt adatokat az Azure biztonsági mentési szolgáltatásban, ez az egyes helyreállítási pontok a tényleges adatok összege mennyisége alapul.

Vegyük például, egy szabványos A2 méretű virtuális gépekről, amelyek két adatlemeznek 1 TB maximális mérettel. Az alábbi táblázat áttekintést nyújt az egyes lemezeken tárolt adatok:

| Lemez típusa | Maximális méret | Tényleges adatok |
| --------- | -------- | ----------- |
| Operációsrendszer-lemez |1023 GB |17 GB |
| Helyi lemez / ideiglenes lemez |135 GB |5 GB (biztonsági mentés nem tartalmazza) |
| Adatlemez 1 |1023 GB |30 GB |
| Adatlemez 2 |1023 GB |0 GB |

A tényleges a virtuális gép ebben az esetben mérete 17 GB + 30 GB + 0 GB = 47 GB. A védett példányméretének (47 GB) válik a havi számlán alapját. A virtuális gép adatmennyiség növekedésével védett példányméretének használt számlázási változásainak megfelelően.

Számlázási nem indul el, amíg befejeződik az első sikeres biztonsági mentés. Ezen a ponton a számlázás mind a tároló, mind a védett példányok kezdődik. Számlázási továbbra is fennáll, mindaddig, amíg nincs a virtuális gép tárolóban tárolt biztonsági mentési adatokat. Ha leállítja a virtuális gépen, de a virtuális gép biztonsági mentési adatok létezik-e az adott tárolóban, számlázási továbbra is.

A megadott virtuális gép számlázási leállítja, csak akkor, ha a védelem ki van kapcsolva, és az összes biztonsági mentési adatok törlése. Amikor a védelem leáll, de nincs aktív biztonsági mentési feladatok, a legutóbbi sikeres VM biztonsági mentés mérete a védett példányméretének a havi számlán használt válik.

## <a name="questions"></a>Kérdései vannak?
Ha kérdései vannak, vagy van olyan szolgáltatás, amelyről hallani szeretne, [küldjön visszajelzést](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>További lépések
* [Virtuális gépek biztonsági mentése](backup-azure-arm-vms.md)
* [Kezelheti a virtuális gép biztonsági mentése](backup-azure-manage-vms.md)
* [Virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md)
* [Virtuális gép biztonsági másolatával kapcsolatos problémák elhárítása](backup-azure-vms-troubleshoot.md)
