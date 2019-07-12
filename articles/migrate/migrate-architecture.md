---
title: Az Azure Migrate-architektúra |} A Microsoft Docs
description: A cikk áttekintést nyújt az Azure Migrate szolgáltatásról.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 16fbf8d3523ac2ab36447aa51a93a0bb5a9fad54
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811426"
---
# <a name="azure-migrate-architecture-and-processes"></a>Az Azure Migrate architektúrájának és folyamatainak

[Az Azure Migrate](migrate-overview.md) hub eszközöket tartalmazza, amelyek segítséget nyújtanak a felderítése, értékelheti és migrálhatja az alkalmazásokat, az infrastruktúra és a számítási feladatok a Microsoft Azure biztosít. A hub tartalmaz, az Azure Migrate eszközökkel és külső független szoftverszállítók (ISV) szállító ajánlatok. 

 Ez a cikk az Azure Migrate Server Assessment, és az Azure Migrate-kiszolgáló áttelepítése a felmérés és áttelepítés architektúrájának és folyamatainak foglalja össze.

## <a name="assessment-with-azure-migrate-server-assessment"></a>Felmérés az Azure Migrate Server Assessment

Az Azure Migrate Server Assessment felmérheti a VMware virtuális gépek és a Hyper-V virtuális gépek az Azure-ba való migrálásra. Értékelés a következőképpen történik:

1. **Értékelés előkészítése**: A helyszíni, egy egyszerűsített az Azure Migrate készülék a helyszíni VMware virtuális gép vagy a Hyper-V virtuális gép és a berendezés regisztráljon az Azure Migrate beállítása.
2. **Virtuális gépek felderítése**: Az Azure Migrate készülék fut, a helyszíni virtuális gépek felderítéséhez. 
    - Semmit nem kell telepíteni a felderített virtuális gépen.
    - Virtuális gépek metaadatait a magok, memória, lemezek, lemezméretek és hálózati adapterek kapcsolatos információkat tartalmaz.
    - Teljesítményadatok CPU és memória-használat, lemez IOPS, a lemez adatátviteli sebessége (MB/s), és a hálózati kimenetet (MB/s) kapcsolatos információkat tartalmaz
- **Gyűjtse össze és felmérheti a gépek**: Felderítés befejeződése után az Azure-ban gyűjtse össze a portálon a virtuális gépek felderítése, amely jellemzően együtt áttelepíteni kívánt virtuális gépek csoportokba. Az értékelés futtatunk egy csoportot.


## <a name="vmware-assessment"></a>VMware-értékelés 

Az ábra összefoglalja, hogyan működik a VMware virtuális gép használata az Azure Migrate Server Assessment.

![Migrálásfelmérési architektúra VMware](./media/migrate-architecture/sas-architecture-vmware.png)

A folyamat a következőképpen történik:

1. **Az Azure Migrate berendezés üzembe**:

    a. Töltse le a (OVA) sablont az Azure Portalról.

    b. Importálja a vCenter Server-géphez a virtuális gép létrehozásához.

    c. Ez a virtuális gép csatlakozni, konfigurálja az alapbeállításokat, és regisztrálja az Azure Migrate.

2. **Kezdeményezési felderítési**:

    a. Csatlakozzon a gyűjtő alkalmazás fut, a felderítés elindításához berendezésen.

    b. A berendezés folyamatosan metaadatok és a teljesítmény adatokat küld a virtuális gépekről az Azure-bA.

    - A berendezés mindig csatlakozik az Azure Migrate szolgáltatás.
    - Környezet változtatások folyamatos felderítés során lesznek rögzítve. Például a felderítési hatókör hozzáadása a virtuális gépek vagy Virtuálisgép-lemezek vagy a hálózati adapterek hozzáadása.

3. **Felmérheti a gépek**:

    a. Felderítés befejeződése után az Azure portal gyűjtse össze a virtuális gépek felderítése csoportokba.  Egy csoport jellemzően együtt áttelepíteni kívánt virtuális gépek áll.

    b. Az egyes csoportokhoz értékelés futtatása.

