---
title: A DMZ-k segítségével az Azure mintaalkalmazás
titlesuffix: Azure Virtual Network
description: Forgalom folyamat-forgatókönyvekhez teszteléséhez DMZ létrehozása után az egyszerű webes alkalmazás üzembe helyezése
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 75e24f2171dd7ad8d2a9de3765b3c9f9d246670f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212324"
---
# <a name="sample-application-for-use-with-dmzs"></a>A mintaalkalmazás a DMZ-k segítségével
[Térjen vissza a biztonsági határ ajánlott eljárások lap][HOME]

A PowerShell-parancsfájlok helyi telepítéséhez, és állítsa be egy egyszerű webalkalmazást, amely megjeleníti a tartalom a háttér-AppVM01 kiszolgálóról az előtér-kiszolgálóról IIS01 HTML-lapok IIS01 és AppVM01 kiszolgálókon futtatható.

Ez az alkalmazás egy egyszerű tesztelési környezetben kínál számos, a DMZ-példák, és hogyan módosításokat a végpontokat, a NSG-k, az udr-t és a tűzfal a szabályok hatással lehet a forgalom adatfolyamait.

## <a name="firewall-rule-to-allow-icmp"></a>Tűzfalszabályt, amely engedélyezi az ICMP Használatát
Ez egyszerű PowerShell-utasítással ICMP (pingelések) forgalom engedélyezésére bármely Windows virtuális gépen futtatható. A tűzfal frissítés lehetővé teszi, hogy a könnyebb tesztelés és hibaelhárítás azáltal, hogy a pingelés protokoll a windows tűzfal (a legtöbb Linux-disztribúciók ICMP alapértelmezés szerint be van kapcsolva) áthaladását.

```PowerShell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

Használja az alábbi parancsfájlok, a tűzfal szabály hozzáadás-e az első utasítás.

## <a name="iis01---web-application-installation-script"></a>IIS01 – webes alkalmazás telepítési parancsfájlt
Ezt a szkriptet fogja végrehajtani:

1. Nyissa meg a IMCPv4 (Ping), a helyi kiszolgáló windows tűzfalon, a könnyebb tesztelés
2. Telepítse az IIS és a .net keretrendszer legalább 4.5
3. Create an ASP.NET web page and a Web.config file
4. A fájlhozzáférés könnyebbé tenni az alapértelmezett alkalmazáskészlet módosítása
5. A rendszergazdai fiókot és jelszót a névtelen felhasználó beállítása

Ez a PowerShell-szkript helyileg kell futtatni RDP IIS01 be kellett közben.

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
            <li> Local Server Time - Shows if this page is or isn''t cached anywhere</li>
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

# Set App Pool to Classic Pipeline to remote file access will work easier
    Write-Host "Updating IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>AppVM01 - kiszolgáló telepítési parancsfájlját
Ez a szkript állítja be a háttéralkalmazás az egyszerű alkalmazáshoz. Ezt a szkriptet fogja végrehajtani:

1. Nyissa meg a IMCPv4 (Ping), a tűzfalon a könnyebb tesztelés
2. Hozzon létre egy könyvtárat a webhelyhez
3. Hozzon létre egy szövegfájlt, távolról a weblap által érhetők el
4. A könyvtár- és a névtelen hozzáférés engedélyezése az engedélyek beállítása
5. Kapcsolja ki az Internet Explorer – fokozott biztonsági, hogy könnyebben böngészés erről a kiszolgálóról 

> [!IMPORTANT]
> **Ajánlott eljárás**: Soha ne kapcsolja ki az Internet Explorer fokozott biztonsági éles kiszolgálón, valamint célszerű általában egy rossz böngészik az éles kiszolgálón. Nyissa meg a névtelen hozzáférés fájlmegosztásokat is egy rossz ötlete, de kész itt az egyszerűség kedvéért.
> 
> 

Ez a PowerShell-szkript helyileg kell futtatni RDP AppVM01 be kellett közben. PowerShell az annak biztosítása érdekében a sikeres végrehajtást rendszergazdaként kell futtatni.

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
    Write-Host "File Server Set up Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01 – DNS-kiszolgáló telepítési szkript
Ez a mintaalkalmazás a DNS-kiszolgáló beállítása nincs parancsprogramja van. Tesztelés a tűzfalszabályok, NSG-t vagy udr-t kell tartalmaznia a DNS-forgalom, ha a DNS01 kiszolgálónak kell manuálisan beállítani. A hálózati konfigurációs xml-fájl és a Resource Manager-sablon mindkét példákat tartalmaz DNS01 az elsődleges DNS-kiszolgáló és a nyilvános DNS-kiszolgáló, Level 3, a biztonsági mentési DNS-kiszolgáló által üzemeltetett. A szintjét 3 DNS-kiszolgáló lenne a tényleges DNS-kiszolgáló nem helyi forgalomhoz használt, és a DNS01 nem beállítása nincs helyi hálózat DNS fel.

## <a name="next-steps"></a>További lépések
* IIS-kiszolgálón történő IIS01 parancsprogrammal
* AppVM01 a fájlkiszolgáló-parancsfájl futtatása
* Tallózással keresse meg a nyilvános IP-cím a IIS01 a build ellenőrzése

<!--Link References-->
[HOME]: ../best-practices-network-security.md
