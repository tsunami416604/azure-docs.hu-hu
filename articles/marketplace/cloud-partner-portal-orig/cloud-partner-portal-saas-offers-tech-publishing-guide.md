---
title: SaaS-alkalmazás műszaki közzétételi útmutató |} A Microsoft Docs
description: Ismerteti a piactéren való közzétételéhez SaaS-alkalmazásokat kínál a megfelelő.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 7430e57ceaec8a280c17f1276d503919a9703182
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228895"
---
<a name="saas-application-technical-publishing-guide"></a>SaaS-alkalmazás műszaki közzétételi útmutató
===========================================

Üdvözli a SaaS alkalmazások műszaki közzétételi útmutató! Ez az útmutató célja jelölt és a létező gyártók alkalmazásaik és szolgáltatásaik az appsource-on vagy az Azure Marketplace-ajánlat SaaS-alkalmazások használatával listázásához.

Más Marketplace-ajánlatok áttekintéséhez tekintse meg a [Marketplace közzétételi útmutatójában](https://aka.ms/sellerguide).


<a name="what-are-pre-requisites-for-publishing-a-saas-app"></a>Mik azok a SaaS-alkalmazás-közzététel Előfeltételek?
-------------------------------------------------

Közzétételi portál a portálon, amely lehetővé teszi több személy közösen dolgozzon fel az ajánlat közzétételi szerepköralapú hozzáférést biztosít. További információkért lásd: [felhasználók kezelése](./cloud-partner-portal-manage-users.md). 

Mielőtt egy ajánlatot a kiadó nevében tehetők közzé fiókra, egyéni felhasználók számára az egyik *tulajdonosa* szerepkör szükséges ahhoz, hogy elfogadja a [használati feltételek](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft PrivacyStatement](https://www.microsoft.com/privacystatement/default.aspx), és [a Microsoft Azure Certified ProgramAgreement](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


<a name="creating-an-offer"></a>Ajánlat létrehozása
-----------------

Ez a szakasz ismerteti, hogyan hozhat létre egy új SaaS-alkalmazás ajánlat folyamatán.

![SaaS-ajánlat áttekintése](media/cpp-creating-saas-offers/saas-offer-overview.png)

Az SaaS-alkalmazás az ajánlat az alábbi táblázatban ismertetett öt szakaszokban áll:

| **Az ajánlat szakasz**  | **Leírás**                                                                                                     |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Ajánlat beállításai     | Adja meg egy egyedi nevét és Azonosítóját a SaaS-alkalmazás segítségével                                                                         |
| Technikai információ     | Konfigurálja a Szolgáltatottszoftver-megoldás típust, és adja meg a kapcsolat adatait az alkalmazás segítségével                            |
| Tesztverzió         | Az opcionális szakasz, amely lehetővé teszi, hogy egy szolgáltatás, amely lehetővé teszi az ügyfeleknek tesztelje ajánlatát, mielőtt azok a vásárláskor definiálja. |
| Storefront részletei | A marketing, a jogi, a lead felügyelet és a listaelem szakaszokat tartalmazza:   <br/> – A Marketing szakaszban adja meg a leírását és emblémák megfelelően jelenik meg a Marketplace-en felhasználói portálon vonatkozó ajánlatot szükséges teszi lehetővé.  <br/> – A Lead felügyelet lehetővé teszi, hogy meghatározza a hely hol átirányítása a felhasználó új potenciális ügyfeleket az Azure Marketplace-en teljes felhasználói portálon.  <br/> -A jogi szakaszban adja meg az adatvédelmi szabályzat és a használati időszak jogi dokumentáció teszi lehetővé.  |
| Kapcsolattartó            | Lehetővé teszi, hogy adja meg az ajánlat támogatási kapcsolattartási adatokat.                                                                  |
|  |  |


### <a name="creating-a-new-offer"></a>Egy új ajánlat létrehozása

Bejelentkezés után a Cloud Partner portálra, válassza ki a **új ajánlat** elemet a bal oldali menüsáv, és megjelenik egy menü elérhető ajánlatok. Az alábbi képen egy példa ezeket az ajánlatokat:

![Új SaaS-ajánlat](media/cpp-creating-saas-offers/saas-new-offer.png)

Válassza ki az ajánlat, amely már engedélyezett a listából, és megnyílik egy új ajánlat űrlap.

![Az új SaaS-ajánlat űrlap](media/cpp-creating-saas-offers/saas-new-offer-2.png)

A következő táblázat ismerteti az ajánlat mezők:

| **Az ajánlat mezők** | **Leírás**                                                                                            |
|------------------|----------------------------------------------------------------------------------------------------------- |
| Ajánlat azonosítója         | Közzétevő-profilon belül az ajánlat egyedi azonosítója. Ez az azonosító termék URL-címek és a számlázási jelentésekben látható lesz. Csak kisbetűs alfanumerikus karakterekből és kötőjelekből (-) állhat. Az azonosító nem végződhet kötőjellel, és legfeljebb 50 karakter hosszú lehet. Vegye figyelembe, hogy ez a mező után egy ajánlatot élesíti zárolva van. Például ha egy kiadó, a Contoso, ajánlat azonosítója minta virtuális gép egy ajánlatot tesz közzé, azt fog megjelenni, az Azure piactéren: [https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview](https://azuremarketplace.microsoft.com/) |
| Közzétevő azonosítója     | A Gyártóazonosítóval a Marketplace-en az egyedi azonosítója. Az ajánlatok kell lennie az összes csatlakoztatott a közzétevő-azonosítót. Nem lehet módosítani a Gyártóazonosítóval, miután a rendszer menti az ajánlatot.                                                                                       |
| Name (Név)             | Ez az ajánlat megjelenített neve. Ez a név fog megjelenni, az Azure Marketplace-en és az Azure Portalon. Legfeljebb 50 karakterből állhat. Itt útmutatást, hogy a termék felismerhető márkanév tartalmazza. Itt a vállalat neve ne tartalmazza, hacsak nem értékesített hogyan. Ha ez az ajánlat a saját webhelyen található vannak marketing, ellenőrizze, hogy a név pontosan hogyan megjelenik a webhelyen.             |
|  |  |

Kattintson a **mentése** a folyamat mentéséhez. Ez a szakasz azt ismerteti, hogy az ajánlat hozzáadását tervezi.


### <a name="technical-information"></a>Technikai információ

A technikai információ szakaszban adja meg a következőket teszi lehetővé:

![SaaS-ajánlat technikai információ](media/cpp-creating-saas-offers/saas-new-offer-technical-info.png)

A legfontosabb döntés, hogy fog egy SaaS-lista vagy kereskedelmi engedélyezve van. Ha Ön SaaS szerepel a címtárban, jelöljön ki között:

-   Ingyenes – a SaaS-alkalmazás, ahol magonkénti az alkalmazás URL-Címének megadása.
-   Ingyenes próba - adjon meg egy URL-címét a SaaS-alkalmazás, ahol keresztül futtatható a próbaverzió, az ajánlat megvásárlása előtt.
-   Velem a kapcsolatot – csak akkor érvényes, ha egy érdeklődő rendszer csatlakoztatva van, ez a beállítás lehetővé teszi, hogy az ügyfelek számára, kérje meg, ha értesítenének, és a egy érdeklődő Önnel.

Ha Ön egy SaaS-alkalmazás, amely az Azure Marketplace-en, és szeretné engedélyezni a keresztül a Microsoft tranzakció, válassza ki a kereskedelmi **értékesítheti Azure-on keresztül**.  
Az SaaS-alkalmazás csatlakoztatása kapcsolatos további információkért lásd: [SaaS - értékesítheti Azure-on keresztül](./cloud-partner-portal-saas-offer-subscriptions.md).


### <a name="test-drive"></a>Tesztverzió

Egy próbaverziós megoldást vezet be az ügyfelek számára az ajánlott eljárás az, győződjön meg, hogy az általuk is megvásárolható magabiztosan. Próbaverziós lehetőségekről Test Drive az Érdeklődők generálása kiváló minőségű, a leghatékonyabb, és ezek nagyobb átalakítás vezet.

A termék főbb funkciók és előnyök, mutatja be egy való életből vett megvalósítási forgatókönyv gyakorlati, önálló irányítású próbaverzió ügyfelek biztosít.

![SaaS-ajánlat kipróbálása](media/cpp-creating-saas-offers/saas-new-offer-test-drive.png)


#### <a name="how-a-test-drive-works"></a>A Test Drive működése

A potenciális ügyfelek keres, és felderíti az alkalmazás a Marketplace-en. Az ügyfél jelentkezik be, és elfogadja a használati feltételeket. Ezen a ponton az ügyfél megkapja az előre konfigurált környezet rögzített számú óra, próbálja ki a szerepeltetendő magas minősített érdeklődő kap közben.

![SaaS ajánlat test Drive 2](media/cpp-creating-saas-offers/saas-new-offer-test-drive-2.png)

Függetlenül attól, hogy milyen bonyolult az alkalmazás van a Microsoft Test Drive segít a termék keltse életre az ügyfél számára. Nincsenek elérhető Tesztverzió három különböző típusú, minden egyes termék, a forgatókönyv és a piactér típusa alapján.

-   **Az Azure Resource Manager** – egy Azure ARM Test Drive egy központi telepítési sablont, amely tartalmazza az összes Azure-erőforrások, amelyek tartalmazzák a megoldás a közzétevő létrehozása folyamatban. Termékek, amelyek illeszkednek a Test Drive az ilyen típusú, amelyekről csak Azure-erőforrások használatához.
-   **Logikai alkalmazás** – A logikai alkalmazás Tesztverziós olyan központi telepítési sablont, amely magában foglalja az összes összetett megoldások létrehozására szolgáló architektúrák hivatott. Az összes Dynamics-alkalmazások vagy egyéni termékek Test Drive az ilyen típusú kell használnia.
-   **Power bi-ban** – A Power BI Test Drive egy személyre szabott irányítópult mutató beágyazott hivatkozást tartalmaz. Minden olyan terméket, amely egy interaktív Power BI vizuális kell használnia a Test Drive az ilyen típusú bemutatásához szeretne. A feltöltendő szüksége a Power BI embedded URL-CÍMÉT.

A test Drive hozzáadásának a fő közzétételi lépései a következők:

1.  A Test Drive forgatókönyv meghatározása
2.  Hozhat létre és/vagy a Resource Manager-sablon módosítása
3.  A Test Drive részletes manuális létrehozása
4.  Tegye közzé újra az ajánlatot

További információkért lásd: [Test Drive](./what-is-test-drive.md).


### <a name="storefront-details"></a>Storefront részletei

SaaS-alkalmazások összefoglalását és leírást, hogy az alkalmazása adni az első két szakaszait kell.

![SaaS-ajánlat kirakat részletei](media/cpp-creating-saas-offers/saas-new-offer-storefront-details.png)

A következő táblázat ismerteti az ajánlat\'s kirakat részletei:

| **Az ajánlat mezők**        | **Leírás**                                                                                          |
|-------------------------| ---------------------------------------------------------------------------------------------------------|
| Ajánlat-összefoglaló           | Az ajánlat értékajánlat összefoglalása. Az ajánlat keresés lapon fog megjelenni. Legfeljebb 100 karakterből kell lennie.   |
| Az ajánlat leírása       | Az leírást, amely megjelenik az alkalmazások részletei lapon. Engedélyezett maximális hossza 1300 karaktert *Megjegyzés* ebben a mezőben fogadja el a HTML-tartalmat a címkéket például & ltp\>, & lth1\>, & lth2\>, & ltli\>, stb., amely lehetővé teszi sokkal presentable tenni a tartalmat. Közzétételi portál csapat dolgozik egy funkció, amellyel lehetővé teszik a előnézete látható az iteratív a tartalom több presentable kirakat részleteinek hozzáadása – eközben bármelyike használható bármilyen online valós idejű HTML-eszközök például http://htmledit.squarefree.com megtekintéséhez a leírás hogyan jelenne meg. |
| Ágazatok              | Válassza ki a legjobb igazított az ajánlat iparágak. Ha az alkalmazás több iparágban vonatkozik, maximum kettőt kiválaszthatja. |
| Javasolt kategóriák    | Válassza ki a kategóriákat, amelyek az ajánlat legjobb igazodik. Legfeljebb három kategóriák közül választhat.     |
| Alkalmazás verziója     | Adja meg az alkalmazás verziószáma                                                                |
| A keresési kulcsszavak (max. 3) | Adja meg legfeljebb három keresési kulcsszavakat, amellyel az ügyfelek az alkalmazás keresése a piactéren kirakat webhelyen. |
|  |  |


### <a name="marketing-artifacts"></a>Marketing-összetevők

A marketing összetevők szakasz lehetővé teszi, hogy az Azure piactéren, marketing-eszközeivel, mint az emblémát, a videók, a képernyőképek és a dokumentumok definiálja:

![SaaS-ajánlat marketing összetevők](media/cpp-creating-saas-offers/saas-new-offer-marketing-artifacts.png)

A következő táblázat ismerteti a Marketing mezőket:

| **Az ajánlat mezők** | **Leírás**                                                                                                          |
|------------------| ------------------------------------------------------------------------------------------------------------------------ |
| Emblémát            | Ha Ön egy **értékesítheti Azure-on keresztül** SaaS-alkalmazás, adjon meg az összes embléma rendszerkép. Ha csak egy listát, mindössze 2 emblémák szükség. A Cloud Partner portálra feltöltött összes emblémát kell használnia az alábbi irányelveket:     <br/> -Tarthatja számát az elsődleges és másodlagos színt az embléma alacsony. Az Azure arculata egyszerű színpalettát használ.     <br/> -Kerülje a fekete, az embléma háttérszíne fehér. Az Azure Portal a témák színei fekete-fehér. Ehelyett használja az egyes szín, amely biztosítja, az embléma neves az Azure Portalon. Javasoljuk az egyszerű alapszínek használatát. Ha áttetsző háttérrel használ, akkor ügyeljen arra, hogy az embléma és a szöveg nem lesznek fekete, a fehér vagy a kék.     <br/> – Háttér átmenetének ne használja az embléma.     <br/> -Elkerüli a szöveg, még akkor is a vállalat vagy a márkanév, az embléma. Az embléma megjelenését és működését "egyszerű" kell lennie, és kerülje átmenetekhez.    <br/> – A emblémakép nem kell nyújtani.                   |
| Videók           | Az Ön ajánlatát a videók hivatkozások hozzáadását teszi lehetővé. YouTube-on és/vagy Vimeo videók, az ügyfelek számára láthatók és az ajánlat mutató hivatkozásokat is használhatja. Emellett adja meg a 1280 x 720 képpont méretű png-kép a videó egy miniatűr képére kell. Legfeljebb négy videót ajánlat / rendelkezhet. |
| Dokumentumok        | Marketing dokumentumok hozzáadása az ajánlat lehetővé teszi. Minden dokumentum PDF formátumban kell megadni, és legfeljebb három dokumentumok ajánlat rendelkezhet.                                                                                                                                                      |
| Képernyőképek      | Az ajánlat pillanatképeiért hozzáadását teszi lehetővé. Nincs legfeljebb öt képernyőképek ajánlat / adható hozzá. A maximális képméret 1280 x 720 képpont.                                                                                                                                             |
| Hasznos oldalak     | Az ajánlat az architektúra-diagramok, mutasson a, vagy egyéb webhely, amely egy ügyfél szeretné tekintse meg a külső URL-címek hozzáadását teszi lehetővé.                                                                                                                                                              |
|  |  |

A következő kép bemutatja, hogyan jelenjen meg az adatokat egy Marketplace keresési eredményben:

![SaaS-ajánlat marketing közzétevő adatait](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info.png)

A következő kép bemutatja, hogyan az ajánlat jelenjen meg a Marketplace-en, miután egy ügyfél a kisebb ajánlat csempére kattint:

![SaaS-ajánlat marketing közzétevő info2](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info-2.png)


### <a name="lead-management-and-legal-information"></a>Felügyelet és-– jogi információk

A jogi szakaszban állítsa be az ajánlat jogi dokumentáció teszi lehetővé.
SaaS-alkalmazás ajánlatok szükséges két jogi dokumentumok: adatvédelmi irányelveket és a használati feltételeket. További információkért lásd:  
[Ügyfélérdeklődések konfigurálása](./cloud-partner-portal-get-customer-leads.md).

![SaaS-ajánlat marketing jogi információ](media/cpp-creating-saas-offers/saas-new-offer-legal-info.png)

A következő táblázat ismerteti a jogi szakasz tulajdonságai:

| **Az ajánlat mezők**   | **Leírás**                                                                                 |
|--------------------| ----------------------------------------------------------------------------------------------- |
| Adatvédelmi szabályzat URL-címe | A vállalata adatvédelmi szabályzat URL-címe.                                                       |
| Használati feltételek       | A használati feltételeket, az ajánlatban. Írja be vagy másolja be a használati feltételeket itt. Alapszintű HTML használata engedélyezett, például, hogy ez a mező veszi html tartalom a címkéket például & ltp\>, & lth1\>, & lth2\>, & ltli\>stb. *Fontos* erősen ajánlott, hogy tekintse át, és a egy böngészőben az ajánlat elküldése előtt létrehozott HTML megtekintése. |
|  |  |


### <a name="contact-information"></a>Kapcsolattartási adatok

Ebben a szakaszban kell megadnia a támogatási kapcsolatba lép a cégtől, amely felelős támogató ügyfelek élvezhetik az ajánlat előnyeit.
Három fő területen: Engineering Contact támogatási kapcsolattartó és támogatási URL-címek:

![SaaS-ajánlat marketing kapcsolattartási adatok](media/cpp-creating-saas-offers/saas-new-offer-contact-info.png)


| **Contact**         | **Leírás**                                                                                                                          |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| Engineering Contact | Adja meg a nevét, a e-mailek és a egy Microsoft támogatási és az üzleti problémáit kérhetnek-mérnöki kapcsolattartó telefonszáma. |
| Támogatási kapcsolattartó     | Adja meg a nevét, e-mail, telefonszám, és támogatja az URL-címet, az ügyfelek a kapcsolatot, ha van támogatási kérelmeket.                  |
|  |  |


Készen áll az ajánlatot, és kattintson a közzététel után hitelesítő végighalad az ajánlatot. Teszteljük a SaaS-alkalmazás tesztelése a URL-CÍMÉT, ha szerepel a címtárban, vagy ha rendelkezik Azure-on keresztül árul végpontok kiválasztott körül manuális ellenőrzést keresztül. A manuális jóváhagyás során azt is döntse el, az alkalmazás kell jelennek meg az (AppSource, Azure Marketplace-en vagy mindkettő) megfelelő megjelennie.

<a name="updating-the-offer"></a>Az ajánlat frissítése
------------------

Számos különböző típusú frissítések, amelyeket érdemes tennie az ajánlatra, miután közzé lett téve, és azt élő. Az ajánlat új verziójának a módosítás kell menteni és újra közzé, hogy tükrözze a Marketplace-en.

<a name="deleting-an-existing-offer"></a>Egy meglévő ajánlat törlése
--------------------------

Dönthet úgy, távolítsa el az ajánlatot a piactérről. Ajánlat eltávolítása azt jelenti, hogy új ügyfeleket nem vásárolhat vagy üzembe helyezni az ajánlatot, azonban ez nem befolyásolja a már meglévő ügyfeleket. Az ajánlat megszüntetése a szolgáltatás és/vagy az Ön és ügyfelei közötti licencszerződés megszüntetését jelenti.

Útmutatás és ajánlatok eltávolítását és megszüntetését házirendek vonatkoznak rájuk a Microsoft Marketplace kiadói szerződésében (lásd 7. szakasz) és a részvételi szabályzatának megfelelően (lásd a szakasz 6.2). Ez a témakör a különböző támogatott delete forgatókönyvek és lépések kapcsolatos információkat, számukra is igénybe vehet.

### <a name="delete-the-live-offer"></a>Az élő ajánlat törlése

Számos különféle szempontok igénylő kell elvégzi, ha egy kérelem egy élő ajánlat eltávolítása. Kövesse az útmutatást kaphat a támogatási csapat egy élő ajánlat eltávolítása az Azure Marketplace-ről az alábbi lépéseket:

1.  Létre egy támogatási jegyet, ezen hivatkozás használatával
2.  Válassza a Problem Type lista **ajánlatok kezelésével**, kategória listájában válassza ki a **módosítása egy ajánlat és/vagy a Termékváltozat már éles környezetben**.
3.  A kérelem elküldése

A támogatási csapat végigvezeti Önt az ajánlat törlése folyamatban.

> [!NOTE] 
> Az ajánlat törlése nem befolyásolja a jelenlegi vásárlások az adott ajánlat. Ezen vásárlásokkal továbbra is működnek, mint korábban.
Azonban az ajánlat nem érhető el minden olyan új vásárlások a Törlés befejezése után.
