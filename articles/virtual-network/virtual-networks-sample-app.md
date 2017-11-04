---
title: "Az Azure mintaalkalmazás DMZ-k való használatra |} Microsoft Docs"
description: "Telepíti az egyszerű webes alkalmazást DMZ létrehozása után forgalom folyamata forgatókönyvek tesztelése"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 60340ab7-b82b-40e0-bd87-83e41fe4519c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 8506238e41c5d9dac8d76d729d4919b30a0528b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sample-application-for-use-with-dmzs"></a>Mintaalkalmazás DMZ-k való használatra
[A biztonsági határ bevált gyakorlatok laphoz való visszatéréshez][HOME]

A PowerShell-parancsfájlok helyi futtathatja a kiszolgálókon IIS01 és AppVM01 telepítsen és konfiguráljon egy egyszerű webalkalmazást fog megjelenni a tartalmat a háttér-kiszolgálófiók AppVM01 az előtér-kiszolgálóról IIS01 html-lapot.

Ez az alkalmazás egy egyszerű tesztelési környezetben biztosít számos DMZ példája és milyen módosításokat a végpontok, a NSG-k, a UDR és a tűzfalon a szabályok hatással lehet a forgalom.

## <a name="firewall-rule-to-allow-icmp"></a>Tűzfalszabály ICMP engedélyezése
Ez egyszerű PowerShell utasítás bármely Windows virtuális gépen, amely engedélyezi az ICMP (pingelések) forgalmat is futtatható. A tűzfal frissítés lehetővé teszi, hogy könnyebben tesztelés és hibaelhárítás céljából, azzal, hogy a ping protokoll lehetővé az áthaladás, a windows tűzfal (a legtöbb Linux disztribúciókkal ICMP alapértelmezés szerint be van).

```PowerShell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

A következő parancsfájlokat használ, a tűzfal szabály hozzáadása esetén az első utasításban.

## <a name="iis01---web-application-installation-script"></a>IIS01 - webes alkalmazás telepítési parancsfájlt
A rendszer ezt a parancsfájlt:

1. Nyissa meg a IMCPv4 (Ping) a helyi kiszolgáló windows tűzfalon könnyebb teszteléshez
2. Telepítse az IIS és a .net keretrendszer legalább 4.5
3. Hozzon létre egy ASP.NET-weblap és a Web.config fájlban
4. A fájlhozzáférés könnyebben alapértelmezett alkalmazáskészlet módosítása
5. A névtelen felhasználó beállítása a rendszergazdai fiókot és jelszót

A PowerShell parancsfájl fusson helyileg RDP IIS01 be kellett közben.

```PowerShell
# IIS Server Post Build Config Script
# Get Admin Account and Password
    Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
    $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
    $thePassword = Read-Host -Prompt "The Admin Password"

# Turn On ICMPv4
    Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Install IIS
    Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
    add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console

# Create Web App Pages
    Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
    $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
    <%@ Import Namespace="System.IO" %>
    <script language="vb" runat="server">
        Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
            Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
            Dim objStreamReader As StreamReader
            objStreamReader = File.OpenText(FILENAME)
            Dim contents As String = objStreamReader.ReadToEnd()
            lblOutput.Text = contents
            objStreamReader.Close()
            lblTime.Text = Now()
        End Sub
    </script>

    <!DOCTYPE html>
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
        <title>DMZ Example App</title>
    </head>
    <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
      <form id="frmMain" runat="server">
        <div>
          <h1>Looks like you made it!</h1>
          This is a page from the inside (a web server on a private network),<br />
          and it is making its way to the outside! (If you are viewing this from the internet)<br />
          <br />
          The following sections show:
          <ul style="margin-top: 0px;">
            <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
            <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
            <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
          </ul>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Image File Linked from the Internet</b>:<br />
            <br />
            <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
        </div>
      </form>
    </body>
    </html>'

    $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.web>
        <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
        <httpRuntime targetFramework="4.5" />
        <identity impersonate="true" />
        <customErrors mode="Off"/>
      </system.web>
      <system.webServer>
        <defaultDocument>
          <files>
            <add value="Home.aspx" />
          </files>
        </defaultDocument>
      </system.webServer>
    </configuration>'

    $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
    $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii

# Set App Pool to Clasic Pipeline to remote file access will work easier
    Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successfull!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>AppVM01 - kiszolgáló telepítési parancsfájlját
Ezt a parancsfájlt hoz létre a háttérkiszolgálók egyszerű alkalmazás. A rendszer ezt a parancsfájlt:

1. Nyissa meg a IMCPv4 könnyebb tesztelési a tűzfalon (Ping)
2. Hozzon létre egy könyvtárat a webhelyhez
3. Hozzon létre egy szövegfájlt, hogy távolról hozzáférést a weblap által
4. A könyvtár- és engedélyezi a hozzáférést a névtelen hozzáférés engedélyeinek beállítása
5. Kapcsolja ki az Internet Explorer fokozott biztonsági engedélyezi a könnyebb böngészés erről a kiszolgálóról 

> [!IMPORTANT]
> **Az ajánlott eljárás**: Soha ne kapcsolja ki az Internet Explorer fokozott biztonsági egy üzemi kiszolgálón, valamint célszerű általában rossz webböngészéshez egy éles kiszolgálóról. A névtelen hozzáféréshez fájlmegosztások megnyitása is hibás ötlet, de kész itt az egyszerűség érdekében.
> 
> 

A PowerShell parancsfájl fusson helyileg RDP AppVM01 be kellett közben. PowerShell annak biztosítása érdekében a sikeres végrehajtáshoz rendszergazdaként kell futtatni.

```PowerShell
# AppVM01 Server Post Build Config Script
# PowerShell must be run as Administrator for Net Share commands to work

# Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Create Directory
    New-Item "C:\WebShare" -ItemType Directory

# Write out Rand.txt
    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii

# Set Permissions on share
    $Acl = Get-Acl "C:\WebShare"
    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
    $Acl.SetAccessRule($AccessRule)
    Set-Acl "C:\WebShare" $Acl

# Create network share
    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"

# Turn Off IE Enhanced Security Configuration for Admins
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0

    Write-Host
    Write-Host "File Server Set up Successfull!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01 - DNS-kiszolgáló telepítési parancsfájlt
Nem szerepel a DNS-kiszolgáló beállítása a mintaalkalmazás parancsprogram van. Ha a tűzfalszabályok, NSG-t vagy UDR vizsgálata kell lennie, a DNS-forgalom, kell a DNS01 kiszolgálót manuálisan kell beállítani. A hálózati konfigurációs XML-fájl és a Resource Manager-sablon mindkét példákat tartalmaz DNS01 az elsődleges DNS-kiszolgáló és a nyilvános DNS-kiszolgálóként, 3. szintet, a biztonsági mentési DNS-kiszolgáló által üzemeltetett. A szint 3 DNS-kiszolgáló volna a tényleges nem helyi adatforgalomhoz használt DNS-kiszolgálóra, és DNS01 nem állítania, nem helyi hálózati DNS lépne.

## <a name="next-steps"></a>Következő lépések
* A IIS01 parancsfájl futtatása az IIS-kiszolgálón
* AppVM01 fájlkiszolgáló parancsprogrammal
* Keresse meg a nyilvános IP-cím a IIS01 a build ellenőrzése

<!--Link References-->
[HOME]: ../best-practices-network-security.md
