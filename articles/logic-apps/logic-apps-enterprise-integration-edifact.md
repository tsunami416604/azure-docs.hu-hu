---
title: EDIFACT üzenetek B2B vállalati integrációs - Azure Logic Apps |} Microsoft Docs
description: Exchange EDIFACT üzenetek B2B vállalati integráció az Azure Logic Apps EDI-formátumban
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: 2257d2c8-1929-4390-b22c-f96ca8b291bc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/26/2016
ms.author: LADocs; jonfan
ms.openlocfilehash: d85db6f5be487578ceab261d2779589439bfad31
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300096"
---
# <a name="exchange-edifact-messages-for-enterprise-integration-with-logic-apps"></a>A logic apps vállalati integrációs Exchange EDIFACT-üzenetek

EDIFACT üzenetváltásra az Azure Logic Apps, mielőtt EDIFACT-egyezmény létrehozása, és tárolja, hogy a szerződés integrációs fiókját. Az alábbiakban a szükséges lépéseket EDIFACT-egyezmény létrehozása.

> [!NOTE]
> Ezen a lapon a EDIFACT szolgáltatásaira vonatkozik, amelyek az Azure Logic Apps. További információkért lásd: [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Előkészületek

A szükséges elemeket itt található:

* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéshez társított  
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integráció-fiókban

> [!NOTE]
> Amikor egy szerződést hoz létre, a üzeneteket fogadni, vagy küld és a partner tartalma a szerződés típusának meg kell egyeznie.

Miután [integrációs-fiók létrehozása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) és [adja hozzá a partnerek](logic-apps-enterprise-integration-partners.md), EDIFACT szerződés ezeket a lépéseket követve hozhat létre.

## <a name="create-an-edifact-agreement"></a>EDIFACT-egyezmény létrehozása 

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com "Azure Portal") 

