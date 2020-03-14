---
title: 'Oktatóanyag: rendelkezésre állási csoport konfigurálása'
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre SQL Server always on rendelkezésre állási csoportot az Azure Virtual Machines-on.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.custom: seo-lt-2019
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.openlocfilehash: ed5fc923c82fb0d0e4004e18159d943564c6f55e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249853"
---
# <a name="tutorial-configure-availability-group-on-azure-sql-server-vm-manually"></a>Oktatóanyag: rendelkezésre állási csoport konfigurálása az Azure SQL Server VM manuálisan

Ez az oktatóanyag bemutatja, hogyan hozhat létre SQL Server always on rendelkezésre állási csoportot az Azure Virtual Machines-on. A teljes oktatóanyag egy rendelkezésre állási csoportot hoz létre két SQL Server adatbázis-replikával.

**Becsült időtartam**: az előfeltételek teljesítése után körülbelül 30 percet vesz igénybe.

A diagram az oktatóanyagban felépített tudnivalókat mutatja be.

![Rendelkezésreállási csoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag feltételezi, hogy rendelkezik SQL Server always on rendelkezésre állási csoportokkal kapcsolatos alapvető ismeretekkel. Ha további információra van szüksége, tekintse [meg az Always On rendelkezésre állási csoportok áttekintése (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx)című témakört.

A következő táblázat felsorolja azokat az előfeltételeket, amelyeket el kell végeznie az oktatóanyag megkezdése előtt:

|  |Követelmény |Leírás |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Két SQL-kiszolgáló | – Azure-beli rendelkezésre állási csoport <br/> -Egyetlen tartományban <br/> -A feladatátvételi fürtszolgáltatás telepített funkciója |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Fájlmegosztás a tanúsító fürthöz |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server szolgáltatásfiók | Tartományi fiók |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server Agent szolgáltatásfiók | Tartományi fiók |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|A tűzfal portjai nyitva vannak | -SQL Server: **1433** az alapértelmezett példányhoz <br/> – Adatbázis-tükrözési végpont: **5022** vagy bármely elérhető port <br/> -Rendelkezésre állási csoport terheléselosztó IP-címe állapotának mintavétele: **59999** vagy bármely elérhető port <br/> – A fürt alapvető terheléselosztó IP-címe állapotának mintavétele: **58888** vagy bármely elérhető port |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Feladatátvételi fürtszolgáltatás hozzáadása | Ehhez a szolgáltatáshoz mindkét SQL Server-kiszolgáló szükséges |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Telepítési tartományi fiók | -Helyi rendszergazda az egyes SQL Servereken <br/> -SQL Server sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagja SQL Server egyes példányaihoz  |


Az oktatóanyag megkezdése előtt be kell [fejeznie az Always On rendelkezésre állási csoportok létrehozásához szükséges előfeltételeket az Azure Virtual Machinesban](virtual-machines-windows-portal-sql-availability-group-prereq.md). Ha ezek az előfeltételek már befejeződtek, ugorjon a [fürt létrehozása](#CreateCluster)lehetőségre.

  >[!NOTE]
  > Az oktatóanyagban ismertetett lépések közül sokat automatizálhat az [Azure SQL VM parancssori](virtual-machines-windows-sql-availability-group-cli.md) felülettel és az [Azure Gyorsindítás sablonjaival](virtual-machines-windows-sql-availability-group-quickstart-template.md).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>A fürt létrehozása

Az előfeltételek befejezését követően az első lépés egy Windows Server feladatátvevő fürt létrehozása, amely két SQL-kiszolgálót és egy tanúsító kiszolgálót tartalmaz.

1. Az első SQL Server RDP-t egy olyan tartományi fiók használatával, amely az SQL-kiszolgálók és a tanúsító kiszolgáló rendszergazdája.

   >[!TIP]
   >Ha követte az [előfeltételeket tartalmazó dokumentumot](virtual-machines-windows-portal-sql-availability-group-prereq.md), létrehozott egy **CORP\Install**nevű fiókot. Használja ezt a fiókot.

2. A **Kiszolgálókezelő** irányítópultján válassza az **eszközök**, majd a **Feladatátvevőfürt-kezelő**lehetőséget.
3. A bal oldali ablaktáblán kattintson a jobb gombbal a **Feladatátvevőfürt-kezelő**elemre, majd kattintson **a fürt létrehozása**parancsra.
   ![fürt létrehozása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. A fürt létrehozása varázslóban hozzon létre egy egycsomópontos fürtöt a következő táblázatban található beállításokkal:

   | Oldalala | Beállítások |
   | --- | --- |
   | Kezdés előtt |Alapértelmezett beállítások használata |
   | Kiszolgálók kiválasztása |Írja be az első SQL Server nevet az **adja meg a kiszolgáló nevét** mezőbe, majd kattintson a **Hozzáadás**gombra. |
   | Érvényesítési figyelmeztetés |Válassza a **nem lehetőséget. nem igényelem a Microsoft támogatását ehhez a fürthöz, ezért nem kívánja futtatni az ellenőrző teszteket. Amikor a Tovább gombra kattintok, folytassa a fürt létrehozását**. |
   | Hozzáférési pont a fürt felügyeletéhez |Adja meg a fürt nevét, például **SQLAGCluster1** a **fürt nevében**.|
   | Megerősítés |Csak akkor használja az alapértelmezett értékeket, ha tárolóhelyeket használ. Lásd a táblázatot követő megjegyzést. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>A Windows Server feladatátvételi fürt IP-címének beállítása

  > [!NOTE]
  > A Windows Server 2019 rendszeren a fürt egy **elosztott kiszolgálónevet** hoz létre a **fürt hálózati neve**helyett. Ha a Windows Server 2019-et használja, ugorja át az oktatóanyagban szereplő, a fürt alapvető nevére vonatkozó lépéseket. Fürt hálózati nevét a [PowerShell](virtual-machines-windows-portal-sql-create-failover-cluster.md#windows-server-2019)használatával hozhatja létre. További információért tekintse át a következőt [: fürt hálózati objektuma](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) . 

1. A **Feladatátvevőfürt-kezelőban**görgessen le a **fürt alapvető erőforrásai** elemre, és bontsa ki a fürt részleteit. A **hibás** állapotú **nevet** és az **IP-cím** erőforrásait is látnia kell. Az IP-cím erőforrás nem hozható online állapotba, mert a fürt ugyanahhoz az IP-címhez van hozzárendelve, mint maga a gép, ezért ez egy duplikált cím.

2. Kattintson a jobb gombbal a sikertelen **IP-cím** erőforrásra, majd kattintson a **Tulajdonságok**elemre.

   ![Fürt tulajdonságai](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Válassza a **statikus IP-cím** lehetőséget, és adjon meg egy elérhető címet a virtuális gépekkel azonos alhálózaton.

4. A **fürt alapvető erőforrásai** szakaszban kattintson a jobb gombbal a fürt nevére, majd kattintson az **online állapotba hozás**lehetőségre. Ezután várjon, amíg mindkét erőforrás online állapotú. Ha a fürt neve erőforrás online állapotba kerül, az frissíti a tartományvezérlő-kiszolgálót egy új AD-számítógépfiók-fiókkal. Ezt az AD-fiókot használhatja a rendelkezésre állási csoport fürtözött szolgáltatásának későbbi futtatásához.

### <a name="addNode"></a>A másik SQL Server hozzáadása a fürthöz

Adja hozzá a másik SQL Server a fürthöz.

1. A böngésző fáján kattintson a jobb gombbal a fürtre, majd kattintson a **Csomópont hozzáadása**parancsra.

    ![Csomópont hozzáadása a fürthöz](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. A **Csomópont hozzáadása varázslóban**kattintson a **tovább**gombra. A **kiszolgálók kiválasztása** lapon adja hozzá a második SQL Server. A Kiszolgálónév mezőbe írja be a kiszolgáló **nevét, majd** kattintson a **Hozzáadás**gombra. Ha elkészült, kattintson a **tovább**gombra.

1. Az **érvényesítési figyelmeztetés** lapon kattintson a **nem** (éles környezetben az ellenőrzési tesztek elvégzéséhez) elemre. Ezután kattintson a **Tovább** gombra.

8. Ha tárolóhelyeket használ a **megerősítő** lapon, törölje a jelet az összes megfelelő **tároló hozzáadása a fürthöz** feliratú jelölőnégyzetből.

   ![Csomópont-megerősítés hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Ha tárolóhelyeket használ, és nem törli **az összes megfelelő tároló hozzáadása a fürthöz**lehetőséget, a Windows leválasztja a virtuális lemezeket a fürtözési folyamat során. Ennek eredményeképpen azok nem jelennek meg a Lemezkezelés eszközben vagy az Intézőben, amíg a tárolóhelyek el nem lesznek távolítva a fürtből, és nem kapcsolódnak újra a PowerShell használatával. A tárolóhelyek több lemezt is csoportosítanak a Storage-készletekbe. További információ: [tárolóhelyek](https://technet.microsoft.com/library/hh831739).

1. Kattintson a **Tovább** gombra.

1. Kattintson a **Finish** (Befejezés) gombra.

   Feladatátvevőfürt-kezelő azt mutatja, hogy a fürt új csomóponttal rendelkezik, és a **csomópontok** tárolóban listázza azt.

10. Jelentkezzen ki a távoli asztali munkamenetből.

### <a name="add-a-cluster-quorum-file-share"></a>Fürt Kvórumának fájlmegosztás hozzáadása

Ebben a példában a Windows-fürt fájlmegosztást használ a fürt Kvórumának létrehozásához. Ez az oktatóanyag egy csomópont-és fájlmegosztás-többségi kvórumot használ. További információ: [A feladatátvevő fürtök kvórumkonfigurációinak ismertetése](https://technet.microsoft.com/library/cc731739.aspx).

1. Kapcsolódjon a tanúsító fájlmegosztás-tagkiszolgáló távoli asztali munkamenettel.

1. A **Kiszolgálókezelőben**kattintson az **eszközök**elemre. Nyissa meg a **Számítógép-kezelés**programot.

1. Kattintson a **megosztott mappák**elemre.

1. Kattintson a jobb gombbal a **megosztások**elemre, majd kattintson az **új megosztás..** . elemre.

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Megosztás létrehozásához használja **a megosztott mappa létrehozása varázslót** .

1. A **mappa elérési útja**területen kattintson a **Tallózás** gombra, és keresse meg vagy hozza létre a megosztott mappa elérési útját. Kattintson a **Tovább** gombra.

1. A **név, leírás és beállítások területen** ellenőrizze a megosztás nevét és elérési útját. Kattintson a **Tovább** gombra.

1. A **megosztott mappa engedélyeinek** beállítása **testreszabja az engedélyeket**. Kattintson az **Egyéni...** elemre.

1. A **testreszabási engedélyek**területen kattintson a **Hozzáadás...** elemre.

1. Győződjön meg arról, hogy a fürt létrehozásához használt fiók teljes hozzáféréssel rendelkezik.

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Kattintson az **OK** gombra.

1. A **megosztott mappák engedélyei**területen kattintson a **Befejezés**gombra. Kattintson ismét a **Befejezés** gombra.  

1. Kijelentkezés a kiszolgálóról

### <a name="configure-cluster-quorum"></a>Fürt Kvórumának konfigurálása

Ezután állítsa be a fürt kvórumát.

1. Kapcsolódjon az első fürtcsomópont-csomóponthoz a távoli asztal használatával.

1. **Feladatátvevőfürt-kezelő**kattintson a jobb gombbal a fürtre, mutasson a **További műveletek**pontra, majd kattintson a **fürt kvórum beállításainak konfigurálása..** . elemre.

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. A **fürt Kvórumának konfigurálása varázslóban**kattintson a **tovább**gombra.

1. A **kvórum konfigurációjának kiválasztása beállításnál**válassza **a tanúsító kvórum kijelölése**lehetőséget, majd kattintson a **tovább**gombra.

1. A **kvórum tanúsító kijelölése lapon**kattintson **a tanúsító fájlmegosztás konfigurálása**elemre.

   >[!TIP]
   >A Windows Server 2016 támogatja a Felhőbeli tanúkat. Ha ezt a típusú tanúsító választja, nincs szükség a tanúsító fájlmegosztás megosztására. További információ: [Felhőbeli tanúsító üzembe helyezése feladatátvevő fürtön](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Ez az oktatóanyag egy tanúsító fájlmegosztást használ, amelyet a korábbi operációs rendszerek támogatnak.

1. A **tanúsító fájlmegosztás konfigurálása**területen adja meg a létrehozott megosztás elérési útját. Kattintson a **Tovább** gombra.

1. Ellenőrizze a beállításokat a **megerősítéshez**. Kattintson a **Tovább** gombra.

1. Kattintson a **Finish** (Befejezés) gombra.

A fürt alapvető erőforrásai egy tanúsító fájlmegosztás használatára vannak konfigurálva.

## <a name="enable-availability-groups"></a>Rendelkezésre állási csoportok engedélyezése

Ezután engedélyezze a **AlwaysOn rendelkezésre állási csoportok** funkciót. Hajtsa végre ezeket a lépéseket mindkét SQL Serveren.

1. A **kezdőképernyőn** indítsa el a **SQL Server konfigurációkezelő**.
2. A böngésző fáján kattintson a **SQL Server szolgáltatások**elemre, majd a jobb gombbal kattintson a **SQL Server (MSSQLSERVER)** szolgáltatásra, majd kattintson a **Tulajdonságok**elemre.
3. Kattintson a **magas rendelkezésre állás AlwaysOn** fülre, majd válassza a **AlwaysOn rendelkezésre állási csoportok engedélyezése**lehetőséget a következő módon:

    ![AlwaysOn rendelkezésre állási csoportok engedélyezése](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Kattintson az **Alkalmaz** gombra. Az előugró ablakban kattintson az **OK gombra** .

5. Indítsa újra az SQL Server szolgáltatást.

Ismételje meg ezeket a lépéseket a többi SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Adatbázis létrehozása az első SQL Server

1. Indítsa el az RDP-fájlt az első SQL Server egy olyan tartományi fiókkal, amely tagja a sysadmin rögzített kiszolgálói szerepkörnek.
1. Nyissa meg SQL Server Management Studio és kapcsolódjon az első SQL Serverhoz.
7. **Object Explorer**kattintson a jobb gombbal az **adatbázisok** elemre, majd kattintson az **új adatbázis**elemre.
8. Az **adatbázis neve**mezőbe írja be a következőt: **MyDB1**, majd kattintson **az OK**gombra.

### <a name="backupshare"></a>Biztonsági másolat megosztásának létrehozása

1. A **Kiszolgálókezelő**első SQL Server kattintson az **eszközök**elemre. Nyissa meg a **Számítógép-kezelés**programot.

1. Kattintson a **megosztott mappák**elemre.

1. Kattintson a jobb gombbal a **megosztások**elemre, majd kattintson az **új megosztás..** . elemre.

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Megosztás létrehozásához használja **a megosztott mappa létrehozása varázslót** .

1. A **mappa elérési útja**területen kattintson a **Tallózás** gombra, és keresse meg vagy hozza létre az adatbázis biztonsági másolatának megosztott mappájának elérési útját. Kattintson a **Tovább** gombra.

1. A **név, leírás és beállítások területen** ellenőrizze a megosztás nevét és elérési útját. Kattintson a **Tovább** gombra.

1. A **megosztott mappa engedélyeinek** beállítása **testreszabja az engedélyeket**. Kattintson az **Egyéni...** elemre.

1. A **testreszabási engedélyek**területen kattintson a **Hozzáadás...** elemre.

1. Győződjön meg arról, hogy mindkét kiszolgáló SQL Server és SQL Server Agent szolgáltatás fiókja teljes hozzáféréssel rendelkezik.

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Kattintson az **OK** gombra.

1. A **megosztott mappák engedélyei**területen kattintson a **Befejezés**gombra. Kattintson ismét a **Befejezés** gombra.  

### <a name="take-a-full-backup-of-the-database"></a>Készítsen teljes biztonsági másolatot az adatbázisról

A naplózási lánc inicializálásához biztonsági mentést kell készítenie az új adatbázisról. Ha nem készít biztonsági másolatot az új adatbázisról, azt nem lehet felvenni egy rendelkezésre állási csoportba.

1. **Object Explorerban**kattintson a jobb gombbal az adatbázisra, mutasson a **feladatok...** lehetőségre, majd a **biztonsági mentés**elemre.

1. Az **OK** gombra kattintva teljes biztonsági mentést készíthet az alapértelmezett biztonsági mentési helyre.

## <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozása
Most már készen áll a rendelkezésre állási csoport konfigurálására a következő lépések végrehajtásával:

* Hozzon létre egy adatbázist az első SQL Server.
* Készítsen egy teljes biztonsági mentést és egy tranzakciónapló biztonsági másolatát az adatbázisról
* A teljes és a naplóbeli biztonsági másolatok visszaállítása a második SQL Serverra a nem **helyreállítási** lehetőséggel
* A rendelkezésre állási csoport (**AG1**) létrehozása szinkron véglegesítés, automatikus feladatátvétel és olvasható másodlagos replikák révén

### <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozása:

1. Távoli asztali munkamenetben az első SQL Server. A SSMS **Object Explorer** kattintson a jobb gombbal a **AlwaysOn magas rendelkezésre állása** elemre, majd kattintson az **új rendelkezésre állási csoport varázsló**elemre.

    ![Új rendelkezésre állási csoport elindítása varázsló](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. A **Bevezetés** lapon kattintson a **tovább**gombra. A **rendelkezésre állási csoport nevének megadása** lapon írja be a rendelkezésre állási csoport nevét (például **AG1**) a **rendelkezésre állási csoport neve**mezőbe. Kattintson a **Tovább** gombra.

    ![Új AG varázsló, adja meg az AG nevét](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Az **adatbázisok kiválasztása** lapon válassza ki az adatbázist, és kattintson a **tovább**gombra.

   >[!NOTE]
   >Az adatbázis megfelel a rendelkezésre állási csoport előfeltételeinek, mert legalább egy teljes biztonsági mentést végzett a kívánt elsődleges replikán.

   ![Új AG varázsló, adatbázisok kiválasztása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. A **replikák megadása** lapon kattintson a **replika hozzáadása**lehetőségre.

   ![Új AG varázsló, replikák meghatározása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. Megjelenik a **Kapcsolódás a kiszolgálóhoz** párbeszédpanel. Írja be a **kiszolgáló neve**mezőbe a második kiszolgáló nevét. Kattintson a **Csatlakozás** gombra.

   A **replikák megadására** szolgáló lapon a második kiszolgáló jelenik meg a **rendelkezésre állási replikák**területen. Konfigurálja a replikákat az alábbiak szerint.

   ![Új AG varázsló, adja meg a replikákat (kész)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Kattintson a **végpontok** elemre a rendelkezésre állási csoport adatbázis-tükrözési végpontjának megtekintéséhez. Használja ugyanazt a portot, amelyet a [Tűzfalszabály adatbázis-tükrözési végpontokhoz való](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall)beállításakor használt.

    ![Új AG varázsló, válassza a kezdeti adatszinkronizálás lehetőséget.](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. A **kezdeti adatszinkronizálás kiválasztása** lapon válassza a **teljes** lehetőséget, és adjon meg egy megosztott hálózati helyet. A helyhez használja a [létrehozott biztonsági mentési megosztást](#backupshare). A példában a **\\\\\<első SQL Server\>\Backup\\** . Kattintson a **Tovább** gombra.

   >[!NOTE]
   >A teljes szinkronizálás teljes biztonsági mentést készít az adatbázisról SQL Server első példányán, és visszaállítja a második példányra. Nagyméretű adatbázisok esetén a teljes szinkronizálás nem ajánlott, mert hosszú időt is igénybe vehet. Ezt az időt manuálisan is elvégezheti, ha kézzel készít biztonsági másolatot az adatbázisról, és visszaállítja `NO RECOVERY`. Ha az adatbázis már vissza van állítva `NO RECOVERY` a második SQL Server a rendelkezésre állási csoport konfigurálása előtt, válassza a **csak csatlakozás**lehetőséget. Ha a rendelkezésre állási csoport konfigurálása után szeretné a biztonsági mentést készíteni, válassza a **kezdeti adatszinkronizálás kihagyása**lehetőséget.

    ![Új AG varázsló, válassza a kezdeti adatszinkronizálás lehetőséget.](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Az **Érvényesítés** lapon kattintson a **tovább**gombra. Ennek a lapnak a következő képhez hasonlóan kell kinéznie:

    ![Új AG varázsló, ellenőrzés](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >A figyelő konfigurációja figyelmeztetést kapott, mert még nem konfigurálta a rendelkezésre állási csoport figyelőjét. Figyelmen kívül hagyhatja ezt a figyelmeztetést, mert az Azure Virtual Machines szolgáltatásban az Azure Load Balancer létrehozása után létrehozhatja a figyelőt.

10. Az **Összefoglalás** lapon kattintson a **Befejezés**gombra, majd várjon, amíg a varázsló konfigurálja az új rendelkezésre állási csoportot. A **folyamat** lapon **további részletekre** kattintva megtekintheti a részletes előrehaladást. A varázsló befejezése után vizsgálja meg az **eredmények** lapot annak ellenőrzéséhez, hogy a rendelkezésre állási csoport sikeresen létrejött-e.

     ![Új AG varázsló, eredmények](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. A varázslóból való kilépéshez kattintson a **Bezárás** gombra.

### <a name="check-the-availability-group"></a>A rendelkezésre állási csoport keresése

1. A **Object Explorer**bontsa ki a **AlwaysOn magas rendelkezésre állása**, majd a **rendelkezésre állási csoportok**csomópontot. Ekkor megjelenik az új rendelkezésre állási csoport ebben a tárolóban. Kattintson a jobb gombbal a rendelkezésre állási csoportra, majd kattintson az **irányítópult megjelenítése**lehetőségre.

   ![Az AG irányítópult megjelenítése](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   A **AlwaysOn irányítópultjának** ehhez hasonlóan kell kinéznie.

   ![AG irányítópult](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Láthatja a replikákat, az egyes replikák feladatátvételi módját és a szinkronizálási állapotot.

2. A **Feladatátvevőfürt-kezelő**kattintson a fürtre. Válassza a **szerepkörök**lehetőséget. A használt rendelkezésre állási csoport neve a fürt egyik szerepköre. A rendelkezésre állási csoport nem rendelkezik IP-címmel az ügyfélkapcsolatokhoz, mert nem konfigurált egy figyelőt. Az Azure Load Balancer létrehozása után konfigurálnia kell a figyelőt.

   ![AG Feladatátvevőfürt-kezelő](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Ne próbálkozzon a rendelkezésre állási csoport feladatátvételével a Feladatátvevőfürt-kezelő. Az összes feladatátvételi műveletet a SSMS **AlwaysOn-irányítópultján** belül kell végrehajtani. További információ: [a Feladatátvevőfürt-kezelő és a rendelkezésre állási csoportok használatának korlátozásai](https://msdn.microsoft.com/library/ff929171.aspx).
    >

Ezen a ponton van egy rendelkezésre állási csoport, amely replikákat tartalmaz SQL Server két példányán. A rendelkezésre állási csoportot példányok között helyezheti át. Még nem tud csatlakozni a rendelkezésre állási csoporthoz, mert nem rendelkezik figyelővel. Az Azure Virtual Machines szolgáltatásban a figyelőnek terheléselosztó szükséges. A következő lépés a terheléselosztó létrehozása az Azure-ban.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Azure-terheléselosztó létrehozása

Az Azure Virtual Machines szolgáltatásban egy SQL Server rendelkezésre állási csoport terheléselosztó szükséges. A terheléselosztó tárolja a rendelkezésre állási csoport figyelők és a Windows Server feladatátvevő fürt IP-címeit. Ez a szakasz összefoglalja, hogyan hozható létre a terheléselosztó a Azure Portalban.

Egy Azure Load Balancer lehet standard Load Balancer vagy egy alapszintű Load Balancer. Standard Load Balancer több funkcióval rendelkezik, mint az alapszintű Load Balancer. Rendelkezésre állási csoport esetén a standard Load Balancerra akkor van szükség, ha rendelkezésre állási zónát használ (a rendelkezésre állási csoport helyett). A terheléselosztó típusai közötti különbség részleteiért lásd: [Load BALANCER SKU-összehasonlítás](../../../load-balancer/concepts-limitations.md#skus).

1. A Azure Portal nyissa meg az erőforráscsoportot, amelyben az SQL-kiszolgálókat, majd kattintson a **+ Hozzáadás**elemre.
1. **Load Balancer**keresése. Válassza ki a Microsoft által közzétett terheléselosztó.

   ![AG Feladatátvevőfürt-kezelő](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Kattintson a  **Create** (Létrehozás) gombra.
1. Konfigurálja a terheléselosztó következő paramétereit.

   | Beállítás | Mező |
   | --- | --- |
   | **Name (Név)** |Használjon egy szöveges nevet a terheléselosztó számára, például **sqlLB**. |
   | **Típus** |Belső |
   | **Virtuális hálózat** |Használja az Azure-beli virtuális hálózat nevét. |
   | **Alhálózat** |Használja annak az alhálózatnak a nevét, amelyben a virtuális gép található.  |
   | **IP-cím hozzárendelése** |Statikus |
   | **IP-cím** |Az alhálózaton elérhető címek használata. Használja ezt a címeket a rendelkezésre állási csoport figyelője számára. Vegye figyelembe, hogy ez eltér a fürt IP-címétől.  |
   | **Előfizetés** |Használja ugyanazt az előfizetést, mint a virtuális gépet. |
   | **Hely** |Ugyanazt a helyet használja, mint a virtuális gép. |

   A Azure Portal panelnek így kell kinéznie:

   ![Load Balancer létrehozása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. A terheléselosztó létrehozásához kattintson a **Létrehozás**gombra.

A terheléselosztó konfigurálásához létre kell hoznia egy háttér-készletet, egy mintavételt, és be kell állítania a terheléselosztási szabályokat. Ezeket a Azure Portal.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Háttér-készlet hozzáadása a rendelkezésre állási csoport figyelője számára

1. A Azure Portal lépjen a rendelkezésre állási csoportba. Előfordulhat, hogy frissítenie kell a nézetet az újonnan létrehozott terheléselosztó megtekintéséhez.

   ![Load Balancer keresése az erőforráscsoporthoz](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Kattintson a terheléselosztó elemre, majd a **háttérbeli készletek**elemre, és kattintson a **+ Hozzáadás**gombra.

1. Adja meg a háttér-készlet nevét.

1. Társítsa a háttér-készletet a virtuális gépeket tartalmazó rendelkezésre állási csoporttal.

1. A **célként megadott hálózati IP-konfigurációk**területen jelölje be a **virtuális gép** lehetőséget, és válassza ki mindkét virtuális gépet, amely a rendelkezésre állási csoport replikáit fogja tárolni. Ne adja meg a tanúsító fájlmegosztás kiszolgálóját.

   >[!NOTE]
   >Ha a virtuális gépek nem lettek megadva, a kapcsolatok csak az elsődleges replikához lesznek sikeresek.

1. A háttér-készlet létrehozásához kattintson **az OK** gombra.

### <a name="set-the-probe"></a>A mintavétel beállítása

1. Kattintson a terheléselosztó elemre, majd az **állapot**-tesztek elemre, és kattintson a **+ Hozzáadás**elemre.

1. A figyelő állapotának mintavételét az alábbiak szerint állítsa be:

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Name (Név)** | Szöveg | SQLAlwaysOnEndPointProbe |
   | **Protocol (Protokoll)** | TCP kiválasztása | TCP |
   | **Port** | A fel nem használt portok | 59999 |
   | **Intervallum**  | A mintavételi kísérletek közötti időtartam másodpercben |5 |
   | **Nem Kifogástalan állapot küszöbértéke** | Az egymást követő mintavételi hibák száma, amelyeknek meg kell történnie, hogy a virtuális gép nem kifogástalannak minősül  | 2 |

1. Az állapot-mintavétel beállításához kattintson **az OK** gombra.

### <a name="set-the-load-balancing-rules"></a>Terheléselosztási szabályok beállítása

1. Kattintson a terheléselosztó elemre, kattintson a terheléselosztási **szabályok**elemre, majd kattintson a **+ Hozzáadás**gombra.

1. Állítsa be a figyelő terheléselosztási szabályait az alábbiak szerint.

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Name (Név)** | Szöveg | SQLAlwaysOnEndPointListener |
   | **Előtérbeli IP-cím** | Válasszon egy címtartományt |Használja a terheléselosztó létrehozásakor létrehozott címeket. |
   | **Protocol (Protokoll)** | TCP kiválasztása |TCP |
   | **Port** | A rendelkezésre állási csoport figyelője portjának használata | 1433 |
   | **Háttér-port** | Ez a mező nem használható, ha a közvetlen kiszolgáló visszaadására beállított lebegőpontos IP-cím | 1433 |
   | **Mintavételi** |A mintavételhez megadott név | SQLAlwaysOnEndPointProbe |
   | **Munkamenetek megőrzése** | Legördülő lista | **NEz egy** |
   | **Üresjárat időkorlátja** | A TCP-kapcsolatok megnyitásának percben tartása | 4 |
   | **Lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása)** | |Engedélyezve |

   > [!WARNING]
   > A rendszer a közvetlen kiszolgáló visszaadását állítja be a létrehozás során. A név nem módosítható.

1. A figyelő terheléselosztási szabályainak beállításához kattintson **az OK** gombra.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>A fürt alapvető IP-címének hozzáadása a Windows Server feladatátvevő fürthöz (WSFC)

A WSFC IP-címének is szerepelnie kell a terheléselosztó számára.

1. A portálon, az Azure Load balancerben kattintson az előtér **-IP-konfiguráció** elemre, majd kattintson a **+ Hozzáadás**elemre. Használja a WSFC konfigurált IP-címet a fürt alapvető erőforrásaiban. Adja meg az IP-címet statikusként.

1. A terheléselosztó területen kattintson az **állapot**-tesztek elemre, majd kattintson a **+ Hozzáadás**elemre.

1. Állítsa be a WSFC-fürt alapvető IP-címének állapotát a következőképpen:

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Name (Név)** | Szöveg | WSFCEndPointProbe |
   | **Protocol (Protokoll)** | TCP kiválasztása | TCP |
   | **Port** | A fel nem használt portok | 58888 |
   | **Intervallum**  | A mintavételi kísérletek közötti időtartam másodpercben |5 |
   | **Nem Kifogástalan állapot küszöbértéke** | Az egymást követő mintavételi hibák száma, amelyeknek meg kell történnie, hogy a virtuális gép nem kifogástalannak minősül  | 2 |

1. Az állapot-mintavétel beállításához kattintson **az OK** gombra.

1. Állítsa be a terheléselosztási szabályokat. Kattintson a terheléselosztási **szabályok**elemre, majd kattintson a **+ Hozzáadás**gombra.

1. Állítsa be a fürt alapvető IP-címének terheléselosztási szabályait az alábbiak szerint.

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Name (Név)** | Szöveg | WSFCEndPoint |
   | **Előtérbeli IP-cím** | Válasszon egy címtartományt |Használja a WSFC IP-cím beállításakor létrehozott címet. Ez eltér a figyelő IP-címétől. |
   | **Protocol (Protokoll)** | TCP kiválasztása |TCP |
   | **Port** | A fürt IP-címéhez használja a portot. Ez egy elérhető port, amely nem használatos a figyelő mintavételi portjához. | 58888 |
   | **Háttér-port** | Ez a mező nem használható, ha a közvetlen kiszolgáló visszaadására beállított lebegőpontos IP-cím | 58888 |
   | **Mintavételi** |A mintavételhez megadott név | WSFCEndPointProbe |
   | **Munkamenetek megőrzése** | Legördülő lista | **NEz egy** |
   | **Üresjárat időkorlátja** | A TCP-kapcsolatok megnyitásának percben tartása | 4 |
   | **Lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása)** | |Engedélyezve |

   > [!WARNING]
   > A rendszer a közvetlen kiszolgáló visszaadását állítja be a létrehozás során. A név nem módosítható.

1. A terheléselosztási szabályok megadásához kattintson **az OK** gombra.

## <a name="configure-listener"></a>A figyelő konfigurálása

A következő lépés a rendelkezésre állási csoport figyelő konfigurálása a feladatátvevő fürtön.

> [!NOTE]
> Ez az oktatóanyag bemutatja, hogyan hozhat létre egyetlen figyelőt – egyetlen ILB IP-címmel. Ha legalább egy figyelőt egy vagy több IP-címmel szeretne létrehozni, tekintse meg a [rendelkezésre állási csoport figyelője és a Load Balancer létrehozása című témakört. Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)-ban.
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Figyelő portjának beállítása

A SQL Server Management Studioban állítsa be a figyelő portját.

1. Indítsa el SQL Server Management Studio és kapcsolódjon az elsődleges replikához.

1. Navigáljon a **AlwaysOn magas rendelkezésre** állása | **rendelkezésre állási csoportok** | **rendelkezésre állási csoport figyelőkhöz**.

1. Ekkor megjelenik a Feladatátvevőfürt-kezelőban létrehozott figyelő neve. Kattintson a jobb gombbal a figyelő nevére, és kattintson a **Tulajdonságok**elemre.

1. A **port** mezőben határozza meg a rendelkezésre állási csoport figyelő portszámát. 1433 az alapértelmezett érték, majd kattintson az **OK**gombra.

Most már rendelkezik egy SQL Server rendelkezésre állási csoporttal a Resource Manager módban futó Azure-beli virtuális gépeken.

## <a name="test-connection-to-listener"></a>A figyelőhöz való kapcsolódás tesztelése

A kapcsolódás tesztelése:

1. Az RDP-t egy olyan SQL Serverra, amely ugyanabban a virtuális hálózatban található, de nem tulajdonosa a replikának. A fürt más SQL Server is használható.

1. A **Sqlcmd** segédprogram használatával tesztelheti a kapcsolódást. Az alábbi szkript például egy **Sqlcmd** kapcsolatot létesít az elsődleges replikával a figyelőn keresztül a Windows-hitelesítéssel:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Ha a figyelő nem az alapértelmezett portot (1433) használja, akkor a portot a csatlakozási karakterláncban kell megadni. Például a következő Sqlcmd-parancs csatlakozik egy figyelőhöz a 1435-as porton:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

Az SQLCMD-kapcsolat automatikusan csatlakozik a SQL Server-példányhoz, amely az elsődleges replikát tárolja.

> [!TIP]
> Győződjön meg arról, hogy a megadott port meg van nyitva a tűzfalon mindkét SQL Server-kiszolgálón. Mindkét kiszolgálónak szüksége van egy bejövő szabályra a használt TCP-porthoz. További információ: [Tűzfalszabály hozzáadása vagy szerkesztése](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Következő lépések

- [Adjon hozzá egy IP-címet egy terheléselosztó számára egy második rendelkezésre állási csoport számára](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
