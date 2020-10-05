---
title: Mi az az Azure Security Center?| Microsoft Docs
description: Ez az oldal leírja Security Center főbb előnyeit – felfedezheti a biztonsági állapotát, és javíthatja a Felhőbeli és a helyszíni erőforrások lefedettségével.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 2eee46f99af247ffd0e11b262ace0a96453270d4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91323674"
---
# <a name="what-is-azure-security-center"></a>Mi az az Azure Security Center?

A Azure Security Center egy egységes infrastruktúra-alapú biztonsági felügyeleti rendszer, amely erősíti az adatközpontok biztonsági állapotát, és komplex veszélyforrások elleni védelmet biztosít a felhőben lévő hibrid számítási feladatokhoz, akár az Azure-ban, akár nem, akár a helyszínen is.

Az erőforrások biztonságának fenntartása a felhőalapú szolgáltató, az Azure és Ön, az ügyfél közötti közös erőfeszítés. Gondoskodnia kell arról, hogy a munkaterhelések biztonságban legyenek a felhőre való áttéréskor, és ha a IaaS (infrastruktúra-szolgáltatás) való áttérés során több ügyfél-felelősségre van szüksége, mint amennyi a (z) Azure Security Center biztosítja a hálózat megerősítéséhez szükséges eszközöket, gondoskodik a szolgáltatások biztonságossá tételéről, és gondoskodik arról, hogy a biztonsági testhelyzetben legyen.

Az Azure Security Center a három legsürgetőbb biztonsági kihívásra reagál:

-   **Gyorsan változó munkaterhelések** – a felhő erőssége és kihívása. A felhasználók egyfelől több lehetőséggel rendelkeznek. Másfelől azonban hogyan tud meggyőződni arról, hogy a felhasználók által létrehozott és használt, folyamatosan változó szolgáltatósok megfelelnek az Ön biztonsági elvárásainak, és követik az ajánlott biztonsági eljárásokat?

-   **Egyre kifinomultabb támadások** – Bárhol is futtatja a számítási feladatokat, a támadások egyre kifinomultabbá válnak. Biztonságossá kell tennie a nyilvános felhőben található számítási feladatait, amelyek tulajdonképpen az interneten található feladatok, és az ajánlott biztonsági eljárások követése nélkül sebezhetővé teszik az Ön környezetét.

-   **Kevés a biztonsági szakértő** – A biztonsági riasztások és riasztási rendszerek száma jelentősen meghaladja a környezetek védelemmel való ellátásához szükséges háttértudással és tapasztalattal rendelkező rendszergazdák számát. Kihívást jelenthet naprakésznek maradni a legújabb támadásfajtákról, a folyamatosan változó biztonsági világban így szinte lehetetlen egy helyben maradni.

A Security Center eszközeivel megvédheti magát a fenti kihívásokkal szemben, és a következőket teheti:

-   A **biztonsági helyzet megerősítése**: Security Center felméri a környezetet, és lehetővé teszi az erőforrások állapotának megismerését, és hogy azok biztonságosak-e.

-   **Fenyegetések elleni védelem**: Security Center felméri a számítási feladatokat, és növeli a veszélyforrások megelőzésére vonatkozó ajánlásokat és biztonsági riasztásokat.

-   **Gyorsabb biztonság**: A Security Centerben minden a felhőkörnyezetek sebességével történik. A natív integrációnak köszönhetően a Security Center üzembe helyezése könnyű, ezután pedig kihasználhatja az automatikus kiépítés és az Azure-szolgáltatások nyújtotta védelem előnyeit.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="architecture"></a>Architektúra

Mivel Security Center natívan része az Azure-nak, az Azure-ban a Pásti-szolgáltatások, beleértve a Service Fabric, a SQL Database, az SQL felügyelt példányokat és a Storage-fiókokat, a Security Center figyelése és védelme anélkül, hogy az üzembe helyezés szükségessé vált.

Emellett Security Center a nem Azure-beli kiszolgálókat és virtuális gépeket is védi a felhőben vagy a helyszínen, Windows-és Linux-kiszolgálókon egyaránt, ha telepíti az Log Analytics ügynököt. Az Azure Virtual Machines szolgáltatás automatikusan kiépítve van Security Centerban.

