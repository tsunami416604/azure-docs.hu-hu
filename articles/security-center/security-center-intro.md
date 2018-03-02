---
title: Mi az az Azure Security Center?| Microsoft Docs
description: "Információk az Azure Security Centerről, annak főbb funkcióiról és működéséről."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: terrylan
ms.openlocfilehash: 08102ce4caead003925aa600f4f7f005b1c336e0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="what-is-azure-security-center"></a>Mi az az Azure Security Center?
Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. A Security Centerrel biztonsági szabályzatokat alkalmazhat a számítási feladatokra, korlátozhatja a fenyegetéseknek való kitettséget, valamint észlelheti és elháríthatja a támadásokat.

Miért használja a Security Centert?

- **Központosított szabályzatkezelés** – Biztosíthatja a vállalati vagy hatósági követelményeknek való megfelelést a hibrid felhőalapú számítási feladatok biztonsági szabályzatainak központi kezelésével.
- **Folyamatos biztonsági értékelés** – Figyelheti a gépek, hálózatok, tárolási és adatszolgáltatások, valamint az alkalmazások biztonsági állapotát a potenciális biztonsági problémák észleléséhez.
- **Végrehajtható javaslatok** – Elháríthatja a biztonsági réseket a rangsorolt és végrehajtható biztonsági javaslatokkal, mielőtt a támadók kihasználhatnák azokat.
- **Speciális felhővédelmi funkciók** – Csökkentheti a fenyegetéseket a felügyeleti portokhoz való igény szerinti hozzáféréssel és a virtuális gépeken futó alkalmazások szabályozott engedélyezésével.
- **Rangsorolt riasztások és incidensek** – A rangsorolt riasztások és incidensek segítségével először a komoly fenyegetésekre összpontosíthat.
- **Integrált biztonsági megoldások** – Számos különféle forrásból, köztük csatlakoztatott partnermegoldásokból származó biztonsági adatokat gyűjthet, kereshet és elemezhet.

A **Security Center – Áttekintés** gyors betekintést biztosít az Azure-beli és nem Azure-beli számítási feladatok biztonsági rendszerébe, lehetővé téve a számítási feladatok biztonsági állapotának felderítését és kiértékelését, valamint a kockázatok észlelését és csökkentését. A beépített irányítópult segítségével azonnal képet kaphat a figyelmet igénylő biztonsági riasztásokról és résekről.

![Áttekintés][1]

## <a name="centralized-policy-management"></a>Központosított szabályzatkezelés
A biztonsági szabályzat határozza meg a számítási feladatokhoz tartozó kívánt konfigurációkat, és segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést. A Security Centerben szabályzatokat határozhat meg, és a számítási feladat típusához vagy az adatok bizalmasságához igazíthatja azokat.

A Security Center-szabályzatok a következő összetevőkből állnak:

- **Adatgyűjtés**: Meghatározza az ügynökkiépítés és biztonsági [adatgyűjtés](security-center-enable-data-collection.md) beállításait.
- **Biztonsági szabályzat**: Meghatározza a Security Center által figyelt ás javasolt vezérlőket a [biztonsági szabályzat](security-center-policies.md) szerkesztésével.
- **E-mail-értesítések**: Meghatározza a biztonsági felelősök kapcsolati adatait és [e-mail-értesítési](security-center-provide-security-contact-details.md) beállításait.
- **Tarifacsomag**: Meghatározza az Ingyenes vagy Standard [díjszabást](security-center-pricing.md). A kiválasztott csomag határozza meg, hogy a Security Center mely szolgáltatásai érhetők el a hatókörbe eső erőforrásokhoz.

![Biztonsági házirend][2]

További információkért lásd: [A biztonsági szabályzatok áttekintése](security-center-policies-overview.md).

## <a name="continuous-security-assessment"></a>Folyamatos biztonsági értékelés
A Security Center elemzi a számítási erőforrások, a virtuális hálózatok, a tárolási és adatszolgáltatások, valamint az alkalmazások biztonsági állapotát. A folyamatos értékelés segít felderíteni a potenciális biztonsági problémákat, például a hiányzó biztonsági frissítésekkel rendelkező rendszereket vagy az elérhetővé vált hálózati portokat. Válasszon egy csempét a Megelőzés szakaszban a további információk, például az erőforráslista és az azonosított biztonsági rések megtekintéséhez.

