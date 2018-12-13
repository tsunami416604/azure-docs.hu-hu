---
title: Azure-ban és Dynamcis 365 tanácsadási szolgáltatási ajánlat |} A Microsoft Docs
description: Útmutató a meghatározása, és egy Azure-ban vagy Dynamcis 365 tanácsadási szolgáltatási ajánlat közzététele a Cloud Partner portálra.
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
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 7ea4a58f072071f60cf87d1e8817ae19658be5fb
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310419"
---
# <a name="azure-and-dynamics-365-consulting-service-offer"></a>Az Azure és Dynamics 365 tanácsadási szolgáltatási ajánlat

<table> <tr> <td>Ez a szakasz azt ismerteti, hogyan közzététel tanácsadási szolgáltatást vagy a Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace-en</a> vagy a <a href="https://appsource.microsoft.com">az AppSource piactérről</a>. Megoldások a Microsoft alapján <a href="https://dynamics.microsoft.com">Dynamics 365</a>, <a href="https://products.office.com">Office 365</a>, <a href="https://powerbi.microsoft.com">Power BI</a>, és <a href="https://powerapps.microsoft.com">PowerApps</a> jogosultak arra, hogy az appsource-on szerepelnie. Más Microsoft-alapján más ajánlatokra <a href="https://azure.microsoft.com/services">Azure-szolgáltatások</a> is jogosult, ha az Azure Marketplace-en. </td> <td><img src="./media/consulting-services-publishing-offer/consulting-icon1.png"  alt="Microsoft consulting icon" /></td> </tr> </table>


## <a name="publishing-benefits"></a>Közzétételi lehetőségekről

Vagy a Microsoft piacterein való közzététel jelentős előnyökkel jár:

- A vállalat előléptetése a Microsoft márka kihasználva.
- Potenciálisan elérje a több mint 100 millió Office 365 és Dynamics 365-felhasználókat az appsource-ban; érjen el több mint 200 ezer szervezetek Azure Marketplace-en keresztül.
- Kiváló minőségű érdeklődők le ezeket a piactér.
- A szolgáltatások által a Microsoft a mező és telefonos értékesítést csapat előléptetett beolvasása.


## <a name="define-your-consulting-services-offer"></a>A tanácsadási szolgáltatási ajánlat megadása

A csomagolt tanácsadási szolgáltatási ajánlat megadása. Rögzített ár rögzített hatókörét, a rögzített becsült, időtartam összpontosíthat (vagy ingyenes), és elsősorban előtti sales jellegű egyetlen ügyfél számára. Válassza ki, amely már a népszerű és hatékony, új üzleti vezetői, megismételhető csomagolt marketingmódszerek.

## <a name="publish-a-consulting-service-offer"></a>A tanácsadási szolgáltatási ajánlat közzététele

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

Az új ajánlat képernyőn az első lépés, ha az ajánlat identitást.  Az ajánlat identitás három részből áll: **Ajánlat azonosítója**, **közzétevő-azonosító**, és **neve**.  Ezek a részek mindegyike terjed ki az alábbi szakaszokban található.

#### <a name="offer-id"></a>Ajánlat azonosítója

Ezt az azonosítót hoz létre, amikor először küld az ajánlat egyedi neve. Csak kisbetűs alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhatnak, kell állnia. A Ajánlatazonosító meg fognak jelenni az URL-címet, és hatások keresési összetevő motorjának eredménye. Ha például *yourcompanyname\_exampleservice*

Ahogy a példában látható, az ajánlat azonosítója lekérdezi hozzáfűzi a Gyártóazonosítóval egyedi azonosító létrehozása. Ez lehet lefoglalt és indexelik a keresőprogramok állandó hivatkozásként van közzétéve. 

*Miután egy ajánlatot az élő, annak azonosítója nem lehet frissíteni.*

#### <a name="publisher-id"></a>Közzétevő azonosítója

Ez az azonosító van a fiókjával kapcsolatban. Ha a szervezeti fiókkal jelentkezik be van jelentkezve, a Gyártóazonosítóval jelennek meg a legördülő menüből.

