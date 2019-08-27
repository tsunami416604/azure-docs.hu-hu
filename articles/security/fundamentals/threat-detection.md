---
title: Azure komplex veszélyforrások észlelése | Microsoft Docs
description: Ismerkedjen meg a Azure AD Identity Protection és képességeivel.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 86b17efdb811e9e4040b8b267c161b950f6de5fd
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036180"
---
# <a name="azure-advanced-threat-detection"></a>Azure komplex veszélyforrások észlelése

Az Azure komplex veszélyforrások észlelésére szolgáló funkciókat kínál olyan szolgáltatásokon keresztül, mint az Azure Active Directory (Azure AD), a Azure Monitor a naplók és a Azure Security Center. A biztonsági szolgáltatások és képességek ezen gyűjteménye egyszerű és gyors megoldást kínál arra, hogy megértse, mi történik az Azure-beli üzembe helyezéseken belül.

Az Azure számos lehetőséget kínál a biztonsági beállítások konfigurálására és testreszabására az alkalmazások központi telepítésének követelményeinek kielégítése érdekében. Ebből a cikkből megtudhatja, hogyan teljesítheti ezeket a követelményeket.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

A [Azure ad Identity Protection](../../active-directory/identity-protection/overview.md) egy [prémium szintű Azure Active Directory P2](../../active-directory/active-directory-whatis.md) kiadási funkció, amely áttekintést nyújt a szervezet identitásait érintő kockázati eseményekről és lehetséges sebezhetőségekről. Az Identity Protection meglévő Azure AD-anomália-észlelési képességeket használ, amelyek az [Azure ad rendellenes tevékenységi jelentéseiben](../../active-directory/active-directory-reporting-azure-portal.md)érhetők el, és új kockázati események típusait észlelik, amelyek valós idejű rendellenességek észlelésére képesek.

![Azure AD Identity Protection diagram](./media/threat-detection/azure-threat-detection-fig1.png)

Az Identity Protection adaptív gépi tanulási algoritmusokat és heurisztikus használatot alkalmaz a rendellenességek és a kockázati események észlelésére, amelyek jelezhetik, hogy az identitás biztonsága sérült. Ezeknek az adatoknak a használatával az Identity Protection jelentéseket és riasztásokat hoz létre, így megvizsgálhatja ezeket a kockázati eseményeket, és elvégezheti a megfelelő szervizelési vagy kockázatcsökkentő műveleteket.

Azure Active Directory Identity Protection több, mint egy figyelési és jelentéskészítési eszköz. A kockázati események alapján az Identity Protection kiszámítja az egyes felhasználókra vonatkozó felhasználói kockázati szintet, így a kockázatkezelési házirendek konfigurálásával automatikusan védetté teheti a szervezet identitásait.

Ezek a kockázatalapú házirendek a Azure Active Directory és az [EMS](../../active-directory/active-directory-conditional-access-azure-portal.md)által biztosított egyéb [feltételes hozzáférés](../../active-directory/active-directory-conditional-access-azure-portal.md) -vezérlések mellett automatikusan letilthatják vagy felhasználhatják az olyan adaptív szervizelési műveleteket, amelyek jelszavas alaphelyzetbe állítást és több tényezőt tartalmaznak hitelesítés kényszerítése.

### <a name="identity-protection-capabilities"></a>Identity Protection-képességek

Azure Active Directory Identity Protection több, mint egy figyelési és jelentéskészítési eszköz. A szervezet identitásának megóvása érdekében olyan kockázatalapú házirendeket állíthat be, amelyek automatikusan válaszolnak az észlelt problémákra, amikor a rendszer elérte az adott kockázati szintet. Ezek a házirendek a Azure Active Directory és az EMS által biztosított egyéb feltételes hozzáférés-vezérlések mellett automatikusan letilthatják vagy indíthatják el az adaptív szervizelési műveleteket, beleértve a jelszó-visszaállítást és a többtényezős hitelesítés kényszerítését.

Néhány példa az Azure Identity Protection által a fiókok és identitások biztonságossá tételéhez:

[Kockázati események és kockázatos fiókok észlelése](../../active-directory/identity-protection/overview.md)
-   Hat kockázati esemény típusának észlelése gépi tanulási és heurisztikus szabályok használatával.
-   Felhasználói kockázati szintek kiszámítása.
-   A biztonsági rések kiemelésével egyéni ajánlásokat adhat meg az általános biztonsági testhelyzet javításához.

[Kockázati események kivizsgálása](../../active-directory/identity-protection/overview.md)
-   Értesítés küldése a kockázati eseményekről.
-   A kockázati események vizsgálata a releváns és a környezetfüggő információk használatával.
-   Alapvető munkafolyamatok biztosítása a vizsgálatok nyomon követéséhez.
-   Könnyű hozzáférést biztosít a javítási műveletekhez, például a jelszó-visszaállításhoz.

[Kockázatalapú, feltételes hozzáférési szabályzatok](../../active-directory/identity-protection/overview.md)
-   A kockázatos bejelentkezések enyhítése a bejelentkezések blokkolásával vagy a többtényezős hitelesítési kihívások megkövetelésével.
-   Kockázatos felhasználói fiókok blokkolása vagy biztonságossá tétele.
-   A többtényezős hitelesítés regisztrálásának megkövetelése a felhasználóknak.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

