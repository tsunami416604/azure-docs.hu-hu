---
title: Költségek kezelése hatékonyan SQL Serverhez készült Azure-beli virtuális gépeken |} A Microsoft Docs
description: Ajánlott eljárásokat biztosít a megfelelő SQL Server virtuális gép díjszabási modelljét választja.
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
ms.date: 07/02/2018
ms.author: jroth
ms.openlocfilehash: e9033724f62b383ce70488b98a3a8919e3cb198a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345277"
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Az SQL Server Azure virtuális gépek díjszabási útmutatóját

Ez a cikk útmutatást díjszabási [SQL Servert futtató virtuális gépek](virtual-machines-windows-sql-server-iaas-overview.md) az Azure-ban. Számos beállítás, amelyek befolyásolják a költségeket, és fontos, hogy válassza ki a megfelelő lemezképet, amely elosztja a költségek az üzleti igényekhez.

> [!TIP]
> Csak meg kell keresnie egy költségbecslést, SQL Server edition és a virtuális gép mérete, adott kombinációk ki, ha további információt a díjszabási lapon a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) vagy [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Válassza ki a platform és az SQL Server jelen kiadása a **operációs rendszer/szoftver** listája.
>
> ![A virtuális gép díjszabását ismertető weblapon felhasználói felület](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Vagy használja a [díjkalkulátor](https://azure.microsoft.com/pricing/#explore-cost) hozzáadása és konfigurálása egy virtuális gépet. 

## <a name="free-licensed-sql-server-editions"></a>Ingyenes licencű SQL Server-kiadások

Ha azt szeretné, fejlesztése, tesztelése, vagy a koncepció igazolása, használja a licencelt szabadon **SQL Server Developer edition**. Ez a kiadás rendelkezik az SQL Server Enterprise edition, így összeállításához és teszteléséhez bármilyen típusú alkalmazást összes funkcióját. A Developer edition azonban éles környezetben nem futtatható. Egy SQL Server Developer edition virtuális Gépet csak költségeket terhel a költség, a virtuális gép, mert nincs társított SQL Server licencelési költségeit.

Ha szeretné-e éles egy könnyen használható számítási feladatok futtatásához (< 4 mag, < 1 GB memória, < 10 GB/adatbázis), használja a szabadon licencelt **SQL Server Express kiadásának**. Egy SQL Server Express edition virtuális Gépet is csak költségeket terhel a virtuális gép költségét.

Ezekhez a fejlesztéshez és teszteléshez és könnyen használható éles számítási feladatokra mentheti is költséget takaríthat meg egy kisebb Virtuálisgép-méretet, amely megfelel az ilyen munkaterhelések kiválasztásával. A DS1v2 bizonyos esetekben a jó választás lehet.

SQL Server 2017 Azure virtuális gép létrehozásához ezeket a rendszerképeket, tekintse meg a következőket:

| Platform | Szabadon licencelt képek |
|---|---|
| Windows Server 2016 | [Az SQL Server 2017 Developer Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017-es expressz Azure virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [Az SQL Server 2017 Developer Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017-es expressz Azure virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Az SQL Server 2017 Developer Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017-es expressz Azure virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [Az SQL Server 2017 Developer Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017-es expressz Azure virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Fizetős SQL Server-kiadások

Ha egy nem egyszerűsített éles környezetbeli számítási feladatokra, használja a következő SQL Server-kiadásokat egyikét:

| SQL Server Edition | Számítási feladat |
|-----|-----|
| Web | Kis webhelyek |
| Standard | Kis és közepes méretű számítási feladatok |
| Enterprise | Nagy méretű vagy üzleti szempontból alapvető fontosságú számítási feladatokhoz|

SQL Server-licenc ezeknél a kiadásoknál fizetnie két lehetősége van: *azért kell fizetnie, felhasználásonként* vagy *hozott licences (BYOL)*.

## <a name="pay-per-usage"></a>Fizessen a használat

**Használat az SQL Server-licencét és** azt jelenti, hogy az a másodpercenkénti költség, az Azure virtuális Gépen futó tartalmazza-e az SQL Server-licenc költsége. Láthatja, hogy az SQL Server különböző kiadásai (Web, Standard, Enterprise) díjszabása az Azure-beli virtuális gépen díjszabási oldalán találhat [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) vagy [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

A költségek megegyezik a (2012 SP3 2017) az SQL Server összes verziójára. Másodpercenkénti licencelési díját attól függ, hogy a virtuális gép vcpu-k számát.

Kell fizetnie az SQL Server licencelési felhasználásonként ajánlott:

- **Ideiglenes vagy rendszeres számítási feladatokat**. Ha például egy alkalmazást, amely támogatnia kell a egy esemény egy pár hónap minden évben, vagy az üzleti elemzési hétfőn.

- **Ismeretlen élettartamát vagy skálája a számítási feladatok**. Ha például egy alkalmazást, amely nincs szükség néhány hónapon belül, vagy amelynek több vagy kevesebb számítási teljesítmény, igény szerinti függően szükség lehet.

SQL Server 2017 Azure virtuális gép létrehozása egy használatalapú / használati rendszerképek, tekintse meg a következőket:

| Platform | Licencelt kép |
|---|---|
| Windows Server 2016 | [Az SQL Server 2017 webes Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[Az SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[Az SQL Server 2017 Enterprise Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [Az SQL Server 2017 webes Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[Az SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[Az SQL Server 2017 Enterprise Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Az SQL Server 2017 webes Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[Az SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[Az SQL Server 2017 Enterprise Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [Az SQL Server 2017 webes Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[Az SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[Az SQL Server 2017 Enterprise Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> A portálon egy SQL Servert futtató virtuális gép létrehozásakor a **méret kiválasztása** ablakban látható becsült költség. Fontos tudni, hogy ezt a becslést csak a számítási költségek bármely operációs rendszer licencelési költségeit (Windows vagy külső Linux operációs rendszerek) együtt a virtuális gépek futtatásához.
>
> ![Válassza ki a Virtuálisgép-méret panel](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Nem tartalmazza a további SQL Server licencelési költségeit, Web, Standard és Enterprise kiadások. A legpontosabb díjszabási becslés lekéréséhez válassza ki az operációs rendszer és az SQL Server-kiadást díjszabási oldalán [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) vagy [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="bring-your-own-license-byol"></a>Hozza magával a licencét (BYOL).

**Saját SQL Server licence keretében**, más néven **BYOL**, az azt jelenti, hogy egy Azure virtuális Gépen a frissítési garanciával rendelkező SQL Server egy meglévő mennyiségi licenc használatával. SQL Server virtuális gép használatával BYOL csak díjak a virtuális gép futtatásával járó költségeket, a nem SQL Server-licenc, tekintve, hogy már vásárolt licencek és a frissítési garanciával rendelkező mennyiségi licencelési program keretében.

> [!NOTE]
> A BYOL-lemezképeknek jelenleg csak Windows virtuális gépek számára elérhető. A csak Linux rendszerű virtuális gép azonban az SQL Server manuálisan telepítheti. Tekintse meg a az irányelveket a [Linux rendszerű SQL virtuális gép – gyakori kérdések](../../linux/sql/sql-server-linux-faq.md).

A saját SQL és licencelés keretében ajánlott:

- **Folyamatos számítási feladatok**. Ha például egy alkalmazást, amely üzleti műveletek 24 x 7 támogatnia kell.

- **Számítási feladatokkal és ismert élettartama, és a méretezési csoport**. Ha például egy alkalmazást, amely az egész évre, és mely igény szerint rendelkezik lett előrejelzett van szükség.

BYOL használata egy SQL Server virtuális Gépet, az SQL Server Standard vagy Enterprise-licenccel kell rendelkeznie, és [frissítési garancia](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), amely beállítás szükséges bizonyos mennyiségi licencelési programok és a egy nem kötelező beszerzési másokkal. A mennyiségi licencelési programok keretében biztosított árképzési szint változik, a szerződés és a mennyiség és/vagy az SQL Server-ra vonatkozó típusa alapján. De célravezető, mint saját licence folyamatos éles számítási feladatokra van a következő előnyökkel jár:

| BYOL juttatás | Leírás |
|-----|-----|
| **Költségmegtakarítás** | Költséghatékonyabb, mint és használat, ha a munkaterhelés folyamatosan fut az SQL Server Standard vagy Enterprise a saját SQL Server licence *több mint 10 hónap*. |
| **Hosszú távú megtakarítások** | Átlagosan van *30 %-kal olcsóbb évenként* vásárolni, vagy egy SQL Server-licencét megújítása az első 3 évig. Továbbá 3 év után nem kell többé újítsa meg a licencet, csak a frissítési garancia fizetnie. Ezen a ponton célszerű *200 %-kal olcsóbb*. |
| **Ingyenes passzív másodlagos replika** | Egy másik előnye, hogy saját licence van az [ingyenes passzív másodlagos replikájának licencelése](https://azure.microsoft.com/pricing/licensing-faq/) egy SQL-kiszolgálón a magas rendelkezésre állás érdekében. Ez csökkenti radikálisan a magas rendelkezésre állású SQL Server-telepítés (például Always On rendelkezésre állási csoportok) fele a licencelési költségeit. A jogok a passzív másodlagos futtatásához biztosított a feladatátvétel kiszolgálók frissítési garancián keresztül. |

Hozzon létre egy SQL Server 2017 Azure virtuális Gépet egy bring-your-saját licenc rendszerképek, tekintse meg a virtuális gép "{BYOL}" előtaggal:

- [Az SQL Server 2017 Enterprise Azure virtuális Gépen](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [Az SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Ossza meg velünk 10 napon belül az SQL Server-licencek számát használ az Azure-ban. Az előző képek mutató utasításokat ehhez rendelkeznie.

> [!NOTE]
> Nem alkalmas módosítása egy használatalapú másodpercenkénti saját licenc használata az SQL Server rendszerű virtuális gép licencelési modelljét. Jelen esetben létre kell hoznia egy új BYOL virtuális gépet, majd az új virtuális gépre kell telepítenie az adatbázisokat.

## <a name="reduce-costs"></a>Csökkentheti a költségeket

A felesleges költségek elkerülése érdekében egy optimális virtuális gép méretének kiválasztása, és időszakos leállások nem folytonos számítási feladatok esetében érdemes lehet.

### <a id="machinesize"></a> A virtuális gép megfelelő méretezés

Az SQL Server licencelési díját közvetlenül kapcsolódik a vcpu-k számát. Válassza ki a virtuális gép méretét, amely megfelel a várt igényét a CPU, memória, tárolási és i/o-sávszélességet. Lehetséges gépméretet teljes listáját lásd: [Windows Virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) és [Linux Virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Nincsenek új gépméretek, amelyek bizonyos típusú SQL Server számítási feladatok jól működnek. Ezek a gépek méretek karbantartása magas szintű memória, tárolási és i/o-sávszélességet, de vannak egy alacsonyabb virtuális magok száma. Vegyük példaként az alábbi példában:

| Virtuális gép mérete | Virtuális magok | Memory (Memória) | A lemezek maximális száma | Max. i/o-teljesítménye | SQL-licencelési költségei | Összköltség (számítási és licencelés) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51,200 iops-érték vagy 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51,200 iops-érték vagy 768 MB/s | 75 %-kal alacsonyabb | 57 %-kal alacsonyabb |

> [!IMPORTANT]
> Itt látható egy időponthoz – példa. A legutóbbi előírások, tekintse meg a gép méretei cikkeket, és az Azure díjszabási oldalán találhat [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) és [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Az előző példában láthatja, hogy a specifikációk **Standard_DS14v2** és **Standard_DS14-4v2** vcpu-k kivételével azonosak. Az utótag **-4v2** végén a **Standard_DS14-4v2** gépméretet aktív vcpu-k számát jelzi. Az SQL Server-licencköltségek felmerülése vcpu-k száma vannak társítva, mivel ez jelentősen csökkenti a forgatókönyvekben, ahol a felesleges vcpu-k nem szükségesek a virtuális gép költségét. Itt látható egy példa, és számos gépméretek korlátozott vcpu-k azonosítható a utótag mintával együtt. További információkért tekintse meg a következő blogbejegyzésben: [megjelent az új Azure-beli Virtuálisgép-méretek költséghatékony adatbázis további munkára](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Állítsa le a virtuális Géphez, ha lehetséges

Ha bármilyen számítási feladatot, amely nem folyamatos Futtatás használ, fontolja meg, az inaktív időszak közben a virtuális gép leállítása. A fizetés használat alapján történik.

Ha például egyszerűen próbálja ki az SQL Server-beli virtuális gépen, ha nem szeretne költségekkel véletlenül Ha hétig működés. Egy megoldás az, hogy használja a [automatikus leállítási funkciót](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![SQL virtuális gép autoshutdown](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Az automatikus leállítás hasonló szolgáltatások által biztosított körének része [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Egyéb munkafolyamatok fontolja meg automatikusan leállítani, és Azure virtuális gépek újraindítása a parancsfájl-kezelési megoldással, például [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Leállítása és felszabadítása a virtuális gép az egyetlen módja díjak elkerülése érdekében. Egyszerűen leállítása vagy energiagazdálkodási lehetőségek használatával állítsa le a virtuális gép továbbra is tekintetében a használati díjak.

## <a name="next-steps"></a>További lépések

Általános Azure díjszabási útmutatóját, lásd: [az Azure-elszámolással és költségkezeléssel váratlan költségek megelőzése](../../../billing/billing-getting-started.md). A legújabb virtuális gépek díjszabását, beleértve az SQL Server, tekintse meg az Azure virtuális gép Azure díjszabási oldalán találhat [Windows virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) és [Linux rendszerű virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Az Azure virtuális gépeken futó SQL Server, lásd a következő cikkeket:

- [Windows virtuális gépeken az SQL Server használatának áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
- [Az SQL Server Linuxos virtuális gépeken – áttekintés](../../linux/sql/sql-server-linux-virtual-machines-overview.md)