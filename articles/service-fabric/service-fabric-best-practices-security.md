---
title: Az Azure Service Fabric ajánlott biztonsági eljárásai
description: Ajánlott eljárások és tervezési szempontok az Azure Service Fabric-fürtök és -alkalmazások biztonságának megőrzéséhez.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: fa8bb41684271c7d4ebe90e31ce8019994fc1f41
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478752"
---
# <a name="azure-service-fabric-security"></a>Az Azure Service Fabric biztonsága 

Az Azure [biztonsági gyakorlati tanácsairól](https://docs.microsoft.com/azure/security/)az [Azure Service Fabric biztonsági gyakorlati tanácsai](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices)

## <a name="key-vault"></a>Key Vault

[Az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) az Azure Service Fabric-alkalmazások és -fürtök ajánlott titkos kiszolgálási szolgáltatása.
> [!NOTE]
> Ha a key vaultból származó tanúsítványok/titkos kulcsok virtuálisgép-méretezési készlettitkos kulcsként vannak telepítve egy virtuálisgép-méretezési csoportban, akkor a Key Vault és a Virtuálisgép-méretezési csoport közös en kell elhelyezve.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Szolgáltatásháló-tanúsítvány által kiállított hitelesítésszolgáltató létrehozása

Az Azure Key Vault-tanúsítvány létrehozható vagy importálható egy Key Vaultba. Key Vault-tanúsítvány létrehozásakor a személyes kulcs a Key Vaultban jön létre, és soha nem érhető el a tanúsítvány tulajdonosa. A tanúsítvány létrehozása a Key Vaultban az alábbiakat mondja:

- Önaláírt tanúsítvány létrehozása nyilvános-titkos kulcspár létrehozásához és tanúsítványhoz való társítására. A tanúsítványt saját kulccsal kell aláírni. 
- Hozzon létre egy új tanúsítványt manuálisan egy nyilvános-titkos kulcspár létrehozásához és egy X.509 tanúsítványaláíró kérelem létrehozásához. Az aláírási kérelmet a regisztrációs hatóság vagy a hitelesítésszolgáltató is aláírhatja. Az aláírt x509-es tanúsítvány egyesíthető a függőben lévő kulcspárral a Key Vaultkókk kv-tanúsítványának befejezéséhez. Bár ez a módszer további lépéseket igényel, nagyobb biztonságot nyújt, mert a személyes kulcs a Key Vaultban jön létre, és csak a Key Vaultban van létrehozva. Ezt az alábbi ábra ismerteti. 

Tekintse át [az Azure Keyvault tanúsítványlétrehozási módszereit](https://docs.microsoft.com/azure/key-vault/create-certificate) további részletekért.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Key Vault-tanúsítványok telepítése a Service Fabric fürt virtuálisgép-méretezési csoportjaiba

Ha tanúsítványokat szeretne telepíteni egy közös helyen lévő keyvaultból egy virtuálisgép-méretezési csoportba, használja az osProfile virtuálisgép-méretezési [készletet.](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) Az Erőforrás-kezelő sablon tulajdonságai a következők:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> A tárolót engedélyezni kell az Erőforrás-kezelő sablon telepítéséhez.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Hozzáférés-vezérlési lista (ACL) alkalmazása a Service Fabric-fürt tanúsítványára

[A Virtual Machine Scale Set extensions](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) publisher Microsoft.Azure.ServiceFabric a csomópontok biztonságának konfigurálására szolgál.
Ha a Service Fabric-fürtfolyamatai tanúsítványaira a tanúsítványaira szeretne ACL-t alkalmazni, használja a következő Erőforrás-kezelő sablontulajdonságait:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Service Fabric-fürttanúsítvány védelme köznéven

A Service Fabric-fürt `Common Name`tanúsítványáltali védelméhez használja az Erőforrás-kezelő sablon [tulajdonságtanúsítványátCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames), az alábbiak szerint:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> A Service Fabric-fürtök az állomás tanúsítványtárolójában talált első érvényes tanúsítványt fogják használni. Windows rendszerben ez lesz a legutóbbi lejárati dátummal rendelkező tanúsítvány, amely megegyezik a köznapi név és a kiállító ujjlenyomatával.

Az Azure-tartományok,\<például\>a * \<SUBDOMAIN\>.cloudapp.azure.com vagy az ALDOMAIN .trafficmanager.net, a Microsoft tulajdonában vannak. A hitelesítésszolgáltatók nem állítanak ki tanúsítványokat a tartományokhoz jogosulatlan felhasználók számára. A legtöbb felhasználónak tartományt kell vásárolnia egy regisztrátortól, vagy jogosult tartományrendszergazdának kell lennie ahhoz, hogy egy hitelesítésszolgáltató ilyen közös nevű tanúsítványt állítjon ki Önnek.

Ha további részleteket szeretne tudni arról, hogy miként állíthatja be a DNS-szolgáltatást úgy, hogy a tartományt Microsoft IP-címre oldja fel, olvassa el, hogyan konfigurálhatja [az Azure DNS-t a tartomány üzemeltetésére.](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)

> [!NOTE]
> Miután a tartománynévkiszolgálókat delegálása az Azure DNS-zóna névkiszolgálóira, adja hozzá a következő két rekordot a DNS-zónához:
> - Az "A" rekord a domain APEX, hogy nem egy `Alias record set` az összes IP-címek az egyéni domain feloldja.
> - A "C" rekord a Microsoft altartományok kiépített, hogy nem egy `Alias record set`. Használhatja például a Traffic Manager vagy a Terheléselosztó DNS-nevét.

Ha frissíteni szeretné a portált, hogy egyéni `"managementEndpoint"`DNS-nevet jelenítsen meg a Service Fabric-fürthöz, frissítse a Service Fabric-fürt erőforrás-kezelőjének következő sablontulajdonságait:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>A Service Fabric csomag titkos értékeinek titkosítása

A Service Fabric-csomagokban titkosított közös értékek közé tartoznak az Azure Container Registry (ACR) hitelesítő adatai, a környezeti változók, a beállítások és az Azure Volume bővítmény beépülő tárfiók kulcsai.

[Titkosítási tanúsítvány beállítása és titkos kulcsok titkosítása Windows-fürtökön](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows):

Saját aláírással alárendelt tanúsítvány létrehozása a titkos adattitkosításhoz:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Használja a [Key Vault-tanúsítványok üzembe helyezése a Service Fabric fürt virtuálisgép-méretezési csoportjaiban](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) található utasításokat a Key Vault-tanúsítványok üzembe helyezéséhez a Service Fabric-fürt virtuálisgép-méretezési készleteibe.

Titkosítsa a titkos kulcsot a következő PowerShell-paranccsal, majd frissítse a Service Fabric-alkalmazásjegyzéket a titkosított értékkel:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

[Titkosítási tanúsítvány beállítása és titkos kulcsok titkosítása Linux-fürtökön:](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux)

Saját aláírással alárendelt tanúsítvány létrehozása a titkos kulcsok titkosításához:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Használja a [Key Vault-tanúsítványok üzembe helyezése a Service Fabric fürt virtuálisgép-méretezési készletek](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) a Service Fabric fürt virtuálisgép-méretezési készletek utasításokat.

Titkosítsa titkos kulcsot a következő parancsokkal, majd frissítse a Service Fabric alkalmazásjegyzéket a titkosított értékkel:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

A védett értékek titkosítása után adja meg a [titkosított titkos kulcsokat a Service Fabric-alkalmazásban,](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application)és [fejtse vissza a szolgáltatáskódból származó titkosított titkos kulcsokat.](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code)

## <a name="include-certificate-in-service-fabric-applications"></a>Tanúsítvány felvétele a Service Fabric-alkalmazásokba

Ahhoz, hogy az alkalmazás hozzáférést a titkos kulcsokhoz, adja meg a tanúsítványt egy **SecretsCertificate** elem hozzáadásával az alkalmazás jegyzékfájl.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Service Fabric-alkalmazások hitelesítése az Azure Resources szolgáltatáshoz a felügyelt szolgáltatásidentitás (MSI) használatával

Az Azure-erőforrások felügyelt identitásairól a [Mi az Azure-erőforrások felügyelt identitásai című](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)témakörben olvashat.
Az Azure Service Fabric-fürtök a [felügyelt szolgáltatásidentitást](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources)támogató virtuálisgép-méretezési csoportokon találhatók.
Az MSI által a hitelesítéshez használható szolgáltatások listájának lehívásához olvassa el az [Azure Active Directory-hitelesítést támogató Azure Services című témakört.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication)


Ha engedélyezni szeretné a rendszer hez rendelt felügyelt identitást egy virtuális gép méretezési `"Microsoft.Compute/virtualMachinesScaleSets"` csoport vagy egy meglévő virtuálisgép-méretezési csoport létrehozása során, deklarálja a következő tulajdonságot:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
További információért olvassa el [a Mi a felügyelt identitások az Azure-erőforrásokhoz?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity)

Ha létrehozott egy [felhasználó által hozzárendelt felügyelt identitást,](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity)deklarálja a sablonban a következő erőforrást, hogy hozzárendelje a virtuálisgép-méretezési csoporthoz. Cserélje `\<USERASSIGNEDIDENTITYNAME\>` le a létrehozott felhasználó által hozzárendelt felügyelt identitás nevére:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Ahhoz, hogy a Service Fabric-alkalmazás egy felügyelt identitást használhasson, engedélyeket kell adni az Azure Resources-nek, amelyet hitelesítéshez szükséges.
A következő parancsok hozzáférést biztosítanak egy Azure-erőforráshoz:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

A Service Fabric-alkalmazáskódban [szerezzen be egy hozzáférési jogkivonatot](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) az Azure Resource Manager számára úgy, hogy a REST-et az alábbihoz hasonlóvá teszi:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

A Service Fabric alkalmazás ezután használhatja a hozzáférési jogkivonatot az Active Directoryt támogató Azure-erőforrások hitelesítéséhez.
A következő példa bemutatja, hogyan kell ezt a Cosmos DB erőforrás:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Windows biztonsági alapkonfigurációk
[Azt javasoljuk, hogy valósítson meg egy iparági szabványnak megfelelő konfigurációt, amely széles körben ismert és jól bevált, például a Microsoft biztonsági alapkonfigurációk, szemben az alapterv saját maga;](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines) a virtuálisgép-méretezési készletek kiépítése az Azure Desired State Configuration (DSC) bővítménykezelő, konfigurálása a virtuális gépek, ahogy azok online, így fut nak az éles szoftver.

## <a name="azure-firewall"></a>Azure Firewall
[Az Azure Firewall egy felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely védi az Azure virtuális hálózati erőforrásait. Ez egy teljesen állapotalapú tűzfal, mint a szolgáltatás beépített magas rendelkezésre állás és korlátlan felhő méretezhetőség.](https://docs.microsoft.com/azure/firewall/overview); ez lehetővé teszi, hogy a kimenő HTTP/S-forgalmat a teljesen minősített tartománynevek (FQDN) megadott listájára korlátozza, beleértve a helyettesítő karaktereket is. Ez a szolgáltatás nem igényel TLS/SSL-végződést. Azt javasoljuk, hogy használja [az Azure Firewall FQDN-címkék](https://docs.microsoft.com/azure/firewall/fqdn-tags) a Windows-frissítések, és a hálózati forgalom engedélyezése a Microsoft Windows Update végpontok átfolyhat a tűzfalon keresztül. [Az Azure Firewall sablon használatával történő üzembe helyezése](https://docs.microsoft.com/azure/firewall/deploy-template) mintát biztosít a Microsoft.Network/azureFirewalls erőforrássablon-definícióhoz. A Service Fabric-alkalmazások közös tűzfalszabályai a fürtök virtuális hálózatának engedélyezését teszik lehetővé:

- *download.microsoft.com
- *servicefabric.azure.com
- *.core.windows.net

Ezek a tűzfalszabályok kiegészítik az engedélyezett kimenő hálózati biztonsági csoportokat, amelyek magukban foglalják a ServiceFabric és a Storage, a virtuális hálózat engedélyezett célhelyei.

## <a name="tls-12"></a>TLS 1.2
[Tsg](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Windows Defender 

Alapértelmezés szerint a Windows Defender víruskereső telepítve van a Windows Server 2016 rendszerre. További információt a [Windows Defender víruskereső a Windows Server 2016 rendszeren](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)talál. A felhasználói felület alapértelmezés szerint telepítve van néhány sk-ra, de nem szükséges. A Windows Defender teljesítményre gyakorolt hatásának és erőforrás-felhasználásának csökkentése érdekében, és ha a biztonsági házirendek lehetővé teszik a nyílt forráskódú szoftverek folyamatainak és elérési útjának kizárását, deklarálja a következő Virtual Machine Scale Scale Set Extension Resource Manager sablontulajdonságokat, hogy kizárja a Service Fabric-fürtöt a vizsgálatokból:


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Ha nem használja a Windows Defender rendszert, olvassa el a kártevőirtó dokumentációját a konfigurációs szabályokról. A Windows Defender nem támogatott Linux on.

## <a name="platform-isolation"></a>Platform elkülönítése
Alapértelmezés szerint a Service Fabric-alkalmazások hozzáférést kapnak a Service Fabric futásidejű is, amely nyilvánul meg a különböző formákban: [környezeti változók](service-fabric-environment-variables-reference.md) mutató fájl elérési útjait a gazdagépen megfelelő alkalmazás és a Fabric-fájlok, egy folyamatközi kommunikációs végpont, amely elfogadja az alkalmazás-specifikus kérelmeket, és az ügyfél-tanúsítvány, amely fabric elvárja, hogy az alkalmazás hitelesítésére használja magát. Abban az esetben, ha a szolgáltatás nem megbízható kódot üzemeltet magának, célszerű letiltani ezt a hozzáférést az SF futásidejéhez - kivéve, ha kifejezetten szükséges. A futásidejű hozzáférés az alkalmazásjegyzék Házirend szakaszában található házirendek szakaszban található következő nyilatkozattal törlődik: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>További lépések

* Hozzon létre egy fürtöt a Windows Server: Service Fabric fürt létrehozása windows Server rendszerhez futtató virtuális gépeken vagy [számítógépeken.](service-fabric-cluster-creation-for-windows-server.md)
* Hozzon létre egy fürtöt virtuális gépeken vagy számítógépeken Linux ot futtató számítógépen: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md).
* További információ a [Service Fabric támogatási lehetőségeiről.](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
