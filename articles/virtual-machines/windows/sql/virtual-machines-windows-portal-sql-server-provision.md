---
title: Virtuális gép kiépítése az Azure Portalsegítségével
description: Ez az útmutató ismerteti a Windows SQL Server 2017 virtuális gépek azure portalon való létrehozására vonatkozó lehetőségeket.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 1fdf776570b6f10a363fb98dfe343387d86219d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249788"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Windows SQL Server virtuális gép kiépítése az Azure Portalon

Ez az útmutató részletesen ismerteti a windows OS SQL Server virtuális gép az Azure Portalon létrehozott különböző lehetőségeket. Ez a cikk több konfigurációs lehetőségeket, mint az [SQL Server virtuális gép rövid útmutató,](quickstart-sql-vm-create-portal.md)amely megy keresztül egy lehetséges létesítési feladat. 

Ez az útmutató saját SQL Server virtuális gép létrehozásához. Vagy használja referenciaként az Azure Portalon elérhető lehetőségekhez.

> [!TIP]
> Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a>SQL Server virtuálisgép-gyűjtemény képei

SQL Server virtuális gép létrehozásakor a virtuálisgép-gyűjteményből kiválaszthat egy előre konfigurált lemezképet. Az alábbi lépések bemutatják, hogyan választhatja ki az SQL Server 2017-lemezképek egyikét.

1. Válassza az **Azure SQL** az Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Válassza a **+ Add lehetőséget** az SQL kiválasztása telepítési **lehetőség** lap megnyitásához. A további információkat a **Részletek megjelenítése**lehetőséget választva tekintheti meg. 
1. Írja `2017` be az SQL Server lemezkép keresőmezőjét a **virtuális sql gépek** csempéjén, majd válassza a Legördülő menü ingyenes SQL Server **licenc: SQL Server 2017 Developer on Windows Server 2016** lehetőséget. 


   ![SQL Virtuálisgép-lemezkép kijelölése](media/virtual-machines-windows-portal-sql-server-provision/select-sql-vm-image-portal.png)

   > [!TIP]
   > A fejlesztői kiadás ebben a forgatókönyvben használatos, mivel ez az SQL Server teljes funkcionalitású, ingyenes kiadása a fejlesztési teszteléshez. Csak a virtuális gép futtatásával járó költségeket kell kifizetni. Azonban szabadon választhatja ki az ebben a forgatókönyvben használni kívánt képeket. Az elérhető lemezképek leírását az [SQL Server Windows Virtual Machines áttekintése című témakörben találja.](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)

   > [!TIP]
   > Az SQL Server licencelési költségei beépülnek a létrehozott virtuális gép másodpercenkénti díjszabásába, és kiadásonként és magonként eltérőek. Az SQL Server Developer edition azonban ingyenes a fejlesztés/tesztelés (nem éles környezetben), és az SQL Express ingyenes a könnyű számítási feladatokhoz (kevesebb, mint 1 GB memória, kevesebb mint 10 GB tárhely). Azt is, hogy-a-saját licenc (BYOL) és csak a virtuális gép. Az ilyen rendszerképek nevei {BYOL} előtagot kapnak. 
   >
   > A lehetőségekkel kapcsolatos további információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).


1. Kattintson a **Létrehozás** gombra.


## <a name="1-configure-basic-settings"></a>1. Alapbeállítások konfigurálása


Az **Alapok** lapon adja meg a következő információkat:

* A **Projekt részletei**csoportban győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva. 
*  Az **Erőforráscsoport** csoport csoportban válasszon ki egy meglévő erőforráscsoportot a listából, vagy válassza az **Új létrehozása lehetőséget** új erőforráscsoport létrehozásához. Az erőforráscsoportok az Azure-ban egymáshoz kapcsolódó erőforrásokból (virtuális gépekből, tárfiókokból, virtuális hálózatokból stb.) álló gyűjtemények. 

    ![Előfizetés](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Az Azure szolgáltatásban futó SQL Server üzemelő példányok tesztelésekor vagy a velük való megismerkedéskor érdemes egy új erőforráscsoportot használni. A tesztelés befejezése után törölje az erőforráscsoportot a virtuális gép és az erőforráscsoporthoz társított összes erőforrás automatikus törléséhez. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../../../azure-resource-manager/management/overview.md) (Az Azure Resource Manager áttekintése).


