---
title: Mi az az Azure Security Center?| Microsoft Docs
description: Ez a lap ismerteti a Security Center legfontosabb előnyei – a biztonsági állapot felderítése, és a felhőalapú és a helyszíni erőforrások lefedettségének javítása.
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
ms.date: 06/06/2019
ms.author: memildin
ms.openlocfilehash: 11201d6560319dd34ec829f1fb34bd13d45011d7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435817"
---
# <a name="what-is-azure-security-center"></a>Mi az Azure Security Center?

Az Azure Security Center egy egységes infrastruktúra-biztonsági felügyeleti rendszer, amely megerősíti az adatközpontok biztonsági állapotát, és fejlett veszélyforrások elleni védelmet nyújt a felhőben lévő hibrid számítási feladatokban – függetlenül attól, hogy az Azure-ban vagy sem – és a helyszínen is.

Az erőforrások biztonságának megőrzése a felhőszolgáltató, az Azure és Ön, az ügyfél közös erőfeszítése. Győződjön meg arról, hogy a számítási feladatok biztonságosak, ahogy a felhőbe, és ezzel egyidejűleg, amikor az IaaS (infrastruktúra szolgáltatásként) több ügyfél felelőssége, mint volt paaS (platform szolgáltatásként), és a SaaS (szoftver, mint szolgáltatás). Az Azure Security Center biztosítja a hálózat megkeményedéséhez, a szolgáltatások biztonságához és a biztonsági helyzethez szükséges eszközöket.

Az Azure Security Center a három legsürgetőbb biztonsági kihívásra reagál:

-   **Gyorsan változó számítási feladatok** – Ez a felhő előnye és egyben hátránya is. A felhasználók egyfelől több lehetőséggel rendelkeznek. Másfelől azonban hogyan tud meggyőződni arról, hogy a felhasználók által létrehozott és használt, folyamatosan változó szolgáltatósok megfelelnek az Ön biztonsági elvárásainak, és követik az ajánlott biztonsági eljárásokat?

-   **Egyre kifinomultabb támadások** – Bárhol is futtatja a számítási feladatokat, a támadások egyre kifinomultabbá válnak. Biztonságossá kell tennie a nyilvános felhőben található számítási feladatait, amelyek tulajdonképpen az interneten található feladatok, és az ajánlott biztonsági eljárások követése nélkül sebezhetővé teszik az Ön környezetét.

-   **Kevés a biztonsági szakértő** – A biztonsági riasztások és riasztási rendszerek száma jelentősen meghaladja a környezetek védelemmel való ellátásához szükséges háttértudással és tapasztalattal rendelkező rendszergazdák számát. Kihívást jelenthet naprakésznek maradni a legújabb támadásfajtákról, a folyamatosan változó biztonsági világban így szinte lehetetlen egy helyben maradni.

A Security Center eszközeivel megvédheti magát a fenti kihívásokkal szemben, és a következőket teheti:

-   **A biztonsági helyzet megerősítése:** A Security Center felméri a környezetet, és lehetővé teszi az erőforrások állapotának és biztonságának megértését.

-   **Fenyegetések elleni védelem:** A Security Center felméri a számítási feladatokat, és fenyegetésmegelőzési javaslatokat és biztonsági riasztásokat hoz.

-   **Gyorsabb biztonság:** A Security Centerben minden felhősebességen történik. Mivel natív anonim módon integrálva van, a Security Center telepítése egyszerű, így automatikus annektálást és védelmet biztosít az Azure-szolgáltatásokkal.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="architecture"></a>Architektúra

Mivel a Security Center natívan része az Azure-nak, az Azure PaaS-szolgáltatásait – beleértve a Service Fabricet, az SQL-adatbázisokat és a tárfiókokat – a Security Center minden üzembe helyezés nélkül figyeli és védi.

Emellett a Security Center védi a nem Azure-kiszolgálók és a virtuális gépek a felhőben vagy a helyszínen, mind a Windows és Linux kiszolgálók, a Log Analytics-ügynök telepítésével őket. Az Azure virtuális gépek automatikusan ki vannak építve a Security Centerben.

