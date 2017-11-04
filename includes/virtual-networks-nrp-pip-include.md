## <a name="public-ip-address"></a>Nyilvános IP-cím
Egy nyilvános IP-cím erőforrás vagy egy fenntartott vagy dinamikus internetes IP-címet tartalmaz. Bár létrehozhat egy nyilvános IP-cím megegyezik egy önálló, kell társítsa a címet szeretné valójában egy másik objektumhoz. A nyilvános IP-címet a terheléselosztóhoz, Alkalmazásátjáró vagy egy hálózati Adaptert, Internet-hozzáférést biztosít azokhoz az erőforrásokhoz is hozzárendelhető.  

| Tulajdonság | Leírás | Példaértékek |
| --- | --- | --- |
| **publicIPAllocationMethod** |Meghatározza, hogy az IP-cím *statikus* vagy *dinamikus*. |statikus, dinamikus |
| **idleTimeoutInMinutes** |Határozza meg az üresjárati időkorlát, 4 perces alapértelmezett értékkel. Ha egy adott munkamenethez nincs további csomagok ezen időn belül érkezik, a munkamenet megszakítása. |4 és 30 közötti értéket |
| **IP-cím** |Az objektum hozzárendelt IP-címet. Ez a tulajdonság csak olvasható. |104.42.233.77 |

### <a name="dns-settings"></a>DNS-beállítások
Nyilvános IP-címek rendelkezik nevű gyermekobjektum **dnsSettings** tartalmaz a következő tulajdonságokat:

| Tulajdonság | Leírás | Példaértékek |
| --- | --- | --- |
| **domainNameLabel** |Nevű a névfeloldáshoz használnak. |a webszolgáltatáshoz, ftp, vm1 |
| **teljesen minősített tartományneve** |A nyilvános IP-cím teljesen minősített nevet. |www.westus.cloudapp.Azure.com |
| **reverseFqdn** |Teljesen minősített tartománynevét, amely megkeresi az IP-címet, és a DNS-ben a PTR típusú rekord, regisztrálva van. |www.contoso.com. |

A minta nyilvános IP-cím JSON formátumban:

    {
       "name": "PIP01",
       "location": "North US",
       "tags": { "key": "value" },
       "properties": {
          "publicIPAllocationMethod": "Static",
          "idleTimeoutInMinutes": 4,
          "ipAddress": "104.42.233.77",
          "dnsSettings": {
             "domainNameLabel": "mylabel",
             "fqdn": "mylabel.westus.cloudapp.azure.com",
             "reverseFqdn": "contoso.com."
          }
       }
    } 

### <a name="additional-resources"></a>További források
* További információk [nyilvános IP-címek](../articles/virtual-network/virtual-networks-reserved-public-ip.md).
* További tudnivalók [szintű nyilvános IP-címek példány](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).
* Olvassa el a [REST API referenciadokumentációt](https://msdn.microsoft.com/library/azure/mt163638.aspx) nyilvános IP-címek.

