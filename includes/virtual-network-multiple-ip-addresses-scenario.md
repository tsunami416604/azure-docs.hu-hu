## <a name="scenario"></a>Forgatókönyv
Virtuális gép egyetlen hálózati adapter és létrehozása és egy virtuális hálózathoz csatlakoztatva van. A virtuális gép igényel három különböző *titkos* IP címeket és a két *nyilvános* IP-címeket. Az IP-címek vannak rendelve a következő IP-konfigurációk:

* **IPConfig-1:** hozzárendel egy *statikus* magánhálózati IP-címet és egy *statikus* nyilvános IP-cím.
* **IPConfig-2:** hozzárendel egy *statikus* magánhálózati IP-címet és egy *statikus* nyilvános IP-cím.
* **IPConfig-3:** hozzárendel egy *statikus* magánhálózati IP-cím és a nyilvános IP-cím.
  
    ![Több IP-cím](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

Az IP-konfigurációk esetén a hálózati adapterhez társított hálózati adapter jön létre, és a hálózati Adaptert a virtuális Géphez van csatolva a virtuális gép létrehozásakor. Az a forgatókönyvhöz használt IP-címek vannak az ábrán látható. Rendelhet, hogy bármilyen IP cím és a hozzárendelés típusa van szüksége.

> [!NOTE]
> Bár a cikkben leírt lépéseket az összes IP-konfigurációk rendel hozzá egyetlen hálózati adapter, is rendelhet több IP-konfigurációk a hálózati adapterek virtuális gépen több hálózati Adapterrel. Több hálózati adapterrel rendelkező virtuális gép létrehozása, olvassa el a [több hálózati adapterrel rendelkező virtuális gép létrehozása](../articles/virtual-network/virtual-network-deploy-multinic-arm-ps.md) cikk.