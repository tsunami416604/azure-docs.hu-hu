---
title: SQL Server rendelkezésre állási csoportok – Azure-beli virtuális gépek – oktatóanyag |} A Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy SQL Server Always On rendelkezésre állási csoportot az Azure Virtual machines szolgáltatásban.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.openlocfilehash: 42a4ea1e4dc352e56fbd65f69c9ed71e3b0c1038
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238075"
---
# <a name="configure-always-on-availability-group-in-azure-vm-manually"></a>Konfigurálása Always On rendelkezésre állási csoportot az Azure virtuális Gépen manuálisan

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy SQL Server Always On rendelkezésre állási csoportot az Azure Virtual machines szolgáltatásban. A részletes útmutató egy rendelkezésre állási csoportot hoz létre a két különböző SQL Server adatbázis-replikát.

**Becsült időtartam**: az előfeltételek teljesülése után elvégzése nagyjából 30 percet vesz igénybe.

Az ábra az oktatóanyag során létre.

![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag feltételezi, hogy az SQL Server Always On rendelkezésre állási csoportok alapvető ismeretekkel. Ha további információra van szüksége, tekintse meg [mindig a rendelkezésre állási csoportok áttekintése (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

Az alábbi táblázat az oktatóanyag elkezdése előtt hajtsa végre az előfeltételekről:

|  |Követelmény |Leírás |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Két SQL-kiszolgáló | – Az Azure rendelkezésre állási csoport <br/> – Egyetlen tartományban <br/> -A Feladatátvételi fürtszolgáltatás telepítése |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | A fürt tanúsító fájlmegosztás |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Az SQL Server-szolgáltatásfiók | Tartományi fiók |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Az SQL Server Agent szolgáltatásfiók | Tartományi fiók |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Tűzfal portok megnyitása | -Az SQL Server: **1433-as** alapértelmezett példány <br/> -Adatbázis-tükrözési végpontját: **5022** vagy minden elérhető port <br/> – Rendelkezésre állási csoport terheléselosztó IP cím állapotmintája: **59999** vagy minden elérhető port <br/> -Terheléselosztói IP cím állapotfigyelő mintavételező fürt core: **58888** vagy minden elérhető port |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Adja hozzá a Feladatátvételi fürtszolgáltatáshoz | Mindkét SQL-kiszolgálókat kell ezt a szolgáltatást |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Telepítési tartományi fiók | -Minden egyes SQL Server helyi rendszergazdája <br/> -SQL Server SysAdmin (rendszergazda) rögzített kiszolgálói szerepkör az SQL Server minden példányának tagság  |


Az oktatóanyag elkezdéséhez kell [Always On rendelkezésre állási csoportok létrehozásához az Azure Virtual machines gépeken előfeltételeinek teljesítéséhez](virtual-machines-windows-portal-sql-availability-group-prereq.md). Ha már végrehajtotta az Előfeltételek is ugorhat [-fürt létrehozása](#CreateCluster).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>A fürt létrehozása

Az Előfeltételek befejeztével az első lépéseként hozhat létre Windows Server feladatátvételi fürt, amely tartalmazza a két SQL-kiszolgálója és a egy tanúsító kiszolgáló.

1. RDP-vel az első SQL-kiszolgáló egy tartományi fiókkal, amely rendszergazda az SQL Server-kiszolgálók és a tanúsító kiszolgálón is.

   >[!TIP]
   >Ha követte a [előfeltételeket tartalmazó dokumentumot](virtual-machines-windows-portal-sql-availability-group-prereq.md), létrehozott egy olyan fiókkal, amelynek neve **CORP\Install**. Ezt a fiókot használja.

2. Az a **Kiszolgálókezelő** irányítópulton válassza **eszközök**, és kattintson a **Feladatátvevőfürt-kezelőben**.
3. A bal oldali ablaktáblán kattintson a jobb gombbal **Feladatátvevőfürt-kezelőben**, és kattintson a **hozzon létre egy fürtöt**.
   ![Fürt létrehozása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. A fürt létrehozása varázsló hozzon létre egy egycsomópontos fürtöt az alábbi táblázatban a beállításokkal a lapok vétel:

   | Oldal | Beállítások |
   | --- | --- |
   | A telepítés megkezdése előtt |Alapértelmezések használata |
   | Kiszolgálók kiválasztása |Írja be az első SQL-kiszolgáló nevére a **Enter server name** kattintson **Hozzáadás**. |
   | Érvényesítési figyelmeztetés |Válassza ki **szám-e ehhez a fürthöz nincs szüksége támogatásra a Microsofttól, és ezért nem kívánja az érvényesítési tesztek futtatásához. Ha a Tovább gombra kattintva folytassa a fürt létrehozása**. |
   | Hozzáférési pont a fürt felügyeletéhez |Írja be a fürt nevét, például **SQLAGCluster1** a **fürtnév**.|
   | Megerősítés |Használja az alapértelmezett értékeket, a tárolóhelyek használata. Lásd a táblázat utáni megjegyzést. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>A Windows server feladatátvételi fürt IP-cím beállítása

1. A **Feladatátvevőfürt-kezelőben**, görgessen le a **fürt alapvető erőforrásai** , és bontsa ki a fürt részletes adatai. Mindkét kell megjelennie a **neve** és a **IP-cím** erőforrásokat a **sikertelen** állapota. Az IP-cím erőforrás nem állítható online állapotba, mert a fürt a gép maga is azonos IP-cím van hozzárendelve, ezért a duplikált cím.

2. Kattintson a jobb gombbal az a sikertelen **IP-cím** erőforrás, és kattintson **tulajdonságok**.

   ![Fürt tulajdonságai](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Válassza ki **statikus IP-cím** , és adja meg a virtuális gépek egy ugyanazon az alhálózaton elérhető címet.

4. Az a **fürt alapvető erőforrásai** szakaszt, kattintson a jobb gombbal a fürt nevét, és kattintson a **online állapotba hozás**. Ezután Várjon, amíg mindkét erőforrás online. Amikor a fürt neve erőforrás online állapotba kerül, az új AD-számítógépfiók frissíti a tartományvezérlő kiszolgálóhoz. Használja ezt a fiókot AD a rendelkezésre állási csoport fürtözött szolgáltatás később futtatásához.

### <a name="addNode"></a>Az SQL Server hozzáadása a fürthöz

Az SQL Server hozzáadása a fürthöz.

1. A böngésző konzolfáján kattintson a jobb gombbal a fürt, és kattintson a **csomópont hozzáadása**.

    ![Csomópont hozzáadása a fürthöz](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. Az a **csomópont hozzáadása varázsló**, kattintson a **tovább**. Az a **kiszolgálók kiválasztása** oldalon adja hozzá a második SQL-kiszolgáló. Írja be a kiszolgáló nevére a **Enter server name** majd **Hozzáadás**. Amikor elkészült, kattintson a **tovább**.

1. Az a **érvényesítési figyelmeztetés** kattintson **nem** (éles forgatókönyvekben végre kell hajtania az ellenőrző teszteket). Ezután kattintson a **Tovább** gombra.

8. Az a **megerősítő** lapon, a tárolóhelyek használatakor, törölje a jelet a jelölőnégyzetből, címkéjű **minden megfelelő tároló felvétele a fürtbe.**

   ![Adja hozzá a megerősítési csomópont](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Ha a tárolóhelyeket használja, és nem törölje a jelet **minden megfelelő tároló felvétele a fürtbe**, Windows leválasztja a virtuális lemezek a csoportosítási eljárás során. Ennek eredményeképpen nem jelennek meg a logikailemez-kezelő vagy Explorer mindaddig, amíg a tárolóhelyek el lesznek távolítva a fürtöt, és csatolni a PowerShell használatával. A tárolóhelyek több lemezek a tárolókészletekhez csoportosítja. További információkért lásd: [tárolóhelyek](https://technet.microsoft.com/library/hh831739).

1. Kattintson a **Tovább** gombra.

1. Kattintson a **Befejezés** gombra.

   A Feladatátvevőfürt-kezelő jeleníti meg, hogy a fürt rendelkezik egy új csomópont, és felsorolja a a **csomópontok** tároló.

10. Jelentkezzen ki a távoli asztali munkamenetet.

### <a name="add-a-cluster-quorum-file-share"></a>A fürt kvórum fájlmegosztás hozzáadása

Ebben a példában a Windows-fürt létrehozása a fürt kvórum fájlmegosztást használ. Ebben az oktatóanyagban egy csomópont- és fájlmegosztástöbbség kvóruma. További információkért lásd: [a feladatátvevő fürtök kvórumkonfigurációinak ismertetése](https://technet.microsoft.com/library/cc731739.aspx).

1. Csatlakozzon a fájlmegosztás tanúsító tag kiszolgálója a távoli asztali munkamenetet.

1. A **Kiszolgálókezelő**, kattintson a **eszközök**. Nyissa meg **számítógép-kezelés**.

1. Kattintson a **megosztott mappák**.

1. Kattintson a jobb gombbal **megosztások**, és kattintson a **új megosztás...** .

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Használat **megosztott mappa létrehozása varázsló** megosztás létrehozása.

1. A **mappa elérési útja**, kattintson a **Tallózás** , és keresse meg, vagy hozzon létre egy megosztott mappa elérési útját. Kattintson a **Tovább** gombra.

1. A **nevét, leírását és beállítások** ellenőrizze-e a megosztás nevét és elérési útja. Kattintson a **Tovább** gombra.

1. A **megosztott mappa engedélyeit** beállítása **engedélyek testreszabása**. Kattintson a **egyéni...** .

1. A **engedélyek testreszabása**, kattintson a **hozzáadása...** .

1. Győződjön meg arról, hogy a fürt létrehozásához használt fiók teljes körű vezérléssel rendelkezik.

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Kattintson az **OK** gombra.

1. A **megosztott mappa engedélyeit**, kattintson a **Befejezés**. Kattintson a **Befejezés** újra.  

1. Jelentkezzen ki a kiszolgáló

### <a name="configure-cluster-quorum"></a>A fürtkvórum konfigurálása

Következő lépésként állítsa a fürt kvórumát.

1. Az első fürtcsomópontra, a távoli asztalról csatlakozni.

1. A **Feladatátvevőfürt-kezelőben**, kattintson a jobb gombbal a fürt, mutasson a **további műveletek**, és kattintson a **fürt kvórumbeállításainak megadása...** .

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. A **beállítása varázsló**, kattintson a **tovább**.

1. A **kvórumbeállítások kijelölése**, válassza a **a kvórum tanúsítójának kijelölése**, és kattintson a **tovább**.

1. A **kvórum Tanúsítójának kijelölése**, kattintson a **konfigurálja egy tanúsító fájlmegosztást**.

   >[!TIP]
   >A Windows Server 2016-ban a felhőbeli tanúsító támogatja. Ha úgy dönt, hogy az ilyen típusú tanúsító, nem kell egy fájlt tanúsító megosztani. További információkért lásd: [felhőbeli tanúsító a feladatátvevő fürt telepítése](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Ebben az oktatóanyagban egy tanúsító fájlmegosztást, amely korábbi operációs rendszerek által támogatott.

1. A **tanúsító fájlmegosztás**, írja be a létrehozott megosztás elérési útja. Kattintson a **Tovább** gombra.

1. Ellenőrizze a beállításokat a **megerősítő**. Kattintson a **Tovább** gombra.

1. Kattintson a **Befejezés** gombra.

Egy tanúsító fájlmegosztást a fürt alapvető erőforrásai vannak konfigurálva.

## <a name="enable-availability-groups"></a>Rendelkezésre állási csoportok engedélyezése

Következő lépésként engedélyezze a **AlwaysOn rendelkezésre állási csoportok** funkció. Hajtsa végre ezeket a lépéseket, a két SQL-kiszolgálón.

1. Az a **Start** indítása jobb **SQL Server Configuration Manager**.
2. A böngésző konzolfáján kattintson **SQL Server Services**, kattintson a jobb gombbal a **SQL Server (MSSQLSERVER)** szolgáltatást, és kattintson a **tulajdonságok**.
3. Kattintson a **AlwaysOn magas rendelkezésre állású** lapfülre, majd válassza ki **engedélyezze az AlwaysOn rendelkezésre állási csoportok**, az alábbiak szerint:

    ![Engedélyezze az AlwaysOn rendelkezésre állási csoportok](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Kattintson az **Alkalmaz** gombra. Kattintson a **OK** az előugró párbeszédpanelen.

5. Indítsa újra az SQL Server szolgáltatást.

Ismételje meg ezeket a lépéseket az SQL Server.

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

1. Indítsa el az RDP-fájlt, és az első SQL-kiszolgáló egy tartományi fiókkal, amely a sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagja.
1. Nyissa meg az SQL Server Management Studiót, és csatlakozzon az első SQL-kiszolgáló.
7. A **Object Explorer**, kattintson a jobb gombbal **adatbázisok** kattintson **új adatbázis**.
8. A **adatbázisnév**, típus **MyDB1**, majd kattintson a **OK**.

### <a name="backupshare"></a> Hozzon létre egy biztonsági mentési megosztást

1. Az első, az SQL Server **Kiszolgálókezelő**, kattintson a **eszközök**. Nyissa meg **számítógép-kezelés**.

1. Kattintson a **megosztott mappák**.

1. Kattintson a jobb gombbal **megosztások**, és kattintson a **új megosztás...** .

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Használat **megosztott mappa létrehozása varázsló** megosztás létrehozása.

1. A **mappa elérési útja**, kattintson a **Tallózás** , és keresse meg, vagy hozzon létre egy adatbázis biztonsági mentési tartalmazó megosztott mappa elérési útját. Kattintson a **Tovább** gombra.

1. A **nevét, leírását és beállítások** ellenőrizze-e a megosztás nevét és elérési útja. Kattintson a **Tovább** gombra.

1. A **megosztott mappa engedélyeit** beállítása **engedélyek testreszabása**. Kattintson a **egyéni...** .

1. A **engedélyek testreszabása**, kattintson a **hozzáadása...** .

1. Győződjön meg arról, hogy mindkét kiszolgálón az SQL Server és SQL Server Agent szolgáltatásfiókok teljes hozzáféréssel rendelkeznek.

   ![Új megosztás](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Kattintson az **OK** gombra.

1. A **megosztott mappa engedélyeit**, kattintson a **Befejezés**. Kattintson a **Befejezés** újra.  

### <a name="take-a-full-backup-of-the-database"></a>Biztonsági mentés, az adatbázis teljes

Készítsen biztonsági másolatot az új adatbázis a naplózási láncban inicializálni kell. Ha nem ad meg az új adatbázis biztonsági másolatának, azt egy rendelkezésre állási csoportban nem szerepelhet.

1. A **Object Explorer**, gombbal az adatbázisra, mutasson a **feladatok...** , kattintson a **biztonsági mentése**.

1. Kattintson a **OK** kell tennie a teljes biztonsági mentés az alapértelmezett helyre.

## <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozása
Most már készen áll az alábbi lépéseket követve rendelkezésre állási csoport konfigurálása:

* Az első SQL Server-adatbázis létrehozása.
* Egy teljes biztonsági mentés és a tranzakciós napló biztonsági mentését az adatbázis is igénybe vehet
* Állítsa vissza a teljes, és a biztonsági mentések bejelentkezni a második SQL Server a **NORECOVERY** lehetőség
* A rendelkezésre állási csoport létrehozása (**AG1**) a szinkron véglegesítés, automatikus feladatátvétel és olvasható másodlagos replikával

### <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozása:

1. A távoli asztali munkamenetet az első SQL-kiszolgáló. A **Object Explorer** az ssms-ben, kattintson a jobb gombbal **AlwaysOn magas rendelkezésre állású** kattintson **új rendelkezésre állási csoport varázsló**.

    ![Új rendelkezésre állási csoport varázsló indítása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Az a **bemutatása** kattintson **tovább**. Az a **adja meg a rendelkezésre állási csoport nevének** írja be például a rendelkezésre állási csoport nevét **AG1**, a **rendelkezésre állási csoport nevének**. Kattintson a **Tovább** gombra.

    ![Új rendelkezésre állási csoport varázsló, adja meg a rendelkezésre állási csoport nevét](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Az a **kiválasztása adatbázisok** lapon válassza ki az adatbázist, és kattintson **tovább**.

   >[!NOTE]
   >Az adatbázis megfelel az előfeltételeknek, rendelkezésre állási csoporthoz, mert legalább egy teljes biztonsági mentés végrehajtása a tervezett elsődleges replikán.

   ![Új rendelkezésre állási csoport varázsló, válassza ki az adatbázisokat](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Az a **replikák megadása** kattintson **hozzáadása replika**.

   ![Új rendelkezésre állási csoport varázsló, adja meg a replikák](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. A **kapcsolódás a kiszolgálóhoz** felugró párbeszédpanel. Írja be a nevét, a második kiszolgáló **kiszolgálónév**. Kattintson a **Connect** (Csatlakozás) gombra.

   Térjen vissza a **replikák megadása** lapon meg kell jelennie a második kiszolgálóra szerepel **rendelkezésre állási másodpéldányok**. A replikákat a következőképpen konfigurálja.

   ![Új rendelkezésre állási csoport varázsló, adja meg a replikák (kész)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Kattintson a **végpontok** az adatbázis-tükrözési végpontját a rendelkezésre állási csoport megtekintéséhez. Használhatja ugyanazt a portot, amelyet beállításakor használt a [adatbázis-tükrözési végpont tűzfalszabályt](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Új rendelkezésre állási csoport varázsló kezdeti adatszinkronizálás kiválasztása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. Az a **kezdeti adatszinkronizálás kiválasztása** lapra, jelölje be **teljes** , és adja meg egy megosztott hálózati helyre. A helyet használja a [létrehozott biztonsági másolatok megosztásáról](#backupshare). A példában volt **\\\\\<első SQL-kiszolgáló\>\Backup\\**. **Kattintson** a Tovább gombra.

   >[!NOTE]
   >Teljes szinkronizálás teljes biztonsági másolatot készít az adatbázis az SQL Server első példányát, és visszaállítja a második példány. A nagy méretű adatbázisok esetében a teljes szinkronizálás nem javasolt, mert hosszú ideig is eltarthat. Most manuálisan az adatbázis biztonsági mentése és visszaállítása, az csökkentheti `NO RECOVERY`. Ha az adatbázis már visszaállítva a `NO RECOVERY` válassza ki a második SQL Serveren a rendelkezésre állási csoport konfigurálása előtt **csak összekapcsolás**. Ha szeretné venni a biztonsági mentés a rendelkezésre állási csoport konfigurálása után, válassza a **kezdeti adatszinkronizálás kihagyása**.

    ![Új rendelkezésre állási csoport varázsló kezdeti adatszinkronizálás kiválasztása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Az a **érvényesítési** kattintson **tovább**. Ezen a lapon az alábbi képhez hasonlóan kell kinéznie:

    ![Új rendelkezésre állási csoport varázsló érvényesítése](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >A figyelő konfigurációjába vonatkozó figyelmeztetés van, mert nem állított be egy rendelkezésre állási csoport kérésfigyelőjének. Figyelmen kívül hagyhatja ezt a figyelmeztetést, mert az Azure-beli virtuális gépeken hoz létre a figyelő az Azure load balancer létrehozása után.

10. Az a **összegzése** kattintson **Befejezés**, majd várjon, amíg a varázsló konfigurálja az új rendelkezésre állási csoporthoz. Az a **folyamatban** lapon, rákattinthat **további részleteket** részletes folyamat előrehaladásának megtekintéséhez. Ha a varázsló befejeződött, ellenőrizze a **eredmények** lapon ellenőrizze, hogy a rendelkezésre állási csoport sikeresen létrejött-e.

     ![Új rendelkezésre állási csoport varázsló eredménye](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Kattintson a **Bezárás** a varázslóból való kilépéshez.

### <a name="check-the-availability-group"></a>Ellenőrizze a rendelkezésre állási csoport

1. A **Object Explorer**, bontsa ki a **AlwaysOn magas rendelkezésre állású**, majd bontsa ki a **rendelkezésre állási csoportok**. Meg kell jelennie az új rendelkezésre állási csoport ebben a tárolóban. Kattintson a jobb gombbal a rendelkezésre állási csoport, és kattintson a **megjelenítése irányítópult**.

   ![Rendelkezésre állási csoport irányítópultján megjelenítése](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   A **AlwaysOn irányítópult** ehhez hasonlóan kell kinéznie.

   ![Rendelkezésre állási csoport irányítópultján](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Láthatja, hogy a replikákat, a feladatátvételi módot a szinkronizálás állapotát és minden egyes replikának.

2. A **Feladatátvevőfürt-kezelőben**, kattintson a fürtre. Válassza ki **szerepkörök**. A rendelkezésre állási csoport használt név egy szerepkört a fürtön. A rendelkezésre állási csoport nem rendelkezik ügyfélkapcsolatokat, IP-címet, mivel egy figyelő nem konfigurálta. Az Azure load balancer létrehozása után konfigurálja a figyelőt.

   ![Rendelkezésre állási csoport a Feladatátvevőfürt-kezelő](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Ne próbálja átadja a feladatokat a rendelkezésre állási csoporthoz a Feladatátvevőfürt-kezelőben. Összes feladatátvételi műveletet kell végezni a **AlwaysOn irányítópult** az ssms-ben. További információkért lásd: [korlátozások a használja a Feladatátvevőfürt-kezelő rendelkezésre állási csoportokkal](https://msdn.microsoft.com/library/ff929171.aspx).
    >

Ezen a ponton rendelkezik egy rendelkezésre állási csoporthoz az SQL Server két példánya a replikákat. A rendelkezésre állási csoport helyezheti át a példányok között. Nem lehet csatlakoztatni a rendelkezésre állási csoporthoz még, mert nem rendelkezik egy figyelőt. Azure-beli virtuális gépeken a figyelő egy terheléselosztót igényel. A következő lépés, hogy a terheléselosztó létrehozása az Azure-ban.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Azure-terheléselosztó létrehozása

Azure-beli virtuális gépek egy SQL Server rendelkezésre állási csoport terheléselosztó szükséges. A terheléselosztó IP-címeket tartalmazza a rendelkezésre állási csoport figyelője és a Windows Server feladatátvevő fürt. Ez a szakasz foglalja össze a terheléselosztó létrehozása az Azure Portalon.

Egy Azure Load Balancer Standard Load Balancer és a egy alapszintű Load Balancer lehet. A standard Load Balancer rendelkezik több funkcióval, mint az alapszintű Load Balancer. Rendelkezésre állási csoporthoz a Standard Load Balancer akkor szükséges, ha egy rendelkezésre állási zónában (helyett egy rendelkezésre állási csoport) használja. A load balancer típusok közötti különbség a részletekért lásd: [Load Balancer Termékváltozat összehasonlító](../../../load-balancer/load-balancer-overview.md#skus).

1. Az Azure Portalon nyissa meg az erőforráscsoport, amelyben az SQL-kiszolgálók, és kattintson a **+ Hozzáadás**.
1. Keresse meg **Load Balancer**. Válassza ki a Microsoft által kiadott terheléselosztó.

   ![Rendelkezésre állási csoport a Feladatátvevőfürt-kezelő](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Kattintson a **Create** (Létrehozás) gombra.
1. Konfigurálja az alábbi paramétereket a terheléselosztóhoz.

   | Beállítás | Mező |
   | --- | --- |
   | **Name (Név)** |Például használja a terheléselosztó szöveges nevét **sqlLB**. |
   | **Típus** |Belső |
   | **Virtuális hálózat** |Az Azure virtuális hálózat nevét használja. |
   | **Alhálózat** |Az alhálózatot, amelyet a virtuális gép nevét használja.  |
   | **IP-cím hozzárendelése** |Statikus |
   | **IP-cím** |Egy rendelkezésre álló alhálózati címet használja. A rendelkezésre állási csoport figyelőjének használja ezt a címet. Vegye figyelembe, hogy ez eltér a fürt IP-címről.  |
   | **Előfizetés** |Használja a virtuális gép ugyanahhoz az előfizetéshez. |
   | **Hely** |A virtuális gép is ugyanazt a helyet használja. |

   Az Azure portal paneljén kell kinéznie:

   ![Load Balancer létrehozása](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Kattintson a **létrehozás**, a terheléselosztó létrehozásához.

A terheléselosztó konfigurálásához szeretne egy háttérkészlet, a mintavétel létrehozása és a terheléselosztási szabályok beállítása. Ezek az Azure Portalon teheti meg.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>A rendelkezésre állási csoport figyelőjének a háttérkészlet hozzáadása

1. Az Azure Portalon nyissa meg a rendelkezésre állási csoport. Szüksége lehet az újonnan létrehozott terheléselosztó megtekintéséhez a nézet frissítéséhez.

   ![Keresse meg a terheléselosztó az erőforráscsoportban](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Kattintson a terheléselosztóhoz, majd **háttérkészletek**, és kattintson a **+ Hozzáadás**.

1. Adja meg a háttérkészlet nevét.

1. A háttérkészlet társítása a rendelkezésre állási csoport, amely tartalmazza a virtuális gépeket.

1. A **cél IP-konfigurációja**, ellenőrizze **virtuális gép** , és válassza ki a rendelkezésre állási csoport replikái üzemeltető virtuális gépek mindegyikét. A tanúsító fájlmegosztás kiszolgálója nem tartalmazzák.

   >[!NOTE]
   >Ha mindkét virtuális gép nincs megadva, a kapcsolatokat csak az elsődleges replikára lesz sikeres.

1. Kattintson a **OK** hozhat létre a háttérkészlethez.

### <a name="set-the-probe"></a>A mintavétel beállítása

1. Kattintson a terheléselosztóhoz, majd **állapotadat-mintavételek**, és kattintson a **+ Hozzáadás**.

1. A figyelő az állapotfigyelő mintavételező csoportot az alábbiak szerint:

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Name (Név)** | Szöveg | SQLAlwaysOnEndPointProbe |
   | **Protocol (Protokoll)** | Válassza a TCP | TCP |
   | **Port** | Minden nem használt portot | 59999 |
   | **Intervallum**  | Másodpercek alatt a mintavételi kísérletek közötti idő |5 |
   | **Nem kifogástalan állapot küszöbértéke** | Egymást követő hibák, amelyek tekint, hogy nem megfelelő állapotú virtuális gépek száma  | 2 |

1. Kattintson a **OK** az állapotminta beállításához.

### <a name="set-the-load-balancing-rules"></a>A terheléselosztási szabályok beállítása

1. Kattintson a terheléselosztóhoz, majd **terheléselosztási szabályok**, és kattintson a **+ Hozzáadás**.

1. A figyelő terheléselosztási szabályok módon állítsa be.
   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Name (Név)** | Szöveg | SQLAlwaysOnEndPointListener |
   | **Előtérbeli IP-cím** | Válasszon címet |A terheléselosztó létrehozásakor létrehozott címet használja. |
   | **Protocol (Protokoll)** | Válassza a TCP |TCP |
   | **Port** | A port használata a rendelkezésre állási csoport figyelője | 1433 |
   | **Háttérport** | Ha a nem fix IP-értéke a közvetlen kiszolgálói visszatérési nem használja ezt a mezőt | 1433 |
   | **Mintavétel** |A mintavétel megadott név | SQLAlwaysOnEndPointProbe |
   | **Munkamenet megőrzését** | Legördülő lista | **Egyik sem** |
   | **Üresjárat időkorlátja** | A TCP-kapcsolat nyitva tartása perc | 4 |
   | **Nem fix IP (közvetlen kiszolgálói válasz)** | |Engedélyezve |

   > [!WARNING]
   > A közvetlen kiszolgálói válasz létrehozásakor van beállítva. A név nem módosítható.

1. Kattintson a **OK** figyelő terheléselosztási szabályok beállítása.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>A Windows Server feladatátvételi fürt (WSFC) a fürt alapvető IP-cím hozzáadásához

A WSFC-IP-cím is kell lennie a terheléselosztón.

1. Kattintson a portál, az azonos Azure load balancer a **előtérbeli IP-konfiguráció** kattintson **+ Hozzáadás**. A fürt alapvető erőforrásai a WSFC konfigurált IP-címet használja. Statikus IP-cím beállítva.

1. Kattintson a terheléselosztó **állapotadat-mintavételek**, és kattintson a **+ Hozzáadás**.

1. A WSFC fürt core IP cím állapotmintát csoportot az alábbiak szerint:

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Name (Név)** | Szöveg | WSFCEndPointProbe |
   | **Protocol (Protokoll)** | Válassza a TCP | TCP |
   | **Port** | Minden nem használt portot | 58888 |
   | **Intervallum**  | Másodpercek alatt a mintavételi kísérletek közötti idő |5 |
   | **Nem kifogástalan állapot küszöbértéke** | Egymást követő hibák, amelyek tekint, hogy nem megfelelő állapotú virtuális gépek száma  | 2 |

1. Kattintson a **OK** az állapotminta beállításához.

1. A terheléselosztási szabályok megadása Kattintson a **terheléselosztási szabályok**, és kattintson a **+ Hozzáadás**.

1. Állítsa be a fürt alapvető IP cím terheléselosztási szabályok a következő.
   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Name (Név)** | Szöveg | WSFCEndPoint |
   | **Előtérbeli IP-cím** | Válasszon címet |A WSFC-IP-cím konfigurálásakor létrehozott címet használja. Ez a figyelő IP-cím eltér |
   | **Protocol (Protokoll)** | Válassza a TCP |TCP |
   | **Port** | A port használata a fürt IP-címét. Ez az egy szabad portot, amely nem szolgál a figyelő mintavételi portot. | 58888 |
   | **Háttérport** | Ha a nem fix IP-értéke a közvetlen kiszolgálói visszatérési nem használja ezt a mezőt | 58888 |
   | **Mintavétel** |A mintavétel megadott név | WSFCEndPointProbe |
   | **Munkamenet megőrzését** | Legördülő lista | **Egyik sem** |
   | **Üresjárat időkorlátja** | A TCP-kapcsolat nyitva tartása perc | 4 |
   | **Nem fix IP (közvetlen kiszolgálói válasz)** | |Engedélyezve |

   > [!WARNING]
   > A közvetlen kiszolgálói válasz létrehozásakor van beállítva. A név nem módosítható.

1. Kattintson a **OK** a terheléselosztási szabályok beállítása.

## <a name="configure-listener"></a> A figyelő konfigurálása

Ehhez a következő dolog, hogy egy rendelkezésre állási csoport kérésfigyelőjének konfigurálása a feladatátvevő fürt.

> [!NOTE]
> Ez az oktatóanyag bemutatja, hogyan hozhat létre egyetlen figyelője működik – egy ILB IP-címet. Egy vagy több IP-címek használatával egy vagy több figyelő létrehozásához lásd: [Create Availability Group listener és a load balancer |} Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Figyelő-port beállítása

Az SQL Server Management Studióban állítsa be a figyelőjének portszámára.

1. Indítsa el az SQL Server Management Studiót, és kapcsolódjon az elsődleges replika.

1. Navigáljon a **AlwaysOn magas rendelkezésre állás** | **rendelkezésre állási csoportok** | **rendelkezésre állási csoport figyelője**.

1. Meg kell jelennie a figyelő nevét, amely a Feladatátvevőfürt-kezelő létrehozta. Kattintson a jobb gombbal a figyelő nevét, és kattintson a **tulajdonságok**.

1. Az a **Port** mezőben, adja meg a portszámot a rendelkezésre állási csoport kérésfigyelőjének. az alapértelmezett érték 1433-as, majd kattintson az **OK**.

Most már rendelkezik egy SQL Server rendelkezésre állási csoportot az Azure virtual machines Resource Manager módban.

## <a name="test-connection-to-listener"></a>A figyelő a kapcsolat tesztelése

A kapcsolat teszteléséhez:

1. RDP-vel az azonos virtuális hálózatba, de a replika nem rendelkezik SQL-kiszolgálóra. Az SQL Server a fürtben is használhatja.

1. Használat **sqlcmd** segédprogram a kapcsolat teszteléséhez. Például hozza létre a következő parancsfájl egy **sqlcmd** kapcsolatot az elsődleges replika, a figyelő a Windows-hitelesítés használatával:

  ```cmd
  sqlcmd -S <listenerName> -E
  ```

  Ha a figyelő nem az alapértelmezett portot használ portot (1433), adja meg a portot a kapcsolati karakterláncban. A következő sqlcmd parancsot például egy figyelő 1435 porton csatlakozik:

  ```cmd
  sqlcmd -S <listenerName>,1435 -E
  ```

Az SQLCMD-kapcsolatot automatikusan csatlakozik bármely SQL Server-példány az elsődleges replikát.

> [!TIP]
> Győződjön meg arról, hogy a megadott port nyitva a tűzfalon az SQL-kiszolgálók is. Mindkét kiszolgáló szükséges egy bejövő szabályt a TCP-portot, amelyet használhat. További információkért lásd: [hozzáadása vagy szerkesztése tűzfalszabály](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>További lépések

- [IP-címet ad hozzá egy második rendelkezésre állási csoport terheléselosztó](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