4. **Tekintse át az értékelés**: 

    a. Az értékelés befejeződése után megtekintheti a portálon.

    b. További elemzés céljából töltse le az értékelés Excel-formátumban.



### <a name="vmware-ports"></a>VMware-portok
Az Azure Migrate a következő kapcsolat portokat használja a VMware-ről:

**Forrás** | **Target** | **Port** | **Részletek**
--- | --- | --- | ---
Az Azure Migrate-készülék | Azure Migrate szolgáltatás | Cél-TCP 443-as porton | Metaadat-és teljesítményadatokat küld az Azure Migrate.
Az Azure Migrate-készülék | vCenter Server | Cél-TCP 443-as porton | Csatlakozás a vCenter Serverhez tartozó metaadat-és teljesítményadatokat. 443-as alapértelmezett érték, de a vCenter figyeli egy másik porton lehet módosítani. 
RDP-ügyfelet | Az Azure Migrate-készülék | Target-TCP3389 | RDP-kapcsolat eseményindító felfedezni a berendezés.

### <a name="collected-vmware-metadata"></a>VMware metaadatainak összegyűjtése sikerült

A berendezés küld metaadatok és a teljesítménnyel kapcsolatos adatokat a virtuális gépekről az Azure-bA.

**Művelet** | **Részletek**
--- | ---
**Összegyűjtött metaadatok** | vCenter-virtuális gép neve<br/> vCenter-virtuális gép elérési útja (mappa gazdagép/fürt)<br/> IP- és MAC-címek<br/> Operációs rendszer<br/> Magok/lemezek és hálózati adapterek száma<br/> Lemez és a memória méretét.
**Összegyűjtött teljesítményadatok** | CPU/memória használata<br/> Egy lemez adatait (lemez írási/olvasási átviteli; lemez olvasási/írási művelet / másodperc)<br/> Hálózati adapter adatokat (hálózati meg a hálózat).<br/><br/> Teljesítményadatok összegyűjtése folyamatosan, miután a berendezést a vCenter-kiszolgáló csatlakozik. A régebbi adatok nem lesznek gyűjtve.

## <a name="hyper-v-assessment"></a>A Hyper-V-értékelés

Az ábra összefoglalja, hogyan működik a Hyper-V assessment Sign-e az Azure Migrate Server Assessment.

![Hyper-V migrálásfelmérési architektúra](./media/migrate-architecture/sas-architecture-hyper-v.png)

A folyamat a következőképpen történik:

1. **Az Azure Migrate berendezés létrehozása**:

    a. Töltse le a tömörített formátumban a virtuális gép az Azure Portalról.

    b. Egy Hyper-V gazdagépre való importálása azt.

    c. Csatlakozzon a virtuális berendezés. Ez a alapvető beállítások konfigurálása, és regisztrálja az Azure Migrate.

2. **Kezdeményezési felderítési**:

    a. Csatlakozás az Azure Migrate készülék felderítés elindításához. Semmit nem kell telepíteni a felderített virtuális gépen.

    b. A berendezés folyamatosan adatokat küld a virtuális gép metaadatainak és a teljesítmény az Azure Migrate.

    - A berendezés mindig csatlakozik az Azure Migrate szolgáltatás (CIM-munkamenetek használatával).
    - Környezet változtatások folyamatos felderítés során lesznek rögzítve. Például a felderítési hatókör hozzáadása a virtuális gépek vagy Virtuálisgép-lemezek vagy a hálózati adapterek hozzáadása.


3. **Felmérheti a gépek**:

    a. Felderítés befejeződése után az Azure portal gyűjtse össze a virtuális gépek felderítése csoportokba.  Egy csoport jellemzően együtt áttelepíteni kívánt virtuális gépek áll.

    b. Az egyes csoportokhoz értékelés futtatása.

4. **Tekintse át az értékelés**:

    a. Az értékelés befejeződése után megtekintheti a portálon.

    b. További elemzés céljából töltse le az értékelés Excel-formátumban.

### <a name="hyper-v-ports"></a>A Hyper-V portok

Azure Migrate szolgáltatás a következő kapcsolat portokat használja a Hyper-V:

