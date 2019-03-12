---
title: Fordított DNS az Azure-szolgáltatások |} A Microsoft Docs
description: Fordított DNS-lekérdezések az Azure-ban üzemeltetett szolgáltatások konfigurálása
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: e162d838cb4895841428a827b56bec28e3e16b8a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533143"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Az Azure-ban üzemeltetett szolgáltatások fordított DNS konfigurálása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure-ban üzemeltetett szolgáltatások fordított DNS-lekérdezések.

Azure-szolgáltatások IP-címek az Azure által hozzárendelt, és a Microsoft által birtokolt használatára. Ezek a fordított DNS-rekordok (PTR típusú rekord) a megfelelő Microsoft tulajdonú DNS-névkeresési zónák kell létrehozni. Ez a cikk azt ismerteti, hogyan teheti ezt.

Ebben a forgatókönyvben nem tévesztendő lehetővé teszi az [az Azure DNS-ben rendelt IP-címtartományok fordított DNS-névkeresési zónák üzemeltetése](dns-reverse-dns-hosting.md). Ebben az esetben a névkeresési zóna által jelölt IP-címtartományokkal kell rendelni a szervezethez, általában az Internetszolgáltató által.

A cikk elolvasása előtt meg kell ismernie a [fordított DNS és támogatás az Azure-ban – áttekintés](dns-reverse-dns-overview.md).

Az Azure DNS-ben a számítási erőforrások (például virtuális gépek, a virtual machine scale sets vagy Service Fabric-fürtök) egy nyilvános IP-címre erőforráson keresztül érhetők el. Fordított DNS-lekérdezések úgy vannak konfigurálva, a "Fordított teljes tartománynév" tulajdonság a publicipaddress használatával.


Fordított DNS jelenleg nem támogatott az Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>Fordított DNS-rekordok érvényesítése

Egy harmadik fél nem lehet az Azure-szolgáltatás a leképezés csak a DNS-tartományok fordított DNS-rekordok létrehozásához. Ennek megelőzése érdekében az Azure csak lehetővé teszi egy fordított DNS-rekordot, ahol a fordított DNS-rekordban megadott tartomány neve megegyezik a létrehozását, vagy oldja fel, a DNS-nevét vagy IP-címhez a nyilvános IP-címre vagy a felhőalapú szolgáltatás Azure-előfizetéshez.

Az ellenőrzés csak akkor hajtható végre, amikor a fordított irányú DNS-rekord beállítani vagy módosítani. Rendszeres újbóli érvényesítése nem történik meg.

For example: suppose the PublicIpAddress resource has the DNS name contosoapp1.northus.cloudapp.azure.com and IP address 23.96.52.53. A fordított teljes tartománynév a nyilvános IP-címre vonatkozó adhat meg:
* The DNS name for the PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* A DNS-neve eltérő nyilvános IP-címre ugyanahhoz az előfizetéshez, például contosoapp2.westus.cloudapp.azure.com
* Egy személyes DNS-beli név, például a app1.contoso.com, mindaddig, amíg ez a név az *első* konfigurálva, egy CNAME rekord contosoapp1.northus.cloudapp.azure.com, vagy másik nyilvános IP-címre ugyanabban az előfizetésben.
* Egy személyes DNS-beli név, például a app1.contoso.com, mindaddig, amíg ez a név az *első* konfigurálva, egy A rekordot 23.96.52.53 IP-címet, vagy másik nyilvános IP-címre azonos előfizetésben található IP-címét.

Az azonos korlátozások vonatkoznak a Cloud Services – címfeloldási DNS.


## <a name="reverse-dns-for-publicipaddress-resources"></a>PublicIpAddress erőforrásokat címfeloldási DNS

Ez a témakör részletes utasításokat a PublicIpAddress erőforrásokat a fordított DNS konfigurálása a Resource Manager-alapú üzemi modellben, az Azure PowerShell, az Azure klasszikus parancssori felület vagy az Azure CLI használatával. A PublicIpAddress erőforrásokat fordított DNS konfigurálása jelenleg nem támogatott az Azure Portalon keresztül.

Az Azure jelenleg támogatja a címfeloldási DNS csak az IPv4-PublicIpAddress erőforrásokat. Az IPv6 nem támogatott.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Fordított DNS hozzáadása egy meglévő PublicIpAddresses

#### <a name="powershell"></a>PowerShell

Fordított DNS hozzáadása egy meglévő nyilvános IP-címre:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Fordított DNS hozzáadása egy meglévő PublicIpAddress, amely még nem rendelkezik a DNS-név, is meg kell adnia egy DNS-név:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

Fordított DNS hozzáadása egy meglévő nyilvános IP-címre:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Fordított DNS hozzáadása egy meglévő PublicIpAddress, amely még nem rendelkezik a DNS-név, is meg kell adnia egy DNS-név:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