Az ügynököktől és az Azure-ból gyűjtött események korrelálnak a biztonsági elemzési motorban, hogy személyre szabott javaslatokat (megerősítési feladatokat) nyújtsanak, amelyeket követnie kell a számítási feladatok biztonságosságának és a biztonsági riasztásoknak a biztosítása érdekében. Az ilyen riasztásokat a lehető leghamarabb vizsgálja meg, hogy megbizonyosodjon arról, hogy a számítási feladatok nem történik rosszindulatú támadások.

Ha engedélyezi a Security Center t, a Security Center beépített biztonsági szabályzat a Security Center beépített kezdeményezésként jelenik meg az Azure Policy-ben. A beépített kezdeményezés automatikusan hozzá van rendelve az összes Security Center regisztrált előfizetéshez (ingyenes vagy standard szintű). A beépített kezdeményezés csak naplózási házirendeket tartalmaz. Az Azure-szabályzat biztonsági központszabályzatairól a [Biztonsági házirendek használata című](tutorial-security-policy.md)témakörben talál további információt.

## <a name="strengthen-security-posture"></a>Biztonsági helyzet megerősítése

Az Azure Security Center lehetővé teszi a biztonsági állapot megerősítését. Ez azt jelenti, hogy segít azonosítani és végrehajtani a biztonsági gyakorlati tanácsokként ajánlott edzési feladatokat, és megvalósítani azokat a gépeken, az adatszolgáltatásokban és az alkalmazásokban. Ez magában foglalja a biztonsági szabályzatok kezelését és érvényesítését, valamint annak biztosítását, hogy az Azure virtuális gépei, a nem Azure-kiszolgálók és az Azure PaaS-szolgáltatások megfelelőek legyenek. A Security Center biztosítja azokat az eszközöket, amelyekre szüksége van ahhoz, hogy madártávlatból megtekinthesse a munkaterheléseket, és a hálózati biztonsági állapot ot is láthatóvá tegye. 

### <a name="manage-organization-security-policy-and-compliance"></a>A szervezet biztonsági házirendjének és megfelelőségének kezelése

Ez egy alapvető biztonsági tudnivaló, és győződjön meg arról, hogy a számítási feladatok biztonságosak, és kezdődik, amelyek személyre szabott biztonsági szabályzatok a helyén. Mivel a Security Center összes szabályzata az Azure-szabályzatok vezérlőin alapul, a **világszínvonalú házirend-megoldások**teljes skáláját és rugalmasságát kapja. A Security Centerben beállíthatja, hogy a szabályzatok felügyeleti csoportokon, előfizetések között, sőt egy teljes bérlőre is fussanak.

![A Security Center irányítópultja](media/security-center-intro/sc-dashboard.png)

A Security Center segítségével **azonosíthatja a Shadow IT-előfizetéseket.** Az irányítópulton nem **szereplő** címkével ellátott előfizetéseket azonnal megfigyelheti, ha újonnan létrehozott előfizetések vannak, és meggyőződhet arról, hogy azokat a szabályzatok lefedik, és az Azure Security Center védi őket.

![A Biztonsági központ házirendjének irányítópultja](media/security-center-intro/sc-policy-dashboard.png)

A Security Center fejlett figyelési képességei lehetővé teszik **a megfelelőség és a vállalatirányítás nyomon követését és kezelését az idő múlásával.** Az **általános megfelelőség** azt méri, hogy az előfizetések mennyire felelnek meg a számítási feladatokhoz társított szabályzatok. 

