---
title: Rendelkezésre állási csoport konfigurálása (Azure gyorsindítási sablon)
description: Azure-alapú gyorsindítási sablonokkal hozza létre a Windows feladatátvevő fürtöt, csatlakozzon az SQL Server virtuális gépekhez a fürthöz, hozza létre a figyelőt, és konfigurálja a belső terheléselosztót az Azure-ban.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022387"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Azure-gyorsindítási sablonok használata az SQL Server rendelkezésre állási csoportjának konfigurálásához egy Azure virtuális gépen
Ez a cikk bemutatja, hogyan használhatja az Azure gyorsindítási sablonjait az Azure-beli SQL Server virtuális gépek mindig rendelkezésre állási csoportkonfigurációjának üzembe helyezésének részleges automatizálásához. Ebben a folyamatban két Azure-gyorsindítási sablon használatos: 

   | Sablon | Leírás |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Létrehozza a Windows feladatátvevő fürtöt, és csatlakozik hozzá az SQL Server virtuális gépekhez. |
   | [101-sql-vm-aglistener-beállítás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Létrehozza a rendelkezésre állási csoport figyelője, és konfigurálja a belső terheléselosztó. Ez a sablon csak akkor használható, ha a Windows feladatátvevő fürt a **101-sql-vm-ag-setup** sablonnal jött létre. |
   | &nbsp; | &nbsp; |

A rendelkezésre állási csoport konfigurációjának más részeit manuálisan kell elvégezni, például a rendelkezésre állási csoport létrehozását és a belső terheléselosztó létrehozását. Ez a cikk az automatikus és manuális lépések sorrendjét tartalmazza.
 

## <a name="prerequisites"></a>Előfeltételek 
Az Always On rendelkezésre állási csoport gyorsútmutató-sablonok használatával történő beállításának automatizálásához a következő előfeltételekkel kell rendelkeznie: 
- [Egy Azure-előfizetés](https://azure.microsoft.com/free/).
- Erőforráscsoport tartományvezérlővel. 
- Egy vagy több tartományhoz csatlakozó virtuális gép az [Azure-ban, amely en SQL Server 2016 (vagy újabb) Enterprise Edition,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) amelyek ugyanabban a rendelkezésre állási készlet vagy rendelkezésre állási zónában vannak, és amelyek regisztrálva vannak [az SQL virtuálisgép-erőforrás-szolgáltatónál.](virtual-machines-windows-sql-register-with-resource-provider.md)  
- Két elérhető (egyetlen entitás által nem használt) IP-címek: egy a belső terheléselosztó, és egy a rendelkezésre állási csoport figyelő ugyanazon az alhálózaton belül, mint a rendelkezésre állási csoport. Meglévő terheléselosztó használatba való történő használata esetén csak egy elérhető IP-címre van szükség.  

## <a name="permissions"></a>Engedélyek
A következő engedélyek szükségesek a Mindig rendelkezésre állási csoport konfigurálásához az Azure gyorsindítási sablonjainak használatával: 

- Olyan meglévő tartományi felhasználói fiók, amely **számítógépobjektum-létrehozási** engedéllyel rendelkezik a tartományban.  Például egy tartományi rendszergazdai fiók általában megfelelő account@domain.comengedéllyel rendelkezik (például: ). _Ez a fiók is része kell lennie a helyi rendszergazdai csoport minden virtuális gép a fürt létrehozásához._
- Az SQL Server szolgáltatást vezérlő tartományi felhasználói fiók. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>1. lépés: Hozza létre a feladatátvevő fürtöt, és csatlakozzon az SQL Server virtuális gépekhez a fürthöz egy gyorsútmutató sablon használatával 
Miután az SQL Server virtuális gépek regisztrálva lettek az SQL VM erőforrás-szolgáltatónál, csatlakozhat az SQL Server virtuális gépekhez az *SqlVirtualMachineGroups*szolgáltatáshoz. Ez az erőforrás határozza meg a Windows feladatátvevő fürt metaadatait. Metaadatok közé tartozik a verzió, kiadás, teljesen minősített tartománynév, Active Directory-fiókok kezelésére mind a fürt és az SQL Server szolgáltatás, és a tárfiók, mint a felhő tanúsító. 

Sql Server virtuális gépek hozzáadása az *SqlVirtualMachineGroups* erőforráscsoporthoz a Windows feladatátvevő fürtszolgáltatását a fürt létrehozásához, majd az SQL Server virtuális gépekhez való csatlakozásához. Ez a lépés automatikus an **101-sql-vm-ag-setup** gyorsindítási sablon. A következő lépésekkel valósíthatja meg:

1. Nyissa meg a [**101-sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) gyorsindítási sablont. Ezután válassza **az Üzembe helyezés az Azure-ban** lehetőséget a rövid útmutató sablon megnyitásához az Azure Portalon.
1. Töltse ki a Windows feladatátvevő fürt metaadatainak konfigurálásához szükséges mezőket. A választható mezőket üresen hagyhatja.

   Az alábbi táblázat a sablon szükséges értékeit mutatja be: 

   | **Mező** | Érték |
   | --- | --- |
   | **Előfizetés** |  Az az előfizetés, amelyben az SQL Server virtuális gépei léteznek. |
   |**Erőforráscsoport** | Az az erőforráscsoport, amelyben az SQL Server virtuális gépei tartózkodnak. | 
   |**Feladatátvevő fürt neve** | Az új Windows feladatátvevő fürt kívánt neve. |
   | **Meglévő virtuálisgép-lista** | Az SQL Server virtuális gépek, amelyek szeretné, hogy részt vegyenek a rendelkezésre állási csoportban, és része az új fürt. Ezeket az értékeket vesszővel és szóközvel (például *SQLVM1, SQLVM2)* válassza el egymástól. |
   | **SQL Server verzió** | Az SQL Server virtuális gépek SQL Server-verziója. Válassza ki a legördülő listából. Jelenleg csak az SQL Server 2016 és az SQL Server 2017 rendszerképek támogatottak. |
   | **Meglévő teljesen minősített tartománynév** | Annak a tartománynak a meglévő teljes tartománynévje, amelyben az SQL Server virtuális gépei tartózkodnak. |
   | **Meglévő tartományi fiók** | Olyan meglévő tartományi felhasználói fiók, amely **számítógépobjektum létrehozása** engedéllyel rendelkezik a tartományban, mivel a [CNO](/windows-server/failover-clustering/prestage-cluster-adds) a sablon központi telepítése során jön létre. Például egy tartományi rendszergazdai fiók általában megfelelő account@domain.comengedéllyel rendelkezik (például: ). *Ez a fiók is része kell lennie a helyi rendszergazdai csoport minden virtuális gép a fürt létrehozásához.*| 
   | **Tartományi fiók jelszava** | A korábban említett tartományi felhasználói fiók jelszava. | 
   | **Meglévő Sql Service-fiók** | Az [SQL Server szolgáltatást](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) a rendelkezésre állási csoport account@domain.comtelepítése során vezérlő tartományi felhasználói fiók (például: ). |
   | **Sql szolgáltatás jelszava** | Az SQL Server szolgáltatást vezérlő tartományi felhasználói fiók által használt jelszó. |
   | **Felhőbeli tanú neve** | Egy új Azure-tárfiók, amely jön létre, és a felhő tanúsító számára lesz használva. Ezt a nevet módosíthatja. |
   | **\_összetevők helye** | Ez a mező alapértelmezés szerint be van állítva, és nem módosítható. |
   | **\_összetevők Hely Sas Token** | Ez a mező szándékosan üresen marad. |
   | &nbsp; | &nbsp; |

1. Ha elfogadja a feltételeket, jelölje **be az Elfogadom a fent meghatározott feltételeket** jelölőnégyzetet. Ezután válassza **a Vásárlás** lehetőséget a rövid útmutató sablon telepítésének befejezéséhez. 
1. Az üzembe helyezés figyeléséhez válassza ki a központi telepítést a felső navigációs szalagcím **Értesítések** csengőikon, vagy nyissa meg az **Erőforráscsoport** az Azure Portalon. Válassza **a Központi telepítések** lehetőséget a **Beállítások**csoportban, és válassza a **Microsoft.Template** központi telepítését. 

>[!NOTE]
> A sablon központi telepítése során megadott hitelesítő adatok csak a központi telepítés hosszában tárolódnak. A telepítés befejezése után ezek a jelszavak törlődnek. Ha további SQL Server virtuális gépeket ad hozzá a fürthöz, a rendszer újra meg kéri őket. 


## <a name="step-2-manually-create-the-availability-group"></a>2. lépés: A rendelkezésre állási csoport manuális létrehozása 
Manuálisan hozza létre a rendelkezésre állási csoportot a szokásos módon az [SQL Server Management Studio,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)a [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)vagy [a Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)használatával. 

>[!IMPORTANT]
> *Ne* hozzon létre figyelőt ebben az időben, mert a **101-sql-vm-aglistener-setup gyorsindítási** sablon nem, hogy automatikusan a 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>3. lépés: Manuálisan hozza létre a belső terheléselosztót
A Mindig rendelkezésre álláson lévő csoportfigyelő az Azure Load Balancer belső példányát igényli. A belső terheléselosztó egy "lebegő" IP-címet biztosít a rendelkezésre állási csoport figyelőjének, amely lehetővé teszi a gyorsabb feladatátvételt és újracsatlakozást. Ha egy rendelkezésre állási csoportban lévő SQL Server virtuális gépek ugyanannak a rendelkezésre állási csoportnak a részét képezik, használhatja az alapszintű terheléselosztót. Ellenkező esetben szabványos terheléselosztót kell használnia. 

> [!IMPORTANT]
> A belső terheléselosztónak ugyanabban a virtuális hálózatban kell lennie, mint az SQL Server virtuálisgép-példányainak. 

Csak létre kell hoznia a belső terheléselosztót. 4. lépésben a **101-sql-vm-aglistener-setup gyorsindítási** sablon kezeli a konfiguráció többi részét (például a háttérkészletet, az állapotminta és a terheléselosztási szabályok). 

1. Az Azure Portalon nyissa meg az SQL Server virtuális gépeket tartalmazó erőforráscsoportot. 
2. Az erőforráscsoportban válassza a **Hozzáadás**lehetőséget.
3. Keresse meg a **terheléselosztót**. A keresési eredmények között válassza a **Microsoft**által közzétett **Terheléselosztó**lehetőséget.
4. A **Terheléselosztó** panelen válassza a **Létrehozás gombot.**
5. A **Terheléselosztó létrehozása** párbeszédpanelen a következőképpen konfigurálja a terheléselosztót:

   | Beállítás | Érték |
   | --- | --- |
   | **Név** |Írjon be egy szövegnevet, amely a terheléselosztót jelöli. Írja be például az **sqlLB**értéket. |
   | **Típus** |**Belső**: A legtöbb implementáció kontakaregy belső terheléselosztót, amely lehetővé teszi, hogy az ugyanazon a virtuális hálózaton belüli alkalmazások csatlakozzanak a rendelkezésre állási csoporthoz.  </br> **Külső**: Lehetővé teszi, hogy az alkalmazások nyilvános internetkapcsolaton keresztül csatlakozzanak a rendelkezésre állási csoporthoz. |
   | **Virtuális hálózat** | Válassza ki azt a virtuális hálózatot, amelyben az SQL Server-példányok találhatók. |
   | **Alhálózat** | Válassza ki azt az alhálózatot, amelyben az SQL Server-példányok találhatók. |
   | **IP-cím hozzárendelése** |**Statikus** |
   | **Privát IP-cím** | Adjon meg egy elérhető IP-címet az alhálózatból. |
   | **Előfizetés** |Ha több előfizetéssel rendelkezik, ez a mező megjelenhet. Válassza ki az erőforráshoz társítani kívánt előfizetést. Általában ugyanaz az előfizetés, mint a rendelkezésre állási csoport összes erőforrása. |
   | **Erőforráscsoport** |Válassza ki azt az erőforráscsoportot, amelyben az SQL Server-példányok találhatók. |
   | **Helyen** |Válassza ki azt az Azure-helyet, amelyben az SQL Server-példányok találhatók. |
   | &nbsp; | &nbsp; |

6. Kattintson a **Létrehozás** gombra. 


>[!IMPORTANT]
> Az egyes SQL Server virtuális gépek nyilvános IP-erőforrásának szabványos termékváltozattal kell rendelkeznie, hogy kompatibilis legyen a standard terheléselosztóval. A virtuális gép nyilvános IP-erőforrásának termékváltozatának meghatározásához nyissa meg az **Erőforráscsoport**lehetőséget, válassza ki az SQL Server virtuális gép **nyilvános IP-cím erőforrását,** és keresse meg az értéket a **Termékváltozat** ban az **Áttekintő** ablaktáblában. 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>4. lépés: Hozza létre a rendelkezésre állási csoport figyelőjét, és konfigurálja a belső terheléselosztót a rövid útmutató sablon használatával

Hozza létre a rendelkezésre állási csoport figyelőjét, és konfigurálja automatikusan a belső terheléselosztót a **101-sql-vm-aglistener-setup** gyorsindítási sablon használatával. A sablon a Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener erőforrást rendeli. A **101-sql-vm-aglistener-setup** gyorsindítási sablon az SQL Virtuálisgép-erőforrás-szolgáltatón keresztül a következő műveleteket végzi el:

- Létrehoz egy új előtér-IP-erőforrást (a telepítés során megadott IP-címérték alapján) a figyelő számára. 
- A fürt és a belső terheléselosztó hálózati beállításainak konfigurálása. 
- Konfigurálja a belső terheléselosztó, az állapotminta és a terheléselosztási szabályok háttérkészletét.
- Létrehozza a rendelkezésre állási csoport figyelőa a megadott IP-címet és nevet.
 
>[!NOTE]
> A **101-sql-vm-aglistener-setup csak** akkor használható, ha a Windows feladatátvevő fürta **101-sql-vm-ag-setup** sablonnal jött létre.
   
   
A belső terheléselosztó konfigurálásához és a rendelkezésre állási csoport figyelőjének létrehozásához tegye a következőket:
1. Nyissa meg a [101-sql-vm-aglistener-setup gyorsindítási](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) sablont, és válassza a Telepítés az **Azure-ba** lehetőséget a gyorsindítási sablon elindításához az Azure Portalon.
1. Töltse ki a belső terheléselosztó konfigurálásához szükséges mezőket, és hozza létre a rendelkezésre állási csoport figyelője. A választható mezőket üresen hagyhatja. 

   Az alábbi táblázat a sablon szükséges értékeit mutatja be: 

   | **Mező** | Érték |
   | --- | --- |
   |**Erőforráscsoport** | Az az erőforráscsoport, ahol az SQL Server virtuális gépek és a rendelkezésre állási csoport létezik. | 
   |**Meglévő feladatátvevő fürt neve** | Annak a fürtnek a neve, amelyhez az SQL Server virtuális gépei csatlakoznak. |
   | **Meglévő Sql availability csoport**| Annak a rendelkezésre állási csoportnak a neve, amelynek az SQL Server virtuális gépei részét képezik. |
   | **Meglévő virtuálisgép-lista** | A korábban említett rendelkezésre állási csoport részét játszó SQL Server virtuális gépek nevei. A neveket vesszővel és szóközvel (például *SQLVM1, SQLVM2)* válassza el egymástól. |
   | **Figyelő** | A figyelőhöz rendelni kívánt DNS-név. Alapértelmezés szerint ez a sablon megadja az "aglistener" nevet, de módosíthatja. A név nem haladhatja meg a 15 karaktert. |
   | **Figyelő port** | A figyelő által használni kívánt port. Ez a port általában 1433-as alapértelmezett. Ez a sablon által megadott portszám. De ha az alapértelmezett port megváltozott, a figyelő port kell használnia ezt az értéket. | 
   | **Figyelő IP** | A figyelő által használandó IP-cím. Ez a cím jön létre a sablon telepítése során, így adjon meg egy, amely még nem használatban van.  |
   | **Meglévő alhálózat** | Az SQL Server virtuális gépek belső alhálózatának neve (például: *default).* Ezt az értéket az **Erőforráscsoport**, a virtuális hálózat kiválasztása, a **Beállítások** ablaktábla **Alhálózatok** elemének kiválasztása, valamint a **Név**alatti érték másolása segítségével határozhatja meg. |
   | **Meglévő belső terheléselosztó** | A 3. |
   | **Szonda port** | A belső terheléselosztó által használt mintavételi port. A sablon alapértelmezés szerint 59999-et használ, de ezt az értéket módosíthatja. |
   | &nbsp; | &nbsp; |

1. Ha elfogadja a feltételeket, jelölje **be az Elfogadom a fent meghatározott feltételeket** jelölőnégyzetet. A **Gyorsútmutató** sablon telepítésének befejezéséhez válassza a Vásárlás lehetőséget. 
1. Az üzembe helyezés figyeléséhez válassza ki a központi telepítést a felső navigációs szalagcím **Értesítések** csengőikon, vagy nyissa meg az **Erőforráscsoport** az Azure Portalon. Válassza **a Központi telepítések** lehetőséget a **Beállítások**csoportban, és válassza a **Microsoft.Template** központi telepítését. 

>[!NOTE]
>Ha a központi telepítés nem sikerül a felénél, manuálisan el kell [távolítania az újonnan létrehozott figyelő](#remove-the-availability-group-listener) t a PowerShell használatával, mielőtt újraüzembe helyezné a **101-sql-vm-aglistener-setup** gyorsindítási sablon. 

## <a name="remove-the-availability-group-listener"></a>Az elérhetőségi csoport figyelőjének eltávolítása
Ha később el kell távolítania a sablon konfigurált rendelkezésre állási csoport figyelőjét, át kell mennie az SQL virtuálisgép-erőforrás-szolgáltatón. Mivel a figyelő regisztrálva van az SQL Virtuálisgép-erőforrás-szolgáltatón keresztül, csak az SQL Server Management Studio-n keresztüli törlése nem elegendő. 

A legjobb módszer az SQL VM erőforrás-szolgáltatón keresztül i. kódrészlet használatával a PowerShell használatával. Ezzel eltávolítja a rendelkezésre állási csoport figyelőmetaadatait az SQL virtuálisgép-erőforrás-szolgáltatóból. Azt is fizikailag törli a figyelőt a rendelkezésre állási csoportból. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Gyakori hibák
Ez a rész néhány ismert problémát és azok lehetséges megoldását ismerteti. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Rendelkezésre állási csoport\<figyelője a rendelkezésre állási csoport " AG-név>" már létezik
A kijelölt rendelkezésre állási csoport az Azure-ban rövid útmutató sablona a rendelkezésre állási csoport figyelő már tartalmaz egy figyelőt. Vagy fizikailag a rendelkezésre állási csoporton belül, vagy a metaadatok az SQL virtuálisgép-erőforrás-szolgáltatón belül maradnak. Távolítsa el a figyelőt a [PowerShell](#remove-the-availability-group-listener) használatával a **101-sql-vm-aglistener-setup** gyorsindítási sablon újratelepítése előtt. 

### <a name="connection-only-works-from-primary-replica"></a>A kapcsolat csak az elsődleges kópiából működik
Ez a viselkedés valószínűleg egy sikertelen **101-sql-vm-aglistener-setup** sablon központi telepítése, amely elhagyta a konfigurációt a belső terheléselosztó inkonzisztens állapotban. Ellenőrizze, hogy a háttérkészlet felsorolja-e a rendelkezésre állási készletet, és hogy léteznek-e szabályok az állapotmintához és a terheléselosztási szabályokhoz. Ha valami hiányzik, a belső terheléselosztó konfigurációja inkonzisztens állapot. 

A probléma megoldásához távolítsa el a figyelőt a [PowerShell](#remove-the-availability-group-listener)használatával, törölje a belső terheléselosztót az Azure Portalon keresztül, és kezdje újra a 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - Csak az SQL virtuálisgépek listája frissíthető
Ez a hiba akkor fordulhat elő, ha a **101-sql-vm-aglistener-setup** sablon telepítésekor a figyelő t törölték az SQL Server Management Studio (SSMS) rendszeren keresztül, de nem törölték az SQL VM erőforrás-szolgáltató. A figyelő SSMS-en keresztüli törlése nem távolítja el a figyelő metaadatait az SQL virtuálisgép-erőforrás-szolgáltatóból. A figyelőt törölni kell az erőforrás-szolgáltatóból a [PowerShellen](#remove-the-availability-group-listener)keresztül. 

### <a name="domain-account-does-not-exist"></a>A tartományi fiók nem létezik
Ennek a hibának két oka lehet. A megadott tartományfiók nem létezik, vagy hiányoznak az [egyszerű felhasználónév (UPN)](/windows/desktop/ad/naming-properties#userprincipalname) adatai. A **101-sql-vm-ag-setup** sablon egy tartományfiókot vár az UPN képernyőn (azaz ), de előfordulhat, *user@domain.com*hogy egyes tartományi fiókokból hiányzik. Ez általában akkor fordul elő, ha egy helyi felhasználót az első tartományi rendszergazdai fióknak telepítettek át, amikor a kiszolgálót tartományvezérlővé léptették elő, vagy amikor egy felhasználót a PowerShellen keresztül hoztak létre. 

Ellenőrizze, hogy létezik-e a fiók. Ha igen, akkor lehet, hogy fut a második helyzet. A probléma megoldásához tegye a következőket:

1. A tartományvezérlőn nyissa meg az **Active Directory – felhasználók és számítógépek** ablakot a **Kiszolgálókezelő** **Eszközök** beállításából. 
2. Nyissa meg a fiókot a bal oldali ablaktáblában a **Felhasználók** lehetőséget választva.
3. Kattintson a jobb gombbal a fiókra, és válassza **a Tulajdonságok parancsot.**
4. Válassza a **Fiók** lapot. Ha a **Felhasználó bejelentkezési neve** mező üres, ez okozza a hibát. 

    ![Az üres felhasználói fiók hiányzó UPN-t jelez](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Töltse ki a **Felhasználó bejelentkezési nevét** mezőben, hogy megfeleljen a felhasználó nevének, és válassza ki a megfelelő tartományt a legördülő listából. 
6. A módosítások mentéséhez válassza az **Alkalmaz** lehetőséget, és zárja be a párbeszédpanelt az **OK**gombra kattintva. 

Miután elvégzette ezeket a módosításokat, próbálja meg még egyszer üzembe helyezni az Azure gyorsindítási sablont. 



## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Az SQL Server virtuális gépek áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [Gyakori kérdések az SQL Server virtuális gépekhez](virtual-machines-windows-sql-server-iaas-faq.md)
* [Az SQL Server virtuális gépek díjszabási útmutatója](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Kibocsátási megjegyzések az SQL Server virtuális gépekhez](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Licencelési modellek váltása SQL Server virtuális géphez](virtual-machines-windows-sql-ahb.md)