Fordított DNS hozzáadása egy meglévő nyilvános IP-címre:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Fordított DNS hozzáadása egy meglévő PublicIpAddress, amely még nem rendelkezik a DNS-név, is meg kell adnia egy DNS-név:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Fordított DNS nyilvános IP-cím létrehozása

A fordított irányú DNS-tulajdonság már meg van adva egy új nyilvános IP-címre létrehozása:

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

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Fordított DNS nézet egy meglévő nyilvános IP-címre

A konfigurált értéket egy meglévő nyilvános IP-címre megtekintése:

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

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Fordított DNS eltávolítása meglévő nyilvános IP-címek

Fordított DNS tulajdonság eltávolítása egy meglévő nyilvános IP-címre:

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


## <a name="configure-reverse-dns-for-cloud-services"></a>Fordított DNS konfigurálása a Felhőszolgáltatásokhoz

Ez a szakasz részletes utasításokat a fordított DNS konfigurálása a Cloud Services az Azure PowerShell-lel a klasszikus üzemi modellt biztosít. Fordított DNS konfigurálása a Cloud Services az Azure Portalon, az Azure klasszikus parancssori felület vagy az Azure CLI-n keresztül nem támogatott.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Fordított DNS hozzáadása meglévő felhőszolgáltatásokhoz

Fordított DNS-rekord hozzáadása egy meglévő felhőalapú szolgáltatást:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>A fordított irányú DNS-Felhőszolgáltatás létrehozása

Új felhőalapú szolgáltatás létrehozása a fordított irányú DNS-tulajdonság már meg van adva:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Fordított DNS nézetben a meglévő Cloud Services

A fordított irányú DNS-tulajdonság megtekintése a meglévő Cloud Services számára:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Fordított DNS eltávolítása a meglévő Cloud Services

Fordított DNS tulajdonság eltávolítása egy meglévő felhőalapú szolgáltatást:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>GYIK

### <a name="how-much-do-reverse-dns-records-cost"></a>IP-címek fenntartási fordított DNS-rekordok költség?

Azok a ingyenes!  Nincs fordított DNS-rekordok vagy a lekérdezések további költség nélkül.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>A fordított DNS-rekordok feloldja az internetről?

Igen. Miután beállította a fordított irányú DNS-tulajdonság az Azure szolgáltatás, Azure kezeli a DNS-delegálás és győződjön meg arról, hogy fordított irányú DNS-rekord feloldása az összes internetes felhasználó számára szükséges DNS-zónák.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Fordított DNS-rekordok alapértelmezett jön létre az Azure-szolgáltatások?

Nem. Fordított DNS egy olyan opcionális szolgáltatás. Nem alapértelmezett fordított DNS-rekordok jönnek létre, ha úgy dönt, hogy nem konfigurálja őket.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Mi az a teljes tartománynév (FQDN) formátumban?

Teljes tartománynevek előre sorrendben vannak megadva, és a egy ponttal (például "app1.contoso.com.") kell végződniük.

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Mi történik, ha az ellenőrzés a fordított irányú DNS-már meg sikertelen?

A fordított irányú DNS-ellenőrzés nem sikerül, ha a művelet a fordított irányú DNS-rekord konfigurálása sikertelen lesz. Javítsa ki a fordított irányú DNS-értékét szükség szerint, és próbálkozzon újra.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Konfigurálhatok fordított DNS az Azure App Service?

Nem. Fordított DNS az Azure App Service nem támogatott.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Konfigurálhatok több fordított DNS-rekordok az Azure szolgáltatáshoz?

Nem. Az Azure minden Azure-Felhőszolgáltatás vagy nyilvános IP-címre egy egyszeri fordított DNS-rekord támogatja.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Konfigurálhatja a fordított DNS IPv6 PublicIpAddress erőforrásokat?

Nem. Az Azure jelenleg támogatja a címfeloldási DNS csak IPv4-PublicIpAddress erőforrásokat és a Cloud Services számára.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Küldhetek e-maileket a külső tartományokra a saját Azure számítási szolgáltatások?

E-mail küldéséhez közvetlenül az Azure-telepítés technikai lehetővé teszi az előfizetés típusától függ. Előfizetés típusa, függetlenül a Microsoft azt javasolja, megbízható mail-továbbítási szolgáltatások használata a kimenő e-mailek küldésére. További részletekért lásd: [továbbfejlesztett Azure Security küld e-mailek – 2017 novemberi frissítés](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>További lépések

Fordított DNS további információkért lásd: [Wikipedia DNS-címkeresés](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Ismerje meg, hogyan [a névkeresési zóna Azure DNS-ben az Internetszolgáltató által kiosztott IP-címtartományt a gazdagép](dns-reverse-dns-for-azure-services.md).

