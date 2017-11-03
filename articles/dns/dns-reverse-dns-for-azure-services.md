---
title: "Névkeresési DNS az Azure-szolgáltatásokhoz |} Microsoft Docs"
description: "Az Azure-ban tárolt szolgáltatások DNS-névlekérdezés konfigurálása"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 63701e1ce0c1c6dcf2ce02ebce272b8280395e7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Az Azure-ban tárolt szolgáltatások címfeloldási DNS konfigurálása

Ez a cikk az Azure-ban tárolt szolgáltatások DNS-névlekérdezés konfigurálását ismerteti.

Az Azure szolgáltatások IP-címek hozzárendelése az Azure-ban, és a Microsoft tulajdonában használják. A címfeloldási DNS-rekordok (PTR-rekordok) kell létrehozni a megfelelő Microsoft által birtokolt névkeresési DNS-címkeresési zónák. Ez a cikk azt ismerteti, hogyan ehhez.

Ez a forgatókönyv nem keverendő teszi [üzemeltetni a hozzárendelt IP-címtartományokhoz az Azure DNS-névkeresési DNS-címkeresési zónák](dns-reverse-dns-hosting.md). Ebben az esetben az IP-címtartományai a névkeresési zóna által képviselt kell rendelni a szervezetben, általában az Internetszolgáltató által.

A cikk elolvasása előtt meg kell ismernie a [címfeloldási DNS- és támogatás az Azure-ban – áttekintés](dns-reverse-dns-overview.md).

Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md).
* A Resource Manager üzembe helyezési modellel számítási erőforrások (például virtuális gépek, a virtuálisgép-méretezési csoportok vagy a Service Fabric-fürtök) PublicIpAddress erőforrás keresztül érhetők el. DNS-névlekérdezés úgy vannak konfigurálva, a PublicIpAddress "ReverseFqdn" tulajdonsággal.
* A klasszikus üzembe helyezési modellel a számítási erőforrások elérhetők felhőalapú szolgáltatások segítségével. DNS-névlekérdezés úgy vannak konfigurálva, a felhőalapú szolgáltatás "ReverseDnsFqdn" tulajdonság használatával.

Névkeresési DNS jelenleg nem támogatott az Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>A címfeloldási DNS-rekordok érvényesítése

A harmadik fél nem kell tudni az Azure-szolgáltatás a leképezés csak akkor a DNS-tartományok címfeloldási DNS-rekordok létrehozása. Ennek megelőzése érdekében Azure csak lehetővé teszi, hogy amennyiben a címfeloldási DNS-rekord megadott tartománynév megegyezik a címfeloldási DNS-rekord létrehozása, vagy oldja fel, a DNS-nevét vagy IP-címhez a nyilvános vagy a Felhőszolgáltatás azonos Azure-előfizetést.

Az ellenőrzés csak akkor hajtható végre, ha a címfeloldási DNS-rekord beállítani vagy módosítani. Rendszeres ismételt érvényesítése nem megy végbe.

Például: Tegyük fel, hogy a nyilvános erőforrás van, a DNS-név contosoapp1.northus.cloudapp.azure.com és IP-cím 23.96.52.53. Az a PublicIpAddress ReverseFqdn adhatók meg:
* A nyilvános DNS-nevének contosoapp1.northus.cloudapp.azure.com
* Ugyanazt az előfizetést, például a contosoapp2.westus.cloudapp.azure.com egy másik nyilvános DNS-neve
* A kreatív DNS-beli név, például a app1.contoso.com, mindaddig, amíg ez a név *első* konfigurálva, egy CNAME contosoapp1.northus.cloudapp.azure.com, vagy egy másik nyilvános ugyanahhoz az előfizetéshez.
* A kreatív DNS-beli név, például a app1.contoso.com, mindaddig, amíg ez a név *első* konfigurálva, az A rekord az IP-cím 23.96.52.53, vagy olyan ugyanazt az előfizetést másik nyilvános IP-címét.

Az azonos megkötések alkalmazása névkeresési DNS-szolgáltatásokhoz.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Névkeresési DNS PublicIpAddress erőforrások

Ez a témakör részletes útmutatást PublicIpAddress erőforrások címfeloldási DNS konfigurálása a Resource Manager üzembe helyezési modellel, az Azure PowerShell, Azure CLI 1.0 vagy Azure CLI 2.0. Nyilvános erőforrások címfeloldási DNS konfigurálása jelenleg nem támogatott az Azure-portálon.

Az Azure jelenleg támogatja a fordított DNS kizárólag a nyilvános IPv4-erőforrások. Az IPv6 nem támogatott.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Névkeresési DNS hozzáadása egy meglévő PublicIpAddresses

#### <a name="powershell"></a>PowerShell

Névkeresési DNS hozzáadása egy meglévő nyilvános:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

