---
title: A szolgáltatásminta
description: Áttekintést kaphat a szolgáltatás-mintaképekről, azok architektúrájáról és képességeiről, valamint arról, hogy milyen feltételeket kell figyelembe vennie az üzembe helyezendő alkalmazás kiválasztásakor.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594311"
---
# <a name="about-service-meshes"></a>A szolgáltatásminta

A szolgáltatásháló olyan képességeket biztosít, mint a forgalomkezelés, a rugalmasság, a házirend, a biztonság, az erős identitás és a számítási feladatok megfigyelhetősége. Az alkalmazás levan választva ezektől a működési képességektől, és a szolgáltatásháló kihelyezi őket az alkalmazásrétegből, és le az infrastruktúra-rétegre.

## <a name="scenarios"></a>Forgatókönyvek

Íme néhány olyan forgatókönyv, amely szolgáltatásháló használata esetén engedélyezhető a számítási feladatokhoz:

- **A fürt összes forgalmának titkosítása** – A fürt ben megadott szolgáltatások közötti kölcsönös TLS engedélyezése. Ez kiterjeszthető a hálózati peremhálózaton lévő be- és kijutásra. Alapértelmezés szerint biztonságos beállítást biztosít, és nincs szükség az alkalmazáskódra és az infrastruktúrára vonatkozó módosításokra.

- **Kanári és szakaszos bevezetés –** Adja meg a feltételeket egy részhalmaza a forgalom kell irányítani egy sor új szolgáltatások a fürtben. A kanári-kiengedés sikeres tesztelése után távolítsa el a feltételes útválasztást, és fokozatosan növelje az új szolgáltatásba irányuló forgalom %-át. Végül minden forgalom új szolgáltatásra lesz irányítva.

- **Forgalomkezelés és -kezelés** – Hozzon létre egy szabályzatot egy szolgáltatáson, amely az összes forgalmat egy adott eredetű szolgáltatás verziójára korlátozza. Vagy egy olyan házirend, amely újrapróbálkozási stratégiát alkalmaz a megadott szolgáltatások közötti hibaosztályokra. Az élő forgalom tükrözése a szolgáltatások új verzióira az áttelepítés vagy a hibakeresés során. A rugalmasság tesztelése érdekében adja be a szolgáltatások közötti hibákat egy tesztkörnyezetben.

- **Megfigyelhetőség** – Betekintést nyerhet abba, hogy a szolgáltatások hogyan kapcsolódnak egymás között a forgalomhoz. Metrikák, naplók és nyomkövetések a fürt összes forgalmat, és a bejövő/kimenő forgalom. Elosztott nyomkövetési képességeket adhat az alkalmazásokhoz.

## <a name="architecture"></a>Architektúra

A szolgáltatásháló általában egy vezérlősíkból és az adatsíkból áll.

A **vezérlősík** számos olyan összetevővel rendelkezik, amelyek támogatják a szolgáltatásháló kezelését. Ez általában tartalmaz egy felügyeleti felületet, amely lehet felhasználói felület vagy API. Általában lesznek olyan összetevők is, amelyek kezelik a szabályt és a házirend-definíciókat, amelyek meghatározzák, hogy a szolgáltatáshálónak hogyan kell végrehajtania bizonyos képességeket. Vannak olyan összetevők is, amelyek kezelik a biztonság szempontjait, például az erős identitást és az mTLS tanúsítványait. A szolgáltatás-menekáltalában egy metrikák vagy megfigyelhetőségi összetevő, amely összegyűjti és összesíti a metrikák és a telemetriai adatokat a számítási feladatokból.

Az **adatsík** általában egy proxyból áll, amely et transzparens módon injektál a számítási feladatokoldalként. Ez a proxy úgy van beállítva, hogy a számítási feladatokat tartalmazó podba irányuló és kívüli összes hálózati forgalmat szabályozza. Ez lehetővé teszi, hogy a proxy konfigurálva legyen az mTLS-en keresztüli forgalom biztonságossá tétele, dinamikus útvonalforgalom, szabályzatok alkalmazása a forgalomra, valamint a metrikák és a nyomkövetési információk gyűjtésére. 

