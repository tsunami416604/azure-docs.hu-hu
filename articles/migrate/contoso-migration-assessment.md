---
title: Contoso áttelepítése az Azure-bA helyszíni munkaterhelések értékeléséhez |} Microsoft Docs
description: Ismerje meg, hogyan Contoso értékelésére azok a helyszíni gépeket az Azure-bA az Azure-áttelepítést és az adatbázis működő áttelepítéshez
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: 8568668032a97e574a85758080818311839a9caa
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301133"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Contoso áttelepítése: a helyszíni munkaterhelések Azure áttelepítés ellenőrzéséhez

Ez a cikk bemutatja, hogyan Contoso értékelésére a helyszíni SmartHotel alkalmazást, az Azure-bA az áttelepítés előkészítése során.

Ez a dokumentum a dokumentum hogyan fiktív cég Contoso áttelepíti a Microsoft Azure felhőbe a helyszíni erőforrások cikkek sorozat harmadik. Az adatsorozat háttér-információkat tartalmaz, és bemutatják, hogyan állíthat be egy áttelepítési infrastruktúra központi telepítési forgatókönyvek több felmérheti a helyszíni erőforrások az áttelepítéshez megfelelőségét, futtassa áttelepítések különböző típusú. Forgatókönyvek nő összetettségét, és a következőkben hozzáadott további cikkek adott idő alatt.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
A következő cikket: 1: áttekintés | Contoso-áttelepítési stratégia, a cikk adatsorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
2. cikk: Az Azure-infrastruktúra telepítése | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Ugyanabban az infrastruktúrában található összes Contoso áttelepítési forgatókönyvek szolgál. | Elérhető
3. cikk: Felmérheti a helyszíni erőforrások (Ez a cikk) | Bemutatja, hogyan Contoso fut-e a helyszíni kétrétegű SmartHotel alkalmazásuk VMware futó értékelését. Ezek értékeléséhez app virtuális gépek a [Azure áttelepítése](migrate-overview.md) szolgáltatás, és az alkalmazás SQL Server-adatbázis a [Azure adatbázis áttelepítési Segéd](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
4. cikk: Azonosítóterületen (növekedési-és-shift) Azure virtuális gépek és a kezelt SQL-példány | Bemutatja, hogyan Contoso áttelepíti az Azure-bA a SmartHotel alkalmazást. Áttelepítés után az alkalmazás előtér VM használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás adatbázis használata a [Azure-adatbázis áttelepítése](https://docs.microsoft.com/azure/dms/dms-overview) szolgáltatás kezelt SQL-példány át. | Elérhető
5. cikk: Azonosítóterületen (növekedési-és-shift) az Azure virtuális gépen | Bemutatja, hogyan Contoso át SmartHotel alkalmazásuk csak a Site Recovery segítségével virtuális gépeket.
Cikk 6: Azonosítóterületen (növekedési-és-shift) és az Azure virtuális gépeken futó SQL Server rendelkezésre állási csoportok | Bemutatja, hogyan Contoso áttelepíti a SmartHotel alkalmazást. Azok a Site Recovery segítségével telepíti át az alkalmazás virtuális gépek és az adatbázis áttelepítési szolgáltatás áttelepítése az app-adatbázis egy SQL Server rendelkezésre állási csoporthoz. | Elérhető
7. cikk: Azonosítóterületen (növekedési-és-shift) Azure virtuális gépek és az Azure-beli MySQL kiszolgáló | Bemutatja, hogyan Contoso áttelepíti a SmartHotel app virtuális gépek áttelepítéséhez a Site Recovery és MySQL munkaterület használatával (biztonsági mentése és visszaállítása) Azure-beli MySQL Server-példány. | Elérhető

Ha szeretné használni a mintaalkalmazást a cikk ezt használja, való nyílt forráskódú, és letöltheti a [github](https://github.com/Microsoft/SmartHotel360).

## <a name="overview"></a>Áttekintés

Szerint áttelepítése az Azure-ba, a Contoso vállalat szeretné futtatni a műszaki és pénzügyi értékelés mérje fel, hogy megfelelő-e a felhőre való áttérést-e a helyszíni számítási feladatait. A Contoso team különösen áttelepítési gép és az adatbázis kompatibilitási felmérése, és a kapacitás és az Azure-beli erőforrásaik költségei szeretné.

A láb Nyugat-európai és jobb megértése érdekében az érintett, akkor fog értékeléséhez két, a helyszíni alkalmazások technológiák a következő táblázat tartalmazza. Vegye figyelembe, hogy azok még felmérése áttelepítési forgatókönyvekről, hogy az áttelepítéshez áthelyezési és azonosítóterületen alkalmazások. További tudnivalók áthelyezését, és a újrabontása a [Contoso áttelepítése – áttekintés](contoso-migration-overview.md).

**Alkalmazás neve** | **Platform** | **Alkalmazás rétegek** | **Részletek**
--- | --- | --- | ---
SmartHotel<br/><br/> Kezeli a Contoso utazás követelmények | Windows fut az SQL Server-adatbázis | Egy virtuális gép (WEBVM), és az SQL Server fut, a másik virtuális gép (SQLVM) rendszeren futó előtér ASP.NET-webhely alkalmazás kétféle | Virtuális gépek a VMware vCenter-kiszolgáló által kezelt ESXi-állomáson futó.<br/><br/> A mintaalkalmazás letölthető [github](https://github.com/Microsoft/SmartHotel360).
OSTicket<br/><br/> Contoso szolgáltatás ügyfélszolgálati alkalmazás | Futó Linux/Apache, a egy MySQL PHP (LÁMPA). | Egy virtuális gép (OSTICKETWEB), és a MySQL-adatbázis, a másik virtuális gép (OSTICKETMYSQL) futó előtér PHP-webhely alkalmazás kétféle | Az app service alkalmazások vevői által nyomon követésére szolgál problémák belső az alkalmazottak és a külső ügyfeleket.<br/><br/> A mintaalkalmazás letölthető [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Aktuális architektúrája


Itt található ábra: az aktuális Contoso helyszíni infrastruktúrát.

![Contoso-architektúra](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso található az a New York Város keleti Amerikai Egyesült Államokban lévő egyik fő datacenter rendelkezik.
- Három további helyi ágak keresztül az Amerikai Egyesült Államokban rendelkeznek.
- A fő adatközpontok csatlakozik az internethez a részszálas metro ethernet-kapcsolat (500 MB/s).
- Minden fiókiroda helyi csatlakozik az internethez, üzleti osztály kapcsolatok használata IPSec VPN-alagutat a fő adatközpontok vissza a. Ez lehetővé teszi, hogy a teljes hálózat véglegesen csatlakoztatja, és optimalizálja a internetkapcsolat.
- A fő adatközpontok teljesen virtualizált a VMware. Két ESXi 6.5 virtualizációs gazdagépeket, kezeli a vCenter Server 6.5 rendelkeznek.
- Contoso az Active Directory, az Identitáskezelés és a DNS-kiszolgálók a belső hálózaton.
- A tartományvezérlők, az adatközpontban, a VMware virtuális gépek futtatásához. A helyi ágak: tartományvezérlő fizikai kiszolgálókon.





## <a name="business-drivers"></a>A stratégiai célok

Az informatikai vezetőségi tagja szorosan együttműködik az üzleti partnerek megérteni az üzleti szeretné ezt az áttelepítést a elérése:

- **Üzleti növekedési cím**: Contoso egyre, és ennek eredményeképpen nincs nyomás a helyszíni rendszer és a infrastruktúra.
- **Hatékonyabbá teheti**: Contoso kell távolítsa el a felesleges eljárásokat, és a folyamatok egyszerűsíthető a fejlesztők és a felhasználók.  Üzleti igények informatikai fogja használni, és nem hulladék idő vagy pénz, így gyorsabban továbbítása az ügyfelek igényei.
- **Növeli az agilitást**: Contoso informatikai kell lennie a gyorsabb, az üzleti igényeihez. Gyorsabb, mint a piactéren, a sikeres versenyképes engedélyezéséhez módosításokat reagálni képesnek kell lennie.  Ez nem útban, vagy egy üzleti blokkoló válnak.
- **Skála**: növekedésével az üzleti sikeresen, a Contoso informatikai biztosítania kell a korábbi rendszerek esetén képes ütemben nő.

## <a name="assessment-goals"></a>A célok értékelése

A Contoso felhő csapat az áttelepítési értékelések célokat le van rögzítve:

- Az áttelepítés után alkalmazások az Azure-ban kell azonos teljesítménybeli képességeinek mint jelenleg az helyszíni VMWare környezetben.  A felhőre történő nem jelenti azt, hogy alkalmazás teljesítménye kevésbé fontos.
- Contoso kell tudni, hogy kompatibilis az alkalmazásaikat és az adatbázisok Azure-követelményeknek, valamint azok üzemeltetési beállítások az Azure-ban.
- Contoso-adatbázis felügyeleti alkalmazások áthelyezte a felhőbe után kell tartani.  
- Contoso szeretné tudni, nem csak az áttelepítési lehetőségek, de a felhőben történő áthelyezés után az infrastruktúra kapcsolódó költségeket is.

## <a name="assessment-tools"></a>Felmérőeszközök
Contoso Microsoft eszközök értékeléséhez használ. Ezek az eszközök megfelel-e a cél és kell biztosítania az összes szükséges információt.

**Technológia** | **Leírás** | **Költségek**
--- | --- | ---
[Adatbázis áttelepítési Segéd (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Azok DMA mérheti fel, és a kompatibilitási problémák, amelyek hatással lehetnek az Azure-adatbázis működés észlelni fogja használni. DMA értékelésére szolgáltatásparitást SQL források és célok között, és azt javasolja, hogy a teljesítmény és megbízhatóság fejlesztései. | Ez egy ingyenesen letölthető eszköz.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso Ez a szolgáltatás segítségével felmérheti a VMware virtuális gépek. Felméri a gépek migrálásra való alkalmasságát, és méretezési, illetve költségbecsléseket ad az Azure-ban való futtatásra vonatkozóan.  | Nincs jelenleg (lehet, hogy 2018) díjmentes használja ezt a szolgáltatást.
[Szolgáltatástérkép](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Az Azure Migrate a Service Map használatával jeleníti meg a függőségeket a migrálni kívánt gépek között. |  A Service Map az Azure Log Analytics részét képezi. Jelenleg 180 napig díjmentesen használható.

Ebben a forgatókönyvben a Contoso tölti le, és a DMA utazás alkalmazásuk a helyszíni SQL Server-adatbázis felmérésére futtatja. Azure használnak annak ellenőrzéséhez, az alkalmazás virtuális gépek, az Azure-bA áttelepítés előtt függőségi leképezéssel át.



## <a name="assessment-architecture"></a>Értékelés architektúrája


![Migrálásfelmérési architektúra](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso egy tipikus vállalaton belüli képviselő fiktív nevét. 
- Contoso rendelkezik egy helyszíni adatközpontot (**contoso-datacenter**), a helyszíni tartományvezérlőkkel (CONTOSODC1, CONTOSODC2).
- VMware virtuális gépek találhatók a VMware ESXI gazdagépek 6.5-ös verziót futtat. Gazdagépek: **contosohost1**, **contosohost2**
- A VMware-környezetben 6.5 vCenter-kiszolgáló által felügyelt (**venter**, a virtuális gép futó.
- A SmartHotel utazás alkalmazást:
    - Az alkalmazás két VMware virtuális gépek között többszintű **WEBVM** és **SQLVM**.
    - A virtuális gépek a VMware ESXi-állomáson lévő **contosohost1.contoso.com**.
    - A virtuális gépek futnak a Windows Server 2008 R2 Datacenter SP1.
- A VMware-környezetet egy virtuális gépen futó vCenter Server (**vcenter.contoso.com**) felügyeli.
- A OSTicket szolgáltatás ügyfélszolgálati alkalmazást:
    - Az alkalmazás két virtuális gép között többszintű **OSTICKETWEB** és **OSTICKETMYSQL**.
    - A virtuális gépek futnak, az Ubuntu Linux Server 16.04-es lts verzió.
    - A OSTICKETWEB virtuális gép fut, Apache 2 és a PHP 7.0-ban.
    - A OSTICKETMYSQL virtuális gép fut MySQL 5.7.22.

![Architektúra](./media/contoso-migration-assessment/architecture.png)


## <a name="prerequisites"></a>Előfeltételek

Ez a Contoso (és) minek a vizsgálathoz:

- Tulajdonos vagy közreműködő hozzáférés az Azure-előfizetés, vagy egy erőforráscsoportot az Azure-előfizetésben.
- Egy 5.5-ös, 6.0-s vagy 6.5-ös verziójú helyszíni vCenter-kiszolgáló.
- Egy csak olvasható fiók a vCenter-kiszolgálón, vagy a szükséges jogosultság egy ilyen fiók létrehozásához.
- Jogosultság egy virtuális gép létrehozására a vCenter-kiszolgálón egy .OVA-sablon használatával.
- Legalább egy 5.0-s vagy újabb verziójú ESXi-gazdagép.
- Legalább két helyszíni VMware virtuális gép, amelyek közül az egyik egy SQL Server-adatbázist futtat.
- Az egyes virtuális gépek Azure áttelepítése ügynökök telepítéséhez szükséges engedélyekkel.
- A virtuális gépeknek közvetlen internetkapcsolattal kell rendelkezniük.
        - Az internetes hozzáférést korlátozhatja [a szükséges URL-címekre](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        -Ha internetkapcsolat nélküli gépek a [OMS átjáró](../log-analytics/log-analytics-oms-gateway.md) őket telepíteni kell.
- Az SQL Server-példányt futtató virtuális gép teljes tartományneve az adatbázis-értékeléshez.
- Az SQL Server virtuális gépen futó Windows tűzfalnak engedélyeznie kell a külső kapcsolatokat a 1433-as (alapértelmezett) TCP-porton, hogy a DMA csatlakozni tudjon.


## <a name="assessment-overview"></a>Értékelés – áttekintés

Az alábbiakban hogyan Contoso fog a felmérés elvégzéséhez:


> [!div class="checklist"]
> * **1. lépés: Töltse le és telepítse a DMA**: DMA készítse elő a helyszíni SQL Server-adatbázis értékelését.
> * **2. lépés: Az adatbázis a DMA értékeléséhez**: futtasson, és elemezze az adatbázis assessment.
> * **3. lépés: Felkészülés a Azure telepítse át a virtuális gép assessment**: a helyszíni fiókok és a VMware végeznünk beállítások beállítása.
> * **4. lépés: Azure áttelepíteni a helyszíni virtuális gépek felderítése**: hozzon létre egy Azure áttelepítése adatgyűjtő virtuális gép. Ezután futnak a virtuális gépek felderítése a értékelésére adatgyűjtő.
> * **5. lépés: Felkészülés a függőségi elemzésekről az Azure áttelepítése**: ügynökök telepítése Azure telepítse át a virtuális gépeken, hogy a virtuális gépek közötti függőségi leképezési láthatják.
> * **6. lépés: A virtuális gépek Azure áttelepítése értékeléséhez**: függőség ellenőrzése, a virtuális gépek csoport és az értékelés futtatása. Után készen áll az értékelés, azok elemezze az áttelepítés előkészítése során.


## <a name="step-1-download-and-install-the-dma"></a>1. lépés: Töltse le és telepítse a DMA

1. Contoso letölti a DMA a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - A Segéd, amely képes csatlakozni az SQL-példány bármelyik olyan gépen telepíthető. Nem szükséges az SQL Server-gépen futtatnia.
    - Az SQL Server állomás számítógépen nem futtatható.
2. Futtatás a letöltött telepítőfájl (DownloadMigrationAssistant.msi), a telepítés elindításához.
3. Az a **Befejezés** lapot, és melyik **indítsa el a Microsoft adatok áttelepítési Segéd** a varázsló befejezése előtt.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>2. lépés: Futtasson, és elemezze az adatbázis assessment SmartHotel

Most már a Contoso értékelését, elemezheti a helyszíni SQL Server a SmartHotel alkalmazás futtathatja.

1. Az adatbázis áttelepítési segédjében, kattintson **új**, jelölje be **Assessment**, és nevezze el az értékelés projekt - **SmartHotel**.
2. Akkor válassza ki a **server adatforrástípust** , **SQL Server Azure virtuális gépeken**. 

    ![Forrás kiválasztása](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      A DMA jelenleg nem támogatja a felügyelt SQL-példányokra való migrálások értékelését. A probléma megoldásához Contoso használ SQL Server Azure virtuális gépen feltételezett célként értékeléséhez.

3. A **célverzió válasszon**, és melyik SQL Server 2017 cél verzióval. Válassza ezt a lehetőséget, mert az SQL által felügyelt példány által használt verzió van szükségük.
4. Kompatibilitási szolgáltatás és az új szolgáltatások információk felderítéséhez válassza ki:
    - **Kompatibilitási problémák** vegye figyelembe, hogy megszakíthatja áttelepítési, vagy egy kisebb beállítások áttelepítése előtt igénylő módosításokat. A program megőrizheti ki a szolgáltatásokat, jelenleg használatban van elavultak, szerez tudomást. A problémák kompatibilitási szint szerint vannak rendezve.
    - **Új szolgáltatások ajánlás** jelzi, hogy az áttelepítés után az adatbázis használható SQL Server célplatform új funkciói. Ezek teljesítmény, biztonság és tárterület alapján vannak rendszerezve.

    ![Cél kiválasztása](./media/contoso-migration-assessment/dma-assessment-2.png)

2. A **kapcsolódás a kiszolgálóhoz**, akkor adja meg a virtuális gép fut, az adatbázis és a hitelesítő adatok elérésére. Engedélyezni kell **megbízható kiszolgálói tanúsítvány** győződjön meg arról, hogy el tudja érni az SQL Server. Majd azok **Connect**.

    ![Cél kiválasztása](./media/contoso-migration-assessment/dma-assessment-3.png)

3. A **Hozzáadás forrás**, adnak hozzá, azok segítségével mérheti fel, és kattintson a kívánt adatbázist **következő** az értékelés elindításához.
4. Az értékelés jön létre.
    
    ![Értékelés létrehozása](./media/contoso-migration-assessment/dma-assessment-4.png)

5. A **felülvizsgálati eredményeinek**, láthatják az értékelési eredmények.


### <a name="analyze-the-database-assessment"></a>Az adatbázis-értékelés elemzése

Eredmények jelennek meg, amint azok elérhetők. Ha ezek a hibák elhárításában kell kattintaniuk **újraindítása Assessment** kattintva futtassa újra a assessment.

1. Az a **kompatibilitási problémák** jelentéssel azok ellenőrzése minden kompatibilitási szinttel rendelkező probléma merül fel. A kompatibilitási szintek a következőképpen feleltethetők meg az SQL Server-verzióknak:

    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Kompatibilitási problémák](./media/contoso-migration-assessment/dma-assessment-5.png)

2. Az a **javaslatok funkció** jelentéssel Contoso megtekintheti a teljesítmény, a biztonság és a tárolási funkciók, amelyek az értékelés azt javasolja, hogy az áttelepítés után. Számos funkcióval használata ajánlott, beleértve a memórián belüli online Tranzakciófeldolgozási és Oszlopcentrikus, a Stretch Database, mindig titkosítja, dinamikus Adatmaszkolási és transzparens Data Encryption (TDE).

    ![Szolgáltatási javaslatok](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Azt javasoljuk, hogy a Contoso [lehetővé teszi, hogy a TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) az összes SQL Server adatbázisok, és ez fontos még több adatbázisok esetén a felhőben. TDE csak akkor engedélyezhető, az áttelepítés után. Ha a TDE már engedélyezve van, szüksége lesz a tanúsítvánnyal vagy aszimmetrikus kulcs áthelyezése a célkiszolgáló a fő adatbázist. [További információk](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. A JSON- vagy CSV formátumú assessment exportálhatja.

Vegye figyelembe, hogy ha futtatja a nagyobb skálázási értékelése a következőket teheti:

- Egyszerre több vizsgálatokat futtatja, és az értékelés állapotának megtekintéséhez nyissa meg a **összes értékelések** lap.
- [Egy SQL Server adatbázisba értékelések egyesíteni](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- [Összevonása akkor történjen meg a Power bi-jelentés értékelések](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-3-prepare-for-vm-assessment-with-azure-migrate"></a>3. lépés: Felkészülés a Azure telepítse át a virtuális gép értékelése

Contoso kell Azure át fogja használni a automatikusan virtuális gépek felderítése a ellenőrzéséhez ellenőrizze a virtuális gépek létrehozásához szükséges engedélyek VMware-fiók létrehozásához tekintse meg a portokat, amelyeket meg kell nyitni, és a statisztika beállítások szintjének beállítása.

### <a name="set-up-a-vmware-account"></a>VMware-fiók beállítása

 VM-felderítéshez futnia kell egy csak olvasható fiókot a vCenter, a következő tulajdonságokkal: 

- Felhasználó típusa: Egy legalább olvasási jogosultsággal rendelkező felhasználó.
- Engedélyek: Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható.
- Részletek: A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.
- A hozzáférés korlátozásához rendelje a **Gyermekobjektumba propagálás** objektummal rendelkező **Nincs hozzáférés** szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).

### <a name="verify-permissions-to-create-a-vm"></a>A virtuális gép létrehozásához szükséges engedélyek ellenőrzése

Contoso ellenőrizze, hogy jogosult a virtuális gép létrehozása a fájl importálásával. PETESEJTEK formátumban. [További információk](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Portok ellenőrzése

A Contoso assessment függőségi hozzárendelést használ. A szolgáltatás működéséhez ellenőrizni szeretné virtuális gépekre telepített ügynök. Csatlakozni az Azure-bA az egyes virtuális gépek a 443-as TCP-portot kell az ügynököt. [További információ](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) a kapcsolódási követelményekről.


### <a name="set-statistics-settings"></a>Statisztikai beállítások megadása

Ahhoz, azok a telepítés megkezdéséhez Contoso 3. szint kell beállítania a vCenter-kiszolgáló statisztikák beállításait. Vegye figyelembe:

- A szint beállítása után várjon legalább egy napot az értékelés futtatása előtt kell. Máskülönben előfordulhat, hogy az értékelés nem a vártnak megfelelően fog működni.
- Ha a szint nagyobb mint 3, az értékelés működik, de:
    - A rendszer nem gyűjti majd a lemezek és a hálózat teljesítményadatait.
    - A tárolás tekintetében az Azure Migrate egy standard lemez létrehozását javasolja az Azure-ban, amelynek a mérete megegyezik a helyszíni lemezével.
    - A hálózatkezelés tekintetében a rendszer azt javasolja, hogy minden helyszíni hálózati adapterhez hozzon létre egy Azure-beli hálózati adaptert.
    - A számítási kapacitás tekintetében az Azure Migrate áttekinti a VM-magokat és a memória méretét, és egy azonos konfigurációjú Azure-beli virtuális gép létrehozását javasolja. Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja.
- [További információ](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) a 3. szintű méretezésről.

Az alábbiak szerint állíthatja azokat a szintje:

1. A vSphere webes ügyféllel akkor nyissa meg a vCenter server-példányt.
2. A **kezelése** > **beállítások** > **általános**, kattintanak **szerkesztése**.
3. A **statisztika**, azok beállítása a statisztika szint **3. szint**.

    ![vCenter statisztikai szintje](./media/contoso-migration-assessment/vcenter-statistics-level.png)



## <a name="step-4-discover-vms"></a>4. lépés: Virtuális gépek felderítése

Virtuális gépek felderítése, a Contoso Azure áttelepítése projektben hoz létre. Azok töltse le és állítsa be a virtuális gép gyűjtő, és futtassa a gyűjtő a helyszíni virtuális gépek felderítése.

### <a name="create-a-project"></a>Projekt létrehozása

1. Az a [Azure-portálon](https://portal.azure.com), keresnek a **Azure áttelepítése**, és hozzon létre egy projektet (ContosoMigration).
2. Adja meg a projekt nevét, az Azure-előfizetés, és hozzon létre egy új Azure erőforráscsoportot **ContosoFailoverRG**. Vegye figyelembe:

    - Azure Migrate-projektet csak az USA középnyugati régiójában és keleti régiójában lehet létrehozni.
    - Bármilyen célhelyre tervezhet migrálást.
    - A projekt helye csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.

    ![Azure Migrate](./media/contoso-migration-assessment/project-1.png)


### <a name="download-the-collector-appliance"></a>A gyűjtőberendezés letöltése

Az Azure Migrate létrehoz egy gyűjtőberendezésnek nevezett helyszíni virtuális gépet. A virtuális gép felderíti a helyszíni VMware virtuális gépeket, és az azokkal kapcsolatos metaadatokat továbbítja az Azure Migrate szolgáltatásnak. A gyűjtő készülék beállításához Contoso letölti egy. PETESEJTEK sablont, és importálja azt a helyszíni vCenter-kiszolgáló a virtuális gép létrehozásához.

1. Az Azure áttelepítése Projekt > **bevezetés** > **felderítési & felmérési** > **gépek felderítése**, letöltik a. PETESEJTEK sablonfájl.
2. Másolják a projekt azonosítója és kulcsa. A gyűjtő konfigurálásához szükséges.

    ![Az .ova-fájl letöltése](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>A gyűjtőberendezés ellenőrzése

A virtuális Gépen való telepítése előtt a Contoso ellenőrzi, hogy a. PETESEJTEK fájl biztonságos.

1. A számítógépen, amelyen a fájl letöltése akkor nyissa meg egy rendszergazdai parancsablakot.
2. Azok a következő parancsot a kivonat létrehozásához a petesejtjének:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A létrehozott kivonatnak egyeznie kell ezekkel a beállításokkal (1.0.9.7-es verzió)

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c


### <a name="create-the-collector-appliance"></a>A gyűjtőberendezés létrehozása

Most Contoso importálja a letöltött fájlt a vCenter-kiszolgáló és a konfigurációs kiszolgáló virtuális gép kiépítéséhez.

1. A vSphere Client-konzolon kattintson **fájl** > **OVF-sablon telepítése**.

    ![Az OVF telepítése](./media/contoso-migration-assessment/vcenter-wizard.png)

2. Az OVF-sablon központi telepítése varázslóban > **forrás**, akkor adja meg a helyét a. PETESEJTEK fájlt.
3. A **név és hely**, akkor adja meg egy rövid nevet a gyűjtő virtuális gép számára, és a készlet helyét, amelyben a virtuális gép üzemelni fog. Is adja meg, hogy a gazdagép vagy fürt, amelyen a gyűjtő készülék futni fog.
5. A **tárolási**, akkor adja meg a tárolási helyét és a **lemezformátum**, és hogyan szeretnék a tárolót létesíteni.
7. A **Hálózatleképezés**, akkor adja meg a hálózaton, amelyhez a gyűjtő virtuális gép csatlakozni fog. A hálózatnak internetkapcsolattal kell rendelkeznie a metaadatok az Azure-ba küldéséhez.
8. Tekintse át a beállításokat, és válassza ki **kapcsolja be a telepítést követően**> **Befejezés**. A berendezés létrehozása után megjelenik egy üzenet, amely visszaigazolja a sikeres létrehozást.

### <a name="run-the-collector-to-discover-vms"></a>A gyűjtő futtatása a virtuális gépek felderítéséhez

Most a virtuális gépek felderítése a gyűjtő futnak. Ügyeljen arra, hogy a gyűjtő jelenleg csak támogatja a "Angol (Egyesült Államok)" az operációs rendszer nyelve és a gyűjtő felület nyelve.

1. A vSphere Client-konzol > **nyissa meg a konzolt**, nyelvi, időzóna és a virtuális gép adatgyűjtő jelszó beállításainak megadása.
2. Az asztalon kattintson a **futtassa a gyűjtő** helyi.

    ![Gyűjtő parancsikonja](./media/contoso-migration-assessment/collector-shortcut.png)

4. Az az Azure-áttelepítése gyűjtő > **előfeltétel**, fogadja el a licencfeltételeket, és a külső adatokat olvasni.
5. A gyűjtő ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel, hogy van-e szinkronizálva az időbeállítás, és, hogy a gyűjtő szolgáltatás fut-e (telepítve van a virtuális gép alapértelmezés szerint). VMWare PowerCLI is telepíti.

    > [!NOTE]
    > Feltételezzük, hogy a virtuális gép közvetlen, proxy nélkül internet-hozzáféréssel rendelkezik.

    ![Előfeltételek ellenőrzése](./media/contoso-migration-assessment/collector-verify-prereqs.png)


5. A **adja meg a vCenter-kiszolgáló adatait**, adja meg a név (FQDN) vagy a vCenter-kiszolgáló IP-címét, és a csak olvasható a hitelesítő adatokat a felderítéshez használni.
7. Egy felderítési hatókörének megadása virtuális gép, és melyik. A gyűjtő csak a megadott hatókörön belül deríti fel a virtuális gépeket. A hatókör egy adott mappára, adatközpontra vagy fürtre állítható be. Nem tartalmazhat 1500-nál több virtuális gépet.

    ![Csatlakozás a vCenterhez](./media/contoso-migration-assessment/collector-connect-vcenter.png)

6. A **megadása áttelepítési projekt**, adja meg a Projektazonosítónak Azure áttelepítése és a portálról másolt kulcsot. Ezt úgy szerezheti be őket ismét a projekt **áttekintése** lap > **gépek felderítése**.  

    ![Csatlakozás az Azure szolgáltatáshoz](./media/contoso-migration-assessment/collector-connect-azure.png)

7. A **gyűjtemény folyamatjelző** Contoso felderítési figyelheti, és győződjön meg arról, hogy a virtuális gépek gyűjtött metaadatai a hatókörben. Az adatgyűjtő mutatja a felderítés hozzávetőleges időtartamát.

    ![Gyűjtés folyamatban](./media/contoso-migration-assessment/collector-collection-process.png) 



### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

Gyűjtemény befejezése után a Contoso ellenőrzi, hogy a virtuális gépek megjelennek a portálon.

1. Az Azure áttelepítése Projekt > **kezelése** > **gépek**, akkor ellenőrizze, hogy megjelenik-e a felderíteni kívánt virtuális gépeket.

    ![Felderített gépek](./media/contoso-migration-assessment/discovery-complete.png)

3. Vegye figyelembe, hogy az Azure Migrate-ügynök jelenleg nincs telepítve a gépeken. Contoso most telepíti ezeket a függőségeket megtekintéséhez.

    ![Felderített gépek](./media/contoso-migration-assessment/machines-no-agent.png)



## <a name="step-5-prepare-for-dependency-analysis"></a>5. lépés: Felkészülés a függőségi elemzés

Megtekintheti a Contoso elérni kívánt virtuális gépek közötti függőségeket, akkor töltse le és telepítse ügynökök az alkalmazás virtuális gépek. Contoso ezt végzi, az alkalmazások, Windows és Linux minden virtuális gépeken.

### <a name="take-a-snapshot"></a>Pillanatkép készítése

Virtuális gép egy másolatát, a pillanatképek készítése előtt telepítve van az ügynök által módosítása előtt vezetnek.

![Gép pillanatképe](./media/contoso-migration-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>A virtuálisgép-ügynökök letöltése és telepítése

1. Az a **gépek** lap, a gép, és melyik, majd **telepítése szükséges** a a **függőségek** oszlop.
2. Az a **gépek felderítése** lapon tehetik a következőket:
    - Az egyes Windows virtuális gépek MMA és függőségi ügynök letöltése
    - Az egyes Linux virtuális gépek MMA és függőségi ügynök letöltése
3. Most másolják a munkaterület azonosítója és kulcsa. Ezeket a kell ezeket a MMA telepítésekor.

    ![Ügynök letöltése](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Telepíti az ügynököt a Windows virtuális gépek

A telepítés minden egyes virtuális gépeken futnak.

#### <a name="install-the-mma-on-windows-vms"></a>Telepítse az MMA a Windows virtuális gépeken

1. Ezek kattintson duplán a letöltött ügynök.
2. A **célmappa**, akkor hagyja az alapértelmezett telepítési mappa > **következő**.
2. A **ügynök telepítésének beállításai**, és melyik **az ügynök csatlakoztatása az Azure Naplóelemzés** > **következő**.

    ![Az MMA telepítése](./media/contoso-migration-assessment/mma-install.png)
    
5. A **Azure Naplóelemzés**, azok illessze be a munkaterület azonosítója és a portál fájlból másolt kulcsot. 

    ![Az MMA telepítése](./media/contoso-migration-assessment/mma-install2.png)

6. A **telepítésre kész**, akkor most már telepítheti az MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>A függőségi ügynök telepíthető Windows virtuális gépek

1. Ezek kattintson duplán a letöltött függőségi ügynök.
2. Ezeket a fogadja el a licencfeltételeket, és várja meg a telepítés befejezéséhez.

    ![Függőségi ügynök](./media/contoso-migration-assessment/dependency-agent.png)


### <a name="install-the-agents-on-linux-vms"></a>Telepíti az ügynököt a Linux virtuális gépek

A telepítés minden egyes virtuális gépeken futnak.

#### <a name="install-the-mma-on-linux-vms"></a>Telepítse az MMA a Linux virtuális gépeken

1. Minden virtuális gép használata a telepítése a python ctypes könyvtár: **sudo apt-get telepítése python-ctypeslib**.
2. A parancs az MMA ügynök telepítéséhez a legfelső szintű kell futnak.  Legyen, legfelső szintű futtassa a következő parancsot, és adja meg a gyökér szintű jelszó: **sudo -i**.
3. Most már a MMA ügynök telepítése.
    - A parancs a megfelelő munkaterület azonosítója és kulcsa beilleszteni.
    - Parancsok vannak a 64 bites.
    - A **munkaterület azonosítója** és **elsődleges kulcs** az OMS-portálon belül található > **beállítások**, a a **csatlakoztatott források** fülre.
    - Töltse le az OMS-ügynököt, a ellenőrzőösszegei, illetve a telepítés/bevezetni az ügynök ellenőrzése a következő parancsok futtatásával.

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```
 


#### <a name="install-the-dependency-agent-on-linux-vms"></a>A függőségi ügynök telepíthető Linux virtuális gépek

MMA a telepítést követően a Contoso telepítheti a függőségi ügynök a Linux virtuális gépeken.

1. A függőségi ügynök telepítve van a Linux rendszerű számítógépeken InstallDependencyAgent Linux64.bin, egy önkicsomagoló bináris parancsfájl használatával. Akkor futtassa a fájlt megosztása, vagy adja hozzá a végrehajtási engedélyeket magában a fájlban.

2. A Linux függőségi ügynök legfelső szintű telepítése:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```


## <a name="step-6-run-and-analyze-the-vm-assessment"></a>6. lépés: Futtasson, és elemezze a virtuális gép értékelése

Contoso most ellenőrizze a gép függőségeket, és hozzon létre egy csoportot. Ezután futnak a csoporthoz tartozó értékelési.

### <a name="verify-dependencies-and-create-a-group"></a>Ellenőrizze a függőségeket, és hozzon létre egy csoportot


1. A gépek elemzéséhez, kattintson **függőségeinek megtekintése**.

    ![Gépek függőségeinek megtekintése](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. Az SQLVM esetében a függőségi térkép a következő részleteket tartalmazza:

    - Az SQLVM-en futó, aktív hálózati kapcsolatokkal rendelkező folyamatcsoportok/folyamatok a megadott időszakban (alapértelmezés szerint egy óra)
    - Az összes függő gép bejövő (ügyfél) és kimenő (kiszolgálói) TCP-kapcsolatai.
    - A telepített Azure Migrate-ügynökkel rendelkező függő gépek külön mezőkben jelennek meg
    - A telepített ügynökkel nem rendelkező gépek esetében a port- és IP-címadatok jelennek meg.

3. Gépek telepítve van az ügynök (WEBVM) akkor kattintson az a gép jelölőnégyzetet, ha további információkat, beleértve a teljes tartománynév, az operációs rendszer és a MAC-címet.

    ![Csoportos függőségek megtekintése](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Most a virtuális gépek hozzáadása a csoporthoz (SQLVM és WEBVM), és melyik.  Ezek is használja a CTRL + kattintással válassza ki a több virtuális gép.
5. Kattintva **csoport létrehozása**, és adjon meg egy nevet (smarthotelapp).

> [!NOTE]
    > A függőségek részletesebb megtekintéséhez terjessze ki az időtartományt. Megadhat egy adott időtartamot vagy kezdő és befejező dátumokat is.


### <a name="run-an-assessment"></a>Értékelés futtatása


1. Az a **csoportok** lapon nyissa meg azt a csoportot (smarthotelapp), és kattintson a **assessment létrehozása**.

    ![Értékelés létrehozása](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Az értékelés a **Kezelés** > **Értékelések** lapon jelenik meg.

Contoso használja az alapértelmezett assessment beállításokat, de a beállítások is testre szabhatók. [További információk](how-to-modify-assessment.md).



### <a name="analyze-the-vm-assessment"></a>A virtuálisgép-kiértékelés elemzése

Egy Azure áttelepítése assessment információkat tartalmaz a helyszíni virtuális gépek kompatibilitás az Azure-hoz, javasolt megfelelő méretének kiválasztását az Azure virtuális gépekhez, és becsült havi Azure költségei.

![Értékelési jelentés](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

![Értékelés megjelenítése](./media/contoso-migration-assessment/assessment-display.png)

Egy értékelési kapja abban, hogy minősítést 1 csillag 5 csillag (1 csillag alatt a legalacsonyabb és a legmagasabb alatt 5 csillag).
- A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve.
- A minősítés segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát.
- Megbízhatósági minősítés akkor hasznos, ha is *teljesítmény-alapú méretezési* , Azure át lehet, hogy nincs elegendő adatpont a használat alapú méretezési teendő. A *helyszíni méretezésnél* a megbízhatósági minősítés mindig 5 csillagos, mert az Azure Migrate rendelkezik a virtuális gép méretezéséhez szükséges összes adatponttal.
- Az elérhető adatpontok százalékától függően van megadva a megbízhatósági minősítés:

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%–40% | 2 csillag
   41%–60% | 3 csillag
   61%–80% | 4 csillag
   81%–100% | 5 csillag

#### <a name="verify-readiness"></a>Felkészültség ellenőrzése

![Készenléti állapot értékelése](./media/contoso-migration-assessment/azure-readiness.png)  

Az értékelési jelentés egy táblában összefoglalva jeleníti meg az információkat. Vegye figyelembe, hogy a teljesítményalapú méretezés megjelenítéséhez az Azure Migrate-nek szüksége van a következő adatokra. Ha ezt az információt nem lehet összegyűjteni, az értékelés pontatlan lehet.

- A processzor és a memória kihasználtsági adatai.
- A virtuális géphez csatlakoztatott összes lemez olvasási/írási IOPS-értéke és adatátviteli teljesítménye.
- A virtuális géphez csatlakoztatott összes hálózati adapter bejövő és kimenő hálózati forgalmának adatai.


**Beállítás** | **Jelzés** | **Részletek**
--- | --- | ---
**Azure-beli virtuális gép felkészültsége** | Azt jelzi, hogy a virtuális gép készen áll-e a migrálásra | Lehetséges állapotok:</br><br/>- Készen áll az Azure-beli migrálásra<br/><br/>- Feltételekkel kész <br/><br/>- Nem áll készen az Azure-beli migrálásra<br/><br/>- A felkészültség ismeretlen<br/><br/> Ha a virtuális gép nem áll készen, bemutatunk néhány lehetséges javítási intézkedést.
**Azure-beli virtuális gép mérete** | A készen álló virtuális gépek esetében javaslatot teszünk egy Azure-beli virtuálisgép-méretre. | A méretezési javaslat az értékelési tulajdonságoktól függ:<br/><br/>- Ha teljesítményalapú méretezést használt, a méretezés a virtuális gépek teljesítményelőzményeit veszi figyelembe.<br/><br/>- Ha „helyszíni méretezést” használt, a méretezés a helyszíni virtuális gép méretétől függ, és a kihasználtsági adatokat nem használja fel a rendszer.
**Ajánlott eszköz** | Mivel az ügynökök a mi gépeinken futnak, az Azure Migrate megvizsgálja a gépen futó folyamatokat, és meghatározza, hogy a gép adatbázisgép-e.
**Virtuális gép adatai** | A jelentés a helyszíni virtuális gép beállításait mutatja, beleértve az operációs rendszer, a rendszerindítási típus, a lemez és a tárterület adatait.


#### <a name="review-monthly-cost-estimates"></a>Havi költségbecslések áttekintése

Ez a nézet a virtuális gépeknek az Azure-ban való futtatásával kapcsolatos összes számítási és tárolási költséget mutatja az egyes gépek adataival együtt.

![Készenléti állapot értékelése](./media/contoso-migration-assessment/azure-costs.png)

- A költségbecslések az egyes gépekre vonatkozó méretjavaslatokon alapulnak.
- A becsült havi számítási és tárolási költségek a csoportban lévő virtuális gépekre összesítve szerepelnek.


## <a name="clean-up-after-assessment"></a>Értékelés után tisztítása

- Az értékelés befejezése után a Contoso megőrzi a jövőbeli értékelésének Azure áttelepítési a készülék.
- A virtuális gép VMware kikapcsolása. Akkor lesz indítsa el újra további virtuális gépek értékelésekor.
- Az Azure-ban a Contoso áttelepítési projekt fogja vezetnek.  Az ContosoFailoverRG erőforráscsoport keleti Velünk Azure régióban jelenleg telepítették.
-  A gyűjtő VM 180 napos próbaverziója licenccel rendelkezik. Ha ezt a határt lejár azok kell letölteni, és állítsa be a gyűjtő újra.


## <a name="conclusion"></a>Összegzés

Ebben a forgatókönyvben a Contoso értékelni SmartHotel alkalmazás adatbázisában a DMA eszközt, és a helyszíni virtuális gépeket az Azure áttelepítése szolgáltatással. Azok a vizsgálatot, győződjön meg arról, hogy a helyszíni erőforrások készen áll az Azure-bA áttelepítési majd tekintse át.

## <a name="next-steps"></a>További lépések

A sorozat következő cikkben Contoso áthelyezi a növekedési és shift áttelepítése az Azure-ban a SmartHotel alkalmazást. Contoso áttelepíti a frontend WEBVM az alkalmazás használatával az Azure Site Recovery és az alkalmazás-adatbázis egy Azure SQL felügyelt-példányra, az adatbázis áttelepítési szolgáltatással. [Első lépések](contoso-migration-rehost-vm-sql-managed-instance.md) ezzel az üzembe helyezéssel.