Névkeresési DNS hozzáadása egy meglévő nyilvános, amely még nem rendelkezik a DNS-név, is meg kell adnia a DNS-név:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

Névkeresési DNS hozzáadása egy meglévő nyilvános:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Névkeresési DNS hozzáadása egy meglévő nyilvános, amely még nem rendelkezik a DNS-név, is meg kell adnia a DNS-név:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

Névkeresési DNS hozzáadása egy meglévő nyilvános:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Névkeresési DNS hozzáadása egy meglévő nyilvános, amely még nem rendelkezik a DNS-név, is meg kell adnia a DNS-név:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Hozzon létre egy nyilvános IP-cím a címfeloldási DNS-ben

A címfeloldási DNS tulajdonság már meg van adva egy új nyilvános létrehozása:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Egy meglévő nyilvános címfeloldási DNS megtekintése

A konfigurált értéket egy meglévő nyilvános megjelenítése:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Távolítsa el a címfeloldási DNS a meglévő nyilvános IP-címek

A címfeloldási DNS tulajdonság eltávolítása egy meglévő nyilvános:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Névkeresési DNS konfigurálása a Cloud Services csomag

Ez a témakör részletes útmutatást a Felhőszolgáltatások fordított DNS konfigurálása a klasszikus üzembe helyezési modellel, az Azure PowerShell. Névkeresési DNS konfigurálása a Cloud Services nem támogatja az Azure-portál, Azure CLI 1.0-s vagy Azure CLI 2.0.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Névkeresési DNS hozzáadása meglévő Cloud Services csomag

A címfeloldási DNS-rekord hozzáadása egy meglévő felhőalapú szolgáltatást:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>A címfeloldási DNS-felhőalapú szolgáltatás létrehozása

Új felhőalapú szolgáltatás létrehozása a címfeloldási DNS tulajdonság már meg van adva:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Nézet névkeresési DNS meglévő Cloud Services csomag

Létező felhőalapú szolgáltatást szeretné megtekinteni a címfeloldási DNS-tulajdonság:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Távolítsa el a címfeloldási DNS a meglévő Cloud Services csomag

A címfeloldási DNS tulajdonság eltávolítása egy meglévő felhőalapú szolgáltatást:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>GYIK

### <a name="how-much-do-reverse-dns-records-cost"></a>Milyen mértékű névkeresési DNS-rekordok költség?

Szabad fontosságúak!  Nincs a címfeloldási DNS-rekordok vagy lekérdezések további költség nélkül.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>A címfeloldási DNS-rekordok feloldja az internetről?

Igen. Ha egyszer már megadta a címfeloldási DNS-tulajdonság az Azure Service, Azure kezeli a DNS-delegálás és győződjön meg arról, hogy címfeloldási DNS-rekord megszünteti az összes internetes felhasználó számára szükséges DNS-zónák.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Alapértelmezett címfeloldási DNS-rekordok létrejönnek az az Azure-szolgáltatásokat?

Nem. Névkeresési DNS egyik választható szolgáltatása nem. Nincs alapértelmezett címfeloldási DNS-rekordok jönnek létre, ha nem konfigurálja őket.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Mi az a teljes tartománynév (FQDN) formátumban?

Teljes tartománynevek előre sorrendben legyenek megadva, és egy pontot (például "app1.contoso.com.") kell végződnie.

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Mi történik, ha az eredetiség ellenőrzésének a címfeloldási DNS-fiókjához megadott sikertelen?

A névkeresési DNS-ellenőrzés sikertelen lesz, ahol konfigurálhatja a címfeloldási DNS-rekord a művelet sikertelen lesz. Javítsa ki a címfeloldási DNS értékét szükség szerint, és próbálkozzon újra.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Beállítható címfeloldási DNS az Azure App Service?

Nem. Névkeresési DNS nem támogatott az Azure App Service szolgáltatásban.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Beállítható több címfeloldási DNS-rekordok az Azure Service?

Nem. Azure egyetlen címfeloldási DNS-rekord támogatja az egyes Azure Cloud Service vagy nyilvános.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Konfigurálhatja a címfeloldási DNS IPv6 PublicIpAddress erőforrások?

Nem. Az Azure jelenleg támogatja a fordított DNS csak az IPv4-alapú PublicIpAddress erőforrások és a Felhőszolgáltatások.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Lehet küldeni e-mailek külső tartományokhoz a saját Azure számítási szolgáltatások?

Nem. [Az Azure Compute szolgáltatások nem támogatják a külső tartományokkal az e-maileket küldő](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)

## <a name="next-steps"></a>Következő lépések

A címfeloldási DNS-további információkért lásd: [Wikipedia névkeresési DNS](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Megtudhatja, hogyan [az az Internetszolgáltató által hozzárendelt IP-címtartományt, az Azure DNS-névkeresési zóna](dns-reverse-dns-for-azure-services.md).

