#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Nyilvános végpontok létrehozása a felhőalapú készüléken

1. Jelentkezzen be az Azure portálra.
2. Lépjen a **Virtuális gépek** elemre, majd válassza ki a felhőalapú készülékként használt virtuális gépet, és kattintson rá.
    
3. Létre kell hozni egy hálózati biztonság csoportra (network security group, NSG) vonatkozó szabályt a virtuális gépbe és gépből irányuló forgalom szabályozásához. Az NSG-re vonatkozó szabály létrehozásához hajtsa végre az alábbi lépéseket.
    1. Válassza a **Hálózati biztonsági csoport** lehetőséget.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Kattintson a megjelenő alapértelmezett hálózati biztonsági csoportra.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Válassza a **Bejövő biztonsági szabályok** elemet.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Kattintson a **+ Hozzáadás** gombra egy bejövő biztonsági szabály létrehozásához.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        A Bejövő biztonsági szabály felvétele panelen:

        1. A **Név** mezőbe írja be a végponthoz a következő nevet: WinRMHttps.
        
        2. A **Prioritás** értékhez válasszon egy 1000-nél (amely az alapértelmezett szabálynak van fenntartva) kisebb számot. Minél magasabb az érték, annál alacsonyabb a prioritás.

        3. A **Forrás** elemet állítsa **Bármely** értékre.

        4. A **Szolgáltatásnál** válassza a **WinRM** lehetőséget. A **Protokoll** értéke automatikusan **TCP** lesz, a **Porttartomány** értéke pedig **5986**.

        5. A szabály létrehozásához kattintson az **OK** gombra.

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. Az utolsó lépés a hálózati biztonsági csoport társítása egy alhálózattal vagy egy adott hálózati adapterrel. Ha a hálózati biztonsági csoportot egy alhálózattal szeretné társítani, végezze el a következő lépéseket.
    1. Nyissa meg az **Alhálózatok** részt.
    2. Kattintson a **+ Társítás** gombra.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Válassza ki a virtuális hálózatot, majd válassza ki a megfelelő alhálózatot.
    4. A szabály létrehozásához kattintson az **OK** gombra.

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Ha létrehozta a szabályt, a részletek között megnézheti a nyilvános virtuális IP-címet (VIP). Jegyezze fel ezt az IP-címet.


