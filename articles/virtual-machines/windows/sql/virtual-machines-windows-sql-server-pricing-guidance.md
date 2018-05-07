---
title: Költségek hatékony kezelésére az SQL Server Azure virtuális gépeken futó |} Microsoft Docs
description: Ajánlott eljárásokat biztosít a megfelelő SQL Server virtuális gép fizetési modell kiválasztása.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2018
ms.author: jroth
ms.openlocfilehash: 71c86af9d4dcdf1026b4f539574b9932ef1cfc89
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Útmutató a SQL Server Azure virtuális gépek díjszabása

Ez a cikk árképzési útmutatást nyújt [SQL Server virtuális gépek](virtual-machines-windows-sql-server-iaas-overview.md) az Azure-ban. Több lehetőség, amelyek hatással vannak a költség, és fontos, hogy válassza ki a megfelelő lemezképet, hogy az üzleti követelményeinek költségek.

> [!TIP]
> Ha csak szeretné tudni, a költség becsült az SQL Server edition és a virtuálisgép-méret egyedi kombinációja, olvassa el a tarifákat tartalmazó oldalt [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) vagy [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Válassza ki a platformot, és az SQL Server jelen kiadása a **operációsrendszer-szoftver** listája.
>
> ![A virtuális gépek díjszabása lapon felhasználói felület](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Vagy használja a [árképzési Számológép](https://azure.microsoft.com/pricing/#explore-cost) hozzáadása és konfigurálása a virtuális gép. 

## <a name="free-licensed-sql-server-editions"></a>Ingyenes licenccel rendelkező SQL Server kiadásai

Fejleszthet, teszteléséhez, vagy a koncepció igazolása, majd használja a szabadon licencelt **SQL Server Developer kiadásában**. Ez a kiadás rendelkezik SQL Server Enterprise edition, hogy lehetővé teszi a összeállításához és teszteléséhez bármilyen összes funkcióját. A fejlesztői edition azonban éles környezetben nem futtatható. Egy SQL Server Developer kiadásában VM csak költségeket terhel a költség, a virtuális gép, mert nincs társítva SQL Server licencelési költségeit.

Ha éles környezetben egy egyszerűsített munkaterhelés futtatni kívánt (< 4 mag, < 1 GB memória, < 10 GB/adatbázis), használja a szabadon licencelt **SQL Server Express edition**. Egy SQL Server Express edition VM is csak költségeket terhel a virtuális gép költségét.

A fejlesztési és tesztelési célú és könnyű termelési számítási feladatokhoz is mentheti pénz válasszon, amely megfelel az ilyen terhelések kisebb Virtuálisgép-méretet. A DS1v2 bizonyos esetekben hasznos lehet.

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

## <a name="pay-per-usage"></a>Használati / kell fizetnie

**Az SQL Server licence / használati fizető** azt jelenti, hogy az Azure virtuális Gépen futó másodpercenként költsége tartalmaz-e az SQL Server licence költségét. Megtekintheti az árképzést ismertető oldalra az Azure virtuális gép számára az SQL Server különböző kiadásai (Web, Standard, Enterprise) árazása [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) vagy [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

A költség megegyezik az SQL Server (2012 SP3 2017) bármely verziója. A másodpercenként licencelési költségeit függ az VM magok száma ez az összes SQL Server licencelési szabványának.

Az SQL Server fizető / használati licencelési ajánlott:

- **Ideiglenes vagy rendszeres munkaterhelések**. Például olyan alkalmazás, amelynek támogatnia kell a esemény néhány hónappal az összes év, és üzleti elemző hétfőn.

- **Ismeretlen élettartama vagy skálája a munkaterhelések**. Például egy alkalmazást, amely nincs szükség néhány hónapon belül, vagy több vagy kevesebb számítási teljesítményt, attól függően, hogy igény szerint szükségesek.

SQL Server 2017 Azure virtuális gép létrehozása a fizetési / használati lemezképek egyike, tekintse meg a következő hivatkozásokat:

| Platform | A licenccel rendelkező lemezképek |
|---|---|
| Windows Server 2016 | [SQL Server 2017 webes Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 szabványos Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 vállalati Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 webes Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 szabványos Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 vállalati Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 webes Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 szabványos Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 vállalati Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 webes Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 szabványos Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 vállalati Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Ha egy SQL Server virtuális gép létrehozása a portálon, a **méret kiválasztása** ablak egy becsült költségét jeleníti meg. Fontos vegye figyelembe, hogy ez a becslés csak egyetlen operációs rendszer licencelési költségeit (Windows vagy külső Linux operációs rendszerek) együtt a virtuális gép futó számítási költségeket.
>
> ![Válassza ki a Virtuálisgép-méret panelen](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Nem tartalmazza a további SQL Server licencelési költségeit webes, Standard és Enterprise kiadás. Ahhoz, hogy a legpontosabb árképzési becslés, válassza ki az operációs rendszer és az SQL Server jelen kiadása a tarifákat tartalmazó oldalt a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) vagy [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="bring-your-own-license-byol"></a>Hozza magával a licencét (BYOL).

**A saját SQL Server licence kapcsolásának License Mobility keresztül**, más néven **BYOL**, azt jelenti, hogy frissítési garancia egy Azure virtuális gép egy meglévő SQL Server mennyiségi licenc használatával. Egy SQL Server használó virtuális gépek BYOL csak költségek a virtuális gép futtatásának, nem pedig az SQL Server-licenc, figyelembe véve, hogy már vásárolt licencek és frissítési garancia mennyiségi licencelési programon keresztül.

> [!NOTE]
> A BYOL képek jelenleg csak Windows virtuális gépeknél érhető el. A csak Linux virtuális gép azonban az SQL Server manuálisan telepítheti. Lásd a jelen a [Linux SQL virtuális gép – gyakori kérdések](../../linux/sql/sql-server-linux-faq.md).

Hozása a saját SQL keresztül License Mobility licencelési ajánlott:

- **Folyamatos munkaterhelések**. Például olyan alkalmazás, amelynek támogatnia kell a vállalati műveletek 24 x 7.

- **Az ismert élettartamát és a skála munkaterhelések**. Például egy alkalmazást, amely szükséges a teljes év és mely igény szerinti rendelkezik lett előrejelzett.

SQL Server virtuális gépen BYOL használatához az SQL Server Standard vagy Enterprise licenccel kell rendelkeznie, és [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), amely az egyes mennyiségi licencelési programok és egy opcionális beszerzési másokkal szükséges lehetőség. A mennyiségi licencelési programok keresztül elérhető árképzési szint változik, a szerződés és a mennyiség és vagy SQL Server elkötelezettségének típusa alapján. De a szokásos megoldás, mint a saját licenc folyamatos termelési számítási feladatokhoz mihamarabb elérhetővé tenni a következő előnyöket biztosítja:

| BYOL juttatás | Leírás |
|-----|-----|
| **Költségmegtakarítások** | Hozása a saját SQL Server licence költséghatékonyabb megoldás, mint fizető / használata, ha egy munkaterhelés folyamatosan fut az SQL Server Standard vagy Enterprise a *10 hónapnál*. |
| **Hosszú távú megtakarítások** | Átlagosan van *30 % olcsóbb évente* vásárolni, vagy egy SQL Server licence megújítása esetén az első három év. Ezenkívül három év, után nem kell többé újítsa meg a licenc, frissítési garancia ugyanúgy kell fizetnie. Ezen a ponton van *200 % olcsóbb*. |
| **Szabad passzív másodlagos replika** | Egy másik előnye, hogy a saját licenc a [szabad passzív egy másodlagos másodpéldány a licencelés](https://azure.microsoft.com/pricing/licensing-faq/) egy SQL-kiszolgálón a magas rendelkezésre állás érdekében. Ez a magas rendelkezésre állású SQL Server telepítését (például az Always On rendelkezésre állási csoportok) fele a licencelési költségek kivágása. A passzív másodlagos futtatásához a jogosultságokat kapnak a feladatátvételi kiszolgálók Software Assurance juttatás keresztül. |

SQL Server 2017 Azure virtuális gép létrehozása a bring your-saját-licencet lemezképek egyike, olvassa el a virtuális gépek előtagként a "{BYOL}":

- [SQL Server 2017 vállalati Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 szabványos Azure virtuális gép](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Ossza meg velünk 10 napon belül hány SQL Server-licencet használ az Azure-ban. Az előző lemezképek mutató hivatkozások utasításokat ennek rendelkezik.

> [!NOTE]
> Már nem lehet módosítani egy fizetési másodpercenkénti saját licenc használatához SQL Server virtuális gép licencelési modelljét. Jelen esetben létre kell hoznia egy új BYOL virtuális gépet, majd az új virtuális gépre kell telepítenie az adatbázisokat.

## <a name="reduce-costs"></a>Csökkentheti a költségeket

Felesleges költségek elkerülése érdekében válassza ki az optimális virtuális gép méretét, és vegye figyelembe a nem folyamatos munkaterhelések szakaszos leállítások.

### <a id="machinesize"></a> A virtuális gép megfelelő méretezés

Az SQL Server licencelési költségeit közvetlenül kapcsolódik az magok száma. Válassza ki a Processzor, memória, tárolási és i/o-sávszélesség várt igényeinek megfelelő Virtuálisgép-méretet. Lehetséges gépméretet teljes listáját lásd: [Windows Virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) és [Linux Virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Nincsenek új méreteket jól használható bizonyos típusú SQL Server számítási feladatait. Ezek a gépek méretű memória, tárolási és i/o-sávszélesség magas szintű karbantartása, de egy alacsonyabb virtualizált magok száma rendelkeznek. Vegyük példaként a következő példa:

| Virtuális gép mérete | Virtuális magok | Memory (Memória) | Maximális lemezek | A maximális i/o-teljesítmény | SQL-licencelési költségeit. | A teljes költségek (számítási + licencelés) |
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

## <a name="next-steps"></a>További lépések

Általános Azure díjszabása útmutatásért lásd: [Azure számlázás és költség felügyeleti váratlan költségek megakadályozása](../../../billing/billing-getting-started.md). A legújabb virtuális gépek díjszabása, beleértve az SQL Server, lásd: az Azure virtuális gép Azure díjszabása a [Windows virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) és [Linux virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Azure virtuális gépeken futó SQL Server áttekintéséhez tekintse meg a következő cikkeket:

- [Az SQL Server a Windows virtuális gépek – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md)
- [SQL Server a Linux virtuális gépek – áttekintés](../../linux/sql/sql-server-linux-virtual-machines-overview.md)