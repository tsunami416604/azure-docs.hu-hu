---
title: A Cortana intelligence |} A Microsoft Docs
description: A Cortana intelligence-ajánlat közzététele.
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
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: a6357640ceeb7c5f1bde4eea85aa5265990935f4
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157156"
---
# <a name="publish-a-cortana-intelligence-offer-using-the-cloud-partner-portal"></a>Használatával a Cloud Partner portálra a Cortana Intelligence-ajánlat közzététele

Ez a cikk ismerteti, hogyan teheti közzé a Cortana Intelligence-ajánlat használatával a Cloud Partner portálra.

## <a name="prerequisites"></a>Előfeltételek

A Cloud Partner portálra támogatja a szerepköralapú hozzáférés a portált, amely lehetővé teszi, hogy a munkatársai közösen dolgozzon a ajánlat közzététele. További információkért lásd: [felhőalapú portál Felhasználók kezelése](./cloud-partner-portal-manage-users.md).

Mielőtt ajánlat nevében a közzétevő tehetők közzé fiókra, egyéni felhasználók számára az egyik \"tulajdonosa\" szerepkör szükséges ahhoz, hogy elfogadja a [használati feltételek](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft adatvédelmi nyilatkozatát](https://www.microsoft.com/privacystatement/default.aspx), és [Microsoft Azure Certified Program szerződése](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="to-start-creating-a-cortana-inteligence-offer"></a>A Cortana Inteligence ajánlat létrehozásának megkezdéséhez

Után minden az előfeltételek teljesülnek, készen áll az Cortana Inteligence ajánlat szerzői műveleteket.

