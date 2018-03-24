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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: 4f46e796ff1ab85c0061c70ff9a725a6945a4f5d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-load-balancer-overview"></a>Az Azure Load Balancer áttekintése

Az Azure Load Balancer lehetővé teszi az alkalmazások és a szolgáltatások magas rendelkezésre állás létrehozása. Terheléselosztó bejövő, valamint az olyan kimenő-forgatókönyveket teszi lehetővé, és kis késés, nagyobb teljesítményt nyújt, és akár több millió összes TCP és UDP-alkalmazás flow méretezi.   

Terheléselosztó fogja kiosztani a terheléselosztó előtérbeli háttér címkészletet példányokhoz szabályok és állapotteljesítmény megfelelően érkező új bejövő forgalom.  

Továbbá egy nyilvános terheléselosztó is biztosíthat a virtuális hálózaton belüli virtuális gépek kimenő kapcsolatok a nyilvános IP-címek magánhálózati IP-címek fordítása.

Az Azure Load Balancer érhető el két különböző Termékváltozatai: Basic és Standard.  Méretezés, a szolgáltatások és a díjszabás különbségek vannak.  Olyan alapvető terheléselosztással lehetséges forgatókönyv is létrehozhatók szabványos terheléselosztással, bár a megközelítést némileg eltérőek lehetnek.  Megismerkedhet a terheléselosztót, mert fontos Ismerkedjen meg a alapelvek betartására és a Termékváltozat-specifikus eltéréseket.

## <a name="why-use-load-balancer"></a>Miért használ a Load Balancer? 

Az Azure Load Balancer a következőkre használható:

