---
title: Az Azure-beli virtuális gépek áthelyezését nagyszámítógépes |} A Microsoft Docs
description: Áthelyezési a nagyszámítógépes számítási feladatok, például az IBM Z-alapú rendszerekben a Microsoft Azure virtuális gépeken (VM) használatával.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 8be0ebc486739f8826e8a1d5a5307a219ba71b6f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192717"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>A nagyszámítógépes újratárolása az Azure-beli virtuális gépeken

A munkaterhelések áttelepítését a nagyszámítógépes környezeteket a felhőben lehetővé teszi, hogy korszerűsítheti infrastruktúráját, és gyakran költséget takaríthat meg. Számos számítási feladatok kis kód módosítása, például adatbázisok nevei frissítése továbbíthatók az Azure-bA.

Az előfizetési időszak *nagyszámítógépes* általában nagy számítógéprendszer hivatkozik, de a jelenleg telepített túlnyomó IBM rendszer Z kiszolgálók vagy az IBM plug-compatible rendszerek MVS, DOS, VSE, az operációs rendszer/390 vagy z/OS rendszerű.

Egy Azure virtuális gép (VM) segítségével azonosíthatók és felügyelheti az erőforrásokat egy adott alkalmazáshoz egyetlen példányán. Például a z/operációs rendszer IBM Nagyszámítógépek logikai partíció (LPARS) használni erre a célra. A nagyszámítógépes IBM Db2-adatbázishoz használható egy LPAR társított COBOL programokkal CICS régió, és a egy külön LPAR. Egy tipikus [n szintű alkalmazáshoz az Azure-ban](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) üzembe helyezi az Azure virtuális gépeket, amelyek az egyes rétegek alhálózatait osztható virtuális hálózatban.

Az Azure virtuális gépek fordítóprogramot lift-and-shift-forgatókönyveket támogató és nagyszámítógépes emulációs környezetben futtathatja. Fejlesztési és tesztelési célra általában az első munkaterhelések a nagyszámítógépes áttelepíthetők az Azure fejlesztési-tesztelési környezet között. Közös server-összetevőjét, Emulálhatja online tranzakció-folyamat (OLTP), a batch és az adatok feldolgozási rendszerek tartalmazza az alábbi ábrán látható módon.

![Emulációs környezetek Azure-beli lehetővé teszi z/OS-alapú rendszerek futtatására.](media/01-overview.png)

