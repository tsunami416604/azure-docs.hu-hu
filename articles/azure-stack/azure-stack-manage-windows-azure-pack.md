---
title: "Windows Azure Pack virtuális gépek Azure-veremből kezelése |} Microsoft Docs"
description: "Útmutató a Windows Azure Pack (WAP) virtuális gépek kezelése a felhasználói portál Azure-készletben."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: a7e4896c84938b392a86f4d9609c4932324c785d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>Windows Azure Pack virtuális gépek Azure-veremből kezelése

*A következőkre vonatkozik: Azure szoftverfejlesztői készletet*

A csomagban Azure verem engedélyezheti a hozzáférést a Windows Azure Pack futó virtuális gépek bérlői Azure verem felhasználói portálról. Felhasználók a verem Azure portál segítségével kezelik a meglévő infrastruktúra-szolgáltatási virtuális gépeket és virtuális hálózatok. Ezeket az erőforrásokat elérhetővé válnak a Windows Azure Pack az alapul szolgáló Service Provider Foundation (SPF) és a Virtual Machine Manager (VMM) keresztül. Pontosabban a felhasználók is:

* Keresse meg az erőforrások
* Vizsgálja meg a konfigurációs értékek
* A virtuális gép indítása vagy leállítása
* Csatlakozás virtuális géphez keresztül a távoli asztal protokoll (RDP)
* Ellenőrzőpontjainak létrehozása és kezelése
* Törölje a virtuális gépek és virtuális hálózatok

Ez a funkció a Windows Azure Pack csatlakozás Azure verem (előzetes verzió) biztosítja. Ez a cikk bemutatja, hogyan konfigurálja az összekötőt egy egy csomópontos Azure verem környezethez.

Az előzetes kiadásban az összekötő vegye figyelembe a következőket:
* Használja a Windows Azure Pack-összekötő csak tesztelési környezetben (az Azure-verem és a Windows Azure Pack), és nem az éles környezetekben.
* Windows Azure Pack frissítés Rollup (UR) 9.1 vagy újabb és a System Center SPF és a 9-es vagy újabb verzió VMM UR kell rendelkeznie.
* A VMM és az SPF összetevőt a System Center 2012 R2 vagy a System Center 2016 lehet.
* Mindkét Azure veremben, és a Windows Azure Pack konfigurációs lépéseket kell végrehajtani.
* Az utasítások nem - Cloud Platform System CPS környezetek vonatkozik.
* Az ismert problémák ellenőrzéséhez tekintse meg [Microsoft Azure verem hibaelhárítási](azure-stack-troubleshooting.md).


## <a name="architecture"></a>Architektúra
Az alábbi ábrán látható, a Windows Azure Pack összekötő fő összetevőit.

![A Windows Azure Pack összekötő összetevők](media/azure-stack-manage-wap/image1.png)

Figyelje meg a következő adatokat:
* Az Azure-verem felhasználói portálon keresztül fér hozzá az erőforrás-információkat mindkét felhőben (Azure verem és a Windows Azure Pack).
* A felhasználónak rendelkeznie kell egy fiókot, amely mindkét környezetben érvényes.
* Az Azure-verem felhasználói portál a Windows Azure Pack futó összetevők hálózati hozzáférést kell biztosítani.
* Az a **WAP** szakasz a diagramon, megjelenik az új Windows Azure Pack összekötő modulok (WAP-bővítményt, és összekötő) és a meglévő Windows Azure Pack bérlői API a SPF és a VMM összetevővel.


## <a name="identity-management"></a>Identitáskezelés
A Windows Azure Pack bérlői API megbízhatónak kell lennie az Azure-verem biztonságijogkivonat-szolgáltatás (STS).

Amikor egy felhasználó egy műveletet a verem Azure portálon keresztül, amely ezeket az erőforrásokat a Windows Azure Pack hajt végre, a portál alkalmaz, a Windows Azure Pack bérlői API. Ezért a megadott felhasználói hitelesítési jogkivonat a megbízható STS kell származnia. Az alábbi ábrában láthat:

