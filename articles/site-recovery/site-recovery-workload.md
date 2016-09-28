<properties
    pageTitle="Milyen számítási feladatokat tud védeni az Azure Site Recovery?" 
    description="Az Azure Site Recovery a helyszíni virtuális gépek és a fizikai kiszolgálók Azure-ba vagy egy másodlagos helyszíni helyre történő replikálásának, feladatátvételének és helyreállításának koordinálásával képes védelmet nyújtani a számítási feladatok és alkalmazások számára." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="07/06/2016" 
    ms.author="raynew"/>


# Milyen számítási feladatokat tud védeni az Azure Site Recovery?


Ebből a cikkből megtudhatja, hogy mely számítási feladatok és alkalmazások számára nyújthat védelmet az Azure Site Recovery segítségével.

Megjegyzéseit vagy kérdéseit a cikk alján, vagy az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.

## Áttekintés

A legtöbb vállalatnál szükség van üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely meghatározza, hogy hogyan tudnak az alkalmazások, a számítási feladatok és az adatok elérhetők maradni a tervezett és nem tervezett leállások során, illetve, hogy hogyan lehet minél gyorsabban helyreállítani a normál működést.

Az Azure Site Recovery szolgáltatása lehetővé teszi, hogy a helyszíni fizikai kiszolgálóknak és az (Azure-) felhőben futó virtuális gépeknek egy másodlagos helyre történő replikálásával alkalmazásbarát hibatűrési megoldásokat helyezzen üzembe, így járul hozzá a BDCR-stratégia megvalósításához. A Site Recovery használata esetén nem számít, hogy az alkalmazások fizikai kiszolgálókon vagy VMware- vagy Hyper-V virtuális gépeken futnak, sem az, hogy Windows- vagy Linux-alapúak, a replikálást, illetve a vészhelyreállítási vagy feladatátvételi és -visszaadási teszteket ugyanúgy gond nélkül elvégezheti.


A Site Recovery számos Microsoft-alkalmazással (például SharePoint, Exchange, Dynamics, SQL Server és Active Directory) képes együttműködni. A Microsoft ezenfelül számos vezető fejlesztővel (például Oracle, SAP, IBM és Red Hat) is együtt dolgozik, így az ezektől a vállalatoktól származó alkalmazások és szolgáltatások is garantáltan futnak a Microsoft platformjain, köztük az Azure-on is. Megoldását hozzáigazíthatja az egyes alkalmazások igényeihez.

## Miért érdemes a Site Recoveryt használni az alkalmazások védelmére?

A Site Recovery az alábbi módokon járul hozzá az alkalmazásszintű védelemhez és helyreállításhoz: 

- Akár 30 másodperces helyreállítási időkorlátjával közel egyidejű replikációt kínál, így a legfontosabb üzleti alkalmazásokhoz is megfelelő.
- Egy- és többrétegű alkalmazásokról egyaránt képes alkalmazáskonzisztens pillanatképek készítésére.
- Együttműködik az SQL Server AlwaysOn szolgáltatással, és számos más alkalmazásszintű replikációs technológiát is képes felhasználni (például AD-replikáció, SQL AlwaysOn, Exchange adatbázis-elérhetőségi csoportok (DAG) és Oracle Data Guard).
- A rugalmas helyreállítási tervek segítségével egész alkalmazáscsoportok is egyetlen kattintással visszaállíthatók, emellett külső parancsprogramok és manuális műveletek is beépíthetők a tervekbe. 
- A Site Recovery és az Azure fejlett hálózatkezelési funkciói átláthatóvá teszik az alkalmazáshálózati követelményeket, ideértve az IP-címek lefoglalásának képességét, a terheléselosztás konfigurálását, valamint az Azure Traffic Manager integrációját, amely alacsony RTO-jú hálózatváltást garantál.
-  A szolgáltatás gazdag, éles használatra kész és alkalmazásspecifikus parancsprogramokat tartalmazó automatizációs kódtárat tartalmaz, amely letölthető, és beépíthető a helyreállítási tervekbe.



##A számítási feladatok összefoglalása

A Site Recovery bármilyen, támogatott virtuális gépen futó alkalmazást képes replikálni. Ezenfelül a termékekért felelős csoportokkal együttműködésben további, alkalmazásspecifikus teszteket is végrehajtottunk.

