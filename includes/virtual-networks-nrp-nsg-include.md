## <a name="network-security-group"></a>Hálózati biztonsági csoport
Az NSG-erőforrás létrehozását lehetővé tevő biztonsági határ munkaterhelésekhez, implementálásával engedélyezése, és megtagadási szabályoknak. Ezek a szabályok alkalmazhatók a virtuális gép, egy hálózati adapter vagy egy alhálózatot.

| Tulajdonság | Leírás | Példaértékek |
| --- | --- | --- |
| **alhálózatok** |Az NSG vonatkozik, az alhálózati azonosítók listáját. |/Subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/TestRG/Providers/Microsoft.Network/virtualNetworks/TestVNet/Subnets/FrontEnd |
| **securityRules** |Az NSG alkotó biztonsági szabályok listája |Lásd: [biztonsági szabály](#Security-rule) alatt |
| **defaultSecurityRules** |Minden NSG található alapértelmezett biztonsági szabályok listája |Lásd: [alapértelmezett biztonsági szabályok](#Default-security-rules) alatt |

* **A biztonsági szabály** -egy NSG rendelkezhet több biztonsági szabály lett meghatározva. Minden egyes szabály engedélyezheti vagy tagadhatja különböző típusú forgalom.

### <a name="security-rule"></a>A biztonsági szabály
A szabály egy NSG-t az alábbi tulajdonságait tartalmazó gyermek erőforrása.

| Tulajdonság | Leírás | Példaértékek |
| --- | --- | --- |
| **Leírás** |Egy leírást a szabályhoz |Bejövő adatforgalom engedélyezésére X alhálózatban lévő virtuális gépen |
| **protokoll** |A szabálynak megfelelő protokoll |TCP, UDP vagy * |
| **sourcePortRange** |A szabálynak megfelelő forrásporttartomány |80, 100-200, * |
| **destinationPortRange** |A szabálynak megfelelő célporttartomány |80, 100-200, * |
| **sourceAddressPrefix** |A szabálynak megfelelő forráscím-előtag |10.10.10.1, 10.10.10.0/24, VirtualNetwork |
| **destinationAddressPrefix** |A szabálynak megfelelő célcím-előtag |10.10.10.1, 10.10.10.0/24, VirtualNetwork |
| **iránya** |A forgalom szabálynak megfelelő iránya |bejövő vagy kimenő |
| **prioritás** |A szabály prioritását. Szabályok prioritás szerinti sorrendben ellenőrzi, a szabály vonatkozik, ha nincsenek további szabályok kiértékelésének egyeztetéséhez. |10, 100, 65000 |
| **hozzáférés** |Az alkalmazandó hozzáférés típusa, ha a csomag megfelel a szabálynak |engedélyezés vagy megtagadás |

NSG a JSON formátum. minta:

    {
        "name": "NSG-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkSecurityGroups",
        "location": "westus",
        "tags": {
            "displayName": "NSG - Front End"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "securityRules": [
                {
                    "name": "rdp-rule",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/rdp-rule",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "description": "Allow RDP",
                        "protocol": "Tcp",
                        "sourcePortRange": "*",
                        "destinationPortRange": "3389",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "access": "Allow",
                        "priority": 100,
                        "direction": "Inbound"
                    }
                }
            ],
            "defaultSecurityRules": [
                { [...],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                }
            ]
        }
    }

### <a name="default-security-rules"></a>Alapértelmezett biztonsági szabályok

Alapértelmezett szabályok érhető el a biztonsági szabályok azonos tulajdonságokkal rendelkezik. Adja meg, amelyek az NSG-k vonatkoznak annak biztosítása érdekében erőforrások közötti hálózati kapcsolat léteznek. Ellenőrizze, hogy tudja, amely [alapértelmezett biztonsági szabályok](../articles/virtual-network/virtual-networks-nsg.md#default-rules) létezik.

### <a name="additional-resources"></a>További források
* További információk [NSG-k](../articles/virtual-network/virtual-networks-nsg.md).
* Olvassa el a [REST API referenciadokumentációt](https://msdn.microsoft.com/library/azure/mt163615.aspx) az NSG-ket.
* Olvassa el a [REST API referenciadokumentációt](https://msdn.microsoft.com/library/azure/mt163580.aspx) a biztonsági szabályok.
