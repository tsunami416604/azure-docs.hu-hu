---
title: "Az SQL Server rendelkezésre állási csoportok – az Azure Virtual Machines - oktatóanyag |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan hozhat létre egy SQL Server mindig a rendelkezésre állási csoport Azure virtuális gépeken."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/09/2017
ms.author: mikeray
ms.openlocfilehash: 228ca9ca5fddc493d27bfd6a40df5ee7306d6aa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-always-on-availability-group-in-azure-vm-manually"></a>Konfigurálás mindig a rendelkezésre állási csoport az Azure virtuális gép manuálisan

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy SQL Server mindig a rendelkezésre állási csoport Azure virtuális gépeken. A teljes útmutató egy rendelkezésre állási csoportban két SQL-kiszolgálón az adatbázis-replikát hoz létre.

**Becsült idő**: végezze el az előfeltételek teljesülése után körülbelül 30 percet vesz igénybe.

A diagram azt ábrázolja, az oktatóanyag felépítéséhez.

![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag feltételezi, hogy rendelkezik-e az SQL Server Always On rendelkezésre állási csoportokkal kapcsolatos alapvető ismeretekkel. Ha további tájékoztatásra van szüksége, tekintse meg [az mindig a rendelkezésre állási csoportok áttekintése (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).

A következő táblázat felsorolja az előfeltételeket, amelyeket végre kell hajtania az oktatóanyag elindítása előtt:

|  |Követelmény |Leírás |
|----- |----- |----- |
|![Négyzetes](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Két SQL-kiszolgáló | -Az Azure rendelkezésre állási csoportok <br/> -Egyetlen tartományban <br/> -A Feladatátvételi fürtszolgáltatás telepítése |
|![Négyzetes](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | A fürt tanúsító fájlmegosztás |  
|![Négyzetes](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server-szolgáltatásfiók | Tartományi fiók |
|![Négyzetes](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server Agent szolgáltatásfiók használatával | Tartományi fiók |  
|![Négyzetes](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Tűzfal portjainak megnyitása | -SQL kiszolgáló: **1433** az alapértelmezett példány esetén <br/> -Adatbázis-tükrözési végpontját: **5022** vagy minden elérhető port <br/> -Azure terheléselosztói mintavétel: **59999** vagy minden elérhető port |
|![Négyzetes](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Adja hozzá a Feladatátvételi fürtszolgáltatás | Mindkét SQL Server szükség erre a szolgáltatásra |
|![Négyzetes](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Telepítési tartományi fiók | -Minden egyes SQL Server helyi rendszergazdája <br/> – SQL Server SysAdmin (rendszergazda) rögzített kiszolgálói szerepkör az SQL Server minden példányának tagja  |


Az oktatóanyag elkezdéséhez kell [végezze el az Azure virtuális gépek létrehozását a Always On rendelkezésre állási csoportok használatának előfeltételei](virtual-machines-windows-portal-sql-availability-group-prereq.md). Ha az Előfeltételek már megadta, de is ugorhat való [fürt létrehozása](#CreateCluster).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
##A fürt létrehozása

Az Előfeltételek elvégzése után az első lépés a Windows Server feladatátvevő fürt, amely tartalmazza a két SQL-kiszolgálója és egy tanúsító kiszolgáló létrehozásához.  

1. Az első SQL Server egy tartományi fiókkal, amely az SQL Server-kiszolgálók és a tanúsító kiszolgálói rendszergazda RDP.

   >[!TIP]
   >Ha követte a [Előfeltételek dokumentumát](virtual-machines-windows-portal-sql-availability-group-prereq.md), nevű létrehozott **CORP\Install**. Használja ezt a fiókot.

2. Az a **Kiszolgálókezelő** jelölje be az irányítópult **eszközök**, és kattintson a **Feladatátvevőfürt-kezelő**.
3. A bal oldali ablaktáblán kattintson jobb gombbal **Feladatátvevőfürt-kezelő**, és kattintson a **hozzon létre egy fürtöt**.
   ![Fürt létrehozása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. A fürt létrehozása varázsló hozzon létre egy egy csomópontos fürtre lépéseit az alábbi táblázatban a beállításokkal a lapok végrehajtani:

   | Oldal | Beállítások |
   | --- | --- |
   | Előkészületek |Alapértelmezések használata |
   | Kiszolgálók kiválasztása |Írja be az első SQL-kiszolgáló neve a **kiszolgáló nevének megadása** kattintson **Hozzáadás**. |
   | Érvényesítési figyelmeztetés |Válassza ki **szám I nem igényel Microsoft-támogatást ehhez a fürthöz, és ezért nem szeretné, hogy az érvényesítési tesztek futtatásához. A Tovább gombra kattintva, továbbra is a fürt létrehozása**. |
   | Hozzáférési pont a fürt felügyeletéhez |Írja be a fürt nevét, például **SQLAGCluster1** a **fürtnév**.|
   | Megerősítés |Használhatja az alapértelmezett értékeket, kivéve, ha a tárolóhelyek használata. Lásd a táblázat utáni megjegyzést. |

### <a name="set-the-cluster-ip-address"></a>A fürt IP-cím beállítása

1. A **Feladatátvevőfürt-kezelő**, görgessen le a **fürt alapvető erőforrásai** , és bontsa ki a fürt adatait. Mindkét kell megjelennie a **neve** és a **IP-cím** erőforrásokat a **sikertelen** állapotát. Az IP-cím erőforrás nem állítható online állapotba, mert a fürt hozzá van rendelve a gép magát a azonos IP-címre, ezért a ismétlődő címet.

2. Kattintson a jobb gombbal az a sikertelen **IP-cím** erőforrás, és kattintson **tulajdonságok**.

   ![Fürt tulajdonságai](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Válassza ki **statikus IP-cím** , és adja meg az alhálózatból az SQL Server esetén a cím szövegmezőben egy címet. Kattintson a **OK**.
4. Az a **fürt alapvető erőforrásai** szakaszt, kattintson a jobb gombbal a fürt nevét, és kattintson **online állapotba hozás**. Ezután Várjon, amíg az mindkét erőforrás online. A fürt hálózatnév-erőforrás online állapotba kerül, amikor új AD-számítógépfiók frissíti a tartományvezérlő kiszolgálóhoz. Az AD-fiókot használja a rendelkezésre állási csoport fürtözött szolgáltatás később futtatásához.

### <a name="addNode"></a>Az SQL Server hozzáadása fürthöz

A más SQL-kiszolgáló hozzáadása a fürthöz.

1. A böngésző konzolfáján kattintson jobb gombbal a fürtre, és kattintson **csomópont hozzáadása**.

    ![Csomópont hozzáadása a fürthöz](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. Az a **csomópont hozzáadása varázsló**, kattintson a **következő**. Az a **kiszolgálók kiválasztása** lapon adja hozzá a második SQL-kiszolgálót. Írja be a kiszolgáló nevére a **kiszolgáló nevének megadása** majd **Hozzáadás**. Amikor elkészült, kattintson a **következő**.

1. Az a **érvényesítési figyelmeztetés** kattintson **nem** (éles forgatókönyv esetében végre kell hajtania az ellenőrző teszteket). Ezután kattintson a **Tovább** gombra.

8. Az a **megerősítő** lapon, a tárolóhelyek használata, törölje a jelet a jelölőnégyzetből, címkével **minden megfelelő tároló felvétele a fürt.**

   ![Vegye fel a csomópont megerősítése](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Ha a tárolóhelyek használ, és nem, törölje a jelet **minden megfelelő tároló felvétele a fürt**, Windows leválasztja a virtuális lemezek a csoportosítási eljárás során. Ennek eredményeképpen csak akkor jelennek meg a Lemezkezelés vagy Explorer a tárolóhelyek el lesznek távolítva a fürthöz, és objektumkörnyezetben a PowerShell használatával. A tárolóhelyek több lemezére való felsorolását tartalmazza. További információkért lásd: [tárolóhelyek](https://technet.microsoft.com/library/hh831739).

1. Kattintson a **Tovább** gombra.

1. Kattintson a **Befejezés** gombra.

   Feladatátvevőfürt-kezelő jeleníti meg, hogy a fürt új csomópontjának rendelkezik, és megjeleníti azt a a **csomópontok** tároló.

10. Jelentkezzen ki a távoli asztali munkamenetben.

### <a name="add-a-cluster-quorum-file-share"></a>A fürt kvórum fájlmegosztás hozzáadása

Ebben a példában a Windows-fürt létrehozása a fürt kvórum fájlmegosztást használ. Ez az oktatóanyag egy csomópont- és fájlmegosztástöbbség kvórum használja. További információkért lásd: [a feladatátvevő fürtök kvórumkonfigurációinak ismertetése](http://technet.microsoft.com/library/cc731739.aspx).

1. A fájlmegosztás tanúsító tag kiszolgálója egy távoli asztali munkamenet-csatlakozni.

1. A **Kiszolgálókezelő**, kattintson a **eszközök**. Nyissa meg **számítógép-kezelés**.

1. Kattintson a **megosztott mappák**.

1. Kattintson a jobb gombbal **megosztások**, és kattintson a **új megosztás...** .

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Használjon **megosztott mappa létrehozása varázsló** megosztás létrehozásához.

1. A **mappa elérési útja**, kattintson a **Tallózás** , és keresse meg, vagy hozzon létre egy megosztott mappa elérési útját. Kattintson a **Tovább** gombra.

1. A **nevét, leírását és beállítások** ellenőrizze a megosztás neve és elérési útját. Kattintson a **Tovább** gombra.

1. A **megosztott mappákra vonatkozó engedélyek** beállítása **engedélyek testreszabását**. Kattintson a **egyéni...** .

1. A **engedélyek testreszabása**, kattintson a **hozzáadása...** .

1. Győződjön meg arról, hogy a fürt létrehozásához használt fiók teljes körű vezérléssel rendelkezik.

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Kattintson az **OK** gombra.

1. A **megosztott mappákra vonatkozó engedélyek**, kattintson a **Befejezés**. Kattintson a **Befejezés** újra.  

1. Jelentkezzen ki a kiszolgáló

### <a name="configure-cluster-quorum"></a>A fürtkvórum konfigurálása

Ezután állítsa be a fürt kvórumát.

1. Az első fürtcsomópontra, a távoli asztalról csatlakozni.

1. A **Feladatátvevőfürt-kezelő**, kattintson a jobb gombbal a fürtre, majd a **további műveletek**, és kattintson a **fürt kvórumbeállításainak megadása...** .

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. A **fürtkvórum beállítása varázslót**, kattintson a **következő**.

1. A **kvórumbeállítások kijelölése**, válassza a **a kvórum tanúsítójának kijelölése**, és kattintson a **következő**.

1. A **kvórum Tanúsítójának kijelölése**, kattintson a **konfigurálása egy tanúsító fájlmegosztást**.

   >[!TIP]
   >Windows Server 2016 felhő tanúsító támogatja. Ha úgy dönt, hogy az ilyen típusú tanúsító, nem kell egy fájl tanúsító fájlmegosztás. További információkért lásd: [központi telepítése a feladatátvevő fürt tanúsító felhő](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Ez az oktatóanyag egy tanúsító fájlmegosztást, amely korábbi operációs rendszerek által támogatott használja.

1. A **tanúsító fájlmegosztás**, adja meg a létrehozott megosztás elérési útvonalát. Kattintson a **Tovább** gombra.

1. Ellenőrizze, hogy a beállítások **megerősítő**. Kattintson a **Tovább** gombra.

1. Kattintson a **Befejezés** gombra.

Egy tanúsító fájlmegosztást a fürt alapvető erőforrásai vannak konfigurálva.

## <a name="enable-availability-groups"></a>Rendelkezésre állási csoportok engedélyezése

Következő lépésként engedélyezze a **AlwaysOn rendelkezésre állási csoportok** szolgáltatás. Hajtsa végre ezeket a lépéseket, a két SQL-kiszolgálón.

1. Az a **Start** indítsa el a jobb **SQL Server Configuration Manager**.
2. A böngésző konzolfáján kattintson **SQL Server Services**, kattintson a jobb gombbal a **SQL Server (MSSQLSERVER)** szolgáltatást, és kattintson a **tulajdonságok**.
3. Kattintson a **AlwaysOn magas rendelkezésre állású** lapra, majd válasszon **engedélyezze az AlwaysOn rendelkezésre állási csoportokat**, az alábbiak szerint:

    ![Engedélyezze az AlwaysOn rendelkezésre állási csoportok](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Kattintson az **Alkalmaz** gombra. Kattintson a **OK** az előugró párbeszédpanelen.

5. Indítsa újra az SQL Server szolgáltatást.

Ismételje ezeket a lépéseket az SQL Server.

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

## <a name="create-a-database-on-the-first-sql-server"></a>Az első SQL Server-adatbázis létrehozása

1. Nyissa meg az RDP-fájlt, és az első SQL-kiszolgáló egy tartományi fiókkal, amely a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagja.
1. Nyissa meg az SQL Server Management Studio eszközt, és az első SQL-kiszolgáló csatlakozzon.
7. A **Object Explorer**, kattintson a jobb gombbal **adatbázisok** kattintson **új adatbázis**.
8. A **adatbázisnév**, típus **MyDB1**, majd kattintson a **OK**.

### <a name="backupshare"></a>Hozzon létre egy biztonsági mentési megosztást

1. Az első, az SQL Server **Kiszolgálókezelő**, kattintson a **eszközök**. Nyissa meg **számítógép-kezelés**.

1. Kattintson a **megosztott mappák**.

1. Kattintson a jobb gombbal **megosztások**, és kattintson a **új megosztás...** .

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Használjon **megosztott mappa létrehozása varázsló** megosztás létrehozásához.

1. A **mappa elérési útja**, kattintson a **Tallózás** , és keresse meg, vagy hozzon létre egy adatbázis biztonsági mentési tartalmazó megosztott mappa elérési útját. Kattintson a **Tovább** gombra.

1. A **nevét, leírását és beállítások** ellenőrizze a megosztás neve és elérési útját. Kattintson a **Tovább** gombra.

1. A **megosztott mappákra vonatkozó engedélyek** beállítása **engedélyek testreszabását**. Kattintson a **egyéni...** .

1. A **engedélyek testreszabása**, kattintson a **hozzáadása...** .

1. Győződjön meg arról, hogy az SQL Server és SQL Server Agent szolgáltatásfiókokat mindkét kiszolgáló teljes hozzáféréssel rendelkeznek.

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Kattintson az **OK** gombra.

1. A **megosztott mappákra vonatkozó engedélyek**, kattintson a **Befejezés**. Kattintson a **Befejezés** újra.  

### <a name="take-a-full-backup-of-the-database"></a>A teljes adatbázis biztonsági másolatok készítéséhez

Készítsen biztonsági másolatot az új adatbázis naplóláncában inicializálni kell. Ha nem ad meg az új adatbázis biztonsági másolata, akkor egy rendelkezésre állási csoportban nem szerepelhet.

1. A **Object Explorer**, kattintson a jobb gombbal az adatbázist, majd a **feladatok...** , kattintson a **készítsen biztonsági másolatot**.

1. Kattintson a **OK** egy teljes biztonsági mentés mit kell tennie az alapértelmezett helyre.

## <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozása
Most már készen áll az alábbi lépéseket követve rendelkezésre állási csoport konfigurálása:

* Az első SQL Server adatbázis létrehozása.
* Teljes biztonsági mentés és a tranzakciós napló biztonsági mentését az adatbázis is igénybe vehet
* Állítsa vissza a teljes és a biztonsági mentések bejelentkezni a második SQL Server a **NORECOVERY** beállítás
* A rendelkezésre állási csoport létrehozása (**AG1**) szinkron véglegesítésre, automatikus feladatátvétel és olvasható másodlagos másodpéldányokra

### <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozására:

1. A távoli asztali munkamenetgazda és az első SQL-kiszolgáló. A **Object Explorer** szolgáltatáshoz az ssms, kattintson a jobb gombbal **AlwaysOn magas rendelkezésre állású** kattintson **új rendelkezésre állási csoport varázsló**.

    ![Új rendelkezésre állási csoport varázsló indítása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Az a **bemutatása** kattintson **következő**. Az a **adja meg a rendelkezésre állási csoport nevének** írja be például a rendelkezésre állási csoport nevét **AG1**, a **rendelkezésre állási csoport nevének**. Kattintson a **Tovább** gombra.

    ![Új rendelkezésre állási csoport varázsló, adja meg a rendelkezésre állási csoport nevét](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Az a **válasszon adatbázisok** lapon válassza ki az adatbázist, majd kattintson **következő**.

   >[!NOTE]
   >Az adatbázis egy rendelkezésre állási csoport előfeltételeit megfelel, mivel legalább egy teljes biztonsági mentés elvégezte a kívánt elsődleges replikán.

   ![Új rendelkezésre állási csoport varázsló, válassza ki az adatbázisokat](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Az a **replikák megadása** kattintson **adja hozzá a replika**.

   ![Új rendelkezésre állási csoport varázsló, adja meg a replikák](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. A **kapcsolódás a kiszolgálóhoz** párbeszédpanel jelenik meg. Írja be a nevét, a második kiszolgáló **kiszolgálónév**. Kattintson a **Connect** (Csatlakozás) gombra.

   Vissza a **replikák megadása** lapon meg kell jelennie a második kiszolgáló felsorolt **rendelkezésre állási másodpéldányok**. Az alábbiak szerint konfigurálhatja a replikákat.

   ![Új rendelkezésre állási csoport varázsló, adja meg a replikák (kész)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Kattintson a **végpontok** az adatbázis-tükrözési végpontját a rendelkezésre állási csoport megjelenítéséhez. Használhatja ugyanazt a portot, amelyet használt, ha úgy állítja be a [adatbázis-tükrözési végpont tűzfalszabályt](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Új rendelkezésre állási csoport varázsló kezdeti adatszinkronizálás kiválasztása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. Az a **kezdeti adatszinkronizálás kiválasztása** lapon, hogy melyik **teljes** , és adja meg egy megosztott hálózati helyre. A helyre, használja a [létrehozott biztonsági másolatokat tároló megosztáson](#backupshare). Igen, a példában **\\\\\<első SQL-kiszolgáló\>\Backup\**. Kattintson a **Tovább** gombra.

   >[!NOTE]
   >Teljes szinkronizálás teljes mentést készít az adatbázist az SQL Server első példányát, és annak visszaállítására a második példány. Nagy adatbázisok esetén a teljes szinkronizálás nem ajánlott, mert hosszú ideig is eltarthat. Manuálisan az adatbázis biztonsági másolatát, és állítja vissza a megadott idő csökkentése érdekében `NO RECOVERY`. Ha az adatbázis már vissza a `NO RECOVERY` SQL-kiszolgálón a második a rendelkezésre állási csoport konfigurálása előtt, válassza ki a **csak összekapcsolás**. Ha a biztonsági mentés venni a rendelkezésre állási csoport konfigurálása után, válassza a kívánt **kezdeti adatszinkronizálás kihagyása**.

    ![Új rendelkezésre állási csoport varázsló kezdeti adatszinkronizálás kiválasztása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Az a **érvényesítési** kattintson **következő**. Ezen a lapon az alábbi képen hasonlóan kell kinéznie:

    ![Új rendelkezésre állási csoport varázsló, érvényesítése](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >A figyelő konfigurációjába vonatkozó figyelmeztetés van, mert nem állított be egy rendelkezésre állási csoport figyelőjét. Mivel az Azure virtuális gépeken hoz létre a figyelő az Azure load balancer létrehozása után, kihagyhatja ezt a figyelmeztetést.

10. Az a **összegzés** kattintson **Befejezés**, majd várjon, amíg a varázsló konfigurálja az új rendelkezésre állási csoport. Az a **folyamatban** lap, kattintson **további részleteket** részletes előrehaladásának megtekintéséhez. A varázsló befejezése után vizsgálja meg a **eredmények** lapon győződjön meg arról, hogy a rendelkezésre állási csoport sikeresen létrejött.

     ![Új rendelkezésre állási csoport varázsló, annak az eredménye](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Kattintson a **Bezárás** a varázslóból való kilépéshez.

### <a name="check-the-availability-group"></a>A rendelkezésre állási csoport ellenőrzése

1. A **Object Explorer**, bontsa ki a **AlwaysOn magas rendelkezésre állású**, majd bontsa ki a **rendelkezésre állási csoportok**. Meg kell jelennie az új rendelkezésre állási csoport ebben a tárolóban. Kattintson a jobb gombbal a rendelkezésre állási csoportot, és kattintson a **irányítópult megjelenítése**.

   ![Rendelkezésre állási csoport Irányítópult megjelenítése](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   A **AlwaysOn irányítópult** ehhez hasonlóan kell kinéznie.

   ![Rendelkezésre állási csoport Irányítópult](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   A replikákat, a feladatátvételi módot minden replika-és a szinkronizálás állapotát tekintheti meg.

2. A **Feladatátvevőfürt-kezelő**, kattintson arra a fürtre. Válassza ki **szerepkörök**. A rendelkezésre állási csoport nevét a szerepkört a fürtön. Rendelkezésre állási csoport nem rendelkezik ügyfél-kommunikációhoz, IP-címet, mivel egy figyelő nincs konfigurálva. A figyelő konfigurál egy Azure terheléselosztó létrehozása után.

   ![A Feladatátvevőfürt-kezelő AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Ne próbálja meg a rendelkezésre állási csoport a Feladatátvevőfürt-kezelőből feladatátvételt. Az összes feladatátvételi műveleteket kell elvégezni belül **AlwaysOn irányítópult** szolgáltatáshoz az ssms. További információkért lásd: [korlátozások használja a Feladatátvevőfürt-kezelőjén rendelkezésre állási csoportokkal](https://msdn.microsoft.com/library/ff929171.aspx).
    >

Ezen a ponton rendelkezik egy rendelkezésre állási csoporthoz az SQL Server két példánya replikával. Áthelyezheti a rendelkezésre állási csoport példányai között. Nem csatlakozik a rendelkezésre állási csoport még mivel nem rendelkezik egy figyelő. Az Azure virtuális gépeken a figyelő egy adott terheléselosztóhoz szükséges. A következő lépés, ha a terheléselosztó az Azure-ban.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Hozzon létre egy Azure terheléselosztó

Azure virtuális gépeken futó SQL Server rendelkezésre állási csoport terheléselosztó szükséges. A terheléselosztó a rendelkezésre állási csoport figyelője az IP-címet tartalmazza. Ez a szakasz a terheléselosztó létrehozása az Azure portálon foglalja össze.

1. Az Azure-portálon lépjen az erőforráscsoport, ahol az SQL-kiszolgálók, és kattintson a **+ Hozzáadás**.
2. Keresse meg **terheléselosztó**. Válassza ki a Microsoft által kiadott terheléselosztó.

   ![A Feladatátvevőfürt-kezelő AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1.  Kattintson a **Create** (Létrehozás) gombra.
3. Adja meg a következő paramétereit a terheléselosztóhoz.

   | Beállítás | Mező |
   | --- | --- |
   | **Name (Név)** |A load balancer szöveg nevét használja például **sqlLB**. |
   | **Típus** |Belső |
   | **Virtuális hálózat** |Az Azure virtuális hálózat nevét használja. |
   | **Alhálózat** |Az alhálózathoz, amelyhez a virtuális gép nevét használja.  |
   | **IP-cím hozzárendelése** |Statikus |
   | **IP-cím** |Egy alhálózatból címet használja. |
   | **Előfizetés** |Használja a virtuális gép ugyanahhoz az előfizetéshez. |
   | **Hely** |A virtuális gép ugyanazon a helyen használható. |

   Az Azure portál panel kell kinéznie:

   ![Terheléselosztó létrehozása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Kattintson a **létrehozása**, a terheléselosztó létrehozása.

A terheléselosztó konfigurálásához szüksége a háttérkészlet, a mintavétel létrehozása és a terheléselosztási szabályok beállítása. Ezek az Azure portálon teheti meg.

### <a name="add-backend-pool"></a>Háttérkészlet hozzáadása

1. Az Azure-portálon lépjen a következő rendelkezésre állási csoporthoz. Frissítse a nézetet, hogy az újonnan létrehozott terheléselosztó módosítania.

   ![Terheléselosztó erőforráscsoportban található](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Kattintson a terheléselosztóhoz, majd **háttérkészletek**, és kattintson a **+ Hozzáadás**. Az alábbiak szerint állíthatja háttérkészlet:

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Name (Név)** | Adjon meg egy szöveges nevet | SQLLBBE
   | **Társított** | Válassza ki a listából | Rendelkezésre állási csoport
   | **A rendelkezésre állási csoport** | Adjon nevet a rendelkezésre állási csoport, amelyek az SQL Server virtuális gépen | sqlAvailabilitySet |
   | **Virtuális gépek** |A két Azure SQL Server virtuális gép neve | SQL Server-0, SQL Server-1

1. Írja be a háttérkészlethez nevét.

1. Kattintson a **+ adja hozzá a virtuális gépek**.

1. A rendelkezésre állási készletbe adja meg a rendelkezésre állási csoport, hogy az SQL Server-kiszolgálók szerepelnek.

1. A virtuális gépek tartalmazzák mind az SQL Server-kiszolgálók. A tanúsító fájlmegosztás kiszolgálója nem tartalmaznak.

1. Kattintson a **OK** háttérkészlet létrehozásához.

### <a name="set-the-probe"></a>A mintavételi beállítása

1. Kattintson a terheléselosztóhoz, majd **állapot-mintavételi csomagjai**, és kattintson a **+ Hozzáadás**.

1. Állítsa be a állapotmintáihoz az alábbiak szerint:

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Name (Név)** | Szöveg | SQLAlwaysOnEndPointProbe |
   | **Protocol (Protokoll)** | Válassza ki a TCP | TCP |
   | **Port** | Minden nem használt portot | 59999 |
   | **Időköz**  | Másodpercben mintavételi kísérletek közötti idő |5 |
   | **Sérült küszöbérték** | Egymást követő sikertelen mintavételek bekövetkező akkor, ha a nem megfelelő virtuális gépek száma  | 2 |

1. Kattintson a **OK** beállítása a állapotmintáihoz.

### <a name="set-the-load-balancing-rules"></a>A terheléselosztási szabályok beállítása

1. Kattintson a terheléselosztóhoz, majd **terheléselosztási szabályok**, és kattintson a **+ Hozzáadás**.

1. A terheléselosztási szabályok az alábbiak szerint állítsa be.
   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Name (Név)** | Szöveg | SQLAlwaysOnEndPointListener |
   | **Előtérbeli IP-cím** | Válasszon címet |A load balancer létrehozásakor létrehozott címet használja. |
   | **Protocol (Protokoll)** | Válassza ki a TCP |TCP |
   | **Port** | A port az SQL Server-példány használata | 1433 |
   | **Háttér-Port** | Ha a fix IP-Címek értéke a közvetlen kiszolgálói visszatérési nem használja ezt a mezőt | 1433 |
   | **Hálózatfigyelő** |A mintavétel a megadott név | SQLAlwaysOnEndPointProbe |
   | **Munkamenet megőrzését** | Legördülő lista | **Egyik sem** |
   | **Üresjárati időtúllépés** | Tartsa nyitva, a TCP-kapcsolat perc | 4 |
   | **Lebegőpontos IP (közvetlen kiszolgálói válasz)** | |Engedélyezve |

   > [!WARNING]
   > A közvetlen kiszolgálói válasz érték létrehozása során. Nem módosítható.

1. Kattintson a **OK** a terheléselosztási szabályok beállítása.

## <a name="configure-listener"></a>A figyelő konfigurálása

A következő lépés egy rendelkezésre állási csoport figyelőjének beállítása a feladatátvevő fürtön.

> [!NOTE]
> Ez az oktatóanyag bemutatja, hogyan hozható létre egyetlen figyelő - egy ILB IP-címmel. Egy vagy több IP-címeket használ egy vagy több figyelői létrehozásához lásd: [figyelő rendelkezésre állási csoport létrehozása és a terheléselosztó |} Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Set figyelő port

Az SQL Server Management Studio a figyelő port beállítása.

1. Indítsa el az SQL Server Management Studio eszközt, és kapcsolódjon az elsődleges másodpéldány.

1. Navigáljon a **AlwaysOn magas rendelkezésre állás** | **rendelkezésre állási csoportok** | **rendelkezésre állási csoport figyelői**.

1. Meg kell jelennie a figyelő nevét, amelyet a Feladatátvevőfürt-kezelőt hozott létre. Kattintson a jobb gombbal a figyelő nevét, és kattintson a **tulajdonságok**.

1. Az a **Port** mezőben adja meg a portszámot a korábban használt $EndpointPort használatával a rendelkezésre állási csoport figyelője (az alapértelmezett 1433-as volt az), majd kattintson a **OK**.

Most már rendelkezik egy SQL Server rendelkezésre állási csoport erőforrás-kezelő módban futó Azure virtuális gépeken.

## <a name="test-connection-to-listener"></a>Figyelő kapcsolat ellenőrzéséhez

A kapcsolat ellenőrzéséhez:

1. RDP egy SQL Server ugyanazon virtuális hálózatban, de nem tulajdonosa a replikát. A fürt használhatja az SQL Server.

1. Használjon **sqlcmd** segédprogram létrehozott kapcsolat ellenőrzéséhez. Például az alábbi parancsfájlt hoz létre egy **sqlcmd** kapcsolatot az elsődleges másodpéldány, a figyelő a Windows-hitelesítés használatával:

    ```
    sqlcmd -S <listenerName> -E
    ```

    Ha a figyelő az alapértelmezettől eltérő portot használ (1433) portot, adja meg a portot a kapcsolati karakterláncban. A következő sqlcmd paranccsal például egy figyelő 1435 porton csatlakozik:

    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Az SQLCMD kapcsolat automatikusan csatlakozik bármely SQL Server-példányt az elsődleges replikát.

> [!TIP]
> Győződjön meg arról, hogy a megadott port meg nyitva a tűzfalon az mindkét SQL-kiszolgálók. A TCP-portot, amelyekkel egy bejövő forgalomra vonatkozó szabály mindkét kiszolgáló szükséges. További információkért lásd: [hozzáadása vagy szerkesztése tűzfalszabály](http://technet.microsoft.com/library/cc753558.aspx).
>
>



<!--**Notes**: *Notes provide just-in-time info: A Note is “by the way” info, an Important is info users need to complete a task, Tip is for shortcuts. Don’t overdo*.-->


<!--**Procedures**: *This is the second “step." They often include substeps. Again, use a short title that tells users what they’ll do*. *("Configure a new web project.")*-->

<!--**UI**: *Note the format for documenting the UI: bold for UI elements and arrow keys for sequence. (Ex. Click **File > New > Project**.)*-->

<!--**Screenshot**: *Screenshots really help users. But don’t include too many since they’re difficult to maintain. Highlight areas you are referring to in red.*-->

<!--**No. of steps**: *Make sure the number of steps within a procedure is 10 or fewer. Seven steps is ideal. Break up long procedure logically.*-->


<!--**Next steps**: *Reiterate what users have done, and give them interesting and useful next steps so they want to go on.*-->

## <a name="next-steps"></a>Következő lépések

- [IP-cím hozzáadása egy terheléselosztót egy másik rendelkezésre állási csoport](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
