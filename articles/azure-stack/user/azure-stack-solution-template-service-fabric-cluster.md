---
title: Azure-készletben biztonságos Service Fabric-fürt központi telepítése |} Microsoft Docs
description: Azure-készletben biztonságos Service Fabric-fürt telepítése
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: mattbriggs
ms.reviewer: shnatara
ms.openlocfilehash: a88d8dd2af94ac796a3b2e3c667fd40a308f02a1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>A verem Azure Service Fabric-fürt telepítése

Használja a **Service Fabric-fürt** Azure verem biztonságos Service Fabric-fürt központi telepítése az Azure piactérről elemre. 

A Service Fabric használatával kapcsolatos további információkért lásd: [áttekintés az Azure szolgáltatás Frabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) és [Service Fabric-fürt biztonsági forgatókönyvek](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), az Azure-dokumentációban.

## <a name="prerequisites"></a>Előfeltételek

A következő központi telepítéséhez szükségesek a Service Fabric-fürt:
1. **Fürt tanúsítvány**  
   Ez az a X.509 tanúsítvány ad hozzá KeyVault Service Fabric telepítésekor. 
   - A **CN** ezzel a tanúsítvánnyal egyeznie kell a teljes tartomány neve (FQDN), a Service Fabric-fürtöt hoz létre. 
   - A tanúsítvány formátuma kell lennie PFX, mint a nyilvános és titkos kulcsok szükségesek. 
   Lásd: [követelmények](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) a kiszolgálóoldali tanúsítvány létrehozásához.

    > [!NOTE]  
    > Egy önaláírt tanúsítványt inplace a x.509 tanúsítvány tesztelési célokra használható. Önaláírt tanúsítványok nem kell egyeznie a fürt teljes Tartománynevét.

2.  **Rendszergazdai ügyféltanúsítvány** Ez az a tanúsítvány, az ügyfél fogja használni, hogy a Service Fabric-fürt, amely lehet önaláírt hitelesítést. Lásd: [követelmények](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) az ügyfél-tanúsítvány létrehozásához.

3.  **Az Azure-verem piactéren elérhetőnek kell lennie a következő elemek:**
     - **Windows Server 2016** – a sablon a Windows Server 2016 lemezképet használja a fürt létrehozásához.  
     - **Ügyfél parancsprogramok futtatására szolgáló bővítmény** -virtuálisgép-bővítmény a Microsoft.  
     - **PowerShell célállapot-konfigurációs szakasz** -virtuálisgép-bővítmény a Microsoft.


## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz
A Service Fabric-fürt üzembe helyezéséhez meg kell adnia a megfelelő KeyVault *titkos kulcs azonosítója* vagy a Service Fabric-fürt URL-cím. Az Azure Resource Manager-sablon a KeyVault bemenetként vesz igénybe, és majd beolvassa a fürt tanúsítványt a Service Fabric-fürt telepítése során. 

> [!IMPORTANT]  
> Adja hozzá a titkos kulcs a KeyVault a használja a Service Fabric PowerShell kell használnia. Ne használja a portálon.  

