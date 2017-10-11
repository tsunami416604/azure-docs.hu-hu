## <a name="route-tables"></a>Az útvonaltáblák
Útválasztási táblázat erőforrások hogyan a forgalom belül az Azure-infrastruktúra meghatározásához használt útvonalak tartalmazza. Felhasználó által megadott útvonalakat (UDR) segítségével az összes forgalom küldése az adott alhálózat virtuális készülékre, például egy tűzfal vagy behatolás észlelési rendszert (Azonosítók). Az alhálózatokhoz egy útválasztási táblázatot lehet társítani. 

Az útvonaltáblák az alábbi tulajdonságokat tartalmazzák.

| Tulajdonság | Leírás | Példaértékek |
| --- | --- | --- |
| **útvonalak** |Felhasználó által megadott útvonalak útvonaltábla |Lásd: [felhasználó által megadott útvonalak](#User-defined-routes) |
| **alhálózatok** |Az útvonaltábla vonatkozik, az alhálózatok gyűjteménye |Lásd: [alhálózatok](#Subnets) |

### <a name="user-defined-routes"></a>Felhasználó által definiált útvonalak
Létrehozhat udr-EK adhatja meg, ha forgalmat kell küldeni, a cél címe alapján. Az eltolásokat tekintheti útvonal az alapértelmezett átjáró definíciófrissítések a hálózati csomagok célcím alapján.

Udr-EK az alábbi tulajdonságokat tartalmazzák. 

| Tulajdonság | Leírás | Példaértékek |
| --- | --- | --- |
| **addressPrefix** |Cím előtagján vagy a teljes IP-címet a cél |192.168.1.0/24, 192.168.1.101 |
| **nexthoptype elem** |A forgalom kapnak eszköz típusa |VirtualAppliance, VPN-átjárót, az Internet |
| **nexthopipaddress eleme** |A következő ugrás IP-címe |192.168.1.4 |

A minta útvonaltábla JSON formátumban:

    {
        "name": "UDR-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/routeTables",
        "location": "westus",
        "properties": {
            "provisioningState": "Succeeded",
            "routes": [
                {
                    "name": "RouteToFrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd/routes/RouteToFrontEnd",
                    "etag": "W/\"v\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "nextHopType": "VirtualAppliance",
                        "nextHopIpAddress": "192.168.0.4"
                    }
                }
            ],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd"
                }
            ]
        }
    }

### <a name="additional-resources"></a>További források
* További információk [udr-EK](../articles/virtual-network/virtual-networks-udr-overview.md).
* Olvassa el a [REST API referenciadokumentációt](https://msdn.microsoft.com/library/azure/mt502549.aspx) az útválasztási táblázatokat.
* Olvassa el a [REST API referenciadokumentációt](https://msdn.microsoft.com/library/azure/mt502539.aspx) felhasználó által megadott útvonalak (udr-EK).

