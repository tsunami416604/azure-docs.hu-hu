---
title: SQL Server Business Intelligence |} Microsoft Docs
description: "Ez a témakör a klasszikus üzembe helyezési modellel létrehozott erőforrást használ, és az SQL Server rendszert futtató Azure virtuális gépek (VM) rendelkezésre álló üzleti Üzletiintelligencia-funkcióit mutatja be."
services: virtual-machines-windows
documentationcenter: na
author: guyinacube
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
ms.author: asaxton
ms.openlocfilehash: 65bada117e7d005362b0ac0ce7cc5336a92e0889
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>Az SQL Server Business Intelligence használata Azure-beli virtuális gépeken
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

A Microsoft Azure virtuális gép az SQL Server telepített tartalmazó lemezképek rendelkezik. A gyűjtemény képek támogatott SQL Server különböző kiadásaiban is ugyanazokat a telepítési fájlokat a helyszíni számítógépek és a virtuális gépek telepítése. Ez a témakör felsorolja a képek a telepített SQL Server Business Üzletiintelligencia-szolgáltatások és a virtuális gép kiépítése után szükséges konfigurációs lépéseket. Ez a témakör ismerteti a támogatott üzembe helyezési topológiák BI-funkciókat és ajánlott eljárások is.

## <a name="license-considerations"></a>Licenc kapcsolatos szempontok
SQL Server a Microsoft Azure virtuális gépek licencre két módja van:

