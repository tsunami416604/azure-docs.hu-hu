A rendelkezésre állási csoport figyelőjének egy IP-cím és a hálózati nevet, amely az SQL Server rendelkezésre állási csoport figyeli. A rendelkezésre állási csoport figyelőjének létrehozásához tegye a következőket:

1. <a name="getnet"></a>A fürt hálózati erőforrás nevének beolvasása.

    a. RDP segítségével csatlakozzon az Azure virtuális géphez, amelyen az elsődleges replika. 

    b. Nyissa meg a Feladatátvevőfürt-kezelőt.

    c. Válassza ki a **hálózatok** csomópont, és figyelje meg a fürt hálózati nevét. Ennek a névnek a `$ClusterNetworkName` változó a PowerShell parancsfájl. Az alábbi ábrán a hálózati fürtnév **fürt hálózati 1**:

   ![Fürt hálózati név](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>Az ügyfél-hozzáférési pont hozzáadása.  
    Az ügyfél-hozzáférési pont a hálózatnév az adatbázisokat a rendelkezésre állási csoportban való kapcsolódáshoz használt alkalmazások. Az ügyfél hozzáférési pontjának létrehozása a Feladatátvevőfürt-kezelőben.

    a. Bontsa ki a fürt nevét, és kattintson a **szerepkörök**.

    b. Az a **szerepkörök** ablaktáblájában kattintson a jobb gombbal a rendelkezésre állási csoport nevét, majd válassza ki **erőforrás hozzáadása** > **ügyfél-hozzáférési pont**.

   ![Ügyfél-hozzáférési pont](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Az a **neve** hozzon létre egy nevet az új figyelőt. 
   Az új figyelőt az neve: a hálózat nevét használják az SQL Server rendelkezésre állási csoportban adatbázisaihoz való kapcsolódásra.
   
    d. A figyelő létrehozásának befejezéséhez kattintson **következő** kétszer, majd **Befejezés**. Nem kapcsolja a figyelő vagy erőforrás online ezen a ponton.

3. <a name="congroup"></a>Az IP-erőforrás a rendelkezésre állási csoport konfigurálása.

    a. Kattintson a **erőforrások** lapot, és bontsa ki a létrehozott ügyfél-hozzáférési pontját.  
    Az ügyfél-hozzáférési pont offline állapotban.

   ![Ügyfél-hozzáférési pont](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Kattintson a jobb gombbal az IP-erőforrás, és kattintson a tulajdonságok. Jegyezze fel az IP-címet, és ezért a `$IPResourceName` változó a PowerShell parancsfájl.

    c. A **IP-cím**, kattintson a **statikus IP-cím**. Állítsa be az IP-cím a címmel, ha úgy állítja be a terheléselosztói címet, az Azure portálon használt.

   ![IP-erőforrás](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

4. <a name = "dependencyGroup"></a>Ellenőrizze az SQL Server rendelkezésre állási csoport erőforrása függ, az ügyfél-csatlakozási pont.

    a. A Feladatátvevőfürt-kezelőben kattintson **szerepkörök**, majd kattintson a rendelkezésre állási csoporthoz.

    b. Az a **erőforrások** lap **egyéb erőforrások**, kattintson a jobb gombbal a rendelkezésre állási csoport, és kattintson a **tulajdonságok**. 

    c. A függőségek lapon adja meg az ügyfél hozzáférési pont (figyelő) erőforrás nevét.

   ![IP-erőforrás](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Kattintson az **OK** gombra.

5. <a name="listname"></a>Ellenőrizze az ügyfél hozzáférési pont erőforrás függ, az IP-címet.

    a. A Feladatátvevőfürt-kezelőben kattintson **szerepkörök**, majd kattintson a rendelkezésre állási csoporthoz. 

    b. Az a **erőforrások** lapra, kattintson a jobb gombbal az ügyfél hozzáférési pont erőforrásra **kiszolgálónév**, és kattintson a **tulajdonságok**. 

   ![IP-erőforrás](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Kattintson a **függőségek** fülre. Győződjön meg arról, hogy az IP-cím egy függőséget. Ha nem, függőség beállítása az IP-címet. Ha nincsenek felsorolva több erőforrást, győződjön meg arról, hogy az IP-címek vagy nem rendelkezik-e és függőségeit. Kattintson az **OK** gombra. 

   ![IP-erőforrás](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    d. Kattintson a jobb gombbal a figyelő nevét, és kattintson **online állapotba hozás**. 

    >[!TIP]
    >Ellenőrizheti, hogy a függőségek megfelelően vannak-e konfigurálva. A Feladatátvevőfürt-kezelőben, nyissa meg a szerepkörökhöz, kattintson a jobb gombbal a rendelkezésre állási csoporthoz, kattintson a **további műveletek**, és kattintson a **függőségi jelentés megjelenítése**. A függőségek megfelelően vannak konfigurálva, amikor a rendelkezésre állási csoport függ a hálózat neve, pedig a hálózati név függ az IP-címet. 


6. <a name="setparam"></a>A fürt paraméterek beállítása a PowerShellben.
    
    a. Másolja a következő PowerShell-parancsfájlt az SQL Server-példányok közül. A változók a környezet frissítése.     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b. Állítsa be a fürt paramétereket a PowerShell-parancsfájl futtatásával a fürtcsomópontok egyike.  

    > [!NOTE]
    > Ha az SQL Server-példány külön régióban, kétszer a PowerShell parancsfájl futtatásához szükséges. Az első alkalommal használja a `$ILBIP` és `$ProbePort` első régióban. A második alkalommal használja a `$ILBIP` és `$ProbePort` második régióban. A fürthálózat nevének és a fürt IP-erőforrás neve megegyezik. 
