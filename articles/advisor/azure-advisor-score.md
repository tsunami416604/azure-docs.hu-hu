---
title: Azure-beli számítási feladatok optimalizálása az Advisor score használatával
description: Az Advisor pontszám használata a legtöbbet kihasználhatja az Azure-ban
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: c9e6a13b39ac3436a71b6cecee4c91c609589378
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90039032"
---
# <a name="advisor-score-to-get-the-most-out-of-azure"></a>Az Advisor pontszáma, amellyel a legtöbbet hozhatja ki az Azure-ban

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
* Az **Advisor kategória pontszámai** segítenek megérteni, hogy mely kategóriákra van szükség, és segít a rangsorolásban.
* Az egyes javaslatok **potenciális pontszámának növekedése** az egyes kategóriákhoz tartozó szervizelési műveletek rangsorolása érdekében

Az egyes javaslatok a pontszámhoz való hozzájárulása egyértelműen látható a Azure Portal áttekintés lapján. Az ajánlott eljárások elfogadásával növelheti a pontszámát, és rangsorolhatja a lehető legnagyobb **pontszámú** javaslatokat, amelyekkel a lehető leggyorsabban elvégezhető a gyors előrehaladás.  

![Az Advisor pontszámának hatása](./media/advisor-score-2.png)

Mivel az Advisor pontozási módszertana további súlyozást tesz elérhetővé a hosszú távú ajánlásokkal rendelkező drágább erőforrásoknál, a lehető leghamarabb szervizelését az erőforrásokat a legmagasabb kiskereskedelmi költséggel. Ha az Advisor bármely javaslata nem vonatkozik egy adott erőforrásra, elvetheti ezeket az ajánlásokat, hogy kizárják őket a pontszám kiszámításával, és visszajelzést küldjön az Advisornak a javaslatok javítására. 

## <a name="how-is-advisor-score-calculated"></a>Hogyan számítják ki az Advisor score értékét?
Az Advisor megjeleníti a kategória pontszámait és a teljes tanácsadói pontszámot százalékban kifejezve. Bármely kategóriában a 100%-os pontszám azt jelenti, hogy az *Advisor által értékelt*összes erőforrást az Advisor által javasolt ajánlott eljárások követik. A spektrum másik végén a 0%-os pontszám azt jelenti, hogy az Advisor által értékelt források egyike sem követi az Advisor javaslatait. 
**Az öt kategória közül mind a legmagasabb lehetséges pontszáma 100.** Az Advisor összesített pontszámát a rendszer az összes vonatkozó kategória pontszáma alapján számítja ki, és az összes vonatkozó kategória közül a legmagasabb lehetséges pontszám összege alapján oszlik meg. A legtöbb előfizetés esetében az Advisor hozzáadja a pontszámot az egyes kategóriákhoz, és az 500-as megosztást. A **Kategóriák pontszámát azonban csak akkor számítjuk ki, ha az Advisor által értékelt erőforrásokat használ.**

### <a name="scoring-methodology"></a>Pontozási módszertan: 
Az Advisor pontszámának kiszámítása négy lépésben végezhető el:
1. Az Advisor kiszámítja az érintett **erőforrások napi kiskereskedelmi költségeit**, amelyek az előfizetések azon erőforrásai, amelyek legalább egy tanácsadói javaslattal rendelkeznek.
2. Az Advisor kiszámítja a **becsült erőforrások napi kiskereskedelmi költségeit**, az Advisor által figyelt erőforrásokat, függetlenül attól, hogy vannak-e javaslatai. 
3. Az Advisor minden egyes ajánlási típus esetében kiszámítja az erőforrás-elosztási **arányt**, amely az érintett erőforrások költségeit terheli a kiértékelt erőforrások díja alapján.
4. Az Advisor három további súlyozást alkalmaz az összes kategória kifogástalan erőforrás-arányára:
* A nagyobb hatású javaslatok súlyozása nehezebb, mint az alacsonyabb hatású.
* A hosszú távú javaslatokat tartalmazó erőforrások többek között a pontszám alapján lesznek kiszámítva.
* Az Advisorban figyelmen kívül hagyott erőforrásokat a rendszer teljesen eltávolítja a pontszám számításból. 
    
Az Advisor ezt a modellt az Advisor kategóriájának szintjén alkalmazza (a biztonság [biztonságos pontszám](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) modellt használ), és minden egyes kategóriához az USA Advisor pontszámát adja meg, és egy egyszerű átlagot eredményez.


## <a name="advisor-score-faq"></a>Advisor pontszám – GYIK
* **Milyen gyakran frissül a pontszám?**
A pontszám naponta legalább egyszer frissül. 
* **Meg kell-e tekinteni az Advisorban szereplő ajánlásokat a pontszám beszerzéséhez?**
Nem. A pontszám azt mutatja be, hogy az Advisor által javasolt ajánlott eljárásokat kell-e alkalmazni, még akkor is, ha soha nem tekinti meg ezeket a javaslatokat az Advisorban, és proaktívan alkalmazza az ajánlott eljárásokat  
* **Hogyan számítja ki az Advisor az erőforrások napi kiskereskedelmi költségeit az előfizetések esetében?**
Az Advisor a Azure.com díjszabási oldalán közzétett *fizetések* díjszabását használja, amely nem tükrözi a vonatkozó kedvezményeket, és megszorozza az adott erőforrás utolsó napján felhasznált mennyiséggel. A kedvezmények kihagyása az erőforrás-díj kiszámításával az Advisor pontszáma az előfizetések, a bérlők és a beléptetések esetében eltérő lehet. 
* **Mi a teendő, ha egy javaslat nem releváns?**
Ha elveti az Advisor javaslatát, az a pontszám kiszámítása után nem lesz kihagyva. A nem hiányzó javaslatok azt is segítenek, hogy az Advisor javítsa a javaslatok minőségét.
* **Miért változott a pontszám?** A pontszám változhat, ha az Advisor által javasolt ajánlott eljárásokat bevezetve kijavítja az érintett erőforrásokat. Ha Ön vagy bármely más, az előfizetésre vonatkozó engedélyekkel módosított vagy létrehozott egy új erőforrást, akkor a pontszám ingadozásai is megjelenhetnek, mivel a pontszám a költségeket befolyásoló erőforrások arányán alapul, az összes erőforrás teljes költsége alapján.
* **Függ a pontszám attól, hogy mennyit költek az Azure-ban?**
Nem. A pontszám az előfizetés és a szolgáltatás-kombináció méretének szabályozására szolgál. 
* **Különbséget tesz a pontozási módszer a termelési és fejlesztési-tesztelési feladatok között?**
Nem, de jelenleg nem, de az egyes erőforrásokra vonatkozó ajánlásokat elhagyhatja, ha ezeket az erőforrásokat fejlesztési és tesztelési célokra használják, és a javaslat nem alkalmazható.
* **Összehasonlítható-e az előfizetések közötti pontszámok egy 100-erőforrással és egy 100 000-erőforrással rendelkező előfizetéssel?**
A pontozási módszer úgy van kialakítva, hogy az előfizetéshez és a szolgáltatásokhoz tartozó erőforrások számát szabályozza, így a kevesebb erőforrással rendelkező előfizetések nagyobb vagy alacsonyabb pontszámot kapnak, mint az előfizetések több erőforrással. 

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
