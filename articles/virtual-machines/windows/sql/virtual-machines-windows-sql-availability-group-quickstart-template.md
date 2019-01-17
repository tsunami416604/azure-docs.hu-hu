---
title: WSFC, hozzon létre figyelőt, és konfigurálja az ILB Always On rendelkezésre állási csoporthoz az SQL Server virtuális gép az Azure gyorsindítási sablon
description: Használja az Azure gyorsindítási sablonok segítségével egyszerűsítheti azok rendelkezésre állási csoportok létrehozása az SQL Server virtuális gépek az Azure-ban a fürt létrehozása sablon használatával SQL virtuális gépek a fürthöz, hozza létre a figyelőt, és konfigurálja az ILB.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 9be8717bc9b1d15a59486edf206dd0657a711c06
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360279"
---
# <a name="create-wsfc-listener-and-configure-ilb-for-an-always-on-availability-group-on-a-sql-server-vm-with-azure-quickstart-template"></a>WSFC, hozzon létre figyelőt, és konfigurálja az ILB Always On rendelkezésre állási csoporthoz az SQL Server virtuális gép az Azure gyorsindítási sablon
Ez a cikk ismerteti, hogyan használhatja az Azure gyorsindítási sablonok részlegesen automatizálni az üzembe helyezést egy Always On rendelkezésre állási csoport konfigurációjának az SQL Server Virtual Machines az Azure-ban. Nincsenek az a folyamat által használt két Azure gyorsindítási sablonok. 

   | Sablon | Leírás |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | A Windows feladatátvevő fürtöt hoz létre, és azt csatlakoztatja az SQL Server virtuális gépek. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | A rendelkezésre állási csoport figyelőjének hoz létre, és a belső terheléselosztó konfigurálja. |
   | &nbsp; | &nbsp; |

A rendelkezésre állási csoport konfigurációjának más részein kell elvégezni, manuálisan, például a rendelkezésre állási csoport létrehozása és a belső terheléselosztó létrehozása. Ez a cikk az automatikus és manuális lépések sorrendjét.
 

