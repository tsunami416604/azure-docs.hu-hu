---
title: "Dokumentálja a személyes adatok védelme az Azure-ral Jelentéskészítő eszközök |} Microsoft Docs"
description: "hogyan használható az Azure jelentéskészítési szolgáltatásaival és technológiáival személyes adatok védelme érdekében."
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.openlocfilehash: 0ec9ceb63c3e1872e9815a7895b624276fc46123
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="document-protection-of-personal-data-with-azure-reporting-tools"></a>Dokumentálja a személyes adatok védelme az Azure-ral Jelentéskészítő eszközök

Ez a cikk bemutatja, hogyan lehet Azure jelentési szolgáltatások és technológiák használata a személyes adatok védelme érdekében.

## <a name="scenario"></a>Forgatókönyv

Egy nagy körutazás cég, az Amerikai Egyesült Államokban telephelyének bővíti a műveleteket a mediterrán, Adriai, és Balti tengerek, valamint a Brit-szigetekre útvonalak biztosítani. Ezek az erőfeszítések érdekében több kisebb körutazás sorok Olaszország, Németországban, Dánia és az Egyesült szerzett

A vállalat a Microsoft Azure feldolgozási és a vállalati adatok tárolására használ. Ez magában foglalja a személyes azonosításra alkalmas adatokat, például neveket, címeket, telefonszámokat, és a globális felhasználói bázis hitelkártya adatait. Az összes hely például a címet, telefonszámot, azonosító számokat és egyéb információkat a vállalat alkalmazottai hagyományos emberi erőforrások adatokat is tartalmaz. A körutazás sor is fenntartja, nagy adatbázis ellenszolgáltatás és hűség program tagok nyomon követéséhez a kapcsolatokat az aktuális és korábbi ügyfelek személyes információkat is tartalmaz.

Vállalati alkalmazottak hozzáférjenek a hálózathoz, a távoli iroda a vállalat, és a világ utazás ügynökök rendelkezésre elérni egyes vállalati erőforrásokat.

## <a name="problem-statement"></a>Probléma leírása

A vállalat kell védelmében az ügyfelek és az alkalmazottak és a személyes adatok keresztül egy többrétegű biztonsági stratégia Azure felügyeleti és biztonsági használó szolgáltatások elérésére és a személyes adatok szigorú vezérlőit bevezetése, és képesnek kell lennie bemutatják a védelmi intézkedések a belső és külső auditorok.

## <a name="company-goal"></a>Vállalati cél

A védelmi jellegű biztonsági stratégia részeként is a vállalat célja, hogy a hozzáférést, és a személyes adatok nyomon követése, és gondoskodjon arról, hogy a megfelelő adatvédelmi személyes adatok védelmét dokumentációban létezik és működik.

## <a name="solutions"></a>Megoldások

A Microsoft Azure nyomon követéséhez átfogó figyelést, naplózási és diagnosztikai eszközök rekord tevékenységeknek és eseményeknek társított és elérése és személyes adatok, földrajzi folyamata, és a személyes adatok harmadik fél elérésének feldolgozása. A felhőben személyes adatok biztonsága érdekében feladata egy megosztott, mert a Microsoft rendelkező ügyfelek emellett biztosítja:

- Részletes információkat a saját feldolgozási ügyfelek adatok

- A Microsoft által felügyelt biztonsági intézkedések

- Hol és hogyan ügyfelek adatokat küld

- Microsoft saját adatvédelmi értékelést folyamat részletes adatait

### <a name="azure-active-directory"></a>Azure Active Directory

