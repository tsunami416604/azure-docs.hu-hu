### <a name="to-verify-your-connection-by-using-powershell"></a>A kapcsolat ellenőrzése a PowerShell használatával

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

### <a name="to-verify-your-connection-by-using-the-azure-portal"></a>A kapcsolat ellenőrzése az Azure Portal használatával

Az Azure Portalon a kapcsolatra lépve tekintheti meg a kapcsolat állapotát. Ezt többféleképpen megteheti. Az alábbi lépések a kapcsolat megkeresését és ellenőrzését mutatják be.

1. Az [Azure Portalon](http://portal.azure.com) kattintson az **Összes erőforrás** lehetőségre, és keresse meg a virtuális hálózati átjárót.
2. A virtuális hálózati átjáró paneljén kattintson a **Kapcsolatok** lehetőségre. Láthatja az egyes kapcsolatok állapotát.
3. Kattintson az ellenőrizni kívánt kapcsolat nevére az **Alapvető erőforrások** megnyitásához. Az Alapvető erőforrások panelen áttekintheti a kapcsolat további adatait. Az **Állapot** „Sikeres” vagy „Csatlakoztatva” értékű, ha a kapcsolat sikeresen létrejött.

    ![Kapcsolat ellenőrzése](./media/vpn-gateway-verify-connection-rm-include/connectionsucceeded.png)

<!--HONumber=Oct16_HO3-->