1. Licenc mobilitási előnyeit, amelyek részei Software Assurance. További információkért lásd: [Azure frissítési garancián keresztüli Licenchordozhatósági](https://azure.microsoft.com/pricing/license-mobility/).
2. Az Azure virtuális gépek, a telepített SQL Server feldolgozási sebessége nagy. A "SQL Server" című [Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

A licencelési és a jelenlegi díjszabás további információkért lásd: [virtuális gépek Licensing FAQ](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server-rendszerképeit elérhető Azure virtuálisgép-katalógusban
A Microsoft Azure virtuális gép számos olyan rendszerkép található, amely tartalmazza a Microsoft SQL Server rendelkezik. A virtuálisgép-rendszerképek telepített szoftvereket a az operációs rendszer és az SQL Server verziójától függ. Az Azure virtuális gépek oldalon elérhető képek listájának gyakran változik.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![SQL-lemezképet Azure virtuális gép gyűjteménye](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) A következő PowerShell-parancsfájl az Azure-rendszerképek "SQL-kiszolgáló" a ImageName a tartalmazó listát ad vissza:

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

Kiadásainak és az SQL Server által támogatott szolgáltatások további információkért tekintse át a következőket:

* [SQL Server kiadásai](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)
* [Az SQL Server 2016 kiadásai által támogatott szolgáltatások](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Üzleti INTELLIGENCIA szolgáltatásokat az SQL Server virtuálisgép-katalógusban lemezképeket telepítve
A következő táblázat összefoglalja a közös Microsoft Azure virtuális gépen lévő képek telepíthető az SQL Server Business Intelligence szolgáltatások:

* SQL Server 2016 SP1 Enterprise
* Az SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* Az SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* Az SQL Server 2012 SP3 Standard

| SQL Server BI szolgáltatás | A gyűjtemény kép telepítve | Megjegyzések |
| --- | --- | --- |
| **Jelentéskészítési szolgáltatások natív mód** |Igen |Telepített, de a konfiguráció – beleértve a jelentés manager URL-címet igényel. Című témakör [Reporting Services konfigurálása](#configure-reporting-services). |
| **Reporting Services – SharePoint módban** |Nem |A Microsoft Azure virtuális gép gyűjteménye a rendszerkép nem tartalmaz a SharePoint vagy a SharePoint telepítési fájlok. <sup>1</sup> |
| **Analysis Services többdimenziós és az adatok adatbányászati (OLAP)** |Igen |Telepítve és konfigurálva, az alapértelmezett Analysis Services-példány |
| **Analysis Services táblázatos** |Nem |Támogatott az SQL Server 2012, 2014 és 2016 képek, de az nincs telepítve alapértelmezés szerint. Egy másik Analysis Services-példány telepítését. Lásd az egyéb SQL Server-szolgáltatások és szolgáltatások telepítése ebben a témakörben. |
| **Analysis Services PowerPivot sharepointhoz** |Nem |A Microsoft Azure virtuális gép gyűjteménye a rendszerkép nem tartalmaz a SharePoint vagy a SharePoint telepítési fájlok. <sup>1</sup> |

<sup>1</sup> kapcsolatban további tájékoztatást a SharePoint és az Azure virtuális gépek [a SharePoint 2013-hoz a Microsoft Azure architektúrák](https://technet.microsoft.com/library/dn635309.aspx) és [SharePoint telepítési Microsoft Azure virtuális gépeken](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Futtassa a következő PowerShell-parancsot, amely tartalmazza az "SQL" a szolgáltatás nevét a telepített szolgáltatások listáját.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Általános javaslatok és ajánlott eljárások
* A minimális méretnél egy virtuális gép **A3** SQL Server Enterprise Edition használatakor. A **A4** virtuálisgép-méret ajánlott az SQL Server BI Analysis Services és a Reporting Services telepítéséhez.
  
    Az aktuális Virtuálisgép-méretek információkért lásd: [Azure virtuálisgép-méretek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Lemezkezelés esetén ajánlott eljárás az adatokat, a napló és a meghajtók biztonságimásolat-fájlok tárolására eltérő **C**: és **D**:. Hozzon létre például egy adatlemezt **E**: és **F**:.
  
  * A gyorsítótárazási házirend az alapértelmezett meghajtó meghajtó **C**: nincs optimalizálva az adatai.
  * A **D**: elsősorban a lapozófájlt használt ideiglenes meghajtó. A **D**: meghajtó nem őrzi meg, és nem menti a blob Storage tárolóban. Felügyeleti feladatokhoz, mint a Módosítás gombra a virtuális gép méretének visszaállítása a **D**: meghajtó. Javasoljuk, hogy **nem** használja a **D**: adatbázis-fájlok, beleértve a tempdb meghajtó.
    
    A lemezek létrehozása és további információkért lásd: [hogyan lehet adatlemezt csatolni egy virtuális gép](../classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Állítsa le, vagy távolítsa el a szolgáltatások nem tervezi használni. Például ha a virtuális gép csak a Reporting Serviceshez, állítsa le vagy távolítsa el az Analysis Services és az SQL Server Integration Services. Az alábbi képen a szolgáltatások alapértelmezés szerint az elindított példája.
  
    ![SQL Server-szolgáltatások](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > Az SQL Server adatbázismotor a támogatott BI esetekben szükséges. A virtuális gép topológia, egyetlen kiszolgáló az adatbázismotor szükséges az azonos virtuális gépen kell futnia.
  
    További információkért tekintse meg a következőt: [Reporting Services eltávolítása](https://msdn.microsoft.com/library/hh479745.aspx) és [távolítsa el az Analysis Services példány](https://msdn.microsoft.com/library/ms143687.aspx).
* Ellenőrizze **Windows Update** az új "fontos frissítések". A Microsoft Azure virtuális gép képfájljait gyakran frissülnek; azonban a fontos frissítések válhat elérhető **Windows Update** után utolsó frissítésének a Virtuálisgép-lemezkép.

## <a name="example-deployment-topologies"></a>Példa üzembe helyezési topológiák
Az alábbiakban például a Microsoft Azure virtuális gépek használó központi telepítések. Az alábbi ábrákon topológiák a lehetséges topológiákat használhatja az SQL Server BI szolgáltatás és a Microsoft Azure virtuális gépek csak közé tartoznak.

### <a name="single-virtual-machine"></a>Egyetlen virtuális gép
Analysis Services, Reporting Services, az SQL Server adatbázismotor és az adatforrások egy virtuális gépen.

![üzleti intelligencia számvitel a forgatókönyvet, és 1 virtuális gép](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Két virtuális gép
* Analysis Services, a Reporting Services és az SQL Server adatbázismotor egy virtuális gépen. A központi telepítés tartalmazza a jelentés server-adatbázisokat.
* A második virtuális gép adatforrások. A második virtuális gép az SQL Server adatbázismotor adatforrásként tartalmazza.

![üzleti intelligencia iaas forgatókönyv 2 virtuális gépekkel](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Vegyes Azure – adatok az Azure SQL-adatbázis
* Analysis Services, a Reporting Services és az SQL Server adatbázismotor egy virtuális gépen. A központi telepítés tartalmazza a jelentés server-adatbázisokat.
* Az adatforrás az Azure SQL database.

![üzleti intelligencia iaas forgatókönyvek vm és adatforrásként AzureSQL](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Hibrid – helyszíni adatok
* A központi telepítési példánkban Analysis Services és a Reporting Services, az SQL Server adatbázismotor futtassa egy virtuális gépen. A virtuális gépen fut a jelentéskészítő kiszolgáló adatbázisok. A virtuális gép csatlakozik egy helyszíni tartományban Azure virtuális hálózaton keresztül, vagy néhány más VPN-megoldás tunneling.
* Az adatforrás a helyszíni.

![üzleti intelligencia iaas-forgatókönyvek virtuális gép és a helyszíni adatforrások](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>A Reporting Services natív mód konfigurálása
A virtuális gép gyűjtemény kép az SQL Server jelentéskészítési szolgáltatások natív mód telepítve, tartalmazza, azonban a jelentéskészítő kiszolgáló nincs konfigurálva. A jelen szakaszban szereplő lépéseket a Reporting Services jelentéskészítő kiszolgáló konfigurálása. További információk a jelentéskészítési szolgáltatások natív mód beállítása, a következő témakörben: [telepítése Reporting Services natív mód jelentéskészítő kiszolgáló (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> A jelentéskészítő kiszolgáló konfigurálása a Windows PowerShell-parancsfájlok használó hasonló tartalom, lásd: [a PowerShell szolgáltatás használatával hozzon létre egy Azure virtuális gép, egy natív mód jelentéskészítő kiszolgáló](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Csatlakozzon a virtuális géphez, és indítsa el a Reporting Services Konfigurációkezelő
Nincsenek két gyakori munkafolyamat egy Azure virtuális géphez való csatlakozáshoz:

* Kattintson a csatlakozás a virtuális gép nevét, majd **Connect**. A távoli asztali kapcsolat megnyitása, és automatikusan kitölti a rendszer a számítógép nevét.
  
    ![Csatlakozás az azure virtuális géphez](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Csatlakozzon a virtuális géphez a Windows távoli asztali kapcsolat. A távoli asztal a felhasználói felületen:
  
  1. Típus a **felhőszolgáltatás neve** a számítógép neve.
  2. Írja be a kettőspont (:) és a nyilvános port számát, az TCP-távoli asztali végpont konfigurált.
     
      Myservice.cloudapp.NET:63133
     
      További információkért lásd: [Mi az a cloud service?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**Indítsa el a Reporting Services Konfigurációkezelő**

A **Windows Server 2012/2016**:

1. Az a **Start** írja be **Reporting Services** az alkalmazások listájának megtekintéséhez.
2. Kattintson a jobb gombbal **Reporting Services Configuration Manager** kattintson **Futtatás rendszergazdaként**.

A **Windows Server 2008 R2**:

1. Kattintson a **Start**, és kattintson a **minden program**.
2. Kattintson a **Microsoft SQL Server 2016**.
3. Kattintson a **konfigurációs eszközök**.
4. Kattintson a jobb gombbal **Reporting Services Configuration Manager** kattintson **Futtatás rendszergazdaként**.

Vagy:

1. Kattintson a **Start**.
2. Az a **Keresés programokban és fájlokban** párbeszédpanelen írja be a **a reporting services**. Ha a virtuális gép fut a Windows Server 2012, írja be a **a reporting services** a Windows Server 2012 kezdőképernyőn.
3. Kattintson a jobb gombbal **Reporting Services Configuration Manager** kattintson **Futtatás rendszergazdaként**.
   
    ![Keresse meg az ssrs configuration Managerben](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Reporting Services konfigurálásához
**Szolgáltatásfiók és a webszolgáltatás URL-címe:**

1. Ellenőrizze a **kiszolgálónév** a helyi kiszolgáló nevét, majd kattintson az **Connect**.
2. Vegye figyelembe a üres **jelentéskészítő kiszolgáló adatbázisának neve**. Az adatbázis létrehozása a konfiguráció befejezését.
3. Ellenőrizze a **jelentéskészítő kiszolgáló állapota** van **elindítva**. Ha szeretné ellenőrizni a szolgáltatás a Windows Kiszolgálókezelő, a szolgáltatás nem a **SQL Server Reporting Services** Windows-szolgáltatás.
4. Kattintson a **szolgáltatásfiók** és szükség szerint módosíthatja azt a fiókot. A virtuális gép használata a tartományhoz nem csatlakoztatott környezetben, a beépített **ReportServer** fiókja is elegendő. A szolgáltatás fiók további információkért lásd: [szolgáltatásfiók](https://msdn.microsoft.com/library/ms189964.aspx).
5. Kattintson a **webes szolgáltatás URL-címe** a bal oldali ablaktáblán.
6. Kattintson a **alkalmaz** konfigurálhatja az alapértelmezett értékeket.
7. Megjegyzés: a **jelentés webes szolgáltatás URL-eket**. Jegyezze fel, az alapértelmezett TCP-port 80-as és az URL-cím része. Egy későbbi lépésben hozzon létre egy Microsoft Azure virtuális gép végpontjának a port.
8. Az a **eredmények** ablaktáblában ellenőrizze a művelet sikeresen befejeződött.

**Adatbázis:**

1. Kattintson a **adatbázis** a bal oldali ablaktáblán.
2. Kattintson a **adatbázis**.
3. Győződjön meg arról **hozzon létre egy új jelentéskészítő kiszolgáló adatbázisában** van kijelölve, majd kattintson a Tovább gombra.
4. Győződjön meg arról **kiszolgálónév** kattintson **kapcsolat tesztelése**.
5. Ha az eredmény **kapcsolat tesztelése sikerült**, kattintson a **OK** majd **következő**.
6. Megjegyzés: az adatbázis neve **ReportServer** és a **jelentéskészítő kiszolgáló mód** van **natív** kattintson **következő**.
7. Kattintson a **következő** a a **hitelesítő adatok** lap.
8. Kattintson a **következő** a a **összegzés** lap.
9. Kattintson a **következő** a a **előrehaladás és befejezési** lap.

**Webes portál URL-címe vagy a Jelentéskezelő URL-címe 2012 és a 2014:**

1. Kattintson a **webes portál URL-CÍMÉT**, vagy **Jelentéskezelő URL-címe** 2014 és 2012, a bal oldali ablaktáblán.
2. Kattintson az **Alkalmaz** gombra.
3. Az a **eredmények** ablaktáblában ellenőrizze a művelet sikeresen befejeződött.
4. Kattintson a **kilépési**.

A jelentéskészítő kiszolgáló engedélyek információkért lásd: [engedélyek megadása natív mód jelentéskészítő kiszolgálón](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Keresse meg a helyi Jelentéskezelő
Ellenőrizze a konfigurációt, navigáljon a Jelentéskezelő a virtuális Gépen.

1. A virtuális Gépre indítsa el az Internet Explorer rendszergazdai jogosultságokkal.
2. Tallózással keresse meg a virtuális Gépre http://localhost/reports.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Távoli webes portál vagy a Jelentéskezelő 2014 és 2012 való csatlakozáshoz
Ha azt szeretné csatlakoztatni a webes portál vagy a Jelentéskezelő 2014 és a 2012-ben a távoli számítógépről, a virtuális gépen a új virtuális gép TCP-végpont létrehozása. Alapértelmezés szerint a jelentéskészítő kiszolgáló figyel a HTTP-kérelmek **80-as port**. Ha a jelentéskészítő kiszolgáló URL-címei, egy másik port használatára konfigurálja, eltérő portszámot kell megadnia az alábbi utasításokat.

1. Hozzon létre egy végpontot a virtuális gép a 80-as TCP-porton. További információ: a [virtuális gép végpontok és tűzfalportok](#virtual-machine-endpoints-and-firewall-ports) szakasz ebben a dokumentumban.
2. Nyissa meg a 80-as portot a tűzfalon a virtuális gép.
3. Keresse meg a webes portálhoz, vagy a jelentés-kezelőt, Azure virtuális gép **DNS-név** a kiszolgáló neve, az URL-címben. Példa:
   
    **Jelentéskészítő kiszolgáló**: http://uebi.cloudapp.net/reportserver **webes portál**: http://uebi.cloudapp.net/reports
   
    [Beállítani a tűzfalat a jelentéskészítő kiszolgáló elérése](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Hozzon létre, és a jelentések közzététele az Azure virtuális géphez
A következő táblázat összefoglalja az egyes lehetőségekről a helyi számítógépről a jelentéskészítő kiszolgálón található meg a Microsoft Azure virtuális gép meglévő jelentések közzététele érdekében:

* **Jelentéskészítő**: A virtuális gépet tartalmaz, kattintson a-Microsoft SQL Server jelentéskészítő az SQL 2014 és az 2012 egyszer verzióját. Elindítja a jelentés jelentéskészítő első alkalommal a virtuális gép az SQL 2016:
  
  1. A böngészőben rendszergazdai jogosultságokkal.
  2. Keresse meg a webes portál, a virtuális gépen, és válassza ki a **letöltése** jobb felső részén ikonra.
  3. Válassza ki **jelentéskészítő**.
     
     További információkért lásd: [Start jelentéskészítő](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools**: virtuális gép: SQL Server Data Tools a virtuális gépre van telepítve, és segítségével hozzon létre **jelentés Server projektek** és a jelentések a virtuális gépen. SQL Server Data Tools közzéteheti a jelentéseket a jelentéskészítő kiszolgáló, a virtuális gépen.
* **SQL Server Data Tools: Távoli**: A helyi számítógépen, a Reporting Services-projekt létrehozása az SQL Server Data Tools összetevővel, amely tartalmazza a Reporting Services-jelentéseket. A webes szolgáltatás URL-Címéhez való kapcsolódáshoz a projekt konfigurálásához.
  
    ![SSRS-projekt SSDT projektjének tulajdonságai](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Hozzon létre egy. Amely kapcsolatos jelentéseket tartalmazza, majd töltse fel és a meghajtó csatlakoztatása virtuális merevlemezen.
  
  1. Hozzon létre egy. Virtuális merevlemez-meghajtóról a helyi számítógépen, amely tartalmazza a jelentéseket.
  2. Hozzon létre és telepítsen egy felügyeleti tanúsítványt.
  3. A VHD-fájl feltöltése az Azure-bA az Add-AzureVHD parancsmaggal [az Azure-ba, a Windows Server VHD létrehozása és feltöltése](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. A lemez csatolása a virtuális géphez.

## <a name="install-other-sql-server-services-and-features"></a>Más SQL Server-szolgáltatások és szolgáltatások telepítése
További SQL Server-szolgáltatásokat, például az Analysis Services táblázatos módban telepítéséhez futtassa az SQL-kiszolgáló telepítése varázsló. A telepítési fájlokat a virtuális gép helyi lemezen vannak.

1. Kattintson a **Start** majd **minden program**.
2. Kattintson a **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** vagy **Microsoft SQL Server 2012** majd **konfigurációs eszközök**.
3. Kattintson a **SQL Server telepítési központjának**.

Vagy C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe vagy C:\SQLServer_11.0_full\setup.exe futtatása

> [!NOTE]
> Az SQL Server telepítése az első futtatásakor további telepítési fájlokat le lehet tölteni, és a virtuális gép újraindítását és újra kell indítania az SQL Server telepítése szükséges.
> 
> Ha szeretné ismételten szabni a lemezképet, a Microsoft Azure virtuális gép választott, célszerű a saját SQL Server-rendszerképet. Analysis Services SysPrep funkciót az SQL Server 2012 SP1 CU2 volt engedélyezve. További információkért lásd: [telepítése az SQL Server SysPrep használata szempontjai](https://msdn.microsoft.com/library/ee210754.aspx) és [Sysprep támogatási kiszolgálói szerepköre tekintetében](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Analysis Services táblázatos módban telepítése
A jelen szakaszban szereplő lépéseket **összefoglalója** Analysis Services táblázatos módban a telepítését. További információkért tekintse át a következőket:

* [Telepítse az Analysis Services táblázatos módban](https://msdn.microsoft.com/library/hh231722.aspx)
* [A táblázatos modellezési (Adventure Works oktatóanyag)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Analysis Services táblázatos módban telepítése:**

1. Az SQL Server telepítése varázslóban kattintson **telepítési** a bal oldali ablaktáblán, és kattintson a **új SQL server önálló telepítése vagy szolgáltatások hozzáadása egy meglévő telepítéshez**.
   
   * Ha megjelenik a **Tallózás a mappák**, keresse meg a c:\SQLServer_13.0_full, c:\SQLServer_12.0_full vagy c:\SQLServer_11.0_full, és kattintson a **Ok**.
2. Kattintson a **tovább** a termék frissítések lapon.
3. Az a **telepítési típus** lapon jelölje be **az SQL Server új telepítés** kattintson **következő**.
4. Az a **telepítési szerepkör** kattintson **SQL Server-szolgáltatások telepítése**.
5. Az a **szolgáltatásválasztást** kattintson **Analysis Services**.
6. Az a **példány konfigurációja** írja be egy nevet, például a **táblázatos** be **nevű példány** és **példányazonosító** szövegmezőket.
7. Az a **Analysis Services konfigurációs** lapon jelölje be **táblázatos módban**. Adja hozzá az aktuális felhasználó rendszergazdai engedélyek listájához.
8. Végezze el, és zárja be az SQL Server telepítővarázsló.

## <a name="analysis-services-configuration"></a>Az Analysis Services beállítása
### <a name="remote-access-to-analysis-services-server"></a>Az Analysis Services-kiszolgálóhoz távoli hozzáférést
Analysis Services-kiszolgáló csak a windows-hitelesítést támogatja. Távolról elérje ügyfélalkalmazásokat, például az SQL Server Management Studio vagy SQL Server Data Tools Analysis Services, a virtuális géphez kell csatlakoztatni kell a helyi tartományhoz, Azure virtuális hálózat használata. További információ: [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

A **alapértelmezett példány** TCP porton figyel az Analysis Services **2383**. Nyissa meg a portot a virtuális gépek tűzfalon. Egy fürtözött nevű Analysis Services-példány is figyeli a porton **2383**.

Az egy **megnevezett példány** az Analysis Services, az SQL Server tallózó szolgáltatásának van szükség, a port hozzáférés kezeléséhez. Az SQL Server Browser alapértelmezett konfigurálás az port **2382**.

Nyissa meg a virtuális gépek tűzfal port **2382** , és hozzon létre egy statikus Analysis Services példány port neve.

1. Ellenőrizze a portokat, amelyeket már használnak a virtuális Gépet, és melyik folyamat használja a portokat, a következő paranccsal rendszergazdai jogosultságokkal:
   
        netstat /ao
2. Használjon SQL Server Management Studio létrehozása egy statikus Analysis Services nevű példány port "Port" frissítésével táblázatos AS értéket példány általános tulajdonságok. További információkért lásd a "rögzített port használatára egy alapértelmezett vagy megnevezett példány" a [Analysis Services hozzáférés engedélyezése a Windows tűzfal beállításainak](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Indítsa újra az Analysis Services szolgáltatás táblázatos példányát.

További információ: a **virtuális gép végpontok és tűzfalportok** szakasz ebben a dokumentumban.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Virtuális gép végpontokat és a tűzfal portok
Ez a szakasz a Microsoft Azure virtuális gép végpontok létrehozásához és, hogy nyissa meg a virtuális gép tűzfalak foglalja össze. A következő táblázat összefoglalja a **TCP** végpontjainak létrehozásához és a portokkal a virtuális gépek tűzfal megnyitásához.

* Egy virtuális használ, és teljesül a következő két elemet, ha nem szeretne létrehozni a virtuális gép végpontok, és nem kell a a virtuális Gépen a portok megnyitása.
  
  * Nem távolról csatlakozhat az SQL Server szolgáltatásokat a virtuális Gépen. A virtuális gép egy távoli asztali kapcsolatot létesít, és az SQL Server funkcióinak helyileg a virtuális Gépen nem számít az SQL Server szolgáltatásai távoli kapcsolatot.
  * A virtuális gép nem csatlakoztatása az Azure virtuális hálózat vagy egy másik VPN-bújtatás megoldás helyszíni tartományokhoz.
* Ha a virtuális gép nem csatlakozik egy tartományhoz, de szeretné távolról kapcsolódni az SQL Server szolgáltatásokat a virtuális Gépen:
  
  * Nyissa meg a portokat a tűzfalon a virtuális Gépen.
  * Hozzon létre virtuális gép végpontok a beállításértékeket portok (*).
* Ha a virtuális gép csatlakozik egy tartományhoz, a VPN-alagút, például az Azure virtuális hálózat használatával, majd a végpontok esetén nincs szükség. Azonban a portok megnyitása a a virtuális Gépen.
  
  | Port | Típus | Leírás |
  | --- | --- | --- |
  | **80** |TCP |Jelentéskészítő kiszolgáló távelérés (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |SQL Server Browser bővítményt. Ez szükséges, ha a virtuális gép csatlakozik egy tartományhoz. |
  | **2382** |TCP |SQL Server Browser bővítményt. |
  | **2383** |TCP |SQL Server Analysis Services alapértelmezett példány, fürtözött elnevezett példányok. |
  | **Felhasználó által definiált** |TCP |Hozzon létre egy statikus Analysis Services példány port nevű úgy dönt, a portszámot, és a port számát, a tűzfal majd feloldása. |

Végpontok létrehozásával kapcsolatos további információkért tekintse át a következőket:

* Hozza létre a végpontok:[üzembe helyezése a virtuális gépek végpontok](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* SQL Server: A "Csatlakozni a virtuális gép használata az SQL Server Management Studio konfigurációs lépések" című szakaszában talál [Azure SQL Server virtuális gépek kiépítése](../sql/virtual-machines-windows-portal-sql-server-provision.md).

A következő ábra szemlélteti a portokat nyissa meg a virtuális gép tűzfal távoli hozzáférés lehetővé tételéhez szolgáltatásait és összetevőit a virtuális Gépen.

![üzleti intelligencia alkalmazások az Azure virtuális gépeken megnyitandó portokról](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Erőforrások
* Microsoft server szoftver az Azure virtuális gép környezetben használt terméktámogatási irányelveiből. A következő témakör összefoglalja, például a BitLocker, a Feladatátvételi fürtszolgáltatás és a hálózati terheléselosztás szolgáltatás támogatását. [Microsoft server szoftver támogatása az Azure virtuális gépek](http://support.microsoft.com/kb/2721672).
* [SQL Server Azure virtuális gépek – áttekintés](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Az Azure SQL Server virtuális gépek kiépítése](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Hogyan lehet adatlemezt csatolni egy virtuális géphez](../classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Az Azure virtuális gép az SQL Server-adatbázis migrálása](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [A kiszolgálói módú Analysis Services példány meghatározása](https://msdn.microsoft.com/library/gg471594.aspx)
* [Többdimenziós modellezési (Adventure Works oktatóanyag)](https://technet.microsoft.com/library/ms170208.aspx)
* [Azure dokumentációs központban](https://azure.microsoft.com/documentation/)
* [Hibrid környezetben a Power BI használatával](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Küldje el visszajelzését és kapcsolattartási adatokat a Microsoft SQL Server-csatlakozás](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Közösségi tartalom
* [Az Azure SQL-adatbázisok felügyelete-a PowerShell használatával](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)

