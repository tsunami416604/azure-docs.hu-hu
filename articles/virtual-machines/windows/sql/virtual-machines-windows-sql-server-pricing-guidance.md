---
title: "Költségek hatékony kezelésére az SQL Server Azure virtuális gépeken futó |} Microsoft Docs"
description: "Ajánlott eljárásokat biztosít a megfelelő SQL Server virtuális gép fizetési modell kiválasztása."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/17/2017
ms.author: jroth
ms.openlocfilehash: 391f30e0c81aeaf313d58f1f4af877b5be9ed919
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Útmutató a SQL Server Azure virtuális gépek díjszabása

Ez a cikk árképzési útmutatást az SQL Server Azure virtuális gép. Több lehetőség, amelyek hatással vannak a költség, és fontos, hogy válassza ki a megfelelő lemezképet, hogy az üzleti követelményeinek költségek.

## <a name="free-licensed-sql-server-editions"></a>Ingyenes licenccel rendelkező SQL Server kiadásai

Fejleszthet, teszteléséhez, vagy a koncepció igazolása, majd használja az ingyenes licenccel rendelkező **SQL Server Developer kiadásában**. Ez a kiadás mindent tartalmaz, az SQL Server Enterprise edition, így használhatja bármilyen kívánt alkalmazás létrehozásához. Azt csak nem engedélyezte, hogy éles környezetben futtatni. Egy SQL Server Developer VM csak költségek, a nem SQL Server licencelési költségeit, a virtuális gép.

Ha éles környezetben egy egyszerűsített munkaterhelés futtatni kívánt (< 4 mag, < 1 GB memória, < 10 GB/adatbázis), majd használja az ingyenes licenccel rendelkező **SQL Server Express edition**. Egy SQL Express virtuális Gépet a virtuális gép költségét csak nem elszámolás SQL licencelési.

A következő fejlesztési és tesztelési célú, illetve egyszerűsített termelési számítási feladatokhoz is mentheti pénz válasszon, amely megfelel az ilyen terhelések kisebb Virtuálisgép-méretet. A DS1v2 előfordulhat, hogy az ilyen terhelések érdemes választani.

SQL Server 2017 Azure virtuális gép létrehozása egy ezeket a lemezképeket, tekintse meg a következő hivatkozásokat:

