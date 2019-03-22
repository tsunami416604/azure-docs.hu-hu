---
title: Csatlakozhat az IBM Nagyszámítógépek az Azure-ral – Azure Logic Apps alkalmazások 3270
description: Integráció és automatizálás 3270 képernyő adatvezérelt alkalmazások az Azure-ral IBM 3270 és az Azure Logic Apps-összekötő használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 7388dc0c61dad9c31da0c178febcee4c8481bc50
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313192"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>IBM Nagyszámítógépek 3270 képernyő adatvezérelt alkalmazások integrálása az Azure-ral IBM 3270 és az Azure Logic Apps-összekötő használatával

> [!NOTE]
> Összekötő [ *nyilvános előzetes verzióban*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Az Azure Logic Apps és az IBM 3270 összekötő elérheti, és általában 3270 emulátor képernyők a meghajtó IBM nagyszámítógépes alkalmazások futtatása. Ezzel a módszerrel integrálható az IBM nagyszámítógépes alkalmazások az Azure, Microsoft, és más alkalmazások, szolgáltatások és rendszerek automatizált munkafolyamatok létrehozásával az Azure Logic Apps. Az összekötő az IBM Nagyszámítógépek TN3270 protokoll használatával kommunikál, és az Azure Government és Azure China 21Vianet kivételével az összes Azure Logic Apps régiókban érhető el. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Ez a cikk ismerteti a 3270 connector használata az ezeket a szempontokat: 

* Miért érdemes használni az Azure Logic Apps és az összekötő működésével 3270 képernyő adatvezérelt alkalmazások az IBM 3270-összekötő

* Az előfeltételeket és a beállítása az 3270-összekötővel

* A lépéseket, hogyan adhat hozzá a logikai alkalmazás 3270 összekötő-műveletek

## <a name="why-use-this-connector"></a>Miért érdemes használni ezt az összekötőt?

IBM Nagyszámítógépek alkalmazások eléréséhez 3270 terminálemulátorral, más néven "zöld képernyő" általában használják. Ez a módszer egy tesztelt módja, de korlátozások vonatkoznak. Bár a Host Integration Server (HIS) segítségével az ügyfelek közvetlenül ezeket az alkalmazásokat, egyes esetekben a képernyő és az üzleti logika szétválasztása nem lehetséges. Vagy esetleg már nem rendelkezik a gazdagép alkalmazások működéséről az adatokat.

Kiterjeszti ezeket a forgatókönyveket, a 3270 tervezési eszközt, amely a rekordot vagy "capture" használja, az adott feladathoz használt gazdagép képernyők együttműködik az Azure Logic Appsben az IBM 3270 összekötő, határozzák meg navigációs az adott feladathoz a nagyszámítógépes alkalmazáson keresztül, és határozza meg a a feladat bemeneti és kimeneti paraméterekkel módszerek. A tervezési eszköz metaadatokat, amelyek a 3270 összekötőt használja, amikor ezt a feladatot, amely egy művelet meghívása jelöli a logikai alkalmazás alakítja át az információkat.

Miután a tervezési eszköz-ből generál a metaadatait tartalmazó fájl, ezt a fájlt hozzá az Azure-beli integrációs fiókot. Ily módon a logikai alkalmazás hozzáférhet az alkalmazás metaadatai 3270 összekötő művelet hozzáadása. Az összekötő beolvassa az integrációs fiók a metaadatait tartalmazó fájl navigációs a 3270 képernyők keresztül kezeli, és dinamikusan bemutatja a connector 3270 művelet paramétereit. Majd adja meg a gazdaalkalmazást az adatokat, és az összekötő az eredményeket adja vissza a logikai alkalmazáshoz. Ezzel a módszerrel az örökölt alkalmazásokat integrálhatja az Azure, Microsoft, és más alkalmazások, szolgáltatások és Azure Logic Apps támogató rendszerek.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Ajánlott: Egy [integrációs service-környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Ez a környezet létrehozásának és a logikai alkalmazás futtatásának helyeként választhat. Az ISE-ben az Azure virtuális hálózataiban lévő védett erőforrások a logikai alkalmazás hozzáférést biztosít.

* A logikai alkalmazás automatizálása és futtatása a 3270 képernyő modellalapú alkalmazást

  Az IBM 3270 összekötő eseményindítók nem rendelkezik, ezért egy eseményindítót, indítsa el a logikai alkalmazást, mint például a **ismétlődési** eseményindító. Ezután hozzáadhatja 3270 összekötő-műveletek. Első lépésként [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Az ISE használata esetén válassza ki azt, hogy az ISE-ben, a logikai alkalmazás helye.

* [Töltse le és telepítse a 3270 tervezési eszköz](https://aka.ms/3270-design-tool-download).
Az egyetlen előfeltétel, [a Microsoft .NET-keretrendszer 4.6.1-es](https://aka.ms/net-framework-download).

  Ez az eszköz segít rögzítse a képernyők, navigációs útvonalak, módszerek és a tevékenységek paramétereinek hozzáadása és futtató 3270 összekötő-műveletek az alkalmazásban. Az eszköz létrehoz egy gazdagép integrációs Designer XML (HIDX) fájlt, amely biztosítja a szükséges metaadatokat az összekötő használatához a nagyszámítógépes alkalmazását.
  
  Letöltötte, és ezt az eszközt telepítette, kövesse az alábbi lépéseket a gazdagépen való csatlakozáshoz:

  1. Nyissa meg a 3270 tervezési eszköz. Az a **munkamenet** menüjében válassza **gazdagép munkamenetek**.
  
  1. Adja meg a TN3270 gazdagép kiszolgáló adatait.

* Egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), amely az, hogy a hely, ahol ezt a HIDX fájlt tárolja egy térképen, a logikai alkalmazás férhet hozzá a metaadatokat és metódus-definíciókat, az adott fájlban. 

  Győződjön meg arról, hogy az integrációs fiók össze van kapcsolva a logikai alkalmazás használja. Is ha használja az ISE-ben, győződjön meg arról az integrációs fiók hely a azonos ISE-ben, amely a logikai alkalmazás használja.

* A nagyszámítógépes alkalmazások üzemeltető TN3270-kiszolgáló hozzáférését

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Hozzon létre a metaadatok – áttekintés

Egy 3270 képernyő modellalapú alkalmazást, az a képernyők és datová Pole egyediek a forgatókönyvek, így a 3270 összekötő ezt az alkalmazást, amely formájában metaadatokat adhat információkra lesz szüksége. A metaadatok, amely segít azonosítani és felismerni a képernyők a logikai alkalmazás, ismerteti, hogyan lehet Navigálás a képernyők, között információkat ismerteti, hogy a bemeneti adatok hol, valamint a várható eredmények helyét. Adja meg, és hozzon létre a metaadatok, használhatja a 3270 tervezési eszközt, amely végigvezeti ilyen specifikus *módok*, vagy szakaszokban részletesebben leírtak szerint:

* **Rögzítés**: Ebben a módban jegyezze fel a bank egyenleg első a képernyők a nagyszámítógépes alkalmazások, például az egy adott feladat elvégzése szükséges.

* **Navigációs**: Ebben a módban adja meg a terv vagy elérési útja, hogy hogyan navigálnak a nagyszámítógépes alkalmazások képernyők az adott feladathoz.

* **metódusok**: Ebben a módban határoz meg a módszer, például `GetBalance`, amely leírja, hogy a képernyő navigációs útvonal. Azt is választhatja a mezőket az egyes képernyőkre válnak a metódus a bemeneti és kimeneti paraméterek.

### <a name="unsupported-elements"></a>Nem támogatott elemeket.

A tervezési eszköz nem támogatja ezeket az elemeket:

* IBM alapszintű leképezési támogatási (BMS) leképezések részleges: Ha importál egy BMS térképet, a tervezési eszköz figyelmen kívül hagyja a részleges képernyő definíciókat.
* Bejövő és kimenő paraméterek: Bejövő és kimenő paraméter nem lehet definiálni.
* Feldolgozás menüben: Az előzetes verzióban nem támogatott
* Feldolgozás tömb: Az előzetes verzióban nem támogatott

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Képernyő rögzítése

Ebben a módban egy elem, amely egyedileg azonosítja az adott képernyő 3270 képernyőn megjelenő jelölje meg. Például hogy előfordulhat, hogy megadja egy szövegsor vagy egy összetettebb feltételek, például a megadott szöveg és a egy nem üres mező. Jegyezze fel ezeket a képernyőket a gazdagép-kiszolgálón való élő kapcsolaton keresztül, vagy ezek az információk importálása az IBM alapszintű leképezési támogatási (BMS) térképet. Az élő kapcsolat a gazdagép csatlakozik a TN3270 emulátor használ. Minden összekötő műveletet kell rendelni egy feladat, amely csatlakozik a munkamenet kezdődik és végződik a munkamenet leválasztása.

1. Ha még nem tette, nyissa meg a 3270 tervezési eszköz. Az eszköztáron válassza **rögzítése** úgy, hogy Ön adja meg a rögzítési módot.

1. Rögzítés elindításához nyomja le az F5 billentyűt, vagy a **rögzítése** menüjében válassza **Rögzítés indítása**. 

1. Az a **munkamenet** menüjében válassza **Connect**.

1. Az a **rögzítése** ablaktáblán, az első képernyőn az alkalmazásba, az alkalmazás az adott feladat, amely rögzítése közben lépésenként indítása.

1. Miután elvégezte a feladat, jelentkezzen ki az alkalmazásból módon.

1. Az a **munkamenet** menüjében válassza **Disconnect**.

1. Rögzítés leállítása, nyomja le a Shift + F5 kulcsok, vagy a **rögzítése** menüjében válassza **rögzítés**.

   Miután rögzíti a képernyők egy tevékenységhez, a Tervező eszköz megjeleníti, amelyek ezeken a képernyőkön miniatűrök. Ezek a miniatűrök néhány megjegyzések:

   * A rögzített képernyők mellékelt, hogy "Üres" nevű képernyő.

     Ha először csatlakozik [CICS](https://www.ibm.com/it-infrastructure/z/cics), el kell küldenie a "Törlés" kulcsot, mielőtt adhatja meg a futtatni kívánt tranzakció neve. A képernyő, ahol elküldheti a "Törlés" kulcs nem rendelkezik ilyennel *felismerés attribútumok*, például a képernyő címének, amelyeket hozzáadhat a képernyőn beszédfelismerést szerkesztővel. Ezen a képernyőn jelölésére, a miniatűrök "Üres" nevű képernyő tartalmazza. Később használható ezt a képernyőt a képernyő jelölő, írja be a tranzakció neve.

   * Alapértelmezés szerint egy rögzített képernyő nevét használja az első szótól a képernyőn. Ha már létezik ilyen nevű, a tervezési eszköz hozzáfűzi a nevét, egy alulhúzásból és egy számot, például "WBGB" és "WBGB_1".

1. Nevezze el jobban leírja egy rögzített képernyőre, kövesse az alábbi lépéseket:

   1. Az a **gazdagépet képernyőket** ablaktáblán jelölje ki a képernyőt szeretné nevezni.

   1. Ugyanazt a panelt, a lap alsó részén található azonos ablaktábláján keresse meg a **képernyő nevét** tulajdonság.

   1. Módosítsa az aktuális képernyő nevét egy leíró nevet.

1. Most adja meg az egyes képernyőkre azonosítására szolgáló mezőket.

   A 3270 adatfolyam a képernyők nem rendelkezik alapértelmezett azonosítók, így ki kell választania az egyes képernyőkre egyedi szöveg. Összetett forgatókönyvek esetén több feltétel, például a egyedi szöveg és a egy mezőt egy adott feltételt is megadhat.

Miután elvégezte a felismerés mezők kiválasztása, helyezze át a következő módot.

### <a name="conditions-for-identifying-repeated-screens"></a>Ismétlődő képernyők azonosítására szolgáló feltételeket

Az összekötő és a képernyők közötti különbséget, akkor általában egyedi szöveg keresése a rögzített képernyők közötti azonosítóként használható képernyőn. Ismétlődő képernyők szükség lehet további azonosítási módszerek. Tegyük fel például, két képernyő, amely néznek ki, kivéve az egyik képernyőn egy érvényes értéket ad vissza, míg a másik képernyőn hibaüzenetet ad vissza.

A tervezési eszköz adhat hozzá *felismerés attribútumok*, például a képernyő címének, például a "Get fiókja egyenlege", a képernyőn beszédfelismerést szerkesztővel. Ha egy elágaztatott elérési utat, és mindkét ágak vissza ugyanazon a képernyőn, de különböző eredményekkel, más felismerés attribútumok szüksége van. Futási időben az összekötő ezek az attribútumok használja az aktuális ág és elágazás meghatározására. Az alábbiakban a feltételek is használhatja:

* Adott érték: Ez az érték megegyezik a megadott karakterlánc, a megadott helyen.
* Adott érték: Ez az érték nem egyezik a megadott karakterlánc, a megadott helyen.
* Üres: Ez a mező je prázdná.
* NEM lehet üres: Ez a mező nem számít üresnek.

További tudnivalókért tekintse meg a [példa navigációs terv](#example-plan) jelen témakör későbbi részében.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Navigációs tervek meghatározása

Ebben a módban adja meg a folyamat vagy végiglépkedhet az adott feladathoz a nagyszámítógépes alkalmazások képernyők lépéseit. Például egyes esetekben előfordulhat, hogy rendelkezik több elérési úthoz, amely az alkalmazás is igénybe vehet, ahol egy útvonal a megfelelő eredményt adó, amíg az elérési út hibát ad vissza. Az összes megjelenő képernyőn adja meg a szükséges helyezi át a következő képernyőn, például a billentyűleütések `CICSPROD <enter>`.

> [!TIP]
> Ha több feladatot, amely a azonos csatlakozás, és válassza le a képernyő még automatizálása, a tervezési eszköz biztosít speciális csatlakozás és a leválasztás energiaséma-típusokat. Az alábbi díjcsomagok definiálásakor felveheti őket a navigációs terv elején és végén.

### <a name="guidelines-for-plan-definitions"></a>Útmutató a terv definíciók

* Például minden képernyőjén, csatlakoztatása és leválasztása a kezdve.

* Hozzon létre egy önálló csomagot, vagy használja a csatlakozás és a leválasztás tervek, amely is felhasználhatja a képernyők közös összes tranzakció.

  * A legutóbbi képernyő a Connect terv ugyanazon a képernyőn az első képernyőt a navigációs terv kell lennie.

  * A leválasztás terv első képernyője ugyanazon a képernyőn az utolsó képernyőt a navigációs terv kell lennie.

* A rögzített képernyők előfordulhat, hogy számos ismétlődő képernyők tartalmazhat így válassza ki, minden ismétlődő képernyők csak egyetlen példányát használja a csomagban. Íme néhány példa az ismétlődő képernyők:

  * A bejelentkezési képernyőn, például a **MSG – 10** képernyő
  * Az üdvözlőképernyőn CICS számára
  * A "tiszta" vagy **üres** képernyő

<a name="create-plans"></a>

### <a name="create-plans"></a>Csomagok létrehozása

1. A 3270 tervezési eszköz eszköztáron válassza **navigációs** úgy, hogy a navigációs üzemmódba.

1. A csomag a elindításához a **navigációs** panelen válassza a **új csomag**.

1. A **válasszon új csomag neve**, adja meg a csomag nevét. Az a **típus** listájához, válassza ki a csomag típusa:

   | Az adatforgalmi díjcsomag típusa | Leírás |
   |-----------|-------------|
   | **Folyamat** | Önálló vagy egyesített csomagok |
   | **Kapcsolódás** | Connect-csomagok |
   | **Kapcsolat bontása** | Kapcsolat bontása-csomagok |
   |||

1. Az a **gazdagépet képernyőket** panelen kattintson a jobb a rögzített miniatűrök, a navigációs terv a surface húzza a **navigációs** ablaktáblán.

   Megjeleníteni az üres képernyő, ahol a tranzakció nevét adja meg, használja a "Üres" képernyő.

1. A képernyő, amely leírja a feladatot, amelyet a sorrendben rendezheti.

1. Határozza meg a flow elérési képernyők, többek között a elágazásoknak, és összekapcsolja a tervezési eszköz eszköztárában között válassza a **folyamat**.

1. Az első képernyőn válassza ki a folyamatot. Húzza át, és felhívja a kapcsolat a következő képernyőn, a folyamat.

1. Az összes megjelenő képernyőn, adja meg az értékeket a **támogatási kulcs** tulajdonság (figyelmet-azonosító) és a **rögzített szöveg** tulajdonság, amely a folyamat helyezi át a következő képernyőn.

   Előfordulhat, hogy csak a támogatási kulcsot vagy a támogatási kulcs és rögzített szöveg.

Miután befejezte a navigációs tervet, [metódusok meghatározásához a következő módban](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Példa

Ebben a példában tegyük fel, hogy egy CICS tranzakció nevű, "WBGB", amely rendelkezik a következő lépéseket futtatja: 

* Az első képernyőn adjon meg egy nevet és a egy fiókot.
* A második képernyőn a fiókja egyenlege kap.
* Lépjen ki az "Üres" képernyőre.
* Kijelentkezik a CICS "MSG – 10" képernyőhöz.

Feltételezve, hogy ismételje meg a lépéseket, de helytelen adatokat adja meg, rögzítheti a képernyőn, és a hibaüzenet látható. Az alábbiakban a képernyők rögzíti:

* MSG-10
* Üdvözli a CICS
* Üres
* WBGB_1 (input)
* WBGB_2 (hiba)
* Empty_1
* MSG-10_1

Itt számos képernyők beolvasni az egyedi nevét, bár egyes képernyők ugyanazon a képernyőn, például "MSG – 10" és "Üres". Ismétlődő képernyő használja csak egy példány a képernyő a csomagban. Az alábbiakban a példák azt mutatják be, hogyan nézhet önálló csomag vásárlása, Connect terv, Disconnect-csomagot és egy kombinált terv:

* Önálló csomag

  ![Önálló navigációs csomag](./media/connectors-create-api-3270/standalone-plan.png)

* Csatlakozás a terv

  ![Csatlakozás a terv](./media/connectors-create-api-3270/connect-plan.png)

* Válassza le a csomag

  ![Válassza le a csomag](./media/connectors-create-api-3270/disconnect-plan.png)

* Összetett tervnek

  ![Összetett tervnek](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Példa: Ismétlődő képernyők azonosítása

Keresse meg és tesz különbséget a képernyők az összekötőhöz, általában egyedi szöveg keresése a képernyő, amely lehetővé teszi, az azonosítót a rögzített képernyők között. Ismétlődő képernyők szükség lehet további azonosítási módszerek. A példa terv egy elágazás rendelkezik, ahol megtekintheti a képernyők, amelyek hasonlóak. A képernyőn látható a fiókja egyenlege adja vissza, míg a másik képernyőn hibaüzenetet ad vissza.

A tervezési eszköz lehetővé teszi, hogy felismerési attribútumok hozzáadását, például a képernyő címének neve "Első fiókja egyenlege", a képernyőn beszédfelismerést használatával szerkesztő. Abban az esetben a hasonló képernyő egyéb attribútumokkal kell. Futási időben az összekötő ezek az attribútumok használja a fiókiroda és elágazás meghatározására.

* Az ág által visszaadott érvényes bemenet, amely a képernyő és a fiókja egyenlege, hozzáadhat olyan mező, amely egy "üres" feltételt.

* Az ág, amely hibát ad vissza hozzáadhat olyan mező, amely az "üres" feltételt.

<a name="define-method"></a>

## <a name="define-methods"></a>Metódusok meghatározásához

Ebben a módban megadhat olyan módszer, amely a navigációs csomagban vannak társítva. Minden módszer paraméterrel adja meg az adatok típusát, például egy karakterlánc, egész szám, dátum vagy idő, és így tovább. Ha elkészült, a metódus az élő gazdagépen tesztelése, és győződjön meg arról, hogy a metódus a várt módon működik-e. Ezután hozza létre a metaadatait tartalmazó fájl, vagy a Host Integration Designer XML (HIDX) fájlt, amely most már rendelkezik a metódus definíciók létrehozására és futtatására egy műveletet, az IBM 3270 összekötő használatára.

1. A 3270 tervezési eszköz eszköztáron válassza **módszerek** úgy, hogy módszerek üzemmódba. 

1. Az a **navigációs** ablaktáblán jelölje ki a képernyőt, amely rendelkezik a kívánt beviteli mezőket.

1. A metódus első bemeneti paraméter hozzáadásához kövesse az alábbi lépéseket:

   1. Az a **rögzítése** panelen 3270 emulátor képernyőn válassza a teljes mező, nem csak szöveg a mezőbe, hogy szeretné-e az első bemenetként.

      > [!TIP]
      > Megjeleníti az összes mezőt, és győződjön meg arról, hogy a válassza ki a teljes mezőt, a **nézet** menüjében válassza **minden mező**.

   1. A tervezési eszköz eszköztáron válassza **beviteli mező**. 

   Több bemeneti paraméterek hozzáadásához ismételje meg a fenti lépéseket mindegyik paraméterhez.

1. A metódus az első kimeneti paraméter hozzáadásához kövesse az alábbi lépéseket:

   1. Az a **rögzítése** panelen 3270 emulátor képernyőn válassza a teljes mező, nem csak szöveg a mezőbe, hogy szeretné-e az első kimenetként.

      > [!TIP]
      > Megjeleníti az összes mezőt, és győződjön meg arról, hogy a válassza ki a teljes mezőt, a **nézet** menüjében válassza **minden mező**.

   1. A tervezési eszköz eszköztáron válassza **kimeneti mező**.

   Több kimeneti paraméterek hozzáadásához ismételje meg a fenti lépéseket mindegyik paraméterhez.

1. Miután hozzáadta a metódus-paraméterek, adja meg ezeket a tulajdonságokat, az egyes paraméterekhez tartozó:

   | Tulajdonság neve | Lehetséges értékek | 
   |---------------|-----------------|
   | **Adattípus** | Dátum-idő, Decimal, Int,-bájt hosszú, röviden, karakterlánc |
   | **A mező kitöltése módszer** | Paraméterek kitöltésének típusaival, szükség esetén az üres cellák kitöltése támogatják: <p><p>- **Típus**: Meg egymás után a mezőbe. <p>- **Töltse ki**: Cserélje le a mező tartalmát karaktereket, kitöltését az üres, ha szükséges. <p>- **EraseEofType**: Törölje a mezőt, és ezután meg egymás után a mezőbe. |
   | **Formázó karakterlánc** | Néhány paraméter adattípusok használata egy formázó karakterlánc, amely tájékoztatja a 3270 összekötő szöveg konvertálása a képernyőn egy .NET adattípussá: <p><p>- **DateTime**: A dátum és idő formátumú karakterlánc követi a [.NET egyéni dátum és idő formázása karakterláncok](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Ha például a dátum `06/30/2019` használja a formázó karakterlánc `MM/dd/yyyy`. <p>- **Tizedes tört**: A decimális formátum-karakterláncot használ a [COBOL kép záradék](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Például a szám `100.35` használja a formázó karakterlánc `999V99`. |
   |||

## <a name="save-and-view-metadata"></a>Mentse és metaadatainak megtekintése

A módszer meghatározása után, de a metódus tesztelése előtt, mentse az összes információt, amelyet eddig megadott RAP (.rap) fájlba.
Ez a fájl RAP módban során bármikor mentheti. A tervezési eszköz, amely megnyithat, és keresse meg a tervezési eszköz telepítési mappa ezen a helyen, és a "WoodgroveBank.rap" fájl megnyitásakor áttekintéséhez RAP mintafájl is tartalmazza:

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Azonban ha a RAP-mintafájlt vagy egy HIDX fájl létrehozása a minta RAP fájlból, miközben a tervezési eszköz telepítési mappában marad a fájl módosításainak mentése, előfordulhat, hogy "hozzáférés megtagadva" hibaüzenetet kap. Alapértelmezés szerint a tervezési eszköz települ, emelt szintű engedélyek nélkül a Program Files mappában található. Ha hibaüzenetet kap, próbálja meg e megoldások egyikének:

* A minta-fájl másolása másik helyre.
* A tervezési eszköz futtassa rendszergazdaként.
* Győződjön meg arról, saját magának az SDK mappából a tulajdonosa.

## <a name="test-your-method"></a>A metódus tesztelése

1. Az élő állomás, miközben továbbra is módszerek módban, a módszer futtatásához nyomja le az F5 billentyűt, vagy a tervezési eszköz eszköztárán válassza a **futtatása**.

   > [!TIP]
   > Módok bármikor módosíthatja. Az a **fájl** menüjében válassza **mód**, majd válassza ki a kívánt módot.

1. Adja meg a paraméterek értékei, és válassza a **OK**.

1. Továbbra is a következő képernyő, válassza a **tovább**.

1. Ha elkészült, válassza ki a **kész**, amely jelzi, hogy a kimeneti paraméter értékeit.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Hozzon létre és HIDX fájl feltöltése

Amikor készen áll, hozza létre a HIDX fájlt, így feltöltheti az integrációs fiókba. A 3270 tervezési eszköz létrehoz egy új almappát, ahová mentette a RAP-fájl a HIDX fájlt.

1. A 3270 tervezési eszköz eszköztáron válassza **kód előállítása**.

1. Nyissa meg a RAP fájlt tartalmazó mappát, és nyissa meg az almappát, az eszközt hozott létre a HIDX fájl létrehozása után. Győződjön meg arról, hogy az eszköz hozza létre a HIDX fájlt.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és keresse meg az integrációs fiókban.

1. Adja hozzá a HIDX fájlt egy térképen az integrációs fiókba által [hozzáadásához a maps ezeket hasonló lépésekkel](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), hozzárendelés-típust választja, válassza ki, de **HIDX**.

Ez a témakör későbbi részében, IBM 3270 művelet hozzáadása a logikai alkalmazáshoz első alkalommal, amikor kéri, hogy a logikai alkalmazás és a gazdagép-kiszolgáló közötti kapcsolat létrehozása azáltal, hogy kapcsolati információkat, például az integrációs fiók és a gazdagép-kiszolgáló nevét . Miután létrehozta a kapcsolatot, választhatja a korábban hozzáadott HIDX fájl, a metódus végrehajtásához és a használandó paramétereket.

Amikor befejezte az ezeket a lépéseket, a művelettel a logikai alkalmazást, az IBM Nagyszámítógépek, a meghajtó képernyőt az alkalmazáshoz való kapcsolódáshoz szolgáltatásban létrehozott, adja meg az adatokat, adja vissza az eredményeket, és így tovább. Emellett továbbra is más műveletek hozzáadása a logikai alkalmazáshoz, a más alkalmazások, szolgáltatások és rendszerek integrálásához.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Run IBM 3270 actions

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Válassza ki az utolsó lépésnél, ahová a művelet hozzáadása, **új lépés**, és válassza ki **művelet hozzáadása**. 

1. A keresőmező alatt válassza ki a **vállalati**. A Keresés mezőbe írja be szűrőként "3270". A műveletek listából válassza a következő műveletet: **A nagyszámítógépes programot futtató TN3270 kapcsolaton keresztül**

   ![3270 művelet kiválasztása](./media/connectors-create-api-3270/select-3270-action.png)

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. Ha nincs kapcsolat még létezik, adja meg a szükséges információkat a kapcsolat, és válassza a **létrehozás**.

   | Tulajdonság | Szükséges | Value | Leírás |
   |----------|----------|-------|-------------|
   | **Kapcsolat neve** | Igen | <*kapcsolat neve*> | A kapcsolat neve |
   | **Integrációs fiók azonosítója** | Igen | <*integration-account-name*> | Az integrációs fiók nevét |
   | **Integrációs fiók SAS URL-címe** | Igen | <*integration-account-SAS-URL*> | Az integrációs fiók közös hozzáférésű Jogosultságkód (SAS) URL-cím, amely is létrehozhat az Azure Portalon az integrációs fiók beállításait. <p>1. A az integrációs fiók menü alatt **beállítások**válassza **visszahívási URL-Címének**. <br>2. A jobb oldali ablaktáblán, másolja a **generált visszahívási URL-Címének** értéket. |
   | **Kiszolgáló** | Igen | <*TN3270-server-name*> | A kiszolgáló nevét a TN3270 szolgáltatás |
   | **Port** | Nem | <*TN3270-server-port*> | A TN3270-kiszolgáló által használt port. Ha üresen hagyja, az összekötő használja `23` az alapértelmezett érték. |
   | **Eszköz típusa** | Nem | <*IBM-terminal-model*> | Az a modell neve vagy az IBM terminál emulálása érdekében. Ha üresen hagyja, az összekötőt az alapértelmezett értékeket használja. |
   | **Znaková Stránka** | Nem | <*code-page-number*> | A kód oldal száma a gazdagép számára. Ha üresen hagyja, az összekötő használja `37` az alapértelmezett érték. |
   | **Logikai egység neve** | Nem | <*logical-unit-name*> | Az állomás kérhet az adott logikai egység neve |
   | **SSL engedélyezése?** | Nem | Be- vagy kikapcsolása | Kapcsolja be, vagy kapcsolja ki az SSL-titkosítást. |
   | **Ellenőrizze a gazdagép ssl-tanúsítvány?** | Nem | Be- vagy kikapcsolása | Kapcsolja be vagy ki a kiszolgálói tanúsítvány érvényesítése. |
   ||||

   Példa:

   ![Kapcsolat tulajdonságai](./media/connectors-create-api-3270/connection-properties.png)

1. Adja meg a művelet a szükséges információkat:

   | Tulajdonság | Szükséges | Value | Leírás |
   |----------|----------|-------|-------------|
   | **Hidx neve** | Igen | <*HIDX-file-name*> | Válassza ki a használni kívánt 3270 HIDX fájlt. |
   | **Metódus neve** | Igen | <*metódus neve*> | Válassza ki a módszert a HIDX fájlt, amelyet használni szeretne. A módszer kiválasztása után a **új paraméter hozzáadása** lista jelenik meg, így a paraméterek a módszer használata. |
   ||||

   Példa:

   **Válassza ki a HIDX fájlt**

   ![HIDX fájl kiválasztása](./media/connectors-create-api-3270/select-hidx-file.png)

   **Válassza ki a módszert**

   ![Módszer kiválasztása](./media/connectors-create-api-3270/select-method.png)

   **Válassza ki a paramétereket**

   ![Válassza ki a paramétereket](./media/connectors-create-api-3270/add-parameters.png)

1. Ha elkészült, mentse, és a logikai alkalmazás futtatásához.

   Után a logikai alkalmazás befejezése fut, a futtatási jelennek meg a lépéseket. 
   Sikeres lépésekkel pipák, miközben sikertelen lépések bemutatják az "x".

1. Tekintse át a bemeneteit és kimeneteit mindegyik lépéshez, bontsa ki a lépés.

1. Tekintse át a kimeneteket, válassza a **tekintse meg a nyers kimenetek**.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek eseményindítók, műveleteket és korlátot, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át a [összekötő referencialapja](/connectors/si3270/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
