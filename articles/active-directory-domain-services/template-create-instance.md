---
title: Az Azure DS tartományi szolgáltatások engedélyezése sablon használatával | Microsoft dokumentumok
description: Megtudhatja, hogy miként konfigurálható és engedélyező az Azure Active Directory tartományi szolgáltatások egy Azure Resource Manager-sablon használatával
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: iainfou
ms.openlocfilehash: 2daadb539bc08df37f15c187866b735e45309288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612796"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Azure Active Directory tartományi szolgáltatások által kezelt tartomány létrehozása Azure Resource Manager-sablon használatával

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM-hitelesítés, amelyek teljes mértékben kompatibilisek a Windows Server Active Directoryval. Ezeket a tartományi szolgáltatásokat a tartományvezérlők telepítése, kezelése és javítása nélkül használhatja fel. Az Azure AD DS integrálható a meglévő Azure AD-bérlővel. Ez az integráció lehetővé teszi a felhasználók számára, hogy a vállalati hitelesítő adataikkal jelentkezzenek be, és a meglévő csoportok és felhasználói fiókok segítségével biztosíthatja az erőforrásokhoz való hozzáférést.

Ez a cikk bemutatja, hogyan engedélyezheti az Azure AD DS-t egy Azure Resource Manager-sablon használatával. A támogató erőforrások az Azure PowerShell használatával jönnek létre.

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához a következő erőforrásokra van szükség:

* Az Azure PowerShell telepítése és konfigurálása.
    * Szükség esetén kövesse az utasításokat [az Azure PowerShell-modul telepítéséhez és az Azure-előfizetéshez való csatlakozáshoz.](/powershell/azure/install-az-ps)
    * Győződjön meg arról, hogy a [Connect-AzAccount][Connect-AzAccount] parancsmag használatával jelentkezik be az Azure-előfizetésbe.
* Telepítse és konfigurálja az Azure AD PowerShellt.
    * Szükség esetén kövesse az utasításokat [az Azure AD PowerShell-modul telepítéséhez és az Azure AD-hez való csatlakozáshoz.](/powershell/azure/active-directory/install-adv2)
    * Győződjön meg arról, hogy a [Connect-AzureAD-parancsmag][Connect-AzureAD] használatával bejelentkezik az Azure AD-bérlőbe.
* Az Azure AD DS engedélyezéséhez *globális rendszergazdai* jogosultságokra van szüksége az Azure AD-bérlőben.
* A szükséges Azure AD DS-erőforrások létrehozásához *közreműködői* jogosultságokra van szüksége az Azure-előfizetésben.

## <a name="dns-naming-requirements"></a>DNS-elnevezési követelmények

Amikor létrehoz egy Azure AD DS-példányt, meg kell adnia egy DNS-nevet. A DNS-név kiválasztásakor néhány szempontot figyelembe kell venni:

* **Beépített tartománynév:** Alapértelmezés szerint a rendszer a könyvtár beépített tartománynevét használja (egy *.onmicrosoft.com* utótagot). Ha biztonságos LDAP-hozzáférést szeretne engedélyezni a felügyelt tartományhoz az interneten keresztül, nem hozhat létre digitális tanúsítványt az alapértelmezett tartománnyal való kapcsolat védelmére. A Microsoft rendelkezik az *.onmicrosoft.com* tartománnyal, így a hitelesítésszolgáltató (CA) nem állít ki tanúsítványt.
* **Egyéni tartománynevek:** A leggyakoribb módszer egy egyéni tartománynév megadása, amely általában már a tulajdonában van, és irányítható. Ha egy irányítható, egyéni tartományt használ, a forgalom megfelelően tud folyni az alkalmazások támogatásához.
* **Nem irányítható tartományutótagok:** Általában azt javasoljuk, hogy kerülje a nem irányítható tartománynév-utótagot, például *a contoso.local*nevet. A *.local* utótag nem irányítható, és problémákat okozhat a DNS-feloldással kapcsolatban.

