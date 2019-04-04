---
title: A nagyszámítógépes újratárolása az Azure-beli virtuális gépeken
description: Áthelyezési a nagyszámítógépes számítási feladatok, például az IBM Z-alapú rendszerekben a Microsoft Azure virtuális gépeken (VM) használatával.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 8b7c2a088dc917c319acf6cad251ad53367a14b6
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895037"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>A nagyszámítógépes újratárolása az Azure-beli virtuális gépeken

A munkaterhelések áttelepítését a nagyszámítógépes környezeteket a felhőben lehetővé teszi, hogy korszerűsítheti infrastruktúráját, és gyakran költséget takaríthat meg. Számos számítási feladatok kis kód módosítása, például adatbázisok nevei frissítése továbbíthatók az Azure-bA.

Általában a beszéd, a kifejezés *nagyszámítógépes* azt jelenti, hogy nagy számítógép. Pontosabban a túlnyomó jelenleg használatban, IBM rendszer Z kiszolgálók vagy az IBM plug-compatible rendszerek MVS, DOS, VSE, az operációs rendszer/390 vagy z/OS rendszerű.

Egy Azure virtuális gép (VM) segítségével azonosíthatók és felügyelheti az erőforrásokat egy adott alkalmazáshoz egyetlen példányán. Például a z/operációs rendszer IBM Nagyszámítógépek logikai partíció (LPARS) használni erre a célra. A nagyszámítógépes IBM Db2-adatbázishoz használható egy LPAR társított COBOL programokkal CICS régió, és a egy külön LPAR. Egy tipikus [n szintű alkalmazáshoz az Azure-ban](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) üzembe helyezi az Azure virtuális gépeket, amelyek az egyes rétegek alhálózatait osztható virtuális hálózatban.

Az Azure virtuális gépek fordítóprogramot lift-and-shift-forgatókönyveket támogató és nagyszámítógépes emulációs környezetben futtathatja. Fejlesztési és tesztelési célra általában az első munkaterhelések a nagyszámítógépes áttelepíthetők az Azure fejlesztési-tesztelési környezet között. Közös server-összetevőjét, Emulálhatja online tranzakció-folyamat (OLTP), a batch és az adatok feldolgozási rendszerek tartalmazza az alábbi ábrán látható módon.

![Emulációs környezetek Azure-beli lehetővé teszi z/OS-alapú rendszerek futtatására.](media/01-overview.png)

