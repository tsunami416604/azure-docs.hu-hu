---
title: Azure VMware megoldás a CloudSimple-től – Optimalizálja a CloudSimple magánfelhőt az Oracle RAC számára
description: Új fürt üzembe helyezése és virtuális gép optimalizálása az Oracle Real Application Clusters (RAC) telepítéséhez és konfigurálásához
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b945beaa7497e1ad19315bacf1284dd0cbc24d6a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868077"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Optimalizálja a CloudSimple private cloud-ot az Oracle RAC telepítéséhez

Az Oracle Real Application Clusters (RAC) a CloudSimple private cloud környezetben telepíthető. Ez az útmutató ismerteti, hogyan telepíthet egy új fürtöt, és hogyan optimalizálhatja a virtuális gép az Oracle RAC-megoldás. A témakörben ismertetett lépések végrehajtása után telepítheti és konfigurálhatja az Oracle RAC-ot.

## <a name="storage-policy"></a>Tárolási szabályzat

Az Oracle RAC sikeres megvalósításához megfelelő számú csomópontra van szükség a fürtben.  A vSAN-tárolási házirendben a tolerálható hibák (FTT) az adatbázis, a napló és a lemezek újraírásához használt adatlemezekre vonatkoznak.  A hibák hatékony tolerálására szükséges csomópontok száma 2N+1, ahol N a pénzügyi pénzügyi hibafüggvény értéke.

Példa: Ha a kívánt pénzügyi pénzügyi szám 2, akkor a fürtben lévő csomópontok teljes számának 2*2+1 = 5-nek kell lennie.

## <a name="overview-of-deployment"></a>A telepítés áttekintése

Az alábbi szakaszok bemutatják, hogyan állíthatja be a CloudSimple private cloud környezetet az Oracle RAC számára.

1. Gyakorlati tanácsok a lemezkonfigurációhoz
2. CloudSimple privát felhőalapú vSphere-fürt telepítése
3. Hálózatkezelés beállítása oracle RAC-hoz
4. A vSAN tárolási házirendjeinek beállítása
5. Oracle virtuális gépek létrehozása és megosztott virtuálisgép-lemezek létrehozása
6. Virtuálisgép és gazdagép affinitási szabályainak beállítása

## <a name="best-practices-for-disk-configuration"></a>Gyakorlati tanácsok a lemezkonfigurációhoz

Az Oracle RAC virtuális gépek több lemezzel rendelkeznek, amelyek adott funkcióhoz használatosak.  A megosztott lemezek az Oracle RAC Cluster által használt összes virtuális gépen vannak csatlakoztatva.  Az operációs rendszer és a szoftvertelepítő lemezek csak az egyes virtuális gépeken vannak csatlakoztatva.  

![Oracle RAC virtuálisgép-lemezek – áttekintés](media/oracle-vm-disks-overview.png)

A következő példa az alábbi táblázatban megadott lemezeket használja.

| Lemez                                      | Cél                                       | Megosztott lemez |
|-------------------------------------------|-----------------------------------------------|-------------|
| Operációs rendszer                                        | Operációsrendszer-lemez                         | Nem          |
| Rács                                      | Az Oracle Grid szoftver helyének telepítése     | Nem          |
| Adatbázis                                  | Az Oracle adatbázis-kezelő szoftver helyének telepítése | Nem          |
| ORAHAZA                                   | Oracle adatbázis-bináris fájlok alaphelye    | Nem          |
| DATA1, DATA2, DATA3, DATA4                | Az Oracle adatbázisfájlokat tároló lemez   | Igen         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Naplólemezek ismétlése                                | Igen         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Szavazólemezek                                  | Igen         |
| FRA1, FRA2                                | Gyors helyreállítási terület lemezek                      | Igen         |

