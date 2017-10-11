Fontos fontos megjegyezni, hogy egy rendelkezésre állási csoport figyelőjének beállítása az Azure-ban két módja van. A módszer a figyelő létrehozásakor használja az Azure terheléselosztó a következő típusban eltérőek lehetnek. Az alábbi táblázat a különbségeket ismerteti:

| Terheléselosztó típusa | Megvalósítás | A következő esetekben használja: |
| --- | --- | --- |
| **Külső** |Használja a *nyilvános virtuális IP-cím* a felhőalapú szolgáltatás, amely a virtuális gépek (VM) üzemelteti. |A figyelő a virtuális hálózaton, beleértve az internetről kívül az eléréséhez szükséges. |
| **Belső** |Használja az *belső terheléselosztó* a magán-címet a figyelőhöz. |A figyelőt csak az azonos virtuális hálózaton belül érheti el. A hozzáférés a hibrid környezetek telephelyek közötti VPN tartalmazza. |

> [!IMPORTANT]
> Egy figyelő, amely a felhőalapú szolgáltatás nyilvános VIP (külső terheléselosztó), amennyiben az ügyfél használ, a figyelő, és adatbázisokat azonos Azure-régióban, után nem számítunk kilépő díjakat. Ellenkező esetben a figyelő a visszaadott adatok tekinthető kimenő, és normál adatátvitel ütemben fel van töltve. 
> 
> 

Egy ILB csak virtuális hálózatokat regionális hatókörbe és konfigurálhatja. Meglévő virtuális hálózatot, amely egy affinitáscsoporthoz konfigurált egy ILB nem használható. További információkért lásd: [belső load balancer áttekintése](../articles/load-balancer/load-balancer-internal-overview.md).

