---
title: A TLS 1,2-et nem támogató alkalmazások által okozott hibák elhárítása | Microsoft Docs
description: A TLS 1,2-et nem támogató alkalmazások által okozott hibák elhárítása
services: cloud-services
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: top-support-issue
ms.assetid: ''
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/17/2020
ms.author: tagore
ms.openlocfilehash: a9d15a94421694583562f433c20413fcc84697c7
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270867"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>A TLS 1,2-et nem támogató alkalmazások hibaelhárítása
Ez a cikk bemutatja, hogyan engedélyezheti a régebbi TLS protokollokat (TLS 1,0 és 1,1), valamint az örökölt titkosítási csomagok alkalmazását a Windows Server 2019 Cloud Service webes és feldolgozói szerepköreinek további protokolljainak támogatásához. 

Tisztában vagyunk vele, hogy a TLS 1,0 és a TLS 1,1 elavult lépéseinek elvégzése közben ügyfeleinknek a régebbi protokollokat és a titkosítási csomagokat is támogatniuk kell, amíg meg nem tervezik az elavult műveleteket.  Habár nem javasoljuk a régi értékek újbóli engedélyezését, útmutatást nyújtunk az ügyfeleknek. Javasoljuk, hogy a jelen cikkben ismertetett módosítások végrehajtása előtt értékelje a regresszió kockázatát. 

> [!NOTE]
> A vendég operációsrendszer-család 6 kiadásában az 1.0/1.1 titkosítási algoritmus letiltásával kikényszeríti a TLS 1,2. 


## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>A TLS 1,0, a TLS 1,1 és a régebbi titkosítási csomagok támogatásának eldobása 
A legjobb titkosítást támogató elkötelezettségünk támogatásával a Microsoft bejelentette, hogy a TLS 1,0-es és 1,1-es verzióról az 2017-i júniustól kezdődően elindítják az áttelepítést.   Ez a kezdeti bejelentés óta a Microsoft bejelentette, hogy a Microsoft Edge és az Internet Explorer 11 támogatott verzióiban alapértelmezés szerint letiltjuk a Transport Layer Security (TLS) 1,0-es és 1,1-es verzióját a 2020 első felében.  Az Apple, a Google és a Mozilla hasonló hirdetményei jelzik az iparág irányát.   

## <a name="tls-configuration"></a>TLS-konfiguráció  
A Windows Server 2019 Cloud Server rendszerképet a beállításjegyzék szintjén le van tiltva a TLS 1,0 és a TLS 1,1. Ez azt jelenti, hogy a Windows ezen verziójára telepített alkalmazások és a Windows stack for TLS egyeztetés használata nem teszi lehetővé a TLS 1,0 és a TLS 1,1 kommunikációt.   

A kiszolgáló a titkosítási csomagok korlátozott készletét is tartalmazza: 

```
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 
```

## <a name="step-1-create-the-powershell-script-to-enable-tls-10-and-tls-11"></a>1\. lépés: a PowerShell-szkript létrehozása a TLS 1,0 és a TLS 1,1 engedélyezéséhez 

A következő kód példaként használható olyan parancsfájl létrehozásához, amely lehetővé teszi a régebbi protokollok és titkosítási csomagok használatát. A jelen dokumentációban a szkript neve: **TLSsettings. ps1**. A parancsfájlt a helyi asztalon tárolja a későbbi lépésekben való egyszerű hozzáférés érdekében. 