**Forrás** | **Target** | **Port** | **Részletek**
--- | --- | --- | ---
Az Azure Migrate-készülék | Azure Migrate szolgáltatás | Cél-TCP 443-as porton | Metaadat-és teljesítményadatokat küld az Azure Migrate.
Az Azure Migrate-készülék | Hyper-V gazdagép vagy fürt | Target-Default WinRM ports-HTTP:5985; HTTPS:5986 | Metaadat-és teljesítményadatokat az állomás csatlakozni. A kapcsolathoz használt CIM-munkamenethez
RDP-ügyfelet | Az Azure Migrate-készülék | Target-TCP3389 | RDP-kapcsolat eseményindító felfedezni a berendezés.

## <a name="collected-hyper-v-vm-metadata"></a>A Hyper-V virtuális gép metaadatainak összegyűjtése sikerült

A berendezés küld metaadatok és a teljesítménnyel kapcsolatos adatokat a virtuális gépekről az Azure-bA.


**Művelet** | **Részletek**
--- | ---
**Összegyűjtött metaadatok** | a virtuális gép neve<br/> Virtuális gép elérési útja (mappa gazdagép/fürt)<br/> IP- és MAC-címek<br/> Operációs rendszer<br/> Magok/lemezek és hálózati adapterek száma<br/> Memória mérete<br/> Lemez iops-t, a lemez adatátviteli sebessége (MB/s) hálózati kimenetet (MB/s)
**Összegyűjtött teljesítményadatok** |  CPU/memória használata<br/> Egy lemez adatait (lemez írási/olvasási átviteli; lemez olvasási/írási művelet / másodperc)<br/> Hálózati adapter adatokat (hálózati meg a hálózat).<br/><br/> Teljesítményadatok összegyűjtése folyamatosan, miután a készüléket a Hyper-V-gazdagép csatlakozik. A régebbi adatok nem lesznek gyűjtve.




## <a name="migration-with-azure-migrate-server-migration"></a>Áttelepítés az Azure-ral Migrálása kiszolgáló áttelepítése

Az Azure Migrate-áttelepítés használatával, az Azure-bA a következő telepíthet át:

- Helyszíni VMware virtuális gépek
- A helyszíni Hyper-V virtuális gépek
- A helyszíni fizikai kiszolgálók
- AWS Windows Virtuálisgép-példányok
- GCP Windows Virtuálisgép-példányok

Az áttelepítési folyamat kis mértékben különbözik, attól függően, hogy mit migráláshoz.


## <a name="migrate-vmware-vms"></a>VMware virtuális gépek áttelepítése

Az Azure Migrate kiszolgáló áttelepítése két módszert kínál a át a helyszíni VMware virtuális gépek:

- **Ügynök nélküli kivételfigyelés replikációs**: Telepítse át a virtuális gépek anélkül, hogy telepít semmit a őket.
- **Replikációs ügynök-alapú**. Az ügynök telepítése a virtuális gép.

Bár a replikációs ügynök nélküli kivételfigyelés egyszerűbb üzembe helyezési szempontból, számos korlátozás jelenleg rendelkezik. [További](server-migrate-overview.md) ezeket a korlátozásokat.
 

### <a name="agent-based-migration"></a>Az ügynök-alapú áttelepítés

VMware virtuális gépek áttelepítése az ügynök-alapú szükséges összetevőket kell telepíteni, számos, a táblázat foglalja össze.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Konfigurációs kiszolgáló gép** | Egy helyszíni VMware virtuális gép a letöltött OVF-sablon üzembe helyezett.<br/><br/> A gép fut, a helyszíni Site Recovery minden összetevőjét, többek között a konfigurációs kiszolgáló vagy folyamatkiszolgáló. | **Konfigurációs kiszolgáló**: A helyszíni és Azure közötti kommunikáció koordinálását, valamint felügyeli az adatreplikációt.<br/><br/> **Folyamatkiszolgáló**: Alapértelmezés szerint telepítve van a konfigurációs kiszolgálón. Ez fogadja a replikált adatokat; gyorsítótárazás, tömörítés és titkosítással optimalizálja őket és elküldi azt az Azure-bA. A folyamatkiszolgáló is telepíti az Azure Site Recovery mobilitási szolgáltatást a virtuális gépekre, és elvégzi a helyszíni gépek automatikus felderítését.
**Mobilitási szolgáltatás** | A mobilitási szolgáltatás minden replikált VMware virtuális gép telepítve kell lennie. | Azt javasoljuk, hogy lehetővé tegye az automatikus telepítési adatok a folyamatkiszolgálótól. Azt is megteheti a szolgáltatás manuális telepítése vagy egy automatikus központi telepítési módszer, például a System Center Configuration Managerrel használja.