![Tipikus szolgáltatásháló architektúra](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Funkciók

A szolgáltatás egyes mintaegy természetes illeszkedést, és a hangsúly az adott forgatókönyvek támogatása, de általában azt találjuk, hogy a legtöbb fogja megvalósítani számos, ha nem az összes, a következő képességek.

### <a name="traffic-management"></a>Forgalomirányítás 

- **Protokoll** – 7.
- **Dinamikus útválasztás** – feltételes, súlyozás, tükrözés
- **Rugalmasság** – időhosszabbítások, újrapróbálkozások, megszakítók
- **Házirend** – hozzáférés-ellenőrzés, árfolyamkorlátok, kvóták
- **Tesztelés** - hibabefecskendezés

### <a name="security"></a>Biztonság

- **Titkosítás** – mTLS, tanúsítványkezelés, külső hitelesítésszolgáltató
- **Erős identitás** – SPIFFE vagy hasonló
- **Hitelesítés** – hitelesítés, engedélyezés

### <a name="observability"></a>Megfigyelhetőség

- **Mérőszámok** – arany mérőszámok, prométheusz, grafana
- **Nyomkövetés** – nyomkövetések a munkaterhelések között
- **Forgalom** – fürt, be- és kijutás

### <a name="mesh"></a>Teljes

- **Támogatott számítástechnika** - Kubernetes, virtuális gépek
- **Többfürtös** - átjárók, összevonás

## <a name="selection-criteria"></a>Kiválasztási kritériumok

A szolgáltatásháló kiválasztása előtt győződjön meg arról, hogy tisztában van a követelményekkel és a szolgáltatásháló telepítésének okaival. Próbálja meg feltenni a következő kérdéseket.

- **A ingress vezérlő elegendő az igényeimnek?** - Néha rendelkezik egy képesség, mint a/ b tesztelés vagy forgalom felosztása a be- és be- és be- és be- és be- és betolatás elegendő a szükséges forgatókönyv támogatásához. Ne adjon komplexitást a környezethez, ha nincs jó oldala.

- **A számítási feladatok és a környezet eltűrheti a további általános költségeket?** - A szolgáltatásháló támogatásához szükséges összes további összetevő további erőforrásokat igényel, például a processzort és a memóriát. Emellett az összes proxy és a hozzájuk tartozó házirend-ellenőrzések késést adnak a forgalomhoz. Ha olyan számítási feladatok vannak, amelyek nagyon érzékenyek a késésre, vagy nem tudják biztosítani a további erőforrásokat a szolgáltatásháló-összetevők lefedéséhez, akkor fontolja meg újra.

- **Feleslegesen növeli-e a további komplexitást?** - Ha a szolgáltatásháló telepítésének oka az, hogy olyan képességet szerezzen, amely nem feltétlenül kritikus az üzleti vagy üzemeltetési csapatok számára, akkor fontolja meg, hogy a telepítés, a karbantartás és a konfiguráció további összetettsége megéri-e.

- **Lehet-e ezt fokozatosan alkalmazni?** - Néhány szolgáltatás sikamlós, hogy a sok képesség lehet elfogadni egy növekményes megközelítés. Telepítse csak azokat az összetevőket, amelyek a siker biztosításához szükséges. Ha már magabiztosabb, és további képességekre van szükség, majd fedezze fel azokat. Ellenállni a késztetésnek, hogy telepíteni *mindent* a kezdetektől fogva.

Ha alapos megfontolás után úgy dönt, hogy szüksége van egy szolgáltatáshálóra a szükséges képességek biztosításához, akkor a következő döntés az, hogy *melyik szolgáltatásháló?*

Vegye figyelembe a következő területeket, és melyek azok leginkább igazodnak az Ön igényeinek. Ez elvezeti Önt a környezetének és a munkaterhelésnek leginkább megfelelő hez. A [Következő lépések](#next-steps) szakasz további részletes információkat nyújt az adott szolgáltatási kapcsolatokról és arról, hogy hogyan vannak leképezve ezekre a területekre.

- **Műszaki** - forgalomirányítás, politika, biztonság, megfigyelhetőség

- **Üzleti** - kereskedelmi támogatás, alapítvány (CNCF), OSS licenc, irányítás

- **Működés** – telepítés/frissítések, erőforrás-követelmények, teljesítménykövetelmények, integrációk (metrikák, telemetria, irányítópultok, eszközök, SMI), vegyes számítási feladatok (Linux és Windows csomópontkészletek), számítástechnika (Kubernetes, virtuális gépek), többfürtös

- **Biztonság** - hitelesítés, identitás, tanúsítványkezelés és elforgatás, dugasztalható külső hitelesítésszolgáltató


## <a name="next-steps"></a>További lépések

Az alábbi dokumentáció további információt nyújt az Azure Kubernetes-szolgáltatásban (AKS) kipróbálható szolgáltatáshálókról:

> [!div class="nextstepaction"]
> [Tudjon meg többet Istio ...][istio-about]

> [!div class="nextstepaction"]
> [Tudjon meg többet a Linkerd ...][linkerd-about]

> [!div class="nextstepaction"]
> [Tudjon meg többet a konzul ...][consul-about]

A Service Mesh Interface (SMI) egy szabványos felületet is felfedezhet a Kubernetes szolgáltatáshálóihoz:

- [Szolgáltatásháló illesztőfelülete (SMI)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md