#### <a name="name"></a>Name (Név)

Ez a karakterlánc, mi az appsource-on vagy az Azure Marketplace-en ajánlat nevét jeleníti meg.

**Fontos:** Csak adja meg a tényleges szolgáltatás nevét. Szolgáltatás típusa és időtartama, valamint nem tartalmazzák.

Edgewater Fullscope szerint a következő példa bemutatja, hogyan van-e összeállítva ajánlat nevét. Az ajánlat neve jelenik meg:

![Hozzon létre egy új Consulting Services-ajánlat](media/consulting-services-publishing-offer/cppsampleconsultingoffer.png)

Az ajánlat neve négy részből áll:

-   **Időtartam:** – az a **kirakat részletek** a szerkesztő lapján. Időtartam órában, napban vagy hétben a jelöl.
-   **Szolgáltatás típusa:** – meghatározva a **kirakat részletek** a szerkesztő lapján. A szolgáltatások típusok a következők `Assessment`, `Briefing`, `Implementation`, `Proof of concept`, és `Workshop`.
-   **Preposition:** – a véleményező beszúrva
-   **Név:** – az a **ajánlat beállítások** lap.

Az alábbi lista tartalmazza a több jól elnevezett ajánlat neve:

-   Professzionális szolgáltatások alapok: 1 – hr Briefing
-   Felhőplatform-áttelepítés: 1 – hr Briefing
-   A PowerApps és Microsoft Flow: 1 napos Workshop
-   Az Azure Machine Learning-szolgáltatások: 3 – hét PoC
-   A tégla, majd kattintson a kiskereskedelmi megoldást: 1 – hr Briefing
-   Saját adatok importálása: 1 – hét Workshop
-   Felhőbeli elemzési: 3 napos Workshop
-   A Power BI képzés: 3 napos Workshop
-   Értékesítési megoldásról: 1 – hét végrehajtása
-   CRM rövid útmutató: 1 napos Workshop
-   Dynamics 365 for Sales: Értékelés 2 nap

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

Az ajánlat összefoglalásának, amely alatt az ajánlat neve jelenik meg az ajánlat rövid leírása. Egyszerű szöveg használata összefoglaló ajánlat megadásakor, és nem kell sortörést. Az ajánlat összegzéseket és azok megfelelő ajánlat nevét jó példa a következők:

*1. példa*

-   **Ajánlat neve:** Felhőbeli elemzési: 3 napos Workshop
-   **Ajánlat-összefoglaló:** A Microsoft Azure és a Power BI, a jelenlegi környezet és a mini POC értékelése áttekintése.

*2. példa*

-   **Ajánlat neve:** Az Azure IoT ipari: 30 napos a koncepció igazolása
-   **Ajánlat-összefoglaló:** Hozzon létre egy ipari csatlakoztatott termék próbaüzem való biztonságos csatlakozáshoz berendezések a mezőben egy Azure IoT Hub-megoldás az irányítópultok, jelentések és értesítések.

*3. példa*

-   **Ajánlat neve:** Professzionális szolgáltatások: 1 – hr Briefing
-   **Ajánlat-összefoglaló:** Áttekintés és előre konfigurálva, a kiterjesztett Dynamics 365 professzionális szolgáltatások biztosítása a projektek, a számlázással és az erőforrások továbbfejlesztett kezelési műveletek megoldás bemutatója.

*4. példa*

-   **Ajánlat neve:** A világon a Power bi-ban: 4 – hr Workshop
-   **Ajánlat-összefoglaló:** Megkezdheti az első irányítópultját, és ajánlott eljárások megismerése. Legfeljebb 12 diákoknak végzett fejlesztés.

*5. példa*

-   **Ajánlat neve:** A Dynamics és projektek: Értékelés 3 napos
-   **Ajánlat-összefoglaló:** Adatgyűjtési követelmények és professzionális szolgáltatásokat nyújtó cégek és vállalatok projekt adatvezérelt ERP-megoldás.

