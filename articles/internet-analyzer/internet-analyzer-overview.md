---
title: Azure Internet Analyzer | Microsoft dokumentumok
description: További információ az Azure Internet Analyzer szolgáltatásról
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 3d9d1240e31d40a3d9c75d56314acc1721d90b99
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73501945"
---
# <a name="what-is-internet-analyzer-preview"></a>Mi az Internet Analyzer? (Előzetes verzió)

Az Internet Analyzer egy ügyféloldali mérési platform, amely azt teszteli, hogy a hálózati infrastruktúra változásai hogyan befolyásolják az ügyfelek teljesítményét. Akár a helyszínről migrál az Azure-ba, akár egy új Azure-szolgáltatást értékel, az Internet Analyzer lehetővé teszi, hogy a migrálás előtt a felhasználók adatai és a Microsoft részletes elemzései alapján jobban megérthesse és optimalizálhassa a hálózati architektúrát az Azure-ral.

Az Internet Analyzer egy kis JavaScript-ügyfelet használ a webalkalmazásba ágyazva, hogy mérje a végfelhasználók késését a kiválasztott hálózati célkészlethez, _végpontokat_hívunk. Az Internet Analyzer lehetővé teszi több egymás melletti teszt beállítását, lehetővé téve a különböző forgatókönyvek kiértékelését az infrastruktúra és az ügyfelek igényeinek fejlődésével. Az Internet Analyzer egyéni és előre konfigurált végpontokat biztosít, így kényelmes és rugalmas, hogy megbízható teljesítménydöntéseket hozzon a végfelhasználók számára. 


> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="quick--customizable-tests"></a>Gyors & testreszabható tesztek