![Windows Azure Pack összekötő hitelesítés diagramja](media/azure-stack-manage-wap/image2.png)

A fejlesztési kit környezetben a Windows Azure Pack és Azure verem független identitás-szolgáltatóktól kell. Felhasználók számára, akik mindkét környezetben a verem Azure portálról egyszerű felhasználónév (UPN) megegyező felhasználónévvel kell rendelkeznie mindkét identitás-szolgáltatóktól. Például a fiók  *azurestackadmin@azurestack.local*  is szerepelnie kell a STS a Windows Azure Pack csomaghoz. Az AD FS nincs beállítva kimenő bizalmi kapcsolatok támogatásához, ha az AD FS Azure verem példányát megbízhatósági létrehozza a Windows Azure Pack összetevői (bérlői API-t).

## <a name="prerequisites"></a>Előfeltételek

### <a name="download-the-windows-azure-pack-connector"></a>A Windows Azure Pack-összekötő letöltése
Az a [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc), töltse le az .exe fájlt, és bontsa ki a helyi számítógépen. A tartalom később, olyan számítógépre, amelyen férhetnek hozzá a Windows Azure Pack környezet másolása.

### <a name="deployment-option-requirement"></a>Központi telepítési beállítás követelmény
Windows Azure Pack integrálása, Azure verem telepítheti az AD FS vagy az Azure Active Directory-beállítás használatával.

### <a name="connectivity-requirements"></a>Hálózati kapcsolati követelményeinek
1. A számítógépről, amelyen az Azure-verem felhasználói portál eléréséhez, ellenőrizze, hogy a webböngészőn keresztül érheti el a Windows Azure Pack bérlői portált.
2. AzS-WASP01 virtuális gépet az Azure-verem kapcsolódnia kell a Windows Azure Pack bérlői portál számítógépen kell lennie. Ping.exe segítségével ellenőrizze a hálózati kapcsolatot. 
3.  Az új összekötő-szolgáltatások érvényes tanúsítványok kell rendelkeznie. Ezek az SSL-tanúsítványok egy megbízható hitelesítésszolgáltató (CA) kell kiállítania. Önaláírt tanúsítványok nem használhatók. Az SSL-tanúsítványok Azure verem (pontosabban a AzS-WASP01 VM) és a bérlő az Azure-verem felhasználói portál eléréséhez használhatja más számítógépek megbízhatónak kell lennie.
 
    >[!NOTE]
    Mivel AzS-WASP01 a Server Core telepítési lehetőség a Windows Server fut, például Certutil.ext parancssori eszköz segítségével importálja a tanúsítványt. Például akkor képes a AzS-WASP01 c:\temp másolja a .cer fájlt, és futtassa a parancsot **"CA" "c:\temp\certname.cer" certutil - addstore**.

4.  A Windows Azure Pack bérlői virtuális gépek a verem Azure portálon keresztül RDP-kapcsolatot létrehozni, a Windows Azure Pack környezet lehetővé kell tenni a bérlői virtuális gépek távoli asztali forgalmat.
5.  Azure verem bérlői virtuális gépek és a Windows Azure Pack bérlői virtuális gépek közötti kapcsolat külső IP-címeket kell irányítható a két környezet között. Ez az összekapcsolhatóság társítása a különböző környezetek között a virtuális gép nevek feloldása DNS-kiszolgáló is lehetnek.