1.  Jelentkezzen be a [cloud partner Portalon](http://cloudpartner.azure.com/).
2.  A bal oldali navigációs sávján válassza **+ új ajánlat**.
3. Válassza ki **a Cortana Intelligence**.
4. Válassza ki a **szerkesztő** lapján a **új ajánlat** nézetre, és konfigurálja a következő beállításokat:
    -   Ajánlat beállításai
    -   Technikai információ
    -   Storefront részletei
    -   Kapcsolatok

    Az előző beállítások minden egyes jelennek meg az űrlap kitöltésekor.  Minden egyes űrlap mezőinek meg egy piros csillaggal jelölt szükséges (\*).

## <a name="to-configure-offer-settings"></a>Az ajánlat beállításainak konfigurálása

Az ajánlat beállításokat biztosít alapvető információkat szeretne az ajánlatot, például egy ajánlat azonosítója, a közzétevő-azonosító és az ajánlat neve.

### <a name="offer-id"></a>Ajánlat azonosítója

Ez a közzétevő-profilon belül az ajánlat egyedi azonosítója.
Ez az azonosító akkor látható, a termék URL-címeket. Csak kisbetűs alfanumerikus karakterekből és kötőjelekből (-) állhat. Az azonosító nem végződhet kötőjellel, és legfeljebb 50 karakter hosszú lehet. 
>[!Note]
>Ez a mező zárolva van, amikor egy ajánlatot élesíti.

**Példa**

Ha a kiadó **contoso** hoz létre egy ajánlatot az Ajánlatazonosító *minta-Cortana Intelligence*, ez fog megjelenni az appsource-ban, mint "https://appsource.microsoft.com/marketplace/apps/**contoso**. *minta-Cortana Intelligence*? lapon = Overview ".

### <a name="publisher-id"></a>Közzétevő azonosítója

Közzétevő profil legördülő listája. Ez az ajánlat a közzétenni kívánt közzétevő profil kiválasztásához használható.

>[!Note]
>Ez a mező zárolva van, amikor egy ajánlatot élesíti.

### <a name="name"></a>Name (Név)

Az ajánlat megjelenített neve. Ez a név a [AppSource](https://appsource.microsoft.com). Legfeljebb 50 karakterből állhat.

Amikor befejezi a biztosít az információkat, az ajánlat beállításait, válassza ki **mentése** továbbléphet az ajánlat a műszaki adatok részére. a beállítás.

## <a name="to-configure-technical-info"></a>Technikai információ konfigurálása

Ez a nézet használatával technikai információkat, amelyek az ajánlat lap jelenik meg. Ez a nézet a következő mezők érvényesek.

### <a name="partner-mpn-id"></a>Partner MPN-azonosító

Ha egy regisztrált Microsoft-partner, nyissa meg a [partnerek webhelyén](https://partners.microsoft.com/) , és jelentkezzen be a Partner szervezet azonosító lekérése Adja meg, hogy az azonosító a **Partner MPN-azonosító**.

### <a name="analytics-components-used"></a>Használt összetevők Analytics

Válassza ki az ajánlat használó összes összetevőt. Válassza ki legalább 1-összetevőt. Válassza ki **Microsoft R** csak akkor, ha az SQL 2016 R Services, HDInsight prémium szintű, Asszony licencek bármelyike használ, vagy a virtuális gépeken futó Asszony.

### <a name="additional-components-used"></a>További használt összetevők

Válassza ki az összes többi összetevő, amely a megoldás használ.

### <a name="customer-name-using-solution"></a>Ügyfél neve megoldással

Adja meg az ügyfél nevét, a megoldás által használt éles környezetben. 

>[!Note]
>Ezek az információk nyilvánosságra kerülését az appsource-ban. Csak tesztelési megoldás használatos.

### <a name="azure-consumption-requirement-met"></a>Azure-használati követelmény teljesül?

Adja meg e állít elő, a megoldás legalább $1 K-ügyfelek havonta Azure-csomag összetevőinek, vagy ha a megoldást használja a Microsoft R.

>[!Note]
>Ezek az információk nyilvánosságra kerülését az appsource-ban. Csak tesztelési megoldás használatos.

### <a name="demo-video-url"></a>Bemutató videó URL-címe

Adjon meg egy URL-címet egy bemutatót a megoldásomat/alkalmazásomat a videó, értékesítés előtti csapata ügyfelek jeleníti meg. 

>[!Note]
>Ezek az információk nyilvánosságra kerülését az appsource-ban. Csak tesztelési megoldás használatos.

#### <a name="demo-video-guidelines"></a>Bemutató videó irányelvek

- Videó hossza kisebb, mint a 15 percnek kell lennie.
- A videó minimálisan módosítani kell a megoldás funkcióival rögzítése. 
- A videó kiemeli a felhasználó által használt funkciók fő szempontja, és az integráció a speciális analitikai összpontosít. 
- Bemutató videók **nem lesz** elérhetővé nyilvánosan az ügyfelek számára, de várhatóan hogyan képviselője a megoldás *lenne* egy potenciális vevő látható. Ennek megfelelően kell parancsfájlalapú és ismételhető.
- Minden képernyő videoszerkesztővel exportálhatók a szabványos videokártya formátumban (például MPEG) hozhat létre a videó használja. 

A következőkben megtudhatja, hogyan hozhat létre a Skype vállalati videók. 

1. [A konferencia kezdete](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
2. [Az asztal megosztását](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
3. [Rögzítés megkezdéséhez](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
4. Miután leállította az felvételt, [közzététel a recording a rögzítés manager használatával](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

Töltse fel a rögzített videó egy szolgáltatás, amely lehetővé teszi, hogy hozzon létre egy megosztott URL-címet. Ha például egy [Vendég hivatkozásra a OneDrive vagy SharePoint](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232).

### <a name="supported-regions"></a>Támogatott régiók

Válassza ki, amely a megoldás támogatja az összes régióban. Legalább 1 régió kiválasztása

### <a name="power-bi-desktop-file-pbix"></a>A Power BI asztali fájl (.pbix)

Ha van ilyen, töltse fel egy .pbix-fájlt. Győződjön meg arról, hogy adatokat importálunk a fájl nem hivatkozott kívülről. A beágyazott jelentés hozunk létre az Ön számára.

### <a name="solution-architecture"></a>Megoldás architektúrája

Egy dokumentum, amely leírja a megoldás architektúrája, feltöltése. Feltöltése a diagram útmutatásként megoldás architektúrája, lásd: [Advanced Analytics megoldás munkafolyamat-sablon](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx).

>[!Note]
>Ezek az információk nyilvánosságra kerülését az appsource-ban. Csak tesztelési megoldás használatos.

### <a name="select-segments"></a>Válassza ki a szegmensek

Válassza ki az összes kapcsolódó iparági szegmens. Ha az alkalmazás bármely fel nem sorolt szegmens, adja meg a szegmens nevét a **mások** mező. Pedig csak a szegmens három szavát.

### <a name="select-business-processes"></a>Válassza ki az üzleti folyamatok

Válassza ki az üzleti folyamatok megoldását jellemző. Ha az alkalmazás a listán nem szereplő folyamatokat, adja meg a folyamat nevét a **mások** mező. Ön csak a folyamat három szavát.

### <a name="trial-info"></a>Próbaverziós adatai

-   **SaaS próbaverziós URL-címe:** Adja meg az URL-címet az alkalmazás kipróbálására nyújt lehetőséget.
-   **Test Drive próbaverziós URL-címe:** Adja meg az URL-címet az alkalmazás Test Drive felhasználói élményt.

Kísérletek kapcsolatos további információkért lásd: **alkalmazástípus** Ez a cikk a következő szakaszban.

Válassza ki a technikai információ a szükséges információkat biztosító befejezi **mentése** továbbléphet az ajánlat az áruház oldala részletes részére. 

## <a name="to-configure-storefront-details"></a>Áruház oldala részletes konfigurálása

### <a name="offer-summary"></a>Ajánlat-összefoglaló

Ez az az ajánlat értékajánlat összegzését. Az ajánlat keresés lapon fog megjelenni. Az összefoglalás maximális hossza 100 karakternél.

### <a name="offer-description"></a>Az ajánlat leírása

Ez az a leírást, amely megjelenik az alkalmazás részletei lapon.
A leírás hossza legfeljebb 1300 karakter lehet.

Vegye figyelembe, hogy ez a mező szükséges html tartalom, például a címkékkel rendelkező \<p\>, \<h1\>, \<h2\>, \<li\>, stb., amely lehetővé teszi, hogy sokkal több tenni a tartalmat presentable. Közzétételi portál csapat dolgozik egy funkció, amellyel lehetővé teszik a előnézete látható az iteratív a tartalom több presentable kirakat részleteinek hozzáadása – eközben bármelyike használható bármilyen online valós idejű html-eszközök például [ http://htmledit.squarefree.com ](http://htmledit.squarefree.com/) megtekintéséhez, hogyan néz meg a leírást.

Javasolt Leírás formátuma érdekében a rendelkezik a szöveges érték alapján alárendelt szakaszokra propositions, amelyek mindegyike egy alárendelt heading kiemelve. Látogatók általában glance "ajánlat összegzése" mezőt és alárendelt fejlécek milyen alkalmazást címek gist lekérni, és miért, érdemes az alkalmazás csak egy gyors áttekintés. Ezért fontos, hogy a felhasználó első\'s figyelmet okot őket egy olvassa be az adatait.

#### <a name="partner-examples"></a>Partner-példák

- [Neal Analytics Készletoptimalizálás](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Plexure kiskereskedelmi személyre szabása](https://appsource.microsoft.com/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [AvePoint közszolgáltatásokat](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

### <a name="industries"></a>Ágazatok

Válassza ki az iparág legjobb igazított az alkalmazást. Ha az alkalmazás több iparágban vonatkozik, hagyja üresen ezt a mezőt.

### <a name="categories"></a>Kategóriák

Válassza ki, amely az alkalmazás a kategóriák. Válasszon egy legfeljebb két kategóriába.

### <a name="app-type"></a>Alkalmazástípus

Válassza ki a próbaverziót, amely az alkalmazás támogatni fogja az appsource-ban. Válassza ki az a következő kísérletek:
- **Ingyenes** azt jelenti, hogy az alkalmazás díjmentes.
- **Próbaverzió** azt jelenti, hogy az ügyfelek próbálja meg a megadott időszakra.
- **A próbaverzió kérése** azt jelenti, hogy a felhasználók kérhetnek az alkalmazás-próbaverzióra.

Partnerek kétféle típusú próbaverziós élményt biztosíthat az appsource-ban.

- A **próbaverzió** lehetőség, más néven **ügyfél vezetett kísérletek (chilei idő szerint)** a következők egyike lehet: 
    - SaaS próbalehetőség
        - Az ügyfél egy URL-cím, vagy a partner kaphatnak. Az ügyfél végighalad AAD összevont egyszeri bejelentkezés, egy ideje bevitel kipróbálására nyújt lehetőséget.
        - Ez az egy több-bérlős, amely a felhasználó konfigurációs/adatok más felhasználóktól elkülöníti az SaaS-alkalmazás. És ez a tapasztalat csak egy részhalmazát, amely csak olvasható műveletekhez biztosít.

        **Példák:**

        - [AFS POP kiskereskedelmi végrehajtása](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview)
        - [AvePoint közszolgáltatásokat](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview). (Ez az alkalmazás élményt nyújt a válogatott egyértelmű elérési úttal felhasználói személyekre szabott egy kiválasztott készletét.)

     - Tesztverzió
        - A (vagy a partner) megoldást, és a egy részhalmazát csomagolható példányosítható appsource-ban Azure Resource Manager-sablonok használatával és. Appsource-ban kezelheti az alkalmazás egy adott partner előfizetés idő boxing és karbantartásával forró/hideg készletét, példány stb.
        - Sablonok és a segítséget, ha ezt a lehetőséget a mintakód is biztosítunk.

        **Példák:**

        - [Neal Analytics Készletoptimalizálás](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)

- A **a próbaverzió iránti kérelme** (**Partner vezetett kísérletek /. LBI**) beállítás azt igényli az ügyfelek számára adja meg a partner követési kapcsolattartási adatok űrlapot. A partner következik, és a egy bemutató- vagy az alkalmazás próbaverzióját biztosít. További információkért lásd: [AppSource próbaverziója forgatókönyv](https://aka.ms/trialexperienceforwebapps) videó összefoglaló jellegű áttekintést.

>[!Note]
>Az adat tartalmazza, amelyek **ügyfél vezetett kísérletek** rendelkezik, mint a lehetséges egy magasabb érdeklődők vonzását **Partner vezetett kísérletek**.


### <a name="help-link-for-your-app"></a>Az alkalmazáshoz Súgó hivatkozásra

Adjon meg egy olyan oldalra, amelyen a súgó információit az alkalmazás URL-címe.

### <a name="supported-countriesregions"></a>Támogatott országok/régiók

Ez a mező az országokban vagy régiókban, ahol az ajánlat lesz elérhető, próba határozza meg.

![Támogatott országok/régiók](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot3.png)

### <a name="supported-languages"></a>Támogatott nyelvek

Válassza ki a nyelvet, amely az alkalmazás támogatja-e. Ha az alkalmazás támogatja a nyelveket, amelyek nem látható a listában, az ajánlat közzététele és e-mail-minket: <appsource@microsoft.com> és jelezze nekünk, más nyelvek támogatása.

### <a name="app-version"></a>Az alkalmazás verziója

Adja meg a verziószám az alkalmazás.

### <a name="products-your-app-works-with"></a>Az alkalmazás együttműködik termékek

Lista adott termék, amely az alkalmazás működik együtt. Legfeljebb 3 termékek listázhatja. Termék listázásához, válassza ki a **plusz jelre** (mellett új) és a egy megnyitott szövegmező jön létre az Ön számára. Adja meg, amely az alkalmazás együttműködik a termék nevét.

### <a name="hide-key"></a>Kulcs elrejtése

Ez az ajánlat előnézeti URL-CÍMÉT az ajánlatot a nyilvánosság elől elrejtendő kombinálva egy kulcsot. Akkor sem a jelszót. Minden olyan alfanumerikus karakterláncnak adhat meg.

### <a name="offer-logo-small"></a>Az ajánlat embléma (kicsi)

Az embléma jelenik meg az alkalmazás keresési oldalon. A png rendszerképformátuma formátuma csak engedélyezett. A lemezkép mérete 48 x 48 képpont.

### <a name="offer-logo-large"></a>Az ajánlat embléma (nagy)

Az embléma jelenik meg az alkalmazás részletei lap. A png rendszerképformátuma formátuma csak engedélyezett. A lemezkép mérete 48 képpont x 48 képpont.

### <a name="video"></a>Videó

Legfeljebb négy videókat tölthet fel. Minden egyes feltölteni kívánt:
- Adja meg a videó neve
- Adjon meg egy URL-címe (YouTube vagy Vimeo csak)
- Adjon meg egy miniatűr videó társítani. A miniatűr kell használnia a png rendszerképformátuma, és annak mérete X 720 képpont 1280 képpont lehet. 

Új video(s) hozzáadásához válassza **+ új**, a következő képernyőfelvételen látható módon.

![Új videó hozzáadása](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot4.png)

### <a name="document"></a>Dokumentum

Legfeljebb három dokumentumok tölthet fel. Minden egyes dokumentum a PDF-fájlformátumot kell használni. Új dokumentum hozzáadása:

- Válassza ki **+ új**
- Adja meg a dokumentum nevét
- Válassza ki **feltöltése** feltölteni egy dokumentumot.

![Új dokumentum hozzáadása](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot5.png)

### <a name="privacy-policy"></a>Adatvédelmi szabályzat

Adja meg az alkalmazás adatvédelmi szabályzat URL-címe

### <a name="terms-of-use"></a>Használati feltételek

Adja meg a használati feltételeket az alkalmazás. Appsource-on ügyfelei elfogadja ezeket a feltételeket, mielőtt az alkalmazás próbálkozhatnak van szükség.

>[!Note]
>Ebben a mezőben fogadja el a tartalom használatával HTML-címkék például html-tartalom < p\>, < h1\>, és a < li\>. Ezekkel a címkékkel formázhatja a tartalom használható. 

### <a name="lead-destination"></a>Cél vezethet

Válassza ki a CRM-rendszerrel, az érdeklődőket a rendszer hol tárolja. 

Válassza ki **Azure Table** használata a CRM-rendszerekhez: A Salesforce, a Marketo vagy a Microsoft Dynamics CRM. 

A CRM-rendszerrel kapcsolatos további információért szeretné használni, válassza ki a következő hivatkozások a támogatott rendszerek egyikét.

-   [Azure-tábla](./cloud-partner-portal-lead-management-instructions-azure-table.md)
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
-   [A Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
-   [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)
