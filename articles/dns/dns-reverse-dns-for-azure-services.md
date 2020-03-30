---
title: Dns-fordítás azure-szolgáltatásokhoz – Azure DNS
description: Ezzel a tanulási útvonallal elkezdheti konfigurálni az Azure-ban üzemeltetett szolgáltatások névfeloldási DNS-kereséseit.
services: dns
documentationcenter: na
author: rohinkoul
manager: KumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 073e84ece11f6817bfe2c5a94735ec6e16dac4fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932375"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Fordított DNS konfigurálása az Azure-ban üzemeltetett szolgáltatásokhoz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja a névfeloldási DNS-keresések az Azure-ban üzemeltetett szolgáltatások.

Az Azure-beli szolgáltatások az Azure által hozzárendelt és a Microsoft tulajdonában lévő IP-címeket használják. Ezeket a fordított DNS-rekordokat (PTR-rekordokat) a Microsoft tulajdonában lévő névfeloldási DNS-keresési zónákban kell létrehozni. Ez a cikk bemutatja, hogyan kell ezt csinálni.

Ez a forgatókönyv nem tévesztendő össze azzal a képességgel, hogy [az Azure DNS-ben a hozzárendelt IP-tartományok névkapcsolati zónáit üzemeltetje.](dns-reverse-dns-hosting.md) Ebben az esetben a névkeresési zónával jelölt IP-tartományokat hozzá kell rendelni a szervezethez, általában az isp-hez.

Mielőtt elolvasná ezt a cikket, ismernie kell ezt [az áttekintést a fordított DNS és támogatás az Azure-ban.](dns-reverse-dns-overview.md)

Az Azure DNS-ben a számítási erőforrások (például a virtuális gépek, a virtuálisgép-méretezési csoportok vagy a Service Fabric-fürtök) egy PublicIpAddress erőforráson keresztül vannak elérhetővé téve. A névfeloldási DNS-keresések a PublicIpAddress "ReverseFqdn" tulajdonságával vannak konfigurálva.


A fordított DNS jelenleg nem támogatott az Azure App Service számára.

## <a name="validation-of-reverse-dns-records"></a>Fordított DNS-rekordok érvényesítése

Egy harmadik fél nem hozhat létre fordított DNS-rekordokat az Azure-szolgáltatás-hozzárendeléshez a DNS-tartományokhoz. Ennek megakadályozása érdekében az Azure csak akkor engedélyezi egy fordított DNS-rekord létrehozását, ahol a fordított DNS-rekordban megadott tartománynév megegyezik egy PublicIpAddress vagy Cloud Service DNS-nevével vagy IP-címével ugyanabban az Azure-előfizetésben.

Ez az ellenőrzés csak akkor történik meg, ha a fordított DNS-rekord be van állítva vagy módosul. A rendszeres újraellenőrzés nem történik meg.

Tegyük fel például, hogy a PublicIpAddress erőforrás dns-neve contosoapp1.northus.cloudapp.azure.com és IP-címe: 23.96.52.53. A PublicIpAddress ReverseFqdn tulajdonsága a következőképpen adható meg:
* A PublicIpAddress DNS-neve, contosoapp1.northus.cloudapp.azure.com
* Egy másik PublicIpAddress DNS-neve ugyanabban az előfizetésben, például contosoapp2.westus.cloudapp.azure.com
* A hiúság DNS-név, például app1.contoso.com, feltéve, hogy ez a név *először* cname-ként van konfigurálva contosoapp1.northus.cloudapp.azure.com, vagy egy másik PublicIpAddress címhez ugyanabban az előfizetésben.
* A hiúság DNS-név, például app1.contoso.com, feltéve, hogy ez a név *először* a 23.96.52.53 IP-címre vagy egy másik PublicIpAddress IP-cím IP-címére van konfigurálva ugyanabban az előfizetésben.

Ugyanezek a korlátozások vonatkoznak a felhőszolgáltatások fordított DNS-ére is.


## <a name="reverse-dns-for-publicipaddress-resources"></a>A PUBLICIpAddress erőforrások DNS-ének sztornírozása

Ez a szakasz részletes útmutatást nyújt a fordított DNS-hez a PublicIpAddress erőforrások hoz konfigurálásához az Erőforrás-kezelő telepítési modelljében, az Azure PowerShell, az Azure classic CLI vagy az Azure CLI használatával. A fordított DNS konfigurálása a PublicIpAddress erőforrásokhoz jelenleg nem támogatott az Azure Portalon keresztül.

Az Azure jelenleg csak az IPv4 PublicIpAddress erőforrások fordított DNS-ét támogatja. Az IPv6 nem támogatja.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Fordított DNS hozzáadása meglévő Nyilvános ipcímekhez

#### <a name="powershell"></a>PowerShell

A fordított DNS frissítése meglévő PublicIpAddress címre:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Ha névfeloldási DNS-t szeretne hozzáadni egy olyan meglévő PublicIpAddress címhez, amelynek még nincs DNS-neve, meg kell adnia egy DNS-nevet is:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

