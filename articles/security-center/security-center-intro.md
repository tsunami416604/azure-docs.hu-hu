---
title: Mi az az Azure Security Center?| Microsoft Docs
description: Információk az Azure Security Centerről, annak főbb funkcióiról és működéséről.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 16473b7814763d80f7e850acf44839f5ef640a18
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109044"
---
# <a name="what-is-azure-security-center"></a>Mi az az Azure Security Center?

Az Azure Security Center egységes infrastruktúra biztonsági felügyeleti rendszer, amely megerősíti a adatközpontok biztonsági állapotát, és a komplex veszélyforrások elleni védelmet biztosít a felhőben – a hibrid számítási feladatokhoz, akár az Azure-ban vagy sem –, valamint a helyszínen.

A felhőszolgáltató, az Azure és az ügyfél akkor erőfeszítései az erőforrások biztonságban tartása. Győződjön meg arról, hogy a felhőbe, és a egy időben, amikor helyez át IaaS (szolgáltatott infrastruktúra) több ügyfél felelőssége, mint a PaaS (szolgáltatásként nyújtott platformon) és SaaS (szolgáltatott szoftver) van a számítási feladatok biztonságosak, hogy. Az Azure Security Center felvértezni a hálózat, a szolgáltatások biztonságos és biztonsági helyzetét felett róla, hogy az eszközöket biztosít.

Az Azure Security Center kihívásaira három sürgős biztonsági:

-   **Gyorsan változó számítási feladatok** – egy szint és a felhő kihívást is. Többet végezhet el, másrészt jogosultak a végfelhasználók számára. Az egyéb hogyan, győződjön meg arról, hogy a folyamatosan változó szolgáltatások használják és létrehozása a biztonsági szabványoknak legfeljebb, és kövesse az ajánlott biztonsági eljárások?

-   **Egyre kifinomult támadások** – bárhol is, a számítási feladatok futtatásához, a támadások továbbra is megkapja a további kifinomult. Kell biztonságossá tenni a nyilvános felhőalapú számítási feladatokhoz, amelyek akkor vannak érvényben, egy Internet felé néző munkaterhelést futtatják, amelyről veszélynek teheti még ha nem követi a biztonság bevált eljárásokat.

-   **Biztonsági ismeretek olyan rövid ellátási** – a biztonsági riasztások és a riasztási rendszer száma sokkal outnumbers szélén, hogy a környezetek védve vannak a szükséges háttér-információkért és a felhasználói élményt a rendszergazdák számát. Egy állandó kihívás, lehetetlenné helyükön maradnak, míg a világ biztonsági egy elvárásait előtér tartózkodó naprakészen tartása a legújabb támadások.

Meg ezekkel a kihívásokkal szembeni védelem érdekében a Security Center nyújt az eszközöket:

-   **Erősítse biztonsági állapotát**: A Security Center felméri a környezetét, és lehetővé teszi az erőforrások állapotának ismertetése,:, biztonságos, vagy nem?

-   **A fenyegetések ellen**: A Security Center a számítási feladatokat értékeli, és kiváltja a fenyegetések megelőzéséről javaslatok és a fenyegetésészlelés riasztásai.

-   **Gyorsabb, biztonságos első**: A Security Centerben mindent történik, a felhőbeli nagy sebességű. Natív módon integrálva van, mert éppen a Security Center központi telepítés egyszerű, így a autoprovisioning és védelme az Azure-szolgáltatásokkal.

## <a name="architecture"></a>Architektúra

Mivel a natív Azure-ban, az Azure-ban – beleértve a Service Fabric segítségével a PaaS-szolgáltatások részét képezi a Security Center SQL-adatbázisok és a storage-fiókok – figyelt és nélkül kialakulhat környezethez a Security Center által védett.

Emellett a Security Center védi a nem Azure-kiszolgálóknak és virtuális gépeknek a felhőben vagy a helyszínen, Windows- és Linux-kiszolgálók, a Microsoft Monitoring Agent telepítésével őket. Az Azure virtuális gépek, automatikus regisztrációt a Security Centerben.

Az események gyűjtése az ügynökök és az Azure közötti kapcsolatot biztosít, személyre szabott javaslatok (szolgáltatáskorlátozása feladatokat), a biztonsági elemzési motor a kell, hogy a számítási feladatok biztonságos kövesse, valamint a fenyegetés-észlelési riasztás. Az ilyen riasztások, a lehető leghamarabb ellenőrizze, hogy a rosszindulatú támadások zajló nem található a számítási feladatok kell vizsgálni.

