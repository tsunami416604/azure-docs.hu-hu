---
title: "SQL Server-rendszerű virtuális gép kiépítése | Microsoft Docs"
description: "Az Azure-ban létrehozhat SQL Server rendszerű virtuális gépet, és csatlakozhat hozzá a portál használatával. Ez az oktatóanyag a Resource Manager módot használja."
services: virtual-machines-windows
documentationcenter: na
author: rothja
editor: 
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: jroth
experimental: true
experimental_id: a641df96-f27d-40
ms.translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: e16792bb762287bc16c280386981a4d442448674
ms.contentlocale: hu-hu
ms.lasthandoff: 04/22/2017


---
# <a name="provision-a-sql-server-virtual-machine-in-the-azure-portal"></a>SQL Server rendszerű virtuális gép létrehozása az Azure portálon
> [!div class="op_single_selector"]
> * [Portál](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
> 
> 

Ez az átfogó oktatóanyag bemutatja, hogyan hozhat létre az SQL Servert futtató virtuális gépet az Azure portál használatával.

Az Azure virtuálisgép-katalógusában számos olyan rendszerkép található, amely a Microsoft SQL Servert tartalmazza. Mindössze néhány kattintással kiválaszthatja az SQL virtuálisgép-rendszerképek egyikét a katalógusban, és létrehozhatja azt az Azure környezetben.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* [SQL virtuálisgép-rendszerkép kiválasztása a katalógusból](#select-a-sql-vm-image-from-the-gallery)
* [A virtuális gép konfigurálása és létrehozása](#configure-the-vm)
* [A virtuális gép megnyitása a távoli asztallal](#open-the-vm-with-remote-desktop)
* [Távoli csatlakozás az SQL Serverhez](#connect-to-sql-server-remotely)

## <a name="select-a-sql-vm-image-from-the-gallery"></a>SQL virtuálisgép-rendszerkép kiválasztása a katalógusból
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) a saját fiókjával.

   > [!NOTE]
   > Ha nem rendelkezik Azure-fiókkal, az [Azure ingyenes próbát](https://azure.microsoft.com/pricing/free-trial/) biztosít.

2. Az Azure Portalon kattintson az **Új** elemre. A portál megnyitja az **Új** panelt. Az SQL Server virtuálisgép-erőforrásai a Marketplace **Számítás** csoportjában találhatók.
3. Az **Új** panelen kattintson a **Számítás**, majd az **Összes megjelenítése** elemre.
4. A **Szűrő** szövegmezőbe írja be az SQL Server kifejezést, majd nyomja le az ENTER billentyűt.

   ![Az Azure Virtuális gépek panelje](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade2.png)

5. Tekintse át az elérhető SQL Server-rendszerképeket. Minden rendszerkép egy SQL Server-verziót és egy operációs rendszert azonosít. 
6. Válassza ki a Windows Server 2016-on futó SQL Server 2016 SP1 Developer rendszerképét.

   > [!TIP]
   > Ez az oktatóanyag azért a Developer kiadást alkalmazza, mert az az SQL Server teljes körű verziója, amely fejlesztési-tesztelési célokra ingyenesen használható. Csak a virtuális gép futtatásával járó költségeket kell kifizetni.

   > [!NOTE]
   > Az SQL-alapú virtuális gépek rendszerképeinek percalapú díjszabása tartalmazza az SQL-kiszolgáló licencelési költségeit (kivéve a Developer és az Express kiadások esetén). Az SQL Server Developer ingyenesen használható fejlesztési/tesztelési célokra (üzemi környezetben nem), az SQL Express pedig ingyenesen használható kisebb számítási feladatokhoz (1 GB-nál kisebb memória, 10 GB-nál kevesebb tárhely).
   > Van egy másik lehetőség is: a saját licenc használata (BYOL), amely esetben csak a virtuális gépért kell fizetni. Az ilyen rendszerképek nevei {BYOL} előtagot kapnak. A lehetőségekkel kapcsolatos további információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

7. Ellenőrizze, hogy a **Telepítési modell kiválasztása** alatt a **Resource Manager** van-e kiválasztva. Az új virtuális gépek esetén az ajánlott üzemi modell a Resource Manager. Kattintson a **Létrehozás** gombra.

    ![SQL virtuális gép létrehozása a Resource Manager használatával](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a name="configure-the-vm"></a>A virtuális gép konfigurálása
Az SQL Server rendszerű virtuális gépek konfigurálásra öt panel szolgál.

| Lépés | Leírás |
| --- | --- |
| **Alapvető beállítások** |[Az alapvető beállítások konfigurálása](#1-configure-basic-settings) |
| **Méret** |[A virtuális gép méretének kiválasztása](#2-choose-virtual-machine-size) |
| **Beállítások** |[Választható funkciók konfigurálása](#3-configure-optional-features) |
| **Az SQL Server beállításai** |[Az SQL Server beállításainak konfigurálása](#4-configure-sql-server-settings) |
| **Összefoglalás** |[Az összefoglalás áttekintése](#5-review-the-summary) |

## <a name="1-configure-basic-settings"></a>1. Az alapvető beállítások konfigurálása
Az **Alapvető beállítások** panelen adja meg a következő információkat:

* Adjon meg egy egyedi **nevet** a virtuális gép számára.
* Adjon meg egy **felhasználónevet** a virtuális gép helyi rendszergazdai fiókja számára. Ezt a fiókot a szolgáltatás hozzáadja az SQL Server **sysadmin** rögzített kiszolgálói szerepköréhez.
* Adjon meg egy erős **jelszót**.
* Ha több előfizetéssel is rendelkezik, ellenőrizze, hogy az új virtuális géphez a helyes előfizetést adta-e meg.
* Az **Erőforráscsoport** szövegmezőjében adja meg egy új erőforráscsoport nevét. Meglévő erőforráscsoport használatához kattintson a **Meglévő használata** lehetőségre. Az erőforráscsoportok az Azure-ban egymáshoz kapcsolódó erőforrásokból (virtuális gépekből, tárfiókokból, virtuális hálózatokból stb.) álló gyűjtemények.
  
  > [!NOTE]
  > Az Azure szolgáltatásban futó SQL Server üzemelő példányok tesztelésekor vagy a velük való megismerkedéskor érdemes egy új erőforráscsoportot használni. A tesztelés befejezése után törölje az erőforráscsoportot a virtuális gép és az erőforráscsoporthoz társított összes erőforrás automatikus törléséhez. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../../../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).
  > 
  > 
* Válasszon egy **helyet** az üzemelő példánynak.
* Kattintson az **OK** gombra a beállítások mentéséhez.
  
    ![Alapvető SQL-beállítások panel](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. A virtuális gép méretének kiválasztása
A **Méret** lépésben válassza ki a virtuális gép méretét a **Méret kiválasztása** panelen. A panel kezdetben a választott rendszerkép alapján jeleníti meg az ajánlott gépméreteket.

> [!IMPORTANT]
> A **Méret kiválasztása** panelen megjelenő, becsült havi költségek nem tartalmazzák az SQL Server licencelési költségeit. Ez egyedül a virtuális gép költsége. Az SQL Server Express és Developer kiadásai esetében ez a teljes becsült költség. Más kiadások esetében tekintse meg a [Windows rendszerű virtuális gépek árképzését ismertető oldalt](https://azure.microsoft.com/pricing/details/virtual-machines/windows/), és válassza ki az SQL Server megfelelő kiadását. Lásd még [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

![SQL virtuális gépek méretbeállításai](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

A termelési számítási feladatokhoz ajánlott olyan virtuálisgép-méretet választani, amely támogatja a [Premium Storage](../../../storage/storage-premium-storage.md) tárolást. Ha nincs szüksége ekkora szintű teljesítményre, az **Összes megjelenítés** gombbal megjelenítheti az összes lehetséges gépméretet. Fejlesztési vagy tesztelési környezetben például érdemes kisebb méretű gépet használni.

> [!NOTE]
> További információ a virtuális gépek méretével kapcsolatban: [Virtuális gépek méretei](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Az SQL Server rendszerű virtuális gépek méretével kapcsolatos megfontolások: [Performance best practices for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-performance.md) (Az SQL Server teljesítményéhez kapcsolódó ajánlott eljárások Azure virtuális gépek esetén).

Válassza ki a virtuális gép méretét, majd kattintson a **Kijelölés** elemre.

## <a name="3-configure-optional-features"></a>3. Választható funkciók konfigurálása
A **Beállítások** panelen konfigurálhatja az Azure-tárolót, a hálózatot és a virtuális gép figyelését.

* A **Tárolás** alatt a **Lemez típusa** értékeként adja meg a Standard vagy a Prémium (SSD) lehetőséget. A termelési számítási feladatokhoz a Prémium szintű Storage ajánlott.

> [!NOTE]
> Ha a Prémium (SSD) lehetőséget választja egy olyan méretű géphez, amely nem támogatja a Prémium szintű Storage tárolást, akkor a gép méretét a szolgáltatás automatikus módosítja.  
> 
> 

* A **Tárfiók** alatt elfogadhatja az automatikusan megadott tárfióknevet. A **Tárfiók** elemre kattintva választhat egy meglévő fiókot is, és konfigurálhatja a tárfiók típusát. Alapértelmezés szerint az Azure egy új fiókot hoz létre helyileg redundáns tárolással. További információ a tárolási lehetőségekről: [Azure Storage replication](../../../storage/storage-redundancy.md) (Az Azure Storage replikációja).
* A **Hálózat** alatt elfogadhatja az automatikusan kitöltött értékeket. Az egyes funkciókra kattintva manuálisan is konfigurálhatja a **virtuális hálózatot**, az **alhálózatot**, a **nyilvános IP-címet** és a **hálózati biztonsági csoportot**. A jelen oktatóanyag esetén használja az alapértelmezett értékeket.
* Alapértelmezés szerint az Azure ugyanazzal a tárfiókkal engedélyezi a **Figyelést**, amely a virtuális géphez ki lett jelölve. Ezeket a beállításokat itt módosíthatja.
* A **Rendelkezésre állási csoport** alatt adjon meg egy rendelkezésre állási csoportot. A jelen oktatóanyag esetén válassza a **Nincs** lehetőséget. Ha SQL AlwaysOn rendelkezésre állási csoportok beállítását tervezi, konfigurálja a rendelkezésre állást, hogy később ne kelljen újra létrehoznia a virtuális gépet.  További információk: [Manage the Availability of Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Virtuális gépek rendelkezésre állásának kezelése).

Ha végzett a beállítások konfigurálásával, kattintson az **OK** gombra.

## <a name="4-configure-sql-server-settings"></a>4. Az SQL Server beállításainak konfigurálása
Az **SQL Server beállításai** panelen konfigurálhatja az SQL Server adott beállításait és optimalizálási lehetőségeit. Az SQL Server konfigurálható beállításai közé az alábbiak tartoznak.

| Beállítás |
| --- |
| [Kapcsolatok](#connectivity) |
| [Hitelesítés](#authentication) |
| [Tároló konfigurálása](#storage-configuration) |
| [Automatikus javítás](#automated-patching) |
| [Automatikus biztonsági mentés](#automated-backup) |
| [Azure Key Vault-integráció](#azure-key-vault-integration) |
| [R-szolgáltatások](#r-services) |

### <a name="connectivity"></a>Kapcsolatok
Az **SQL kapcsolatok** alatt adja meg, milyen típusú hozzáférést szeretne az ezen a virtuális gépen futó SQL Server-példányhoz. A jelen oktatóanyag esetén válassza a **Nyilvános (internet)** lehetőséget, hogy az SQL Serverhez csatlakozhassanak gépek vagy szolgáltatások az interneten keresztül. Ha ezt a lehetőséget választja, az Azure automatikusan úgy konfigurálja a tűzfalat és a hálózati biztonsági csoportot, hogy az 1433-as porton engedélyezve legyen a forgalom.  

![SQL kapcsolati lehetőségek](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

Ahhoz, hogy az SQL Serverhez az interneten keresztül csatlakozni lehessen, engedélyeznie kell az SQL Server-hitelesítést is, aminek ismertetését a következő szakaszban olvashatja.

> [!NOTE]
> Az SQL Server rendszerű virtuális gép hálózati kommunikációja többféleképpen is korlátozható. Ehhez a virtuális gép létrehozása után szerkessze a hálózati biztonsági csoportot. További információ: [What is a Network Security Group (NSG)?](../../../virtual-network/virtual-networks-nsg.md) (Mi az a hálózati biztonsági csoport?).
> 
> 

Ha nem szeretné engedélyezni az adatbázis-alrendszerhez az interneten keresztül való csatlakozást, válassza az alábbi lehetőségek valamelyikét:

* A **Helyi (csak virtuális gépen belül)** beállítással az SQL Serverhez csak a virtuális gépen belülről lehet csatlakozni.
* A **Magánjellegű (virtuális hálózaton belül)** beállítással az SQL Serverhez az azonos virtuális hálózaton található gépek és szolgáltatások csatlakozhatnak.

> [!NOTE]
> Az SQL Server Express kiadás virtuálisgép-rendszerképe nem engedélyezi automatikusan a TCP/IP-protokollt. Ez még a nyilvános és a magán kapcsolati lehetőségekre is érvényes. Az Express kiadás esetén a virtuális gép létrehozása után [manuálisan kell engedélyezni a TCP/IP-protokollt](#configure-sql-server-to-listen-on-the-tcp-protocol) az SQL Server Configuration Manager használatával.
> 
> 

Általánosságban elmondható, hogy a forgatókönyv által lehetővé tett legszigorúbb kapcsolódási korlátozás kiválasztásával növelhető a biztonság. Hálózati biztonsági csoportszabályok és SQL-/Windows-hitelesítés használatával azonban mindegyik lehetőség biztosítható.

A **Port** alapértelmezett beállítás 1433. Megadhat eltérő portszámot is.
További információ: [Csatlakozás SQL Server rendszerű virtuális géphez (Resource Manager) | Microsoft Azure](virtual-machines-windows-sql-connect.md).

### <a name="authentication"></a>Hitelesítés
Ha SQL Server-hitelesítésre van szüksége, kattintson az **Engedélyezés** lehetőségre az **SQL-hitelesítés** alatt.

![SQL Server-hitelesítés](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

> [!NOTE]
> Ha az SQL Servert az interneten keresztül tervezi elérni (vagyis a Nyilvános kapcsolódási beállítást választja), akkor itt kell engedélyeznie az SQL-hitelesítést. Az SQL Serverhez való nyilvános hozzáféréshez SQL-hitelesítésre van szükség.
> 
> 

Ha engedélyezi az SQL Server-hitelesítést, adjon meg egy **bejelentkezési nevet** és egy **jelszót**. Ez a felhasználónevet SQL Server-hitelesítési bejelentkezési névként, és egyben a **sysadmin** rögzített kiszolgálói szerepkör egy tagjaként is konfigurálja a szolgáltatás. További információk a hitelesítési módokról: [Choose an Authentication Mode](http://msdn.microsoft.com/library/ms144284.aspx) (Hitelesítési mód kiválasztása).

Ha nem engedélyezi az SQL Server-hitelesítést, akkor a helyi virtuális gépen lévő rendszergazdai fiók használatával is csatlakozhat az SQL Server-példányhoz.

### <a name="storage-configuration"></a>Tároló konfigurálása
Kattintson a **Tároló konfigurációja** elemre a tárolási követelmények megadásához.

![SQL-tároló konfigurálása](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

> [!NOTE]
> Ha a standard szintű tárolót választotta, ez a beállítás nem érhető el. Az automatikus tárolóoptimalizálás csak a prémium szintű Storage esetében érhető el.
> 
> 

Megadhat különböző követelményeket, például a kimeneti/bemeneti műveletek másodpercenkénti számát (IOPS), a MB/másodpercben megadott átviteli sebességet, illetve a tárterület teljes méretét. Ezeket az értékeket a csúszkákkal állíthatja be. A portál ezen követelmények alapján automatikusan kiszámítja a lemezek számát.

Alapértelmezés szerint az Azure a tárolót 5000 IOPS értékre, 200 MB/másodperces átviteli sebességre és 1 TB tárterületre optimalizálja. Ezek a tárolóbeállítások a számítási feladatok mennyiségéhez igazodva módosíthatók. A **Storage optimized for** (Tároló optimalizálási módja) területen válasszon az alábbi lehetőségek közül:

* A **General** (Általános) az alapértelmezett beállítás, amely a legtöbb számítási feladatot támogatja.
* A **Transactional** (Tranzakciós) feldolgozás a tárolót az adatbázisok hagyományos OLTP számítási feladataira optimalizálja.
* A **Data warehousing** (Adatraktározás) beállítás elemzési és jelentéskészítési számítási feladatokra optimalizálja a tárolót.

> [!NOTE]
> A csúszkák felső határa a virtuális gép választott méretétől függően változik.
> 
> 

### <a name="automated-patching"></a>Automatikus javítás
Az **Automatikus javítás** alapértelmezés szerint engedélyezve van. Az automatizált javítás lehetővé teszi, hogy az Azure automatikus javításokat alkalmazzon az SQL Serveren és az operációs rendszeren. A karbantartási időszak beállításához adja meg a hét egy napját, egy időpontot és egy időtartamot. Az Azure ebben a karbantartási időszakban végzi el a javításokat. A karbantartási időszak ütemezése a virtuális gép területi beállítása szerinti időt használja. Ha nem szeretné, hogy az Azure automatikusan alkalmazza a javításokat az SQL Serveren és az operációs rendszeren, kattintson a **Letiltás** elemre.  

![SQL automatikus javítás](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

További információk: [Automated Patching for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md) (Az SQL Server automatikus javítása Azure virtuális gépeken).

### <a name="automated-backup"></a>Automatikus biztonsági mentés
Az **Automatikus biztonsági mentés** területen engedélyezheti az összes adatbázis automatikus mentését. Az automatikus biztonsági mentés alapértelmezés szerint le van tiltva.

Az SQL automatikus biztonsági mentésének engedélyezésekor konfigurálhatja a következőket:

* Biztonsági mentések megőrzési ideje (napokban)
* A biztonsági mentésekhez használt tárfiók
* Titkosítási beállítás és a biztonsági mentések jelszavas védelme
* Rendszeradatbázisok biztonsági mentése
* Biztonsági mentések ütemezésének konfigurálása

A biztonsági mentés titkosításához kattintson az **Engedélyezés** elemre. Ezután adja meg a **Jelszót**. Az Azure a biztonsági mentések titkosításához létrehoz egy tanúsítványt, amelyet a megadott jelszóval véd.

![SQL automatikus biztonsági mentés](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup2.png)

 További információk: [Automated Backup for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md) (Az SQL Server automatikus biztonsági mentése Azure virtuális gépeken).

### <a name="azure-key-vault-integration"></a>Azure Key Vault-integráció
Ha biztonsági titkokat az Azure-ban szeretne titkosítva tárolni, kattintson az **Azure key vault integration** (Azure Key Vault-integráció) elemre, majd az **Enable** (Engedélyezés) elemre.

![SQL Azure Key Vault-integráció](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

A következő táblázat tartalmazza az Azure Key Vault-integráció konfigurálásához szükséges paramétereket.

| PARAMÉTER | LEÍRÁS | PÉLDA |
| --- | --- | --- |
| **Key Vault URL** |A Key Vault helye. |https://contosokeyvault.vault.azure.net/ |
| **Egyszerű név** |Az Azure Active Directory szolgáltatás egyszerű neve. Ezt a nevet ügyfél-azonosítónak is hívják. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Egyszerű titok** |Az Azure Active Directory szolgáltatás egyszerű titka. Ezt a titkot ügyféltitoknak is hívják. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Hitelesítő adat neve** |**Hitelesítő adat neve**: Az AKV-integráció létrehoz egy hitelesítő adatot az SQL Serverben, amely hozzáférést biztosít a virtuális gépnek a Key Vaulthoz. Válasszon egy nevet ennek a hitelesítő adatnak. |mycred1 |

További információkért lásd: [Configure Azure Key Vault Integration for SQL Server on Azure VMs](virtual-machines-windows-ps-sql-keyvault.md) Az Azure Key Vault-integráció konfigurálása az SQL Serverhez Azure virtuális gépeken.

Amikor végzett az SQL Server beállításainak konfigurálásával, kattintson az **OK** gombra.

### <a name="r-services"></a>R-szolgáltatások
Lehetősége van az [SQL Server R Services](https://msdn.microsoft.com/library/mt604845.aspx) engedélyezésére. Ez lehetőséget nyújt az SQL Server 2016 továbbfejlesztett elemzéseinek használatára. Kattintson az **Engedélyezés** elemre az **SQL Server beállításai** panelen.

![Az SQL Server R Services engedélyezése](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)


## <a name="5-review-the-summary"></a>5. Összegzés áttekintése
Az **Összefoglalás** panelen tekintse át az összefoglalást, majd kattintson az **OK** gombra a virtuális géphez megadott SQL Server, erőforráscsoport és erőforrások létrehozásához.

Az üzemelő példány az Azure portálról felügyelhető. A képernyő felső részén látható **Értesítések** gomb megjeleníti az üzemelő példány állapotának alapvető információit.

> [!NOTE]
> Az üzembe helyezési idők szemléltetéséhez üzembe helyeztem egy SQL virtuális gépet az USA keleti régiójában az alapértelmezett beállításokkal. Ez az üzembe helyezési teszt összesen 26 percet vett igénybe. A régiótól és a kiválasztott beállításoktól függően Ön ennél rövidebb vagy hosszabb üzembe helyezési időt is tapasztalhat.
> 
> 

## <a name="open-the-vm-with-remote-desktop"></a>A virtuális gép megnyitása a távoli asztallal
A következő lépésekkel csatlakozzon a virtuális géphez a távoli asztalról:

1. Miután az Azure virtuális gép létrejött, az Azure-irányítópulton megjelenik a virtuális gép ikonja. A gépet a meglévő virtuális gépek tallózásával is megkeresheti. Kattintson az új SQL virtuális gépre. A **Virtuális gép** panelen láthatók a virtuális gép részletei.
2. A **Virtuális gép** panel felső részén kattintson a **Csatlakozás** elemre.
3. A böngésző letölt egy RDP-fájlt a virtuális géphez. Nyissa meg az RDP-fájlt.
    ![Távoli asztalról az SQL virtuális géphez](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
4. A távoli asztali kapcsolat arról értesíti, hogy a távoli kapcsolat közzétevője nem azonosítható. Kattintson a **Csatlakozás** gombra a folytatáshoz.
5. A **Windows rendszerbiztonság** párbeszédpanelen kattintson a **Másik fiók használata** elemre.
6. A **Felhasználónév** mezőbe írja be a **\<felhasználónevet>**, ahol a(z) <user name> az a felhasználónév, amelyet a virtuális gép konfigurálásakor megadott. A név elé egy kezdő fordított perjelet kell tenni.
7. Írja be a **Jelszót**, amelyet korábban beállított a virtuális géphez, majd kattintson az **OK** gombra a csatlakozáshoz.
8. Ha egy további **Távoli asztali kapcsolat** párbeszédpanel rákérdez, hogy szeretne-e csatlakozni, kattintson az **Igen** gombra.

Az SQL Server virtuális géphez való csatlakozás után elindíthatja az SQL Server Management Studiót, és a helyi rendszergazdai hitelesítő adataival csatlakozhat Windows-hitelesítés használatával. Ha engedélyezte az SQL Server-hitelesítést, akkor az SQL-hitelesítésen keresztül is csatlakozhat a kiépítés során megadott SQL bejelentkezési azonosítójával és jelszavával.

A géphez való hozzáférés lehetővé teszi, hogy igény szerint közvetlenül módosítsa a gép és az SQL Server beállításait. Például konfigurálhatja a tűzfal beállításait, vagy módosíthatja az SQL Server-konfiguráció beállításait.

## <a name="connect-to-sql-server-remotely"></a>Távoli csatlakozás az SQL Serverhez
Ebben az útmutatóban **Nyilvános** hozzáférést választottunk a virtuális géphez és **SQL Server-hitelesítéshez**. Ezek a beállítások automatikusan úgy konfigurálták a virtuális gépet, hogy az az internetről bármely ügyfél számára engedélyezi az SQL Serverhez való csatlakozást (feltéve, hogy helyes SQL-bejelentkezési névvel rendelkeznek).

> [!NOTE]
> Ha a kiépítés során nem a Nyilvános hozzáférést választotta, akkor az SQL Server-példány internetes eléréséhez további lépések megtétele szükséges. További információ: [Connect to a SQL Server Virtual Machine](virtual-machines-windows-sql-connect.md) (Csatlakozás SQL Server rendszerű virtuális géphez).
> 
> 

A következő szakaszok bemutatják, hogyan csatlakozhat a virtuális gépén található SQL Server-példányhoz egy másik számítógépről az interneten keresztül.

> [!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]
> 
> 

## <a name="next-steps"></a>Következő lépések
További információ az SQL Server használatáról az Azure-ban: [SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) (SQL Server az Azure virtuális gépeken) és [Frequently Asked Questions](virtual-machines-windows-sql-server-iaas-faq.md) (Gyakran ismételt kérdések).

Áttekintő videó az SQL Server Azure virtuális gépeken való használatáról: [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016) (Az Azure VM a legjobb platform az SQL Server 2016-kiszolgálókhoz).

Az Azure virtuális gépeken futó SQL Server [képzési tervének felfedezése](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/).