Egyes nagyszámítógépes számítási feladatok áttelepíthetők az Azure-bA viszonylag könnyen, míg mások is rehosted, Azure-ban egy partneri megoldás használatával. Részletes útmutatás az egy partneri megoldás választása a [Azure nagyszámítógépes Migrálási központ](https://azure.microsoft.com/migration/mainframe/) segítségével.

## <a name="set-up-devtest-environment-using-a-micro-focus-rehosting-platform"></a>Állítsa be a fejlesztési-tesztelési környezetben fókusz Micro rehosting platform használatával

Micro fókusz Enterprise Server egyike a legnagyobb nagyszámítógépes újratárolása platformon érhető el. Használhatja azt az operációs rendszer z/számítási feladatok futtatása kevesebb költséggel jár x86 a platform az Azure-ban.

Első lépésként tekintse meg a következő cikkeket:

- [Micro fókusz Enterprise Server 4.0-s és 4.0-s vállalati fejlesztői telepítése Azure-ban](./microfocus/set-up-micro-focus-on-azure.md)
- [Állítsa be Micro fókusz CICS BankDemo Micro fókusz vállalati fejlesztői 4.0 az Azure-ban](./microfocus/demo.md)

## <a name="tmaxsoft-openframe-on-azure"></a>Az Azure-ban TmaxSoft OpenFrame

TmaxSoft OpenFrame egy népszerű nagyszámítógépes megoldás, amely megkönnyíti a meglévő nagyszámítógépes eszközök átemelése őket az Azure-ba való áthelyezését. Az Azure-ban OpenFrame környezet fejlesztési, bemutatók, tesztelési vagy éles számítási feladatok ideális.

Első lépésként töltse le a [TmaxSoft OpenFrame telepítse az Azure-ban](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/) e-könyv.

## <a name="set-up-a-devtest-environment-using-ibm-z-devtest-120"></a>IBM Z fejlesztési-tesztelési 12.0-s verzióját használó fejlesztési-tesztelési környezet kialakítása

IBM Z fejlesztési és tesztelési környezetet (IBM zD & T) állít be egy nem éles környezetben az Azure-ban, amely a fejlesztési, tesztelési és bemutatók z/OS-alapú alkalmazások is használhat.

Az emuláció környezet az Azure-on keresztül alkalmazás fejlesztők szabályozott Disztribúciók (ADCDs) Z példányok különböző is üzemeltethet. Az Azure és az Azure Stack zD & T személyes kiadás, zD & T párhuzamos Sysplex, és zD & T Enterprise Edition futtathatja.

Első lépésként tekintse meg a következő cikkeket:

-   [Állítsa be a IBM zD & T 12.0-s, az Azure-ban](./ibm/install-ibm-z-environment.md)
-   [Állítsa be a ADCD zD & T](./ibm/demo.md)

## <a name="migrate-ibm-db2-purescale-to-azure"></a>IBM DB2-höz pureScale migrálása az Azure-bA

Az IBM DB2-höz pureScale környezet egy adatbázis-fürtöt az Azure-ban magas rendelkezésre állást és méretezhetőséget, a Linux operációs rendszer biztosít. Bár ez nem azonos az eredeti környezetbe, IBM DB2-höz pureScale linuxon biztosít hasonló magas rendelkezésre állás és méretezhetőség szolgáltatások, IBM DB2-höz z/os-t, a nagyszámítógépes párhuzamos Sysplex konfigurációban futó.

Első lépésként lásd [IBM DB2-höz pureScale az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Megfontolandó szempontok

Áttelepítésekor nagyszámítógépes számítási feladatokat az Azure-infrastruktúrához szolgáltatás (IaaS) számos különböző típusú, igény szerinti, méretezhető számítási erőforrást – beleértve az Azure virtuális gépek közül választhat. Az Azure számos szolgáltatást kínál [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/overview) és [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/overview) virtuális gépeket.

### <a name="high-availability-and-failover"></a>Magas rendelkezésre állás és feladatátvétel

Az Azure kínál a kötelezettségvállalás-alapú szolgáltatásiszint-szerződések (SLA), ahol több 9-es rendelkezésre áll az alapértelmezett, optimalizált és a szolgáltatások helyi vagy a földrajzi alapú replikáció. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.

Az Azure IaaS virtuális gépek például, feladatátvételi támaszkodik a megadott rendszer funkciókat, például a Feladatátvételi fürtszolgáltatás példányok és [rendelkezésre állási csoportok](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets). Használhatja az Azure platform, a platformszolgáltatás (PaaS) erőforrások, például [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) és [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction), a platform automatikusan kezeli a feladatátvételt.

### <a name="scalability"></a>Méretezhetőség

Nagyszámítógépek általában vertikális felskálázás, felhőalapú környezet horizontális felskálázás közben. Az Azure számos szolgáltatást kínál [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) és [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) igényeknek megfelelő méretű. A felhőben is méretezhető felfelé vagy lefelé egyezés pontos felhasználói előírásoknak. Számítási, tárolási és a szolgáltatások [méretezési](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) igény szerint egy használatalapú számlázási modellben.

### <a name="storage"></a>Storage

A felhőben rugalmas, méretezhető tárolási lehetőségek rendelkezik, és hogy csak azért fizet, amit. [Az Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) az adatobjektumok nagymértékben skálázható objektumtároló, a fájlrendszer-szolgáltatását a felhőben, egy megbízható üzenetküldési tárolóban és a NoSQL-alapú tárolót biztosít. Felügyelt és nem felügyelt lemezek virtuális gépekhez, állandó és biztonságos lemezes tárolás adja meg.

### <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

A saját vész-helyreállítási hely karbantartása, lehet, hogy egy költséges javaslatának felvétele. Könnyen megvalósítható és költséghatékony lehetőségei az Azure rendelkezik [biztonsági mentési](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup), [helyreállítási](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és [redundancia](https://docs.microsoft.com/azure/storage/common/storage-redundancy) helyi és regionális szinten, vagy a geo-redundancia használatával.

## <a name="azure-government-for-mainframe-migrations"></a>Az Azure Government nagyszámítógépes áttelepítések

Helyezze át a nagyszámítógépes alkalmazások modernebb és rugalmas platform számos állami szektor entitás megismerni. A Microsoft Azure Government cloud Services, a globális a Microsoft Azure platformon, de a Szövetségi, állami és helyi kormányzati rendszerekből csomagolt egy fizikailag elkülönített példány. Világszínvonalú biztonság, védelem és a kifejezetten az Egyesült Államok kormányzati szervei és partnereik számára megfelelőségi szolgáltatásokat biztosít.

Az Azure Government elért egy ideiglenes Authority to Operate (P-ATO) a FedRAMP nagy jelentőségű rendszerek esetében, amelyek az ilyen típusú környezet. 

Első lépésként le kell töltenie [nagyszámítógépes alkalmazások a Microsoft Azure Government felhőben](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="learn-more"></a>Részletek

Ha tervezi, a széles körű nagyszámítógépes migráció [partner](partner-workloads.md) ökoszisztémája rendkívül nyújtanak segítséget. Egy partneri megoldás kiválasztásával kapcsolatban részletes útmutatásért tekintse meg a [Platform korszerűsítése Alliance](https://www.platformmodernization.org/pages/mainframe.aspx).

Lásd még:

-   [Nagyszámítógépek migrálása](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
-   [hibaelhárítással](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
-   [Az Azure-migrálás nagyszámítógépes nyelvszakértőinkből](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
