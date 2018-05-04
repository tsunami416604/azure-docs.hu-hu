---
title: Az Azure Load Balancer áttekintése |} Microsoft Docs
description: Azure Load Balancer funkciók, architektúrájának és megvalósítási áttekintése. Ismerje meg, hogyan működik, a terheléselosztó és a felhőben kihasználja azt.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 2d9e0fc50bed4e8301a24a062407b490d688803d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-load-balancer"></a>Mi az Azure Load Balancer?

Az Azure Load Balancer az alkalmazások, és hozzon létre magas rendelkezésre állású szolgáltatások. Terheléselosztó bejövő és kimenő forgatókönyveket támogat, biztosít alacsony késéssel és magas teljesítmény és méretezi akár több millió flow TCP és UDP-alkalmazásokhoz.  

Terheléselosztó új bejövő forgalom, amely az ügyfélszámítógépekre érkeznek a terheléselosztó előtér-háttér-készlet példányokhoz, szabályok és állapotteljesítmény alapján terjeszti. 

Továbbá egy nyilvános terheléselosztó biztosíthat a virtuális gépek (VM) kimenő kapcsolatok a virtuális hálózaton belüli a nyilvános IP-címek magánhálózati IP-címek fordítása.

Az Azure Load Balancer érhető el két termékváltozatok: Basic és Standard. Méretezés, a szolgáltatások és a díjszabás különbségek vannak. Olyan forgatókönyv, amely alapszintű terheléselosztó tesz lehetővé is létrehozhatók szabványos terheléselosztással, bár némileg eltérőek lehetnek megközelítések. Megismerkedhet a terheléselosztót, mert fontos Ismerkedjen meg a alapelvek betartására és a Termékváltozat-specifikus eltéréseket.

## <a name="why-use-load-balancer"></a>Miért használ a Load Balancer? 

Az Azure Load Balancer használhatja:

* Terheléselosztásához bejövő internetes forgalmat a virtuális gépek. Ez a konfiguráció is ismert, egy [nyilvános terheléselosztó](#publicloadbalancer).
* A virtuális hálózaton belüli virtuális gépek közötti forgalom terheléselosztásához. A betöltési terheléselosztó előtér egy hibrid forgatókönyvben egy a helyszíni hálózatból is elérhető. Mindkét forgatókönyvet is ismert, konfigurációt alkalmaz egy [belső terheléselosztó](#internalloadbalancer).
* Port előre forgalmat egy adott portot a bejövő hálózati cím címfordítási (NAT) szabályait adott virtuális gépeken.
* Adjon meg [kimenő kapcsolat](load-balancer-outbound-connections.md) egy nyilvános terheléselosztó használatával a virtuális hálózaton belüli virtuális gépek esetén.


>[!NOTE]
> Azure teljes körűen felügyelt terheléselosztási megoldások együttesét nyújtja a forgatókönyvek esetén. Ha a Transport Layer Security (TLS) protokoll lezárást ("SSL kiszervezési") vagy egy-HTTP/HTTPS-kérést, alkalmazásréteg-feldolgozási keres, tekintse át a [Alkalmazásátjáró](../application-gateway/application-gateway-introduction.md). Ha a globális DNS a(z) terheléselosztást, tekintse át [Traffic Manager](../traffic-manager/traffic-manager-overview.md). A végpont forgatókönyvek előnye származhat ezek a megoldások kombinálásával, igény szerint.

## <a name="what-are-load-balancer-resources"></a>Mik azok a load balancer erőforrásokat?

Terheléselosztó erőforrást egy nyilvános terheléselosztó vagy a belső terheléselosztót is létezik. A load balancer erőforrás funkciók egész előtér, egy szabályt, a állapotmintáihoz és a háttér-készlet definíciójának beolvasása. Virtuális gépek a háttér-készletbe helyezze el a virtuális gépről a háttér-készlet megadásával.

Load balancer erőforrások objektumai belül, amely akkor is express hogyan Azure kell programot, a több-bérlős-infrastruktúrát a létrehozni kívánt forgatókönyvben. Nincs közvetlen kapcsolat load balancer erőforrások és a tényleges infrastruktúra között. Terheléselosztó létrehozása nem hozzon létre egy példányt, és a kapacitás mindig érhető el. 

## <a name="fundamental-load-balancer-features"></a>Terheléselosztó alapvető funkciók

Terheléselosztó TCP és UDP-alkalmazásokhoz a következő alapvető lehetőségeket biztosítja:

* **Terheléselosztás**

    Az Azure terheléselosztó terjeszteni érkező forgalmat, előtér-háttér-készlet példányokhoz terheléselosztási szabályt hozhat létre. Terheléselosztó bejövő forgalom terjesztéshez kivonat-alapú algoritmust használ, és háttér-készlet példányokhoz flow fejlécek újraírja ennek megfelelően. A kiszolgáló áll rendelkezésre új adatfolyamok fordul elő, ha egy állapotmintáihoz jelzi a háttér-kifogástalan állapotú végpontok.
    
    Alapértelmezés szerint a terheléselosztó IP-forráscím, a forrásport, a cél IP-cím, a célport és a IP-protokollszáma álló 5 rekordos kivonatot használ adatfolyamok hozzárendelése elérhető kiszolgálók. Ha szeretné, hozzon létre egy adott IP-forráscím való affinitását engedélyezés a 2 vagy 3-rekordot kivonatát egy megadott szabály. Az azonos csomagátvitellel az összes csomagot az elosztott terhelésű előtér mögött azonos példányon lévő érkeznek. Ha az ügyfél indít el egy új módszer a azonos forrás IP-címről, a port az adatforrás-módosításokkal. Ennek eredményeképpen a 5 rekordos okozhat a forgalom egy másik háttér-végpont ugorhat.

    További információkért lásd: [terheléselosztó terheléselosztási mód](load-balancer-distribution-mode.md). Az alábbi képen a kivonat-alapú terjesztési jeleníti meg:

    ![Kivonat-alapú terjesztési](./media/load-balancer-overview/load-balancer-distribution.png)

    *Ábra: Kivonat-alapú terjesztési*

* **Port átirányítása**

    A terheléselosztó bejövő NAT-szabályban létrehozhat a port forgalom egy adott portot a virtuális hálózaton belül egy adott háttér-példány a megadott előtér-IP-cím az adott port. Ez is úgy érhető el, mint a terheléselosztás azonos kivonat-alapú terjesztési. Általános példák ezt a képességet a az Azure virtuális hálózaton belüli Virtuálisgép-példányokhoz Remote Desktop Protocol (RDP) vagy a Secure Shell (SSH) munkameneteket. Több belső végpont leképezheti a különféle portokat a azonos előtérbeli IP-cím. Az interneten egy további jump mező a nélkül a virtuális gépek távoli kezelésére használhatja őket.

* **Független és átlátható alkalmazás**

    Terheléselosztó közvetlenül kommunikál TCP vagy UDP vagy az alkalmazási rétegre és bármely TCP vagy UDP-alapú forgatókönyvet támogatja. Például bár a Load Balancer nem szünteti meg magát a TLS, létrehozhatja és TLS alkalmazások a horizontális felskálázáshoz használ a Load Balancer és majd állítsa le a TLS-kapcsolatot, a virtuális Gépre magát. Terheléselosztó nem szünteti meg a folyamat, és a protokoll kézfogások mindig közvetlenül az ügyfél és a háttér-készlet kivonatoló által kiválasztott példánya között történik. Például a TCP-kézfogás mindig akkor fordul elő, az ügyfél és a kijelölt háttér-virtuális gép között. A rendszer egy választ, amely a háttér-virtuális gép jön létre egy előtér kérelmet egy választ. Load Balancer kimenő hálózati teljesítmény csak a virtuális gép Termékváltozat választja korlátozza, és változatlan marad életben hosszabb ideig Ha soha nem éri el az üresjárati időkorlát.

* **Automatikus újrakonfigurálása**

    Terheléselosztó azonnal újrakonfigurálása akkor példányok felfelé vagy lefelé. Hozzáadását vagy eltávolítását a virtuális gépek a háttér-készletből átállítja a terheléselosztóhoz, további műveleteket végez a terheléselosztó erőforrást nélkül.

* **Állapotteljesítmény**

     Annak megállapításához, a háttér-készletben található példányok állapotát, a terheléselosztók állapotfigyelő mintavételt, amelyet Ön meg. Egy mintavételt nem válaszol, amikor a terheléselosztó leállítja az új kapcsolatok küld a nem megfelelő példányok. Nem érinti a meglévő kapcsolatokat, és mindaddig, amíg az alkalmazás leállása a folyamat üresjárati időkorlátot következik be, vagy a virtuális gép le van állítva.

    Három típusú mintavételt támogatja:

    - **Egyéni HTTP-vizsgálatot**: Ez a Hálózatfigyelő hozhat létre a saját egyéni logika egy háttér címkészletet példány állapotának meghatározására. A terheléselosztó rendszeresen ellenőrzi a végpont (alapértelmezés szerint minden 15 másodperc). A példány kifogástalan, ha az időkorlát (alapértelmezett 31 másodperc) belül az válaszol egy HTTP 200 tekinthető. Bármely állapota nem 200-as HTTP azt eredményezi, ez a Hálózatfigyelő sikertelen lesz. Ez a Hálózatfigyelő esetén is hasznos végrehajtási példány eltávolítása a terheléselosztó Elforgatás saját logikát. Konfigurálhatja például, a példány visszatérési nem 200 állapotot, ha a példány nagyobb, mint 90 %-át Processzor.  Ez a Hálózatfigyelő felülbírálja az alapértelmezett Vendég ügynök mintavétel.

    - **Egyéni TCP-Hálózatfigyelővel**: Ez a Hálózatfigyelő támaszkodik egy meghatározott mintavételi portot sikeres TCP munkamenetet létesít. Mindaddig, amíg a virtuális Gépen a megadott figyelő létezik, ez a Hálózatfigyelő sikeres lesz. A rendszer elutasította a kapcsolatot, ha a mintavételi sikertelen lesz. Ez a Hálózatfigyelő felülbírálja az alapértelmezett Vendég ügynök mintavétel.

    - **Vendég ügynök mintavétele (platformon, csak a [PaaS] service virtuális gépeken)**: A terheléselosztót is használhatják a vendégügynököt a virtuális Gépen belül. A vendégügynök figyeli, és válaszol egy HTTP 200 OK választ, csak akkor, ha a példány a kész állapotban van. Ha az ügynök nem válaszol egy HTTP 200 OK, a terheléselosztó jelöli meg a példány válaszol, és leállítja a forgalom küldése annak a példánynak. A load balancer továbbra is próbálják elérni a példányt. Ha a vendégügynök válaszol egy HTTP 200, a terheléselosztó küld forgalom példánynak újra. Vendég ügynök mintavételt végső esetben végezze el, és nem kell használni, amikor HTTP vagy TCP egyéni mintavételi konfigurációk lehetségesek. 
    
* **Kimenő kapcsolatok (forrás NAT)**

    A terheléselosztó előtérbeli IP-cím fordítható minden kimenő irányából magánhálózati IP-címek a virtuális hálózaton belüli nyilvános IP-címeket az interneten. Ha egy nyilvános front-end vállalja egy terheléselosztási szabályt egy háttér-virtuális gép van kötve, Azure programok kimenő kapcsolatokat automatikusan lefordítani a nyilvános front-end IP-cím. A forrás hálózati Címfordítás (SNAT) is nevezik. SNAT fontos előnyökkel jár:

    * Lehetővé teszi egyszerű és vész-helyreállítási szolgáltatások, mert az előtér dinamikusan képezhető le a szolgáltatást egy másik példánya.
    * Ez egyszerűbbé teszi a hozzáférés-vezérlési lista (ACL) felügyeletet. Előtér-IP-címek kifejezett hozzáférés-vezérlési listák nem szolgáltatások méretezési, felfelé vagy lefelé változtatják meg az beszerzése újratelepítése.

    További információkért lásd: [kimenő kapcsolatok](load-balancer-outbound-connections.md).

Standard terheléselosztó további Termékváltozat-specifikus képességekkel rendelkezik túl ezek alapjait. Tekintse át a jelen cikkben alább további része.

## <a name="skus"></a> Terheléselosztó SKU összehasonlítás betöltése

Terheléselosztó a Basic és Standard termékváltozat, minden egyes eltérő forgatókönyv méretezési, szolgáltatások, és az árképzés terén is támogatja. Olyan forgatókönyv, amely alapszintű terheléselosztó tesz lehetővé, valamint szabványos terheléselosztó hozhatók létre. Valójában az API-kat a mindkét termékváltozatok hasonló és meghívott keresztül egy SKU megadását. Az API-t támogató termékváltozatok terheléselosztó és a nyilvános IP-cím áll rendelkezésre a 2017-08-01 API-val kezdve. Mindkét termékváltozatok azonos általános API és struktúra rendelkezik.

Azonban attól függően, hogy mely SKU választja, a teljes forgatókönyv konfigurációs eltérőek lehetnek, némileg. Load Balancer dokumentáció meghívja, amikor egy cikk csak egy adott SKU vonatkozik. Összehasonlítása, és különbségek megismeréséhez lásd az alábbi táblázatot. További információkért lásd: [szabványos Load Balancer áttekintése](load-balancer-standard-overview.md).

>[!NOTE]
> Ha egy újabb kialakítási forgatókönyv használ, érdemes lehet szabványos terheléselosztót. 

Önálló virtuális gépek, a rendelkezésre állási készletek és a Virtuálisgép-méretezési készlet csak egy másikra, soha nem is csatlakozhat. Használatakor őket egy nyilvános IP-címek, mind a Terheléselosztóról, mind a nyilvános IP-cím SKU meg kell egyeznie. Terheléselosztó és a nyilvános IP-SKU nincsenek változtatható.

_Ajánlott eljárás, explicit módon, adja meg a SKU annak ellenére, hogy még nem kötelező._  Ilyenkor a szükséges változtatásokat tartják minimális. Ha nincs megadva a Termékváltozat, az alapszintű Termékváltozat 2017-08-01 API verzióját használja szándéka kerül értelmezésre.

>[!IMPORTANT]
>Standard terheléselosztó egy új terheléselosztó termék és nagy mértékben alapvető terheléselosztó felülbírálja. A két termék között fontos és szándékos különbségek vannak. A végpont forgatókönyvekben, amelyek alapvető terheléselosztó tesz lehetővé is szabványos terheléselosztó hozhatja létre. Ha most már használt alapvető terheléselosztóhoz, tanulmányozza át a legutóbbi változtatásokat a Standard és a Basic és a művelet hatása között viselkedésének megértése szabványos terheléselosztással. Ez a szakasz célszerű gondosan felülvizsgálni.

| | [Standard Termékváltozat](load-balancer-standard-overview.md) | Alapszintű termékváltozat |
| --- | --- | --- |
| Háttér-készlet mérete | Legfeljebb 1000 példányokat. | Legfeljebb 100 példányokat. |
| Háttér-készlet végpontok | A virtuális gép egyetlen virtuális hálózatban, beleértve a virtuális gépek, a rendelkezésre állási készletek és a Virtuálisgép-méretezési készlet keverékéből. | Virtuális gépek egyetlen rendelkezésre állási csoport vagy Virtuálisgép-méretezési készlet. |
| Azure-beli rendelkezésre állási zónák | Zónaredundáns és zonal előtér-webkiszolgálóinak a bejövő és kimenő, kimenő Attribútumfolyam-megfeleltetéseket után is megmaradnak a zóna hiba, terheléselosztás határokon zóna. | / |
| Diagnosztika | Azure figyelő többdimenziós mérőszámainkat, például a byte és csomag számlálók, egészségügyi mintavételi állapot, kapcsolódási kísérletek (TCP SZIN), kimenő kapcsolat állapota (SNAT sikeres és sikertelen adatfolyamok), az aktív adatforrás vezérlősík mérések. | A nyilvános Azure Naplóelemzés betölteni a terheléselosztó csak, a SNAT Erőforrásfogyás riasztást, a háttér-készlet állapotfigyelő száma. |
| Magas rendelkezésre ÁLLÁSÚ portok | Belső terheléselosztó. | / |
| Alapértelmezés szerint biztonságos | Alapértelmezés szerint le a következő nyilvános IP-cím és a betöltés terheléselosztó végpontok. A forgalmat a hálózati biztonsági csoport explicit módon engedélyezett entitások kell használni. | Alapértelmezett nyissa meg a hálózati biztonsági csoport nem kötelező. |
| Kimenő kapcsolatok | Több első szabály lemondáshoz végződik. Egy kimenő forgatókönyv _kell_ explicit módon hozhatók létre a virtuális Gépet fogja tudni használni a kimenő kapcsolat. [Virtuális hálózati Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md) kimenő kapcsolat nélkül elérhető, és nem számítanak a feldolgozott adatok felé. Kimenő kapcsolatok és a feldolgozott adatok beleszámít keresztül érhető el a nyilvános IP-címek, beleértve, amelyek nem érhetők el a virtuális hálózati szolgáltatás végpontként Azure PaaS-szolgáltatásokat. Ha csak egy belső elosztott terhelésű van egy virtuális Gépet szolgáltató, alapértelmezett SNAT keresztül kifelé irányuló kapcsolatok nem érhetők el. Kimenő SNAT programozási protokoll specifikus, a protokoll, a bejövő terheléselosztási szabály alapján. | Egyetlen előtér, ha több előtér-webkiszolgálóinak meglévő véletlenszerűen kiválasztott. Ha csak egy belső elosztott terhelésű van egy virtuális Gépet szolgáltató, az alapértelmezett SNAT használja. |
| Több előtér-webkiszolgálóinak | Bejövő és kimenő. | Csak a bejövő. |
| Felügyeleti műveletek | A legtöbb műveletek < 30 másodperc. | 60-90 másodpercet jellemző. |
| SLA | 99,99 olyan adatelérési utat két kifogástalan állapotú virtuális gépek százalékos értéke. | A virtuális gép SLA implicit. | 
| Díjszabás | Díjak alapuló szabályok száma és adatfeldolgozási bejövő vagy kimenő társított az erőforrás.  | Nem kell fizetni. |

További információkért lásd: [szolgáltatási korlátait terheléselosztóhoz](https://aka.ms/lblimits). Standard terheléselosztó részletekért lásd: [áttekintése](load-balancer-standard-overview.md), [árképzési](https://aka.ms/lbpricing), és [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Alapelvek

### <a name = "publicloadbalancer"></a>Nyilvános terheléselosztó

Egy nyilvános terheléselosztó van leképezve a nyilvános IP-cím és port száma bejövő forgalmat a magánhálózati IP-cím és port számát a virtuális gép, és ez fordítva is igaz a válasz forgalmat a virtuális gépről. Terheléselosztási szabályok alkalmazásával megadott típusú forgalom szét több virtuális gépeket vagy szolgáltatásokat. Webes kérelem forgalom terhelését például több webkiszolgáló között is terjednek.

Az alábbi ábrán egy elosztott terhelésű végpont a webes forgalomban, amelyet használ a nyilvános és titkos TCP 80-as portot a három virtuális géppel. Három virtuális gépeken vannak egy elosztott terhelésű készlet.

![Példa nyilvános terheléselosztóra](./media/load-balancer-overview/IC727496.png)

*Ábra: Webes forgalom terheléselosztása betölteni egy nyilvános terheléselosztó használatával*

Amikor internetes ügyfelek weblap kérést küld a webes alkalmazás 80-as TCP-porton a nyilvános IP-címe, Azure terheléselosztó a kérelmek elosztása a három virtuális gépeket az elosztott terhelésű készlet. A load balancer algoritmusok kapcsolatos további információkért lásd: a [load balancer áttekintése lapon](load-balancer-overview.md#load-balancer-features).

Alapértelmezés szerint az Azure Load Balancer osztja el a hálózati forgalom több Virtuálisgép-példány között. Munkamenet-kapcsolatot is konfigurálhatja. További információkért lásd: [terheléselosztó terheléselosztási mód](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Belső terheléselosztó

A belső terheléselosztók csak erőforrásokat, amelyek a virtuális hálózaton belül, vagy Azure-infrastruktúra eléréséhez használja, amely a VPN-forgalom irányítja. Ebben a tekintetben a belső terheléselosztók nyilvános terheléselosztó eltér. Azure-infrastruktúra korlátozza a hozzáférést az elosztott terhelésű előtér-IP-címek egy virtuális hálózat. Előtér-IP-címek és virtuális hálózatok soha nem érhetők el közvetlenül egy internet-végponthoz. Belső üzleti alkalmazások futtatása az Azure-ban, és Azure-ban vagy a helyszíni erőforrások érhetők el.

A belső terheléselosztók lehetővé teszi, hogy a következő típusú terheléselosztási:

* **A virtuális hálózaton belül**: terheléselosztás virtuális gépek a virtuális hálózat, amely az azonos virtuális hálózaton belüli virtuális gépek csoportjaira.
* **A létesítmények közötti virtuális hálózat**: terheléselosztás a helyszíni számítógépek, amelyek az azonos virtuális hálózaton belüli virtuális gépek csoportjaira. 
* **A többrétegű alkalmazások**: terheléselosztás, ahol a háttér-rétegek nincsenek internetre internetre Többrétegű alkalmazások. A háttér-rétegek forgalom-terheléselosztást igénylő az internetre irányuló réteg (lásd a következő ábrát).
* **Az üzletági alkalmazások**: az üzletági alkalmazásokhoz, amelyek az Azure-ban nélkül további load balancer hardver- vagy a terheléselosztás. Ez az eset tartalmazza a helyszíni kiszolgálók, amelyek a számítógépet, amelynek a forgalmát terhelésű készletében.

![Példa belső terheléselosztóra](./media/load-balancer-overview/IC744147.png)

*Ábra: Terheléselosztás több rétegből álló alkalmazások mindkét nyilvános és a belső terheléselosztók használatával*

## <a name="pricing"></a>Díjszabás
Normál terheléselosztó használatára beállított terheléselosztási szabályok számának és a feldolgozott bejövő és kimenő adatok mennyisége alapján számítjuk fel. Standard terheléselosztó árakról, látogasson el a [terheléselosztó árképzési](https://azure.microsoft.com/pricing/details/load-balancer/) lap.

Alapszintű terheléselosztó tartományregisztráció ingyenesen.

## <a name="sla"></a>SLA

A szokásos terhelés terheléselosztó SLA kapcsolatos információkért látogasson el a [Load Balancer SLA](https://aka.ms/lbsla) lap. 

## <a name="next-steps"></a>További lépések

Most már rendelkezik Azure Load Balancer áttekintése. A terheléselosztó megkezdéséhez, hozzon létre egyet, és virtuális gépek egyéni IIS-bővítmény telepítése, és elosztani a kéréseket a webes alkalmazás a virtuális gépek között. Megtudhatja, hogyan, tekintse meg a [hozzon létre egy alapszintű terheléselosztó](quickstart-create-basic-load-balancer-portal.md) gyors üzembe helyezés.
