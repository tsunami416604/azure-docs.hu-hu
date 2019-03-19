---
title: EDIFACT-üzenetek B2B vállalati integráció – Azure Logic Apps |} A Microsoft Docs
description: EDIFACT-üzenetek B2B vállalati integráció az Azure Logic Apps Enterprise Integration Pack EDI formátumban
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 2257d2c8-1929-4390-b22c-f96ca8b291bc
ms.date: 07/26/2016
ms.openlocfilehash: bbcdad7c5496cd08994a613b07e1bc7c611e4572
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57876850"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>EDIFACT-üzenetek a B2B vállalati integráció az Azure Logic Apps Enterprise Integration Pack

EDIFACT üzenetváltás az Azure Logic Apps, mielőtt az EDIFACT-egyezmény létrehozása, és az integrációs fiókban lévő megállapodás tárolja. Az EDIFACT-egyezmény létrehozása lépései a következők.

> [!NOTE]
> Ezen a lapon az EDIFACT-funkciók az Azure Logic Apps ismertetjük. További információkért lásd: [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Előkészületek

A következő szükséges elemek:

* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéséhez társított  
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integrációs fiók

> [!NOTE]
> Amikor egy szerződést hoz létre, a tartalmat, és a partnertől küld és fogad, az üzenetek a szerződés típusának meg kell egyeznie.

Miután [integrációs fiók létrehozásáról](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) és [partnerek felvétele](logic-apps-enterprise-integration-partners.md), EDIFACT-egyezmény a következő lépésekkel hozhat létre.

## <a name="create-an-edifact-agreement"></a>Az EDIFACT-egyezmény létrehozása 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com "Azure Portal") 

2. Az Azure fő menüjéből válassza **minden szolgáltatás**. A Keresés mezőbe írja be a "integráció" névre, és válassza ki **integrációs fiókok**.

   ![Keresse meg az integrációs fiók](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Ha **minden szolgáltatás** nem jelenik meg, lehetséges, hogy először bontsa ki a menüben. A összecsukott menü tetején válassza **feliratok megjelenítése**.

3. A **integrációs fiókok**, válassza ki az integrációs fiók, ahol szeretné létrehozni a szerződést.

   ![Integrációs fiók hová hozza létre a szerződés kiválasztása](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Válasszon **szerződések**. Ha nem rendelkezik egy szerződés-csempét, először adja hozzá a csempét.   

   ![Válassza a "Szerződés" csempe](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. A szerződések a lapon választhatja ki **Hozzáadás**.

   ![Válassza az "Add"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. Alatt **Hozzáadás**, adjon meg egy **neve** esetében a szerződés hatálya alá. A **Szerződéstípus**válassza **EDIFACT**. Válassza ki a **Gazdagéppartner**, **gazdagép-identitás**, **Vendégpartner**, és **Vendégidentitás** esetében a szerződés hatálya alá.

   ![Adja meg a szerződés részletei](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Tulajdonság | Leírás |
   | --- | --- |
   | Name (Név) |A Szerződés neve |
   | Egyezmény típusa | EDIFACT kell lennie. |
   | Gazdagéppartner |Egy szerződést kell a gazdagép és Vendég partner. A gazdagéppartner a szervezet, amely beállítja a szerződés jelöli. |
   | Gazdagép-identitás |A gazdagéppartner azonosítója |
   | Vendégpartner |Egy szerződést kell a gazdagép és Vendég partner. A vendégpartner a szervezet, amely a fogadó partner üzleti állapotát jelöli. |
   | Vendégidentitás |A vendégpartner azonosítója |
   | Fogadási beállítások |Ezek a tulajdonságok vonatkoznak az összes, a szerződés által fogadott üzeneteket. |
   | Küldési beállítások |Ezek a tulajdonságok a szerződés által küldött összes üzenet vonatkozik. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Hogyan fogadja a szerződés kezeli az üzenetek konfigurálása

Most, hogy beállította a megállapodás tulajdonságai, konfigurálhatja, hogyan azonosítja a jelen szerződés, és kezeli a partner a jelen szerződés keretében kapott bejövő üzenetek.

1. A **Hozzáadás**válassza **fogadási beállítások**.
Konfigurálja ezeket a tulajdonságokat a partnerrel, amely az üzeneteket, a szerződés alapján. Vlastnost leírásáért lásd: e szakasz táblázatai.

   **Beállítások** van szakaszokba rendeztük: Azonosítók, visszaigazoló, sémákat, Ellenőrzőszámok, érvényesítése és belső beállítások.

   ![Konfigurálja a "Kapják meg a beállításokat"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Miután elkészült, ügyeljen arra, hogy a beállítások mentéséhez kiválasztásával **OK**.

Most a szerződés elkészült kezelje a bejövő üzenetek, amelyek megfelelnek a kiválasztott beállításokat.

### <a name="identifiers"></a>Azonosítók

| Tulajdonság | Leírás |
| --- | --- |
| UNB6.1 (címzett referenciajelszava) |Adjon meg egy alfanumerikus érték 1 és 14 karakter közötti. |
| UNB6.2 (címzett referenciaminősítője) |Adjon meg egy legalább egy karakter és legfeljebb két karakterből álló alfanumerikus érték. |

### <a name="acknowledgments"></a>Visszaigazolás

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet visszaigazolása (CONTRL) |Válassza ki ezt a jelölőnégyzetet, a műszaki (CONTRL) visszaigazolás térjen vissza az adatcsere feladójának. A visszajelzés küldése beállítások a szerződés alapján adatcsere feladójának zajlik. |
| Nyugtázás (CONTRL) |Válassza ezt a jelölőnégyzetet, térjen vissza a működési (CONTRL) visszaigazolás adatcsere feladójának a visszajelzés elküldésekor a szerződés küldési beállítások alapján adatcsere feladójának. |

### <a name="schemas"></a>Sémák

| Tulajdonság | Leírás |
| --- | --- |
| UNH2.1 (TÍPUS) |Válasszon ki egy tranzakció beállítása. |
| UNH2.2 (VERSION) |Adja meg az üzenet verziója. (Minimális, egy karaktert; maximális, három karakter). |
| UNH2.3 (RELEASE) |Adja meg az üzenet kiadása. (Minimális, egy karaktert; maximális, három karakter). |
| UNH2.5 (KAPCSOLÓDÓ HOZZÁRENDELT KÓD) |Adja meg a hozzárendelt kódot. (Legfeljebb 6 karakter. Csak alfanumerikus karakterekből állhat). |
| UNG2.1 (ALKALMAZÁS FELADÓAZONOSÍTÓJA) |Adjon meg egy legalább egy karaktert és a egy legfeljebb 35 karakterből álló alfanumerikus érték. |
| UNG2.2 (ALKALMAZÁS FELADÓKÓD-MINŐSÍTŐJE) |Adjon meg egy alfanumerikus érték, egy legfeljebb négy karakter hosszú lehet. |
| SCHEMA |Válassza ki a korábban feltöltött sémát, a társított integrációs fiókból használni kívánt. |

### <a name="control-numbers"></a>Ellenőrzőszámok
| Tulajdonság | Leírás |
| --- | --- |
| Adatcsere ellenőrzőszám ismétlődésének letiltása |Ismétlődő érték letiltja, jelölje be ezt a tulajdonságot. Választásakor a az EDIFACT-dekódolást a művelet ellenőrzi, hogy az adatcsere ellenőrzőszáma (UNB5) kapott adatcserét egy korábban feldolgozott adatcsere ellenőrzőszáma nem egyezik. Ha egyezést észlel, az adatcsere nincs feldolgozva. |
| Ismétlődő UNB5 ellenőrzésének gyakorisága (napokban) |Ha úgy döntött, hogy ismétlődő adatcsere ellenőrzőszámok letiltása, megadhatja a napok számát a megfelelő értékre állítja ezt a beállítást, így az ellenőrzés időpontját. |
| Csoport-ellenőrzőszám ismétlődésének letiltása |A duplikált csoportokhoz ellenőrzőszámok (UNG5) érték letiltja, jelölje be ezt a tulajdonságot. |
| Tranzakciókészlet-ellenőrzőszám ismétlődésének letiltása |Az ismétlődő tranzakciókészlet set ellenőrzőszámok (UNH1) érték letiltja, jelölje be ezt a tulajdonságot. |
| EDIFACT szerinti nyugtázás-ellenőrző szám |Kijelölt tranzakció set referencia számok használatra a nyugtázás, adja meg egy értéket az előtag, a hivatkozási számos és a egy utótagot. |

### <a name="validations"></a>Ellenőrzések

Után minden érvényesítési sorban, egy másik automatikusan hozzáadja. Ha nem ad meg szabályokat, érvényesítési az "Alapértelmezett" sort használ.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenettípus |Válassza ki az EDI-üzenet típusa. |
| EDI-ellenőrzés |Hajtsa végre a séma EDI tulajdonságok, mezőhossz-korlátozásai, üres adatelem és záró elválasztók által meghatározott adattípusok EDI-ellenőrzés. |
| Bővített ellenőrzés |Ha az adattípus nem EDI, ellenőrzés be van kapcsolva az elem követelménynek és ismétlődési, enumerálások és az adatok elem hossza érvényesítési (minimális/maximális) engedélyezett. |
| Kezdő/záró nullák engedélyezése |A többi kezdő vagy záró nulla megőrzi, és szóköz karakter. Ne távolítsa el ezeket a karaktereket. |
| Kezdő/záró nullák levágása |Távolítsa el a kezdő vagy záró nulla, a térköz karaktereket. |
| Záróelválasztó-szabályzat |Záró elválasztók készítése. <p>Válassza ki **nem engedélyezett** időtartamig záró elválasztó karakterek és a kapott adatcsere elválasztók. Ha az adatcsere záró elválasztó karakterek és elválasztók, az adatcsere van deklarálva nem érvényes. <p>Válassza ki **nem kötelező** vagy a záró elválasztó karakterek és elválasztók nélkül érték elfogadásához. <p>Válassza ki **kötelező** mikor kell rendelkeznie a fogadott adatcsere, záró elválasztó karakterek és elválasztók. |

### <a name="internal-settings"></a>Belső beállítások

| Tulajdonság | Leírás |
| --- | --- |
| Üres XML-címkék létrehozása, ha a záró elválasztók engedélyezve vannak |Válassza ki ezt a jelölőnégyzetet, hogy közé tartozik a záró elválasztók üres XML-címkéinek adatcsere feladójának. |
| Adatcsere felosztása tranzakciókészletekre – tranzakciókészletek felfüggesztése hiba esetén|Minden tranzakció beállítása egy külön dokumentu XML-adatcsere a megfelelő boríték alkalmaz a tranzakciókészlet elemzi. Csak a sikertelen érvényesítést tranzakciókészletek felfüggesztése. |
| Adatcsere felosztása tranzakciókészletekre – adatcsere felfüggesztése hiba esetén|Minden tranzakció beállítása egy külön dokumentu XML-adatcsere úgy, hogy alkalmazza a megfelelő boríték elemzi. A teljes adatcsere felfüggesztése, ha egy vagy több tranzakciókészletek az adatcsere érvényesítése sikertelen. | 
| Adatcsere megőrzése – tranzakciókészletek felfüggesztése hiba esetén |Az adatcsere érintetlenül hagyja, az XML-dokumentumok teljes kötegelt adatcserét hoz létre. Csak a sikertelen érvényesítést, miközben továbbra is az összes többi tranzakciókészletek feldolgozni tranzakciókészletek felfüggesztése. |
| Adatcsere megőrzése – adatcsere felfüggesztése hiba esetén |Az adatcsere érintetlenül hagyja, az XML-dokumentumok teljes kötegelt adatcserét hoz létre. A teljes adatcsere felfüggesztése, ha egy vagy több tranzakciókészletek az adatcsere érvényesítése sikertelen. |

## <a name="configure-how-your-agreement-sends-messages"></a>Hogyan a szerződéshez küldi az üzeneteket konfigurálása

Beállíthatja, hogyan azonosítja a jelen szerződés, és kezeli a kimenő üzenetek küldését a jelen szerződés keretében a partnerek számára.

1.  A **Hozzáadás**válassza **küldési beállítások**.
Konfigurálja ezeket a tulajdonságokat a partnerrel, aki adatcseréihez használható üzeneteket, a szerződés alapján. Vlastnost leírásáért lásd: e szakasz táblázatai.

    **Küldési beállítások** van szakaszokba rendeztük: Azonosítók, visszaigazoló, sémákat, borítékok, karakterkészletek és elválasztók, Ellenőrzőszámok és ellenőrzések.

    ![Konfigurálja a "Beállítások küldése"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Miután elkészült, ügyeljen arra, hogy a beállítások mentéséhez kiválasztásával **OK**.

Most már a szerződés elkészült kezelésére, amelyek megfelelnek a kiválasztott beállításokat kimenő üzenetek.

### <a name="identifiers"></a>Azonosítók

| Tulajdonság | Leírás |
| --- | --- |
| UNB1.2 (szintaxis verziója) |Vyberte prosím hodnotu mezi **1** és **4**. |
| UNB2.3 (feladó fordított irányú útválasztási címe) |Adjon meg egy legalább egy karakter és legfeljebb 14 karakterből álló alfanumerikus érték. |
| UNB3.3 (címzett fordított irányú útválasztási címe) |Adjon meg egy legalább egy karakter és legfeljebb 14 karakterből álló alfanumerikus érték. |
| UNB6.1 (címzett referenciajelszava) |Adjon meg egy legalább egy és a egy legfeljebb 14 karakterből álló alfanumerikus érték. |
| UNB6.2 (címzett referenciaminősítője) |Adjon meg egy legalább egy karakter és legfeljebb két karakterből álló alfanumerikus érték. |
| UNB7 (alkalmazás hivatkozásazonosítója) |Adjon meg egy legalább egy karakter és legfeljebb 14 karakterből álló alfanumerikus érték |

### <a name="acknowledgment"></a>Nyugtázása
| Tulajdonság | Leírás |
| --- | --- |
| Üzenet visszaigazolása (CONTRL) |Jelölje be ezt a jelölőnégyzetet, ha az üzemeltetett partneri vár a műszaki (CONTRL) visszaigazolás fogadásához. Ez a beállítás meghatározza, hogy a üzemeltetett partnerrel, aki az üzenetet küldi, a Vendég partnertől nyugtázást kér. |
| Nyugtázás (CONTRL) |Jelölje be ezt a jelölőnégyzetet, ha az üzemeltetett partneri vár a egy működési (CONTRL) visszaigazolásának fogadásához. Ez a beállítás meghatározza, hogy a üzemeltetett partnerrel, aki az üzenetet küldi, a Vendég partnertől nyugtázást kér. |
| SG1-/SG4-hurok létrehozása az elfogadott tranzakciókészletekhez |Ha úgy döntött, hogy kérése a egy működési nyugtázás, válassza ki ezt a jelölőnégyzetet, kényszeríteni SG1-/ SG4-hurok létrehozása az elfogadott tranzakciókészletekhez működési CONTRL nyugták. |

### <a name="schemas"></a>Sémák
| Tulajdonság | Leírás |
| --- | --- |
| UNH2.1 (TÍPUS) |Válasszon ki egy tranzakció beállítása. |
| UNH2.2 (VERSION) |Adja meg az üzenet verziója. |
| UNH2.3 (RELEASE) |Adja meg az üzenet kiadása. |
| SCHEMA |Válassza ki a használandó sémát. Sémákat az integrációs fiókjában található. A sémák eléréséhez először hivatkozás az integrációs fiók a logikai alkalmazáshoz. |

### <a name="envelopes"></a>Borítékok
| Tulajdonság | Leírás |
| --- | --- |
| UNB8 (feldolgozási prioritási kód) |Adja meg, amely nem több mint egy karakter hosszúságúnak betűrend szerinti értékét. |
| UNB10 (kommunikációs egyezmény) |Adjon meg egy alfanumerikus érték legalább egy karakter és legfeljebb 40 karakter. |
| UNB11 (tesztjelző) |A jelölőnégyzet bejelölésével jelezze, hogy a generált adatcsere teszt adatai |
| UNA-szegmens (szolgáltatás sztringjére vonatkozó javaslatok) használata |Válassza ki ezt a jelölőnégyzetet, hozzon létre egy UNA-szegmens küldendő adatcserét. |
| UNG-szegmensek (funkciócsoport-fejléc) használata |A vendégpartner küldött üzenetek funkcionális csoport fejlécének csoportosítási szegmens létrehozása a jelölőnégyzet bejelölésével. A következő értékeket használjuk a UNG-szegmensek létrehozása: <p>A **UNG1**, adjon meg egy legalább egy karakter és legfeljebb hat karakterből álló alfanumerikus érték. <p>A **UNG2.1**, adjon meg egy legalább egy karaktert és a egy legfeljebb 35 karakterből álló alfanumerikus érték. <p>A **UNG2.2**, adjon meg egy alfanumerikus érték, egy legfeljebb négy karakter hosszú lehet. <p>A **UNG3.1**, adjon meg egy legalább egy karaktert és a egy legfeljebb 35 karakterből álló alfanumerikus érték. <p>A **UNG3.2**, adjon meg egy alfanumerikus érték, egy legfeljebb négy karakter hosszú lehet. <p>A **UNG6**, adjon meg egy legalább egy és a egy legfeljebb három karakterből álló alfanumerikus érték. <p>A **UNG7.1**, adjon meg egy legalább egy karakter és legfeljebb három karakterből álló alfanumerikus érték. <p>A **UNG7.2**, adjon meg egy legalább egy karakter és legfeljebb három karakterből álló alfanumerikus érték. <p>A **UNG7.3**, adjon meg egy legalább 1 karakter és legfeljebb 6 karakterből álló alfanumerikus érték. <p>A **UNG8**, adjon meg egy legalább egy karakter és legfeljebb 14 karakterből álló alfanumerikus érték. |

### <a name="character-sets-and-separators"></a>Karakterkészletek és elválasztók

A karakterkészlet nem minden üzenettípus használandó elválasztó külön készletét is megadhatja. Ha egy karakterkészlet egy adott üzenet-séma nincs megadva, az alapértelmezett karakterkészlet használatos.

| Tulajdonság | Leírás |
| --- | --- |
| UNB1.1 (rendszerazonosító) |Válassza ki a kimenő adatcsere a alkalmazni EDIFACT-karakterkészlet. |
| Séma |A legördülő listából válassza ki a sémát. Miután végzett az egyes sorok, a rendszer automatikusan hozzáadja az új sort. A kiválasztott séma, az elválasztók készletének kiválasztása, amelyet szeretne használni, az alábbi elválasztó leírása alapján. |
| Bevitel típusa |Válassza ki egy bemeneti típusát a legördülő listából. |
| Összetevő-elválasztó |Összetett adatok elemek elválasztásához, adja meg egy egyetlen karaktert. |
| Adatelem-elválasztó |Összetett adatok egyszerű elemek elválasztásához, adjon meg egy egyetlen karaktert. |
| Szegmenslezáró |Jelezheti az EDI-szegmens, adjon meg egy egyetlen karaktert. |
| Utótag |Válassza ki a szegmens azonosítóját használt karakter. Ha egy utótagot ad meg, majd a szegmens lezáró adatelem lehet üres. Ha a szegmenslezáró üres, majd ki kell jelölnie egy utótagot. |

### <a name="control-numbers"></a>Ellenőrzőszámok
| Tulajdonság | Leírás |
| --- | --- |
| UNB5 (adatcsere-ellenőrző szám) |Írjon be egy előtagot, az adatcsere-ellenőrzőszám, és a egy utótagot a kívánt értéktartományt. Ezeket az értékeket egy kimenő adatcsere létrehozásához használt. Az előtagot és utótagot megadása nem kötelező, míg az ellenőrzőszám megadása kötelező. Az ellenőrzőszám értéke akkor növekszik, egyes új üzenet; az előtag és az utótag változatlan marad. |
| UNG5 (csoportellenőrző szám) |Írjon be egy előtagot, az adatcsere-ellenőrzőszám, és a egy utótagot a kívánt értéktartományt. Ezekkel az értékekkel a csoport-ellenőrzőszám létrehozásához. Az előtagot és utótagot megadása nem kötelező, míg az ellenőrzőszám megadása kötelező. Az ellenőrzőszám értéke akkor növekszik, egyes új üzenet amíg eléri a maximális érték; az előtag és az utótag változatlan marad. |
| UNH1 (üzenetfejléc hivatkozási száma) |Írjon be egy előtagot, az adatcsere-ellenőrzőszám, és a egy utótagot a kívánt értéktartományt. Ezekkel az értékekkel létrehozni az üzenet üzenetfejléc hivatkozási száma. Az előtagot és utótagot megadása nem kötelező, míg a hivatkozási szám megadása kötelező. A hivatkozási szám értéke akkor növekszik, egyes új üzenet; az előtag és az utótag változatlan marad. |

### <a name="validations"></a>Ellenőrzések

Után minden érvényesítési sorban, egy másik automatikusan hozzáadja. Ha nem ad meg szabályokat, érvényesítési az "Alapértelmezett" sort használ.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenettípus |Válassza ki az EDI-üzenet típusa. |
| EDI-ellenőrzés |Hajtsa végre a séma, mezőhossz-korlátozásai, üres adatelem és záró elválasztók EDI tulajdonságainak által meghatározott adattípusok EDI-ellenőrzés. |
| Bővített ellenőrzés |Ha az adattípus nem EDI, ellenőrzés be van kapcsolva az elem követelménynek és ismétlődési, enumerálások és az adatok elem hossza érvényesítési (minimális/maximális) engedélyezett. |
| Kezdő/záró nullák engedélyezése |A többi kezdő vagy záró nulla megőrzi, és szóköz karakter. Ne távolítsa el ezeket a karaktereket. |
| Kezdő/záró nullák levágása |Távolítsa el a felesleges kezdő vagy záró nulla karakter. |
| Záróelválasztó-szabályzat |Záró elválasztók készítése. <p>Válassza ki **nem engedélyezett** időtartamig záró elválasztó karakterek és az elküldött adatcsere elválasztók. Ha az adatcsere záró elválasztó karakterek és elválasztók, az adatcsere van deklarálva nem érvényes. <p>Válassza ki **nem kötelező** érték, vagy a záró elválasztó karakterek és elválasztók nélkül küldhet. <p>Válassza ki **kötelező** az elküldött adatcsere záró elválasztó karakterek és elválasztók kell rendelkeznie. Ha. |

## <a name="find-your-created-agreement"></a>Keresse meg a létrehozott szerződés

1.  A megállapodás tulajdonságai a beállítás befejezése után a **Hozzáadás** lapon a **OK** véglegesítse a szerződés hatálya alá, és térjen vissza az integrációs fiókban.

    Az újonnan hozzáadott szerződés most már megjelenik a **szerződések** listája.

2.  Az integrációs fiókok áttekintése is megtekintheti a szerződéseket. Az integrációs fiók menüjében válassza a **áttekintése**, majd válassza ki a **szerződések** csempére. 

    ![Válassza a "Szerződés" csempe](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="view-swagger-file"></a>A Swagger-fájl megtekintése
Az EDIFACT-összekötő Swagger részleteinek megtekintéséhez: [EDIFACT](/connectors/edifact/).

## <a name="learn-more"></a>Részletek
* [További információ az Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag")  

