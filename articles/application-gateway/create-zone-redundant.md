---
title: Hozzon létre egy zóna redundáns Azure-alkalmazásokban
description: Útmutató a zóna redundáns Alkalmazásátjáró, amely nem igényel minden egyes zónában seperarte átjáró létrehozásához.
services: application-gateway
author: amsriva
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 5/8/2018
ms.author: victorh
ms.openlocfilehash: 03bc58db813534fdd17c9567f6425ab7357ed901
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="create-a-zone-redundant-azure-application-gateway---private-preview"></a>Hozzon létre egy zóna redundáns Azure application gateway - private Preview verziójára

Az alkalmazás átjáró zóna redundáns platform egy új SKU, amely számos fejlesztéssel bővült nyújt, mint a meglévő alkalmazás gateway SKU többek között:
- **Zóna rugalmassági** - átjáró alkalmazástelepítések most is kiterjedhetnek több rendelkezésre állási zóna, szükség lehet kiépíteni, és a PIN-kód külön Alkalmazásátjáró minden egyes zónában a traffic Manager-példányok. Választhat egy zóna vagy több zóna ahol átjáró alkalmazáspéldányok van telepítve, így biztosítja zóna hiba rugalmasság. A háttérkészlet alkalmazásokhoz hasonlóan terjeszthető keresztül a rendelkezésre állási zónák.
- **A teljesítményt javító**
- **Statikus VIP** -az Alkalmazásátjáró VIP most támogatja azt a statikus VIP önmagában. Ez biztosítja, hogy a VIP-címhez társított Alkalmazásátjáró nem változtatja meg újraindítás után.
- **Az ügyfél SSL-tanúsítványok Key vault-integráció**
- **Központi telepítés és frissítés gyorsabb**

> [!NOTE]
> A zóna redundáns Alkalmazásátjáró jelenleg magán előnézetben. Ez az előzetes kiadás egy szolgáltatásiszint-megállapodás nélkül van megadva, és nem ajánlott a termelési számítási feladatokhoz. Bizonyos funkciók nem támogatott, vagy előfordulhat, hogy rendelkezik korlátozott képességeket. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Támogatott régiók

Zóna redundáns alkalmazásátjárót jelenleg támogatott régióban USA keleti régiója 2. További régiókban elérhető lesz hozzáadva.

## <a name="topologies"></a>Topológiák

Az aktuális kiadással már nincs szüksége rögzített zóna alkalmazásátjárót zonal redundancia beolvasandó létrehozásához. Az azonos átjáró alkalmazástelepítés most is kiterjedhetnek több zóna.

Legalább három alkalmazáspéldányra szükség, annak érdekében, hogy vannak elosztva három zónák mindegyikében. Alkalmazásátjáró elosztja példányok között a zónák további példányt adja hozzá.

Az alábbi ábrán például előző zóna redundáns topológiák keresést végrehajtani:

![Régi redundáns topológia](media/create-zone-redundant/old-redundant.png)

Az új zóna redundáns topológia ezen a diagramon néz ki:

![Új zóna redundáns topológia](media/create-zone-redundant/new-redundant.png)

## <a name="deployment"></a>Környezet

### <a name="prerequisites"></a>Előfeltételek

Zóna redundáns funkció jelenleg magán előnézetben csak érhető el. Kell appgwxzone@microsoft.com kell szerepel az engedélyezési listán. Miután a megerősítő üzenet, folytathatja a következő lépéseket. A következő információk belefoglalása az engedélyezett e-mail:

- Előfizetés azonosítója
- Régió neve
- Szükséges alkalmazás átjárók hozzávetőleges száma

### <a name="resource-manager-template-deployment"></a>Erőforrás-kezelő sablon-üzembehelyezés

1. Ellenőrizze, hogy használja az előfizetés korábban már említett szerepel az engedélyezési listán.
2. Egy megerősítő üzenet megérkezése után jelentkezzen be az Azure-fiókjába, és válassza ki a megfelelő előfizetést, ha egynél több előfizetésnek található. Gondoskodjon róla, hogy az előfizetés, de a szerepel az engedélyezési listán.

   ```PowerShell
   Login-AzureRmAccount

   Select-AzureRmSubscription -Subscription "<whitelisted subscription name>”
   ```
3. Új erőforráscsoport létrehozása

   ``` PowerShell
   New-AzureRmResourceGroup -Name <resource group name> -Location "East US 2"
   ```
