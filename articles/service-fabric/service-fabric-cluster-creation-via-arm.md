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
ms.date: 12/07/2017
ms.author: chackdan
ms.openlocfilehash: 251f7fc99f1c8d79f31118df11b7522930903c25
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>A Service Fabric-fürt létrehozása az Azure Resource Manager használatával 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Ez az útmutató lépésről lépésre bemutatja, hogyan állítja be a biztonságos Azure Service Fabric-fürt az Azure-ban Azure Resource Manager használatával. A Microsoft tudomásul, hogy a cikk hosszú. Mindazonáltal kivéve, ha már alaposan tisztában a tartalommal, ügyeljen arra, hogy alaposan kövesse az alábbi lépéseket.

Az útmutató a következő eljárásokat ismerteti:

* Alapfogalmak, érdemes figyelembe vennie ki a service fabric-fürt üzembe helyezése előtt szükséges.
* Fürt létrehozása az Azure service fabric erőforrás-kezelő modulok használatával.
* Azure Active Directory (Azure AD) beállítása a felhasználók felügyeleti műveleteket végez a fürt hitelesítéséhez.
* A fürt egyéni Azure Resource Manager sablont készít, és telepíti azokat.

## <a name="key-concepts-to-be-aware-of"></a>Vegye figyelembe a fontos fogalmakat
Az Azure Service fabric arra, hogy egy x509 használatát tanúsítvány védelméhez a fürt és a végpontok. A Service Fabric tanúsítványokat használ a hitelesítéshez és titkosításhoz a fürtök és a rajtuk található alkalmazások különféle részeinek védelmére. A fürt telepítése, frissítése és törlése, alkalmazások, szolgáltatások és hogy milyen adatokat tartalmaznak, beleértve a felügyeleti műveleteihez hozzáférési/végző ügyfél használható tanúsítványok vagy az Azure Active Directory hitelesítő adatai. Az Azure Active Directory használata erősen ajánlott, mert ez az egyetlen lehetőség meg kell akadályozni az ügyfelek számára a tanúsítványok megosztását.  További információ a tanúsítványok használatának módját a Service Fabric: [Service Fabric-fürt biztonsági forgatókönyvek][service-fabric-cluster-security].

Service Fabric fürt védelmét, és adja meg az alkalmazás szolgáltatásai a biztonsági X.509-tanúsítványokat használ. Használhat [Key Vault] [ key-vault-get-started] az Azure Service Fabric-fürtök tanúsítványainak kezelése. 


### <a name="cluster-and-server-certificate-required"></a>Fürt és a kiszolgálói tanúsítványt (kötelező)
Ezeket a tanúsítványokat (egy elsődleges és opcionálisan egy másodlagos) biztosításához a fürt és a jogosulatlan hozzáférés elkerülése érdekében azt szükségesek. Fürt biztonsági két módon tartalmazza:

* **Fürt hitelesítési:** hitelesíti a fürt összevonási csomópontok kommunikációt. Csak olyan csomópontot, amely bizonyítja, ezzel a tanúsítvánnyal az identitásukat csatlakozhat a fürthöz.
* **Kiszolgálóhitelesítés:** hitelesíti a felügyeleti ügyfél, a fürt felügyeleti végpontokat, így a felügyeleti ügyfél tudja azt a valódi fürt és a nem a "man a középső" van szó. Ez a tanúsítvány is biztosít az SSL protokoll a HTTPS-szolgáltatásfelügyeleti API és a Service Fabric Explorer HTTPS-KAPCSOLATON keresztül.

Ezeket a célokat szolgál, hogy a tanúsítvány a következő követelményeknek kell megfelelniük:

* A tanúsítványnak tartalmaznia kell egy titkos kulccsal. Ezek a tanúsítványok általában rendelkeznek bővítmények .pfx vagy .pem  
* A kulcscseréhez használt, amely exportálható személyes információcsere (.pfx) fájlba a tanúsítványt kell létrehozni.
* A **tanúsítvány tulajdonosának nevét meg kell egyeznie a tartományt, amelyikhez a Service Fabric-fürt eléréséhez használt**. A megfelelő szükség az SSL protokoll a fürt HTTPS felügyeleti végpont és a Service Fabric Explorerben talál. Az SSL-tanúsítványt egy hitelesítésszolgáltatótól (CA) származó nem szerezze be a *. cloudapp.azure.com tartomány. Be kell szereznie egy egyéni tartománynevet a fürt számára. A hitelesítésszolgáltató tanúsítvány kérése, amikor a tanúsítvány tulajdonosának nevét meg kell egyeznie az egyéni tartománynevet, amelyekkel a fürt számára.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Azure Active Directory beállítása az ügyfél-hitelesítéshez (nem kötelező, de ajánlott)