* A **Példány részletei csoportban:**
    1. Adjon meg egy egyedi **virtuális gépnevet**.  
    1. Válassza ki a **régió**helyét. 
    1. Ennek az útmutatónak az alkalmazásában hagyja a **rendelkezésre állási beállításokat** _Nincs szükség infrastruktúra-redundanciára._ A rendelkezésre állási lehetőségekről az [Elérhetőség című témakörben talál](../../windows/availability.md)további információt. 
    1. A **Lemezkép** listában válassza az _Ingyenes SQL Server licenc: SQL Server 2017 Developer on Windows Server 2016_lehetőséget.  
    1. Válassza a virtuális gép **méretének** **módosítását,** és válassza az **A2 alapszintű** ajánlatot. Győződjön meg róla, hogy törölje az erőforrásokat, ha végzett velük, hogy megakadályozzák a váratlan díjakat. Az éles számítási feladatok esetében ajánlott gépméretekért és -konfigurációkért tekintse meg [az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növelésével kapcsolatos ajánlott eljárásokat](virtual-machines-windows-sql-performance.md).

    ![Példány részletei](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> A **Méret kiválasztása** ablakban megjelenő, becsült havi költségek nem tartalmazzák az SQL Server licencelési költségeit. Ez a becslés csak a virtuális gép költsége. Az SQL Server Express és Developer kiadása esetében ez a becslés a teljes becsült költség. Más kiadások esetében tekintse meg a [Windows rendszerű virtuális gépek árképzését ismertető oldalt](https://azure.microsoft.com/pricing/details/virtual-machines/windows/), és válassza ki az SQL Server megfelelő kiadását. Tekintse meg az SQL Server Azure virtuális gépekre és [a virtuális gépek méreteire](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) [vonatkozó díjszabási útmutatót](virtual-machines-windows-sql-server-pricing-guidance.md) is.

* A **Rendszergazdafiók csoportban**adja meg a felhasználónevet és a jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Rendszergazdai fiók](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* A **Bejövő portszabályok csoportban**válassza **a Kijelölt portok engedélyezése lehetőséget,** majd válassza az **RDP (3389)** elemet a legördülő menüből. 

   ![Bejövőport-szabályok](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Választható funkciók konfigurálása

### <a name="disks"></a>Lemezek

A **Lemezek** lapon adja meg a lemezbeállításokat. 

* Az **operációs rendszer lemeztípusa csoportban**válassza ki az operációs rendszerhez kívánt lemez típusát a legördülő menüből. Prémium ajánlott éles rendszerek, de nem érhető el az alapszintű virtuális gép. A prémium szintű SSD használatához módosítsa a virtuális gép méretét. 
* A **Speciális csoportban**válassza az **Igen** lehetőséget a **Felügyelt lemezek**használata csoportban.

   > [!NOTE]
   > A Microsoft a Managed Disks használatát javasolja az SQL Server esetében. A Managed Disks szolgáltatás a háttérben kezeli a tárterületet. Emellett ha ugyanabban a rendelkezésre állási csoportban több, a Managed Diskset használó virtuális gép található, az Azure elosztja a tárolási erőforrásokat, hogy megfelelő redundanciát biztosítson. További információt az [Azure felügyelt lemezek – áttekintés című témakörben talál.](../managed-disks-overview.md) A felügyelt lemezek a rendelkezésre állási csoportokban való használatával kapcsolatos részletekért lásd: [Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel](../manage-availability.md).

![SQL virtuális gép lemez beállításai](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Hálózat

A **Hálózat** lapon adja meg a hálózati beállításokat. 

* Hozzon létre egy új **virtuális hálózatot**, vagy használjon egy meglévő virtuális hálózatot az SQL Server virtuális géphez. Jelöljön ki egy **alhálózatot** is. 

* A **hálózati adapter hálózati biztonsági csoportjában**válasszon egy alapvető biztonsági csoportot vagy a speciális biztonsági csoportot. Az alapbeállítás kiválasztásával kiválaszthatja az SQL Server virtuális gép bejövő portjait (ugyanazokat az értékeket, amelyeket az **Alaplapon** konfiguráltak). A speciális beállítás sal egy meglévő hálózati biztonsági csoportot választhat, vagy újat hozhat létre. 

* Módosíthatja a hálózati beállításokat, vagy megtarthatja az alapértelmezett értékeket.

![SQL virtuális gép hálózati beállításai](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Figyelés

A **Figyelés** lapon konfigurálja a figyelést és az automatikus leállítást. 

* Az Azure engedélyezi **a rendszerindítási diagnosztika** alapértelmezés szerint ugyanazzal a tárfiókkal, amely a virtuális gép számára van kijelölve. Ezeket a beállításokat itt módosíthatja, valamint lehetővé teszi **az operációs rendszer vendégdiagnosztikájának engedélyezését.** 
* Ezen a lapon is engedélyezheti a **Rendszer hez rendelt felügyelt identitást** és az automatikus **leállítást.** 

![SQL virtuális gép kezelési beállításai](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Sql Server-beállítások konfigurálása

Az **SQL Server beállítások** lapján konfiguráljon bizonyos beállításokat és optimalizálásokat az SQL Server hez. Az SQL Server hez konfigurálható beállítások a következők:

| Beállítás |
| --- |
| [Kapcsolat](#connectivity) |
| [Hitelesítés](#authentication) |
| [Azure Key Vault integrációja](#azure-key-vault-integration) |
| [Tároló konfigurálása](#storage-configuration) |
| [Automatikus javítás](#automated-patching) |
| [Automatikus biztonsági mentés](#automated-backup) |
| [Machine Learning Services](#machine-learning-services) |


### <a name="connectivity"></a>Kapcsolatok

Az **SQL kapcsolatok** alatt adja meg, milyen típusú hozzáférést szeretne az ezen a virtuális gépen futó SQL Server-példányhoz. A forgatókönyv alkalmazásában válassza a **Nyilvános (internet)** lehetőséget az SQL Server-kiszolgálóhoz való csatlakozás engedélyezéséhez az interneten lévő gépekről vagy szolgáltatásokról. Ha ez a beállítás be van jelölve, az Azure automatikusan konfigurálja a tűzfalat és a hálózati biztonsági csoportot, hogy engedélyezze a forgalmat a kiválasztott porton.

> [!TIP]
> Alapértelmezés szerint az SQL Server a jól ismert **1433-as** portot figyeli. A nagyobb biztonság érdekében az előző párbeszédpanelen módosítsa a portot egy nem alapértelmezett, például az 1401-es portra. Ha módosítja a portot, akkor a portsegítségével kell csatlakoznia bármely ügyféleszközről, például az SSMS-ből.

![SQL virtuális gép biztonsága](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Ahhoz, hogy az SQL Serverhez az interneten keresztül csatlakozni lehessen, engedélyeznie kell az SQL Server-hitelesítést is, aminek ismertetését a következő szakaszban olvashatja.

Ha nem szeretné engedélyezni az adatbázis-alrendszerhez az interneten keresztül való csatlakozást, válassza az alábbi lehetőségek valamelyikét:

* A **Helyi (csak virtuális gépen belül)** beállítással az SQL Serverhez csak a virtuális gépen belülről lehet csatlakozni.
* A **Magánjellegű (virtuális hálózaton belül)** beállítással az SQL Serverhez az azonos virtuális hálózaton található gépek és szolgáltatások csatlakozhatnak.

Általánosságban elmondható, hogy a forgatókönyv által lehetővé tett legszigorúbb kapcsolódási korlátozás kiválasztásával növelhető a biztonság. Hálózati biztonsági csoportszabályok és SQL-/Windows-hitelesítés használatával azonban mindegyik lehetőség biztosítható. A hálózati biztonsági csoportot a virtuális gép létrehozása után szerkesztheti. További információkért lásd [az SQL Server Azure-beli virtuális gépeken történő futtatásának biztonsági szempontjait](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Hitelesítés

Ha SQL Server-hitelesítésre van szüksége, kattintson az **SQL Server beállítások** lapján az **Engedélyezés** gombra az **SQL** Server beállításai lapon.

![SQL Server-hitelesítés](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Ha az SQL Server t az interneten keresztül szeretné elérni (a Nyilvános kapcsolat beállítás), itt engedélyeznie kell az SQL-hitelesítést. Az SQL Serverhez való nyilvános hozzáféréshez SQL-hitelesítésre van szükség.

Ha engedélyezi az SQL Server-hitelesítést, adjon meg egy **bejelentkezési nevet** és egy **jelszót**. Ez a bejelentkezési név SQL Server hitelesítési bejelentkezésként van konfigurálva, és a **sysadmin** rögzített kiszolgálói szerepkör tagja. További információkat a hitelesítési módokról [a hitelesítési mód kiválasztását leíró](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) cikkben talál.

Ha nem engedélyezi az SQL Server-hitelesítést, akkor a helyi virtuális gépen lévő rendszergazdai fiók használatával is csatlakozhat az SQL Server-példányhoz.


### <a name="azure-key-vault-integration"></a>Azure Key Vault-integráció

Ha biztonsági titkokat szeretne tárolni az Azure-ban titkosításhoz, válassza az **SQL Server beállításait,** és görgessen le az **Azure key vault integrációjához.** Válassza **az Engedélyezés** és a kért adatok kitöltése lehetőséget. 

![Azure Key Vault-integráció](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

A következő táblázat tartalmazza az Azure Key Vault-integráció konfigurálásához szükséges paramétereket.

| PARAMÉTER | LEÍRÁS | PÉLDA |
| --- | --- | --- |
| **Key Vault URL** |A Key Vault helye. |https:\//contosokeyvault.vault.azure.net/ |
| **Egyszerű név** |Az Azure Active Directory szolgáltatás egyszerű neve. Ezt a nevet ügyfél-azonosítónak is hívják. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Egyszerű titok** |Az Azure Active Directory szolgáltatás egyszerű titka. Ezt a titkot ügyféltitoknak is hívják. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Hitelesítő adat neve** |**Hitelesítő adat neve**: Az AKV-integráció létrehoz egy hitelesítő adatot az SQL Serverben, amely hozzáférést biztosít a virtuális gépnek a Key Vaulthoz. Válasszon egy nevet ennek a hitelesítő adatnak. |mycred1 |

További információkért lásd: [Configure Azure Key Vault Integration for SQL Server on Azure VMs](virtual-machines-windows-ps-sql-keyvault.md) Az Azure Key Vault-integráció konfigurálása az SQL Serverhez Azure virtuális gépeken.

### <a name="storage-configuration"></a>Tároló konfigurálása

Az **SQL Server beállításai** lap **Tároló konfiguráció jalapján**válassza a Konfiguráció **módosítása lehetőséget** a Teljesítményoptimalizált tároló konfigurációja lap megnyitásához és a tárolási követelmények megadásához.

![SQL Virtuálisgép-tároló konfigurációja](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

A **Storage optimized for** (Tároló optimalizálási módja) területen válasszon az alábbi lehetőségek közül:

* A **General** (Általános) az alapértelmezett beállítás, amely a legtöbb számítási feladatot támogatja.
* **A tranzakciós feldolgozás** optimalizálja a hagyományos adatbázis OLTP-munkaterhelések tárolását.
* A **Data warehousing** (Adatraktározás) beállítás elemzési és jelentéskészítési számítási feladatokra optimalizálja a tárolót.

![SQL Virtuálisgép-tároló konfigurációja](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

Dönthet úgy, hogy az értékeket alapértelmezetten hagyja, vagy manuálisan módosíthatja a tárolási topológiát az IOPS-igényeknek megfelelően. További információt a [Tároló konfigurációja című](virtual-machines-windows-sql-server-storage-configuration.md)témakörben talál. 

### <a name="sql-server-license"></a>SQL Server licenc
Ha Ön frissítési garancia ügyfél, használhatja az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) segítségével saját SQL Server-licencét, és erőforrásokat takaríthat meg. 

![SQL virtuálisgép-licenc](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatikus javítás

Az **Automatikus javítás** alapértelmezés szerint engedélyezve van. Az automatizált javítás lehetővé teszi, hogy az Azure automatikus javításokat alkalmazzon az SQL Serveren és az operációs rendszeren. A karbantartási időszak beállításához adja meg a hét egy napját, egy időpontot és egy időtartamot. Az Azure ebben a karbantartási időszakban végzi el a javításokat. A karbantartási időszak ütemezése a virtuális gép területi beállítása szerinti időt használja. Ha nem szeretné, hogy az Azure automatikusan alkalmazza a javításokat az SQL Serveren és az operációs rendszeren, kattintson a **Letiltás** elemre.  

![SQL virtuális gép automatikus javítása](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

További információk: [Automated Patching for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md) (Az SQL Server automatikus javítása Azure virtuális gépeken).

### <a name="automated-backup"></a>Automatikus biztonsági mentés

Az **Automatikus biztonsági mentés** területen engedélyezheti az összes adatbázis automatikus mentését. Az automatikus biztonsági mentés alapértelmezés szerint le van tiltva.

Az SQL automatikus biztonsági mentésének engedélyezésekor konfigurálhatja a következő beállításokat:

* Biztonsági mentések megőrzési ideje (napokban)
* A biztonsági mentésekhez használt tárfiók
* Titkosítási beállítás és a biztonsági mentések jelszavas védelme
* Rendszeradatbázisok biztonsági mentése
* Biztonsági mentések ütemezésének konfigurálása

A biztonsági mentés titkosításához kattintson az **Engedélyezés** elemre. Ezután adja meg a **Jelszót**. Az Azure a biztonsági mentések titkosításához létrehoz egy tanúsítványt, amelyet a megadott jelszóval véd. Alapértelmezés szerint az ütemezés automatikusan be van állítva, de a **Kézi**lehetőség kiválasztásával manuális ütemezést hozhat létre. 

![SQL virtuális gép automatikus biztonsági mentései](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

További információk: [Automated Backup for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md) (Az SQL Server automatikus biztonsági mentése Azure virtuális gépeken).


### <a name="machine-learning-services"></a>Machine Learning Services

Lehetősége van a [Machine Learning Services engedélyezésére.](/sql/advanced-analytics/) Ez a beállítás lehetővé teszi a gépi tanulás használatát a Python és az R az SQL Server 2017.This option enables you to use machine learning with Python and R in SQL Server 2017. Az SQL **Server beállítások** ablakában válassza az **Engedélyezés** lehetőséget.


## <a name="4-review--create"></a>4. Felülvizsgálat + létrehozása

A **Véleményezés + létrehozás** lapon tekintse át az összegzést, és válassza a **Létrehozás** lehetőséget az SQL Server, az erőforráscsoport és a virtuális géphez megadott erőforrások létrehozásához.

Az üzemelő példány az Azure Portalról monitorozható. A képernyő felső részén látható **Értesítések** gomb megjeleníti az üzemelő példány állapotának alapvető információit.

> [!NOTE]
> Az üzembe helyezési idők szemléltetéséhez üzembe helyeztem egy SQL virtuális gépet az USA keleti régiójában az alapértelmezett beállításokkal. Ez az üzembe helyezési teszt hozzávetőlegesen 12 percet vett igénybe. A régiótól és a kiválasztott beállításoktól függően Ön ennél rövidebb vagy hosszabb üzembe helyezési időt is tapasztalhat.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a>A virtuális gép megnyitása távoli asztallal

A következő lépésekkel csatlakozzon az SQL Server virtuális géphez a távoli asztalról:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Az SQL Server virtuális géphez való csatlakozás után elindíthatja az SQL Server Management Studiót, és a helyi rendszergazdai hitelesítő adataival csatlakozhat Windows-hitelesítés használatával. Ha engedélyezte az SQL Server-hitelesítést, akkor az SQL-hitelesítésen keresztül is csatlakozhat a kiépítés során megadott SQL bejelentkezési azonosítójával és jelszavával.

A géphez való hozzáférés lehetővé teszi, hogy igény szerint közvetlenül módosítsa a gép és az SQL Server beállításait. Például konfigurálhatja a tűzfal beállításait, vagy módosíthatja az SQL Server-konfiguráció beállításait.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a>Csatlakozás az SQL Server kiszolgálóhoz távolról

Ebben a forgatókönyvben a **Nyilvános** hozzáférés lehetőséget választotta a virtuális géphez és az **SQL Server hitelesítéshez**lehetőséget. Ezek a beállítások automatikusan úgy konfigurálták a virtuális gépet, hogy az az internetről bármely ügyfél számára engedélyezi az SQL Serverhez való csatlakozást (feltéve, hogy helyes SQL-bejelentkezési névvel rendelkeznek).

> [!NOTE]
> Ha az üzembe helyezés során nem a nyilvános hozzáférést választotta, akkor az üzembe helyezés után a portálon keresztül módosíthatja az SQL csatlakozási beállításait. További információ: [Az SQL csatlakozási beállításainak módosítása](virtual-machines-windows-sql-connect.md#change).

A következő szakaszok bemutatják, hogyan csatlakozhat az interneten keresztül az SQL Server virtuálisgép-példányhoz.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Ez a példa az 1433-as közös portot használja. Ezt az értéket azonban módosítani kell, ha az SQL Server virtuális gép telepítése során egy másik portot (például 1401) adott meg. 


## <a name="next-steps"></a>További lépések

További információ az SQL Server használatáról az Azure-ban: [SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) (SQL Server az Azure virtuális gépeken) és [Frequently Asked Questions](virtual-machines-windows-sql-server-iaas-faq.md) (Gyakran ismételt kérdések).
