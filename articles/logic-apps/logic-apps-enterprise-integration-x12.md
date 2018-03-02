---
title: "Az B2B vállalati integrációs - Azure Logic Apps állapotüzeneteit X12 |} Microsoft Docs"
description: "Exchange X12 üzenetek B2B vállalati integráció az Azure Logic Apps EDI-formátumban"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: c644dd98d468a8c99625c45bad3f06031ff22b4e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="exchange-x12-messages-for-enterprise-integration-with-logic-apps"></a>A logic apps vállalati integrációs Exchange X12 állapotüzenete

Előtt X12 tudjon cserélni az Azure Logic Apps üzenetek, létre kell hoznia egy X12 szerződés és a megállapodás tárolása integrációs fiókját. Arról, hogyan hozzon létre egy X12 lépései a következők szerződést.

> [!NOTE]
> Az Azure Logic Apps lap magában foglalja a X12 funkciói. További információkért lásd: [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Előkészületek

A szükséges elemeket itt található:

* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéshez társított
* Legalább két [partnerek](../logic-apps/logic-apps-enterprise-integration-partners.md) , amely az integráció fiókban definiált és a X12 konfigurált azonosítója alapján **üzleti identitások**    
* Egy szükséges [séma](../logic-apps/logic-apps-enterprise-integration-schemas.md) integrációs fiókjába feltölthető

Miután [integrációs-fiók létrehozása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [adja hozzá a partnerek](logic-apps-enterprise-integration-partners.md), és egy [séma](../logic-apps/logic-apps-enterprise-integration-schemas.md) , hogy a használni kívánt, létrehozhat egy X12 szerződés által a következő lépéseket.

## <a name="create-an-x12-agreement"></a>Hozzon létre egy X12 megállapodás

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com "Azure Portal") 

2. Válassza a fő Azure menü **minden szolgáltatás**. A keresési mezőbe, írja be a "integrációt", és válassza ki **integrációs fiókok**.  

   ![Az integráció fiók](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Ha **minden szolgáltatás** nem jelenik meg, lehetséges, hogy először bontsa ki a menüben. Jelölje be a becsukott menü felső részén **megjelenítése menü**.

3. A **integrációs fiókok**, válassza ki az integráció fiókra, amelyhez való hozzáadásához.

   ![Válassza ki azt az integráció fiókot, a szerződés létrehozásának a helyét](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Válassza ki **áttekintése**, majd jelölje be a **megállapodások** csempére. Ha egy megállapodások csempe nem rendelkezik, először vegye fel a csempe. 

   ![Válassza a "Szerződés" csempe](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. A **megállapodások**, válassza a **Hozzáadás**.

   ![Válassza a "Hozzáadás"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)     

6. A **Hozzáadás**, adjon meg egy **neve** a szerződés. A szerződés típusának kiválasztása **X12**. Válassza ki a **fogadó Partner**, **gazdagép identitását**, **Vendég Partner**, és **Vendég identitás** a szerződés. Tulajdonság kapcsolatos további tudnivalókért lásd a táblázat az ebben a lépésben.

    ![Adja meg a szerződés részletei](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Tulajdonság | Leírás |
    | --- | --- |
    | Name (Név) |A szerződés nevét |
    | A szerződés típusa | X12 kell lennie. |
    | Gazdagéppartner |Egy szerződést kell a gazdagép és a Vendég partner. A fogadó partner szervezet, amely beállítja a szerződés jelöli. |
    | Gazdagép-identitás |A fogadó partner azonosítója |
    | Vendégpartner |Egy szerződést kell a gazdagép és a Vendég partner. A Vendég partnert a szervezet, amely a gazdagép partnerrel üzleti jelöli. |
    | Vendégidentitás |A Vendég partner azonosítója |
    | Fogadási beállítások |Minden szerződés által fogadott üzenetek alkalmazni ezeket a tulajdonságokat. |
    | Küldési beállítások |Ezeket a tulajdonságokat a szerződés által küldött összes üzenet vonatkozik. |  

  > [!NOTE]
  > Szerződés, attól függ, a küldő minősítő és azonosítója, és a fogadó minősítő és a partnerek és a bejövő üzenet definiált azonosítója megfelelő X12 feloldását. Ha ezek az értékek módosítása esetén a partner, a szerződés túl kell frissíteni.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Hogyan a szerződés leírók fogadott üzenetek konfigurálása

Most, hogy a szerződés tulajdonságok beállítása, beállíthatja a módját a jelen szerződés azonosítja, és kezeli a bejövő üzenetek és a szerződés partnerétől kapott.

1.  A **Hozzáadás**, jelölje be **fogadási beállítások**.
Állítsa be ezeket a tulajdonságokat a partnerrel, amely az üzenetek Önnel a szerződés alapján. Lásd: e szakasz táblázatai tulajdonság leírásait.

    **Beállítások** van felosztva ezekben a szakaszokban: azonosítók, visszaigazolás, sémákat, borítékok, vezérlő számok, érvényesítést és belső beállítások.

2. Miután elkészült, ügyeljen arra, hogy a beállítások mentéséhez válasszon **OK**.

Most már a szerződés készen áll a bejövő üzenetek, amelyek megfelelnek a kiválasztott beállítások kezeléséhez.

### <a name="identifiers"></a>Azonosítók

![Azonosító tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Tulajdonság | Leírás |
| --- | --- |
| ISA1 (engedélyezési minősítő) |A legördülő listából válassza ki a minősítő értékét. |
| ISA2 |Választható. Adjon meg engedélyezési adatokat értéket. Ha ISA1 a megadott érték nem a 00, adjon meg legalább egy alfanumerikus karaktert, és legfeljebb 10. |
| ISA3 (biztonsági minősítő) |A legördülő listából válassza ki a biztonsági minősítőt érték. |
| ISA4 |Választható. Adja meg a biztonsági információk értékét. Ha ISA3 a megadott érték nem a 00, adjon meg legalább egy alfanumerikus karaktert, és legfeljebb 10. |

### <a name="acknowledgment"></a>Tudomásul vétele

![Nyugtázási tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Várt TA1 |A műszaki visszaigazolás visszatér a interchange küldő |
| Várt be |A működési visszaigazolás visszatér a interchange küldő. Ezután válassza ki, hogy a 997 vagy 999 visszaigazolások, a séma verziója alapján |
| AK2/IK2 hurkot tartalmaz |Engedélyezi az AK2 hurkok a működési nyugták elfogadott tranzakció beállítása |

### <a name="schemas"></a>Sémák

Válassza ki a sémát minden tranzakció típusa (ST1) és a küldő alkalmazás (GS2). A fogadási folyamat visszafejti a bejövő üzenet az egyező értékeket ST1 és GS2 bejövő üzenetben az itt megadott értékeket, és a séma, a bejövő üzenet, a séma itt.

![Válassza ki a sémát](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Verzió |Válassza ki a X12 verzió |
| Tranzakció típusa (ST01) |A tranzakció típusának kiválasztása |
| Küldő alkalmazás (GS02) |A küldő alkalmazás kiválasztása |
| Séma |Válassza ki a használni kívánt sémafájl. Integráció fiókja sémák kerülnek. |

> [!NOTE]
> Konfigurálja a szükséges [séma](../logic-apps/logic-apps-enterprise-integration-schemas.md) , amely fel van töltve a [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Borítékok

![Adja meg az elválasztó egy tranzakció készlet: válassza a szabványos azonosító vagy ismétlődési elválasztó](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Tulajdonság | Leírás |
| --- | --- |
| ISA11 használata |Egy tranzakció készlet használandó elválasztó határozza meg: <p>Válassza ki **szabványos azonosító** kíván használni egy pontot (.) decimális jelöléssel, ahelyett, hogy a bejövő dokumentum a EDI decimális jelöléssel fogadni a feldolgozási sor. <p>Válassza ki **ismétlődési elválasztó** adhatja meg az elválasztó az egyszerű adatelem vagy ismétlődő adatszerkezet ismételt előfordulását. Például általában a beszúrási (^) szolgál a ismétlődési elválasztójelként. A HIPAA lehetséges csak a beszúrási tudja használni. |

### <a name="control-numbers"></a>Ellenőrzőszámok

![Válassza ki a vezérlő számú ismétlődések kezelésének módját](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Ellenőrző szám Interchange ismétlődések letiltása |Ismétlődő kereszteződéseket blokkolása. A fogadott interchange vezérlő számát ellenőrzi, az adatcsere ellenőrző szám (ISA13). Ha találat esetén elvégez, a fogadási folyamat nem dolgozza fel cseréje. Megadhatja, hogy hány nap adjon értéket az ellenőrzés végrehajtásához *keressen ismétlődő ISA13 (naponta)*. |
| Csoport-ellenőrzőszám ismétlődésének letiltása |Blokk felcserélődések duplikált csoportokhoz vezérlő számokkal. |
| Tranzakciókészlet-ellenőrzőszám ismétlődésének letiltása |Blokk felcserélődések ismétlődő tranzakció set vezérlő számokkal. |

### <a name="validations"></a>Ellenőrzések

![A fogadott üzenetek érvényesítési tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Érvényesítési sorokban befejeződése után, egy másik automatikusan kerül. Ha nem adja meg az összes szabály, érvényesítési használja az "Alapértelmezett" sorhoz.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenettípus |Válassza ki a EDI-üzenet típusa. |
| EDI-ellenőrzés |EDI-érvényesítést hajt végre a séma EDI tulajdonságok, hosszára, üres adatelemek és záró elválasztók által definiált adattípusok. |
| Bővített ellenőrzés |Ha az adattípus nem EDI, érvényesítési az elem követelménynek, és engedélyezett ismétlési, enumerálások és adatok elem hossza érvényesítési (min vagy max). |
| Kezdő/záró nullák engedélyezése |Minden további, kezdő vagy záró nulla, és az szóköz karakter. Ne távolítsa el ezeket a karaktereket. |
| Kezdő/záró nullák levágása |Távolítsa el a kezdő vagy záró nulla és karaktereket. |
| Záróelválasztó-szabályzat |Záró elválasztók készítése. <p>Válassza ki **nem engedélyezett** záró határoló és elválasztók, a kapott interchange. Ha cseréje a záró határoló és elválasztókat, az adatcsere van deklarálva nem érvényes. <p>Válassza ki **nem kötelező** kereszteződéseket vagy záró határoló és elválasztók nélkül fogadásához. <p>Válassza ki **kötelező** amikor adatcsere rendelkeznie kell záró határoló és elválasztókat. |

### <a name="internal-settings"></a>Belső beállítások

![Belső beállítások](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Implicit decimális formátum "Nn" alap 10 numerikus értékké konvertálni |A 10-es számértéket "Nn" formátumban megadott EDI számot alakít át. |
| Üres XML-címkék létrehozása, ha a záró elválasztók engedélyezve vannak |Válassza ezt a jelölőnégyzetet, az adatcsere küldő záró elválasztók üres XML-címkék közé tartoznak hozzá. |
| Adatcsere felosztása tranzakciókészletekre – tranzakciókészletek felfüggesztése hiba esetén|Elemzi az egyes tranzakciókra, megadhatja a azokat egy külön XML-dokumentum cseréje a megfelelő boríték a tranzakció-készlethez. Ha az ellenőrzés sikertelen felfüggeszti csak a tranzakciók. |
| Adatcsere felosztása tranzakciókészletekre – adatcsere felfüggesztése hiba esetén|Elemzi az egyes tranzakciókra, állítsa be egy külön XML-dokumentum cseréje a úgy, hogy a megfelelő boríték alkalmazza. Felfüggeszti a teljes adatcsere, amikor a csomópont egy vagy több tranzakció halmazok érvényesítése sikertelen. | 
| Megőrizheti a adatcsere - tranzakció készletek felfüggeszteni hiba |Az adatcsere érintetlenül hagyja, létrehoz egy XML-dokumentum teljes kötegelt adatcserét. Csak a sikertelen érvényesítés, miközben továbbra is az összes többi tranzakció készletek feldolgozni tranzakció készletek felfüggesztése. |
| Adatcsere megőrzése – adatcsere felfüggesztése hiba esetén |Az adatcsere érintetlenül hagyja, létrehoz egy XML-dokumentum teljes kötegelt adatcserét. Felfüggeszti a teljes adatcsere, amikor a csomópont egy vagy több tranzakció halmazok érvényesítése sikertelen. |

## <a name="configure-how-your-agreement-sends-messages"></a>Hogyan a szerződés küld üzeneteket konfigurálása

Hogyan a jelen szerződés azonosítja, és kezeli a kimenő üzenetekhez, és ez a szerződés partnere küldendő is konfigurálhatja.

1.  A **Hozzáadás**, jelölje be **küldési beállítások**.
Állítsa be ezeket a tulajdonságokat a partnerrel, aki adatcseréihez használható üzenetek Önnel a szerződés alapján. Lásd: e szakasz táblázatai tulajdonság leírásait.

    **Küldési beállításainak** van felosztva ezekben a szakaszokban: azonosítók, visszaigazolás, sémákat, borítékok, karakterkészletek és elválasztók, vezérlő számok és érvényesítése.

2. Miután elkészült, ügyeljen arra, hogy a beállítások mentéséhez válasszon **OK**.

Most már a szerződés készen áll a kimenő üzenetek, amelyek megfelelnek a kiválasztott beállítások kezeléséhez.

### <a name="identifiers"></a>Azonosítók

![Azonosító tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Tulajdonság | Leírás |
| --- | --- |
| Engedélyezési minősítő (ISA1) |A legördülő listából válassza ki a minősítő értékét. |
| ISA2 |Adjon meg engedélyezési adatokat értéket. Ha ez az érték nem a 00, majd adja meg, legalább egy alfanumerikus karaktert, és legfeljebb 10. |
| Biztonsági minősítő (ISA3) |A legördülő listából válassza ki a biztonsági minősítőt érték. |
| ISA4 |Adja meg a biztonsági információk értékét. Ha ez az érték nem a 00 érték (ISA4) mezőben adja meg legalább egy alfanumerikus érték, és legfeljebb 10. |

### <a name="acknowledgment"></a>Tudomásul vétele

![Nyugtázási tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Tulajdonság | Leírás |
| --- | --- |
| Várt TA1 |A csomópont küldő térjen vissza a műszaki visszaigazolás (TA1). A beállítással megadható, hogy a fogadó partner, aki az üzenetet küld a szerződésben Vendég partnertől nyugtázás kéri. A nyugtázások a megállapodás kap beállításai alapján a gazdagép partner által várt. |
| Várt be |A csomópont küldő térjen vissza a működési visszaigazolás (be). Válassza ki, hogy a 997 vagy 999 nyugtázásokhoz, a séma verziók dolgozunk alapján. A nyugtázások a megállapodás kap beállításai alapján a gazdagép partner által várt. |
| FA verziója |Válassza ki a FA verzióját |

### <a name="schemas"></a>Sémák

![Válassza ki a használandó sémát](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Tulajdonság | Leírás |
| --- | --- |
| Verzió |Válassza ki a X12 verzió |
| Tranzakció típusa (ST01) |A tranzakció típusának kiválasztása |
| SÉMA |Válassza ki a használandó sémát. Sémák integrációs fiókjában található. Ha először válassza ki a sémát, a rendszer automatikusan beállítja verziója és a tranzakció típusa  |

> [!NOTE]
> Konfigurálja a szükséges [séma](../logic-apps/logic-apps-enterprise-integration-schemas.md) , amely fel van töltve a [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Borítékok

![Adja meg az elválasztó egy tranzakció készlet: válassza a szabványos azonosító vagy ismétlődési elválasztó](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Tulajdonság | Leírás |
| --- | --- |
| ISA11 használata |Egy tranzakció készlet használandó elválasztó határozza meg: <p>Válassza ki **szabványos azonosító** kíván használni egy pontot (.) decimális jelöléssel, ahelyett, hogy a bejövő dokumentum a EDI decimális jelöléssel fogadni a feldolgozási sor. <p>Válassza ki **ismétlődési elválasztó** adhatja meg az elválasztó az egyszerű adatelem vagy ismétlődő adatszerkezet ismételt előfordulását. Például általában a beszúrási (^) szolgál a ismétlődési elválasztójelként. A HIPAA lehetséges csak a beszúrási tudja használni. |

### <a name="control-numbers"></a>Ellenőrzőszámok

![Ellenőrző szám tulajdonságainak megadása](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Vezérlő verziószáma (ISA12) |Válassza ki a szabványos X12 verzióját |
| Használati mutató (ISA15) |Válassza ki a cseréje kontextusában.  Az értékeket információk, termelési adatok, és vizsgálati adatok |
| Séma |Létrehoz egy X12 kódolású tévő, a Küldés csővezeték küld GS és ST szegmensek |
| GS1 |Nem kötelező, válassza ki a megfelelő értéket a legördülő listából a működési kód |
| GS2 |Nem kötelező, alkalmazás-feladó |
| GS3 |Nem kötelező, alkalmazás fogadó |
| GS4 |Nem kötelező, jelölje be a CCYYMMDD, vagy a ÉÉHHNN |
| GS5 |Nem kötelező, jelölje be HHMM, ÓÓPPMM vagy HHMMSSdd |
| GS7 |Nem kötelező, válassza ki a megfelelő értéket a legördülő listából felelős ügynökség |
| GS8 |A dokumentum nem kötelező, verziója |
| Interchange ellenőrző szám (ISA13) |Az adatcsere ellenőrző szám értéktartománya szükséges, adja meg. Adjon meg egy legalább 1 és legfeljebb 999999999 numerikus értéket |
| Csoport ellenőrző szám (GS06) |A vezérlő csoportszám számok számos szükséges, adja meg. Adjon meg egy legalább 1 és legfeljebb 999999999 numerikus értéket |
| Tranzakció Set ellenőrző szám (ST02) |A tranzakció beállítása ellenőrző szám számok számos szükséges, adja meg. Adjon meg egy numerikus értékek tartományán legalább 1 és legfeljebb 999999999 |
| Körzetszám |A tranzakció set vezérlő számok visszaigazolás használt tartomány kijelölt nem kötelező. Adja meg a középső két mező numerikus értéke, és egy alfanumerikus érték (ha szükséges) az előtag és utótag mezők. A középső mezők szükségesek, és az ellenőrző szám minimális és maximális értékeket tartalmaz |
| Utótag |A tranzakció set vezérlő számok nyugtázás használt tartomány kijelölt nem kötelező. Adja meg a középső két mezők számértéket és egy alfanumerikus érték (ha szükséges) az előtag és utótag mezők. A középső mezők szükségesek, és az ellenőrző szám minimális és maximális értékeket tartalmaz |

### <a name="character-sets-and-separators"></a>Karakterkészletek és elválasztók

Eltérő karakterkészlete, az egyes üzenet egy másik halmaz adhatja meg. Ha egy karakterkészletet adott üzenethez a séma nincs megadva, az alapértelmezett karakterkészlet használata.

![Adja meg a üzenettípusok elválasztó karaktert](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Ahhoz, hogy használható |A tulajdonságok, válassza ki a X12 karakterkészlet érvényesítésére. A beállítások a Basic, a kibővített és az UTF8. |
| Séma |A legördülő listából válassza ki a sémát. Minden egyes sorára befejezése után a rendszer automatikusan hozzáadja az új sort. A kijelölt séma válassza ki az szerepel, amely szeretné használni, az alábbi elválasztó leírása alapján. |
| Bevitel típusa |Válassza ki a bemeneti típusát a legördülő listából. |
| Összetevő-elválasztó |Önálló összetett adatok elemek, írja be a következőt egy egyetlen karaktert. |
| Adatelem-elválasztó |Külön egyszerű adatelemek összetett adatelemek belül, adjon meg egy egyetlen karaktert. |
| Helyettesítő karakter |Adja meg a hasznos adatok minden elválasztó karaktere cserélje ki a kimenő X12 létrehozásakor használt helyettesítő karaktert üzenet. |
| Szegmenslezáró |Egy EDI-szegmens jelezheti, írja be a következőt egy egyetlen karaktert. |
| Utótag |Válassza ki a szegmens azonosítóját használt karakter. Ha egy utótagot ad meg, majd a szegmens lezáró adatok elem lehet üres. Ha a szegmens lezáró üres, majd ki kell jelölnie egy utótagot. |

> [!TIP]
> Speciális karakter értékeket meg, a szerződés JSON-ként módosítsa, majd a speciális karakterek a ASCII értéket adjon meg.

### <a name="validation"></a>Ellenőrzés

![Az üzenetküldésre érvényesítési tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Érvényesítési sorokban befejeződése után, egy másik automatikusan kerül. Ha nem adja meg az összes szabály, érvényesítési használja az "Alapértelmezett" sorhoz.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenettípus |Válassza ki a EDI-üzenet típusa. |
| EDI-ellenőrzés |EDI-érvényesítést hajt végre a séma EDI tulajdonságok, hosszára, üres adatelemek és záró elválasztók által definiált adattípusok. |
| Bővített ellenőrzés |Ha az adattípus nem EDI, érvényesítési az elem követelménynek, és engedélyezett ismétlési, enumerálások és adatok elem hossza érvényesítési (min vagy max). |
| Kezdő/záró nullák engedélyezése |Minden további, kezdő vagy záró nulla, és az szóköz karakter. Ne távolítsa el ezeket a karaktereket. |
| Kezdő/záró nullák levágása |Távolítsa el a kezdő vagy záró nulla karakterből. |
| Záróelválasztó-szabályzat |Záró elválasztók készítése. <p>Válassza ki **nem engedélyezett** záró határoló és elküldött adatcsere elválasztó. Ha cseréje a záró határoló és elválasztókat, az adatcsere van deklarálva nem érvényes. <p>Válassza ki **nem kötelező** kereszteződéseket vagy záró határoló és elválasztók nélkül küldhet. <p>Válassza ki **kötelező** Ha elküldött cseréje a záró határoló és elválasztókat rendelkeznie kell. |

## <a name="find-your-created-agreement"></a>A létrehozott megállapodás keresése

1.  A szerződés tulajdonságainak a beállítása után a **Hozzáadás** lapon, válassza ki **OK** hozta létre a szerződés és integrációs fiókjába való visszatéréshez.

    Az újonnan hozzáadott foglalt most megjelenik a **megállapodások** listája.

2.  Az integrációs fiókok áttekintése is megtekintheti a szerződéseket. Az integráció felhasználóifiók-menüjéből válassza **áttekintése**, majd jelölje be a **megállapodások** csempére.

    ![Válassza a "Szerződés" csempe](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>A swagger megtekintése
Tekintse meg a [részletek swagger](/connectors/x12/). 

## <a name="learn-more"></a>Részletek
* [További tudnivalók a vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag")  

