---
title: Mi az az Azure Security Center?| Microsoft Docs
description: Információk az Azure Security Centerről, annak főbb funkcióiról és működéséről.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 682291cfe0e7a06aca1d4b1bef48ba36c72f48d9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839874"
---
# <a name="what-is-azure-security-center"></a>Mi az az Azure Security Center?
Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. A Security Centerrel biztonsági szabályzatokat alkalmazhat a számítási feladatokra, korlátozhatja a fenyegetéseknek való kitettséget, valamint észlelheti és elháríthatja a támadásokat.

Miért használja a Security Centert?

- **Központosított szabályzatkezelés** – Biztosíthatja a vállalati vagy hatósági követelményeknek való megfelelést a hibrid felhőalapú számítási feladatok biztonsági szabályzatainak központi kezelésével.
- **Folyamatos biztonsági értékelés** – Figyelheti a gépek, hálózatok, tárolási és adatszolgáltatások, valamint az alkalmazások biztonságát a potenciális biztonsági problémák észleléséhez.
- **Végrehajtható javaslatok** – Elháríthatja a biztonsági réseket a rangsorolt és végrehajtható biztonsági javaslatokkal, mielőtt a támadók kihasználhatnák azokat.
- **Rangsorolt riasztások és incidensek** – A rangsorolt riasztások és incidensek segítségével először a komoly fenyegetésekre összpontosíthat.
- **Speciális felhővédelmi funkciók** – Csökkentheti a fenyegetéseket a felügyeleti portokhoz való igény szerinti hozzáféréssel és a virtuális gépeken adaptív alkalmazásvezérlés futtatásával.
- **Integrált biztonsági megoldások** – Számos különféle forrásból, köztük csatlakoztatott partnermegoldásokból származó biztonsági adatokat gyűjthet, kereshet és elemezhet.

A **Security Center – Áttekintés** gyors betekintést biztosít az Azure-beli és nem Azure-beli számítási feladatok biztonsági rendszerébe, lehetővé téve a számítási feladatok biztonsági állapotának felderítését és kiértékelését, valamint a kockázatok észlelését és csökkentését. A beépített irányítópult segítségével azonnal képet kaphat a figyelmet igénylő biztonsági riasztásokról és résekről.

![Áttekintés][1]

## <a name="centralized-policy-management"></a>Központosított szabályzatkezelés
A **szabályzat és megfelelőség** (lásd fent) szakaszban gyorsan tájékozódhat az előfizetési lefedettségéről, a szabályzatnak való megfelelőségről és a biztonsági helyzetéről.

### <a name="subscription-coverage"></a>Előfizetési lefedettség
Ez a szakasz megjeleníti az előfizetések összes számát, amihez (olvasása vagy írás) hozzáférése van, és a Security Center lefedettség szintet (Standard vagy ingyenes), ami az előfizetésre vonatkozik:

- **Lefedett (Standard)** – A lefedett előfizetések a Security Center által nyújtott legnagyobb szintű védelem mellett futnak
- **Lefedett (ingyenes)** – A lefedett előfizetések a Security Center által nyújtott korlátozott, ingyenes szintű védelem mellett futnak
- **Nem lefedett** – A Security Center az ilyen állapotú előfizetéseket nem figyeli

A diagram kiválasztásával megnyílik a **Lefedettség** ablak. A lap kiválasztása (**nem lefedett**, **alapszintű lefedettség**, vagy **standard lefedettség**) megadja mindegyik állapotba tartozó előfizetések listáját. Az egyik laphoz tartozó előfizetés kiválasztásával, további információkat kapunk az előfizetésről. Ez az információ lehetővé teszi az előfizetés tulajdonosának azonosítását, akik meg is megkérhetők a Security Center engedélyezésére vagy az előfizetéshez tartozó lefedettség növelésére.

![Security Center lefedettség][9]