### <a name="iis-requirements"></a>Az IIS-követelmények
A Windows Azure Pack bérlői portál (amely lehet egy fizikai gazdagép, a virtuális gép vagy a több virtuális gép) futtató számítógépen telepített URL-újraíró IIS kiterjesztéssel kell rendelkeznie. Ha még nincs telepítve, letöltheti a [Itt](https://www.iis.net/downloads/microsoft/url-rewrite). Ha több virtuális gép a bérlői portál futtatására, telepítse mindegyik virtuális gépre bővítmény.

## <a name="configure-azure-stack"></a>Az Azure verem konfigurálása
A Windows Azure Pack-összekötő konfigurálása előtt engedélyeznie kell a több felhőalapú mód az Azure-verem felhasználói portálon. Ez a mód lehetővé teszi, hogy a felhasználóknak, hogy mely felhőből erőforrások eléréséhez.

Több felhőalapú mód engedélyezéséhez futtatnia kell az Add-AzurePackConnector.ps1 parancsfájl Azure verem telepítést követően. A következő táblázat ismerteti a parancsfájl paramétereit.


|  Paraméter | Leírás | Példa |   
| -------- | ------------- | ------- |  
| AzurePackClouds | A Windows Azure Pack csatlakozók URI. Az URI-k meg kell egyeznie a Windows Azure Pack bérlői portálon. | @{CloudName = "AzurePack1"; CloudEndpoint = "https://waptenantportal1:40005"},@{CloudName = "AzurePack2"; CloudEndpoint = "https://waptenantportal2:40005"}<br><br>  (Alapértelmezés szerint a port érték 40005.) |  
| AzureStackCloudName | A helyi Azure verem felhő képviselő címkéje.| "AzureStack" |
| DisableMultiCloud | A kapcsoló több felhőalapú mód letiltása.| – |
| | |

Az Add-AzurePackConnector.ps1 parancsprogrammal a központi telepítést követően azonnal, vagy újabb. A parancsfájl futtatásához a telepítés után azonnal használja az azonos Windows PowerShell-munkamenetben, ahol Azure Alkalmazásveremben üzembe befejeződött. Ellenkező esetben nyithatja meg egy új Windows PowerShell-munkamenetet rendszergazdaként (bejelentkezett a azurestackadmin fiókot).

1. Futtassa az Add-AzurePackConnector.ps1 parancsfájlt az alábbi parancsokat (az adott környezetre értékek) használatával. Figyelje meg, hogy az Add-AzurePackConnector parancsfájl lehetővé teszi a Windows Azure Pack-összekötő több végpont hozzáadása.
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> Az aktuális probléma van az adott után az Add-AzurePackConnector parancsfájl lejártát követően továbbra is egy lekérdezési ciklusban hosszabb idő (több percig) amíg végződik. Miután meggyőződött arról, hogy az üzenet **részletes: lépés (konfigurálása Azure Pack összekötő) állapotát: "Sikeres"**, leállítani a parancsfájl, vagy várjon, amíg önmagában leállítja. Eltérés azt nem végre, mert a konfiguráció már sikeresen befejeződött.

2. Jegyezze meg a kimeneti fájlok, amelyek ezt a parancsfájlt, a megadott Windows Azure Pack környezetben hozzák létre. A fájlok találhatók: \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput. Ezek a fájlok a Windows Azure Pack környezetekben konfigurálásához szükséges információkat tartalmazzák. Ez a fájl paraméterként adja át később a parancsfájl ezeket az utasításokat. Ez a fájl a következő információkat tartalmazza:

    * **AzurePackConnectorEndpoint**: a Windows Azure Pack Connectort a végpontot tartalmaz.
    * **AuthenticationIdentityProviderPartner**: a következő érték pár tartalmazza:
        * Hitelesítési jogkivonat-aláíró tanúsítvány, amelyet a Windows Azure Pack bérlői API megbízhatónak tart a verem Azure-portálbővítményt a hívásokat fogadni.

        * A "tartományi" társított az aláíró tanúsítvány. Például: https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/.

3.  Keresse meg a kimeneti fájlokat tartalmazó mappát (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput), és másolja a fájlokat a helyi számítógépen. A fájlok ehhez hasonlóan fog kinézni: AzurePack-06-27-15-50.txt.

4.  Tesztelje a konfigurációt.

    a. Nyissa meg a böngészőt, és jelentkezzen be a Azure verem felhasználói portál (https://portal.local.azurestack.external/).
    
    b. A bejelentkezést a bérlő és a portál terhelések, látni fogja, nem tudnak majd előfizetések vagy kiterjesztések beolvasása az Azure Pack felhőből kapcsolatos hibákat. Kattintson a **OK** kattintva zárja be ezeket az üzeneteket. (Ezek a hibaüzenetek eltűnik majd Windows Azure Pack konfigurálása után.)

    c. Figyelje meg a **felhő** legördülő listából válassza ki a portál bal felső sarkában.

    ![A felhő választó az Azure-verem felhasználói felületen](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>Konfigurálja a Windows Azure Pack
A Connector előzetes verziója csak a Windows Azure Pack manuális konfigurálást igényel.

>[!IMPORTANT]
Az előzetes kiadás használja a Windows Azure Pack-összekötő csak tesztelési környezetben, és nem az éles környezetekben.

1.  Összekötő MSI-fájlok a Windows Azure Pack bérlői portál virtuális gépen, és telepítse a tanúsítványokat. (Ha több bérlői portál virtuális gépeket, el kell végeznie ezt a lépést minden egyes virtuális gépen.)

    a. A Fájlkezelőben, másolja a **WAPConnector** mappa (mi korábban letöltött) nevű **c:\temp** a bérlői portál virtuális gépen.

    b. Nyissa meg a bérlői portál virtuális gépek konzol vagy RDP kapcsolódni.

    c. Módosítsa a könyvtárat a **c:\temp\wapconnector\setup\scripts**, és futtassa a **Install-Connector.ps1** parancsfájl három MSI-fájlok telepítése. Nincsenek paraméterei szükség.

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d. Módosítsa a könyvtárat a **c:\inetpub** , és ellenőrizze, hogy telepítve vannak-e a három új helyeket:

       * MgmtSvc-Connector

       * MgmtSvc-ConnectorExtension

       * MgmtSvc-ConnectorController

    e. Az azonos **c:\temp\wapconnector\setup\scripts** mappa, futtassa a **konfigurálása-Certificates.ps1** parancsfájlt tanúsítványok telepítéséhez. Alapértelmezés szerint ugyanazt a tanúsítványt, amelyet a Windows Azure Pack bérlői portált fogja használni. Győződjön meg arról, hogy ez egy érvényes tanúsítványt (az Azure verem AzS-WASP01 virtuális gép és a verem Azure portal hozzáférő minden ügyfélszámítógép megbízható). Ellenkező esetben a kommunikáció nem fognak működni. (Másik lehetőségként explicit módon átadhatók a tanúsítvány-ujjlenyomat paraméterként használatával a - ujjlenyomatra vonatkozó paramétere.)

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f. Az alábbi három szolgáltatások konfigurációjának befejezéséhez futtassa a **konfigurálása-WapConnector.ps1** parancsfájl a Web.config fájl paramétereinek frissítése.

    |  Paraméter | Leírás | Példa |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | A Windows Azure Pack bérlői portál teljes Tartományneve. | tenant.contoso.com | 
    | TenantAPIFQDN | A Windows Azure Pack bérlői API teljes Tartománynevét. | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | Az Azure-verem felhasználói portál teljes Tartományneve. | portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    g. Ha több bérlői portál virtuális gépek, ezek a virtuális gépek mindegyikének ismételje meg az 1. lépés.

2. A bérlői új API MSI telepítése a Windows Azure Pack bérlői API virtuális gépeken.

    a. Ha egy terhelés-kiegyenlítő használja, érdemes lehet a virtuális gép offline állapotúként jelöli.

    b. A Fájlkezelőben, másolja a **WAPConnector** nevű mappa **c:\temp** minden egyes bérlői API-gépen.

    c. Másolja a korábban mentett AzurePackConnectorOutput.txt fájlt a **c:\temp\WAPConnector**.

    d. Nyissa meg a konzol vagy RDP kapcsolatot a bérlői API VM másolt fájlokat.
    
    e. Módosítsa a könyvtárat a **c:\temp\wapconnector\setup\scripts**, és futtassa **frissítés-TenantAPI.ps1**. A WAP-bérlői API ezen új verziójával egy módosítása lehetővé teszi a megbízhatósági kapcsolat nem csak az aktuális STS a, hanem az Active Directory összevonási szolgáltatások, Azure verem példányát tartalmazza.

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.  Ismételje meg a 2. lépés a bérlői API-t használ másik virtuális géphez.
3. A **csak egy** bérlői API virtuális gépek, a konfigurálás-TrustAzureStack.ps1 parancsprogrammal a bérlői API-t és az AD FS-példányt közötti megbízhatósági kapcsolat hozzáadása az Azure-veremben. A sysadmin (rendszergazda) az adatbázis hozzáférése meg Microsoft.MgmtSvc.Store olyan fiókot kell használnia. Ezt a parancsfájlt az alábbi paraméterekkel rendelkezik:

    | Paraméter | Leírás | Példa |
    | --------- | ------------| ------- |
   | SqlServer | A Microsoft.MgmtSvc.Store adatbázist tartalmazó SQL Server neve. Ez a paraméter megadása kötelező. | SQLServer | 
   | DataFile | A kimeneti fájl, amely korábban az Azure-verem több felhőalapú mód konfigurálása során létrejött. Ez a paraméter megadása kötelező. | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | Azt jelzi, hogy a parancsfájl kell kérni interaktív módon az SQL Server-példányhoz való kapcsolódáskor használandó SQL-hitelesítés hitelesítő adatokat. A megadott hitelesítő adatokat kell jogosultsága adatbázisokat, sémákat, távolítsa el, és törölje a felhasználói bejelentkezések. Ha nincs megadva, a parancsfájl azt feltételezi, hogy a jelenlegi felhasználói környezet hozzáfér. | Nincs érték szükséges. |
   |  |  |

    Ha nem tudja használni az SQL Server, végezni a felderítésüket. Csatlakoztassa a bérlői API-számítógéphez, használja a Unprotect-MgmtSvc parancsot a bérlői API Web.config fájlt a védelmének megszüntetéséhez, és keresse meg a kiszolgáló nevét a kapcsolati karakterláncban. Ne felejtse el Protect-MgmtSvc újra, hogy a bérlői API Web.config fájl futtatása.

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>Példa
A következő példa bemutatja a teljes Windows Azure Pack-összekötő központi telepítés egy egy csomópontos Azure verem konfigurációja és két Windows Azure Pack Expressz telepítés. (Minden egyes Expressz telepítés van egy számítógépen, a példában nevű *wapcomputer1* és*wapcomputer2*.)

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
Az .exe-fájl letöltésére a [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc), bontsa ki és másolja a WAPConnector mappát egy **c:\temp** mappába a Windows Azure Pack-számítógépen. Másolja a fájlokat, az előző parancsfájl kimenete okozó (helye: \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) számára a **c:\temp\WAPConnector** mappa. (A fájlok a következőhöz hasonló lesz: AzurePack-06-27-15-50.txt.) Ezután futtassa a következő parancsfájlt, a Windows Azure Pack Példányonként egyszer:

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
1.  Gondoskodjon arról, hogy Azure verem és a Windows Azure Pack közötti hálózati kapcsolat. Ez az összekapcsolhatóság bármely bérlői számítógép, amely hozzáfér a verem Azure portál és a Windows Azure Pack bérlői portál virtuális gépek között kell lennie, ahol az új összekötő-szolgáltatások futnak.
2.  Győződjön meg arról, hogy az összes megadott FQDN helyesek-e.
3.  Győződjön meg arról, hogy az új összekötő-szolgáltatások használt SSL-tanúsítványok megbízhatók Azure verem (pontosabban a AzS-WASP01 VM), és bármely más számítógépre a bérlő az Azure-verem felhasználói portál elérésére használhat.
4. Ismert problémákkal kapcsolatban lásd: [Microsoft Azure verem hibaelhárítási](azure-stack-troubleshooting.md).


## <a name="next-steps"></a>További lépések
[A rendszergazda és a felhasználói portálon Azure verem használata](azure-stack-manage-portals.md)
