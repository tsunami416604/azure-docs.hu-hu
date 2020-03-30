---
title: Csatlakozás 3270-es alkalmazásokhoz IBM nagyszámítógépeken
description: Az Azure Logic Apps és az IBM 3270-es összekötő használatával integrálhatja és automatizálhatja a 3270-es képernyőalapú alkalmazásokat az Azure-ral
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 808eef5424d678559ae94ffd04e41eacd0f16aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371105"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integrálja a 3270 képernyőalapú alkalmazásokat az IBM nagygépein az Azure-ral az Azure Logic Apps és az IBM 3270-es összekötő használatával

> [!NOTE]
> Ez az összekötő [*nyilvános előzetes verzióban*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)érhető el. 

Az Azure Logic Apps és az IBM 3270-es összekötő segítségével elérheti és futtathatja az IBM nagyszámítógépes alkalmazásait, amelyeket általában a 3270-es emulátorképernyőkön való navigálással szeretne vezetni. Ily módon integrálhatja az IBM nagyszámítógépes alkalmazásait az Azure-ral, a Microsofttal és más alkalmazásokkal, szolgáltatásokkal és rendszerekkel az Azure Logic Apps automatikus munkafolyamatok létrehozásával. Az összekötő a TN3270 protokoll használatával kommunikál az IBM nagyszámítógépeivel, és az Azure Government és az Azure China 21Vianet kivételével minden Azure Logic Apps-régióban elérhető. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Ez a cikk a 3270-es összekötő használatának ezeket a szempontokat ismerteti: 

* Miért érdemes használni az IBM 3270-es összekötőt az Azure Logic Apps alkalmazásban, és hogyan fut az összekötő a 3270-es képernyőalapú alkalmazásokban?

* A 3270-es csatlakozó használatának előfeltételei és beállítása

* A 3270-es összekötőműveletek logikai alkalmazáshoz való hozzáadásának lépései

## <a name="why-use-this-connector"></a>Miért érdemes használni ezt a csatlakozót?

Az IBM nagyszámítógépeken lévő alkalmazások eléréséhez általában 3270-es terminálemulátort használ, amelyet gyakran "zöld képernyőnek" neveznek. Ez a módszer egy jól bevált módon, de korlátai vannak. Bár a Gazdaintegrációs kiszolgáló (HIS) segít közvetlenül ezekkel az alkalmazásokkal dolgozni, néha előfordulhat, hogy a képernyő és az üzleti logika elkülönítése nem lehetséges. Vagy, talán már nincs információ, hogyan működnek a fogadó alkalmazások.

Ezeknek a forgatókönyveknek a kiterjesztéséhez az IBM 3270-es összekötő az Azure Logic Apps-ben együttműködik a 3270-es tervezőeszközzel, amelyet egy adott feladathoz használt gazdaképernyők rögzítésére vagy "rögzítésére" használ, meghatározza az adott feladat navigációs folyamatát a nagyszámítógépes alkalmazáson keresztül, és meghatározza a a feladat bemeneti és kimeneti paramétereit tartalmazó módszerek. A tervezőeszköz ezeket az adatokat olyan metaadatokká alakítja, amelyeket a 3270-es összekötő használ, amikor olyan műveletet hív meg, amely a feladatot a logikai alkalmazásból jelöli.

