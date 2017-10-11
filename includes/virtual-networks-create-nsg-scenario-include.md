## <a name="scenario"></a>Forgatókönyv
Jobb szemléltetéséhez NSG-k létrehozása, ez a dokumentum az alábbi forgatókönyvet fogja használni.

![VNet-forgatókönyv](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

Ebben a forgatókönyvben egy NSG-t az egyes alhálózatokon hoz létre a **TestVNet** virtuális hálózatban, folyamata az alábbiakban olvasható: 

* **NSG-előtérbeli**. Az NSG alkalmazandó előtér a *előtér* alhálózatot, és két szabályt tartalmaz:    
  * **RDP-szabály**. Ez a szabály lehetővé teszi az RDP-forgalmát a *előtér* alhálózat.
  * **webalkalmazás-szabály**. Ez a szabály lehetővé teszi a HTTP-forgalom a *előtér* alhálózat.
* **NSG-háttérrendszer**. A háttérben NSG alkalmazandó a *háttér* alhálózatot, és két szabályt tartalmaz:    
  * **SQL-szabály**. Ez a szabály lehetővé teszi, hogy az SQL-forgalom csak az *előtér* alhálózat.
  * **webalkalmazás-szabály**. Ez a szabály letiltja az összes internetes forgalom kötött a *háttér* alhálózat.

Ezek a szabályok kombinációját, hozzon létre egy DMZ-szerű forgatókönyv, ahol a háttérrendszer alhálózati is csak kapnak bejövő forgalmat az SQL az előtér-alhálózatot, és nem fér hozzá az internethez az előtérben lévő alhálózat alhálózat az internettel kommunikál, és fogadni bejövő HTTP-kérelmek csak.