![Biztonsági állapotfigyelés][3]

További információkért lásd: [Biztonsági állapotfigyelés](security-center-monitoring.md).

## <a name="actionable-recommendations"></a>Végrehajtható javaslatok
A Security Center a potenciális biztonsági rések azonosítása érdekében elemzi az Azure-beli és nem Azure-beli erőforrások biztonsági állapotát. A rangsorolt biztonsági javaslatok listája végigvezeti Önt a biztonsági problémák kezelésének folyamatán.

![Javaslatok][4]

További információkért lásd: [Biztonsági javaslatok kezelése](security-center-recommendations.md).

## <a name="just-in-time-vm-access"></a>Igény szerinti hozzáférés a virtuális gépekhez
Csökkentse a hálózat támadási felületét az Azure-beli virtuális gépek felügyeleti portjaihoz való igény szerinti, szabályozott hozzáféréssel, hogy kevésbé legyen kitéve a találgatásos támadásoknak és más hálózati támadásoknak.

![Igény szerinti hozzáférés a virtuális gépekhez][5]

Adjon meg szabályokat arra vonatkozóan, hogy a felhasználók hogyan kapcsolódhatnak a virtuális gépekhez. Szükség esetén hozzáférést igényelhetnek a Security Centertől vagy a PowerShelltől. Ha a kérelem megfelel a szabályoknak, automatikusan megkapják a hozzáférést a kért időre.

További információkért lásd: [A virtuális gépekhez való hozzáférés kezelése igény szerinti hozzáféréssel](security-center-just-in-time.md).

## <a name="adaptive-application-controls"></a>Adaptív alkalmazásvezérlők
Blokkolja a kártevőket és más nemkívánatos alkalmazásokat az adott Azure számítási feladatokhoz igazított, gépi tanulással működtetett engedélyezési javaslatok alkalmazásával.

![Adaptív alkalmazásvezérlők][6]

Tekintse át a Security Center által létrehozott javasolt alkalmazásengedélyezési szabályokat, és alkalmazza azokat egyetlen kattintással, vagy szerkessze a már konfigurált szabályokat.

További információkért lásd: [Adaptív alkalmazásvezérlők](security-center-adaptive-application.md).

## <a name="prioritized-alerts-and-incidents"></a>Rangsorolt riasztások és incidensek
A Security Center fejlett adatelemzést és globális fenyegetésészlelési intelligenciát használ a támadások és az illetéktelen behatolás utáni tevékenységek észlelésére. A riasztásokat a rendszer rangsorolja és incidensekbe csoportosítja, hogy elsőként a komoly fenyegetésekre összpontosíthasson. Létrehozhat saját egyéni biztonsági riasztásokat is.

![Rangsorolt riasztások és incidensek][7]

A vizuális, interaktív vizsgálati felületnek köszönhetően gyorsan felmérheti a támadások hatókörét és hatását, és előre definiált vagy eseti lekérdezéseket hajthat végre a biztonsági adatok részletesebb vizsgálatára.

További információkért lásd: [Biztonsági riasztások kezelése és válaszadás a riasztásokra](security-center-managing-and-responding-alerts.md).

## <a name="integrate-your-security-solutions"></a>A biztonsági megoldások integrálása
A Security Centerben számos különféle forrásból származó biztonsági adatot gyűjthet, kereshet és elemezhet, például az olyan csatlakoztatott partnermegoldásokból, mint a tűzfalak és más Microsoft-szolgáltatások.

![Biztonsági megoldások integrálása][8]

További információkért lásd: [Biztonsági megoldások integrálása](security-center-partner-integration.md).

## <a name="next-steps"></a>További lépések

- A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Az Azure-előfizetése jelenleg a Security Center Ingyenes tarifacsomagját engedélyezi. A speciális biztonságkezelési és fenyegetésészlelési képességek kihasználásához frissítenie kell a Standard tarifacsomagra. A Standard szint az első 60 napban ingyenesen használható. További információkért tekintse meg a [Security Center díjszabási lapját](https://azure.microsoft.com/pricing/details/security-center/).
- Ha készen áll a Security Center Standard verziójának engedélyezésére, a [Rövid útmutató: Az Azure-előfizetés a Security Center Standard verziójába való felvétele](security-center-get-started.md) végigvezeti Önt a lépéseken.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
