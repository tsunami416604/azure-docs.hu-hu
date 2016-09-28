### A kapcsolat ellenőrzése a PowerShell használatával

A sikeres kapcsolat ellenőrzéséhez használja a `Get-AzureRmVirtualNetworkGatewayConnection` parancsmagot a `-Debug` argumentummal vagy anélkül. 

1. A következő parancsmag-példával az értékeket a sajátjaival megegyezően konfigurálhatja. Ha a rendszer arra kéri, válassza az „A” lehetőséget az összes futtatásához. A példában a `-Name` a létrehozott és tesztelni kívánt kapcsolat nevére utal.

        Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG

2. A parancsmag futtatása után tekintse meg az értékeket. Az alábbi példában a kapcsolati állapot „Csatlakoztatva”, és láthatja a bemenő és kimenő bájtokat.

        Body:
        {
          "name": "MyGWConnection",
          "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
          "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
            "virtualNetworkGateway1": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
        teways/vnetgw1"
            },
            "localNetworkGateway2": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
        ways/LocalSite"
            },
            "connectionType": "IPsec",
            "routingWeight": 10,
            "sharedKey": "abc123",
            "connectionStatus": "Connected",
            "ingressBytesTransferred": 33509044,
            "egressBytesTransferred": 4142431
          }

### A kapcsolat ellenőrzése az Azure Portal használatával

Az Azure Portalon a kapcsolatra lépve tekintheti meg a kapcsolat állapotát. Ezt többféleképpen megteheti. Az alábbiakban a kapcsolat megkeresésének egyik módját látja.

1. Az [Azure Portalon](http://portal.azure.com) lépjen a **Virtuális hálózati átjárók** területre. Kattintson az átjáró nevére.
2. A panel **Beállítások** területén kattintson a **Kapcsolatok** elemre. Láthatja az egyes kapcsolatok állapotát.
3. A kapcsolat további információinak megtekintéséhez kattintson a kapcsolat nevére. A kapcsolat Alapvető szolgáltatások oldalán figyelje meg a **Kapcsolat állapotát**. Az állapot „Sikeres” vagy „Kapcsolatban” értékű, ha a kapcsolat sikeresen létrejött. Az átmenő adatokat az **Bejövő adatforgalom** és a **Kimenő adatforgalom** mezőben ellenőrizheti.

    Az alábbi példában a **Kapcsolat állapota** értéke „Nincs kapcsolat”. 

    ![Kapcsolat ellenőrzése](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)

<!--HONumber=Sep16_HO4-->


