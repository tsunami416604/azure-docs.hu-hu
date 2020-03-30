---
title: Biztonsági mentési útmutató az SAP HANA-hoz az Azure virtuális gépeken | Microsoft dokumentumok
description: Az SAP HANA biztonsági mentési útmutatója két fő biztonsági mentési lehetőséget biztosít az SAP HANA számára az Azure virtuális gépeken
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: bb32350597059209e5baf01d53b0c59fdc2344f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255237"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Biztonsági mentési útmutató az SAP HANA-hoz az Azure virtuális gépeken

## <a name="getting-started"></a>Első lépések

Az Azure virtuális gépeken futó SAP HANA biztonsági mentési útmutatója csak az Azure-specifikus témaköröket ismerteti. Az általános SAP HANA biztonsági mentéskapcsolódó elemek, ellenőrizze az SAP HANA dokumentációt. Elvárjuk, hogy ismerje az adatbázis biztonsági mentési stratégiáit, az okokat és a motivációkat, hogy megbízható és érvényes biztonsági mentési stratégiával rendelkezik, és tisztában legyen a vállalat biztonsági mentési eljárással, a biztonsági mentési időszakmegőrzésével és a visszaállítással kapcsolatos követelményeivel Eljárás.

SAP HANA hivatalosan támogatott különböző Azure virtuális gép típusok, például az Azure M-sorozat. Az SAP HANA-tanúsítvánnyal rendelkező Azure-beli virtuális gépek és a HANA nagypéldány-egységek teljes listáját a [Certified IaaS-platformok keresése](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)webhelyen találja. A Microsoft Azure számos olyan egységet kínál, ahol az SAP HANA nem virtualizált fizikai kiszolgálókon fut. Ezt a szolgáltatást [HANA nagy példánynak nevezik.](hana-overview-architecture.md) Ez az útmutató nem terjed ki a hana nagy példányok biztonsági mentési folyamataira és eszközeire. De az Azure virtuális gépekre korlátozódik. A HANA nagy példányokkal rendelkező biztonsági mentési/visszaállítási folyamatokkal kapcsolatos részletekért olvassa el a [HLI biztonsági mentésés visszaállítás című cikket.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)

A cikk középpontjában az SAP HANA három biztonsági mentési lehetősége áll az Azure virtuális gépeken:

