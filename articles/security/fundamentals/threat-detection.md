---
title: Az Azure speciális veszélyészlelési szolgáltatása | Microsoft dokumentumok
description: Ismerje meg az Azure AD Identity Protection és annak képességeit.
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
ms.openlocfilehash: 3c1c385a87fc302d180729ec2e4bcd1c4a315f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981464"
---
# <a name="azure-advanced-threat-detection"></a>Azure fejlett fenyegetésészlelés

Az Azure fejlett fenyegetésészlelési funkciókat kínál olyan szolgáltatásokon keresztül, mint az Azure Active Directory (Azure AD), az Azure Monitor-naplók és az Azure Security Center. A biztonsági szolgáltatások és képességek ezen gyűjteménye egyszerű és gyors módot kínál annak megértésére, hogy mi történik az Azure-környezetekben.

Az Azure a biztonság konfigurálásához és testreszabásához számos lehetőséget biztosít az alkalmazástelepítések követelményeinek megfelelően. Ez a cikk ismerteti, hogyan felel meg ezeknek a követelményeknek.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[Az Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) az [Azure Active Directory Premium P2](../../active-directory/active-directory-whatis.md) kiadásának egyik szolgáltatása, amely áttekintést nyújt a szervezet identitását érintő kockázatészlelésekről és potenciális biztonsági résekről. Az Identity Protection az Azure AD anomáliaészlelési képességeket használja, amelyek az [Azure AD rendellenes tevékenységjelentéseken](../../active-directory/active-directory-reporting-azure-portal.md)keresztül érhetők el, és új kockázatészlelési típusokat vezet be, amelyek valós idejű anomáliákat észlelnek.

![Az Azure AD identitásvédelem diagramja](./media/threat-detection/azure-threat-detection-fig1.png)

Az Identity Protection adaptív gépi tanulási algoritmusokat és heurisztikát használ az anomáliák és a kockázatészlelések észlelésére, amelyek azt jelezhetik, hogy egy identitás sérült. Ezen adatok használatával az Identity Protection jelentéseket és riasztásokat hoz létre, így megvizsgálhatja ezeket a kockázatészleléseket, és megteheti a megfelelő szervizelési vagy kockázatcsökkentési műveletet.

Az Azure Active Directory identity protection több, mint egy figyelési és jelentéskészítő eszköz. A kockázatészlelések alapján az Identity Protection kiszámítja a felhasználói kockázati szintet az egyes felhasználók számára, így konfigurálhatja a kockázatalapú házirendeket a szervezet identitásainak automatikus védelmére.

Ezek a kockázatalapú szabályzatok az Azure Active Directory és [az EMS](../../active-directory/active-directory-conditional-access-azure-portal.md)által biztosított egyéb [feltételes hozzáférési vezérlők](../../active-directory/active-directory-conditional-access-azure-portal.md) mellett automatikusan blokkolhatják vagy felajánlhatják az adaptív javítási műveleteket, amelyek magukban foglalják a jelszó-visszaállítást és a többtényezős hitelesítés kényszerítését.

### <a name="identity-protection-capabilities"></a>Identitásvédelmi képességek

Az Azure Active Directory identity protection több, mint egy figyelési és jelentéskészítő eszköz. A szervezet identitásainak védelme érdekében konfigurálhatja a kockázatalapú házirendeket, amelyek automatikusan reagálnak az észlelt problémákra, ha egy adott kockázati szintet elértek. Ezek a szabályzatok az Azure Active Directory és az EMS által biztosított egyéb feltételes hozzáférés-vezérlések mellett automatikusan blokkolhatják vagy kezdeményezhetik az adaptív javítási műveleteket, beleértve a jelszó-visszaállítást és a többtényezős hitelesítés kényszerítését.

Példák néhány módja annak, hogy az Azure Identity Protection segítséget nyújtson fiókjai és identitásainak védelmében:

[A kockázatészlelés és a kockázatos fiókok észlelése](../../active-directory/identity-protection/overview.md)
-   Hat kockázatészlelési típus észlelése gépi tanulás és heurisztikus szabályok használatával.
-   A felhasználói kockázati szintek kiszámítása.
-   A biztonsági rések kiemelésével egyéni javaslatokat adhat az általános biztonsági helyzet javítására.

[Kockázatészlelések vizsgálata](../../active-directory/identity-protection/overview.md)
-   Értesítések küldése a kockázatészlelésekről.
-   Vizsgálja meg a kockázatészlelések et a releváns és kontextuális információk alapján.
-   A vizsgálatok nyomon követéséhez alapvető munkafolyamatokat biztosít.
-   Egyszerű hozzáférést biztosít a javítási műveletekhez, például a jelszó alaphelyzetbe állításához.

[Kockázatalapú, feltételes hozzáférési házirendek](../../active-directory/identity-protection/overview.md)
-   A kockázatos bejelentkezések csökkentése a bejelentkezések blokkolásával vagy a többtényezős hitelesítési kihívások megkövetelésével.
-   Kockázatos felhasználói fiókok letiltása vagy biztonságossá tétele.
-   A felhasználók regisztrációjának megkövetelése a többtényezős hitelesítéshez.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

Az [Azure Active Directory privilegizált identitáskezelés (PIM)](../../active-directory/privileged-identity-management/pim-configure.md)segítségével kezelheti, szabályozhatja és figyelheti a szervezeten belüli hozzáférést. Ez a funkció hozzáférést biztosít az Azure AD és más Microsoft online szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz.

