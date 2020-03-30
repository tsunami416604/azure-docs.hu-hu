---
title: Árútmutató & költségek kezelése
description: Gyakorlati tanácsok az SQL Server virtuálisgépek megfelelő díjszabási modelljének kiválasztásához.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b7a8f7136d0899ab3afe04bce2d25bc2e7f8d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981712"
---
# <a name="pricing-guidance-for-azure-sql-server-vms"></a>Díjszabási útmutató az Azure SQL Server virtuális gépekhez

Ez a cikk díjszabási útmutatást nyújt az [Azure-beli SQL Server virtuális gépekhez.](virtual-machines-windows-sql-server-iaas-overview.md) Számos lehetőség van, amelyek befolyásolják a költségeket, és fontos, hogy válassza ki a megfelelő képet, amely egyensúlyt teremt a költségek és az üzleti követelmények.

> [!TIP]
> Ha csak az SQL Server-kiadás és a virtuális gép méretének adott kombinációjára vonatkozó költségbecslést kell megtudnia, tekintse meg a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) vagy [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux)díjszabási oldalát. Válassza ki a platformot és az SQL Server kiadást az **OPERÁCIÓS rendszer/szoftver** listából.
>
> ![Felhasználói felület a virtuális gép díjszabási oldalán](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Vagy használja a [díjkalkulátor](https://azure.microsoft.com/pricing/#explore-cost) virtuális gép hozzáadásához és konfigurálásához. 

## <a name="free-licensed-sql-server-editions"></a>Ingyenesen licencelt SQL Server kiadások

Ha koncepcióigazolást szeretne kifejleszteni, tesztelni vagy építeni, használja a szabadon licencelt **SQL Server Developer kiadást.** Ez a kiadás az SQL Server Enterprise kiadás összes szolgáltatásával rendelkezik, amely lehetővé teszi bármilyen típusú alkalmazás felépítését és tesztelését. A Fejlesztői kiadás azonban nem futtatható éles környezetben. Az SQL Server Developer edition virtuális gép csak a virtuális gép költségét terheli, mivel nincsenek kapcsolódó SQL Server licencelési költségek.

Ha éles környezetben (<4 mag, <1 GB memóriában, <10 GB/adatbázis) szeretne futtatni egy könnyű számítási feladatot, használja a szabadon licencelt **SQL Server Express kiadást.** Az SQL Server Express edition virtuális gép is csak a virtuális gép költségét is felszámítja.

Ezekhez a fejlesztési/tesztelési és könnyű éles számítási feladatokhoz is pénzt takaríthat meg egy kisebb virtuális gép méretének kiválasztásával, amely megfelel ezeknek a számítási feladatoknak. A DS1v2 lehet egy jó választás bizonyos esetekben.

Ha sql Server 2017 Azure virtuális géppel szeretne létrehozni az alábbi lemezképek egyikével, tekintse meg az alábbi hivatkozásokat:

| Platform | Szabadon licencelt képek |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Fejlesztő Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express Azure Virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Fejlesztő Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express Azure Virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Fejlesztő Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express Azure Virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Fejlesztő Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express Azure Virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Fizetős SQL Server kiadások

Ha nem könnyű éles munkaterheléssel rendelkezik, használja az alábbi SQL Server kiadások egyikét:

| SQL Server Edition | Számítási feladat |
|-----|-----|
| Web | Kis weboldalak |
| Standard | Kis és közepes munkaterhelések |
| Enterprise | Nagy vagy kritikus fontosságú munkaterhelések|

Az SQL Server licencelése két lehetőség közül választhat: *használatonként fizetni,* vagy *saját licencet (BYOL) hozhat magával.*

## <a name="pay-per-usage"></a>Fizetés használatonként

**Az SQL Server-licenc használatonkénti (más** néven **díj fizetés)** kifizetése azt jelenti, hogy az Azure virtuális gép futtatásának másodpercenkénti költsége tartalmazza az SQL Server-licenc költségét. A különböző SQL Server-kiadások (Web, Standard, Enterprise) díjszabása az Azure VM-díjszabási lapon [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) vagy [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux)rendszeren látható.

A költség megegyezik az SQL Server összes verziójában (2012 SP3–2017). A másodpercenkénti licencelési költség a virtuális gép vCPU-inak számától függ.

Az SQL Server licencelésének kifizetése használatonként a következő célokra ajánlott:

- **Ideiglenes vagy időszakos munkaterhelések**. Például egy alkalmazás, amelynek támogatnia kell egy eseményt egy pár hónapig minden évben, vagy üzleti elemzés hétfőnként.

- **Ismeretlen élettartamú vagy skálázott munkaterhelések.** Például egy olyan alkalmazás, amelyre néhány hónapon belül nincs szükség, vagy amely igénytől függően több vagy kevesebb számítási teljesítményt igényelhet.

Ha az alábbi, felosztó-ki-felképek egyikével szeretne létrehozni egy SQL Server 2017-es Azure-virtuális gépet, tekintse meg az alábbi hivatkozásokat:

| Platform | Licencelt képek |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 standard Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure Virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 standard Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure Virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Web Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 standard Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure Virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 standard Azure virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure Virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Amikor létrehoz egy SQL Server virtuális gépet a portálon, a **Méret kiválasztása** ablak becsült költséget jelenít meg. Fontos megjegyezni, hogy ez a becslés csak a számítási költségek a virtuális gép futtatásához együtt az operációs rendszer licencelési költségeit (Windows vagy külső Linux operációs rendszerek).
>
> ![Válassza a Virtuálisgép méretpanelt](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Nem tartalmazza a webes, standard és nagyvállalati kiadások hoz a további SQL Server licencelési költségeket. A legpontosabb árképzési becsléshez válassza ki az operációs rendszert és az SQL Server kiadást a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) vagy [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)díjszabási lapján.

> [!NOTE]
> Most már lehet változtatni a licencelési modell pay-per-használat, hogy a saját licenc (BYOL) és vissza. További információ: [Az SQL virtuális gépek licencelési modelljének módosítása.](virtual-machines-windows-sql-ahb.md) 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a>Hozza magával saját licencét (BYOL)

**A saját SQL Server-licenc** **byol**néven is nevezik, azt jelenti, hogy egy azure-beli virtuális gépben egy meglévő SQL Server mennyiségi licencet használ frissítési garanciával. A BYOL-t használó SQL Server virtuális gépek csak a virtuális gép futtatásának költségét számítják fel, az SQL Server licencelése ért nem, mivel már beszerezte a licenceket és a Frissítési garanciát egy mennyiségi licencelési programon keresztül.

> [!IMPORTANT]
> A BYOL-lemezképekhez nagyvállalati szerződés szükséges a Frissítési Garanciával. Jelenleg nem érhetők el az Azure Cloud Solution Partner (CSP) részeként. A csp-ügyfelek saját licencet hozhatnak létre, ha üzembe helyeznek egy felosztó-kirovó lemezképet, majd engedélyezik az [Azure Hybrid Benefit szolgáltatást.](virtual-machines-windows-sql-ahb.md)

> [!NOTE]
> A BYOL-lemezképek jelenleg csak Windows virtuális gépeken érhetők el. Az SQL Server manuálisan is telepíthető linuxos virtuális gépre. Tekintse meg a Linux SQL VM gyakori kérdések című [témakör irányelveit.](../../linux/sql/sql-server-linux-faq.md)

A licenchordozhatóságon keresztül a saját SQL-licencelés használata a következő célokra ajánlott:

- **Folyamatos munkaterhelések**. Például egy alkalmazás, amely támogatnia kell az üzleti műveletek 24x7.

- **Ismert élettartamú és skálázott munkaterhelések.** Például egy alkalmazás, amely szükséges az egész évre, és amely az igény előre jelzett.

A BYOL SQL Server virtuális géphez való használatához rendelkeznie kell az SQL Server Standard vagy az Enterprise and [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1)licenccel, amely bizonyos mennyiségi licencelési programokon és másokkal való opcionális vásárláson keresztül szükséges. A mennyiségi licencelési programok által biztosított díjszabási szint a szerződés típusától, a mennyiségtől és az SQL Server iránti elkötelezettségtől függően változik. De általános szabály, hogy a saját licencet a folyamatos éles számítási feladatok hoz a következő előnyökkel jár:

| BYOL előny | Leírás |
|-----|-----|
| **Költségmegtakarítás** | Az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) akár 55%-os megtakarítást is biztosít. További információ: [Licencelési modell váltása](virtual-machines-windows-sql-ahb.md) |
| **Szabad passzív másodlagos replika** | A saját licenc ingyenes licencének másik előnye, hogy egy [passzív másodlagos replika ingyenes](https://azure.microsoft.com/pricing/licensing-faq/) enyelegi-kiszolgálónként magas rendelkezésre állási célokra. Ez felére csökkenti a magas rendelkezésre állású SQL Server-telepítés licencelési költségét (például az Always On Availability Groups használatával). A passzív másodlagos futtatásához való jogok a feladatátvevő kiszolgálók frissítési garancia előnye révén biztosítva. |

Ha egy SQL Server 2017-es Azure virtuális gépet szeretne létrehozni a saját licenccel rendelkező lemezképek egyikével, tekintse meg a"{BYOL}" előtaggal ellátott virtuális gépeket:

- [SQL Server 2017 Enterprise Azure Virtuális gép](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 standard Azure virtuális gép](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> 10 napon belül tudassa velünk, hogy hány SQL Server-licencet használ az Azure-ban. Az előző képekre mutató hivatkozások erre vonatkozó utasításokat tartalmaznak.

> [!NOTE]
> Most már lehet változtatni a licencelési modell pay-per-használat, hogy a saját licenc (BYOL) és vissza. További információ: [Az SQL virtuális gépek licencelési modelljének módosítása.](virtual-machines-windows-sql-ahb.md) 



## <a name="reduce-costs"></a>Költségek csökkentése

A szükségtelen költségek elkerülése érdekében válasszon egy optimális virtuálisgép-méretet, és fontolja meg a nem folyamatos munkaterhelések időszakos leállítását.

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a>A virtuális gép megfelelő méretezése

Az SQL Server licencelési költsége közvetlenül kapcsolódik a vCPU-k számához. Válasszon olyan virtuális gépméretet, amely megfelel a processzor, a memória, a tárhely és az I/O-sávszélesség várható igényeinek. A számítógépméret-beállítások teljes listáját a [Windows virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) és a Linux virtuális gépek [méretei (Windows VM-méretek) tartalmazza.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Vannak új gépméretek, amelyek jól működnek bizonyos típusú SQL Server számítási feladatok. Ezek a gépek mérete magas memória-, tárolási és I/O-sávszélességet tart fenn, de alacsonyabb virtualizált magszámmal rendelkeznek. Vegyük például a következő példát:

| Virtuális gép mérete | vCPU-k | Memory (Memória) | Lemezek maximális | Maximális I/O átviteli sebesség | SQL licencelési költségek | Összes költség (Számítás + licencelés) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51,200 IOPS vagy 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51,200 IOPS vagy 768 MB/s | 75%-kal alacsonyabb | 57%-kal alacsonyabb |

> [!IMPORTANT]
> Ez egy pontos példa. A legfrissebb specifikációkat a gépméretekkel és a Windows és [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) Azure díjszabási lapjával [találja.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)

Az előző példában láthatja, hogy a **Standard_DS14v2** és **Standard_DS14-4v2** specifikációi azonosak, kivéve a vCPU-kat. A **Standard_DS14-4v2** gépméret végén található **-4v2** utótag az aktív vCPU-k számát jelzi. Mivel az SQL Server licencelési költségei a vCPU-k számához vannak kötve, ez jelentősen csökkenti a virtuális gép költségét azokban az esetekben, amikor nincs szükség a további vCPU-kra. Ez egy példa, és sok gépméretek korlátozott vCPU-k, amelyek ezzel az utótag minta azonosított. További információ: [Announceing new Azure VM sizes for more cost-effective database work.](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)

### <a name="shut-down-your-vm-when-possible"></a>A virtuális gép leállítása, ha lehetséges

Ha olyan számítási feladatokat használ, amelyek nem futnak folyamatosan, fontolja meg a virtuális gép leállítását az inaktív időszakokban. A fizetés használat alapján történik.

Ha például egyszerűen kipróbálja az SQL Servert egy Azure-beli virtuális gépen, nem szeretne díjakat felmeríteni azzal, hogy véletlenül hetekig futtatja. Az egyik megoldás az [automatikus leállítási funkció](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)használata.

![SQL virtuális gép automatikus leállítása](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Az automatikus leállítás az [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab)által biztosított hasonló funkciók nagyobb készletének része.

Más munkafolyamatok esetén fontolja meg az Azure-beli virtuális gépek automatikus leállítását és újraindítását egy parancsfájl-készítési megoldással, például [az Azure Automation használatával.](https://azure.microsoft.com/services/automation/)

> [!IMPORTANT]
> A virtuális gép leállítása és a virtuális gép felosztása az egyetlen módja a díjak elkerülésének. Egyszerűen leállítása vagy használata energiagazdálkodási lehetőségek leállítása a virtuális gép továbbra is használati díjakat.

## <a name="next-steps"></a>További lépések

Az Azure általános díjszabási útmutatóját a [Váratlan költségek megelőzése az Azure számlázásával és költségkezelésével](../../../cost-management-billing/manage/getting-started.md)című témakörben talál. A virtuális gépek legújabb díjszabását, beleértve az SQL Servert is, tekintse meg az Azure Virtuálisgép-azure díjszabási lapját [windowsos virtuális gépekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) és [Linuxos virtuális gépekhez.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)

Az Azure virtuális gépeken futó SQL Server áttekintését az alábbi cikkekben találja:

- [Az SQL Server windowsos virtuális gépeken – áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
- [Az SQL Server linuxos virtuális gépekről – áttekintése](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
