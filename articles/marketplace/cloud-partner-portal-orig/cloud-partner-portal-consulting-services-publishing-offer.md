---
title: Tanácsadási szolgáltatások |} A Microsoft Docs
description: Az útmutató meghatározása és a egy tanácsadási szolgáltatási ajánlat közzététele.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: b3f6b9166afce9ca0cdeaa1c9dd6e10a5a976453
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810587"
---
<a name="defining-and-publishing-your-consulting-services-offer"></a>Meghatározása és a tanácsadási szolgáltatási ajánlat közzététele
======================================================

Ez az útmutató célja határozza meg, és a tanácsadási szolgáltatási ajánlat közzététele az a Cloud Partner portálra.

<a name="define-your-consulting-services-offer"></a>A tanácsadási szolgáltatási ajánlat megadása
-------------------------------------

A csomagolt tanácsadási szolgáltatási ajánlat megadása. Rögzített ár rögzített hatókörét, a rögzített becsült, időtartam összpontosíthat (vagy ingyenes), és elsősorban előtti sales jellegű egyetlen ügyfél számára. Válassza ki, amely már a népszerű és hatékony, új üzleti vezetői, megismételhető csomagolt marketingmódszerek.

<a name="publish-a-consulting-service-offer"></a>A tanácsadási szolgáltatási ajánlat közzététele
----------------------------------

A következő szakaszok ismertetik a tanácsadási szolgáltatási ajánlat közzétételi folyamata

1.  Új ajánlat létrehozása
2.  Az ajánlat beállításainak megadása
3.  Adja meg, Storefront részleteit, és hogy tegye közzé az Azure Marketplace-en vagy az appsource-ban.
4.  Az ajánlat közzététele

### <a name="create-a-new-offer"></a>Új ajánlat létrehozása

Hozzon létre egy új ajánlatunk, hajtsa végre az alábbi lépéseket:

1.  A Cloud Partner portálra főmenüjében válassza **új ajánlat**.
2.  Válassza ki az új ajánlat menü **tanácsadási szolgáltatás**.

    ![Hozzon létre egy új Consulting Services-ajánlat](media/consulting-services-publishing-offer/cppselectnewconsultingoffer.png)

### <a name="define-offer-settings"></a>Az ajánlat beállításainak megadása

Az új ajánlat képernyőn az első lépés, ha az ajánlat identitást.
Az ajánlat identitás három részből áll:

1.  Ajánlat azonosítója
2.  Közzétevő azonosítója
3.  Name (Név)

Ezek a részek mindegyike terjed ki az alábbi szakaszokban található.

#### <a name="offer-id"></a>Ajánlat azonosítója

Ez a hoz létre, amikor először küld az ajánlat egyedi nevét. Csak kisbetűs alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhatnak, kell állnia. Az azonosító az URL-címe látható lesz, és hatások keresési összetevő motorjának eredménye. Ha például *yourcompanyname\_exampleservice*

Ahogy a példában látható, az ajánlat azonosítója hozzáfűzi lekérdezi a a Gyártóazonosítóval hozhat létre egy egyedi azonosítója. Ez lehet lefoglalt és indexelik a keresőprogramok állandó hivatkozásként van közzétéve.

**Az ajánlat nem élő után is\'t frissíteni kell**

#### <a name="publisher-id"></a>Közzétevő azonosítója

Ez a fiók kapcsolódik. Ha a szervezeti fiókkal jelentkezik be van jelentkezve, a Gyártóazonosítóval jelennek meg a legördülő menüből.

#### <a name="name"></a>Name (Név)

Ez a mi jelenik meg: az ajánlat neve az appsource-on vagy az Azure Marketplace-en.

**Fontos:** csak adja meg a tényleges szolgáltatás nevét. Szolgáltatás típusa és időtartama, valamint nem tartalmazzák.

Edgewater Fullscope szerint a következő példa bemutatja, hogyan van-e összeállítva ajánlat nevét. Az ajánlat neve jelenik meg, ehhez hasonló:

![Hozzon létre egy új Consulting Services-ajánlat](media/consulting-services-publishing-offer/cppsampleconsultingoffer.png)

Az ajánlat neve négy részből áll:

-   **Időtartam:** határozhat meg ezt a szerkesztő kirakat Részletek lapján. Időtartam órában, napban vagy hétben a jelöl.
-   **Szolgáltatás típusa:** határozhat meg ezt a szerkesztő kirakat Részletek lapján. A szolgáltatások típusok a következők: értékelés, Briefing, megvalósítás, a koncepció igazolása és Workshopra.
-   **Preposition:** szúrhat be a felülvizsgáló
-   **Name:** Ez az ajánlat beállítások lapján definiált.