![Azure AD kiemelt identitáskezelési diagram](./media/threat-detection/azure-threat-detection-fig2.png)

A PIM segít:

-   Értesítéseket és jelentéseket kaphat az Azure AD-rendszergazdákról, valamint a Microsoft online szolgáltatásaihoz, például az Office 365-höz és az Intune-hoz való just-in-time (JIT) felügyeleti hozzáférésről.

-   Jelentéseket kaphat a rendszergazdai hozzáférési előzményekről és a rendszergazdai hozzárendelések változásairól.

-   A kiemelt szerepkörhöz való hozzáféréssel kapcsolatos riasztások.

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók

[Az Azure Monitor naplói](../../azure-monitor/index.yml) egy Microsoft felhőalapú informatikai felügyeleti megoldás, amely segít a helyszíni és a felhőalapú infrastruktúra kezelésében és védelmében. Mivel az Azure Monitor naplók felhőalapú szolgáltatásként vannak megvalósítva, az infrastruktúra-szolgáltatásokba történő minimális befektetéssel gyorsan fel- és működtetheti azt. Az új biztonsági funkciók automatikusan kézbesítve lesznek, így a rendszer megtakarítja a folyamatos karbantartási és frissítési költségeket.

Az értékes szolgáltatások önmagukban történő biztosítása mellett az Azure Monitor naplói integrálhatók a System Center-összetevőkkel, például a [System Center Operations Managerrel,](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/)hogy kiterjesszék a meglévő biztonságkezelési beruházásokat a felhőbe. A System Center és az Azure Monitor naplók együtt teljes körű hibrid felügyeleti élményt biztosíthatnak.

### <a name="holistic-security-and-compliance-posture"></a>Holisztikus biztonsági és megfelelőségi testtartás

A [Log Analytics biztonsági és naplózási irányítópultja](../../security-center/security-center-intro.md) átfogó képet nyújt a szervezet informatikai biztonsági helyzetéről, és beépített keresési lekérdezéseket biztosít a figyelmet igénylő, jelentős problémákra. A Biztonság és naplózás irányítópultja az Azure Monitor-naplók biztonságával kapcsolatos mindenszolgáltatás kezdőképernyője. Magas szintű betekintést tesz lehetővé a számítógépek biztonsági állapotába. Megtekintheti az összes eseményt az elmúlt 24 órából, 7 napból vagy bármely más egyéni időkeretből.

Az Azure Monitor naplói segítségével gyorsan és egyszerűen megismerheti az informatikai műveletek környezetében, beleértve a szoftverfrissítési értékelést, a kártevőirtó értékelést és a konfigurációs alapkonfigurációk általános biztonsági állapotát. A biztonsági napló adatai könnyen elérhetők a biztonsági és megfelelőségi naplózási folyamatok egyszerűsítése érdekében.

![A Log Analytics biztonsági és naplózási irányítópultja](./media/threat-detection/azure-threat-detection-fig3.jpg)

A Log Analytics biztonsági és naplózási irányítópultja négy fő kategóriába sorolható:

-   **Biztonsági tartományok:** Lehetővé teszi a biztonsági rekordok további feltárását az idő múlásával; a rosszindulatú programok értékeléséhez való hozzáférés; aktualizálási értékelések; a hálózati biztonság, az identitás és a hozzáférési információk megtekintése; a biztonsági eseményekkel rendelkező számítógépek megtekintése; és gyorsan elérheti az Azure Security Center irányítópultját.

-   **Figyelemre méltó problémák:** Lehetővé teszi az aktív problémák számának és súlyosságának gyors azonosítását.

-   **Észlelések (előzetes verzió)**: Lehetővé teszi a támadási minták azonosítását a biztonsági riasztások megjelenítésével, amint azok az erőforrásokkal szemben történnek.

-   **Fenyegetésfelderítés:** Lehetővé teszi a támadási minták azonosítását a kimenő rosszindulatú IP-forgalommal rendelkező kiszolgálók teljes számának, a rosszindulatú fenyegetés típusának és az IP-helyek térképének megjelenítésével.

-   **Gyakori biztonsági lekérdezések**: Felsorolja a környezet figyeléséhez leggyakrabban használt biztonsági lekérdezéseket. Amikor kiválaszt egy lekérdezést, megnyílik a Keresés ablaktábla, és megjeleníti az adott lekérdezés eredményeit.

### <a name="insight-and-analytics"></a>Betekintés és elemzés
Az Azure [Monitor naplók középpontjában](../../log-analytics/log-analytics-queries.md) a tárház, amely az Azure üzemelteti.

![Insight és analytics diagram](./media/threat-detection/azure-threat-detection-fig4.png)

Adatokat gyűjthet a tárházba a csatlakoztatott forrásokból az adatforrások konfigurálásával és megoldások hozzáadásával az előfizetéshez.

![Az Azure Monitor naplózza az irányítópultot](./media/threat-detection/azure-threat-detection-fig5.png)

Az adatforrások és a megoldások külön rekordtípusokat hoznak létre saját tulajdonságkészlettel, de a tárház lekérdezéseiben továbbra is elemezheti őket. Ugyanazokat az eszközöket és módszereket használhatja a különböző források által gyűjtött különböző adatokkal való munkához.


