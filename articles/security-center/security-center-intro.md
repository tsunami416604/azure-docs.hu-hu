---
title: Mi az az Azure Security Center?| Microsoft Docs
description: Információk az Azure Security Centerről, annak főbb funkcióiról és működéséről.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/06/2019
ms.author: v-mohabe
ms.openlocfilehash: 762ab2800193aa084b771b7d000b383a7004097e
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984919"
---
# <a name="what-is-azure-security-center"></a>Mi az az Azure Security Center?

Azure Security Center egy egységes infrastruktúra-alapú biztonsági felügyeleti rendszer, amely erősíti az adatközpontok biztonsági állapotát, és komplex veszélyforrások elleni védelmet biztosít a felhőben lévő hibrid számítási feladatokhoz, akár az Azure-ban, akár nem a helyszínen.

Az erőforrások biztonságának fenntartása a felhőalapú szolgáltató, az Azure és Ön, az ügyfél közötti közös erőfeszítés. Gondoskodnia kell arról, hogy a munkaterhelések biztonságban legyenek a felhőre való áttéréskor, és ha a IaaS (infrastruktúra-szolgáltatás) való áttérés során több ügyfél-felelősségre van szüksége, mint amennyi a (z) Azure Security Center biztosítja a hálózat megerősítéséhez szükséges eszközöket, gondoskodik a szolgáltatások biztonságossá tételéről, és gondoskodik arról, hogy a biztonsági testhelyzetben legyen.

Azure Security Center a három legsürgetőbb biztonsági kihívással foglalkozik:

-   **Gyorsan változó munkaterhelések** – a felhő erőssége és kihívása. Egyrészt a végfelhasználók többek között felhatalmazást kapnak. A másik lehetőség, hogy hogyan gondoskodik arról, hogy az állandóan változó szolgáltatások használata és létrehozása a biztonsági szabványok szerint történjen, és kövesse az ajánlott biztonsági eljárásokat?

-   **Egyre kifinomultabb támadások** – bárhol is futtathatja a számítási feladatokat, a támadások egyre kifinomultabbak maradnak. Biztosítania kell a nyilvános Felhőbeli munkaterheléseket, amelyek valójában olyan internetes munkaterhelést jelentenek, amely még nagyobb biztonságot is biztosít, ha nem követi az ajánlott biztonsági eljárásokat.

-   A **biztonsági képességek rövid időn** belül megtalálhatók – a biztonsági riasztások és a riasztási rendszerek száma jóval több, mint a környezetek védelmének biztosításához szükséges háttérrel és tapasztalattal rendelkező rendszergazdák száma. Ha naprakészen tart a legújabb támadásokkal kapcsolatban, állandó kihívást jelent, ami lehetetlenné teszi a rendelkezésre állást, miközben a biztonság világa folyamatosan változik.

Ha segítségre van szüksége a kihívások elleni védelemben, Security Center a következőket teszi lehetővé:

-   A **biztonsági testhelyzet megerősítése**: Security Center felméri a környezetet, és lehetővé teszi az erőforrások állapotának megismerését, és hogy azok biztonságosak-e.

-   **Fenyegetések elleni védelem**: Security Center felméri a számítási feladatokat, és növeli a fenyegetések megelőzésére vonatkozó ajánlásokat és a veszélyforrások észlelésére vonatkozó riasztásokat.

-   **Gyorsabb biztonságos használat**: Security Center minden a Felhőbeli sebességgel történik. Mivel natív módon van integrálva, az Security Center egyszerű üzembe helyezése, amely az Azure-szolgáltatásokkal való kiépítést és védelmet biztosít.

## <a name="architecture"></a>Architektúra

Mivel a Security Center natívan része az Azure-nak, az Azure-ban a Pásti-szolgáltatások, beleértve a Service Fabricokat, az SQL-adatbázisokat és a Storage-fiókokat is, a Security Center figyeli és védi őket, anélkül, hogy üzembe kellene

Emellett Security Center a Microsoft monitoring Agent telepítésével a Windows-és Linux-kiszolgálókon a nem Azure-beli kiszolgálókat és virtuális gépeket is védi a felhőben vagy a helyszínen. Az Azure Virtual Machines szolgáltatás automatikusan kiépítve van Security Centerban.