Miután létrehozta a metaadatfájlt a tervezőeszközről, hozzáadja a fájlt egy azure-beli integrációs fiókhoz. Így a logikai alkalmazás hozzáférhet az alkalmazás metaadataihoz, amikor 3270-es összekötő műveletet ad hozzá. Az összekötő beolvassa a metaadat-fájlt az integrációs fiókból, kezeli a 3270-es képernyőkön való navigálást, és dinamikusan bemutatja a 3270-es összekötő művelet paramétereit. Ezután adatokat adhat meg a gazdaalkalmazásnak, és az összekötő visszaadja az eredményeket a logikai alkalmazásnak. Így integrálhatja a régi alkalmazásokat az Azure-ral, a Microsofttal és az Azure Logic Apps által támogatott egyéb alkalmazásokkal, szolgáltatásokkal és rendszerekkel.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Ajánlott: [Integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Ezt a környezetet választhatja ki a logikai alkalmazás létrehozásának és futtatásának helyeként. Az ISE hozzáférést biztosít a logikai alkalmazásból az Azure virtuális hálózatokon belül védett erőforrásokhoz.

* A 3270-es képernyőalapú alkalmazás automatizálásához és futtatásához használandó logikai alkalmazás

  Az IBM 3270-összekötő nem rendelkezik eseményindítók, ezért egy másik eseményindító a logikai alkalmazás indításához, például az **ismétlődési** eseményindító. Ezután hozzáadhat 3270-es összekötő műveleteket. Első lépésekhez [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) 
  Ha ISE-t használ, válassza ki az ISE-t a logikai alkalmazás helyeként.

* [Töltse le és telepítse a 3270 Tervező eszközt.](https://aka.ms/3270-design-tool-download)
Az egyetlen előfeltétel a [Microsoft .](https://aka.ms/net-framework-download)

  Ez az eszköz segít rögzíteni a képernyők, navigációs útvonalak, módszerek és paraméterek a feladatok az alkalmazásban, amely hozzáadott és futtatja a 3270 összekötő műveletek. Az eszköz létrehoz egy ÁLLOMÁSintegrációs tervező XML-fájlt (HIDX), amely biztosítja a szükséges metaadatokat az összekötő számára a nagyszámítógépes alkalmazás vezetéséhez.
  
  Az eszköz letöltése és telepítése után kövesse az alábbi lépéseket a gazdagéphez való csatlakozáshoz:

  1. Nyissa meg a 3270 tervezőeszközt. A **Munkamenet** menüben válassza a **Host Sessions parancsot.**
  
  1. Adja meg a TN3270 gazdakiszolgáló adatait.

* Integrációs [fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), amely az a hely, ahol a HIDX-fájlt térképként tárolja, így a logikai alkalmazás hozzáférhet a fájl metaadataihoz és metódusdefinícióihoz. 

  Győződjön meg arról, hogy az integrációs fiók kapcsolódik a használt logikai alkalmazáshoz. Ise használata esetén győződjön meg arról, hogy az integrációs fiók helye megegyezik a logikai alkalmazás által használt ISE-vel.

* Hozzáférés a nagyszámítógépes alkalmazást tartalmazó TN3270 kiszolgálóhoz

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Metaadatok létrehozása – áttekintés

Egy 3270-es képernyőalapú alkalmazásban a képernyők és az adatmezők egyediek a forgatókönyvekhez, ezért a 3270-es összekötőnek szüksége van az alkalmazásra vonatkozó információkra, amelyeket metaadatként adhat meg. Ez a metaadat olyan információkat ír le, amelyek segítenek a logikai alkalmazásnak a képernyők azonosításában és felismerésén, a képernyők közötti navigálásban, az adatok bevitelének helyével és az eredmények várható helyével. A metaadatok megadásához és létrehozásához használja a 3270 tervezőeszközt, amely végigvezeti az adott módokon vagy szakaszokon, ahogy azt később részletesebben *ismerteti:*

* **Rögzítés**: Ebben a módban rögzítheti az adott feladat nagyszámítógépes alkalmazással való elvégzéséhez szükséges képernyőket, például a banki egyenleg megszerzéséhez.

* **Navigáció**: Ebben a módban megadhatja a tervet vagy elérési utat, hogyan navigálhat a nagyszámítógépes alkalmazás képernyőin az adott feladathoz.

* **Metódusok**: Ebben a módban definiálja `GetBalance`például azt a módszert, amely leírja a képernyő navigációs útvonalát. Azt is kiválaszthatja a mezőket minden képernyőn, hogy lesz a módszer bemeneti és kimeneti paramétereit.

### <a name="unsupported-elements"></a>Nem támogatott elemek

A tervezőeszköz nem támogatja az alábbi elemeket:

* Részleges IBM Basic Mapping Support (BMS) leképezések: BMS-leképezések importálásakor a tervezőeszköz figyelmen kívül hagyja a részleges képernyődefiníciókat.
* In/Out paraméterek: Nem definiálhat Be/Out paramétereket.
* Menüfeldolgozás: Nem támogatott az előzetes verzióban
* Tömbfeldolgozás: Nem támogatott az előzetes verzióban

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Rögzítési képernyők

Ebben a módban minden 3270-es képernyőn megjelöl egy elemet, amely egyedileg azonosítja a képernyőt. Megadhatja például egy szövegsort vagy összetettebb feltételkészletet, például adott szöveget és egy nem üres mezőt. Ezeket a képernyőket rögzítheti a gazdakiszolgálóval való élő kapcsolaton keresztül, vagy importálhatja ezeket az adatokat az IBM Basic Mapping Support (BMS) térképről. Az élő kapcsolat TN3270 emulátort használ az állomáshoz való csatlakozáshoz. Minden összekötő műveletnek egyetlen feladathoz kell leképeznie, amely a munkamenethez való csatlakozással kezdődik, és a munkamenet leválasztásával végződik.

1. Ha még nem tette meg, nyissa meg a 3270 tervezőeszközt. Az eszköztáron válassza a **Rögzítés** lehetőséget, hogy rögzítési módba lépjen.

1. A felvétel elindításához nyomja le az F5 billentyűt, vagy a **Felvétel** menüben válassza a **Felvétel indítása parancsot.** 

1. A **Munkamenet menüben** válassza a **Csatlakozás parancsot.**

1. A **Rögzítés** ablaktáblán az alkalmazás első képernyőjétől kezdve lépjen végig az alkalmazáson a rögzített feladathoz.

1. Miután befejezte a feladatot, jelentkezzen ki az alkalmazásból, ahogy általában teszi.

1. A **Munkamenet** menüben válassza a **Kapcsolat bontása parancsot.**

1. A felvétel leállításához nyomja le a Shift + F5 billentyűt, vagy a **Felvétel menüben** válassza a **Felvétel leállítása parancsot.**

   Miután rögzítette egy feladat képernyőit, a tervezőeszköz azokat a miniatűröket jeleníti meg, amelyek ezeket a képernyőket jelölik. Néhány megjegyzés ezekről a miniatűrökről:

   * A rögzített képernyők közé tartozik az "Üres" nevű képernyő.

     Amikor először csatlakozik a [CICS-hez,](https://www.ibm.com/it-infrastructure/z/cics)el kell küldenie a "Clear" kulcsot, mielőtt megadhatna a futtatni kívánt tranzakció nevét. A képernyőn, ahová a "Clear" gombot küldi, nincsenek *felismerési jellemzők*, például a képernyő címe, amelyet a Képernyőfelismerés szerkesztővel adhat hozzá. A képernyő ábrázolásához a bélyegképek tartalmaznak egy "Üres" nevű képernyőt. Ezt a képernyőt később arra használhatja, hogy képviselje azt a képernyőt, amelyen beírja a tranzakció nevét.

   * Alapértelmezés szerint a rögzített képernyő neve a képernyő első szavát használja. Ha ez a név már létezik, a tervezőeszköz aláhúzásjellel és számmal fűzi hozzá a nevet, például "WBGB" és "WBGB_1".

1. Ha értelmesebb nevet szeretne adni a rögzített képernyőnek, kövesse az alábbi lépéseket:

   1. A **Gazdaképernyők** ablaktáblában jelölje ki az átnevezni kívánt képernyőt.

   1. Ugyanabban az ablaktáblában, ugyanazon ablaktábla alján keresse meg a **Képernyőnév tulajdonságot.**

   1. Módosítsa az aktuális képernyőnevet leíróbb névre.

1. Most adja meg az egyes képernyők azonosítására szolgáló mezőket.

   A 3270-es adatfolyamban a képernyők nem rendelkeznek alapértelmezett azonosítókkal, ezért minden képernyőn egyedi szöveget kell kijelölnie. Összetett esetekben több feltételt is megadhat, például egyedi szöveget és egy adott feltételt tartalmazó mezőt.

Miután befejezte a felismerési mezők kiválasztását, lépjen a következő módra.

### <a name="conditions-for-identifying-repeated-screens"></a>Az ismétlődő képernyők azonosításának feltételei

Ahhoz, hogy az összekötő navigálhasson és különbséget tegyen a képernyők között, általában egyedi szöveget talál a képernyőn, amelyet azonosítóként használhat a rögzített képernyők között. Az ismétlődő képernyőkesetén további azonosítási módszerekre lehet szükség. Tegyük fel például, hogy két képernyője ugyanúgy néz ki, kivéve, hogy az egyik képernyő érvényes értéket ad vissza, míg a másik képernyő hibaüzenetet ad vissza.

A tervezőeszközben a Képernyőfelismerés-szerkesztő segítségével adhat hozzá *felismerési jellemzőket*, például egy képernyőcímet, például a "Számlaegyenleg begete"-t. Ha elágazó elérési út, és mindkét ág ugyanazt a képernyőt adja vissza, de eltérő eredménnyel, más felismerési attribútumokra van szüksége. Futási időben az összekötő ezeket az attribútumokat használja az aktuális elágazás és a villa meghatározásához. A következő feltételeket használhatja:

* Adott érték: Ez az érték megegyezik a megadott karakterlánccal a megadott helyen.
* NEM adott érték: Ez az érték nem egyezik meg a megadott karakterlánccal a megadott helyen.
* Üres: Ez a mező üres.
* NEM üres: Ez a mező nem üres.

További információ: A jelen témakör későbbi, [példa navigációs terve.](#example-plan)

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Navigációs tervek meghatározása

Ebben a módban meghatározhatja a nagyszámítógépes alkalmazás képernyőin való navigálás lépéseit az adott feladathoz. Előfordulhat például, hogy egynél több olyan elérési úttal rendelkezik, amelyet az alkalmazás akkor vehet fel, ha az egyik útvonal a megfelelő eredményt adja, míg a másik útvonal hibát okoz. Minden képernyőhöz adja meg a következő képernyőre való lépéshez szükséges billentyűleütéseket, például `CICSPROD <enter>`.

> [!TIP]
> Ha több olyan feladatot automatizál, amelyek ugyanazt a csatlakoztatási és leválasztó képernyőt használják, a tervezőeszköz speciális Csatlakoztatási és leválasztási tervtípusokat biztosít. Amikor definiálja ezeket a terveket, hozzáadhatja őket a navigációs terv elejéhez és végéhez.

### <a name="guidelines-for-plan-definitions"></a>A tervdefiníciók irányelvei

* Tartalmazza az összes képernyőt, kezdve a csatlakozással és a kapcsolat bontásával.

* Létrehozhat egy önálló csomagot, vagy használhatja a Csatlakozás és leválasztás csomagokat, amelyek lehetővé teszik az összes tranzakcióra vonatkozó közös képernyők újrafelhasználását.

  * A Connect-csomag utolsó képernyőjének meg kell egyeznie a navigációs terv első képernyőjével.

  * A kapcsolat bontása terv első képernyőjének meg kell egyeznie a navigációs terv utolsó képernyőjével.

* Előfordulhat, hogy a rögzített képernyők sok ismétlődő képernyőt tartalmaznak, ezért a tervben csak egy példányt válasszon ki és használjon. Íme néhány példa az ismétlődő képernyőkre:

  * A bejelentkezési képernyő, például az **MSG-10** képernyő
  * A CICS üdvözlőképernyője
  * A "Clear" vagy **üres** képernyő

<a name="create-plans"></a>

### <a name="create-plans"></a>Tervek létrehozása

1. A 3270 Tervező eszköz eszköztárán válassza a **Navigáció** lehetőséget, hogy navigációs módba lépjen.

1. A terv elindításához a **Navigációs** ablakban válassza az **Új terv**lehetőséget.

1. Az **Új csomag név kiválasztása csoportban**adja meg a terv nevét. A **Típus** listában válassza ki a terv típusát:

   | Terv típusa | Leírás |
   |-----------|-------------|
   | **Folyamat** | Önálló vagy kombinált csomagokhoz |
   | **Kapcsolódás** | Csomagok csatlakoztatása esetén |
   | **Leválasztás** | Csomagok bontása esetén |
   |||

1. A **Gazdaképernyők** ablaktáblából húzza a rögzített miniatűröket a **navigációs** ablak navigációs tervfelületére.

   Az üres képernyő ábrázolásához használja az "Üres" képernyőt.

1. Rendezze el a képernyőket a definiálni kívánt feladatot leíró sorrendben.

1. A tervezőeszköz eszköztárán a képernyők közötti áramlási útvonal (beleértve a villákat és illesztéseket) meghatározásához válassza a **Folyamat**lehetőséget.

1. Válassza ki a folyamat első képernyőjét. Húzza a kapcsolatot a folyamat következő képernyőjére.

1. Minden képernyőhöz adja meg az **AID key** tulajdonság (Figyelemazonosító) és a **Rögzített szöveg** tulajdonság értékeit, amelyek a folyamatot a következő képernyőre helyezi.

   Lehet, hogy csak az AID gombot, vagy mind az AID gombot és a rögzített szöveget.

A navigációs terv befejezése után [a következő módban határozhatja meg a módszereket.](#define-method)

<a name="example-plan"></a>

### <a name="example"></a>Példa

Ebben a példában tegyük fel, hogy egy "WBGB" nevű CICS-tranzakciót futtat, amely a következő lépéseket foglalja el: 

* Az első képernyőn meg kell adnia egy nevet és egy fiókot.
* A második képernyőn megkapja a számlaegyenleget.
* Kilép az "Üres" képernyőre.
* A CICS-ből az "MSG-10" képernyőre jelentkezik ki.

Tegyük fel, hogy megismétli ezeket a lépéseket, de helytelen adatokat ad meg, így rögzítheti a hibát megjelenítő képernyőt. Itt vannak a képernyők elfog:

* MSG-10
* CICS Üdvözöljük
* Üres
* WBGB_1 (bemenet)
* WBGB_2 (hiba)
* Empty_1
* MSG-10_1

Bár sok képernyő itt egyedi neveket kap, egyes képernyők ugyanazok a képernyők, például az "MSG-10" és az "Üres". Ismétlődő képernyő esetén csak egy példányt használjon az adott képernyőhöz a tervben. Íme néhány példa arra, hogyan nézhet ki egy önálló csomag, a Csatlakoztatási terv, a Kapcsolat bontása terv és a kombinált terv:

* Önálló terv

  ![Önálló navigációs terv](./media/connectors-create-api-3270/standalone-plan.png)

* Csomag összekapcsolása

  ![Csomag összekapcsolása](./media/connectors-create-api-3270/connect-plan.png)

* Leválasztás i terve

  ![Leválasztás i terve](./media/connectors-create-api-3270/disconnect-plan.png)

* Kombinált terv

  ![Kombinált terv](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Példa: Ismétlődő képernyők azonosítása

Az összekötő navigálásához és a képernyők megkülönböztetéséhez általában egyedi szöveget talál a képernyőn, amelyet azonosítóként használhat a rögzített képernyőkön. Az ismétlődő képernyőkesetén további azonosítási módszerekre lehet szükség. A példatervnek van egy elágazása, ahol hasonló képernyőket kaphat. Az egyik képernyő a számlaegyenleget adja vissza, míg a másik képernyőn hibaüzenet jelenik meg.

A tervezőeszköz lehetővé teszi, hogy a Képernyőfelismerés-szerkesztő segítségével felvegye a felismerési attribútumokat, például a "Számlaegyenleg beget" nevű képernyőcímet. Abban az esetben, ha hasonló képernyők esetén más attribútumokra van szükség. Futási időben az összekötő ezeket az attribútumokat használja az elágazás és az elágazás meghatározásához.

* Az érvényes bemenetet visszaadó ágban, amely a számlaegyenleget tartalmazó képernyő, hozzáadhat egy "nem üres" feltételt tartalmazó mezőt.

* A hibával visszaadott ágban hozzáadhat egy "üres" feltételt tartalmazó mezőt.

<a name="define-method"></a>

## <a name="define-methods"></a>Metódusok meghatározása

Ebben a módban a navigációs tervhez társított módszert határozhatja meg. Minden metódusparaméterhez meg kell adni az adattípust, például egy karakterláncot, egész, dátum vagy idő, és így tovább. Ha elkészült, tesztelheti a módszert az élő gazdagépen, és ellenőrizheti, hogy a módszer a várt módon működik-e. Ezután létrehozza a metaadatfájlt vagy a GAZDAintegrációs tervező XML-fájlját, amely most már rendelkezik az IBM 3270-összekötő műveletének létrehozásához és futtatásához használható metódusdefiníciókkal.

1. A 3270 Tervező eszköz eszköztárán válassza a **Metódusok** lehetőséget, hogy belépjen a Módszerek módba. 

1. A **Navigációs** ablakban jelölje ki azt a képernyőt, amelyen a kívánt beviteli mezők vannak.

1. A metódus első beviteli paraméterének hozzáadásához kövesse az alábbi lépéseket:

   1. A **Rögzítés** ablaktáblán a 3270-es emulátor képernyőn válassza ki a teljes mezőt, ne csak a mezőbe nõtt szöveget, amelyet első bevitelként szeretne beírni.

      > [!TIP]
      > Az összes mező megjelenítéséhez és a teljes mező kiválasztásához kattintson a **Nézet** menü **Minden mező parancsára.**

   1. A tervezőeszköz eszköztárán válassza a **Beviteli mező**lehetőséget. 

   További bemeneti paraméterek hozzáadásához ismételje meg az előző lépéseket az egyes paramétereknél.

1. A módszer első kimeneti paraméterének hozzáadásához kövesse az alábbi lépéseket:

   1. A **Rögzítés** ablaktáblán a 3270-es emulátor képernyőn válassza ki a teljes mezőt, ne csak a mezőbe nbelül lévő szöveget, amelyet első kimenetként szeretne.

      > [!TIP]
      > Az összes mező megjelenítéséhez és a teljes mező kiválasztásához kattintson a **Nézet** menü **Minden mező parancsára.**

   1. A tervezőeszköz eszköztárán válassza a **Kimeneti mező**lehetőséget.

   További kimeneti paraméterek hozzáadásához ismételje meg az előző lépéseket az egyes paraméterekhez.

1. Miután hozzáadja a metódus összes paraméterét, adja meg ezeket a tulajdonságokat az egyes paraméterekhez:

   | Tulajdonság neve | Lehetséges értékek | 
   |---------------|-----------------|
   | **Adattípus** | Bájt,Dátumidő,Decimális, Int, Hosszú, Rövid, Karakterlánc |
   | **Mezőkitöltési technika** | A paraméterek támogatják ezeket a kitöltési típusokat, szükség esetén üres ekkel töltve: <p><p>- **Írja be:** Írja be egymás után a karaktereket a mezőbe. <p>- **Kitöltés**: Cserélje le a mező tartalmát karakterekre, szükség esetén üres helyekkel kitöltve. <p>- **EraseEofType**: Törölje a mezőt, majd írjon be egymás után karaktereket a mezőbe. |
   | **Karakterlánc formázása** | Egyes paraméteradattípusok formátumkarakterláncot használnak, amely tájékoztatja a 3270-es összekötőt arról, hogyan konvertálja a szöveget a képernyőről .NET adattípussá: <p><p>- **DateTime**: A DateTime formátumú karakterlánc a [.NET egyéni dátum- és időformátum-karakterláncokat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)követi. A dátum `06/30/2019` például a `MM/dd/yyyy`formátumkarakterláncot használja. <p>- **Decimális**: A decimális formátumú karakterlánc a [COBOL Picture záradékot](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html)használja. A szám `100.35` például a `999V99`formátumkarakterláncot használja. |
   |||

## <a name="save-and-view-metadata"></a>Metaadatok mentése és megtekintése

A módszer meghatározása után, de a módszer tesztelése előtt mentse az eddig megadott összes információt egy RAP (.rap) fájlba.
A RAP-fájlba bármikor menthető bármely üzemmódban. A tervezőeszköz tartalmaz egy minta RAP fájlt is, amelyet megnyithat és áttekinthet a tervezőeszköz telepítési mappájának ezen a helyen történő böngészésével és a "WoodgroveBank.rap" fájl megnyitásával:

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Ha azonban megpróbálja menteni a minta RAP-fájl módosításait, vagy hidx-fájlt hoz létre a minta RAP-fájlból, miközben a fájl a tervezőeszköz telepítési mappájában marad, előfordulhat, hogy "hozzáférés megtagadva" hibaüzenet jelenik meg. Alapértelmezés szerint a tervezőeszköz emelt szintű engedélyek nélkül települ a Program Files mappába. Ha hibaüzenetet kap, próbálkozzon az alábbi megoldásokkal:

* Másolja a mintafájlt egy másik helyre.
* Futtassa a tervezőeszközt rendszergazdaként.
* Tegye magát az SDK mappa tulajdonosának.

## <a name="test-your-method"></a>A módszer tesztelése

1. Ha a módszert az élő állomáson szeretné futtatni, miközben még metódus módban van, nyomja le az F5 billentyűt, vagy a tervezőeszköz eszköztárán válassza a **Futtatás lehetőséget.**

   > [!TIP]
   > A módokat bármikor módosíthatja. A **Fájl menüben** válassza a **Mód**parancsot, majd válassza ki a kívánt módot.

1. Adja meg a paraméterek értékeit, és válassza **az OK gombot.**

1. A következő képernyő folytatásához válassza a **Tovább gombot.**

1. Ha végzett, válassza a **Kész**lehetőséget, amely a kimeneti paraméter értékeit jeleníti meg.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>HIDX-fájl létrehozása és feltöltése

Ha készen áll, hozza létre a HIDX-fájlt, hogy feltölthesse az integrációs fiókjába. A 3270 Tervező eszköz a HIDX-fájlt egy új almappában hozza létre, ahová a RAP-fájlt mentette.

1. A 3270 Tervező eszköz eszköztárán válassza a **Kód létrehozása lehetőséget.**

1. Nyissa meg a RAP-fájlt tartalmazó mappát, és nyissa meg azt az almappát, amelyet az eszköz a HIDX-fájl létrehozása után hozott létre. Ellenőrizze, hogy az eszköz létrehozta-e a HIDX-fájlt.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és keresse meg az integrációs fiókot.

1. Adja hozzá hidx fájlját leképezésként az integrációs fiókhoz a [térképek hozzáadásához szükséges hasonló lépések végrehajtásával,](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)de amikor kiválasztja a térkép típusát, válassza a **HIDX**lehetőséget.

A témakör későbbi részében, amikor először ad hozzá egy IBM 3270-es műveletet a logikai alkalmazáshoz, a rendszer kéri, hogy hozzon létre kapcsolatot a logikai alkalmazás és a gazdakiszolgáló között a kapcsolati adatok megadásával, például az integrációs fiók és a gazdakiszolgáló nevével. . A kapcsolat létrehozása után kiválaszthatja a korábban hozzáadott HIDX-fájlt, a futtatandó módszert és a használandó paramétereket.

Amikor befejezi ezeket a lépéseket, használhatja a logikai alkalmazásban létrehozott műveletet az IBM nagyszámítógéphez való csatlakozáshoz, az alkalmazás meghajtóképernyőihez való csatlakozáshoz, az adatok beírásához, az eredmények visszaadásához és így tovább. A logikai alkalmazáshoz további műveleteket is adhat más alkalmazásokkal, szolgáltatásokkal és rendszerekkel való integrációhoz.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>IBM 3270 műveletek futtatása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Az utolsó lépés alatt, ahol műveletet szeretne hozzáadni, válassza az **Új lépés**lehetőséget, és válassza a **Művelet hozzáadása**lehetőséget. 

1. A keresőmező alatt válassza az **Enterprise**lehetőséget. A keresőmezőbe írja be szűrőként a "3270" értéket. A műveletlistából válassza ezt a műveletet: **Nagyszámítógépes program futtatása TN3270 kapcsolaton keresztül**

   ![3270-es művelet kiválasztása](./media/connectors-create-api-3270/select-3270-action.png)

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. 
   Válassza ki a**+** megjelenő pluszjelet ( ), majd válassza **a Művelet hozzáadása**lehetőséget.

1. Ha még nincs kapcsolat, adja meg a kapcsolathoz szükséges adatokat, és válassza a **Létrehozás gombot.**

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Kapcsolat neve** | Igen | <*kapcsolat neve*> | A kapcsolat neve |
   | **Integrációs fiók azonosítója** | Igen | <*integrációs fiók név*> | Az integrációs fiók neve |
   | **Integrációs fiók SAS URL-címe** | Igen | <*integráció-fiók-SAS-URL*> | Az integrációs fiók megosztott hozzáférésű aláírása (SAS) URL-címe, amely az integrációs fiók beállításaiból generálható az Azure Portalon. <p>1. Az integrációs fiók **menübeállítások**területén válassza a **Visszahívás URL-címét.** <br>2. A jobb oldali ablaktáblában másolja a **Generated Callback URL-értékét.** |
   | **Kiszolgáló** | Igen | <*TN3270-kiszolgáló-név*> | A TN3270 szolgáltatás kiszolgálóneve |
   | **Port** | Nem | <*TN3270-kiszolgáló-port*> | A TN3270 kiszolgáló által használt port. Ha üresen hagyja, `23` az összekötő lesz az alapértelmezett érték. |
   | **Eszköz típusa** | Nem | <*IBM-terminál-modell*> | Az IBM terminál emulálási modellneve vagy száma. Ha üresen hagyja, az összekötő az alapértelmezett értékeket használja. |
   | **Kódlap** | Nem | <*kód-oldalszám*> | Az állomás kódlapszáma. Ha üresen hagyja, `37` az összekötő lesz az alapértelmezett érték. |
   | **Logikai egység neve** | Nem | <*logikai egység neve*> | Az állomástól kért logikai egység neve |
   | **Engedélyezi az SSL-t?** | Nem | Be- vagy kikapcsolása | A TLS-titkosítás be- és kikapcsolása. |
   | **Ellenőrzi az állomás ssl-tanúsítványát?** | Nem | Be- vagy kikapcsolása | A kiszolgáló tanúsítványának érvényesítése be- és kikapcsolása. |
   ||||

   Példa:

   ![Kapcsolat tulajdonságai](./media/connectors-create-api-3270/connection-properties.png)

1. Adja meg a művelethez szükséges információkat:

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Hidx neve** | Igen | <*HIDX-fájlnév*> | Jelölje ki a használni kívánt 3270-es HIDX-fájlt. |
   | **Metódus neve** | Igen | <*metódusneve*> | Jelölje ki a használni kívánt HIDX-fájlban lévő metódust. Miután kiválasztott egy módszert, megjelenik az **Új paraméter hozzáadása** lista, így kiválaszthatja az adott módszerrel használandó paramétereket. |
   ||||

   Példa:

   **A HIDX-fájl kijelölése**

   ![HIDX-fájl kijelölése](./media/connectors-create-api-3270/select-hidx-file.png)

   **Válassza ki a módszert**

   ![Módszer kiválasztása](./media/connectors-create-api-3270/select-method.png)

   **A paraméterek kiválasztása**

   ![Paraméterek kiválasztása](./media/connectors-create-api-3270/add-parameters.png)

1. Ha elkészült, mentse és futtassa a logikai alkalmazást.

   Miután a logikai alkalmazás futása befejeződik, a futtatáslépései megjelennek. 
   A sikeres lépésekben pipák láthatók, míg a sikertelen lépések az "X" betűt mutatják.

1. Az egyes lépcsők bemenetei és kimenetei áttekintéséhez bontsa ki ezt a lépést.

1. A kimenetek áttekintéséhez válassza a **Nyers kimenetek megtekintése**lehetőséget.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részleteket, például az eseményindítókat, műveleteket és korlátokat az összekötő Swagger-fájlja szerint lásd az [összekötő referencialapján.](https://docs.microsoft.com/connectors/si3270/)

> [!NOTE]
> [Az integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
