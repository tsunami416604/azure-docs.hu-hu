---
title: Natív üzemmódú jelentéskészítő kiszolgálót futtató virtuális gép létrehozása a PowerShell használatával |} A Microsoft Docs
description: 'Ez a témakör azt ismerteti, és végigvezeti a telepítési és a egy SQL Server Reporting Services natív üzemmódú jelentéskészítő kiszolgálót egy Azure virtuális gép konfigurációja. '
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
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
ms.author: maghan
ms.openlocfilehash: 32be473ab93231805cdae097e3e984a2e74da973
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233082"
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Natív üzemmódú jelentéskészítő kiszolgálót futtató Azure-beli virtuális gép létrehozása a PowerShell-lel
> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ez a témakör azt ismerteti, és végigvezeti a telepítési és a egy SQL Server Reporting Services natív üzemmódú jelentéskészítő kiszolgálót egy Azure virtuális gép konfigurációja. A jelen dokumentumban leírt lépések hozhat létre a virtuális gép és a egy Windows PowerShell-parancsfájlt a virtuális gépen a Reporting Services konfigurálásának manuális lépései kombinációját használják. A konfigurációs parancsprogram tartalmaz, a tűzfal port megnyitása a HTTP vagy HTTPs.

> [!NOTE]
> Ha nincs szüksége **HTTPS** a jelentéskészítő kiszolgálón **hagyja ki a 2. lépés**.
> 
> Miután a virtuális gép létrehozása az 1. lépésben, látogasson el a szakasz a parancsfájl használata a HTTP és a jelentéskészítő kiszolgáló konfigurálása. A szkript futtatása után a jelentéskészítő kiszolgáló használatra készen áll.