![A Security Center házirendje az idő múlásával](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Folyamatos értékelések

A Security Center folyamatosan felderíti a számítási feladatok között üzembe helyezett új erőforrásokat, és felméri, hogy a biztonsági ajánlott eljárások nak megfelelően vannak-e konfigurálva, ha nem, akkor megjelölik őket, és a gépek védelme érdekében a javításra váró javaslatok rangsorolt listáját kapja.

Az egyik leghatékonyabb eszköz, amelyet a Security Center biztosít a hálózat biztonsági állapotának folyamatos figyelésére, a **Hálózati térkép.** A térkép lehetővé teszi a számítási feladatok topológiájának megtekintését, így láthatja, hogy minden csomópont megfelelően van-e konfigurálva. Láthatja, hogyan kapcsolódnak a csomópontok, ami segít blokkolni a nem kívánt kapcsolatokat, amelyek megkönnyíthetik a támadók számára a hálózat mentén való kúszást.

![A Security Center hálózati térképe](media/security-center-intro/sc-net-map.png)

A Security Center egy lépéssel könnyebbé teszi a biztonsági riasztások mérséklését a **biztonságos pontszám hozzáadásával.** A biztonságos pontszámok most antól minden egyes kapott javaslathoz társítva vannak, hogy segítsen megérteni, milyen fontos az egyes javaslatok az általános biztonsági testtartáshoz. Ez döntő fontosságú ahhoz, hogy **a biztonsági munka fontosnak tartsa.**

![Biztonsági központ biztonságos pontszáma](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>A biztonság optimalizálása és javítása az ajánlott vezérlők konfigurálásával

Az Azure Security Center értékének szíve a javaslatokban rejlik. A javaslatok a számítási feladatokon található biztonsági szempontokhoz igazodnak, és a Security Center nem csak a biztonsági rések megtalálásával végzi el a biztonsági rendszergazdamunkáját, hanem konkrét utasításokat is biztosít a rról, hogyan szabadulhat meg tőlük.

![A Biztonsági központ javaslatai](media/security-center-intro/sc-recommendations.png)

Ily módon a Security Center nem csak biztonsági házirendek beállítását teszi lehetővé, hanem biztonságos konfigurációs szabványok alkalmazását az erőforrásokközött.

A javaslatok segítségével csökkentheti a támadási felületet az egyes erőforrások között. Ez magában foglalja az Azure virtuális gépek, nem Azure-kiszolgálók és az Azure PaaS-szolgáltatások, például az SQL és storage-fiókok és több – ahol az egyes típusú erőforrások értékelése eltérő, és saját szabványokkal rendelkezik.

![Példa a Biztonsági központ ajánlására](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Védelem a fenyegetésekkel szemben

A Security Center fenyegetésvédelmi szolgáltatása lehetővé teszi a fenyegetések észlelését és megelőzését az Infrastructure as a Service (IaaS) rétegben, a nem Azure-kiszolgálókon, valamint a Platformok szolgáltatásként (PaaS) az Azure-ban.

A Security Center fenyegetésvédelmi rendszere magában foglalja a fúziós kill-chain elemzést, amely automatikusan korrelálja a riasztásokat a környezetben a számítógépes ölési lánc elemzése alapján, hogy jobban megértse a támadási kampány teljes történetét, hogy hol kezdődött, és milyen hatással volt az erőforrásokra.



![A Security Center támadási javaslata](media/security-center-intro/sc-attack-recommendation.png)

### <a name="integration-with-microsoft-defender-advanced-threat-protection"></a>Integráció a Microsoft Defender komplex veszélyforrások elleni védelemmel

A Security Center automatikus, natív integrációt biztosít a Microsoft Defender komplex veszélyforrások elleni védelemmel. Ez azt jelenti, hogy konfiguráció nélkül a Windows és Linux gépek teljes mértékben integrálva vannak a Security Center ajánlásaival és felméréseivel.

Emellett a Security Center lehetővé teszi az alkalmazásvezérlési házirendek automatizálását kiszolgálói környezetekben. A Security Center adaptív alkalmazásvezérlői teljes körű alkalmazásengedélyezési listát tesznek lehetővé a Windows-kiszolgálókon. Nem kell létrehoznia a szabályokat, és ellenőriznie kell a szabálysértéseket, minden automatikusan megtörténik.

### <a name="protect-paas"></a>PaaS védelme

A Security Center segítségével észlelheti a fenyegetéseket az Azure PaaS-szolgáltatásokban. Észlelheti az Azure-szolgáltatásokat célzó fenyegetéseket, beleértve az Azure App Serviceszolgáltatást, az Azure SQL-t, az Azure Storage-fiókot és egy több adatszolgáltatást. A Microsoft Cloud App Security felhasználói és entitásviselkedési elemzésével (UEBA) való natív integráció előnyeit is kihasználhatja az Azure-tevékenységnaplók anomáliafelismerés végrehajtásához.

### <a name="block-brute-force-attacks"></a>Blokk brute force támadások

A Security Center segít korlátozni a találgatásos támadásoknak való kitettséget. A virtuálisgép-portokhoz való hozzáférés csökkentésével a just-in-time virtuális gép-hozzáférés használatával megkeményítheti a hálózatot a szükségtelen hozzáférés megakadályozásával. A kijelölt portokon biztonságos hozzáférési házirendeket állíthat be, csak az arra jogosult felhasználók, az engedélyezett forrás IP-címtartományok vagy IP-címek számára, valamint korlátozott ideig.

![Security Center nyers erő](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Adatszolgáltatások védelme

A Security Center olyan képességeket tartalmaz, amelyek segítségével az azure-SQL-ben automatikusan rendszerezheti az adatokat. Emellett értékeléseket kaphat az Azure SQL és Storage-szolgáltatások potenciális biztonsági réseiről, valamint javaslatokat kaphat azok csökkentésére.

### <a name="protect-iot-and-hybrid-cloud-workloads"></a>IoT- és hibrid felhőbeli számítási feladatok védelme

Az Azure Security Center for IoT (Things internetes hálózata) leegyszerűsíti a hibrid számítási feladatok védelmét azáltal, hogy egységes láthatóságot és vezérlést, adaptív veszélyforrások megelőzését, valamint intelligens veszélyforrások elleni védelmet és válaszokat biztosít a peremhálózaton, a helyszíni, az Azure-ban és más felhőkben futó számítási feladatok között. További információ: [Azure Security Center for IoT.](https://docs.microsoft.com/azure/asc-for-iot/)

## <a name="get-secure-faster"></a>Gyorsabb biztonság

A natív Azure-integráció (beleértve az Azure Policy és az Azure Monitor naplóit) kombinálva a Microsoft más biztonsági megoldásaival, például a Microsoft Cloud App Security és a Windows Defender komplex veszélyforrások elleni védelemmel való zökkenőmentes integrációval biztosíthatja, hogy a biztonsági megoldás átfogó, valamint egyszerűen bedolgozható és bevezetésre kerüljön.

Emellett kiterjesztheti a teljes megoldást az Azure-on kívül más felhőkön és helyszíni adatközpontokban futó számítási feladatokra is.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Az Azure-erőforrások automatikus felderítése és fedélzeti bedeszkázása

A Security Center zökkenőmentes, natív integrációt biztosít az Azure és az Azure erőforrásaival. Ez azt jelenti, hogy az Azure Policy és a beépített Security Center-szabályzatok teljes biztonsági történetét összehívhatja az Összes Azure-erőforrásban, és meggyőződhet arról, hogy az egész automatikusan alkalmazza az újonnan felderített erőforrásokat az Azure-ban való létrehozásasorán.

Kiterjedt naplógyűjtés – a Windows és linuxos naplók mind a biztonsági elemzőmotorban használatosak, és javaslatok és riasztások létrehozására szolgálnak.

## <a name="next-steps"></a>További lépések

- A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- A Security Center ingyenes tarifacsomagja engedélyezve van az Azure-előfizetéssel. A fejlett biztonsági felügyeleti és fenyegetésvédelmi képességek előnyeinek kihasználásához frissítenie kell a szabványos tarifacsomagra. A standard szint 30 napig ingyenesen kipróbálható. További információt a [Security Center díjszabási lapján](https://azure.microsoft.com/pricing/details/security-center/)talál.
- Ha készen áll a Security Center-szabvány engedélyezésére, a [rövid útmutató: Az Azure-előfizetés bedolgozása](security-center-get-started.md) a Security Center Standard szolgáltatásba végigvezeti a lépéseken.