**Számítási feladat** | **Hyper-V virtuális gépek replikálása másodlagos helyre** | **Hyper-V virtuális gépek replikálása az Azure-ba** | **VMware virtuális gépek replikálása másodlagos helyre** | **VMware virtuális gépek replikálása az Azure-ba**
---|---|---|---|---
Active Directory, DNS | I | I | I | I 
Webalkalmazások (IIS, SQL) | I | I | I | I
SCOM | I | I | I | I
SharePoint | I | I | I | I
SAP<br/><br/>SAP-hely replikálása Azure-ba (nem fürtözött megoldások) | I (a Microsoft által végzett tesztek alapján) | I (a Microsoft által végzett tesztek alapján) | I (a Microsoft által végzett tesztek alapján) | I (a Microsoft által végzett tesztek alapján)
Exchange (nem DAG) | I | Hamarosan | I | I
Távoli asztal/VDI | I | I | I | N/A 
Linux (operációs rendszer és alkalmazások) | I (a Microsoft által végzett tesztek alapján) | I (a Microsoft által végzett tesztek alapján) | I (a Microsoft által végzett tesztek alapján) | I (a Microsoft által végzett tesztek alapján) 
Dynamics AX | I | I | I | I
Dynamics CRM | I | Hamarosan | I | Hamarosan elérhető
Oracle | I (a Microsoft által végzett tesztek alapján) | I (a Microsoft által végzett tesztek alapján) | I (a Microsoft által végzett tesztek alapján) | I (a Microsoft által végzett tesztek alapján)
Windows fájlkiszolgáló | I | I | I | I


## Active Directory és DNS replikálása

A legtöbb vállalati alkalmazás számára elengedhetetlen az Active Directory- és DNS-infrastruktúra használata. A vészhelyreállítás során a számítási feladatok és alkalmazások újraindításához az infrastruktúra e komponenseinek is védelmet és helyreállítást kell biztosítania.

A Site Recovery segítséglével teljesen automatizált vészhelyreállítási tervet hozhat létre az Active Directoryhoz és a DNS-hez. Ha például az a cél, hogy feladatátvételt hajtson végre az elsődleges helyen működő SharePointon és az SAP-n egy másodlagos helyre, beállíthat egy olyan helyreállítási tervet, amely először az Active Directory-feladatokat adja át, majd egy kiegészítő alkalmazásspecifikus helyreállítási tervet, amely átadja az Active Directoryn alapuló összes többi alkalmazást.

[Itt részletesebben tájékozódhat](site-recovery-active-directory.md) az Active Directory és a DNS védelméről.

## Az SQL Server védelme

Az SQL Server adatszolgáltatási alapot nyújt az üzleti alkalmazások helyi adatközpontban működő adatszolgáltatásai számára.  A Site Recovery és az SQL Serverhez elérhető HA/DR technológiák együttesen akár az SQL Servert használó többrétegű vállalati alkalmazások számára is képesek védelmet nyújtani. A Site Recovery a következőket biztosítja:

- Egyszerű és költséghatékony vészhelyreállítási megoldás az SQL Serverhez. Az SQL Server önálló kiszolgálói és fürtjei több verziójának és kiadásának replikálása az Azure-ba vagy egy másodlagos helyre.  
- Integráció az SQL AlwaysOn elérhetőségi csoportokkal a feladatátvételnek és -visszavételnek az Azure Site Recovery helyreállítási tervekkel való kezelése céljából.
- Végpontok közötti helyreállítási tervek az alkalmazás összes rétege számára, ideértve az SQL Server-adatbázisokat is.
- Az SQL Server csúcsterhelésre való méretezése a Site Recovery segítségével nagyobb IaaS-típusú virtuális gépekre váltással az Azure-ban.
- Az SQL Server vészhelyreállítási funkcióinak egyszerű tesztelése. A Site Recovery segítségével anélkül végezhet feladatátvételi teszteket, elemezheti az adatokat, és futtathat megfelelőség-ellenőrzéseket, hogy káros hatást gyakorolna az éles környezetre.

[Itt részletesebben tájékozódhat](site-recovery-sql.md) az SQL Server védelméről.

##A SharePoint védelme

Az Azure Site Recovery a következőkkel segít a SharePoint védelmében:

- Használata esetén nem szükséges költséges készenléti kiszolgálófarmot kiépítenie a vészhelyreállításhoz. A Site Recovery segítségével a farm egésze (a webes, az alkalmazás- és az adatbázisréteg) replikálható az Azure-ba vagy egy másodlagos helyre.
- Leegyszerűsíti az alkalmazások üzembe helyezését és felügyeletét. A rendszer automatikusan replikálja az elsődleges helyekre telepített frissítéseket, így azok a farm feladatátvételét és helyreállítását követően azonnal elérhetőek lesznek a másodlagos helyen. Csökkenti a felügyelet összetettségét, valamint a készenléti farm naprakészen tartásával kapcsolatos költségeket.
- Leegyszerűsíti a SharePoint-alkalmazások fejlesztését és tesztelését, mivel segítségével az éleshez hasonló, igény szerinti replikakörnyezet hozható létre a teszteléshez és a hibakereséshez.
- Megkönnyíti a felhőre való átállást, mivel a SharePoint üzemelő példányok a Site Recovery segítségével is áttelepíthetők az Azure-ba.