2. Válassza ki a fő Azure menü **minden szolgáltatás**. A keresési mezőbe, írja be a "integrációt", és válassza ki **integrációs fiókok**.

   ![Az integráció fiók](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Ha **minden szolgáltatás** nem jelenik meg, lehetséges, hogy először bontsa ki a menüben. Jelölje be a becsukott menü felső részén **szöveg címkéket**.

3. A **integrációs fiókok**, válassza ki az integráció fiókra, amelyhez a szerződést létrehozásához.

   ![Válassza ki azt az integráció fiókot, a szerződés létrehozásának a helyét](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Válasszon **megállapodások**. Ha egy megállapodások csempe nem rendelkezik, először vegye fel a csempe.   

   ![Válassza a "Szerződés" csempe](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. A szerződések a lapon választhatja ki **Hozzáadás**.

   ![Válassza a "Hozzáadás"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. A **Hozzáadás**, adjon meg egy **neve** a szerződés. A **szerződés típusának**, jelölje be **EDIFACT**. Válassza ki a **fogadó Partner**, **gazdagép identitását**, **Vendég Partner**, és **Vendég identitás** a szerződés.

   ![Adja meg a szerződés részletei](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Tulajdonság | Leírás |
   | --- | --- |
   | Name (Név) |A szerződés nevét |
   | A szerződés típusa | EDIFACT kell lennie. |
   | Gazdagéppartner |Egy szerződést kell a gazdagép és a Vendég partner. A fogadó partner szervezet, amely beállítja a szerződés jelöli. |
   | Gazdagép-identitás |A fogadó partner azonosítója |
   | Vendégpartner |Egy szerződést kell a gazdagép és a Vendég partner. A Vendég partnert a szervezet, amely a gazdagép partnerrel üzleti jelöli. |
   | Vendégidentitás |A Vendég partner azonosítója |
   | Fogadási beállítások |Minden szerződés által fogadott üzenetek alkalmazni ezeket a tulajdonságokat. |
   | Küldési beállítások |Ezeket a tulajdonságokat a szerződés által küldött összes üzenet vonatkozik. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Hogyan a szerződés leírók fogadott üzenetek konfigurálása

Most, hogy a szerződés tulajdonságok beállítása, beállíthatja a módját a jelen szerződés azonosítja, és kezeli a bejövő üzenetek és a szerződés partnerétől kapott.

1. A **Hozzáadás**, jelölje be **fogadási beállítások**.
Állítsa be ezeket a tulajdonságokat a partnerrel, amely az üzenetek Önnel a szerződés alapján. Lásd: e szakasz táblázatai tulajdonság leírásait.

   **Beállítások** van felosztva ezekben a szakaszokban: azonosítók, visszaigazolás, sémákat, vezérlő számok, érvényesítése és belső beállítások.

   ![Konfigurálja a "Kapják meg a beállításokat"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Miután elkészült, ügyeljen arra, hogy a beállítások mentéséhez válasszon **OK**.

Most már a szerződés készen áll a bejövő üzenetek, amelyek megfelelnek a kiválasztott beállítások kezeléséhez.

### <a name="identifiers"></a>Azonosítók

| Tulajdonság | Leírás |
| --- | --- |
| UNB6.1 (címzett referenciajelszava) |Adjon meg egy 1 és 14 karakter közötti alfanumerikus érték. |
| UNB6.2 (címzett referenciaminősítője) |Adjon meg egy alfanumerikus érték legalább egy karaktert, és legfeljebb két karakter. |

### <a name="acknowledgments"></a>Nyugták

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet visszaigazolása (CONTRL) |Válassza ezt a jelölőnégyzetet a műszaki (CONTRL) visszaigazolás a interchange küldő való visszatéréshez. A visszaigazolás nem jut hozzá a interchange küldő, a szerződés küldése beállítások alapján. |
| Nyugtázás (CONTRL) |Válassza ezt a jelölőnégyzetet a működési (CONTRL) visszaigazolás térjen vissza a interchange küldő a nyugtázás a rendszer elküldi a interchange küldő, a szerződés küldése beállítások alapján. |

### <a name="schemas"></a>Sémák

| Tulajdonság | Leírás |
| --- | --- |
| UNH2.1 (TÍPUS) |Válassza ki a tranzakció készlet típusa. |
| UNH2.2 (VERZIÓ) |Adja meg az üzenet verziószáma. (Minimális, egy karakter; maximális, három karakter). |
| UNH2.3 (KIADÁS) |Adja meg az üzenet kiadás száma. (Minimális, egy karakter; maximális, három karakter). |
| UNH2.5 (TÁRSÍTOTT HOZZÁRENDELT KÓD) |Adja meg a hozzárendelt kódot. (Legfeljebb hat karakterből állnak. Csak alfanumerikus karakterekből állhat). |
| UNG2.1 (APP-KÜLDŐAZONOSÍTÓ) |Adjon meg egy alfanumerikus érték legalább egy karaktert és egy legfeljebb 35 karakter hosszú lehet. |
| UNG2.2 (A KÜLDŐ ALKALMAZÁS KÓD MINŐSÍTŐ) |Adjon meg egy alfanumerikus érték egy legfeljebb négy karakter hosszú lehet. |
| SÉMA |Válassza ki a korábban feltöltött sémát használni kívánt társított integrációs fiókjából. |

### <a name="control-numbers"></a>Ellenőrzőszámok
| Tulajdonság | Leírás |
| --- | --- |
| Ellenőrző szám Interchange ismétlődések letiltása |Ismétlődő kereszteződéseket blokkolására, jelölje be ezt a tulajdonságot. Választásakor a EDIFACT dekódolási művelet ellenőrzi, kapott adatcserét adatcsere-ellenőrző szám (UNB5) nem egyezik meg a korábban feldolgozott interchange ellenőrzési számot. Ha találat esetén elvégez, az adatcsere nincs feldolgozva. |
| Ismétlődő UNB5 ellenőrzésének gyakorisága (napokban) |Ha úgy döntött, hogy engedélyezi a duplikált interchange vezérlő számok, megadhatja a napok számát mikor kell végrehajtani az ellenőrzést, azzal, hogy a megfelelő értékre állítja ezt a beállítást. |
| Csoport-ellenőrzőszám ismétlődésének letiltása |Letilthatja felcserélődések számokkal duplikált csoportokhoz vezérlő (UNG5), jelölje be ezt a tulajdonságot. |
| Tranzakciókészlet-ellenőrzőszám ismétlődésének letiltása |Letilthatja felcserélődések számokkal ismétlődő tranzakció set vezérlő (UNH1), jelölje be ezt a tulajdonságot. |
| EDIFACT szerinti nyugtázás-ellenőrző szám |A tranzakció set hivatkozási számok használatra megadását a nyugtázás, adja meg egy értéket az előtag, hivatkozási számos és egy utótagként. |

### <a name="validations"></a>Ellenőrzések

Érvényesítési sorokban befejeződése után, egy másik automatikusan kerül. Ha nem adja meg az összes szabály, érvényesítési használja az "Alapértelmezett" sorhoz.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenettípus |Válassza ki a EDI-üzenet típusa. |
| EDI-ellenőrzés |EDI-érvényesítést hajt végre a séma EDI tulajdonságok, hosszára, üres adatelemek és záró elválasztók által definiált adattípusok. |
| Bővített ellenőrzés |Ha az adattípus nem EDI, érvényesítési az elem követelménynek, és engedélyezett ismétlési, enumerálások és adatok elem hossza érvényesítési (min vagy max). |
| Kezdő/záró nullák engedélyezése |Minden további, kezdő vagy záró nulla, és az szóköz karakter. Ne távolítsa el ezeket a karaktereket. |
| Kezdő/záró nullák levágása |Távolítsa el a kezdő vagy záró nulla és karaktereket. |
| Záróelválasztó-szabályzat |Záró elválasztók készítése. <p>Válassza ki **nem engedélyezett** záró határoló és elválasztók, a kapott interchange. Ha cseréje a záró határoló és elválasztókat, az adatcsere van deklarálva nem érvényes. <p>Válassza ki **nem kötelező** kereszteződéseket vagy záró határoló és elválasztók nélkül fogadásához. <p>Válassza ki **kötelező** Ha kapott adatcsere rendelkeznie kell záró határoló és elválasztókat. |

### <a name="internal-settings"></a>Belső beállítások

| Tulajdonság | Leírás |
| --- | --- |
| Üres XML-címkék létrehozása, ha a záró elválasztók engedélyezve vannak |Válassza ezt a jelölőnégyzetet, az adatcsere küldő záró elválasztók üres XML-címkék közé tartoznak hozzá. |
| Adatcsere felosztása tranzakciókészletekre – tranzakciókészletek felfüggesztése hiba esetén|Elemzi az egyes tranzakciókra, megadhatja a azokat egy külön XML-dokumentum cseréje a megfelelő boríték a tranzakció-készlethez. Csak a sikertelen érvényesítést fognak tranzakció készletek felfüggesztése. |
| Adatcsere felosztása tranzakciókészletekre – adatcsere felfüggesztése hiba esetén|Elemzi az egyes tranzakciókra, állítsa be egy külön XML-dokumentum cseréje a úgy, hogy a megfelelő boríték alkalmazza. A teljes adatcsere felfüggesztése, ha a csomópont egy vagy több tranzakció halmazok érvényesítése sikertelen. | 
| Megőrizheti a adatcsere - tranzakció készletek felfüggeszteni hiba |Az adatcsere érintetlenül hagyja, létrehoz egy XML-dokumentum teljes kötegelt adatcserét. Csak a sikertelen érvényesítés, miközben továbbra is az összes többi tranzakció készletek feldolgozni tranzakció készletek felfüggesztése. |
| Adatcsere megőrzése – adatcsere felfüggesztése hiba esetén |Az adatcsere érintetlenül hagyja, létrehoz egy XML-dokumentum teljes kötegelt adatcserét. A teljes adatcsere felfüggesztése, ha a csomópont egy vagy több tranzakció halmazok érvényesítése sikertelen. |

## <a name="configure-how-your-agreement-sends-messages"></a>Hogyan a szerződés küld üzeneteket konfigurálása

Beállíthatja, hogyan a jelen szerződés azonosítja, és a partnerek számára, és ez a szerződés elküldött kimenő üzenetek kezeli.

1.  A **Hozzáadás**, jelölje be **küldési beállítások**.
Állítsa be ezeket a tulajdonságokat a partnerrel, aki adatcseréihez használható üzenetek Önnel a szerződés alapján. Lásd: e szakasz táblázatai tulajdonság leírásait.

    **Küldési beállításainak** van felosztva ezekben a szakaszokban: azonosítók, visszaigazolás, sémákat, borítékok, karakterkészletek és elválasztók, vezérlő számok és érvényesítést.

    ![Konfigurálja a "Küldési beállításai"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Miután elkészült, ügyeljen arra, hogy a beállítások mentéséhez válasszon **OK**.

Most már a szerződés készen áll a kimenő üzenetek, amelyek megfelelnek a kiválasztott beállítások kezeléséhez.

### <a name="identifiers"></a>Azonosítók

| Tulajdonság | Leírás |
| --- | --- |
| UNB1.2 (szintaxis verzió) |Válasszon ki egy értéket közötti **1** és **4**. |
| UNB2.3 (feladó fordított irányú útválasztási címe) |Adja meg legalább egy karaktert és 14 karakterek alfanumerikus értékét. |
| UNB3.3 (címzett fordított irányú útválasztási címe) |Adja meg legalább egy karaktert és 14 karakterek alfanumerikus értékét. |
| UNB6.1 (címzett referenciajelszava) |Adja meg legalább egy és 14 karakterek alfanumerikus értékét. |
| UNB6.2 (címzett referenciaminősítője) |Adjon meg egy alfanumerikus érték legalább egy karaktert, és legfeljebb két karakter. |
| UNB7 (alkalmazás hivatkozásazonosítója) |Adjon meg egy legalább egy karaktert és 14 karakterek alfanumerikus érték |

### <a name="acknowledgment"></a>Tudomásul vétele
| Tulajdonság | Leírás |
| --- | --- |
| Üzenet visszaigazolása (CONTRL) |Jelölje be ezt a jelölőnégyzetet, ha a központi partner várhatóan kap egy műszaki (CONTRL). A beállítással megadható, hogy a központi partner, aki az üzenetet küld, a Vendég partnertől nyugtázást kéri. |
| Nyugtázás (CONTRL) |Akkor válassza ezt a jelölőnégyzetet, ha a szolgáltatott partner várhatóan kap egy funkcionális (CONTRL). A beállítással megadható, hogy a központi partner, aki az üzenetet küld, a Vendég partnertől nyugtázást kéri. |
| SG1-/SG4-hurok létrehozása az elfogadott tranzakciókészletekhez |Ha úgy döntött, hogy egy funkcionális nyugtázási kérelem, jelölje be a jelölőnégyzetet SG1/HK4 hurkok generációja hatályos működési CONTRL nyugták elfogadott tranzakció csoportjai számára. |

### <a name="schemas"></a>Sémák
| Tulajdonság | Leírás |
| --- | --- |
| UNH2.1 (TÍPUS) |Válassza ki a tranzakció készlet típusa. |
| UNH2.2 (VERZIÓ) |Adja meg az üzenet verziószáma. |
| UNH2.3 (KIADÁS) |Adja meg az üzenet kiadás száma. |
| SÉMA |Válassza ki a használandó sémát. Sémák integrációs fiókjában található. A sémák szeretne használni, először az integráció fiók összekötése a Logic Apps alkalmazást. |

### <a name="envelopes"></a>Borítékok
| Tulajdonság | Leírás |
| --- | --- |
| UNB8 (feldolgozási prioritási kód) |Adjon meg egy szedett egynél több karakter hosszú. |
| UNB10 (kommunikációs egyezmény) |Adjon meg egy alfanumerikus érték legalább egy karaktert, és legfeljebb 40 karakter. |
| UNB11 (tesztjelző) |A jelölőnégyzet azt jelzi, hogy a létrehozott interchange vizsgálati adatok |
| UNA-szegmens (szolgáltatás sztringjére vonatkozó javaslatok) használata |Válassza ezt a jelölőnégyzetet a küldendő adatcserét UNA szegmens létrehozásához. |
| UNG-szegmensek (funkciócsoport-fejléc) használata |Válassza ezt a jelölőnégyzetet, a Vendég partnernek küldött üzenetek funkcionális csoport fejlécben csoportosítási szegmensek létrehozására. A következő értékeket fogja használni a UNG szegmensek létrehozása: <p>A **UNG1**, adja meg legalább egy karaktert és legfeljebb hat karakterből álló alfanumerikus értékét. <p>A **UNG2.1**, adjon meg egy alfanumerikus érték legalább egy karaktert és egy legfeljebb 35 karakter hosszú lehet. <p>A **UNG2.2**, adjon meg egy alfanumerikus érték egy legfeljebb négy karakter hosszú lehet. <p>A **UNG3.1**, adjon meg egy alfanumerikus érték legalább egy karaktert és egy legfeljebb 35 karakter hosszú lehet. <p>A **UNG3.2**, adjon meg egy alfanumerikus érték egy legfeljebb négy karakter hosszú lehet. <p>A **UNG6**, adja meg legalább egy és legfeljebb három karakterből álló alfanumerikus értékét. <p>A **UNG7.1**, adja meg legalább egy karaktert és legfeljebb három karakterből álló alfanumerikus értékét. <p>A **UNG7.2**, adja meg legalább egy karaktert és legfeljebb három karakterből álló alfanumerikus értékét. <p>A **UNG7.3**, adjon meg egy legalább 1 karakter és 6 karakterből álló alfanumerikus érték. <p>A **UNG8**, adja meg legalább egy karaktert és 14 karakterek alfanumerikus értékét. |

### <a name="character-sets-and-separators"></a>Karakterkészletek és elválasztók

Eltérő karakterkészlete, egy másik csoportját az egyes üzenet használandó elválasztó karaktert adhat meg. Ha egy karakterkészletet adott üzenethez a séma nincs megadva, az alapértelmezett karakterkészlet szerepel.

| Tulajdonság | Leírás |
| --- | --- |
| UNB1.1 (rendszerazonosító) |Válassza ki a kimenő interchange alkalmazandó EDIFACT karakterkészletet. |
| Séma |A legördülő listából válassza ki a sémát. Minden egyes sorára befejezése után a rendszer automatikusan hozzáadja az új sort. A kijelölt séma válassza ki az szerepel, amely szeretné használni, az alábbi elválasztó leírása alapján. |
| Bevitel típusa |Válassza ki a bemeneti típusát a legördülő listából. |
| Összetevő-elválasztó |Önálló összetett adatok elemek, írja be a következőt egy egyetlen karaktert. |
| Adatelem-elválasztó |Külön egyszerű adatelemek összetett adatelemek belül, adjon meg egy egyetlen karaktert. |
| Szegmenslezáró |Egy EDI-szegmens jelezheti, írja be a következőt egy egyetlen karaktert. |
| Utótag |Válassza ki a szegmens azonosítóját használt karakter. Ha egy utótagot ad meg, majd a szegmens lezáró adatok elem lehet üres. Ha a szegmens lezáró üres, majd ki kell jelölnie egy utótagot. |

### <a name="control-numbers"></a>Ellenőrzőszámok
| Tulajdonság | Leírás |
| --- | --- |
| UNB5 (adatcsere-ellenőrző szám) |Írjon be egy előtagot, egy interchange ellenőrző szám és egy utótagként értékek tartományán. Ezek az értékek egy kimenő interchange létrehozásához használt. Az előtag és utótag opcionálisak, amíg a vezérlő számot kell megadni. Az ellenőrző szám értéke akkor nő, minden új üzenet; az előtag és utótag ugyanaz marad. |
| UNG5 (csoportellenőrző szám) |Írjon be egy előtagot, egy interchange ellenőrző szám és egy utótagként értékek tartományán. Ezeket az értékeket a vezérlő csoportszám létrehozásához használt. Az előtag és utótag opcionálisak, amíg a vezérlő számot kell megadni. Az ellenőrző szám értéke akkor nő, minden új üzenet a maximális érték eléréséig; az előtag és utótag ugyanaz marad. |
| UNH1 (üzenetfejléc hivatkozási száma) |Írjon be egy előtagot, egy interchange ellenőrző szám és egy utótagként értékek tartományán. Ezeket az értékeket az üzenet fejlécének hivatkozási szám létrehozásához használt. Az előtag és utótag opcionálisak, amíg a hivatkozási számot kell megadni. A hivatkozási számot értéke akkor nő, minden új üzenet; az előtag és utótag ugyanaz marad. |

### <a name="validations"></a>Ellenőrzések

Érvényesítési sorokban befejeződése után, egy másik automatikusan kerül. Ha nem adja meg az összes szabály, érvényesítési használja az "Alapértelmezett" sorhoz.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenettípus |Válassza ki a EDI-üzenet típusa. |
| EDI-ellenőrzés |EDI-érvényesítést hajt végre a séma, hosszára, üres adatelemek és záró elválasztók EDI tulajdonságainak által definiált adattípusok. |
| Bővített ellenőrzés |Ha az adattípus nem EDI, érvényesítési az elem követelménynek, és engedélyezett ismétlési, enumerálások és adatok elem hossza érvényesítési (min vagy max). |
| Kezdő/záró nullák engedélyezése |Minden további, kezdő vagy záró nulla, és az szóköz karakter. Ne távolítsa el ezeket a karaktereket. |
| Kezdő/záró nullák levágása |Távolítsa el a kezdő vagy záró nulla karakterből. |
| Záróelválasztó-szabályzat |Záró elválasztók készítése. <p>Válassza ki **nem engedélyezett** záró határoló és elküldött adatcsere elválasztó. Ha cseréje a záró határoló és elválasztókat, az adatcsere van deklarálva nem érvényes. <p>Válassza ki **nem kötelező** kereszteződéseket vagy záró határoló és elválasztók nélkül küldhet. <p>Válassza ki **kötelező** Ha elküldött cseréje a záró határoló és elválasztókat rendelkeznie kell. |

## <a name="find-your-created-agreement"></a>A létrehozott megállapodás keresése

1.  A szerződés tulajdonságainak a beállítása után a **Hozzáadás** lapon, válassza ki **OK** hozta létre a szerződés és integrációs fiókjába való visszatéréshez.

    Az újonnan hozzáadott foglalt most megjelenik a **megállapodások** listája.

2.  Az integrációs fiókok áttekintése is megtekintheti a szerződéseket. Az integráció felhasználóifiók-menüjéből válassza **áttekintése**, majd jelölje be a **megállapodások** csempére. 

    ![Válassza a "Szerződés" csempe](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="view-swagger-file"></a>A Swagger-fájl megtekintése
A Swagger adatai a EDIFACT-összekötő megtekintése: [EDIFACT](/connectors/edifact/).

## <a name="learn-more"></a>Részletek
* [További tudnivalók a vállalati integrációs csomag](logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag")  

