---
title: Mainframe rehosting az Azure virtuális gépeken
description: A nagyszámítógépes számítási feladatok, például az IBM Z-alapú rendszerek virtuális gépeket (VM-eket) használó, a Microsoft Azure-ban.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: ccdeefabeedfca4959239696361ccce0bc6c1c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76289798"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframe rehosting az Azure virtuális gépeken

A nagyszámítógépes környezetekből a felhőbe való áttelepítése lehetővé teszi az infrastruktúra modernizálását és gyakran a költségek megtakarítását. Rengeteg számítási feladat vihető át Az Azure-ba csupán kisebb kódmódosítással, például az adatbázisok nevének frissítésével.

Általánosságban elmondható, hogy a *nagyszámítógép* kifejezés nagy számítógépes rendszert jelent. Pontosabban, a jelenleg használt túlnyomó többsége IBM System Z kiszolgáló vagy IBM plug-kompatibilis rendszerek, amelyek MVS, DOS, VSE, OS/390 vagy z/OS rendszereket futtatnak.

Egy Azure virtuális gép (VM) egy adott alkalmazás erőforrásainak elkülönítésére és kezelésére szolgál egyetlen példányban. Nagyszámítógépek, például az IBM z/OS logikai partíciókat (LPARS) használnak erre a célra. A nagyszámítógép egy LPAR-t használhat a kapcsolódó COBOL-programokkal rendelkező CICS-régióhoz, és egy külön LPAR-t az IBM Db2 adatbázishoz. Egy tipikus [n-tier alkalmazás az Azure-ban](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) üzembe helyezi az Azure virtuális gépek egy virtuális hálózat, amely az egyes rétegek alhálózatokra szegmentálható.

Az Azure virtuális gépek nagyszámítógépes emulációs környezeteket és lefordítókat futtathatnak, amelyek támogatják a növekedési és -shift forgatókönyveket. A fejlesztés és tesztelés gyakran az elsők között vannak, amelyek nagyszámítógépről Azure fejlesztési/tesztelési környezetbe vándorolnak. Az emulálható gyakori kiszolgálóösszetevők közé tartoznak az online tranzakciós folyamatok (OLTP), a kötegek és az adatbetöltési rendszerek, ahogy az alábbi ábrán látható.

![Az Azure-beli emulációs környezetek lehetővé teszik a z/OS alapú rendszerek futtatását.](media/01-overview.png)