Az ügynököktől és az Azure-ból gyűjtött események korrelálnak a Security Analytics-motorban, hogy testre szabott ajánlásokat nyújtsanak (megerősítő feladatok), hogy a munkaterhelések biztonságosak legyenek, és biztonsági riasztások legyenek. Az ilyen riasztásokat a lehető leghamarabb meg kell vizsgálnia, hogy a számítási feladatokban ne legyenek kártékony támadások.

A Security Center engedélyezésekor a Security Center beépített biztonsági szabályzata Azure Policy a Security Center kategóriába tartozó beépített kezdeményezésként jelenik meg. A beépített kezdeményezés automatikusan hozzá van rendelve az összes Security Center regisztrált előfizetéshez (ingyenes vagy standard díjszabási szint). A beépített kezdeményezés csak naplózási házirendeket tartalmaz. A Azure Policy Security Center házirendjeivel kapcsolatos további információkért lásd: [a biztonsági házirendek használata](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Biztonsági helyzet megerősítése

Azure Security Center lehetővé teszi a biztonsági helyzet megerősítését. Ez azt jelenti, hogy segít azonosítani és végrehajtani az ajánlott biztonsági eljárásokkal és a gépek, az adatszolgáltatások és az alkalmazások megvalósításával kapcsolatos korlátozási feladatokat. Ez magában foglalja a biztonsági szabályzatok kezelését és érvényesítését, valamint arról, hogy az Azure-beli virtuális gépek, a nem Azure-kiszolgálók és az Azure Pásti-szolgáltatások megfelelőek-e. A Security Center biztosítja azokat az eszközöket, amelyekre szüksége van a munkaterhelések madártávlatból-nézetéhez, és a hálózati biztonsági ingatlanra fókuszált láthatóságot biztosít. 

### <a name="manage-organization-security-policy-and-compliance"></a>A szervezet biztonsági házirendjének és megfelelőségének kezelése

Ez egy biztonsági alapszintű, és gondoskodik arról, hogy a munkaterhelések biztonságosak legyenek, és a rendszer a testre szabott biztonsági házirendekkel kezdődik. Mivel a Security Center összes házirendje Azure Policy vezérlőelemekre épül, a **világszínvonalú házirend-megoldás**teljes skáláját és rugalmasságát tapasztalja. A Security Centerban beállíthatja, hogy a szabályzatok a felügyeleti csoportokon, az előfizetések között, vagy akár egy teljes bérlőn fussanak.

:::image type="content" source="./media/security-center-intro/sc-dashboard.png" alt-text="Házirend-kezelés lap":::

Security Center segít **AZONOSÍTANI az árnyék-előfizetéseket**. Ha az irányítópulton **nem** szereplő előfizetéseket keres, azonnal megtudhatja, hogy vannak-e újonnan létrehozott előfizetések, és gondoskodjon róla, hogy a szabályzatok hatálya alá legyenek szabva, és Azure Security Center által védettek legyenek.

:::image type="content" source="./media/security-center-intro/sc-policy-dashboard.png" alt-text="Házirend-kezelés lap":::

### <a name="continuous-assessments"></a>Folyamatos értékelések

Security Center folyamatosan felfedi a számítási feladatok során üzembe helyezett új erőforrásokat, és megállapítja, hogy az ajánlott biztonsági eljárásoknak megfelelően vannak-e konfigurálva, és ha nem, akkor a rendszer megjelöli őket, és megtekinti azokat a javaslatokat, amelyeket a gépek védelme érdekében javítania kell.

Annak megértéséhez, hogy az egyes javaslatok milyen fontosak az általános biztonsági helyzethez, Security Center csoportosítsa a javaslatokat a biztonsági ellenőrzésekben, és hozzáadja a **biztonságos pontszám** értéket az egyes vezérlőkhöz. Ez kulcsfontosságú a **biztonsági feladatok rangsorolásának**lehetővé tételéhez.

:::image type="content" source="./media/security-center-intro/sc-secure-score.png" alt-text="Házirend-kezelés lap":::

### <a name="network-map"></a>Hálózati Térkép

Az egyik leghatékonyabb eszköz Security Center biztosítja a hálózat biztonsági állapotának folyamatos figyelését a **hálózati Térkép**. A térkép segítségével megtekintheti a számítási feladatok topológiáját, így megtekintheti, hogy az egyes csomópontok megfelelően vannak-e konfigurálva. Láthatja, hogy a csomópontok hogyan kapcsolódnak egymáshoz, ami segít letiltani a nemkívánatos kapcsolatokat, amelyek megkönnyítik a támadók számára a hálózatra való csúszást.

:::image type="content" source="./media/security-center-intro/sc-net-map.png" alt-text="Házirend-kezelés lap":::


### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Az ajánlott vezérlők konfigurálásával optimalizálhatja és javíthatja a biztonságot.

Azure Security Center értékének szíve a javaslataiban rejlik. A javaslatok a számítási feladatokban található biztonsági kérdésekre vannak szabva, és Security Center a biztonsági rendszergazda dolgozik Önnek, nem csupán a biztonsági rések megtalálásával, hanem konkrét útmutatást ad arról, hogy miként lehet megszabadulni belőlük.

Így a Security Center lehetővé teszi, hogy ne csak biztonsági házirendeket állítson be, hanem biztonságos konfigurációs szabványokat alkalmazzon az erőforrások között.

A javaslatok segítségével csökkentheti a támadási felületet az egyes erőforrások között. Ez magában foglalja az Azure Virtual Machines, a nem Azure-kiszolgálókat és az Azure Pásti szolgáltatásait, például az SQL-és a Storage-fiókokat, valamint az egyes erőforrások különböző típusait, és saját szabványokkal rendelkeznek.

:::image type="content" source="./media/security-center-intro/sc-recommendation-example.png" alt-text="Házirend-kezelés lap":::

## <a name="protect-against-threats"></a>Védelem a fenyegetésekkel szemben

Security Center fenyegetések elleni védelme lehetővé teszi a fenyegetések észlelését és megelőzését a szolgáltatásként (IaaS), a nem Azure-beli kiszolgálókon, valamint az Azure-ban szolgáltatásként (Pásti) elérhető platformokon.

Security Center fenyegetés elleni védelme magában foglalja a Fusion kill-Chain Analysis szolgáltatást, amely a környezetben a Cyber kill-Chain Analysis szolgáltatáson alapuló riasztásokat automatikusan összekapcsolja, így könnyebben megismerheti a támadási kampányok teljes történetét, ahol elindult, és milyen hatással van az erőforrásaira.

:::image type="content" source="./media/security-center-intro/sc-alerts.png" alt-text="Házirend-kezelés lap":::

### <a name="integration-with-microsoft-defender-advanced-threat-protection"></a>Integráció a Microsoft Defender komplex veszélyforrások elleni védelemmel

A Security Center tartalmaz automatikus, natív integrációt a Microsoft Defender komplex veszélyforrások elleni védelemmel. Ez azt jelenti, hogy konfiguráció nélkül a Windows és a Linux rendszerű gépek teljes mértékben integrálva vannak Security Center javaslataival és értékelésével.

Emellett a Security Center lehetővé teszi az alkalmazás-vezérlési házirendek automatizálását kiszolgálói környezetekben. A Security Center adaptív alkalmazás-vezérlői lehetővé teszik a teljes körű alkalmazás-jóváhagyási listát a Windows-kiszolgálókon. Nincs szükség a szabályok létrehozására és a szabálysértések vizsgálatára, mindezt automatikusan elvégezheti Önnek.

### <a name="protect-paas"></a>Pásti-védelem

A Security Center segítségével észlelheti a fenyegetéseket az Azure Pásti szolgáltatásai között. Az Azure-szolgáltatásokhoz (például Azure App Service, Azure SQL, Azure Storage-fiók és több adatszolgáltatáshoz) irányuló fenyegetések észlelése is észlelhető. Emellett kihasználhatja a natív integrációt Microsoft Cloud App Security felhasználói és entitások viselkedés-elemzésével (UEBA), hogy az Azure-beli tevékenység naplófájljaiban anomália-észlelést végezzen.

### <a name="block-brute-force-attacks"></a>Találgatásos támadásokat blokkol

Security Center segít korlátozni a találgatásos támadásokra való kitettséget. A virtuálisgép-portokhoz való hozzáférés csökkentésével a virtuális gépek igény szerinti elérését használva megerősítheti a hálózatot a szükségtelen hozzáférés megakadályozása érdekében. A biztonságos hozzáférési házirendeket beállíthatja a kiválasztott portokon, csak a jogosult felhasználók, az engedélyezett forrás IP-címtartományok vagy az IP-címek, valamint a korlátozott időtartamra.

### <a name="protect-data-services"></a>Az adatszolgáltatások védelme

A Security Center olyan képességeket tartalmaz, amelyek segítenek az adatok automatikus besorolásában az Azure SQL-ben. Az Azure SQL és a Storage szolgáltatásokban is lehetséges biztonsági réseket is kiértékelheti, és javaslatokat kaphat a megoldásra.

### <a name="protect-iot-and-hybrid-cloud-workloads"></a>A IoT és a hibrid felhőalapú számítási feladatok elleni védelem

A Defender for IoT (eszközök internetes hálózata) az egységes láthatóság és vezérlés, az adaptív veszélyforrások megelőzése és az intelligens veszélyforrások elleni védelem, valamint az Azure-ban, a helyszínen, az Azure-ban és más felhőkben futó munkaterhelések terén egyaránt egyszerűsíti a hibrid munkaterhelések védelmét. További információ: [Defender for IoT](../defender-for-iot/index.yml).

## <a name="get-secure-faster"></a>Gyorsabb biztonságos használat

A natív Azure-integráció (beleértve a Azure Policy és a Azure Monitor naplókat is) a más Microsoft biztonsági megoldásokkal való zökkenőmentes integrációval együtt, például a Microsoft Cloud App Security és a Windows Defender komplex veszélyforrások elleni védelem segítségével biztosíthatja, hogy a biztonsági megoldás átfogó, valamint a bevezetéshez és a bevezetéshez egyszerű legyen.

Emellett a teljes megoldást az Azure-on kívül is kiterjesztheti a többi felhőben és a helyszíni adatközpontokban futó számítási feladatokhoz.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Azure-erőforrások automatikus felderítése és előkészítése

A Security Center zökkenőmentes, natív integrációt biztosít az Azure-és az Azure-erőforrásokkal. Ez azt jelenti, hogy az összes Azure-erőforrásra kiterjedő teljes biztonsági történetet összekapcsolhatja, amely magában foglalja Azure Policy és beépített Security Center szabályzatokat, és gondoskodik arról, hogy a teljes dolgot automatikusan alkalmazza az újonnan felderített erőforrásokra, amikor az Azure-ban hozza létre őket.

Kiterjedt naplózási gyűjtemény – a Windows és a Linux rendszerből származó naplók mind kihasználva vannak a biztonsági elemzési motorban, és javaslatok és riasztások létrehozásához használatosak.

## <a name="next-steps"></a>További lépések

- A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).

- A Security Center ingyenes díjszabási szintje minden aktuális Azure-előfizetésnél engedélyezve van, ha a Azure Portal első alkalommal látogatja meg a Azure Security Center irányítópultot, vagy ha az API-n keresztül engedélyezte a programozott módon. A speciális biztonsági felügyelet és a fenyegetések észlelési képességeinek kihasználásához frissítenie kell a standard díjszabási szintre. A standard szintű csomag 30 napig ingyenesen kipróbálható. További információkért tekintse meg a [Security Center díjszabási lapját](https://azure.microsoft.com/pricing/details/security-center/).

- Ha most már készen áll a Security Center Standard engedélyezésére, a gyors üzembe helyezési útmutató [: az Azure-előfizetésének előkészítése a Security Center Standard](security-center-get-started.md) végigvezeti Önt a lépéseken.