## <a name="prerequisites-and-assumptions"></a>Előfeltételek és feltételezések
* **Azure-előfizetés**: Ellenőrizze az Azure-előfizetésében rendelkezésre álló magok számát. Ha hoz létre, az ajánlott Virtuálisgép-méretet **A3**, kell **4** elérhető magok száma. Ha egy Virtuálisgép-méretet használja **A2**, kell **2** elérhető magok száma.
  
  * Ellenőrizze az előfizetés az Azure Portalon magkorlátja kattintson a beállítások gombra a bal oldali panelen, majd kattintson a használati a felső menüben.
  * A magkvóta növeléséhez forduljon [Azure-támogatási](https://azure.microsoft.com/support/options/). Virtuális gép mérete információkért lásd: [az Azure virtuálisgép-méretek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* **Windows PowerShell-parancsprogramok**: A témakör azt feltételezi, hogy a Windows PowerShell alapszintű ismeretét. Windows PowerShell használatával kapcsolatos további információkért tekintse meg a következőket:
  
  * [Windows PowerShell indítása a Windows Serveren](https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell)
  * [Első lépések a Windows PowerShell-lel](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>1. lépés: Egy Azure virtuális gép üzembe helyezése
1. Keresse meg az Azure Portalon.
2. Kattintson a **virtuális gépek** a bal oldali panelen.
   
    ![a Microsoft azure virtual machines](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. Kattintson az **Új** lehetőségre.
   
    ![Új gomb](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. Kattintson a **katalógusból**.
   
    ![új virtuális gépet a katalógusból](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. Kattintson a **SQL Server 2014 RTM Standard – Windows Server 2012 R2** és kattintson a Tovább nyílra.
   
    ![tovább](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Ha a Reporting Services adatvezérelt előfizetések funkció van szüksége, válassza a **SQL Server 2014 RTM Enterprise Windows Server 2012 R2**. Az SQL Server-kiadások és támogatásáról további információkért lásd: [SQL Server 2012 kiadásai által támogatott funkciók](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).
6. Az a **virtuálisgép-konfiguráció** lapon, a következő mezők szerkesztése:
   
   * Ha egynél több található **verzió KIADÁSÁT követően**, válassza ki a legújabb verziót.
   * **A virtuális gép neve**: A gép nevét is szolgál a következő konfigurációs lapon alapértelmezett felhőalapú szolgáltatás DNS-nevét. A DNS-nevének egyedinek kell lennie az Azure-szolgáltatás. Fontolja meg a virtuális gép konfigurálását, amely azt ismerteti, mire használható a virtuális gép számítógépnevére. Például ssrsnativecloud.
   * **Réteg**: Standard
   * **Méret: A3** van a SQL Server számítási feladatok esetében ajánlott Virtuálisgép-méretet. Ha egy virtuális gép csak egy jelentéskészítő kiszolgáló, egy virtuális gép méretét A2 is használhatók, kivéve, ha a jelentéskészítő kiszolgáló egy nagy méretű számítási feladatok során lép. A virtuális gép díjszabási információkért, lásd: [Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/).
   * **Új felhasználónév**: a megadott jön létre a virtuális gépre rendszergazdaként.
   * **Új jelszó** és **megerősítése**. Ez a jelszó használatos az új rendszergazdai fiókhoz, és erős jelszó használatát javasoljuk.
   * Kattintson a **Tovább** gombra. ![next](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. A következő lapon szerkessze a következő mezőket:
   
   * **Cloud Service**: válasszon **új Felhőszolgáltatás hozható létre**.
   * **Cloud Service DNS-név**: Ez az, hogy a Felhőszolgáltatás, amely a virtuális Géphez társított nyilvános DNS-nevét. Az alapértelmezett név a virtuális gép neve a beírt név. Ha a későbbi lépésekben, a következő témakörben, a megbízható SSL-tanúsítványt hoz létre, és ezután a DNS-név értéke szolgál a "**kiadott**" a tanúsítvány.
   * **Régió/kapcsolat/csoport/virtuális hálózat**: válassza ki a végfelhasználók legközelebb eső régiót.
   * **Storage-fiók**: egy automatikusan létrehozott storage-fiókot.
   * **Rendelkezésre állási csoport**: nincs.
   * **VÉGPONTOK** tartsa a **a távoli asztal** és **PowerShell** végpontok majd adja hozzá a HTTP vagy HTTPS-végpont a környezettől függően.
     
     * **HTTP**: az alapértelmezett nyilvános és magánhálózati port **80-as**. Vegye figyelembe, hogy ha nem a 80-as, magánhálózati portot módosítsa **$HTTPport = 80** a http-szkriptben.
     * **HTTPS**: az alapértelmezett nyilvános és magánhálózati port **443-as**. Biztonsági szempontból ajánlott, hogy módosítsa a magánhálózati portot, és konfigurálja a tűzfalat, és a jelentéskészítő kiszolgáló a magánhálózati port használatára. A végpontok további információkért lásd: [hogyan állítsa be kommunikációt egy virtuális géppel](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Vegye figyelembe, hogy ha nem a 443-as portot használja a paraméter módosítani **$HTTPsport = 443-as** a HTTPS-szkriptben.
   * Kattintson a Tovább gombra. ![tovább](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. A varázsló utolsó oldalán, tartsa meg az alapértelmezett **Virtuálisgép-ügynök telepítése** kiválasztott. Ebben a témakörben leírt lépéseket nem használja a Virtuálisgép-ügynök, de ha azt tervezi, hogy a virtuális Gépet, a Virtuálisgép-ügynök és a kiterjesztések lehetővé teszi CM javíthatja azt.  A Virtuálisgép-ügynök további információkért lásd: [Virtuálisgép-ügynök és -bővítmények – 1. rész](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Az alapértelmezett telepített kiterjesztéseket ad futó egyik, a "BGINFO" bővítményt, amely a virtuális gép asztalához, például a belső IP-cím és a szabad lemezterület a rendszer-információkat jeleníti meg.
9. Kattintson a Kész gombra. ![OK](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. A **állapot** megjeleníti a virtuális gép **indítása (kiépítés)** jelenik majd meg, és kiépítése folyamat során **futó** kiépített és készen áll a használatra a virtuális gép esetén.

## <a name="step-2-create-a-server-certificate"></a>2. lépés: Hozzon létre egy kiszolgálói tanúsítvány
> [!NOTE]
> Ha nincs szüksége HTTPS a jelentéskészítő kiszolgálón, akkor **hagyja ki a 2. lépés** , és keresse meg a **konfigurálása a jelentéskészítő kiszolgáló és a HTTP-szkripttel**. A HTTP-parancsfájl segítségével gyorsan konfigurálása a jelentéskészítő kiszolgáló és a jelentéskészítő kiszolgáló használatra kész lesz.

Annak érdekében, hogy a virtuális gépen a HTTPS protokoll használatához egy megbízható SSL-tanúsítvány szükséges. A forgatókönyvtől függően az alábbi két módszer egyikét használhatja:

* Érvényes SSL-tanúsítvány kibocsátása a hitelesítésszolgáltató (CA), és a Microsoft által megbízhatónak tartott. A Microsoft Root Certificate programon keresztül kell elosztani a legfelső szintű Hitelesítésszolgáltatói tanúsítványok szükségesek. Ez a program kapcsolatos további információkért lásd: [Windows és Windows Phone 8 SSL Root Certificate Program (tag CAs)](https://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) és [bemutatása a Microsoft Root Certificate Program](https://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).
* Egy önaláírt tanúsítványt. Önaláírt tanúsítványok használata nem ajánlott éles környezetben.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>A létrehozott szerint egy megbízható hitelesítésszolgáltató (CA) tanúsítvány használata
1. **A webhely kiszolgálói tanúsítvány kérése hitelesítésszolgáltatótól származó**. 
   
    A kiszolgálói tanúsítvány varázsló használhatja, vagy egy tanúsítványkérelem-fájlt (Certreq.txt), amelyeket elküldhet egy hitelesítésszolgáltató létrehozásához, vagy egy online hitelesítésszolgáltató kérelmet létrehozni. Például a Microsoft tanúsítványszolgáltatások a Windows Server 2012-ben. Attól függően, a kiszolgálói tanúsítvány által kínált azonosító megbízhatósági szintjét néhány hónappal jóváhagyja a kérelmét, és küldünk egy tanúsítványfájlt a hitelesítésszolgáltató több napot. 
   
    Egy kiszolgáló tanúsítványigényléssel kapcsolatos további információkért tekintse meg a következőket: 
   
   * Használat [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
   * Biztonsági eszközök a Windows Server 2012 rendszerhez.
     
     [Biztonsági eszközök a Windows Server 2012 rendszerhez](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > A **kiadott** megbízható SSL-tanúsítvány mező lehet ugyanaz, mint a **Felhőszolgáltatás DNS neve** az új virtuális gép esetében használt.

2. **A tanúsítvány telepítése a webkiszolgálón**. A webkiszolgáló ebben az esetben a virtuális gép, amelyen a jelentéskészítő kiszolgáló és a webhely a későbbi lépésekben létrejön, amikor a Reporting Services konfigurálásához. A tanúsítvány telepítése a webkiszolgálón a tanúsítvány MMC beépülő modul használatával kapcsolatos további információkért lásd: [telepítsen egy kiszolgálói tanúsítványt](https://technet.microsoft.com/library/cc740068).
   
    Ha a jelentéskészítő kiszolgáló, az érték a tanúsítványok konfigurálása az ebben a témakörben-parancsprogramja használni kívánt **ujjlenyomat** , mint a parancsfájl egy paraméter megadása kötelező. Tekintse meg a részleteket a következő szakaszban a tanúsítvány ujjlenyomatának beszerzésével.
3. A tanúsítvány hozzárendelése a jelentéskészítő kiszolgálón. A hozzárendelés befejeződött a következő szakaszban a jelentéskészítő kiszolgáló konfigurálásakor.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>A virtuális gépek önaláírt tanúsítvány használatára
Egy önaláírt tanúsítványt hozott létre a virtuális gépen a virtuális gép lett üzembe helyezve. A tanúsítvány neve megegyezik a virtuális gép DNS-név rendelkezik. Annak érdekében, hogy a hitelesítési hibák elkerülése érdekében szükség rá, hogy a tanúsítvány megbízható, az a virtuális gépre és a hely összes felhasználó által-e.

1. Megbízható a legfelső szintű hitelesítésszolgáltató a tanúsítvány a helyi virtuális gépen, vegye fel a tanúsítványt a **megbízható legfelső szintű hitelesítésszolgáltatók**. Az alábbiakban látható lépéseit összegzését. A hitelesítésszolgáltató megbízható módjáról részletes lépéseiért lásd: [telepítsen egy kiszolgálói tanúsítványt](https://technet.microsoft.com/library/cc740068).
   
   1. Az Azure Portalon válassza ki a virtuális Gépet, és kattintson a Csatlakozás gombra. A böngésző konfigurációtól függően előfordulhat, hogy kéri a virtuális Géphez való csatlakozáshoz egy .rdp-fájlt menteni.
      
       ![Csatlakozás az azure virtuális géphez](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) A felhasználói virtuális gép nevét, a felhasználónév és a virtuális gép létrehozásakor beállított jelszót használja. 
      
       Ha például az alábbi ábrán a virtuális gép neve van **ssrsnativecloud** és a felhasználónév **testuser**.
      
       ![bejelentkezési tartalmazza a virtuális gép neve](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Mmc.exe futtassa. További információkért lásd: [hogyan: tanúsítványok megtekintése az MMC beépülő modullal rendelkező](https://msdn.microsoft.com/library/ms788967.aspx).
   3. A Konzolalkalmazás **fájl** menüben adja hozzá a **tanúsítványok** beépülő modult, válassza **számítógépfiók** , amikor a rendszer kéri, és kattintson a **következő**.
   4. Válassza ki **helyi számítógép** kezeléséhez, és kattintson a **Befejezés**.
   5. Kattintson a **Ok** majd bontsa ki a **tanúsítványok - személyes** csomópontokat, majd kattintson **tanúsítványok**. A tanúsítvány neve megegyezik a virtuális gép DNS-nevét, és végződik **cloudapp.net**. Kattintson a jobb gombbal a tanúsítvány nevét, és kattintson a **másolási**.
   6. Bontsa ki a **megbízható legfelső szintű hitelesítésszolgáltatók** csomópontot, és kattintson rá jobb gombbal **tanúsítványok** majd **beillesztési**.
   7. Ellenőrzéséhez kattintson duplán a tanúsítvány neve alatt **megbízható legfelső szintű hitelesítésszolgáltatók** , és ellenőrizze, hogy nem tartalmaz hibát, és láthatja, hogy a tanúsítványt. Ha a jelentéskészítő kiszolgáló, az érték a tanúsítványok konfigurálása a HTTPS-parancsprogramja ebben a témakörben használni kívánt **ujjlenyomat** , mint a parancsfájl egy paraméter megadása kötelező. **Az ujjlenyomat értéket beolvasni**, hajtsa végre a következőket. Emellett van egy PowerShell-minta a szakaszban az ujjlenyomat beolvasása [konfigurálása a jelentéskészítő kiszolgáló és a HTTPS-szkripttel](#use-script-to-configure-the-report-server-and-HTTPS).
      
      1. Kattintson duplán a tanúsítvány, például ssrsnativecloud.cloudapp.net nevére.
      2. Kattintson a **részletek** fülre.
      3. Kattintson a **ujjlenyomat**. Az ujjlenyomat értéke megjelenik a részletek mezőjében, például a6 08 3 c. df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 2c 9c fb 2f.
      4. Másolja az ujjlenyomatot, és mentse az értéket későbbi használatra, vagy most szerkessze a szkriptet.
      5. (*) A parancsfájl futtatása előtt távolítsa el a szóközöket a érték párok között. Például az ujjlenyomat előtt feljegyzett most lenne a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
      6. A tanúsítvány hozzárendelése a jelentéskészítő kiszolgálón. A hozzárendelés befejeződött a következő szakaszban a jelentéskészítő kiszolgáló konfigurálásakor.

Ha egy önaláírt SSL-tanúsítványt használ, a tanúsítványon szereplő névvel már megegyezik a virtuális gép állomásnevét. Ezért a DNS-ben, a gép már regisztrálva van globálisan, és bármely ügyfél részéről hozzáférhető.

## <a name="step-3-configure-the-report-server"></a>3. lépés: A jelentéskészítő kiszolgáló konfigurálása
Ez a szakasz végigvezeti a virtuális gép konfigurálása a Reporting Services natív üzemmódú jelentéskészítő kiszolgálót. A jelentéskészítő kiszolgáló konfigurálása a következő módszerek egyikét használhatja:

* A szkript használatával a jelentéskészítő kiszolgáló konfigurálása
* A jelentéskészítő kiszolgáló konfigurálása a Configuration Manager használatával.

Részletes lépéseket, tekintse meg a szakasz [csatlakozzon a virtuális géphez, és indítsa el a Reporting Services Configuration Manager](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Hitelesítési Megjegyzés:** Windows-hitelesítés ajánlott hitelesítési módszert, és a Reporting Services alapértelmezett hitelesítési. Csak a virtuális gépen konfigurált felhasználók hozzáférhetnek a Reporting Services, és a Reporting Services-szerepkörök rendelve.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Parancsfájl használata a HTTP és a jelentéskészítő kiszolgáló konfigurálása
A jelentéskészítő kiszolgáló konfigurálása a Windows PowerShell-szkript használatához kövesse az alábbi lépéseket. A konfiguráció HTTP, HTTPS-nem tartalmazza:

1. Az Azure Portalon válassza ki a virtuális Gépet, és kattintson a Csatlakozás gombra. A böngésző konfigurációtól függően előfordulhat, hogy kéri a virtuális Géphez való csatlakozáshoz egy .rdp-fájlt menteni.
   
    ![Csatlakozás az azure virtuális géphez](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) A felhasználói virtuális gép nevét, a felhasználónév és a virtuális gép létrehozásakor beállított jelszót használja. 
   
    Ha például az alábbi ábrán a virtuális gép neve van **ssrsnativecloud** és a felhasználónév **testuser**.
   
    ![bejelentkezési tartalmazza a virtuális gép neve](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Nyissa meg a virtuális gép **Windows PowerShell ISE-ben** rendszergazdai jogosultságokkal. A PowerShell ISE-ben a Windows server 2012 rendszeren alapértelmezés szerint telepítve van. Ajánlott az ISE-ben egy standard szintű Windows PowerShell-ablakot helyett használhatja, hogy a parancsfájl illessze be az ISE-ben, módosítsa a parancsfájlt, és futtassa a szkriptet.
3. A Windows PowerShell ISE-ben, kattintson a **nézet** menüben, majd kattintson **parancsfájl ablaktábla megjelenítése**.
4. Másolja a következő, és illessze be a parancsfájl a Windows PowerShell ISE parancsfájl panelen.
   
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
6. A szkript úgy van beállítva, a Reporting Services rendszerhez. Ha azt szeretné, a parancsfájl futtatásához a Reporting Services, a "v11", a Get-WmiObject utasítást a névtér elérési útját verzió részét módosíthatja.
7. Futtassa a szkriptet.

**Érvényesítési**: annak ellenőrzéséhez, hogy működik-e a alapvető jelentéskészítő kiszolgáló működését, tekintse meg a [a konfiguráció ellenőrzése](#verify-the-configuration) későbbi szakaszában talál.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Parancsfájl használata a jelentéskészítő kiszolgáló és a HTTPS konfigurálása
A Windows PowerShell segítségével a konfigurálása a jelentéskészítő kiszolgáló, a következő lépéseket. A konfiguráció nem HTTP, HTTPS tartalmazza.

1. Az Azure Portalon válassza ki a virtuális Gépet, és kattintson a Csatlakozás gombra. A böngésző konfigurációtól függően előfordulhat, hogy kéri a virtuális Géphez való csatlakozáshoz egy .rdp-fájlt menteni.
   
    ![Csatlakozás az azure virtuális géphez](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) A felhasználói virtuális gép nevét, a felhasználónév és a virtuális gép létrehozásakor beállított jelszót használja. 
   
    Ha például az alábbi ábrán a virtuális gép neve van **ssrsnativecloud** és a felhasználónév **testuser**.
   
    ![bejelentkezési tartalmazza a virtuális gép neve](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Nyissa meg a virtuális gép **Windows PowerShell ISE-ben** rendszergazdai jogosultságokkal. A PowerShell ISE-ben a Windows server 2012 rendszeren alapértelmezés szerint telepítve van. Ajánlott az ISE-ben egy standard szintű Windows PowerShell-ablakot helyett használhatja, hogy a parancsfájl illessze be az ISE-ben, módosítsa a parancsfájlt, és futtassa a szkriptet.
3. Ahhoz, hogy a parancsfájlok futtatását, futtassa a következő Windows PowerShell-parancsot:
   
        Set-ExecutionPolicy RemoteSigned
   
    Ezután futtathatja a szabályzat ellenőrzése a következő:
   
        Get-ExecutionPolicy
4. A **Windows PowerShell ISE-ben**, kattintson a **nézet** menüben, majd kattintson **parancsfájl ablaktábla megjelenítése**.
5. Másolja a következő szkriptet, és illessze be a Windows PowerShell ISE parancsfájl panelen.
   
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
   
   * Ez egy **szükséges** paramétert. Ha a tanúsítvány értékét az előző lépésekben nem mentette, használja a következő módszerek egyikét a tanúsítvány-ujjlenyomat értékét másol a tanúsítványok ujjlenyomatát.:
     
       A virtuális gépen nyissa meg a Windows PowerShell ISE-ben, és futtassa a következő parancsot:
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       A kimenet az alábbihoz hasonlóan fog kinézni. A parancsfájl egy üres sort ad vissza, ha a virtuális gép nem rendelkezik egy tanúsítvánnyal, például konfigurálva, a részben [a virtuális gépek önaláírt tanúsítvány használatára](#to-use-the-virtual-machines-self-signed-certificate).
     
     VAGY
   * A virtuális gép futtatása az mmc.exe, majd adja hozzá a **tanúsítványok** beépülő modult.
   * Alatt a **Megbízható gyökérhitelesítő hatóságok** csomópontot, kattintson duplán a tanúsítvány nevét. Ha a virtuális gép az önaláírt tanúsítványt használ, a tanúsítvány neve megegyezik a virtuális gép DNS-nevét, és végződik **cloudapp.net**.
   * Kattintson a **részletek** fülre.
   * Kattintson a **ujjlenyomat**. Az ujjlenyomat értéke megjelenik a részletek mezőjében, például af 11 60 b6 4b 28 8 d 89 0a 82 12 ff 6b a9-es c3 66 4f 31 90 48
   * **A parancsfájl futtatása előtt**, távolítsa el a szóközöket a érték párok között. Például af1160b64b288d890a8212ff6ba9c3664f319048
7. Módosítsa a **$httpsport** paramétert: 
   
   * Ha követte a 443-as portot a HTTPS-végpont, majd nem kell frissíteni ezt a paramétert a szkriptben. Ellenkező esetben használja a kiválasztott titkos HTTPS-végpontot a virtuális gép konfigurálásakor-port értéke.
8. Módosítsa a **$DNSName** paramétert: 
   
   * A parancsfájl konfigurálva van egy helyettesítő tanúsítványt $DNSName = "+". Ha nem szeretne egy helyettesítő tanúsítvány kötésének konfigurálása, tegye megjegyzésbe $DNSName mégis ="+"és a következő sort, a teljes $DNSNAme referencia, ## $DNSName="$server.cloudapp.net engedélyezése".
     
       Módosítsa a $DNSName értékét, ha nem szeretné használni a virtuális gép DNS-név a Reporting Services rendszerhez. Használja a paramétert, ha a tanúsítvány is használnia kell ezt a nevet, és regisztrálja a globálisan a DNS-kiszolgáló nevét.
9. A szkript úgy van beállítva, a Reporting Services rendszerhez. Ha azt szeretné, a parancsfájl futtatásához a Reporting Services, a "v11", a Get-WmiObject utasítást a névtér elérési útját verzió részét módosíthatja.
10. Futtassa a szkriptet.

**Érvényesítési**: annak ellenőrzéséhez, hogy működik-e a alapvető jelentéskészítő kiszolgáló működését, tekintse meg a [a konfiguráció ellenőrzése](#verify-the-connection) későbbi szakaszában talál. A tanúsítvány kötés nyisson meg egy parancssort rendszergazdai jogosultságokkal, és futtassa a következő parancsot:

    netsh http show sslcert

Az eredmény a következő tartalmazza:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>A jelentéskészítő kiszolgáló konfigurálása a Configuration Manager használatával
Ha nem szeretné a jelentéskészítő kiszolgáló konfigurálása a PowerShell-parancsprogram futtatásával, kövesse az ebben a szakaszban a Reporting Services natív módú configuration manager használatához a jelentéskészítő kiszolgáló konfigurálása.

1. Az Azure Portalon válassza ki a virtuális Gépet, és kattintson a Csatlakozás gombra. A felhasználónév és a virtuális gép létrehozásakor beállított jelszót használja.
   
    ![Csatlakozás az azure virtuális géphez](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Futtassa a Windows update, és telepítse a frissítéseket a virtuális géphez. Ha a virtuális gép újraindításra szükség, indítsa újra a virtuális Gépet, és újból csatlakozik a virtuális gép az Azure Portalról.
3. A virtuális gépen a Start menüben írja be a következőt **Reporting Services** , és nyissa meg **Reporting Services Configuration Manager**.
4. Módosítsa az alapértelmezett értékeket a **kiszolgálónév** és **jelentéskészítő kiszolgáló példánya**. Kattintson a **Connect** (Csatlakozás) gombra.
5. A bal oldali ablaktáblán kattintson a **Web Service URL-cím**.
6. Alapértelmezés szerint a 80-as HTTP-porthoz "Az összes hozzárendelt" IP-Címmel rendelkező RS van konfigurálva. HTTPS hozzáadása:
   
   1. A **SSL-tanúsítvány**: válassza ki, például [virtuális gép neve] használni kívánt tanúsítványt. cloudapp.net. Ha nincsenek felsorolva tanúsítványok, tekintse meg a szakasz **2. lépés: hozzon létre egy kiszolgálói tanúsítványt** telepítéséről és a virtuális gép tanúsítvány megbízható információk.
   2. A **SSL-Port**: válassza ki a 443-as porton. Ha konfigurálta a privát HTTPS-végpontot a virtuális gépen más magánhálózati port, itt használni ezt az értéket.
   3. Kattintson a **alkalmaz** és várjon, amíg a művelet befejeződik.
7. A bal oldali ablaktáblán kattintson a **adatbázis**.
   
   1. Kattintson a **Databas módosítása**e.
   2. Kattintson a **hozzon létre egy új jelentéskészítő kiszolgáló adatbázisa** majd **tovább**.
   3. Hagyja meg az alapértelmezett **kiszolgálónév**: nevezze el a virtuális géppel, és hagyja meg az alapértelmezett **hitelesítési típus** , **aktuális felhasználó** – **integrált biztonsági**. Kattintson a **Tovább** gombra.
   4. Hagyja meg az alapértelmezett **adatbázisnév** , **ReportServer** kattintson **tovább**.
   5. Hagyja meg az alapértelmezett **hitelesítési típus** , **szolgáltatás hitelesítő adatai** kattintson **tovább**.
   6. Kattintson a **tovább** a a **összefoglalás** lapot.
   7. Ha a konfiguráció befejeződött, kattintson a **Befejezés**.
8. A bal oldali ablaktáblán kattintson a **Report Manager URL-cím**. Hagyja meg az alapértelmezett **virtuális könyvtár** , **jelentések** kattintson **alkalmaz**.
9. Kattintson a **kilépési** gombra kattintva zárja be a Reporting Services Configuration Manager.

## <a name="step-4-open-windows-firewall-port"></a>4. lépés: A Windows tűzfal Port megnyitása
> [!NOTE]
> Ha a parancsfájlok a jelentéskészítő kiszolgáló konfigurálása, kihagyhatja az ebben a szakaszban. A parancsfájl tartalmazza a tűzfalport megnyitása egy lépésben. Az alapértelmezett volt a HTTP-hez a 80-as és 443-as portot a HTTPS-hez.
> 
> 

Távolról csatlakozni a Jelentéskezelő vagy a jelentéskészítő kiszolgáló a virtuális gépen, a virtuális gép TCP-végpont szükséges. Szükség van a virtuális gép tűzfalban való megnyitásáról a ugyanazt a portot. A végpont jött létre, amikor a virtuális gép lett üzembe helyezve.

Ez a szakasz a tűzfalport megnyitása Ez a alapvető információkat. További információkért lásd: [tűzfal konfigurálása a jelentéskészítő kiszolgáló elérése](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> Ha a parancsfájl a jelentéskészítő kiszolgáló konfigurálása, kihagyhatja az ebben a szakaszban. A parancsfájl tartalmazza a tűzfalport megnyitása egy lépésben.
> 
> 

Ha konfigurálta a magánhálózati port HTTPS esetén 443-astól eltérő, módosítsa megfelelően a következő parancsfájlt. Port megnyitásához **443-as** a Windows tűzfalon, hajtsa végre a következőket:

1. Nyissa meg egy Windows PowerShell-ablakot rendszergazdai jogosultságokkal.
2. Ha nem a 443-as portot a HTTPS-végpontot a virtuális gép konfigurálásakor használt, frissítse az alábbi parancsban a portját, és futtassa a parancsot:
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. A parancs befejeződésekor **Ok** jelenik meg a parancssort.

Győződjön meg arról, hogy a port van megnyitva, nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsot:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése
Annak ellenőrzéséhez, hogy az alapszintű jelentés server most már működőképes, a böngészőben nyissa meg rendszergazdai jogosultságokkal, és keresse meg a következő jelentéskészítő kiszolgáló ad Jelentéskezelő URL-CÍMEK:

* A virtuális gépen keresse meg a jelentéskészítő kiszolgáló URL-címe:
  
        http://localhost/reportserver
* A virtuális gépen keresse meg a jelentés Manager URL-címe:
  
        http://localhost/Reports
* A helyi számítógépről, keresse meg a **távoli** Manager jelentést a virtuális gépen. Frissítse a DNS-név a következő példában szükség szerint. Amikor a rendszer jelszót kér, használja az volt a virtuális gép üzembe helyezésekor létrehozott rendszergazdai hitelesítő adatait. A felhasználó neve szerepel a [tartomány]\[felhasználónév] formátumot, ahol a tartomány-e a virtuális gép számítógép nevét, például ssrsnativecloud\testuser. Ha nem használja a HTTP**S**, távolítsa el a **s** URL-címét. Tekintse meg a következő szakaszban talál további felhasználók létrehozásával a virtuális Gépen.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* A helyi számítógépről keresse meg a távoli jelentéskészítő kiszolgáló URL-címe. Frissítse a DNS-név a következő példában szükség szerint. Ha a HTTPS nem használ, távolítsa el a s az URL-cím.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Felhasználók létrehozása és a szerepkörök hozzárendelésére
A jelentéskészítő kiszolgáló ellenőrzése és konfigurálása után a gyakori felügyeleti feladat egy vagy több felhasználó létrehozása és hozzárendelése a felhasználók a Reporting Services-szerepkörök. További információkért tekintse meg a következőket:

* [Helyi felhasználói fiók létrehozása](https://technet.microsoft.com/library/cc770642.aspx)
* [A jelentéskészítő kiszolgáló (Jelentéskezelő) jogokat a felhasználónak hozzáférést](https://msdn.microsoft.com/library/ms156034.aspx))
* [Hozzon létre és kezelhetik a szerepkör-hozzárendeléseket](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Hozhat létre és jelentések közzétételét az Azure virtuális gépen
Az alábbi táblázatban összefoglaltuk a helyi számítógépről a jelentéskészítő kiszolgálón, a Microsoft Azure virtuális gépen futó meglévő jelentések közzétételéhez elérhető beállításokról:

* **RS.exe parancsfájl**: használata RS.exe parancsfájl másolása a jelentéselemek és a meglévő jelentéskészítő kiszolgáló, a Microsoft Azure virtuális gépen. További információkért lásd: a "Natív üzemmódú natív üzemmódra – Microsoft Azure virtuális gép" szakasz a [minta Reporting Services rs.exe parancsfájl tartalmának át jelentéskészítő kiszolgáló közötti](https://msdn.microsoft.com/library/dn531017.aspx).
* **Jelentéskészítő**: A virtuális gépet tartalmaz, kattintson a – egyszer verzióját a Microsoft SQL Server jelentéskészítőt. A jelentés jelentéskészítő az első alkalommal elindítani a virtuális gépen:
  
  1. Rendszergazdai jogosultságokkal indítsa el a böngészőjében.
  2. Keresse meg a Jelentéskezelő virtuális gépen, és kattintson a **jelentéskészítő** a menüszalagon.
     
     További információkért lásd: [telepítése, eltávolítása és a jelentéskészítő támogató](https://technet.microsoft.com/library/dd207038.aspx).
* **SQL Server Data Tools: Virtuális gép**: SQL Server 2012 létrehozta a virtuális Gépet, akkor az SQL Server Data Tools a virtuális gépen telepítve van, és a létrehozásához használt **jelentéskészítő kiszolgáló projektek** és a jelentések a virtuális gépen. SQL Server Data Tools a jelentéseket tehetnek közzé a jelentéskészítő kiszolgáló a virtuális gépen.
  
    Ha létrehozta a virtuális gép az SQL server 2014, SQL Server Data Tools - BI for visual Studio is telepítheti. További információkért tekintse meg a következőket:
  
  * [A Microsoft SQL Server Data Tools – Business Intelligence Pro Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)
  * [A Microsoft SQL Server Data Tools – Business Intelligence a Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [SQL Server Data Tools és az SQL Server Business Intelligence (SSDT-BI)](https://docs.microsoft.com/sql/ssdt/previous-releases-of-sql-server-data-tools-ssdt-and-ssdt-bi)
* **SQL Server Data Tools: Távoli**: A helyi számítógépen, Reporting Services projekt létrehozása az SQL Server Data Tools, amely tartalmazza a Reporting Services-jelentéseket. A projekt szeretne csatlakozni a web service URL-cím konfigurálása.
  
    ![SSRS-projekthez az SSDT projekt tulajdonságai](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **Parancsfájl használata**: jelentéskészítő kiszolgáló tartalmának másolása a szkript használatával. További információkért lásd: [minta Reporting Services rs.exe parancsfájl tartalmának át jelentéskészítő kiszolgáló közötti](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Minimalizálja a költségeket, ha a virtuális gép nem használ
> [!NOTE]
> Költségek minimalizálása érdekében az Azure Virtual Machines amikor nincs használatban, állítsa le a virtuális Gépet az Azure Portalról. Ha egy virtuális Gépen belül a Windows energiagazdálkodási lehetőségek használatával állítsa le a virtuális Gépet, a virtuális gép továbbra is számítunk fel az ugyanilyen mértékben csökken. Költségek csökkentése érdekében állítsa le a virtuális gép az Azure Portalon kell. Ha már nincs szüksége a virtuális Gépet, ne felejtse el törölni a virtuális gép és a hozzárendelt .vhd-fájlok tárolási díjak elkerülése érdekében. További információkért lásd a gyakori kérdésekkel foglalkozó szakaszban található [Virtual Machines díjszabását](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>További információ
### <a name="resources"></a>További források
* Egyetlen kiszolgáló telepítéséhez SQL Server Business Intelligence és a SharePoint 2013-hoz kapcsolódó hasonló tartalmakat, lásd: [Windows PowerShell-lel történő hozzon létre egy Azure virtuális gép az SQL Server BI és a SharePoint 2013](https://blogs.technet.microsoft.com/ptsblog/2013/10/24/use-powershell-to-create-a-windows-azure-vm-with-sql-server-bi-and-sharepoint-2013/).
* Az SQL Server Business Intelligence az Azure Virtual machines gépeken üzemelő példányok kapcsolatos általános információkért lásd: [SQL Server Business Intelligence az Azure Virtual machines gépeken](virtual-machines-windows-classic-ps-sql-bi.md).
* Azure-beli számítási díjak kapcsolatos további információkért tekintse meg a virtuális gépek lapján [Azure díjkalkulátorát](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Közösségi tartalom
* Parancsfájl használata nélkül egy Reporting Services natív üzemmódú jelentéskészítő kiszolgálót létrehozásáról részletes utasításokért lásd: [üzemeltető SQL Reporting Service Azure virtuális gépen](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Az Azure virtuális gépeken futó SQL Server egyéb forrásokra mutató hivatkozásokat
[Az SQL Server használata az Azure Virtual Machines – áttekintés](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

