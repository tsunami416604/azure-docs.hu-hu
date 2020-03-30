---
title: EDIFACT üzenetek a B2B integrációhoz
description: EDIFACT-üzenetek cseréje EDI formátumban a B2B vállalati integrációhoz az Azure Logic Apps alkalmazásokkal a vállalati integrációs csomaggal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/26/2016
ms.openlocfilehash: 3ada12a0cde122fb78815a1d3241d8acb9da2580
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651457"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>EDIFACT-üzenetek Exchange EDIFACT üzenetek a B2B vállalati integrációhoz az Azure Logic Apps vállalati integrációs csomaggal

EdIFACT-üzenetek cseréje előtt az Azure Logic Apps, létre kell hoznia egy EDIFACT-szerződést, és tárolja a megállapodást az integrációs fiókban. Az EDIFACT-szerződés létrehozásának lépéseit az alábbiakban olvashatja.

> [!NOTE]
> Ez az oldal az Azure Logic Apps EDIFACT funkcióit ismerteti. További információ: [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Előkészületek

Itt vannak a szükséges elemek:

* Az Azure-előfizetéshez már definiált és társított [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md)  
* Legalább két [partner,](logic-apps-enterprise-integration-partners.md) akik már definiálva vannak az integrációs fiókban

> [!NOTE]
> Amikor létrehoz egy megállapodást, a partnernek kapott vagy küldött üzenetek tartalmának meg kell egyeznie a megállapodás típusával.

Miután [létrehozott egy integrációs fiókot,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) és [partnereket adott hozzá,](logic-apps-enterprise-integration-partners.md)az alábbi lépések végrehajtásával létrehozhat egy EDIFACT-szerződést.

## <a name="create-an-edifact-agreement"></a>EDIFACT-szerződés létrehozása 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com "Azure portál") 

2. Az Azure főmenüjében válassza a **Minden szolgáltatás lehetőséget.** A keresőmezőbe írja be az "integráció" szót, majd válassza **az Integrációs fiókok lehetőséget.**

   ![Az integrációs fiók megkeresése](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Ha nem jelenik meg **az Összes szolgáltatás,** előfordulhat, hogy először ki kell bontania a menüt. Az összecsukott menü tetején válassza a **Szövegcímkék megjelenítése**lehetőséget.

3. Az **Integrációs fiókok csoportban**válassza ki azt az integrációs fiókot, amelyhez létre szeretné hozni a szerződést.

   ![Válassza ki az integrációs fiókot, ahol létre szeretné hozni a megállapodást](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Válassza a **Megállapodások lehetőséget.** Ha nem rendelkezik Megállapodások csempével, először adja hozzá a csempét.   

   ![Válassza a "Megállapodások" csempe](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. A Megállapodások lapon válassza a **Hozzáadás gombot.**

   ![Válassza a "Hozzáadás" lehetőséget](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. A **Hozzáadás**csoportban adja meg a szerződés **nevét.** A **Megállapodás típushoz**válassza az **EDIFACT**lehetőséget. Válassza ki a **házigazda partnert,** **a fogadóidentitást**, **a vendégpartnert**és a **vendégidentitást** a szerződéshez.

   ![A megállapodás részleteinek megadása](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Tulajdonság | Leírás |
   | --- | --- |
   | Név |A megállapodás neve |
   | Megállapodás típusa | EdIFACT-nek kell lennie |
   | Fogadó partner |A megállapodáshoz házigazda és vendégpartner is szükséges. A gazdapartner a szerződést konfiguráló szervezetet jelöli. |
   | Állomás identitása |A gazdapartner azonosítója |
   | Vendégpartner |A megállapodáshoz házigazda és vendégpartner is szükséges. A vendégpartner képviseli azt a szervezetet, amely a fogadó partnerrel üzleti kapcsolatban áll. |
   | Vendég identitása |A vendégpartner azonosítója |
   | Fogadási beállítások |Ezek a tulajdonságok a megállapodás által fogadott összes üzenetre vonatkoznak. |
   | Beállítások küldése |Ezek a tulajdonságok a megállapodás által küldött összes üzenetre vonatkoznak. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>A szerződés által fogadott üzenetek kezelésének konfigurálása

Most, hogy beállította a szerződés tulajdonságait, beállíthatja, hogy ez a megállapodás hogyan azonosítja és kezelje a partnertől a jelen szerződés keretében kapott bejövő üzeneteket.

1. A **Hozzáadás**csoportban válassza a **Fogadási beállítások lehetőséget.**
Konfigurálja ezeket a tulajdonságokat az Önnel üzeneteket cserélő partnerrel kötött megállapodás alapján. A tulajdonságleírásokat lásd az ebben a szakaszban található táblázatokban.

   **A fogadási beállítások** a következő szakaszokba vannak rendezve: Azonosítók, Nyugtázás, Sémák, Vezérlőszámok, Érvényesítés és Belső beállítások.

   ![A "Fogadási beállítások" konfigurálása](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Miután elkészült, az **OK**gombra választva mentse a beállításokat.

Most már a megállapodás készen áll a kiválasztott beállításoknak megfelelő bejövő üzenetek kezelésére.

### <a name="identifiers"></a>Azonosítók

| Tulajdonság | Leírás |
| --- | --- |
| UNB6.1 (címzett hivatkozási jelszava) |Írjon be egy 1 és 14 karakter közötti alfanumerikus értéket. |
| UNB6.2 (Címzett hivatkozási minősítője) |Írjon be egy legalább egy és legfeljebb két karakterből álló alfanumerikus értéket. |

### <a name="acknowledgments"></a>Köszönetnyilvánítás

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet fogadása (CONTRL) |Jelölje be ezt a jelölőnégyzetet, ha technikai (CONTRL) elismervényt szeretne visszaküldeni a csomópont küldőjének. A nyugta a megállapodás Küldési beállításai alapján kerül elküldésre a csomópont feladójának. |
| Nyugtázás (CONTRL) |Jelölje be ezt a jelölőnégyzetet, ha egy funkcionális (CONTRL) nyugtát szeretne visszaadni a csomópont küldőjének: A rendszer a megállapodás Küldési beállításai alapján elküldi a visszaigazolást a csomópont küldőjének. |

### <a name="schemas"></a>Sémák

| Tulajdonság | Leírás |
| --- | --- |
| UNH2.1 (TÍPUS) |Válasszon egy tranzakciókészlet-típust. |
| UNH2.2 (VERZIÓ) |Adja meg az üzenet verziószámát. (Minimum, egy karakter; maximum, három karakter). |
| UNH2.3 (kiadás) |Adja meg az üzenet kiadási számát. (Minimum, egy karakter; maximum, három karakter). |
| UNH2.5 (TÁRSÍTOTT HOZZÁRENDELT KÓD) |Adja meg a hozzárendelt kódot. (Maximum hat karakter. Alfanumerikus nak kell lennie). |
| UNG2.1 (APP SENDER-azonosító) |Írjon be egy legalább egy karakterből és legfeljebb 35 karakterből álló alfanumerikus értéket. |
| UNG2.2 (ALKALMAZÁSKÜLDŐKÓD-MINŐSÍTŐ) |Írjon be egy alfanumerikus értéket, legfeljebb négy karakterrel. |
| Séma |Válassza ki a korábban feltöltött sémát, amelyet használni szeretne a társított integrációs fiókból. |

### <a name="control-numbers"></a>Vezérlőszámok

| Tulajdonság | Leírás |
| --- | --- |
| Csomópont-ellenőrzési szám ismétlődések tiltása |Az ismétlődő csomópontok blokkolásához jelölje ki ezt a tulajdonságot. Ha be van jelölve, az EDIFACT dekódolási művelet ellenőrzi, hogy a fogadott adatcsere-ellenőrző szám (UNB5) nem egyezik-e meg egy korábban feldolgozott adatcsere-ellenőrzési számmal. Ha egyezést észlel, a rendszer nem dolgozza fel a csomópontot. |
| Ellenőrizze, hogy nem tartalmaz-e ismétlődő UNB5-öt minden (nap) |Ha úgy döntött, hogy leállítja az ismétlődő bankközi vezérlőszámokat, megadhatja, hogy hány napig végezze el az ellenőrzést a beállításmegfelelő értékmegadásával. |
| Csoportvezérlőszám ismétlődésének leengedése |Az ismétlődő csoportvezérlőszámokkal (UNG5) rendelkező csomópontok blokkolásához jelölje be ezt a tulajdonságot. |
| A Tranzakciókészlet vezérlőszámának duplikált példányai |Az ismétlődő tranzakciókészlet-vezérlőszámokkal (UNH1) rendelkező csomópontok blokkolásához jelölje be ezt a tulajdonságot. |
| EDIFACT nyugtázás-ellenőrzési szám |A nyugtázásban használt tranzakciókészlet hivatkozási számok meghatározásához adjon meg egy értéket az előtaghoz, egy hivatkozási számtartományhoz és egy utótaghoz. |

### <a name="validation"></a>Ellenőrzés

Az egyes ellenőrzési sorok befejezésekor a program automatikusan hozzáad egy másikat. Ha nem ad meg szabályokat, akkor az érvényesítés az "Alapértelmezett" sort használja.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet típusa |Válassza ki az EDI-üzenet típusát. |
| EDI érvényesítése |Edi-érvényesítést végezhet a séma EDI tulajdonságai, hosszkorlátozásai, üres adatelemei és záró elválasztók által meghatározott adattípusokon. |
| Kiterjesztett érvényesítés |Ha az adattípus nem EDI, az érvényesítés az adatelem követelményén és az engedélyezett ismétlésen, enumeráláson és adatelemhossz-érvényesítésen (min/max) történik. |
| Kezdő/záró nullák engedélyezése |Őrizze meg a további kezdő vagy záró nulla és szóköz karaktereket. Ne távolítsa el ezeket a karaktereket. |
| Sortávolság/záró nullák vágása |A kezdő vagy záró nulla és szóköz eltávolítása. |
| Záró elválasztó házirend |Záró elválasztók létrehozása. <p>Válassza **a Nem engedélyezett** lehetőséget, ha meg szeretné tiltani a záró határolókat és elválasztókat a fogadott csomópontban. Ha a csomópontnak záró határolói és elválasztói vannak, akkor az áttér és a csomópont érvénytelennek minősül. <p>Válassza **a Választható** lehetőséget a záró határolókkal és elválasztókkal rendelkező vagy anélkül eső csomópontok fogadásához. <p>Válassza **a Kötelező lehetőséget,** ha a fogadott intercsere záró határolókkal és elválasztókkal rendelkezik. |

### <a name="internal-settings"></a>Belső beállítások

| Tulajdonság | Leírás |
| --- | --- |
| Üres XML-címkék létrehozása, ha a záró elválasztók engedélyezettek |Jelölje be ezt a jelölőnégyzetet, ha azt szeretné, hogy a csomópont-küldő üres XML-címkéket tartalmazzon a záró elválasztókhoz. |
| Csomópont felosztása tranzakciókészletekként - tranzakciókészletek felfüggesztése hiba esetén|A tranzakciókészletben beállított tranzakciókat külön XML-dokumentumba elemzi a megfelelő boríték nak a tranzakciókészletre való alkalmazásával. Csak azokat a tranzakciókészleteket függessze fel, amelyek nem érvényesítése sikertelen. |
| Csomópont felosztása tranzakciókészletekként - a csomópont felfüggesztése hiba esetén|A megfelelő boríték alkalmazásával elemzi a csomópontban beállított tranzakciókat egy külön XML-dokumentumba. A teljes adatcsere felfüggesztése, ha a csomópont egy vagy több tranzakciókészlete nem érvényesíti az ellenőrzést. | 
| Adatcsere megőrzése - tranzakciókészletek felfüggesztése hiba esetén |Érintetlenül hagyja a csomópontot, létrehoz egy XML-dokumentumot a teljes kötegelt csomóponthoz. Csak azokat a tranzakciókészleteket függessze fel, amelyek nem érvényesítése sikertelen, miközben folytatja az összes többi tranzakciókészlet feldolgozását. |
| Csomópont megőrzése - a csomópont felfüggesztése hiba esetén |Érintetlenül hagyja a csomópontot, létrehoz egy XML-dokumentumot a teljes kötegelt csomóponthoz. A teljes adatcsere felfüggesztése, ha a csomópont egy vagy több tranzakciókészlete nem érvényesíti az ellenőrzést. |

## <a name="configure-how-your-agreement-sends-messages"></a>A szerződés üzenetküldési módjának beállítása

Beállíthatja, hogy ez a szerződés hogyan azonosítsa és kezelje a partnereknek a jelen szerződés keretében küldött kimenő üzeneteket.

1.  A **Hozzáadás**csoportban válassza a **Küldési beállítások lehetőséget.**
Konfigurálja ezeket a tulajdonságokat az Önnel üzeneteket cserélő partnerével kötött megállapodás alapján. A tulajdonságleírásokat lásd az ebben a szakaszban található táblázatokban.

    **A Küldési beállítások** a következő szakaszokba vannak rendezve: azonosítók, nyugtázás, sémák, borítékok, karakterkészletek és elválasztók, vezérlőszámok és érvényesítések.

    ![A "Beállítások küldése" beállítása](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Miután elkészült, az **OK**gombra választva mentse a beállításokat.

Most már a megállapodás készen áll a kiválasztott beállításoknak megfelelő kimenő üzenetek kezelésére.

### <a name="identifiers"></a>Azonosítók

| Tulajdonság | Leírás |
| --- | --- |
| UNB1.2 (szintaktikai verzió) |**Válasszon** egy 1 és **4**közötti értéket. |
| UNB2.3 (feladó fordított útválasztási címe) |Írjon be egy legalább egy karakterből és legfeljebb 14 karakterből álló alfanumerikus értéket. |
| UNB3.3 (címzett fordított útválasztási címe) |Írjon be egy legalább egy karakterből és legfeljebb 14 karakterből álló alfanumerikus értéket. |
| UNB6.1 (címzett hivatkozási jelszava) |Írjon be egy legalább egy és legfeljebb 14 karakterből álló alfanumerikus értéket. |
| UNB6.2 (Címzett hivatkozási minősítője) |Írjon be egy legalább egy és legfeljebb két karakterből álló alfanumerikus értéket. |
| UNB7 (kérelem hivatkozásazonosító) |Írjon be egy legalább egy karakterből és legfeljebb 14 karakterből álló alfanumerikus értéket. |

### <a name="acknowledgment"></a>Elismerés

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet fogadása (CONTRL) |Jelölje be ezt a jelölőnégyzetet, ha a szolgáltatott partner technikai (CONTRL) nyugtázásra számít. Ezzel a beállítással megadhatja, hogy az üzenetet küldő vendégpartner nyugtázást kér a vendégpartnertől. |
| Nyugtázás (CONTRL) |Jelölje be ezt a jelölőnégyzetet, ha a hosztolt partner funkcionális (CONTRL) nyugtát vár. Ezzel a beállítással megadhatja, hogy az üzenetet küldő vendégpartner nyugtázást kér a vendégpartnertől. |
| SG1/SG4 hurok létrehozása elfogadott tranzakciókészletekhez |Ha úgy döntött, hogy funkcionális nyugtázást kér, jelölje be ezt a jelölőnégyzetet, ha az elfogadott tranzakciókészletek funkcionális CONTRL nyugtázásában kényszerítheti az SG1/SG4 hurkok létrehozását. |

### <a name="schemas"></a>Sémák

| Tulajdonság | Leírás |
| --- | --- |
| UNH2.1 (TÍPUS) |Válasszon egy tranzakciókészlet-típust. |
| UNH2.2 (VERZIÓ) |Adja meg az üzenet verziószámát. |
| UNH2.3 (kiadás) |Adja meg az üzenet kiadási számát. |
| Séma |Válassza ki a használni kívánt sémát. A sémák az integrációs fiókban találhatók. A sémák eléréséhez először kapcsolja össze az integrációs fiókot a logikai alkalmazással. |

### <a name="envelopes"></a>Borítékok

| Tulajdonság | Leírás |
| --- | --- |
| UNB8 (Prioritáskód feldolgozása) |Írjon be egy karakternél nem több karakterhosszú betűrendes értéket. |
| UNB10 (kommunikációs megállapodás) |Írjon be egy legalább egy karakterből és legfeljebb 40 karakterből álló alfanumerikus értéket. |
| UNB11 (tesztjelző) |Jelölje be ezt a jelölőnégyzetet, ha azt szeretné jelezni, hogy a létrehozott adatcsere tesztadat. |
| UNA szegmens alkalmazása (Service String Advice) |Jelölje be ezt a jelölőnégyzetet, ha una szegmenst szeretne létrehozni a csomópont elküldéséhez. |
| UNG-szegmensek alkalmazása (függvénycsoport fejléce) |Jelölje be ezt a jelölőnégyzetet, ha csoportosítási szegmenseket szeretne létrehozni a funkcionális csoport fejlécében a vendégpartnernek küldött üzenetekben. Az UNG-szegmensek létrehozásához a következő értékek et használjuk: <p>**Az UNG1 mezőben**adjon meg egy legalább egy karakterből és legfeljebb hat karakterből álló alfanumerikus értéket. <p>Az **UNG2.1**mezőben adjon meg egy legalább egy karakterből és legfeljebb 35 karakterből álló alfanumerikus értéket. <p>Az **UNG2.2**mezőben írjon be egy alfanumerikus értéket, legfeljebb négy karakterrel. <p>Az **UNG3.1**mezőben adjon meg egy legalább egy karakterből és legfeljebb 35 karakterből álló alfanumerikus értéket. <p>Az **UNG3.2**mezőben írjon be egy alfanumerikus értéket, legfeljebb négy karakterrel. <p>**Az UNG6**mezőben adjon meg egy legalább egy és legfeljebb három karakterből álló alfanumerikus értéket. <p>Az **UNG7.1**mezőben adjon meg egy legalább egy karakterből és legfeljebb három karakterből álló alfanumerikus értéket. <p>**Az UNG7.2**mezőben adjon meg egy legalább egy karakterből és legfeljebb három karakterből álló alfanumerikus értéket. <p>**Az UNG7.3**mezőben adjon meg egy legalább 1 karakterből és legfeljebb 6 karakterből álló alfanumerikus értéket. <p>**Az UNG8**mezőben adjon meg egy legalább egy karakterből és legfeljebb 14 karakterből álló alfanumerikus értéket. |

### <a name="character-sets-and-separators"></a>Karakterkészletek és elválasztók

A karakterkészleten kívül az egyes üzenettípusokhoz más-más határolójelet is megadhat. Ha egy adott üzenetsémához nincs megadva karakterkészlet, akkor a rendszer az alapértelmezett karakterkészletet használja.

| Tulajdonság | Leírás |
| --- | --- |
| UNB1.1 (rendszerazonosító) |Válassza ki a kimenő adatcsere esetén alkalmazni kívánt EDIFACT karakterkészletet. |
| Séma |Válasszon ki egy sémát a legördülő listából. Az egyes sorok befejezése után a program automatikusan új sort ad hozzá. A kijelölt sémához válassza ki a használni kívánt elválasztó készletet az alábbi elválasztó leírások alapján. |
| Bevitel típusa |Válasszon egy bemeneti típust a legördülő listából. |
| Komponens elválasztó |Az összetett adatelemek szétválasztásához írjon be egyetlen karaktert. |
| Adatelem-elválasztó |Ha az egyszerű adatelemeket az összetett adatelemeken belül szeretné elválasztani, írjon be egyetlen karaktert. |
| Szegmens terminátor |Az EDI szegmens végét jelző karaktert írjon be. |
| Utótag |Válassza ki a szegmensazonosítóhoz használt karaktert. Ha utótagot jelöl ki, akkor a szegmensterminátor adatelem üres lehet. Ha a szegmensterminátor üresen marad, akkor ki kell jelölnie egy utótagot. |

### <a name="control-numbers"></a>Vezérlőszámok

| Tulajdonság | Leírás |
| --- | --- |
| UNB5 (csomópont-ellenőrzési szám) |Adjon meg egy előtagot, egy értéktartományt az interchange vezérlőszámhoz, és egy utótagot. Ezek az értékek kimenő csomópont létrehozásához használatosak. Az előtag és az utótag nem kötelező, míg a vezérlőszám szükséges. A vezérlőszám minden új üzenetnél növekszik; az előtag és az utótag ugyanaz marad. |
| UNG5 (csoportellenőrzési szám) |Adjon meg egy előtagot, egy értéktartományt az interchange vezérlőszámhoz, és egy utótagot. Ezek az értékek a csoportvezérlő számának létrehozásához használatosak. Az előtag és az utótag nem kötelező, míg a vezérlőszám szükséges. A vezérlőszám minden új üzenetnél növekszik, amíg el nem éri a maximális értéket; az előtag és az utótag ugyanaz marad. |
| UNH1 (üzenetfejléc hivatkozási száma) |Adjon meg egy előtagot, egy értéktartományt az interchange vezérlőszámhoz, és egy utótagot. Ezek az értékek az üzenetfejléc hivatkozási számának létrehozására szolgálnak. Az előtag és az utótag nem kötelező, míg a hivatkozási szám szükséges. A hivatkozási szám minden új üzenetnél növekszik; az előtag és az utótag ugyanaz marad. |

### <a name="validation"></a>Ellenőrzés

Az egyes ellenőrzési sorok befejezésekor a program automatikusan hozzáad egy másikat. Ha nem ad meg szabályokat, akkor az érvényesítés az "Alapértelmezett" sort használja.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenet típusa |Válassza ki az EDI-üzenet típusát. |
| EDI érvényesítése |Edi-érvényesítést hajtson végre a séma EDI tulajdonságai, a hosszkorlátozások, az üres adatelemek és a záró elválasztók által meghatározott adattípusokon. |
| Kiterjesztett érvényesítés |Ha az adattípus nem EDI, az érvényesítés az adatelem követelményén és az engedélyezett ismétlésen, enumeráláson és adatelemhossz-érvényesítésen (min/max) történik. |
| Kezdő/záró nullák engedélyezése |Őrizze meg a további kezdő vagy záró nulla és szóköz karaktereket. Ne távolítsa el ezeket a karaktereket. |
| Sortávolság/záró nullák vágása |A kezdő vagy záró nulla karakterek eltávolítása. |
| Záró elválasztó házirend |Záró elválasztók létrehozása. <p>Válassza **a Nem engedélyezett** lehetőséget, ha le szeretné tiltani a záró határolókat és elválasztókat az elküldött csomópontban. Ha a csomópontnak záró határolói és elválasztói vannak, akkor az áttér és a csomópont érvénytelennek minősül. <p>Válassza **a Választható** lehetőséget, ha az interpikátorokat záró határolókkal vagy elválasztókkal vagy anélkül szeretné küldeni. <p>Válassza a **Kötelező** lehetőséget, ha az elküldött interklennek záró határolókkal és elválasztókkal kell rendelkeznie. |

## <a name="find-your-created-agreement"></a>A létrehozott szerződés megkeresése

1.  Miután befejezte a szerződés összes tulajdonságának beállítását, a **Hozzáadás** lapon válassza az **OK** gombot a szerződés létrehozásának befejezéséhez és az integrációs fiókhoz való visszatéréshez.

    Az újonnan hozzáadott megállapodás most megjelenik a **Megállapodások** listában.

2.  A megállapodásokat az integrációs fiók áttekintésében is megtekintheti. Az integrációs fiók **menüjében válassza az Áttekintés parancsot,** majd válassza a **Megállapodások csempét.** 

    ![Válassza a "Megállapodások" csempe](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részleteket, például az összekötő Swagger-fájljában leírt műveleteket és korlátokat az [összekötő referencialapján](https://docs.microsoft.com/connectors/edifact/)találja.

> [!NOTE]
> [Az integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)