| Platform | A licenccel rendelkező szabadon lemezképek |
|---|---|
| Windows Server 2016 | [SQL Server 2017 fejlesztői Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express Azure virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 fejlesztői Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express Azure virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 fejlesztői Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express Azure virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 fejlesztői Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express Azure virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Fizetett SQL Server kiadásai

Ha egy nem lightweight éles munkaterhelés, használja a következő SQL Server verzióinak egyikét:

| SQL Server Edition | Számítási feladat |
|-----|-----|
| Web | Kis webhelyek |
| Standard | Kis-és közepes méretű munkaterhelések |
| Enterprise | Nagy méretű vagy kritikus fontosságú munkaterhelésekhez|

Az SQL Server licencelési ezeknek a kiadásoknak díj ellenében két lehetősége van: *kell fizetnie, amennyit egy használati* vagy *saját licenc (használata BYOL)*.

### <a name="pay-per-usage"></a>Használati / kell fizetnie

**Az SQL Server licence / használati fizető** azt jelenti, hogy az Azure virtuális Gépen futó perc költsége tartalmaz-e az SQL Server licence költségét. Látható az az SQL Server különböző kiadásai (Web, Standard, Enterprise) díjszabása a [Azure virtuális gép árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard). A költség megegyezik az SQL Server (2012 SP3 2017) bármely verziója. Csakúgy, mint az SQL Server általában licencelés, a percalapú licencelési költségeit VM magok száma függ.

Az SQL Server fizető / használati licencelési ajánlott:

- Ideiglenes vagy rendszeres munkaterhelések. Például olyan alkalmazás, amelynek támogatnia kell a esemény néhány hónappal az összes év, és üzleti elemző hétfőn.
- Ismeretlen élettartama vagy skálája a munkaterhelések. Például egy alkalmazást, amely nincs szükség néhány hónapon belül, vagy több vagy kevesebb számítási teljesítményt, attól függően, hogy igény szerint szükségesek.

SQL Server 2017 Azure virtuális gép létrehozása a fizetési / használati lemezképek egyike, tekintse meg a következő hivatkozásokat:

| Platform | A licenccel rendelkező lemezképek |
|---|---|
| Windows Server 2016 | [SQL Server 2017 webes Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 szabványos Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 vállalati Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 webes Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 szabványos Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 vállalati Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 webes Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 szabványos Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 vállalati Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 webes Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 szabványos Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 vállalati Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Ha egy SQL Server virtuális gép létrehozása a portálon, a **méret kiválasztása** ablak egy becsült költségét jeleníti meg. Fontos vegye figyelembe, hogy ez a becslés csak licencelési költségeit Windows virtuális gépek időszakokon együtt a virtuális gép futtatási számítási költségeket. Nem tartalmazza a további SQL Server licencelési költségeit webes, Standard és Enterprise kiadás. Azt is nem tartalmaz külső Linux operációs rendszerek további licencelési költségekkel Linux virtuális gépekhez. Ahhoz, hogy a legpontosabb árképzési becslés, válassza ki az operációs rendszer és az SQL Server jelen kiadása a tarifákat tartalmazó oldalt a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) és [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).
>
> ![Válassza ki a Virtuálisgép-méret panelen](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)

### <a name="bring-your-own-license-byol"></a>Hozza magával a licencét (BYOL).

**A saját SQL Server licence kapcsolásának License Mobility keresztül**, más néven **BYOL**, azt jelenti, hogy frissítési garancia egy Azure virtuális gép egy meglévő SQL Server mennyiségi licenc használatával. Egy SQL Server használó virtuális gépek BYOL csak költségek a virtuális gép futtatásának, nem pedig az SQL Server-licenc, figyelembe véve, hogy már vásárolt licencek és frissítési garancia mennyiségi licencelési programon keresztül.

> [!NOTE]
> A BYOL képek jelenleg csak Windows virtuális gépeknél érhető el. A csak Linux virtuális gép azonban az SQL Server manuálisan telepítheti. Lásd a jelen a [Linux SQL virtuális gép – gyakori kérdések](../../linux/sql/sql-server-linux-faq.md).

Hozása a saját SQL keresztül License Mobility licencelési ajánlott:

- Folyamatos munkaterhelések. Például olyan alkalmazás, amelynek támogatnia kell a vállalati műveletek 24 x 7.
- Az ismert élettartamát és a skála munkaterhelések. Például egy alkalmazást, amely szükséges a teljes év és mely igény szerinti rendelkezik lett előrejelzett.

BYOL használandó SQL Server virtuális gépen, az SQL Server Standard vagy Enterprise licenccel kell rendelkeznie és [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), amely az egyes keresztül szükséges lehetőség [mennyiségi licencelés](https://www.microsoft.com/en-us/download/details.aspx?id=10585) programok és egy nem kötelező vásárolja meg másokkal.  A mennyiségi licencelési programok keresztül elérhető árképzési szint változik, a szerződés és a mennyiség és vagy SQL Server elkötelezettségének típusa alapján. De a szokásos megoldás, mint a saját licenc folyamatos termelési számítási feladatokhoz mihamarabb elérhetővé tenni a következő előnyöket biztosítja:

| BYOL juttatás | Leírás |
|-----|-----|
| **Költségmegtakarítások** | Hozása a saját SQL Server licence költséghatékonyabb megoldás, mint fizető / használata, ha egy munkaterhelés folyamatosan fut az SQL Server Standard vagy Enterprise a *10 hónapnál*. |
| **Hosszú távú megtakarítások** | Átlagosan van *30 % olcsóbb évente* vásárolni, vagy egy SQL Server licence megújítása esetén az első három év. Ezenkívül három év, után nem kell többé újítsa meg a licenc, frissítési garancia ugyanúgy kell fizetnie. Ezen a ponton van *200 % olcsóbb*. |
| **Szabad passzív másodlagos replika** | Egy másik előnye, hogy a saját licenc a [szabad passzív egy másodlagos másodpéldány a licencelés](https://azure.microsoft.com/pricing/licensing-faq/) egy SQL-kiszolgálón a magas rendelkezésre állás érdekében. Ez a magas rendelkezésre állású SQL Server telepítését (például az Always On rendelkezésre állási csoportok) fele a licencelési költségek kivágása. A passzív másodlagos futtatásához a jogosultságokat kapnak a feladatátvételi kiszolgálók Software Assurance juttatás keresztül. |

SQL Server 2016 Azure virtuális gép valamelyik, a bring your-saját-licencet lemezképek létrehozásához lásd: a virtuális gépek előtagként a "{BYOL}":

- [SQL Server 2016 Enterprise Azure virtuális Gépen](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016)
- [SQL Server 2016 szabványos Azure virtuális gép](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)

> [!NOTE]
> Ossza meg velünk 10 napon belül hány fogja használni az Azure SQL Server-licencet. Az előző lemezképek mutató hivatkozások utasításokat ennek rendelkezik.

## <a name="avoid-unnecessary-costs"></a>Felesleges költségek elkerülése érdekében

Felesleges költségek elkerülése érdekében válassza ki az optimális virtuális gép méretét, és vegye figyelembe a nem folyamatos munkaterhelések szakaszos leállítások.

### <a id="machinesize"></a>A virtuális gép megfelelő méretezés

Az SQL Server licencelési költségeit közvetlenül kapcsolódik az magok száma. Válassza ki a Processzor, memória, tárolási és i/o-sávszélesség várt igényeinek megfelelő Virtuálisgép-méretet. Lehetséges gépméretet teljes listáját lásd: [Windows Virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) és [Linux Virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Nincsenek új méreteket jól használható bizonyos típusú SQL Server számítási feladatait. Ezek a gépek méretű memória, tárolási és i/o-sávszélesség magas szintű karbantartása, de egy alacsonyabb virtualizált magok száma rendelkeznek. Vegyük példaként a következő példa:

| Virtuálisgép-mérettel | Vcpu | Memory (Memória) | Maximális lemezek | A maximális i/o-teljesítmény | SQL-licencelési költségeit. | A teljes költségek (számítási + licencelés) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51,200 IOPS vagy 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51,200 IOPS vagy 768 MB/s | 75 %-kal alacsonyabb | 57 %-kal alacsonyabb |

> [!IMPORTANT]
> Itt látható egy időpontban példa. A legutóbbi előírások, tekintse meg a gép méretét a cikkek és az árképzést ismertető oldalra az Azure [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) és [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Az előző példában láthatja, hogy a specifikációk **Standard_DS14v2** és **Standard_DS14-4v2** Vcpu kivételével azonos. A utótag **-4v2** végén a **Standard_DS14-4v2** gépméretet aktív Vcpu számát jelzi. SQL Server licencelési költségei magok száma vannak társítva, mert jelentősen csökkenti a költségeket a virtuális gép forgatókönyvekben, ahol a felesleges Vcpu nem szükségesek. Itt látható egy példa, és a korlátozott Vcpu, amelyek a utótag mintával sok méreteket. További információkért lásd a következő blogbejegyzésben [több költséghatékony adatbázis munkát az új Azure Virtuálisgép-méretek bejelentése](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Állítsa le a virtuális Géphez, amikor lehetséges

Ha a futó alkalmazások és szolgáltatások nem folyamatosan használ, fontolja meg, inaktív időszakokban a virtuális gép leállítása. A fizetés használat alapján történik.

Például ha egyszerűen próbálja ki az SQL Server egy Azure virtuális gépen, akkor nem kívánt függő díj terheli távozó véletlenül hétig futnak. Egy megoldást a [automatikus leállítási szolgáltatás](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![SQL virtuális gép autoshutdown](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Automatikus leállítási része által nyújtott hasonló szolgáltatásokat nagyobb mennyiségű [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Az egyéb munkafolyamatok, fontolja meg automatikusan leállítani, és Azure virtuális gépek újraindításával és a parancsfájl-kezelési megoldás, mint [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Leállítás és felszabadítás, a virtuális gép egyetlen lehetséges módja elkerülése érdekében. Egyszerűen leállítása vagy az Energiagazdálkodási lehetőségek használatával állítsa le a virtuális gép továbbra is terhel használat.

## <a name="next-steps"></a>Következő lépések

Általános Azure díjszabása útmutatásért lásd: [Azure számlázás és költség felügyeleti váratlan költségek megakadályozása](../../../billing/billing-getting-started.md).

A legújabb virtuális gépek díjszabása, beleértve az SQL Server, lásd: a [Azure virtuális gép árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard).

További információ az SQL Server virtuális gépek mindkét [SQL Server Windows virtuális gépek](virtual-machines-windows-sql-server-iaas-overview.md) és [SQL Server Linux virtuális gépek](../../linux/sql/sql-server-linux-virtual-machines-overview.md).
