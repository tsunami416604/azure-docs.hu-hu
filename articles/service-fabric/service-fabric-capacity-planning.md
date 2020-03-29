---
title: A Service Fabric-alkalmazások kapacitástervezése
description: A Service Fabric-alkalmazásokhoz szükséges számítási csomópontok számának azonosítását ismerteti
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cd5a5c55ff873e4891ac63361d0c4a0b56d70109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377208"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>A Service Fabric-alkalmazások kapacitástervezése
Ez a dokumentum bemutatja, hogyan becsülheti meg az Azure Service Fabric-alkalmazások futtatásához szükséges erőforrások (CPU-k, RAM, lemeztároló) mennyiségét. Gyakori, hogy az erőforrás-követelmények idővel változnak. Általában kevés erőforrást igényel a szolgáltatás fejlesztése/tesztelése során, majd több erőforrást igényel az éles környezetbe való bekerüléssorán, és az alkalmazás népszerűsége nő. Az alkalmazás tervezésekor gondolja át a hosszú távú követelményeket, és hozzon olyan döntéseket, amelyek lehetővé teszik, hogy a szolgáltatás a magas ügyféligényeknek megfelelően méretezhető legyen.

 Service Fabric-fürt létrehozásakor dönti el, hogy milyen típusú virtuális gépek (VM-ek) alkotják a fürtöt. Minden virtuális gép jön-val egy korlátozott mennyiségű erőforrás tinin (magok és sebesség), hálózati sávszélesség, RAM és a lemeztároló formájában. A szolgáltatás idővel növekszik, frissíthet virtuális gépekre, amelyek nagyobb erőforrásokat kínálnak, és/vagy további virtuális gépeket adnak hozzá a fürthöz. Az utóbbi, először meg kell terveznünk a szolgáltatást, hogy kihasználhassa az új virtuális gépek, amelyek dinamikusan hozzáadódik a fürthöz.

Egyes szolgáltatások kezelése kevés vagy egyáltalán nem a virtuális gépek magukat. Ezért ezeknek a szolgáltatásoknak a kapacitástervezéselsősorban a teljesítményre kell összpontosítania, ami azt jelenti, hogy a virtuális gépek megfelelő processzorait (magokat és sebességet) kell kiválasztani. Ezenkívül érdemes figyelembe venni a hálózati sávszélességet, beleértve a hálózati átvitelek gyakori előfordulását és az átvitel nagy részét. Ha a szolgáltatásnak jól kell teljesítenie, valamint a szolgáltatás használat növekszik, további virtuális gépeket adhat hozzá a fürthöz, és terheléselosztása a hálózati kérelmek et az összes virtuális gép között.

A virtuális gépeken nagy mennyiségű adatot kezelő szolgáltatások esetében a kapacitástervezésnek elsősorban a méretre kell összpontosítania. Ezért alaposan vegye figyelembe a virtuális gép RAM- és lemeztárolókapacitását. A Windows virtuális memóriakezelő rendszere a lemezterületet az alkalmazáskódramnak tűnővé teszi. Emellett a Service Fabric futásidejű intelligens lapozás csak a memóriában lévő adatok megőrzése és a hideg adatok áthelyezése a lemezre. Az alkalmazások így több memóriát használhatnak, mint amennyi fizikailag elérhető a virtuális gépen. A több RAM egyszerűen növeli a teljesítményt, mivel a virtuális gép több lemezes tárhelyet tud tárolni a RAM-ban. A kiválasztott virtuális gépnek rendelkeznie kell egy olyan lemezzel, amely elég nagy ahhoz, hogy tárolja a kívánt adatokat a virtuális gépen. Hasonlóképpen a virtuális gép elegendő RAM-mal kell rendelkeznie ahhoz, hogy a kívánt teljesítményt nyújtsa. Ha a szolgáltatás adatai idővel növekszik, további virtuális gépeket adhat hozzá a fürthöz, és particionálhatja az adatokat az összes virtuális gép között.

## <a name="determine-how-many-nodes-you-need"></a>Határozza meg, hogy hány csomópontra van szüksége
A szolgáltatás particionálása lehetővé teszi a szolgáltatás adatainak horizontális felskálázása. A particionálásról további információt a [Particionálási szolgáltatásfabric című témakörben talál.](service-fabric-concepts-partitioning.md) Minden partíciónak el kell férnie egyetlen virtuális gépen belül, de több (kis) partíció egyetlen virtuális gépre helyezhető. Tehát, miután több kis partíciót ad nagyobb rugalmasságot, mint néhány nagyobb partíciókat. A kompromisszum az, hogy sok partícióval növeli a Service Fabric terhelését, és nem hajthatja végre a tranzakciós műveleteket partíciók között. Több potenciális hálózati forgalom is fennáll, ha a szolgáltatáskódnak gyakran hozzá kell férnie a különböző partíciókon élő adatokhoz. A szolgáltatás tervezésekor alaposan fontolja meg ezeket az előnyöket és hátrányokat, hogy hatékony particionálási stratégiát érjön el.

