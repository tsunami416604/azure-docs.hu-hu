1. Keresse meg a portálon azt a virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.
2. A VNet lap **Beállítások** részén az **Alhálózatok** elemre kattintva bontsa ki az Alhálózatok oldalt.
3. Az **Alhálózatok** lap tetején lévő **+Átjáró-alhálózat** elemre kattintva nyissa meg az **Alhálózat hozzáadása** lapot.

  ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addgwsub.png "Az átjáró alhálózatának hozzáadása")
4. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Az GatewaySubnet érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata. Módosítsa úgy a **címtartomány** automatikusan kitöltött értékeit, hogy megfeleljenek a konfigurációs követelményeinek.

  ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addsub2.png "Az átjáró alhálózatának hozzáadása")
5. Az alhálózat létrehozásához kattintson a lap alján található **OK** gombra.