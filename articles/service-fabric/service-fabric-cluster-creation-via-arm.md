---
title: "Az Azure Service Fabric-fürt létrehozása sablonból |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan állíthat be egy biztonságos, az Azure Service Fabric-fürt Azure Active Directory (Azure AD), Azure Resource Manager és az Azure Key Vault használatával az ügyfél-hitelesítéshez."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: chackdan
ms.openlocfilehash: 47152d05eb7e31e7fe1f35e33a10fe8e903e21e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>A Service Fabric-fürt létrehozása az Azure Resource Manager használatával
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Ez az útmutató lépésről lépésre bemutatja, hogyan állítja be a biztonságos Azure Service Fabric-fürt az Azure-ban Azure Resource Manager használatával. A Microsoft tudomásul, hogy a cikk hosszú. Mindazonáltal kivéve, ha már alaposan tisztában a tartalommal, ügyeljen arra, hogy alaposan kövesse az alábbi lépéseket.

Az útmutató a következő eljárásokat ismerteti:

* A fürt- és biztonsági-tanúsítványok feltöltésére egy az Azure key vault beállítása
* Védett fürt létrehozása az Azure-ban Azure Resource Manager használatával
* Felhasználók hitelesítése az Azure Active Directory (Azure AD) segítségével a kiszolgálófürt-felügyelet

A biztonságos fürtre egy fürt, amely megakadályozza a jogosulatlan felügyeleti műveleteket. Ez magában foglalja, telepítése, frissítése és törlése, alkalmazások, szolgáltatások és hogy milyen adatokat tartalmaznak. Egy nem biztonságos fürt olyan fürt, hogy bárki bármikor csatlakozhat, és felügyeleti műveletek. Bár lehetséges egy nem biztonságos fürtök létrehozásához, erősen ajánlott, hogy hozzon létre egy biztonságos fürt a kezdettől. Egy nem biztonságos fürt később nem védhető, mert létre kell hozni egy új fürtöt.