> [!TIP]
> Ha egyéni tartománynevet hoz létre, a meglévő DNS-névtereket is gondot kell fordítania. Javasoljuk, hogy a meglévő Azure-tól vagy helyszíni DNS-névterülettől elkülönülő tartománynevet használjon.
>
> Ha például rendelkezik egy meglévő DNS-névtérrel *contoso.com,* hozzon létre egy Azure AD DS által felügyelt tartományt *a aaddscontoso.com*egyéni tartománynevével. Ha biztonságos LDAP-t kell használnia, regisztrálnia kell és birtokolnia kell ezt az egyéni tartománynevet a szükséges tanúsítványok létrehozásához.
>
> Előfordulhat, hogy további DNS-rekordokat kell létrehoznia a környezet más szolgáltatásaihoz, vagy feltételes DNS-továbbítókat a környezetben meglévő DNS-névterek között. Ha például olyan webkiszolgálót futtat, amely a gyökér DNS-nevet használó webhelyet üzemeltet, előfordulhatnak olyan elnevezési ütközések, amelyek további DNS-bejegyzéseket igényelnek.
>
> Ezekben az oktatóanyagokban és útmutatócikkekben *a aaddscontoso.com* egyéni tartománya rövid példaként szolgál. Minden parancsban adja meg a saját tartománynevét.

A következő DNS-névkorlátozások is érvényesek:

* **Tartományi előtagkorlátozások:** 15 karakternél hosszabb előtaggal nem hozhat létre felügyelt tartományt. A megadott tartománynév előtagnak (például a *aaddscontoso.com* tartománynévben az *aaddscontoso)* 15 vagy kevesebb karaktert kell tartalmaznia.
* **Hálózati névütközések:** A felügyelt tartomány DNS-tartományneve nem létezhet már a virtuális hálózatban. Pontosabban ellenőrizze a következő eseteket, amelyek névütközéshez vezetnek:
    * Ha már rendelkezik ugyanazzal a DNS-tartománynévvel rendelkező Active Directory-tartománnyal az Azure virtuális hálózatán.
    * Ha a felügyelt tartományt engedélyező virtuális hálózat VPN-kapcsolattal rendelkezik a helyszíni hálózattal. Ebben az esetben győződjön meg arról, hogy nincs olyan tartománya, amelynek a helyszíni hálózatán ugyanaz a DNS-tartománynév van.
    * Ha rendelkezik egy ezzel a névvel rendelkező azure-felhőszolgáltatással az Azure virtuális hálózatán.

## <a name="create-required-azure-ad-resources"></a>A szükséges Azure AD-erőforrások létrehozása

Az Azure AD DS használatához egyszerű szolgáltatásra és egy Azure AD-csoportra van szükség. Ezek az erőforrások lehetővé teszik, hogy az Azure AD DS felügyelt tartomány adatokat szinkronizáljon, és meghatározza, hogy mely felhasználók rendelkeznek rendszergazdai engedélyekkel a felügyelt tartományban.

Először regisztrálja az Azure AD tartományi szolgáltatások erőforrás-szolgáltatót a [Register-AzResourceProvider][Register-AzResourceProvider] parancsmag használatával:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Hozzon létre egy Azure AD szolgáltatásnév az Azure AD DS [Új-AzureADServicePrincipal][New-AzureADServicePrincipal] parancsmagjával, amely kommunikálhat és hitelesítheti magát. Egy adott alkalmazásazonosítót használ nevű *tartományvezérlő szolgáltatások* azonosítója *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Ne módosítsa az alkalmazásazonosítót.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Most hozzon létre egy *AAD DC rendszergazdák* nevű Azure AD-csoportot a [New-AzureADGroup][New-AzureADGroup] parancsmag használatával. A csoporthoz hozzáadott felhasználók ezután engedélyt kapnak felügyeleti feladatok végrehajtására az Azure AD DS felügyelt tartományban.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Az *AAD DC rendszergazdák* csoport létrehozása, hozzá egy felhasználót a csoporthoz az [Add-AzureADGroupMember][Add-AzureADGroupMember] parancsmag használatával. Először az *AAD DC rendszergazdák* csoportobjektum-azonosítót a [Get-AzureADGroup][Get-AzureADGroup] parancsmag használatával kapja meg, majd a kívánt felhasználói objektumazonosítót a [Get-AzureADUser][Get-AzureADUser] parancsmag használatával.

