1. A portálon lépjen az **Új** > **Hálózatkezelés** > **Helyi hálózati átjáró** elemre.

    ![helyi hálózati átjáró létrehozása](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. A **Helyi hálózati átjáró létrehozása** panelen adjon meg egy **Nevet** a helyi hálózati átjáróobjektumnak.
 
3. Adjon meg egy érvényes nyilvános **IP-címet** azon VPN-eszköz vagy virtuális hálózati átjáró számára, amelyhez csatlakozni kíván.<br>Ha a helyi hálózat egy helyszíni helyet képvisel, ez az IP-cím annak a VPN-eszköznek az IP-címe, amelyhez csatlakozni kíván. Ez nem lehet a NAT mögött, és elérhetőnek kell lennie az Azure számára.<br>Ha a helyi hálózat egy másik virtuális hálózatot képvisel, akkor a virtuális hálózat virtuális hálózati átjárójához rendelt nyilvános IP-címet kell megadnia.<br>

4. A **Címtartomány** annak a hálózatnak a címtartományára utal, amelyet a helyi hálózat képvisel. Több címtartományt is felvehet. Ügyeljen arra, hogy az itt megadott címtartományok ne legyenek átfedésben olyan egyéb hálózatok címtartományaival, amelyekhez csatlakozni kíván.
 
5. Az **Előfizetés** résznél ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg.

6. Az **Erőforráscsoport** beállításnál válassza ki a használni kívánt erőforráscsoportot. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy korábban létrehozottat.

7. A **Hely** beállításnál válassza ki a helyet, ahol az objektum létrejön. Érdemes ugyanazt a helyet kiválasztani, ahol a virtuális hálózat is található, de ez nem kötelező.

8. A helyi hálózati átjáró létrehozásához kattintson a **Create** (Létrehozás) gombra.



<!--HONumber=sep16_HO1-->