### <a name="policy-compliance"></a>Szabályzatmegfelelőség
Szabályzatoknak való megfelelést valamennyi hozzárendelt szabályzat megfelelőségi tényezője határozza meg. Egy felügyeleti csoportra, előfizetésre vagy munkaterületre az összesített megfelelőségi érték a hozzárendelések súlyozott átlaga. A súlyozott átlag figyelembe veszi egy hozzárendelés szabályzatainak számát és azon erőforrások számát, amelyre a hozzárendelés érvényes.

Ha például az előfizetéshez két virtuális gép tartozik, és hozzá van rendelve egy kezdeményezés öt szabályzattal, akkor tíz értékelése lesz az előfizetésében. Ha az egyik virtuális gép két szabályzatnak nem felel meg, akkor az előfizetés teljes megfelelőségi értéke 80% lesz.

Ez a szakasz az általános megfelelőségi arányt és a legkevésbé megfelelő előfizetéseket jeleníti meg. A **Környezet szabályzati megfelelőségének megjelenítése** kiválasztásával megnyílik a **Szabályzatkezelés** ablak. A **Szabályzatkezelés** panel megjeleníti a felügyeleti csoportok, előfizetések és munkaterületek hierarchikus felépítését. Itt kezelhetők a biztonsági szabályzatok egy előfizetést vagy a felügyeleti csoport kiválasztásával.

![Szabályzatkezelés][10]

A biztonsági szabályzat határozza meg a számítási feladatokhoz tartozó kívánt konfigurációkat, és segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést. A Security Centerben lehet definiálni a szabályzatokat és lehet a számítási feladat típusához vagy az adatok bizalmasságához igazítani őket, meghatározva, hogy mely vezérlőket figyeli és ajánlja a Security Center. A Security Centerben a biztonsági szabályzat a felügyeleti csoportra vagy előfizetésre kattintva szerkeszthető. Az Azure Policy használatával is létrehozhat új definíciókat, adhat meg további szabályzatokat és hozzárendelhet szabályzatokat a felügyeleti csoportokhoz.

A **Beállítások szerkesztése >** kiválasztásával módosíthatja a következő Security Center beállításokat előfizetési, felügyeleti csoporti, erőforráscsoport vagy munkaterület szinten:

- **Adatgyűjtés**: Meghatározza az ügynökkiépítés és biztonsági [adatgyűjtés](security-center-enable-data-collection.md) beállításait.
- **E-mail-értesítések**: Meghatározza a biztonsági felelősök kapcsolati adatait és [e-mail-értesítési](security-center-provide-security-contact-details.md) beállításait.
- **Tarifacsomag**: Meghatározza az Ingyenes vagy Standard [díjszabást](security-center-pricing.md). A kiválasztott csomag határozza meg, hogy a Security Center mely szolgáltatásai érhetők el a hatókörbe eső erőforrásokhoz.
- **Biztonsági konfiguráció szerkesztése**: Megtekintheti és módosíthatja azokat az operációs rendszer beállításokat, amelyeket a Security Center az esetleges biztonsági rések azonosítása céljából értékel.

További információ: [A Security Center biztonsági szabályzatainak integrálása az Azure Policyvel](security-center-azure-policy.md).

### <a name="manage-and-govern-your-security-posture"></a>A biztonsági helyzet kezelése és szabályozása
Az irányítópult jobb oldalán a **szabályzat és megfelelőség** alatt olyan információk láthatók, amelyek alapján azonnal beavatkozhat az általános biztonsági helyzet javítása érdekében. Példák:

- A Security Center szabályzatainak definiálása és hozzárendelése után felülvizsgálhatja és követheti a biztonsági követelményeknek való megfelelőséget
- A Security Center biztonsági értesítéseinek elérhetővé tétele a SIEM-összekötő számára
- Szabályzati megfelelőség időbeli alakulása

## <a name="continuous-security-assessment"></a>Folyamatos biztonsági értékelés
Az erőforrás biztonsági higiéniai szakasz a **Security Center – áttekintés** alatt rövid áttekintést ad az erőforrások biztonsági higiéniai állapotáról, megjeleníti az egyes erőforrástípusokra az azonosított gondok számát és biztonsági állapotot. A folyamatos értékelés segít felderíteni a potenciális biztonsági problémákat, például a hiányzó biztonsági frissítésekkel rendelkező rendszereket vagy az elérhetővé vált hálózati portokat.