A következő példában a felhasználói objektum azonosítója a `admin@aaddscontoso.onmicrosoft.com`fiók hoz egy. Cserélje le ezt a felhasználói fiókot annak a felhasználónak az upn-jára, akit hozzá szeretne adni az *AAD DC Rendszergazdák* csoporthoz:

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Végül hozzon létre egy erőforráscsoportot a [New-AzResourceGroup][New-AzResourceGroup] parancsmag használatával. A következő példában az erőforráscsoport neve *myResourceGroup,* és a *westus* régióban jön létre. Használja a saját nevét és a kívánt régiót:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Ha olyan régiót választ, amely támogatja a rendelkezésre állási zónákat, az Azure AD DS-erőforrások zónák között vannak elosztva a további redundancia érdekében. A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében legalább három különálló zóna van az összes engedélyezett régióban.

Nincs mit beállítani az Azure AD DS zónák között elosztott. Az Azure platform automatikusan kezeli az erőforrások zónaelosztását. További információért és a régió elérhetőségéről a [Mik azok a rendelkezésre állási zónák az Azure-ban?][availability-zones]

## <a name="resource-definition-for-azure-ad-ds"></a>Az Azure AD DS erőforrás-definíciója

Az Erőforrás-kezelő erőforrás-definíciója részeként a következő konfigurációs paraméterek szükségesek:

| Paraméter               | Érték |
|-------------------------|---------|
| Tartománynév              | A felügyelt tartomány DNS-tartományneve, figyelembe véve az előtagok és ütközések elnevezésével kapcsolatos korábbi pontokat. |
| filteredSync            | Az Azure AD DS lehetővé teszi az Azure AD-ben elérhető *összes* felhasználó és csoport szinkronizálását, vagy csak adott csoportok *hatókörrel való* szinkronizálását. Ha úgy dönt, hogy szinkronizálja az összes felhasználót és csoportot, később nem választhatja úgy, hogy csak hatókör-szinkronizálást hajt végre.<br /> A hatókörrel szinkronizált szinkronizálásról az [Azure AD tartományszolgáltatások hatóköre szinkronizáláscímű][scoped-sync]témakörben talál további információt.|
| értesítésBeállítások    | Ha az Azure AD DS felügyelt tartományában bármilyen riasztás jön létre, e-mail-értesítésekküldhetők. <br />Az Azure-bérlő *globális rendszergazdái* és az *AAD DC rendszergazdák* csoport tagjai *engedélyezhetők* ezekhez az értesítésekhez.<br /> Ha szükséges, további címzetteket is hozzáadhat az értesítésekhez, ha figyelemfelkeltő riasztások vannak.|
| tartományKonfigurációtípusa | Alapértelmezés szerint egy Azure AD DS felügyelt tartomány *jön létre,* mint egy felhasználói erdő. Ez az erdőtípus szinkronizálja az Azure AD összes objektumát, beleértve a helyszíni Activeád-ds környezetben létrehozott felhasználói fiókokat is. Felhasználói erdő létrehozásához nem kell *megadnia a tartománykonfigurációs* értékét.<br /> Az *erőforráserdő* csak az Azure AD-ben közvetlenül létrehozott felhasználókat és csoportokat szinkronizálja. Az erőforráserdők jelenleg előzetes verzióban jelennek meg. Erőforráserdő létrehozásához állítsa az értéket *ResourceTrusting* értékre.<br />Az *erőforráserdőkkel* kapcsolatos további információkért, például arról, hogy miért használhat egyet, és hogyan hozhat létre erdőszintű bizalmi kapcsolatokat a helyszíni AD DS-tartományokkal, olvassa el az [Azure AD DS-erőforráserdők áttekintését.][resource-forests]|