A [Azure Active Directory Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md)segítségével felügyelheti, vezérelheti és figyelheti a szervezeten belüli hozzáférését. Ez a funkció magában foglalja az Azure AD-ban és más Microsoft-online szolgáltatásokban található erőforrásokhoz való hozzáférést, például az Office 365-et vagy Microsoft Intune.

![Azure AD Privileged Identity Management diagram](./media/threat-detection/azure-threat-detection-fig2.png)

A PIM a következőket teszi lehetővé:

-   Riasztások és jelentések beszerzése az Azure AD-rendszergazdákkal és az igény szerinti (JIT) rendszergazdai hozzáférés a Microsoft online szolgáltatások, például az Office 365 és az Intune szolgáltatáshoz.

-   A rendszergazdai hozzáférési előzményekkel és a rendszergazdai hozzárendelések változásaival kapcsolatos jelentések beolvasása.

-   Riasztást kaphat a Kiemelt szerepkörhöz való hozzáférésről.

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók

A [Azure monitor naplók](../../azure-monitor/index.yml) egy olyan Microsoft felhőalapú informatikai felügyeleti megoldás, amely segít a helyszíni és a Felhőbeli infrastruktúra felügyeletében és kezelésében. Mivel Azure Monitor naplókat felhőalapú szolgáltatásként implementálják, az infrastruktúra-szolgáltatások minimális beruházásainak köszönhetően gyorsan üzembe helyezhető. Az új biztonsági funkciók automatikusan, a folyamatos karbantartási és frissítési költségek megtakarításával lesznek elérhetők.

Amellett, hogy az értékes szolgáltatásokat saját maga is biztosítja, Azure Monitor naplók integrálható a System Center-összetevőkkel [](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), például System Center Operations managerekkel, hogy kiterjessze a meglévő biztonsági felügyeleti beruházásokat a felhőbe. A System Center és a Azure Monitor naplók együtt használhatók teljes hibrid felügyeleti élmény biztosításához.

### <a name="holistic-security-and-compliance-posture"></a>Holisztikus biztonsági és megfelelőségi testhelyzet

A [Log Analytics Security and Audit irányítópult](../../security-center/security-center-intro.md) átfogó áttekintést nyújt a szervezet informatikai biztonsági állapotáról, és beépített keresési lekérdezéseket tartalmaz a jelentős problémákra, amelyek szükségesek a beavatkozáshoz. A Security and Audit irányítópult a Azure Monitor naplókban a biztonsággal kapcsolatos összes adathoz tartozó kezdőképernyő. Magas szintű betekintést tesz lehetővé a számítógépek biztonsági állapotába. Az elmúlt 24 óra, 7 nap vagy bármely más egyéni időkeret alapján is megtekintheti az összes eseményt.

Azure Monitor naplók segítségével gyorsan és könnyen megismerheti bármely környezet általános biztonsági állapotát, mindezt az IT-műveletek kontextusában, beleértve a szoftverfrissítés értékelését, a kártevők értékelését és az alapkonfigurációkat. A biztonsági naplókra vonatkozó adatszolgáltatások könnyen elérhetők a biztonsági és megfelelőségi naplózási folyamatok egyszerűsítése érdekében.

![A Log Analytics Security and Audit irányítópult](./media/threat-detection/azure-threat-detection-fig3.jpg)

A Log Analytics Security and Audit irányítópult négy fő kategóriába van rendezve:

-   **Biztonsági tartományok**: Lehetővé teszi a biztonsági rekordok további megismerését az idő múlásával; hozzáférés a kártevők értékeléséhez; frissítési felmérések; hálózati biztonsági, identitási és hozzáférési információk megtekintése; biztonsági eseményekkel rendelkező számítógépek megtekintése; és gyorsan hozzáférhet a Azure Security Center irányítópulthoz.

-   **Jelentős problémák**: Lehetővé teszi az aktív problémák számának és a problémák súlyosságának gyors azonosítását.

-   **Észlelések (előzetes verzió)** : Lehetővé teszi a támadási minták azonosítását a biztonsági riasztások megjelenítésével, amikor azok az erőforrásokon történnek.

-   **Fenyegetés intelligencia**: Lehetővé teszi a támadási minták azonosítását, ha megjeleníti a kimenő kártékony IP-forgalmat, a kártékony veszélyforrás típusát és az IP-címek helyeinek térképét.

-   **Gyakori biztonsági lekérdezések**: Felsorolja a környezet figyeléséhez használható leggyakoribb biztonsági lekérdezéseket. Ha bármelyik lekérdezést kiválasztja, megnyílik a keresés ablaktábla, és megjeleníti a lekérdezés eredményét.

### <a name="insight-and-analytics"></a>Betekintés és elemzés
[Azure monitor naplók](../../log-analytics/log-analytics-queries.md) középpontjában az Azure által üzemeltetett adattár található.

![Insight and Analytics diagram](./media/threat-detection/azure-threat-detection-fig4.png)

Adatokat gyűjt a tárházhoz a csatlakoztatott forrásokból az adatforrások konfigurálásával és a megoldások az előfizetéshez való hozzáadásával.

![A Azure Monitor naplók irányítópultja](./media/threat-detection/azure-threat-detection-fig5.png)

Az egyes adatforrások és megoldások külön bejegyzéstípusokat hoznak létre a saját tulajdonságokkal, de továbbra is elemezheti őket a tárházban lévő lekérdezésekben. Ugyanazokat az eszközöket és metódusokat használhatja a különböző forrásokból összegyűjtött különféle adatokkal való munkához.