* Egyenleg bejövő internetes forgalmat a virtuális gépek betölteni. Ez a konfiguráció is ismert, egy [nyilvános terheléselosztót](#publicloadbalancer).
* Betöltési oszthatja el a forgalmat a virtuális hálózaton belüli virtuális gépek között. A terheléselosztó előtérbeli egy hibrid forgatókönyvben egy a helyszíni hálózatból is elérhető. Mindkét forgatókönyvet használja a konfiguráció szerint egy [belső terheléselosztó](#internalloadbalancer).
* Port előre forgalmat egy adott portot a bejövő NAT-szabályok adott virtuális gépeken.
* Adjon meg [kimenő kapcsolat](load-balancer-outbound-connections.md) egy nyilvános terheléselosztó használatával a virtuális hálózaton belüli virtuális gépek számára.


>[!NOTE]
> Azure teljes körűen felügyelt terheléselosztási megoldások az forgatókönyvek együttesét nyújtja.  Ha a TLS-lezárást ("SSL kiszervezési") vagy HTTP/HTTPS alkalmazás réteg feldolgozási keres, tekintse át a [Alkalmazásátjáró](../application-gateway/application-gateway-introduction.md).  Ha a globális DNS a(z) terheléselosztást, tekintse át [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Előfordulhat, hogy a végpont forgatókönyvek előnyt ezek a megoldások kombinálásával, igény szerint.

## <a name="what-is-load-balancer"></a>Mi az a Load Balancer?

Egy terheléselosztó erőforrást egy nyilvános terheléselosztó vagy egy belső terheléselosztó létezhet. A Load Balancer erőforrás funkciók egész időtúllépést, egy szabályt, a állapotmintáihoz és a háttér-készlet definíciójának beolvasása.  Virtuális gépek bekerülnek a háttérkészlet a háttérkészlet, a virtuális gépről megadásával.

Load Balancer erőforrások objektumai belül, amely akkor is express hogyan Azure kell programot, a több-bérlős-infrastruktúrát a létrehozni kívánt forgatókönyvhöz.  Nincs terheléselosztó erőforrások és a tényleges infrastruktúra; közötti közvetlen kapcsolat Terheléselosztó létrehozása nem hozzon létre egy példányt, és a kapacitás mindig érhető el. 

## <a name="fundamental-load-balancer-features"></a>Terheléselosztó alapvető funkciók

Terheléselosztó TCP és UDP-alkalmazásokhoz a következő alapvető lehetőségeket biztosítja:

* **Terheléselosztás**

    Az Azure terheléselosztó terheléselosztási szabályt háttér címkészletet példányokhoz időtúllépést érkező forgalom létrehozását teszi lehetővé.  A kivonat-alapú algoritmust használja a bejövő forgalom terjesztéshez, és ennek megfelelően újraírja háttér címkészletet példányokhoz flow fejlécek. A kiszolgáló új adatfolyamok fordul elő, ha a állapotmintáihoz jelzi a megfelelő háttér végpont számára érhető el.
    
    Alapértelmezés szerint 5 rekordos IP-forráscím, a forrásport, a cél IP-cím, a célport és a IP-protokollszáma álló kivonatot adatfolyamok hozzárendelése elérhető kiszolgálók használ.  Ha szeretné, hozzon létre egy adott IP-forráscím való affinitását engedélyezés a 2 vagy 3-rekordot kivonatát egy megadott szabály.  Az azonos csomagátvitellel az összes csomag érkeznek, az elosztott terhelésű előtér mögött egyazon példányán.  Ha az ügyfél indít el egy új módszer a azonos forrás IP-címről, a port az adatforrás-módosításokkal. Az eredményül kapott 5 rekordos okozhat a forgalmat, ezért egy másik háttér végpont ugorhat.

    További információkért lásd: [terheléselosztó terheléselosztási mód](load-balancer-distribution-mode.md). A következő ábra a kivonat-alapú eloszlását mutatja be:

    ![Kivonat-alapú terjesztési](./media/load-balancer-overview/load-balancer-distribution.png)

    *Ábra - kivonat-alapú terjesztési*

* **Port átirányítása**

    Az Azure Load Balancer lehetővé teszi a bejövő NAT-szabály létrehozása a port forgalom egy adott előtérbeli IP-cím egy adott portot adott háttér-példány a virtuális hálózaton belül az adott port. Ez is úgy érhető el, mint a terheléselosztás azonos kivonat-alapú terjesztési.  Ez a lehetőség gyakori forgatókönyvei Remote Desktop Protocol (RDP) vagy a Secure Shell (SSH) munkamenet azon különálló virtuális gép példányok a virtuális hálózaton belül.  A különböző portok a azonos előtérbeli IP-címhez több belső végpont is leképezheti. Ezek használhatja a távoli felügyeletéhez a virtuális gépek egy további jump mező a nélkül az interneten keresztül.

* **Független és átlátható alkalmazás**

    Terheléselosztó közvetlenül kommunikál TCP vagy UDP vagy az alkalmazási rétegre és bármely TCP vagy UDP-alapú forgatókönyvet támogatja.  Például terheléselosztó állítsa le a TLS magát, amíg létrehozhatja és TLS alkalmazásokat használ a Load Balancer ki és állítsa le a TLS-kapcsolatot a virtuális gépen, magát. Terheléselosztó nem szünteti meg a folyamat, és protokoll kézfogások mindig közvetlenül az ügyfél és a kivonatoló által kiválasztott háttér címkészletet példány között. Például a TCP-kézfogás mindig van az ügyfél és a kijelölt háttér virtuális gép között.  És egy háttértűzfal kérelem választ a háttér virtuális gépből létrehozott választ.  Terheléselosztó kimenő hálózati teljesítmény, a virtuális gép SKU úgy dönt, és a forgalom csak korlátozott marad életben hosszú ideig soha nem éri el az üresjárati időkorlát.

* **Automatikus újrakonfigurálása**

    Az Azure Load Balancer azonnal újrakonfigurálása akkor példányok felfelé vagy lefelé. Hozzáadása vagy eltávolítása a virtuális gépek háttérkészlet átállítja a terheléselosztóhoz, további műveleteket végez a terheléselosztó erőforrás nélkül.

* **Állapotteljesítmény**

    Az Azure Load Balancer állapotfigyelő mintavételt adhat meg a háttérkészletben példányok állapotának meghatározására használja. Egy mintavételt nem válaszol, amikor a terheléselosztó leállítja az új kapcsolatok küld a nem megfelelő példányok. A meglévő kapcsolatok nem érintettek, és folytatódik, amíg az alkalmazás leállása a folyamat üresjárati időkorlátot következik be, vagy a virtuális gép le van állítva.

    Három típusú mintavételt támogatja:

    - **Egyéni HTTP-vizsgálatot:** létrehozása a saját egyéni logika egy háttér címkészletet példány állapotának meghatározására használható. A load balancer rendszeresen fog mintavételi a végpont (alapértelmezés szerint minden 15 másodperc). A példány kifogástalan, ha az időkorlát (alapértelmezett 31 másodperc) belül az válaszol a HTTP 200 tekinthető. Bármely állapota nem 200-as HTTP azt eredményezi, ez a Hálózatfigyelő sikertelen lesz.  Ez akkor is hasznos a saját logikai példány eltávolítása a terheléselosztó Elforgatás megvalósításához. Konfigurálhatja például, a példány visszatérési nem 200 állapotot, ha a példány CPU 90 % feletti.   Ez a Hálózatfigyelő felülbírálja az alapértelmezett Vendég ügynök mintavétel.

    - **Egyéni TCP-Hálózatfigyelővel:** Ez a Hálózatfigyelő sikeres TCP-munkamenet létrehozása egy meghatározott mintavételi port támaszkodik.  Mindaddig, amíg a virtuális gépen a megadott figyelő létezik, akkor ez a Hálózatfigyelő sikeres lesz. A rendszer elutasította a kapcsolatot, ha a mintavételi sikertelen lesz. Ez a Hálózatfigyelő felülbírálja az alapértelmezett Vendég ügynök mintavétel.

    - **Vendég ügynök mintavétele (platformon, a szolgáltatási célú virtuális gépek csak):** a terheléselosztót is használhatják a vendégügynököt a virtuális gépen belül. A vendégügynök figyeli, és válaszol egy HTTP 200 OK választ, csak akkor, ha a példány a kész állapotban van. Ha az ügynök nem válaszol egy HTTP 200 OK, a terheléselosztó jelöli meg a példány válaszol, és leállítja a forgalom küldése annak a példánynak. A load balancer továbbra is próbálják elérni a példányt. Ha a vendégügynök válaszol egy HTTP 200, a terheléselosztó fog forgalmat küldeni annak a példánynak újra.  Vendég ügynök mintavételt végső esetben végezze el, és nem kell használni, amikor HTTP vagy TCP egyéni mintavételi konfigurációk lehetségesek. 
    
* **Kimenő kapcsolatokat (a forrás hálózati Címfordítás)**

    Minden kimenő irányából magánhálózati IP-címek a virtuális hálózaton belüli nyilvános IP-címeket az interneten fordítható a terheléselosztó előtérbeli IP-címre. Ha egy nyilvános front-end háttér virtuális gép vállalja terheléselosztási szabály van kötve, Azure programok kimenő kapcsolatokat automatikusan lefordítani a nyilvános front-end IP-címet. A forrás hálózati Címfordítás (SNAT) is nevezik. SNAT fontos előnyökkel jár:

    + Egyszerű frissítés és katasztrófa-helyreállítás szolgáltatások, lehetővé teszi óta az előtérbeli dinamikusan képezhető le a szolgáltatást egy másik példánya.
    + Ez egyszerűbbé teszi a hozzáférés-vezérlési lista (ACL) felügyeletet. Előtérbeli IP-cím nem módosítható, mert a szolgáltatások növelheti, kifejezett hozzáférés-vezérlési listák le, vagy újratelepítése beolvasása.

    Tekintse meg [kimenő kapcsolatok](load-balancer-outbound-connections.md) cikk alapelveiről lehetősége.

Standard terheléselosztó további Termékváltozat-specifikus képességek ezek – alapok túl van.  Tekintse át a jelen cikkben alább további része.

## <a name="skus"></a> Terheléselosztó SKU összehasonlítás betöltése

Az Azure Load Balancer két különböző Termékváltozatai támogatja: Basic és Standard.  A forgatókönyv méretezés, a szolgáltatások és a díjszabás különbségek vannak.  Olyan alapvető terheléselosztással lehetséges forgatókönyv, valamint szabványos terheléselosztó hozhatók létre.  Valójában az API-kat a mindkét termékváltozatok hasonló és meghívott keresztül egy SKU megadását.  Az API-t támogató termékváltozatok a terheléselosztó és a nyilvános IP-cím áll rendelkezésre a 2017-08-01 API-val kezdődő.  Mindkét termékváltozatok azonos általános API és struktúra rendelkezik.

Előfordulhat azonban, attól függően, amelyek SKU választja, a konfiguráció teljes forgatókönyv részletes némileg eltérő. A Load Balancer dokumentáció meghívja egy cikk csak egy adott SKU alkalmazandó esetén. Tekintse át a következő különbségek megismeréséhez, és hasonlítsa össze az alábbi táblázat.  Felülvizsgálati [szokásos terhelés terheléselosztó áttekintése](load-balancer-standard-overview.md) további tájékoztatást talál.

>[!NOTE]
> Új tervek szabványos terheléselosztót használja. 

Önálló virtuális gépek, a rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok csak egy SKU, soha nem is kell csatlakoztatni. Nyilvános IP-címek használata esetén mind a Terheléselosztóról, mind a nyilvános IP-cím SKU meg kell egyeznie. Terheléselosztó és a nyilvános IP-SKU nincsenek változtatható.

_Ajánlott eljárás, explicit módon, adja meg a SKU annak ellenére, hogy még nem kötelező._  Ilyenkor a szükséges változtatásokat tartják minimális. Ha nincs megadva a Termékváltozat, a levelezéshez 2017-08-01 API-verzió az alapszintű Termékváltozat használandó kerül értelmezésre.

>[!IMPORTANT]
>Standard terheléselosztó egy új terheléselosztó termék és nagy mértékben alapvető terheléselosztó felülbírálja.  Fontos és szándékos mindkét termék közötti különbségek vannak.  Olyan alapvető terheléselosztással lehetséges végpont forgatókönyv szabványos terheléselosztó hozhatók létre.  Ha már használt alapvető terheléselosztóhoz, tanulmányozza át szabványos terheléselosztással jelentős változásokat a Standard és a Basic és a művelet hatása között viselkedésének megértése. Ez a szakasz célszerű gondosan felülvizsgálni.

| | [Standard Termékváltozat](load-balancer-standard-overview.md) | Alapszintű termékváltozat |
| --- | --- | --- |
| Háttér-készlet mérete | legfeljebb 1000 példányok | legfeljebb 100 példányok|
| Háttér címkészletet végpontok | Egyetlen virtuális hálózatban, beleértve a virtuális gépek rendelkezésre állási csoportok esetében, amelyet minden virtuális gép virtuálisgép-skálázási készletekben. | virtuális gépek egy egyetlen rendelkezésre állási készlet vagy a virtuális gép méretezési beállítása |
| Rendelkezésre állási zónák | Zónaredundáns és zonal frontends a bejövő és kimenő, a kimenő forgalom hozzárendelések után is megmaradnak a zóna hiba, terheléselosztás kereszt-zóna | / |
| Diagnosztika | Azure figyelő bájt és csomag számlálók, egészségügyi többdimenziós metrikákat a állapot, kapcsolódási kísérletek (TCP SZIN), kimenő kapcsolat állapota (SNAT sikeres és sikertelen adatfolyamok), az aktív adatforrás vezérlősík mérések mintavételi modulja | Az Azure naplóelemzés csak a nyilvános terheléselosztót, SNAT Erőforrásfogyás riasztást, háttér címkészletet állapotfigyelő száma |
| Magas rendelkezésre ÁLLÁSÚ portok | Belső terheléselosztó | / |
| Alapértelmezés szerint biztonságos | nyilvános IP-cím és a terheléselosztó végpontok mindig az alapértelmezett bezárul, explicit módon engedélyezett használt hálózati biztonsági csoport | Alapértelmezett nyissa meg a hálózati biztonsági csoport nem kötelező |
| Kimenő kapcsolatok | Több frontends szabály lemondáshoz száma. Kimenő címmel rendelkező virtuális gép társítása _kell_ explicit módon hozható létre.  Ez magában foglalja a más Azure PaaS-szolgáltatásokkal létesített kapcsolatok vagy [VNet Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md) kell használni. Kimenő kapcsolatok alapértelmezett SNAT keresztül nem érhetők el, ha csak egy belső terheléselosztón van szolgál a virtuális gép. | Egyetlen előtér. Csak belső terheléselosztó van szolgál a virtuális gépek alapértelmezett SNAT használatos |
| Több frontends | Bejövő és kimenő | Csak bejövő |
| Műveletek | A legtöbb műveletek < 30 másodperc | 60-90 másodpercet tipikus |
| SLA | a két kifogástalan állapotú virtuális gép elérési útja 99,99 %-os | A virtuális gép SLA implicit | 
| Díjszabás | Számlázott szabályok száma alapján, adatfeldolgozási bejövő vagy kimenő társított erőforrás  | Nem kell fizetni |

Felülvizsgálati [szolgáltatási korlátait terheléselosztóhoz](https://aka.ms/lblimits).  Standard terheléselosztóhoz is tekintse át a részletes [áttekintése](load-balancer-standard-overview.md), [árképzési](https://aka.ms/lbpricing), és [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Alapelvek

### <a name = "publicloadbalancer"></a>Nyilvános terheléselosztó

Nyilvános terheléselosztó továbbítja a nyilvános IP-cím és port száma bejövő forgalmat a magánhálózati IP-cím és port számát a virtuális gép, és ez fordítva is igaz a válasz forgalmat a virtuális gépről. Terheléselosztási szabályok lehetővé teszik több virtuális gépek vagy szolgáltatások közötti forgalom adott típusú terjesztése. Webes kérelem forgalom terhelését például több webkiszolgáló között is terjednek.

Az alábbi ábrán egy elosztott terhelésű végpont a webes forgalomban, amely a nyilvános és titkos TCP port a 80-as három virtuális gép között meg van osztva. A három virtuális gépek vannak egy elosztott terhelésű készlet.

![Példa nyilvános terheléselosztóra](./media/load-balancer-overview/IC727496.png)

**1. ábra: Terheléselosztási egy nyilvános terheléselosztó használatával webes forgalom**

Internetes ügyfelek weblap-kérelmeket küldjön a webes alkalmazás 80-as TCP-porton a nyilvános IP-címe, ha az Azure terheléselosztó a kérelmeket az elosztott terhelésű készlet a három virtuális gépek között osztja el. A load balancer algoritmusok kapcsolatos további információkért lásd: a [load balancer áttekintése lapon](load-balancer-overview.md#load-balancer-features).

Alapértelmezés szerint az Azure Load Balancer osztja el a hálózati forgalom több virtuálisgép-példánya között. Munkamenet-kapcsolatot is konfigurálhatja. További információkért lásd: [terheléselosztó terheléselosztási mód](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Belső terheléselosztó

Belső terheléselosztó csak arra utasítja a erőforrásokat, amelyek a virtuális hálózaton belül, vagy Azure-infrastruktúra eléréséhez használja, amely a VPN-forgalom. Ebben a tekintetben a belső terheléselosztó különbözik egy nyilvános terheléselosztó. Azure-infrastruktúra korlátozza a hozzáférést a virtuális hálózati elosztott terhelésű előtérbeli IP-címét. Előtérbeli IP-címek és virtuális hálózatok soha nem érhetők el közvetlenül egy internet-végponthoz. Belső üzleti alkalmazások futtatása az Azure-ban, és Azure-ban vagy a helyszíni erőforrások érhetők el.

Belső terheléselosztó lehetővé teszi, hogy a következő típusú terheléselosztási:

* A virtuális hálózaton belül: terheléselosztás virtuális gépek a virtuális hálózat, amely az azonos virtuális hálózaton belüli virtuális gépek csoportjaira.
* A létesítmények közötti virtuális hálózat: terheléselosztás a helyszíni számítógépek, amelyek az azonos virtuális hálózaton belüli virtuális gépek csoportjaira. 
* A többrétegű alkalmazások: az internetre irányuló Többrétegű alkalmazások, ahol a háttér-rétegek nincsenek internetre terheléselosztás. A háttér-rétegek kell az internetre irányuló forgalom terheléselosztási réteg (lásd a 2. ábra).
* Az üzletági alkalmazások: az üzletági alkalmazásokhoz, amelyek az Azure-ban nélkül további load balancer hardver- vagy a terheléselosztás. Ez az eset tartalmazza a helyszíni kiszolgálók, amelyek a számítógépet, amelynek a forgalmát terhelésű készletében.

![Példa belső terheléselosztóra](./media/load-balancer-overview/IC744147.png)

**2. ábra – terheléselosztási Többrétegű alkalmazások mindkét nyilvános és a belső terheléselosztók használatával**

## <a name="pricing"></a>Díjszabás
Standard terheléselosztó terheléselosztási szabályok konfigurálva és az összes bejövő és kimenő adatfeldolgozási száma alapján megterhelni termék. Standard terheléselosztóhoz árakról, látogasson el a [Load Balancer árképzési](https://azure.microsoft.com/pricing/details/load-balancer/) lap.

Alapszintű terheléselosztó tartományregisztráció ingyenesen.

## <a name="sla"></a>SLA

A szokásos terhelés terheléselosztó SLA kapcsolatos információkért látogasson el a [Load Balancer SLA](https://aka.ms/lbsla) lap. 

## <a name="next-steps"></a>További lépések

- Felülvizsgálati [részletesebben szabványos terheléselosztó](load-balancer-standard-overview.md)
- További információ használatáról [szabványos terheléselosztó és a rendelkezésre állási zónák](load-balancer-standard-availability-zones.md)
- További információ használatáról [terheléselosztó kimenő kapcsolatok](load-balancer-outbound-connections.md)
- További tudnivalók [terheléselosztó magas rendelkezésre ÁLLÁSÚ portok betöltése](load-balancer-ha-ports-overview.md)
- További információ használatáról [több Frontends rendelkező terheléselosztó](load-balancer-multivip-overview.md)
- További tudnivalók [VNet Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md)
- Megtudhatja, hogyan hozzon létre egy [alapvető nyilvános terheléselosztó](load-balancer-get-started-internet-portal.md)

