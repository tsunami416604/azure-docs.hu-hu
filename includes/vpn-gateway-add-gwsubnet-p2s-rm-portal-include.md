1. Keresse meg a [portálon](http://portal.azure.com) azt a Resource Manager-alapú virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.
2. A VNet lap **Beállítások** részén az **Alhálózatok** elemre kattintva bontsa ki az **Alhálózatok** oldalt.
3. Az **Alhálózatok** lap **+Átjáróalhálózat** elemére kattintva nyissa meg az **Alhálózat hozzáadása** lapot. 

  ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Az átjáró alhálózatának hozzáadása")
4. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Ez az érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata. Módosítsa úgy a **címtartomány** automatikusan kitöltött értékeit, hogy megfeleljenek a konfigurációs követelményeinek, majd a lap alján található **OK** gombra kattintva hozza létre az alhálózatot.

  ![Az alhálózat hozzáadása](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Az alhálózat hozzáadása")