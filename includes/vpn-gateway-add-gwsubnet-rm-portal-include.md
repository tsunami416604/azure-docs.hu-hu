1. Keresse meg a portálon azt a virtuális hálózatot, amelyhez csatlakoztatni kíván egy átjárót.

2. A virtuális hálózat paneljének **Beállítások** részén az **Alhálózatok** elemre kattintva bontsa ki az Alhálózatok panelt.

3. Az **Alhálózatok** panel tetején kattintson a **+Átjáróalhálózat** elemre. Ekkor megnyílik az **Alhálózat hozzáadása** panel. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Ez az érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata.

    ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsubnet250.png)

4. Ha szükséges, módosíthatja a címtartomány CIDR-blokkját. Az ajánlott CIDR-blokk megerősítéséhez tekintse át a konfigurációra vonatkozó egyedi követelményeket.

5. A panel alján található **OK** gombra kattintva hozza létre az alhálózatot.






<!--HONumber=sep16_HO1-->