A következő parancsfájl használata a KeyVault létrehozása és hozzáadása a *fürt tanúsítvány* rá. (Lásd a [Előfeltételek](#prerequisites).) A parancsfájl futtatása előtt tekintse át a minta parancsfájlt, és frissítse a jelzett paramétereket ad meg a környezetben. Ezt a parancsfájlt is fog kimeneti meg kell adni az Azure Resource Manager sablonhoz értékeket. 

> [!TIP]  
> A parancsfájl sikeres lehet, mielőtt olyan nyilvános ajánlat, amely tartalmazza a számítási, hálózati, tárolási és KeyVault szolgáltatásokat lehet. 

  ```PowerShell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


További információkért lásd: [KeyVault kezelése a PowerShell-lel Azure veremben](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-kv-manage-powershell).

## <a name="deploy-the-marketplace-item"></a>Központi telepítése a Piactéri elemet

1. A felhasználói portálon Ugrás **új** > **számítási** > **Service Fabric-fürt**. 

   ![Válassza ki a Service Fabric-fürt](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. Az összes lapon például a *alapjai*, töltse ki a központi telepítés űrlapot. Használhatja az alapértelmezett értékeket, ha nem biztos a értékét. Válassza ki **OK** ahhoz, hogy a következő oldalra:

   ![Alapvető beállítások](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

3. Az a *hálózati beállítások* lapon adhat meg adott portok megnyitásához az alkalmazások számára:

   ![Hálózati beállítások](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. Az a *biztonsági* lapján adja meg a portáltól kapott értéket [létrehozása az Azure KeyVault](#add-a-secret-to-key-vault) , majd ismét feltölteni a titkos kulcsot.

   Az a *felügyeleti ügyfél tanúsítványának ujjlenyomata*, adja meg a ujjlenyomata a *rendszergazdai ügyféltanúsítvány*. (Lásd a [Előfeltételek](#prerequisites).)
   
   - Forrás Kulcstárolónak: Adja meg a teljes *keyVault azonosító* karakterláncot a parancsprogram eredmények. 
   - Fürt tanúsítvány URL-címe: A teljes URL-CÍMÉT adja meg a *titkos kulcs azonosítója* a parancsfájl eredmények. 
   - A fürt tanúsítvány ujjlenyomata: Adja meg a *fürt tanúsítvány ujjlenyomata* a parancsfájl eredmények.
   - Rendszergazdai ügyfél tanúsítvány-ujjlenyomatok: Adja meg a *felügyeleti ügyfél tanúsítványának ujjlenyomata* hozott létre az előfeltételeket. 

   ![Parancsfájl kimeneti](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Biztonság](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. Fejezze be a varázslót, és válassza **létrehozása** a Service Fabric-fürt központi telepítése.



## <a name="access-the-service-fabric-cluster"></a>Hozzáférés a Service Fabric-fürt
A Service Fabric-fürt a Service Fabric Explorer vagy a Service Fabric PowerShell használatával végezheti el.


### <a name="use-service-fabric-explorer"></a>Használja a Service Fabric Explorerrel
1.  Ellenőrizze, hogy az Admin ügyféltanúsítvány hozzáféréssel rendelkezik a böngészőt, és érheti el a Service Fabric-fürt.  

    a. Nyissa meg az Internet Explorert, és navigáljon **Internetbeállítások** > **tartalom** > **tanúsítványok**.
  
    b. A tanúsítványokat, jelölje be **importálási** elindítani a *Tanúsítványimportáló varázsló*, és kattintson a **következő**. A a *importálandó fájl* kattintson **Tallózás**, és válassza ki a **rendszergazdai ügyféltanúsítvány** az Azure Resource Manager sablonhoz megadott.
        
       > [!NOTE]  
       > Ez a tanúsítvány nincs a fürt tanúsítvány, amely korábban a KeyVault lett adva.  

    c. Győződjön meg arról, hogy rendelkezik-e "Személyes információcsere" a Fájlkezelőben ablak a bővítmény legördülő listában kiválasztott.  

       ![Személyes információcsere](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. Az a *tanúsítványtároló* lapon jelölje be **személyes**, majd fejezze be a varázslót.  
       ![Tanúsítványtároló](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
2. A Service Fabric-fürt teljes Tartományneve megkeresése:  

    a. Nyissa meg az erőforráscsoporthoz társított a Service Fabric fürt, majd keresse meg a *nyilvános IP-cím* erőforrás. Jelölje ki az objektumot, nyissa meg a nyilvános IP-címet társított a *nyilvános IP-cím* panelen.  

      ![Nyilvános IP-cím](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. A nyilvános IP-cím cím panelen a teljes tartománynév részére *DNS-név*.  

      ![DNS-név](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

3. Az URL-cím keresése a Service Fabric Explorerben talál, és az ügyfél-csatlakozási végpont, tekintse át a sablon telepítésének eredményeit.

4. A böngészőben nyissa meg a https://*FQDN*: 19080. Cserélje le *FQDN* a Service Fabric-fürt a 2. lépésben a teljes tartománynévvel.   
   Ha már használta egy önaláírt tanúsítványt, lesz megjelenik egy figyelmeztetés, hogy a kapcsolat nincs biztonságos. A webhelyhez a folytatáshoz válasszon **további információ**, majd **nyissa meg a képernyőn látható weblapon**. 

5. A hely felé történő hitelesítésre ki kell választania a használandó tanúsítványt. Válassza ki **több lehetőséget**, válassza ki a megfelelő tanúsítványt, és kattintson a **OK** csatlakozni a Service Fabric Explorerben talál. 

   ![Hitelesítés](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Service Fabric PowerShell használata

1. Telepítse a *Microsoft Azure Service Fabric SDK* a [Windows a fejlesztőkörnyezet előkészítése](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) az Azure Service Fabric-dokumentációban.  

2. A telepítés befejezése után állítsa be a rendszer környezeti változókat, győződjön meg arról, hogy a Service Fabric-parancsmagok elérhetők-e a Powershellből.  
    
    a. Ugrás a **Vezérlőpult** > **rendszer és biztonság** > **rendszer**, majd válassza ki **Speciális rendszerbeállítások**.  
    
      ![Vezérlőpult](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. Az a **speciális** lapján *Rendszertulajdonságok*, jelölje be **környezeti változók**.  

    c. A *rendszerváltozók*, szerkesztése **elérési** , és győződjön meg arról, hogy **C:\\Program Files\\Microsoft Service Fabric\\bin\\háló \\Fabric.Code** környezeti változók listájának elején.  

      ![Környezeti változók listájának](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

3. Miután megváltoztatta a környezeti változók sorrendjét, indítsa újra a PowerShell, és futtassa a következő PowerShell-parancsfájl a Service Fabric-fürt eléréséhez:

   ````PowerShell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ````
   
   > [!NOTE]  
   > Nincs nincs *https://* előtt a parancsfájlt a fürt nevét. Port 19000 megadása kötelező.
 