Az Internet Analyzer a felhőmigrálással, az új vagy további Azure-régiókra való üzembe helyezéssel, illetve az azure-beli új alkalmazás- és tartalomkézbesítési platformok, például az [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) és a Microsoft Azure [CDN](https://azure.microsoft.com/services/cdn/)tesztelésével kapcsolatos, teljesítménnyel kapcsolatos kérdésekkel foglalkozik. 

Az Internet Analyzer programban létrehozott minden teszt két végpontból áll: az A végpontból és a B végpontból. 

Konfigurálhatja a saját egyéni végpontját, vagy választhat az előre konfigurált Azure-végpontok közül. Egyéni végpontok kell használni, hogy értékelje a helyszíni számítási feladatok, a példányok más felhőszolgáltatók, vagy az egyéni Azure-konfigurációk. A tesztek két egyéni végpontból állhatnak; azonban legalább egy egyéni végpontot az Azure-ban kell üzemeltetni. Az előre konfigurált Azure-végpontok segítségével gyorsan és egyszerűen kiértékelhető a népszerű Azure-hálózati platformok, például az Azure Front Door, az Azure Traffic Manager és az Azure CDN. 

Az előzetes verzió során a következő előre konfigurált végpontok érhetők el: 

* **Azure-régiók**
    * Dél-Brazília
    * Közép-India
    * USA középső régiója
    * Kelet-Ázsia
    * USA keleti régiója
    * Nyugat-Japán
    * Észak-Európa
    * Dél-Afrika Észak-Afrika
    * Délkelet-Ázsia 
    * Egyesült Arab Emírségek északi
    * Az Egyesült Királyság nyugati régiója  
    * Nyugat-Európa
    * USA nyugati régiója 
    * USA nyugati régiója, 2.
* **Több Azure-régiókombináció** 
    * USA keleti része, Dél-Brazília 
    * USA keleti része, Kelet-Ázsia 
    * Nyugat-Európa, Dél-Brazília
    * Nyugat-Európa, Délkelet-Ázsia
    * Nyugat-Európa, Észak-Egyesült Arab Emírségek
    * USA nyugati része, USA keleti része 
    * Nyugat-USA, Nyugat-Európa
    * USA nyugati, egyesült arab emírségekbeli észak-amerikai egyesült arab emírségekbeli
    * Nyugat-Európa, Egyesült Arab Emírségek Észak-, Délkelet-Ázsia
    * NYUGAT-USA, Nyugat-Európa, Kelet-Ázsia
    * USA nyugati része, Észak-Európa, Délkelet-Ázsia, Egyesült Arab Emírségek Észak-, Dél-Afrika Észak-Afrika 
* **Azure + Azure bejárati ajtó** – a fent felsorolt egyetlen vagy több Azure-régiókombinációban telepítve
* **Azure + Azure CDN a Microsofttól** – a fent felsorolt egyetlen Azure-régiókombináción telepítve
* **Azure + Azure Traffic Manager** – a fent felsorolt bármely azure-régiókombináción telepítve

## <a name="suggested-test-scenarios"></a>Javasolt tesztforgatókönyvek 

Annak érdekében, hogy a legjobb teljesítménydöntéseket hozhassa az ügyfelek számára, az Internet Analyzer lehetővé teszi, hogy két végpontot értékeljen ki a végfelhasználók adott populációja számára. 

Míg az Internet Analyzer számos kérdésre képes válaszolni, a leggyakoribbak a következők: 
* Milyen hatással van a felhőbe való migrálás teljesítményre? 
    * *Javasolt teszt: Egyéni (a jelenlegi helyszíni infrastruktúra) vs. Azure (bármely előre konfigurált végpont)*
* Mi az értéke annak, ha az adataimat a peremre helyezem, szemben az adatközponttal? 
    *  *Javasolt teszt: Azure vs. Azure bejárati ajtó, Azure vs. A Microsoft Azure CDN-je*
* Mi az Azure Front Door teljesítményelőnye?
    *  *Javasolt teszt: Egyéni/ Azure/ CDN vs. Azure bejárati ajtó*
* Milyen előnyökkel jár az Azure CDN a Microsofttól? 
    *  *Javasolt teszt: Egyéni/ Azure/ AFD vs. Azure CDN a Microsofttól*
* Hogyan halmozódik fel az Azure CDN a Microsofttól? 
    *  *Javasolt teszt: Egyéni (egyéb CDN-végpont) és a Microsoft Azure CDN-je*
* Mi a legjobb felhő a végfelhasználói lakosság számára az egyes régiókban? 
    *  *Javasolt teszt: Egyéni (egyéb felhőszolgáltatás) és az Azure (bármely előre konfigurált végpont)*

## <a name="how-it-works"></a>Működés

Az Internet Analyzer használatához állítson be egy Internet Analyzer erőforrást a Microsoft Azure portalon, és telepítse a kis JavaScript-ügyfelet az alkalmazásba. Az ügyfél méri a késést a végfelhasználók tól a kiválasztott végpontok letöltésével egy képpontos képet HTTPS-en keresztül. A késésmérések összegyűjtése után az ügyfél elküldi a mérési adatokat az Internet Analyzer-nek.

Amikor egy felhasználó felkeresi a webalkalmazást, a JavaScript-ügyfél két végpontot választ ki az összes konfigurált teszt ben. Minden végpont esetében az ügyfél _hideg_ és _meleg_ mérést hajt végre. A _hideg_ mérés további késést von maga után a felhasználó és a végpont közötti tiszta hálózati késés mellett, például a DNS-feloldás, a TCP-kapcsolat kézfogása és az SSL/TLS egyeztetés mellett. A _meleg_ mérés közvetlenül a _hideg_ mérés befejezése után következik, és kihasználja a modern böngészők állandó TCP-kapcsolat-kezelését, hogy pontos analfabetizmust kapjon. Ha a felhasználó böngészője támogatja, a W3C erőforrás időzítési API-t a pontos mérési időzítéshez használja. Jelenleg csak a meleg késés mérések et használják elemzésre.

![architektúra](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Scorecardok 

A teszt megkezdése után telemetriai adatok láthatók az Internetes elemző erőforrásban a Scorecard lapon. Ezek az adatok mindig összesítve vannak. A következő szűrőkkel módosíthatja, hogy az adatok melyik nézetében jelenjen meg: 

* **Vizsgálat:** Válassza ki azt a tesztet, amelynek eredményeit meg szeretné tekinteni. A vizsgálati adatok akkor jelennek meg, ha elegendő adat áll az elemzés befejezéséhez – a legtöbb esetben 24 órán belül. 
* **Időszak & befejezési dátum:** Az Internet Analyzer naponta három scorecardot generál – minden scorecard más összesítési időszakot tükröz – az azt megelőző 24 órát (napot), az azt megelőző hét napot (hét), és az azt megelőző 30 napot (hónap). A "Záró dátum" szűrővel válassza ki a megtekinteni kívánt időszakot. 
* **Ország:** Ezzel a szűrővel megtekintheti az országban élő végfelhasználókra vonatkozó adatokat. A globális szűrő az összes földrajzi területen jeleníti meg az adatokat.  

A scorecardokról további információt a [scorecard okának értelmezése](internet-analyzer-scorecard.md) oldalon talál. 


## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [hozhat létre az első Internetes elemző erőforrást.](internet-analyzer-create-test-portal.md)
* Olvassa el az [Internet Analyzer GYIK](internet-analyzer-faq.md). 
