---
title: B2B vállalati integráció – Azure Logic Apps üzenetek X12 |} A Microsoft Docs
description: Az Exchange X12 üzenetek B2B vállalati integráció az Azure Logic Apps Enterprise Integration Pack EDI formátumban
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.date: 01/31/2017
ms.openlocfilehash: c4ee56f4ddcccb1fc4ddd84aa1c1b16dea9754d9
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123957"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>B2B vállalati integráció az Azure Logic Apps Enterprise Integration Pack Exchange X12 üzenete

Mielőtt X12 is cserél üzeneteket az Azure Logic Apps, létre kell hoznia X12 szerződés és az integrációs fiókban lévő megállapodás tárolja. X12 létrehozásának lépései a következők szerződés.

> [!NOTE]
> A lap tartalmazza a X12 szolgáltatások Azure Logic Apps. További információkért lásd: [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Előkészületek

A következő szükséges elemek:

* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéséhez társított
* Legalább két [partnerek](../logic-apps/logic-apps-enterprise-integration-partners.md) , és határozza meg az integrációs fiókban lévő konfigurálva a X12 azonosítóját a **üzleti identitások**    
* Egy kötelező [séma](../logic-apps/logic-apps-enterprise-integration-schemas.md) , feltöltheti az integrációs fiók

Miután [integrációs fiók létrehozása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [partnerek felvétele](logic-apps-enterprise-integration-partners.md), és egy [séma](../logic-apps/logic-apps-enterprise-integration-schemas.md) , hogy a használni kívánt, létrehozhat X12 szerződést a következő lépésekkel.

## <a name="create-an-x12-agreement"></a>Hozzon létre X12 szerződés

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com "Azure Portal") 

2. Az Azure fő menüjéből válassza **minden szolgáltatás**. A Keresés mezőbe írja be a "integráció" névre, és válassza ki **integrációs fiókok**.  

   ![Keresse meg az integrációs fiók](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Ha **minden szolgáltatás** nem jelenik meg, lehetséges, hogy először bontsa ki a menüben. A összecsukott menü tetején válassza **Megjelenítés menü**.

3. A **integrációs fiókok**, válassza ki az integrációs fiók, ahol szeretne hozzáadni a szerződést.

   ![Integrációs fiók hová hozza létre a szerződés kiválasztása](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Válassza ki **áttekintése**, majd válassza ki a **szerződések** csempére. Ha nem rendelkezik egy szerződés-csempét, először adja hozzá a csempét. 

   ![Válassza a "Szerződés" csempe](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. A **szerződések**, válassza a **Hozzáadás**.

   ![Válassza az "Add"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)     

6. Alatt **Hozzáadás**, adjon meg egy **neve** esetében a szerződés hatálya alá. A szerződés típusának kiválasztása **X12**. Válassza ki a **Gazdagéppartner**, **gazdagép-identitás**, **Vendégpartner**, és **Vendégidentitás** esetében a szerződés hatálya alá. Tulajdonság kapcsolatos további információkért lásd az ebben a lépésben a táblában.

    ![Adja meg a szerződés részletei](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Tulajdonság | Leírás |
    | --- | --- |
    | Name (Név) |A Szerződés neve |
    | Szerződés típusa | X12 kell lennie. |
    | Gazdagéppartner |Egy szerződést kell a gazdagép és Vendég partner. A gazdagéppartner a szervezet, amely beállítja a szerződés jelöli. |
    | Gazdagép-identitás |A gazdagéppartner azonosítója |
    | Vendégpartner |Egy szerződést kell a gazdagép és Vendég partner. A vendégpartner a szervezet, amely a fogadó partner üzleti állapotát jelöli. |
    | Vendégidentitás |A vendégpartner azonosítója |
    | Fogadási beállítások |Ezek a tulajdonságok vonatkoznak az összes, a szerződés által fogadott üzeneteket. |
    | Küldési beállítások |Ezek a tulajdonságok a szerződés által küldött összes üzenet vonatkozik. |  

  > [!NOTE]
  > Szerződés attól függ, a feladó minősítője és azonosítója, és a címzett minősítője és a partnerek és a bejövő üzenet meghatározott azonosító megfelelő X12 feloldása. Ha ezeket az értékeket módosítsa a partner, frissítse túl a megállapodás.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Hogyan fogadja a szerződés kezeli az üzenetek konfigurálása

Most, hogy beállította a megállapodás tulajdonságai, konfigurálhatja, hogyan azonosítja a jelen szerződés, és kezeli a partner a jelen szerződés keretében kapott bejövő üzenetek.

1.  A **Hozzáadás**válassza **fogadási beállítások**.
Konfigurálja ezeket a tulajdonságokat a partnerrel, amely az üzeneteket, a szerződés alapján. Vlastnost leírásáért lásd: e szakasz táblázatai.

    **Beállítások** van szakaszokba rendeztük: azonosítók, visszaigazoló, sémákat, borítékok, Ellenőrzőszámok, ellenőrzés és belső beállítások.

2. Miután elkészült, ügyeljen arra, hogy a beállítások mentéséhez kiválasztásával **OK**.

Most a szerződés elkészült kezelje a bejövő üzenetek, amelyek megfelelnek a kiválasztott beállításokat.

### <a name="identifiers"></a>Azonosítók

![Azonosító tulajdonságainak megadása](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Tulajdonság | Leírás |
| --- | --- |
| ISA1 (engedélyezési minősítő) |Válassza ki az engedélyezési minősítő értéket a legördülő listából. |
| ISA2 |Választható. Engedélyezési információkat értéket adjon meg. ISA1 megadott érték kívül 00 esetén adjon meg legalább egy alfanumerikus karakter és legfeljebb 10. |
| ISA3 (biztonsági minősítő) |Válassza ki a biztonsági minősítő értéket a legördülő listából. |
| ISA4 |Választható. Adja meg a biztonsági információinak értékét. ISA3 megadott érték kívül 00 esetén adjon meg legalább egy alfanumerikus karakter és legfeljebb 10. |

### <a name="acknowledgment"></a>Nyugtázása

![Nyugtázás tulajdonságainak megadása](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Várt érték: TA1 |Visszaadja a műszaki visszaigazolás adatcsere feladójának |
| Várt érték: FA |A működési visszaigazolás adatcsere feladójának adja vissza. Ezután válassza ki, hogy a, vagy 999 997 visszaigazolások, a sémaverzió alapján |
| Például: AK2-/ IK2-hurok |Povoluje generování událostí: AK2 hurkokat a működési nyugták az elfogadott tranzakciókészletekhez |

### <a name="schemas"></a>Sémák

Válassza ki az egyes tranzakciótípus (ST1) és a küldő alkalmazás (GS2) sémát. A fogadási folyamat visszafejti a bejövő üzenetben az egyező értékeket ST1 és GS2 bejövő üzenetben az itt megadott értékeket, és a sémát a sémát a bejövő üzenet itt.

![Válassza ki a sémát](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Verzió |Válassza ki a X12 verzió |
| Tranzakció típusa (ST01) |Jelölje be a tranzakció típusa |
| Küldő alkalmazás (GS02) |Válassza ki a küldő alkalmazást |
| Séma |Válassza ki a használni kívánt fájl. Sémákat az integrációs fiók kerülnek. |

> [!NOTE]
> Konfigurálja a szükséges [séma](../logic-apps/logic-apps-enterprise-integration-schemas.md) töltenek fel, amely a [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Borítékok

![Adja meg az elválasztó egy tranzakciókészlet: válassza a szabványos azonosító vagy ismétlődés-elválasztó](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Tulajdonság | Leírás |
| --- | --- |
| ISA11 használata |A tranzakciókészlet használandó elválasztó határozza meg: <p>Válassza ki **szabványos azonosító** decimális jelölés használandó ponttal (.), ahelyett, hogy a bejövő dokumentum az EDI a decimális jelölés kap a folyamatot. <p>Válassza ki **ismétlődés-elválasztó** az elválasztó egy egyszerű adatelemének vagy ismétlődő adatstruktúra ismételt előfordulásának a megadásához. Ha például általában a karátot (^) szolgál a ismétlődési elválasztóként. A HIPAA sémák a karátot csak használhatja. |

### <a name="control-numbers"></a>Ellenőrzőszámok

![Válassza ki az ellenőrző szám ismétlődések kezelése](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Adatcsere ellenőrzőszám ismétlődésének letiltása |Ismétlődő adatcsere letiltása. Az adatcsere ellenőrzőszáma (ISA13) ellenőrzi a beérkezett adatcsere-ellenőrzőszám. Egyezés észlelése esetén a fogadási folyamat nem dolgozza fel az adatcsere. Megadhatja, hogy hány napig értékét, így az ellenőrzés végrehajtásához *ismétlődő ISA13 ellenőrzésének gyakorisága (napokban)*. |
| Csoport-ellenőrzőszám ismétlődésének letiltása |Blokk felcserélődések a duplikált csoportokhoz ellenőrzőszámok. |
| Tranzakciókészlet-ellenőrzőszám ismétlődésének letiltása |Blokk felcserélődések az ismétlődő tranzakciókészlet set ellenőrzőszámok. |

### <a name="validations"></a>Ellenőrzések

![A fogadott üzenetek érvényesítési tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Után minden érvényesítési sorban, egy másik automatikusan hozzáadja. Ha nem ad meg szabályokat, érvényesítési az "Alapértelmezett" sort használ.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenettípus |Válassza ki az EDI-üzenet típusa. |
| EDI-ellenőrzés |Hajtsa végre a séma EDI tulajdonságok, mezőhossz-korlátozásai, üres adatelem és záró elválasztók által meghatározott adattípusok EDI-ellenőrzés. |
| Bővített ellenőrzés |Ha az adattípus nem EDI, ellenőrzés be van kapcsolva az elem követelménynek és ismétlődési, enumerálások és az adatok elem hossza érvényesítési (minimális/maximális) engedélyezett. |
| Kezdő/záró nullák engedélyezése |A többi kezdő vagy záró nulla megőrzi, és szóköz karakter. Ne távolítsa el ezeket a karaktereket. |
| Kezdő/záró nullák levágása |Távolítsa el a kezdő vagy záró nulla, a térköz karaktereket. |
| Záróelválasztó-szabályzat |Záró elválasztók készítése. <p>Válassza ki **nem engedélyezett** időtartamig záró elválasztó karakterek és a kapott adatcsere elválasztók. Ha az adatcsere záró elválasztó karakterek és elválasztók, az adatcsere van deklarálva nem érvényes. <p>Válassza ki **nem kötelező** vagy a záró elválasztó karakterek és elválasztók nélkül érték elfogadásához. <p>Válassza ki **kötelező** mikor kell rendelkeznie az adatcsere, záró elválasztó karakterek és elválasztók. |

### <a name="internal-settings"></a>Belső beállítások

![Belső beállítások kiválasztása](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Tulajdonság | Leírás |
| --- | --- |
| "Nn" decimális formátum konvertálása alap 10 numerikus értéket |10-es numerikus értékké "Nn" formátumban megadott EDI számot alakít át. |
| Üres XML-címkék létrehozása, ha a záró elválasztók engedélyezve vannak |Válassza ki ezt a jelölőnégyzetet, hogy közé tartozik a záró elválasztók üres XML-címkéinek adatcsere feladójának. |
| Adatcsere felosztása tranzakciókészletekre – tranzakciókészletek felfüggesztése hiba esetén|Minden tranzakció beállítása egy külön dokumentu XML-adatcsere a megfelelő boríték alkalmaz a tranzakciókészlet elemzi. Ha az ellenőrzés nem sikerül felfüggeszti csak a tranzakciók. |
| Adatcsere felosztása tranzakciókészletekre – adatcsere felfüggesztése hiba esetén|Minden tranzakció beállítása egy külön dokumentu XML-adatcsere úgy, hogy alkalmazza a megfelelő boríték elemzi. Felfüggeszti a teljes adatcsere, ha egy vagy több tranzakciókészletek az adatcsere érvényesítése sikertelen. | 
| Adatcsere megőrzése – tranzakciókészletek felfüggesztése hiba esetén |Az adatcsere érintetlenül hagyja, az XML-dokumentumok teljes kötegelt adatcserét hoz létre. Csak a sikertelen érvényesítést, miközben továbbra is az összes többi tranzakciókészletek feldolgozni tranzakciókészletek felfüggesztése. |
| Adatcsere megőrzése – adatcsere felfüggesztése hiba esetén |Az adatcsere érintetlenül hagyja, az XML-dokumentumok teljes kötegelt adatcserét hoz létre. Felfüggeszti a teljes adatcsere közben, ha egy vagy több tranzakciókészletek az adatcsere érvényesítése sikertelen. |

## <a name="configure-how-your-agreement-sends-messages"></a>Hogyan a szerződéshez küldi az üzeneteket konfigurálása

Beállíthatja, hogyan azonosítja a jelen szerződés, és kezeli a partner a jelen szerződés keretében küldött kimenő üzenetek.

1.  A **Hozzáadás**válassza **küldési beállítások**.
Konfigurálja ezeket a tulajdonságokat a partnerrel, aki adatcseréihez használható üzeneteket, a szerződés alapján. Vlastnost leírásáért lásd: e szakasz táblázatai.

    **Küldési beállítások** van szakaszokba rendeztük: azonosítók, visszaigazoló, sémákat, borítékok, karakterkészletek és elválasztók, Ellenőrzőszámok és érvényesítési.

2. Miután elkészült, ügyeljen arra, hogy a beállítások mentéséhez kiválasztásával **OK**.

Most már a szerződés elkészült kezelésére, amelyek megfelelnek a kiválasztott beállításokat kimenő üzenetek.

### <a name="identifiers"></a>Azonosítók

![Azonosító tulajdonságainak megadása](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Tulajdonság | Leírás |
| --- | --- |
| Engedélyezési minősítő (ISA1) |Válassza ki az engedélyezési minősítő értéket a legördülő listából. |
| ISA2 |Engedélyezési információkat értéket adjon meg. Ha ez az érték nem 00, majd adja meg, legalább egy alfanumerikus karakter és legfeljebb 10. |
| Biztonsági minősítő (ISA3) |Válassza ki a biztonsági minősítő értéket a legördülő listából. |
| ISA4 |Adja meg a biztonsági információinak értékét. Ha ez az érték nem a 00 érték (ISA4) szövegmező, majd adja meg, legalább egy alfanumerikus érték és a egy legfeljebb 10. |

### <a name="acknowledgment"></a>Nyugtázása

![Nyugtázás tulajdonságainak megadása](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Tulajdonság | Leírás |
| --- | --- |
| Várt érték: TA1 |A technikai visszaigazolás (TA1) térjen vissza az adatcsere feladójának. Ez a beállítás meghatározza, hogy a gazdagép-partnerrel, aki az üzenetet küld egy visszaigazoló kérelmek egyezményben vendégként partnertől. Ezek a nyugták a szerződés kap beállításai alapján a gazdagép-partner által várt. |
| Várt érték: FA |Adatcsere feladójának térjen vissza a működési visszaigazolás (be). Válassza ki, hogy a, vagy 999 997 nyugtázások, a alapján séma dolgozik. Ezek a nyugták a szerződés kap beállításai alapján a gazdagép-partner által várt. |
| FA verziója |Válassza ki a FA verziója |

### <a name="schemas"></a>Sémák

![Válassza ki a használni kívánt sémájának](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Tulajdonság | Leírás |
| --- | --- |
| Verzió |Válassza ki a X12 verzió |
| Tranzakció típusa (ST01) |Jelölje be a tranzakció típusa |
| SÉMA |Válassza ki a használandó sémát. Sémákat az integrációs fiókjában található. Ha először válassza ki a sémát, a rendszer automatikusan beállítja verziója és a tranzakció típusa  |

> [!NOTE]
> Konfigurálja a szükséges [séma](../logic-apps/logic-apps-enterprise-integration-schemas.md) töltenek fel, amely a [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Borítékok

![Adja meg az elválasztó egy tranzakciókészlet: válassza a szabványos azonosító vagy ismétlődés-elválasztó](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Tulajdonság | Leírás |
| --- | --- |
| ISA11 használata |A tranzakciókészlet használandó elválasztó határozza meg: <p>Válassza ki **szabványos azonosító** decimális jelölés használandó ponttal (.), ahelyett, hogy a bejövő dokumentum az EDI a decimális jelölés kap a folyamatot. <p>Válassza ki **ismétlődés-elválasztó** az elválasztó egy egyszerű adatelemének vagy ismétlődő adatstruktúra ismételt előfordulásának a megadásához. Ha például általában a karátot (^) szolgál a ismétlődési elválasztóként. A HIPAA sémák a karátot csak használhatja. |

### <a name="control-numbers"></a>Ellenőrzőszámok

![Adja meg az ellenőrzőszám tulajdonságai](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Ellenőrző-verziószám (ISA12) |Válassza ki a standard szintű X12 verzióját |
| Használati mutató (ISA15) |Válassza ki az adatcsere keretében.  Az értékek információkat, termelési adatok, vagy vizsgálati adatok |
| Séma |Létrehoz egy X12-kódolású tévő, a Küldés folyamat elküldi a GS és ST szegmensek |
| GS1 |Nem kötelező megadni, válassza ki a megfelelő értéket a legördülő listából a működési kód |
| GS2 |Nem kötelező, csak alkalmazás-feladó |
| GS3 |Nem kötelező, csak alkalmazás fogadó |
| GS4 |Nem kötelező, válassza ki a CCYYMMDD, vagy a ÉÉHHNN |
| GS5 |Nem kötelező megadni, jelölje be HHMM, HHMMSS vagy HHMMSSdd |
| GS7 |Nem kötelező megadni, válassza ki a megfelelő értéket a legördülő listából a felelős hivatal |
| GS8 |A dokumentum nem kötelező, csak verzióját |
| Adatcsere Ellenőrzőszáma (ISA13) |Szükség esetén adjon meg egy tartományt az adatcsere-ellenőrzőszám. Adjon meg egy numerikus értéket legalább 1 és legfeljebb 999999999 |
| Csoport Ellenőrzőszáma (GS06) |Számok számos szükséges, adja meg a csoport-ellenőrzőszám. Adjon meg egy numerikus értéket legalább 1 és legfeljebb 999999999 |
| Tranzakciókészlet Ellenőrzőszáma (ST02) |A tranzakció beállítása ellenőrzőszám számok számos szükséges, adja meg. Adja meg a numerikus értékek tartományát legalább 1 és legfeljebb 999999999 |
| Körzetszám |Kijelölt tranzakció set ellenőrzőszámok használatban vétele címtartománya nem kötelező. Adja meg a középső két mező numerikus értékét, és a egy alfanumerikus érték (ha szükséges) a előtagokkal és utótagokkal mezőket. A középső mezők szükség, és az ellenőrzőszám minimális és maximális értékeit tartalmazza |
| Utótag |Kijelölt tranzakció beállítása egy visszaigazoló használt ellenőrzőszámok címtartománya nem kötelező. Adja meg a középső két mező numerikus értékét, és a egy alfanumerikus érték (ha szükséges) a előtagokkal és utótagokkal mezőket. A középső mezők szükség, és az ellenőrzőszám minimális és maximális értékeit tartalmazza |

### <a name="character-sets-and-separators"></a>Karakterkészletek és elválasztók

Eltérő a karakterkészletet, a elválasztó külön készletét adja meg mindegyik üzenet típusa. Ha egy karakterkészlet egy adott üzenet-séma nincs megadva, az alapértelmezett karakterkészlet szolgál.

![Adja meg az üzenetek elválasztó karaktereket](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Tulajdonság | Leírás |
| --- | --- |
| Használandó karakterkészlet |A tulajdonságait, válassza ki a X12 karakterkészlet ellenőrzése. A beállítások a következők: alapszintű, bővített és UTF8. |
| Séma |A legördülő listából válassza ki a sémát. Miután végzett az egyes sorok, a rendszer automatikusan hozzáadja az új sort. A kiválasztott séma, az elválasztók készletének kiválasztása, amelyet szeretne használni, az alábbi elválasztó leírása alapján. |
| Bevitel típusa |Válassza ki egy bemeneti típusát a legördülő listából. |
| Összetevő-elválasztó |Összetett adatok elemek elválasztásához, adja meg egy egyetlen karaktert. |
| Adatelem-elválasztó |Összetett adatok egyszerű elemek elválasztásához, adjon meg egy egyetlen karaktert. |
| Helyettesítő karakter |Adjon meg egy helyettesítő karaktert, és cserélje le a hasznos adatok összes elválasztó karakter, a kimenő X12 létrehozásakor használt üzenet. |
| Szegmenslezáró |Jelezheti az EDI-szegmens, adjon meg egy egyetlen karaktert. |
| Utótag |Válassza ki a szegmens azonosítóját használt karakter. Ha egy utótagot ad meg, majd a szegmens lezáró adatelem lehet üres. Ha a szegmenslezáró üres, majd ki kell jelölnie egy utótagot. |

> [!TIP]
> Speciális karakter használata szükséges értékeket ad meg, hogy a JSON-fájlként szerződés szerkesztése, és adja meg az ASCII érték a speciális karaktert.

### <a name="validation"></a>Ellenőrzés

![Üzenetek küldése érvényesítési tulajdonságainak beállítása](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Után minden érvényesítési sorban, egy másik automatikusan hozzáadja. Ha nem ad meg szabályokat, érvényesítési az "Alapértelmezett" sort használ.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenettípus |Válassza ki az EDI-üzenet típusa. |
| EDI-ellenőrzés |Hajtsa végre a séma EDI tulajdonságok, mezőhossz-korlátozásai, üres adatelem és záró elválasztók által meghatározott adattípusok EDI-ellenőrzés. |
| Bővített ellenőrzés |Ha az adattípus nem EDI, ellenőrzés be van kapcsolva az elem követelménynek és ismétlődési, enumerálások és az adatok elem hossza érvényesítési (minimális/maximális) engedélyezett. |
| Kezdő/záró nullák engedélyezése |A többi kezdő vagy záró nulla megőrzi, és szóköz karakter. Ne távolítsa el ezeket a karaktereket. |
| Kezdő/záró nullák levágása |Távolítsa el a felesleges kezdő vagy záró nulla karakter. |
| Záróelválasztó-szabályzat |Záró elválasztók készítése. <p>Válassza ki **nem engedélyezett** időtartamig záró elválasztó karakterek és az elküldött adatcsere elválasztók. Ha az adatcsere záró elválasztó karakterek és elválasztók, az adatcsere van deklarálva nem érvényes. <p>Válassza ki **nem kötelező** érték, vagy a záró elválasztó karakterek és elválasztók nélkül küldhet. <p>Válassza ki **kötelező** az elküldött adatcsere záró elválasztó karakterek és elválasztók kell rendelkeznie. Ha. |

## <a name="find-your-created-agreement"></a>Keresse meg a létrehozott szerződés

1.  A megállapodás tulajdonságai a beállítás befejezése után a **Hozzáadás** lapon a **OK** véglegesítse a szerződés hatálya alá, és térjen vissza az integrációs fiókban.

    Az újonnan hozzáadott szerződés most már megjelenik a **szerződések** listája.

2.  Az integrációs fiókok áttekintése is megtekintheti a szerződéseket. Az integrációs fiók menüjében válassza a **áttekintése**, majd válassza ki a **szerződések** csempére.

    ![Válassza a "Szerződés" csempe](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>A swagger megtekintése
Tekintse meg a [részletek swagger](/connectors/x12/). 

## <a name="learn-more"></a>Részletek
* [További információ az Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag")  