Ha engedélyezi a Security Center, a Security Center beépített biztonsági házirend, a Security Center kategória egy beépített kezdeményezés Azure Policy is megjelenik. A beépített kezdeményezés automatikusan regisztrálva a Security Center előfizetéseken (ingyenes vagy Standard szinten). A beépített kezdeményezésére csak naplózási házirendeket tartalmaz. A Security Center-szabályzatok az Azure Policy kapcsolatos további információkért lásd: [biztonsági házirendek használata](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Biztonsági helyzet megerősítése

Az Azure Security Center lehetővé teszi, hogy erősítse meg biztonsági állapotát. Ez azt jelenti, hogy segítséget azonosítása és hajtsa végre az ajánlott biztonsági eljárások az vezethet be feladatokat, és végrehajtja azokat a gépeket, adatokat szolgáltatások és alkalmazások között. Ez magában foglalja, kezelése és a biztonsági házirendeket alkalmazhat, és gondoskodik róla, hogy az Azure-beli virtuális gépek, nem Azure-kiszolgálók és Azure PaaS-szolgáltatások megfelelő. A Security Center a szüksége lesz egy Madártávlatból a számítási feladatok, a hálózati biztonsági hagyatéki a fókuszban lévő látható-e az eszközöket kínál. 

### <a name="manage-organization-security-policy-and-compliance"></a>Kezelheti a szervezet biztonsági házirend és megfelelőség

Egy ismert, és győződjön meg arról, a számítási feladatok biztonságosak, és a biztonsági házirendeket kellene szabott kezdődik, az alapvető biztonsági. A Security Center a szabályzatokat az Azure policy vezérlők épülnek, mert a teljes körű és rugalmas kap egy **világszínvonalú házirend megoldás**. A Security Centerben a szabályzatok felügyeleti csoportokhoz, futtathatók, előfizetések között, és a egy egész bérlő esetében még akkor is beállíthatja.

![A Security Center irányítópultja](media/security-center-intro/sc-dashboard.png)

Security Center segít **azonosíthatja az ÁRNYÉK-előfizetések**. Megnézzük az előfizetések feliratú **alá nem tartozó** az irányítópulton, azonnal, ha újonnan létrejönnek az előfizetések és győződjön meg arról, hogy a szabályzatok hatálya alá, és az Azure Security Center által védett.

![A Security Center házirend irányítópult](media/security-center-intro/sc-policy-dashboard.png)

A Security Center speciális monitorozási funkciókat is lehetővé teszik **nyomon követheti és kezelheti a megfelelőségi és szabályozási idővel**. A **összesített megfelelőségi** biztosít az IP-címek fenntartási az előfizetések megfelelnek a házirendek a számítási feladathoz tartozó mérték. 

![Idővel a Security Center-szabályzat](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Folyamatos értékelésekkel

A Security Center folyamatosan deríti fel a számítási feladatok telepített új erőforrásokhoz, és felméri, hogy ajánlott biztonsági eljárások megfelelően vannak konfigurálva, ha nem, azok van megjelölve, és mi javaslatok rangsorolt listájának lekérése szeretné javítani a gépek védelme érdekében.

Az egyik leghatékonyabb eszköze a Security Center folyamatosan állapotának figyelése a security a hálózat nem biztosít a **hálózati térkép létrehozásához**. A térkép segítségével tekintse meg a topológia a számítási feladatok, így láthatja, ha minden egyes csomóponton megfelelően van-e konfigurálva. Láthatja, hogyan a csomópontok csatlakoznak, amely sikerült potenciálisan egyszerűbbé a támadónak a hálózat mentén kötés nem kívánt kapcsolat blokkolható, ami segít.

![A Security Center hálózati térkép létrehozásához](media/security-center-intro/sc-net-map.png)

A biztonság csökkentése a Security Center teszi hozzáadásával egy lépés egyszerűbb, riasztást küld egy **biztonságos pontszám**. A biztonságos pontszámok most már társított segítségével megismerheti, mennyire fontos javaslatot, az átfogó biztonsági rendszer kialakításához kap javaslatot. Ez elengedhetetlen, hogy lehetővé teszi **biztonsági munkánk**.

![A Security Center biztonságos pontszám](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optimalizálása és a biztonság fokozása érdekében ajánlott vezérlők

A szív érték az Azure Security Center javaslatait rejlik. A javaslatok a talált a számítási feladatok az adott biztonsági problémák vannak igazítva, és a Security Center elvégzi a biztonsági rendszergazdai munkához, nem csak a biztonsági rések keresése, de így a konkrét utasításokat, hogy hogyan távolíthatja el azokat.

![Security Center-javaslatok](media/security-center-intro/sc-recommendations.png)

Ily módon a Security Center lehetővé teszi nemcsak a biztonsági szabályzatok beállítása, de az erőforrások biztonságos konfigurációs szabványok igyekszik.

A javaslatok segítenek csökkenteni a támadási felületet az erőforrások száma mindegyik. Ha olyan erőforrástípusok eltérően adatokon, és rendelkezik a saját szabványok Azure-beli virtuális gépek, nem Azure-kiszolgálókat, és az Azure PaaS-szolgáltatások, például az SQL és Storage-fiókok és több - tartalmazó.

![A Security Center javaslat példa](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Védelem a fenyegetések ellen

A Security Center veszélyforrások elleni védelem lehetővé teszi a észlelhesse és előzhesse meg az infrastruktúra fenyegetésekre rétegként szolgáltatás (IaaS), nem Azure-kiszolgálók, valamint meghajtóbetűjeleket platformok (PaaS) szolgáltatás az Azure-ban.

A Security Center veszélyforrások elleni védelem tartalmazza a környezetben az internetes támadási fázis elemzésével segítségével jobban megismerheti egy támadássorozatot, ahol elindult, és mi a teljes esettanulmányt riasztások automatikusan hátterében fusion támadási fázis elemzés az erőforrások, mint hatás típusú.



![A Security Center a támadás javaslat](media/security-center-intro/sc-attack-recommendation.png)

### <a name="advanced-threat-protection"></a>Komplex veszélyforrások elleni védelem

A Security Centerrel natív integráció a Windows Defender komplex veszélyforrások elleni védelem beépített kap. Ez azt jelenti, hogy bármilyen konfigurálás nélkül a Windows virtuális gépek és kiszolgálók teljesen integrálva van a Security Center javaslatokat és értékeléseket. Speciális fenyegetésészlelés is a Linux rendszerű virtuális gépek és kiszolgálók beépített érhető el.

Ezenfelül a Security Center segítségével automatizálhatja az Alkalmazásvezérlési házirendek kiszolgálói környezetekben. A Security Center az adaptív alkalmazásvezérlők végpontok közötti alkalmazás-engedélyezési engedélyezése a Windows-kiszolgáló között. Nem kell a szabályok létrehozásához, és ellenőrizze a megsértések, azt az összes automatikusan történik, az Ön számára.

### <a name="protect-paas"></a>PaaS védelme

A Security Center segít a fenyegetések észlelését az Azure PaaS-szolgáltatások között. Azure-szolgáltatások többek között az Azure App Service, Azure SQL, Azure Storage-fiókot, és további szolgáltatások célzó fenyegetések észlelését. A natív integráció a Microsoft Cloud App Security felhasználói és az entitás viselkedési Analytics (UEBA), hajtsa végre az Azure-Tevékenységnaplók rendellenesség-észlelést is is igénybe vehet.

### <a name="block-brute-force-attacks"></a>Blokk találgatásos támadások kényszerítése

A Security Center segítségével korlátozhatja a találgatásos támadásokkal szembeni sérülékenységet. Hozzáférés csökkentésével, a virtuális gép portok, a just-in-time VM access használatával is felvértezni megakadályozzák, hogy a szükségtelen hozzáférést a hálózaton. Beállíthat biztonságos hozzáférési szabályzat a kiválasztott portokat, csak a jogosult felhasználók, forrás IP-címtartományok vagy IP-címek, engedélyezett és a egy korlátozott ideig.

![A Security Center találgatásos támadás](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Data services védelme

A Security Center tartalmaz, amelyekkel hajtsa végre az automatikus besorolás az adatok Azure SQL-ben. Azure SQL- és tárolási szolgáltatások és azok mérséklésére vonatkozó értékelések a potenciális biztonsági réseket is kaphat.

## <a name="get-secure-faster"></a>Gyorsabb, biztonságos beolvasása

Zökkenőmentes integráció a Microsoft más biztonsági megoldások, például a Microsoft Cloud App Security és a Windows Defender komplex veszélyforrások elleni védelem érdekében győződjön meg arról, hogy a biztonsági kombinálva (beleértve az Azure Policy és a Log Analytics) natív Azure-integráció megoldás átfogó, valamint az olyan egyszerű üzembe helyezni, és elérhetővé teheti.

Ezenfelül a teljes megoldás túl az Azure a helyszíni adatközpontokban és más felhőkben futó munkaterhelések bővítheti.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Automatikus észleléséhez, és az Azure-erőforrások előkészítése

A Security Center zökkenőmentes, natív integrációt biztosít az Azure és az Azure erőforrások. Ez azt jelenti, hogy lekéréses együtt egy teljes körű biztonsági történetet használata esetén az Azure Policy és beépített a Security Center-szabályzatok az Azure-erőforrások között, majd győződjön meg arról, hogy a teljes tartalom automatikusan vonatkozik az újonnan felderített erőforrásokhoz, hozza létre őket az Azure-ban.

Kiterjedt Erőforrásnapló-gyűjtés – Windows és Linux naplóinak a minden kihasználhatók a biztonsági elemzési motor a és a javaslatok és riasztások létrehozásához használt.

## <a name="next-steps"></a>További lépések

- A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Az Azure-előfizetése jelenleg a Security Center Ingyenes tarifacsomagját engedélyezi. A speciális biztonságkezelési és fenyegetésészlelési képességek kihasználásához frissítenie kell a Standard tarifacsomagra. A standard szintű csomag ingyenes, melynek. További információkért tekintse meg a [Security Center díjszabási lapját](https://azure.microsoft.com/pricing/details/security-center/).
- Ha készen áll a Security Center Standard engedélyezéséhez, a [a rövid útmutató: Előkészítése az Azure-előfizetés a Security Center Standard](security-center-get-started.md) végigvezeti a lépéseken.