Tegyük fel, hogy az alkalmazás egyetlen állapotalapú szolgáltatással rendelkezik, amely egy áruház méretével rendelkezik, amely várhatóan egy év alatt DB_Size GB-ra fog növekedni. Ön hajlandó felvenni több alkalmazás (és partíciók), ahogy azt tapasztalja, növekedés túl abban az évben.  A replikációs tényező (RF), amely meghatározza a szolgáltatás replikáinak számát, hatással van a teljes DB_Size. Az összes kópia teljes DB_Size a replikációs tényező és a DB_Size szorzata.  Node_Size a szolgáltatáshoz használni kívánt csomópontonkénti lemezterületet/RAM-ot jelöli. A legjobb teljesítmény érdekében a DB_Size a fürt memóriájába kell illeszkednie, és a virtuális gép RAM-ja körül Node_Size kell választani. A RAM-kapacitásnál nagyobb Node_Size lefoglalásával a Service Fabric futásidejű által biztosított lapozásra támaszkodik. Így előfordulhat, hogy a teljesítmény nem optimális, ha a teljes adat forrónak minősül (azóta az adatok be/ kilapoznak). Azonban sok szolgáltatás, ahol csak egy töredéke az adatok forró, ez költséghatékonyabb.

A maximális teljesítményhez szükséges csomópontok száma a következőképpen számítható ki:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>A növekedés elszámolása
Előfordulhat, hogy ki szeretné számítani a csomópontok számát a DB_Size alapján, amelyekre a szolgáltatás várhatóan növekedni fog, a DB_Size mellett, amelyekkel kezdett. Ezután növelje a csomópontok számát a szolgáltatás növekedésével, így nem túlkiépíti a csomópontok számát. A partíciók számának azonban a szolgáltatás maximális növekedéssel történő futtatásakor szükséges csomópontok számán kell alapulnia.

Jó, hogy néhány extra gépek állnak rendelkezésre bármikor, így kezelni tudja a váratlan kiugrások vagy hiba (például, ha néhány virtuális gép leáll).  Míg az extra kapacitást a várt kiugrások használatával kell meghatározni, a kiindulási pont az, hogy néhány extra virtuális gépet (5-10 százalékextra) foglaljon le.

Az előző egyetlen állapotalapú szolgáltatást feltételez. Ha egynél több állapotalapú szolgáltatással rendelkezik, hozzá kell adnia a többi szolgáltatáshoz társított DB_Size az egyenletbe. Azt is megteheti, hogy az egyes állapotalapú szolgáltatások hoz külön-külön kiszámíthatja a csomópontok számát.  Előfordulhat, hogy a szolgáltatás replikák vagy partíciók, amelyek nem kiegyensúlyozott. Ne feledje, hogy a partíciók is több adatot, mint mások. A particionálásról további információt [a gyakorlati tanácsokról szóló particionálási cikkben](service-fabric-concepts-partitioning.md)talál. Az előző egyenlet azonban partíció és replika független, mert a Service Fabric biztosítja, hogy a replikák között a csomópontok között optimalizált módon.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Számolótábla használata költségszámításhoz
Most tegyünk néhány valós számot a képletbe. Egy [példaszámolótábla](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) bemutatja, hogyan tervezhető meg egy olyan alkalmazás kapacitása, amely háromféle adatobjektumot tartalmaz. Minden objektumesetében megközelítjük a méretét és azt, hogy hány objektumra számítunk. Azt is megadhatjuk, hogy az egyes objektumtípusokhoz hány replikát szeretnénk. A számolótábla kiszámítja a fürtben tárolandó memória teljes mennyiségét.

Ezután megadjuk a virtuális gép méretét és a havi költséget. A virtuális gép mérete alapján a számolótábla megmondja, hogy a minimális számú partíciót kell használnia az adatok felosztása, hogy fizikailag elférjen a csomópontokon. Előfordulhat, hogy nagyobb számú partíciót szeretne az alkalmazás speciális számítási és hálózati forgalmi igényeinek megfelelően. A számolótábla azt mutatja, hogy a felhasználói profil objektumait kezelő partíciók száma 1-ről 6-ra nőtt.

Most, ezen információk alapján a számolótábla azt mutatja, hogy fizikailag is lejuthat az összes adatot a kívánt partíciókat és replikákat egy 26 csomós fürtön. Azonban ez a fürt lenne sűrűn csomagolt, ezért érdemes lehet néhány további csomópontok csomópontok hibák és frissítések befogadására. A számolótábla azt is megmutatja, hogy az 57-nél több csomópont nem ad további értéket, mert üres csomópontokkal rendelkezik. Ismét érdemes lehet 57 csomópont fölé menni a csomóponthibák és frissítések befogadásához. A táblázatot az alkalmazás egyedi igényeinek megfelelően módosíthatja.   

![Számolótábla költségszámításhoz][Image1]

## <a name="next-steps"></a>További lépések
Tekintse meg [a particionálási szolgáltatás fabric-szolgáltatások][10] a szolgáltatás particionálásáról.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
