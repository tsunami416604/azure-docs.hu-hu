---
title: A Microsoft biztonsági kódelemzési feladatainak testreszabása
titleSuffix: Azure
description: Ez a cikk a Microsoft biztonsági kódelemzés bővítményének feladatait ismerteti
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 6cdf892651407defc21f359a8e3b326b4af63b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499986"
---
# <a name="configure-and-customize-the-build-tasks"></a>A buildelési feladatok konfigurálása és testreszabása

Ez a cikk részletesen ismerteti az egyes buildfeladatokban elérhető konfigurációs beállításokat. A cikk a biztonsági kódelemző eszközök feladataival kezdődik. Az utófeldolgozási feladatokkal ér véget.

## <a name="anti-malware-scanner-task"></a>Anti-Malware Scanner feladat

>[!NOTE]
> A Kártevőirtó-olvasó buildelési feladathoz olyan buildügynökszükséges, amelyen a Windows Defender engedélyezve van. A Visual Studio 2017-es és újabb üzemeltetése ilyen ügynököt biztosít. A buildfeladat nem fog futni a Visual Studio 2015 üzemeltetett ügynökén.
>
> Bár az aláírások nem frissíthetők ezeken az ügynökökön, az aláírásoknak mindig három óránál rövidebbnek kell lenniük.

A feladat konfigurációjának részletei a következő képernyőképen és szövegben láthatók.

![A Kártevőirtó-olvasó buildfeladat konfigurálása](./media/security-tools/5-add-anti-malware-task600.png)

A képernyőkép **Típus** listájában az **Alap beállítás** van kiválasztva. Válassza **az Egyéni** lehetőséget a beszkanderi testreszabást biztosító parancssori argumentumok megadásához.

A Windows Defender a Windows Update ügyfélprogram segítségével tölti le és telepíti az aláírásokat. Ha az aláírás frissítése sikertelen a buildelőügynökön, a **HRESULT** hibakód valószínűleg a Windows Update webhelyről érkezik.

A Windows Update hibáiról és azok csökkentéséről a [Windows Update hibakódok összetevő szerinti](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) és a TechNet cikkében a Windows Update Agent – Hibakódok című témakörben olvashat [bővebben.](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)