Az ügynököktől és az Azure-ból gyűjtött események a biztonsági elemzési motorban vannak összekapcsolva, hogy testreszabott ajánlásokat nyújtsanak (megerősítő feladatok), amelyekkel meggyőződhet arról, hogy a munkaterhelések biztonságosak, és észlelik a fenyegetések észlelésére vonatkozó riasztásokat. Az ilyen riasztásokat a lehető leghamarabb meg kell vizsgálnia, hogy a számítási feladatokban ne legyenek kártékony támadások.

A Security Center engedélyezésekor a Security Center beépített biztonsági szabályzata Azure Policy a Security Center kategóriába tartozó beépített kezdeményezésként jelenik meg. A beépített kezdeményezés automatikusan hozzá van rendelve az összes Security Center regisztrált előfizetéshez (ingyenes vagy standard szintű csomag). A beépített kezdeményezés csak naplózási házirendeket tartalmaz. A Azure Policy Security Center házirendjeivel kapcsolatos további információkért lásd: [a biztonsági házirendek használata](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Biztonsági helyzet megerősítése

Azure Security Center lehetővé teszi a biztonsági helyzet megerősítését. Ez azt jelenti, hogy segít azonosítani és végrehajtani az ajánlott biztonsági eljárásokkal és a gépek, az adatszolgáltatások és az alkalmazások megvalósításával kapcsolatos korlátozási feladatokat. Ez magában foglalja a biztonsági szabályzatok kezelését és érvényesítését, valamint arról, hogy az Azure-beli virtuális gépek, a nem Azure-kiszolgálók és az Azure Pásti-szolgáltatások megfelelőek-e. A Security Center biztosítja azokat az eszközöket, amelyekre szüksége van a munkaterhelések madártávlatból-nézetéhez, és a hálózati biztonsági ingatlanra fókuszált láthatóságot biztosít. 

### <a name="manage-organization-security-policy-and-compliance"></a>A szervezet biztonsági házirendjének és megfelelőségének kezelése

Ez egy biztonsági alapszintű, és gondoskodik arról, hogy a munkaterhelések biztonságosak legyenek, és a rendszer a testre szabott biztonsági házirendekkel kezdődik. Mivel a Security Center összes szabályzata az Azure Policy Controls szolgáltatásra épül, a **világszínvonalú házirend-megoldás**teljes skáláját és rugalmasságát tapasztalja. A Security Centerban beállíthatja, hogy a szabályzatok a felügyeleti csoportokon, az előfizetések között, vagy akár egy teljes bérlőn fussanak.

![A Security Center irányítópultja](media/security-center-intro/sc-dashboard.png)

Security Center segít **AZONOSÍTANI az árnyék-előfizetéseket**. Ha az irányítópulton **nem** szereplő előfizetéseket keres, azonnal megtudhatja, hogy vannak-e újonnan létrehozott előfizetések, és gondoskodjon róla, hogy a szabályzatok hatálya alá legyenek szabva, és Azure Security Center által védettek legyenek.

![Security Center szabályzat irányítópultja](media/security-center-intro/sc-policy-dashboard.png)

A Security Center fejlett monitorozási képességei lehetővé teszik a **megfelelőség és a szabályozás időbeli nyomon követését és kezelését**. A **teljes megfelelőség** azt méri, hogy az előfizetések milyen mértékben felelnek meg a számítási feladathoz társított szabályzatoknak. 

![Security Center a szabályzat időbeli alakulását](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Folyamatos értékelések

Security Center folyamatosan felfedi a számítási feladatokban üzembe helyezett új erőforrásokat, és megállapítja, hogy az ajánlott biztonsági eljárásoknak megfelelően vannak-e konfigurálva, és ha nem, a rendszer megjelöli őket, és a javaslatok rangsorolt listáját kapja a gépek biztonsága érdekében javítania kell a számítógépet.

Az egyik leghatékonyabb eszköz Security Center biztosítja a hálózat biztonsági állapotának folyamatos figyelését a **hálózati Térkép**. A térkép segítségével megtekintheti a számítási feladatok topológiáját, így megtekintheti, hogy az egyes csomópontok megfelelően vannak-e konfigurálva. Láthatja, hogy a csomópontok hogyan kapcsolódnak egymáshoz, ami segít letiltani a nemkívánatos kapcsolatokat, amelyek megkönnyítik a támadók számára a hálózatra való csúszást.

![Security Center hálózati Térkép](media/security-center-intro/sc-net-map.png)

A Security Center egy **biztonságos pontszám**hozzáadásával megkönnyíti a biztonsági riasztások egy lépésének csökkentését. A biztonságos pontszámok mostantól a kapott javaslatokhoz vannak társítva, amelyek segítségével megismerheti, hogy az egyes javaslatok milyen fontosak az általános biztonsági helyzetek eléréséhez. Ez kulcsfontosságú a **biztonsági feladatok rangsorolásának**lehetővé tételéhez.

![Security Center biztonságos pontszám](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Az ajánlott vezérlők konfigurálásával optimalizálhatja és javíthatja a biztonságot.

Azure Security Center értékének szíve a javaslataiban rejlik. A javaslatok a számítási feladatokban található biztonsági kérdésekre vannak szabva, és Security Center a biztonsági rendszergazda dolgozik Önnek, nem csupán a biztonsági rések megtalálásával, hanem konkrét útmutatást ad arról, hogy miként lehet megszabadulni belőlük.

![Security Center-javaslatok](media/security-center-intro/sc-recommendations.png)

Így a Security Center lehetővé teszi, hogy ne csak biztonsági házirendeket állítson be, hanem biztonságos konfigurációs szabványokat alkalmazzon az erőforrások között.

A javaslatok segítségével csökkentheti a támadási felületet az egyes erőforrások között. Ez magában foglalja az Azure Virtual Machines, a nem Azure-kiszolgálókat és az Azure Pásti szolgáltatásait, például az SQL-és a Storage-fiókokat, valamint az egyes erőforrások különböző típusait, és saját szabványokkal rendelkeznek.

![Példa Security Center javaslatra](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Védelem a fenyegetések ellen

Security Center fenyegetések elleni védelme lehetővé teszi a fenyegetések észlelését és megelőzését a szolgáltatásként (IaaS), a nem Azure-beli kiszolgálókon, valamint az Azure-ban szolgáltatásként (Pásti) elérhető platformokon.

Security Center fenyegetés elleni védelme magában foglalja a Fusion kill-Chain Analysis szolgáltatást, amely a környezetben a Cyber kill-Chain Analysis szolgáltatáson alapuló riasztásokat automatikusan összekapcsolja, így könnyebben megismerheti a támadási kampányok teljes történetét milyen hatással van az erőforrásaira.



![Security Center támadási javaslat](media/security-center-intro/sc-attack-recommendation.png)

### <a name="advanced-threat-protection"></a>Komplex veszélyforrások elleni védelem

A Security Center révén natív integrációt érhet el a Windows Defender komplex veszélyforrások elleni védelemmel a dobozból. Ez azt jelenti, hogy konfiguráció nélkül a Windows rendszerű virtuális gépek és kiszolgálók teljes mértékben integrálva vannak Security Center javaslataival és értékelésével. A komplex veszélyforrások észlelése a Linux rendszerű virtuális gépek és kiszolgálók esetében is elérhető.

Emellett a Security Center lehetővé teszi az alkalmazás-vezérlési házirendek automatizálását kiszolgálói környezetekben. Az adaptív alkalmazás-vezérlők a Security Center lehetővé teszik a teljes körű alkalmazás engedélyezési feltételeit a Windows-kiszolgálókon. Nincs szükség a szabályok létrehozására és a szabálysértések vizsgálatára, mindezt automatikusan elvégezheti Önnek.

### <a name="protect-paas"></a>Pásti-védelem

A Security Center segítségével észlelheti a fenyegetéseket az Azure Pásti szolgáltatásai között. Az Azure-szolgáltatásokhoz (például Azure App Service, Azure SQL, Azure Storage-fiók és több adatszolgáltatáshoz) irányuló fenyegetések észlelése is észlelhető. Emellett kihasználhatja a natív integrációt Microsoft Cloud App Security felhasználói és entitások viselkedés-elemzésével (UEBA), hogy az Azure-beli tevékenység naplófájljaiban anomália-észlelést végezzen.

### <a name="block-brute-force-attacks"></a>Találgatásos támadásokat blokkol

Security Center segít korlátozni a találgatásos támadásokra való kitettséget. A virtuálisgép-portokhoz való hozzáférés csökkentésével a virtuális gépek igény szerinti elérését használva megerősítheti a hálózatot a szükségtelen hozzáférés megakadályozása érdekében. A biztonságos hozzáférési házirendeket beállíthatja a kiválasztott portokon, csak a jogosult felhasználók, az engedélyezett forrás IP-címtartományok vagy az IP-címek, valamint a korlátozott időtartamra.

![Security Center találgatásos támadás](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Az adatszolgáltatások védelme

A Security Center olyan képességeket tartalmaz, amelyek segítenek az adatok automatikus besorolásában az Azure SQL-ben. Az Azure SQL és a Storage szolgáltatásokban is lehetséges biztonsági réseket is kiértékelheti, és javaslatokat kaphat a megoldásra.

### <a name="protect-iot-and-hybrid-cloud-workloads"></a>A IoT és a hibrid felhőalapú számítási feladatok elleni védelem

A IoT (eszközök internetes hálózata) Azure Security Center leegyszerűsíti a hibrid munkaterhelések védelmét azáltal, hogy egységes láthatóságot és vezérlést, adaptív veszélyforrások elleni védelmet, valamint intelligens veszélyforrások észlelését és reagálást tesz elérhetővé az Edge-ben, a helyszínen, Azure és más felhőkben. További információ: [Azure Security Center a IoT](https://docs.microsoft.com/azure/asc-for-iot/).

## <a name="get-secure-faster"></a>Gyorsabb biztonságos használat

A natív Azure-integráció (beleértve a Azure Policy és a Azure Monitor naplókat is), valamint a más Microsoft biztonsági megoldásokkal való zökkenőmentes integrációt, például a Microsoft Cloud App Security és a Windows Defender komplex veszélyforrások elleni védelem súgójában ellenőrizze, hogy a biztonsági megoldás átfogó, valamint egyszerű a bevezetéshez és a bevezetéshez.

Emellett a teljes megoldást az Azure-on kívül is kiterjesztheti a többi felhőben és a helyszíni adatközpontokban futó számítási feladatokhoz.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Azure-erőforrások automatikus felderítése és előkészítése

A Security Center zökkenőmentes, natív integrációt biztosít az Azure-és az Azure-erőforrásokkal. Ez azt jelenti, hogy az összes Azure-erőforráshoz Azure Policy és beépített Security Center szabályzatot tartalmazó teljes biztonsági történetet egyesítheti, és gondoskodhat arról, hogy a rendszer automatikusan alkalmazza a teljes dolgot az újonnan felderített erőforrásokra a létrehozásuk során Az Azure-ban.

Kiterjedt naplózási gyűjtemény – a Windows és a Linux rendszerből származó naplók mind kihasználva vannak a biztonsági elemzési motorban, és javaslatok és riasztások létrehozásához használatosak.

## <a name="next-steps"></a>További lépések

- A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Az Azure-előfizetése jelenleg a Security Center Ingyenes tarifacsomagját engedélyezi. A speciális biztonságkezelési és fenyegetésészlelési képességek kihasználásához frissítenie kell a Standard tarifacsomagra. A standard szintű csomag ingyenesen kipróbálható. További információkért tekintse meg a [Security Center díjszabási lapját](https://azure.microsoft.com/pricing/details/security-center/).
- Ha most már készen áll a Security Center Standard engedélyezésére [, a gyors üzembe helyezési útmutató: Security Center standard szintű](security-center-get-started.md) Azure-előfizetésének bevezetésével végigvezeti a lépéseken.