A Azure Monitor naplókkal való interakciójának nagy része az a Azure Portal, amely bármely böngészőben fut, és hozzáférést biztosít a konfigurációs beállításokhoz és több eszközhöz, amelyekkel elemezheti és kezelheti az összegyűjtött adatokat. A portálról a következőket használhatja:
* [](../../log-analytics/log-analytics-queries.md) A naplóban megkeresi a gyűjtött adatok elemzéséhez szükséges lekérdezéseket.
* [Irányítópultok](../../azure-monitor/learn/tutorial-logs-dashboards.md), amelyek a legértékesebb keresések grafikus nézeteivel testreszabhatók.
* [Megoldások](../../monitoring/monitoring-solutions.md), amelyek további funkciókat és elemzési eszközöket biztosítanak.

![Elemzési eszközök](./media/threat-detection/azure-threat-detection-fig6.png)

A megoldások funkciókkal bővítik Azure Monitor naplókat. Elsősorban a felhőben futnak, és a log Analytics-tárházban összegyűjtött adatok elemzését teszik lehetővé. A megoldások olyan új bejegyzéstípusokat is meghatározhatnak, amelyeket a rendszer a naplók keresésével vagy egy, a log Analytics-irányítópulton a megoldás által biztosított további felhasználói felület használatával elemezni lehet.

Az Security and Audit irányítópult az ilyen típusú megoldások egyik példája.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatizálás és vezérlés: Riasztás a biztonsági beállítások sodródása esetén

Azure Automation automatizálja a felügyeleti folyamatokat a PowerShellen alapuló és a felhőben futó runbookok. A runbookok futtathatók a helyi adatközpontban is, és kezelhetők velük a helyi erőforrások. Azure Automation a PowerShell desired State Configuration (DSC) használatával biztosítja a konfiguráció felügyeletét.

![Azure Automation diagram](./media/threat-detection/azure-threat-detection-fig7.png)

Létrehozhatja és kezelheti az Azure-ban üzemeltetett DSC-erőforrásokat, és alkalmazhatja őket a Felhőbeli és a helyszíni rendszerekre is. Ezzel meghatározhatja és automatikusan érvényesítheti a konfigurációt, vagy jelentéseket készíthet a drift szolgáltatásban, így biztosítva, hogy a biztonsági konfigurációk a házirendben maradjanak.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center segíti az Azure-erőforrások megóvását. Integrált biztonsági monitorozást és házirend-kezelést biztosít az Azure-előfizetések között. A szolgáltatáson belül az Azure-előfizetések és- [erőforráscsoportok](../../azure-resource-manager/manage-resources-portal.md) esetében is meghatározhatja a szabályzatokat.

![Azure Security Center diagram](./media/threat-detection/azure-threat-detection-fig8.png)

A Microsoft biztonsági kutatói folyamatosan figyelik a megjelenő fenyegetéseket. Rendelkezésükre áll a telemetriai adatok kiterjedt halmaza, amelyet a Microsoft a globális felhőbeli és helyszíni jelenléte során gyűjtött össze. Ezen adatkészletek széles körű és változatos gyűjteményére alapozva a Microsoft fel tudja fedezni az új támadási mintákat és trendeket a helyszíni fogyasztói és a vállalati termékeiben, valamint az online szolgáltatásaiban.

Így a Security Center gyorsan frissítheti az észlelési algoritmusokat, mivel a támadók új és egyre kifinomultabb biztonsági réseket szabadítanak fel. Ez a megközelítés segít lépést tartani egy gyorsan változó fenyegetési környezettel.

![Security Center fenyegetések észlelése](./media/threat-detection/azure-threat-detection-fig9.jpg)

A Security Center fenyegetésészlelése úgy működik, hogy automatikusan összegyűjti a biztonsági információkat az Azure-erőforrásokból, a hálózatból és a csatlakoztatott partneri megoldásokból. Elemzi ezeket az információkat, és korrelálja a különböző forrásokból származó információkat a fenyegetések azonosítása érdekében.

