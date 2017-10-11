## <a name="nic"></a>A HÁLÓZATI ADAPTER
Kártya (NIC) erőforrás hálózati illesztő hálózati kapcsolatot biztosít annak VNet az erőforrás egy létező alhálózatot. Bár létrehozhat egy hálózati adapter nem önálló objektumként, rendelje hozzá azt egy másik objektum ténylegesen összekapcsolhatóság kell. Egy hálózati Adaptert egy virtuális gép csatlakozni egy alhálózatot, egy nyilvános IP-címet vagy egy terheléselosztó használható.  

| Tulajdonság | Leírás | Példaértékek |
| --- | --- | --- |
| **virtualMachine** |Virtuális gép hálózati adapter társítva van. |/Subscriptions/{GUID}/../microsoft.COMPUTE/virtualMachines/vm1 |
| **macAddress** |A hálózati adapter MAC-címe |4 és 30 közötti értéket |
| **hálózati biztonsági csoporthoz tartozik** |A hálózati adapterhez társított NSG-t |/Subscriptions/{GUID}/../microsoft.Network/networkSecurityGroups/myNSG1 |
| **dnsSettings** |A hálózati adapter DNS-beállítások |Lásd: [PIP](#Public-IP-address) |

A hálózati kártyát, vagy a hálózati adapter, jelöli meg a hálózati adaptert egy virtuális gép (VM) társítható. A virtuális gép egy vagy több hálózati adapterrel rendelkezhet.

![A hálózati adapter által a egyetlen virtuális gép](./media/resource-groups-networking/Figure3.png)

### <a name="ip-configurations"></a>IP-konfigurációk
Hálózati adapterei nevű gyermekobjektum **IP-konfigurációk** tartalmaz a következő tulajdonságokat:

| Tulajdonság | Leírás | Példaértékek |
| --- | --- | --- |
| **alhálózat** |Alhálózatot a hálózati adapter az onnected. |/Subscriptions/{GUID}/../microsoft.Network/virtualNetworks/myvnet1/Subnets/mysub1 |
| **privateipaddress tulajdonságot** |A hálózati kártyának az alhálózat IP-cím |10.0.0.8 |
| **címkiosztási** |IP-címkiosztási módszerét |Dinamikus vagy statikus |
| **enableIPForwarding** |Hogy a hálózati adapter is használható Útválasztás |IGAZ vagy HAMIS eredményt ad |
| **elsődleges** |A hálózati adapter-e a virtuális gép az elsődleges hálózati adapter |IGAZ vagy HAMIS eredményt ad |
| **Nyilvános** |A PIP társított hálózati adapter |Lásd: [DNS-beállítások](#DNS-settings) |
| **konfigurációja terheléselosztói Háttércímkészletet** |Biztonsági end címkészlet társítva van a hálózati adapter | |
| **loadBalancerInboundNatRules** |A bejövő terhelés terheléselosztó NAT-szabályok a hálózati adapter társítva | |

A minta nyilvános IP-cím JSON formátumban:

    {
        "name": "lb-nic1-be",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkInterfaces",
        "location": "eastus",
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "ipConfigurations": [
                {
                    "name": "NIC-config",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/NIC-config",
                    "etag": "W/\"0027f1a2-3ac8-49de-b5d5-fd46550500b1\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "privateIPAddress": "10.0.0.4",
                        "privateIPAllocationMethod": "Dynamic",
                        "subnet": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet"
                        },
                        "loadBalancerBackendAddressPools": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool"
                            }
                        ],
                        "loadBalancerInboundNatRules": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"
                            }
                        ]
                    }
                }
            ],
            "dnsSettings": { ... },
            "macAddress": "00-0D-3A-10-F1-29",
            "enableIPForwarding": false,
            "primary": true,
            "virtualMachine": {
                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Compute/virtualMachines/web1"
            }
        }
    }

### <a name="additional-resources"></a>További források
* Olvassa el a [REST API referenciadokumentációt](https://msdn.microsoft.com/library/azure/mt163579.aspx) a hálózati adapterek.