Egyes nagyszámítógépes számítási feladatok áttelepíthetők az Azure-bA viszonylag könnyen, míg mások is rehosted, Azure-ban egy partneri megoldás használatával. Részletes útmutatás az egy partneri megoldás választása a [Azure nagyszámítógépes Migrálási központ](https://azure.microsoft.com/migration/mainframe/) segítségével.

## <a name="mainframe-migration"></a>Nagyszámítógép migrálása

Áthelyezési, építse újra, cseréje vagy kivonása? IaaS vagy PaaS? A nagyszámítógépes alkalmazások a megfelelő migrálási stratégiát megállapításához lásd: a [nagyszámítógépes migráció](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) az útmutató az Azure Architecture Centert.

## <a name="micro-focus-rehosting-platform"></a>Micro fókusz újratárolása platform

Micro fókusz Enterprise Server egyike a legnagyobb nagyszámítógépes újratárolása platformon érhető el. Használhatja azt az operációs rendszer z/számítási feladatok futtatása kevesebb költséggel jár x86 a platform az Azure-ban.

Első lépések:

- [Telepítse a vállalati kiszolgáló és a nagyvállalati fejlesztő az Azure-ban](./microfocus/set-up-micro-focus-azure.md)
- [Az Azure nagyvállalati fejlesztő CICS BankDemo beállítása](./microfocus/demo.md)
- [Vállalati kiszolgáló futtatása Docker-tárolóban az Azure-ban](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>Az Azure-ban TmaxSoft OpenFrame

TmaxSoft OpenFrame lift-and-shift esetekben használható népszerű nagyszámítógépes rehosting megoldás is. Az Azure-ban OpenFrame környezet fejlesztési, bemutatók, tesztelési vagy éles számítási feladatok ideális.

Első lépések:

- [TmaxSoft OpenFrame használatának első lépései](./tmaxsoft/get-started.md)
- [Az e-könyv letöltése](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z fejlesztési és tesztelési környezetet (IBM zD & T) állít be egy nem éles környezetben az Azure-ban, amely a fejlesztési, tesztelési és bemutatók z/OS-alapú alkalmazások is használhat.

Az emuláció környezet az Azure-ban a különböző típusú alkalmazás fejlesztők szabályozott Disztribúciók (ADCDs) keresztül Z példányok is üzemeltethet. Az Azure és az Azure Stack zD & T személyes kiadás, zD & T párhuzamos Sysplex, és zD & T Enterprise Edition futtathatja.

Első lépések:

- [Állítsa be a IBM zD & T 12.0-s, az Azure-ban](./ibm/install-ibm-z-environment.md)
- [Állítsa be a ADCD zD & T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2-höz pureScale az Azure-ban

Az IBM DB2-höz pureScale környezet egy adatbázis-fürtöt az Azure biztosít. Nem azonos az eredeti környezetbe, de biztosít hasonló rendelkezésre állást és méretezést IBM DB2-höz, a – z/OS egy párhuzamos Sysplex telepítőjének futtatása.

Első lépésként lásd [IBM DB2-höz pureScale az Azure-ban](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Megfontolandó szempontok

Áttelepítésekor nagyszámítógépes számítási feladatokat az Azure-infrastruktúrához szolgáltatás (IaaS) számos különböző típusú, igény szerinti, méretezhető számítási erőforrást – beleértve az Azure virtuális gépek közül választhat. Az Azure számos szolgáltatást kínál [Linux](/azure/virtual-machines/linux/overview) és [Windows](/azure/virtual-machines/windows/overview) virtuális gépeket.

### <a name="compute"></a>Compute

Az Azure számítási teljesítmény a nagyszámítógépes kapacitás favorably hasonlítja össze. Ha szeretne áttérni a nagyszámítógépes számítási feladatok áthelyezése az Azure-ba, hasonlítsa össze a nagyszámítógépes metrika egymillió másodpercenként (MIPS) virtuális CPU-kra. 

Ismerje meg, hogyan [nagyszámítógépes számítási áthelyezése az Azure-bA](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Magas rendelkezésre állás és feladatátvétel

Az Azure a kötelezettségvállalás-alapú szolgáltatásiszint-szerződések (SLA) kínál. Több-nines rendelkezésre állás az alapértelmezett, és SLA-k is lehet optimalizálni a szolgáltatások helyi vagy a földrajzi alapú replikáció. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.

Az Azure IaaS például egy virtuális Gépet, a rendszer adott funkciók gondoskodhat a feladatátvétel támogatásáról – például a Feladatátvételi fürtszolgáltatás példányok és [rendelkezésre állási csoportok](/azure/virtual-machines/windows/regions-and-availability#availability-sets). Az Azure platform a platformszolgáltatás (PaaS) erőforrásként használatakor a platform automatikusan kezeli a feladatátvevő. Ilyenek például [Azure SQL Database](/azure/sql-database/sql-database-technical-overview) és [Azure Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Méretezhetőség

Nagyszámítógépek általában vertikális felskálázás, felhőalapú környezetekben horizontális felskálázás közben. Az Azure számos szolgáltatást kínál [Linux](/azure/virtual-machines/linux/sizes) és [Windows](/azure/virtual-machines/windows/sizes) igényeknek megfelelő méretű. A felhőben is méretezhető felfelé vagy lefelé egyezés pontos felhasználói előírásoknak. Számítási, tárolási és a szolgáltatások [méretezési](/azure/architecture/best-practices/auto-scaling) igény szerint egy használatalapú számlázási modellben.

### <a name="storage"></a>Storage

A felhőben rugalmas, méretezhető tárolási lehetőségek rendelkezik, és hogy csak azért fizet, amit. [Az Azure Storage](/azure/storage/common/storage-introduction) az adatobjektumok nagymértékben skálázható objektumtároló, a fájlrendszer-szolgáltatását a felhőben, egy megbízható üzenetküldési tárolóban és a NoSQL-alapú tárolót biztosít. Felügyelt és nem felügyelt lemezek virtuális gépekhez, állandó és biztonságos lemezes tárolás adja meg.

Ismerje meg, hogyan [nagyszámítógépes tár áthelyezése az Azure-bA](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

A saját vész-helyreállítási hely karbantartása, lehet, hogy egy költséges javaslatának felvétele. Könnyen megvalósítható és költséghatékony lehetőségei az Azure rendelkezik [biztonsági mentési](/azure/backup/backup-introduction-to-azure-backup), [helyreállítási](/azure/site-recovery/site-recovery-overview), és [redundancia](/azure/storage/common/storage-redundancy) helyi és regionális szinten, vagy a geo-redundancia használatával.

## <a name="azure-government-for-mainframe-migrations"></a>Az Azure Government nagyszámítógépes áttelepítések

Helyezze át a nagyszámítógépes alkalmazások modernebb és rugalmas platform számos állami szektor entitás megismerni. A Microsoft Azure Government a globális a Microsoft Azure platform egy fizikailag elkülönített példányát – Szövetségi, állami és helyi önkormányzati rendszerek csomagolva. Világszínvonalú biztonság, védelem és a kifejezetten az Egyesült Államok kormányzati szervei és partnereik számára megfelelőségi szolgáltatásokat biztosít.

Az Azure Government elért egy ideiglenes Authority to Operate (P-ATO) a FedRAMP nagy jelentőségű rendszerek esetében, amelyek az ilyen típusú környezet.

Első lépésként le kell töltenie [nagyszámítógépes alkalmazások a Microsoft Azure Government felhőben](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>További lépések

Kérje meg a [partnerek](partner-workloads.md) , segítséget a nagyszámítógépes alkalmazások újratárolásához. Egy partneri megoldás kiválasztásával kapcsolatban részletes útmutatásért lásd a [Platform korszerűsítése Alliance](https://www.platformmodernization.org/pages/mainframe.aspx) webhelyén.

Lásd még:

- [A nagyszámítógépes témákról tanulmányok](mainframe-white-papers.md)
- [Nagyszámítógép migrálása](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Hibaelhárítás](/azure/virtual-machines/troubleshooting/)
- [Nagyszámítógépek migrálása az Azure-ba egyszerűen](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