```Powershell
#******************* FUNCTION THAT ACTUALLY UPDATES KEYS; WILL RETURN REBOOT FLAG IF CHANGES *********************** 
 
Function Set-CryptoSetting {  
    param (  
        $regKeyName,  
        $value,  
        $valuedata,  
        $valuetype       
    )  
    
    $restart = $false 
  
    # Check for existence of registry key, and create if it does not exist  
    If (!(Test-Path -Path $regKeyName)) {  
        New-Item $regKeyName | Out-Null  
    }  
 
    # Get data of registry value, or null if it does not exist  
    $val = (Get-ItemProperty -Path $regKeyName -Name $value -ErrorAction SilentlyContinue).$value  
 
    If ($val -eq $null) {  
        # Value does not exist - create and set to desired value  
        New-ItemProperty -Path $regKeyName -Name $value -Value $valuedata -PropertyType $valuetype | Out-Null  
        $restart = $true 
    } 
 
    Else {  
        # Value does exist - if not equal to desired value, change it  
        If ($val -ne $valuedata) {  
            Set-ItemProperty -Path $regKeyName -Name $value -Value $valuedata  
            $restart = $true  
        }  
    }  
 
    $restart  
}  
 
#*************************************************************************************************************** 
 
#****************************** CIPHERSUITES FOR OS VERSIONS WINDOWS 10 AND ABOVE ****************************** 
 
function Get-BaseCipherSuitesWin10Above() 
{ 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384," 
 
# Legacy cipher suites 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_GCM_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA," 
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA" 
 
 return $cipherorder 
} 
 

#*************************************************************************************************************** 
 
 
#********************************************** REGISTRY KEYS **************************************************** 
 
 
function Get-RegKeyPathToEnable() 
{ 
    $regKeyPath = @( 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2",         
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client", 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" 
    ) 
    return $regKeyPath 
} 
 
#*************************************************************************************************************** 
 
$localRegistryPath = @() 
 
# Enable TLS 1.2, TLS 1.1 and TLS 1.0 
$localRegistryPath += Get-RegKeyPathToEnable 
 
#******************* CREATE THE REGISTRY KEYS IF THEY DON'T EXIST******************************** 
 
# Check for existence of the registry keys, and create if they do not exist  
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) {  
   Write-Host "Checking for existing of key: $($localRegistryPath[$i]) Severity Level: Information"
   If (!(Test-Path -Path $localRegistryPath[$i])) {
        New-Item $localRegistryPath [$i] | Out-Null
    Write-Host "Creating key: $($localRegistryPath[$i]) Severity Level: Information"
    }
}  
 
#********************************* EXPLICITLY Enable TLS12,  TLS11 and TLS10********************************* 
 
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) { 
    if ($localRegistryPath[$i].Contains("Client") -Or $localRegistryPath[$i].Contains("Server")) { 
      Write-Host "Enabling this key: $($localRegistryPath[$i]) Severity: Information "
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() Enabled 1 DWord   
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() DisabledByDefault 0 DWord  
        $reboot = $reboot -or $result 
    } 
} 
 
#**************************************** SET THE CIPHER SUITE ORDER******************************** 
 
$cipherlist = @() 
 
# Set cipher suite order 
$cipherlist += Get-BaseCipherSuitesWin10Above 
$CipherSuiteRegKey = "HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"  
 
if (!(Test-Path -Path $CipherSuiteRegKey))  
{  
    New-Item $CipherSuiteRegKey | Out-Null  
    $reboot = $True  
    Write-Host "Creating key: $($CipherSuiteRegKey) Severity: Information "
}  
 
#Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherorder  
Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherlist  
#********************************************* REBOOT ******************************************* 
 
Write-Host "A reboot is required in order for changes to effect"  
Write-Host "Rebooting now..."  
shutdown.exe /r /t 5 /c "Crypto settings changed" /f /d p:2:4
```

## <a name="step-2-create-a-command-file"></a>2\. lépés: hozzon létre egy parancsfájlt 

Hozzon létre egy **RunTLSSettings. cmd** nevű cmd-fájlt az alábbi paranccsal. A parancsfájlt a helyi asztalon tárolja a későbbi lépésekben való egyszerű hozzáférés érdekében. 

```cmd
PowerShell -ExecutionPolicy Unrestricted %~dp0TLSsettings.ps1
REM This line is required to ensure the startup tasks does not block the role from starting in case of error.  DO NOT REMOVE!!!! 
EXIT /B 0
```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>3\. lépés: az indítási feladat hozzáadása a szerepkör Service Definition (csdef) szolgáltatásához 

Adja hozzá a következő kódrészletet a meglévő szolgáltatási definíciós fájlhoz. 


```
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
```

Íme egy példa, amely a feldolgozói szerepkört és a webes szerepkört is megjeleníti. 

```
<?xmlversion="1.0"encoding="utf-8"?> 
<ServiceDefinitionname="CloudServiceName"xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition"schemaVersion="2015-04.2.6"> 
    <WebRolename="WebRole1"vmsize="Standard_D1_v2"> 
        <Sites> 
            <Sitename="Web"> 
                <Bindings> 
                    <Bindingname="Endpoint1"endpointName="Endpoint1"/> 
                </Bindings> 
            </Site> 
        </Sites> 
        <Startup> 
            <Taske xecutionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
            </Task> 
        </Startup> 
        <Endpoints> 
            <InputEndpointname="Endpoint1"protocol="http"port="80"/> 
        </Endpoints> 
    </WebRole> 
<WorkerRolename="WorkerRole1"vmsize="Standard_D1_v2"> 
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
</WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-5-add-the-scripts-to-your-cloud-service"></a>5\. lépés: a szkriptek hozzáadása a felhőalapú szolgáltatáshoz 

1) A Visual Studióban kattintson a jobb gombbal a webszerepkörre
2) Válassza a **Hozzáadás** lehetőséget
3) **Meglévő elem** kijelölése
4) A Fájlkezelőben navigáljon az asztalra, ahol a **TLSsettings. ps1** és a **RunTLSSettings. cmd** fájlt tárolta 
5) Válassza ki a két fájlt a Cloud Services projekthez való hozzáadáshoz

## <a name="step-6-enable-copy-to-output-directory"></a>6\. lépés: a másolás engedélyezése a kimeneti könyvtárba

Annak biztosítása érdekében, hogy a szkriptek feltöltése a Visual studióból leküldhető összes frissítéssel megtörténjen, a *Másolás a kimeneti könyvtárba* beállításnak *mindig a másolásra* kell állítania

1) A webrole alatt kattintson a jobb gombbal a RunTLSSettings. cmd fájlra.
2) **Tulajdonságok** kiválasztása
3) A Tulajdonságok lapon módosítsa a *Másolás a kimeneti könyvtárba* a *mindig* lehetőséget.
4) A **TLSsettings. ps1** lépéseinek megismétlése

## <a name="step-7-publish--validate"></a>7\. lépés: &-ellenőrzés közzététele

Most, hogy a fenti lépések befejeződik, tegye közzé a frissítést a meglévő Cloud Service-ben. 

Az [SSLLabs](https://www.ssllabs.com/) használatával ellenőrizheti a végpontok TLS-állapotát 

 
