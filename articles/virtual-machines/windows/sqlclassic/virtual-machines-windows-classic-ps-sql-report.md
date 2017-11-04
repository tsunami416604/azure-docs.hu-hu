---
title: "Natív üzemmódú jelentéskészítő kiszolgálón hozzon létre egy virtuális Gépet a PowerShell használatával |} Microsoft Docs"
description: "Ez a témakör ismerteti, és bemutatja, hogyan telepítését és konfigurálását az SQL Server Reporting Services natív üzemmódú jelentéskészítő kiszolgáló egy Azure virtuális gépen. "
services: virtual-machines-windows
documentationcenter: na
author: guyinacube
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 553af55b-d02e-4e32-904c-682bfa20fa0f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: asaxton
ms.openlocfilehash: 5e5c11251cd316e8161dbe362b300be76927ac01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Natív üzemmódú jelentéskészítő kiszolgálót futtató Azure-beli virtuális gép létrehozása a PowerShell-lel
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ez a témakör ismerteti, és bemutatja, hogyan telepítését és konfigurálását az SQL Server Reporting Services natív üzemmódú jelentéskészítő kiszolgáló egy Azure virtuális gépen. A jelen dokumentumban leírt lépések manuális lépések kombinálhatja a virtuális gép és a Reporting Services konfigurálásához a virtuális Gépet a Windows PowerShell-parancsfájl létrehozásához. A konfigurációs parancsfájl tartalmazza a HTTP és HTTPs a tűzfal port megnyitása.

> [!NOTE]
> Ha nincs szüksége **HTTPS** a jelentéskészítő kiszolgálón **a 2**.
> 
> Miután létrehozta a virtuális gép az 1. lépésben, keresse a parancsfájl használata a jelentéskészítő kiszolgáló és a HTTP konfigurálása. A parancsfájl futtatása után a jelentéskészítő kiszolgáló használatra készen áll.