Az alábbi lista néhány jól elnevezett ajánlat nevét tartalmazza:

-   A professzionális szolgáltatások Essentials: 1-Hr Briefing
-   Felhőplatform-áttelepítés: 1-Hr Briefing
-   A PowerApps és a Microsoft Flow: 1 napos Workshop
-   Az Azure Machine Learning-szolgáltatások: 3 – hét PoC
-   A tégla, majd kattintson a megoldás kiskereskedelmi: 1-Hr Briefing
-   Saját adatok importálása: 1 – hét Workshop
-   Felhőbeli elemzési: 3 napos Workshop
-   A Power BI képzés: 3 napos Workshop
-   Értékesítési megoldásról: 1 – hét végrehajtása
-   CRM rövid útmutató: 1 napos Workshop
-   Dynamics 365 for Sales: 2 nap-értékelés

Befejezése után a **ajánlat beállítások** lapon mentheti a beküldött elemet. Az ajánlat neve megjelenik a szerkesztő felett, és visszaállítja az összes kínál található.

### <a name="enter-storefront-details"></a>Adja meg a Storefront részletei

Ezután meg kell adja meg, hogy az áruház oldala. Az áruház oldala részletei a következő szakaszok áll:

-   Az ajánlat részletei
-   Közzétevői adatokkal
-   Lista részletei
-   Marketing-összetevők

#### <a name="offer-details"></a>Az ajánlat részletei

Az ajánlat részletei szakasz a következő mezőket tartalmazzák:

-   Ajánlat-összefoglaló
-   Az ajánlat leírása

##### <a name="offer-summary"></a>Ajánlat-összefoglaló

Az ajánlat összefoglalásának, amely alatt az ajánlat neve jelenik meg az ajánlat rövid leírása. Egyszerű szöveges kell használnia, az ajánlat-összefoglaló beírásakor, és nem tartalmazhat sortörést. Az ajánlat összegzéseket és azok megfelelő ajánlat nevét jó példa a következők:

*1. példa*

-   **Ajánlat neve:** Felhőanalitika: 3 napos Workshop
-   **Ajánlat-összefoglaló:** áttekintése a Microsoft Azure és a Power BI, a jelenlegi környezet és a mini POC értékelése.

*2. példa*

-   **Ajánlat neve:** ipari Azure IoT: 30 napos a koncepció igazolása
-   **Ajánlat-összefoglaló:** hozzon létre egy ipari csatlakoztatott termék próbaüzem való biztonságos csatlakozáshoz berendezések a mezőben egy Azure IoT Hub-megoldás az irányítópultok, jelentések és értesítések.

*3. példa*

-   **Ajánlat neve:** szakmai szolgáltatásoknál: 1-Hr Briefing
-   **Ajánlat-összefoglaló:** áttekintés és bemutató előre konfigurálva van, a Dynamics 365 kiterjesztett Operations megoldást biztosít a projektek, a számlázás és professzionális szolgáltatások erőforrásait felügyeleti bővített.

*4. példa*

-   **Ajánlat neve:** Power bi-ban a világ: 4 – Hr Workshop
-   **Ajánlat-összefoglaló:** megkezdheti az első irányítópultját, és ajánlott eljárások megismerése. Legfeljebb 12 diákoknak végzett fejlesztés.

*5. példa*

-   **Ajánlat neve:** Dynamics és projektek: 3 napos értékelése
-   **Ajánlat-összefoglaló:** adatgyűjtési követelmények és professzionális szolgáltatásokat nyújtó cégek és vállalatok projekt adatvezérelt ERP-megoldás.

##### <a name="offer-description"></a>Az ajánlat leírása

Ez az, hogy a tanácsadási szolgáltatási ajánlat leírása. Egy jó ajánlat leírása az engagement fog kinézni, és mi lesz a termék az ügyfélnek vége pontos részleteit ismerteti. Megismerheti, mit kapnak az ügyfélnek egyértelműen segítenie kell.

E-mail-hivatkozások, illetve nem tartalmazzák telefonszámok részleggel, az ajánlat leírása. Nem lesznek egy gombra az ajánlat, amely feltölti az ügyfél azonosíthatja az ajánlat az érdeklődő felügyeleti célként megadott vezet.

