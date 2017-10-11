## <a name="load-balancer"></a>Load Balancer
A terheléselosztó szolgál az alkalmazások méretezésére. Tipikus telepítési forgatókönyvek tartalmaz, amely több Virtuálisgép-példányok futó alkalmazások. A Virtuálisgép-példányok által olyan terheléselosztóhoz, amely segíti a különböző példányok hálózati forgalom elosztását vannak fronted. 

![A hálózati adapter által a egyetlen virtuális gép](./media/resource-groups-networking/figure8.png)

| Tulajdonság | Leírás |
| --- | --- |
| *frontendipconfiguration osztálya lehet* |a terheléselosztó tartalmazhat egy vagy több előtérbeli IP-címek, más néven a virtuális IP-címek (VIP). Az IP-címeket szolgál a forgalom érkező és nyilvános IP-cím vagy a magánhálózati IP-címe |
| *backendaddresspools készletek* |Ezek azok a virtuális gép hálózati adaptert, amelyre terhelés eloszlik a társított IP-címek |
| *esetén a terheléselosztási szabályok* |egy szabály tulajdonság leképezi a megadott előtér-IP és port kombinációján a háttérbeli IP-címek egy készletének – port kombináció. A terheléselosztó erőforrást egyetlen definícióját meghatározhatja, hogy több terheléselosztási szabályok, minden egyes szabály egy első kombinációja tükröző befejező IP-cím és port és a záró IP-cím és port társított virtuális gépek biztonsági. A szabály a háttérkészlethez a több virtuális gép az előtér-készlet egy port |
| *Mintavétel* |mintavételt lehetővé teszik a Virtuálisgép-példányok állapotának nyomon követéséhez. Ha egy állapotmintáihoz nem sikerül, a virtuálisgép-példányt lesz kívül Elforgatás automatikusan |
| *inboundNatRules* |NAT-szabályok meghatározása a bejövő forgalom haladnak keresztül az első IP befejezését, majd a háttérbeli IP-címhez egy adott virtuális gép példányhoz elosztott. NAT-szabály egy virtuális gépre a háttérkészlethez előtér-készletben egyetlen port |

Példa a terheléselosztó-sablon Json formátumban:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "dnsNameforLBIP": {
          "type": "string",
          "metadata": {
            "description": "Unique DNS name"
          }
        },
        "location": {
          "type": "string",
          "allowedValues": [
            "East US",
            "West US",
            "West Europe",
            "East Asia",
            "Southeast Asia"
          ],
          "metadata": {
            "description": "Location to deploy"
          }
        },
        "addressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/16",
          "metadata": {
            "description": "Address Prefix"
          }
        },
        "subnetPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/24",
          "metadata": {
            "description": "Subnet Prefix"
          }
        },
        "publicIPAddressType": {
          "type": "string",
          "defaultValue": "Dynamic",
          "allowedValues": [
            "Dynamic",
            "Static"
          ],
          "metadata": {
            "description": "Public IP type"
          }
        }
      },
      "variables": {
        "virtualNetworkName": "virtualNetwork1",
        "publicIPAddressName": "publicIp1",
        "subnetName": "subnet1",
        "loadBalancerName": "loadBalancer1",
        "nicName": "networkInterface1",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
        "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
        "nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
        "backEndIPConfigID": "[concat(variables('nicId'),'/ipConfigurations/ipconfig1')]"
      },
      "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[parameters('location')]",
      "properties": {
        "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsNameforLBIP')]"
        }
      }
    },
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[parameters('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[variables('subnetRef')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(variables('lbID'), '/backendAddressPools/LoadBalancerBackend')]"
                }
              ],
              "loadBalancerInboundNatRules": [
                {
                  "id": "[concat(variables('lbID'),'/inboundNatRules/RDP')]"
                }
              ]
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-05-01-preview",
      "name": "[variables('loadBalancerName')]",
      "type": "Microsoft.Network/loadBalancers",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "loadBalancerFrontEnd",
            "properties": {
              "publicIPAddress": {
                "id": "[variables('publicIPAddressID')]"
              }
            }
          }
        ],
        "backendAddressPools": [
          {
            "name": "loadBalancerBackEnd"
          }
        ],
        "inboundNatRules": [
          {
            "name": "RDP",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "protocol": "tcp",
              "frontendPort": 3389,
              "backendPort": 3389,
              "enableFloatingIP": false
            }
          }
        ]
      }
    }
      ]
    }

### <a name="additional-resources"></a>További források
Olvasási [terheléselosztó REST API](https://msdn.microsoft.com/library/azure/mt163651.aspx) további információt.

