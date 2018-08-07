---
title: Azure Service Fabric-fürt létrehozása sablonból |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan állítható be a biztonságos Service Fabric-fürtön az Azure-ban Azure Resource Manager, az Azure Key Vault és az Azure Active Directory (Azure AD) használatával az ügyfél-hitelesítéshez.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: aljo
ms.openlocfilehash: 780420c3ff69eb7da6e7c73b973164ed47c2c047
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525484"
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Service Fabric-fürt létrehozása Azure Resource Manager használatával 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Ez az útmutató lépésről lépésre végigvezeti az Azure-ban egy biztonságos Azure Service Fabric-fürt beállítása az Azure Resource Managerrel. Hogy tudomásul veszi, hogy a cikk e hosszú. Mindazonáltal kivéve, ha Ön már jól ismerik a tartalmat, ügyeljen arra, gondosan kövesse az alábbi lépéseket.

Az útmutató a következő eljárásokat ismerteti:

* Kulcs fogalmakat, amelyek a Service Fabric-fürt üzembe helyezése előtt érdemes figyelembe vennie.
* Fürt létrehozása az Azure-ban a Service Fabric Resource Manager-modulokat.
* Az Azure Active Directory (Azure AD) beállítása a fürt felügyeleti műveleteket felhasználók hitelesítéséhez.
* A fürthöz tartozó egyéni Azure Resource Manager-sablon készítése és üzembe helyezni.

## <a name="key-concepts-to-be-aware-of"></a>Vegye figyelembe a fő fogalmak
Az Azure-ban, a Service Fabric építjük, amelyek használatát egy x509 tanúsítvány a fürt és a végpontok védelmét. A Service Fabric tanúsítványokat használ a hitelesítéshez és titkosításhoz a fürtök és a rajtuk található alkalmazások különféle részeinek védelmére. Hozzáférés/végrehajtása műveletek a fürtöt, telepítése, frissítése és törlése, alkalmazások, szolgáltatások és az adatokat tartalmaznak, az ügyfél használhatja, tanúsítványok vagy Azure Active Directory hitelesítő adatokat. Azure Active Directory használata erősen ajánlott, mivel az az egyetlen módszer, hogy az ügyfelek tanúsítványokat megosztását.  További információ a tanúsítványok használata a Service Fabric: [Service Fabric-fürtök biztonsági forgatókönyveit][service-fabric-cluster-security].

Service Fabric-fürt biztonságossá tétele és adja meg az alkalmazás biztonsági funkciók X.509-tanúsítványokat használ. Használhat [Key Vault] [ key-vault-get-started] kattintva kezelheti az Azure Service Fabric-fürtök tanúsítványait. 


### <a name="cluster-and-server-certificate-required"></a>Fürt és a kiszolgálói tanúsítványt (kötelező)
Fürt biztonságossá tétele és a jogosulatlan hozzáférés elkerülése érdekében, hogy ezeket a tanúsítványokat (egy elsődleges és opcionálisan egy másodlagos) szükségesek. Fürtbiztonság kétféle módon biztosít:

* **Fürt hitelesítési:** hitelesíti a fürt összevonási csomópontok közötti kommunikációt. Csak a is igazolnia az identitását, ezt a tanúsítványt a csomópontok csatlakozhassanak a fürthöz.
* **Kiszolgálói hitelesítés:** hitelesíti a fürtkezelési végpontoknak egy kezelési ügyfél, így az ügyfél felismeri, hogy ez a valódi fürtön, és nem a "közbeékelődéses" beszél. Ezt a tanúsítványt is biztosít az SSL protokoll a HTTPS-felügyeleti API-hoz és a Service Fabric Explorert a HTTPS-kapcsolaton keresztül.

A tanúsítvány következő célokat, az alábbi követelményeknek kell megfelelnie:

* A tanúsítványnak tartalmaznia kell egy titkos kulcsot. Ezek a tanúsítványok általában rendelkeznek bővítmények .pfx vagy .pem  
* A tanúsítvány a kulcscsere, amely egy személyes információcsere (.pfx) fájl exportálható kell létrehozni.
* A **tanúsítvány tulajdonosnevének egyeznie kell a tartományhoz, amelyben a Service Fabric-fürt eléréséhez használt**. Erre a megfeleltetésre azért szükség az SSL protokoll biztosítja a fürt HTTPS-felügyeleti végpont és a Service Fabric Explorer. Az SSL-tanúsítványt egy hitelesítésszolgáltatótól (CA) nem szerezheti be a *. cloudapp.Azure.com formát követi tartományhoz. Egyéni tartománynevet kell beszereznie a fürt számára. Amikor tanúsítványt igényel egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének meg kell felelnie a fürthöz használt egyéni tartománynévnek.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Az Azure Active Directory beállítása az ügyfél-hitelesítéshez (nem kötelező, de ajánlott)

Azure ad-ben alkalmazásokhoz való felhasználói hozzáférés kezelése lehetővé teszi a szervezetek (más néven bérlők). Alkalmazások vannak osztva a webalapú bejelentkezési felhasználói felület és a egy natív ügyfél felhasználói élményét. Ez a cikk feltételezzük, hogy már létrehozott egy bérlőt. Ha nem rendelkezik, először olvassa el [Azure Active Directory-bérlő beszerzése][active-directory-howto-tenant].