### <a name="agent-based-replication-process"></a>Az ügynök-alapú replikációs folyamat

![Replikációs folyamat](./media/migrate-architecture/v2a-architecture-henry.png)

1. Amikor engedélyezi egy virtuális gép replikációját, megkezdődik a kezdeti replikálás az Azure-bA. 
    - A replikálás blokkszintű, közel-folyamatos, a virtuális gépen futó mobilitási szolgáltatás használatával történik.
    - Replikációs házirend-beállítások érvényesek:
        - **Helyreállítási Időkorlát küszöbértéke**. Ez a beállítás nem befolyásolja a replikáció. Ez segít a megfigyeléshez. Egy esemény jelenik meg, és szükség esetén e-mailt küldeni, ha az aktuális helyreállítási Időkorlát meghaladja a küszöbértéket, amely azt adja meg.
        - **Helyreállítási pont megőrzése**. Ezzel a beállítással illesztésnek vissza szeretne lépni egy bekövetkező szolgáltatáskimaradás esetén időben. A premium storage maximális megőrzési ideje 24 órán keresztül. Standard szintű tárolóban működő érték 72 óra. 
        - **Alkalmazáskonzisztens pillanatképek**. Alkalmazáskonzisztens pillanatkép is kell minden 1 12 órát vehet attól függően, az alkalmazás igényeinek megfelelően. Standard szintű Azure blob-pillanatfelvételek a pillanatképeket. A mobilitási ügynök, a virtuális gépeken futó kérelmek megfelelően ezt a beállítást, és a könyvjelzők, amelyek a replikáció Stream-időponthoz egy konzisztens alkalmazás VSS-pillanatkép.

2. Forgalom replikálja az Azure storage nyilvános végpontokat az interneten keresztül.

    - Másik lehetőségként használhatja az Azure ExpressRoute [Microsoft társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#microsoftpeering).
    - Forgalom a helyek közötti virtuális magánhálózati (VPN) keresztül egy helyszíni hely Azure-ba történő nem támogatott.
3. Kezdeti replikálás befejezése után kezdődik replikációja az Azure-bA. A gépek nyomon követett módosításait a folyamatkiszolgálónak érkeznek.
2. Kommunikáció a következőképpen történik:

    - Virtuális gépek a helyszíni konfigurációs kiszolgálóval HTTPS a 443-as porton bejövő kommunikációt, a replikáció kezelését.
    - A konfigurációs kiszolgáló koordinálja a replikációt az Azure-ral HTTPS 443-as kimenő porton keresztül.
    - Virtuális gépek a replikációs adatokat a folyamatkiszolgálónak (a konfigurációs kiszolgáló gépen futó) HTTPS 9443-as porton bejövő küldése. Ez a port módosítható.
    - A folyamatkiszolgáló fogadja a replikált adatokat, optimalizálja a és titkosítja azokat, és elküldi azt az Azure storage 443-as porton keresztüli kimenő.
5. A replikációs adatok első föld bejelentkezik egy gyorsítótárfiókot, az Azure-ban. Ezek a naplófájlok feldolgozásának és az adatokat az Azure-ban tárolt felügyelt lemez (az Azure Site Recovery kezdőérték lemez). A helyreállítási pontok ezen a lemezen jönnek létre.







## <a name="next-steps"></a>További lépések

- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
- A Közösségtől segítségre van szüksége, látogasson el a [Azure Migrate MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?forum=AzureMigrate&filter=alltypes&sort=lastpostdesc) vagy [Stack Overflow](https://stackoverflow.com/search?q=azure+migrate).