Az Azure AD lehetővé teszi, hogy a szervezetek (más néven bérlői) alkalmazásokhoz való felhasználói hozzáférések kezelése. Alkalmazások oszthatók rendelkező webalapú bejelentkezési felhasználói felület és az egy natív ügyfél révén azokat. Ebben a cikkben azt feltételezzük, hogy már létrehozta a bérlő. Ha nem, olvassa el [Azure Active Directory-bérlő beszerzése][active-directory-howto-tenant].

A Service Fabric-fürt a felügyeleti funkciót, beleértve a webalapú több belépési pontot nyújt [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] és [Visual Studio][service-fabric-manage-application-in-visual-studio]. Ennek eredményeképpen hoz létre, két Azure AD-alkalmazások történő hozzáférés szabályozása érdekében a fürt, egy webes alkalmazás és egy natív alkalmazás.

További információ arról, a dokumentum későbbi szakaszában beállítása.

### <a name="application-certificates-optional"></a>Alkalmazás-tanúsítványok (nem kötelező)
Tetszőleges számú további tanúsítványokat is telepíthető egy fürt biztonsági okokból. Az fürt létrehozása előtt fontolja meg az alkalmazás biztonsági kell telepíteni a csomópontokat, például a tanúsítványt igénylő forgatókönyvek:

* Titkosítás és visszafejtés az alkalmazáskonfigurációs értékeket.
* Replikáció során az adatok csomópontok közötti titkosítása.

A biztonságos fürtök létrehozására fogalma azonos, hogy azok a Linux vagy a Windows-fürtök. 

### <a name="client-authentication-certificates-optional"></a>Ügyfél-hitelesítési tanúsítványok (nem kötelező)
A további tanúsítványok tetszőleges rendszergazda vagy felhasználó ügyfélműveleteknél adható meg. Alapértelmezés szerint a fürt tanúsítvány rendszergazda ügyfél jogosultságokkal rendelkezik. További ügyfél-tanúsítványok bármelyikének ne telepítse a fürthöz, ugyanúgy kell megadni: a fürt konfigurációjába megengedett, azonban az ügyfélgépek csatlakozzon a fürthöz, és végezze el minden olyan felügyeleti telepíteni kell műveletek.


## <a name="prerequisites"></a>Előfeltételek 
A biztonságos fürtök létrehozására fogalma azonos, hogy azok a Linux vagy a Windows-fürtök. Ez az útmutató ismerteti az azure powershell vagy az új fürtök létrehozása az azure CLI használata. Az előfeltételeket vagy 

