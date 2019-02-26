---
title: Az Azure Service Fabric ajánlott biztonsági eljárások |} A Microsoft Docs
description: Ajánlott eljárások az Azure Service Fabric biztonsága.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 350aef037f019733e02331623758c14a3c64ab50
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804992"
---
# <a name="azure-service-fabric-security"></a>Az Azure Service Fabric biztonsága 

További információ [Azure ajánlott biztonsági eljárások](https://docs.microsoft.com/azure/security/), tekintse át [Azure Service Fabric ajánlott biztonsági eljárások](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices)

## <a name="key-vault"></a>Key Vault

[Az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) van a javasolt titkos kulcsok Azure Service Fabric-alkalmazások és-fürtök a felügyeleti szolgáltatás.
> [!NOTE]
> Ha a Virtual Machine Scale Set-titokként egy virtuális gép méretezési beállítása a Key vault tanúsítványokkal vagy titkos kódokkal van telepítve, majd a Key Vault és a Virtual Machine Scale Set kell közös elhelyezésű.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>A Service Fabric-tanúsítványt kiállító hitelesítésszolgáltató létrehozása

Az Azure Key Vault-tanúsítvánnyal vagy létrehozott vagy importált egy Key Vaultot. Amikor a Key Vault-tanúsítvánnyal jön létre, a titkos kulcsot a Key vaultban létrejött, és sosem hagyja el a tanúsítvány tulajdonosának. Az alábbiakban hozzon létre egy tanúsítványt a Key Vaultban módjai:

- Hozzon létre egy nyilvános-titkos kulcspár létrehozása, és társíthatja azt egy tanúsítvány egy önaláírt tanúsítványt. A tanúsítvány a saját kulcs alá fogja írni. 
- Hozzon létre egy új tanúsítványt manuálisan hozzon létre egy nyilvános-titkos kulcspárt, és hozzon létre egy X.509 tanúsítvány-aláírási kérelmet. Az aláírási kérelmet a regisztrációszolgáltató vagy hitelesítésszolgáltató által írhatók alá. A tanúsítvány egyesíthető a függőben lévő kulccsal aláírt x509 párosítsa a KV tanúsítványt a Key Vaultban végrehajtásához. Bár ez a módszer további lépéseket igényel, azt biztosítja a nagyobb biztonságot, mert a titkos kulcs létrehozott, és a Key Vault korlátozott. Ennek az az alábbi ábrán a magyarázatát. 

Felülvizsgálat [Azure Keyvault tanúsítvány létrehozásának módszerei](https://docs.microsoft.com/azure/key-vault/create-certificate) további részleteket.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>A Key Vault tanúsítványok telepítése a Service Fabric-fürt virtuális gépek méretezési

Egy virtuálisgép-méretezési csoportban egy közös elhelyezésű keyvault a tanúsítványok központi telepítéséhez használja a Virtual Machine Scale Set [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile). A Resource Manager-sablon tulajdonságai a következők:

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
> A tároló engedélyezni kell a Resource Manager-sablon üzembe helyezése.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Egy hozzáférés-vezérlési lista (ACL) vonatkoznak a tanúsítványt a Service Fabric-fürthöz

[Virtuális gépek méretezési csoportjának bővítményei](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) közzétevő Microsoft.Azure.ServiceFabric a csomópontok biztonsági konfigurálására szolgál.
Egy ACL alkalmazza a tanúsítványokat a Service Fabric-fürt folyamatok, használja a következő Resource Manager-sablon tulajdonságainak:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Biztonságos Service Fabric-fürt tanúsítvány köznapi név szerint

A Service Fabric-fürt tanúsítvánnyal biztonságos `Common Name`, használja a Resource Manager-sablon tulajdonságot [certificateCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames), az alábbiak szerint:

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
> Service Fabric-fürtök az első érvényes tanúsítvány úgy találja, a gazdagép tanúsítványtárolóban fogja használni. A Windows ez lesz a legújabb lejáró dátum, amely megfelel a köznapi neve és kiállító ujjlenyomata a tanúsítvány.

Azure-tartomány, például a *\<az ALTARTOMÁNY\>. cloudapp.Azure.com formát követi vagy \<az ALTARTOMÁNY\>. trafficmanager.net, a Microsoft tulajdonában. Jogosulatlan felhasználók hitelesítésszolgáltatók tanúsítványainak tartományok nem ad ki. A legtöbb felhasználó kell tartományregisztrálótól egy tartományhoz, vagy egy meghatalmazott tartományi rendszergazda, a hitelesítésszolgáltató a tanúsítványt állít ki, a közös néven.

A további részleteket a Microsoft IP-cím, oldja fel a tartomány DNS-szolgáltatás konfigurálásához tekintse át a konfigurálása [üzemeltessen saját tartományt az Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

> [!NOTE]
> After delegating your domains name servers to your Azure DNS zone name servers, add the following two records to your DNS Zone:
> - Egy "A" rekord, amely nem APEX-tartomány egy `Alias record set` minden IP-cím az egyéni tartomány megoldja.
> - A Microsoft alárendelt tartományok számára kiépített nem "C" rekord egy `Alias record set`. Használhatja például a Traffic Manager vagy a terheléselosztó DNS-nevét.

A portálon megjelenjen egy egyéni DNS-nevet a Service Fabric-fürt frissítése `"managementEndpoint"`, frissítéséhez kövesse a Service Fabric-fürt Resource Manager-sablon tulajdonságait:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Titkos értékkel a Service Fabric-csomag titkosítása

Gyakori a Service Fabric-csomagokhoz titkosított tartalmazzák-e az Azure Container Registry (ACR) hitelesítő adatait, a környezeti változók, beállítások és Azure kötet beépülő modul tárfiókkulcsok.

A [állítsa be a titkosítási tanúsítványt, és Windows-fürtök a titkos kulcsok titkosítására](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows):

Hozzon létre egy önaláírt tanúsítványt a titkos kulcs titkosításához:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Kövesse az utasításokat a [Service Fabric-fürt virtuális gép üzembe helyezése Key Vault-tanúsítványokat a méretezési csoportokat](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) Key Vault Certificates központi telepítése a Service Fabric-fürt a virtuálisgép-méretezési csoportok.

Titkosítani a titkos kód a következő PowerShell-paranccsal, és ezután frissítse a Service Fabric-alkalmazás jegyzékfájlja a titkosított értékkel:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

A [állítsa be a titkosítási tanúsítványt, és titkosítani a titkos kulcsokat a Linux-fürtökön](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux):

Hozzon létre egy önaláírt tanúsítványt a titkos kulcsok titkosítására:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Kövesse az utasításokat a [Service Fabric-fürt virtuális gép üzembe helyezése Key Vault-tanúsítványokat a méretezési csoportokat](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) , a Service Fabric-fürt a virtuálisgép-méretezési csoportok.

Titkosítani a titkos kód a következő parancsokat, és frissítse a Service Fabric Application Manifest titkosított értékre:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

A védett értékek titkosítása után [adja meg a titkosított titkos kulcsok a Service Fabric-alkalmazás](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application), és [visszafejteni a titkosított titkos kulcsok szolgáltatás kódból](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Felügyeltszolgáltatás-identitás (MSI) Azure-erőforrások a Service Fabric-alkalmazások hitelesítése

Az Azure-erőforrások felügyelt identitások kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitások?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-it-work).
Az Azure Service Fabric-fürtök a Virtual Machine Scale Sets, amely támogatja a tárolt [Felügyeltszolgáltatás-identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources).
A szolgáltatások listájának lekéréséhez, hogy MSI használható hitelesítésre, tekintse meg [Azure-szolgáltatások, amelyek támogatják az Azure Active Directory-hitelesítéssel](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás egy virtuálisgép-méretezési csoportot, vagy egy meglévő virtuális gépek méretezési létrehozása során, a következő deklarálja `"Microsoft.Compute/virtualMachinesScaleSets"` tulajdonság:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Lásd: [Mi az Azure-erőforrások felügyelt identitások?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity) további információt.

Ha létrehozott egy [felhasználó által hozzárendelt felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity), a sablonban, a virtuálisgép-méretezési csoporthoz rendelje hozzá a következő erőforrás deklarálható. Cserélje le `\<USERASSIGNEDIDENTITYNAME\>` a felhasználó által hozzárendelt nevű felügyelt identitás hozott létre:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Mielőtt a Service Fabric application győződjön meg arról is, használja egy felügyelt identitás, engedélyt kell biztosítani az Azure-erőforrások hitelesíteni kell.
Az alábbi parancsokat a hozzáférést az Azure-erőforrás:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

A Service Fabric-alkalmazás kódba hozzáférési jogkivonat beszerzésére az Azure Resource Manager azáltal, hogy a többi összes hasonló a következőhöz:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

A Service Fabric-alkalmazás a hozzáférési jogkivonatot az Azure-erőforrások, amelyek támogatják az Active Directory hitelesítést használhatja.
Az alábbi példa bemutatja, hogyan teheti ezt a Cosmos DB-erőforráshoz:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```

## <a name="windows-defender"></a>Windows Defender 

Alapértelmezés szerint a Windows Defender víruskereső Windows Server 2016 telepítve van. További információkért lásd: [Windows Defender víruskereső Windows Server 2016 rendszeren](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). A felhasználói felület néhány termékváltozat alapértelmezés szerint telepítve van, de nem kötelező. Semmilyen teljesítménycsökkenést hatás és erőforrás-felhasználás többletterhelést felmerült a Windows Defender, és ha a biztonsági házirendek lehetővé teszik, hogy a folyamatok és a nyílt forráskódú szoftverek esetén elérési utak kizárása a következő virtuális gép méretezési bővítmény erőforrás deklarálja A Service Fabric-fürt kizárását vizsgálatok sablontulajdonságok Manager:


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
> Tekintse meg a kártevőirtó dokumentációjában a konfigurációs szabályok, ha nem használja a Windows Defender. A Windows Defender Linux rendszeren nem támogatott.

## <a name="next-steps"></a>További lépések

* Fürt létrehozása a virtuális gépet, vagy Windows Server rendszerű számítógépek: [A Service Fabric-fürt létrehozása a Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Virtuális gépek és a számítógépeken, Linux rendszerű fürt létrehozása: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md).
* Ismerje meg [Service Fabric támogatási lehetőségeinek](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png