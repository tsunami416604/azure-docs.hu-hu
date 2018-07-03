---
title: X.509-tanúsítványok létrehozása a PowerShell használatával |} A Microsoft Docs
description: Hogyan használható a PowerShell helyi X.509-tanúsítványok létrehozása és engedélyezése az X.509-alapú biztonsági az Azure IoT hub szimulált környezetben.
author: dsk-2015
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: dkshir
ms.openlocfilehash: d0063ff79a0bda88fffb486f03286f6784ece7fa
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "34637599"
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>A hitelesítésszolgáltató által aláírt X.509 tanúsítványok kezelése a PowerShell-parancsprogramok

Az IoT hub az X.509-alapú biztonsági a kezdéshez szükséges egy [X.509-tanúsítványláncot](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), amely tartalmazza a legfelső szintű tanúsítvány, valamint a másnapi levéltanúsítványt köztes tanúsítványokra. Ez *hogyan* az útmutató lépésről lépésre PowerShell-példaszkriptekre használó [OpenSSL](https://www.openssl.org/) létrehozására és aláírására X.509-tanúsítványokat. Azt javasoljuk, hogy ez az útmutató az Kísérletezési csak, mivel e lépések során a gyártási folyamat a való világból történik. Ezek a tanúsítványok segítségével biztonsági szimulálása a az Azure IoT hub használatával a *X.509 tanúsítvány alapú hitelesítést*. A jelen útmutató lépéseit tanúsítványok helyileg létrehozása a Windows-gépen. 

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy az OpenSSL bináris vásárolt. A következő lehetőségekkel vagy
    - Töltse le a OpenSSL forráskód és a bináris fájlok felépítéséhez a gépen, vagy 
    - Töltse le és telepítse a [külső OpenSSL bináris](https://wiki.openssl.org/index.php/Binaries), például a [a projektet a SourceForge](https://sourceforge.net/projects/openssl/).

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>X.509-tanúsítványok létrehozása
A következő lépések bemutatják, hogyan hozhat létre az X.509 legfelső szintű tanúsítványok helyileg egy példát. 

1. Nyisson meg egy PowerShell-ablakot, egy *rendszergazda*.  
   **Megjegyzés:** kell megnyitnia a PowerShell magát, nem PowerShell ISE-ben, a Visual Studio Code vagy egyéb eszközöket, amelyek az alapul szolgáló PowerShell-konzolt burkolása.  Nem-konzol használata alapján PowerShell eredményez `openssl` függő alábbi parancsokat.

2. Lépjen a munkakönyvtárba. Futtassa a következő szkriptet a globális változók beállítása. 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertCommonName      = "Azure IoT Root CA"
    $_rootCertSubject         = "CN=$_rootCertCommonName"
    $_intermediateCertSubject = "Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. Futtassa a következő parancsfájlt, amely átmásolja az OpenSSL bináris fájljainak a munkakönyvtár és a környezeti változókat:

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. Ezután futtassa az alábbi parancsfájlt, amely szerint a megadott e tanúsítványt keres *tulajdonosnévvel* már telepítve van, és hogy OpenSSL megfelelően van konfigurálva a gépen:
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    Ha minden megfelelően konfigurálva, az "Sikeres" üzenet. 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>X.509-tanúsítványláncot létrehozása
Tanúsítványláncolat kialakításához legfelső szintű hitelesítésszolgáltató, például "CN = az Azure IoT legfelső szintű hitelesítésszolgáltató", hogy ezt a mintát használja, a következő PowerShell-parancsfájl futtatásával. Ez a szkript is frissíti a Windows operációs rendszer tanúsítványtárolójában, hoz létre, valamint a munkakönyvtárban fájlokat. 
    1. A következő szkriptet hoz létre egy PowerShell függvény létrehozása önaláírt tanúsítvány, egy adott *tulajdonos neve* és a szolgáltató aláírást. 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$commonName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$commonName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. A következő PowerShell-függvény használatával a fenti funkciót, valamint az OpenSSL bináris köztes X.509-tanúsítványokat hoz létre. 
    ```PowerShell
    function New-CAIntermediateCert([string]$commonName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($commonName + ".cer")
        $newCert = New-CASelfsignedCertificate $commonName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. A következő PowerShell-függvényt az X.509-tanúsítványláncot hoz létre. Olvasási [Certificate chains](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) további információt.
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    Ez a szkript létrehoz egy fájlt *RootCA.cer* a munkakönyvtárban. 
    4. Végül a fenti PowerShell függvények létrehozása az X.509-tanúsítványláncot, a parancs futtatásával `New-CACertChain` a PowerShell-ablakban. 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>Az x.509-es Hitelesítésszolgáltatói tanúsítvány birtokában igazolása

Ez a szkript hajtja végre a *a koncepció-ekre* folyamata az X.509-tanúsítvány. 

A PowerShell-ablakot az asztalon futtassa a következő kódot:
   
   ```PowerShell
   function New-CAVerificationCert([string]$requestedSubjectName)
   {
       $verifyRequestedFileName = ".\verifyCert4.cer"
       $rootCACert = Get-CACertBySubjectName $_rootCertSubject
       Write-Host "Using Signing Cert:::" 
       Write-Host $rootCACert
   
       $verifyCert = New-CASelfsignedCertificate $requestedSubjectName $rootCACert $false

       Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
       if (-not (Test-Path $verifyRequestedFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
       }
   
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $requestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
   }
   New-CAVerificationCert "<your verification code>"
   ```

Ez a kód létrehoz egy tanúsítványt a megadott tulajdonosnévvel nevű, nevű fájlként a hitelesítésszolgáltató által aláírt *VerifyCert4.cer* a munkakönyvtárban. A tanúsítványfájl segítségével ellenőrizze az IoT hubbal, hogy a CA aláírási engedélye (azaz a titkos kulcsot).


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>Az eszköz levél X.509-tanúsítvány létrehozása

Ez a szakasz bemutatja, egy PowerShell-parancsprogram, amely létrehoz egy levéltanúsítványának eszköz és a megfelelő tanúsítványlánc is használhatja. 

A PowerShell-ablakban a helyi gépén futtassa az eszközt egy hitelesítésszolgáltató által aláírt X.509-tanúsítvány létrehozásához a következő parancsfájlt:

   ```PowerShell
   function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
   {
       $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
       $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
       $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
       $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
   
       $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

       $newDeviceCertPfx = New-CASelfSignedCertificate $deviceName $signingCert $false
   
       $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

       # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
       Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
       if (-not (Test-Path $newDevicePfxFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
       }

       # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
       Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
       openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

       # Convert the PEM to get formats we can process
       if ($useEcc -eq $true)
       {
           openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       else
       {
           openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
   }
   ```

Ezután futtassa `New-CADevice "<yourTestDevice>"` a PowerShell-ablakban használatával hozott létre az eszköz valódi név. Amikor a hitelesítésszolgáltató titkos kulcs jelszavának megadását kéri, adja meg "123". Ez létrehoz egy  _<yourTestDevice>.pfx_ fájlt a munkakönyvtárban.

## <a name="clean-up-certificates"></a>Tanúsítványok törlése

A kezdő sávon vagy **beállítások** alkalmazás keresse meg és válassza ki **számítógép-tanúsítványok kezelése**. Által kiállított tanúsítványok eltávolítása ** Azure IoT hitelesítésszolgáltató TestOnly x. Ezek a tanúsítványok léteznie kell a következő három helyen: 

* Tanúsítványok – helyi számítógép > személyes > tanúsítványok
* Tanúsítványok – helyi számítógép > megbízható legfelső szintű hitelesítésszolgáltatók > tanúsítványok
* Tanúsítványok – helyi számítógép > köztes hitelesítésszolgáltatók > tanúsítványok

   ![Az Azure IoT TestOnly Hitelesítésszolgáltatói tanúsítványok eltávolítása](./media/iot-hub-security-x509-create-certificates/cleanup.png)