A feladathoz szükséges YAML-konfigurációval kapcsolatos információkért tekintse meg [a Kártevőirtó YAML-beállításokat](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>BinSkim feladat

> [!NOTE]
> A BinSkim-feladat futtatása előtt a buildnek meg kell felelnie az alábbi feltételek egyikének:
>  - A build bináris összetevőket hoz létre a felügyelt kódból.
>  - A BinSkim segítségével elemezni kívánt bináris összetevők et véglegesített.

A feladat konfigurációjának részletei az alábbi képernyőképen és listában láthatók.

![A BinSkim buildelési feladat konfigurálása](./media/security-tools/7-binskim-task-details.png)

- Állítsa a buildkonfigurációt Debug beállításra, hogy .pdb hibakeresési fájlok keletkezjenek. A BinSkim ezeket a fájlokat használja a kimeneti bináris fájlokban lévő problémák forráskódhoz való visszaképezésére.
- A saját parancssor kutatásának és létrehozásának elkerülése érdekében tegye a következőket:
     - A **Típus** listában válassza az **Alapszintű**lehetőséget.
     - A **Függvénylistában** válassza az **Elemzés lehetőséget.**
- A **Cél**mezőbe írjon be egy vagy több kijelölőt egy fájlhoz, könyvtárhoz vagy szűrőmintához. Ezek a kijelölők egy vagy több elemzendő bináris fájlra oldódnak:
    - Több megadott célt pontosvesszővel (;) kell elválasztani.
    - A megnevező lehet egyetlen fájl, vagy helyettesítő karaktereket is tartalmazhat.
    - A címtárspecifikációknak \\mindig *-val kell végződniük.
    - Példák:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- Ha a Típus listában a **Parancssor** lehetőséget **választja,** a binskim.exe fájl futtatását kell futtatnia:
     - Győződjön meg arról, hogy a binskim.exe első argumentumai az **igeelemzés,** amelyet egy vagy több görbespecifikáció követ. Minden elérési út lehet teljes elérési út vagy a forráskönyvtárhoz viszonyított elérési út.
     - Több célútvonalat szóközrel kell elválasztani.
     - A **/o** vagy a **/output** kapcsolót kihagyhatja. A kimeneti érték hozzáadódik az Ön számára, vagy kicserélik.
     - A szabványos parancssori konfigurációk a következőképpen jelennek meg.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > A záró \\* fontos, ha könyvtárakat ad meg a célhoz.

A BinSkim parancssori argumentumairól, az azonosítónkénti szabályokról vagy a kilépési kódokról a [BinSkim felhasználói útmutatójában](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)talál további információt.

A feladathoz szükséges YAML-konfigurációval kapcsolatos információkért tekintse meg [a BinSkim YAML beállításait](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Hitelesítő adatok vizsgálatának végrehajtása

A feladat konfigurációjának részletei az alábbi képernyőképen és listában láthatók.

![A Hitelesítő adatok olvasói buildelési feladatának konfigurálása](./media/security-tools/3-taskdetails.png)

Az elérhető lehetőségek a következők:

  - **Kimeneti formátum**: A rendelkezésre álló értékek közé tartozik **a TSV**, **CSV**, **SARIF**és **PREfast**.
  - **Eszköz verzió:** Javasoljuk, hogy válassza a **Legújabb**lehetőséget.
  - **Beolvasási mappa**: A beolvasandó adattármappa.
  - **Keresők fájltípusa**: A beolvasáshoz használt keresőfájl megkeresésének lehetőségei.
  - **Fájl elnyomása:** A [JSON-fájlok](https://json.org/) elnyomhatják a kimeneti naplóban lévő problémákat. A tiltó forgatókönyvekről a cikk GYIK című részében olvashat bővebben.
  - **Részletes kimenet**: Magától értetődő.
  - **Kötegméret:** A hitelesítő adatolvasó futtatásához használt egyidejű szálak száma. Az alapértelmezett érték 20. A lehetséges értékek 1 és 2 147 483 647 között mozognak.
  - **Egyeztetési időtúlesély:** Az az időmásodpercben, amíg a keresőegyezést próbál meg próbálni, mielőtt felhagyna a csekkel.
  - **Fájlbeolvasási puffer mérete**: A tartalom olvasása közben használt puffer mérete bájtban. Az alapértelmezett érték 524 288.  
  - **Maximális fájlvizsgálat olvasási bájt:** A tartalomelemzés során a fájlból beolvasandó bájtok maximális száma. Az alapértelmezett érték 104 857 600.
  - **Vezérlőbeállítások:** > **A feladat futtatásának**lehetősége: Itt adhatja meg, hogy a feladat mikor fusson. **Az összetettebb feltételek** megadásához válassza az Egyéni feltételek lehetőséget.
  - **Verzió:** A build feladat verziója az Azure DevOps-on belül. Ez a beállítás nem gyakran használatos.

A feladat hoz a YAML konfigurációjával kapcsolatos információkért tekintse meg [a Hitelesítő adatok olvasójának YAML beállításait](yaml-configuration.md#credential-scanner-task)

## <a name="microsoft-security-risk-detection-task"></a>Microsoft biztonsági kockázatészlelési feladat

> [!NOTE]
> Az MSRD-feladat használata előtt létre kell hoznia és konfigurálnia kell egy fiókot a Microsoft Security Risk Detection (MSRD) szolgáltatással. Ehhez a szolgáltatáshoz külön bevezetési folyamat szükséges. A bővítmény legtöbb más feladatával ellentétben ez a feladat külön előfizetést igényel az MSRD-rel.
>
> További információt a [Microsoft biztonsági kockázatészlelésése](https://aka.ms/msrddocs) és a [Microsoft biztonsági kockázatok észlelése: Útmutató](https://docs.microsoft.com/security-risk-detection/how-to/) képpen talál.

A feladat konfigurálásának részletei az alábbi listában láthatók. Bármely felhasználói felületi elem esetében az egérmutatót az adott elem fölé viheti, hogy segítséget kapjon.

   - **Azure DevOps-szolgáltatásvégpont neve MSRD:** Az Azure DevOps szolgáltatás végpontjának egy általános típusa tárolja a fedélzeti MSRD-példány URL-címét és a REST API-hozzáférési jogkivonatot. Ha létrehozott egy ilyen végpontot, itt adhatja meg. Ellenkező esetben válassza a **Kezelés** hivatkozást az MSRD-feladat hozásához és konfigurálásához.
   - **Fiókazonosító**: Az MSRD-fiók URL-címéről lehívható GUID.
   - **Bináris url-címek**: a nyilvánosan elérhető URL-címek pontosvesszővel tagolt listája. A fuzzing gép használja ezeket az URL-eket, hogy töltse le a binárisokat.
   - **A Magfájlok URL-címei:** A nyilvánosan elérhető URL-ek pontosvesszővel tagolt listája. A fuzzing gép használja ezeket az URL-eket, hogy töltse le a magokat. Az érték megadása nem kötelező, ha a kezdőfájlokat a bináris fájlokkal együtt tölti le.
   - **OS platform típusa**: Az operációs rendszer (OS) platform gépek, amelyek futnak a fuzzing feladat. A rendelkezésre álló értékek a **Windows** és **a Linux**.
   - **Windows Edition / Linux Edition:** A fuzzing feladatot futtató gépek operációs rendszer-kiadása. Felülírhatja az alapértelmezett értéket, ha a gépek egy másik operációs rendszer kiadása.
   - **Csomagtelepítő parancsfájl:** A tesztgépen futtatandó parancsfájl. Ez a parancsfájl telepíti a tesztcélprogramot és annak függőségeit a fuzzing feladat elküldése előtt.
   - **Állásbenyújtási paraméterek:**
       - **Seed Directory**: Az elérési út a könyvtára a fuzzing gép, amely tartalmazza a magokat.
       - **Seed Extension**: A fájlnév kiterjesztése a magok.
       - **Tesztillesztő végrehajtható fájlja**: A tárolófájl elérési útja a fuzzing gépen.
       - **Tesztillesztő végrehajtható architektúrája:** A cél végrehajtható fájl architektúrája. A rendelkezésre álló értékek **x86** és **amd64**.
       - **Tesztillesztő argumentumai**: A tesztvégrehajtható fájlnak átadott parancssori argumentumok. A(z) "%testfile%" argumentum ot az idézőjelek kel együtt a program automatikusan lecseréli a célfájl teljes elérési útjának. Ezt a fájlt a tesztvezető elemzi, és szükséges.
       - **A tesztillesztő folyamata a teszt befejezésekor kilép:** Jelölje be ezt a jelölőnégyzetet, ha a tesztvezető t a befejezéskor le kell állítani. Törölje, ha a vizsgálóvezetőt erőszakkal le kell zárni.
       - **Maximális időtartam (másodpercben)**: A célprogram által a bemeneti fájl elemzéséhez szükséges leghosszabb és szerűen várható idő becslése. Minél pontosabb a becslés, annál hatékonyabban fut a fuzzing alkalmazás.
       - **Tesztillesztő többször is futtatható**: Jelölje be ezt a jelölőnégyzetet, ha a tesztillesztő képes ismétlődően futni állandó vagy megosztott globális állapottól függően.
       - **Tesztillesztő átnevezhető:** Jelölje be ezt a jelölőnégyzetet, ha a tesztillesztő végrehajtható fájlja átnevezhető, és továbbra is megfelelően működik.
       - **A fuzzing alkalmazás egyetlen operációs rendszerfolyamatként fut:** Jelölje be ezt a jelölőnégyzetet, ha a tesztillesztőprogram egyetlen operációsrendszer-folyamat alatt fut. Törölje, ha a tesztvezető további folyamatokat hoz létre.

A feladat Hoz a YAML konfigurációjáról a [Microsoft security risk detection YAML beállításaiközött](yaml-configuration.md#microsoft-security-risk-detection-task) talál információt.

## <a name="roslyn-analyzers-task"></a>Roslyn Analizátorok feladat

> [!NOTE]
> A Roslyn Analizátorok feladat futtatása előtt a buildnek meg kell felelnie az alábbi feltételeknek:
>
> - A builddefiníció tartalmazza a beépített MSBuild vagy VSBuild build feladat fordítására C# vagy Visual Basic kódot. Az analizátorok feladat a beépített feladat bemeneti és kimeneti bemenetén alapul az MSBuild összeállítás futtatásához, amelyen a Roslyn-elemzők engedélyezve vannak.
> - A buildelőfeladatot futtató buildügynök a Visual Studio 2017 15.5-ös vagy újabb verziójával rendelkezik, így a fordító 2.6-os vagy újabb verzióját használja.

A tevékenység konfigurációjának részletei az alábbi listában és megjegyzésben láthatók.

Az elérhető lehetőségek a következők:

- **Szabálykészlet:** Az értékek **SDL szükséges,** **SDL ajánlott,** vagy a saját egyéni szabálykészlet.
- **Analizátorok verzió:** Javasoljuk, hogy válassza a **Legújabb**lehetőséget.
- **Fordítófigyelmeztetések elnyomása Fájl**: Letiltott figyelmeztető azonosítók listáját tartalmazó szövegfájl.
- **Vezérlőbeállítások:** > **A feladat futtatásának**lehetősége: Itt adhatja meg, hogy a feladat mikor fusson. Összetettebb feltételek megadásához válassza az **Egyéni feltételek lehetőséget.**

> [!NOTE]
>
> - A Roslyn Analizátorok integrálva vannak a fordítóval, és csak a csc.exe összeállítás részeként futtathatók. Ezért ez a feladat megköveteli, hogy a fordítási parancs, amely korábban futott a build, hogy újra lejátssza, vagy fussújra. Ez a visszajátszás vagy futtatás a Visual Studio Team Services (VSTS) lekérdezésével történik az MSBuild build feladatnaplókhoz.
>
>   Nincs más módja annak, hogy a feladat megbízhatóan megkapja az MSBuild fordítás imassát a build definícióból. Fontolóra vettük egy szabadkézi szövegdoboz hozzáadását, hogy a felhasználók beírhassák a parancssoraikat. De akkor nehéz lenne tartani ezeket a parancssorokat naprakészen és szinkronban a fő buildel.
>
>   Az egyéni buildek a teljes parancskészlet visszajátszását igénylik, nem csak a fordítóparancsokat. Ezekben az esetekben a Roslyn Analizátorok engedélyezése nem triviális vagy megbízható.
>
> - A Roslyn Analizátorok integrálva vannak a fordítóval. A Roslyn Analizátorok fordítást igényelnek.
>
>   Ez az új buildfeladat a már megépített C# projektek újrafordításával valósul meg. Az új feladat csak az MSBuild és a VSBuild build feladatokat használja ugyanabban a buildben vagy builddefinícióban, mint az eredeti feladat. Ebben az esetben azonban az új feladat használja őket roslyn analizátorok engedélyezve van.
>
>   Ha az új feladat ugyanazon az ügynökön fut, mint az eredeti feladat, az új feladat kimenete felülírja az eredeti feladat kimenetét a *s* sources mappában. Bár a build kimenete ugyanaz, azt javasoljuk, hogy futtassa az MSBuild parancsot, másolja a kimenetet a műtermékek átmeneti könyvtárba, majd futtassa a Roslyn Analizátorok.

A Roslyn-elemzők feladathoz szükséges további forrásokért tekintse meg [a Roslyn-alapú elemzők](https://docs.microsoft.com/dotnet/standard/analyzers/) a Microsoft Docs-ot.

Az analizátor csomagot a [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)oldalon találja.

A feladathoz szükséges YAML-konfigurációval kapcsolatos információkért tekintse meg [a Roslyn Analyzeers YAML beállításait](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>TSLint feladat

A TSLint-ről további információt a [TSLint GitHub tártárban](https://github.com/palantir/tslint)talál.

>[!NOTE] 
>Mint talán tudja, a [TSLint GitHub repo](https://github.com/palantir/tslint) kezdőlapja azt mondja, hogy a TSLint valamikor 2019-ben elavult. A Microsoft alternatív feladatként vizsgálja az [ESLint-et.](https://github.com/eslint/eslint)

A feladathoz szükséges YAML-konfigurációval kapcsolatos információkért tekintse meg a [TSLint YAML opciókat](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Biztonsági elemzési naplók közzététele feladat

A feladat konfigurációjának részletei az alábbi képernyőképen és listában láthatók.

![A Biztonsági elemzési naplók közzététele buildfeladat konfigurálása](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Műtermék neve**: Bármely karakterlánc-azonosító.
- **Műtermék típusa:** A ttól függően, hogy a kiválasztás, közzéteheti a naplókat az Azure DevOps Server vagy egy megosztott fájl, amely elérhető a buildügynök.
- **Eszközök**: Megőrizheti a naplókat bizonyos eszközökhöz, vagy a **Minden eszköz** lehetőséget választva az összes napló megőrzéséhez.

A feladat YAML-konfigurációjával kapcsolatos információkért tekintse meg [a Biztonsági naplók közzététele YAML-beállításokat.](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Biztonsági jelentés feladat

A biztonsági jelentés konfigurációjának részletei az alábbi képernyőképen és listában láthatók.

![A biztonsági jelentés buildelési feladatának konfigurálása](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Jelentések**: Válassza ki bármelyik **folyamatkiszolgálót,** a **TSV-fájlt**és a **Html fájlformátumokat.** Minden kijelölt formátumhoz egy jelentésfájl jön létre.
- **Eszközök:** Válassza ki azokat az eszközöket a builddefinícióban, amelyekhez az észlelt problémák összegzését szeretné összefoglalni. Minden egyes kiválasztott eszköz esetében lehetőség van annak kiválasztására, hogy csak hibák jelennek-e meg, vagy az összesítő jelentésben a hibák és a figyelmeztetések is megjelennek.
- **Speciális beállítások:** Ha a kijelölt eszközök egyikéhez nincsenek naplók, akkor naplózhat egy figyelmeztetést vagy egy hibát. Ha hibát naplóz, a feladat sikertelen lesz.
- **Alapnaplók mappa**: Testre szabhatja az alap naplók mappát, ahol naplók találhatók. De ez a lehetőség általában nem használt.

A feladathoz szükséges YAML-konfigurációval kapcsolatos információkért tekintse meg [a Biztonsági jelentés YAML-beállításait](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>Elemzés utáni tevékenység

A feladat konfigurációjának részletei az alábbi képernyőképen és listában láthatók.

![Az elemzés utáni buildelési feladat konfigurálása](./media/security-tools/a-post-analysis600.png)

- **Eszközök:** Válassza ki azokat az eszközöket a builddefinícióban, amelyekhez feltételesen szeretne buildtörést beadni. Minden egyes kiválasztott eszköz esetében lehetőség van annak kiválasztására, hogy csak a hibákat vagy a hibákat és a figyelmeztetéseket is meg szeretné-e szakítani.
- **Jelentés**: A buildtörést okozó eredményeket tetszés szerint is megírhatja. Az eredmények az Azure DevOps konzolablakába és naplófájljába kerülnek.
- **Speciális beállítások:** Ha a kijelölt eszközök egyikéhez nincsenek naplók, akkor naplózhat egy figyelmeztetést vagy egy hibát. Ha hibát naplóz, a feladat sikertelen lesz.

A feladat Hoz a YAML konfigurációjáról az [Elemzés utáni YAML-beállításokban](yaml-configuration.md#post-analysis-task) talál információt.

## <a name="next-steps"></a>További lépések

A YAML-alapú konfigurációról a [YAML konfigurációs útmutatóban](yaml-configuration.md)talál további információt.

Ha további kérdései vannak a Security Code Analysis kiterjesztéssel és a kínált eszközökkel kapcsolatban, tekintse meg [a GYIK oldalt.](security-code-analysis-faq.md)