### <a name="secure-score"></a>Biztonsági pontszám
Az Azure Security Center biztonságos pontszáma áttekinti a biztonsági ajánlásokat és rangsorolja őket, hogy tudja, melyik javaslatot hajtsa végre először, segíti megtalálni a legsúlyosabb biztonsági réseket, ami alapján rangsorolhatók a vizsgálatok. A biztonsági pontszám egy mérés eszköz, amely a biztonság erősítésével segíti a biztonságos munkaterhelés elérését. További információk: [Biztonsági pontszám az Azure Security Centerben](security-center-secure-score.md).

### <a name="health-monitoring"></a>Állapotfigyelés
Az **erőforrás állapotának monitorozása** alatt valamely erőforrástípus kiválasztásakor megkapja az erőforrások listáját és az esetlegesen azonosított biztonsági réseket. Az erőforrástípusok a számítások & alkalmazások, hálózatok, adatok & tárolás és az identitás & hozzáférés.

Kiválasztottuk a **számítás & alkalmazások** típust. A **számítások** alatt négy fül található:

- **Áttekintés**: Monitorozás és a Security Center által azonosított javaslatok.
- **Virtuális gépek és számítógépek**: a virtuális gépek és számítógépek listája, valamint mindegyikre az aktuális biztonsági állapota.
- **Felhőszolgáltatások**: a Security Center által figyelt összes webes és feldolgozói szerepkör listája.
- **App services (előzetes verzió)**: a webalkalmazások és az app service environment-környezetek listája és mindegyikre az aktuális biztonsági állapota.

![Biztonsági állapotfigyelés][3]

További információk: [Biztonsági állapotfigyelés](security-center-monitoring.md).

## <a name="actionable-recommendations"></a>Végrehajtható javaslatok
A Security Center a potenciális biztonsági rések azonosítása érdekében elemzi az Azure-beli és nem Azure-beli erőforrások biztonsági állapotát. Az **Erőforrások** alatti **Javaslatok** kiválasztásakor a rangsorolt biztonsági javaslatok listája végigvezeti Önt a biztonsági problémák kezelésének folyamatán.

![Javaslatok][4]

Bővebb információ: [Biztonság javaslatok kezelése](security-center-recommendations.md).

### <a name="most-prevalent-recommendations"></a>A legelterjedtebb javaslatok
Az irányítópult jobb oldalán az **Erőforrások** alatt megkapja a legnagyobb számú erőforrásra létező legelterjedtebb javaslatok listáját. A legelterjedtebb javaslatok kiemeli, mire kell irányítani a figyelmet. A jobbra mutató nyílra kattintással megkapja a legnagyobb hatású javaslatot.

![A legelterjedtebb javaslatok][11]

Ez az környezetben létező legnagyobb hatású javaslat. Ennek a javaslatnak a megoldása fogja leginkább javítani a megfelelőséget. Ebben a példában a javaslat az „lemeztitkosítás alkalmazása”. A **Megfelelőség javítása** kiválasztásakor leírja a javaslatot és megadja hozzá az érintett erőforrásokat.

![Lemeztitkosítás alkalmazása][12]

## <a name="threat-protection"></a>Fenyegetések elleni védelem
Ez a terület az erőforrásokon észlelt biztonsági riasztásokba és azok súlyossági szintjébe enged betekintést.

### <a name="prioritized-alerts-and-incidents"></a>Rangsorolt riasztások és incidensek
A Security Center fejlett adatelemzést és globális fenyegetésészlelési intelligenciát használ a támadások és az illetéktelen behatolás utáni tevékenységek észlelésére. A riasztásokat a rendszer rangsorolja és incidensekbe csoportosítja, hogy elsőként a komoly fenyegetésekre összpontosíthasson. Létrehozhat saját egyéni biztonsági riasztásokat is.

