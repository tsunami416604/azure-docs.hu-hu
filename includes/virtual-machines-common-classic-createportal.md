

Az *egyéni* virtuális gép egyszerűen egy olyan virtuális gép, amelyet a **Marketplace** egy **kiemelt alkalmazásával** hoz létre, mert elvégzi a munka oroszlánrészét Ön helyett. Emellett továbbra is több konfigurációs lehetőség közül választhat. Ezek többek között a következők:

* A virtuális gép csatlakoztatása virtuális hálózathoz.
* Az Azure virtuálisgép-ügynök és Azure virtuális gépi bővítmények (pl. kártevőirtók) telepítése.
* A virtuális gép hozzáadása meglévő felhőszolgáltatásokhoz.
* A virtuális gép hozzáadása meglévő tárfiókhoz.
* A virtuális gép hozzáadása egy rendelkezésre állási csoporthoz.

<!--
> [!IMPORTANT]
> If you want your virtual machine to use a virtual network so you can connect to it directly by host name or set up cross-premises connections, make sure that you specify the virtual network when you create the virtual machine. A virtual machine can be configured to join a virtual network only when you create the virtual machine. For details on virtual networks, see [Azure Virtual Network overview](../articles/virtual-network/virtual-networks-overview.md).
>
>
 -->

> [!IMPORTANT]
> Ha azt szeretné, hogy a virtuális gép virtuális hálózatot használjon, ügyeljen rá, hogy megadja a virtuális hálózatot a virtuális gép létrehozásakor.

> * A virtuális hálózatok használatának két előnye, hogy közvetlenül csatlakozhat a virtuális géphez, valamint hogy létesítmények közötti kapcsolatokat is beállíthat.

> * A virtuális gépek csak a létrehozásuk során konfigurálhatók virtuális hálózathoz való csatlakozásra. A virtuális hálózatokkal kapcsolatos további információk: [Az Azure Virtual Network áttekintése](../articles/virtual-network/virtual-networks-overview.md).
>
>

## <a name="to-create-the-virtual-machine"></a>A virtuális gép létrehozása


<!--HONumber=Feb17_HO3-->


