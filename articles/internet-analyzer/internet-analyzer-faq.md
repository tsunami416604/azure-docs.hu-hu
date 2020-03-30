---
title: Internetes elemző – gyakori kérdések | Microsoft dokumentumok
description: Az Azure Internet Analyzer gyakran feltett kérdések.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184259"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Gyakori kérdések az Azure Internet Analyzer (előzetes verzió)

Ez az Azure Internet Analyzer gyIK- ha további kérdései vannak, keresse fel a [visszajelzési fórumot,](https://aka.ms/internetAnalyzerFeedbackForum) és tegye közzé kérdését. Amikor egy kérdést gyakran felteszünk, hozzáadjuk ehhez a cikkhez, hogy gyorsan és egyszerűen megtalálható legyen.

## <a name="how-do-i-participate-in-the-preview"></a>Hogyan vehetek részt az előnézetben?

Az előzetes verzió kiválasztott ügyfelek számára érhető el. Ha szeretne csatlakozni az előnézethez, kérjük, tegye a következőket:

1. Jelentkezzen be az [Azure Portalra.](https://ms.portal.azure.com)
2. Nyissa meg az **Előfizetések** lapot.
3. Kattintson arra az Azure-előfizetésre, amelyhez az Internet Analyzer szolgáltatást használni kívánja.
4. Nyissa meg az **előfizetés erőforrás-szolgáltatói** beállításait.
5. Keresse meg a **Microsoft.Network** kifejezést, és kattintson a **Regisztráció** (vagy **Újraregisztrálás)** gombra.
![hozzáférési kérelem](./media/ia-faq/request-preview-access.png)

6. [Jóváhagyás kérése](https://aka.ms/internetAnalyzerContact) az e-mail cím és az Azure-előfizetés-azonosító megadásával, amelyet a hozzáférési kérelem benyújtásához használtak.
7. A kérelem jóváhagyását követően e-mailben megerősítést kap, és létrehozhat/frissíthet/módosíthat az Internet Analyzer erőforrásait az újonnan engedélyezett Azure-előfizetésből.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Be kell ágyazni az ügyfelet a teszt futtatásához?

Igen, az Internet Analyzer klienstelepítve kell lennie az alkalmazásban a felhasználókra jellemző mutatók gyűjtéséhez. [További információ az ügyfél telepítéséről.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Kiszámláznak az előzetes verzióban való részvételért?
Nem, az Azure Internet Analyzer ingyenesen használható előzetes verzióban. Nincs szolgáltatásiszint-szerződés az előzetes verzió során.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Milyen forgatókönyveket tervez az Internet Analyzer?

Az Internet Analyzer célja, hogy a felhasználói populációalapján hálózati teljesítményelemzéseket biztosítson. Annak érdekében, hogy a felhasználók számára a legjobb teljesítménydöntéseket hozhassa, az Internet Analyzer összehasonlítja két internetes végpont teljesítményét a különböző felhasználói sokaság használatával. Míg az Internet Analyzer számos kérdésre képes válaszolni, a leggyakoribbak a következők:

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

## <a name="which-tests-can-i-run-in-preview"></a>Milyen teszteket futtathatok előzetes verzióban?

Az Internet Analyzer programban létrehozott minden teszt két végpontból áll: az A végpontból és a B végpontból. Az alábbi kombinációk bármelyike futtatható tesztként:  
* Két előre konfigurált végpont,
* Egy egyéni és egy előre konfigurált végpont, vagy
* Két [egyéni végpont (egy](internet-analyzer-custom-endpoint.md) egyéni végpontnak az Azure-ban kell lennie.

Az előzetes verzióban a következő előre konfigurált végpontok érhetők el:
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

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Miben különbözik az Internet Analyzer az Azure által nyújtott egyéb figyelési szolgáltatásoktól?

Az Internet Analyzer segít megérteni a végfelhasználók teljesítményét, és segít a teljesítményük javítását célzó döntések meghozatalában. Míg más Azure-figyelési eszközök betekintést nyújtanak az Azure-szolgáltatásokba, az Internet Analyzer a felhasználók teljes körű internetes teljesítményének mérésére összpontosít.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Hogyan kezeli a mérési adatokat az Internet Analyzer?

Az Azure [erős biztonsági folyamatokkal rendelkezik, és a megfelelőségi szabványok széles körének felel meg.](https://azure.microsoft.com/support/trust-center/) Csak Ön és a kijelölt csapata férhet hozzá az adataihoz. A Microsoft munkatársai csak korlátozott hozzáféréssel rendelkezhetnek az Ön tudomása szerint korlátozott anameddig. Az átvitel és a nyugalom alatt van titkosítva.

## <a name="next-steps"></a>További lépések

További információért tekintse meg [az Internet Analyzer áttekintését.](internet-analyzer-overview.md)
