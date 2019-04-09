---
title: Kiépítési útmutató Windows SQL Server rendszerű virtuális gépekhez az Azure Portalon |} A Microsoft Docs
description: Ez az útmutató a Windows SQL Server 2017 virtuális gépekre az Azure Portalon történő lehetőségeit ismerteti.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 05/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bb051d37f3a1dd82d7d46bfe8b22c2ba1251be85
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259201"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Az Azure Portalon Windows SQL Server virtuális gép kiépítése

Ez az útmutató részletesen a rendelkezésre álló különböző lehetőségeket az Azure Portalon Windows SQL Server virtuális gép létrehozásakor. Ez a cikk ismerteti a további konfigurációs lehetőségeket, mint a [SQL Server rendszerű virtuális gép rövid](quickstart-sql-vm-create-portal.md), amely további keresztül egy lehetséges kiépítés feladat kerül. 

Ez az útmutató segítségével saját SQL Server virtuális gép létrehozása. Vagy használhatja referenciaként az Azure Portalon elérhető beállítások.

> [!TIP]
> Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a id="select"></a> Az SQL Server virtuálisgép-katalógus rendszerképek

Az SQL Server virtuális gép létrehozásakor több előre konfigurált lemezképek közül választhat a virtuálisgép-katalógus. A következő lépések bemutatják, hogyan lehet az SQL Server 2017-rendszerképek egyikét választhatja ki.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) a saját fiókjával.

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra. A portál megnyitja az **Új** ablakot.

1. Az **Új** ablakban kattintson a **Számítás**, majd az **Összes megjelenítése** elemre.

1. A keresőmezőbe írja be az **SQL Server 2017** kifejezést, majd nyomja le az ENTER billentyűt.

1. A szűrő legördülő listákból válassza _Windows Server 2016_ a a **operációs rendszer** válassza _Microsoft_ , a **közzétevő**. 

     ![Új számítás ablak](./media/virtual-machines-windows-portal-sql-server-provision/azure-new-compute-blade.png)

1. Tekintse át az elérhető SQL Server-rendszerképeket. Minden rendszerkép egy SQL Server-verziót és egy operációs rendszert azonosít.

