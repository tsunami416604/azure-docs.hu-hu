---
title: Altartomány delegálása - Azure PowerShell – Azure DNS
description: Ezzel a tanulási útvonallal elkezdheti delegálni egy Azure DNS-altartományt az Azure PowerShell használatával.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: 7e019afaae98422b8d5a3c8fa7a5f79e26c6a149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937711"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Azure DNS-altartomány delegálása az Azure PowerShell használatával

Az Azure PowerShell segítségével delegálhatja a DNS-altartomány. Ha például a contoso.com tartomány tulajdonosa, delegálhat egy *mérnöki* nevű altartományt egy másik, különálló zónába, amelyet a contoso.com zónától elkülönítve felügyelhet.

Ha szeretné, delegálhat egy altartományt az [Azure Portalhasználatával.](delegate-subdomain.md)

> [!NOTE]
> Contoso.com példaként szolgál ebben a cikkben. A contoso.com helyére írja be a saját tartománynevét.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Azure DNS-altartomány delegálásához először delegálhatja a nyilvános tartományt az Azure DNS-nek. A névkiszolgálók delegálásra való konfigurálásával kapcsolatos útmutatásért [tekintse meg a Tartomány delegálása az Azure DNS-re](./dns-delegate-domain-azure-dns.md) című témakört. Miután a tartomány delegált az Azure DNS-zónában, delegálhatja az altartományt.

## <a name="create-a-zone-for-your-subdomain"></a>Zóna létrehozása az altartományhoz

Először hozza létre a **mérnöki** altartomány zónáját.

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Jegyezze fel a névkiszolgálókat

Ezután vegye figyelembe a mérnöki altartomány négy névkiszolgálóit.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Tesztrekord létrehozása

Hozzon létre egy **A** rekordot a mérnöki zónában a teszteléshez.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>NS-rekord létrehozása

Ezután hozzon létre egy névkiszolgálói (NS) rekordot a contoso.com zónában lévő **mérnöki** zónához.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>A küldöttség tesztelése

Az nslookup segítségével tesztelje a delegálást.

1. Indítson el egy PowerShell-ablakot.
2. A parancssorba írja be a következőt:`nslookup www.engineering.contoso.com.`
3. Nem mérvadó választ kell kapnia, amely a **10.10.10.10**címet mutatja.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogy [miként konfigurálhatja a fordított DNS-t az Azure-ban üzemeltetett szolgáltatásokhoz.](dns-reverse-dns-for-azure-services.md)