[Az Azure Active Directory](https://azure.microsoft.com/services/active-directory/) a Microsoft felhőalapú, több-bérlős directory és az identity management szolgáltatás. A szolgáltatás bejelentkezési és a jelentéskészítési képességek naplózási biztosít részletes be- és alkalmazás használati kapcsolatos információk segítséget nyújtanak a figyelheti, és ellenőrizze a megfelelő hozzáféréssel az ügyfelek és az alkalmazottak a személyes adatok.

A tevékenység jelentések két típusa van:

- A [tevékenység jelentések/naplók](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) adja meg a rendszer tevékenységek/feladatok részletes rekord

- A [bejelentkezések jelentés/tevékenységnapló](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) bemutatja, akik hajtott végre az ellenőrzési jelentésben felsorolt minden tevékenység

A kettő együtt használja, minden feladat és végző felhasználók listáját, minden egyes előzményadatait követheti nyomon. Mindkét típusú jelentésekre testre szabhatók, és szűrhetők.

#### <a name="how-do-i-access-the-audit-and-security-logs"></a>Hogyan érhető el a naplózási és biztonsági naplókat?

A naplózási és biztonsági naplókat érhetők el az Active Directory portálon három különböző módon: keresztül a **tevékenység** szakasz (válassza **naplók** vagy **bejelentkezések**), vagy a **felhasználók és csoportok** vagy **vállalati alkalmazások** alatt **kezelése** az Active Directoryban. Jelentés is elérhető az Azure Active Directory reporting API használatával. 

1. Válassza ki az Azure-portálon **Azure Active Directoryban.**

2. Az a **tevékenység** szakaszban jelölje be **a naplók.**

    ![](media/protection-personal-data-azure-reporting-tools/image001.png)

3. A lista nézet testreszabásához kattintva **oszlopok** az eszköztáron.

4.  Jelöljön ki egy elemet a listanézetben kapcsolatos az összes rendelkezésre álló részletek megtekintéséhez.

    ![](media/protection-personal-data-azure-reporting-tools/image003.png)

Az Azure Active Directory jelentéskészítési is magában foglalja a kétféle típusú biztonsági jelentések **felhasználók meg van jelölve, a kockázat** és **kockázatos bejelentkezések**, amely is segítenek az esetleges kockázatokat az Azure környezetben.

A jelentési szolgáltatás kapcsolatos további információkért lásd: [Azure Active Directory reportingban](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)

Látogasson el [Azure Active Directory Tevékenységjelentések](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal#activity-reports) a érhető el az Azure Active Directoryban jelentésekkel kapcsolatos további részletekért. A webhely eléréséről és használatáról további részleteit tartalmazza [auditnaplókat Tevékenységjelentések](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) és [bejelentkezési Tevékenységjelentések](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) a portálon. Emellett tájékoztatást kapcsolatos [felhasználók meg van jelölve, a kockázat](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-user-at-risk) és [kockázatos bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins) jelentéseket.

Látogasson el a [Azure Active Directory naplózási API-referencia](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference) hely és az Azure Directory reporting programozott módon módjáról további információt.

### <a name="log-analytics"></a>Log Analytics

[Naplófájl Analytics](https://azure.microsoft.com/services/log-analytics/) is [adatokat gyűjteni Azure figyelő](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-storage) egyeztetéséhez, más adatokkal, és adja meg a további elemzés. Azure figyelő összegyűjti és elemzi az Azure környezetben vonatkozó figyelési adatokat. 

Például a napló keresések, nézetek és megoldásokat a Naplóelemzési elemzésére szolgáló eszközöket gyűjtött adatokat, így a teljes környezet központi elemzés dolgozhat. A Naplóelemzési összesítő és a Windows-eseménynaplók, az IIS-napló és a rendszerbejegyzések, így könnyen észleli a potenciális személyes adatszivárgáshoz teheti ki a személyes adatok illetéktelen felhasználókkal.

#### <a name="how-do-i-use-log-analytics"></a>Log Analytics használata?

A Naplóelemzési az OMS-portálon vagy az Azure portálon, bármely webböngészőben keresztül érheti el. A Log Analyticsben található lekérdezési nyelv segítségével adatokat olvashat be és vonhat össze a tárházban. Hozzon létre, és közvetlenül a portálon adatelemzéshez napló keresések mentése.

A Naplóelemzési munkaterület létrehozása az Azure portálon, tegye a következőket:

1. Válassza ki **Naplóelemzési** a piactéren szolgáltatások közül.

2. Válassza ki **létrehozása,** majd adja meg az OMS-munkaterület nevét, válassza ki az előfizetés, az erőforráscsoport, a hely és IP-címek.

3. Kattintson a **OK** a munkaterületek listájának megjelenítéséhez.

4. Válassza ki a munkaterületen, a részletek megtekintéséhez.

    ![](media/protection-personal-data-azure-reporting-tools/image004.png)

Látogasson el a [Log Analytics-dokumentáció](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) további információt a szolgáltatást.

Látogasson el a [Ismerkedjen meg a Naplóelemzési munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started) hozzon létre egy értékelési munkaterületet, és a szolgáltatás használata alapjainak az oktatóanyagot.

Keresse fel a pontosabb információt az alábbi weblapok Naplóelemzési használata a fent leírt naplók való csatlakozáshoz:

[A Windows Eseménynapló adatforrások Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)

[A Naplóelemzési naplózza az IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)

[A Naplóelemzési Syslog adatforrások](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-syslog)

### <a name="azure-monitorazure-activity-log"></a>Az Azure figyelő/Azure tevékenységnapló 

[Az Azure figyelő](https://azure.microsoft.com/services/monitor/) alapszintű infrastruktúra metrikák és a naplók biztosít a legtöbb szolgáltatás a Microsoft Azure-ban.
Figyelési kapcsolódva információt kaphat a részletes információk az Azure-alkalmazások segítségével. Azure figyelése az Azure diagnostics bővítmény (Windows vagy Linux) a legtöbb alkalmazás szintű metrikák és naplók összegyűjtésére támaszkodik. [Az Azure tevékenységnapló](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) Azure megfigyelővel megtekintheti az erőforrások egyike. Minden API-hívás nyomon követi, és számos olyan az előforduló tevékenységek információt biztosít [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
Az erőforrás által az Azure-infrastruktúra kapcsolatos kereshet a tevékenységnapló (korábbi nevén az operatív vagy a vizsgálati naplók) kapcsolatos információt. 

Bár a információkat tárolja, amely a műveletnapló vonatkozik, teljesítmény és a szolgáltatás állapotát, is van az adatok védelmére vonatkozó információt. A tevékenység-naplót használó, meghatározhatja a "mi, ki, és mikor" az esetleges írási műveleteket (PUT, POST, Törlés) végezhet a felügyelt erőforrások közé az Azure-előfizetéshez.

Például biztosít egy olyan rekordot rendszergazda törli a hálózati biztonsági csoport, amely jelentős hatással lehet a személyes adatok védelmét. Tevékenység naplóbejegyzések Azure figyelő 90 napig tárolja.

#### <a name="how-do-i-use-the-data-collected-by-azure-monitor"></a>Miként használható az Azure-figyelő által gyűjtött adatok?

Számos módon az adatokat a műveletnapló és más Azure-figyelő erőforrások használatára.

- Az adatok valós sorban a más helyre is adatfolyam.

- Az adatok tárolhatók hosszabb időtartamokat, mint az alapértelmezett beállításokat, a használatával egy [Azure storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-introduction) és egy adatmegőrzési beállítást.

- Használatával az adatok grafikus és diagramokat, a visual a [Azure-portálon](https://azure.microsoft.com/features/azure-portal/), [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), [Microsoft PowerBI](https://powerbi.microsoft.com/), vagy külső képi megjelenítés eszközök.

- Lekérheti az adatok Azure figyelő REST API használatával, parancssori felület parancsait [PowerShell](https://docs.microsoft.com/powershell/) parancsmagok vagy a .NET SDK-val.

Ismerkedés az Azure-figyelő, jelölje be **több szolgáltatások** az Azure portálon.

1. Görgessen le a **figyelő** a a **figyeléséhez és felügyeletéhez** szakasz.

    ![](media/protection-personal-data-azure-reporting-tools/image005.png)

2.  Megnyitja a figyelő a **tevékenységnapló** nézet.

    ![](media/protection-personal-data-azure-reporting-tools/image007.png)

Létrehozhatja és mentheti a gyakran alkalmazott szűrőket tartalmazó lekérdezéseket, majd a legfontosabb lekérdezéseket rögzítheti a portál irányítópultján, így mindig tudni fogja, hogy előfordultak-e olyan események, amelyek megfelelnek a megadott feltételeknek.

1. A szűrése erőforráscsoport, timespan és eseménykategória.

    ![](media/protection-personal-data-azure-reporting-tools/image008.png)

2. A portál Irányítópultjára lekérdezések kattintva majd rögzíthető a **PIN-kód** gombra. Ez segít a működési adatok egyetlen forrásként a szolgáltatásoknak. A lekérdezés neve és a találatok száma az irányítópulton megjelenik.

A figyelő segítségével is megtekintheti az összes Azure-erőforrások metrikáját, diagnosztika beállításokat és a riasztások és keresése. Az Azure-figyelő és tevékenységnapló használatáról további információk: [Ismerkedés az Azure-figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

### <a name="azure-diagnostics"></a>Azure Diagnostics 

Az Azure-ban a diagnosztika funkció lehetővé teszi, hogy több forrásból származó adatok gyűjtésére. Lehet, hogy a Windows eseménynaplók, többek között a biztonsági napló, különösen akkor hasznos, nyomon követése és dokumentálásához személyes adatok védelmét. A biztonsági napló nyomon követi a bejelentkezés sikeres és sikertelen események, valamint engedélyek módosítását, bizonyos típusú támadások, biztonsági házirendek módosításai, biztonsági csoporttagsági változások és még sok más jelző mintázatok észlelését.

Például Event ID 4695 riasztást küld a megkísérelt unprotection naplózható védett adatokról. Ez vonatkozik az az adatok adatvédelmi API (DPAPI), amely segít megvédeni az adatok, például a titkos kulcsok, tárolt hitelesítő adatok és más bizalmas adatokat.

#### <a name="how-do-i-enable-the-diagnostics-extension-for-windows-vms"></a>Hogyan engedélyezhető a diagnosztika bővítmény Windows virtuális gépek?

PowerShell segítségével engedélyezi a diagnosztika bővítményt, a Windows virtuális gépek, hogy napló adatainak gyűjtéséről. Ennek lépéseit az határozza meg a központi telepítési modellt használ (erőforrás-kezelő vagy klasszikus). Ahhoz, hogy a diagnosztika bővítmény egy meglévő virtuális gépen keresztül a Resource Manager üzembe helyezési modellel létrehozott, használhatja a [Set-AzureRMVMDiagnosticsExtension PowerShell-parancsmag](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension?view=azurermps-4.3.1).

   ![](media/protection-personal-data-azure-reporting-tools/image009.png)

*\$diagnosticsconfig_path* a diagnosztika konfigurációs XML-Fájlban lévő tartalmazó fájl elérési útja. A virtuális gép Azure Diagnostics engedélyezésével kapcsolatos további: [Windows rendszerű virtuális gépként Azure Diagnostics engedélyezéséhez használja a Powershellt.](https://docs.microsoft.com/azure/virtual-machines/windows/ps-extensions-diagnostics)

Az Azure diagnostics-bővítmény a gyűjtött adatok átviteléhez az Azure storage-fiók, vagy elküldi a szolgáltatásokat, például az Application Insights. Használhatja az adatok naplózását.

#### <a name="how-do-i-store-and-view-diagnostic-data"></a>Hogyan tárolja és diagnosztikai adatainak megtekintéséhez?

Fontos megjegyezni, hogy diagnosztikai adatok nem véglegesen tárolja kivéve, ha azt át a Microsoft Azure storage emulator vagy az Azure storage. Tárolásához és diagnosztikai adatokat az Azure Storage megtekintéséhez kövesse az alábbi lépéseket:

1. Adjon meg egy tárfiókot a ServiceConfiguration.cscfg fájlban. Az Azure Diagnostics használhatja a Blob szolgáltatás vagy a Table szolgáltatás adatok típusától függően. Windows-eseménynaplók tábla formában tároljuk.

2. Az adatok átvitelét. A diagnosztikai adatok átviteléhez a konfigurációs fájl használatával kérhet. Az SDK 2.4 és az előző programozott módon is tehet a kérelmet.

3. Az adatok megjelenítése, használatával [Azure Tártallózó](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer), [Server Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) a Visual Studio vagy [Azure Diagnostics Manager](https://www.cerebrata.com/products/azure-diagnostics-manager) az Azure Management Studio.

A lépések végrehajtásával kapcsolatos további információkért lásd: [tároló és a nézet diagnosztikai adatokat az Azure Storage.](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)

### <a name="azure-storage-analytics"></a>Azure Storage Analytics 

Storage Analytics egy társzolgáltatás sikeres és sikertelen kérelmekkel kapcsolatos részletes információkat naplózza. Ezeket az információkat egyes kérelmeket a, amelyek segítik a dokumentálásáért a szolgáltatás a személyes adataihoz való hozzáférés figyelésére használható. Azonban tárolási analitika naplózás nem alapértelmezés szerint engedélyezve van a tárfiók. Az Azure portálon engedélyezheti azt.

#### <a name="how-do-i-configure-monitoring-for-a-storage-account"></a>Hogyan konfigurálhatók a tárfiók figyelését?

A storage-fiók figyelésének konfigurálása, tegye a következőket:

1. Válassza ki **tárfiókok** az Azure portálon, majd válassza ki a figyelni kívánt fiók nevét.

    ![](media/protection-personal-data-azure-reporting-tools/image011.png)

2. Az a **figyelés** szakaszban jelölje be **diagnosztika.**

3.  Válassza ki a **típus** metrikák adatok kívánja figyelni az egyes szolgáltatásokhoz (Blob, Table, fájl). Arra utasítani az Azure Storage olvasási, írási és törlési kérelmek a blob, table és queue szolgáltatások diagnosztikai naplók menteni, válassza ki a **naplók Blob, Table naplók** és **naplók várólistára.**

    ![](media/protection-personal-data-azure-reporting-tools/image013.png)

4. A csúszka a lap alján, állítsa be a **megőrzési** házirend napban (1 – 365 érték). Hét nap az alapértelmezett beállítás.

5. Válassza ki **mentése** a konfigurációs beállításokat alkalmazni szeretné.

Tárolási naplózási naplóbejegyzések egyes kérelmeket a következő információkat tartalmazza:

- Például a kezdési idő, a végpontok közötti késés és a kiszolgáló késleltetés információk.

- A tárolási művelet, például a művelet részleteit írja be, a tárolási objektum az ügyfél a kulcs eléréséhez, sikeres vagy sikertelen volt, és a HTTP-állapotkódot adott vissza az ügyfélnek.

- Hitelesítési adatokat, például a használt ügyfél-hitelesítés típusát.

- Párhuzamossági információkat, például a ETag érték és az utolsó módosítás időbélyeg.

- A kérelem-válasz üzenetek méretének.

Részletes utasítások a tárolási analitika naplózásának engedélyezéséről, lásd: [figyelése egy tárfiókot az Azure portálon.](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

### <a name="azure-security-center"></a>Azure Security Center 

[Az Azure Security Center](https://azure.microsoft.com/services/security-center/) megelőzésére és észleli a veszélyeket és ajánlások válaszol az Azure-erőforrások biztonsági állapotát figyeli. A dokumentum több lehetőséget biztosít a biztonsági intézkedéseket, amelyek személyes adatok védelme.

Biztonsági állapotfigyelés segít a biztonsági házirendek betartását. Proaktív stratégiát jelent, amely azonosítani azokat a számítógépeket, amelyek nem felelnek meg a vállalati szabványoknak vagy ajánlott eljárások az erőforrásokat ellenőrizve biztonsági figyelés. A következő erőforrások biztonsági állapota figyelhető meg:

- Számítási (virtuális gépek és felhőszolgáltatások)

- Hálózatkezelés (virtuális hálózatok)

- Tárolás és adatokat (a kiszolgáló és az adatbázis naplózásának és a fenyegetések észlelésére, TDE, tárolás titkosítása)

- Alkalmazások (potenciális biztonsági problémákat)

Biztonsági kérdések e kategóriák közül egyik sem sikerült fenyegetést az személyes adatok védelme.

#### <a name="how-do-i-view-the-security-state-of-my-azure-resources"></a>Hogyan megtekintheti a saját Azure-erőforrások biztonsági állapotát?

A Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát. Megtekintheti a potenciális biztonsági hiányosságok meghatározza a **megelőzési** szakasza az irányítópulton.

   ![](media/protection-personal-data-azure-reporting-tools/image014.png)

1. Az a **megelőzési** szakaszban jelölje be a **számítási** csempére. Itt láthatja az **áttekintése,** együtt a **virtuális gépek** listája az összes virtuális gépek és azok biztonsági állapota és a **a felhőalapú szolgáltatások** webes és feldolgozói szerepkörök listája a Security Center által figyelt.

2. Az a **áttekintése** lapon, a második a javaslatra kattintva további információk megjelenítéséhez.

3. Az a **virtuális gépek** lapra, válassza ki a virtuális gép további részletek megtekintéséhez.

Adatgyűjtést az Azure Security Centerben engedélyezett, ha a Microsoft Monitoring Agent automatikusan kiépített összes meglévő és új támogatott virtuális gépek vannak telepítve. Ez az ügynök gyűjtött adatokat tárolja vagy egy meglévő [Naplóelemzési](https://azure.microsoft.com/services/log-analytics/) az előfizetés vagy új munkaterület társított munkaterület.

[A fenyegetés Eszközintelligencia-jelentések](https://docs.microsoft.com/azure/security-center/security-center-threat-report) Security Center által biztosított. Ezek segítenek megfejteni a identitás, a célok, a jelenlegi és korábbi támadás kampányokat és taktikai, eszközök és a használt eljárásokat a támadó behatolók hasznos információkat. Megoldás és a szervizelési információk is megtalálható.

Az elsődleges fenyegetés jelentések célja segítségével hatékonyan válaszolni az azonnali fenyegetés és intézkedéseket ezt követően, hogy a probléma csökkentése érdekében. A jelentésekben szereplő adatok is nagyban, a dokumentum az incidensekre adott reakciók, a jelentéskészítési és naplózási célokra.

A fenyegetés Eszközintelligencia-jelentések jelenjenek meg. PDF formátumú tartalmaz egy hivatkozást keresztül elért a **jelentések** mezőjét a **gyanús folyamat végrehajtása** az egyes az Azure Security Center biztonsági riasztások panel.

Megtekintése és használata a fenyegetés az Eszközintelligencia-jelentés módjáról további információkért lásd: [Azure Security Center fenyegetés Eszközintelligencia jelentés.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

## <a name="next-steps"></a>További lépések:

[Bevezetés az Azure Active Directory reporting API használatába](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

[Mi az a Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

[A Microsoft Azure Figyelés szolgáltatásának áttekintése](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

[Bevezetés az Azure tevékenységnapló (videó)](https://azure.microsoft.com/resources/videos/intro-activity-log/)
