<properties
    pageTitle="Az Azure Stack POC üzembe helyezése | Microsoft Azure"
    description="Ebből a cikkből megtudhatja, hogyan készítheti elő az Azure Stack POC-t, és hogyan futtathatja a PowerShell-parancsfájlt az Azure Stack POC üzembe helyezéséhez."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/29/2016"
    ms.author="erikje"/>


# Az Azure Stack POC üzembe helyezése
Az Azure Stack POC üzembe helyezéséhez először [elő kell készítenie az üzembe helyezési gépet](#prepare-the-deployment-machine), majd [futtatnia kell a PowerShell telepítési parancsfájlt](#run-the-powershell-deployment-script).

## Az üzembe helyezési gép előkészítése

1. Győződjön meg arról, hogy kapcsolódni tud fizikailag az üzembe helyezési géphez, vagy rendelkezik fizikai konzolhozzáféréssel (például KVM-mel). Szüksége lesz ilyen hozzáférésre azután, hogy a 9. lépésben újraindítja az üzembe helyezési gépet.
 
2. Győződjön meg arról, hogy az üzembe helyezési gép megfelel a [minimális követelményeknek](azure-stack-deploy.md). Használhatja a [Deployment Checker for Azure Stack Technical Preview 1](https://gallery.technet.microsoft.com/Deployment-Checker-for-76d824e1) szolgáltatást a követelmények megerősítéséhez.

3.  [Töltse le](http://aka.ms/ReqOSforAzureStack) és telepítse a Windows Server 2016 Datacenter Edition Technical Preview 4 hu-HU verzióját (Teljes verzió).

4.  [Töltse le](https://azure.microsoft.com/overview/azure-stack/try/?v=try) az Azure Stack POC üzembe helyezési csomagot a C meghajtó egyik mappájába, (például c:\\AzureStack).

5.  Futtassa a **Microsoft Azure Stack POC.exe** fájlt.

    Ez létrehozza a \\Microsoft Azure Stack POC\\ mappát, amely a következő elemeket tartalmazza:

    -   DeployAzureStack.ps1: Azure Stack POC telepítési PowerShell-parancsfájl

    -   MicrosoftAzureStackPOC.vhdx: Azure Stack adatcsomag

    -   SQLServer2014.vhdx: SQL Server VHD

    -   WindowsServer2012R2DatacenterEval.vhd

    -   WindowsServer2016Datacenter.vhdx: Windows Server 2016 Datacenter VHD (3124262 KB)

    > [AZURE.IMPORTANT] Legalább 128 GB szabad területre van szükség a fizikai rendszerindítási köteten.

6. Másolja át a WindowsServer2016Datacenter.vhdx fájlt a C:\ meghajtóra, és nevezze át MicrosoftAzureStackPOCBoot.vhdx-re.

7. A Fájlkezelőben kattintson jobb gombbal a MicrosoftAzureStackPOCBoot.vhdx elemre, majd kattintson a **Csatlakoztatás** lehetőségre.

8. Nyisson meg rendszergazdaként egy parancssori ablakot, és futtassa az alábbi bcdboot parancsot. Ez a parancs létrehoz egy kétrendszeres környezetet. Ettől kezdve a fenti rendszerindítási beállítással indítsa el a gépet.

        bcdboot <mounted drive letter>:\windows

9. Indítsa újra a gépet. Automatikusan futtatni fogja a Windows telepítőt, amint a VHD rendszer elkészül. Ezt követően fizikailag kapcsolódnia kell az üzembe helyezési géphez, vagy fizikai konzolhozzáféréssel kell rendelkeznie a következő lépések teljesítéséhez.

10. Ha a BIOS rendszer tartalmaz ilyen beállítást, úgy konfigurálja, hogy a helyi időt használja az UTC idő helyett.

11. Ha a rendszer kéri, adja meg az ország, a nyelv, a billentyűzet beállításait vagy az egyéb beállításokat. Ha a telepítő kéri a termékkulcsot, itt találhatja meg: [Rendszerkövetelmények és telepítési információk](https://technet.microsoft.com/library/mt126134.aspx).

12. Ha a rendszer kéri, állítsa be a rendszergazdai fiók jelszavát, és jelentkezzen be azzal a fióknévvel és jelszóval.

13. Ha nem rendelkezik DHCP-kiszolgálóval, az újraindítás és a bejelentkezés után hozza létre a statikus konfigurációt a hálózati adaptereken.

14. Engedélyezze a Távoli asztali kapcsolatokat. Ehhez lépjen a **Rendszer tulajdonságai** menübe, és válassza a **Bejövő távoli kapcsolatok engedélyezése** lehetőséget.

15. Jelentkezzen be egy rendszergazdai engedélyekkel rendelkező helyi fiókkal.

16. Ellenőrizze, hogy az Azure Stack POC adataihoz rendelkezésre álló **pontosan** négy meghajtó:
  - Látható-e a Lemezkezelőben
  - Nincs-e használatban
  - Online, nem lefoglalt állapotúként jelenik-e meg

17. Győződjön meg arról, hogy a gazdagép nem csatlakozik tartományhoz.

18. Az Internet Explorerrel ellenőrizze a hálózati kapcsolatot az Azure.com webhelyhez.

> [AZURE.IMPORTANT] A TP1 POC üzembe helyezése pontosan négy meghajtót támogat a tárolási szolgáltatásokhoz, és csak egy hálózati adaptert a hálózatkezeléshez.
>
> - **A tároláshoz** használjon eszközkezelőt vagy WMI-t a többi meghajtó letiltására (a lemezek lemezkezelővel történő offline állapotba helyezése nem elegendő).
>
> - **A hálózatnál**, ha több hálózati adapterrel rendelkezik, győződjön meg róla, hogy csak egy van engedélyezve (és a többi le van tiltva), mielőtt futtatja az alábbi üzembe helyezési parancsfájlt.
>
> Ha a fent meghatározott VHD-rendszerindítási lépéseket használta, szüksége lesz ezekre a frissítésekre a VHD-ből való indítás után, mielőtt elindítaná az üzembe helyezési parancsfájlt.

## A PowerShell üzembe helyezési parancsfájl futtatása

1. Helyezze át az alábbi üzembe helyezési fájlokat a D:\Microsoft Azure Stack POC\ mappából a C:\Microsoft Azure Stack POC mappába\.
    - DeployAzureStack.ps1
    - MicrosoftAzureStackPOC.vhdx
    - SQLServer14.vhdx
    - WindowsServer2012R2DatacenterEval.vhd
    - WindowsServer2016Datacenter.vhdx

2.  Nyissa meg a PowerShellt rendszergazdaként.

3.  A PowerShellben nyissa meg az Azure Stack mappát (\\Microsoft Azure Stack POC\\, ha az alapértelmezést használta).

4.  Futtassa az üzembe helyezési parancsot:

        .\DeployAzureStack.ps1 –Verbose

    Kínában használja helyette az alábbi parancsot:

        .\DeployAzureStack.ps1 –Verbose -UseAADChina $true

    Megkezdődik az üzembe helyezés, és az Azure Stack POC tartománynév azurestack.local tartománnyal lesz kódolva.

5.  A **Enter the password for the built-in administrator** (Jelszó beírása beépített rendszergazdához) kérésnél írjon be egy jelszót, majd erősítse meg. Ez lesz az összes virtuális gép jelszava. Ne felejtse el feljegyezni ezt a szolgáltatás-rendszergazdai jelszót.

6.  A **Please login to your Azure account in the pop-up Azure authentication page** (Kérjük, jelentkezzen be az Azure-fiókjába az előugró Azure hitelesítési oldalon) kérdésnél nyomjon meg egy billentyűt a Microsoft Azure bejelentkezési párbeszédpanelének megnyitásához.

7.  Adja meg az Azure Active Directory-fiók hitelesítő adatait. A felhasználónak globális rendszergazdának kell lennie a címtárbérlőben

8.  A PowerShellbe visszatérve a fiókválasztás megerősítési kérésénél írja be az *y* karaktert. Ez létrehoz két felhasználót és három alkalmazást az Azure Stack számára abban a címtárbérlőben: egy rendszergazdai felhasználót az Azure Stackhez, egy bérlő felhasználót a TiP tesztekhez, valamint egy-egy alkalmazást a Portal, az API és a Monitoring erőforrás-szolgáltatókhoz. Emellett a telepítő Azure PowerShell, XPlat CLI és Visual Studio jóváhagyásokat is hozzáad a címtárbérlőhöz.

9.  A **Microsoft Azure Stack POC is ready to deploy. Continue?** (Microsoft Azure Stack POC készen áll a telepítésre. Folytatja?) kérdésnél írja be az *y* karaktert.

10.  Az üzembe helyezési folyamat néhány órát fog igénybe venni, amelynek során a rendszer többször is automatikusan újra fog indulni. Ha az üzembe helyezés közben bejelentkezik, a rendszer automatikusan el fog indítani egy PowerShell-ablakot, ami ki fogja jelezni a telepítés állapotát. A PowerShell-ablak az üzembe helyezés befejezésével bezárul.

11. Az Azure Stack POC gépen jelentkezzen be AzureStack\administrator felhasználóként, nyissa meg a **Kiszolgálókezelőt**, majd kapcsolja ki az **Internet Explorer – Fokozott biztonsági beállításokat** a rendszergazdák és a felhasználók számára.

Ha az üzembe helyezés idő- vagy dátumhibával hiúsul meg, konfigurálja a BIOS rendszert, hogy a helyi időt használja az UTC helyett. Ezt követően telepítse újra.

Ha a parancsfájl futtatása sikertelen, indítsa újra a parancsfájlt. Ha a hiba továbbra is fennáll, törölje le és indítsa újra.

A parancsfájl naplóját a POC gazdagépen találhatja: `C:\ProgramData\microsoft\azurestack`.

### A DeployAzureStack.ps1 opcionális paraméterei

**AADCredential** (PSCredential) – Beállítja az Azure Active Directory felhasználónevet és jelszót. Ha ez a paraméter nincs megadva, a parancsfájl kérni fogja a felhasználónevet és a jelszót.

**AADTenant** (karakterlánc) – Beállítja a bérlőcímtárat. Ezt a paramétert egy adott címtár megadására használhatja, ha az AAD-fiók engedéllyel rendelkezik több címtár kezelésére. Ha ez a paraméter nincs megadva, a parancsfájl kérni fogja a címtárat.

**AdminPassword** (SecureString) – Beállítja az alapértelmezett rendszergazdai jelszót. Ha ez a paraméter nincs megadva, a parancsfájl kérni fogja a jelszót.

**Force** (Kapcsoló) – Beállítja, hogy a parancsmag megerősítések nélkül fusson.

**NATVMStaticGateway** (Karakterlánc) – Beállítja a statikus IP-címben használt alapértelmezett átjárót a NATVM számára. Csak akkor használja ezt a paramétert, ha a DHCP nem tud érvényes IP-címet rendelni az internet eléréséhez. Ha használja ezt a paramétert, akkor a NATVMStaticIP paramétert is használnia kell.
Például: `.\DeployAzureStack.ps1 –Verbose -NATVMStaticIP 10.10.10.10/24 – NATVMStaticGateway 10.10.10.1`

**NATVMStaticIP** (karakterlánc) – Beállít egy további statikus IP-címet a NATVM számára. Csak akkor használja ezt a paramétert, ha a DHCP nem tud érvényes IP-címet rendelni az internet eléréséhez.
Például: `.\DeployAzureStack.ps1 –Verbose -NATVMStaticIP 10.10.10.10/24`

**NoAutoReboot** (Kapcsoló) – Beállítja, hogy a parancsfájl automatikus újraindítások nélkül fusson.

**ProxyServer** (Karakterlánc) – Beállítja a proxyadatokat. Csak akkor használja ezt a paramétert, ha a környezetének proxyt kell használnia az internet eléréséhez. A hitelesítő adatokat igénylő proxykiszolgálók nem támogatottak.
Például: `.\DeployAzureStack.ps1 -Verbose -ProxyServer 172.11.1.1:8080`

**PublicVLan** (Karakterlánc) – Beállítja a VLAN-azonosítót. Csak akkor használja ezt a paramétert, ha a gazdagépnek és a NATVM-nek VLAN-azonosítót kell beállítania a fizikai hálózat (és az internet) eléréséhez.
Például: `.\DeployAzureStack.ps1 –Verbose –PublicVLan 305`

**TIPServiceAdminCredential** (PSCredential) – Beállítja egy meglévő szolgáltatás-rendszergazdai Azure Active Directory-fiók hitelesítő adatait. Ezt a fiókot a TiP (Éles környezetben végzett tesztelés) használja. Ha ez a paraméter nincs megadva, automatikusan létrejön egy fiók.

**TIPTenantAdminCredential** (PSCredential) – Beállítja egy meglévő bérlői rendszergazdai Azure Active Directory-fiók hitelesítő adatait. Ezt a fiókot a TiP (Éles környezetben végzett tesztelés) használja. Ha ez a paraméter nincs megadva, automatikusan létrejön egy fiók.

**UseAADChina**(Boolean) – Állítsa ezt a Boolean paramétert $true értékre, ha Azure China-val (Mooncake) kívánja üzembe helyezni a Microsoft Azure Stack POC-t.

## Az automatikus TiP tesztek kikapcsolása (nem kötelező)

A Microsoft Azure Stack Technical Preview 1 ellenőrző teszteket tartalmaz, amelyeket a rendszer az üzembe helyezési folyamat során, és ismétlődő napi ütemezés szerint használ. Ezek az Azure Stack bérlő által végzett műveleteket szimulálják, és Test-in-POC (TiP) felhasználói fiókokat hoznak létre az Azure Active Directoryban a tesztek futtatásához. Sikeres üzembe helyezést követően ki lehet kapcsolni ezeket a TiP teszteket. 

**Az automatikus TiP tesztek kikapcsolása**

  - A ClientVM gépen futtassa a következő parancsmagot:

  `Disable-ScheduledTask -TaskName AzureStackSystemvalidationTask`

**A teszteredmények megtekintése**

  - A ClientVM gépen futtassa a következő parancsmagot:

  `Get-AzureStackTiPTestsResult`



## A Microsoft Azure Stack POC telemetriájának kikapcsolása (nem kötelező)


A Microsoft Azure Stack POC üzembe helyezése előtt kikapcsolhatja a Microsoft Azure Stack telemetriáját a gépen, amelyről az üzembe helyezést végzik. A funkció egyetlen gépen való kikapcsolásával kapcsolatban tekintse meg a következőt: [http://windows.microsoft.com/en-us/windows-10/feedback-diagnostics-privacy-faq](http://windows.microsoft.com/en-us/windows-10/feedback-diagnostics-privacy-faq), és módosítsa a **Diagnosztikai és a használati adatok** beállítást **Alapszintűre**.



A Microsoft Azure Stack POC üzembe helyezése után kikapcsolhatja a telemetriát azokon virtuális gépeken, amelyeket csatlakoztak az Azure Stack tartományhoz. Az ezeken a virtuális gépeken érvényes csoportházirend létrehozásával és a telemetriai beállítások kezelésével kapcsolatban tekintse meg a következőt: [https://technet.microsoft.com/library/mt577208(v=vs.85).aspx\#BKMK\_UTC](https://technet.microsoft.com/library/mt577208%28v=vs.85%29.aspx#BKMK_UTC), és válassza ki a **0** vagy az **1** értéket a **Telemetria engedélyezése** csoportházirendnél. Két virtuális gép (bgpvm és natvm) nem csatlakozik az Azure Stack-tartományhoz. Ha a két virtuális gép visszajelzési és diagnosztikai beállításait külön-külön kívánja megadni, tekintse át a következőt: [http://windows.microsoft.com/en-us/windows-10/feedback-diagnostics-privacy-faq](http://windows.microsoft.com/en-us/windows-10/feedback-diagnostics-privacy-faq).

## Következő lépések

[Csatlakozás az Azure Stackhez](azure-stack-connect-azure-stack.md)



<!--HONumber=Sep16_HO3-->