Egyes nagyszámítógépes számítási feladatok viszonylag könnyen áttelepíthetők az Azure-ba, míg mások egy partnermegoldás használatával újraüzemeltethetők az Azure-ban. A partnermegoldás kiválasztásával kapcsolatos részletes útmutatásért az [Azure Mainframe Migration center](https://azure.microsoft.com/migration/mainframe/) segíthet.

## <a name="mainframe-migration"></a>Nagyszámítógép migrálása

Újrafogadó, újjáépíteni, cserélni, vagy nyugdíjba? IaaS vagy PaaS? A mainframe-alkalmazás megfelelő áttelepítési stratégiájának meghatározásához tekintse meg a [Mainframe-áttelepítési](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) útmutatót az Azure Architecture Centerben.

## <a name="micro-focus-rehosting-platform"></a>Micro Focus rehosting platform

Micro Focus Enterprise Server az egyik legnagyobb mainframe rehosting platformok állnak rendelkezésre. Használhatja a z/OS számítási feladatok futtatásához egy olcsóbb x86-os platformon az Azure-ban.

Első lépések:

- [Az Enterprise Server és az Enterprise Developer telepítése az Azure-ban](./microfocus/set-up-micro-focus-azure.md)
- [A CICS BankDemo vállalati fejlesztői beállítása az Azure-ban](./microfocus/demo.md)
- [Nagyvállalati kiszolgáló futtatása Docker-tárolóban az Azure-ban](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame az Azure-ban

TmaxSoft OpenFrame egy népszerű mainframe rehosting megoldás használt lift-and-shift forgatókönyvek. Az Azure-beli OpenFrame-környezet fejlesztési, bemutatók, tesztelési vagy éles számítási feladatok kezelésére alkalmas.

Első lépések:

- [Ismerkedés a TmaxSoft OpenFrame-szel](./tmaxsoft/get-started.md)
- [Töltse le az ebook](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

Az IBM Z fejlesztési és tesztelési környezet (IBM zD&T) nem éles környezetet hoz létre az Azure-ban, amelyet a z/OS alapú alkalmazások fejlesztéséhez, teszteléséhez és bemutatóihoz használhat.

Az Azure-beli emulációs környezet különböző típusú Z-példányokat üzemeltethet az Alkalmazásfejlesztők által ellenőrzött disztribúciókon (ADCD) keresztül. Futtathatja a zD&T Personal Edition, zD&T Parallel Sysplex és zD&T Enterprise Edition az Azure és az Azure Stack.

Első lépések:

- [Az IBM zD&t 12.0 beállítása az Azure-ban](./ibm/install-ibm-z-environment.md)
- [AD beállítása zD&T-en](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale az Azure-ban

Az IBM DB2 pureScale környezet egy adatbázis-fürtöt biztosít az Azure számára. Nem azonos az eredeti környezettel, de hasonló rendelkezésre állást és skálázást biztosít, mint az IBM DB2 a párhuzamos Sysplex telepítésben futó z/OS-hez.

Első lépésekért tekintse meg [az IBM DB2 pureScale az Azure-ban.](/azure/virtual-machines/linux/ibm-db2-purescale-azure)

## <a name="considerations"></a>Megfontolandó szempontok

Amikor a nagyszámítógépes számítási feladatokat szolgáltatásként (IaaS) telepíti át az Azure-infrastruktúrába, többféle igény szerinti, méretezhető számítástechnikai erőforrás közül választhat, beleértve az Azure virtuális gépeket is. Az Azure [számos Linux](/azure/virtual-machines/linux/overview) és [Windows](/azure/virtual-machines/windows/overview) virtuális gépet kínál.

### <a name="compute"></a>Compute

Az Azure számítási teljesítménye kedvezően viszonyul a nagyszámítógép kapacitásához. Ha egy nagyszámítógépes számítási feladatot az Azure-ba szeretne áthelyezhetővé, hasonlítsa össze a másodpercenként egymillió utasítás (MIPS) nagyszámítógépes metrikáját a virtuális CPU-khoz. 

Ismerje meg, hogyan helyezheti át a [nagyszámítógépes számítást az Azure-ba.](./concepts/mainframe-compute-azure.md)

### <a name="high-availability-and-failover"></a>Magas rendelkezésre állás és feladatátvétel

Az Azure kötelezettségvállalásalapú szolgáltatásiszint-szerződéseket (SLA-kat) kínál. A több kilences rendelkezésre állás az alapértelmezett, és az SL-ek optimalizálhatók a szolgáltatások helyi vagy geoalapú replikációjával. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.

Az Azure IaaS, például egy virtuális gép, adott rendszerfüggvények feladatátvételi támogatást nyújtanak, például a feladatátvevő fürtözési példányok és a rendelkezésre állási csoportok. Ha az Azure platformot szolgáltatásként (PaaS) használja, a platform automatikusan kezeli a feladatátvételt. Ilyen például az [Azure SQL Database](/azure/sql-database/sql-database-technical-overview) és az Azure [Cosmos DB.](/azure/cosmos-db/introduction)

### <a name="scalability"></a>Méretezhetőség

A nagyszámítógépek általában felskálázva vannak, míg a felhőalapú környezetek horizontálisan kivannak. Az Azure számos [Linux-](/azure/virtual-machines/linux/sizes) és [Windows-méretet](/azure/virtual-machines/windows/sizes) kínál az igényeinek megfelelően. A felhő is skálázódik fel-le, hogy megfeleljen a pontos felhasználói specifikációk. A számítási teljesítmény, a tárolás és a szolgáltatások igény szerint [skálázható](/azure/architecture/best-practices/auto-scaling) a használatalapú számlázási modell szerint.

### <a name="storage"></a>Storage

A felhőben rugalmas, méretezhető tárolási lehetőségek széles választéka áll rendelkezésre, és csak azért kell fizetnie, amire szüksége van. [Az Azure Storage](/azure/storage/common/storage-introduction) egy nagymértékben méretezhető objektumtárolót kínál az adatobjektumokhoz, egy fájlrendszer-szolgáltatást a felhőhöz, egy megbízható üzenettárolót és egy NoSQL-tárolót. A virtuális gépek esetében a felügyelt és a nem felügyelt lemezek állandó, biztonságos lemeztárolást biztosítanak.

Ismerje meg, hogyan helyezheti át a [nagyszámítógépes tárhelyet az Azure-ba.](./concepts/mainframe-storage-azure.md)

### <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

Fenntartása a saját vész-helyreállítási hely költséges lehet. Az Azure könnyen megvalósítható és költséghatékony lehetőségeket kínál a [biztonsági mentéshez,](/azure/backup/backup-introduction-to-azure-backup) [a helyreállításhoz](/azure/site-recovery/site-recovery-overview)és [a redundanciához](/azure/storage/common/storage-redundancy) helyi vagy regionális szinten, illetve a földrajzi redundancia révén.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government nagyszámítógépes áttelepítésekhez

Számos közszektorbeli szervezet szívesen helyezné át nagyszámítógépes alkalmazásait egy modernebb, rugalmasabb platformra. A Microsoft Azure Government a globális Microsoft Azure platform fizikailag elkülönített példánya, amely szövetségi, állami és helyi kormányzati rendszerekhez van csomagolva. Világszínvonalú biztonsági, védelmi és megfelelőségi szolgáltatásokat nyújt kifejezetten az Egyesült Államok kormányzati szervei és partnerei számára.

Az Azure Government ideiglenes működési felhatalmazást (P-ATO) szerzett a FedRAMP Nagy hatású rendszerekhez, amelyeknek szükségük van az ilyen típusú környezetre.

Első lépésekhez töltse le a [Microsoft Azure Government felhőt nagyszámítógépes alkalmazásokhoz.](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)

## <a name="next-steps"></a>További lépések

Kérje meg [partnereinket,](partner-workloads.md) hogy segítsenek áttelepíteni vagy újraüzemeltetni a nagyszámítógépes alkalmazásokat. 

Lásd még:

- [Tanulmányok a nagyszámítógépes témákról](mainframe-white-papers.md)
- [Nagyszámítógép migrálása](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [hibaelhárítással](/azure/virtual-machines/troubleshooting/)
- [Nagyszámítógép demystifying az Azure-migráláshoz](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
