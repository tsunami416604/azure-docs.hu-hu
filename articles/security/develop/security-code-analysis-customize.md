---
title: Microsoft Azure biztonsági kód elemzése feladat testreszabási útmutatója
description: Ez a cikk a biztonsági kód elemzése bővítményben található feladatok testreszabását ismerteti
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
ms.openlocfilehash: ab219b71eb8cd6f6172b7d02a639301c67811b49
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718352"
---
# <a name="how-to-configure--customize-the-build-tasks"></a>kézikönyv: & Konfigurálása a felépítési feladatok testreszabásához

Ez az oldal részletesen ismerteti az egyes összeállítási feladatokban elérhető konfigurációs beállításokat, kezdve a biztonsági kód elemzési eszközeinek feladataival, majd a feldolgozás utáni feladatok

## <a name="anti-malware-scanner-task"></a>Kártevők elleni víruskereső feladat

> [!NOTE]
> A kártevő szoftvereket felépítő feladathoz olyan Build ügynökre van szükség, amelyen engedélyezve van a Windows Defender, amely az "üzemeltetett VS2017" és a későbbi Build-ügynökök esetében igaz. (Nem fog futni az örökölt/VS2015 "tárolt" ügynökön) Az aláírások nem frissíthetők ezen ügynökökön, de az aláírásnak mindig viszonylag aktuálisnak kell lennie, és kevesebb, mint 3 óra.

Képernyőfelvétel és részletek az alábbi konfiguráláshoz.

![A kártevő-elhárító képolvasó felépítési feladatának testreszabása](./media/security-tools/5-add-anti-malware-task600.png) 

- A Type = **Basic** típus beállításai
- A Type = **Custom**paraméterrel megadhatja a vizsgálat testreszabásához szükséges parancssori argumentumokat.

A Windows Defender az Windows Update ügyfelet használja az aláírások letöltésére és telepítésére. Ha az aláírás frissítése sikertelen a Build-ügynökön, a HRESULT valószínűleg Windows Update származik. A Windows Update hibákra és a mérséklésre vonatkozó további információkért tekintse meg [ezt a lapot](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) és a [TechNet-oldalt](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)

## <a name="binskim-task"></a>BinSkim feladat

> [!NOTE]
> A BinSkim feladat futtatásának előfeltétele, hogy a buildnek meg kell felelnie az alábbi feltételek egyikének.
>    - A Build bináris összetevőket hoz létre a felügyelt kódból.
>    - A BinSkim-mel elemezni kívánt bináris összetevőkkel rendelkezik.

Képernyőfelvétel és részletek az alábbi konfiguráláshoz. 

![BinSkim-telepítő](./media/security-tools/7-binskim-task-details.png)  
1. Állítsa be úgy a Build konfigurációját, hogy hibakeresést végezzen * **. pdb** hibakeresési fájlok létrehozásához. A BinSkim a kimeneti bináris fájlban található hibák leképezésére szolgálnak. 
2. Válassza a Type = **Basic** & függvény = **elemzés** lehetőséget, hogy elkerülje a saját parancssorok keresését és létrehozását. 
3. **Cél** – egy vagy több olyan fájl-, könyvtár-vagy szűrési minta, amely az elemzéshez egy vagy több bináris fájlt feloldja. 
    - Több célt pontosvesszővel kell elválasztani **(;)** . 
    - Egyetlen fájl lehet, vagy helyettesítő karakterekkel is rendelkezhet.
    - A címtáraknak mindig véget kell\*
    - Példák:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
4. Ha a Type = **Command sort**választja, 
     - Győződjön meg arról, hogy a **BinSkim. exe** első argumentuma a teljes elérési utakat vagy a forrás könyvtárához viszonyított elérési utakat használó művelet **elemzése** .
     - **Parancssori** bemenet esetén a több célt szóközzel kell elválasztani.
     - Kihagyhatja az **/o** vagy a **/output** file paramétert; a rendszer felveszi vagy lecseréli. 
     - **Szabványos parancssori konfiguráció** 

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
          > [!NOTE]
          > A záró \* érték nagyon fontos a cél könyvtárának vagy könyvtárainak megadásakor. 

További információ a parancssori argumentumokról, az azonosító-vagy kilépési kódok BinSkim: a [BinSkim felhasználói útmutatója](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)

## <a name="credential-scanner-task"></a>Hitelesítőadat-olvasó feladat
Képernyőfelvétel és részletek az alábbi konfiguráláshoz.
 
![Hitelesítőadat-képolvasó testreszabása](./media/security-tools/3-taskdetails.png)