Az Azure Monitor-naplókkal való interakció nagy része az Azure Portalon keresztül történik, amely bármely böngészőben fut, és hozzáférést biztosít a konfigurációs beállításokhoz és több eszközhöz az összegyűjtött adatok elemzéséhez és kezeléséhez. A portálról a következőket használhatja:
* [Naplókeresés,](../../log-analytics/log-analytics-queries.md) ahol lekérdezéseket hozhat létre az összegyűjtött adatok elemzéséhez.
* [Irányítópultok](../../azure-monitor/learn/tutorial-logs-dashboards.md), amelyeket a legértékesebb keresések grafikus nézeteivel testreszabhat.
* [Megoldások](../../monitoring/monitoring-solutions.md), amelyek további funkciókat és elemzési eszközöket biztosítanak.

![Elemzési eszközök](./media/threat-detection/azure-threat-detection-fig6.png)

A megoldások funkciókat adnak az Azure Monitor naplóihoz. Ezek elsősorban a felhőben futnak, és a naplóelemzési tárházban gyűjtött adatok elemzését biztosítják. A megoldások olyan új rekordtípusokat is definiálhatnak, amelyek naplókeresésekkel vagy a megoldás által a naplóelemzési irányítópulton biztosított további felhasználói felület használatával elemezhetők.

A Biztonsági és naplózás i irányítópult egy példa az ilyen típusú megoldásokra.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatizálás és vezérlés: Riasztás a biztonsági konfigurációs eltolódásokról

Az Azure Automation automatizálja a felügyeleti folyamatokat a PowerShellen alapuló és a felhőben futó runbookokkal. A runbookok futtathatók a helyi adatközpontban is, és kezelhetők velük a helyi erőforrások. Az Azure Automation konfigurációkezelést biztosít a PowerShell kívánt állapotkonfigurációjával (DSC).

![Az Azure Automation-diagram](./media/threat-detection/azure-threat-detection-fig7.png)

Az Azure-ban üzemeltetett DSC-erőforrásokat létrehozhatja és kezelheti, és alkalmazhatja őket a felhőalapú és a helyszíni rendszerekre. Ezzel meghatározhatja és automatikusan kényszerítheti a konfigurációjukat, vagy jelentéseket kaphat a driftről, így biztosíthatja, hogy a biztonsági konfigurációk a házirenden belül maradjanak.

## <a name="azure-security-center"></a>Azure Security Center

Az Azure Security Center segít megvédeni az Azure-erőforrásokat. Integrált biztonsági figyelést és házirend-kezelést biztosít az Azure-előfizetésekben. A szolgáltatáson belül az Azure-előfizetések és [az erőforráscsoportok](../../azure-resource-manager/management/manage-resources-portal.md) használatával szemben is definiálhat házirendeket a nagyobb részletesség érdekében.

![Az Azure Security Center diagramja](./media/threat-detection/azure-threat-detection-fig8.png)

A Microsoft biztonsági kutatói folyamatosan figyelik a megjelenő fenyegetéseket. Rendelkezésükre áll a telemetriai adatok kiterjedt halmaza, amelyet a Microsoft a globális felhőbeli és helyszíni jelenléte során gyűjtött össze. Ezen adatkészletek széles körű és változatos gyűjteményére alapozva a Microsoft fel tudja fedezni az új támadási mintákat és trendeket a helyszíni fogyasztói és a vállalati termékeiben, valamint az online szolgáltatásaiban.

Így a Security Center gyorsan frissítheti észlelési algoritmusait, mivel a támadók új és egyre kifinomultabb biztonsági réseket adnak ki. Ez a megközelítés segít lépést tartani a gyorsan változó fenyegetési környezettel.

![A Security Center fenyegetésészlelése](./media/threat-detection/azure-threat-detection-fig9.jpg)

A Security Center fenyegetésészlelése úgy működik, hogy automatikusan összegyűjti a biztonsági információkat az Azure-erőforrásokból, a hálózatból és a csatlakoztatott partneri megoldásokból. Elemzi ezt az információt, több forrásból származó információkat korrelálva, hogy azonosítsa a fenyegetéseket.

