---
title: Az Azure PowerShell-lel az Azure DNS altartomány delegálása
description: Ismerje meg, hogyan lehet egy Azure PowerShell-lel az Azure DNS-altartomány delegálása.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: victorh
ms.openlocfilehash: 40b2a4d98e6269d9740856ba44c1043af75ce1b8
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55896700"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Az Azure PowerShell-lel az Azure DNS altartomány delegálása

Azure PowerShell használatával a DNS altartományában delegálására. Például ha Ön a tulajdonosa a contoso.com tartományhoz, akkor is engedélyezheti – delegálja nevű altartomány *mérnöki* , egy másik zónához, amely a contoso.com zóna a külön-külön felügyelheti.

Igény szerint egy altartományt az delegálhatja a [az Azure Portal](delegate-subdomain.md).

> [!NOTE]
> A contoso.com egész cikkben példaként szolgál. A contoso.com helyére írja be a saját tartománynevét.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Azure DNS altartományában delegálni, először delegálnia kell az Azure DNS nyilvános tartományát. Lásd: [tartomány delegálása az Azure DNS](./dns-delegate-domain-azure-dns.md) vonatkozó utasításokat a névkiszolgálókat a delegálás konfigurálása. Az Azure DNS-zónabeli tartományához delegált, miután az altartomány delegálhat.

## <a name="create-a-zone-for-your-subdomain"></a>Create a zone for your subdomain

Először hozza létre a zónát a **mérnöki** altartományt.

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Vegye figyelembe a Névkiszolgálók

Next, note the four name servers for the engineering subdomain.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Test-rekord létrehozása

Hozzon létre egy **A** rekord engineering zónában teszteléshez használni kívánt.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>NS-rekord létrehozása

Ezután hozzon létre egy név névkiszolgáló (NS) rekordot a **mérnöki** a contoso.com zónában.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>A delegálás tesztelése

Az nslookup követve tesztelheti a delegálást.

1. Nyisson meg egy PowerShell-ablakot.
2. Írja be a parancssorba `nslookup www.engineering.contoso.com.`
3. Ekkor kap egy nem mérvadó válasz, a cím megjelenítése **10.10.10.10**.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [fordított DNS az Azure-ban üzemeltetett szolgáltatások konfigurálása](dns-reverse-dns-for-azure-services.md).