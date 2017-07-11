1. A portál **Minden erőforrás** területén kattintson a **+Hozzáadás** gombra. 
2. A **Mindenben** panel keresőmezőjébe írja be a **Helyi hálózati átjáró** kifejezést, majd kattintson a keresés gombra. Ez visszaad egy listát. Kattintson a **Helyi hálózati átjáró** elemre a panel megnyitásához, majd kattintson a **Létrehozás** elemre a **Helyi hálózati átjáró létrehozása** panel megnyitásához.

  ![helyi hálózati átjáró létrehozása](./media/vpn-gateway-add-lng-s2s-rm-portal-include/createlng.png)

3. A **Helyi hálózati átjáró létrehozása** panelen adja meg a helyi hálózati átjáró értékeit.

  - **Név:** Adja meg a helyi hálózati átjáróobjektum nevét.
  - **IP-cím:** Ez annak a VPN-eszköznek a nyilvános IP-címe, amelyhez az Azure-t csatlakozni kívánja. Adjon meg egy érvényes nyilvános IP-címet. Az IP-cím nem lehet NAT mögött, és elérhetőnek kell lennie az Azure számára. Ha most még nem rendelkezik az IP-címmel, használhatja a képernyőfelvételen látható értékeket, később azonban vissza kell térnie ide, és lecserélni a helyőrző IP-címet a VPN-eszköz nyilvános IP-címére. Ellenkező esetben az Azure nem fog tudni csatlakozni.
  - A **Címtartomány** annak a hálózatnak a címtartományára utal, amelyet a helyi hálózat képvisel. Több címtartományt is felvehet. Ügyeljen arra, hogy az itt megadott címtartományok ne legyenek átfedésben olyan egyéb hálózatok címtartományaival, amelyekhez csatlakozni kíván. Az Azure a helyszíni VPN-eszköz IP-címéhez irányítja át a megadott címtartományt. *A saját értékeit használja itt, ne a képernyőképen látható értékeket*.
  - **Előfizetés:** Ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg.
  - **Erőforráscsoport:** Válassza ki a használni kívánt erőforráscsoportot. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy korábban létrehozottat.
  - **Hely:** Válassza ki a helyet, ahol az objektum létrejön. Érdemes ugyanazt a helyet kiválasztani, ahol a virtuális hálózat is található, de ez nem kötelező.

4. Miután végzett az értékek megadásával, a kattintson a **Létrehozás** gombra a panel alján a helyi hálózati átjáró létrehozásához.