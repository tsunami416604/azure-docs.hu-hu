---
title: Power BI-alkalmazásajánlat létrehozása – Azure Piactér
description: Megtudhatja, hogy miként hozhat létre és tehet közzé Power BI-alkalmazásajánlatot a Microsoft AppSource alkalmazásban.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: dc036ae3cba6aa4d3a689562afffb991fadc8c0a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867601"
---
# <a name="create-a-power-bi-app"></a>Power BI-alkalmazás létrehozása

> [!IMPORTANT]
> Power BI-alkalmazásajánlatainak kezelését áthelyezzük a Cloud Partner Portalról a Partnerközpontba. Az ajánlatok áttelepítéséig kövesse a [Power BI-alkalmazásajánlat létrehozása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-create-offer) a Cloud Partner Portal számára című útmutató utasításait az ajánlatok kezeléséhez.

Ez a cikk azt ismerteti, hogy miként hozhat létre és tehet közzé Power BI-alkalmazásajánlatot a Microsoft [AppSource alkalmazásban.](https://appsource.microsoft.com/)

A Power BI-alkalmazásajánlat létrehozása előtt kereskedelmi piactéri fiókkal kell rendelkeznie a Partnerközpontban. Ha még nem hozott létre ilyet, olvassa [el A kereskedelmi piactér-fiók létrehozása a Partnerközpont-fiókban](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) című témakört.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [Partnerközpontba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **Kereskedelmi piactér** > **áttekintése parancsot.**

    :::image type="content" source="media/power-bi-menu-overview.png" alt-text="a kereskedelmi piactér menüjének áttekintése" border="false":::

3. Válassza a **+ Új ajánlat** > **Power BI App lehetőséget.** Megjelenik **az Új ajánlat** párbeszédpanel.

> [!IMPORTANT]
> Ha a **Power BI Alkalmazás** beállítás nem jelenik meg&#39;, vagy nincs engedélyezve&#39;, a fiók nak nem&#39;engedélye az ajánlattípus létrehozására. Kérjük, ellenőrizze, hogy&#39;teljesítette-e az ajánlattípus összes [követelményét,](create-power-bi-app-overview.md) beleértve a fejlesztői fiókregisztrálását is.

### <a name="offer-id-and-alias"></a>Ajánlatazonosító és alias

Adja meg **az ajánlatazonosítót.** Ez a fiókban lévő minden egyes ajánlat egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a piactéri ajánlat és az Azure Resource Manager-sablonok webcímében, ha vannak ilyenek.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásjeleket, de szóközöket nem, és legfeljebb 50 karakter ből állhat. Ha például beírja az **1-es tesztajánlatot,** az ajánlat webcíme a lesz. `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- Az ajánlatazonosító nem módosítható, miután a Létrehozás lehetőséget **választja.**

Adja meg **az ajánlat aliasát**. Ez a név a Partnerközpontban található ajánlatra vonatkozik.

- Ez a név nem használatos a piactéren, és eltér az ajánlat neve és egyéb értékek jelennek meg az ügyfelek számára.
- Ez nem módosítható a **Létrehozás (Létrehozás) lehetőséget**követően.

Miután megadta ezt a két értéket, válassza a **Létrehozás** gombot az Ajánlat áttekintő lapján.

## <a name="offer-overview"></a>Ajánlat áttekintése

Az **Ajánlat áttekintése** lap az ajánlat közzétételéhez szükséges lépések (befejezett és közelgő) vizuális megjelenítését, valamint az egyes lépések befejezéséhez szükséges lépéseket jeleníti meg.

Ez magában foglalja a linkeket műveletek elvégzésére ezt az ajánlatot alapján a kiválasztás. Például:

- Ha az ajánlat piszkozat - [Ajánlattervezet törlése](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Ha az ajánlat élő - [Hagyja abba az ajánlat értékesítését](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Ha az ajánlat előzetes verzióban érhető el - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Ha még nem fejezte be a kiadói kijelentkezést – [Közzététel megszakítása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Ajánlat beállítása

Az ajánlat beállításához kövesse az alábbi lépéseket.

### <a name="connect-lead-management"></a>Csatlakoztassa az érdeklődőkezelést

Amikor az ajánlatot a PartnerKözponttal teszi közzé a piactéren, csatlakoztatnia kell azt az ügyfélkapcsolat-kezelés (CRM) rendszeréhez. Ez lehetővé teszi, hogy megkapja az ügyfél elérhetőségi adatait, amint valaki érdeklődést mutat vagy használja a terméket.

1. Válasszon egy érdeklődési célt, ahová küldhetjük az érdeklődőket. A Partnerközpont a következő CRM rendszereket támogatja:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) az ügyfelek elköteleződése érdekében
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Ha a CRM-rendszer nem szerepel a fenti felsorolásban, használja [az Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) vagy https [endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) az ügyfél érdeklődői adatok tárolására. Ezután exportálja az adatokat a CRM-rendszerbe.

2. A Partnerközpontban történő közzétételkor csatlakoztassa az ajánlatot az érdeklődő célhelyéhez.
3. Ellenőrizze, hogy az érdeklődő célállomásához való csatlakozás megfelelően van-e konfigurálva. Miután közzétette a Partnerközpontban, érvényesítjük a kapcsolatot, és elküldjük Önnek a tesztérdeklődőt. Amíg megtekinti az ajánlat előnézeti verzióját, tesztelheti az érdeklődőkapcsolatot is, ha megpróbálja megvásárolni az ajánlatot az előzetes verzióban.
4. Győződjön meg arról, hogy az érdeklődő célállomásához való kapcsolat naprakész marad, hogy ne veszítse el az érdeklődőket.

Íme néhány további érdeklődőkezelési erőforrás:

- [Érdeklődőkezelés – áttekintés](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Gyakori kérdések az érdeklődők kezeléséről](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Az érdeklődők konfigurálásának gyakori hibái](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Érdeklődőkezelés – áttekintés](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (győződjön meg róla, hogy az előugró ablakok blokkolása ki van kapcsolva)

Válassza **a Vázlat mentése lehetőséget,** mielőtt a következő szakaszhoz, tulajdonságokhoz lépne.

## <a name="properties"></a>Tulajdonságok

Ezen az oldalon meghatározhatja az ajánlata csoportosításához használt kategóriákat és iparágakat a piactéren, az alkalmazás verzióján és az ajánlatot támogató jogi szerződéseken.

### <a name="category"></a>Kategória

Válasszon legalább egy és legfeljebb három kategóriát. Ezek a kategóriák arra szolgálnak, hogy az ajánlatot a megfelelő piactér keresési területekre helyezze, és megjelennek az ajánlat részleteit tartalmazó oldalon. Az ajánlat leírásában ismertesse, hogyan támogatja az ajánlat ezeket a kategóriákat.

### <a name="industry"></a>Iparág

Szükség esetén legfeljebb két iparágat és két vertikális ágazatot választhat ki az egyes ágazatok alatt. Bár a kategóriák az ajánlat megjelenítésére szolgálnak, az iparág és a vertikális csoportok a keresési szűrőkben használatosak, és a kirakatban kerülnek alkalmazásra. Ha az ajánlat egy adott iparágat és/vagy vertikális titulust céloz meg, az ajánlat leírásával magyarázza el, hogy az ajánlat hogyan támogatja a kiválasztott iparágakat vagy vertikális ágazatokat. Ha az ajánlat nem iparág-specifikus&#39;, hagyja üresen ezt a szakaszt.

> [!NOTE]
> Miközben azon dolgozunk, hogy új iparágakat és vertikális ágazatokat vezessünk be az ajánlatfelderítési élmény javítása érdekében, előfordulhat, hogy egyes iparágak vagy vertikális ágazatok még nem láthatók a Kirakatban. A (*) jelöléssel ellátott iparágak és vertikális ágazatok egy későbbi időpontban lesznek elérhetők. Minden közzétett ajánlat kulcsszókereséssel fedezhető fel.
<p>&nbsp;

| **Iparág** | **Alágazat** |
| --- | --- |
| *Autóipar | *Autóipar |
| Mezőgazdaság | *Egyéb - szegmentálatlan |
| Disztribúció | *Nagykereskedelem<br>Csomagszállítás és csomagszállítás |
| Education | *Felsőoktatás<br> * Alap- és középfokú oktatás / K-12<br>*Könyvtárak és múzeumok |
| Pénzügyi szolgáltatások | *Bank- és<br> tőkepiacok* Biztosítási |
| Government | *Védelem és hírszerzés (korábban nemzetbiztonsági és<br> közbiztonsági ügynek hívták)* Közbiztonság és jogérvényesülés<br>*Polgári kormány |
| Egészségügy (korábban Egészség) | *Egészségügyi fizető<br> * Egészségügyi szolgáltató<br>*Gyógyszerek |
| Gyártás és erőforrások (korábban Gyártásnak hívták) | *Kémiai és agrokémiai<br> * Diszkrét gyártás<br>*Energia |
| Kiskereskedelmi és fogyasztási cikkek (korábban kiskereskedelmi termék) | *Fogyasztási<br> cikkek* Kiskereskedők |
| *Média és kommunikáció (korábban Média és szórakozás) | *Média és<br> szórakozás* Távközlési |
| Professzionális szolgáltatások | *Jogi<br> * Partner szakmai szolgáltatások |
| *Építészet és építés (korábban építészetmérnöki megoldás) | *Egyéb - szegmentálatlan |
| *Vendéglátás és utazás | *Szállodák és<br> szabadidő* Utazás és szállítás<br>*Éttermek és élelmiszer-szolgáltatások |
| *Egyéb közszféra Industries | *Erdészet és<br> halászat* Nonprofit |
| *Ingatlan | *Egyéb - szegmentálatlan |

### <a name="legal"></a>Jogi tudnivalók

#### <a name="terms-and-conditions"></a>használati feltételei

Saját egyéni feltételek megadásához írja be a legfeljebb 10 000 karaktert a **Feltételek** mezőbe. Ha a szerződési feltételek hosszabb leírást igényelnek, adjon meg egy internetes hivatkozást arra, hogy hol találhatók. Aktív hivatkozásként jelenik meg az ügyfelek számára.

Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

Válassza **a Vázlat mentése lehetőséget,** mielőtt a következő szakaszra lépne, az Ajánlat listaelem.

## <a name="offer-listing"></a>Ajánlat lista

Itt adhatja meg a piactéren megjelenő ajánlatrészleteit. Ez magában foglalja az ajánlat nevét, leírását, képeit és így tovább.

### <a name="language"></a>Nyelv

Válassza ki azt a nyelvet, amelyen az ajánlat megjelenik. Jelenleg az **angol (Egyesült Államok)** az egyetlen elérhető lehetőség.

Marketplace-adatok (például az ajánlat neve, leírása és képek) definiálása az egyes nyelvek/piacok. Válassza ki az információ megadásához szükséges nyelvet/piacnevet.

> [!NOTE]
> Ajánlat részletei nem kell angol nyelven, ha az ajánlat &quot;leírása kezdődik a mondat, Ez az alkalmazás csak akkor érhető el [nem angol nyelven]. &quot; Az is&#39;, hogy hasznos linket biztosít, hogy olyan nyelven kínáljon tartalmat, amely&#39;eltér az ajánlati listán használttól.

### <a name="name"></a>Name (Név)

Az itt megadott név az ajánlat címeként jelenik meg. Ez a mező előre ki van töltve az **Ajánlat alias** mezőjében az ajánlat létrehozásakor megadott szöveggel. A nevet később módosíthatja.

A név:

- Védjeggyel védhető (és védjegy- vagy szerzői jogi szimbólumokat is tartalmazhat).
- Nem lehet hosszabb 50 karakternél.
- Nem tartalmazhat nak emojikat.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását. Ez legfeljebb 100 karakter hosszú lehet, és a piactér keresési eredményei között használatos.

### <a name="description"></a>Leírás

Adjon meg hosszabb leírást az ajánlatról, akár 3000 karakterig. Ez jelenik meg az ügyfelek számára a piactéri lista áttekintése.

A leírásban szerepeljen az alábbiak közül egy vagy több:

- Az ajánlat értékét és legfontosabb előnyeit.
- Kategória vagy iparági szövetségek, vagy mindkettő.
- Alkalmazáson belüli vásárlási lehetőségek.
- Minden szükséges közzététel.

Íme néhány tipp az írás a leírás:

- Egyértelműen írja le az ajánlat értékét a leírás első néhány mondatában. Adja meg a következő elemeket:
  - Az ajánlat leírása.
  - Az ajánlat előnyeit élvező felhasználó típusa.
  - Az ügyfél igényeinek megfelelően vagy az ajánlat címeinek kérdésén.
- Ne feledje, hogy az első néhány mondat megjelenhet a keresési eredmények között.
- Ne hagyatkozzon a termék értékesítéséhez szükséges funkciókra és funkciókra. Ehelyett összpontosítson az ajánlat által nyújtott értékre.
- Próbálja meg használni az iparág-specifikus szókincs vagy előny-alapú megfogalmazás.

Ahhoz, hogy az ajánlat **leírása** vonzóbbá, használja a rich text szerkesztő formázni a leírást. A Rich Text szerkesztő lehetővé teszi számok, felsorolásjelek, félkövér, dőlt betűk és behúzások hozzáadását, hogy a leírás olvashatóbb legyen.

:::image type="content" source="media/power-bi-rich-text-editor.png" alt-text="a Rich Text szerkesztőt szemlélteti" border="false":::

### <a name="search-keywords"></a>Kulcsszavak keresése

Adjon meg akár három választható keresési kulcsszót is, hogy az ügyfelek megtalálják az ajánlatot a piacon. A legjobb eredmény érdekében használja ezeket a kulcsszavakat a leírásban is.

### <a name="helpprivacy-web-addresses"></a>Súgó/adatvédelem webcímek

Adjon meg hivatkozásokat, amelyek segítségével az ügyfelek jobban megérthetik az ajánlatát.

#### <a name="help-link"></a>Súgó hivatkozás

Adja meg azt a webcímet, ahol az ügyfelek többet megtudhatnak az ajánlatról.

#### <a name="privacy-policy-url"></a>Adatvédelmi irányelvek URL-címe

Adja meg a szervezet adatvédelmi szabályzatának webcímét. Ön felelős annak biztosításáért, hogy ajánlata megfeleljen az adatvédelmi törvényeknek és előírásoknak. Ön felelős azért is, hogy érvényes adatvédelmi szabályzatot tesz közzé a webhelyén.

### <a name="contact-information"></a>Kapcsolattartási adatok

Meg kell adnia egy **támogatási partner** és egy **mérnöki partner**nevét, e-mail címét és telefonszámát. Ez az információ nem&#39;jelenik meg az ügyfeleknek. A Microsoft rendelkezésére áll, és a Felhőszolgáltató (CSP) partnerei számára is elérhető.

- Támogatási kapcsolattartó (szükséges): Általános támogatási kérdések esetén.
- Műszaki kapcsolat (szükséges): Műszaki kérdések és tanúsítási kérdések esetén.
- CsP-program kapcsolattartója (nem kötelező): A kriptaprogrammal kapcsolatos viszonteladói kérdések esetén.

A **Támogatási kapcsolattartó csoportban** adja meg a **támogatási webhely** webcímét, ahol a partnerek támogatást kaphatnak az ajánlathoz.

### <a name="supporting-documents"></a>Igazoló dokumentumok

Adjon meg legalább egy és legfeljebb három kapcsolódó marketingdokumentumot PDF formátumban. Például tanulmányok, brosúrák, ellenőrzőlisták vagy bemutatók.

### <a name="marketplace-images"></a>Marketplace-képek

Adjon meg emblémákat és képeket az ajánlatához. Minden képnek .png formátumúnak kell lennie. Az elmosódott képek elutasításra kerülnek.

>[!Note]
>Ha probléma van a fájlok feltöltésével, győződjön https://upload.xboxlive.com meg arról, hogy a helyi hálózat nem blokkolja a Partnerközpont által használt szolgáltatást.

#### <a name="store-logos"></a>Áruházi emblémák

Adjon meg .png fájlokat az ajánlathoz,&#39;s emblémát két méretben: **kicsi** (48 x 48 képpont) és **nagy** (216 x 216 képpont).

Mindkét embléma kötelező, és a piactér i.

#### <a name="screenshots"></a>Képernyőképek

Adjon hozzá legalább egy és legfeljebb öt képernyőképet, amelyek megmutatják, hogyan működik az ajánlata. Mindegyiknek 1280 x 720 képpont méretűnek és .png formátumúnak kell lennie.

#### <a name="videos-optional"></a>Videók (nem kötelező)

Akár öt videót is hozzáadhatsz, amelyek bemutatják az ajánlatodat. Adja meg a videó&#39;nevét, webcímét és miniatűr .png képét 1280 x 720 képpont méretben.

#### <a name="additional-marketplace-listing-resources"></a>További piactér-listaforrások

Ha többet szeretne tudni az ajánlatlisták létrehozásáról, olvassa el [az Ajánlott eljárások felsorolása című témakört.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="technical-configuration"></a>Műszaki konfiguráció

Az alkalmazást éles környezetben népszerűsítse, és adja meg a Power BI alkalmazástelepítő hivatkozását, amely lehetővé teszi az ügyfelek számára az alkalmazás telepítését. További információt az [Alkalmazások közzététele irányítópultokkal és jelentésekkel a Power BI-ban című témakörben talál.](https://docs.microsoft.com/power-bi/service-create-distribute-apps)

## <a name="supplemental-content"></a>Kiegészítő tartalom

Adjon meg további információkat az ajánlatáról, hogy segítsen nekünk érvényesíteni. Ez az információ nem&#39;jelenik meg az ügyfelek, vagy közzé a piactéren.

### <a name="validation-assets"></a>Érvényesítési eszközök

Szükség esetén utasításokat adhat (legfeljebb 3000 karaktert) a Microsoft ellenőrző csapatának az alkalmazás konfigurálásához, csatlakoztatásához és teszteléséhez. Adja meg a tipikus konfigurációs beállításokat, fiókokat, paramétereket vagy egyéb információkat, amelyek az Adatok csatlakoztatása beállítás teszteléséhez használhatók. Ez az információ csak az ellenőrző csoport számára látható, és csak ellenőrzési célokra használható.

## <a name="review-and-publish"></a>Véleményezés és közzététel

Miután&#39;teljesítette az ajánlat összes szükséges szakaszát, elküldheti az ajánlatot az ellenőrzésre és a közzétételre.

A portál jobb felső sarkában válassza a **Véleményezés és közzététel**lehetőséget.

Az ellenőrzés oldalon a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak készültségi állapotát. Nem tehet közzé&#39;, amíg az ajánlat összes szakasza készként van megjelölve.
  - **Nincs elindítva** – A szakasz még nem indult el, és ki kell tölteni.
  - **Hiányos** – A szakasz hibákat, amelyeket ki kell javítani, vagy megköveteli, hogy további információkat. A jelen dokumentum korábbi szakaszaiban útmutatást talál.
  - **Kész** – A szakasz tartalmazza az összes szükséges adatot, és nincsenek hibák. Az ajánlat minden szakaszának teljesnek kell lennie az ajánlat elküldése előtt.
- Adja meg a tesztelési utasításokat a minősítő csoportnak, hogy megbizonyosodjon az alkalmazás megfelelő teszteléséről. Is, hogy minden olyan kiegészítő megjegyzéseket, amelyek hasznosak a megértés az ajánlatot.

Az ajánlat közzétételre való elküldéséhez válassza a **Közzététel**lehetőséget.

Küldünk Önnek egy e-mailt, hogy tudd, ha az ajánlat előzetes verziója elérhető az ellenőrzéshez és a jóváhagyáshoz. Ha közzé szeretné tenni az ajánlatot a nyilvánosság számára (vagy ha egy privát ajánlatot, egy privát közönségnek), nyissa meg a Partnerközpontot, és válassza a **Go-live**lehetőséget.