A következő tömörített paraméterek definíciója bemutatja, hogyan deklarálják ezeket az értékeket. A *aaddscontoso.com* nevű felhasználói erdő az Azure AD felügyelt tartományával szinkronizált összes Azure AD-felhasználóval jön létre:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

A következő tömörített Resource Manager sablon erőforrás-típus ezután az Azure AD DS felügyelt tartomány definiálására és létrehozására szolgál. Az Azure virtuális hálózatnak és alhálózatnak már léteznie kell, vagy létre kell hozni a Resource Manager-sablon részeként. Az Azure AD DS felügyelt tartomány csatlakozik ehhez az alhálózathoz.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Ezek a paraméterek és az erőforrás-típus egy szélesebb Erőforrás-kezelő sablon részeként használható felügyelt tartomány üzembe helyezéséhez, ahogy az a következő szakaszban látható.

## <a name="create-a-managed-domain-using-sample-template"></a>Felügyelt tartomány létrehozása mintasablon nal

A következő teljes Erőforrás-kezelő mintasablon létrehoz egy Azure AD DS felügyelt tartományt és a támogató virtuális hálózati, alhálózati és hálózati biztonsági csoport szabályokat. A hálózati biztonsági csoport szabályai szükségesek a felügyelt tartomány biztonságossá tétele érdekében, és győződjön meg arról, hogy a forgalom megfelelően tud folyni. Létrejön egy *aaddscontoso.com* DNS-nevével rendelkező felhasználói erdő, amelyen az összes felhasználó szinkronizálva van az Azure AD-ből:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Ez a sablon az előnyben részesített központi telepítési módszerrel telepíthető, például az [Azure Portal,][portal-deploy]az [Azure PowerShell][powershell-deploy]vagy a CI/CD-folyamat használatával. A következő példa a [New-AzResourceGroupDeployment][New-AzResourceGroupDeployment] parancsmag. Adja meg a saját erőforráscsoport nevét és a sablonfájlnevét:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Néhány percet vesz igénybe az erőforrás létrehozása, és adja vissza a vezérlést a PowerShell-parancssorba. Az Azure AD DS felügyelt tartomány továbbra is kivan építve a háttérben, és akár egy órát is igénybe vehet a központi telepítés befejezéséhez. Az Azure Portalon az Azure AD DS felügyelt tartomány **áttekintése** lap mutatja az aktuális állapotot ebben a telepítési szakaszban.

Ha az Azure Portal azt mutatja, hogy az Azure AD DS felügyelt tartomány a kiépítés befejeződött, a következő feladatokat kell végrehajtani:

* Frissítse a virtuális hálózat DNS-beállításait, hogy a virtuális gépek megtalálják a tartományhoz való csatlakozáshoz vagy hitelesítéshez szükséges felügyelt tartományt.
    * A DNS konfigurálásához válassza ki az Azure AD DS felügyelt tartományát a portálon. Az **Áttekintés ablakban** a rendszer kéri, hogy automatikusan konfigurálja ezeket a DNS-beállításokat.
* [Engedélyezze a jelszó-szinkronizálást az Azure AD tartományi szolgáltatásokkal,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) hogy a végfelhasználók vállalati hitelesítő adataikkal bejelentkezhessenek a felügyelt tartományba.

## <a name="next-steps"></a>További lépések

Az Azure AD DS felügyelt tartományának működés közbeni megtekintéséhez [tartományban csatlakozhat egy Windows virtuális géphez,][windows-join] [konfigurálhatja a biztonságos LDAP-t][tutorial-ldaps], és beállíthatja a [jelszókivonat-szinkronizálást.][tutorial-phs]

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment
