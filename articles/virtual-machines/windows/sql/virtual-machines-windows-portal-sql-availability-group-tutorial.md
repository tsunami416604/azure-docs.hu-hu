---
title: 'Oktatóanyag: A rendelkezésre állási csoport konfigurálása'
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy SQL Server Mindig rendelkezésre állási csoport az Azure virtuális gépeken.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 426ba4c0ac84799b4d0e6bf9330508f928437fd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060177"
---
# <a name="tutorial-configure-availability-group-on-azure-sql-server-vm-manually"></a>Oktatóanyag: Rendelkezésre állási csoport konfigurálása manuálisan az Azure SQL Server virtuális gépen

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy SQL Server Mindig rendelkezésre állási csoport az Azure virtuális gépeken. A teljes oktatóanyag létrehoz egy rendelkezésre állási csoportot egy adatbázis-kópiával két SQL-kiszolgálón.

**Becsült idő**: Körülbelül 30 percet vesz igénybe, ha az előfeltételek teljesülnek.

Az ábra bemutatja, mit épít az oktatóanyagban.

![Rendelkezésreállási csoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag feltételezi, hogy alapvető ismereteket szerzett az SQL Server Mindig rendelkezésre állási csoportokról. Ha további információra van szüksége, [olvassa el a Mindig rendelkezésre állási csoportok (SQL Server) áttekintése című témakört.](https://msdn.microsoft.com/library/ff877884.aspx)

Az alábbi táblázat felsorolja azokat az előfeltételeket, amelyeket az oktatóanyag megkezdése előtt el kell végeznie:

|  |Követelmény |Leírás |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Két SQL-kiszolgáló | - Egy Azure rendelkezésre állási készlet <br/> - Egyetlen területen <br/> - A feladatátvevő fürtözési szolgáltatás telepítve |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Fájlmegosztás a tanúsító számára |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server szolgáltatásfiók | Tartományi fiók |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server Agent szolgáltatásfiók | Tartományi fiók |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Megnyitott tűzfalportok | - SQL Server: **1433** az alapértelmezett példányhoz <br/> - Adatbázis tükrözés végpont: **5022** vagy bármely rendelkezésre álló port <br/> - Rendelkezésre állási csoport terheléselosztó IP-cím állapot szonda: **59999** vagy bármely rendelkezésre álló port <br/> - Fürt mag terheléselosztó IP-cím állapot érzékelő: **58888** vagy bármely rendelkezésre álló port |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Feladatátvevő fürtözési szolgáltatás hozzáadása | Mindkét SQL-kiszolgáló igényli ezt a szolgáltatást |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Telepítési tartományfiók | - Helyi rendszergazda minden SQL Server <br/> - Az SQL Server sysadmin fix kiszolgálói szerepkörének tagja az SQL Server minden egyes példányához  |


Az oktatóanyag megkezdése előtt el kell végeznie a [Mindig rendelkezésre állási csoportok azure-beli virtuális gépekben való létrehozásának előfeltételeit.](virtual-machines-windows-portal-sql-availability-group-prereq.md) Ha ezek az előfeltételek már befejeződtek, ugorjon a [Fürt létrehozása](#CreateCluster)elemre.

  >[!NOTE]
  > Az oktatóanyagszámos lépése most már automatizálható az [Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) és az [Azure Quickstart Templates segítségével.](virtual-machines-windows-sql-availability-group-quickstart-template.md)


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>A fürt létrehozása

Az előfeltételek befejezése után az első lépés egy Windows Server feladatátvevő fürt létrehozása, amely két SQL Severs és egy tanúsító kiszolgálót tartalmaz.

1. RDP az első SQL Server kiszolgálóra, amely olyan tartományi fiókot használ, amely az SQL-kiszolgálók és a tanúsító kiszolgáló rendszergazdája.

   >[!TIP]
   >Ha követte az [előfeltételek et tartalmazó dokumentumot,](virtual-machines-windows-portal-sql-availability-group-prereq.md)létrehozott egy **CORP\Install**nevű fiókot. Használja ezt a fiókot.

2. A **Kiszolgálókezelő** irányítópultján válassza az **Eszközök**lehetőséget, majd kattintson a **Feladatátvevőfürt-kezelő parancsra.**
3. A bal oldali ablaktáblában kattintson a jobb gombbal a **Feladatátvevőfürt-kezelő**elemre, majd kattintson **a Fürt létrehozása parancsra.**
   ![Fürt létrehozása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. A Fürt létrehozása varázslóban hozzon létre egy egycsomópontos fürtöt az alábbi táblázat beállításaival rendelkező lapokon való átlépéssel:

   | Oldal | Beállítások |
   | --- | --- |
   | Előkészületek |Alapértelmezett értékek használata |
   | Kiszolgálók kiválasztása |Írja be az első SQL Server-nevet az **Enter kiszolgálónevébe,** és kattintson a **Hozzáadás gombra.** |
   | Érvényesítési figyelmeztetés |Válassza a **Nem lehetőséget. Ha a Tovább gombra kattintok, folytassa a fürt létrehozása című gombot.** |
   | Hozzáférési pont a fürt felügyeletéhez |Írjon be egy fürtnevet, például **SQLAGCluster1** a **Fürtnév**mezőbe.|
   | Megerősítés |Használja az alapértelmezett beállításokat, kivéve, ha tárolóhelyeket használ. Lásd a táblázat utáni megjegyzést. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>A Windows server feladatátvevő fürt IP-címének beállítása

  > [!NOTE]
  > Windows Server 2019 rendszerben a fürt a fürt **hálózatneve**helyett **elosztott kiszolgálónevet** hoz létre. Windows Server 2019 használata esetén hagyja ki az oktatóanyagban a fürt alapnevére hivatkozó lépéseket. Fürthálózati nevet a [PowerShell](virtual-machines-windows-portal-sql-create-failover-cluster.md#windows-server-2019)használatával hozhat létre. További információért tekintse át a [Feladatátvevő fürt: Fürthálózati objektum](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) blogot. 

1. A **Feladatátvevőfürt-kezelőben**görgessen le a **Fürtalapvető erőforrások elemre,** és bontsa ki a fürt részleteit. A **Név** és az **IP-cím** erőforrásoknak is meg kell **jelennie** a Sikertelen állapotban. Az IP-címerőforrás nem hozható online állapotba, mert a fürt ugyanazt az IP-címet kapta, mint maga a gép, ezért duplikált cím.

2. Kattintson a jobb gombbal a sikertelen **IP-cím** erőforrásra, majd kattintson a **Tulajdonságok parancsra.**

   ![Fürt tulajdonságai](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Válassza a **Statikus IP-cím lehetőséget,** és adjon meg egy elérhető címet ugyanabból az alhálózatból, mint a virtuális gépek.

4. A **Fürtalapvető erőforrások csoportban** kattintson a jobb gombbal a fürt nevére, és válassza az **Online állapot beállítása**parancsra. Ezután várjon, amíg mindkét erőforrás online állapotba kerül. Amikor a fürtnév-erőforrás online állapotba kerül, új AD-számítógépfiókkal frissíti a tartományvezérlő-kiszolgálót. Ezzel az AD-fiókkal később futtatható az Elérhetőségi csoport fürtözött szolgáltatása.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>A másik SQL Server hozzáadása a fürthöz

Adja hozzá a másik SQL Servert a fürthöz.

1. A böngészőfában kattintson a jobb gombbal a fürtre, és válassza **a Csomópont hozzáadása parancsra.**

    ![Csomópont hozzáadása a fürthöz](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. A **Csomópont hozzáadása varázslóban**kattintson a **Tovább**gombra. A **Kiszolgálók kiválasztása** lapon adja hozzá a második SQL Server t. Írja be a kiszolgáló nevét az **Enter kiszolgáló nevébe,** majd kattintson **a Hozzáadás gombra.** Ha elkészült, kattintson a **Tovább**gombra.

1. Az **Érvényesítési figyelmeztetés** lapon kattintson a **Nem** gombra (éles környezetben el kell végeznie az érvényesítési teszteket). Ezután kattintson a **Tovább gombra.**

8. Ha tárolóhelyeket használ, **törölje** a jelet a Fürthöz tartozó összes jogosult tároló **hozzáadása jelölőnégyzetből.**

   ![Csomópont-megerősítés hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Ha tárolóhelyeket használ, és nem törölje a jelet **az Összes jogosult tároló hozzáadása a fürthöz**jelölőnégyzetből, a Windows leválasztja a virtuális lemezeket a fürtözési folyamat során. Ennek eredményeképpen nem jelennek meg a Lemezkezelőben vagy az Intézőben, amíg a tárolóhelyeket el nem távolítja a fürtből, és a PowerShell használatával újra nem csatolták. A Tárolóhelyek több lemezt csoportosít a tárolókészletekbe. További információ: [Tárolóhelyek](https://technet.microsoft.com/library/hh831739).

1. Kattintson a **Tovább** gombra.

1. Kattintson a **Befejezés** gombra.

   A feladatátvevőfürt-kezelő azt mutatja, hogy a fürt új csomópont, és felsorolja azt a **csomópontok** tárolóban.

10. Jelentkezzen ki a távoli asztali munkamenetből.

### <a name="add-a-cluster-quorum-file-share"></a>Fürtkvórumfájl-megosztás hozzáadása

Ebben a példában a Windows-fürt fájlmegosztást használ a fürt kvórumának létrehozásához. Ez az oktatóanyag csomópont- és fájlmegosztási többségi kvórumot használ. További információ: [A feladatátvevő fürtök kvórumkonfigurációinak ismertetése](https://technet.microsoft.com/library/cc731739.aspx).

1. Csatlakozzon a tanúsító tagok fájlmegosztási kiszolgálójához egy távoli asztali munkamenettel.

1. A **Kiszolgálókezelőben**kattintson **az Eszközök**gombra. Nyissa meg **a Számítógép-kezelés ablakot.**

1. Kattintson **a Megosztott mappák gombra.**

1. Kattintson a jobb gombbal **a Megosztás elemre,** és kattintson az **Új megosztás... parancsra.**

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Megosztás létrehozása a **Megosztott mappa létrehozása varázsló** val.

1. A **Mappaelérési út on**kattintson a **Tallózás gombra,** és keresse meg vagy hozza létre a megosztott mappa elérési útját. Kattintson a **Tovább** gombra.

1. A **Név, a Leírás és** a Beállítások lapon ellenőrizze a megosztás nevét és elérési útját. Kattintson a **Tovább** gombra.

1. A **Megosztott mappa engedélyei** beállításhoz állítsa **be az Engedélyek testreszabása beállítást.** Kattintson **az Egyéni...** gombra.

1. Az **Engedélyek testreszabása**területen kattintson a **Hozzáadás...** gombra.

1. Győződjön meg arról, hogy a fürt létrehozásához használt fiók teljes hozzáféréssel rendelkezik.

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Kattintson az **OK** gombra.

1. A **Megosztott mappa engedélyei**területen kattintson a **Befejezés gombra.** Kattintson ismét **a Befejezés** gombra.  

1. Kijelentkezés a kiszolgálóról

### <a name="configure-cluster-quorum"></a>Fürtkvórum konfigurálása

Ezután állítsa be a fürt kvórumát.

1. Csatlakozzon az első fürtcsomóponthoz a távoli asztallal.

1. A **Feladatátvevőfürt-kezelőben**kattintson a jobb gombbal a fürtre, mutasson a **További műveletek**pontra, és kattintson a **Fürtkvórum beállításainak konfigurálása parancsra.**

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. A **Fürtkvórum konfigurálása varázslóban**kattintson a **Tovább**gombra.

1. A **Kvórum konfigurációja lehetőség kiválasztásában**válassza **a Kvórum tanúsító kiválasztása**lehetőséget, majd kattintson a **Tovább**gombra.

1. A **Kvórum tanúsító kiválasztása kor**kattintson a **Fájlmegosztási tanúsító beállítása gombra.**

   >[!TIP]
   >A Windows Server 2016 támogatja a felhőbeli tanúsítókat. Ha ilyen típusú tanút választ, nincs szüksége a konkretitra. További információ: [Deploy a cloud witness for a Failover Cluster](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Ez az oktatóanyag egy fájlmegosztási tanúsítót használ, amelyet a korábbi operációs rendszerek támogatnak.

1. A **Fájlmegosztás tanúsító konfigurálása**mezőbe írja be a létrehozott megosztás elérési útját. Kattintson a **Tovább** gombra.

1. Ellenőrizze a beállításokat a **Megerősítés**oldalon. Kattintson a **Tovább** gombra.

1. Kattintson a **Befejezés** gombra.

A fürt alapvető erőforrásai egy fájlmegosztási tanúsítal vannak konfigurálva.

## <a name="enable-availability-groups"></a>Rendelkezésre állási csoportok engedélyezése

Ezután engedélyezze az **AlwaysOn rendelkezésre állási csoportok** funkciót. Mindkét SQL-kiszolgálón végezze el ezeket a lépéseket.

1. A **kezdőképernyőről** indítsa el az **SQL Server Configuration Manager programot.**
2. A böngészőfában kattintson az **SQL Server Services**elemre, majd kattintson a jobb gombbal az SQL Server **(MSSQLSERVER)** szolgáltatásra, majd kattintson a **Tulajdonságok parancsra.**
3. Kattintson az **AlwaysOn Magas rendelkezésre állás** fülre, majd válassza **az AlwaysOn rendelkezésre állási csoportok engedélyezése**lehetőséget az alábbiak szerint:

    ![AlwaysOn rendelkezésre állási csoportok engedélyezése](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Kattintson az **Alkalmaz** gombra. Kattintson az **OK** gombra az előugró párbeszédpanelen.

5. Indítsa újra az SQL Server szolgáltatást.

Ismételje meg ezeket a lépéseket a másik SQL Server kiszolgálón.

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

## <a name="create-a-database-on-the-first-sql-server"></a>Adatbázis létrehozása az első SQL Server en

1. Indítsa el az RDP-fájlt az első SQL Server kiszolgálóra, amelynek tartományfiókja a fix kiszolgálói szerepkör tagja.
1. Nyissa meg az SQL Server Management Studio alkalmazást, és csatlakozzon az első SQL Server kiszolgálóhoz.
7. Az **Objektumkezelőben**kattintson a jobb gombbal **az Adatbázisok** elemre, és kattintson az Új adatbázis **parancsra.**
8. Az **Adatbázis neve mezőbe**írja be a **MyDB1**nevet, majd kattintson **az OK**gombra.

### <a name="create-a-backup-share"></a><a name="backupshare"></a>Biztonsági másolat megosztása

1. A **Kiszolgálókezelő**első SQL Server kiszolgálóján kattintson az **Eszközök**gombra. Nyissa meg **a Számítógép-kezelés ablakot.**

1. Kattintson **a Megosztott mappák gombra.**

1. Kattintson a jobb gombbal **a Megosztás elemre,** és kattintson az **Új megosztás... parancsra.**

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Megosztás létrehozása a **Megosztott mappa létrehozása varázsló** val.

1. A **Mappaelérési út**on kattintson a **Tallózás gombra,** és keresse meg vagy hozza létre az adatbázis biztonsági mentési megosztott mappájának elérési útját. Kattintson a **Tovább** gombra.

1. A **Név, a Leírás és** a Beállítások lapon ellenőrizze a megosztás nevét és elérési útját. Kattintson a **Tovább** gombra.

1. A **Megosztott mappa engedélyei** beállításhoz állítsa **be az Engedélyek testreszabása beállítást.** Kattintson **az Egyéni...** gombra.

1. Az **Engedélyek testreszabása**területen kattintson a **Hozzáadás...** gombra.

1. Győződjön meg arról, hogy az SQL Server és az SQL Server Agent szolgáltatásfiókok mindkét kiszolgálóteljes hozzáféréssel rendelkeznek.

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Kattintson az **OK** gombra.

1. A **Megosztott mappa engedélyei**területen kattintson a **Befejezés gombra.** Kattintson ismét **a Befejezés** gombra.  

### <a name="take-a-full-backup-of-the-database"></a>Az adatbázis teljes biztonsági mentése

A naplólánc inicializálásához biztonsági másolatot kell biztonsági másolatot kell adnia az új adatbázisról. Ha nem készít biztonsági másolatot az új adatbázisról, az nem vehető fel egy rendelkezésre állási csoportba.

1. Az **Objektumkezelőben**kattintson a jobb gombbal az adatbázisra, mutasson a **Feladatok pontra...** menüpontra, kattintson **a Biztonsági másolatok**pontra parancsra.

1. Az **OK gombra** kattintva teljes biztonsági mentést készíthet az alapértelmezett biztonsági mentési helyre.

## <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozása
Most már készen áll egy rendelkezésre állási csoport konfigurálására a következő lépésekkel:

* Hozzon létre egy adatbázist az első SQL Server kiszolgálón.
* Az adatbázis teljes biztonsági mentése és tranzakciós naplójának biztonsági mentése
* A teljes és naplós biztonsági mentések visszaállítása a második SQL Server kiszolgálóra a **NORECOVERY** beállítással
* A rendelkezésre állási csoport (**AG1**) létrehozása szinkron véglegesítéssel, automatikus feladatátvételsel és olvasható másodlagos replikákkal

### <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozása:

1. Távoli asztali munkamenetben az első SQL Server kiszolgálóra. Az **SSMS objektumkezelőjében** **kattintson a** jobb gombbal az AlwaysOn High Availability elemre, és kattintson az **Új rendelkezésre állási csoport varázsló parancsra.**

    ![Új rendelkezésre állási csoport indítása varázsló](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. A **Bevezetés** lapon kattintson a **Tovább**gombra. Az **Elérhetőségi csoport nevének megadása** lapon írja be az Elérhetőségi csoport nevét (például **AG1,** **az Elérhetőségi csoport neve mezőbe.** Kattintson a **Tovább** gombra.

    ![Új AG varázsló, a g név megadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Az Adatbázisok kiválasztása lapon jelölje ki az **adatbázist,** és kattintson a **Tovább**gombra.

   >[!NOTE]
   >Az adatbázis megfelel a rendelkezésre állási csoport előfeltételeinek, mert legalább egy teljes biztonsági mentést készített a tervezett elsődleges replika.

   ![Új AG varázsló, Adatbázisok kiválasztása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. A **Replikák megadása** lapon kattintson a **Replika hozzáadása gombra.**

   ![Új AG varázsló, Kópiák megadása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. Megjelenik **a Csatlakozás kiszolgálóhoz** párbeszédpanel. Írja be a második kiszolgáló nevét a **kiszolgáló nevébe.** Kattintson a **Csatlakozás** gombra.

   A **Replikák megadása** lapon most meg kell jelennie a **rendelkezésre állási replikák**listában felsorolt második kiszolgálónak. Konfigurálja a replikákat az alábbiak szerint.

   ![Új AG varázsló, Replikák megadása (kész)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Kattintson **a Végpontok** elemre az adatbázis tükrözési végpontjának megtekintéséhez ehhez az elérhetőségi csoporthoz. Ugyanazt a portot használja, amelyet a [tűzfalszabály adatbázis-tükrözési végpontok beállításához](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall)használt.

    ![Új AG varázsló, Kezdeti adatszinkronizálás kiválasztása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. A **Kezdeti adatszinkronizálás kiválasztása** lapon válassza a **Teljes** lehetőséget, és adja meg a megosztott hálózati helyet. A helyhez használja a [létrehozott biztonsági mentési megosztást.](#backupshare) A példában ez volt, ** \\ \\ \<\>Első\\SQL Server \Backup**. Kattintson a **Tovább** gombra.

   >[!NOTE]
   >A teljes szinkronizálás az sql server első példányán az adatbázis teljes biztonsági másolatát veszi alapul, és visszaállítja azt a második példányra. Nagy adatbázisok esetén a teljes szinkronizálás nem ajánlott, mert az hosszú időt vehet igénybe. Ezt az időt úgy csökkentheti, hogy manuálisan készít `NO RECOVERY`biztonsági másolatot az adatbázisról, és visszaállítja azt a segítségével. Ha az adatbázis már `NO RECOVERY` vissza lett állítva a második SQL Server kiszolgálón az elérhetőségi csoport konfigurálása előtt, válassza a **Csatlakozás csak**lehetőséget. Ha a rendelkezésre állási csoport konfigurálása után el szeretné készíteni a biztonsági mentést, válassza a **Kezdeti adatszinkronizálás kihagyása**lehetőséget.

    ![Új AG varázsló, Kezdeti adatszinkronizálás kiválasztása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Az **Érvényesítés** lapon kattintson a **Tovább gombra.** Ennek az oldalnak az alábbi képhez hasonlóan kell kinéznie:

    ![Új AG varázsló, érvényesítés](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Van egy figyelmeztetés a figyelő konfigurációját, mert még nem konfigurált a rendelkezésre állási csoport figyelője. Ezt a figyelmeztetést figyelmen kívül hagyhatja, mert az Azure virtuális gépeken az Azure load balancer létrehozása után hozza létre a figyelőt.

10. Az **Összegzés** lapon kattintson a **Befejezés**gombra, majd várjon, amíg a varázsló konfigurálja az új elérhetőségi csoportot. A **Folyamat** lapon a **További részletek** gombra kattintva megtekintheti a részletes folyamatot. A varázsló befejezése után ellenőrizze az **Eredmények** lapon, hogy a rendelkezésre állási csoport sikeresen létrejött-e.

     ![Új AG varázsló, eredmények](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Kattintson a **Bezárás** gombra a varázslóból való kilépéshez.

### <a name="check-the-availability-group"></a>A rendelkezésre állási csoport ellenőrzése

1. Az **Objektumkezelőben**bontsa ki **az AlwaysOn High Availability (Magas rendelkezésre állás)** elemet, majd a **Rendelkezésre állási csoportok csomópontot.** Ekkor megjelenik az új rendelkezésre állási csoport ebben a tárolóban. Kattintson a jobb gombbal az Elérhetőségi csoportra, és válassza az **Irányítópult megjelenítése parancsra.**

   ![AG irányítópult jának megjelenítése](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   Az **AlwaysOn irányítópultnak** ehhez hasonlóan kell kinéznie.

   ![AG irányítópult](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Láthatja a replikákat, az egyes replika feladatátvételi módját és a szinkronizálási állapotot.

2. A **Feladatátvevőfürt-kezelőben**kattintson a fürtre. Válassza a **Szerepkörök lehetőséget.** A használt rendelkezésre állási csoport neve a fürt egyik szerepköre. Ez a rendelkezésre állási csoport nem rendelkezik IP-címmel az ügyfélkapcsolatokhoz, mert nem konfigurált a figyelő. Az Azure-terheléselosztó létrehozása után konfigurálja a figyelőt.

   ![AG a feladatátvevői fürtkezelőben](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Ne próbálja meg átkerülni a feladatátvevőfürt-kezelő rendelkezésre állási csoportját. Minden feladatátvételi műveletet az **AlwaysOn irányítópulton** belül kell végrehajtani az SSMS-ben. További információt a [Feladatátvevőfürt-kezelő rendelkezésre állási csoportokkal való használatára vonatkozó korlátozások című témakörben talál.](https://msdn.microsoft.com/library/ff929171.aspx)
    >

Ezen a ponton rendelkezik egy rendelkezésre állási csoport replikák két példánya az SQL Server. Az elérhetőségi csoportot áthelyezheti a példányok között. A rendelkezésre állási csoporthoz még nem tud csatlakozni, mert nincs figyelője. Az Azure virtuális gépeken a figyelő terheléselosztót igényel. A következő lépés a terheléselosztó létrehozása az Azure-ban.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Azure-terheléselosztó létrehozása

Az Azure virtuális gépeken az SQL Server rendelkezésre állási csoportjához terheléselosztó szükséges. A terheléselosztó tartalmazza az elérhetőségi csoport figyelőinek és a Windows Server feladatátvételi fürtjének IP-címeit. Ez a szakasz összefoglalja, hogyan hozhat létre a terheléselosztó az Azure Portalon.

Az Azure Load Balancer lehet standard terheléselosztó vagy alapszintű terheléselosztó. A Standard Load Balancer több funkcióval rendelkezik, mint az alap terheléselosztó. Egy rendelkezésre állási csoport, a standard terheléselosztó szükséges, ha egy rendelkezésre állási zóna (egy rendelkezésre állási csoport helyett). A terheléselosztó típusok közötti különbségről a [Terheléselosztó Termékváltozat összehasonlítása című](../../../load-balancer/concepts-limitations.md#skus)témakörben talál részleteket.

1. Az Azure Portalon nyissa meg azt az erőforráscsoportot, ahol az SQL-kiszolgálók vannak, és kattintson a **+ Hozzáadás gombra.**
1. Keresse meg a **terheléselosztót.** Válassza ki a Microsoft által közzétett terheléselosztót.

   ![AG a feladatátvevői fürtkezelőben](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Kattintson **a Létrehozás gombra.**
1. Konfigurálja a következő paramétereket a terheléselosztóhoz.

   | Beállítás | Mező |
   | --- | --- |
   | **Név** |Használjon szöveges nevet a terheléselosztóhoz, például **sqlLB**. |
   | **Típus** |Belső |
   | **Virtuális hálózat** |Használja az Azure virtuális hálózat nevét. |
   | **Alhálózat** |Annak az alhálózatnak a nevét, amelyben a virtuális gép található.  |
   | **IP-cím hozzárendelése** |Statikus |
   | **IP-cím** |Használjon egy alhálózatból elérhető címet. Ezt a címet a rendelkezésre állási csoport figyelője használja. Ne feledje, hogy ez eltér a fürt IP-címétől.  |
   | **Előfizetés** |Használja ugyanazt az előfizetést, mint a virtuális gép. |
   | **Helyen** |Használja ugyanazt a helyet, mint a virtuális gép. |

   Az Azure Portal panel így kell kinéznie:

   ![Terheléselosztó létrehozása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Kattintson a **Létrehozás**gombra a terheléselosztó létrehozásához.

A terheléselosztó konfigurálásához létre kell hoznia egy háttérkészletet, egy mintavételt, és be kell állítania a terheléselosztási szabályokat. Végezze el ezeket az Azure Portalon.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Háttérkészlet hozzáadása az elérhető ségi csoport figyelőjének

1. Az Azure Portalon nyissa meg a rendelkezésre állási csoport. Előfordulhat, hogy frissítenie kell a nézetet az újonnan létrehozott terheléselosztó megtekintéséhez.

   ![Terheléselosztó keresése az erőforráscsoportban](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Kattintson a terheléselosztóra, a **Háttérkészletek**elemre, majd a **+Hozzáadás**gombra.

1. Írja be a háttérkészlet nevét.

1. Társítsa a háttérkészletet a virtuális gépeket tartalmazó rendelkezésre állási készlettel.

1. A **Célhálózati IP-konfigurációk**csoportban ellenőrizze a **VIRTUÁLIS GÉP** lehetőséget, és válassza ki a rendelkezésre állási csoport replikáit tároló virtuális gépeket. Ne adja meg a fájlmegosztási tanúsító kiszolgálót.

   >[!NOTE]
   >Ha mindkét virtuális gép nincs megadva, a kapcsolatok csak az elsődleges replika sikeres lesz.

1. A háttérkészlet létrehozásához kattintson az **OK** gombra.

### <a name="set-the-probe"></a>A szonda beállítása

1. Kattintson a terheléselosztóra, **az Állapotminta parancsra,** majd a **+Hozzáadás**gombra.

1. Állítsa be a figyelő állapotminta az alábbiak szerint:

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Név** | Szöveg | SQLAlwaysOnEndPointprobe |
   | **Protocol (Protokoll)** | Válassza a TCP beállítást | TCP |
   | **Port** | Bármilyen nem használt port | 59999 |
   | **Intervallum**  | A mintavételi kísérletek közötti idő másodpercben |5 |
   | **Nem kifogástalan állapot küszöbértéke** | A virtuális gép nem megfelelő állapotúnak minősülő egymást követő mintavételi hibák száma  | 2 |

1. Kattintson az **OK** gombra az állapotminta beállításához.

### <a name="set-the-load-balancing-rules"></a>A terheléselosztási szabályok beállítása

1. Kattintson a terheléselosztóra, a **Terheléselosztási szabályok**parancsra, majd a **+Hozzáadás**gombra.

1. Állítsa be a figyelő terheléselosztási szabályokat az alábbiak szerint.

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Név** | Szöveg | SQLAlwaysOnEndPointListener |
   | **Előtérbeli IP-cím** | Cím kiválasztása |Használja a terheléselosztó létrehozásakor létrehozott címet. |
   | **Protocol (Protokoll)** | Válassza a TCP beállítást |TCP |
   | **Port** | Használja a portot a rendelkezésre állási csoport figyelője | 1433 |
   | **Háttérport** | Ez a mező nem használatos, ha a lebegő IP-cím közvetlen kiszolgálói visszatérésre van beállítva | 1433 |
   | **Mintavétel** |A mintavételhez megadott név | SQLAlwaysOnEndPointprobe |
   | **Munkamenet-megőrzés** | Legördülő lista | **Nincs** |
   | **Üresjárati időtúllépés** | A TCP-kapcsolat nyitva tartásához percek | 4 |
   | **Lebegő IP-cím (közvetlen kiszolgálói visszatérés)** | |Engedélyezve |

   > [!WARNING]
   > A közvetlen kiszolgáló-visszatérés a létrehozás során van beállítva. A név nem módosítható.

1. Kattintson az **OK** gombra a figyelő terheléselosztási szabályainak beállításához.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>A Windows Server feladatátvevő fürt (WSFC) fürtmagjának IP-címének hozzáadása

A WSFC IP-címnek is a terheléselosztón kell lennie.

1. A portálon ugyanazon az Azure-terheléselosztón kattintson az **előtétes IP-konfigurációra,** majd a **+Hozzáadás**gombra. Használja a WSFC-hez a fürt alapvető erőforrásaiban konfigurált IP-címet. Állítsa be az IP-címet statikusként.

1. A terheléselosztón kattintson az **Állapotminta menügombra,** majd a **+Hozzáadás**gombra.

1. Állítsa be a WSFC-fürt alapvető IP-címállapot-érzékelőjének állapotát az alábbiak szerint:

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Név** | Szöveg | WSFCEndPointProbe |
   | **Protocol (Protokoll)** | Válassza a TCP beállítást | TCP |
   | **Port** | Bármilyen nem használt port | 58888 |
   | **Intervallum**  | A mintavételi kísérletek közötti idő másodpercben |5 |
   | **Nem kifogástalan állapot küszöbértéke** | A virtuális gép nem megfelelő állapotúnak minősülő egymást követő mintavételi hibák száma  | 2 |

1. Kattintson az **OK** gombra az állapotminta beállításához.

1. Állítsa be a terheléselosztási szabályokat. Kattintson **a Terheléselosztási szabályok**gombra, majd a **+Hozzáadás**gombra.

1. Állítsa be a fürt alapvető IP-címterhelés-elosztási szabályait az alábbiak szerint.

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Név** | Szöveg | WSFCEndPoint |
   | **Előtérbeli IP-cím** | Cím kiválasztása |Használja a WSFC IP-cím konfigurálásakor létrehozott címet. Ez eltér a figyelő IP-címétől |
   | **Protocol (Protokoll)** | Válassza a TCP beállítást |TCP |
   | **Port** | Használja a fürt IP-címének portját. Ez egy elérhető port, amely nem használható a figyelő mintavételi port. | 58888 |
   | **Háttérport** | Ez a mező nem használatos, ha a lebegő IP-cím közvetlen kiszolgálói visszatérésre van beállítva | 58888 |
   | **Mintavétel** |A mintavételhez megadott név | WSFCEndPointProbe |
   | **Munkamenet-megőrzés** | Legördülő lista | **Nincs** |
   | **Üresjárati időtúllépés** | A TCP-kapcsolat nyitva tartásához percek | 4 |
   | **Lebegő IP-cím (közvetlen kiszolgálói visszatérés)** | |Engedélyezve |

   > [!WARNING]
   > A közvetlen kiszolgáló-visszatérés a létrehozás során van beállítva. A név nem módosítható.

1. A terheléselosztási szabályok beállításához kattintson az **OK** gombra.

## <a name="configure-the-listener"></a><a name="configure-listener"></a>A figyelő konfigurálása

A következő teendő egy rendelkezésre állási csoport figyelőjének konfigurálása a feladatátvevő fürtön.

> [!NOTE]
> Ez az oktatóanyag bemutatja, hogyan hozhat létre egyetlen figyelőt – egy ILB IP-címmel. Ha egy vagy több IP-címet használó figyelőt szeretne létrehozni, olvassa el a [Rendelkezésre álláscsoport figyelőjének és terheléselosztójának létrehozása című témakört | Az Azure-ban.](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Figyelőport beállítása

Az SQL Server Management Studio-ban állítsa be a figyelőportot.

1. Indítsa el az SQL Server Management Studio alkalmazást, és csatlakozzon az elsődleges kópiához.

1. Keresse meg **az AlwaysOn magas rendelkezésre állási** | **rendelkezésre állási csoportok** | **rendelkezésre állási csoportfigyelőit.**

1. Most látnia kell a feladatátvevői fürtkezelőben létrehozott figyelőnevet. Kattintson a jobb gombbal a figyelő nevére, és válassza a **Tulajdonságok parancsot.**

1. A **Port** mezőben adja meg az Elérhetőségi csoport figyelőjének portszámát. Az 1433 az alapértelmezett, majd kattintson az **OK gombra.**

Most már rendelkezik egy SQL Server-rendelkezésre állási csoporttal az Azure-beli virtuális gépeken, amelyek Erőforrás-kezelő módban futnak.

## <a name="test-connection-to-listener"></a>A kapcsolat tesztelése a figyelővel

A kapcsolat tesztelése:

1. RDP egy SQL Server, amely ugyanabban a virtuális hálózatban, de nem a replika. Használhatja a fürt másik SQL Server kiszolgálóját.

1. A kapcsolat teszteléséhez használja az **sqlcmd** segédprogramot. A következő parancsfájl például **sqlcmd** kapcsolatot hoz létre az elsődleges kópiával a figyelőn keresztül a Windows-hitelesítéssel:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Ha a figyelő nem az alapértelmezett portot (1433) használja, adja meg a portot a kapcsolati karakterláncban. Például a következő sqlcmd parancs csatlakozik egy figyelőhöz az 1435-ös porton:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

Az SQLCMD-kapcsolat automatikusan csatlakozik az elsődleges replikát az SQL Server bármelyik példányához.

> [!TIP]
> Győződjön meg arról, hogy a megadott port mindkét SQL-kiszolgáló tűzfalán meg van nyitva. Mindkét kiszolgálónak bejövő szabályra van szüksége a használt TCP-porthoz. További információt a [Tűzfalszabály hozzáadása és szerkesztése](https://technet.microsoft.com/library/cc753558.aspx)című témakörben talál.

## <a name="next-steps"></a>További lépések

- [Ip-cím hozzáadása egy második rendelkezésre állási csoport terheléselosztóhoz.](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP)