[Itt részletesebben olvashat](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) a SharePoint védelméről.


## A Dynamics AX védelme

Az Azure Site Recovery a következőkkel segít a Dynamics AX ERP-megoldások védelmében:

- Segítséget nyújt az egész Dynamics AX-környezetnek (a webes és AOS-rétegeknek, az adatbázisrétegeknek, a SharePointnak) az Azure-ba vagy egy másodlagos helyre történő replikációjának előkészítésében.
- Leegyszerűsíti a Dynamics AX üzemelő példányok felhőbe (Azure-ba) való áttelepítését.
- Leegyszerűsíti a Dynamics AX-alkalmazások fejlesztését és tesztelését, mivel segítségével az éleshez hasonló, igény szerinti környezetet hozható létre a teszteléshez és a hibakereséshez.

[Itt részletesebben tájékozódhat](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) a Dynamic AX védelméről.

## A távoli asztali szolgáltatások védelme 

A távoli asztali szolgáltatások (RDS) lehetővé teszik a virtuális asztali infrastruktúra (VDI), illetve a munkamenet-alapú asztal és alkalmazások használatát, így a felhasználók bárhonnan dolgozhatnak. Az Azure Site Recovery segítségével:

- Felügyelt vagy nem felügyelt, készletbe vont virtuális asztalait másodlagos helyre, távoli alkalmazásait és munkameneteit pedig másodlagos helyre vagy az Azure-ba replikálhatja.
- A következőket replikálhatja:

**Távoli asztali szolgáltatások** | **Hyper-V virtuális gépek replikálása másodlagos helyre** | **Hyper-V virtuális gépek replikálása az Azure-ba** | **VMware virtuális gépek replikálása másodlagos helyre** | **VMware virtuális gépek replikálása az Azure-ba** | **Fizikai kiszolgálók replikálása egy másodlagos helyre** | **Fizikai kiszolgálók replikálása az Azure-ba**
---|---|---|---|---|---|---
**Készletbe vont virtuális asztalok (nem felügyelt)** | Igen | Nem | Igen | Nem | Igen | Nem
**Készletbe vont virtuális asztalok (felügyelt, UPD nélkül)** | Igen | Nem | Igen | Nem | Igen | Nem
**Távoli alkalmazások és asztali munkamenetek (UPD nélkül)** | Igen | Igen | Igen | Igen | Igen | Igen


[Itt részletes tájékoztatást olvashat](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) az RDS védelméről.


## Az Exchange védelme

A Site Recovery a következőkkel segít az Exchange védelmében:

- A kisebb Exchange üzemelő példányok, például önálló vagy nem fürtözött kiszolgálók esetében a Site Recovery képes replikációt és feladatátvételt végezni az Azure-ba vagy egy másodlagos helyre.
- Nagyobb üzemelő példányok esetében a Site Recovery együttműködik az Exchange-adatbázisok rendelkezésre állási csoportjaival (DAG).
- Vállalati környezetben az Exchange-vészhelyreállításhoz az Exchange DAG-k használatát javasoljuk.  A Site Recoveryben létrehozott helyreállítási csoportok tartalmazhatnak DAG-ket, amelyek képesek a helyek közötti DAG-feladatátvétel megvalósítására.


[További információk](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) az Exchange védelméről.

## Az SAP védelme

A Site Recovery segítségével a következőkkel védheti SAP üzemelő példányait: 

- Az üzemelő példány különböző rétegeinek az Azure-ba vagy másodlagos helyre replikálásával védelmet nyújthat az egész SAP üzemelő példány számára.
- A Site Recovery segítségével áttelepítheti az Azure-ba az SAP üzemelő példányt, így leegyszerűsítheti a felhőre való átállást.
- Leegyszerűsíti az SAP-fejlesztést és -tesztelést, mivel segítségével az éleshez hasonló, igény szerinti környezetet hozható létre az alkalmazások teszteléséhez és a hibakereséséhez.

[Itt részletesen tájékozódhat](http://aka.ms/asr-sap) az SAP védelméről.

## Következő lépések

[Előkészületek](site-recovery-best-practices.md) a Site Recovery üzembe helyezéséhez




<!--HONumber=Sep16_HO4-->