Az ajánlat leírása a Markdown formátumban kell megadnia. Ha nem ismeri a Markdown vagy a HTML-formázását, megtekintheti az erőforrások a [Microsoft docs webhelyén](https://docs.microsoft.com/contribute/how-to-write-use-markdown).

Ezek a formátumok használatával biztosítja az ajánlat maximális olvashatóság érdekében van az ügyfelek.

Az ajánlat leírása rövid tartani, és a maximális karakterszámot, a felhasználók nem tetszett, olvassa el a sok szöveget tartalmazó formátumhoz. Továbbra is fennáll feltöltését a marketing brosúrák, adatlapok és egyéb dokumentumokat, amelyek az ajánlat mélyebb részletesen ismertetik.

A következő példa bemutatja egy jól szintből ajánlat leírása és a kapcsolódó neve és összegzése:

**Ajánlat neve:** Felhőanalitika: 3 napos Workshop

**Ajánlat-összefoglaló:** áttekintése a Microsoft Azure és a Power BI, a jelenlegi környezet és a mini POC értékelése.

**Ajánlat leírása:** a 3 napos workshop technikai és üzleti vezetők számára, és az ügyfél létesítményben helyszíni tárolt.

***Napirend***

1. napi

-   Biztonságos, méretezhető és az Azure Data Lake, a HDInsight vagy Azure SQL Data Warehouse a Microsoft-felhőben adatok rendezése összpontosít.

2. napon

-   Bemutatja, hogyan konfigurálhatja és telepítheti a Microsoft R és az Azure Machine Learning a fejlett elemzési megoldásokat.

3 nap

-   Bemutatja, hogyan rajzolhat gyakorlatban hasznosítható elemzéseket nyújt, és a Power bi-ban, beleértve a közös hozhat létre a Power BI-irányítópultra egy együttműködésen alapuló munkamenet analytics üzembe helyezése.

A workshop végén az ügyfél fogja tudni magas szintű tervének és a egy data és analitikai megoldások megvalósításának ütemterv megadása a Microsoft-felhőben.

*Markdown-fájl számára egy ajánlatot, ez a formátum a következő mintát:*

    This 3-day workshop is for technical and business leaders and is held on-site at the client’s facility.

      ### Agenda

      **Day 1**

      * Focuses on how to secure, scale, and organize data within the Microsoft cloud using Azure Data Lake, HDInsight, or Azure SQL Data Warehouse

      **Day 2**

      * Covers how to configure and deploy advanced analytics solutions with Microsoft R and Azure Machine Learning

      **Day 3**

      * Covers how to draw actionable insights and operationalize analytics with Power BI, including a collaborative session to co-build a Power BI dashboard.


      ### Deliverables
      By the end of the workshop, the client will be able to define a high-level plan and an implementation roadmap for data and analytics solutions in the Microsoft cloud.


#### <a name="publisher-information"></a>**Közzétevői adatokkal**

**MPN-AZONOSÍTÓ**

A 9 számjegyű Microsoft Partner Network (MPN) azonosítóját. Ha nem rendelkezik az MPN-azonosító, Microsoft Partner Centeren beszerezni egy léphet.

**Partnerközpont azonosítója**

Új Partner Center-azonosító, ha rendelkezik ilyennel.

**MPN-AZONOSÍTÓ**

Adjon meg egy titkos kulcsot, mielőtt továbblépne élő az appsource-ban az ajánlat kipróbálásához.
Ez a jelszó nem.

#### <a name="listing-details"></a>Lista részletei

**Tanácsadási szolgáltatás típusa**

A Microsoft a fókuszáló kizárólag a rögzített hatókör, meghatározott időtartamra, becsült vagy rögzített ár (vagy ingyenes), és elsősorban előtti-sales-orientált tanácsadási szolgáltatási ajánlatok egyetlen ügyfél számára, és az értékelés, megvalósítás, megvalósíthatósági, amelynek keretében tájékoztatják, vagy Workshop ajánlatok, végzett vagy a helyszínen vagy virtuálisan (tájékoztatói vezetett helyszíni kell lennie).

A következő öt típusú ajánlatok foglalja magában:

-   **Értékelés:** ügyfél környezet alkalmazhatósági megoldások meghatározása és becsült költségek és az időzítési értékelését.
-   **Tájékoztató:** megoldást vagy ügyfél érdeklődés keretrendszereket, bemutatók és példák ügyfelektől megrajzolásához tanácsadási szolgáltatás bemutatása. Tájékoztatói vezetett helyszíni kell lennie.
-   **Megvalósítás:** teljes telepítéséhez, amely egy teljes mértékben működő megoldást eredményez. A próbaüzem a Microsoft azt javasolja, korlátozza az megoldásokat, amelyekkel egy hét múlva megvalósított vagy kevesebb is lehet.
-   **A koncepció igazolása:** meghatározni, ha a megoldás felel meg az ügyfél követelményei és A korlátozott hatókörű megvalósítás.
-   **Workshop:** egy interaktív engagement be tevékenységeik az ügyfél helyszíni képzés, tájékoztatói, értékelések vagy a vásárlói adatok vagy a környezetben létrehozott bemutatókat tartalmazó.

**Elérhetőség ország/régió**

Válassza ki az ország és régió, ahol az tanácsadási szolgáltatási ajánlat elérhető lesz. Egyetlen ajánlat több országban vagy régióban nem tehetők közzé. Minden ország vagy régió egy új ajánlatot kell létrehozni.

**Ágazatok**

Válassza ki, hogy a tanácsadási szolgáltatást kínál, akkor alkalmazható, az iparágban.

**Időtartam**

Válasszon ki egy számot (például 3, 4, stb.), időtartama és majd óra, nap vagy hét alatt.

**Az elsődleges termékek**

Válassza ki az Azure piactéren való közzétételéhez **Azure** elsődleges termék, valamint majd érintett területekről.

Közzététele az appsource-ba, válassza ki a **Dynamics 365**, **Power BI**, vagy **PowerApps**az elsődleges üzemel. Egyéb releváns vonatkozó termék is választhat, és a tanácsadási szolgáltatást kínál, ezeket a termékeket, az appsource-on társított listaelemek jelennek meg.

**Releváns kompetenciák**

Válassza ki a megfelelő, azok mellett az ajánlat részleteit is megjelenik ez az ajánlat kompetenciák.

#### <a name="marketing-artifacts"></a>Marketing-összetevők

**Vállalati emblémát (.png formátumú, 48 x 48 képpont)**

Töltsön fel egy képet, a csempére az ajánlat az ajánlat katalógus nézet lapon fog megjelenni. A lemezkép-48 x 48 képpont felbontású .png képnek kell lennie.

**A vállalati embléma a vállalati embléma (.png formátumú, 216 x 216 képpont)**

Töltse fel az Ön ajánlatát a részletező lapján megjelenő kép. A kép 216 x 216 képpont felbontású rendelkező .png képnek kell lennie.

**Videók (max. 4)**

Legfeljebb négy ügyfél esettanulmány videók vagy ügyfél hivatkozási videók feltöltését. Ha nem rendelkezik ilyennel, elmagyarázza a vállalat szakértői az ajánlathoz kapcsolódó videó feltöltése. Ha egy Power bi-ban vagy a PowerApps bemutatónkban, töltse fel a bemutató videót itt. Hivatkozások videó YouTube vagy Vimeo kell lennie.

**Dokumentumok (max. 3)**

Töltse fel a marketing ismertetőben, hogy a tanácsadási szolgáltatási ajánlat részletesen ismerteti. Másik lehetőségként is feltölthet cége ismertetőjét, adatlapok és esettanulmányok.

**Képernyőképek (max. 5)**

Töltse fel az ajánlatra, az ajánlat le vagy a vállalat további információt adhat meg legfeljebb öt lemezképbe. Ha például kódrészlettel, amely a marketing ismertetőben, egy megfelelő dia bemutató, vagy egy kép a vállalat hír vagy szakértelemmel.

### <a name="publish-your-offer"></a>Az ajánlat közzététele

Miután megadta a beállításokat kínálnak, Storefront adatait és partnerek, válassza ki a **közzététel** , és adjon meg egy e-mail címet. Ha készen áll az ajánlat közzététele a Microsoft, kapni fog egy e-mailt az előzetes verzióra, mielőtt élő kerül. A portálon ellenőrizheti az állapotot az Ön ajánlatát időpontban bármikor visszatérhet.

Ajánlatok jelenhet meg a "Közzététel megszakítva" vagy "Közzététel sikertelen" állapotot a közzétételi folyamat során. Ez a folyamat normál része, és lehetővé teszi a Microsoft az ajánlat szerkesztenie. Ha megjelenik a "Közzététel megszakítva", az ajánlat hagyja az adott állapotban.