## <a name="prerequisites-and-assumptions"></a>Előfeltételek és Előfeltételek
* **Azure-előfizetés**: Ellenőrizze az elérhető az Azure-előfizetésében magok száma. Ha létrehozta az ajánlott Virtuálisgép-méretet **A3**, kell **4** rendelkezésre álló magot. Ha egy Virtuálisgép-méretet **A2**, kell **2** rendelkezésre álló magot.
  
  * Ellenőrizze a core korlátot az előfizetéséhez, a klasszikus Azure portálon, kattintson a beállítások gombra a bal oldali ablaktáblán, majd kattintson a használati a felső menüben.
  * A core kvóta növeléséhez forduljon [Azure támogatási](https://azure.microsoft.com/support/options/). Virtuálisgép-méret információkért lásd: [Azure virtuálisgép-méretek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* **A Windows PowerShell-Parancsprogramokról**: A témakör feltételezi, hogy rendelkezik-e a Windows PowerShell alapszintű ismeretét. Windows PowerShell használatával kapcsolatos további információkért tekintse meg a következőket:
  
  * [A Windows PowerShell indítása a Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
  * [Bevezetés a Windows PowerShell használatával](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>1. lépés: Az Azure virtuális gép kiépítése
1. Tallózással keresse meg a klasszikus Azure portálon.
2. Kattintson a **virtuális gépek** a bal oldali ablaktáblán.
   
    ![a Microsoft azure virtuális gépek](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. Kattintson az **Új** lehetőségre.
   
    ![Új gomb](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. Kattintson a **gyűjteményből**.
   
    ![új virtuális gép gyűjteményből](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. Kattintson a **SQL Server 2014 RTM Standard – Windows Server 2012 R2** és kattintson a nyílra, a folytatáshoz.
   
    ![következő](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Ha a Reporting Services adatvezérelt előfizetések funkció van szüksége, válassza a **SQL Server 2014 RTM vállalati – Windows Server 2012 R2**. SQL Server kiadása és által nyújtott szolgáltatások támogatásáról további információkért lásd: [SQL Server 2012 kiadásai által támogatott funkciók](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).
6. Az a **virtuálisgép-konfiguráció** lapon, módosítsa a következő mezőket:
   
   * Ha egynél több **VERZIÓJÚ kiadási dátum**, válassza ki az alkalmazás legújabb verziójára.
   * **Virtuális gép neve**: A számítógép nevét is szolgál a következő konfigurációs lapon alapértelmezett felhőalapú szolgáltatás DNS-nevét. Az Azure szolgáltatásban, egyedinek kell lennie a DNS-nevét. Fontolja meg, hogy a virtuális Gépet, amely leírja, hogy a virtuális gép használt számítógépnévvel. Például ssrsnativecloud.
   * **Réteg**: Standard
   * **Méret: A3** van az SQL Server munkaterhelésekhez ajánlott Virtuálisgép-méretet. Ha egy virtuális gép csak egy jelentéskészítő kiszolgálón, a virtuális gép méretét A2 is használhatók, kivéve, ha a jelentéskészítő kiszolgáló során a nagy terhelés lép fel. A virtuális gép a díjszabásról, lásd: [Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/).
   * **Új felhasználónevet**: a megadott jön létre a virtuális gép rendszergazdai jogosultságokkal.
   * **Új jelszó** és **megerősítése**. Ezt a jelszót az új rendszergazdafiókhoz szolgál, és erős jelszó használatát javasoljuk.
   * Kattintson a **Tovább** gombra. ![következő](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. A következő lapon szerkessze a következő mezőket:
   
   * **A felhőalapú szolgáltatás**: válasszon **hozzon létre egy új felhőalapú szolgáltatás**.
   * **A felhőalapú szolgáltatás DNS-név**: a felhőalapú szolgáltatás, a virtuális Géphez társított nyilvános DNS-neve. Alapértelmezés szerint ez a virtuális gép nevét a megadott névvel. Ha a témakör a későbbi lépésekben hoz létre egy megbízható az SSL-tanúsítványt, és majd a DNS-név értékének használható a "**kiadott**" a tanúsítvány.
   * **Régió/affinitás csoport/virtuális hálózati**: válassza ki a végfelhasználók legközelebb eső régiót.
   * **A Tárfiók**: egy automatikusan létrehozott tárfiókot használja.
   * **A rendelkezésre állási csoport**: nincs.
   * **VÉGPONTOK** tartsa a **távoli asztal** és **PowerShell** végpontok majd adja hozzá a HTTP vagy HTTPS-végpont a környezettől függően.
     
     * **HTTP**: az alapértelmezett nyilvános és titkos portok: **80**. Vegye figyelembe, hogy ha egy magánhálózati port a 80-as, nem módosíthatja **$HTTPport = 80** a http-parancsfájl.
     * **HTTPS**: az alapértelmezett nyilvános és titkos portok: **443-as**. Biztonsági szempontból ajánlott, hogy módosítsa a magánhálózati port és a tűzfal és a jelentéskészítő kiszolgáló a magánhálózati port használatára. A végpontok további információkért lásd: [hogyan állítsa be kommunikáció a virtuális gép](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Vegye figyelembe, hogy egy 443,-astól eltérő port használata esetén módosítsa a paraméter **$HTTPsport = 443-as** a HTTPS-parancsfájl.
   * Kattintson a Tovább gombra. ![következő](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. A varázsló utolsó oldalán, tartsa meg az alapértelmezett **a Virtuálisgép-ügynök telepítése** kijelölt. A témakörben ismertetett lépések nem használja a Virtuálisgép-ügynök, de ha le szeretné tartani a virtuális Gépet, a Virtuálisgép-ügynök és a bővítmények lehetővé teszi javítása érdekében, hogy CM.  A Virtuálisgép-ügynök további információkért lásd: [ügynök és Virtuálisgép-bővítmények – 1. rész](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Az alapértelmezett telepített kiterjesztéseket ad futó egyik a "BGINFO" bővítményt, amely a virtuális gép asztali, a rendszer-információkat, például a belső IP-cím és a szabad lemezterületet jeleníti meg.
9. Kattintson a Kész gombra. ![oké](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. A **állapota** látható a virtuális gép **indítása (kiépítés)** a kiépítési folyamat során értékként jelenik majd meg **futtató** a virtuális gép esetén kiosztott és készen áll a használatra.

## <a name="step-2-create-a-server-certificate"></a>2. lépés: A kiszolgálói tanúsítvány létrehozása
> [!NOTE]
> Ha nincs szüksége HTTPS a jelentéskészítő kiszolgálón, akkor **a 2** , és keresse meg a **parancsfájl segítségével konfigurálja a jelentéskészítő kiszolgáló és a HTTP**. A HTTP-parancsfájl segítségével gyorsan konfigurálása a jelentéskészítő kiszolgáló, és a jelentéskészítő kiszolgáló készen áll a használatra.

A virtuális Gépen a HTTPS protokoll használatához megbízható az SSL-tanúsítvány van szüksége. A forgatókönyvtől függően az alábbi két módszer egyikét használhatja:

* Érvényes SSL-tanúsítvány kiadott által hitelesítésszolgáltató (CA), és a Microsoft megbízhatónak. A Microsoft Root Certificate programon keresztül terjeszteni a hitelesítésszolgáltató legfelső szintű tanúsítványok szükségesek. A programra vonatkozó további információkért lásd: [Windows és Windows Phone 8 SSL Root Certificate Program (tag CAs)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) és [bemutatása a Microsoft Root Certificate Program](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).
* Egy önaláírt tanúsítványt. Önaláírt tanúsítványok éles környezetekben nem ajánlott.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Létrehozott egy megbízható tanúsítvány hitelesítésszolgáltatói (CA) tanúsítvány használatára
1. **A webhely kiszolgálói tanúsítványt kérhet egy hitelesítésszolgáltatótól**. 
   
    A kiszolgálói tanúsítvány varázsló használhatja, vagy egy kérelem Tanúsítványfájl (Certreq.txt) hitelesítésszolgáltató küldendő létrehozásához, vagy egy online hitelesítésszolgáltatót kérelmet létrehozni. Például a Microsoft tanúsítványszolgáltatások újdonságai a Windows Server 2012-ben. Attól függően, hogy a kiszolgálói tanúsítvány által kínált azonosító megbízhatósági szintjét akkor a hitelesítésszolgáltatót a kérelem jóváhagyása és a tanúsítvány fájl küldése néhány hónapig néhány nap. 
   
    A kiszolgálói tanúsítványok kérésével kapcsolatos további információkért tekintse át a következőket: 
   
   * Használjon [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
   * Biztonsági eszközök a Windows Server 2012 rendszerhez.
     
     [Biztonsági eszközök a Windows Server 2012 rendszerhez](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > A **kiadott** mező a megbízható az SSL-tanúsítvány egyeznie kell a **felhőalapú szolgáltatás DNS-név** az új virtuális gép használja.

2. **A kiszolgálótanúsítvány telepítésének a webkiszolgálón**. A webkiszolgáló ebben az esetben a virtuális gép, amelyen a jelentéskészítő kiszolgáló és a webhely a későbbi lépésekben jön létre, amikor a Reporting Services konfigurálásához. A webkiszolgáló a kiszolgáló-tanúsítvány telepítése a tanúsítvány MMC beépülő modul használatával kapcsolatos további információkért lásd: [a kiszolgálótanúsítvány telepítésének](https://technet.microsoft.com/library/cc740068).
   
    Ha a jelentéskészítő kiszolgáló, az érték a tanúsítványok konfigurálása az ebben a témakörben-parancsprogramja használni kívánt **ujjlenyomat** szükséges, a parancsfájl-paraméterként. Tekintse át a következő című szakaszban talál információt szerezni a tanúsítvány ujjlenyomatát.
3. A kiszolgáló-tanúsítványt hozzárendeli a jelentéskészítő kiszolgálón. A hozzárendelés befejeződött a következő szakaszban a jelentéskészítő kiszolgáló konfigurálásakor.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>A virtuális gépek önaláírt tanúsítvány használatára
Önaláírt tanúsítvány hozta létre a virtuális Gépre, a virtuális gép lett kiépítve. A tanúsítvány a a virtuális gép DNS-névvel megegyező névvel rendelkezik. Hitelesítési hibák elkerülése érdekében szükség, hogy megbízható, maga a virtuális gépen és a hely összes felhasználó-e a tanúsítvány.

1. Megbízható a legfelső szintű hitelesítésszolgáltató a tanúsítvány a helyi virtuális Gépen, vegye fel a tanúsítványt a **megbízható legfelső szintű hitelesítésszolgáltatók**. A következő szükséges lépések összefoglalása látható. Ismerteti, hogy bízzon meg a hitelesítésszolgáltató, lásd a [a kiszolgálótanúsítvány telepítésének](https://technet.microsoft.com/library/cc740068).
   
   1. A klasszikus Azure portálon válassza ki a virtuális Gépet, és kattintson a Csatlakozás gombra. A böngésző konfigurációtól függően előfordulhat, hogy kérni fogja menteni egy .rdp fájlt, a virtuális Géphez való kapcsolódáshoz.
      
       ![Csatlakozás az azure virtuális géphez](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) A felhasználói virtuális gép nevét, a felhasználónév és a virtuális gép létrehozásakor beállított jelszót használja. 
      
       Például az alábbi ábrán a virtuális gép neve van **ssrsnativecloud** és a felhasználónév **tesztfelhasználó néven**.
      
       ![bejelentkezési tartalmaz virtuális gép neve](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Futtassa a mmc.exe. További információkért lásd: [hogyan: tanúsítványok megtekintése az MMC beépülő modullal rendelkező](https://msdn.microsoft.com/library/ms788967.aspx).
   3. A Konzolalkalmazás **fájl** menüben adja hozzá a **tanúsítványok** beépülő modulban válassza **számítógépfiók** kéri, és kattintson **tovább**.
   4. Válassza ki **helyi számítógép** kezelése, majd **Befejezés**.
   5. Kattintson a **Ok** majd bontsa ki a **tanúsítványok - személyes** csomópont, és kattintson **tanúsítványok**. A tanúsítvány neve után a virtuális Gépet DNS-nevét, és végződik **cloudapp.net**. Kattintson a jobb gombbal a tanúsítványnevet, és kattintson a **másolási**.
   6. Bontsa ki a **megbízható legfelső szintű hitelesítésszolgáltatók** csomópontot, és kattintson rá jobb gombbal **tanúsítványok** majd **Beillesztés**.
   7. Érvényesíteni, kattintson duplán arra a tanúsítvány neve alatt **megbízható legfelső szintű hitelesítésszolgáltatók** , és ellenőrizze, hogy nincsenek hibák, és a tanúsítvány látható. Ha szeretné-e a HTTPS-parancsprogramja Ez a témakör segítségével konfigurálja a jelentéskészítő kiszolgáló, a tanúsítványok értékének **ujjlenyomat** szükséges, a parancsfájl-paraméterként. **Az ujjlenyomat értékének eléréséhez**, adja meg a következőket. Szerepel továbbá egy PowerShell-példa szakaszában az ujjlenyomat beolvasása [parancsfájl segítségével konfigurálja a jelentéskészítő kiszolgáló és a HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
      
      1. Kattintson duplán a tanúsítvány, például ssrsnativecloud.cloudapp.net neve.
      2. Kattintson a **részletek** fülre.
      3. Kattintson a **ujjlenyomat**. Az ujjlenyomat értékének jelenik meg a részleteket tartalmazó mező, például a6 08 3 c. df f9 0b f7 e3 7c 25 kell adnia végrehajtási adatokat a4 kell adnia végrehajtási adatokat 7e ac 91 9c 2c fb 2f.
      4. Másolja le az ujjlenyomatot, és mentse a értéket későbbi használatra, vagy a parancsfájl most szerkesztése.
      5. (*) A parancsfájl futtatása előtt távolítsa el a szóközöket a értékpárok Between. Például az ujjlenyomat előtt feljegyzett most lenne a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
      6. A kiszolgáló-tanúsítványt hozzárendeli a jelentéskészítő kiszolgálón. A hozzárendelés befejeződött a következő szakaszban a jelentéskészítő kiszolgáló konfigurálásakor.

Ha egy önaláírt SSL-tanúsítványt használ, a tanúsítvány neve már megfelel a virtuális gép állomásnevét. Ezért a DNS, a gép már regisztrálva van globálisan, és bármely ügyfél érhető el.

## <a name="step-3-configure-the-report-server"></a>3. lépés: A jelentéskészítő kiszolgáló konfigurálása
Ez a szakasz végigvezeti a virtuális gép konfigurálása natív üzemmódú Reporting Services jelentéskészítő kiszolgálón. A jelentéskészítő kiszolgáló konfigurálása a következő módszerek egyikét használhatja:

* A parancsfájl használata a jelentéskészítő kiszolgáló konfigurálása
* A jelentéskészítő kiszolgáló konfigurálása a Configuration Manager használatával.

Részletes lépéseket, tekintse meg a szakasz [csatlakozzon a virtuális géphez, és indítsa el a Reporting Services Configuration Manager](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Hitelesítési Megjegyzés:** Windows-hitelesítés a javasolt hitelesítési módszert, valamint az alapértelmezett Reporting Services hitelesítés. Csak a virtuális Gépen konfigurált felhasználók férhetnek a Reporting Services és a Reporting Services szerepkörökhöz hozzárendelt.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Parancsfájl használata a jelentéskészítő kiszolgáló és a HTTP konfigurálása
A jelentéskészítő kiszolgáló konfigurálása a Windows PowerShell-parancsfájl használatához kövesse az alábbi lépéseket. A konfiguráció HTTP, HTTPS-nem tartalmazza:

1. A klasszikus Azure portálon válassza ki a virtuális Gépet, és kattintson a Csatlakozás gombra. A böngésző konfigurációtól függően előfordulhat, hogy kérni fogja menteni egy .rdp fájlt, a virtuális Géphez való kapcsolódáshoz.
   
    ![Csatlakozás az azure virtuális géphez](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) A felhasználói virtuális gép nevét, a felhasználónév és a virtuális gép létrehozásakor beállított jelszót használja. 
   
    Például az alábbi ábrán a virtuális gép neve van **ssrsnativecloud** és a felhasználónév **tesztfelhasználó néven**.
   
    ![bejelentkezési tartalmaz virtuális gép neve](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Nyissa meg a virtuális gép **Windows PowerShell ISE** rendszergazdai jogosultságokkal. A PowerShell ISE a Windows server 2012 rendszeren alapértelmezés szerint telepítve van. Javasoljuk, akkor az ISE helyett a szabványos Windows PowerShell-ablakot, hogy a parancsfájl illessze be az ISE, módosítsa a parancsfájlt, és futtassa a parancsfájlt.
3. A Windows PowerShell ISE, kattintson a **nézet** menüre, majd majd **parancsfájl ablaktábla megjelenítése**.
4. Másolja a következő parancsfájlt, és illessze be a parancsfájlt a Windows PowerShell ISE parancsfájl ablaktáblára.
   
        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
   
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
   
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Report Server Configuration Steps
   
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
   
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
   
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
5. Ha a virtuális Gépet egy eltérő 80-as HTTP-port hozta létre, módosítsa a paramétert $HTTPport = 80-as.
6. A parancsfájl a Reporting Services van beállítva. Ha azt szeretné, a parancsfájl futtatásához a Reporting Services, a "v11", a Get-WmiObject fényében a névtér elérési verzió részét módosíthatja.
7. Futtassa a szkriptet.

**Érvényesítési**: az alapszintű jelentéskészítő kiszolgálói funkciók működésének ellenőrzéséhez tekintse meg a [a konfiguráció ellenőrzése](#verify-the-configuration) későbbi szakaszában talál.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Parancsfájl használata a jelentéskészítő kiszolgáló és a HTTPS konfigurálása
A jelentéskészítő kiszolgáló konfigurálása a Windows PowerShell használatához kövesse az alábbi lépéseket. A konfiguráció tartalmazza a HTTPS-en nem HTTP.

1. A klasszikus Azure portálon válassza ki a virtuális Gépet, és kattintson a Csatlakozás gombra. A böngésző konfigurációtól függően előfordulhat, hogy kérni fogja menteni egy .rdp fájlt, a virtuális Géphez való kapcsolódáshoz.
   
    ![Csatlakozás az azure virtuális géphez](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) A felhasználói virtuális gép nevét, a felhasználónév és a virtuális gép létrehozásakor beállított jelszót használja. 
   
    Például az alábbi ábrán a virtuális gép neve van **ssrsnativecloud** és a felhasználónév **tesztfelhasználó néven**.
   
    ![bejelentkezési tartalmaz virtuális gép neve](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Nyissa meg a virtuális gép **Windows PowerShell ISE** rendszergazdai jogosultságokkal. A PowerShell ISE a Windows server 2012 rendszeren alapértelmezés szerint telepítve van. Javasoljuk, akkor az ISE helyett a szabványos Windows PowerShell-ablakot, hogy a parancsfájl illessze be az ISE, módosítsa a parancsfájlt, és futtassa a parancsfájlt.
3. Ahhoz, hogy a parancsfájlok futtatását, futtassa a következő Windows PowerShell-parancsot:
   
        Set-ExecutionPolicy RemoteSigned
   
    Ezt követően futtathatja a házirend ellenőrzése a következőt:
   
        Get-ExecutionPolicy
4. A **Windows PowerShell ISE**, kattintson a **nézet** menüre, majd majd **parancsfájl ablaktábla megjelenítése**.
5. Másolja a következő parancsfájlt, és illessze be a Windows PowerShell ISE parancsfájl ablaktáblán.
   
        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
   
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
   
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Reporting Services Report Server Configuration Steps
   
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
   
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
   
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## 3. Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
   
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
   
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
6. Módosítsa a **$certificatehash** paraméter a parancsfájlban:
   
   * Ez egy **szükséges** paraméter. Ha a tanúsítvány érték az előző lépésekben nem mentette, az a következő módszerek egyikét másolhatja a tanúsítvány kivonatoló értékét a tanúsítványok ujjlenyomatát.:
     
       A virtuális Gépre nyissa meg a Windows PowerShell ISE, és futtassa a következő parancsot:
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       A kimenet az alábbihoz hasonló fog kinézni. A parancsfájl egy üres sort ad vissza, ha a virtuális gép nem rendelkezik konfigurált például tanúsítvány, című részében [a virtuális gépek önaláírt tanúsítvány használatára](#to-use-the-virtual-machines-self-signed-certificate).
     
     VAGY
   * A virtuális gép fut mmc.exe, majd adja hozzá a **tanúsítványok** beépülő modult.
   * Az a **megbízható legfelső szintű hitelesítésszolgáltatók** csomópontot, kattintson duplán a tanúsítvány neve. A virtuális gép önaláírt tanúsítványt használ, ha a tanúsítvány neve után a virtuális Gépet DNS-nevét, és végződik **cloudapp.net**.
   * Kattintson a **részletek** fülre.
   * Kattintson a **ujjlenyomat**. Az ujjlenyomat értékének jelenik meg a részleteket tartalmazó mező, például af 11 60 b6 4b 28 8 d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
   * **A parancsfájl futtatása előtt**, távolítsa el a szóközöket érték párok között. Például af1160b64b288d890a8212ff6ba9c3664f319048
7. Módosítsa a **$httpsport** paraméter: 
   
   * Ha a 443-as portot használja a HTTPS-végponton, majd nem módosítania ezt a paramétert a parancsfájlban. Ellenkező esetben használja a port értékét a titkos HTTPS-végpont a virtuális Gépre konfigurálásakor választotta.
8. Módosítsa a **$DNSName** paraméter: 
   
   * A parancsfájl beállítása helyettesítő tanúsítvány $DNSName = "+". Ha így tesz, nem kívánt segítségével konfigurálhatja a helyettesítő tanúsítvány kötés $DNSName megjegyzéssé ="+"és a következő sorban, a teljes $DNSNAme hivatkozás, ## $DNSName="$server.cloudapp.net engedélyezése".
     
       Módosítsa a $DNSName értékét, ha nem szeretné használni a Reporting Services a virtuális gép DNS-nevét. Ha a paraméter használata esetén a tanúsítványt kell használnia az ezt a nevet, és regisztrálnia globálisan a DNS-kiszolgáló nevét.
9. A parancsfájl a Reporting Services van beállítva. Ha azt szeretné, a parancsfájl futtatásához a Reporting Services, a "v11", a Get-WmiObject fényében a névtér elérési verzió részét módosíthatja.
10. Futtassa a szkriptet.

**Érvényesítési**: az alapszintű jelentéskészítő kiszolgálói funkciók működésének ellenőrzéséhez tekintse meg a [a konfiguráció ellenőrzése](#verify-the-connection) későbbi szakaszában talál. Lévő tanúsítvány ellenőrzéséhez kötési nyisson meg egy parancssort rendszergazdai jogosultságokkal, és futtassa a következő parancsot:

    netsh http show sslcert

Az eredmény a következőket tartalmazza:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>A jelentéskészítő kiszolgáló konfigurálása a Configuration Manager használatával
Ha nem szeretné, hogy a jelentéskészítő kiszolgáló konfigurálása a PowerShell parancsfájl futtatásához, kövesse az ebben a szakaszban a jelentéskészítő kiszolgáló konfigurálása a configuration manager jelentéskészítési szolgáltatások natív mód segítségével.

1. A klasszikus Azure portálon válassza ki a virtuális Gépet, és kattintson a Csatlakozás gombra. A felhasználónév és a virtuális gép létrehozásakor beállított jelszó használata.
   
    ![Csatlakozás az azure virtuális géphez](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Futtassa a Windows update, és telepítse a frissítéseket a virtuális géphez. Ha a virtuális gép újraindítására szükség, indítsa újra a virtuális gép, és csatlakozzon újra a virtuális Gépet a klasszikus Azure portálon.
3. A virtuális Gépen a Start menüben írja be a **Reporting Services** , és nyissa meg **Reporting Services Configuration Manager**.
4. Hagyja meg az alapértelmezett értékeit **kiszolgálónév** és **jelentéskészítő kiszolgáló példánya**. Kattintson a **Connect** (Csatlakozás) gombra.
5. Kattintson a bal oldali ablaktáblában **webes szolgáltatás URL-címe**.
6. Alapértelmezés szerint RS "Az összes hozzárendelt" IP-80-as HTTP-port van konfigurálva. HTTPS hozzáadása:
   
   1. A **SSL-tanúsítvány**: válassza ki a [virtuális gép neve] például használni kívánt tanúsítványt. cloudapp.net. Ha nincsenek felsorolva tanúsítványok, tekintse meg a szakasz **2. lépés: hozzon létre egy kiszolgálói tanúsítványt** telepítéséről és a virtuális Gépen a tanúsítvány megbízható olvashat.
   2. A **SSL-Port**: válassza ki a 443-as. Ha konfigurálta a titkos HTTPS-végpont a virtuális gép más magánhálózati port, használni ezt az értéket.
   3. Kattintson a **alkalmaz** és várjon, amíg a művelet elvégzéséhez.
7. Kattintson a bal oldali ablaktáblában **adatbázis**.
   
   1. Kattintson a **Databas módosítása**e.
   2. Kattintson a **hozzon létre egy új jelentéskészítő kiszolgáló adatbázisában** majd **következő**.
   3. Hagyja meg az alapértelmezett **kiszolgálónév**: neve legyen a virtuális Gépet, és hagyja meg az alapértelmezett **hitelesítési típus** , **aktuális felhasználó** – **integrált biztonsági**. Kattintson a **Tovább** gombra.
   4. Hagyja meg az alapértelmezett **adatbázisnév** , **ReportServer** kattintson **következő**.
   5. Hagyja meg az alapértelmezett **hitelesítési típus** , **szolgáltatás hitelesítő adatai** kattintson **következő**.
   6. Kattintson a **következő** a a **összegzés** lap.
   7. A konfiguráció befejeztével kattintson **Befejezés**.
8. Kattintson a bal oldali ablaktáblában **Jelentéskezelő URL-címe**. Hagyja meg az alapértelmezett **virtuális könyvtár** , **jelentések** kattintson **alkalmaz**.
9. Kattintson a **kilépési** a Reporting Services Configuration Manager bezárásához.

## <a name="step-4-open-windows-firewall-port"></a>4. lépés: A Windows tűzfal Port megnyitása
> [!NOTE]
> Ha a parancsfájlok a jelentéskészítő kiszolgáló konfigurálása, ez a szakasz kihagyhatja. A parancsfájl tartalmazza a lépés a tűzfal-port megnyitásához. Az alapértelmezett port a 80-as HTTP és HTTPS – 443-as port volt.
> 
> 

Távolról kapcsolódni a Jelentéskezelő vagy a jelentéskészítő kiszolgáló, a virtuális gépen, a TCP-végpontot a virtuális gép szükséges. Szükség van rá ugyanazt a portot a virtuális gép megnyitása. A végpont jött létre, amikor a virtuális gép lett kiépítve.

Ez a szakasz a tűzfal-port megnyitásának módjáról alapvető információkat nyújt. További információkért lásd: [beállítani a tűzfalat a jelentéskészítő kiszolgáló elérése](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> Ha a parancsfájl a jelentéskészítő kiszolgáló konfigurálására szolgáló, ez a szakasz kihagyhatja. A parancsfájl tartalmazza a lépés a tűzfal-port megnyitásához.
> 
> 

Ha a magánhálózati port a HTTPS használatára konfigurált 443-astól eltérő, annak megfelelően módosítsa a következő parancsfájlt. Port megnyitásához **443-as** a Windows tűzfalon, adja meg a következőket:

1. Nyissa meg a Windows PowerShell ablakot rendszergazdai jogosultságokkal.
2. Ha a 443-astól eltérő portot a HTTPS-végpont a virtuális Gépre való konfigurálásakor, frissíteni a következő parancsot a portot, és futtassa a parancsot:
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. A parancs befejeződésekor **Ok** jelenik meg a parancssort.

Győződjön meg arról, hogy a port van megnyitva, nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsot:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése
Győződjön meg arról, hogy a jelentés alapvető funkcióihoz most működik, nyissa meg a böngésző rendszergazdai jogosultságokkal, és tallózással keressen meg a következő jelentéskészítő kiszolgáló ad Jelentéskezelő URL-CÍMEK:

* A virtuális Gépre keresse meg a jelentéskészítő kiszolgáló URL-címe:
  
        http://localhost/reportserver
* A virtuális Gépre keresse meg a jelentés Manager URL-címe:
  
        http://localhost/Reports
* A helyi számítógépről, keresse meg a **távoli** Manager jelentést a virtuális Gépet. Szükség szerint az alábbi példában a DNS-név frissítése. Amikor a rendszer kéri a jelszót, akkor jön létre, amikor a virtuális gép lett kiépítve rendszergazdai hitelesítő adatokat használja. A felhasználó nevét kell a [tartomány]\[felhasználónév] formátumban, ahol a tartomány pedig a virtuális gép számítógép nevét, például ssrsnativecloud\testuser. Ha nem használja a HTTP**S**, távolítsa el a **s** az URL-címben. Tekintse meg a következő szakaszban talál további felhasználók létrehozásával a virtuális Gépen.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* A helyi számítógépről keresse meg azt a távoli jelentéskészítő kiszolgáló URL-címe. Szükség szerint az alábbi példában a DNS-név frissítése. Ha HTTPS nem használ, távolítsa el az s az URL-címben.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Felhasználók létrehozása és szerepkörök hozzárendelése
Konfigurálása, és a jelentéskészítő kiszolgáló ellenőrzése után a gyakori felügyeleti feladatokat, hogy hozzon létre egy vagy több felhasználó és a Reporting Services szerepkörökhöz rendeljen hozzá felhasználókat. További információkért tekintse át a következőket:

* [Helyi felhasználói fiók létrehozása](https://technet.microsoft.com/library/cc770642.aspx)
* [A jelentéskészítő kiszolgáló (Jelentéskezelő) GRANT felhasználói hozzáférése](https://msdn.microsoft.com/library/ms156034.aspx))
* [Létrehozása és kezelése a szerepkör-hozzárendelések](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Hozzon létre, és a jelentések közzététele az Azure virtuális géphez
A következő táblázat összefoglalja az egyes lehetőségekről a helyi számítógépről a jelentéskészítő kiszolgálón található meg a Microsoft Azure virtuális gép meglévő jelentések közzététele érdekében:

* **RS.exe parancsfájl**: használata RS.exe parancsfájl jelentés elemeit és a meglévő jelentéskészítő kiszolgáló számára a Microsoft Azure virtuális gép másolása. További információkért lásd: a "Natív mód natív üzemmódra – a Microsoft Azure virtuális gép" szakaszban a [minta Reporting Services rs.exe tartalom áttelepítése jelentés kiszolgálók közötti parancsfájl](https://msdn.microsoft.com/library/dn531017.aspx).
* **Jelentéskészítő**: A virtuális gépet tartalmaz, kattintson a-Microsoft SQL Server jelentéskészítő egyszer verzióját. Elindítja a jelentés jelentéskészítő első alkalommal a virtuális gépet:
  
  1. A böngészőben rendszergazdai jogosultságokkal.
  2. Keresse meg a Jelentéskezelő virtuális gépen, és kattintson a **jelentéskészítő** a szalagon.
     
     További információkért lásd: [telepítése, eltávolítása és a jelentéskészítő támogató](https://technet.microsoft.com/library/dd207038.aspx).
* **SQL Server Data Tools: Virtuális gép**: Ha a virtuális gép az SQL Server 2012-ben létrehozott, akkor az SQL Server Data Tools a virtuális gépre van telepítve, és segítségével hozzon létre **jelentés Server projektek** és a jelentések a virtuális gépen. SQL Server Data Tools közzéteheti a jelentéseket a jelentéskészítő kiszolgáló, a virtuális gépen.
  
    Ha a virtuális gép az SQL server 2014 hozta létre, telepítheti az SQL Server Data Tools - BI visual Studio. További információkért tekintse át a következőket:
  
  * [Microsoft SQL Server Data Tools összetevővel - üzleti intelligencia, a Visual Studio 2013-hoz](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Microsoft SQL Server Data Tools összetevővel - üzleti intelligencia, a Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [SQL Server Data Tools összetevővel és az SQL Server Business Intelligence (SSDT-bA)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)
* **SQL Server Data Tools: Távoli**: A helyi számítógépen, a Reporting Services-projekt létrehozása az SQL Server Data Tools összetevővel, amely tartalmazza a Reporting Services-jelentéseket. A webes szolgáltatás URL-Címéhez való kapcsolódáshoz a projekt konfigurálásához.
  
    ![SSRS-projekt SSDT projektjének tulajdonságai](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **Parancsfájl használata**: parancsfájl segítségével másolja a jelentéskészítő kiszolgáló tartalmat. További információkért lásd: [minta Reporting Services rs.exe tartalom áttelepítése jelentés kiszolgálók közötti parancsfájl](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Ha nem használ a virtuális gép minimalizálása érdekében a költség
> [!NOTE]
> Költségek minimalizálása érdekében az az Azure virtuális gépek Ha nincsenek használatban, állítsa le a virtuális Gépet a klasszikus Azure portálon. A Windows energiagazdálkodási beállításait használja a virtuális Gépen belül a virtuális gép leállítása, ha még mindig van szó akkora a virtuális gép számára. Költségek csökkentése érdekében le a virtuális Gépet, a klasszikus Azure portálon kell. Ha már nincs szüksége a virtuális Gépet, ne felejtse el a virtuális gép és a hozzárendelt .vhd fájlokat tároló díjak elkerülése érdekében törölje. További információkért lásd: a következő gyakran feltett [virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>További információ
### <a name="resources"></a>Erőforrások
* Az SQL Server Business Intelligence és a SharePoint 2013 egyetlen kiszolgáló telepítéséhez kapcsolódó hasonló tartalomhoz, lásd: [a Windows PowerShell szolgáltatás használatával hozzon létre egy Azure virtuális gép az SQL Server BI és a SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).
* A hasonló tartalomhoz kapcsolódó SQL Server Business Intelligence és a SharePoint 2013 többkiszolgálós telepítésben, lásd: [központi telepítése az SQL Server Business Intelligence Azure virtuális gépek](https://msdn.microsoft.com/library/dn321998.aspx).
* A SQL Server Business Intelligence Azure virtuális gépek központi telepítéséhez kapcsolódó általános információkért lásd: [SQL Server Business Intelligence Azure virtuális gépek](virtual-machines-windows-classic-ps-sql-bi.md).
* Az Azure számítási díjakat költsége kapcsolatos további információkért lásd: a virtuális gépek lapján [Azure árképzési Számológép](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Közösségi tartalom
* A jelentéskészítési szolgáltatások natív mód jelentéskészítő kiszolgáló létrehozásával parancsfájl használata nélkül részletes utasításokért lásd: [üzemeltető SQL jelentési szolgáltatás az Azure virtuális gép](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Az SQL Server Azure virtuális gépeken egyéb forrásokra mutató hivatkozások
[SQL Server Azure virtuális gépek – áttekintés](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