A biztonságos fürtök létrehozására fogalma azonos, hogy azok a Linux vagy a Windows-fürtök. További információk és segítő parancsfájlok biztonságos Linux-fürtök létrehozásához, lásd: [biztonságos fürtök Linux rendszeren létrehozására](#secure-linux-clusters).

## <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába
Ez az útmutató használ [Azure PowerShell][azure-powershell]. Amikor egy új PowerShell-munkamenetet indít el, jelentkezzen be az Azure-fiókjával, és jelölje ki az előfizetését, Azure parancsok végrehajtása előtt.

Jelentkezzen be az Azure-fiókjával:

```powershell
Login-AzureRmAccount
```

Jelölje ki az előfizetését:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-a-key-vault"></a>Kulcstároló beállítása
Ez a szakasz ismerteti, és a Service Fabric-alkalmazások számára az Azure Service Fabric-fürt kulcstároló létrehozása. Az Azure Key Vault teljes körű, tekintse meg a [Key Vault – első lépések útmutató][key-vault-get-started].

Service Fabric fürt védelmét, és adja meg az alkalmazás szolgáltatásai a biztonsági X.509-tanúsítványokat használ. Key Vault használatával kezelheti az Azure Service Fabric-fürtök tanúsítványait. A fürt telepítésekor az Azure-ban, az Azure erőforrás-szolgáltató, amely felelős az Service Fabric-fürtök létrehozására kéri le a tanúsítványokat a Key Vault, és telepíti azokat a virtuális gépek fürtön.

A következő diagram azt ábrázolja, az Azure Key Vault, a Service Fabric-fürt és a fürt létrehozásakor a key vaultban tárolt tanúsítványokat használ az Azure erőforrás-szolgáltató közötti kapcsolat:

![Tanúsítvány telepítése ábrája][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az első lépés, ha kimondottan a key vaultban egy erőforráscsoportot. Azt javasoljuk, hogy a key vault kerüljenek-e a saját erőforráscsoportot. Ez a művelet lehetővé teszi a számítási és tárolási erőforrás csoportokhoz, beleértve az erőforráscsoport, amely tartalmazza a Service Fabric-fürt, a kulcsok és titkos kulcsok elvesztése eltávolítását. Az erőforráscsoport, amely tartalmazza a kulcstartót _ugyanabban a régióban kell lennie_ a fürt által használt.

Ha több régióba fürtök üzembe helyezésekor, javasoljuk, hogy adott nevet az erőforráscsoport és a kulcsot tároló oly módon, amely azt jelzi, mely régióhoz tartozik.  

```powershell

    New-AzureRmResourceGroup -Name westus-mykeyvault -Location 'West US'
```
A kimeneti kell kinéznie:

```powershell

    WARNING: The output object type of this cmdlet is going to be modified in a future release.

    ResourceGroupName : westus-mykeyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/westus-mykeyvault

```
<a id="new-key-vault"></a>

### <a name="create-a-key-vault-in-the-new-resource-group"></a>Hozzon létre egy kulcstartót az új erőforráscsoport
A key vault _engedélyezni kell a központi telepítés_ tanúsítványok beszerzése és telepíti azt a virtuálisgép-példányok számítási erőforrás-szolgáltató engedélyezéséhez:

```powershell

    New-AzureRmKeyVault -VaultName 'mywestusvault' -ResourceGroupName 'westus-mykeyvault' -Location 'West US' -EnabledForDeployment

```

A kimeneti kell kinéznie:

```powershell

    Vault Name                       : mywestusvault
    Resource Group Name              : westus-mykeyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault
    Vault URI                        : https://mywestusvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```
<a id="existing-key-vault"></a>

## <a name="use-an-existing-key-vault"></a>Használjon egy meglévő kulcstároló

Egy meglévő kulcstároló használatára akkor _engedélyezni kell a központi telepítési_ tanúsítványok beszerzése és telepíti a fürtcsomópontokon a számítási erőforrás-szolgáltató engedélyezéséhez:

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

```

<a id="add-certificate-to-key-vault"></a>

## <a name="add-certificates-to-your-key-vault"></a>Vegyen fel tanúsítványokat a kulcstartót

A Service Fabric tanúsítványokat használ a hitelesítéshez és titkosításhoz a fürtök és a rajtuk található alkalmazások különféle részeinek védelmére. További információ a tanúsítványok használatának módját a Service Fabric: [Service Fabric-fürt biztonsági forgatókönyvek][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Fürt és a kiszolgálói tanúsítványt (kötelező)
Ez a tanúsítvány szükséges biztosításához a fürt és a jogosulatlan hozzáférés elkerülése érdekében azt. Fürt biztonsági két módon tartalmazza:

* Fürt hitelesítési: hitelesíti a fürt összevonási csomópontok kommunikációt. Csak olyan csomópontot, amely bizonyítja, ezzel a tanúsítvánnyal az identitásukat csatlakozhat a fürthöz.
* Kiszolgálóhitelesítés: hitelesíti a felügyeleti ügyfél, a fürt felügyeleti végpontokat, így a felügyeleti ügyfél tudja azt a valódi fürthöz van szó. Ez a tanúsítvány is biztosít az SSL protokoll a HTTPS-szolgáltatásfelügyeleti API és a Service Fabric Explorer HTTPS-KAPCSOLATON keresztül.

Ezeket a célokat szolgál, hogy a tanúsítvány a következő követelményeknek kell megfelelniük:

* A tanúsítványnak tartalmaznia kell egy titkos kulccsal.
* A kulcscseréhez használt, amely exportálható személyes információcsere (.pfx) fájlba a tanúsítványt kell létrehozni.
* A tanúsítvány tulajdonosának nevét meg kell egyeznie a tartományt, amelyikhez a Service Fabric-fürt eléréséhez használt. A megfelelő szükség az SSL protokoll a fürt HTTPS felügyeleti végpontokat és a Service Fabric Explorerben talál. Az SSL-tanúsítványt egy hitelesítésszolgáltatótól (CA) származó nem szerezze be a. cloudapp.azure.com tartomány. Be kell szereznie egy egyéni tartománynevet a fürt számára. A hitelesítésszolgáltató tanúsítvány kérése, amikor a tanúsítvány tulajdonosának nevét meg kell egyeznie az egyéni tartománynevet, amelyekkel a fürt számára.

### <a name="application-certificates-optional"></a>Alkalmazás-tanúsítványok (nem kötelező)
Tetszőleges számú további tanúsítványokat is telepíthető egy fürt biztonsági okokból. Az fürt létrehozása előtt fontolja meg az alkalmazás biztonsági kell telepíteni a csomópontokat, például a tanúsítványt igénylő forgatókönyvek:

* Titkosítás és visszafejtés az alkalmazáskonfigurációs értékeket.
* Replikáció során az adatok csomópontok közötti titkosítása.

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Az Azure erőforrás-szolgáltató használt tanúsítványok formázás
Adja hozzá, és a titkos kulcs fájlok használata (.pfx) közvetlenül a kulcstartót keresztül. Azonban az Azure számítási erőforrás-szolgáltató megköveteli a kulcsok egy speciális JavaScript Object Notation (JSON) formátumban tárolja. A formátum base 64 kódolású karakterlánc és a titkos kulcs jelszava a .pfx fájl tartalmazza. Ezen követelmények teljesítése érdekében a kulcsok kell helyezni egy JSON-karakterláncban és "titkos kulcsainak" a key vault majd tárolja.

A folyamat megkönnyítése, egy [PowerShell modul az elérhető a Githubon][service-fabric-rp-helpers]. A modul használatára, tegye a következőket:

1. Töltse le a tárházban teljes tartalmát egy helyi könyvtárba.
2. Nyissa meg a helyi könyvtárba.
2. A PowerShell-ablakban a ServiceFabricRPHelpers modul importálása:

```powershell

 Import-Module "C:\..\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

```

A `Invoke-AddCertToKeyVault` parancsot a PowerShell modul automatikusan a tanúsítvány titkos kulcsa alakítja JSON karakterláncnak és feltölti azt a key vault. A parancs segítségével a fürt tanúsítványt és a további tanúsítványok hozzáadása a key vault. Ismételje meg ezt a lépést minden további tanúsítványokat, a fürtön telepíteni szeretné.

#### <a name="uploading-an-existing-certificate"></a>Létező tanúsítvány feltöltése

```powershell

 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName westus-mykeyvault -Location "West US" -VaultName mywestusvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

```

Ha a hiba, a hasonló itt, ez általában azt jelenti, hogy rendelkezik-e URL-cím erőforrás-ütközés. Az ütközés feloldásához módosítsa a kulcstároló neve.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Az ütközés fel lett oldva, miután a kimenet ehhez hasonló kell kinéznie:

```

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup westus-mykeyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing value mywestusvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to mywestusvault in vault mywestusvault


Name  : CertificateThumbprint
Value : E21DBC64B183B5BF355C34C46E03409FEEAEF58D

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault

Name  : CertificateURL
Value : https://mywestusvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

>[!NOTE]
>A három előző karakterláncok, CertificateThumbprint, SourceVault és CertificateURL, a biztonságos Service Fabric-fürt beállításához, valamint a-t használja az alkalmazás biztonsági alkalmazás tanúsítványokat van szüksége. Ha nem menti a karakterláncokat, kérheti le azokat a key vault később lekérdezésével nehézkes lehet.

<a id="add-self-signed-certificate-to-key-vault"></a>

#### <a name="creating-a-self-signed-certificate-and-uploading-it-to-the-key-vault"></a>Önaláírt tanúsítvány létrehozása, majd ismét feltölteni a key vault

Ha a tanúsítványok a key vault már feltöltött, kihagyhatja ezt a lépést. Ebben a lépésben egy új önaláírt tanúsítvány létrehozása, majd ismét feltölteni a kulcstartót szolgál. Módosítsa a következő parancsfájl paramétereit, és futtassa, miután a rendszer kéri a tanúsítvány jelszót.  

```powershell

$ResourceGroup = "chackowestuskv"
$VName = "chackokv2"
$SubID = "6c653126-e4ba-42cd-a1dd-f7bf96ae7a47"
$locationRegion = "westus"
$newCertName = "chackotestcertificate1"
$dnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$localCertPath = "C:\MyCertificates" # location where you want the .PFX to be stored

 Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResourceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath

```

Ha a hiba, a hasonló itt, ez általában azt jelenti, hogy rendelkezik-e URL-cím erőforrás-ütközés. Az ütközés feloldásához módosítsa a kulcstároló neve, RG nevét, és így tovább.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Az ütközés fel lett oldva, miután a kimenet ehhez hasonló kell kinéznie:

```
PS C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers> Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -Password $certPassword -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath
Switching context to SubscriptionId 6c343126-e4ba-52cd-a1dd-f8bf96ae7a47
Ensuring ResourceGroup chackowestuskv in westus
WARNING: The output object type of this cmdlet will be modified in a future release.
Creating new vault westuskv1 in westus
Creating new self signed certificate at C:\MyCertificates\chackonewcertificate1.pfx
Reading pfx file from C:\MyCertificates\chackonewcertificate1.pfx
Writing secret to chackonewcertificate1 in vault westuskv1


Name  : CertificateThumbprint
Value : 96BB3CC234F9D43C25D4B547sd8DE7B569F413EE

Name  : SourceVault
Value : /subscriptions/6c653126-e4ba-52cd-a1dd-f8bf96ae7a47/resourceGroups/chackowestuskv/providers/Microsoft.KeyVault/vaults/westuskv1

Name  : CertificateURL
Value : https://westuskv1.vault.azure.net:443/secrets/chackonewcertificate1/ee247291e45d405b8c8bbf81782d12bd

```

>[!NOTE]
>A három előző karakterláncok, CertificateThumbprint, SourceVault és CertificateURL, a biztonságos Service Fabric-fürt beállításához, valamint a-t használja az alkalmazás biztonsági alkalmazás tanúsítványokat van szüksége. Ha nem menti a karakterláncokat, kérheti le azokat a key vault később lekérdezésével nehézkes lehet.

 Ezen a ponton helye a következő elemeket kell rendelkezniük:

* A kulcstároló erőforráscsoportot.
* A key vault és az URL-CÍMÉT (az előző PowerShell kimenet hívott SourceVault).
* A fürt kiszolgálói hitelesítési tanúsítványt és annak a kulcstároló URL-címet.
* Az alkalmazás tanúsítványok és azok a kulcstároló URL-címeit.


<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Az ügyfél-hitelesítéshez az Azure Active Directory beállítása

Az Azure AD lehetővé teszi, hogy a szervezetek (más néven bérlői) alkalmazásokhoz való felhasználói hozzáférések kezelése. Alkalmazások oszthatók rendelkező webalapú bejelentkezési felhasználói felület és az egy natív ügyfél révén azokat. Ebben a cikkben azt feltételezzük, hogy már létrehozta a bérlő. Ha nem, olvassa el [Azure Active Directory-bérlő beszerzése][active-directory-howto-tenant].

A Service Fabric-fürt a felügyeleti funkciót, beleértve a webalapú több belépési pontot nyújt [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] és [Visual Studio][service-fabric-manage-application-in-visual-studio]. Ennek eredményeképpen hoz létre, két Azure AD-alkalmazások történő hozzáférés szabályozása érdekében a fürt, egy webes alkalmazás és egy natív alkalmazás.

Egyes szerepet játszó konfigurálása az Azure AD-ben a Service Fabric-fürt lépések leegyszerűsítése létrehoztunk Windows PowerShell-parancsfájlokkal.

> [!NOTE]
> A fürt létrehozása előtt el kell végeznie az alábbi lépéseket. A parancsfájlok a fürt nevét és a végpontok várható, mivel az értékeket kell megtervezni, és nem az, hogy már létrehozta értékeket.

1. [Töltse le a parancsfájlok] [ sf-aad-ps-script-download] a számítógépre.
2. Kattintson a jobb gombbal a zip-fájl, jelölje be **tulajdonságok**, jelölje be a **Unblock** jelölőnégyzetet, majd kattintson a **alkalmaz**.
3. Csomagolja ki a tömörített fájlt.
4. Futtatás `SetupApplications.ps1`, és adja meg a TenantId ClusterName és WebApplicationReplyUrl paraméterekként. Példa:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    A TenantId találhatja meg a következő PowerShell-parancs végrehajtása `Get-AzureSubscription`. A TenantId minden előfizetés a következő parancs végrehajtásakor jeleníti meg.

    Az Azure AD-alkalmazások, a parancsfájl által létrehozott előtag ClusterName szolgál. Nem a tényleges fürtnév pontosan egyeznie kell. Célja, hogy csak az, hogy egyszerűbb legyen az Azure AD-összetevők leképezi a Service Fabric-fürt, amely akkor van használatban.

    WebApplicationReplyUrl az alapértelmezett végpont az Azure AD a felhasználóknak ad vissza, miután a bejelentkezés befejezéséhez. Ez a végpont állítja be a fürtöt, amely alapértelmezés szerint a Service Fabric Explorer végpontja:

    https://&lt;cluster_domain&gt;: 19080/Explorer

    Jelentkezzen be az Azure AD-bérlő rendszergazdai jogosultsággal rendelkező fiók kéri. Miután bejelentkezik, a parancsfájl hoz létre, a web- és natív alkalmazások a Service Fabric-fürt képviseli. Ha megnézi a bérlős alkalmazásokhoz a [a klasszikus Azure portálon][azure-classic-portal], két új bejegyzést kell megjelennie:

   * *ClusterName*\_fürt
   * *ClusterName*\_ügyfél

   A parancsfájl a JSON-ban az Azure Resource Manager sablonhoz szükséges a fürt létrehozásakor a következő szakaszban, ezért érdemes megnyitva, a PowerShell-ablakot jelenít meg.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Service Fabric-fürt Resource Manager sablon létrehozása
Ebben a szakaszban az előző PowerShell-parancsok kimenetének Service Fabric fürt erőforrás-kezelő sablonként használhatók.

A minta Resource Manager-sablonok találhatók a [Azure gyors üzembe helyezési sablon gyűjteménye a Githubon][azure-quickstart-templates]. Ezek a sablonok kiindulási pontként használható a fürt sablon.

### <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása
Ez az útmutató használja a [biztonságos 5-csomópontot tartalmazó fürtben] [ service-fabric-secure-cluster-5-node-1-nodetype] példa sablon és a sablon paramétereit. Töltse le `azuredeploy.json` és `azuredeploy.parameters.json` a számítógépre, és nyissa meg a fájlt a kedvenc szövegszerkesztőjével.

### <a name="add-certificates"></a>Tanúsítványok hozzáadása
Adja hozzá a tanúsítványok a fürt Resource Manager-sablon a kulcstároló, amely tartalmazza a tanúsítvány kulcsait Vezérlőpultjának. Azt javasoljuk, hogy a Resource Manager sablon paraméterfájl helyezze el a kulcstároló értékek. Így megtartja az erőforrás-kezelő sablonfájl újrafelhasználható és szabad értékek adott a környezethez.

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Minden tanúsítvány felvétele a virtuális gép méretezési készlet osProfile
A skála erőforrás (Microsoft.Compute/virtualMachineScaleSets) osProfile szakaszában telepítve van-e a fürt minden tanúsítványt kell konfigurálni. Ez a művelet arra utasítja az erőforrás-szolgáltató a tanúsítvány telepítése a virtuális gépeken. Ehhez a telepítéshez a a fürt tanúsítvány és az alkalmazások használni kívánt alkalmazás biztonsági tanúsítványokat is tartalmaz:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>A Service Fabric-fürt tanúsítvány konfigurálása
A fürt hitelesítési tanúsítványt meg kell adni mind a Service Fabric fürt erőforrás (Microsoft.ServiceFabric/clusters), és a Service Fabric-bővítményt a virtuálisgép-méretezési beállítja azt a virtuálisgép-méretezési készlet erőforrás. Ezzel az elrendezéssel lehetővé teszi, hogy a fürt hitelesítési és felügyeleti végpontok kiszolgáló hitelesítésének konfigurálását a Service Fabric erőforrás-szolgáltató.

##### <a name="virtual-machine-scale-set-resource"></a>Virtuálisgép-méretezési erőforrás:
```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>A Service Fabric-erőforrás:
```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-azure-ad-configuration"></a>Helyezze be az Azure AD konfigurálása
Az Azure Active Directory beállítása, korábban létrehozott illeszthető közvetlenül a Resource Manager-sablon. Azt javasoljuk azonban, hogy meg először nyerje ki az értékeket a Resource Manager sablon egyszer használatos és szabad értékek a környezethez adott paraméterek fájlba.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### < a "konfigurálása – arm" ></a>Sablonparaméterek erőforrás-kezelő konfigurálása
<!--- Loc Comment: It seems that <a "configure-arm" > must be replaced with <a name="configure-arm"></a> since the link seems not to be redirecting correctly --->
Végezetül a kimeneti értékeit a kulcstartót és az Azure AD PowerShell-parancsok használata a paraméterfájlban feltöltéséhez:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
Ezen a ponton helye a következő elemeket kell rendelkezniük:

* Kulcstároló erőforráscsoport
  * Key Vault
  * Fürt kiszolgálói hitelesítési tanúsítvány
  * Adatok rejtjelezése tanúsítvány
* Az Azure Active Directory-bérlő
  * Az Azure AD-alkalmazást a web-alapú felügyeleti és a Service Fabric Explorerrel
  * Natív ügyfél kezelése az Azure AD-alkalmazás
  * Felhasználók és a hozzárendelt szerepkör
* A Service Fabric-fürt Resource Manager-sablon
  * Kulcstároló keresztül konfigurált tanúsítványok
  * Az Azure Active Directory konfigurálva

Az alábbi ábrán látható, ahol a kulcstartót és az Azure Active Directory beállítása felelnek meg a Resource Manager-sablon.

![Erőforrás-kezelő kapcsolatfüggőségi térképének megjelenítéséhez][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>A fürt létrehozása
Most már készen áll a fürt létrehozása használatával [Azure-erőforrás sablon-üzembehelyezés][resource-group-template-deploy].

#### <a name="test-it"></a>Tesztelheti
A következő PowerShell-parancs segítségével a Resource Manager-sablon tesztelést paraméterfájl:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Telepítés
A Resource Manager sablon teszt sikeres, ha a következő PowerShell-parancs segítségével telepítheti a Resource Manager-sablon paraméterfájl:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Felhasználók hozzárendelése szerepkörökhöz
Miután létrehozta a fürt képviselő alkalmazások, a felhasználók a szerepkörökhöz rendeljen hozzá a Service Fabric által támogatott: olvasási és a rendszergazda segítségét. A szerepkörök használatával rendelhet a [a klasszikus Azure portálon][azure-classic-portal].

1. Az Azure portálon, nyissa meg az-bérlőjéhez tartozik, és válassza **alkalmazások**.
2. Válassza ki a webalkalmazás, melynek neve például `myTestCluster_Cluster`.
3. Kattintson a **felhasználók** fülre.
4. Válasszon ki egy felhasználót rendelni, és kattintson a **hozzárendelése** gomb a képernyő alján.

    ![Felhasználók hozzárendelése szerepkörökhöz gomb][assign-users-to-roles-button]
5. Válassza ki a szerepkör hozzárendelése a felhasználóhoz.

    !["A felhasználók hozzárendelése" párbeszédpanel][assign-users-to-roles-dialog]

> [!NOTE]
> További információ a szerepkörök a Service Fabric: [szerepköralapú hozzáférés-vezérlés a Service Fabric ügyfelek](service-fabric-cluster-security-roles.md).
>
>

 <a name="secure-linux-clusters"></a>
 <!--- Loc Comment: It seems that letter S in cluster was missing, which caused the wrong redirection of the link --->

## <a name="create-secure-clusters-on-linux"></a>Hozzon létre a biztonságos fürtök Linux rendszeren
A folyamat megkönnyítése adtunk egy [segítő parancsfájl](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). A segítő parancsfájl használata előtt győződjön meg arról, hogy már van telepítve az Azure parancssori felület (CLI), és az elérési úthoz. Győződjön meg arról, hogy a parancsfájl futtatásával végrehajtására jogosult `chmod +x cert_helper.py` azt letöltése után. Az első lépés az, hogy az Azure-fiókjával jelentkezzen be a parancssori felület használatával a `azure login` parancsot. Az Azure-fiókjába való bejelentkezés után a parancsfájllal segítő, a hitelesítésszolgáltató által aláírt tanúsítvány, az alábbi parancs látható módon:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]
```

A - ifile paramétere lehet egy .pfx fájlba vagy a .pem fájl bemenetként, és a tanúsítvány típusát (pfx vagy pem, illetve ha az önaláírt tanúsítvány ss).
A paraméter -h jelenít meg a súgószöveg.


A parancs a következő három karakterláncok kimeneteként adja vissza:

* SourceVaultID, amely az új KeyVault ResourceGroup hozott létre az Ön azonosítója
* A tanúsítvány eléréséhez CertificateUrl
* CertificateThumbprint, amelyek használják a hitelesítéshez

A következő példa bemutatja, hogyan használja a parancsot:

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
Az előző parancs végrehajtása a következő három karakterláncok az alábbiak szerint:

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

A tanúsítvány tulajdonosának nevét meg kell egyeznie a tartományt, amelyikhez a Service Fabric-fürt eléréséhez használt. A megfelelés szükség az SSL protokoll a fürt HTTPS felügyeleti végpontokat és a Service Fabric Explorerben talál. Nem szerezzen be egy SSL-tanúsítványt egy hitelesítésszolgáltatóból a `.cloudapp.azure.com` tartomány. Be kell szereznie egy egyéni tartománynevet a fürt számára. A hitelesítésszolgáltató tanúsítvány kérése, amikor a tanúsítvány tulajdonosának nevét meg kell egyeznie az egyéni tartománynevet, amelyekkel a fürt számára.

A tulajdonos nevét a bejegyzést kell létrehoznia egy biztonságos Service Fabric-fürt (nélkül az Azure AD), részben ismertetett módon is [erőforrás-kezelő konfigurálása Sablonparaméterek](#configure-arm). Kapcsolódás a biztonságos fürt az utasításokat követve [ügyfelek hozzáférése egy fürt hitelesítéséhez](service-fabric-connect-to-secure-cluster.md). Linux-fürtök nem támogatják az Azure AD-alapú hitelesítés. A rendszergazda és az ügyfél szerepkörök rendelhet a [szerepkörök hozzárendelése a felhasználók](#assign-roles) szakasz. Amikor megad egy Linux-fürt felügyeleti és az ügyfél szerepkörök, meg kell adnia a tanúsítvány-ujjlenyomatok hitelesítéshez. A tulajdonos nevét, mert a visszavont tanúsítványok, sem a tanúsítványlánc ellenőrzése zajlik nincs megadva.

Ha önaláírt tanúsítványt a teszteléshez használni kívánt, ugyanazzal a parancsfájllal segítségével hozhat létre egy. Majd feltöltheti a tanúsítványt a key vaultban azáltal, hogy a jelző `ss` a tanúsítvány elérési útja és a tanúsítvány nevének megadása helyett. Például tekintse meg a létrehozása és feltöltése egy önaláírt tanúsítványt a következő parancsot:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net"
```
Ez a parancs visszaadja a azonos három karakterláncok: SourceVault CertificateUrl és CertificateThumbprint. Biztonságos Linux-fürt és a hely hol helyezkedik el, az önaláírt tanúsítvány létrehozásához használhatja a karakterláncokat. Az önaláírt tanúsítvány kapcsolódik a fürthöz van szüksége. Kapcsolódás a biztonságos fürt az utasításokat követve [ügyfelek hozzáférése egy fürt hitelesítéséhez](service-fabric-connect-to-secure-cluster.md).

A tanúsítvány tulajdonosának nevét meg kell egyeznie a tartományt, amelyikhez a Service Fabric-fürt eléréséhez használt. A megfelelés szükség az SSL protokoll a fürt HTTPS felügyeleti végpontokat és a Service Fabric Explorerben talál. Nem szerezzen be egy SSL-tanúsítványt egy hitelesítésszolgáltatóból a `.cloudapp.azure.com` tartomány. Be kell szereznie egy egyéni tartománynevet a fürt számára. A hitelesítésszolgáltató tanúsítvány kérése, amikor a tanúsítvány tulajdonosának nevét meg kell egyeznie az egyéni tartománynevet, amelyekkel a fürt számára.

A paraméterek az Azure portálon, a segítő parancsfájlból leírtak szerint kitöltheti a [fürt létrehozása az Azure portálon](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal) szakasz.

## <a name="next-steps"></a>Következő lépések
Ezen a ponton rendelkezik olyan Azure Active Directory-felügyeleti hitelesítés biztonságos fürttel. Ezt követően [csatlakozzon a fürthöz](service-fabric-connect-to-secure-cluster.md) és megtudhatja, hogyan [alkalmazás titkos kulcsok kezelése](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Az ügyfél-hitelesítéshez Azure Active Directory beállításának hibaelhárítása
Futtatása a problémát az ügyfél-hitelesítéshez az Azure AD beállítása közben, tekintse át a lehetséges megoldások ebben a szakaszban.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer kéri, hogy válasszon ki egy tanúsítványt
#### <a name="problem"></a>Probléma
Bejelentkezés után sikeresen az Azure AD a Service Fabric Explorerben, a böngésző adja vissza a kezdőlapra, de az üzenet figyelmeztet a tanúsítvány kiválasztásához.

![SFX válassza a tanúsítványt párbeszédpanel][sfx-select-certificate-dialog]

#### <a name="reason"></a>Ok
A felhasználó nincs hozzárendelve az Azure AD-fürt alkalmazás szerepet. Így az Azure AD-alapú hitelesítés nem sikerül, a Service Fabric-fürt. Service Fabric Explorer tanúsítványhitelesítés áll vissza.

#### <a name="solution"></a>Megoldás
Kövesse az utasításokat az Azure AD beállítása, és a felhasználói szerepköröket. Is, azt javasoljuk, hogy kapcsolja be "Az alkalmazás eléréséhez szükséges felhasználói hozzárendelés" mint `SetupApplications.ps1` does.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>PowerShell-kapcsolatot egy hibaüzenettel meghiúsul: "a megadott hitelesítő adatok nem érvényesek"
#### <a name="problem"></a>Probléma
Csatlakozzon a fürthöz "AzureActiveDirectory" biztonsági módban, a bejelentkezés után sikeresen az Azure AD PowerShell segítségével történik, ha a kapcsolódás sikertelen, hiba történt: "a megadott hitelesítő adatok érvénytelenek."

#### <a name="solution"></a>Megoldás
Ez a megoldás megegyezik az előző egy.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer hibát ad vissza, amikor bejelentkezik: "AADSTS50011"
#### <a name="problem"></a>Probléma
A Service Fabric Explorerben Azure ad bejelentkezés megkísérlésekor a lap adja vissza a hiba: "AADSTS50011: A válaszcímet &lt;URL-cím&gt; nem egyezik meg az alkalmazáshoz beállított válasz-címeket: &lt;guid&gt;."

![A válaszcím SFX nem egyezik.][sfx-reply-address-not-match]

#### <a name="reason"></a>Ok
A fürt (webalkalmazás) Service Fabric Explorer jelölő kísérletet tesz az Azure AD hitelesítése, és a kérés részeként biztosítja a átirányítási visszatérési URL-CÍMÉT. Az URL-címe nem szerepel az Azure AD-alkalmazás, de **válasz URL-CÍMEN** listája.

#### <a name="solution"></a>Megoldás
A a **konfigurálása** lapon a fürt (webalkalmazás) alkalmazás hozzáadása az URL-címet a Service Fabric Explorer számára a **válasz URL-CÍMEN** listában, és cserélje le a listában található elemek egyikét. Ha végzett, mentse a módosítást.

![Webes alkalmazás válasz URL-címe][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>A fürt csatlakoztatása az Azure AD hitelesítési PowerShell használatával
Csatlakozás a Service Fabric-fürt, használja az alábbi PowerShell-példa:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

A Connect-ServiceFabricCluster parancsmaggal kapcsolatban további tudnivalókért lásd: [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Felhasználhatja a több fürt azonos Azure AD-bérlő?
Igen. De ne felejtse el a Service Fabric Explorer URL-cím hozzáadása a fürt (webalkalmazás) alkalmazást. Service Fabric Explorer nem működik.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Miért továbbra is szükséges egy kiszolgálói tanúsítványt az Azure AD be van kapcsolva?
FabricClient és FabricGateway a kölcsönös hitelesítést végezni. Az Azure AD-hitelesítés során az Azure AD-integrációs biztosít egy ügyfél-azonosító a kiszolgálóra, és a kiszolgálói tanúsítvány a kiszolgáló identitásának ellenőrzésére szolgál. A Service Fabric-tanúsítványokkal kapcsolatos további információkért lásd: [X.509-tanúsítványokat és a Service Fabric][x509-certificates-and-service-fabric].

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

