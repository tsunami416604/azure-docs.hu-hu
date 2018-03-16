---
title: "HPC Pack 2016 fürt az Azure-ban |} Microsoft Docs"
description: "Megtudhatja, hogyan HPC Pack 2016-fürt üzembe helyezése az Azure-ban"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 03/09/2018
ms.author: danlep
ms.openlocfilehash: c26dd85d896445e19efb9906d953fd535fc1fb5c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>HPC Pack 2016-fürt üzembe helyezése az Azure-ban

Ez a cikk telepítéséhez kövesse a [Microsoft HPC Pack 2016 Update 1](https://technet.microsoft.com/library/cc514029) fürt az Azure virtuális gépeken. HPC Pack Microsoft a Microsoft Azure és a Windows Server technológiáira szabad HPC megoldása és a HPC széles tartomány munkaterhelések támogatja.

Valamelyikével a [Azure Resource Manager-sablonok](https://github.com/MsHpcPack/HPCPack2016) a HPC Pack 2016 fürt telepítéséhez. Fürt topológia több lehetősége van az eltérő számú és típusú fürt átjárócsomópontokat, és a számítási csomópontok.

## <a name="prerequisites"></a>Előfeltételek

### <a name="pfx-certificate"></a>PFX-tanúsítvány

A Microsoft HPC Pack 2016 fürt csak a személyes információcseréhez kapcsolódó (PFX) tanúsítványának a HPC-csomópontok közötti kommunikáció védelméhez. A tanúsítvány a következő követelményeknek kell megfelelniük:

* Alkalmas a kulcscserére titkos kulccsal kell rendelkeznie
* Kulcshasználat a digitális aláírás és kulcstitkosítás tartalmaz
* Kibővített kulcshasználat ügyfél-hitelesítéshez és a kiszolgáló hitelesítése tartalmazza

Ha még nem rendelkezik olyan tanúsítvány, amely megfelel ezeknek a követelményeknek, a tanúsítványt kérhet egy hitelesítésszolgáltatótól. Alternatív megoldásként az alábbi parancsokkal létrehozni az önaláírt tanúsítvány alapján az operációs rendszer, amelyen a parancsot futtatja. Ezután exportálja a tanúsítványt jelszóval védett PFX-fájlt, és a titkos kulcs.

* **A Windows 10 vagy Windows Server 2016**, futtassa a beépített **New-SelfSignedCertificate** PowerShell-parancsmagot az alábbiak szerint:

  ```PowerShell
  New-SelfSignedCertificate -Subject "CN=HPC Pack 2016 Communication" -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2") -CertStoreLocation cert:\CurrentUser\My -KeyExportPolicy Exportable -NotAfter (Get-Date).AddYears(5)
  ```
* **Windows 10 vagy Windows Server 2016-nál korábbi operációs rendszerek**, töltse le a [önaláírt tanúsítvány generátor](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) a Microsoft Script Center. Bontsa ki a tartalmát, és futtassa az alábbi parancsokat a PowerShell-parancssorba:

    ```PowerShell 
    Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
    New-SelfSignedCertificateEx -Subject "CN=HPC Pack 2016 Communication" -KeySpec Exchange -KeyUsage "DigitalSignature,KeyEncipherment" -EnhancedKeyUsage "Server Authentication","Client Authentication" -StoreLocation CurrentUser -Exportable -NotAfter (Get-Date).AddYears(5)
    ```

A tanúsítvány létrehozása után az aktuális felhasználó tárolójában, használja a tanúsítványok beépülő modul exportálja a tanúsítványt jelszóval védett PFX-fájlt, és a titkos kulcs. Emellett exportálhatja a tanúsítványt használja a [Export-Pfxcertificate](/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps) PowerShell-parancsmagot.

### <a name="upload-certificate-to-an-azure-key-vault"></a>Az egy az Azure key vault-tanúsítvány feltöltése

A HPC-fürt telepítése előtt a PFX-tanúsítvány feltöltése egy [az Azure key vault](../../key-vault/index.md) titkos kulcs és rekord a telepítés során használja a következő információkat: **tároló neve**, **tároló Erőforráscsoport**, **tanúsítvány URL-címe**, és **tanúsítvány ujjlenyomata**.

A következő egy PowerShell-parancsfájlpélda a tanúsítvány feltöltése, a kulcstároló létrehozása és a szükséges adatokat. További információ a tanúsítvány feltöltése egy az Azure key vault: [Ismerkedés az Azure Key Vault](../../key-vault/key-vault-get-started.md).

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>Támogatott topológiák

Válasszon egyet a a [Azure Resource Manager-sablonok](https://github.com/MsHpcPack/HPCPack2016) a HPC Pack 2016 fürt telepítéséhez. Az alábbiakban a magas szintű architektúra három példa fürt topológiája. Magas rendelkezésre állású topológiák több központi fürtcsomópontok közé tartozik.

1. Magas rendelkezésre állású fürt az Active Directory-tartomány

    ![Az Active Directory-tartománynak a magas rendelkezésre ÁLLÁSÚ fürt](./media/hpcpack-2016-cluster/haad.png)


2. Magas rendelkezésre állású fürt nélkül Active Directory-tartomány

    ![Magas rendelkezésre ÁLLÁSÚ fürt nélkül AD-tartomány](./media/hpcpack-2016-cluster/hanoad.png)

3. Csak egy átjárócsomóponttal-fürtöt

   ![Egy átjárócsomóponttal-fürtöt](./media/hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>Fürt központi telepítése

A fürt létrehozásához válassza ki a sablont, és kattintson **az Azure telepítéséhez**. Az a [Azure-portálon](https://portal.azure.com), adja meg a sablon paramétereinek, a következő lépésben leírtak szerint. Minden sablon létrehozza a HPC-fürtinfrastruktúra szükséges összes Azure-erőforrások. Erőforrások közé tartoznak, egy Azure virtuális hálózatra, nyilvános IP-cím, terheléselosztót (csak egy magas rendelkezésre állású fürt), hálózati adapterek, rendelkezésre állási csoportok, storage-fiókok, és virtuális gépek.

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>1. lépés: Válassza ki az előfizetést, helyét és erőforráscsoport

A **előfizetés** és a **hely** meg kell egyeznie a PFX-tanúsítvány feltöltött amikor megadott (lásd). Azt javasoljuk, hogy hozzon létre egy másik **erőforráscsoport** a központi telepítéshez.

### <a name="step-2-specify-the-parameter-settings"></a>2. lépés: A paraméter-beállításainak megadása

Adja meg, vagy módosítsa a sablon paraméter értékét. Kattintson a súgó információt minden paraméter melletti ikonra. Is láthatja, hogy az útmutató a [elérhető Virtuálisgép-méretek](sizes.md).

Adja meg az értékeket az előfeltételeket a következő paraméterek rögzített: **tároló neve**, **tároló erőforráscsoport**, **tanúsítvány URL-címe**, és **tanúsítvány ujjlenyomata**.

### <a name="step-3-review-terms-and-create"></a>3. lépés Tekintse át és létrehozása
Tekintse át a sablonhoz társított feltételeit. Ha elfogadja, kattintson a **beszerzési** a telepítés elindításához.

A fürt topológiájától függően telepítési 30 percig is tarthat, vagy hosszabb ideig.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz
1. Lépjen a HPC Pack fürt telepítése után a [Azure-portálon](https://portal.azure.com). Kattintson a **erőforráscsoportok**, és keresse meg az erőforráscsoportot, amely a fürt tették elérhetővé telepítésre. Az átjárócsomópont virtuális gépek található.

    ![Központi fürtcsomópontok a portálon](./media/hpcpack-2016-cluster/clusterhns.png)

2. Kattintson egy átjárócsomópont (magas rendelkezésre állású fürt, kattintson a head csomópontokon). A **áttekintése**, megtalálhatja a nyilvános IP-cím vagy a fürt teljes DNS-nevét.

    ![Fürt kapcsolatbeállításai](./media/hpcpack-2016-cluster/clusterconnect.png)

3. Kattintson a **Connect** jelentkezzen be a távoli asztal használata a megadott rendszergazdai felhasználónév központi csomópontokon. Ha a fürt, amelyet központilag telepített Active Directory-tartományban, a felhasználónév az űrlap van \<privateDomainName >\\\<adminUsername > (például hpc.local\hpcadmin).

## <a name="next-steps"></a>További lépések
* A fürt feladatok elküldéséhez. Lásd: [küldés feladatok HPC egy HPC Pack fürtön, az Azure-ban](hpcpack-cluster-submit-jobs.md) és [kezelése az Azure-ban az Azure Active Directory HPC Pack 2016 fürtöt](hpcpack-cluster-active-directory.md).

