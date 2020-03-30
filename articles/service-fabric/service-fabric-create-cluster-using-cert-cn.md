---
title: Fürt létrehozása tanúsítvány köznapi nevével
description: Megtudhatja, hogyan hozhat létre egy Service Fabric-fürtet egy sablonból származó tanúsítvány köznapi nevével.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 4a4448c88fa9493979f075f6b9c669927dd1d39e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614553"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Olyan Service Fabric-fürt telepítése, amely ujjlenyomat helyett tanúsítványnév köznapi nevet használ
Két tanúsítvány nem rendelkezhet ugyanazzal az ujjlenyomattal, ami megnehezíti a fürttanúsítvány-váltást vagy -kezelést. Több tanúsítványnak azonban lehet ugyanaz a közös neve vagy tárgya.  A tanúsítványnéveket használó fürt sokkal egyszerűbbé teszi a tanúsítványkezelést. Ez a cikk azt ismerteti, hogyan telepítheti a Service Fabric-fürt a tanúsítvány köznapi nevét a tanúsítvány ujjlenyomata helyett.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Tanúsítvány beszereznie
Először szerezzen be egy tanúsítványt egy [hitelesítésszolgáltatótól](https://wikipedia.org/wiki/Certificate_authority).  A tanúsítvány köznapi neve a saját egyéni tartományára tartozik, és tartományregisztrálótól kell vásárolnia. Például "azureservicefabricbestpractices.com"; azok, akik nem a Microsoft alkalmazottai nem tudnak tanúsítványokat létesíteni az MS-tartományokhoz, így nem használhatja az LB vagy a Traffic Manager DNS-neveit a tanúsítvány közös neveként, és ki kell építenie egy [Azure DNS-zónát,](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) ha az egyéni tartomány feloldható az Azure-ban. Ha azt szeretné, hogy a portál a fürt egyéni tartományaliasát tükrözze, akkor a saját egyéni tartományát is deklarálni szeretné a fürt "managementEndpoint"-jaként.

Tesztelési célokra egy hitelesítésszolgáltató által aláírt tanúsítványt kaphat egy ingyenes vagy nyílt hitelesítésszolgáltatótól.

> [!NOTE]
> Önaláírt tanúsítványok, beleértve a létrehozott, amikor egy Service Fabric-fürt az Azure Portalon, nem támogatottak. 

## <a name="upload-the-certificate-to-a-key-vault"></a>A tanúsítvány feltöltése egy kulcstartóba
Az Azure-ban egy Service Fabric-fürt egy virtuálisgép-méretezési csoporton van telepítve.  Töltse fel a tanúsítványt egy kulcstartóba.  Amikor a fürt települ, a tanúsítvány a fürt által futtatott virtuálisgép-méretezési csoportra települ.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Mintasablon letöltése és frissítése
Ez a cikk az [5 csomópontos biztonságos fürt példasablonját](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) és sablonparamétereit használja. Töltse le az *azuredeploy.json* és *azuredeploy.parameters.json* fájlokat a számítógépre.

### <a name="update-parameters-file"></a>Paraméterek fájljának frissítése
Először nyissa meg az *azuredeploy.parameters.json* fájlt egy szövegszerkesztőben, és adja hozzá a következő paraméterértéket:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Ezután állítsa be a *certificateCommonName*, *sourceVaultValue*és *certificateUrlValue* paraméterértékeket az előző parancsfájl által visszaadott értékekre:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>A sablonfájl frissítése
Ezután nyissa meg az *azuredeploy.json* fájlt egy szövegszerkesztőben, és készítsen három frissítést a tanúsítvány köznapi nevének támogatásához.

1. A **paraméterek** szakaszban adjon hozzá egy *certificateCommonName* paramétert:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    Is úgy eltávolítja a *tanúsítványtThumbprint*, lehet, hogy már nem szükséges.

2. Állítsa az *sfrpApiVersion* változó értékét "2018-02-01" értékre:
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. A **Microsoft.Compute/virtualMachineScaleSets** erőforrásban frissítse a virtuálisgép-bővítményt úgy, hogy a tanúsítványbeállításokban a közös nevet használja az ujjlenyomat helyett.  A **virtualMachineProfile**->**extensionS**->**kiterjesztések**->**properties**->**tulajdonságtanúsítványához**->**certificate**adja hozzá a 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    és `"thumbprint": "[parameters('certificateThumbprint')]",`távolítsa el a .

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4. A **Microsoft.ServiceFabric/clusters** erőforrásban frissítse az API-verziót "2018-02-01"-re.  Adjon hozzá egy **commonNames** tulajdonsággal rendelkező **certificateCommonNames** beállítást is, és távolítsa el a **tanúsítványbeállítást** (az ujjlenyomat tulajdonsággal), ahogy az a következő példában szerepel:
   ```json
   {
       "apiVersion": "2018-02-01",
       "type": "Microsoft.ServiceFabric/clusters",
       "name": "[parameters('clusterName')]",
       "location": "[parameters('clusterLocation')]",
       "dependsOn": [
       "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
       ],
       "properties": {
       "addonFeatures": [
           "DnsService",
           "RepairManager"
       ],        
       "certificateCommonNames": {
           "commonNames": [
           {
               "certificateCommonName": "[parameters('certificateCommonName')]",
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > A "certificateIssuerThumbprint" mező lehetővé teszi az adott tulajdonos közös névvel rendelkező tanúsítványok várható kibocsátóinak megadását. Ez a mező elfogadja az SHA1 ujjlenyomatok vesszővel tagolt felsorolását. Ne feledje, hogy ez a tanúsítvány-ellenőrzés megerősítése - abban az esetben, ha a kibocsátó nincs megadva vagy üres, a tanúsítvány tanusítandó, ha a lánc felépíthető, és a hitelesítő által megbízhatónak tartott gyökérben végződik. Ha a kibocsátó meg van adva, a tanúsítvány akkor fogadható el, ha a közvetlen kibocsátó ujjlenyomata megfelel az ebben a mezőben megadott értékek bármelyikének - függetlenül attól, hogy a gyökér megbízható-e vagy sem. Kérjük, vegye figyelembe, hogy a pki különböző hitelesítésszolgáltatókat használhat ugyanazon tulajdonos tanúsítványainak kiállítására, ezért fontos, hogy egy adott tárgyhoz minden várt kibocsátóujjlenyomatot megadjon.
   >
   > A kibocsátó megadása ajánlott eljárásnak minősül; bár kihagyja, hogy továbbra is működni fog - a tanúsítványok láncolva akár egy megbízható gyökér - ez a viselkedés korlátai vannak, és lehet, hogy fokozatosan megszűnik a közeljövőben. Azt is vegye figyelembe, hogy az Azure-ban telepített és a magánpki által kiadott és tulajdonos által deklarált X509-es tanúsítványokkal védett fürtöket nem tudja érvényesíteni az Azure Service Fabric szolgáltatás (fürt-szolgáltatás kommunikáció esetén), ha a PKI tanúsítványszabályzata nem érvényesíthető. nem fedezhető fel, nem érhető el és nem érhető el. 

## <a name="deploy-the-updated-template"></a>A frissített sablon telepítése
Telepítse újra a frissített sablont a módosítások végrehajtása után.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>További lépések
* További információ a [fürtbiztonságról](service-fabric-cluster-security.md).
* A [fürttanúsítványok görgetése](service-fabric-cluster-rollover-cert-cn.md)
* [Fürttanúsítványok frissítése és kezelése](service-fabric-cluster-security-update-certs-azure.md)
* Tanúsítványkezelés egyszerűsítése a [fürt tanúsítványujjlenyomatról közönséges névre történő módosításával](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
