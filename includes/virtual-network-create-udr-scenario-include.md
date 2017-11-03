## <a name="scenario"></a>Forgatókönyv
Jobb mutatja be udr-EK létrehozása, ez a dokumentum az alábbi forgatókönyvet fogja használni.

![KÉPLEÍRÁS](./media/virtual-network-create-udr-scenario-include/figure1.png)

Ebben a forgatókönyvben a egy UDR hoz létre a *első záró alhálózat* és az egy másik UDR a *vissza záró alhálózat* , az alábbiak szerint: 

* **UDR-előtérbeli**. Az előtér UDR alkalmazandó a *előtér* alhálózati, egy útvonalat, és:    
  * **RouteToBackend**. Ez az útvonal által küldött összes forgalom a háttérrendszer alhálózat a **FW1** virtuális gépet.
* **UDR-háttérrendszer**. A háttérben UDR alkalmazandó a *háttér* alhálózati, egy útvonalat, és:    
  * **RouteToFrontend**. Ez az útvonal által küldött összes forgalom az előtér-alhálózat a **FW1** virtuális gépet.

Ezeket az útvonalakat kombinációja biztosítja, hogy irányuló teljes forgalomra egy alhálózatból másik továbbítja a **FW1** virtuális gépet, mely a virtuális készülék használatos. Szükség kapcsolja be ezt a virtuális Gépet, annak érdekében, hogy más virtuális gépek irányuló forgalom fogadja az IP-továbbítást.