A **Biztonsági értesítések súlyosság szerint** vagy a **Biztonsági értesítések időbeli alakulása** kiválasztásával részletes információkat kapunk a riasztásokról.

![Rangsorolt riasztások és incidensek][7]

A vizuális, interaktív vizsgálati felületnek köszönhetően gyorsan felmérheti a támadások hatókörét és hatását, és előre definiált vagy eseti lekérdezéseket hajthat végre a biztonsági adatok részletesebb vizsgálatára.

További információk: [Biztonsági riasztások kezelése és válaszadás a riasztásokra](security-center-managing-and-responding-alerts.md).

Az irányítópult jobb oldalán levő információk segítenek rangsorolni a riasztásokat, hogy melyikkel kell kezdeni és segítenek megismerni a gyakori biztonsági réseket:

- **Leginkább támadott erőforrások**: A legnagyobb számú riasztást felmutató konkrét erőforrások
- **Legelterjedtebb riasztások**: A legnagyobb számú erőforrást érintő riasztástípusok

## <a name="just-in-time-vm-access"></a>Igény szerinti hozzáférés a virtuális gépekhez
Csökkentse a hálózat támadási felületét az Azure-beli virtuális gépek felügyeleti portjaihoz való igény szerinti, szabályozott hozzáféréssel, hogy kevésbé legyen kitéve a találgatásos támadásoknak és más hálózati támadásoknak.

![Igény szerinti hozzáférés a virtuális gépekhez][5]

Adjon meg szabályokat arra vonatkozóan, hogy a felhasználók hogyan kapcsolódhatnak a virtuális gépekhez. Szükség esetén hozzáférést igényelhetnek a Security Centertől vagy a PowerShelltől. Ha a kérelem megfelel a szabályoknak, automatikusan megkapják a hozzáférést a kért időre.

További információk: [Manage virtual machine access using just in time](security-center-just-in-time.md) (A virtuális gépekhez való hozzáférés kezelése igény szerinti hozzáférés használata esetén).

## <a name="adaptive-application-controls"></a>Adaptív alkalmazásvezérlők
Blokkolja a kártevőket és más nemkívánatos alkalmazásokat az adott Azure számítási feladatokhoz igazított, gépi tanulással működtetett engedélyezési javaslatok alkalmazásával.

![Adaptív alkalmazásvezérlők][6]

Tekintse át a Security Center által létrehozott javasolt alkalmazásengedélyezési szabályokat, és alkalmazza azokat egyetlen kattintással, vagy szerkessze a már konfigurált szabályokat.

További információk: [Adaptív alkalmazásvezérlők](security-center-adaptive-application.md).

## <a name="integrate-your-security-solutions"></a>A biztonsági megoldások integrálása
A Security Centerben számos különféle forrásból származó biztonsági adatot gyűjthet, kereshet és elemezhet, például az olyan csatlakoztatott partnermegoldásokból, mint a tűzfalak és más Microsoft-szolgáltatások.

![Biztonsági megoldások integrálása][8]

További információk: [Biztonsági megoldások integrálása](security-center-partner-integration.md).

## <a name="next-steps"></a>További lépések

- A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Az Azure-előfizetése jelenleg a Security Center Ingyenes tarifacsomagját engedélyezi. A speciális biztonságkezelési és fenyegetésészlelési képességek kihasználásához frissítenie kell a Standard tarifacsomagra. A standard szintű csomag ingyenes, melynek. További információkért tekintse meg a [Security Center díjszabási lapját](https://azure.microsoft.com/pricing/details/security-center/).
- Ha készen áll a Security Center Standard verziójának engedélyezésére, a [Rövid útmutató: Az Azure-előfizetés a Security Center Standard verziójába való felvétele](security-center-get-started.md) végigvezeti Önt a lépéseken.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
[9]: ./media/security-center-intro/coverage.png
[10]: ./media/security-center-intro/policy-management.png
[11]: ./media/security-center-intro/highest-impact.png
[12]: ./media/security-center-intro/apply-disk-encryption.png