A Security Center prioritás szerinti sorolja be a biztonsági riasztásokat, és javaslatokat tesz a fenyegetés kiiktatására.

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. A big data- és [gépi tanulási](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technológiák terén elért áttörések a teljes felhőalapú háló eseményeinek kiértékelésére szolgálnak. A fejlett analitika képes észlelni azokat a fenyegetéseket, amelyeket manuális megközelítésekkel és a támadások alakulásának előrejelzésével lehetetlen lenne azonosítani. Ezeket a biztonsági elemzési típusokat a következő szakaszok ismertetik.

### <a name="threat-intelligence"></a>Fenyegetésészlelési intelligencia

A Microsoft nak hatalmas mennyiségű globális fenyegetésfelderítési információja van.

A telemetria több forrásból, például az Azure-ból, az Office 365-ből, a Microsoft CRM online-ból, a Microsoft Dynamics AX-ből, a outlook.com-ból, a MSN.com-ból, a Microsoft Digital Crimes Unit (DCU) és a Microsoft Security Response Centerből (MSRC) áramlik be.

![Fenyegetésfelderítési megállapítások](./media/threat-detection/azure-threat-detection-fig10.jpg)

A kutatók olyan fenyegetésfelderítési információkat is kapnak, amelyeket megosztanak a főbb felhőszolgáltatók között, és feliratkoznak harmadik felek tőlük származó fenyegetésfelderítési hírcsatornákra. Az Azure Security Center mindezeket az adatokat fel tudja használni arra, hogy értesítse Önt az ismert kártékony elemektől eredő fenyegetésekről. Néhány példa:

-   **A gépi tanulás nyújtotta lehetőségek kiaknázása:** Az Azure Security Center hatalmas mennyiségű adathoz fér hozzá a felhőhálózati tevékenységről, amelyek az Azure-telepítéseket célzó fenyegetések észlelésére használhatók.

-   **Találgatásos kényszerítés észlelése:** A gépi tanulás a távoli hozzáférési kísérletek előzménymintájának létrehozására szolgál, amely lehetővé teszi a Secure Shell (SSH), a Remote Desktop Protocol (RDP) és az SQL portok elleni találgatásos támadások észlelését.

-   **Kimenő DDoS és botnet észlelése:** A felhőbeli erőforrásokat célzó támadások közös célja, hogy ezeknek az erőforrásoknak a számítási teljesítményét más támadások végrehajtására használja.

-   **Új viselkedéselemző kiszolgálók és virtuális gépek**: A kiszolgáló vagy a virtuális gép feltörése után a támadók sokféle technikát alkalmaznak a rosszindulatú kód végrehajtására a rendszeren, elkerülve az észlelést, biztosítva az adatmegőrzést és a biztonsági vezérlők elkerülését.

-   **Azure SQL Database Threat Detection:** Fenyegetésészlelése az Azure SQL Database, amely azonosítja a rendellenes adatbázis-tevékenységek, amelyek azt jelzik, szokatlan és potenciálisan káros kísérletek adatbázisok elérésére vagy az adatbázisok kihasználására.

### <a name="behavioral-analytics"></a>Működés elemzése

A működés elemzése olyan módszer, amely megvizsgálja és összehasonlítja az adatokat az ismert minták gyűjteményével. Ezek a minták azonban nem csak egyszerű aláírások. Meghatározásuk hatalmas adatkészletekre alkalmazott összetett gépi tanulási algoritmusokkal történt.

![Viselkedéselemzési megállapítások](./media/threat-detection/azure-threat-detection-fig11.jpg)

A mintákat a rosszindulatú viselkedések szakértői elemzők általi gondos elemzésével is meghatározzák. Az Azure Security Center viselkedéselemzéssel azonosíthatja a feltört erőforrásokat a virtuálisgép-naplók, a virtuális hálózati eszköznaplók, a hálónaplók, az összeomlási memóriaképek és más források elemzése alapján.

Ezenkívül a minták összefüggenek más jelzésekkel, hogy ellenőrizzék a széles körben elterjedt kampány alátámasztó bizonyítékait. Az összefüggések felderítése segít azonosítani a feltörés meghatározott tüneteit mutató eseményeket.

Néhány példa:
-   **Gyanús folyamat-végrehajtás**: a támadók különböző módszereket vetnek be a kártékony szoftverek észrevétlen végrehajtásához. Előfordulhat például, hogy a támadó a rosszindulatú programoknak ugyanazokat a neveket adja meg, mint a legitim rendszerfájlok, de ezeket a fájlokat egy másik helyre helyezi, a jóindulatú fájlhoz hasonló nevet használ, vagy elfedi a fájl valódi kiterjesztését. A Security Center-modellek feldolgozzák a viselkedéseket, és figyelik a folyamatvégrehajtásokat az ilyen kiugró értékek észleléséhez.

-   **Rejtett kártevők és -kizsákmányolás kísérlet:** Kifinomult malware tud elkerül hagyományos antimalware termékek mellett egyik soha írás -hoz korong vagy titkosító szoftver összetevő raktározott -ra korong. Az ilyen kártevők azonban memóriaelemzéssel észlelhetők, mivel a kártevőnek nyomokat kell hagynia a memóriában a működéshez. Amikor a szoftver összeomlik, egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában. Az összeomlási memóriakép memóriájának elemzésével az Azure Security Center észleli a szoftver biztonsági réseinek kihasználására, a bizalmas adatok elérésére és a rendszer titokban megőrzött módon való megőrzésére használt technikákat anélkül, hogy befolyásolná a Gép.

-   **Oldalirányú mozgás és belső felderítés**: A feltört hálózatban való megmaradáshoz, valamint az értékes adatok megtalálásához és begyűjtéséhez a támadók gyakran megpróbálnak oldalirányban áthelyezni a feltört gépről másokra ugyanazon a hálózaton belül. A Security Center figyeli a folyamat- és bejelentkezési tevékenységeket, hogy felderítse a támadó hálózati megtágazásának kísérletét, például a távoli parancsvégrehajtást, a hálózati vizsgálatot és a fiók számbavételét.

-   **Rosszindulatú PowerShell-parancsfájlok:** A PowerShell segítségével a támadók rosszindulatú kódot futtathatnak a célvirtuális gépeken különböző célokra. A Security Center megvizsgálja a PowerShell tevékenységeit, hogy megtalálja a gyanús tevékenységek nyomait.

-   **Kimenő támadások**: a támadók gyakran felhőbeli erőforrásokat céloznak meg azzal a szándékkal, hogy ezeket az erőforrásokat használják fel a további támadások kibontakoztatásához. A feltört virtuális gépek például felhasználhatók más virtuális gépek elleni találgatásos támadások indítására, levélszemét küldésére, illetve nyitott portok és más eszközök interneten történő vizsgálatára. A hálózati forgalomra irányított gépi tanulás alkalmazásával a Security Center észlelni tudja, ha a szokásosnál nagyobb mértékű a kimenő hálózati kommunikáció. Ha levélszemetet észlel, a Security Center a szokatlan e-mail forgalmat az Office 365-ből származó intelligenciával is korrelálja annak megállapítása érdekében, hogy az e-mail valószínűleg aljas-e, vagy egy jogszerű e-mail kampány eredménye.

### <a name="anomaly-detection"></a>Rendellenességek észlelése

Az Azure Security Center további módszere a fenyegetések felderítésére a rendellenességek észlelése. A működés elemzésével (amely a nagy adatkészletekből kinyert ismert mintákon alapul) ellentétben a rendellenességek észlelése „testre szabottabb”, és az üzemelő példányokhoz tartozó alapkonfigurációkra összpontosít. A gépi tanulás a központi telepítések normál tevékenységének meghatározásához kerül alkalmazásra, majd szabályok jönnek létre a biztonsági eseményt képviselő kiugró feltételek meghatározásához. Például:

-   **Bejövő RDP/SSH találgatásos támadások:** Előfordulhat, hogy a központi telepítések foglalt virtuális gépek sok bejelentkezési minden nap, és más virtuális gépek, amelyek kevés, ha van ilyen, bejelentkezések. Az Azure Security Center meghatározhatja ezeknek a virtuális gépeknek az alapkonfigurációs bejelentkezési tevékenységét, és gépi tanulással definiálhatja a normál bejelentkezési tevékenységeket. Ha eltérés van a bejelentkezéssel kapcsolatos jellemzőkhöz meghatározott alaptervvel, riasztás keletkezhet. Ebben az esetben is gépi tanulás alapján határozza meg, hogy mi számít szignifikáns eltérésnek.

### <a name="continuous-threat-intelligence-monitoring"></a>A fenyegetésekre vonatkozó intelligencia folyamatos figyelése

Az Azure Security Center világszerte biztonsági kutatási és adatelemzési csapatokkal működik, amelyek folyamatosan figyelik a fenyegetési környezet változásait. Ide tartoznak a következők:

-   **Fenyegetésfelderítésfigyelés:** A fenyegetésfelderítés mechanizmusokat, mutatókat, következményeket és a meglévő vagy újonnan megjelenő fenyegetésekre vonatkozó végrehajtható tanácsokat tartalmaz. Ezeket az információkat megosztja a biztonsági közösség, és a Microsoft folyamatosan figyeli a fenyegetésfelderítési hírcsatornák at belső és külső forrásokból.

-   **Jelmegosztás:** A biztonsági csapatok tól származó, a felhőbeli és helyszíni szolgáltatások, kiszolgálók és ügyfélvégpont-eszközök széles Microsoft-portfóliójának elemzései megosztásra és elemzésre kerülnek.

-   **Microsoft biztonsági szakemberek**: Folyamatos elkötelezettség a microsoftos csapatokkal, amelyek speciális biztonsági területeken dolgoznak, például a kriminalisztika és a webes támadások észlelése terén.

-   **Észlelési hangolás:** Az algoritmusok valós ügyfél-adatkészleteken futnak, és a biztonsági kutatók együttműködnek az ügyfelekkel az eredmények érvényesítésében. Az igazi és a téves találatok megjelölésével pontosítják a gépi algoritmusokat.

Ezek az együttes erőfeszítések új és továbbfejlesztett észlelésekben csúcsosodnak ki, amelyek azonnal hasznosak lehetnek. Nincs mit tettél volna.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Speciális fenyegetésészlelési funkciók: Egyéb Azure-szolgáltatások

### <a name="virtual-machines-microsoft-antimalware"></a>Virtuális gépek: Microsoft kártevőirtó

A [Microsoft antimalware](antimalware.md) for Azure egy ügynökkel rendelkező megoldás alkalmazások és a bérlői környezetek, amelynek célja, hogy fut a háttérben emberi beavatkozás nélkül. Az alkalmazásszámítási feladatok igényei alapján telepítheti a védelmet, akár alapszintű biztonságos, akár speciális egyéni konfigurációval, beleértve a kártevőirtó figyelést is. Az Azure kártevőirtó egy biztonsági lehetőség az Azure virtuális gépek, amely automatikusan telepítve van az összes Azure PaaS virtuális gépek.

#### <a name="microsoft-antimalware-core-features"></a>A Microsoft kártevőirtó fő szolgáltatásai

Az alábbiakban az Azure azon funkcióit ismertetik, amelyek telepítik és engedélyezik a Microsoft kártevőirtóját az alkalmazásokhoz:

-   **Valós idejű védelem**: Figyeli a felhőszolgáltatásokban és a virtuális gépeken végzett tevékenységeket a rosszindulatú programok végrehajtásának észleléséhez és blokkolásához.

-   **Ütemezett vizsgálat**: Rendszeres időközönként célzott vizsgálathajt végre a rosszindulatú programok észleléséhez, beleértve az aktívan futó programokat is.

-   **Kártevő-szervizelés:** Automatikusan fellép az észlelt kártevők reklisen, például rosszindulatú fájlok törlése vagy karanténba törlése, valamint a kártékony rendszerleíró bejegyzések törlése.

-   **Aláírás-frissítések**: Automatikusan telepíti a legújabb védelmi aláírásokat (vírusdefiníciókat) annak érdekében, hogy a védelem egy előre meghatározott gyakorisággal naprakész legyen.

-   **Kártevőirtó motor frissítései:** Automatikusan frissíti a Microsoft Kártevőirtó motort.

-   **Kártevőirtó platform frissítései:** Automatikusan frissíti a Microsoft kártevőirtó platformját.

-   **Aktív védelem**: A microsoft Azure-nak jelenti az észlelt fenyegetésekés gyanús erőforrások telemetriai metaadatait, hogy biztosítsa a fenyegetések változó helyzetére való gyors reagálást, lehetővé téve a valós idejű szinkron aláírás-kézbesítést a Microsoft aktív védelmi rendszeren keresztül.

-   **Mintajelentés**: Mintákat biztosít és jelent a Microsoft kártevőirtó szolgáltatásának a szolgáltatás finomítása és a hibaelhárítás engedélyezése érdekében.

-   **Kizárások**: Lehetővé teszi, hogy az alkalmazás- és szolgáltatásgazdák bizonyos fájlokat, folyamatokat és meghajtókat konfiguráljanak a védelemből és a vizsgálatból való kizárásra teljesítmény és egyéb okok miatt.

-   **Kártevőirtó eseménygyűjtemény:** rögzíti a kártevőirtó szolgáltatás állapotát, a gyanús tevékenységeket és az operációs rendszer eseménynaplójában végrehajtott javítási műveleteket, és összegyűjti azokat az ügyfél Azure-tárfiókjába.

### <a name="azure-sql-database-threat-detection"></a>Azure SQL adatbázis fenyegetésészlelése

[Az Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) egy új biztonsági intelligencia szolgáltatás az Azure SQL Database szolgáltatásba épített. Az Azure SQL Database Threat Detection éjjel-nappal dolgozik a rendellenes adatbázis-tevékenységek megismeréséhez, profiljához és észleléséhez, és azonosítja az adatbázist fenyegető potenciális veszélyeket.

A biztonsági tisztviselők vagy más kijelölt rendszergazdák azonnali értesítést kaphatnak a gyanús adatbázis-tevékenységekről, amint azok bekövetkeznek. Minden értesítés részletesen ismerteti a gyanús tevékenységet, és azt javasolja, hogyan tovább vizsgálja ki és csökkentse a fenyegetést.

Jelenleg az Azure SQL Database Threat Detection észleli a potenciális biztonsági réseket és az SQL-injektálási támadásokat, valamint a rendellenes adatbázis-hozzáférési mintákat.

A fenyegetésészlelési e-mail értesítés kézhezvételét követően a felhasználók az e-mailben található mély hivatkozáson keresztül navigálhatnak és tekinthetik meg a megfelelő naplózási rekordokat. A hivatkozás megnyit egy naplót vagy egy előre konfigurált auditáló Excel-sablont, amely a gyanús esemény időpontjában megjeleníti a megfelelő naplózási rekordokat, a következők szerint:

-   Az adatbázis/kiszolgáló tárházának naplózása a rendellenes adatbázis-tevékenységekkel.

-   Az esemény időpontjában a napló írásához használt megfelelő naplózási tárolótábla.

-   Az esemény előfordulását közvetlenül követő óra naplózása.

-   Az esemény időpontjában hasonló eseményazonosítóval rendelkező rekordok naplózása (egyes érzékelők esetében nem kötelező).

Az SQL Database fenyegetésérzékelői az alábbi észlelési módszerek egyikét használják:

-   **Determinisztikus észlelés:** Észleli a gyanús mintákat (szabályok on- és alapja) az SQL-ügyféllekérdezésekben, amelyek megfelelnek az ismert támadásoknak. Ez a módszer magas észlelési és alacsony hamis pozitív, de korlátozott lefedettség, mert kategóriájába tartozik a "atomi észlelések."

-   **Viselkedési észlelés:** Észleli a rendellenes tevékenység, amely rendellenes viselkedés az adatbázisban, amely nem volt látható a legutóbbi 30 nap. Sql-ügyfél rendellenes tevékenység például a sikertelen bejelentkezések vagy lekérdezések csúcsa, nagy mennyiségű kinyert adat, szokatlan kanonikus lekérdezések vagy ismeretlen IP-címek az adatbázis eléréséhez használt.

### <a name="application-gateway-web-application-firewall"></a>Alkalmazásátjáró webalkalmazás-tűzfala

[A Web Application Firewall (WAF)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) az [Azure Application Gateway](../../application-gateway/application-gateway-web-application-firewall-overview.md) egyik szolgáltatása, amely védelmet nyújt azon webalkalmazások számára, amelyek alkalmazásátjárót használnak a szabványos [alkalmazáskézbesítési vezérlő](https://kemptechnologies.com/in/application-delivery-controllers) funkciókhoz. A Web Application Firewall ezt úgy éri el, hogy megvédi őket az [OWASP (Open Web Application Security Project, OWASP) 10 leggyakoribb webes biztonsági rése idomításaitól.](https://www.owasp.org/index.php/Top_10_2010-Main)

![Alkalmazásátjáró webalkalmazás-tűzfaldiagramja](./media/threat-detection/azure-threat-detection-fig13.png)

A védelem a következőket tartalmazza:

-   SQL injekciózás elleni védelem.

-   Helyek közötti parancsfájlok védelme.

-   Gyakori webes támadások elleni védelem, például parancsinjektálás, HTTP-kérelem-csempészet, HTTP-válaszfelosztás és távoli fájlfelvétel-támadás.

-   Védelem a HTTP protokoll megsértése ellen.

-   Http-protokoll anomáliák, például a hiányzó gazdagép user-agent és elfogadja a fejlécek elleni védelem.

-   A robotok, a robot- és a lapolvasók elleni megelőzés.

-   Gyakori alkalmazáshelytelen konfigurációk észlelése (azaz Apache, IIS és így tovább).

A WAF konfigurálása az alkalmazásátjárón a következő előnyökkel jár:

-   Megvédi a webalkalmazást a webes biztonsági résektől és támadásoktól a háttérkód módosítása nélkül.

-   Több webalkalmazást véd egyidejűleg egy alkalmazásátjáró mögött. Az alkalmazásátjáró legfeljebb 20 webhely üzemeltetését támogatja.

-   Az alkalmazásátjáró WAF-naplói által létrehozott valós idejű jelentések használatával figyeli a támadások elleni támadásokat.

-   Segít megfelelni a megfelelőségi követelményeknek. Bizonyos megfelelőségi vezérlők megkövetelik, hogy az összes internetfelé néző végpontot WAF-megoldás védi.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>Anomáliadetektálási API: Az Azure Machine Learning segítségével készült

Az Anomáliadetektálás API egy API, amely hasznos az idősorozat-adatok ban a rendellenes minták különböző észleléséhez. Az API anomáliapontszámot rendel az idősorok minden adatpontjához, amely riasztások létrehozására, irányítópultokon keresztüli figyelésre vagy a jegyértékesítési rendszerekhez való csatlakozásra használható.

Az [Anomáliadetektálási API](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) a következő típusú anomáliákat észleli az idősorozat-adatokon:

-   **Tüskék és dipek:** Ha egy szolgáltatás bejelentkezési hibáinak számát vagy egy e-kereskedelmi webhelyen a kijelentkezések számát figyeli, a szokatlan kiugrások vagy dipek biztonsági támadásokat vagy szolgáltatáskimaradásokat jelezhetnek.

-   **Pozitív és negatív trendek:** Ha a memóriahasználatot figyeli a számítástechnikában, a szabad memória méretének csökkentése potenciális memóriavesztést jelez. A szolgáltatásvárólista hosszának figyelése esetén a tartósan emelkedő tendencia egy mögöttes szoftverproblémát jelezhet.

-   **Szintváltozások és az értékek dinamikus tartományának változásai:** Érdekes lehet figyelni a szolgáltatás késések változásait a szolgáltatás frissítése után, vagy alacsonyabb kivételszinteket a frissítés után.

A gépi tanuláson alapuló API a következőket teszi lehetővé:

-   **Rugalmas és robusztus defektálás:** Az anomáliadetektálási modellek lehetővé teszik a felhasználók számára az érzékenységi beállítások konfigurálását és a szezonális és nem szezonális adatkészletek közötti anomáliák észlelését. A felhasználók módosíthatják az anomáliadetektálási modellt, hogy az észlelési API kevésbé vagy érzékenyebb legyen az igényeiknek megfelelően. Ez azt jelentené, hogy észleli a kevésbé vagy több látható anomáliák at adatok szezonális minták és anélkül.

-   **Méretezhető és időszerű észlelés:** A szakértők tartományismeretei által meghatározott jelenlegi küszöbértékekkel történő figyelés hagyományos módja költséges, és nem méretezhető dinamikusan változó adatkészletek millióira. Az api-ban az anomáliadetektálási modelleket a rendszer megtanulja, és a modelleket automatikusan a korábbi és a valós idejű adatokból is rendszerezi.

-   **Proaktív és végrehajtható detektálás:** Lassú trend- és szintváltozás-észlelés alkalmazható a korai anomáliadetektáláshoz. A korai abnormális jeleket, hogy az észlelt lehet használni, hogy közvetlen embereket, hogy vizsgálja meg, és járjon el a problémás területeken. Emellett a kiváltó ok-elemzési modellek és riasztási eszközök ezen anomália-detektálási API-szolgáltatás on fejleszthetők.

Az anomáliadetektálási API hatékony és hatékony megoldás a forgatókönyvek széles köréhez, például a szolgáltatás állapotához és a KPI-figyeléshez, az IoT-hez, a teljesítményfigyeléshez és a hálózati forgalom figyeléséhez. Íme néhány népszerű forgatókönyv, ahol ez az API hasznos lehet:

- Az informatikai részlegeknek eszközökre van szükségük az események, a hibakód, a használati napló és a teljesítmény (CPU, memória és így tovább) időben történő nyomon követéséhez.

-   Az online kereskedelmi webhelyek nyomon szeretnék követni az ügyfelek tevékenységeit, az oldalmegtekintéseket, a kattintásokat és így tovább.

-   A közüzemi vállalatok nyomon akarják követni a víz-, gáz-, villamosenergia-és egyéb erőforrások fogyasztását.

-   Létesítmény vagy épület menedzsment szolgáltatások szeretné figyelemmel kíséri a hőmérséklet, a nedvesség, a forgalom, és így tovább.

-   Az IoT/gyártók az idősorokban lévő érzékelők adatait szeretnék felhasználni a munkafolyamat, a minőség és így tovább figyelésére.

-   A szolgáltatóknak, például a hívásközpontoknak figyelniük kell a szolgáltatásigény alakulását, az incidens volumenét, a várakozási várlista hosszát és így tovább.

-   Az üzleti elemzési csoportok valós időben szeretnék figyelni az üzleti kpi-k (például az értékesítési volumen, az ügyfélhangulat vagy az árképzés) rendellenes mozgását.

### <a name="cloud-app-security"></a>Cloud App Security

[A Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) a Microsoft Cloud Security verem kritikus összetevője. Ez egy átfogó megoldás, amely segítséget nyújt a szervezetnek, ahogy mozog, hogy teljes mértékben kihasználják a felhőalapú alkalmazások ígéretét. Ez tartja meg az irányítást, a jobb láthatóságot a tevékenység. Emellett a felhőalkalmazások kritikus adatainak védelmét is növeli.

A Cloud App Security eszközeivel könnyebb az informatikai árnyék-infrastruktúra feltárása, a kockázatfelmérés, a szabályzatok kényszerítése, a tevékenységek vizsgálata, valamint a veszélyek megakadályozása. A szervezet így biztonságosan helyezheti át az adatait a felhőbe a kritikus adatok felügyelete mellett.

| | |
|---|---|
| Felderítés | Felfedheti az informatikai árnyék-infrastruktúrát a Cloud App Security használatával. A felhőalapú környezet alkalmazásainak, tevékenységeinek, felhasználóinak, adatainak és fájljainak felderítésével nagyobb láthatóságot érhet el. Felderítheti a felhőhöz csatlakoztatott harmadik féltől származó alkalmazásokat.|
|Vizsgálat | Megvizsgálhatja a felhőalkalmazásait felhőalapú nyomkereső eszközökkel, és részletesen megismerheti a hálózatban lévő kockázatos alkalmazásokat, meghatározott felhasználókat és fájlokat. Mintákat kereshet a felhőből gyűjtött adatokban. Jelentéseket hozhat létre a felhő figyelésére. |
| Vezérlés | Szabályzatokkal és riasztásokkal teljes mértékben átveheti az irányítást a hálózati felhőforgalom felett, és mérsékelheti a kockázatokat. A Cloud App Securityvel biztonságos, engedélyezett felhőalkalmazás-alternatívákra telepítheti át a felhasználóit. |
| Védelem | A Cloud App Security használatával szankcionálhatja vagy letilthatja az alkalmazásokat, kényszerítheti az adatvesztés megelőzését, szabályozhatja az engedélyeket és a megosztást, valamint egyéni jelentéseket és riasztásokat hozhat létre. |
| Vezérlés | Szabályzatokkal és riasztásokkal teljes mértékben átveheti az irányítást a hálózati felhőforgalom felett, és mérsékelheti a kockázatokat. A Cloud App Securityvel biztonságos, engedélyezett felhőalkalmazás-alternatívákra telepítheti át a felhasználóit. |
| | |


![Felhőalapú alkalmazásbiztonsági diagram](./media/threat-detection/azure-threat-detection-fig14.png)

A Cloud App Security a következők szerint integrálja a láthatóságot a felhőbe:

-   A Cloud Discovery segítségével leképezheti és azonosíthatja a felhőkörnyezetet és a szervezet által használt felhőalkalmazásokat.

-   Alkalmazások szankcionálása és tiltása a felhőben.

-   A szolgáltatóapi-k előnyeit kihasználó, könnyen telepíthető alkalmazásösszekötők használata a kapcsolódó alkalmazások láthatósága és irányítása érdekében.

-   Folyamatos vezérlést biztosít a házirendek beállításával, majd folyamatos finomhangolásával.

Az ezekből a forrásokból történő adatgyűjtéskor a Cloud App Security kifinomult elemzést futtat rajta. Azonnal értesíti Önt a rendellenes tevékenységekről, valamint részletes információkat biztosít a felhőalapú környezetről. A Cloud App Security szolgáltatásban konfigurálhat egy szabályzatot, amellyel megvédheti a teljes felhőalapú környezetet.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Külső féltől származó speciális fenyegetésészlelési képességek az Azure Piactéren keresztül

### <a name="web-application-firewall"></a>Web Application Firewall (Webalkalmazási tűzfal)

Web Alkalmazás tűzfal ellenőrzi a bejövő webes forgalom és blokkolja az SQL injekciók, cross-site scripting, malware feltöltések, alkalmazás DDoS támadások, és egyéb támadások célzott a webes alkalmazások. Azt is megvizsgálja a válaszokat a háttér-webkiszolgálók adatvesztés megelőzésére (DLP). Az integrált hozzáférés-vezérlési motor lehetővé teszi a rendszergazdák számára, hogy részletes hozzáférés-vezérlési házirendeket hozzanak létre a hitelesítéshez, az engedélyezéshez és a számlázáshoz (AAA), amely erős hitelesítést és felhasználói vezérlést biztosít a szervezeteknek.

A WebApplication Firewall a következő előnyöket nyújtja:

-   Észleli és blokkolja az SQL injekciókat, a webhelyek közötti parancsfájlokat, a rosszindulatú programok feltöltését, az alkalmazás DDoS-t vagy az alkalmazás elleni bármely más támadást.

-   Hitelesítés és hozzáférés-vezérlés.

-   Megvizsgálja a kimenő forgalmat a bizalmas adatok észleléséhez, és elfedheti vagy letilthatja az adatok kiszivárgását.

-   Felgyorsítja a webalkalmazások tartalmának kézbesítését olyan funkciók kal, mint a gyorsítótárazás, a tömörítés és más forgalomoptimalizálás.

Példák az Azure Piactéren elérhető webalkalmazás-tűzfalakra: [Barracuda WAF, Brocade virtual webapplication firewall (vWAF), Imperva SecureSphere és threatstop IP-tűzfal.](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf)

## <a name="next-steps"></a>További lépések

- [Válasz a mai fenyegetések:](../../security-center/security-center-alerts-overview.md#respond-threats)Segít azonosítani az aktív fenyegetések, amelyek célja az Azure-erőforrások at, és biztosítja a szükséges betekintést a gyors reagáláshoz.

- [Azure SQL Database fenyegetésészlelés:](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)Segít az adatbázisokat fenyegető potenciális fenyegetésekkel kapcsolatos aggodalmak kezelésében.