- HANA biztonsági mentés az [Azure biztonsági mentési szolgáltatásokon](https://docs.microsoft.com/azure/backup/backup-overview) keresztül 
- HANA biztonsági mentés a fájlrendszerbe egy Azure Linux virtuális gépen (lásd [SAP HANA Azure Backup fájlszinten](sap-hana-backup-file-level.md))
- HANA biztonsági mentés az Azure storage blob pillanatkép-pillanatkép funkciójával manuálisan vagy az Azure Backup szolgáltatáshasználatával készített tárolási pillanatképek alapján


Az SAP HANA egy biztonsági mentési API-t kínál, amely lehetővé teszi a külső biztonsági mentési eszközök közvetlen ülepcével való integrációt. Termékek, például az Azure Backup szolgáltatás, vagy [a Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) használja ezt a saját felületet sap HANA adatbázis indításához vagy a napló biztonsági mentések újbóli. 


Arról, hogy miként találhatja meg, hogy milyen SAP-szoftvereket támogat az Azure-ban, a [Milyen SAP-szoftverek támogatottak az Azure-telepítésekhez.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)

## <a name="azure-backup-service"></a>Azure biztonsági mentési szolgáltatás

Az első forgatókönyv látható egy olyan forgatókönyv, amelyben az `backint` Azure Backup Service vagy az SAP HANA-felület használatával egy streamelési biztonsági mentést hajt végre egy SAP HANA-adatbázisból. Vagy az Azure Backup szolgáltatás általánosabb képességét használja egy alkalmazáskonzisztens lemezpillanatkép létrehozásához, és ezt az Azure Backup szolgáltatásba való átviteléhez.

Az Azure Backup integrálja és rendelkezik az SAP HANA biztonsági mentési megoldásaként a saját SAP HANA felület, a [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)használatával. A megoldás, a képességek és az Azure-régiók, ahol elérhető, olvassa el a cikk [támogatás mátrix az SAP HANA-adatbázisok az Azure virtuális gépeken.](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support) A HANA-hoz az Azure Backup szolgáltatással kapcsolatos részletekért és alapelvekért olvassa el az [SAP HANA-adatbázis biztonsági mentéséről az Azure-beli virtuális gépekben](https://docs.microsoft.com/azure/backup/sap-hana-db-about)című cikket. 

A második lehetőség az Azure Backup szolgáltatás kihasználására, hogy hozzon létre egy alkalmazás konzisztens biztonsági mentés segítségével lemez pillanatképek az Azure Premium Storage. Más HANA-tanúsítvánnyal rendelkező Azure-tárolók, például [az Azure Ultra-lemez](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd) és az [Azure NetApp-fájlok](https://azure.microsoft.com/services/netapp/) nem támogatják ezt a fajta pillanatképet az Azure Backup szolgáltatáson keresztül. Olvassa el ezeket a cikkeket:

- [Virtuális gép biztonsági infrastruktúrájának megtervezése az Azure-ban](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Az Azure Linux virtuális gépek alkalmazáskonzisztens biztonsági mentése](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) 

ez a tevékenységsorozat alakul ki:

- Az Azure Backup-nak végre kell hajtania egy előre pillanatkép-parancsfájlt, amely az alkalmazást, ebben az esetben az SAP HANA-t konzisztens állapotba helyezi
- Akonzisztens állapot visszaigazolta, az Azure Backup végrehajtja a lemezpillanatképeket
- A pillanatképek befejezése után az Azure Backup visszavonja a pillanatkép előtti parancsfájlban végzett tevékenységet.
- Sikeres végrehajtás után az Azure Backup továbbítja az adatokat a Biztonsági másolat tárolójába

SAP HANA esetén a legtöbb ügyfél az Azure Write Accelerator az SAP HANA-újranaplót tartalmazó kötetek használatával. Az Azure Backup szolgáltatás automatikusan kizárja ezeket a köteteket a pillanatképekből. Ez a kizárás nem károsítja a HANA helyreállítására való képességét. Bár ez blokkolja a képességét, hogy visszaállítsa szinte az összes többi SAP támogatott DBMS.

A hátránya ennek a lehetőségnek az a tény, hogy ki kell fejlesztenie a saját pre- és post-snapshot script. Az előzetes pillanatkép-parancsfájl nak létre kell hoznia egy HANA pillanatképet, és kezelnie kell a végső kivételeseteket. Mivel a pillanatkép utáni parancsfájlnak újra törölnie kell a HANA pillanatképet. A szükséges logikával kapcsolatos további részletekért kezdje az [SAP támogatási megjegyzésével #2039883.](https://launchpad.support.sap.com/#/notes/2039883) Az "SAP HANA-adatok konzisztenciájának a tárolási pillanatképek készítéseke" című szakasz ebben a cikkben a szempontok teljes mértékben vonatkoznak az ilyen típusú biztonsági mentésre.

> [!NOTE]
> Lemezpillanatkép alapú biztonsági mentések az SAP HANA a központi telepítések, ahol több adatbázis-tárolók használata, szükség van egy minimális kiadása HANA 2.0 SP04
> 

A dokumentum későbbi részében a tárolási pillanatképek részleteiről olvashat.

![Ez az ábra két lehetőséget mutat be az aktuális virtuális gép állapotának mentésére](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Egyéb HANA biztonsági mentési módszerek
Három másik biztonsági mentési módszer vagy elérési út is megfontolható:

- Biztonsági mentés az Azure NetApp Files (ANF) alapú NFS-megosztással szemben. Az ANF ismét képes pillanatképeket létrehozni azokról a kötetekről, amelyeken biztonsági másolatokat tárol. Mivel az átviteli, hogy végül szükséges a biztonsági mentések írásához, ez a megoldás válhat egy drága módszer. Bár könnyű létrehozni, mivel hana írhat a biztonsági mentések közvetlenül az Azure natív NFS-megosztás
- A HANA biztonsági mentés végrehajtása a standard SSD vagy az Azure Premium Storage virtuális géphez csatlakoztatott lemezein. Következő lépésként ezeket a biztonsági másolat fájlokat az Azure Blob storage.As next step you can copy those backup files against Azure Blob storage. Ez a stratégia lehet ár bölcs vonzó
- A HANA biztonsági mentés végrehajtása a standard SSD vagy az Azure Premium Storage virtuális géphez csatlakoztatott lemezein. Következő lépésként a lemez rendszeresen pillanatképet kap. Az első pillanatkép után növekményes pillanatképek használhatók a költségek csökkentésére

![Ez az ábra az SAP HANA fájl biztonsági mentésének lehetőségeit mutatja be a virtuális gépen belül](media/sap-hana-backup-guide/other-hana-backup-paths.png)

Ez az ábra az SAP HANA-fájl biztonsági mentésének a virtuális gépen belüli készítésének lehetőségeit mutatja be, majd a HANA biztonsági másolat fájljait más holmással tárolja. Azonban minden olyan megoldás, amely nem egy harmadik fél biztonsági mentési szolgáltatás vagy az Azure Backup szolgáltatás számos közös akadályokat. Néhány ezek közül szerepelhet, például megőrzési felügyelet, automatikus visszaállítási folyamat, és automatikus időponthoz elegendő időbeli helyreállítást biztosít az Azure Backup szolgáltatásként vagy más speciális, harmadik féltől származó biztonsági mentési csomagok és szolgáltatások. A külső szolgáltatások közül sok képes az Azure-on való futtatásra. 


## <a name="sap-resources-for-hana-backup"></a>SAP-erőforrások hana biztonsági mentéshez

### <a name="sap-hana-backup-documentation"></a>SAP HANA biztonsági mentési dokumentáció

- [Bevezetés az SAP HANA felügyeletbe](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [A biztonsági mentési és helyreállítási stratégia megtervezése](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Hana backup ütemezése az ABAP DBACOCKPIT használatával](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Adatbiztonsági mentések ütemezése (SAP HANA cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Gyakori kérdések az SAP HANA biztonsági mentésével kapcsolatban az [SAP Note 1642148-ban](https://launchpad.support.sap.com/#/notes/1642148)
- Gyakori kérdések az SAP HANA adatbázisáról és a tárolási pillanatképekről az [SAP Note 2039883-ban](https://launchpad.support.sap.com/#/notes/2039883)
- Nem megfelelő hálózati fájlrendszerek biztonsági mentéshez és helyreállításhoz az [SAP Note 1820529-ben](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Az SAP HANA biztonsági mentés helyességének ellenőrzése
A biztonsági mentési módszertől függetlenül a teszt-visszaállítás egy másik rendszeren való futtatása feltétlenül szükséges. Ez a megközelítés lehetővé teszi, hogy a biztonsági mentés helyes, és a belső folyamatok biztonsági mentési és visszaállítási munka a várt módon. A biztonsági mentések visszaállítása az infrastruktúra-szükséglet miatt akadályt jelenthet a helyszíni környezetben, így sokkal könnyebb a felhőben végrehajtani a szükséges erőforrások ideiglenes biztosításával erre a célra. Helyes, hogy vannak olyan eszközök hana, amely ellenőrizheti a biztonsági mentési fájlokat a visszaállítható képesség. A gyakori visszaállítási gyakorlatok célja azonban az adatbázis-visszaállítás folyamatának tesztelése és a folyamat betanítása az operatív személyzettel.

Ne feledje, hogy egy egyszerű visszaállítás és ellenőrzése, ha hana van és fut nem elegendő. A tábla konzisztencia-ellenőrzését meg kell adnia, hogy a visszaállított adatbázis rendben van-e. Az SAP HANA az [SAP Note 1977584-ben](https://launchpad.support.sap.com/#/notes/1977584)leírt konzisztencia-ellenőrzések széles körű.

A tábla konzisztencia-ellenőrzésével kapcsolatos információk az SAP table [és katalóguskonzisztencia-ellenőrzés](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)webhelyén is megtalálhatók.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>A HANA biztonsági mentés előnyei és hátrányai a tárolási pillanatképhez képest

Az SAP nem&#39;részesíti előnyben a HANA biztonsági mentést és a tárolási pillanatképet. Felsorolja az előnyeikat és hátrányaikat, így meghatározható, hogy melyiket használjuk a helyzettől és a rendelkezésre álló tárolási technológiától függően (lásd [A biztonsági mentés és helyreállítás tervezésének stratégiáját).](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)

Az Azure-ban vegye figyelembe azt a tényt, hogy az Azure blob pillanatkép-szolgáltatás nem&#39;biztosít fájlrendszer-konzisztenciát több lemez között (lásd: [Blob-pillanatképek használata a PowerShell használatával).](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) 

Emellett meg kell értenie a számlázási következményeket, ha gyakran dolgozik blob pillanatképek a jelen cikkben leírtak szerint: [A pillanatképek elhatárolása díjak megértése](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)– ez&#39;olyan nyilvánvaló, mint az Azure virtuális lemezek használata.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA-adatkonzisztencia a tárolási pillanatképek készítésekekén

Akorábban dokumentált, az Azure Backup pillanatkép-biztonsági mentési képességeinek leírása, a fájlrendszer és az alkalmazás konzisztenciája kötelező a tárolási pillanatképek készítésekekekénések során. A problémák elkerülésének legegyszerűbb módja az SAP HANA leállítása, vagy talán még az egész virtuális gép. Valami, ami nem valósítható meg egy éles példány.

> [!NOTE]
> Lemezpillanatkép alapú biztonsági mentések az SAP HANA a központi telepítések, ahol több adatbázis-tárolók használata, szükség van egy minimális kiadása HANA 2.0 SP04
> 

Az Azure storage nem biztosít fájlrendszer-konzisztenciát több lemez vagy kötetek, amelyek a pillanatkép-folyamat során egy virtuális géphez csatolt. Ez azt jelenti, hogy az alkalmazás konzisztenciáját a pillanatkép során az alkalmazásnak kell kézbesítenie, ebben az esetben maga az SAP HANA. [Az SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) fontos információkat tartalmaz az SAP HANA biztonsági mentéseiről a tárolási pillanatképek alapján. Például az XFS fájlrendszerekkel az **xfs\_fagyasztás futtatása** szükséges a tárolási pillanatkép elindítása előtt, hogy az alkalmazás konzisztenciáját biztosítsa (lásd [xfs\_freeze(8) - Linux man page](https://linux.die.net/man/8/xfs_freeze) az **xfs\_freeze**részleteiért ).

Feltéve, hogy négy Azure virtuális lemezre kiterjedő XFS-fájlrendszer található, a következő lépések konzisztens pillanatképet biztosítanak, amely a HANA-adatterületet jelöli:

1. HANA-adatok pillanatfelvételének előkészítése
1. Fagyassza le az összes lemez/kötet fájlrendszerét (például használja az **xfs\_freeze-t)**
1. Az összes szükséges blobpillanatlétrehozása az Azure-ban
1. A fájlrendszer rögzítésének feloldása
1. Erősítse meg a HANA-adatok pillanatképét (törli a pillanatképet)

Ha az Azure Backup azon képességét, hogy alkalmazás konzisztens pillanatkép biztonsági mentések, lépéseket kell #1 kell kódolni/parancsfájlt az előzetes pillanatkép-parancsfájl. Az Azure Backup szolgáltatás végrehajtja a #2 és #3 lépéseket. A #4 és #5 lépéseit újra meg kell adnia a kódnak a pillanatkép utáni parancsfájlban. Ha nem azure-biztonsági mentési szolgáltatást használ, akkor is meg kell kód/script lépés #2 és #3 a saját.
A HANA-adatok pillanatképeinek létrehozásáról a következő cikkekben olvashat bővebben:

- [HANA-adatok pillanatképei] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- További részletek a #1 lépés végrehajtásához megtalálható a [cikkben Hozzon létre egy adatpillanatkép (Native SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 
- Részletek a HANA-adatok pillanatképeinek megerősítéséhez/törléséhez szükség ként a #5 lépésben található a [Data Snapshot létrehozása (natív SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) című cikkben található. 

Fontos, hogy erősítse meg a HANA pillanatkép. A Copy-on-Write miatt&quot; előfordulhat, hogy az &quot;SAP HANA nem igényel további lemezterületet ebben a pillanatkép-előkészítési módban. azt&#39;, hogy az sap HANA pillanatkép megerősítést nem nyer, nem lehet új biztonsági mentéseket indítani.


### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA biztonsági mentésütemezési stratégia

Az SAP HANA-cikk [a biztonsági mentési és helyreállítási stratégia tervezése](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) egy alaptervet a biztonsági mentések. Támaszkodhat az SAP dokumentációkörül HANA és a más DBMS-szel kapcsolatos tapasztalatok az SAP HANA biztonsági mentési/visszaállítási stratégiájának és folyamatának meghatározásában. A különböző típusú biztonsági mentések sorrendje és a megőrzési időszak nagymértékben függ a megkell adnia az SL-ek.


### <a name="sap-hana-backup-encryption"></a>SAP HANA biztonsági másolat titkosítása

Az SAP HANA az adatok és a napló titkosítását kínálja. Ha az SAP HANA-adatok és -napló nincsenek titkosítva, akkor a biztonsági mentések alapértelmezés szerint nem lesznek titkosítva. Az SAP HANA azonban külön biztonsági mentési titkosítást kínál az [SAP HANA biztonsági mentési titkosításában](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html)dokumentált módon. Ha az SAP HANA régebbi kiadásait futtatja, előfordulhat, hogy ellenőriznie kell, hogy a biztonsági mentéstitkosítás már része volt-e a már biztosított funkcióknak.  


## <a name="next-steps"></a>További lépések
* [SAP HANA Azure Backup fájlszinten](sap-hana-backup-file-level.md) ismerteti a fájlalapú biztonsági mentési lehetőséget.
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure-ban (nagy példányok), tekintse meg az [SAP HANA (nagy példányok) magas rendelkezésre állású és vész-helyreállítási az Azure-ban.](hana-overview-high-availability-disaster-recovery.md)