Az elérhető lehetőségek a következők: 
  - **Kimeneti formátum** – TSV/CSV/SARIF/előgyors
  - **Eszköz verziója** Ajánlott Legújabb
  - **Scan Folder (mappa vizsgálata** ) – a tárházban a beolvasandó mappa
  - **Keresők** fájltípusa – beállítások a vizsgálathoz használt keresők kereséséhez.
  - **Mellőzési fájl** – a rendszer egy [JSON](https://json.org/) -fájlt használ a kimeneti naplóban felmerülő problémák letiltásához (további részleteket az erőforrások szakaszban talál). 
  - **Részletes kimenet** – magától értetődő 
  - **Köteg mérete** – a hitelesítőadat-képolvasók párhuzamos futtatásához használt egyidejű szálak száma. Alapértelmezett értéke 20 (az értéknek 1 és 2147483647 között kell lennie).
  - **Egyeztetési időkorlát** – a kereső egyezésének megkezdéséhez szükséges idő az ellenőrzések elhagyása előtt. 
  - **Fájl vizsgálata olvasási puffer mérete** – puffer mérete a tartalom bájtban való olvasása közben. (Alapértelmezett értéke 524288) 
  - **Maximális fájl vizsgálata – olvasási bájt** – a tartalom elemzése során egy adott fájlból beolvasott bájtok maximális száma. (Alapértelmezett értéke 104857600) 
  - **Feladat futtatása** (a **vezérlési beállítások**alatt) – Megadja, hogy a feladat Mikor fusson. Összetettebb feltételek megadásához válassza az "egyéni feltételek" lehetőséget. 
  - **Verzió** – felépítési feladat verziója az Azure DevOps-n belül. Nem gyakran használt. 

## <a name="microsoft-security-risk-detection-task"></a>Microsoft biztonsági kockázatok észlelése feladat
> [!NOTE]
> Létre kell hoznia és konfigurálnia kell egy fiókot a kockázati észlelési szolgáltatással, hogy használhassa ezt a feladatot. A szolgáltatáshoz külön bevezetési folyamat szükséges; Ez a bővítmény nem "Plug and Play" típusú, mint a többi feladat. Tekintse meg a [Microsoft biztonsági kockázatok észlelése](https://aka.ms/msrddocs) és [a Microsoft biztonsági kockázatok észlelése: ](https://docs.microsoft.com/security-risk-detection/how-to/) Útmutató az utasításokhoz.

Az alábbi konfigurálás részletei.

Adja meg a szükséges adatértékeket; minden beállításhoz a hover Text súgója tartozik.
   - **Az Azure DevOps szolgáltatás végpontjának neve a MSRD**: Ha létrehozta az Azure DevOps Service-végpont általános típusát a MSRD-példány URL-címének (a bevezetéséhez) és a REST API hozzáférési jogkivonat tárolásához, akkor a szolgáltatási végpontot is kiválaszthatja. Ha nem, a kezelés hivatkozásra kattintva hozzon létre és konfiguráljon egy új szolgáltatási végpontot ehhez a MSRD-feladathoz. 
   - **Fiókazonosító**: Ez egy GUID, amely a MSRD-fiók URL-címéből kérhető le.
   - **Bináris fájlok URL-címei**: A nyilvánosan elérhető URL-címek pontosvesszővel tagolt listája (amelyet a fuzzy gép a bináris fájlok letöltéséhez használ).
   - **A vetőmag-fájlok URL-címei**: A nyilvánosan elérhető URL-címek pontosvesszővel tagolt listája (amelyet a rendszer a fuzzy gép a magok letöltéséhez használ). Ez a mező nem kötelező, ha a magok fájljai a bináris fájlokkal együtt letöltődnek.
   - **Operációsrendszer-platform típusa**: A számítógép operációsrendszer-platformjának típusa (Windows vagy Linux), amelyen a Fuzzing feladatot futtatni kívánja.
   - **Windows Edition/Linux kiadás**: Azon gépek operációsrendszer-kiadása, amelyeken a Fuzzing feladatot futtatni kívánja. Ha a gépek eltérő operációsrendszer-kiadással rendelkeznek, felülírhatja az alapértelmezett értéket.
   - **Csomag telepítési parancsfájlja**: Adja meg a tesztelési gépen futtatni kívánt parancsfájlt a tesztkörnyezet és a függőségeinek a zavaros feladatok elküldése előtt történő telepítéséhez.
   - **Feladatok küldésének paraméterei**:
       - **Vetőmag könyvtára**: A magot tartalmazó fuzzy számítógép könyvtárának elérési útja.
       - **Vetőmag-kiterjesztés**: A magok fájlkiterjesztés
       - **Tesztelési illesztőprogram végrehajtható fájlja**: A cél végrehajtható fájl elérési útja a fuzzy számítógépen.
       - **Illesztőprogram-végrehajtható fájl tesztelési architektúrája**: A cél végrehajtható fájl architektúrája (x86 vagy amd64).
       - **Tesztelési illesztőprogram argumentumai**: A tesztelési cél végrehajtható fájljába átadott parancssori argumentumok. Vegye figyelembe, hogy a (z) "% TESTFILE%" szimbólumot, beleértve az idézőjeleket is, automatikusan lecseréli a célfájl teljes elérési útjára, amelyet a teszt illesztőprogramnak el kell elemezni, és szükség van rá.
       - Teszt **befejezése után a teszt-illesztőprogram folyamata kilép**: Ellenőrizze, hogy befejeződött-e a tesztelési illesztőprogram befejezése; Törölje a jelet, ha a teszt illesztőprogramjának kényszerített lezártnak kell lennie.
       - **Maximális időtartam (másodperc)** : Adja meg annak a leghosszabb ésszerű várható időtartamnak a becslését, amely szükséges ahhoz, hogy a program egy bemeneti fájlt elemezzen. Minél pontosabb ez a becslés, annál hatékonyabb a zavaros Futtatás.
       - **A tesztelési illesztőprogram többször is futtatható**: Ellenőrizze, hogy a tesztelési illesztőprogram többször is futtatható-e a megőrzött/megosztott globális állapottól függően.
       - **A tesztelési illesztőprogram átnevezhető**: Ellenőrizze, hogy a teszt-illesztőprogram végrehajtható fájlja átnevezhető-e, és hogy továbbra is megfelelően működik-e.
       - **A Fuzzing alkalmazás egyetlen operációsrendszer-folyamatként fut**: Ellenőrizze, hogy a tesztelési illesztőprogram egyetlen operációsrendszer-folyamat alatt fut-e; Törölje a jelölést, ha a teszt-illesztőprogram további folyamatokat indít el.


## <a name="roslyn-analyzers-task"></a>A-elemzők feladata
> [!NOTE]
> A következő feltételeknek kell megfelelnie, hogy a-hoz szükséges a a-hoz tartozó-elemző feladat futtatása.
>  - A Build definíciója tartalmazza a beépített MSBuild vagy VSBuild Build feladatot a fordítási C# (vagy VB) kód létrehozásához. Ez a feladat az adott felépítési feladat bemenetére és kimenetére támaszkodik az MSBuild-fordítás ismételt futtatásához.
>  - A Build feladatot futtató Build ügynök rendelkezik a Visual Studio 2017 v 15.5 vagy újabb verzióval (a fordítóprogram 2.6. x verziója).
>

Az alábbi konfigurálás részletei.

Az elérhető lehetőségek a következők: 
- **Szabályrendszert** – az SDL szükséges, az SDL használatát ajánljuk, vagy használhat saját egyéni szabályokat is.
- **Elemző verziója** Ajánlott Legújabb
- **Fordítói figyelmeztetések mellőzése fájl** – egy szöveges fájl, amely a figyelmeztetések azonosítóinak listáját tartalmazza, amelyeket le kell tiltani. 
- **Feladat futtatása** (a **vezérlési beállítások**alatt) – Megadja, hogy a feladat Mikor fusson. Összetettebb feltételek megadásához válassza az "**Egyéni feltételek**" lehetőséget. 

> [!NOTE]
> - A a a fordító-elemzők a fordítóprogramok integráltak, és csak a CSC. exe összeállításának részeként futtathatók. Ezért ehhez a feladathoz a buildben korábban futtatott fordítói parancs újrajátszása/újrafuttatása szükséges. Ezt az MSBuild Build VSTS lekérdezésével végezheti el (a feladatnak nincs más módja arra, hogy megbízhatóan lekérje az MSBuild fordítási parancssort a Build-definícióból. a Szabadkézi szövegmező hozzáadásával lehetővé teheti a felhasználók számára a parancssorok megadását. , de nehéz lenne ezeket naprakészen tartani és szinkronizálni a fő buildtel. Az egyéni buildek esetében a parancsok teljes készletét újra le kell játszani, nem csak a fordítóprogram parancsait, és ez nem triviális/megbízható ahhoz, hogy ezekben az esetekben engedélyezzék a következőt:. 
> - A a a fordítóval integrált, és a fordítás meghívása szükséges. Ez a felépítési feladat olyan projektek újrafordításával C# valósul meg, amelyek már csak az MSBuild/VSBuild Build feladattal lettek felépítve, ugyanabban az összeállítási/összeállítási definícióban, de ebben az esetben az elemzők engedélyezve vannak. Ha ez a felépítési feladat az eredeti felépítési feladattal megegyező ügynökön fut, az eredeti MSBuild/VSBuild felépítési feladat kimenete felül lesz írva a "Sources" mappában a Build feladat kimenete alapján. A Build kimenete azonos lesz, de javasoljuk, hogy futtassa az MSBuild-t, másolja a kimenetet az összetevők átmeneti könyvtárába, majd futtassa a következőt:.
>

További források a (z) [Microsoft docs](https://docs.microsoft.com/dotnet/standard/analyzers/)

A Build feladat által telepített és használt Analyzer-csomag [itt](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers) található 

## <a name="tslint-task"></a>TSLint feladat

További információ a TSLint-ről: [TSLint GitHub](https://github.com/palantir/tslint) -tárház
>[!NOTE] 
>Az is előfordulhat, hogy a TSLint egy ideig elavulttá válik a 2019-as verzióban (forrás: [TSLint GitHub](https://github.com/palantir/tslint)-adattár) A csapat jelenleg a [ESLint](https://github.com/eslint/eslint) vizsgálja, és egy új feladatot hoz létre a ESLint-hez az ütemtervben.

## <a name="publish-security-analysis-logs-task"></a>Biztonsági elemzési naplók közzététele feladat
Képernyőfelvétel és részletek az alábbi konfiguráláshoz.

![A közzétételi biztonsági elemzés testreszabása](./media/security-tools/9-publish-security-analsis-logs600.png)  

- Összetevő **neve** – bármely karakterlánc-azonosító lehet
- Összetevő **típusa** – a naplókat közzéteheti az Azure-DevOps-kiszolgálóra vagy egy olyan fájlmegosztás számára, amely elérhető a Build ügynök számára. 
- **Eszközök** – dönthet úgy, hogy megőrzi a naplókat az egyes/meghatározott eszközökhöz, vagy kiválasztja az **összes eszközt** az összes napló megőrzéséhez. 

## <a name="security-report-task"></a>Biztonsági jelentés feladata
Képernyőfelvétel és részletek az alábbi konfiguráláshoz.  
![Telepítés utáni elemzés](./media/security-tools/4-createsecurityanalysisreport600.png) 
- **Jelentések** – a létrehozandó jelentési fájlok kiválasztása; az egyik minden formátum-konzolban, **TSV**-ben és/vagy **HTML** -ben jön létre 
- **Eszközök** – válassza ki a Build-definícióban szereplő eszközöket, amelyekhez az észlelt problémák összegzését szeretné. Minden kiválasztott eszköz esetében lehetőség van annak kiválasztására, hogy csak a hibákat, illetve a jelentésben szereplő hibákat és figyelmeztetéseket szeretné-e látni. 
- **Speciális beállítások** – megadhatja, hogy a rendszer figyelmeztetést vagy hibát (a feladat elvégzését) naplózza-e, ha nincsenek naplók a kiválasztott eszközök valamelyikéhez.
Testreszabhatja az alapnaplók mappát, ahol a naplók találhatók, de ez nem egy tipikus forgatókönyv. 

## <a name="post-analysis-task"></a>Az elemzést követő feladat
Képernyőfelvétel és részletek az alábbi konfiguráláshoz.

![Az elemzések testreszabása](./media/security-tools/a-post-analysis600.png) 
- **Eszközök** – válassza ki azokat az eszközöket a Build-definícióban, amelyekhez az eredmények alapján létre kíván adni egy összeállítási megszakítást. Minden kiválasztott eszköz esetében lehetőség van annak kiválasztására, hogy csak a hibákat, illetve a hibákat és a figyelmeztetéseket szeretné-e megszakítani. 
- **Jelentés** – megadhatja a talált eredményeket, és a Build-töréspontot felhasználhatja az Azure DevOps-konzol ablakába és a naplófájlba. 
- **Speciális beállítások** – megadhatja, hogy a rendszer figyelmeztetést vagy hibát (a feladat elvégzését) naplózza-e, ha nincsenek naplók a kiválasztott eszközök valamelyikéhez.

## <a name="next-steps"></a>További lépések

Ha további kérdései vannak a bővítményről és a rendelkezésre álló eszközökről, tekintse meg a [Gyakori kérdések oldalát.](security-code-analysis-faq.md)


