---
title: "Költségek hatékony kezelésére az SQL Server Azure virtuális gépeken futó |} Microsoft Docs"
description: "Ajánlott eljárásokat biztosít a megfelelő SQL Server virtuális gép fizetési modell kiválasztása."
services: virtual-machines-windows
documentationcenter: na
author: luisherring
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/18/2017
ms.author: jroth
ms.openlocfilehash: 29a92f0c70bffedeb75c50b7fc3b687ee5ee227d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Útmutató a SQL Server Azure virtuális gépek díjszabása

Ez a témakör az SQL Server Azure virtuális gép árképzési útmutatást. Több lehetőség, amelyek hatással vannak a költség, és fontos, hogy válassza ki a megfelelő lemezképet, hogy az üzleti követelményeinek költségek.

## <a name="free-licensed-sql-server-editions"></a>Ingyenes licenccel rendelkező SQL Server kiadásai

Fejleszthet, teszteléséhez, vagy a koncepció igazolása, majd használja az ingyenes licenccel rendelkező **SQL Server Developer kiadásában**. Ez a kiadás mindent tartalmaz, az SQL Server Enterprise edition, így használhatja bármilyen kívánt alkalmazás létrehozásához. Most nem engedélyezték a futtatásához az éles környezetben. SQL Server Developer virtuális gép csak összegű ideiglenes terheléssel az a virtuális Gépet, a nem SQL Server licencelési költségeit.

Ha éles környezetben egy egyszerűsített munkaterhelés futtatni kívánt (< 4 mag, < 1 GB memória, < 10 GB/adatbázis), majd használja az ingyenes licenccel rendelkező **SQL Server Express edition**. SQL Express virtuális gép csak felszámított a költség, a virtuális gép nem SQL licencelési.

A következő fejlesztési és tesztelési célú, illetve egyszerűsített termelési számítási feladatokhoz is mentheti pénz válasszon, amely megfelel az ilyen terhelések kisebb Virtuálisgép-méretet. A DS1v2 előfordulhat, hogy az ilyen terhelések érdemes választani.

SQL Server 2016 Azure virtuális gép valamelyik, a lemezképek létrehozásához lásd: a következő hivatkozásokat:

- [SQL Server 2016 fejlesztői Azure virtuális Gépen](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1DeveloperWindowsServer2016-ARM)
- [SQL Server 2016 Express Azure virtuális gép](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1ExpressWindowsServer2016-ARM)

## <a name="paid-sql-server-editions"></a>Fizetett SQL Server kiadásai

Ha egy nem lightweight éles munkaterhelés, használja a következő SQL Server verzióinak egyikét:

| SQL Server Edition | Számítási feladat |
|-----|-----|
| Web | Kis webhelyek |
| Standard | Kis-és közepes méretű munkaterhelések |
| Enterprise | Nagy méretű vagy kritikus fontosságú munkaterhelésekhez|

Az SQL Server licencelési ezeknek a kiadásoknak díj ellenében két lehetősége van: *kell fizetnie, amennyit egy használati* vagy *saját licenc*.

### <a name="pay-per-usage"></a>Használati / kell fizetnie

**Az SQL Server licence / használati fizető** azt jelenti, hogy az Azure virtuális Gépen futó perc költsége tartalmaz-e az SQL Server licence költségét. Látható az az SQL Server különböző kiadásai (Web, Standard, Enterprise) díjszabása a [Azure virtuális gép árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard). A költség megegyezik az SQL Server (2008 R2 2016) bármely verziója. Csakúgy, mint az SQL Server általában licencelés, a percalapú licencelési költségeit VM magok száma függ.

Az SQL Server fizető / használati licencelési ajánlott:

- Ideiglenes vagy rendszeres munkaterhelések. Például olyan alkalmazás, amelynek támogatnia kell a esemény néhány hónappal az összes év, és üzleti elemző hétfőn.
- Ismeretlen élettartama vagy skálája a munkaterhelések. Például egy alkalmazást, amely nincs szükség néhány hónapon belül, vagy több vagy kevesebb számítási teljesítményt, attól függően, hogy igény szerint szükségesek.

SQL Server 2016 Azure virtuális gép valamelyik, a fizetési / használati lemezképek létrehozásához lásd: a következő hivatkozásokat:

- [SQL Server 2016 webes Azure virtuális Gépen](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016)
- [SQL Server 2016 szabványos Azure virtuális gép](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016)
- [SQL Server 2016 Enterprise Azure virtuális Gépen](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016)

