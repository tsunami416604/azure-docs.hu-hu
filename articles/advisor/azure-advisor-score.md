---
title: Azure-beli számítási feladatok optimalizálása az Advisor score használatával
description: Az Advisor pontszám használata a legtöbbet kihasználhatja az Azure-ban
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 29d8480f501a78c1668b52034f439f998419f9d9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335619"
---
# <a name="optimize-azure-workloads-using-advisor-score"></a>Azure-beli számítási feladatok optimalizálása az Advisor score használatával

## <a name="introduction-to-advisor-score"></a>Az Advisor pontszám bemutatása

Azure Advisor a számítási feladatokra vonatkozó ajánlott eljárásokat nyújt. Ezek a javaslatok személyre szabottak, és a következőkhöz nyújtanak segítséget:
* A számítási feladatok testtartásának javítása és az Azure-beli üzembe helyezések optimalizálása
* Az ajánlott eljárások követésével proaktív módon megelőzheti a leggyakoribb problémákat
* Mérje fel Azure-beli számítási feladatait a [Microsoft Azure jól](https://docs.microsoft.com/azure/architecture/framework/) felépített keretrendszer öt pillére

Az Advisor alapszolgáltatásként az **Advisor pontszáma** úgy van kialakítva, hogy segítsen a célok hatékony és hatékony elérésében. 

Ahhoz, hogy a legtöbbet hozza ki az Azure-ból, fontos tisztában lennie azzal, hogy hol van a számítási feladatok optimalizálási útja, mely szolgáltatásokat/erőforrásokat jól használják, és amelyek nem. Emellett érdemes tudnia, hogyan rangsorolhatja a műveleteit a javaslatok alapján, hogy maximalizálja a végeredményt. Fontos, hogy nyomon kövesse és jelentse a folyamat előrehaladását ebben az optimalizálási úton. Az **Advisor pontszám**használatával egyszerűen elvégezheti ezeket a dolgokat az új gamification-felülettel. Személyre szabott felhőalapú tanácsadóként Azure Advisor folyamatosan vizsgálja a használat telemetria és az erőforrás-konfigurációt az iparági ajánlott eljárások ellenőrzése érdekében. Az Advisor ezt követően egyetlen pontszámban összesíti az eredményeit, így megtudhatja, ha a szükséges lépéseket a megbízható, biztonságos és költséghatékony megoldások létrehozásához hajtja végre. Az Advisor pontszám egy összesített pontszámból áll, amely tovább bontható öt kategóriára, egy-egy Azure Advisor kategóriára, amely a jól felépített keretrendszer öt oszlopát jelöli. A napi, heti és havi trendekkel nyomon követheti a folyamat előrehaladását, és megtekintheti a pontszámokat és a kategóriát, valamint megadhatja a célok eléréséhez szükséges teljesítményteszteket is. 

 ![Az Advisor pontszámának felhasználói felülete](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>Az Advisor pontszámának felhasználása
Az Advisor az Advisor-kategóriák általános tanácsadói pontszámát és részletezését jeleníti meg százalékban kifejezve. Bármely kategória 100%-os pontszáma azt jelenti, hogy az Advisor által értékelt összes erőforrás az Advisor által javasolt ajánlott eljárásokat követi. A spektrum másik végén a 0%-os pontszám azt jelenti, hogy az Advisor által értékelt egyik erőforrás sem követi az Advisor javaslatait. Ezeknek a pontszámoknak a használatával könnyedén elvégezheti az alábbi folyamatok elérését:
* Az **Advisor pontszáma** segítséget nyújt a számítási feladatok/előfizetések alapjául szolgáló alaptervek létrehozásában az Advisor pontszám alapján. Megtekintheti a korábbi trendeket is, hogy megtudja, mi a trend.
* Az **Advisor kategóriáinak pontszámai** alapján megtudhatja, hogy mely Kiemelt javaslatok javítják a legjobb pontszámot. Ezek az értékek mind a javaslat súlyozását, mind a megjósolt egyszerű megvalósítást tükrözik, így biztosítva, hogy a lehető legtöbb értéket lekérje, és segít a rangsorolásban.
* **Kategória pontszáma** az egyes javaslatok esetében az egyes kategóriákhoz tartozó szervizelési műveletek rangsorolásának elősegítése érdekében

Az egyes javaslatok kategóriához való hozzájárulása egyértelműen látható az Azure Portal Advisor pontszám lapján. Az egyes kategóriák pontszámát a kategória pontszám hatása oszlopban szereplő százalékpontok alapján növelheti. Ez az érték a kategórián belüli javaslat súlyát és az előre jelzett könnyű megvalósítást tükrözi a potenciálisan alacsonyan lógó gyümölcsök esetében. A legjobb pontszámot érintő javaslatokra koncentrálva a lehető leghatékonyabban hozhatja ki az időt.  

![Az Advisor pontszámának hatása](./media/advisor-score-2.png)

Ha az Advisor bármely javaslata nem vonatkozik egy adott erőforrásra, elhalaszthatja vagy elvetheti ezeket a javaslatokat, és a következő frissítésből kizárja a pontszám számítását. Az Advisor további Visszajelzésként is ezt a bemenetet fogja használni a modell tökéletesítéséhez.

## <a name="how-is-advisor-score-calculated"></a>Hogyan számítják ki az Advisor score értékét?
Az Advisor megjeleníti a kategória pontszámait és a teljes tanácsadói pontszámot százalékban kifejezve. Bármely kategóriában a 100%-os pontszám azt jelenti, hogy az *Advisor által értékelt*összes erőforrást az Advisor által javasolt ajánlott eljárások követik. A spektrum másik végén a 0%-os pontszám azt jelenti, hogy az Advisor által értékelt források egyike sem követi az Advisor javaslatait. 
**Az öt kategória közül mind a legmagasabb lehetséges pontszáma 100.** Az Advisor összesített pontszámát a rendszer az összes vonatkozó kategória pontszáma alapján számítja ki, és az összes vonatkozó kategória közül a legmagasabb lehetséges pontszám összege alapján oszlik meg. A legtöbb előfizetés esetében az Advisor hozzáadja a pontszámot az egyes kategóriákhoz, és az 500-as megosztást. A **Kategóriák pontszámát azonban csak akkor számítjuk ki, ha az Advisor által értékelt erőforrásokat használ.**

**Példa az Advisor pontszámának számítására**
* Egyszeri előfizetés pontszáma: ez az előfizetés minden egyes kategóriájának a pontszámának egyszerű jelentése. Ha az Advisor kategória pontszámai a következők: Cost = 73, Reliabilit = 85, működési kiválóság = 77, teljesítmény = 100; az **Advisor pontszáma** a következő: (73 + 85 + 77 + 100)/(4x100) = 0,84 vagy 84%.
* Több előfizetés pontszáma: Ha több előfizetés van kiválasztva, akkor a generált általános tanácsadói pontszám a súlyozott összesített kategória pontszáma. Itt minden Advisor-kategória pontszáma az előfizetések által felhasznált erőforrások alapján összesítve történik. A súlyozott összesített kategória pontszámok után egy egyszerű kifejezéssel elvégezheti az előfizetések teljes pontszámát. 


### <a name="scoring-methodology"></a>Pontozási módszertan: 
Az Advisor pontszámának kiszámítása négy lépésben végezhető el:
1. Az Advisor kiszámítja az érintett **erőforrások kiskereskedelmi költségeit**, amelyek az előfizetések azon erőforrásai, amelyek legalább egy tanácsadói javaslattal rendelkeznek.
2. Az Advisor kiszámítja a **kiértékelt erőforrások kiskereskedelmi árát**, az Advisor által figyelt erőforrásokat, függetlenül attól, hogy vannak-e javaslatai. 
3. Az Advisor minden egyes ajánlási típus esetében kiszámítja az erőforrás-elosztási **arányt**, amely az érintett erőforrások kiskereskedelmi díja, a kiértékelt erőforrások kiskereskedelmi díja alapján.
4. Az Advisor három további súlyozást alkalmaz az összes kategória kifogástalan erőforrás-arányára:
  * A nagyobb hatású javaslatok súlyozása nehezebb, mint az alacsonyabb hatású.
  * A hosszú távú javaslatokat tartalmazó erőforrások többek között a pontszám alapján lesznek kiszámítva.
  * A tanácsadóban elhalasztott vagy elhagyott erőforrások teljes egészében törlődnek a pontszám kiszámítása során. 
    
Az Advisor ezt a modellt az Advisor kategóriájának szintjén alkalmazza (a biztonság [biztonságos pontszám](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) modellt használ), és minden egyes kategóriához az USA Advisor pontszámát adja meg, és egy egyszerű átlagot eredményez.


## <a name="advisor-score-faq"></a>Advisor pontszám – GYIK
* **Milyen gyakran frissül a pontszám?**
A pontszám naponta legalább egyszer frissül. 
* **Miért van néhány javaslat üres "-" értékkel a kategória pontszám Impact oszlopában?** Az Advisor nem tartalmaz azonnal új ajánlásokat vagy a pontozási modell legutóbbi változásaival rendelkezőket. Egy rövid próbaidőszak után, amely általában néhány hét, a pontszámban szerepel. 
* **Miért nagyobb a költséghatékonyság hatása bizonyos javaslatok esetében, még akkor is, ha alacsonyabb a lehetséges megtakarítás?**
A díjszabás a kihasználatlan erőforrások potenciális megtakarítását, valamint a javaslatok megvalósításának várható egyszerűségét tükrözi. A további súlyozás például olyan érintett erőforrásokra vonatkozik, amelyek hosszú ideje inaktívak, még akkor is, ha a lehetséges megtakarítások alacsonyabbak. 
* **Miért nincs pontszám egy vagy több kategóriához vagy előfizetéshez?**
Az Advisor csak azokat a kategóriákat és előfizetéseket eredményezi, amelyek az Advisor által értékelt erőforrásokkal rendelkeznek.
* **Mi a teendő, ha egy javaslat nem releváns?**
Ha elveti az Advisor javaslatát, az a pontszám kiszámítása után nem lesz kihagyva. A nem hiányzó javaslatok azt is segítenek, hogy az Advisor javítsa a javaslatok minőségét.
* **Miért változott a pontszám?** A pontszám változhat, ha az Advisor által javasolt ajánlott eljárásokat bevezetve kijavítja az érintett erőforrásokat. Ha Ön vagy bármely más, az előfizetésre vonatkozó engedélyekkel módosított vagy létrehozott egy új erőforrást, akkor a pontszám ingadozásai is megjelenhetnek, mivel a pontszám a költségeket befolyásoló erőforrások arányán alapul, az összes erőforrás teljes költsége alapján.
* **Hogyan számítja ki az Advisor az előfizetéshez tartozó erőforrások kiskereskedelmi költségeit?**
Az Advisor a Azure.com díjszabási oldalán közzétett fizetések díjszabását használja, amely nem tükrözi a vonatkozó kedvezményeket, és megszorozza az adott erőforrás utolsó napján felhasznált mennyiséggel. A kedvezmények kihagyása az erőforrás-díj kiszámításával az Advisor pontszáma az előfizetések, a bérlők és a beléptetések esetében eltérő lehet. 
* **Meg kell-e tekinteni az Advisorban szereplő ajánlásokat, hogy pontokat kapjak a pontszámhoz?** Nem. A pontszám azt mutatja be, hogy az Advisor által ajánlott eljárásokat kell-e alkalmazni, még akkor is, ha az ajánlott eljárásokat proaktív módon fogadja el, és soha nem tekinti meg az Advisor javaslatait.
* **Különbséget tesz a pontozási módszer a termelési és fejlesztési-tesztelési feladatok között?**
Nem, de jelenleg nem, de az egyes erőforrásokra vonatkozó ajánlásokat elhagyhatja, ha ezeket az erőforrásokat fejlesztési és tesztelési célokra használják, és a javaslat nem alkalmazható.
* **Összehasonlítható-e az előfizetések közötti pontszámok egy 100-erőforrással és egy 100 000-erőforrással rendelkező előfizetéssel?**
A pontozási módszer úgy van kialakítva, hogy az előfizetéshez és a szolgáltatásokhoz tartozó erőforrások számát szabályozza, így a kevesebb erőforrással rendelkező előfizetések nagyobb vagy alacsonyabb pontszámot kapnak, mint az előfizetések több erőforrással. 
* **Mit jelent, ha a pontszám hatásának oszlopában a "hamarosan elérhető" érték jelenik meg?**
Ez azt jelenti, hogy ez egy új javaslat, és továbbra is dolgozunk az Advisor score-modellben. Ha ezt az új javaslatot a pontszám kiszámítása során is figyelembe veszi, a pontszám befolyásolja a javaslat értékét.  
* **Függ a pontszám attól, hogy mennyit költek az Azure-ban?**
Nem, a pontszám nem feltétlenül tükrözi, hogy mennyit költ, és a szükségtelen kiadások alacsonyabb költségekkel járnak.

## <a name="how-to-access-advisor"></a>Az Advisor elérése
Az Advisor pontszám nyilvános előzetes verziója Azure Portal. Az Advisor szakaszra kell lépnie, és a bal oldali NAV második menüpontja is megtalálható az Advisor-pontszámban. 

![Advisor score belépési pont](./media/advisor-score-3.png)

## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további információkért lásd:
* [Az Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Az Advisor költségekkel kapcsolatos javaslatai](advisor-cost-recommendations.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)