##### <a name="offer-description"></a>Az ajánlat leírása

A tanácsadási szolgáltatási ajánlat leírása. Egy jó ajánlat leírása az engagement fog kinézni, és mi lesz a termék az ügyfélnek vége pontos részleteit ismerteti. Megismerheti, mit kapnak az ügyfélnek egyértelműen segítenie kell.

E-mail-hivatkozások, illetve nem tartalmazzák telefonszámok részleggel, az ajánlat leírása. Nem lesznek egy gombra az ajánlat, amely feltölti az ügyfél azonosíthatja az ajánlat az érdeklődő felügyeleti célként megadott vezet.

Az ajánlat leírása a Markdown formátumban kell megadnia. Ha nem ismeri a Markdown vagy a HTML-formázását, megtekintheti az erőforrások a [Microsoft docs webhelyén](https://docs.microsoft.com/contribute/how-to-write-use-markdown).

Ezek a formátumok használatával biztosítja az ajánlat maximális olvashatóság érdekében van az ügyfelek.

Az ajánlat leírása rövid tartani, és a maximális karakterszámot, a felhasználók nem szeretne hosszú szöveg formátumhoz. Továbbra is fennáll feltöltését a marketing brosúrák, adatlapok és egyéb dokumentumokat, amelyek az ajánlat mélyebb részletesen ismertetik.

A következő példa bemutatja egy jól szintből ajánlat leírása és a kapcsolódó neve és összegzése:

**Ajánlat neve:** Felhőbeli elemzési: 3 napos Workshop

**Ajánlat-összefoglaló:** A Microsoft Azure és a Power BI, a jelenlegi környezet és a mini POC értékelése áttekintése.

**Az ajánlat leírása:** A 3 napos workshop technikai és üzleti vezetők és az ügyfél létesítményben helyszíni tárolt.

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
Ez az azonosító nem egy jelszót.

#### <a name="listing-details"></a>Lista részletei

**Tanácsadási szolgáltatás típusa**

A Microsoft a fókuszáló kizárólag a rögzített hatókör, meghatározott időtartamra, becsült vagy rögzített ár (vagy ingyenes), és elsősorban előtti-sales-orientált tanácsadási szolgáltatási ajánlatok egyetlen ügyfél számára, és az értékelés, megvalósítás, megvalósíthatósági, amelynek keretében tájékoztatják, vagy Workshop ajánlatok, végzett vagy a helyszínen vagy virtuálisan (tájékoztatói vezetett helyszíni kell lennie).

A következő öt típusú ajánlatok foglalja magában:

-   **Értékelés:** Alkalmazhatósági megoldások meghatározása és becsült költségek és az időzítési ügyfél környezet értékelése.
-   **Tájékoztató:** A megoldás vagy felhasználói érdeklődés keretrendszereket, bemutatók és példák ügyfelektől megrajzolásához tanácsadási szolgáltatás bemutatása. Tájékoztatói vezetett helyszíni kell lennie.
-   **Végrehajtás:** A telepítés befejezéséhez, amely egy teljes mértékben működő megoldást eredményez. A próbaüzem a Microsoft azt javasolja, korlátozza az megoldásokat, amelyekkel egy hét múlva megvalósított vagy kevesebb is lehet.
-   **A koncepció igazolása:** A korlátozott hatókörű megvalósítás meghatározni, ha a megoldás felel meg az ügyfél követelményei.
-   **Workshop:** Egy interaktív engagement ügyfél helyi képzés, tájékoztatói, értékelések vagy bemutatók a vásárlói adatok vagy a környezetben létrehozott tartalmazni képes elvégezni.

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

Ajánlatok jelenhet meg a "Közzététel megszakítva" vagy "Közzététel sikertelen" állapotot a közzétételi folyamat során. Ez az állapot a szokásos részét, és lehetővé teszi a Microsoft az ajánlat szerkesztenie. Ha megjelenik a "Közzététel megszakítva", az ajánlat hagyja az adott állapotban.
