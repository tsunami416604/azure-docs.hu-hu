---
title: Oktatóanyag – Webalkalmazások egyéni Azure DNS-rekordjainak létrehozása
description: Ebben az oktatóanyagban webalkalmazások egyéni tartományi szintű DNS-rekordjait fogja létrehozni az Azure DNS használatával.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/20/2018
ms.author: victorh
ms.openlocfilehash: 9ebbc955bcb426738db598491266c2a1bcb9dd33
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39204942"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>Oktatóanyag: Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban 

Az Azure DNS-t beállíthatja a webalkalmazások egyéni tartományainak üzemeltetéséhez. Létrehozhat például egy Azure-webalkalmazást, majd a felhasználói számára hozzáférést adhat ahhoz a www.contoso.com vagy a contoso.com teljes tartománynév (FQDN) használatával.

> [!NOTE]
> A jelen oktatóanyag példáiban a contoso.com szerepel. A contoso.com helyére írja be a saját tartománynevét.

Ehhez létre kell hoznia a következő három rekordot:

* A contoso.com címre mutató gyökérszintű „A” rekordot.
* Egy gyökérszintű, TXT típusú rekordot az ellenőrzéshez.
* A www névhez tartozó „CNAME” rekordot, amely az „A” rekordra mutat.

Ne feledje, hogy ha egy Azure-beli webalkalmazás számára létrehoz egy „A” rekordot, akkor az „A” rekordot manuálisan kell frissíteni mindannyiszor, ha a webalkalmazás alapjául szolgáló IP-cím módosul.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * „A” és TXT típusú rekord létrehozása az egyéni tartományhoz
> * CNAME-rekord létrehozása az egyéni tartományhoz
> * Az új rekordok tesztelése
> * Egyéni gazdagépnevek hozzáadása a webalkalmazáshoz
> * Az egyéni gazdagépnevek tesztelése


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy App Service-alkalmazást](../app-service/app-service-web-get-started-html.md), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.

- Hozzon létre egy DNS-zónát az Azure DNS-ben, majd delegálja azt az Azure DNS-be a tartománynév kezelőjénél.

   1. A DNS-zóna létrehozásának lépéseit lásd a [DNS-zóna létrehozását](dns-getstarted-create-dnszone.md) ismertető szakaszban.
   2. Az Azure DNS-be történő zónadelegálás lépéseit lásd a [DNS-tartomány delegálását](dns-domain-delegation.md) ismertető részben.

Miután létrehozott egy zónát, és delegálta azt az Azure DNS-be, létrehozhat rekordokat az egyéni tartományhoz.

## <a name="create-an-a-record-and-txt-record"></a>„A” és TXT típusú rekord létrehozása

A rendszer az „A” rekordot használja a név IP-címre történő leképezéséhez. Az alábbi példában egy „@” karaktert fog hozzárendelni „A” rekordként a webalkalmazás IPv4-címének használatával. Az „@” karakter általában a gyökértartományt jelöli.

### <a name="get-the-ipv4-address"></a>Az IPv4-cím lekérése

Az Azure Portal App Services oldalának bal oldali navigációs sávján válassza ki az **Egyéni tartományok** elemet. 

![Egyéni tartomány menü](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Az **Egyéni tartományok** oldalra másolja be az alkalmazás IPv4-címét:

![Navigálás a portálon egy Azure-alkalmazáshoz](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>Az A rekord létrehozása

```powershell
New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>TXT típusú rekord létrehozása

Az App Services ezt a rekordot csak a konfiguráláskor használja annak ellenőrzéséhez, hogy Ön-e az egyéni tartomány tulajdonosa. Miután az App Service-ben érvényesítette és konfigurálta az egyéni tartományt, törölheti ezt a TXT típusú rekordot.

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name `"@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

Ha a tartományt már az Azure DNS kezeli (lásd a [DNS-tartomány delegálását](dns-domain-delegation.md) ismertető részben), akkor az alábbi példának megfelelően hozhat létre CNAME-rekordot a contoso.azurewebsites.net webalkalmazáshoz.

Nyissa meg az Azure PowerShellt, és hozzon létre egy új CNAME-rekordot. Ebben a példában egy CNAME típusú rekordhalmaz jön létre, 600 másodperces élettartammal, a „contoso.com” DNS-zónában, a contoso.azurewebsites.net webalkalmazás aliasával.

### <a name="create-the-record"></a>A rekord létrehozása

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -cname "contoso.azurewebsites.net")
```

A következő példa a válasz:

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## <a name="test-the-new-records"></a>Az új rekordok tesztelése

A „www.contoso.com” és a „contoso.com” nslookup-lekérdezésével ellenőrizheti, hogy a rekordok helyesen lettek-e létrehozva, az alább látható módon:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>Egyéni gazdagépnevek hozzáadása

Most már hozzáadhatja az egyéni gazdagépneveket a webalkalmazásához:

```powershell
set-AzureRmWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>Az egyéni gazdagépnevek tesztelése

Nyissa meg valamilyen böngészőben a `http://www.<your domainname>` és a `http://<you domain name>` webhelyet.

> [!NOTE]
> Ügyeljen rá, hogy az URL-címből ne maradjon le a `http://` előtag, különben a böngésző esetleg maga javasol majd egy URL-címet.

Mindkét URL-cím esetében ugyanannak az oldalnak kell megjelennie. Például:

![Contoso App Service](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a jelen oktatóanyagban létrehozott erőforrásokra, törölheti a **myresourcegroup** erőforráscsoportot.

## <a name="next-steps"></a>További lépések

Ismerje meg az Azure DNS Private Zones létrehozására szolgáló eljárást.

> [!div class="nextstepaction"]
> [Az Azure DNS Private Zones zónáival kapcsolatos első lépések PowerShell-lel](private-dns-getstarted-powershell.md)