> [!IMPORTANT]
> Ha az SQL Server virtuális gép létrehozása az Azure portálon, a becsült havi költségét megjelenik a **méret kiválasztása** panel nem tartalmazza az SQL Server licencelési költségeit. Ez egyedül a virtuális gép költsége.
>
> ![Válassza ki a Virtuálisgép-méret panelen](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>A szabad licenccel rendelkező Express és Developer kiadásában SQL Server ez pedig a teljes becsült költség. A Web-, Standard és Enterprise, a további SQL licencelési költségeit a talált a [Windows virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Jelölje ki a cél az SQL Server kiadása az árképzést ismertető oldalra.

### <a name="bring-your-own-license-byol"></a>Hozza magával a licencét (BYOL).

**A saját SQL Server licence kapcsolásának License Mobility keresztül**, más néven **BYOL**, azt jelenti, hogy frissítési garancia egy Azure virtuális gép egy meglévő SQL Server mennyiségi licenc használatával. Egy SQL Server virtuális gép BYOL csak összegű ideiglenes terheléssel fut a virtuális Gépet, a nem SQL Server licencelési, költségét fényében, hogy már vásárolt licencek és frissítési garancia mennyiségi licencelési programon keresztül.

Hozása a saját SQL keresztül License Mobility licencelési ajánlott:

- Folyamatos munkaterhelések. Például olyan alkalmazás, amelynek támogatnia kell a vállalati műveletek 24 x 7.
- Az ismert élettartamát és a skála munkaterhelések. Például egy alkalmazást, amely a teljes év és mely igény szerinti rendelkezik lett előrejelzett lesz szükség.

BYOL használandó SQL Server virtuális gépen, az SQL Server Standard vagy Enterprise licenccel kell rendelkeznie és [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), amely az egyes keresztül szükséges lehetőség [mennyiségi licencelés](https://www.microsoft.com/en-us/download/details.aspx?id=10585) programok és egy nem kötelező vásárolja meg másokkal.  Változik a mennyiségi licencelési programok keresztül elérhető árképzési szinteket, a szerződés és a mennyiség és vagy SQL Server elkötelezettségének típusa alapján. De a szokásos megoldás, mint a saját licenc folyamatos termelési számítási feladatokhoz mihamarabb elérhetővé tenni a következő előnyöket biztosítja:

| BYOL juttatás | Leírás |
|-----|-----|
| **Költségmegtakarítások** | Hozása a saját SQL Server licence költséghatékonyabb megoldás, mint fizető / használata, ha egy munkaterhelés folyamatosan fog futni, SQL Server Standard vagy Enterprise a *10 hónapnál*. |
| **Hosszú távú megtakarítások** | Átlagosan van *30 % olcsóbb évente* vásárolni, vagy egy SQL Server licence megújítása esetén az első három év. Ezenkívül három év, után nem kell többé újítsa meg a licenc, frissítési garancia ugyanúgy kell fizetnie. Ezen a ponton van *200 % olcsóbb*. |
| **Szabad passzív másodlagos replika** | Egy másik előnye, hogy a saját licenc a [szabad passzív egy másodlagos másodpéldány a licencelés](https://azure.microsoft.com/pricing/licensing-faq/) egy SQL-kiszolgálón a magas rendelkezésre állás érdekében. Ez a magas rendelkezésre állású SQL Server telepítését (pl. használatával az Always On rendelkezésre állási csoportok) fele a licencelési költségek kivágása. A passzív másodlagos futtatásához a jogosultságokat kapnak a feladatátvételi kiszolgálók Software Assurance juttatás keresztül. |

SQL Server 2016 Azure virtuális gép valamelyik, a bring your-saját-licencet lemezképek létrehozásához lásd: a virtuális gépek előtagként a "{BYOL}":

- [SQL Server 2016 Enterprise Azure virtuális Gépen](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016)
- [SQL Server 2016 szabványos Azure virtuális gép](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)

> [!NOTE]
> Tudassa velünk, 10 napon belül hány fogja használni az Azure SQL Server-licencet. Az előző lemezképek mutató hivatkozások utasításokat ennek rendelkezik.

## <a name="avoid-unecessary-costs"></a>Unecessary költségek elkerülése érdekében

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

Tekintse át a többi SQL Server virtuális gép témakörei [SQL Server Azure virtuális gépek – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).