Service Fabric-fürt kínál a különböző belépési pontok annak felügyeleti funkciójához, beleértve a webalapú [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] és [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Ennek eredményeképpen hozzon létre két Azure AD-alkalmazások a fürthöz való hozzáférés szabályozásához, egy webalkalmazás és a egy natív alkalmazást.

További információ arról, a dokumentum későbbi szakaszában beállításához.

### <a name="application-certificates-optional"></a>Tanúsítványok (nem kötelező)
Tetszőleges számú további tanúsítványok telepíthető alkalmazás biztonsági okokból egy fürtön. Mielőtt létrehozná a fürtöt, vegye figyelembe a kell telepíteni a csomópontokon, például a tanúsítványt igénylő alkalmazásbiztonsági forgatókönyveket:

* Titkosítás és visszafejtés alkalmazáskonfigurációs értékeket.
* Az adatok titkosítása az csomópont közötti replikálás során.

A fürtök biztonságos az elképzelés az, azonos, hogy azok Linux vagy Windows-fürtök. 

### <a name="client-authentication-certificates-optional"></a>(Nem kötelező) az ügyfél-hitelesítési tanúsítványok
Tetszőleges számú további tanúsítványok rendszergazdai vagy a felhasználói műveletek adható meg. Alapértelmezés szerint a fürt tanúsítványt a felügyeleti ügyfél jogosultságokkal rendelkezik. További ügyfél-tanúsítványok nem telepíthető a fürtbe, ugyanúgy kell megadni, a fürt konfigurációját a megengedett, azonban szükségük van a számítógépeken az ügyfél csatlakozik a fürthöz, és végezze el minden olyan felügyeleti műveletek.


## <a name="prerequisites"></a>Előfeltételek 
A fürtök biztonságos az elképzelés az, azonos, hogy azok Linux vagy Windows-fürtök. Ez az útmutató ismerteti az Azure PowerShell vagy az Azure CLI új fürtök létrehozásához. Az Előfeltételek a következők egyikét:

-  [Az Azure PowerShell 4.1 és újabb] [ azure-powershell] vagy [az Azure CLI 2.0-s és újabb][azure-CLI].
-  találhat további információkat az ide - a Service Fabric-modulok [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) és [az SF CLI modul](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Fürt üzembe helyezése Service Fabric erőforrás-kezelő modul használatával

Ebben a dokumentumban használjuk a Service Fabric-RM powershell és CLI modul üzembe helyezése egy fürtön, a PowerShell vagy a parancssori modul lehetővé teszi, hogy számos módja létezik. Ossza meg velünk mennek keresztül a azok. Válasszon a forgatókönyvet, hogy úgy gondolja, hogy ajánlott megfelel az igényeinek. 

- Új fürt létrehozása 
    - önaláírt tanúsítvány használatával a rendszer jön létre.
    - egy tanúsítvány használatával saját

Fürt alapértelmezett sablon vagy egy sablont, amely már rendelkezik

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Új fürt létrehozása – a rendszer a létrehozott önaláírt tanúsítvány használatával

A következő paranccsal hozhat létre fürtöt, ha azt szeretné, hogy a rendszer létrehoz egy önaláírt tanúsítványt, és a fürt védelme. Ez a parancs állít be egy elsődleges fürttanúsítvány fürtbiztonsági célra és a rendszergazdai hozzáférés beállításához használt tanúsítvánnyal, hogy felügyeleti műveleteket.

### <a name="login-to-azure"></a>bejelentkezés az Azure-bA

```PowerShell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
```

```CLI
azure login
az account set --subscription $subscriptionId
```
#### <a name="use-the-default-5-node-1-node-type-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Az alapértelmezett 5 1. csomóponton csomópont típusa sablon használata a fürt beállítása a modul részét képező

A következő paranccsal létrehozhat egy fürtöt, adjon meg a minimális paramétereket

A használt sablon érhető el a [Azure Service Fabric-sablonminták: windows-sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) és [Ubuntu-sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Az alábbi működik Windows és Linux-fürtök létrehozására vonatkozó parancsokat, egyszerűen adja meg az operációs rendszer ennek megfelelően. A PowerShell és CLI-parancsok is kimeneti a tanúsítványt a megadott CertificateOutputFolder; azonban győződjön meg arról, hogy tanúsítványmappa már létrehozott. A parancs a más paramétereket, valamint a VM-Termékváltozatok például vesz igénybe.

> [!NOTE]
> Az alábbi Powershell paranccsal csak működik az Azure Resource Manager PowerShell-lel verzió > 6.1-es verzióját. Az Azure Resource Manager PowerShell-verzió aktuális verziójának ellenőrzéséhez futtassa a következő PowerShell-parancsot: "Get-Module AzureRM". Az Azure Resource Manager PowerShell-verzió frissítése erre a hivatkozásra. https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-6.3.0
>
>
```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

```CLI
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

#### <a name="use-the-custom-template-that-you-already-have"></a>Használja az egyéni sablont, amely már rendelkezik 

Ha hozzon létre egy egyéni sablont, hogy illeszkedjen az igényeihez van szüksége, azt javasoljuk, hogy az egyik elérhető sablont megkezdése a [Azure Service Fabric-sablonminták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Kövesse az útmutatást és magyarázatok az [fürtsablonhoz testreszabása] [ customize-your-cluster-template] szakaszt.

Ha már rendelkezik egy egyéni sablont, majd győződjön meg arról, hogy ellenőrizze, hogy a három tanúsítvány kapcsolatos paramétereket a sablon és a alkalmazásparaméter-fájlt a következő nevesített és értékek a következők null értékű.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Íme az előbbiek az egyenértékű CLI-paranccsal. Módosítsa az értékeket a declare utasítást a megfelelő értékekre. Parancssori felület támogatja a többi paramétert, amely támogatja a fenti PowerShell-parancsot.

```CLI
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Új fürt létrehozása – a megadott tanúsítvány segítségével a Hitelesítésszolgáltatótól vásárolta, vagy már rendelkezik

A következő paranccsal hozhat létre fürtöt, ha a fürt védelmére használni kívánt tanúsítványt.

Ha ez egy hitelesítésszolgáltató által aláírt tanúsítvány, amely, megszűnik más célra, majd javasoljuk, hogy kimondottan a key vault adjon meg egy egyedi erőforráscsoport. Azt javasoljuk, hogy a key vault mindössze a saját erőforráscsoport. Ez a művelet lehetővé teszi a számítási és tárolási erőforráscsoport eltávolítására, beleértve az erőforráscsoport, amely a Service Fabric-fürt tartalmazza a kulcsok és titkok elvesztése nélkül. **Az erőforráscsoport, amely tartalmazza a kulcstartó _ugyanabban a régióban kell lennie_ a fürt által használt.**


#### <a name="use-the-default-5-node-1-node-type-template-that-ships-in-the-module"></a>Az alapértelmezett 5 1. csomóponton csomópont típusa sablon használata a modul részét képező
A használt sablon megtalálható a [Azure-minták: Windows sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) és [Ubuntu-sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

```CLI
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

#### <a name="use-the-custom-template-that-you-have"></a>Használja az egyéni sablont, amely rendelkezik 
Ha hozzon létre egy egyéni sablont, hogy illeszkedjen az igényeihez van szüksége, azt javasoljuk, hogy az egyik elérhető sablont megkezdése a [Azure Service Fabric-sablonminták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Kövesse az útmutatást és magyarázatok az [fürtsablonhoz testreszabása] [ customize-your-cluster-template] szakaszt.

Ha már rendelkezik egy egyéni sablont, majd győződjön meg arról, hogy ellenőrizze, hogy a három tanúsítvány kapcsolatos paramétereket a sablon és a alkalmazásparaméter-fájlt a következő nevesített és értékek a következők null értékű.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Íme az előbbiek az egyenértékű CLI-paranccsal. Módosítsa az értékeket a declare utasítást a megfelelő értékekre.

```CLI
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-key-vault"></a>A már feltöltött be a key vault titkos mutató használata

Egy meglévő key vault használata akkor _engedélyezni kell a központi telepítési_ , hogy a számítási erőforrás-szolgáltató tanúsítványok lekérése, és telepítse a fürtcsomópontok számára:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```
Íme az előbbiek az egyenértékű CLI-paranccsal. Módosítsa az értékeket a declare utasítást a megfelelő értékekre.

```CLI
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Az Azure Active Directory beállítása az ügyfél-hitelesítéshez

Azure ad-ben alkalmazásokhoz való felhasználói hozzáférés kezelése lehetővé teszi a szervezetek (más néven bérlők). Alkalmazások vannak osztva a webalapú bejelentkezési felhasználói felület és a egy natív ügyfél felhasználói élményét. Ez a cikk feltételezzük, hogy már létrehozott egy bérlőt. Ha nem rendelkezik, először olvassa el [Azure Active Directory-bérlő beszerzése][active-directory-howto-tenant].

Service Fabric-fürt kínál a különböző belépési pontok annak felügyeleti funkciójához, beleértve a webalapú [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] és [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Ennek eredményeképpen hozzon létre két Azure AD-alkalmazások a fürthöz való hozzáférés szabályozásához, egy webalkalmazás és a egy natív alkalmazást.

Egyes lépéseit az Azure AD konfigurálása a Service Fabric-fürt leegyszerűsítése hoztunk létre egy Windows PowerShell-parancsprogramok halmaza.

> [!NOTE]
> A következő lépéseket kell elvégeznie, a fürt létrehozása előtt. A parancsfájlok várhatóan a fürt nevét és a végpontok, mert az értékeket meg kell tervezni, és nem az, hogy már létrehozott értékeket.

1. [Töltse le a parancsfájlok] [ sf-aad-ps-script-download] a számítógépre.
2. Kattintson a jobb gombbal a zip-fájlt, jelölje be **tulajdonságok**, jelölje be a **feloldása** jelölőnégyzetet, majd kattintson a **alkalmaz**.
3. Csomagolja ki a tömörített fájlt.
4. Futtatás `SetupApplications.ps1`, és adja meg a TenantId ClusterName és WebApplicationReplyUrl paraméterekként. Példa:

```PowerShell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
```

> [!NOTE]
> Az országos felhők (Azure Government, Azure China, az Azure Germany), meg kell adnia a `-Location` paraméter.

A Bérlőazonosító találhatja meg a PowerShell-parancs végrehajtása `Get-AzureSubscription`. Ez a parancs végrehajtása a TenantId minden előfizetés jeleníti meg.

Az Azure AD-alkalmazások, a parancsfájl által létrehozott előtag ClusterName szolgál. Ez nem pontosan egyeznie kell a tényleges fürt neve. Célja, hogy csak az, hogy egyszerűbb legyen az Azure AD-összetevők leképezése, amelyet éppen használ a Service Fabric-fürthöz.

WebApplicationReplyUrl az alapértelmezett végpont, amely az Azure AD a felhasználóknak ad vissza, miután a bejelentkezés befejezéséhez. Állítsa be ezt a végpontot a Service Fabric Explorert a fürtben, amely alapértelmezés szerint az átemelt:

https://&lt;cluster_domain&gt;:19080/Explorer

Jelentkezzen be az Azure AD-bérlői rendszergazdai jogosultságokkal rendelkező fiók kéri. Miután bejelentkezett, a parancsfájl a webes és natív alkalmazások, amelyek a Service Fabric-fürtöt hoz létre. Ha megtekinti a bérlő alkalmazások a [az Azure portal][azure-portal], két új bejegyzést kell megjelennie:

   * *ClusterName*\_fürt
   * *ClusterName*\_ügyfél

A parancsfájl a JSON az Azure Resource Manager-sablon által igényelt, a fürt létrehozásakor a következő szakaszban, így célszerű, hogy ne zárja be a PowerShell-ablakot jelenít meg.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>A Service Fabric-fürt resource manager-sablon létrehozása
Ez a szakasz a felhasználók, akik egyéni hozhat létre egy Service Fabric-fürt resource manager-sablon van. Ha már rendelkezik egy sablont, továbbra is lépjen vissza és a PowerShell vagy parancssori felület modulok használata az üzembe helyezéshez. 

Mintául szolgáló Resource Manager-sablonok érhetők el a [Azure-minták a Githubon](https://github.com/Azure-Samples/service-fabric-cluster-templates). Ezek a sablonok fürtsablonhoz kiindulási pontként használható.

> [!NOTE]
> Az országos felhők (Azure Government, Azure China, az Azure Germany), emellett adja hozzá a következő `fabricSettings` az ARM-sablon: `AADLoginEndpoint`, `AADTokenEndpointFormat` és `AADCertEndpointFormat`.

### <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása
Ez az útmutató használja a [biztonságos fürt 5 csomópontos] [ service-fabric-secure-cluster-5-node-1-nodetype] példasablonja és a sablon paramétereit. Töltse le `azuredeploy.json` és `azuredeploy.parameters.json` a számítógépre, és nyissa meg mindkét fájlt a kedvenc szövegszerkesztőjével.

### <a name="add-certificates"></a>Tanúsítványok hozzáadása
Adja hozzá a tanúsítványok a fürt resource manager-sablon, amely tartalmazza a tanúsítvány kulcsait a key vaulttal való hivatkozással. Adja hozzá ezeket a kulcstartó paramétereket és értékeket egy Resource Manager-sablon paraméterfájljának (azuredeploy.parameters.json). 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>A virtual machine scale set osProfile minden tanúsítvány felvétele
A méretezési csoport erőforrás (Microsoft.Compute/virtualMachineScaleSets) osProfile szakaszában telepítve van a fürt minden tanúsítványt kell konfigurálni. Ez a művelet arra utasítja az erőforrás-szolgáltató a tanúsítvány telepítése a virtuális gépeken. A telepítés a fürttanúsítvány és az alkalmazásokhoz használni kívánt alkalmazás biztonsági tanúsítványokat is tartalmazza:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
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
Mind a Service Fabric fürterőforrás (Microsoft.ServiceFabric/clusters) a fürt hitelesítési tanúsítványt kell konfigurálni, és beállítja a virtuálisgép-méretezési csoport esetében a Service Fabric-bővítménnyel a virtuálisgép-méretezési készlet erőforrás. Ezzel az elrendezéssel fokozott lehetővé teszi, hogy a Service Fabric erőforrás-szolgáltató számára, konfigurálja a fürt és kiszolgálóhitelesítéssel felügyeleti végpontok használata.

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>A tanúsítvány adatait a virtuálisgép-méretezési készletet erőforrás hozzáadni:
```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
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
                  "commonNames": ["[parameters('certificateCommonName')]"],
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

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>A Tanúsítványadatok hozzáadása a Service Fabric-fürt erőforrás:
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
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure AD használata az ügyfél hozzáférésének az Azure AD-konfiguráció hozzáadása

A fürt Resource Manager-sablon az Azure AD-konfiguráció hozzáadása, amely tartalmazza a tanúsítvány kulcsait a key vaulttal való hivatkozással. Adja hozzá ezeket az Azure AD-paramétereket és értékeket egy Resource Manager-sablon paraméterfájljának (azuredeploy.parameters.json).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
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

### <a name="populate-the-parameter-file-with-the-values"></a>Töltse fel értékekkel alkalmazásparaméter-fájlt
Végül a kimeneti értékeket a key vault és az Azure AD PowerShell-parancsok használatával töltse fel a paramétereket tartalmazó fájlt.

Ha azt tervezi, használja az Azure service fabric RM PowerShell-modulok, majd nem kell a fürt Tanúsítványadatok feltöltése. Ha azt szeretné, hogy a rendszer hoz létre a saját aláírt fürt biztonsági tanúsítvány, csak null-ként tartja őket. 

> [!NOTE]
> Az erőforrás-kezelő modulok folytattuk a munkát, és ezek a üres paraméterértékek feltöltéséhez a paraméterek nevei sokkal meg az alábbi névvel

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Ha alkalmazás tanúsítványokat használ, vagy egy meglévő fürthöz, a key vaulthoz feltöltött használja, meg kell ezt az információt, és feltöltheti.

Az erőforrás-kezelő modulok nem tudnak létrehozni az Azure ad-ben a konfigurációt, ezért ha azt tervezi, az Azure AD használata az ügyfél hozzáférésének, meg kell adnia azt.

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

### <a name="test-your-template"></a>A sablon tesztelése  
A következő PowerShell-parancs használatával a Resource Manager-sablon tesztelés alkalmazásparaméter-fájlt:

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Abban az esetben problémákat, és rejtélyes üzeneteket, majd a "-Debug" beállítás.

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

A következő diagram azt ábrázolja, ahol a key vault és az Azure AD konfigurálása elférnek a Resource Manager-sablon.

![Erőforrás-kezelő függőségi térkép][cluster-security-arm-dependency-map]


## <a name="encrypting-the-disks-attached-to-your-windows-cluster-nodevirtual-machine-instances"></a>A Windows csatolt a lemezek titkosítása csomópont/virtuális gép példányok

Titkosításához a csomópontok csatolt lemezek (operációs rendszer meghajtóját és más felügyelt lemezek), hogy az Azure Disk Encryption használhatja. Az Azure Disk Encryption egy olyan új képesség, amely segít [Windows virtuálisgép-lemezek titkosítása](service-fabric-enable-azure-disk-encryption-windows.md). Az Azure Disk Encryption kihasználja az iparági szabvány [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkció a Windows operációs rendszer kötetének kötettitkosítást biztosít. A megoldás integrált [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsokat és titkos kulcsokat a key vault-előfizetés. A megoldás emellett biztosítja, hogy a virtuálisgép-lemezeken lévő összes adatot is titkosítása az Azure storage-ban. 

## <a name="encrypting-the-disks-attached-to-your-linux-cluster-nodevirtual-machine-instances"></a>A Linux rendszerű fürt csomópont/virtuális gép példányok csatlakoztatott lemezek titkosítása

Titkosításához a csomópontok csatolt lemezek (adatmeghajtó és más felügyelt lemezek), hogy az Azure Disk Encryption használhatja. Az Azure Disk Encryption egy olyan új képesség, amely segít [a Linux rendszerű virtuális gépek lemezeinek titkosításához](service-fabric-enable-azure-disk-encryption-linux.md). Az Azure Disk Encryption kihasználja az iparági szabvány [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) az adatlemezek kötettitkosítását biztosít Linux funkcióját. A megoldás integrált [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsokat és titkos kulcsokat a key vault-előfizetés. A megoldás emellett biztosítja, hogy a virtuálisgép-lemezeken lévő összes adatot is titkosítása az Azure storage-ban. 


## <a name="create-the-cluster-using-azure-resource-template"></a>A fürthöz az Azure resource-sablon létrehozása 

Most már telepítheti fürtben, a dokumentum korábbi szakaszában ismertetett lépések segítségével, vagy ha a paraméterfájlban kitölti az értékeket, majd, most már készen áll a fürt létrehozása a [Azure-erőforrás sablonalapú telepítés] [ resource-group-template-deploy] közvetlenül.

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Abban az esetben problémákat, és rejtélyes üzeneteket, majd a "-Debug" beállítás.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Felhasználók szerepkörökhöz rendelése
Az alkalmazások, amelyek a fürt létrehozása után a felhasználók hozzárendelése a Service Fabric által támogatott szerepkörök: csak olvasható és a rendszergazdával. A szerepkörök használatával rendelhet a [az Azure portal][azure-portal].

1. Az Azure Portalon válassza ki a bérlő, a jobb felső sarokban.

    ![Válassza ki a bérlő gomb][select-tenant-button]
2. Válassza ki **Azure Active Directory** a bal oldali lapon, és a "nagyvállalati alkalmazások" válassza.
3. Válassza ki az "Összes alkalmazás", majd keresse meg és válassza ki a webalkalmazás, melynek neve például `myTestCluster_Cluster`.
4. Kattintson a **felhasználók és csoportok** fülre.

    ![Felhasználók és csoportok lapon][users-and-groups-tab]
5. Kattintson a **felhasználó hozzáadása** az új oldalon gombra, válassza ki a felhasználó és a szerepkör hozzárendelése, és kattintson a **kiválasztása** gombra a lap alján.

    ![Felhasználók hozzárendelése szerepkörök oldal][assign-users-to-roles-page]
6. Kattintson a **hozzárendelése** gombra a lap alján.

    ![Adja hozzá a megerősítési hozzárendelés][assign-users-to-roles-confirm]

> [!NOTE]
> További információ a Service Fabric-szerepkörök: [szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Elhárításában nyújtanak segítséget az Azure Active Directory beállítása
Az Azure AD beállításához, és használja azt kihívást jelenthet, így az alábbiakban további információt a mi mindent a hibaelhárításhoz.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>A Service Fabric Explorer kéri, hogy válasszon ki egy tanúsítványt
#### <a name="problem"></a>Probléma
Bejelentkezés után sikeresen az Azure AD a Service Fabric Explorerben, a böngésző adja vissza a kezdőlapra, de egy üzenetben kéri, hogy válasszon ki egy tanúsítványt.

![SFX tanúsítvány párbeszédpanel][sfx-select-certificate-dialog]

#### <a name="reason"></a>Ok
A felhasználó nincs hozzárendelve egy szerepkörhöz az az Azure AD-fürt alkalmazást. Így az Azure AD-hitelesítés sikertelen lesz, Service Fabric-fürtön. A Service Fabric Explorer tanúsítványhitelesítés áll vissza.

#### <a name="solution"></a>Megoldás
Kövesse az utasításokat az Azure AD beállításához, és rendelje hozzá a felhasználói szerepköröket. Ezenkívül azt javasoljuk, hogy kapcsolja be "Az alkalmazás eléréséhez szükséges felhasználói kiosztása" `SetupApplications.ps1` does.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>A PowerShell-lel létesített kapcsolat megszakad egy hiba miatt: "a megadott hitelesítő adatok érvénytelenek"
#### <a name="problem"></a>Probléma
Bejelentkezés után sikeresen az Azure AD "AzureActiveDirectory" biztonsági üzemmódban használatával csatlakozhat a fürthöz PowerShell használatával történik, ha a kapcsolat egy hibaüzenettel meghiúsul: "a megadott hitelesítő adatok érvénytelenek."

#### <a name="solution"></a>Megoldás
Ez a megoldás megegyezik az előző egy.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>A Service Fabric Explorer hibát ad vissza, ha bejelentkezik: "AADSTS50011"
#### <a name="problem"></a>Probléma
Próbáljon meg bejelentkezni az Azure AD a Service Fabric Explorerben, ha az oldal adja vissza a hiba: "AADSTS50011: A válaszcímet &lt;URL-cím&gt; nem egyezik meg a az alkalmazáshoz konfigurált válaszcímekkel: &lt;guid&gt;."

![SFX a válaszcím nem egyezik.][sfx-reply-address-not-match]

#### <a name="reason"></a>Ok
A fürt (web) alkalmazás, amely a Service Fabric Explorer próbál meg Azure AD-val, és a kérés részeként az átirányítási visszatérési URL-címet biztosít. Az URL-cím nem szerepel az Azure AD-alkalmazás, de **válasz URL-cím** listája.

#### <a name="solution"></a>Megoldás
Válassza ki az "Alkalmazásregisztrációk" AAD lapot, válassza ki a fürt alkalmazást, és válassza a **válasz URL-címek** gombra. A "Válasz URL-címek" lapon a Service Fabric Explorer URL-cím hozzáadása a listához, vagy cserélje le a listán szereplő elemek közül. Ha ezzel végzett, mentse a módosítást.

![Webes alkalmazás válasz URL-címe][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Csatlakozás a fürthöz PowerShell-lel az Azure AD-hitelesítés használatával
A Service Fabric-fürthöz csatlakozhat, használja az alábbi PowerShell-példa:

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

A Connect-ServiceFabricCluster parancsmaggal kapcsolatos további információkért lásd: [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Felhasználhatja az Azure AD-bérlőhöz több fürtön?
Igen. De ne felejtse el a Service Fabric Explorer URL-cím hozzáadása a fürt (web) alkalmazáshoz. A Service Fabric Explorer nem működik.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Miért is meg kell egy kiszolgálói tanúsítványt engedélyezve van az Azure ad-ben?
FabricClient és FabricGateway hajtsa végre a kölcsönös hitelesítés. Az Azure AD-hitelesítés során az Azure AD-integrációs biztosít egy ügyfél identitást a kiszolgálónak, és a tanúsítvány a kiszolgáló identitásának ellenőrzésére szolgál. A Service Fabric-tanúsítványokkal kapcsolatos további információkért lásd: [X.509-tanúsítványokat és a Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>További lépések
Ezen a ponton rendelkezik egy biztonságos fürt az Azure Active Directory biztosító felügyeleti hitelesítést. Ezután [csatlakozni a fürthöz](service-fabric-connect-to-secure-cluster.md) , és ismerje meg, hogyan [titkos alkalmazáskulcsok kezelése](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[select-tenant-button]: ./media/service-fabric-cluster-creation-via-arm/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-via-arm/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png