## <a name="prerequisites"></a>Előfeltételek 
A telepítő egy Always On rendelkezésre állási csoport gyorsindítási sablonok használatával automatizálhatja, már rendelkeznie kell a következő előfeltételek vonatkoznak: 
- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy erőforráscsoportot egy [tartományvezérlő](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-forest). 
- Legalább egy tartományhoz csatlakoztatott [virtuális gépek Azure futó SQL Server 2016 (vagy nagyobb) Enterprise verzióban](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) azonos rendelkezésre állási csoport vagy a rendelkezésre állási zónában, amely már a [regisztrálni az SQL virtuális gép erőforrás-szolgáltató](#register-existing-sql-vm-with-new-resource-provider).  

## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Az új erőforrás-szolgáltató meglévő SQL virtuális gép regisztrálása
A rendelkezésre állási csoport Azure gyorsindítási sablonok támaszkodnak az SQL virtuális gép erőforrás-szolgáltató (Microsoft.SqlVirtualMachine), mert meglévő SQL Server virtuális gépek regisztrálva kell lennie az SQL virtuális gép erőforrás-szolgáltató. Hagyja ki ezt a lépést, ha az SQL Server virtuális gép után December 2018, az összes SQL Server virtuális gépek létrehozása, ez a dátum a rendszer automatikusan regisztrálása után létrehozott. Ez a szakasz ismerteti, regisztrálja a szolgáltatót az Azure portal használatával, de is [PowerShell](virtual-machines-windows-sql-ahb.md#powershell). 

  >[!IMPORTANT]
  > Törölte-e az SQL Server VM-erőforrás, állítja vissza a kép megszegi licenc beállítása módosítva lett. 

1. Nyissa meg az Azure Portalon, és navigáljon a **minden szolgáltatás**. 
1. Navigáljon a **előfizetések** , és válassza ki az előfizetést a lényeges.  
1. Az a **előfizetések** panelen lépjen **erőforrás-szolgáltató**. 
1. Típus `sql` viszi, megjelenik az SQL-kapcsolódó erőforrás-szolgáltatók a szűrőben. 
1. Válassza *regisztrálása*, *újraregisztrálni*, vagy *Unregister* számára a **Microsoft.SqlVirtualMachine** szolgáltató attól függően, a kívánt művelet. 

  ![A szolgáltató módosítása](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

## <a name="step-1---create-the-wsfc-and-join-sql-server-vms-to-the-cluster-using-quickstart-template"></a>1. lépés – a WSFC létrehozása és az SQL Server rendszerű virtuális gépekhez csatlakozhat a fürthöz gyorsindítási sablon használatával 
Miután az SQL virtuális gép új erőforrás-szolgáltató az SQL Server virtuális gépek vannak regisztrálva, is csatlakozhat az SQL Server virtuális gépek az *SqlVirtualMachineGroup*. Ehhez az erőforráshoz határozza meg a Windows feladatátvevő fürtöt, a verzió, kiadás, teljes tartománynév, a fürt kezelésére AD-fiókok és a Felhőbeli tanúsító a Tárfiók metaadatait. Az SQL Server virtuális gép hozzáadása a *SqlVirtualMachineGroup* csatlakoztatja a Windows feladatátvevő fürtszolgáltatást és az SQL Server virtuális gépek csatlakozik a fürthöz. Ebben a lépésben az automatizált a **101-sql-vm-ag-telepítés** gyorssablon és kialakítható az alábbi lépéseket követve:

1. Keresse meg a [ **101-sql-vm-ag-telepítés** ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) gyorsindítási sablon válassza **üzembe helyezés az Azure** elindíthatja a gyorsindítási sablon az Azure Portalon.
1. Töltse ki a kötelező mezők konfigurálása a Windows feladatátvevő fürt metaadatait. A választható mezőket kitölteni.

    Az alábbi táblázat az értékeket a sablonhoz szükséges: 

   | **Mező** | Érték |
   | --- | --- |
   | **Előfizetés** |  Az előfizetés, ha az SQL Server virtuális gépek léteznek. |
   |**Erőforráscsoport** | Az erőforráscsoport, ahol az SQL Server virtuális gépek találhatók. | 
   |**A feladatátvevő fürt neve** | A kívánt nevet az új Windows feladatátvevő fürthöz. |
   | **Meglévő virtuálisgép-lista** | Az SQL Server virtuális gépek kíván részt venni a rendelkezésre állási csoportban, és mint ilyen, az új fürt része lehet. Ezek az értékek elválasztás egy vesszőt és egy szóközt (például: SQLVM1, SQLVM2). |
   | **SQL Server Version** | A legördülő listából válassza ki az SQL Server verzióját az SQL Server virtuális gépek. Jelenleg csak az SQL 2016 és az SQL 2017 lemezképek támogatottak. |
   | **Meglévő teljesen minősített tartománynevét** | A tartományhoz, amelyben az SQL Server virtuális gépen található meglévő teljes Tartománynevét. |
   | **Meglévő tartományi fiók** | Meglévő tartományi fiók, amely az SQL Server SysAdmin (rendszergazda) hozzáféréssel rendelkezik. | 
   | **Tartományi fiók jelszava** | A korábban említett tartományi fiók jelszava. | 
   | **Existing Sql Service Account** | A tartományi felhasználói fiók az SQL Server szolgáltatás vezérléséhez használt. Ez az információ található használatával a [ **SQL Server Configuration Manager**](https://docs.microsoft.com/sql/relational-databases/sql-server-configuration-manager?view=sql-server-2017). |
   | **Sql Service Password** | A tartományi felhasználói fiók, amely szabályozza, az SQL Server szolgáltatás által használt jelszó. |
   | &nbsp; | &nbsp; |

1. Ha elfogadja a feltételeket és kikötéseket, jelölje be a jelölőnégyzetet a **elfogadom a feltételeket és a fenti feltételeket** válassza **beszerzési** a rövid útmutató sablon telepítésének véglegesítéséhez. 
1. Az üzemelő példány monitorozásához válassza a központi telepítést a **értesítések** harang ikonra a felső navigációs fejléc, vagy navigáljon arra a **erőforráscsoport** az Azure Portalon válassza ki a  **Központi telepítések** a a **beállítások** mezőben, majd válassza a "Microsoft.Template" központi telepítés. 

## <a name="step-2---manually-create-the-availability-group"></a>2. lépés – a rendelkezésre állási csoport kézi létrehozása 
A rendelkezésre állási csoport manuálisan létrehoznia, ahogy azt szokásosan tenné, segítségével [PowerShell](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell?view=sql-server-2017), [SQL Server Management Studio](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio?view=sql-server-2017) vagy [Transact-SQL](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql?view=sql-server-2017). 

  >[!IMPORTANT]
  > Tegye **nem** jelenleg figyelő létrehozása, mert által automatikusan végbemegy a **101-sql-vm-aglistener-telepítés** gyorsindítási sablon 4. lépés. 

## <a name="step-3---manually-create-the-internal-load-balancer-ilb"></a>3. lépés – a belső Load Balancer (ILB) kézi létrehozása
Az Always On rendelkezésre állási csoport (rendelkezésre állási csoport) figyelőjét egy belső Azure Load Balancer (ILB) van szükség. Az ILB a rendelkezésre állási csoport figyelőjének, amely lehetővé teszi a gyorsabb feladatátvételt és újracsatlakozás egy "nem fix" IP-címet biztosít. Ha az SQL Server virtuális gépek rendelkezésre állási csoportban az azonos rendelkezésre állási csoport részét képezik, ezt követően használhatja az alapszintű Load Balancer; Ellenkező esetben azt kell használnia a Standard Load Balancer.  **Az ILB kell lennie az SQL Server Virtuálisgép-példányok megegyező virtuális hálózatba.** Az ILB csak kell létrehozni, a további konfigurálást (például a háttérkészlet, állapotadat-mintavétel és terheléselosztó szabályai) kezeli a **101-sql-vm-aglistener-telepítés** gyorsindítási sablon 4. lépés. 

1. Az Azure Portalon nyissa meg az erőforráscsoport, amely tartalmazza az SQL Server-virtuálisgépek. 
2. Kattintson az erőforráscsoport **Hozzáadás**.
3. Keresse meg **terheléselosztó** , és ezt követően a keresési eredmények között, válassza **Load Balancer**, amely által közzétett **Microsoft**.
4. Az a **terheléselosztó** panelen kattintson a **létrehozás**.
5. Az a **terheléselosztó létrehozása** párbeszédpanelen adja meg a terheléselosztó a következő:

   | Beállítás | Érték |
   | --- | --- |
   | **Name (Név)** |A load balancer jelölő szöveges nevét. Ha például **sqlLB**. |
   | **Típus** |**Belső**: A legtöbb megvalósításokban belső terheléselosztó, amely lehetővé teszi az alkalmazások az adott virtuális hálózaton belül kapcsolódni a rendelkezésre állási csoporthoz.  </br> **Külső**: Lehetővé teszi az alkalmazások szeretne csatlakozni a nyilvános internetkapcsolaton keresztül a rendelkezésre állási csoportot. |
   | **Virtuális hálózat** |Válassza ki a virtuális hálózathoz, amelyek az SQL Server-példányokat. |
   | **Alhálózat** |Válassza ki az alhálózatot, amelyet az SQL Server-példányok szerepelnek. |
   | **IP-cím hozzárendelése** |**Static** |
   | **Magánhálózati IP-cím** |Adjon meg egy elérhető IP-címet az alhálózatról. Az IP-címet használja, a figyelő a fürt létrehozásakor.|
   | **Előfizetés** |Ha több előfizetéssel rendelkezik, ez a mező jelenhet meg. Válassza ki az ehhez az erőforráshoz társítani kívánt előfizetést. Fontos általában ugyanazt az előfizetést a rendelkezésre állási csoport összes erőforrását. |
   | **Erőforráscsoport** |Válassza ki az erőforráscsoportot, amely az SQL Server-példányok szerepelnek. |
   | **Hely** |Válassza ki az Azure-beli hely, amely az SQL Server-példányok szerepelnek. |
   | &nbsp; | &nbsp; |

6. Kattintson a **Létrehozás** gombra. 


  >[!NOTE]
  > Minden egyes SQL Server rendszerű virtuális gép nyilvános IP-erőforrásból rendelkeznie kell a standard Termékváltozat a Standard Load Balancer való kompatibilitás. A virtuális gép nyilvános IP-erőforrás-Termékváltozat határozza meg, lépjen a **erőforráscsoport**, jelölje be a **nyilvános IP-cím** erőforrás számára a kívánt SQL Server virtuális Gépet, és keresse meg az értéket **Termékváltozat**  , a **áttekintése** ablaktáblán. 

## <a name="step-4---create-the-ag-listener-and-configure-the-ilb-with-the-quickstart-template"></a>4. lépés – a rendelkezésre állási csoport figyelőjének létrehozásához, és konfigurálja az ILB-gyorssablon

A rendelkezésre állási csoport figyelőjének létrehozása és konfigurálása automatikusan a belső Load Balancer (ILB) a **101-sql-vm-aglistener-telepítés** Microsoft.SqlVirtualMachine/Sql virtuális látja el, mert a gyorsindítási sablon Csoportok és rendelkezésre állási csoport figyelőjének gép típusú erőforrást. A **101-sql-vm-aglistener-telepítés** rövid sablon, keresztül az SQL virtuális gép erőforrás-szolgáltató által a következő műveleteket:

 - A fürt és az ILB-hálózat beállításait konfigurálja. 
 - Konfigurálja az ILB-háttérkészlet állapotadat-mintavétel és terheléselosztó szabályai.
 - A rendelkezésre állási csoport figyelőjének hoz létre a megadott IP-címét és nevét.

Konfigurálja az ILB, és a rendelkezésre állási csoport figyelőjének létrehozásához tegye a következőket:
1. Keresse meg a [ **101-sql-vm-aglistener-telepítés** ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) gyorsindítási sablon válassza **üzembe helyezés az Azure** elindíthatja a gyorsindítási sablon az Azure Portalon.
1. Töltse ki a kötelező mezőket az ILB konfigurálásához, és a rendelkezésre állási csoport figyelőjének létrehozásához. A választható mezőket kitölteni. 

    Az alábbi táblázat az értékeket a sablonhoz szükséges: 

   | **Mező** | Érték |
   | --- | --- |
   |**Erőforráscsoport** | Az erőforráscsoport, amelyben az SQL Server virtuális gépek és a rendelkezésre állási csoport létezik. | 
   |**Meglévő feladatátvevő fürt neve** | Az SQL Server virtuális gépek tartományhoz csatlakoztatott a fürt nevére. |
   | **Meglévő Sql rendelkezésre állási csoport**| A rendelkezésre állási csoport, az SQL Server virtuális gépek egy részét képező neve. |
   | **Meglévő virtuálisgép-lista** | A korábban említett rendelkezésre állási csoport részét képező SQL Server virtuális gépek nevei. A neveket vesszővel és a egy szóközzel kell elválasztani (például: SQLVM1, SQLVM2). |
   | **Meglévő teljesen minősített tartománynevét** | A tartományhoz, amelyben az SQL Server virtuális gépen található meglévő teljes Tartománynevét. |
   | **Listener** | A DNS-név, amelyet szeretne hozzárendelni a figyelőt. Alapértelmezés szerint ez a sablon meghatározza a aglistener neve, de ez módosítható. |
   | **Figyelő portja** | Azt szeretné, hogy a figyelő használandó port. Általában ez a port az alapértelmezett 1433-as portot kell lennie, és mint ilyen, ez a sablon által megadott portszám. Azonban ha az alapértelmezett port megváltozott, majd a figyelőjének portszámára kell használni ezt az értéket. | 
   | **Meglévő virtuális hálózat** | Az SQL Server virtuális gépen, és az ILB-ket virtuális hálózat neve. |
   | **Existing Subnet** | A *neve* az SQL Serveres virtuális gépek a belső alhálózat (pl.: alapértelmezett). Ez az érték lehet meghatározni az a **erőforráscsoport**lehetőségre kattint a **vNet**lehetőségre kattint **alhálózatok** alatt a **beállítások**panelre, és másolja az értéket **neve**. |
   | **Meglévő belső Load Balancer** | A 3. lépésben létrehozott ILB neve. |
   | **Probe Port** | A mintavételi portot az ILB használni kívánt. A sablon által 59999 alapértelmezés szerint, de ez az érték módosítható. |
   | &nbsp; | &nbsp; |

1. Ha elfogadja a feltételeket és kikötéseket, jelölje be a jelölőnégyzetet a **elfogadom a feltételeket és a fenti feltételeket** válassza **beszerzési** a rövid útmutató sablon telepítésének véglegesítéséhez. 
1. Az üzemelő példány monitorozásához válassza a központi telepítést a **értesítések** harang ikonra a felső navigációs fejléc, vagy navigáljon arra a **erőforráscsoport** az Azure Portalon válassza ki a  **Központi telepítések** a a **beállítások** mezőben, majd válassza a "Microsoft.Template" központi telepítés. 

  >[!NOTE]
  >Ha az üzembe helyezés sikertelen felénél keresztül, kell manuálisan [távolítsa el az újonnan létrehozott figyelőt](#remove-availability-group-listener) ismételt üzembe helyezése előtt a PowerShell használatával a **101-sql-vm-aglistener-telepítés** gyorsindítási sablon. 

## <a name="remove-availability-group-listener"></a>Távolítsa el a rendelkezésre állási csoport figyelője
Ha később el szeretné eltávolítani a rendelkezésre állási csoport figyelőjének a sablon által konfigurált, haladjon végig az SQL virtuális gép erőforrás-szolgáltató. A figyelő keresztül az SQL virtuális gép erőforrás-szolgáltató regisztrálva van, mivel annak csak SQL Server Management Studio segítségével törlése nem elegendő. Valóban törölni kell a PowerShell-lel SQL virtuális gép erőforrás-szolgáltatón keresztül. Ezzel a rendelkezésre állási csoport figyelőjének metaadatok távolít el az SQL virtuális gép erőforrás-szolgáltató, és fizikailag törli a figyelőt a rendelkezésre állási csoportból. 

A következő kódrészlet törli az SQL rendelkezésre állási csoport figyelőjének, mind az SQL erőforrás-szolgáltató és a rendelkezésre állási csoportból: 

```PowerShell
# Remove the AG listener
# example: Remove-AzureRmResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzureRmResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="known-issues-and-errors"></a>Ismert problémák és hibák
Ez a szakasz bemutatja néhány ismert problémákat és azok lehetséges megoldás. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Rendelkezésre állási csoport figyelőjének rendelkezésre állási csoport "\<AG-Name >" már létezik
A kiválasztott rendelkezésre állási csoport már szerepel a rendelkezésre állási csoport figyelőjének Azure gyorsindítási sablon egy figyelőt, vagy fizikailag a rendelkezésre állási csoporton belül, vagy mint belül az SQL virtuális gép erőforrás-szolgáltató metaadatokat tartalmazza.  Távolítsa el a figyelővel [PowerShell](#remove-availability-group-listener) ismételt üzembe helyezése előtt a **101-sql-vm-aglistener-telepítés** gyorsindítási sablon. 

### <a name="connection-only-works-from-primary-replica"></a>Kapcsolat csak akkor működik, az elsődleges replika
Ez a viselkedés valószínű oka egy sikertelen **101-sql-vm-aglistener-telepítés** sablonalapú telepítés és az ILB-konfiguráció inkonzisztens állapotban. Győződjön meg arról, hogy a háttérkészlet felsorolja a rendelkezésre állási csoport, és hogy szabályok létezik-e az állapotminta és a terheléselosztási szabályokat. Ha hiányzik valami, majd az ILB konfigurációja, inkonzisztens állapotba. 

A probléma elhárításához távolítsa el a figyelővel [PowerShell](#remove-availability-group-listener), törölje a belső terheléselosztó az Azure Portalon, és újrakezdheti a [3. lépés](#step-3---manually-create-the-internal-load-balanced-ilb). 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest – csak az SQL virtuális gép lista frissíthető.
Ez a hiba akkor fordulhat elő, üzembe helyezésekor a **101-sql-vm-aglistener-telepítés** sablon, ha a figyelő keresztül az SQL Server Management Studio (SSMS) törölve lett, de az SQL virtuális gép erőforrás-szolgáltató nem lett törölve. A figyelőt a SSMS törlése nem távolítja el a metaadatokat a figyelő az SQL virtuális gép erőforrás-szolgáltató; a figyelő törölni kell az erőforrás-szolgáltató használatával [PowerShell](#remove-availability-group-listener). 


## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Az SQL Server virtuális gépek – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md)
* [Az SQL Server virtuális gép – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Az SQL Server rendszerű virtuális gép kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Váltás a licencmodellek szükségesek az SQL Server virtuális gép](virtual-machines-windows-sql-ahb.md)