![Oracle virtuálisgép-lemez konfigurációja](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Virtuális gép konfigurációja

* Minden virtuális gép négy SCSI-vezérlővel van konfigurálva.
* Az SCSI-vezérlő típusa VMware paravirtual.
* Több virtuális lemez (.vmdk) jön létre.
* A lemezek különböző SCSI-vezérlőkre vannak csatlakoztatva.
* A többírós megosztás típusa megosztott fürtlemezekhez van beállítva.
* a vSAN tárolási házirend a lemezek magas rendelkezésre állásának biztosítására van definiálva.

### <a name="operating-system-and-software-disk-configuration"></a>Operációs rendszer és szoftverlemez-konfiguráció

Minden Oracle virtuális gép több lemezzel van konfigurálva a gazdaoperációs rendszerhez, a csere, a szoftvertelepítéshez és az operációs rendszer egyéb funkcióihoz.  Ezek a lemezek nincsenek megosztva a virtuális gépek között.  

* Minden virtuális géphez három lemez van konfigurálva virtuális lemezként, és oracle RAC virtuális gépekre van csatlakoztatva.
    * Operációsrendszer-lemez
    * Lemez az Oracle Grid tárolására fájlokat telepít
    * Lemez oracle adatbázis-telepítési fájlok tárolására
* A lemezek vékony **kiépített ként konfigurálhatók.**
* Minden lemez az első SCSI-vezérlőre (SCSI0) van csatlakoztatva.  
* A megosztás beállítása **Nincs megosztás**.
* A redundancia a vSAN-házirendek használatával van definiálva a tárolón.  

![Oracle RAC adatlemezcsoport konfigurációja](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Adatlemez konfigurációja

Az adatlemezeket elsősorban adatbázisfájlok tárolására használják.  

* Négy lemez virtuális lemezként van konfigurálva, és az összes Oracle RAC virtuális gépen van csatlakoztatva.
* Minden lemez egy másik SCSI-vezérlőre van csatlakoztatva.
* Minden virtuális lemez van beállítva, mint **vastag rendelkezés Eager Zeroed**.  
* A megosztás **többírós.**  
* A lemezeket automatikus tárolókezelés (ASM) lemezcsoportként kell konfigurálni.  
* A redundancia a vSAN-házirendek használatával van definiálva a tárolón.  
* Az ASM redundancia **beállítása Külső** redundancia.

![Oracle RAC adatlemezcsoport konfigurációja](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Naplólemez-konfiguráció újracsinálása

A naplófájlok ismétlése az adatbázisban végrehajtott módosítások másolatának tárolására szolgál.  A naplófájlok akkor használatosak, amikor az adatokat hibák után helyre kell hajtani.

* A naplólemezek ismétlését több lemezcsoportként kell konfigurálni.  
* Az összes Oracle RAC virtuális gépen hat lemez jön létre és van csatlakoztatva.
* A lemezek különböző SCSI-vezérlőkre vannak csatlakoztatva
* Minden virtuális lemez van beállítva, mint **vastag rendelkezés Eager Zeroed**.
* A megosztás **többírós.**  
* A lemezeket két ASM-lemezcsoportként kell konfigurálni.
* Minden ASM-lemezcsoport három lemezt tartalmaz, amelyek különböző SCSI-vezérlőkön találhatók.  
* Az ASM redundancia **normál** redundanciára van állítva.
* Öt ismétlési naplófájl jön létre az ASM Redo naplócsoportban

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Oracle RAC újranaplólemez-csoport konfigurációja](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Oracle szavazólemez konfigurációja

A szavazólemezek további kommunikációs csatornaként biztosítják a kvórumlemez-funkciókat, hogy elkerüljék az osztott agyi helyzetet.

* Öt lemez jön létre és van csatlakoztatva az összes Oracle RAC virtuális gépen.
* A lemezek egy SCSI-vezérlőre vannak csatlakoztatva
* Minden virtuális lemez van beállítva, mint **vastag rendelkezés Eager Zeroed**.
* A megosztás **többírós.**  
* A lemezeket ASM-lemezcsoportként kell konfigurálni.  
* Az ASM redundancia **beállítása magas** redundancia.

![Oracle RAC szavazólemez-csoport konfigurációja](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle gyors helyreállítási terület lemez konfiguráció (nem kötelező)

A gyors helyreállítási terület (FRA) az Oracle ASM lemezcsoport által kezelt fájlrendszer.  Az FRA megosztott tárolási helyet biztosít a biztonsági mentési és helyreállítási fájlok számára. Az Oracle archivált naplókat és flashback naplókat hoz létre a gyors helyreállítási területen. Az Oracle Recovery Manager (RMAN) opcionálisan a biztonsági másolatokat és a képmásolatokat a gyors helyreállítási területen tárolja, és a fájlok visszaállításakor használja a média-helyreállítás során.

* Két lemez jön létre és van csatlakoztatva az összes Oracle RAC virtuális gépen.
* A lemezek különböző SCSI-vezérlőre vannak csatlakoztatva
* Minden virtuális lemez van beállítva, mint **vastag rendelkezés Eager Zeroed**.
* A megosztás **többírós.**  
* A lemezeket ASM-lemezcsoportként kell konfigurálni.  
* Az ASM redundancia **beállítása Külső** redundancia.

![Oracle RAC szavazólemez-csoport konfigurációja](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>CloudSimple private cloud vSphere-fürt telepítése

VSphere-fürt privát felhőn való üzembe helyezéséhez kövesse az alábbi eljárást:

1. A CloudSimple portálon [hozzon létre egy privát felhőt.](create-private-cloud.md) A CloudSimple létrehoz egy "felhőtulajdonos" nevű alapértelmezett vCenter-felhasználót az újonnan létrehozott magánfelhőben. Az alapértelmezett private cloud-felhasználóról és engedélymodellről a [Magánfelhő engedélymodell megismerése című](learn-private-cloud-permissions.md)témakörben olvashat részletesen.  Ez a lépés létrehozza az elsődleges felügyeleti fürtöt a privát felhőhöz.

2. A CloudSimple portálon [bontsa ki a magánfelhőt](expand-private-cloud.md) egy új fürttel.  Ez a fürt lesz használva az Oracle RAC telepítéséhez.  Válassza ki a csomópontok számát a kívánt hibatűrés alapján (legalább három csomópont).

## <a name="set-up-networking-for-oracle-rac"></a>Hálózatkezelés beállítása az Oracle RAC számára

1. A magánfelhőben [hozzon létre két VLAN-t](create-vlan-subnet.md), egyet az Oracle nyilvános hálózatához, egyet pedig az Oracle magánhálózatához, és rendeljen hozzá megfelelő alhálózati CID-ket.
2. A VLAN-ok létrehozása után hozza létre az [elosztott portcsoportokat a Private Cloud vCenter en.](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere)
3. Állítson be egy [DHCP- és DNS-kiszolgálóvirtuális gépet](dns-dhcp-setup.md) a felügyeleti fürtön az Oracle környezetben.
4. [Konfigurálja a DNS-továbbítást a](on-premises-dns-setup.md#create-a-conditional-forwarder) magánfelhőben telepített DNS-kiszolgálón.

## <a name="set-up-vsan-storage-policies"></a>VSAN-tárolási házirendek beállítása

a vSAN-házirendek határozzák meg a virtuális gép lemezeken tárolt adatok tolerálandó és lemezcsíkozási hibákat.  A létrehozott tárolási szabályzatot a virtuális gép lemezein kell alkalmazni a virtuális gép létrehozása közben.

1. Jelentkezzen be a privát felhő [vSphere kliensébe.](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access)
2. A felső menüben válassza a **Házirendek és profilok lehetőséget.**
3. A bal oldali menüben válassza a **Virtuálisgép-tárolási házirendek,** majd **a Virtuálisgép-tárolási szabályzat létrehozása**lehetőséget.
4. Adjon meg egy értelmes nevet a házirendnek, és kattintson a **TOVÁBB**gombra.
5. A **Házirend-struktúra** csoportban válassza a **Szabályok engedélyezése vSAN-tárhelyhez lehetőséget,** és kattintson a **TOVÁBB**gombra.
6. A **vSAN** > **rendelkezésre állása** szakaszban válassza **a Nincs** a hely katasztrófa tűréshatár. Ha a hibákat tolerálja, válassza a **RAID - Tükrözés opciót** a kívánt pénzügyi hibakereskedelmi megállapodáshoz.
    ![vSAN](media/oracle-rac-storage-wizard-vsan.png)beállításokat.
7. A **Speciális szakaszban** válassza ki az objektumonkénti lemezsávok számát. Objektumterület-foglalás esetén válassza a **Vastag kiépített**lehetőséget. Válassza **az Objektum-ellenőrzőösszeg letiltása**lehetőséget. Kattintson **a TOVÁBB gombra.**
8. A képernyőn megjelenő utasításokat követve megtekintheti a kompatibilis vSAN-adattárak listáját, áttekintheti a beállításokat, és befejezve a telepítést.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Oracle virtuális gépek létrehozása és megosztott virtuálisgép-lemezek létrehozása az Oracle számára

Virtuális gép létrehozásához az Oracle számára, klónozzon egy meglévő virtuális gép, vagy hozzon létre egy újat.  Ez a szakasz azt ismerteti, hogyan hozhat létre egy új virtuális gép, majd klónozza, hogy hozzon létre egy második telepítése után az alap operációs rendszer.  A virtuális gépek létrehozása után létrehozhat egy lemezek hozzáadása.  Az Oracle cluster megosztott lemezeket használ a naplók tárolására, adataira, naplóira és ismétlésére.

### <a name="create-vms"></a>Virtuális gépek létrehozása

1. A vCenterben kattintson a **Gazdagépek és fürtök** ikonra. Válassza ki az Oracle számára létrehozott fürtöt.
2. Kattintson a jobb gombbal a fürtre, és válassza **az Új virtuális gép parancsot.**
3. Válassza **az Új virtuális gép létrehozása lehetőséget,** és kattintson a **Tovább**gombra.
4. Nevezze el a gépet, válassza ki az Oracle virtuális gép helyét, majd kattintson a **Tovább**gombra.
5. Jelölje ki a fürterőforrást, és kattintson a **Tovább**gombra.
6. Jelölje ki a fürt vSAN-adattanyáját, és kattintson a **Tovább**gombra.
7. Tartsa meg az alapértelmezett ESXi 6.5 kompatibilitási beállítást, és kattintson a **Tovább**gombra.
8. Válassza ki a létrehozni kívánt virtuális gép ISO-jának vendég operációs rendszerét, és kattintson a **Tovább**gombra.
9. Válassza ki az operációs rendszer telepítéséhez szükséges merevlemez-méretet.
10. Ha az alkalmazást egy másik eszközre szeretné telepíteni, kattintson **az Új eszköz hozzáadása gombra.**
11. Válassza ki a hálózati beállításokat, és rendelje hozzá a nyilvános hálózathoz létrehozott elosztott portcsoportot.
12. További hálózati adapterek hozzáadásához kattintson az **Új eszköz hozzáadása** gombra, és válassza ki a magánhálózathoz létrehozott elosztott portcsoportot.
13. Új DC/DVD-meghajtó esetén jelölje ki azt az adattári ISO-fájlt, amely az elsődleges operációs rendszer telepítéséhez az ISO-t tartalmazza. Jelölje ki az ISO-k és sablonok mappába korábban feltöltött fájlt, és kattintson az **OK**gombra.
14. Tekintse át a beállításokat, és kattintson az **OK** gombra az új virtuális gép létrehozásához.
15. A virtuális gép bekapcsolása. Telepítse az operációs rendszert és a szükséges frissítéseket

Az operációs rendszer telepítése után klónozhat egy második virtuális gép. Kattintson a jobb gombbal a virtuális gép bejegyzésre, és válassza a klónozási lehetőséget.

### <a name="create-shared-disks-for-vms"></a>Megosztott lemezek létrehozása virtuális gépekhez

Az Oracle megosztott lemezzel tárolja az adatokat, naplózza és újracsinálja a naplófájlokat.  Létrehozhat egy megosztott lemezt a vCenteren, és csatlakoztathatja mindkét virtuális géphez.  A nagyobb teljesítmény érdekében helyezze az adatlemezeket a különböző SCSI-vezérlőkre Az alábbi lépéseket, amelyek bemutatják, hogyan hozhat létre megosztott lemezt a vCenteren, majd csatolja azt egy virtuális géphez. VCenter Flash kliens a virtuális gép tulajdonságainak módosítására szolgál.

#### <a name="create-disks-on-the-first-vm"></a>Lemezek létrehozása az első virtuális gépen

1. A vCenterben kattintson a jobb gombbal az egyik Oracle virtuális gépre, és válassza a **Beállítások szerkesztése parancsot.**
2. Az új eszközcsoportban válassza az **SCSI-vezérlő** lehetőséget, és kattintson a **Hozzáadás**gombra.
3. Az új eszköz csoportban válassza az **Új merevlemez** lehetőséget, és kattintson a **Hozzáadás**gombra.
4. Bontsa ki az Új merevlemez tulajdonságait.
5. Adja meg a merevlemez méretét.
6. Adja meg a virtuális gép tárolási szabályzata a korábban definiált vSAN tárolási szabályzat.
7. Válassza ki a helyet, mint egy mappát a vSAN adattárban. A hely segít a böngészésben és a lemezek egy második virtuális géphez való csatlakoztatása.
8. Lemezkiépítés esetén válassza a **Vastag kiépítés buzgó nullázása**lehetőséget.
9. Megosztáshoz adja meg a **Többíró**.
10. A virtuális eszközcsomóponthoz válassza ki a 2.

    ![Lemezek létrehozása az első virtuális gépen](media/oracle-rac-new-hard-disk.png)

Ismételje meg a 2–10.

#### <a name="attach-disks-to-second-vm"></a>Lemezek csatolása a második virtuális géphez

1. A vCenterben kattintson a jobb gombbal az egyik Oracle virtuális gépre, és válassza a **Beállítások szerkesztése parancsot.**
2. Az új eszközcsoportban válassza az **SCSI-vezérlő** lehetőséget, és kattintson a **Hozzáadás**gombra.
3. Az új eszközcsoportban válassza a **Meglévő merevlemez** lehetőséget, és kattintson a **Hozzáadás**gombra.
4. Tallózással keresse meg azt a helyet, ahol a lemezt az első virtuális géphez létrehozták, és válassza ki a VMDK-fájlt.
5. Adja meg a virtuális gép tárolási szabályzata a korábban definiált vSAN tárolási szabályzat.
6. Lemezkiépítés esetén válassza a **Vastag kiépítés buzgó nullázása**lehetőséget.
7. Megosztáshoz adja meg a **Többíró**.
8. A virtuális eszközcsomóponthoz válassza ki a 2.

    ![Lemezek létrehozása az első virtuális gépen](media/oracle-rac-existing-hard-disk.png)

Ismételje meg a 2–7.

## <a name="set-up-vm-host-affinity-rules"></a>Virtuálisgép-gazdagép-affinitási szabályok beállítása

Virtuális gép-állomás affinitási szabályok biztosítják, hogy a virtuális gép fut a kívánt gazdagépen.  A vCenteren szabályokat határozhat meg annak biztosítására, hogy az Oracle virtuális gép megfelelő erőforrásokkal futjon a gazdagépen, és hogy megfeleljen a konkrét licencelési követelményeknek.

1. A CloudSimple portálon a felhőtulajdonos felhasználó [jogosultságainak eszkalálódását.](escalate-private-cloud-privileges.md)
2. Jelentkezzen be a privát felhő [vSphere kliensébe.](https://docs.azure.cloudsimple.com/vsphere-access)
3. A vSphere-ügyfélprogramban válassza ki azt a fürtöt, amelyben az Oracle virtuális gépek et telepítik, és kattintson a **Konfigurálás gombra.**
4. A Konfigurálás csoportban válassza a **VM/Host Groups lehetőséget.**
5. Kattintson a gombra. **+**
6. Vegyen fel egy virtuálisgép-csoportot. Válassza ki **a virtuálisgép-csoportot** típusként. Adja meg a csoport nevét. Jelölje ki a virtuális gépeket, majd kattintson az **OK** gombra a csoport létrehozásához.
6. Gazdagépcsoport hozzáadása. Típusként válassza a **Gazdagépcsoportot.** Adja meg a csoport nevét. Jelölje ki azokat a gazdagépeket, amelyeken a virtuális gépek futni fognak, majd kattintson az **OK** gombra a csoport létrehozásához.
7. Szabály létrehozásához kattintson a **VM/Host szabályokra.**
8. Kattintson a gombra. **+**
9. Adja meg a szabály nevét, és jelölje **be az Engedélyezés jelölőnégyzetet.**
10. A szabálytípushoz válassza a **virtuális gépek állomásként lehetőséget.**
11. Válassza ki az Oracle virtuális gépeket tartalmazó virtuális gépcsoportot.
12. Válassza **a Csoport állomásain futtatnia kell választógombot.**
13. Jelölje ki a létrehozott gazdagépcsoportot.
14. A szabály létrehozásához kattintson az **OK** gombra.

## <a name="references"></a>Referencia

* [A vSAN-házirendekről](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [VMware többírós attribútum megosztott VMDK-khoz](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
