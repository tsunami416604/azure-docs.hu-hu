1. Az Azure portálon lépjen az **Új** **>** **Hálózatkezelés** **>** **Helyi hálózati átjáró** elemre.

    ![helyi hálózati átjáró létrehozása](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. A **Helyi hálózati átjáró létrehozása** panelen adjon meg egy **Nevet** a helyi hálózati átjáróobjektumnak.
 
3. Adjon meg egy **IP-címet** az átjárójához. Ez annak a VPN-eszköznek az IP-címe, amelyhez csatlakozni kíván. Ez nem lehet a NAT mögött, és elérhetőnek kell lennie az Azure számára.

4. Az **Címtartomány** a helyi (jellemzően helyszíni) hálózatán található címtartományokra utal. Több címtartományt is felvehet. Az itt megadott tartományok nem fedhetnek át olyan címtartományt, amelyet az átjárón keresztül kommunikáló virtuális hálózatok bármelyikéhez használ.  A helyszíni konfigurációkkal és az Azure virtuális hálózat címtartományaival kell koordinálnia.
 
5. Az **Előfizetés** résznél ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg.

6. Az **Erőforráscsoport** beállításnál válassza ki a használni kívánt erőforráscsoportot. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy korábban létrehozottat. Egy új erőforráscsoport létrehozásához írja be a mezőbe a nevet. Egy korábban létrehozott erőforráscsoport kiválasztásához kattintson a **Erőforráscsoport** elemre az **Erőforráscsoport** panel megnyitásához, majd válassza ki a használni kívánt erőforráscsoportot.

7. Ha egy új helyi hálózati átjárót hoz létre, a **Hely** lehetőségnél használhatja ugyanazt a helyet, mint a virtuális hálózat átjárója. Ez azonban nem kötelező. A helyi hálózati átjáró máshol is lehet. 

8. Ha szeretné könnyen megtalálni az irányítópultról ezt a helyi hálózati átjárót, akkor hagyja bejelölve a „Rögzítés az irányítópulton” jelölőnégyzetet.

9. A helyi hálózati átjáró létrehozásához kattintson a **Create** (Létrehozás) gombra. Az irányítópulton megjelenik a „Deploying Local network gateway” (Helyi hálózati átjáró üzembe helyezése) üzenet.

10. A létrejött helyi hálózati átjáró megnyílik a portálon, hogy meg tudja tekinteni.

    



<!--HONumber=Jun16_HO2-->


