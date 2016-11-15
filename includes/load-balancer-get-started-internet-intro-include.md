Az Azure Load Balancer 4. szintű (TCP, UDP) terheléselosztónak minősül. A terheléselosztó a felhőszolgáltatások vagy virtuális gépek kifogástalan állapotú szolgáltatási példányai között osztja meg a bejövő forgalmat egy terheléselosztói készletben, és ezáltal biztosítja a magas rendelkezésre állást. Az Azure Load Balancer a szolgáltatásokat több portra vagy több IP-címre, illetve portokra és IP-címekre egyaránt továbbíthatja.

A terheléselosztót az alábbi feladatokra lehet konfigurálni:

* Bejövő internetes forgalom terheléselosztása virtuális gépek (VM-ek) között. Ebben a forgatókönyvben a terheléselosztó [internetre kapcsolódó terheléselosztót](../articles/load-balancer/load-balancer-internet-overview.md) jelent.
* Forgalom terheléselosztása virtuális hálózat (VNet) virtuális gépei között, felhőszolgáltatások virtuális gépei között, vagy helyszíni számítógépek és létesítmények közötti virtuális hálózat virtuális gépei között. Ebben a forgatókönyvben a terheléselosztó [belső terheléselosztót (ILB)](../articles/load-balancer/load-balancer-internal-overview.md) jelent.
* Külső forgalom továbbítása egy adott VM-példányhoz.


<!--HONumber=Nov16_HO2-->


