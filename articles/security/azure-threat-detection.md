---
title: Azure speciális Fenyegetésészlelés |} Microsoft Docs
description: Ismerje meg a azonosító adatok védelmét és képességeit.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: eb1bf9f6465a8a94fd2de75f581817887c333289
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895328"
---
# <a name="azure-advanced-threat-detection"></a>Az Azure fejlett fenyegetések észlelése
## <a name="introduction"></a>Bevezetés

### <a name="overview"></a>Áttekintés

A Microsoft kifejlesztette tanulmányok, biztonsági áttekintése, ajánlott eljárások és ellenőrzőlistákat segít a különböző biztonsági képességeivel kapcsolatos elérhető Azure-ügyfél és az Azure platformra körülvevő sorozata. A témakörök között körének és mélysége, és rendszeresen frissülnek. Ez a dokumentum a sorozat része a következő absztrakt szakaszban foglaltak szerint.

### <a name="azure-platform"></a>Az Azure Platform

Azure egy nyilvános felhőszolgáltatási platform, amely támogatja a operációs rendszerek, programozási nyelvek, keretrendszerek, eszközök, adatbázisok és eszközök legszélesebb kiválasztása.
A következő programozási nyelveket támogatja:
-   Futtassa a Linux-tárolók Docker-integráció.
-   JavaScript, Python, .NET, PHP, Java és Node.js-alkalmazások létrehozása
-   Build vissza-végpontok iOS, Android és Windows eszközökhöz.

Azure nyilvános felhőjében services támogatja a fejlesztők milliói technológiát és informatikai szakemberek számára már alapulnak, és megbízható.

Amikor telepít egy szervezet, hogy a szervezet feladata az adatok védelméhez és biztonsági, valamint a rendszer körül irányítás egy nyilvános felhőbe.

Az Azure infrastruktúráját úgy tervezték, hogy képes legyen ügyfelek millióit egyidejűleg kiszolgáló alkalmazásokat üzemeltetni, és olyan megbízható alapot nyújtson, amely megfelel a vállalatok biztonsági igényeinek. Azure konfigurálására és testreszabására biztonsági követelményeinek teljesítése érdekében az alkalmazások telepítésének a lehetőségek széles köréről biztosít. Ez a dokumentum segít az elvárásoknak.

### <a name="abstract"></a>Absztrakt

Azure Active Directory, Azure Operations Management Suite (OMS) és az Azure Security Center, például az advanced threat észlelési funkció használatával a beépített Microsoft Azure-ajánlatok. Ez a témakörgyűjtemény a biztonsági szolgáltatásokat és képességeket biztosítja az gyorsan és egyszerűen a Mi történik, az Azure-környezetekhez belül.

Ez a dokumentum ismerteti a "Microsoft Azure megközelíti" fenyegetés biztonsági rés diagnosztikai felé, és a kiszolgálók és más Azure-erőforrások elleni megcélzott rosszindulatú tevékenységek elemzése a kockázat társított. Ennek segítségével azonosíthatja a optimalizált megoldásokkal azonosítása és a biztonsági rés felügyeleti módszerek a Azure platformon és ügyfélkapcsolati biztonsági szolgáltatásokat és technológiákat.

Ez a dokumentum elsősorban az Azure platformon és -vezérlők ügyfélkapcsolati technológia, és nem kísérli meg cím SLA-k, árképzési modellt és DevOps eljárásokkal kapcsolatos szempontok.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

![Azure Active Directory Identity Protection](./media/azure-threat-detection/azure-threat-detection-fig1.png)


[Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) szolgáltatása a [Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) kiadása, amely a kockázati eseményekről és a szervezet identitásait érintő lehetséges biztonsági rések áttekintést nyújt. Microsoft rendelkezik lett biztonságossá tétele a felhőalapú identitás egy évtizedben keresztül, és az Azure AD Identity Protection Microsoft van ezek azonos védelmi rendszerek elérhetővé tételére vállalati ügyfelek. Azonosító adatok védelmét a meglévő Azure AD anomáliadetektálási észlelési keresztül elérhető szolgáltatásait használja [az Azure AD rendellenes Tevékenységjelentések](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports), és vezet be az új kockázat típusait, amely valós idejű rendellenességek észlelését.

Az Identity Protection adaptív gépi tanulási algoritmusok és heurisztika segítségével észleli a rendellenességeket és a kockázati eseményeket, amelyek azt jelezhetik, hogy valamely identitás biztonsága sérült. Ezeket az adatokat használva Identity Protection állít elő, jelentéseket és riasztásokat, amelyek lehetővé teszik a kockázati eseményekről vizsgálata, és tegye meg megfelelő szervizelési vagy megoldás művelet.

De Azure Active Directory Identity Protection több mint egy figyelési és jelentéskészítési eszköz. Identity Protection kockázati események alapján számítja ki minden felhasználó, amely lehetővé teszi, kockázati-alapú házirendek automatikusan védő a szervezete egy felhasználó kockázati szintjét.

A kockázat-alapú házirendek mellett egyéb [feltételes hozzáférés-vezérlést](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) Azure Active Directory által biztosított és [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), automatikusan letilthatja vagy kínálnak, amely tartalmazza az adaptív szervizelési műveletek jelszó alaphelyzetbe állítását és a többtényezős hitelesítés kényszerítése.

### <a name="identity-protections-capabilities"></a>Identitás-megelőzési képességek

