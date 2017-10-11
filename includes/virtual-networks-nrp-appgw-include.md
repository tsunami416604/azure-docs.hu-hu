## <a name="application-gateway"></a>Application Gateway
Alkalmazásátjáró egy Azure által kezelt HTTP terheléselosztási réteg 7 terheléselosztás megoldást biztosít. Alkalmazás terheléselosztás lehetővé teszi, hogy az a HTTP-alapú hálózati forgalom-útválasztási szabályok használatát. 
<BR>

| Tulajdonság | Leírás |
| --- | --- |
| **backendaddresspools készletek** |A háttér-kiszolgálók IP-címek listáját. A felsorolt IP-címek vagy a virtuális hálózati alhálózat kell tartoznia, vagy egy nyilvános IP-cím/VIP vagy a magánhálózati IP-címe legyen. |
| **backendHttpSettingsCollection** |Minden készlethez beállítások, például a portot, a protokoll és a cookie-alapú kapcsolat van. Ezek a beállítások a készletben vannak kötve, és a készletben lévő összes kiszolgálót is vonatkozik |
| **frontendports portok** |Ez a port nem a nyilvános portot nyit meg az Alkalmazásátjáró. Forgalom találatok ezt a portot, és az egyik a háttérkiszolgálók átirányítja beolvasása |
| **httpListeners** |Figyelő rendelkezik egy elülső rétegbeli portot, a protokollt (Http vagy Https, amelyek kis-és nagybetűket), és az SSL-tanúsítvány neve (ha az SSL beállításának kiszervezése) |
| **requestroutingrules szabályok** |A szabály a figyelőt van kötve, és a háttér kiszolgálókészlet befejezését, majd határozza meg, amely vissza end kiszolgálókészlet kell irányítani a forgalmat. Jelenleg csak a ciklikus multiplexelés szerint működik |

Példa: egy alkalmazás átjáró Json-sablont:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location to deploy to"
          }
        },
        "addressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/16",
          "metadata": {
            "description": "Address prefix for the Virtual Network"
          }
        },
        "subnetPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/28",
          "metadata": {
            "description": "Subnet prefix"
          }
        },
        "skuName": {
          "type": "string",
          "allowedValues": [
            "Standard_Small",
            "Standard_Medium",
            "Standard_Large"
          ],
          "defaultValue": "Standard_Medium",
          "metadata": {
            "description": "Sku Name"
          }
        },
        "capacity": {
          "type": "int",
          "defaultValue": 2,
          "metadata": {
            "description": "Number of instances"
          }
        },
        "backendIpAddress1": {
          "type": "string",
          "metadata": {
            "description": "IP Address for Backend Server 1"
          }
        },
        "backendIpAddress2": {
          "type": "string",
          "metadata": {
            "description": "IP Address for Backend Server 2"
          }
        }
      },
      "variables": {
        "applicationGatewayName": "applicationGateway1",
        "publicIPAddressName": "publicIp1",
        "virtualNetworkName": "virtualNetwork1",
        "subnetName": "appGatewaySubnet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
        "publicIPRef": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]",
        "applicationGatewayID": "[resourceId('Microsoft.Network/applicationGateways',variables('applicationGatewayName'))]",
        "apiVersion": "2015-05-01-preview"
      },
      "resources": [
        {
          "apiVersion": "[variables('apiVersion')]",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIPAddressName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic"
          }
        },
        {
          "apiVersion": "[variables('apiVersion')]",
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
          "apiVersion": "[variables('apiVersion')]",
          "name": "[variables('applicationGatewayName')]",
          "type": "Microsoft.Network/applicationGateways",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', variables    ('virtualNetworkName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', variables    ('publicIPAddressName'))]"
          ],
          "properties": {
            "sku": {
              "name": "[parameters('skuName')]",
              "tier": "Standard",
              "capacity": "[parameters('capacity')]"
            },
            "gatewayIPConfigurations": [
              {
                "name": "appGatewayIpConfig",
                "properties": {
                  "subnet": {
                    "id": "[variables('subnetRef')]"
                  }
                }
              }
            ],
            "frontendIPConfigurations": [
              {
                "name": "appGatewayFrontendIP",
                "properties": {
                  "PublicIPAddress": {
                    "id": "[variables('publicIPRef')]"
                  }
                }
              }
            ],
            "frontendPorts": [
              {
                "name": "appGatewayFrontendPort",
                "properties": {
                  "Port": 80
                }
              }
            ],
            "backendAddressPools": [
              {
                "name": "appGatewayBackendPool",
                "properties": {
                  "BackendAddresses": [
                    {
                      "IpAddress": "[parameters('backendIpAddress1')]"
                    },
                    {
                      "IpAddress": "[parameters('backendIpAddress2')]"
                    }
                  ]
                }
              }
            ],
            "backendHttpSettingsCollection": [
              {
                "name": "appGatewayBackendHttpSettings",
                "properties": {
                  "Port": 80,
                  "Protocol": "Http",
                  "CookieBasedAffinity": "Disabled"
                }
              }
            ],
            "httpListeners": [
              {
                "name": "appGatewayHttpListener",
                "properties": {
                  "FrontendIPConfiguration": {
                    "Id": "[concat(variables('applicationGatewayID'), '/    frontendIPConfigurations/appGatewayFrontendIP')]"
                  },
                  "FrontendPort": {
                    "Id": "[concat(variables('applicationGatewayID'), '/    frontendPorts/appGatewayFrontendPort')]"
                  },
                  "Protocol": "Http",
                  "SslCertificate": null
                }
              }
            ],
            "requestRoutingRules": [
              {
                "Name": "rule1",
                "properties": {
                  "RuleType": "Basic",
                  "httpListener": {
                    "id": "[concat(variables('applicationGatewayID'), '/    httpListeners/appGatewayHttpListener')]"
                  },
                  "backendAddressPool": {
                    "id": "[concat(variables('applicationGatewayID'), '/    backendAddressPools/appGatewayBackendPool')]"
                  },
                  "backendHttpSettings": {
                    "id": "[concat(variables('applicationGatewayID'), '/    backendHttpSettingsCollection/    appGatewayBackendHttpSettings')]"
                  }
                }
              }
            ]
          }
        }
      ]    
    }


### <a name="additional-resources"></a>További források
Olvasási [ Alkalmazásátjáró REST API](https://msdn.microsoft.com/library/azure/mt299388.aspx) további információt.