1. Válassza ki a rendszerkép **ingyenes SQL Server-licenc: SQL Server 2017 Developer Windows Server 2016 rendszeren**.

   > [!TIP]
   > A Developer edition egy teljes körű funkciókkal bíró, ingyenes SQL Server kiadása fejlesztési-tesztelési, mert ez az útmutató szolgál. Csak a virtuális gép futtatásával járó költségeket kell kifizetni. Azonban Ön bármelyik, a forgatókönyv rendszerképet választhatja. Az elérhető rendszerképek, olvassa el a [SQL Server Windows Virtual Machines – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Az SQL Server licencelési költségeit is részét képezik a virtuális gép létrehozása és a változó díjszabása másodpercalapú edition és a magok. Azonban az SQL Server Developer edition díjmentes, használható fejlesztési-tesztelési (éles környezetben nem), és az SQL Express pedig ingyenesen használható (kisebb, mint 1 GB memóriával, kevesebb mint 10 GB tárterület) számítási feladatokhoz. Emellett bring-your-saját licenc (használata BYOL) és a fizetés csak a virtuális gép számára. Az ilyen rendszerképek nevei {BYOL} előtagot kapnak. 
   >
   > A lehetőségekkel kapcsolatos további információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

1. Ellenőrizze, hogy a **Telepítési modell kiválasztása** alatt a **Resource Manager** van-e kiválasztva. Az új virtuális gépek esetén az ajánlott üzemi modell a Resource Manager. 

1. Kattintson a **Létrehozás** gombra.


## <a id="configure"></a> Konfigurációs beállítások

Nincsenek SQL Server virtuális gép konfigurálásához több lapra. Ez az útmutató céljából fogunk dolgozni a következőket: 

| Lépés | Leírás |
| --- | --- |
| **Alapvető beállítások** |[Az alapvető beállítások konfigurálása](#1-configure-basic-settings) |
| **Optional Features (Választható szolgáltatások)** |[Választható funkciók konfigurálása](#2-configure-optional-features) |
| **SQL Server beállításai** |[Az SQL Server beállításainak konfigurálása](#3-configure-sql-server-settings) |
| **Áttekintés + létrehozás** | [Összegzés áttekintése](#4-review--create) |

## <a name="1-configure-basic-settings"></a>1. Az alapvető beállítások konfigurálása


Az a **alapjai** lapra, adja meg a következő információkat:

* A **Project Details**, győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva. 
*  Az a **erőforráscsoport** szakaszt, válassza ki a meglévő erőforrás csoportot a listából, vagy válasszon **új létrehozása** egy új erőforráscsoport létrehozásához. Az erőforráscsoportok az Azure-ban egymáshoz kapcsolódó erőforrásokból (virtuális gépekből, tárfiókokból, virtuális hálózatokból stb.) álló gyűjtemények. 

    ![Előfizetés](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Az Azure szolgáltatásban futó SQL Server üzemelő példányok tesztelésekor vagy a velük való megismerkedéskor érdemes egy új erőforráscsoportot használni. A tesztelés befejezése után törölje az erőforráscsoportot a virtuális gép és az erőforráscsoporthoz társított összes erőforrás automatikus törléséhez. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../../../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).


* A **példány részletei**:
    1. Adjon meg egy egyedi **virtuális gép neve**.  
    1. Válasszon egy helyet a **régió**. 
    1. Ez az útmutató céljából hagyja **rendelkezésre állási beállítások** beállítása _szükséges infrastruktúra redundancia_. Rendelkezésre állási beállításokkal kapcsolatos további információkért, lásd: [Azure-régiók és rendelkezésre állási](../../windows/regions-and-availability.md). 
    1. Az a **kép** listáról válassza ki _ingyenes SQL Server-licenc: SQL Server 2017 Developer Windows Server 2016 rendszeren_.  
    1. Válassza ki a **méretének módosítása** a a **mérete** , a virtuális gépre, majd válassza a **a2-es alapszintű** ajánlat. Ügyeljen arra, hogy miután végzett az minden olyan váratlan költségek megelőzése érdekében az erőforrások törlése. Az éles számítási feladatok esetében ajánlott gépméretekért és -konfigurációkért tekintse meg [az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növelésével kapcsolatos ajánlott eljárásokat](virtual-machines-windows-sql-performance.md).

    ![Példány részletei](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> A **Méret kiválasztása** ablakban megjelenő, becsült havi költségek nem tartalmazzák az SQL Server licencelési költségeit. Ez a becslés a önálló virtuális gép költségét. SQL Server Express és Developer kiadásai esetében ez a becslés a teljes becsült költség. Más kiadások esetében tekintse meg a [Windows rendszerű virtuális gépek árképzését ismertető oldalt](https://azure.microsoft.com/pricing/details/virtual-machines/windows/), és válassza ki az SQL Server megfelelő kiadását. További tájékoztatás a [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md) és [virtuális gépek méretei](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* A **rendszergazdai fiók**, adjon meg egy felhasználónevet és jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Rendszergazdai fiók](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* Alatt **bejövőport-szabályok**, válassza a **lehetővé teszi a kiválasztott portok** , majd **RDP (3389-es)** a legördülő listából. 

   ![Bejövőport-szabályok](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Választható funkciók konfigurálása

### <a name="disks"></a>Lemezek

Az a **lemezek** lapon, a lemez-beállítások konfigurálása. 

* A **operációsrendszer-lemez típusa**, válassza ki a legördülő menüből az operációs rendszer használni kívánt lemez típusa. Prémium szintű éles rendszerek esetén ajánlott, de nem áll rendelkezésre az alapszintű virtuális gép. Prémium szintű SSD vételéhez, módosítsa a virtuális gép mérete. 
* A **speciális**válassza **Igen** használják **Managed Disks**.

   > [!NOTE]
   > A Microsoft a Managed Disks használatát javasolja az SQL Server esetében. A Managed Disks szolgáltatás a háttérben kezeli a tárterületet. Emellett ha ugyanabban a rendelkezésre állási csoportban több, a Managed Diskset használó virtuális gép található, az Azure elosztja a tárolási erőforrásokat, hogy megfelelő redundanciát biztosítson. További információkért lásd: [az Azure Managed Disks – áttekintés] [... / felügyelt lemezek – overview.md). A rendelkezésre állási csoportban a felügyelt lemezekkel kapcsolatos részletekért lásd: [felügyelt lemezek használata a virtuális gépek rendelkezésre állási csoportban] (.. /manage-Availability.md.

![SQL Virtuálisgép-lemez beállításai](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Hálózat

Az a **hálózatkezelés** lapra, konfigurálja a hálózati beállításokat. 

* Hozzon létre egy új **virtuális hálózat**, vagy használjon egy meglévő Vnetet az SQL Server virtuális gép. Kijelöl egy **alhálózati** is. 

* A **hálózati biztonsági csoport**, válassza ki, vagy egy alapszintű biztonsági csoportot, vagy a speciális biztonsági csoportot. Az alapszintű beállítás lehetővé teszi a bejövő portok kiválasztása az SQL Server rendszerű virtuális gép (a konfigurált ugyanazokat az értékeket a **alapszintű** lap). A speciális beállítás lehetővé teszi egy meglévő hálózati biztonsági csoportot válasszon, vagy hozzon létre egy újat. 

* Hálózati beállítások egyéb módosításokat, vagy használja az alapértelmezett értékeket.

![Az SQL virtuális gép hálózati beállítások](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Figyelés

Az a **figyelés** fülre, konfigurálhatja a felügyeletet és automatikus leállítás. 

* Az Azure segítségével **rendszerindító figyelési** ugyanazt a tárfiókot az alapértelmezés szerint kijelölt a virtuális gép számára. Ezeket a beállításokat itt, valamint engedélyezésével módosíthatja **operációs rendszer Vendég diagnosztikai**. 
* Engedélyezheti a **rendszer hozzárendelt felügyelt identitás** és **automatikus leállítási** ezen a lapon. 

![SQL virtuális gép beállítások](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Az SQL Server beállításainak konfigurálása

Az a **SQL Server-beállítások** fülre, adott beállításait és optimalizálási lehetőségeit az SQL Server konfigurálása. Az SQL Server konfigurálható beállítások a következők:



| Beállítás |
| --- |
| [Kapcsolatok](#connectivity) |
| [Authentication](#authentication) |
| [Azure Key Vault-integráció](#azure-key-vault-integration) |
| [Tároló konfigurálása](#storage-configuration) |
| [Automatikus javítás](#automated-patching) |
| [Automatikus biztonsági mentés](#automated-backup) |
| [Az R Services (speciális elemzések)](#r-services-advanced-analytics) |


### <a name="connectivity"></a>Kapcsolatok

Az **SQL kapcsolatok** alatt adja meg, milyen típusú hozzáférést szeretne az ezen a virtuális gépen futó SQL Server-példányhoz. Ez a bemutató céljából, jelölje be a **nyilvános (internet)** kapcsolatok engedélyezéséhez az SQL Server a gépek és szolgáltatások az interneten. Ezt a lehetőséget választja, az Azure automatikusan úgy konfigurálja a tűzfalat és a hálózati biztonsági csoportot, hogy a forgalmat a kiválasztott porton.

> [!TIP]
> Alapértelmezés szerint az SQL Server a jól ismert **1433-as** portot figyeli. A nagyobb biztonság érdekében az előző párbeszédpanelen módosítsa a portot egy nem alapértelmezett, például az 1401-es portra. Ha módosítja a port, csatlakoznia kell az összes ügyféleszközzel, például az SSMS porthoz.

![SQL virtuális gép biztonsági](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Ahhoz, hogy az SQL Serverhez az interneten keresztül csatlakozni lehessen, engedélyeznie kell az SQL Server-hitelesítést is, aminek ismertetését a következő szakaszban olvashatja.

Ha nem szeretné engedélyezni az adatbázis-alrendszerhez az interneten keresztül való csatlakozást, válassza az alábbi lehetőségek valamelyikét:

* A **Helyi (csak virtuális gépen belül)** beállítással az SQL Serverhez csak a virtuális gépen belülről lehet csatlakozni.
* A **Magánjellegű (virtuális hálózaton belül)** beállítással az SQL Serverhez az azonos virtuális hálózaton található gépek és szolgáltatások csatlakozhatnak.

Általánosságban elmondható, hogy a forgatókönyv által lehetővé tett legszigorúbb kapcsolódási korlátozás kiválasztásával növelhető a biztonság. Hálózati biztonsági csoportszabályok és SQL-/Windows-hitelesítés használatával azonban mindegyik lehetőség biztosítható. A hálózati biztonsági csoportot a virtuális gép létrehozása után szerkesztheti. További információkért lásd [az SQL Server Azure-beli virtuális gépeken történő futtatásának biztonsági szempontjait](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Authentication

Ha SQL Server-hitelesítésre van szüksége, kattintson az **Engedélyezés** lehetőségre az **SQL-hitelesítés** alatt.

![SQL Server-hitelesítés](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Ha azt tervezi, SQL Server elérését az interneten (a nyilvános kapcsolódási beállítást választja), engedélyeznie kell az SQL-hitelesítés itt. Az SQL Serverhez való nyilvános hozzáféréshez SQL-hitelesítésre van szükség.

Ha engedélyezi az SQL Server-hitelesítést, adjon meg egy **bejelentkezési nevet** és egy **jelszót**. A bejelentkezési neve egy SQL Server-hitelesítési bejelentkezés, és tagja van konfigurálva a **SysAdmin (rendszergazda)** rögzített kiszolgálói szerepkör. További információkat a hitelesítési módokról [a hitelesítési mód kiválasztását leíró](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) cikkben talál.

Ha nem engedélyezi az SQL Server-hitelesítést, akkor a helyi virtuális gépen lévő rendszergazdai fiók használatával is csatlakozhat az SQL Server-példányhoz.

![SQL Server-hitelesítés](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

### <a name="azure-key-vault-integration"></a>Azure Key Vault-integráció

Ha biztonsági titkokat az Azure-ban szeretne titkosítva tárolni, kattintson az **Azure key vault integration** (Azure Key Vault-integráció) elemre, majd az **Enable** (Engedélyezés) elemre.

![Azure Key Vault-integráció](media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

A következő táblázat tartalmazza az Azure Key Vault-integráció konfigurálásához szükséges paramétereket.

| PARAMÉTER | LEÍRÁS | PÉLDA |
| --- | --- | --- |
| **Key Vault URL** |A Key Vault helye. |https:\//contosokeyvault.vault.azure.net/ |
| **Egyszerű név** |Az Azure Active Directory szolgáltatás egyszerű neve. Ezt a nevet ügyfél-azonosítónak is hívják. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Egyszerű titok** |Az Azure Active Directory szolgáltatás egyszerű titka. Ezt a titkot ügyféltitoknak is hívják. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Hitelesítő adat neve** |**Hitelesítő adat neve**: AKV-integráció az SQL Serverrel, így a virtuális Géphez a key vault eléréséhez hitelesítő adatot hoz létre. Válasszon egy nevet ennek a hitelesítő adatnak. |mycred1 |

További információkért lásd: [Configure Azure Key Vault Integration for SQL Server on Azure VMs](virtual-machines-windows-ps-sql-keyvault.md) Az Azure Key Vault-integráció konfigurálása az SQL Serverhez Azure virtuális gépeken.

### <a name="storage-configuration"></a>Tároló konfigurálása

A **tárolási konfigurációt**válassza **konfigurációjának módosítása** tárolási követelmények megadásához.


> [!NOTE]
> Ha manuálisan konfigurálta a virtuális gépet a Standard szintű tárolás használatára, ez a beállítás nem érhető el. Az automatikus tárolóoptimalizálás csak a prémium szintű Storage esetében érhető el.

> [!TIP]
> Az egyes csúszkák lépéseinek száma és felső határértéke a kiválasztott virtuális gép méretétől függ. A nagyobb méretű és teljesítményű virtuális gépeken nagyobb mértékű vertikális felskálázás végezhető.

Megadhat különböző követelményeket, például a kimeneti/bemeneti műveletek másodpercenkénti számát (IOPS), a MB/másodpercben megadott átviteli sebességet, illetve a tárterület teljes méretét. Ezeket az értékeket a csúszkákkal állíthatja be. Ezek a tárolóbeállítások a számítási feladatok mennyiségéhez igazodva módosíthatók. A portál ezen követelmények alapján automatikusan kiszámítja a csatlakoztatandó és konfigurálandó lemezek számát.

A **Storage optimized for** (Tároló optimalizálási módja) területen válasszon az alábbi lehetőségek közül:

* A **General** (Általános) az alapértelmezett beállítás, amely a legtöbb számítási feladatot támogatja.
* A **Transactional** (Tranzakciós) feldolgozás a tárolót az adatbázisok hagyományos OLTP számítási feladataira optimalizálja.
* A **Data warehousing** (Adatraktározás) beállítás elemzési és jelentéskészítési számítási feladatokra optimalizálja a tárolót.

![SQL Virtuálisgép-tároló konfigurációja](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-storage-configuration.png)

### <a name="sql-server-license"></a>SQL Server-licenc
Ha Ön egy frissítési garanciával rendelkező ügyfél, használhat a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) a saját SQL Server-licencét, és az erőforrások mentéséhez. 

![SQL virtuális gép licenc](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatikus javítás

Az **Automatikus javítás** alapértelmezés szerint engedélyezve van. Az automatizált javítás lehetővé teszi, hogy az Azure automatikus javításokat alkalmazzon az SQL Serveren és az operációs rendszeren. A karbantartási időszak beállításához adja meg a hét egy napját, egy időpontot és egy időtartamot. Az Azure ebben a karbantartási időszakban végzi el a javításokat. A karbantartási időszak ütemezése a virtuális gép területi beállítása szerinti időt használja. Ha nem szeretné, hogy az Azure automatikusan alkalmazza a javításokat az SQL Serveren és az operációs rendszeren, kattintson a **Letiltás** elemre.  

![SQL virtuális gép automatikus javítás](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

További információk: [Automated Patching for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md) (Az SQL Server automatikus javítása Azure virtuális gépeken).

### <a name="automated-backup"></a>Automatikus biztonsági mentés

Az **Automatikus biztonsági mentés** területen engedélyezheti az összes adatbázis automatikus mentését. Az automatikus biztonsági mentés alapértelmezés szerint le van tiltva.

Az SQL automatikus biztonsági mentésének engedélyezésekor konfigurálhatja a következő beállításokat:

* Biztonsági mentések megőrzési ideje (napokban)
* A biztonsági mentésekhez használt tárfiók
* Titkosítási beállítás és a biztonsági mentések jelszavas védelme
* Rendszeradatbázisok biztonsági mentése
* Biztonsági mentések ütemezésének konfigurálása

A biztonsági mentés titkosításához kattintson az **Engedélyezés** elemre. Ezután adja meg a **Jelszót**. Az Azure a biztonsági mentések titkosításához létrehoz egy tanúsítványt, amelyet a megadott jelszóval véd.

További információk: [Automated Backup for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md) (Az SQL Server automatikus biztonsági mentése Azure virtuális gépeken).


### <a name="r-services-advanced-analytics"></a>Az R Services (speciális elemzések)

Lehetősége van ahhoz, hogy [SQL Server R Services (Advanced Analytics)](/sql/advanced-analytics/r/sql-server-r-services/). Ez a beállítás lehetővé teszi, hogy az SQL Server 2017 továbbfejlesztett elemzéseinek használatára. Kattintson az **Engedélyezés** elemre az **SQL Server beállításai** ablakban.


## <a name="4-review--create"></a>4. Áttekintés + létrehozás

Az a **tekintse át + létrehozása** lapon tekintse át az összefoglalást, és válassza ki **létrehozás** hozhat létre SQL Server, erőforráscsoport és erőforrások virtuális Géphez megadott.

Az üzemelő példány az Azure Portalról monitorozható. A képernyő felső részén látható **Értesítések** gomb megjeleníti az üzemelő példány állapotának alapvető információit.

> [!NOTE]
> Az üzembe helyezési idők szemléltetéséhez üzembe helyeztem egy SQL virtuális gépet az USA keleti régiójában az alapértelmezett beállításokkal. Ez az üzembe helyezési teszt hozzávetőlegesen 12 percet vett igénybe. A régiótól és a kiválasztott beállításoktól függően Ön ennél rövidebb vagy hosszabb üzembe helyezési időt is tapasztalhat.

## <a id="remotedesktop"></a> A virtuális gép megnyitása a távoli asztallal

A következő lépésekkel csatlakozzon az SQL Server virtuális géphez a távoli asztalról:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Az SQL Server virtuális géphez való csatlakozás után elindíthatja az SQL Server Management Studiót, és a helyi rendszergazdai hitelesítő adataival csatlakozhat Windows-hitelesítés használatával. Ha engedélyezte az SQL Server-hitelesítést, akkor az SQL-hitelesítésen keresztül is csatlakozhat a kiépítés során megadott SQL bejelentkezési azonosítójával és jelszavával.

A géphez való hozzáférés lehetővé teszi, hogy igény szerint közvetlenül módosítsa a gép és az SQL Server beállításait. Például konfigurálhatja a tűzfal beállításait, vagy módosíthatja az SQL Server-konfiguráció beállításait.

## <a id="connect"></a> Távoli csatlakozás az SQL Serverhez

Ez az útmutató kiválasztott **nyilvános** hozzáférés a virtuális gép és **SQL Server-hitelesítés**. Ezek a beállítások automatikusan úgy konfigurálták a virtuális gépet, hogy az az internetről bármely ügyfél számára engedélyezi az SQL Serverhez való csatlakozást (feltéve, hogy helyes SQL-bejelentkezési névvel rendelkeznek).

> [!NOTE]
> Ha az üzembe helyezés során nem a nyilvános hozzáférést választotta, akkor az üzembe helyezés után a portálon keresztül módosíthatja az SQL csatlakozási beállításait. További információ: [Az SQL csatlakozási beállításainak módosítása](virtual-machines-windows-sql-connect.md#change).

A következő szakaszok bemutatják, hogyan csatlakozhat az SQL Server Virtuálisgép-példányhoz az interneten keresztül.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Ebben a példában a közös 1433-as portot használja. Azonban ez az érték lehet módosítani, ha egy másik portot (például az 1401) lett megadva, az SQL Server rendszerű virtuális gép központi telepítése során kell. 


## <a name="next-steps"></a>További lépések

További információ az SQL Server használatáról az Azure-ban: [SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) (SQL Server az Azure virtuális gépeken) és [Frequently Asked Questions](virtual-machines-windows-sql-server-iaas-faq.md) (Gyakran ismételt kérdések).