4. A sablonok letöltésére [GitHub](https://github.com/amitsriva/CrossZonePreview) meg és jegyezze fel a mappát, ahol mentse őket.
5. Hozzon létre egy új központi telepítési létrehozott erőforráscsoportot. Annak megfelelően módosítsa a sablon és a paraméterek fájljához, központi telepítése előtt. 

   Az alábbi ábrán látható, hogy hol kérheti le a bérlő azonosítója az Azure portálon:

   ![A portálról bérlő azonosítója](media/create-zone-redundant/tenant-id.png)

A sablont hoz létre a következőket:

- **Felhasználóazonosító hozzárendelése** -átjáró alkalmazáspéldányok kulcstároló eléréséhez, és a felhasználó által tárolt tanúsítványok beolvasása engedélyezése szolgál.
- **KeyVault** – a kulcstároló, ahol a felhasználói tanúsítvány található. Ez akkor lehet, valamint a már meglévő kulcstároló.
- **Titkos kulcs** – a titkos kulcsot, amely a Key Vault tárolódik.
- **Hozzáférés-házirend** – egy hozzáférési házirendben a kulcstároló, engedélyt biztosít a felhasználó hozzárendelt azonosítójának használatával, hogy az átjáró alkalmazástelepítések kérheti le a felhasználói tanúsítványok alkalmazni.
- **Nyilvános IP-cím** – a szolgáltatás számára fenntartott IP-címet, amely az Alkalmazásátjáró elérésére szolgál. Az Alkalmazásátjáró életciklusát soha nem változik az IP-címet.
- **Hálózati biztonsági csoportok** – az automatikusan létrehozott megnyitja a beállított figyelő a forgalom átjáró-alhálózatot a NSG. Ez explicit módon létrehozott és a korábbi Termékváltozat, ahol az NSG volt implicit képest új SKU kezelni.
- **Virtuális hálózati** – a virtuális hálózat, amelyen üzembe van helyezve az Alkalmazásátjáró és alkalmazásokat.
- **Alkalmazásátjáró** – Alkalmazásátjáró hoz létre a szükséges zónákat példánya. Alapértelmezés szerint minden zóna (1,2,3) van kiválasztva. A termékváltozat változtatják *Standard_v2*. A Termékváltozat név változhat van. Jelenleg az automatikus skálázási konfiguráció rendelkezik, a minimális és maximális értéke a szükséges példányok száma. Amennyiben az automatikus skálázást engedélyezve van, ezt úgy kell beállítani.

```PowerShell
New-AzureRmResourceGroupDeployment -Name Deployment1 -ResourceGroupName AmitVMSSLinuxTest9 -TemplateFile <complete path to template.json> -TemplateParameterFile <complete path to parameters.json>
```
### <a name="powershell-deployment"></a>PowerShell telepítése

1. Győződjön meg arról, hogy a használt előfizetés szerepel az engedélyezési listán az Előfeltételek korábban már említett.
2. Töltse le és telepítse a saját PowerShell MSI a [GitHub](https://github.com/amitsriva/CrossZonePreview/blob/master/Azure-Cmdlets-5.7.0.19009-x64.msi).
3. A titkos PowerShell zip-fájl letöltése előzetes regisztráció visszaigazoló e-mailben említett helyéről. Bontsa ki a meghajtót a fájlt, és jegyezze fel a helyre.
4. Az előzetes engedélyezése után betölteni az előzetes modulok először előtt meg bejelentkezni fiókjába:

   ```PowerShell
   $azurePSPath = "<complete path to Azure-PowerShell folder>"
   import-module "$azurePSPath\AzureRM.Profile\AzureRM.Profile.psd1"
   import-module "$azurePSPath\Azure.Storage\Azure.Storage.psd1"
   import-module "$azurePSPath\AzureRM.Resources\AzureRM.Resources.psd1"
   import-module "$azurePSPath\AzureRM.Network\AzureRM.Network.psd1"
   import-module "$azurePSPath\AzureRM.KeyVault\AzureRM.KeyVault.psd1"
   ```

4. Jelentkezzen be az Azure-fiókjába, és válassza ki a kívánt előfizetést, ha egynél több előfizetésnek. Győződjön meg arról, akkor válassza ki, hogy a megfelelő előfizetést, de a szerepel az engedélyezési listán.
5. A következő parancsokat, amelyek tartalmazzák a legtöbb létrehozott entitások nevek közös állandók létrehozásához. 

   A bejegyzések pedig ez szükséges lenne elnevezési igény szerint módosítsa.

   ```PowerShell
   $location = "eastus2"
   $version = "300"

   $rgname = "RG_A_$version"
   $appgwName = "AGW_A_$version"
   $vaultName = "KVA$version"
   $userAssignedIdentityName = "UI_A_$version"
   $certificateName = "KVCA$version"
   $nsgName = "NSG_A_$version"
   $vnetName = "VN_A_$version"
   $gwSubnetName = "SN_A_$version"
   $gipconfigname = "GC_A_$version"
   $publicIpName = "PIP_A_$version"
   $fipconfig01Name = "FC_A_$version"
   $poolName = "BP_A_$version"
   $frontendPort01Name = "FP1_A_$version"
   $frontendPort02Name = "FP2_A_$version"
   $poolSetting01Name = "BS_A_$version"
   $listener01Name = "HL1_A_$version"
   $listener02Name = "HL2_A_$version"
   $rule01Name = "RR1_A_$version"
   $rule02Name = "RR2_A_$version"
   $AddressPrefix = "111.111.222.0" 
   ```
6. Hozza létre az erőforráscsoportot:

   ```PowerShell
   $resourceGroup = New-AzureRmResourceGroup -Name $rgname -Location $location -Force
   ```
7. Nem hoz létre a felhasználó hozzárendelt való hozzáférésre az Alkalmazásátjáró használt tanúsítványok lekérése a Key Vault.

   ```PowerShell
   $userAssignedIdentity = New-AzureRmResource -ResourceGroupName $rgname -Location $location -ResourceName $userAssignedIdentityName -ResourceType Microsoft.ManagedIdentity/userAssignedIdentities -Force
   ```
8. Hozza létre a Key Vault fogja tárolni a tanúsítványokat:

   ```PowerShell
   $keyVault = New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgname -Location $location -EnableSoftDelete
   ```
9. A Key Vault-tanúsítvány feltöltése, a titkos kulcs:

   ```PowerShell
   $securepfxpwd = ConvertTo-SecureString -String "<password>" -AsPlainText -Force

   $cert = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name         $certificateName -FilePath ‘<path to pfx file>'  -Password $securepfxpwd
   ```
10. Hozzáférési házirend hozzárendelése a Key Vault a felhasználó hozzárendelt identitásával. Ez lehetővé teszi a kulcstároló eléréséhez alkalmazáspéldányok átjáró titkos:

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $rgname -PermissionsToSecrets get -ObjectId $userAssignedIdentity.Properties.principalId
   ```
11. Hozzon létre a hálózati biztonsági csoport (NSG) hogy férjen hozzá a portokat, ahol új figyelői jönnek létre alkalmazást átjáró-alhálózatot.

    Például HTTP/HTTPS az alapértelmezett portok az NSG lehetővé tenné befelé irányuló 80-as és 443 és 65200-65535 felügyeleti műveleteihez.

   ```PowerShell
   $srule01 = New-AzureRmNetworkSecurityRuleConfig -Name "listeners" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange 22,80,443 -Access Allow -Priority 100

   $srule02 = New-AzureRmNetworkSecurityRuleConfig -Name "managementPorts" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange "65200-65535" -Access Allow -Priority 101

   $nsg = New-AzureRmNetworkSecurityGroup -Name $nsgName -ResourceGroupName $rgname -Location $location -SecurityRules $srule01,$srule02 -Force
   ```

12. Hálózatok és alhálózatok létrehozásával:

   ```PowerShell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 
   $gwSubnetName -AddressPrefix "$AddressPrefix/24" -NetworkSecurityGroup $nsg

   $vnet = New-AzureRmvirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix "$AddressPrefix/24" -Subnet $gwSubnet -Force
   ```
13. Hozzon létre egy fenntartott típus/statikus nyilvános IP-címe:

   ```PowerShell
   $publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -Sku Standard -Force
   ```

14. Az Alkalmazásátjáró létrehozása:

   ```PowerShell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet

   $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name $fipconfig01Name -PublicIPAddress $publicip

   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

   $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort01Name -Port 443

   $fp02 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort02Name -Port 80

   $sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -KeyVaultSecretId $secret.Id

   $listener01 = New-AzureRmApplicationGatewayHttpListener -Name $listener01Name -Protocol Https -FrontendIPConfiguration
 $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01

   $listener02 = New-AzureRmApplicationGatewayHttpListener -Name $listener02Name -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02

   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name $poolSetting01Name -Port 80 -Protocol Http -CookieBasedAffinity Disabled

   $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule01Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

   $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule02Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool

   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2

   $listeners = @($listener02)

   $fps = @($fp01, $fp02)

   $fipconfigs = @($fipconfig01)

   $sslCerts = @($sslCert01)

   $rules = @($rule01, $rule02)

   $listeners = @($listener01, $listener02)

   $appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -UserAssignedIdentityId $userAssignedIdentity.ResourceId -Probes $probeHttps -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfigs -FrontendPorts $fps -HttpListeners $listeners -RequestRoutingRules $rules -Sku $sku -SslPolicy $sslPolicy -sslCertificates $sslCerts -Force
   ```

15. A létrehozott alkalmazás átjáró nyilvános IP-cím beolvasása:

   ```PowerShell
   $pip = Get-AzureRmPublicIpAddress -Name $publicIpName -ResourceGroupName $rgname $pip.IpAddress
   ```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

-  I alapján számlázzuk az Alkalmazásátjáró Preview?

   A minta során a rendszer nem kell fizetni. Lesz számlázva erőforrások Alkalmazásátjáró, például a Key Vault eltérő virtuális gépek stb.
- Milyen régiók érhető el az előzetes?

   Az előzetes kiadásban érhető el jelenleg régióban USA keleti régiója 2. További régiókban elérhető lesz hozzáadva.
- A portál az előzetes támogatott?

   Nem, korlátozódik titkos PowerShell modul, ezért a Resource Manager-sablon során a private Preview verziójára.

- Termelési alkalmazások és szolgáltatások saját előzetes támogatva van?

   Nem, nincs SLA-t vagy támogatási során a private Preview verziójára. Nem ajánlott, amelyre a termelési számítási feladatokhoz előzetes során. Az e-mail aliast használni az előzetes termékcsoport közvetlen interakció támogatása korlátozódik.

- Hogyan jelenthetem a problémát?

   A private Preview verziójára hibákat is tartalmazhat, és lehet gyakori kód központi telepítéseket. Használja a támogatási aliast appgwxzone@microsoft.com problémák és támogatás jelentéskészítéshez.

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások


|Probléma  |Részletek  |
|---------|---------|---------|
|Számlázás     |Jelenleg nincs a számlázási|
|Diagnosztikai naplók (nem metrikák)     |Teljesítmény- és kérelem/válasz naplói nem jelenik meg jelenleg|
|Portál/CLI/SDK     |Portál, a parancssori felületen vagy az SDK nem támogatott. A portál ki a frissítések előzetes átjárókat nem használható.|
|Alkalmanként sikertelen frissítés sablon segítségével     |Ennek az az oka versenyhelyzet KeyVault hozzáférési házirenddel|A Key Vault és felhasználói hozzárendelt azonosító létrehozása után el kell távolítani a sablonból, és csak a titkos kulcs és identitás mutató hivatkozások szükségesek a sablonban.|
|Automatikus skálázás     |Automatikus skálázás jelenleg nem támogatott|
|WAF     |WAF jelenleg nem támogatott|
|Felhasználó megadott, tanúsítványok és a dinamikus virtuális IP-címek     |Az új modell ezek nem támogatottak. Tanúsítványok és a statikus virtuális IP-címek tárolására használt kulcstároló.|
|Ugyanazon az alhálózaton a régi és az Alkalmazásátjáró előzetes verzióját     |Meglévő Alkalmazásátjáró (régi modell) alhálózat nem használható privát előzetes verzióját.|
|A HTTP/2, FIPS mód, WebSocket, Azure Web Apps néven foghatók össze     |Jelenleg nem támogatott |


## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Támogatás és visszajelzés, forduljon a appgwxzone@microsoft.com. Az átjáró termék alkalmazáscsoport örömmel fejlesztések a visszajelzést, és útmutatást nyújtanak szükség.

## <a name="next-steps"></a>További lépések

Más alkalmazás átjáró szolgáltatásainak megismerése:

- [Mi az Azure Application Gateway?](overview.md)