---
title: Az SQL Server Business Intelligence |} A Microsoft Docs
description: Ez a témakör a klasszikus üzemi modellel létrehozott erőforrásokat használja, és az SQL Server rendszert futtató Azure virtuális gépeken (VM) az üzleti intelligenciát (BI) funkcióját ismerteti.
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: c681e7a7-eeda-48aa-bc35-6277f4828244
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2017
ms.author: maghan
ms.openlocfilehash: 29e851772e665b4130ee58b04c264d55bcd54523
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317782"
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>Az SQL Server Business Intelligence használata Azure-beli virtuális gépeken
> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

A Microsoft Azure virtuálisgép-katalógus rendszerképek, amelyek tartalmazzák az SQL Server-telepítéseket tartalmaz. Az SQL Server-kiadásokat támogatja a katalógus-rendszerképek is telepítheti a helyszíni számítógépek és virtuális gépek telepítési fájlokkal. Ez a témakör felsorolja a a lemezképeket telepíteni az SQL Server Business Intelligence-(BI) szolgáltatásokat és a egy virtuális gép kiépítése után szükséges konfigurációs lépéseket. Ez a témakör ismerteti a támogatott üzembe helyezési topológiák BI funkcióiról és ajánlott eljárásokat is.

## <a name="license-considerations"></a>License Considerations
A licenc az SQL Server a Microsoft Azure Virtual Machines szolgáltatásban két módja van:

1. Licenc mobility előnyök tartozó frissítési garanciával rendelkező. További információkért lásd: [az Azure frissítési garancián keresztüli Licenchordozhatóság](https://azure.microsoft.com/pricing/license-mobility/).
2. Fizessen a feldolgozási sebessége az Azure Virtual Machines az SQL Server telepítve van. Az "SQL Server" című szakaszában talál [Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

A licencelési és a jelenlegi díjszabás szerint további információkért lásd: [virtuális gépek licencelés – gyakori kérdések](https://azure.microsoft.com/pricing/licensing-faq/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>Az SQL Server-rendszerképeit elérhető az Azure virtuálisgép-katalógusban
A Microsoft Azure virtuálisgép-katalógus, amelyek tartalmazzák a Microsoft SQL Server több lemezképet tartalmaz. A virtuálisgép-rendszerképek telepített szoftvereket az operációs rendszer verziója és az SQL Server verziója alapján változik. Az Azure-beli virtuálisgép-katalógusában elérhető rendszerképek listájának gyakran változik.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![SQL-lemezképet az Azure-beli Virtuálisgép-katalógus](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) A következő PowerShell-szkriptet az Azure-rendszerképek, amelyek tartalmazzák az "SQL-kiszolgáló" a ImageName a listáját adja vissza:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "Subscriptions" menu in Azure portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

A kiadások és az SQL Server által támogatott funkciókról további információkért tekintse meg a következőket:

* [SQL Server Editions](https://www.microsoft.com/sql-server/sql-server-2017-editions)
* [A kiadások, az SQL Server 2016 által támogatott funkciók](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>BI-funkciók az SQL Server virtuálisgép-katalógusban képeken
Az alábbi táblázat a Microsoft Azure virtuális gép katalógus gyakori rendszerképek telepítve az SQL Server Business Intelligence szolgáltatásait foglalja össze:

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| Az SQL Server BI funkció | A katalógus-lemezkép telepítve | Megjegyzések |
| --- | --- | --- |
| **Jelentéskészítési szolgáltatások natív mód** |Igen |Telepítve van, de konfigurálására, beleértve a jelentés manager URL-címet igényel. Című témakör [Reporting Services konfigurálása](#configure-reporting-services). |
| **A SharePoint-módú Reporting Services** |Nem |A Microsoft Azure virtuális gép image z galerie nem tartalmazza a SharePoint vagy a SharePoint telepítési fájljait. <sup>1</sup> |
| **Analysis Services többdimenziós és az adatok adatbányászati (OLAP)** |Igen |Telepítve és konfigurálva, az alapértelmezett Analysis Services-példány |
| **Analysis Services táblázatos** |Nem |Támogatott az SQL Server 2012, 2014 és 2016 lemezképek, de nincs telepítve alapértelmezés szerint. Telepítse az Analysis Services egy másik példánya. Lásd az egyéb SQL Server-szolgáltatások és szolgáltatások telepítése a ebben a témakörben. |
| **Analysis Services Power Pivot a Sharepointhoz** |Nem |A Microsoft Azure virtuális gép image z galerie nem tartalmazza a SharePoint vagy a SharePoint telepítési fájljait. <sup>1</sup> |

<sup>1</sup> a SharePoint és az Azure virtual machines további információkért lásd: [SharePoint 2013-hoz készült Microsoft Azure architektúrák](https://technet.microsoft.com/library/dn635309.aspx) és [a SharePoint-környezet a Microsoft Azure-beli virtuális gépeken](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Futtassa a következő PowerShell-parancsot, amelyek tartalmazzák az "SQL" a szolgáltatás nevét a telepített szolgáltatások listája.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Általános javaslatok és ajánlott eljárások
* A minimális ajánlott méretet a virtuális gép **A3** SQL Server Enterprise kiadás használatakor. A **A4** virtuálisgép-méretet ajánlott az SQL Server BI Analysis Services és a Reporting Services telepítéseit.
  
    Az aktuális virtuális gépeinek kapcsolatos tudnivalókat lásd: [az Azure virtuálisgép-méretek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Adatok log és meghajtók biztonságimásolat-fájlok tárolására más, az ajánlott eljárás, a Lemezkezelés **C**: és **D**:. Hozzon létre például az adatlemezeket **E**: és **F**:.
  
  * A gyorsítótárazási házirend az alapértelmezett meghajtó meghajtó **C**: nem optimális adatokkal való munkához.
  * A **D**: meghajtó az egy ideiglenes meghajtó, amely elsősorban a lapozófájlt szolgál. A **D**: meghajtó nincs megőrizve, és nem menti a blob storage-bA. Kezelési feladatok, például a virtuális gép módosítás méretezés alaphelyzetbe állítása a **D**: meghajtó. Javasoljuk, hogy **nem** használja a **D**: meghajtó fájljaihoz, beleértve a TempDB adatbázisban.
    
    Lemezek létrehozása és további információkért lásd: [bemutatja, hogyan csatlakoztathat adatlemezt egy virtuális géphez](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Állítsa le vagy távolítsa el a szolgáltatásokat, hogy nem szeretné használni. Például ha a virtuális gép csak használja a Reporting Services leállítása, vagy távolítsa el az Analysis Services és az SQL Server Integration Services. Az alábbi képen egy példa a szolgáltatások alapértelmezés szerint az elindított.
  
    ![SQL Server-szolgáltatások](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > Az SQL Server adatbázismotor a támogatott forgatókönyvekben BI van szükség. A virtuális gép topológia, egyetlen kiszolgáló az adatbázismotor szükséges ugyanazon a virtuális Gépen futnak.
  
    További információkért tekintse meg a következőket: [Távolítsa el a Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) és [távolítsa el az Analysis Services egy példánya](https://msdn.microsoft.com/library/ms143687.aspx).
* Ellenőrizze **Windows Update** az új "fontos frissítések". A Microsoft Azure virtuálisgép-lemezképek gyakran frissülnek; azonban fontos frissítések válhat a rendelkezésre álló **Windows Update** után a Virtuálisgép-rendszerkép utolsó frissítésének.

## <a name="example-deployment-topologies"></a>A példában üzembe helyezési topológiák
Az alábbiakban például üzemelő példánya, amely a Microsoft Azure Virtual Machines használatához. Az alábbi ábrákon topológiák mindössze néhány, a lehetséges topológiákat használhatja az SQL Server BI funkciók és a Microsoft Azure Virtual Machines.

### <a name="single-virtual-machine"></a>Egyetlen virtuális gép
Analysis Services, Reporting Services, SQL Server adatbázismotor és adatforrások egyetlen virtuális gépen.

![1 virtuális gép az üzleti intelligencia számvitel forgatókönyv](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Két virtuális gép
* Analysis Services, a Reporting Services és az SQL Server adatbázismotor egyetlen virtuális gépen. A központi telepítés magában foglalja a jelentéskészítő kiszolgáló adatbázisának.
* A második virtuális gép adatforrások. A második virtuális gép tartalmazza az SQL Server adatbázismotor adatforrásként.

![2 virtuális gépekkel az üzleti intelligencia iaas-forgatókönyveknél](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Vegyes Azure-ban – Azure SQL-adatbázisban lévő adatok
* Analysis Services, a Reporting Services és az SQL Server adatbázismotor egyetlen virtuális gépen. A központi telepítés magában foglalja a jelentéskészítő kiszolgáló adatbázisának.
* Az adatforrás az Azure SQL database.

![üzleti intelligencia iaas forgatókönyvek vm és AzureSQL adatforrásként](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Hibrid – helyszíni adatok
* A központi telepítési példa a Analysis Services a Reporting Services és az SQL Server adatbázismotor futtassa, egyetlen virtuális gépen. A virtuális gépen fut a jelentéskészítő kiszolgáló adatbázisának. A virtuális gép csatlakozik egy helyszíni tartományban keresztül az Azure virtuális hálózat vagy tunneling megoldás néhány egyéb VPN.
* Az adatforrás helyszíni.

![üzleti intelligencia iaas-forgatókönyvek virtuális gép és a helyszíni adatforrások](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>A Reporting Services natív mód konfigurálása
A virtuálisgép-katalógus lemezkép az SQL Server Reporting Services natív üzemmódja telepítve, tartalmaz, azonban a jelentéskészítő kiszolgáló nincs konfigurálva. A jelen szakaszban ismertetett lépések a Reporting Services jelentéskészítő kiszolgáló konfigurálása. További részletes információ a Reporting Services natív üzemmódja konfigurálásáról lásd: [telepítése Reporting Services natív mód jelentéskészítő kiszolgáló (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Hasonló tartalmat, amely a Windows PowerShell-parancsfájlokat használ a jelentéskészítő kiszolgáló konfigurálása, lásd: [a PowerShell szolgáltatás használatával hozzon létre egy Azure virtuális gép és a jelentéskészítő kiszolgáló natív módjában](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>A virtuális gép csatlakozik, és a Reporting Services Configuration Manager indítása
Nincsenek két gyakori munkafolyamatokat egy Azure virtuális gépen való csatlakozáshoz:

* Kattintson a csatlakozás a virtuális gép nevét, majd **Connect**. A távoli asztali kapcsolat megnyitása és a rendszer automatikusan kitölti a számítógép nevét.
  
    ![Csatlakozás az azure virtuális géphez](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Csatlakozzon a virtuális géphez a Windows távoli asztali kapcsolat. A felhasználói felületen, a távoli asztal:
  
  1. Írja be a **felhőszolgáltatás neve** a számítógép neve.
  2. Írja be a kettőspont (:) és a nyilvános port számát, amely távoli asztali végpontot a TCP van konfigurálva.
     
      Myservice.cloudapp.net:63133
     
      További információkért lásd: [Mi a cloud service?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**A Reporting Services Configuration Manager indítása**

A **Windows Server 2012/2016**:

1. Az a **Start** írja be **Reporting Services** alkalmazások listájának megtekintéséhez.
2. Kattintson a jobb gombbal **Reporting Services Configuration Manager** kattintson **Futtatás rendszergazdaként**.

A **Windows Server 2008 R2**:

1. Kattintson a **Start**, és kattintson a **minden program**.
2. Kattintson a **Microsoft SQL Server 2016-ban**.
3. Kattintson a **konfigurációs eszközök**.
4. Kattintson a jobb gombbal **Reporting Services Configuration Manager** kattintson **Futtatás rendszergazdaként**.

Vagy:

1. Kattintson az **Indítás**gombra.
2. Az a **Keresés programokban és fájlokban** párbeszédpanelen írja be a **a reporting services**. Ha a virtuális gép fut a Windows Server 2012, írja be a **a reporting services** a a Windows Server 2012 kezdőképernyőjén.
3. Kattintson a jobb gombbal **Reporting Services Configuration Manager** kattintson **Futtatás rendszergazdaként**.
   
    ![Keresse meg az ssrs configuration Managerben](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Reporting Services konfigurálása
**Fiók és a web service URL-címe:**

1. Ellenőrizze a **kiszolgálónév** a helyi kiszolgáló nevét, majd kattintson a **Connect**.
2. Megjegyzés: az üres **jelentéskészítő kiszolgáló adatbázis neve**. A konfiguráció befejezése után az adatbázis létrehozása.
3. Ellenőrizze a **jelentéskészítő kiszolgáló állapotánál** van **elindítva**. Ha szeretné ellenőrizni a szolgáltatás a Windows Kiszolgálókezelő, a szolgáltatás nem a **SQL Server Reporting Services** Windows-szolgáltatás.
4. Kattintson a **szolgáltatásfiók** és szükség szerint változtassa meg a fiókhoz. Ha a virtuális gépet a tartományhoz nem csatlakoztatott környezetben, a beépített használatos **ReportServer** fiókja is elegendő. A szolgáltatás fiók további információkért lásd: [szolgáltatásfiók](https://msdn.microsoft.com/library/ms189964.aspx).
5. Kattintson a **Web Service URL-cím** a bal oldali panelen.
6. Kattintson a **alkalmaz** konfigurálhatja az alapértelmezett értékeket.
7. Megjegyzés: a **Web Service URL-eket jelentés**. Ne feledje, az alapértelmezett TCP-port a 80-as, és az URL-cím része. Egy későbbi lépésben hozzon létre egy Microsoft Azure virtuális gép végpontját a port.
8. Az a **eredmények** ablaktáblán ellenőrizze a műveletek sikeresen befejeződött.

**Adatbázis:**

1. Kattintson a **adatbázis** a bal oldali panelen.
2. Kattintson a **adatbázisként**.
3. Győződjön meg arról **hozzon létre egy új jelentéskészítő kiszolgáló adatbázisa** van kijelölve, majd kattintson a Tovább gombra.
4. Győződjön meg arról **kiszolgálónév** kattintson **kapcsolat tesztelése**.
5. Ha az eredmény **kapcsolat tesztelése sikeresen befejeződött**, kattintson a **OK** majd **tovább**.
6. Vegye figyelembe az adatbázis neve **ReportServer** és a **jelentéskészítő kiszolgáló mód** van **natív** kattintson **tovább**.
7. Kattintson a **tovább** a a **hitelesítő adatok** lapot.
8. Kattintson a **tovább** a a **összefoglalás** lapot.
9. Kattintson a **tovább** a a **halad, és a Befejezés** lapot.

**Webes portál URL-címe, vagy a Jelentéskezelő URL-címe 2012 és a 2014:**

1. Kattintson a **webes portál URL-cím**, vagy **Report Manager URL-cím** 2014 és 2012, a bal oldali panelen.
2. Kattintson az **Alkalmaz** gombra.
3. Az a **eredmények** ablaktáblán ellenőrizze a műveletek sikeresen befejeződött.
4. Kattintson a **kilépési**.

Jelentéskészítő kiszolgáló engedélyekről további információkért lásd: [engedélyek biztosítása a jelentéskészítő kiszolgáló natív módjában](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Keresse meg a helyi Jelentéskezelő
A konfiguráció ellenőrzéséhez keresse meg a Jelentéskezelő virtuális gépen.

1. A virtuális gépen indítsa el az Internet Explorer rendszergazdai jogosultságokkal.
2. Keresse meg a http: \/ /localhost/jelentések a virtuális gépen.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Csatlakozás a távoli webes portál vagy a Jelentéskezelő 2014 és 2012
Ha szeretne csatlakozni a webes portálon, vagy a Jelentéskezelő 2014 és a egy távoli számítógépről a virtuális gépen, 2012 hozzon létre egy új virtuális gép TCP-végpont. Alapértelmezés szerint a jelentéskészítő kiszolgáló figyeli a HTTP-kérelmekre a következő **80-as port**. Ha konfigurálja a jelentéskészítő URL egy másik port használatára, a port számát kell megadnia az alábbi utasításokat.

1. Hozzon létre egy végpontot a virtuális géphez a 80-as porton. További információ: a [virtuális gépek végpontjaihoz és Tűzfalportjait](#virtual-machine-endpoints-and-firewall-ports) szakasz ebben a dokumentumban.
2. Nyissa meg a 80-as portot a tűzfalon a virtuális gép.
3. Tallózással keresse meg a webes portálon, vagy a jelentés-kezelőt, az Azure virtuális gép **DNS-név** kiszolgálónevet az URL-címben. Példa:
   
    **Jelentéskészítő kiszolgáló**: http://uebi.cloudapp.net/reportserver  **Webes portál**: http://uebi.cloudapp.net/reports
   
    [Tűzfal konfigurálása a jelentéskészítő kiszolgáló elérése](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Hozhat létre és jelentések közzétételét az Azure virtuális gépen
Az alábbi táblázatban összefoglaltuk a helyi számítógépről a jelentéskészítő kiszolgálón, a Microsoft Azure virtuális gépen futó meglévő jelentések közzétételéhez elérhető beállításokról:

* **Jelentéskészítő**: A virtuális gépet tartalmaz, kattintson a – egyszer verzióját a Microsoft SQL Server jelentéskészítő SQL 2014 és a 2012. A jelentés jelentéskészítő az első alkalommal indítása az SQL 2016, a virtuális gépen:
  
  1. Rendszergazdai jogosultságokkal indítsa el a böngészőjében.
  2. Keresse meg a webes portálon, a virtuális gépen, és válassza ki a **letöltése** a jobb felső sarokban lévő ikonra.
  3. Válassza ki **jelentéskészítő**.
     
     További információkért lásd: [indítsa el a jelentéskészítő](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools**: VM:  SQL Server Data Tools a virtuális gépen telepítve van, és a létrehozásához használt **jelentéskészítő kiszolgáló projektek** és a jelentések a virtuális gépen. SQL Server Data Tools a jelentéseket tehetnek közzé a jelentéskészítő kiszolgáló a virtuális gépen.
* **SQL Server Data Tools: Távoli**:  A helyi számítógépen a Reporting Services projekt létrehozása az SQL Server Data Tools, amely tartalmazza a Reporting Services-jelentéseket. A projekt szeretne csatlakozni a web service URL-cím konfigurálása.
  
    ![ssdt project properties for SSRS project](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Hozzon létre egy. Virtuális merevlemez-meghajtó, amely jelentéseket tartalmazza, majd töltse fel és a meghajtó csatlakoztatása.
  
  1. Hozzon létre egy. Virtuális merevlemez a helyi számítógépen, amely tartalmazza a jelentésekben.
  2. Hozzon létre és telepítsen egy felügyeleti tanúsítványt.
  3. A VHD-fájl feltöltése az Azure-ban az Add-AzureVHD parancsmag [létrehozása és feltöltése az Azure-ban Windows Server VHD](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. A lemez csatolása a virtuális gépet.

## <a name="install-other-sql-server-services-and-features"></a>Más SQL Server-szolgáltatások és szolgáltatások telepítése
További SQL Server-szolgáltatásokat, például a táblázatos módban futó Analysis Services telepítéséhez futtassa az SQL-kiszolgáló telepítése varázsló. A telepítési fájlokat a virtuális gép helyi lemezen vannak.

1. Kattintson a **Start** majd **minden program**.
2. Kattintson a **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** vagy **Microsoft SQL Server 2012** majd **konfigurációs eszközök** .
3. Kattintson a **SQL Server telepítési központjának**.

Or run C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe or C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> Az első alkalommal futtatja az SQL Server telepítése a további telepítési fájlok letöltődik, és a virtuális gép újraindítását és az SQL Server-telepítés újraindítást igényelnek.
> 
> Ha ismételten szabni a lemezképet, a Microsoft Azure virtuális gép kiválasztva van szüksége, érdemes létrehozni a saját SQL Server-lemezképet. Analysis Services a SysPrep funkció az SQL Server 2012 SP1 CU2 lett engedélyezve. További információkért lásd: [telepíti az SQL Server SysPrep használata szempontjai](https://msdn.microsoft.com/library/ee210754.aspx) és [kiszolgálói szerepkörök támogatása a Sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Analysis Services táblázatos módban telepítése
A jelen szakaszban ismertetett lépések **összefoglalója** Analysis Services táblázatos módban a telepítését. További információkért tekintse meg a következőket:

* [Telepítse az Analysis Services táblázatos módban](https://msdn.microsoft.com/library/hh231722.aspx)
* [Táblázatos modellezési (az Adventure Works-oktatóanyag)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Analysis Services táblázatos módban telepítése:**

1. Az SQL Server telepítése varázslóban kattintson **telepítési** a bal oldali panelen, és kattintson a **új SQL server önálló telepítése vagy szolgáltatások hozzáadása egy meglévő telepítéshez**.
   
   * Ha megjelenik a **Tallózás a mappák**, c:\SQLServer_13.0_full, c:\SQLServer_12.0_full vagy c:\SQLServer_11.0_full keresse meg, és kattintson a **Ok**.
2. Kattintson a **tovább** a termék frissítések lapon.
3. Az a **telepítési típus** lapon jelölje be **az SQL Server új telepítés** kattintson **tovább**.
4. Az a **telepítési szerepkör** kattintson **SQL Server-szolgáltatások telepítése**.
5. Az a **szolgáltatás kiválasztása** kattintson **Analysis Services**.
6. Az a **példány konfigurációja** írja be egy beszédes nevet, például **táblázatos** be **megnevezett példány** és **példányazonosító** szövegmezők .
7. Az a **Analysis Services-konfiguráció** lapon jelölje be **táblázatos módban**. Adja hozzá az aktuális felhasználó rendszergazdai engedélyek listájához.
8. Végezze el, és zárja be az SQL Server telepítési varázsló.

## <a name="analysis-services-configuration"></a>Analysis Services-konfiguráció
### <a name="remote-access-to-analysis-services-server"></a>Az Analysis Services-kiszolgálóhoz távoli hozzáférést
Analysis Services-kiszolgáló csak a windows-hitelesítést támogatja. Analysis Services távoli kezeléséhez a tártallózónak az ügyfélalkalmazások, például az SQL Server Management Studio vagy SQL Server Data Tools, a virtuális géphez kell csatlakoztatni kell a helyi tartomány, az Azure virtuális hálózat használata. További információ: [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

A **alapértelmezett példány** a TCP-portot figyeli az Analysis Services **2383**. Nyissa meg a portot a virtuális gépek tűzfalon. Egy fürtözött nevű Analysis Services példánya is a porton **2383**.

Az egy **megnevezett példány** az Analysis Services, az SQL Server Browser szolgáltatás van szükség, a port hozzáférés kezeléséhez. Az SQL Server Browser alapértelmezett konfigurációs port **2382**.

A virtuális gépek tűzfalán nyissa meg a portot **2382** , és hozzon létre egy statikus Analysis Services példány portja nevű.

1. Ellenőrizheti a portokat, amelyek már használatban van a virtuális Gépet, és melyik folyamat használja a portokat, rendszergazdai jogosultságokkal futtassa a következő parancsot:
   
        netstat /ao
2. Az SQL Server Management Studio hozhat létre egy statikus Analysis Services megnevezett példány portja frissítésével "Port" érték a táblázatos AS példány általános tulajdonságok. További információkért lásd a "rögzített port használatára egy alapértelmezett vagy megnevezett példány" a [Analysis Services hozzáférés engedélyezése a Windows tűzfal beállításainak](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Indítsa újra az Analysis Services táblázatos szolgáltatáspéldányt.

További információ: a **virtuális gépek végpontjaihoz és Tűzfalportjait** szakasz ebben a dokumentumban.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Virtuális gépek végpontjaihoz és a tűzfal portok
Ez a szakasz a Microsoft Azure virtuális gépek végpontjaihoz hozhat létre és nyissa meg a virtuális gép tűzfalak, portok foglalja össze. A következő táblázat összefoglalja a **TCP** portok a végpontok létrehozásához és a portok a virtuális gépek tűzfalban való megnyitásáról.

* Egyetlen virtuális Gépet használ, és a következő két elemet igaz, ha nem szeretne létrehozni a virtuális gépek végpontjaihoz, és nem kell a portok megnyitása a tűzfalon a virtuális gépen.
  
  * Ön nem távolról csatlakozni a virtuális gépen az SQL Server szolgáltatást. A virtuális Gépet egy távoli asztali kapcsolatot létrehozó és az SQL Server funkcióinak helyileg, a virtuális gép nem számít az SQL Server szolgáltatást egy távoli kapcsolatot.
  * A virtuális gép nem csatlakozik a szolgáltatáshoz az Azure virtuális hálózat vagy egy másik VPN-alagutak megoldás a helyi tartományhoz.
* Ha a virtuális gép nem csatlakozik tartományhoz, de szeretné távolról csatlakozhat a virtuális Gépen található SQL Server-funkciókat:
  
  * Nyissa meg a portokat a tűzfalon a virtuális gépen.
  * Hozzon létre virtuális gépek végpontjaihoz a felmerült portok (*).
* Ha a virtuális gép csatlakozik egy tartományhoz, például az Azure virtuális hálózat VPN-alagút használatával, majd a végpontok nem szükségesek. Azonban a portok megnyitása a virtuális gépen.
  
  | Port | Typo | Leírás |
  | --- | --- | --- |
  | **80** |TCP |Jelentéskészítő kiszolgáló távelérés (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |SQL Server Browser bővítményt. Erre azért van szükség, ha a virtuális gép csatlakozik egy tartományhoz. |
  | **2382** |TCP |SQL Server Browser bővítményt. |
  | **2383** |TCP |SQL Server Analysis Services alapértelmezett példány, és fürtözött elnevezett példányok. |
  | **felhasználó által definiált** |TCP |Hozzon létre egy statikus Analysis Services példány portja nevű választja portszámot, és a port számát, a tűzfal majd feloldása. |

Végpontok létrehozásával kapcsolatos további információkért tekintse meg a következőket:

* Végpontok létrehozása:[egy virtuális géphez végpontok beállítása](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* SQL Server: "A teljes konfiguráció szeretne csatlakozni a virtual machine Using SQL Server Management Studio lépések" című szakaszában talál [kiépítése egy SQL Server virtuális gépet az Azure-ban](../sql/virtual-machines-windows-portal-sql-server-provision.md).

A következő ábra szemlélteti a portok megnyitása a virtuális gép tűzfalon való funkcióinak és összetevőinek távoli hozzáférés engedélyezése a virtuális gépen.

![Nyissa meg a bi-alkalmazások Azure-beli virtuális portok](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>További források
* Tekintse át a támogatási szabályzata a Microsoft kiszolgálói szoftverek az Azure virtuális gép környezetben használja. A következő témakör összefoglalja, például a BitLocker, a Feladatátvételi fürtszolgáltatás és hálózati terheléselosztási funkciók támogatása. [Microsoft kiszolgálószoftveres támogatás az Azure Virtual Machines](https://support.microsoft.com/kb/2721672).
* [Az SQL Server használata az Azure Virtual Machines – áttekintés](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Virtuális gépek](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Az Azure-ban az SQL Server virtuális gép kiépítése](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Bemutatja, hogyan csatlakoztathat adatlemezt egy virtuális géphez](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Egy Azure virtuális Gépen futó SQL Server-adatbázis áttelepítése](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [A kiszolgáló mód az Analysis Services-példány meghatározása](https://msdn.microsoft.com/library/gg471594.aspx)
* [Többdimenziós modellezési (az Adventure Works-oktatóanyag)](https://technet.microsoft.com/library/ms170208.aspx)
* [Az Azure dokumentációs központ](https://azure.microsoft.com/documentation/)
* [A Power BI használatával hibrid környezetben](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Visszajelzés és a kapcsolattartási adatokat a Microsoft SQL Server Connect keresztül beküldése](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Közösségi tartalom
* [Az Azure SQL Database kezelése a PowerShell segítségével](https://azure.microsoft.com/blog/windows-azure-sql-database-management-with-powershell/)