-  [Az Azure PowerShell 4.1-et vagy újabb verzió] [ azure-powershell] vagy [Azure CLI 2.0-s vagy újabb verzió][azure-CLI].
-  Részletek található a szolgáltatás fabic modulok ide - [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) és [az ú CLI modul](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Fürt központi telepítése a service fabric erőforrás-kezelő modul használatával

Ebben a dokumentumban azt szeretné használni a service fabric RM powershell és CLI-modul telepítése a fürt, a powershell vagy a CLI modul parancs lehetővé teszi a forgatókönyvek. Ossza meg velünk halad át a hozzájuk. Mentse a forgatókönyvet, hogy úgy érzi, hogy ajánlott megfelel az igényeinek. 

- Hozzon létre egy új - rendszer létrehozott önaláírt tanúsítványt használó fürtben
    - Fürt alapértelmezett sablon használata
    - a sablon használata, amely már rendelkezik
- Hozzon létre egy új fürtöt - tanúsítványokkal már Ön a tulajdonosa
    - Fürt alapértelmezett sablon használata
    - a sablon használata, amely már rendelkezik

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Új fürtre – a rendszer létrehozott önaláírt tanúsítvány létrehozása

A következő parancs segítségével fürt létrehozása, ha szeretné, hogy a rendszer egy önaláírt tanúsítványt létrehozni, és használja a fürt védelmét. Ez a parancs tanúsítványt állít be egy elsődleges fürt a fürt biztonsági és felügyeleti hozzáférés beállítása, hogy a tanúsítvány használatával felügyeleti műveletek elvégzéséhez használt.

### <a name="login-in-to-azure"></a>bejelentkezés az Azure-bA.

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```
#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Az alapértelmezett 5-csomópont 1 nodetype sablon, a fürt beállítása a modul részét képező használata

Az alábbi parancs segítségével gyorsan, hozzon létre egy fürtöt minimális paraméterek megadásával

A használt sablon megtalálható a [azure service fabric sablon minták: windows sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) és [Ubuntu sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Az alábbi működik a Windows és Linux-fürtök létrehozására vonatkozó parancsokat, egyszerűen adja meg az operációs rendszer, ennek megfelelően. A powershell parancssori felület parancsait is kiírja az tanúsítvány-tanúsítványt a megadott theCertificateOutputFolder /. A parancs felsorolásszerűen más paramétereket, valamint például a virtuális gép Termékváltozat.

```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!1"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"



az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Az egyéni sablon használata, amely már rendelkezik 

Ha egy egyéni sablont az igényeinek szerzői van szüksége, javasoljuk, hogy az egyik elérhető sablont megkezdése a [azure service fabric sablon minták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Kövesse az útmutatást és magyarázataik a [a fürt-sablon testre szabása] [ customize-your-cluster-template] az alábbi szakasz.

Ha már rendelkezik egy egyéni sablont, majd győződjön meg arról, hogy ellenőrizze, hogy a három tanúsítvány kapcsolatos paramétereket a sablonban és a paraméterfájl elnevezése a következő és értékek a következők null értékű.

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


```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Ez az egyenértékű parancssori felület parancsot ugyanerre. Módosítsa az értékeket a declare utasítást a megfelelő értékeket. Parancssori felülete támogatja az összes többi paraméter, amely támogatja a fenti powershell-parancsot.

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


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Új fürt létrehozása – használ a tanúsítványt egy hitelesítésszolgáltatótól vásárolta, vagy már rendelkezik.

A következő paranccsal hozhat létre fürtöt, ha a fürt védelméhez használni kívánt tanúsítványt.

Ha ez egy hitelesítésszolgáltató által aláírt tanúsítvány, amely kat, más célra, majd javasoljuk, hogy kimondottan a key vaultban adjon meg egy eltérő erőforráscsoportban található. Azt javasoljuk, hogy a key vault kerüljenek-e a saját erőforráscsoportot. Ez a művelet lehetővé teszi a számítási és tárolási erőforrás csoportokhoz, beleértve az erőforráscsoport, amely tartalmazza a Service Fabric-fürt, a kulcsok és titkos kulcsok elvesztése eltávolítását. **Az erőforráscsoport, amely tartalmazza a kulcstartót _ugyanabban a régióban kell lennie_ a fürt által használt.**


#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module"></a>Az alapértelmezett 5-csomópont 1 nodetype sablon, a modul részét képező használata
A használt sablon megtalálható a [azure-minták: windows sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) és [Ubuntu sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

```Powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!1" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

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

#### <a name="use-the-custom-template-that-you-have"></a>Az egyéni sablon használata, amely rendelkezik 
Ha egy egyéni sablont az igényeinek szerzői van szüksége, javasoljuk, hogy az egyik elérhető sablont megkezdése a [azure service fabric sablon minták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Kövesse az útmutatást és magyarázataik a [a fürt-sablon testre szabása] [ customize-your-cluster-template] az alábbi szakasz.

Ha már rendelkezik egy egyéni sablont, majd győződjön meg arról, hogy ellenőrizze, hogy a három tanúsítvány kapcsolatos paramétereket a sablonban és a paraméterfájl elnevezése a következő és értékek a következők null értékű.

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


```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword #certPassword

```

Ez az egyenértékű parancssori felület parancsot ugyanerre. Módosítsa az értékeket a declare utasítást a megfelelő értékeket.

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

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-keyvault"></a>A titkos kulcs már feltöltött a keyvault mutató hivatkozások használata

Egy meglévő kulcstároló használatára akkor _engedélyezni kell a központi telepítési_ tanúsítványok beszerzése és telepíti a fürtcsomópontokon a számítási erőforrás-szolgáltató engedélyezéséhez:

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment


$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroup -SecretIdentifier $secretID -TemplateFile $templateFile -ParameterFile $templateParmfile 

```
Ez az egyenértékű parancssori felület parancsot ugyanerre. Módosítsa az értékeket a declare utasítást a megfelelő értékeket.

```cli

declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifieraz $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 

```

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

Jelentkezzen be az Azure AD-bérlő rendszergazdai jogosultsággal rendelkező fiók kéri. Miután bejelentkezik, a parancsfájl hoz létre, a web- és natív alkalmazások a Service Fabric-fürt képviseli. Ha megnézi a bérlős alkalmazásokhoz a [Azure-portálon][azure-portal], két új bejegyzést kell megjelennie:

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

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Service Fabric-fürt Resource Manager sablon létrehozása
Ebben a szakaszban van egyéni kívánó felhasználóknak a Service Fabric fürt Resource Manager-sablon létrehozásához. Miután egy sablon, továbbra is visszatérhet és a powershell vagy a CLI modulok segítségével telepítheti azt. 

A minta Resource Manager-sablonok találhatók a [Azure-minták a Githubon](https://github.com/Azure-Samples/service-fabric-cluster-templates). Ezek a sablonok kiindulási pontként használható a fürt sablon.

### <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása
Ez az útmutató használja a [biztonságos 5-csomópontot tartalmazó fürtben] [ service-fabric-secure-cluster-5-node-1-nodetype] példa sablon és a sablon paramétereit. Töltse le `azuredeploy.json` és `azuredeploy.parameters.json` a számítógépre, és nyissa meg a fájlt a kedvenc szövegszerkesztőjével.

### <a name="add-certificates"></a>Tanúsítványok hozzáadása
Adja hozzá a tanúsítványok a fürt Resource Manager-sablon a kulcstároló, amely tartalmazza a tanúsítvány kulcsait Vezérlőpultjának. Adja hozzá ezeket a kulcstároló paraméterek és értékek a Resource Manager sablon paraméterfájl (azuredeploy.parameters.json). 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Minden tanúsítvány felvétele a virtuális gép méretezési készlet osProfile
A skála erőforrás (Microsoft.Compute/virtualMachineScaleSets) osProfile szakaszában telepítve van-e a fürt minden tanúsítványt kell konfigurálni. Ez a művelet arra utasítja az erőforrás-szolgáltató a tanúsítvány telepítése a virtuális gépeken. Ehhez a telepítéshez a a fürt tanúsítvány és az alkalmazások használni kívánt alkalmazás biztonsági tanúsítványokat is tartalmaz:

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
A fürt hitelesítési tanúsítványt meg kell adni mind a Service Fabric fürt erőforrás (Microsoft.ServiceFabric/clusters), és a Service Fabric-bővítményt a virtuálisgép-méretezési beállítja azt a virtuálisgép-méretezési készlet erőforrás. Ezzel az elrendezéssel lehetővé teszi, hogy a fürt hitelesítési és felügyeleti végpontok kiszolgáló hitelesítésének konfigurálását a Service Fabric erőforrás-szolgáltató.

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Vegye fel a tanúsítvány adatait a virtuálisgép-méretezési erőforrás beállítása:
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

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>A tanúsítvány-információk hozzáadása a Service Fabric-fürt erőforrás:
```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
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

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Az Azure Active Directory beállítása az Azure AD használatára az ügyfél hozzáférésének hozzáadása

A kulcstároló, amely tartalmazza a tanúsítvány kulcsait Vezérlőpultjának hozzáadhat egy fürt Resource Manager-sablon az Azure Active Directory beállítása s. Adja hozzá azokat az Azure AD-paraméterek és értékek a Resource Manager sablon paraméterfájl (azuredeploy.parameters.json).

```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
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

### <a name="populate-the-parameter-file-with-the-values"></a>A paraméterfájl értékekkel való feltöltéséhez.
Végezetül a kimeneti értékeit a kulcstartót és az Azure AD powershell-parancsok használata a paraméterfájlban feltöltéséhez:

Ha tervezi az Azure service fabric RM powershell-modulok, majd nem kell a fürt Tanúsítványadatok feltöltése, ha azt szeretné a rendszer hoz létre az önkiszolgáló aláírt fürt biztonsági tanúsítvány, csak válaszadás null. 

> [!NOTE]
> Az erőforrás-kezelő modulok átvételéhez, és ezek üres paraméterértékek feltöltése a paraméterek sokkal nevének egyeznie az alábbi
>

```json
        "clusterCertificateThumbprint": {
            "value": ""
        },
        "clusterCertificateUrlValue": {
            "value": ""
        },
        "sourceVaultvalue": {
            "value": ""
        },
```

Alkalmazás Tanúsítványos használ, vagy egy meglévő fürthöz, amely a keyvault feltöltött használ, ha szeretné-e ezek az információk beszerzése és feltöltése 

Az RM-modulok nincs geneate teszi meg az Azure AD beállításai. Ezért ha le szeretné használni az Azure AD az ügyfél hozzáférésének, meg kell feltöltéséről.

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
A következő PowerShell-parancs segítségével tesztelje a Resource Manager-sablon paraméter-fájllal:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Ha problémákat tapasztal, és mégis fontos kontextusinformációkat üzeneteket, majd használja "-Debug" lehetőség.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Az alábbi ábrán látható, ahol a kulcstartót és az Azure Active Directory beállítása felelnek meg a Resource Manager-sablon.

![Erőforrás-kezelő kapcsolatfüggőségi térképének megjelenítéséhez][cluster-security-arm-dependency-map]

## <a name="create-the-cluster-using-azure-resource-template"></a>Az Azure erőforrás-sablonnal a fürt létrehozása 

Most már telepítheti fürtben, a dokumentum korábbi szakaszában ismertetett lépésekkel, vagy ha már rendelkezik az értékek a paraméterfájl feltöltve, majd most készen áll a fürt létrehozása a [Azure-erőforrás sablon-üzembehelyezés] [ resource-group-template-deploy] közvetlenül.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Ha problémákat tapasztal, és mégis fontos kontextusinformációkat üzeneteket, majd használja "-Debug" lehetőség.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Felhasználók hozzárendelése szerepkörökhöz
Miután létrehozta a fürt képviselő alkalmazások, a felhasználók a szerepkörökhöz rendeljen hozzá a Service Fabric által támogatott: olvasási és a rendszergazda segítségét. A szerepkörök használatával rendelhet a [Azure-portálon][azure-portal].

1. Az Azure-portálon válassza ki a bérlő jobb felső sarokban.

    ![Válassza ki a bérlő gomb][select-tenant-button]
2. Válassza ki **Azure Active Directory** a bal oldali lapon, és a vállalati alkalmazások"select".
3. Válassza ki a "Minden alkalmazások", keresése és válassza a webes alkalmazás, melynek neve például `myTestCluster_Cluster`.
4. Kattintson a **felhasználók és csoportok** fülre.

    ![Felhasználók és csoportok lapon][users-and-groups-tab]
5. Kattintson a **felhasználó hozzáadása** gombra kattint, az új oldalon, a felhasználó és a szerepkör hozzárendelése, majd válassza ki a **válasszon** gombra az oldal alján.

    ![Felhasználók hozzárendelése szerepkörök lap][assign-users-to-roles-page]
6. Kattintson a **hozzárendelése** gombra az oldal alján.

    ![Adja hozzá a hozzárendelés megerősítése][assign-users-to-roles-confirm]

> [!NOTE]
> További információ a szerepkörök a Service Fabric: [szerepköralapú hozzáférés-vezérlés a Service Fabric ügyfelek](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Hibaelhárítás súgója az Azure Active Directory beállítása
Az Azure AD beállítása, és használja azt, kihívást jelenthet, az alábbiakban néhány mutatók a teendők, az a probléma hibakeresését.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer kéri, hogy válasszon ki egy tanúsítványt
#### <a name="problem"></a>Probléma
Bejelentkezés után sikeresen az Azure AD a Service Fabric Explorerben, a böngésző adja vissza a kezdőlapra, de az üzenet figyelmeztet a tanúsítvány kiválasztásához.

![Tanúsítvány párbeszédpanel SFX][sfx-select-certificate-dialog]

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
Válassza ki az "App regisztrációk" AAD lapon, válassza ki a fürt alkalmazást, majd a **válasz URL-címek** gombra. A "Válasz URL-címek" lapon Service Fabric Explorer URL-cím hozzáadása a listához, vagy cserélje le a listában található elemek egyikét. Ha végzett, mentse a módosítást.

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

## <a name="next-steps"></a>Következő lépések
Ezen a ponton rendelkezik olyan Azure Active Directory-felügyeleti hitelesítés biztonságos fürttel. Ezt követően [csatlakozzon a fürthöz](service-fabric-connect-to-secure-cluster.md) és megtudhatja, hogyan [alkalmazás titkos kulcsok kezelése](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
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

