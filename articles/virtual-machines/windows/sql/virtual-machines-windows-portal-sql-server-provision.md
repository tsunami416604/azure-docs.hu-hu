---
title: "Windows rendszerű SQL Server 2017 virtuális gép létrehozása az Azure-ban | Microsoft Docs"
description: "Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Windows rendszerű SQL Server 2017 virtuális gépet az Azure Portalon."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: jroth
ms.openlocfilehash: 48f9f97d6e0aee6b2c84444289a427bebcb296e2
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Windows rendszerű SQL Server virtuális gép létrehozása az Azure Portalon

> [!div class="op_single_selector"]
> * [Portál](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Ez a rövid útmutató bemutatja, hogyan hozható létre az Azure Portal segítségével egy Windows rendszerű virtuális gép, amelyen telepítve van az SQL Server.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* [SQL virtuálisgép-rendszerkép kiválasztása a katalógusból](#select)
* [A virtuális gép konfigurálása és létrehozása](#configure)
* [A virtuális gép megnyitása a távoli asztallal](#remotedesktop)
* [Távoli csatlakozás az SQL Serverhez](#connect)

## <a id="select"></a> SQL virtuálisgép-rendszerkép kiválasztása a katalógusból

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) a saját fiókjával.

   > [!NOTE]
   > Ha nem rendelkezik Azure-fiókkal, az [Azure ingyenes próbát](https://azure.microsoft.com/pricing/free-trial/) biztosít.

1. Az Azure Portalon kattintson az **Új** elemre. A portál megnyitja az **Új** ablakot.

1. Az **Új** ablakban kattintson a **Számítás**, majd az **Összes megjelenítése** elemre.

   ![Új számítás ablak](./media/virtual-machines-windows-portal-sql-server-provision/azure-new-compute-blade.png)

1. A keresőmezőbe írja be az **SQL Server 2017** kifejezést, majd nyomja le az ENTER billentyűt.

1. Ezután kattintson a **Szűrő** ikonra.

1. A Szűrő ablakban jelölje be a **Windows-alapú** alkategóriát, és válassza a **Microsoft** lehetőséget a közzétevő megadásánál. Ezután kattintson a **Kész** gombra, hogy az eredményeket a Microsoft által közzétett Windows SQL Server-rendszerképekre szűkítése.

   ![Azure-beli virtuális gépek ablak](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade2.png)

1. Tekintse át az elérhető SQL Server-rendszerképeket. Minden rendszerkép egy SQL Server-verziót és egy operációs rendszert azonosít.

1. Válassza az **Ingyenes SQL Server-licenc: Windows Server 2016-on futó SQL Server 2017 Developer** nevű rendszerképet.

   > [!TIP]
   > Ez az oktatóanyag azért a Developer kiadást alkalmazza, mert az az SQL Server teljes körű verziója, amely fejlesztési-tesztelési célokra ingyenesen használható. Csak a virtuális gép futtatásával járó költségeket kell kifizetni. Azonban ebben az oktatóanyagban bármelyik rendszerképet választhatja.

   > [!TIP]
   > Az SQL-alapú virtuális gépek rendszerképeinek percalapú díjszabása tartalmazza az SQL-kiszolgáló licencelési költségeit (kivéve a Developer és az Express kiadások esetén). Az SQL Server Developer ingyenesen használható fejlesztési/tesztelési célokra (üzemi környezetben nem), az SQL Express pedig ingyenesen használható kisebb számítási feladatokhoz (1 GB-nál kisebb memória, 10 GB-nál kevesebb tárhely). Van egy másik lehetőség is: a saját licenc használata (BYOL), amely esetben csak a virtuális gépért kell fizetni. Az ilyen rendszerképek nevei {BYOL} előtagot kapnak. 
   >
   > A lehetőségekkel kapcsolatos további információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

1. Ellenőrizze, hogy a **Telepítési modell kiválasztása** alatt a **Resource Manager** van-e kiválasztva. Az új virtuális gépek esetén az ajánlott üzemi modell a Resource Manager. 

1. Kattintson a **Létrehozás** gombra.

    ![SQL virtuális gép létrehozása a Resource Manager használatával](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a id="configure"></a> A virtuális gép konfigurálása
Az SQL Server rendszerű virtuális gépek konfigurálására öt ablak szolgál.

| Lépés | Leírás |
| --- | --- |
| **Alapvető beállítások** |[Az alapvető beállítások konfigurálása](#1-configure-basic-settings) |
| **Méret** |[A virtuális gép méretének kiválasztása](#2-choose-virtual-machine-size) |
| **Beállítások** |[Választható funkciók konfigurálása](#3-configure-optional-features) |
| **Az SQL Server beállításai** |[Az SQL Server beállításainak konfigurálása](#4-configure-sql-server-settings) |
| **Összefoglalás** |[Az összefoglalás áttekintése](#5-review-the-summary) |

## <a name="1-configure-basic-settings"></a>1. Az alapvető beállítások konfigurálása

Az **Alapvető beállítások** ablakban adja meg a következő információkat:

* Adjon meg egy egyedi **nevet** a virtuális gép számára.

* A virtuális gép lemeztípusaként válassza az **SSD** beállítást az optimális teljesítmény érdekében.

* Adjon meg egy **felhasználónevet** a virtuális gép helyi rendszergazdai fiókja számára. Ezt a fiókot a szolgáltatás hozzáadja az SQL Server **sysadmin** rögzített kiszolgálói szerepköréhez.

* Adjon meg egy erős **jelszót**.

* Ha több előfizetéssel is rendelkezik, ellenőrizze, hogy az új virtuális géphez a helyes előfizetést adta-e meg.

* Az **Erőforráscsoport** szövegmezőjében adja meg egy új erőforráscsoport nevét. Meglévő erőforráscsoport használatához kattintson a **Meglévő használata** lehetőségre. Az erőforráscsoportok az Azure-ban egymáshoz kapcsolódó erőforrásokból (virtuális gépekből, tárfiókokból, virtuális hálózatokból stb.) álló gyűjtemények.

  > [!NOTE]
  > Az Azure szolgáltatásban futó SQL Server üzemelő példányok tesztelésekor vagy a velük való megismerkedéskor érdemes egy új erőforráscsoportot használni. A tesztelés befejezése után törölje az erőforráscsoportot a virtuális gép és az erőforráscsoporthoz társított összes erőforrás automatikus törléséhez. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../../../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).

* Válassza ki a **helyet** az üzemelő példányt futtató Azure-régiónak megfelelően.

* Kattintson az **OK** gombra a beállítások mentéséhez.

    ![Alapvető SQL-beállítások ablak](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. A virtuális gép méretének kiválasztása

A **Méret** lépésben válassza ki a virtuális gép méretét a **Méret kiválasztása** ablakban. Az ablak kezdetben a választott rendszerkép alapján jeleníti meg az ajánlott gépméreteket.

> [!IMPORTANT]
> A **Méret kiválasztása** ablakban megjelenő, becsült havi költségek nem tartalmazzák az SQL Server licencelési költségeit. Ez egyedül a virtuális gép költsége. Az SQL Server Express és Developer kiadásai esetében ez a teljes becsült költség. Más kiadások esetében tekintse meg a [Windows rendszerű virtuális gépek árképzését ismertető oldalt](https://azure.microsoft.com/pricing/details/virtual-machines/windows/), és válassza ki az SQL Server megfelelő kiadását. Lásd még [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

![SQL virtuális gépek méretbeállításai](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Az éles számítási feladatok esetében ajánlott gépméretekért és -konfigurációkért tekintse meg [az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növelésével kapcsolatos ajánlott eljárásokat](virtual-machines-windows-sql-performance.md). Ha nem látja a kívánt gépméretet, kattintson **Az összes megtekintése** gombra.

> [!NOTE]
> További információ a virtuális gépek méretével kapcsolatban: [Virtuális gépek méretei](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Válassza ki a virtuális gép méretét, majd kattintson a **Kijelölés** elemre.

## <a name="3-configure-optional-features"></a>3. Választható funkciók konfigurálása

A **Beállítások** ablakban konfigurálhatja az Azure-tárolót, a hálózatot és a virtuális gép monitorozását.

* A **Tároló** területen válassza az **Igen** lehetőséget a **Felügyelt lemezek használata** beállítás alatt.

   > [!NOTE]
   > A Microsoft a Managed Disks használatát javasolja az SQL Server esetében. A Managed Disks szolgáltatás a háttérben kezeli a tárterületet. Emellett ha ugyanabban a rendelkezésre állási csoportban több, a Managed Diskset használó virtuális gép található, az Azure elosztja a tárolási erőforrásokat, hogy megfelelő redundanciát biztosítson. További információkért lásd az [Azure Managed Disks áttekintését](../../../storage/storage-managed-disks-overview.md). A felügyelt lemezek a rendelkezésre állási csoportokban való használatával kapcsolatos részletekért lásd: [Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel](../manage-availability.md).

* A **Hálózat** alatt elfogadhatja az automatikusan kitöltött értékeket. Az egyes funkciókra kattintva manuálisan is konfigurálhatja a **virtuális hálózatot**, az **alhálózatot**, a **nyilvános IP-címet** és a **hálózati biztonsági csoportot**. A jelen oktatóanyag esetén használja az alapértelmezett értékeket.

* Alapértelmezés szerint az Azure ugyanazzal a tárfiókkal engedélyezi a **Figyelést**, amely a virtuális géphez ki lett jelölve. Ezeket a beállításokat itt módosíthatja.

* Ebben az oktatóanyagban a **Rendelkezésre állási csoport** területen meghagyhatja az alapértelmezett **nincs** beállítást. Ha SQL AlwaysOn rendelkezésre állási csoportok beállítását tervezi, konfigurálja a rendelkezésre állást, hogy később ne kelljen újra létrehoznia a virtuális gépet.  További információk: [Manage the Availability of Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Virtuális gépek rendelkezésre állásának kezelése).

Ha végzett a beállítások konfigurálásával, kattintson az **OK** gombra.

## <a name="4-configure-sql-server-settings"></a>4. Az SQL Server beállításainak konfigurálása
Az **SQL Server beállításai** ablakban konfigurálhatja az SQL Server adott beállításait és optimalizálási lehetőségeit. Az SQL Server konfigurálható beállításai közé az alábbiak tartoznak.

| Beállítás |
| --- |
| [Kapcsolatok](#connectivity) |
| [Hitelesítés](#authentication) |
| [Tároló konfigurálása](#storage-configuration) |
| [Automatikus javítás](#automated-patching) |
| [Automatikus biztonsági mentés](#automated-backup) |
| [Azure Key Vault-integráció](#azure-key-vault-integration) |
| [SQL Server Machine Learning-szolgáltatások](#sql-server-machine-learning-services) |

### <a name="connectivity"></a>Kapcsolatok

Az **SQL kapcsolatok** alatt adja meg, milyen típusú hozzáférést szeretne az ezen a virtuális gépen futó SQL Server-példányhoz. A jelen oktatóanyag esetén válassza a **Nyilvános (internet)** lehetőséget, hogy az SQL Serverhez csatlakozhassanak gépek vagy szolgáltatások az interneten keresztül. Ha ezt a lehetőséget választja, az Azure automatikusan úgy konfigurálja a tűzfalat és a hálózati biztonsági csoportot, hogy az 1433-as porton engedélyezve legyen a forgalom.

![SQL kapcsolati lehetőségek](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

> [!TIP]
> Alapértelmezés szerint az SQL Server a jól ismert **1433-as** portot figyeli. A nagyobb biztonság érdekében az előző párbeszédpanelen módosítsa a portot egy nem alapértelmezett, például az 1401-es portra. Ha így tesz, ehhez a porthoz kell csatlakoznia az összes ügyféleszközzel, például az SSMS-sel.

Ahhoz, hogy az SQL Serverhez az interneten keresztül csatlakozni lehessen, engedélyeznie kell az SQL Server-hitelesítést is, aminek ismertetését a következő szakaszban olvashatja.

Ha nem szeretné engedélyezni az adatbázis-alrendszerhez az interneten keresztül való csatlakozást, válassza az alábbi lehetőségek valamelyikét:

* A **Helyi (csak virtuális gépen belül)** beállítással az SQL Serverhez csak a virtuális gépen belülről lehet csatlakozni.
* A **Magánjellegű (virtuális hálózaton belül)** beállítással az SQL Serverhez az azonos virtuális hálózaton található gépek és szolgáltatások csatlakozhatnak.

Általánosságban elmondható, hogy a forgatókönyv által lehetővé tett legszigorúbb kapcsolódási korlátozás kiválasztásával növelhető a biztonság. Hálózati biztonsági csoportszabályok és SQL-/Windows-hitelesítés használatával azonban mindegyik lehetőség biztosítható. A hálózati biztonsági csoportot a virtuális gép létrehozása után szerkesztheti. További információkért lásd [az SQL Server Azure-beli virtuális gépeken történő futtatásának biztonsági szempontjait](virtual-machines-windows-sql-security.md).

> [!NOTE]
> Az SQL Server Express kiadás virtuálisgép-rendszerképe nem engedélyezi automatikusan a TCP/IP-protokollt. Ez még a nyilvános és a magán kapcsolati lehetőségekre is érvényes. Az Express kiadás esetén a virtuális gép létrehozása után [manuálisan kell engedélyezni a TCP/IP-protokollt](#configure-sql-server-to-listen-on-the-tcp-protocol) az SQL Server Configuration Manager használatával.

### <a name="authentication"></a>Authentication

Ha SQL Server-hitelesítésre van szüksége, kattintson az **Engedélyezés** lehetőségre az **SQL-hitelesítés** alatt.

![SQL Server-hitelesítés](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

> [!NOTE]
> Ha az SQL Servert az interneten keresztül tervezi elérni (vagyis a Nyilvános kapcsolódási beállítást választja), akkor itt kell engedélyeznie az SQL-hitelesítést. Az SQL Serverhez való nyilvános hozzáféréshez SQL-hitelesítésre van szükség.
> 
> 

Ha engedélyezi az SQL Server-hitelesítést, adjon meg egy **bejelentkezési nevet** és egy **jelszót**. Ez a felhasználónevet SQL Server-hitelesítési bejelentkezési névként, és egyben a **sysadmin** rögzített kiszolgálói szerepkör egy tagjaként is konfigurálja a szolgáltatás. További információk a hitelesítési módokról: [Choose an Authentication Mode](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) (Hitelesítési mód kiválasztása).

Ha nem engedélyezi az SQL Server-hitelesítést, akkor a helyi virtuális gépen lévő rendszergazdai fiók használatával is csatlakozhat az SQL Server-példányhoz.

### <a name="storage-configuration"></a>Tároló konfigurálása

Kattintson a **Tároló konfigurációja** elemre a tárolási követelmények megadásához.

![SQL-tároló konfigurálása](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

> [!NOTE]
> Ha manuálisan konfigurálta a virtuális gépet a Standard szintű tárolás használatára, ez a beállítás nem érhető el. Az automatikus tárolóoptimalizálás csak a prémium szintű Storage esetében érhető el.

> [!TIP]
> Az egyes csúszkák lépéseinek száma és felső határértéke a kiválasztott virtuális gép méretétől függ. A nagyobb méretű és teljesítményű virtuális gépeken nagyobb mértékű vertikális felskálázás végezhető.

Megadhat különböző követelményeket, például a kimeneti/bemeneti műveletek másodpercenkénti számát (IOPS), a MB/másodpercben megadott átviteli sebességet, illetve a tárterület teljes méretét. Ezeket az értékeket a csúszkákkal állíthatja be. Ezek a tárolóbeállítások a számítási feladatok mennyiségéhez igazodva módosíthatók. A portál ezen követelmények alapján automatikusan kiszámítja a csatlakoztatandó és konfigurálandó lemezek számát.

A **Storage optimized for** (Tároló optimalizálási módja) területen válasszon az alábbi lehetőségek közül:

* A **General** (Általános) az alapértelmezett beállítás, amely a legtöbb számítási feladatot támogatja.
* A **Transactional** (Tranzakciós) feldolgozás a tárolót az adatbázisok hagyományos OLTP számítási feladataira optimalizálja.
* A **Data warehousing** (Adatraktározás) beállítás elemzési és jelentéskészítési számítási feladatokra optimalizálja a tárolót.

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

### <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning-szolgáltatások

Dönthet úgy, hogy engedélyezi az [SQL Server Machine Learning-szolgáltatásokat](https://msdn.microsoft.com/library/mt604845.aspx). Ez lehetőséget nyújt az SQL Server 2017 továbbfejlesztett elemzéseinek használatára. Kattintson az **Engedélyezés** elemre az **SQL Server beállításai** ablakban.

![SQL Server Machine Learning-szolgáltatások engedélyezése](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

Amikor végzett az SQL Server beállításainak konfigurálásával, kattintson az **OK** gombra.

## <a name="5-review-the-summary"></a>5. Összegzés áttekintése

Az **Összefoglalás** ablakban tekintse át az összefoglalást, majd kattintson a **Vásárlás** gombra a virtuális géphez megadott SQL Server, erőforráscsoport és erőforrások létrehozásához.

Az üzemelő példány az Azure Portalról monitorozható. A képernyő felső részén látható **Értesítések** gomb megjeleníti az üzemelő példány állapotának alapvető információit.

> [!NOTE]
> Az üzembe helyezési idők szemléltetéséhez üzembe helyeztem egy SQL virtuális gépet az USA keleti régiójában az alapértelmezett beállításokkal. Ez az üzembe helyezési teszt hozzávetőlegesen 12 percet vett igénybe. A régiótól és a kiválasztott beállításoktól függően Ön ennél rövidebb vagy hosszabb üzembe helyezési időt is tapasztalhat.

## <a id="remotedesktop"></a> A virtuális gép megnyitása a távoli asztallal

A következő lépésekkel csatlakozzon az SQL Server virtuális géphez a távoli asztalról:

> [!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Az SQL Server virtuális géphez való csatlakozás után elindíthatja az SQL Server Management Studiót, és a helyi rendszergazdai hitelesítő adataival csatlakozhat Windows-hitelesítés használatával. Ha engedélyezte az SQL Server-hitelesítést, akkor az SQL-hitelesítésen keresztül is csatlakozhat a kiépítés során megadott SQL bejelentkezési azonosítójával és jelszavával.

A géphez való hozzáférés lehetővé teszi, hogy igény szerint közvetlenül módosítsa a gép és az SQL Server beállításait. Például konfigurálhatja a tűzfal beállításait, vagy módosíthatja az SQL Server-konfiguráció beállításait.

## <a name="enable-tcpip-for-developer-and-express-editions"></a>TCP/IP engedélyezése a Developer és Express kiadásokhoz

Új SQL Server virtuális gép üzembe helyezésekor az Azure nem engedélyezi automatikusan a TCP/IP protokollt az SQL Server Developer és Express kiadásaihoz. Az alábbi lépések ismertetik, hogyan lehet manuálisan engedélyezni a TCP/IP protokollt, hogy távolról is csatlakozhasson IP-címmel.

Az alábbi lépések az **SQL Server Configuration Manager** segítségével engedélyezik a TCP/IP protokollt az SQL Server Developer és Express kiadásaihoz.

> [!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a id="connect"></a> Távoli csatlakozás az SQL Serverhez

Ebben az útmutatóban **Nyilvános** hozzáférést választottunk a virtuális géphez és **SQL Server-hitelesítéshez**. Ezek a beállítások automatikusan úgy konfigurálták a virtuális gépet, hogy az az internetről bármely ügyfél számára engedélyezi az SQL Serverhez való csatlakozást (feltéve, hogy helyes SQL-bejelentkezési névvel rendelkeznek).

> [!NOTE]
> Ha az üzembe helyezés során nem a nyilvános hozzáférést választotta, akkor az üzembe helyezés után a portálon keresztül módosíthatja az SQL csatlakozási beállításait. További információ: [Az SQL csatlakozási beállításainak módosítása](virtual-machines-windows-sql-connect.md#change).

A következő szakaszok bemutatják, hogyan csatlakozhat a virtuális gépén található SQL Server-példányhoz egy másik számítógépről az interneten keresztül.

> [!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Következő lépések

További információ az SQL Server használatáról az Azure-ban: [SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) (SQL Server az Azure virtuális gépeken) és [Frequently Asked Questions](virtual-machines-windows-sql-server-iaas-faq.md) (Gyakran ismételt kérdések).