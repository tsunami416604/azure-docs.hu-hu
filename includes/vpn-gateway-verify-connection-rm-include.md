Ha ellenőrizni kíván egy VPN-kapcsolatot az Azure portálon, akkor lépjen a **Virtuális hálózati átjárók** elemre, **>** ***kattintson az átjárója nevére, majd a*** **>** **Beállítások** **>** **Kapcsolatok** elemre. A kapcsolat nevét kiválasztva további információkat jeleníthet meg a kapcsolatról. Az alábbi példában a kapcsolat nincs csatlakoztatva, ezért nincs adatáramlás.


![Kapcsolat ellenőrzése](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)


### A kapcsolat ellenőrzése a PowerShell használatával

A `Get-AzureRmVirtualNetworkGatewayConnection –Debug` használatával ellenőrizheti, hogy sikeresen létrejött-e a kapcsolat. A következő parancsmag-példával az értékeket a sajátjaival megegyezően konfigurálhatja. Ha a rendszer arra kéri, válassza az „A” lehetőséget az összes futtatásához.

    Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 A parancsmag futtatása után görgessen végig az értékeken és tekintse meg őket. Az alábbi példában a kapcsolati állapot *Csatlakoztatva*, és láthatja a bemenő és kimenő bájtokat.

    Body:
    {
      "name": "localtovon",
      "id":
    "/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
    ltovon",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
        "virtualNetworkGateway1": {
          "id":
    "/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
    teways/vnetgw1"
        },
        "localNetworkGateway2": {
          "id":
    "/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
    ways/LocalSite"
        },
        "connectionType": "IPsec",
        "routingWeight": 10,
        "sharedKey": "abc123",
        "connectionStatus": "Connected",
        "ingressBytesTransferred": 33509044,
        "egressBytesTransferred": 4142431
      }


<!--HONumber=Jun16_HO2-->