Az Azure Active Directory azonosító adatok védelmét a rendszer több mint egy figyelési és jelentéskészítési eszköz. A szervezet identitásait védelme érdekében beállíthatja kockázat-alapú, amely automatikusan észlelt problémák válaszolni a megadott kockázati szint elérésekor. Ezek a házirendek mellett más feltételes hozzáférés-szabályozási EMS, és az Azure Active Directory által biztosított automatikusan blokkolja, vagy adaptív szervizelési műveletek, például jelszó alaphelyzetbe állítása és a többtényezős hitelesítés kényszerítése kezdeményezni.

Példák néhány módot, amelyek segítségével Azure Identity Protection a fiókok biztosításában és identitások tartalmazza:

[Annak ellenőrzése, kockázati eseményekről és kockázatos fiókok:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Gépi tanulás és heurisztikus szabályok használatával hat kockázat eseménytípusok észlelése
-   Kockázati szintek felhasználói kiszámítása
-   Egyéni kapcsolatos ajánlásokat általános biztonsági állapotát biztonsági rések részt biztosítása

[Vizsgáló kockázati események:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Értesítésküldési kockázati események
-   Megfelelő és környezetfüggő információk alapján kockázati események kivizsgálása
-   Vizsgálatok követéséhez alapvető munkafolyamatok biztosítása
-   Így könnyen elérhetők szervizelési műveletek, például a jelszó alaphelyzetbe állítása

[Kockázati-alapú feltételes hozzáférési házirendek:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   Házirend kockázatos bejelentkezések mérséklése bejelentkezések blokkolja, vagy a multi-factor authentication kihívások szükségessé tételével.
-   Házirend letiltás vagy biztonságos kockázatos felhasználói fiókok
-   A felhasználók a multi-factor authentication regisztrálása házirend

### <a name="azure-ad-privileged-identity-management-pim"></a>Az Azure AD Privileged Identity Management (PIM)

A [az Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure),

![Azure AD Privileged Identity Management](./media/azure-threat-detection/azure-threat-detection-fig2.png)

kezelése, szabályozása, és figyelje a hozzáférést a szervezeten belül. Ebbe beletartozik az Azure AD és más online Microsoft-szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz való hozzáférés.

Az Azure AD Privileged Identity Management nyújt segítséget:

-   Figyelmeztetést kap, és jelentést az Azure AD-rendszergazdák és a "csak az időben" rendszergazdai hozzáféréssel a Microsoft Online szolgáltatások, például Office 365 és az Intune-ban

-   Rendszergazda-hozzárendelések beolvasása a rendszergazdai hozzáférési műveleteiről és a változások

-   Egy kiemelt szerepkörhöz való hozzáféréssel kapcsolatos riasztásokat kaphat

## <a name="microsoft-operations-management-suite-oms"></a>A Microsoft Operations Management Suite (OMS)

[A Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) a Microsoft felhőalapú informatikai felügyeleti megoldás, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúra. Mivel az OMS felhőalapú szolgáltatás, gyorsan és az infrastruktúra-szolgáltatásokra fordított minimális mértékű befektetéssel üzembe helyezhető. Új biztonsági funkciók automatikusan érkeznek, a folyamatos karbantartási mentésekor, és frissítse a költségeket.

Mellett értékes szolgáltatásokat nyújtó önállóan, OMS integrálható a System Center-összetevő például [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/) kiterjesztése a meglévő biztonsági management beruházások kiterjeszti a felhőbe. A System Center és az OMS együttműködve teljes hibrid felügyeleti megoldást képes biztosítani.

### <a name="holistic-security-and-compliance-posture"></a>Körű biztonsági és megfelelőségi állapotát

A [OMS biztonsági és naplózási irányítópult](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) nyújt átfogó képet kaphat a szervezet informatikai biztonságot beépített keresési lekérdezések a jelentős figyelmet igénylő problémák. A biztonsági és naplózási irányítópult a kezdőképernyőn minden biztonsággal az OMS Szolgáltatáshoz. Magas szintű betekintést tesz lehetővé a számítógépek biztonsági állapotába. Olyan funkciót is kínál, amellyel megjeleníthető az elmúlt 24 óra, 7 nap vagy bármely más egyéni időszak összes eseménye.

OMS irányítópultok gyorsan és könnyen megérteni az általános biztonsági állapotát a környezet összes informatikai műveleteket, köztük a kontextusában: szoftver frissítések értékelését, kártevőirtó értékelési és referenciakonfigurációkat. Ezenkívül biztonsági napló adata könnyen hozzáférhető a biztonsági és megfelelőségi ellenőrzési folyamatok egyszerűsítésére.

Az OMS biztonsági és auditálási irányítópultja négy fő kategória alapján van felosztva:

![Az OMS biztonsági és auditálási irányítópultja](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

-   **Biztonsági tartományok:** ezen a területen meg fogja tudni további felfedezés biztonsági rekordok időbeli, kártevő assessment hozzáférni, felmérési és a hálózati biztonság, a identitás- és hozzáférés adatait, a számítógépek biztonsági események frissítse, és gyorsan a hozzáférést az Azure Security Center irányítópultjának.

-   **Jelentős problémák:** ezzel a beállítással gyorsan azonosíthatja az aktív problémák számát és ezek a problémák súlyosságát.

-   **Észlelések (előzetes verzió):** lehetővé teszi a biztonsági riasztások megjelenítése, hogy elvégezze a erőforrásokon támadási mintákat azonosíthatja.

-   **Fenyegetésfelderítési adataival:** lehetővé teszi a támadási mintákat azonosíthatja a kimenő kártékony IP-forgalom, ha ezeket az IP-címekről érkező megjelenítő és a kártevő-fenyegetés típusú kiszolgálók száma megjelenítése.

-   **Általános biztonsági lekérdezések:** ezt a beállítást a leggyakrabban használt biztonsági lekérdezések, amelyek segítségével figyelheti a környezet listáját jeleníti meg. Ha valamelyik lekérdezések gombra kattint, a lekérdezés eredményeit nyílik meg a Search paneljét.

### <a name="insight-and-analytics"></a>Betekintések és elemzés
Középső [Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) az OMS-tárházban, amely Azure felhőben szolgáltatott van.

![Betekintések és elemzés](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Az adatok a csatlakozó forrásokból kerülnek be a tárházba az adatforrások konfigurálása és a megoldások előfizetésbe való felvétele révén.

![előfizetést](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Az adatforrások és megoldások egyaránt különböző rekordtípusokat fognak létrehozni, amelyek saját tulajdonsághalmazzal rendelkeznek, de mégis elemezhetők együtt a tárházra irányuló lekérdezésekben. Ez lehetővé teszi, hogy ugyanazokat az eszközöket és módszereket használva dolgozzon a különböző források által gyűjtött különböző típusú adatokkal.


A Log Analyticshez való együttműködéshez többsége az OMS-portállal, amely böngészők fut, és ez a gyűjtött adatokat, aki hozzáféréssel rendelkezik a konfigurációs beállításokat, és több eszközök elemzéséhez és a működésre keresztül történik. A portálról, használhat [keresések jelentkezzen](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) , összegyűjtött adatok elemzéséhez lekérdezéseket hozhat létre adott [irányítópultok](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards), amely testre szabhatja a legértékesebb keresések és grafikusnézetek[megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), amelyek biztosítják, hogy további funkciók és -elemző eszközökkel.

![elemzésére szolgáló eszközöket](./media/azure-threat-detection/azure-threat-detection-fig6.png)

A megoldások funkciókkal bővítik ki a Log Analytics szolgáltatást. Ezek elsősorban felhőben futnak, és az OMS-tárházban összegyűjtött adatok elemzését végzik el. Ezen felül új, összegyűjtendő rekordtípusokat is meghatározhatnak, amelyek Naplókeresésekkel, illetve az OMS-irányítópulton lévő megoldás által biztosított további felhasználói felület segítségével elemezhetők.
A biztonsági és naplózási példája a következő típusú megoldások.



### <a name="automation--control-alert-on-security-configuration-drifts"></a>Automatizálási & vezérlő: biztonsági beállítások a riasztás drifts

Azure Automation szolgáltatásbeli rendszergazdai folyamatok, amelyek alapján a PowerShell és az Azure felhőben futtatható runbookok automatizálja. A runbookok futtathatók a helyi adatközpontban is, és kezelhetők velük a helyi erőforrások. Azure Automation szolgáltatásbeli konfiguráció kezelése a PowerShell DSC (célállapot-konfiguráció) biztosít.

![Azure Automation](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Hozzon létre és kezelheti az Azure-ban üzemeltetett DSC erőforrásokat, és alkalmazza azokat a felhőalapú és helyszíni rendszerek definiálása automatikusan kényszerítése a konfigurációt, és jelentések készítése a eltéréseket biztosítását, hogy a biztonsági beállításokkal továbbra is a házirend segítségével.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center segítségével, az Azure-erőforrások védelme. Biztosít integrált biztonsági monitorozást és az Azure-előfizetések. A szolgáltatáson belül meghatározhatják áll házirendek nem csak az Azure-előfizetések ellen is elleni [erőforráscsoportok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), így pontosabban is lehet.

![Azure Security Center](./media/azure-threat-detection/azure-threat-detection-fig8.png)

A Microsoft biztonsági kutatói folyamatosan figyelik a megjelenő fenyegetéseket. Rendelkezésükre áll a telemetriai adatok kiterjedt halmaza, amelyet a Microsoft a globális felhőbeli és helyszíni jelenléte során gyűjtött össze. Ezen adatkészletek széles körű és változatos gyűjteményére alapozva a Microsoft fel tudja fedezni az új támadási mintákat és trendeket a helyszíni fogyasztói és a vállalati termékeiben, valamint az online szolgáltatásaiban.

Ebből kifolyólag a Security Center képes gyorsan frissíti az észlelési algoritmusokat támadók kiadás új és egyre kifinomult biztonsági réseket. Ez a megközelítés segít a gyorsan változó fenyegetésekkel teli világában lépést tartani.

![Security Center](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

A Security Center fenyegetésészlelése úgy működik, hogy automatikusan összegyűjti a biztonsági információkat az Azure-erőforrásokból, a hálózatból és a csatlakoztatott partneri megoldásokból.  Ezen információk használatával történik a fenyegetések azonosítására, több forrásból származó adatokat megvizsgálja.
A Security Center prioritás szerinti sorolja be a biztonsági riasztásokat, és javaslatokat tesz a fenyegetés kiiktatására.

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. A big Data típusú adatok eredményeket és [gépi tanulás](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technológiákkal események kiértékelni között a teljes felhőháló – lehetetlen manuális módszer segítségével, és fejlődéséhez előrejelzésére szolgáló észlelése támadásokat. A biztonsági elemzés a következő lépéseket tartalmazza.

### <a name="threat-intelligence"></a>Fenyegetések felderítése

A Microsoft rendkívül nagy mennyiségű adattal rendelkezik a globális fenyegetésészlelési intelligencia keretein belül.
A telemetriai adatok több forrásból, például az Azure, Office 365, Microsoft CRM online, a Microsoft Dynamics AX, Outlook.com-os, MSN.com, a Microsoft Digital Crimes Unit (DCU) és a Microsoft biztonsági válasz Center (MSRC) zajló kommunikációról.

![Fenyegetések felderítése](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

A kutatók ezenkívül olyan adatokat is kapnak a fenyegetésészleléshez, amelyeket a fő felhőszolgáltatók osztanak meg, és előfizetnek a harmadik felek fenyegetésészlelési intelligenciával foglalkozó hírcsatornákra is. Az Azure Security Center mindezeket az adatokat fel tudja használni arra, hogy értesítse Önt az ismert kártékony elemektől eredő fenyegetésekről. Néhány példa:

-   **A teljesítmény a Machine Learning - kifejlesztése** az Azure Security Center segítségével észleli a veszélyeket, az Azure-környezetekhez célzó felhőalapú hálózati tevékenységre vonatkozó adatok hatalmas mennyiségű hozzáféréssel rendelkezik. Példa:

-   **Találgatásos kényszerített észlelések -** gépi tanulás hozhatók létre a távelérési kísérletek egy korábbi mintát, amely lehetővé teszi, hogy SSH, RDP és az SQL-portokhoz találgatásos támadások észleléséhez.

-   **Kimenő DDoS és Botnet észlelési** -egy közös célzó felhőalapú erőforrások támadások célja a számítási teljesítményt az alábbi források segítségével hajtható végre más támadásoknak.

-   **Új viselkedéssel összefüggő Analytics kiszolgálók és virtuális gépek -** után egy kiszolgáló vagy a virtuális gép biztonsága sérül, a támadók alkalmaz-e módszerekkel észlelési elkerülése, biztosítva az adatmegőrzési és így nem futtatható, hogy a rendszer rosszindulatú kód számos biztonsági vezérlők.

-   **Az Azure SQL adatbázis Fenyegetésészlelés -** Fenyegetésészlelés az Azure SQL Database, amely azonosítja az adatbázist érintő rendellenes tevékenységeket szokatlan és potenciálisan káros kísérlik meg elérni vagy adatbázisok kihasználására.

### <a name="behavioral-analytics"></a>Működés elemzése

A működés elemzése olyan módszer, amely megvizsgálja és összehasonlítja az adatokat az ismert minták gyűjteményével. Ezek a minták azonban nem csak egyszerű aláírások. Meghatározásuk hatalmas adatkészletekre alkalmazott összetett gépi tanulási algoritmusokkal történt.

![Működés elemzése](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)


Ezenkívül szakértő elemzők mélyrehatóan elemezték a kártékony működést a meghatározásukhoz. Az Azure Security Center a viselkedéselemzés segítségével azonosíthatja a sérült biztonságú erőforrások virtuális gép naplóinak, virtuális hálózati eszköznaplók, háló naplókat, összeomlási memóriaképek és egyéb forrásokból elemzése alapján.

Ezenkívül megvizsgálja az összefüggéseket más jelekkel, hogy alátámassza a nagy lépétkű kampányok bizonyítékait. Az összefüggések felderítése segít azonosítani a feltörés meghatározott tüneteit mutató eseményeket.

Néhány példa:
-   **Gyanús folyamatok végrehajtását:** támadók végrehajtani a kártevő szoftverek észlelése nélkül számos módszert alkalmaz. Például egy támadó előfordulhat, hogy adjon kártevő jogos rendszerfájlok megegyező nevekkel, de ezeket a fájlokat helyez egy másik helyre, jóindulatú fájl nagyon hasonlít a név vagy maszkolja a true fájlkiterjesztés. A Security Center modelljei feldolgozzák a működéseket, és megfigyelik a folyamat-végrehajtást az ezekhez hasonló kakukktojások felismeréséhez.

-   **Kártevők és kihasználásának kísérletek rejtett:** kifinomult kártevők is foganatosításával hagyományos kártevőirtó termékek soha nem szabad való írás vagy a szoftverösszetevők lemezen tárolt titkosított. Azonban ilyen kártevő észlelhető memória az elemzést követően használja, a kártevő szoftver kell hagyja a nyomkövetések memória függvénynek. Amikor a szoftver összeomlik, egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában. A memória a összeomlási memóriakép elemzésével, az Azure Security Center képes észlelni technikák segítségével szoftver biztonsági rései, bizalmas adatok eléréséhez, valamint rejtett megőrzéséhez a sérült biztonságú gépekről teljesítményére gyakorolt hatás nélkül a gép.

-   **Oldalirányú mozgás és a belső reconnaissance:** való megőrzésre a sérült biztonságú hálózati, és keresse meg vagy betakarítás értékes adatok, a támadók gyakran megkísérelhetik áthelyezése könnyebben a sérült biztonságú számítógépről mások ugyanazon a hálózaton belül. A Security Center figyeli folyamat és bejelentkezési tevékenységek bontsa ki a hálózatról, például távoli utasítás végrehajtása hálózati probing vagy fiók számbavételi egy támadó foothold megpróbálja felderíteni.

-   **PowerShell-parancsfájlok rosszindulatú:** PowerShell segítségével a támadók rosszindulatú kódot végrehajtani a cél virtuális gépek különböző célt. A Security Center megvizsgálja a PowerShell tevékenységeit, hogy megtalálja a gyanús tevékenységek nyomait.

-   **Kimenő támadások:** a támadók gyakran cél azzal a céllal, ezeket az erőforrásokat használatával további támadások csatlakoztatni a felhőben található erőforrásokat. Feltört virtuális gépek, például előfordulhat, hogy más virtuális gépek elleni találgatásos támadásokat indíthatnak, LEVÉLSZEMETET küld, vagy használható vizsgálati megnyitott portok és egyéb eszközök az interneten. A hálózati forgalomra irányított gépi tanulás alkalmazásával a Security Center észlelni tudja, ha a szokásosnál nagyobb mértékű a kimenő hálózati kommunikáció. Ha LEVÉLSZEMÉT, a Security Center is korrelációban szokatlan e-mail forgalom az Office 365 szolgáltatásból meghatározásához. a levelezési valószínűleg eszközintelligencia nefarious vagy egy szabályos e-mail kampány eredményét.

### <a name="anomaly-detection"></a>Anomáliadetektálás

Az Azure Security Center további módszere a fenyegetések felderítésére a rendellenességek észlelése. A működés elemzésével (amely a nagy adatkészletekből kinyert ismert mintákon alapul) ellentétben a rendellenességek észlelése „testre szabottabb”, és az üzemelő példányokhoz tartozó alapkonfigurációkra összpontosít. Ez a módszer gépi tanulás alkalmazásával felméri az üzemelő példányok normál tevékenységeit, majd szabályokat hoz létre az olyan rendkívüli körülmények meghatározásához, amelyek a biztonságot érintő eseményre utalhatnak. Például:

-   **Bejövő RDP/SSH találgatásos támadások:** a kialakítás esetében előfordulhat, sok bejelentkezések foglalt virtuális gépek minden nap és egyéb rendelkező virtuális gépek néhány vagy minden bejelentkezések. Az Azure Security Center határozza meg az alapkonfiguráció bejelentkezési tevékenységét a virtuális gépekhez, és gépi tanulási a szokásos bejelentkezési tevékenységek körül definiálásához használja. Ha az meg van adva bejelentkezési kapcsolódó jellemzőkkel rendelkezik, akkor előfordulhat, hogy riasztást együtt eltérés van. Ebben az esetben is gépi tanulás alapján határozza meg, hogy mi számít szignifikáns eltérésnek.

### <a name="continuous-threat-intelligence-monitoring"></a>Folyamatos Fenyegetésfelderítési adataival figyelése

Az Azure Security Center biztonsági kutatási és adatok tudományos csapat világszerte, amelyek folyamatosan figyelje a módosításokat a veszélyforrásainak tükrében megfigyelhető, működik. Ide tartoznak a következők:

-   **Fenyegetés eszközintelligencia figyelési:** fenyegetés eszközintelligencia mechanizmusok, a mutatók, a gyakorolt hatása és a meglévő vagy megjelenő fenyegetéseket végrehajthatóként útmutatást tartalmaz. Ez az információ elérhető a biztonsági közösség számára, és a Microsoft folyamatosan figyeli a fenyegetésekre vonatkozó intelligencia belső és külső forrásból származó hírcsatornáit.

-   **Jel megosztása:** információkat kaphat a felhőalapú és helyszíni szolgáltatásokat, a kiszolgálók és az ügyfél végponti eszközök széles körű kaphat a Microsoft biztonsági csoportjai megosztott és elemzése.

-   **Microsoft adatbiztonsági szakemberek:** folyamatban lévő engagement az karbantartó csoportok között Microsoft speciális biztonsági mezők, például a törvényszéki használhatók, és a webalkalmazás-támadás észlelése.

-   **Észlelési hangolása:** algoritmusok elleni valós felhasználói adatkészletek futnak, és biztonsági kutatói együttműködve ügyfelek érvényesíteni az eredményeket. Az igazi és a téves találatok megjelölésével pontosítják a gépi algoritmusokat.

Ezek az egyesített erőfeszítések új és hatékonyabb észleléseket eredményeznek, amelyeknek azonnal hasznát veheti anélkül, hogy bármit is tennie kellene.

## <a name="advanced-threat-detection-features---other-azure-services"></a>Az Advanced Threat szolgáltatásai – más Azure-szolgáltatásokkal

### <a name="virtual-machine-microsoft-antimalware"></a>Virtuális gép: Microsoft Antimalware

[A Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) Azure single-ügynök megoldás az alkalmazások és a bérlői környezetben van, úgy tervezték, hogy fut a háttérben, emberi beavatkozás nélkül. Telepítheti a védelmi alapú az alkalmazások és szolgáltatások, vagy alapvető biztonságos--alapértelmezés szerint a rendszer igényeinek megfelelően, vagy speciális egyéni konfiguráció – beleértve a kártevőirtó-figyelés. Azure kártevőirtó az Azure virtuális gépek biztonsági megoldás, és automatikusan települ az összes Azure PaaS virtuális gépet.

**Az Azure telepítéséhez és az alkalmazások engedélyezése a Microsoft Antimalware szolgáltatások**

#### <a name="microsoft-antimalware-core-features"></a>A Microsoft Antimalware alapszolgáltatások

-   **A valós idejű védelem -** figyeli a Felhőszolgáltatások és virtuális gépeken történő észleli és blokkolja a kártevő szoftverek végrehajtási tevékenység.

-   **Ütemezett ellenőrzés -** rendszeres időközönként a kártevő szoftverek, beleértve, aktívan futó programok észlelését célzott vizsgálatát.

-   **Kártevő szoftver eltávolításának -** automatikusan hajt végre műveletet a kártevő, például törlése vagy a karanténba helyezés kártevő fájlok és a rosszindulatú beállításjegyzék-bejegyzések törlése.

-   **Aláírás frissítések -** automatikusan telepíti a legújabb definíciók (vírus-definíciókat) egy előre meghatározott gyakoriságáról naprakész védelem biztosítása érdekében.

-   **Kártevőirtó motor frissítéseit -** automatikusan frissíti a Microsoft Antimalware-motort.

-   **Kártevőirtó Platform frissítések –** automatikusan frissíti a Microsoft Antimalware platform.

-   **Aktív védelem -** telemetriai metaadatok fenyegetésről és gyanús erőforrásokról-jelentést a Microsoft Azure gyors válasz a fenyegetésekről alkotott képet fejlődnek, és engedélyezi a valós idejű szinkron aláírás kézbesítési keresztül biztosításához a Microsoft Active Protection rendszer (MAPS).

-   **Jelentéskészítés - minták** biztosít, és jelenti a mintákat a Microsoft Antimalware szolgáltatás finomíthatja a szolgáltatást, és engedélyezze a hibaelhárítás érdekében.

-   **Kizárások –** lehetővé teszi az alkalmazás és szolgáltatás-rendszergazdák konfigurálása bizonyos fájlokat, a folyamatokat, és fokozza a kizárása védelmi és a teljesítmény-és/vagy más meggondolásból vizsgálatát.

-   **Eseménygyűjtés kártevőirtó -** a kártevőirtó szolgáltatás állapota, a gyanús tevékenységek és az elvégzett szervizelési műveleteket hajtja végre operációs rendszer-eseménynaplójában rögzíti és gyűjti azokat az ügyfél Azure Storage figyelembe.

### <a name="azure-sql-database-threat-detection"></a>Az Azure SQL adatbázis fenyegetések észlelése

[Az Azure SQL adatbázis Fenyegetésészlelés](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) egy új biztonsági az eszközintelligencia szolgáltatás az Azure SQL Database szolgáltatás beépítve. Éjjel dolgozik, ismerje meg, a profil és a adatbázist érintő rendellenes tevékenységeket észleli, Azure SQL adatbázis Fenyegetésészlelés azonosítja az adatbázishoz a potenciális fenyegetések ellen.

Biztonsági tisztviselő vagy más kijelölt rendszergazdák kérheti le az azonnali értesítések adatbázis gyanús tevékenységekről azok bekövetkezésekor. Minden értesítés részletesen bemutatja a gyanús tevékenység, illetve további vizsgálata, és a fenyegetések mérséklésére javasolja.

Azure SQL adatbázis Fenyegetésészlelés jelenleg észleli a potenciális biztonsági réseket és az SQL injektálási támadások és a rendellenes adatbázis hozzáférési minták.

Fenyegetések észlelése e-mail értesítés fogadását követően a felhasználók keresse meg, és megtekintheti a megfelelő naplórekordok az áruházra mutató mélyhivatkozás használ egy naplózási viewer megjelenő üzenet és/vagy előre beállított Excel-sablon, amely körül a megfelelő auditálási rekordok naplózása az alábbiak szerint gyanús esemény időpontja:
-   Az adatbázis-kiszolgáló az adatbázist érintő rendellenes tevékenységeket a naplózási tároló

-   Az esemény időpontjában a napló írása használttal vonatkozó naplózási tároló tábla

-   Az esemény akkor következik be, mert a következő órás rekordok naplózása

-   Naplózási rekordokat hasonló eseményazonosító: az esemény (néhány érzékelők esetén nem kötelező)

SQL adatbázis fenyegetés érzékelők használja a következő észlelési módszerek egyikét:

-   **Determinisztikus észlelést –** gyanús mintázatok (alapuló szabályok) észleli az SQL-ügyfél lekérdezések, amelyek megfelelnek az ismert támadásokat. Ez a módszer van magas felderítését és alacsony vakriasztás, azonban csak korlátozott érvényességének, mert "atomi észlelések" kategóriába tartozik.

-   **Viselkedési észlelés –** rendellenes tevékenységet, amely az adatbázishoz, amely nem fordult elő az elmúlt 30 napban rendellenes viselkedés hibák.  Az SQL client rendellenes tevékenységet például lehet egy csúcs sikertelen bejelentkezések/lekérdezések, nagy mennyiségű adatok kibontása közben, szokatlan kanonikus lekérdezések és az adatbázis eléréséhez használt ismeretlen IP-címek

### <a name="application-gateway-web-application-firewall"></a>Alkalmazás átjáró webalkalmazási tűzfal

[Webalkalmazási tűzfal](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) szolgáltatása [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) webes alkalmazásokhoz, a standard Alkalmazásátjáró használó védelmet biztosít, amely [Alkalmazásvezérlés kézbesítési](https://kemptechnologies.com/in/application-delivery-controllers)funkciók. Webalkalmazási tűzfal ennek érdekében a legtöbb megakadályozásával a [OWASP felső 10 közös webes biztonsági rések](https://www.owasp.org/index.php/Top_10_2010-Main)

![Alkalmazás átjáró webalkalmazás Firewally](./media/azure-threat-detection/azure-threat-detection-fig13.png)

-   SQL-injektálás elleni védelem

-   Webhelyek közötti, parancsprogramot alkalmazó támadások elleni védelem

-   Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem

-   HTTP protokoll megsértése elleni védelem

-   HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem

-   Robotprogramok, webbejárók és képolvasók elleni védelem

-   A gyakori alkalmazás konfigurációs hibák (Ez azt jelenti, hogy Apache, az IIS, stb.) észlelése

A következő juttatás WAF konfigurál Alkalmazásátjáró itt meg:

-   A háttérkód módosítása nélkül védheti a webalkalmazásokat a webes biztonsági résektől és támadásoktól.

-   Egyszerre több webalkalmazást védhet egy Application Gateway mögött. Az Application Gateway akár 20 webhely üzemeltetését is támogatja egyetlen átjáró mögött, amelyek mind védhetők a webes támadásokkal szemben.

-   Az Application Gateway WAF-naplók által létrehozott valós idejű jelentés segítségével figyelheti a webalkalmazást a támadások tekintetében.

-   Egyes megfelelőség-ellenőrzési funkciókhoz szükséges, hogy az összes internetkapcsolattal rendelkező végpontot WAF-megoldás védje. Az engedélyezett webalkalmazási tűzfallal rendelkező Application Gateway használatával teljesítheti ezeket a megfelelőségi követelményeket.

### <a name="anomaly-detection--an-api-built-with-azure-machine-learning"></a>Anomáliadetektálás – az Azure Machine Learning-val készült API-k

Anomáliadetektálás az API-k, amelyek akkor hasznos, ha különböző típusú rendellenes minták észlelésére az az idő adatsorok Azure Machine Learning-val készült. Az API-t egy anomáliadetektálási pontszám rendel az idősorozat, amely használható a riasztások generálásához, az egyes adatpontokban irányítópultjaink figyelése, vagy a hibajegyalapú rendszert való csatlakoztatásra.

A [Anomáliadetektálási észlelési API](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) rendellenességeket idő adatsor a következő típusú képes észlelni:

-   **Napra és esik:** például figyelésekor szolgáltatás sikertelen bejelentkezések száma vagy egy elektronikus kereskedelmi webhely, a kivételek száma szokatlan igényeiben jelentkező vagy immerzióban nem jelzi a biztonsági támadások vagy szolgáltatás üzemzavarokhoz vezethet.

-   **Pozitív és negatív trendek:** memóriahasználat számítástechnikai figyelésekor például zsugorítását a szabad memória mérete, a potenciális memóriavesztés; szolgáltatás várólista hossza figyelésekor egy állandó növekvő tendenciát jelezheti alapul szolgáló szoftver probléma.

-   **Módosítások és a dinamikus értéktartományhoz változásainak szinten:** például szintje változik a szolgáltatási késéseket után a szolgáltatás frissítése vagy az alacsonyabb szintű kivételek után lehet érdekes figyelése.

A machine learning-alapú API:

-   **Rugalmas és robusztus észlelési:** az anomáliadetektálási észlelési modellek engedélyezése a felhasználók számára határozza és nem határozza adatkészlet között rendellenességek észlelését és érzékenységi beállításainak konfigurálása. A felhasználók módosíthatják az anomáliadetektálási modell API észlelését az igényeik szerint több vagy kevesebb érzékeny végrehajtásához. Ez azt jelentené, hogy kevesebb vagy több látható rendellenességeket adatok rendelkező és anélküli határozza minták észlelésére.

-   **Méretezhető és időben történő észlelése:** e küszöbértékek szakértői tartomány Tudásbázis állította a figyelést a hagyományos módon költséges és nem méretezhető, több millió dinamikusan módosítja az adatkészletet. Ez az API-ban az anomáliadetektálási észlelési modellek megjegyzett és modellek automatikusan hangolt előzmény- és a valós idejű adatokból.

-   **Proaktív és végrehajthatóként észlelési:** lassú trend és szint módosítása észlelési közüli korai alkalmazhatók. A korai rendellenes jelek észlelése vizsgálatához és a problémás területek intézkedjen emberek közvetlen használható.  Ezenkívül alapvető oka elemzési modellek és riasztási eszközök lehetnek a anomáliadetektálás API szolgáltatás felett.

Az anomáliadetektálás API egy eredményesebbé és hatékonyabbá teszi megoldás, mint szolgáltatás állapotát & figyelési, IoT, a teljesítmény figyelése és a hálózati forgalom figyelése KPI forgatókönyvek széles köre. Az alábbiakban néhány népszerű forgatókönyv, ahol ez az API lehet hasznos:
- Az informatikai részlegeknek képesnek kell nyomon követheti az eseményeket, a hibakód, a napló és a teljesítmény (a Processzor, memória- és így tovább) eszközök időben.

-   Online kereskedelmi helyeken szeretné nyomon követheti a felhasználói tevékenységek, Lapmegtekintések, kattint, és így tovább.

-   Segédprogram vállalatok szeretné nyomon követni a vízjel, gáz, elektromosság kialakulása és egyéb erőforrások fogyasztásának.

-   Létesítmény/épület szolgáltatások kerüljenek hőmérséklet, nedvesség, forgalom és így tovább.

-   IoT/gyártók kívánt érzékelőadatait idősor figyelő munkafolyamat, minőségét és így tovább.

-   Szolgáltatók, például a telefonos ügyfélszolgálatok kell szolgáltatás igény szerinti trend, incidens figyelése, várja meg a várólista hossza és így tovább.

-   Üzleti elemzés csoportok üzleti KPI-k (például az értékesítési kötet ügyfél hangulati elemek díjszabás) figyelni kívánja a rendellenes adatátviteli valós időben.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) a Microsoft Cloud biztonsági verem kritikus összetevője. A szervezet segítségére lehetnek a felhőalapú alkalmazások ígéret teljes körű kihasználása, de a vezérlő keresztül történő tevékenység jobb átláthatóságával nyomon mozgatásakor átfogó megoldás is. Emellett segít felhőalkalmazások kritikus adatok védelmének növelése.

Eszközöket, amelyek segítenek az árnyékinformatika informatikai kockázatfelmérés, a házirendek kényszerítését, tevékenységek vizsgálata, és veszélyek megakadályozása, amely a szervezet biztonságosabb áthelyezheti a felhőbe a kritikus adatok felügyelete megőrzésével.

<table style="width:100%">
 <tr>
   <td>Ismertetők</td>
   <td>A Cloud App Security rendelkező informatikai árnyékinformatika. Hogy lássák felderíti az alkalmazások, tevékenységekhez, felhasználók, adatokat és fájlokat a felhőalapú környezetben. A felhőhöz csatlakoztatott külső alkalmazások felderítésére.</td>
 </tr>
 <tr>
   <td>Vizsgálat</td>
   <td>Vizsgálja meg a felhőalkalmazások eszközökkel felhő Törvényszéki részletesen a kockázatos alkalmazásokat, egyes felhasználók és a hálózati fájlokat. Keresse meg a mintákat a felhőből gyűjtött adatok. A felhőt figyelő jelentéseket készíthetnek.</td>

 </tr>
 <tr>
   <td>Vezérlés</td>
   <td>A kockázatnak a mérséklése úgy, hogy házirendek és riasztások segítségével teljes mértékben hálózati felhőforgalom felett. Biztonságos, engedélyezett felhőalkalmazás-alternatívákra telepítheti át a felhasználóit a Cloud App Security.</td>

 </tr>
 <tr>
   <td>Védelem</td>
   <td>A Cloud App Security segítségével engedélyezése vagy alkalmazások, adatveszteség-megelőzési, hozzáféréssel és megosztása, és egyéni jelentések és értesítések.</td>

 </tr>
 <tr>
   <td>Vezérlés</td>
   <td>A kockázatnak a mérséklése úgy, hogy házirendek és riasztások segítségével teljes mértékben hálózati felhőforgalom felett. Biztonságos, engedélyezett felhőalkalmazás-alternatívákra telepítheti át a felhasználóit a Cloud App Security.</td>

 </tr>
</table>


![Cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

A cloud App Security integrálható a felhő által látható

-   A Cloud Discoveryvel – leképezése és azonosítása a felhőalapú környezet és a felhőalapú alkalmazásokat szervezete használ.


-   Engedélyezése és tiltása a felhőben lévő alkalmazásokhoz.



-   Előnyeit-szolgáltató API-k, láthatósága és irányítása a csatlakoztatott alkalmazások könnyű telepítése alkalmazás-összekötők használatával.

-   Gondoskodik a folyamatos ellenőrzés-beállítást, és majd folyamatosan pontosabb beállításra, házirendekkel rendelkeznek.

A gyűjtött adatokat e forrásokból, a Cloud App Security kifinomult elemzést futtat az adatokon. Ez azonnal figyelmeztet a rendellenes tevékenységek, és betekintést nyújt a részletes a felhőalapú környezetben. A Cloud App Security házirend konfigurálása és a segítségével megvédheti a felhőalapú környezetben.

## <a name="third-party-atd-capabilities-through-azure-marketplace"></a>Az Azure piactér külső ATD képességeit

### <a name="web-application-firewall"></a>Web Application Firewall (Webalkalmazási tűzfal)

Webalkalmazási tűzfal megvizsgálja a bejövő webes forgalmat és blokkolja SQL-utasítások, többhelyes parancsfájlok, kártevő szoftverek feltöltések & DDoS alkalmazás és más a webalkalmazások irányuló támadások. Azt is ellenőrzi a válaszokat a háttérben futó webes-kiszolgálókról az adatok adatvesztés-megelőzési (DLP). A integrált access control motor lehetővé teszi a rendszergazdák részletes hozzáférés-vezérlési házirendeket hitelesítési, engedélyezési és nyilvántartási (AAA), amely lehetőséget nyújt a szervezetek erős hitelesítés és a felhasználói vezérlő létrehozásához.

**Emeli ki:**
-   Észleli és blokkolja a SQL alkalommal, idegen, kártevő szoftverek feltöltések, alkalmazás DDoS vagy bármely egyéb támadások elleni az alkalmazás.

-   Hitelesítési és hozzáférés-vezérlés.

-   Ellenőrzi a kimenő forgalmat érzékeny adatok észlelése és maszk vagy blokkolni lehessen szivárogjanak az adatokat.

-   A webes alkalmazás tartalmát, például a gyorsítótárazás, tömörítés és egyéb forgalom optimalizálása funkciókkal kézbesítését növekszik.

Az alábbiakban az Azure piactéren elérhető webalkalmazás tűzfalak példát:

[Webalkalmazási tűzfal barracuda, Brocade virtuális webalkalmazási tűzfal (Brocade vWAF), Imperva SecureSphere & ThreatSTOP IP a tűzfalon.](https://azure.microsoft.com/marketplace/partners/brocade_communications/brocade-virtual-web-application-firewall-templatevtmcluster/)

## <a name="next-steps"></a>További lépések

- [Az Azure Security Center észlelési képességei](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)

Az Azure Security Center speciális észlelési képességek segít azonosítani a Microsoft Azure-erőforrások célzó aktív fenyegetéseket, és biztosít, amelyen a, gyorsan képesek reagálni szükséges.

- [Az Azure SQL adatbázis fenyegetések észlelése](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)

Az Azure SQL adatbázis Fenyegetésészlelés segített a potenciális fenyegetések, az adatbázis kétségei cím.