Fordított DNS hozzáadása meglévő PublicIpAddress címhez:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Ha névfeloldási DNS-t szeretne hozzáadni egy olyan meglévő PublicIpAddress címhez, amelynek még nincs DNS-neve, meg kell adnia egy DNS-nevet is:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

Fordított DNS hozzáadása meglévő PublicIpAddress címhez:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Ha névfeloldási DNS-t szeretne hozzáadni egy olyan meglévő PublicIpAddress címhez, amelynek még nincs DNS-neve, meg kell adnia egy DNS-nevet is:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Nyilvános IP-cím létrehozása fordított DNS-sel

Új PublicIpAddress létrehozása már megadott fordított DNS-tulajdonsággal:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Meglévő PublicIpAddress fordított DNS-cím ének megtekintése

Egy meglévő PublicIpAddress konfigurált értékének megtekintése:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Fordított DNS eltávolítása a meglévő nyilvános IP-címekről

Fordított DNS-tulajdonság eltávolítása meglévő PublicIpAddress címről:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Fordított DNS konfigurálása a felhőszolgáltatásokhoz

Ez a szakasz részletes útmutatást nyújt a fordított DNS-felhőszolgáltatásokhoz konfigurálásához a klasszikus üzembe helyezési modellben az Azure PowerShell használatával. A névfeloldási DNS konfigurálása a felhőszolgáltatásokhoz nem támogatott az Azure Portalon, az Azure classic CLI-n vagy az Azure CLI-n keresztül.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Fordított DNS hozzáadása a meglévő felhőszolgáltatásokhoz

Fordított DNS-rekord hozzáadása meglévő felhőszolgáltatáshoz:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Felhőszolgáltatás létrehozása fordított DNS-sel

Új felhőszolgáltatás létrehozása már megadott fordított DNS-tulajdonsággal:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>A meglévő felhőszolgáltatások fordított DNS-jának megtekintése

Meglévő felhőszolgáltatás fordított DNS-tulajdonságának megtekintése:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Fordított DNS eltávolítása a meglévő felhőszolgáltatásokból

Fordított DNS-tulajdonság eltávolítása meglévő felhőszolgáltatásból:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>GYIK

### <a name="how-much-do-reverse-dns-records-cost"></a>Mennyibe kerülnek a fordított DNS-rekordok?

Szabadok!  A fordított DNS-rekordok és lekérdezések nem járnak többletköltséggel.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Feloldódnak a fordított DNS-rekordok az internetről?

Igen. Miután beállította az Azure-szolgáltatás fordított DNS-tulajdonságát, az Azure kezeli az összes olyan DNS-delegálást és DNS-zónát, amely szükséges annak biztosításához, hogy a fordított DNS-rekord feloldása az összes internetfelhasználó számára.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Az Azure-szolgáltatásaimhoz alapértelmezett fordított DNS-rekordok jönnek létre?

Nem. A REVERSE DNS egy opt-in szolgáltatás. Ha úgy dönt, hogy nem konfigurálja őket, nem jönnek létre alapértelmezett névfeloldási DNS-rekordok.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Mi a teljesen minősített tartománynév (FQDN) formátuma?

Az FQDN-ek továbbítási sorrendben vannak megadva, és egy pontnak kell megszüntetnie (például "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Mi történik, ha a megadott fordított DNS-ellenőrzés sikertelen?

Ha a fordított DNS-érvényesítési ellenőrzés sikertelen, a fordított DNS-rekord konfigurálására irányuló művelet sikertelen lesz. Szükség szerint javítsa ki a fordított DNS-értéket, majd próbálkozzon újra.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Konfigurálhatom a fordított DNS-t az Azure App Service szolgáltatáshoz?

Nem. Az Azure App Service nem támogatja a fordított DNS szolgáltatást.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Konfigurálhatok több fordított DNS-rekordot az Azure-szolgáltatásomhoz?

Nem. Az Azure egyetlen fordított DNS-rekordot támogat minden Egyes Azure Cloud Service vagy PublicIpAddress szolgáltatáshoz.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Konfigurálható a fordított DNS az IPv6 PublicIpAddress erőforrásokhoz?

Nem. Az Azure jelenleg csak az IPv4 PublicIpAddress erőforrások és a felhőszolgáltatások fordított DNS-ét támogatja.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Küldhetek e-maileket külső tartományokba az Azure Compute-szolgáltatásaimból?

A technikai képesség, hogy e-mailt küldeni közvetlenül egy Azure-központi telepítés az előfizetés típusától függ. Az előfizetés típusától függetlenül a Microsoft azt javasolja, hogy megbízható levéltovábbítási szolgáltatásokat használjon a kimenő levelek küldéséhez. További részletekért [lásd: Fokozott Azure Security az e-mailek küldéséhez – 2017.](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/)

## <a name="next-steps"></a>További lépések

A fordított DNS-ről további információt a [Wikipédia névfeloldási DNS-keresése című témakörben talál.](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)
<br>
Ismerje meg, hogyan [üzemeltetheti az internetszolgáltató által hozzárendelt IP-tartomány névkeresési zónáját az Azure DNS-ben.](dns-reverse-dns-for-azure-services.md)