A Security Center prioritás szerinti sorolja be a biztonsági riasztásokat, és javaslatokat tesz a fenyegetés kiiktatására.

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. A big data és a [gépi tanulási](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technológiák áttöréseit a teljes felhőalapú hálóban lévő események kiértékelésére használják. A speciális elemzések képesek észlelni azokat a fenyegetéseket, amelyek a manuális megközelítések és a támadások alakulásának előrejelzése révén lehetetlenek. Ezek a biztonsági elemzési típusok a következő fejezetekben találhatók.

### <a name="threat-intelligence"></a>Fenyegetések felderítése

A Microsoft a globális fenyegetést jelentő intelligenciát jelentős mértékben elérheti.

A telemetria több forrásból, például az Azure-ból, az Office 365-ból, a Microsoft CRM Online-ból, a Microsoft Dynamics AX-ből, a outlook.com, a MSN.com, a Microsoft Digital Crimes Unit (DCU) és a Microsoft Security Response Center (MSRC) szolgáltatásból zajlik.

![Fenyegetések felderítésének eredményei](./media/threat-detection/azure-threat-detection-fig10.jpg)

A kutatók emellett a nagy felhőalapú szolgáltatók között megosztott fenyegetést jelentő intelligenciával kapcsolatos információkat is kapnak, és előfizethetnek harmadik féltől származó veszélyforrások elleni intelligenciára. Az Azure Security Center mindezeket az adatokat fel tudja használni arra, hogy értesítse Önt az ismert kártékony elemektől eredő fenyegetésekről. Néhány példa:

-   **A gépi tanulás hatékonyságának kiaknázása**: Azure Security Center a Felhőbeli hálózati tevékenységekkel kapcsolatos nagy mennyiségű adattal fér hozzá, ami felhasználható az Azure-beli üzembe helyezéseket célzó fenyegetések észlelésére.

-   **Találgatásos**támadás észlelése: A gépi tanulás a távelérési kísérletek korábbi mintájának létrehozására szolgál, amely lehetővé teszi az IT számára, hogy felderítse a Secure Shell (SSH), a RDP protokoll (RDP) és az SQL-portok elleni találgatásos támadásokat.

-   **Kimenő DDoS-és botnet-észlelés**: A felhőalapú erőforrásokra irányuló támadások közös célja, hogy az erőforrások számítási erejét használják más támadások végrehajtásához.

-   **Új viselkedési elemzési kiszolgálók és virtuális gépek**: Egy kiszolgáló vagy virtuális gép biztonsága miatt a támadók számos technikát alkalmaznak a kártékony kódok végrehajtására az adott rendszeren, miközben elkerüli az észlelést, biztosítja az adatmegőrzést és szükségtelenné téve a biztonsági vezérlőket.

-   **Azure SQL Database fenyegetések észlelése**: Azure SQL Database fenyegetések észlelése, amely azonosítja a rendellenes adatbázis-tevékenységeket, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

### <a name="behavioral-analytics"></a>Működés elemzése

A működés elemzése olyan módszer, amely megvizsgálja és összehasonlítja az adatokat az ismert minták gyűjteményével. Ezek a minták azonban nem csak egyszerű aláírások. Meghatározásuk hatalmas adatkészletekre alkalmazott összetett gépi tanulási algoritmusokkal történt.

![Viselkedési elemzési eredmények](./media/threat-detection/azure-threat-detection-fig11.jpg)

A mintákat a szakértői elemzők által a kártékony viselkedések alapos elemzésével is meghatározták. A Azure Security Center a viselkedési elemzések segítségével azonosíthatja a feltört erőforrásokat a virtuális gépek naplói, a virtuális hálózati eszközök naplói, a háló naplói, az összeomlási memóriaképek és más források elemzése alapján.

Emellett a mintázatok más jelekkel vannak összekapcsolva, hogy megkeressék a széleskörű kampány alátámasztó bizonyítékait. Az összefüggések felderítése segít azonosítani a feltörés meghatározott tüneteit mutató eseményeket.

Néhány példa:
-   **Gyanús folyamat végrehajtása**: A támadók számos technikát alkalmaznak a kártevő szoftverek észlelés nélküli végrehajtásához. Előfordulhat például, hogy egy támadó a legitim rendszerfájlokkal megegyező neveket ad a kártevők számára, de ezeket a fájlokat egy másik helyen helyezi el, olyan nevet használ, amely hasonló egy jóindulatú fájlhoz, vagy a fájl valódi kiterjesztését. Security Center modellek folyamatok viselkedését és a folyamatok végrehajtásának figyelését a kiugró értékek, például a következő észlelése érdekében.

-   **Rejtett kártevők és kiaknázási kísérletek**: A kifinomult kártevők kihasználhatják a hagyományos antimalware-termékeket, ha soha nem ír lemezre vagy titkosítják a lemezen tárolt szoftver-összetevőket. Az ilyen kártevők azonban a memória-elemzés használatával észlelhetők, mivel a kártevőnek a memóriában lévő nyomkövetést kell hagyni a működéshez. Amikor a szoftver összeomlik, egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában. Az összeomlási memóriakép memóriájának elemzésével a Azure Security Center képes felderíteni a szoftverek sebezhetőségének kihasználásához használt technikákat, a bizalmas adatok elérését és a titokban tartást a sérült gépen anélkül, hogy ez hatással lenne a következő teljesítményére: gép.

-   **Oldalirányú mozgás és belső felderítés**: Ha egy sérült hálózaton kíván megőrizni, és értékes adatok megkeresésére és begyűjtésére van szükség, a támadók gyakran a feltört gépről később próbálnak áthelyezni egy másik hálózaton belül. Security Center figyeli a folyamat-és bejelentkezési tevékenységeket, hogy felderítse a támadók lábát a hálózaton belül, például távoli parancs végrehajtásával, hálózati szondázás és fiókok enumerálásával.

-   **Rosszindulatú PowerShell-parancsfájlok**: A PowerShell felhasználható a támadók számára, hogy különböző célokra rosszindulatú kódot futtassanak a cél virtuális gépeken. A Security Center megvizsgálja a PowerShell tevékenységeit, hogy megtalálja a gyanús tevékenységek nyomait.

-   **Kimenő támadások**: A támadók gyakran Felhőbeli erőforrásokat céloznak azzal a céllal, hogy ezeket az erőforrásokat további támadások csatlakoztatására használják fel. A feltört virtuális gépek például felhasználhatók más virtuális gépek elleni találgatásos támadásokra, levélszemét küldésére, vagy nyitott portok és egyéb eszközök vizsgálatára az interneten. A hálózati forgalomra irányított gépi tanulás alkalmazásával a Security Center észlelni tudja, ha a szokásosnál nagyobb mértékű a kimenő hálózati kommunikáció. Ha a rendszer levélszemét észlelését észleli, Security Center a szokatlan e-mail-forgalmat is összekapcsolja az Office 365 intelligenciával annak megállapítása érdekében, hogy a levél valószínűleg aljas vagy egy legitim e-mail-kampány eredménye-e.

### <a name="anomaly-detection"></a>Anomáliadetektálás

Az Azure Security Center további módszere a fenyegetések felderítésére a rendellenességek észlelése. A működés elemzésével (amely a nagy adatkészletekből kinyert ismert mintákon alapul) ellentétben a rendellenességek észlelése „testre szabottabb”, és az üzemelő példányokhoz tartozó alapkonfigurációkra összpontosít. A gépi tanulás az üzemelő példányok normál tevékenységének meghatározására lett alkalmazva, majd szabályok jönnek létre a biztonsági eseményt képviselő kiugró állapotok meghatározásához. Például:

-   **Bejövő RDP/SSH találgatásos támadások**: Előfordulhat, hogy az üzemelő példányok olyan forgalmas virtuális gépeket foglalnak magukban, amelyek naponta több bejelentkezéssel rendelkeznek, és más virtuális gépeket is tartalmaz, amelyek közül néhány, ha van ilyen A Azure Security Center meghatározhatja a virtuális gépek alapkonfigurációjának bejelentkezési tevékenységeit, és a gépi tanulás használatával határozhatja meg a szokásos bejelentkezési tevékenységeket. Ha a bejelentkezéshez kapcsolódó jellemzőkkel kapcsolatban meghatározott alaptervnek nincs eltérése, akkor a rendszer riasztást generálhat. Ebben az esetben is gépi tanulás alapján határozza meg, hogy mi számít szignifikáns eltérésnek.

### <a name="continuous-threat-intelligence-monitoring"></a>A fenyegetésekre vonatkozó intelligencia folyamatos figyelése

A Azure Security Center az egész világon a biztonsági kutatási és adatelemzési csapatokkal működik, amely folyamatosan figyeli a fenyegetések tájképének változásait. Ide tartoznak a következők:

-   **Fenyegetési intelligencia figyelése**: A fenyegetések intelligencia a meglévő vagy újonnan felmerülő fenyegetésekkel kapcsolatos mechanizmusokat, indikátorokat, következményeket és gyakorlati tanácsokat tartalmaz. Ezeket az információkat a biztonsági Közösség osztja meg, a Microsoft folyamatosan figyeli a belső és külső forrásokból származó veszélyforrások felderítését.

-   **Jel megosztása**: A biztonsági csapatokkal kapcsolatos elemzések a Felhőbeli és a helyszíni szolgáltatások, a kiszolgálók és az ügyfél-végponti eszközök átfogó Microsoft-portfóliójában vannak megosztva és elemezve.

-   **Microsoft biztonsági szakemberek**: Folyamatos együttműködés a Microsoft különböző csoportjaival, amelyek speciális biztonsági mezőkben működnek, mint például a kriminalisztika és a webes támadások észlelése.

-   **Észlelés finomhangolása**: Az algoritmusok valós ügyfél-adatkészleteken futnak, és a biztonsági kutatók az ügyfelekkel együttműködve érvényesítik az eredményeket. Az igazi és a téves találatok megjelölésével pontosítják a gépi algoritmusokat.

Ezek az összetett erőfeszítések új és továbbfejlesztett észlelésekkel zárulnak, amelyeket azonnal kihasználhat. Nem kell végrehajtania a műveletet.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Összetett veszélyforrások észlelési funkciói: Egyéb Azure-szolgáltatások

### <a name="virtual-machines-microsoft-antimalware"></a>Virtuális gépek: Microsoft antimalware

Az Azure-hoz készült [Microsoft antimalware](antimalware.md) egyetlen ügynökből álló megoldás az alkalmazások és a bérlői környezetek számára, amelyeket emberi beavatkozás nélkül lehet a háttérben futtatni. A védelmet az alkalmazás számítási feladatainak igényei szerint helyezheti üzembe, amely alapszintű biztonsági vagy speciális konfigurációval rendelkezik, beleértve a kártevő szoftverek figyelését is. Az Azure antimalware egy biztonsági lehetőség az Azure-beli virtuális gépekhez, amelyeket az Azure-beli virtuális gépeken automatikusan telepítenek.

#### <a name="microsoft-antimalware-core-features"></a>Microsoft antimalware Core-funkciók

Az Azure azon funkcióit tartalmazza, amelyek a Microsoft antimalware alkalmazást helyezik üzembe és engedélyezik alkalmazásai számára:

-   **Valós idejű védelem**: A Cloud Services és a Virtual Machines tevékenységeit figyeli a kártevők végrehajtásának észleléséhez és blokkolásához.

-   **Ütemezett vizsgálat**: A rendszeres időközönként a kártevők észlelése, beleértve az aktívan futó programokat is.

-   **Kártevő-szervizelés**: A automatikusan észleli az észlelt kártevő szoftvereket, például a rosszindulatú fájlok törlését vagy karanténba helyezését, valamint a kártékony beállításjegyzék-bejegyzések tisztítását.

-   **Aláírás-frissítések**: A automatikusan telepíti a legújabb védelmi aláírásokat (vírus-definíciókat), így biztosítva, hogy a védelem előre meghatározott gyakorisággal legyen naprakész.

-   **Antimalware-motor frissítései**: Automatikusan frissíti a Microsoft antimalware motort.

-   **Kártevők elleni platform frissítései**: A automatikusan frissíti a Microsoft antimalware platformot.

-   **Aktív védelem**: A jelentések az észlelt fenyegetésekkel és a gyanús erőforrásokkal kapcsolatos metaadatokat telemetria Microsoft Azure a kialakulóban lévő veszélyforrások tájképének gyors reagálásának biztosítása érdekében, ami lehetővé teszi a valós idejű szinkron aláírások továbbítását a Microsoft Active Protection rendszeren keresztül.

-   **Példák**a jelentésekre: Példákat biztosít a Microsoft antimalware szolgáltatásnak a szolgáltatás pontosítására és a hibaelhárítás engedélyezésére.

-   **Kizárások**: Lehetővé teszi, hogy az alkalmazás-és szolgáltatás-rendszergazdák bizonyos fájlokat, folyamatokat és meghajtókat konfiguráljanak a védelemből való kizárás és a teljesítmény és egyéb okok miatt.

-   **Antimalware-események gyűjteménye**: A kártevő szolgáltatás állapotát, a gyanús tevékenységeket, valamint az operációs rendszer eseménynaplójában végrehajtott szervizelési műveleteket rögzíti, és az ügyfél Azure Storage-fiókjába gyűjti azokat.

### <a name="azure-sql-database-threat-detection"></a>Azure SQL Database fenyegetések észlelése

A [veszélyforrások észlelése Azure SQL Database](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) a Azure SQL Database szolgáltatásba épített új biztonsági intelligencia szolgáltatás. A rendellenes adatbázis-tevékenységek megismeréséhez, profiljához és észleléséhez, Azure SQL Database veszélyforrások észlelése az adatbázis lehetséges fenyegetéseit azonosítja.

A biztonsági tisztviselők vagy más kijelölt rendszergazdák azonnali értesítést kaphatnak a gyanús adatbázis-tevékenységekről, amikor azok történnek. Minden értesítés tartalmazza a gyanús tevékenység részleteit, és a fenyegetés további kivizsgálását és enyhítését javasolja.

Jelenleg Azure SQL Database veszélyforrások észlelése észleli a potenciális biztonsági réseket és az SQL-injektálási támadásokat, valamint a rendellenes adatbázis-hozzáférési mintákat.

A veszélyforrások észlelésére vonatkozó e-mail-értesítések fogadásakor a felhasználók a levélben lévő mély hivatkozáson keresztül navigálhatnak és megtekinthetik a vonatkozó naplózási rekordokat. A hivatkozás egy naplózási megjelenítőt vagy egy előre konfigurált naplózási Excel-sablont nyit meg, amely megjeleníti a gyanús esemény időpontja körüli megfelelő naplózási rekordokat a következők szerint:

-   Naplózza az adatbázis/kiszolgáló számára a rendellenes adatbázis-tevékenységeket.

-   Az esemény időpontjában a napló írásához használt releváns naplózási tábla.

-   Az óra rekordjait közvetlenül az esemény bekövetkezése után naplózza.

-   Naplózza az esemény időpontjában egy hasonló AZONOSÍTÓJÚ rekordot (egyes érzékelők esetében nem kötelező).

SQL Database fenyegetések észlelése a következő észlelési módszerek egyikét használja:

-   **Determinisztikus észlelése**: Észleli a gyanús mintákat (szabályok alapján) az SQL-ügyfél azon lekérdezései között, amelyek megfelelnek az ismert támadásoknak. Ez a módszer magas észlelést és alacsony téves pozitív, de korlátozott lefedettséget biztosít, mivel az "atomi észlelések" kategóriába tartozik.

-   **Viselkedési észlelés**: Észleli a rendellenes tevékenységeket, ami rendellenes viselkedést jelent az adatbázisban, és a legutóbbi 30 napban nem volt látható. Az SQL-ügyfél rendellenes tevékenysége például a sikertelen bejelentkezések vagy lekérdezések, a kinyert adatok nagy mennyisége, a szokatlan kanonikus lekérdezések vagy az adatbázis eléréséhez használt ismeretlen IP-címek.

### <a name="application-gateway-web-application-firewall"></a>Webalkalmazási tűzfal Application Gateway

A webalkalmazási [tűzfal (WAF)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) az [Azure Application Gateway](../../application-gateway/application-gateway-web-application-firewall-overview.md) szolgáltatása, amely védelmet nyújt az Application Gatewayt használó webalkalmazások számára a szabványos [alkalmazások kézbesítés](https://kemptechnologies.com/in/application-delivery-controllers) -vezérlési funkcióihoz. A webalkalmazási tűzfal ezt azáltal védi, hogy az [Open Web Application Security Project (OWASP) Top 10 leggyakoribb webes biztonsági rései](https://www.owasp.org/index.php/Top_10_2010-Main)közül a legtöbbat megvédi.

![Application Gateway webalkalmazási tűzfal diagramja](./media/threat-detection/azure-threat-detection-fig13.png)

A védelem többek között a következők:

-   SQL-befecskendezéses védelem.

-   Helyek közötti parancsfájlok elleni védelem.

-   Gyakori webes támadások elleni védelem, például a parancsok befecskendezése, a HTTP-kérések csempészete, a HTTP-válaszok felosztása és a távoli fájl-integrációs támadás.

-   A HTTP protokoll megsértésének védelme.

-   A HTTP protokollal kapcsolatos rendellenességek, például a gazdagépek hiányzó felhasználói ügynökének védelme és a fejlécek elfogadása.

-   A robotok, a keresőrobotok és a szkennerek elleni megelőzés.

-   Az alkalmazások gyakori konfigurációs beállításainak (azaz Apache, IIS stb.) észlelése.

Az WAF konfigurálása az Application gatewayben a következő előnyöket biztosítja:

-   A webes biztonsági rések és támadások elleni védelem a háttér kódjának módosítása nélkül is védelmet biztosít a webalkalmazásoknak.

-   Egy Application Gateway mögötti időben több webalkalmazás védelmét is védi. Az Application Gateway akár 20 webhely üzemeltetését is támogatja.

-   Az Application Gateway WAF naplófájljai által generált valós idejű jelentéseket használva figyeli a támadások elleni webes alkalmazásokat.

-   Segíti a megfelelőségi követelmények teljesítését. Bizonyos megfelelőségi vezérlők esetében az internetre irányuló összes végpontot WAF-megoldással védeni kell.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>Anomáliák észlelése API: Beépített Azure Machine Learning

A rendellenesség-észlelési API egy olyan API, amely számos rendellenes mintázat észlelésére használható az idősorozat-adataiban. Az API egy anomália-pontszámot rendel az idősorozat minden adatpontjához, amely riasztások generálására, irányítópultokon keresztüli figyelésre vagy a jegyrendszer-szolgáltatásokhoz való csatlakozásra használható.

A [rendellenesség-észlelési API](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) a következő típusú rendellenességek észlelésére képes az idősorozat-adatmennyiségek esetében:

-   **Tüskék és dips**: Ha egy adott szolgáltatáshoz vagy egy e-kereskedelmi helyen lévő pénztárak számához tartozó bejelentkezési hibák számát figyeli, a szokatlan tüskék vagy a dips a biztonsági támadásokat vagy a szolgáltatás megszakadását jelezheti.

-   **Pozitív és negatív trendek**: Amikor a használatban lévő memóriahasználat figyelését végzi, a szabad memória méretének csökkentése a lehetséges memória-szivárgást jelzi. A szolgáltatási várólista hosszának figyelése esetén az állandó felfelé irányuló tendencia egy mögöttes szoftveres problémát jelezhet.

-   **Szintek változásai és változások az értékek dinamikus tartományában**: A szolgáltatás frissítése vagy a kivételek alacsonyabb szintje a frissítés után is érdekes lehet a figyeléshez.

A Machine learning-alapú API a következőket teszi lehetővé:

-   **Rugalmas és robusztus észlelés**: Az anomáliák észlelési modelljei lehetővé teszik a felhasználók számára az érzékenységi beállítások konfigurálását és a szezonális és nem szezonális adatkészletek közötti rendellenességek észlelését. A felhasználók az anomáliák észlelési modelljét úgy módosíthatják, hogy az észlelési API-t az igényeiknek megfelelően kisebb vagy nagyobb legyen. Ez azt jelenti, hogy a kevésbé vagy több látható rendellenességek észlelése a szezonális mintázattal és anélkül.

-   **Méretezhető és kellő időben történő észlelés**: A szakértők tartományi ismeretei által meghatározott küszöbértékekkel való figyelés hagyományos módja költséges, és nem méretezhető több millió dinamikusan változó adatkészletre. Az API rendellenesség-észlelési modelljei megtanultak, és a modelleket a rendszer automatikusan behangolja mind a régi, mind a valós idejű adatokból.

-   **Proaktív és gyakorlatban alkalmazható észlelés**: A lassú és a szint változás észlelése alkalmazható a korai anomáliák észleléséhez. Az észlelt korai rendellenes jeleket felhasználhatja az emberek számára a problémás területek kivizsgálására és a problémák elhárítására. Emellett az alapvető okokat elemző modellek és riasztási eszközök ezen anomália-észlelési API szolgáltatáson felül is fejleszthetők.

Az anomália-észlelési API hatékony és hatékony megoldás a különböző forgatókönyvek, például a szolgáltatások állapotának és a KPI-k figyelésére, a IoT, a teljesítmény figyelésére és a hálózati forgalom figyelésére. Íme néhány népszerű forgatókönyv, ahol ez az API hasznos lehet:

- Az informatikai részlegeknek olyan eszközökre van szükségük, amelyekkel időben nyomon követhetik az eseményeket, a hibakódot, a használati naplót és a teljesítményt (CPU, memória stb.).

-   Az online kereskedelmi webhelyek nyomon szeretnék követni az ügyfelek tevékenységeit, a lapok nézeteit, a kattintásokat stb.

-   A közmű-vállalatok a víz, a gáz, a villamos energia és az egyéb erőforrások felhasználását szeretnék követni.

-   A hőmérséklet, a nedvesség, a forgalom és így továbbra is figyelni kell a létesítményeket vagy a felügyeleti szolgáltatásokat.

-   A IoT/gyártók az idősorozatokban szeretnék használni az érzékelőket a munkafolyamatok, a minőség és egyéb folyamatok figyelésére.

-   A szolgáltatóknak (például a Call centereknek) figyelniük kell a szolgáltatási igények trendjeit, az incidensek mennyiségét, a várakozási sor hosszát és így tovább.

-   Az üzleti elemzési csoportok valós időben figyelik az üzleti KPI-ket (például az értékesítések mennyiségét, az ügyfelek hangulatát vagy árképzését).

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) a Microsoft Cloud biztonsági verem kritikus összetevője. Ez egy átfogó megoldás, amely segítséget nyújt a szervezetnek a Felhőbeli alkalmazások ígérete teljes körű kihasználása érdekében. Kézben tarthatja a szabályozást a tevékenységek jobb láthatóságával. Emellett a felhőalkalmazások kritikus adatainak védelmét is növeli.

A Cloud App Security eszközeivel könnyebb az informatikai árnyék-infrastruktúra feltárása, a kockázatfelmérés, a szabályzatok kényszerítése, a tevékenységek vizsgálata, valamint a veszélyek megakadályozása. A szervezet így biztonságosan helyezheti át az adatait a felhőbe a kritikus adatok felügyelete mellett.

| | |
|---|---|
| Ismertetők | Feltárhatja az árnyékot Cloud App Securityával. Az alkalmazások, a tevékenységek, a felhasználók, az adatfájlok és a Felhőbeli környezetek felfedésével láthatóvá válik. A felhőhöz csatlakoztatott külső féltől származó alkalmazások felderítése.|
|Vizsgálat | A felhőalapú kriminalisztikai eszközökkel kivizsgálhatja a Felhőbeli alkalmazásait a kockázatos alkalmazások, a konkrét felhasználók és a hálózatban lévő fájlok mélyebb betekintéséhez. Mintákat kereshet a felhőből gyűjtött adatok között. Jelentések készítése a felhő figyelésére. |
| Szabályozás | A kockázatok enyhítéséhez házirendeket és riasztásokat állíthat be a hálózati felhő forgalmának maximális szabályozása érdekében. A Cloud App Security segítségével áttelepítheti a felhasználókat biztonságos, engedélyezett Felhőbeli alkalmazási alternatívákba. |
| védelme | A Cloud App Security használatával szankcionálhatja vagy tilthatja le az alkalmazásokat, kényszerítheti az adatvesztés megelőzését, szabályozhatja az engedélyeket és a megosztást, valamint egyéni jelentéseket és riasztásokat hozhat elő. |
| Szabályozás | A kockázatok enyhítéséhez házirendeket és riasztásokat állíthat be a hálózati felhő forgalmának maximális szabályozása érdekében. A Cloud App Security segítségével áttelepítheti a felhasználókat biztonságos, engedélyezett Felhőbeli alkalmazási alternatívákba. |
| | |


![Cloud App Security diagram](./media/threat-detection/azure-threat-detection-fig14.png)

A Cloud App Security a következőket biztosítja a felhővel való láthatósághoz:

-   A Cloud Discovery használatával leképezheti és azonosíthatja a felhőalapú környezetét és a szervezete által használt felhőalapú alkalmazásokat.

-   A felhőben lévő alkalmazások engedélyezése és letiltása.

-   Egyszerűen telepíthető alkalmazás-összekötők használatával, amelyek kihasználják a szolgáltatói API-k előnyeit, és megtekintheti és irányíthatja az alkalmazásokat, amelyekhez csatlakozik.

-   Ezzel a beállítással folyamatos szabályozást, majd folyamatosan finomhangolást, szabályzatokat is használhat.

Az ezekből a forrásokból származó adatok összegyűjtésekor Cloud App Security kifinomult elemzést futtat rajta. Azonnal riasztást küld a rendellenes tevékenységekről, és részletesen betekintést nyújt a felhőalapú környezetbe. A szabályzatot Cloud App Securityban konfigurálhatja, és felhasználhatja a Felhőbeli környezet minden elemének megvédéséhez.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Harmadik féltől származó komplex veszélyforrások észlelési képességei az Azure Marketplace-en keresztül

### <a name="web-application-firewall"></a>Webalkalmazási tűzfal

A webalkalmazási tűzfal ellenőrzi a bejövő webes forgalmat, és blokkolja az SQL-injektálásokat, a helyek közötti parancsfájlkezelést, a kártevők feltöltését, az alkalmazások DDoS-támadásait, valamint a webalkalmazásokra irányuló más támadásokat. Emellett megvizsgálja a háttér-webkiszolgálók válaszait az adatveszteség-megelőzési (DLP) szolgáltatáshoz. Az integrált hozzáférés-vezérlési motor lehetővé teszi, hogy a rendszergazdák részletes hozzáférés-vezérlési házirendeket hozzanak létre a hitelesítéshez, engedélyezéshez és nyilvántartáshoz (AAA), amely erős hitelesítést és felhasználói vezérlést biztosít a szervezeteknek

A webalkalmazási tűzfal a következő előnyöket biztosítja:

-   Észleli és blokkolja az SQL-injektálásokat, a helyek közötti parancsfájlkezelést, a kártevő szoftverek feltöltését, a DDoS-et vagy az alkalmazással szembeni bármilyen más támadást.

-   Hitelesítés és hozzáférés-vezérlés.

-   Megvizsgálja a kimenő forgalmat a bizalmas adatok észlelése érdekében, és képes az adatok kiszivárgására vagy blokkolására.

-   Felgyorsítja a webalkalmazás tartalmának kézbesítését, például gyorsítótárazást, tömörítést és más forgalmi optimalizációt használó képességeket.

Példa az Azure Marketplace-en elérhető webalkalmazási tűzfalakra: [BARRACUDA WAF, brokát Virtual Web Application Firewall (vWAF), inperverz SecureSphere és a ThreatSTOP IP-tűzfal](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>További lépések

- [Válasz a mai fenyegetésekre](../../security-center/security-center-alerts-overview.md#respond-threats): Segít azonosítani az Azure-erőforrásokat célozó aktív fenyegetéseket, és biztosítja a gyors reagáláshoz szükséges megállapításokat.

- [Azure SQL Database fenyegetések észlelése](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): Segít a lehetséges fenyegetésekkel kapcsolatos problémáinak megoldásában az adatbázisokban.
