## <a name="sample-scenario"></a>Mintaforgatókönyv
Jobb szemléltetéséhez NSG-k kezelése, ez a cikk az alábbi forgatókönyvet használja.

![VNet-forgatókönyv](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

Ebben a forgatókönyvben egy NSG-t az egyes alhálózatokon hoz létre a **TestVNet** virtuális hálózatban, folyamata az alábbiakban olvasható: 

* **NSG-előtérbeli**. Az NSG alkalmazandó előtér a *előtér* alhálózatot, és két szabályt tartalmaz:    
  * **RDP-szabály**. Ez a szabály lehetővé teszi az RDP-forgalmát a *előtér* alhálózat.
  * **webalkalmazás-szabály**. Ez a szabály lehetővé teszi a HTTP-forgalom a *előtér* alhálózat.
* **NSG-háttérrendszer**. A háttérben NSG alkalmazandó a *háttér* alhálózatot, és két szabályt tartalmaz:    
  * **SQL-szabály**. Ez a szabály lehetővé teszi, hogy az SQL-forgalom csak az *előtér* alhálózat.
  * **webalkalmazás-szabály**. Ez a szabály letiltja az összes internetes forgalom kötött a *háttér* alhálózat.

Ezek a szabályok kombinációját, hozzon létre egy DMZ-szerű forgatókönyv, ahol a háttérrendszer alhálózati csak fogadhat SQL forgalom a bejövő forgalom az előtér-alhálózatot, és nem fér hozzá az internethez az előtérben lévő alhálózat alhálózat az internettel kommunikál, és fogadni bejövő HTTP-kérelmek csak.

A fent leírt forgatókönyv telepítéséhez kövesse [Ez a hivatkozás](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG), kattintson a **az Azure telepítéséhez**, cserélje le az alapértelmezett paraméterértékek, ha szükséges, és kövesse az utasításokat a portálon. Az alábbi minta utasításokat, a sablon telepítése egy erőforráscsoport-nevek használatával **RG-NSG**. 

