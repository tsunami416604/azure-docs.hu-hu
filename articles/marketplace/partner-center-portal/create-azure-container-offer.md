---
title: Azure-tárolóajánlat létrehozása a Partnerközpontban – Azure Piactér
description: Ez a cikk ismerteti, hogyan hozhat létre és tehet közzé egy tároló ajánlat az Azure Marketplace-en.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b96fe12ccc0292bb5f689fbecabd53d2af54846e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266317"
---
# <a name="create-an-azure-container-offer"></a>Azure Container-ajánlat létrehozása

> [!IMPORTANT]
> Az Azure Container-ajánlatok kezelését áthelyezzük a Cloud Partner Portalról a Partnerközpontba. Az ajánlatok áttelepítéséig kövesse a Cloud Partner Portal [tárolók](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) utasításait az ajánlatok kezeléséhez.

Ez a cikk ismerteti, hogyan hozhat létre és tehet közzé egy tároló ajánlat az Azure Marketplace-en. Mielőtt elkezdené, [hozzon létre egy kereskedelmi piactér-fiókot a Partnerközpontban.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [Partnerközpontba](https://partner.microsoft.com/dashboard/home), majd a felső menüben válassza az **Irányítópult**lehetőséget.
2. A bal oldali menüben válassza a **Kereskedelmi piactér**lehetőséget, majd **az Áttekintés lehetőséget.**
3. Az **Áttekintés** lapon válassza a **+ Új ajánlat**, majd az Azure Container **lehetőséget.** Megjelenik **az Új ajánlat** párbeszédpanel.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-new-container.png" alt-text="A Partnerközpont Áttekintés lapját szemlélteti. Az Új ajánlat gomb és a Consulting szolgáltatás ajánlat ki van emelve.":::

> [!TIP]
> Az ajánlat közzététele után a Partnerközpontban végzett szerkesztések csak az ajánlat újbóli közzététele után jelennek meg a kirakatokban. A módosítások végrehajtása után mindig tegye közzé újra a közzétételt.

### <a name="offer-id-and-alias"></a>Ajánlatazonosító és alias

Adja meg **az ajánlatazonosítót.** Ez a fiókban lévő minden egyes ajánlat egyedi azonosítója.

- Ezt az azonosítót az ügyfelek a piactéri ajánlat és az Azure Resource Manager-sablonok webcímében láthatják, ha vannak ilyenek.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásjeleket, de szóközöket nem, és legfeljebb 50 karakter ből állhat. Ha például beírja az **1-es tesztajánlatot,** az ajánlat webcíme a lesz. `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`
- Az ajánlatazonosító nem módosítható, miután a Létrehozás lehetőséget **választja.**

**Adja meg** **az ajánlat aliasát**. Ez a név a Partnerközpontban található ajánlatra vonatkozik.

- Ez a név nem használatos a piactéren, és eltér az ajánlat neve és egyéb értékek jelennek meg az ügyfelek számára.
- Ez nem módosítható a **Létrehozás (Létrehozás) lehetőséget**követően.

A folytatás előtt válassza a **Létrehozás** gombot.

## <a name="offer-overview"></a>Ajánlat áttekintése

Az **Ajánlat áttekintése** lap az ajánlat közzétételéhez szükséges lépések (befejezett és közelgő) vizuális megjelenítését, valamint az egyes lépések befejezéséhez szükséges lépéseket jeleníti meg.

Ezen az oldalon az ajánlat aktuális állapota alapján különböző hivatkozások jelennek meg. Például:

- Ha az ajánlat piszkozat - [Ajánlattervezet törlése](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Ha az ajánlat élő - [Hagyja abba az ajánlat értékesítését](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Ha az ajánlat előzetes verzióban érhető el - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Ha még nem fejezte be a kiadói kijelentkezést – [Közzététel megszakítása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Ajánlat beállítása

Az ajánlat beállításához kövesse az alábbi lépéseket.

### <a name="connect-lead-management--optional"></a>Csatlakoztassa az érdeklődőkezelést – opcionális

Amikor a Partner Center rel teszi közzé az ajánlatot a piactéren, csatlakoztathatja azt az ügyfélkapcsolat-kezelés (CRM) rendszeréhez. Ez lehetővé teszi, hogy megkapja az ügyfél elérhetőségi adatait, amint valaki érdeklődést mutat vagy használja a terméket.

1. **Válassza ki azt a vezető úti célt, ahová azt szeretné, hogy ügyfélérdeklődőket küldjünk.** A Partnerközpont a következő CRM rendszereket támogatja:

- [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) az ügyfelek elköteleződése érdekében
- [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
- [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

> [!NOTE]
> Ha a CRM-rendszer nem szerepel a fenti felsorolásban, használja az [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) vagy [https endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) az ügyfél érdeklődői adatok tárolására, majd exportálja az adatokat a CRM-rendszerbe.

2. A Partnerközpontban történő közzétételkor csatlakoztassa az ajánlatot az érdeklődő célhelyéhez.
3. Ellenőrizze, hogy az érdeklődő célállomásához való csatlakozás megfelelően van-e konfigurálva. Miután közzétette a Partnerközpontban, érvényesítjük a kapcsolatot, és elküldjük Önnek a tesztérdeklődőt. Amíg megtekinti az ajánlat előnézeti verzióját, tesztelheti az érdeklődőkapcsolatot is, ha megpróbálja megvásárolni az ajánlatot az előzetes verzióban.
4. Győződjön meg arról, hogy az érdeklődő célállomásához való kapcsolat naprakész marad, hogy ne veszítse el az érdeklődőket.

Íme néhány további érdeklődőkezelési erőforrás:

- [Érdeklődőkezelés – áttekintés](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Gyakori kérdések az érdeklődők kezeléséről](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Az érdeklődők konfigurálásának gyakori hibái](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Érdeklődőkezelés – áttekintés](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Győződjön meg arról, hogy az előugró ablakok blokkolása ki van kapcsolva)

Válassza **a Vázlat mentése lehetőséget,** mielőtt a következő szakaszhoz, tulajdonságokhoz lépne.

### <a name="properties"></a>Tulajdonságok

Ezen az oldalon meghatározhatja az ajánlat a piacon, és a jogi szerződések, amelyek támogatják az ajánlatot.

#### <a name="category"></a>Kategória

Válasszon legalább egy és legfeljebb öt kategóriát. Ezek a kategóriák az ajánlat megfelelő piactérkeresési területeken való elszállásozására szolgálnak, és megjelennek az ajánlat részleteit tartalmazó oldalon. Az ajánlat leírásában ismertesse, hogyan támogatja az ajánlat ezeket a kategóriákat. A **tárolók a Tárolók,** majd a **Tárolóképek** kategória alatt jelennek meg.

#### <a name="legal"></a>Jogi tudnivalók

Meg kell adnia az ajánlat feltételeit. Két lehetőség érhető el:

- Használja a Microsoft kereskedelmi piactér általános szerződését.
- Adja meg saját feltételeit.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>A Microsoft kereskedelmi piacterére vonatkozó általános szerződés

Standard szerződés sablont kínálunk, amely megkönnyíti a tranzakciókat a kereskedelmi piacon. Választhat, hogy a megoldást az Általános Szerződés alapján ajánlja fel, amelyet az ügyfeleknek csak egyszer kell ellenőrizniük és elfogadniuk. Ez egy jó lehetőség, ha nem szeretne egyéni feltételeket létrehozni.

Ha többet szeretne megtudni a standard szerződésről, olvassa [el a Microsoft kereskedelmi piactér általános szerződéscímű témakörét.](https://docs.microsoft.com/azure/marketplace/standard-contract) Letöltheti a [Szabványos szerződés](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-fájlt is (győződjön meg róla, hogy az előugró ablakok blokkolása ki van kapcsolva).

Az Általános szerződés használatához jelölje be **a Microsoft kereskedelmi piacterének általános szerződésének használata** jelölőnégyzetet, majd kattintson az Elfogadás **gombra.**

> [!NOTE]
> Miután közzétett egy ajánlatot a Microsoft kereskedelmi piactér általános szerződésével, nem használhatja a saját egyéni szerződéseit. Vagy felajánlja a megoldást a Standard Szerződés alapján, vagy a saját feltételei alapján.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="A Microsoft kereskedelmi piacterének általános szerződésének használata jelölőnégyzetet szemlélteti.":::

##### <a name="your-own-terms-and-conditions"></a>Az Ön saját feltételei

Saját egyéni feltételek megadásához adja meg azokat az **Általános szerződési feltételek** mezőbe. Ebben a mezőben korlátlan mennyiségű karaktert írhat be. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

Válassza **a Vázlat mentése lehetőséget,** mielőtt a következő szakaszra lépne, az Ajánlat listaelem.

## <a name="offer-listing"></a>Ajánlat lista

Ezen az oldalon meghatározhatja a kereskedelmi piacon megjelenő ajánlatrészleteit. Ez magában foglalja az ajánlat nevét, leírását és a képeket.

> [!NOTE]
> Az ajánlat részleteinek nem kell angol nyelven lenniük, ha az ajánlat leírása a következő mondattal kezdődik: "Ez az alkalmazás csak [nem angol nyelven] érhető el." Az is rendben van, hogy egy hasznos linket kínál tartalmat olyan nyelven, amely eltér az ajánlat lista részleteit.

### <a name="name"></a>Név

Az itt megadott név az ajánlat címeként jelenik meg. Ez a mező előre ki van töltve az **Ajánlat alias** mezőjében az ajánlat létrehozásakor megadott szöveggel. A nevet később módosíthatja.

A név:

- Védjeggyel ellátott a védjegy (és ön védjegy- és szerzői jogi szimbólumokat is tartalmazhat).
- Nem lehet hosszabb 50 karakternél.
- Nem tartalmazhat nak emojikat.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Az ajánlat rövid leírása. Ez legfeljebb 100 karakter hosszú lehet, és a piactér keresési eredményei között használatos.

### <a name="long-summary"></a>Hosszú összegzés

Az ajánlat részletesebb leírása. Ez legfeljebb 256 karakter hosszú lehet, és a piactér keresési eredményei között használatos.

### <a name="description"></a>Leírás

Adjon meg hosszabb leírást az ajánlatról, akár 3000 karakterig. Ez jelenik meg az ügyfelek számára a piactéri lista áttekintése.

A leírásban szerepeljen az alábbiak közül egy vagy több:

- Az ajánlat értékét és legfontosabb előnyeit
- Kategória vagy iparági szövetségek, vagy mindkettő
- Alkalmazáson belüli vásárlási lehetőségek
- Minden szükséges közzététel

Íme néhány tipp az írás a leírás:

- Egyértelműen írja le az ajánlat értékét a leírás első néhány mondatában. Adja meg a következő elemeket:
  - Az ajánlat leírása.
  - Az ajánlat előnyeit élvező felhasználó típusa
  - Az ügyfél igényeinek megfelelően vagy az ajánlat címeinek kérdésén.
- Ne feledje, hogy az első néhány mondat megjelenhet a keresési eredmények között.
- Ne hagyatkozzon a termék értékesítéséhez szükséges funkciókra és funkciókra. Ehelyett összpontosítson az ajánlat által nyújtott értékre.
- Próbálja meg használni az iparág-specifikus szókincs vagy előny-alapú megfogalmazás.

Annak érdekében, hogy az ajánlat **leírása** vonzóbbá, használja a Rich Text szerkesztő, formázni a leírást. számozással, felsorolásokkal, félkövér, dőlt betűkkel és behúzásokkal, hogy a leírás olvashatóbb legyen.

:::image type="content" source="media/text-editor2.png" alt-text="A Rich Text szerkesztőt mutatja be." border="false" :::

- Ezzel a legördülő legördülő menüvel bekezdésstílust alkalmazhat a szövegre.

    :::image type="content" source="media/text-editor3.png" alt-text="A Rich Text szerkesztő szövegstílus-vezérlőjének szemléltetése." border="false":::

- Ezekkel az ikonokkal számozást vagy felsorolásjeleket alkalmazhat a szövegre.

     :::image type="content" source="media/text-editor4.png" alt-text="A rich text szerkesztőben a listajeles és számlista-vezérlőket mutatja be." border="false":::

- Ezekkel az ikonokkal behúzást adhat hozzá vagy távolíthat el a szövegből.

    :::image type="content" source="media/text-editor5.png" alt-text="A Rich Text szerkesztő behúzási vezérlőit mutatja be." border="false":::

#### <a name="privacy-policy-link"></a>Adatvédelmi irányelv hivatkozás

Adja meg a szervezet adatvédelmi szabályzatának webcímét. Ön felelős annak biztosításáért, hogy ajánlata megfeleljen az adatvédelmi törvényeknek és előírásoknak. Ön felelős azért is, hogy érvényes adatvédelmi szabályzatot tesz közzé a webhelyén.

#### <a name="useful-links"></a>Hasznos hivatkozások

Nyújtson kiegészítő online dokumentumokat az ajánlatáról. Legfeljebb 25 hivatkozást adhat hozzá. Hivatkozás hozzáadásához válassza **a + Hivatkozás hozzáadása lehetőséget,** majd töltse ki a következő mezőket:

- **Cím** – Az ügyfelek ezt az ajánlat részletes oldalán fogják látni.
- **Link (URL)** – Adjon meg egy hivatkozást az ügyfelek számára az online dokumentum megtekintéséhez. A kapcsolatnak http:// vagy https:// kell kezdődnie.

### <a name="contact-information"></a>Kapcsolattartási adatok

Meg kell adnia egy **támogatási partner** és egy **mérnöki partner**nevét, e-mail címét és telefonszámát. Ez az információ nem jelenik meg az ügyfelek számára, de elérhető a Microsoft számára. A felhőszolgáltató (CSP) partnerei is elvégezhetők.

- Támogatási kapcsolattartó (szükséges): Általános támogatási kérdések esetén.
- Műszaki kapcsolat (szükséges): Műszaki kérdések és tanúsítási kérdések esetén.
- CsP-program kapcsolattartója (nem kötelező): A kriptaprogrammal kapcsolatos viszonteladói kérdések esetén.

A **Támogatási kapcsolattartó** szakaszban adja meg a **támogatási webhelyet,** ahol a partnerek támogatást kaphatnak az ajánlathoz annak alapján, hogy az ajánlat elérhető-e a globális Azure-ban, az Azure Government-ben vagy mindkettőben.

A **CSP-program kapcsolati szakaszában** adja meg azt a linket ( **CSP Program Marketing Materials** ), ahol a CSP-partnerek marketinganyagokat találhatnak az ajánlatához.

#### <a name="additional-marketplace-listing-resources"></a>További piactér-listaforrások

Ha többet szeretne tudni az ajánlathirdetések létrehozásáról, olvassa el [az Ajánlott eljárások felsorolása című témakört.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>Marketplace-képek

Adjon meg emblémákat és képeket az ajánlatához. Minden képnek PNG formátumúnak kell lennie. Az elmosódott képek elutasításra kerülnek.

#### <a name="store-logos"></a>Áruházi emblémák

 Png-fájlok at az ajánlat logója a következő négy képpontméretben:

- **Kicsi** (48 x 48)
- **Közepes** (90 x 90)
- **Nagy** (216 x 216)
- **Széles** (255 x 115)

Mind a négy embléma kötelező, és a piactér i.

#### <a name="screenshots-optional"></a>Képernyőképek (nem kötelező)

Adjon hozzá legfeljebb öt képernyőképet, amelyek megmutatják, hogyan működik az ajánlata. Mindegyiknek 1280 x 720 képpont méretűnek és PNG formátumúnak kell lennie.

#### <a name="videos-optional"></a>Videók (nem kötelező)

Akár öt videót is hozzáadhatsz, amelyek bemutatják az ajánlatodat. Adja meg a videó nevét, webcímét és a videó miniatűr PNG-képét 1280 x 720 képpont méretűen.

#### <a name="offer-examples"></a>Ajánlatpéldák

A következő példák bemutatják, hogyan jelennek meg az ajánlatlista-mezők az ajánlat különböző helyein.

Ez az **Ajánlat listalapját** jeleníti meg az Azure Marketplace-en:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Az Ajánlat listalap az Azure Marketplace-en." :::

Ez a keresési eredményeket jeleníti meg az Azure Marketplace-en:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="A keresési eredményeket az Azure Marketplace-en mutatja be.":::

Ez az **Ajánlat listalapját** jeleníti meg az Azure Portalon:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Az Ajánlat listalap az Azure Portalon.":::

Ez a keresési eredményeket jeleníti meg az Azure Portalon:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="A keresési eredményeket az Azure Portalon mutatja be.":::

## <a name="preview"></a>Előzetes verzió

Az Előnézet lapon korlátozott **előnézeti közönséget** választhat az ajánlat érvényesítéséhez, mielőtt élőben közzétenné.

> [!IMPORTANT]
> Miután megtekintette az ajánlatot az **Előzetes verzióban,** az **Élő ben** lehetőséget kell választania az ajánlat nyilvános közzétételéhez.

Adja meg az előzetes verzióközönséget az Azure-előfizetésazonosítók GUID-jai, valamint az egyes verziók hozadékának választható leírásával. Egyik mezőt sem látják az ügyfelek.

> [!NOTE]
> Az Azure-előfizetés-azonosítóját az Azure Portal Előfizetések lapján találja.

Adjon hozzá legalább egy Azure-előfizetés-azonosítót, akár egyenként (legfeljebb 10) vagy csv-fájl feltöltésével (legfeljebb 100). Az előfizetési azonosítók hozzáadásával meghatározhatja, hogy ki tekintheti meg az ajánlat előnézetét az élő közzététel előtt. Ha az ajánlat már él, kiválaszthatja az előnézeti közönséget az ajánlat módosítási vagy frissítéseinek teszteléséhez.

> [!NOTE]
> Az előnézeti közönség eltér a privát közönségtől. Az **előnézeti** közönség láthatja és megerősítheti az összes ajánlati tervet, mielőtt azok a piactéren megjelennének, beleértve azokat is, amelyeket csak egy **privát** közönség nek tesznek közzé (az Elérhetőség lapon).

A folytatás előtt válassza **a Vázlat mentése** lehetőséget.

### <a name="plan-overview"></a>Terv – áttekintés

Ezen a lapon különböző csomagbeállításokat adhat meg ugyanazon az ajánlaton belül. Ezeket a terveket korábban SK-nak vagy raktározási egységeknek nevezték. A tervek eltérőek lehetnek a rendelkezésre álló felhők, például a globális felhők, a kormányzati felhők és a terv által hivatkozott kép tekintetében. Az ajánlat kereskedelmi piacon való listázásához legalább egy csomagot be kell állítania.

A tervek létrehozása után a **Terv áttekintése** lapon a következők láthatók:

- Terv nevek
- Díjszabási modell
- Felhő elérhetősége (globális vagy kormányzati)
- Jelenlegi közzétételi állapot
- Az elérhető műveletek

A Terv áttekintésében elérhető műveletek a terv aktuális állapotától függően változnak. Ezek például az alábbi jelentések lehetnek:

- **Vázlat törlése** – Ha a terv állapota Vázlat.
- **Értékesítési terv leállítása** – Ha a terv állapota élőben van közzétéve.

#### <a name="create-new-plan"></a>Új terv létrehozása

Válassza **az Új terv létrehozása**lehetőséget. Megjelenik **az Új terv** párbeszédpanel.

A **Tervazonosító** mezőben hozzon létre egy egyedi tervazonosítót az ajánlatban szereplő minden tervhez. Ez az azonosító látható lesz az ügyfelek számára a termék webcímében. Csak kisbetűket és számokat, kötőjeleket vagy aláhúzásjeleket és legfeljebb 50 karaktert használjon.

> [!NOTE]
> A csomagazonosító nem módosítható a **Létrehozás (Létrehozás) választódat**választ.

A **Terv neve** mezőbe írja be a terv nevét. Az ügyfelek ezt a nevet látják, amikor eldöntik, hogy melyik csomagot válasszák ki az ajánlaton belül. Hozzon létre egy egyedi nevet az ajánlatban szereplő minden tervhez. Használhat például egy **Windows Server** ajánlatnevet **a Windows Server 2016** és a Windows **Server 2019**csomagokkal.

### <a name="plan-setup"></a>A terv beállítása

Ezen a lapon kiválaszthatja, hogy a csomag mely felhőkben érhető el. Az ezen a lapon található válaszok hatással vannak arra, hogy mely mezők jelenjenek meg a többi lapon.

#### <a name="cloud-availability"></a>Felhő elérhetősége

A csomagnak legalább egy felhőben elérhetőnek kell lennie.

Válassza ki az **Azure Global** lehetőséget, hogy a csomag használható az ügyfelek az összes globális Azure-régiókban, amelyek a kereskedelmi piactéren. További információt a [Földrajzi elérhetőség és pénznemtámogatás](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Válassza ki az [**Azure Government Cloud**](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) opciót, hogy a megoldás itt jelenjen meg. Ez egy kormányzati közösségi felhő, amely ellenőrzött hozzáféréssel rendelkezik az Amerikai Egyesült Államok szövetségi, állami és helyi vagy törzsi kormányzati szerveinek, valamint a kiszolgálásra jogosult partnereknek. Közzétevőként Ön a felelős a felhőalapú közösség megfelelőségi szabályozásáért, biztonsági intézkedéseiért és gyakorlati tanácsaiért. Az Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az Egyesült Államokban található).

Az Azure Government [nek való közzététel](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) előtt tesztelje és erősítse meg a megoldást ezen a területen belül, mivel az eredmények eltérőek lehetnek. A megoldás létrehozásához és teszteléséhez kérjen próbafiókot a [Microsoft Azure Government próbaverziójától.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Miután a csomag közzé, és elérhető egy adott felhőben, nem tudja eltávolítani a felhőt.

#### <a name="azure-government-cloud-certifications"></a>Az Azure Government Felhőminősítései

Ez a lehetőség csak akkor látható, ha az **Azure Government Cloud** a Felhő **elérhetősége**alatt van kiválasztva.

Az Azure Government-szolgáltatások olyan adatokat kezelnek, amelyekre bizonyos kormányzati előírások és követelmények vonatkoznak. Például FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 és CJIS.

A programok tanúsítványainak megjelenítéséhez legfeljebb 100 hivatkozást adhat meg, amelyek leírják azokat. Ezek lehetnek linkeket a listát a program közvetlenül, vagy a saját honlapján. Ezek a hivatkozások csak az Azure Government-ügyfelek számára láthatók.

## <a name="plan-listing"></a>A terv listája

Ez a lap az aktuális ajánlaton belül az egyes tervekre vonatkozó konkrét információkat jeleníti meg.

### <a name="plan-name"></a>Terv neve

Ez előre ki van töltve azzal a névvel, amelyet a terv létrehozásakor adott. Ezt a nevet szükség szerint módosíthatja. Legfeljebb 50 karakter hosszú lehet. Ez a név jelenik meg a csomag címe ként az Azure Marketplace-en és az Azure Portalon. Ez az alapértelmezett modulnév, miután a terv készen áll a használatra.

### <a name="plan-summary"></a>Terv összegzése

A szoftvercsomag rövid összefoglalása (nem az ajánlat). Ez az összefoglaló megjelenik az Azure Marketplace keresési eredményei között, és legfeljebb 100 karaktert tartalmazhat.

### <a name="plan-description"></a>Terv leírása

Írja le, mi teszi egyedivé ezt a szoftvertervet, valamint az ajánlaton belüli tervek közötti különbségeket. Ne írja le az ajánlatot, csak a tervet. Ez a leírás megjelenik az Azure Marketplace-en és az Azure Portalon az **ajánlat listalapján.** Ez lehet ugyanaz a tartalom, amelyet a terv összegzésében megadott, és legfeljebb 2000 karaktert tartalmazhat.

A mezők **kitöltése** után válassza a Mentés lehetőséget.

#### <a name="plan-examples"></a>Példák tervezése

Az alábbi példák bemutatják, hogyan jelennek meg a tervlistamezők különböző nézetekben.

Ezek azok a mezők az Azure Marketplace-en, amikor a csomag részleteit tekinti kúra részletei:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Az Azure Marketplace-en a csomag részleteinek megtekintésekor látható mezőket mutatja be.":::

Ezek a csomag részletei az Azure Portalon:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="A csomag részleteit az Azure Portalon mutatja be.":::

## <a name="plan-availability"></a>A csomag elérhetősége

Ha el szeretné rejteni a közzétett ajánlatot, hogy az ügyfelek ne kereshessenek, tallózhassanak vagy vásárolhassanak a piactéren, jelölje be a **Terv elrejtése** jelölőnégyzetet az **Elérhetőség** lapon.

Ez a mező akkor használatos, ha:

- Az ajánlat célja, hogy közvetve használható, ha hivatkoznak egy másik alkalmazás.
- Az ajánlatot nem szabad egyenként megvásárolni.
- A tervet a kezdeti teszteléshez használták, és már nem releváns.
- A tervet ideiglenes vagy szezonális ajánlatokra használták, és a továbbiakban nem ajánlották fel.

## <a name="technical-configuration"></a>Műszaki konfiguráció

A tárolórendszerképeket egy privát Azure Container Registry rendszerházban kell [üzemeltetni.](https://azure.microsoft.com/services/container-registry/) A **Műszaki konfiguráció** lapon adja meg a tárolórendszerkép-tárház referenciaadatait az Azure Container Registry-en belül.

Az ajánlat közzététele után a tárolórendszerkép egy adott nyilvános tároló beállításjegyzékben másolja az Azure Piactérre. A tárolórendszerkép használatára vonatkozó összes kérelem az Azure Marketplace nyilvános tárolóbeállításjegyzékéből kerül kiszolgálásra, nem pedig a privát rendszerből. További információt [az Azure Container technikai eszközeinek előkészítése című témakörben talál.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)

### <a name="image-repository-details"></a>Képtár részletei

Adja meg a következő információkat a **Képtár részletei** lapon.

**Azure-előfizetés-azonosító** – Adja meg az előfizetés-azonosítót, ahol a használat jelentett, és a szolgáltatások számlázása az Azure Container Registry, amely tartalmazza a tárolórendszerképet. Ezt az azonosítót az Azure Portal [Előfizetések lapján](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) találja.

**Azure-erőforráscsoport neve** – adja meg az [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) nevét, amely tartalmazza az Azure Container Registry a tárolórendszerképpel. Az erőforráscsoportnak elérhetőnek kell lennie az előfizetés-azonosítóban (fent). A nevet az [Azure Portalerőforrás-csoportok](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) lapján találja.

**Azure Container Registry name** – Adja meg az [Azure Container Registry,](https://docs.microsoft.com/azure/container-registry/container-registry-intro) amely a tárolórendszerképet rendelkezik. A tároló beállításjegyzékének a korábban megadott Azure-erőforráscsoportban kell lennie. Csak a rendszerleíró adatbázis nevét adja meg, a teljes bejelentkezési kiszolgáló nevét ne. Ne felejtse el kihagyni **azurecr.io** a névből. A beállításjegyzék nevét az Azure Portalon található [tárolóregisztriók lapon](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) találja.

**Rendszergazdai felhasználónév az Azure Container Registry** – adja meg a [rendszergazdai felhasználónév](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) kapcsolódik az Azure Container Registry, amely a tároló rendszerképet. A felhasználónév és a jelszó szükséges ahhoz, hogy a vállalat hozzáférhessen a beállításjegyzékhez. A rendszergazdai felhasználónév és jelszó lekéréséhez állítsa a **rendszergazdai funkcióval rendelkező** tulajdonságot **True** értékre az Azure parancssori felület (CLI) használatával. Igény szerint **beállíthatja,** hogy a rendszergazdai felhasználó **engedélyezze** az Azure Portalon.

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="A Tároló beállításjegyzékének frissítése párbeszédpanelt mutatja be.":::

**Jelszó az Azure Container Registry** – adja meg a jelszót a rendszergazdai felhasználónév, amely az Azure Container Registry, és a tároló rendszerkép. A felhasználónév és a jelszó szükséges ahhoz, hogy a vállalat hozzáférhessen a beállításjegyzékhez. A jelszót az Azure Portalon a **Container Registry** > **Access Keys** vagy az Azure CLI segítségével szerezheti be a [show paranccsal.](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Az Access Billentyű menüt mutatja.":::

**Tárház neve az Azure Container Registry**. Adja meg az Azure Container Registry repository, amely a rendszerképet. Adja meg a tárház nevét, amikor lenyomja a lemezképet a rendszerleíró adatbázisba. A tárház nevét a > **Tárolójegyzék-tárolók** lapon találhatja meg. [Container Registry](https://azure.microsoft.com/services/container-registry/) További információ: [View container registry repositories in Azure Portal](https://docs.microsoft.com/azure/container-registry/container-registry-repositories).

> [!NOTE]
> A név beállítása után nem módosítható. A fiók minden ajánlatához használjon egyedi nevet.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Képcímkék az ajánlat új verzióihoz

Az ügyfeleknek képesnek kell lenniük arra, hogy automatikusan frissítéseket kapjanak az Azure Piactérről, amikor frissítést tesz közzé. Ha nem szeretnének frissíteni, képesnek kell lenniük arra, hogy a kép egy adott verzióján maradjanak. Ezt úgy teheti meg, hogy minden alkalommal új képcímkéket ad hozzá, amikor frissíti a képet.

### <a name="image-tag"></a>Képcímke

Ennek a mezőnek tartalmaznia kell egy **legújabb** címkét, amely a kép legújabb verziójára mutat az összes támogatott platformon. Tartalmaznia kell egy verziócímkét is (például az xx.xx.xx-től kezdődően, ahol az xx egy szám). Az ügyfeleknek több platformot kell használniuk a [jegyzékfájl-címkék](https://github.com/estesp/manifest-tool) használatával. A jegyzékcímkét hivatkozó összes címkét hozzá kell adni, hogy feltölthessük őket.

Az összes jegyzékcímke (a legújabb címke kivételével) x.y **-** vagy X.Y.Z- értékkel kell kezdődnie, ahol X, Y és Z egész szám. Ha például egy **legújabb** címke az 1.0.1-linux-x64, az 1.0.1 linux-arm32 és az 1.0.1-windows-arm32 elemre mutat, akkor ezt a hat címkét hozzá kell adni ehhez a mezőhöz. További információt [az Azure Container technikai eszközeinek előkészítése című témakörben talál.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)

> [!NOTE]
> Ne felejtsen el tesztcímkét hozzáadni a képhez, hogy a tesztelés során azonosíthassa a képet.

## <a name="review-and-publish"></a>Véleményezés és közzététel

Miután elvégezte az ajánlat összes szükséges szakaszát, elküldheti azt véleményezésre és közzétételre.

A portál jobb felső sarkában válassza a **Véleményezés és** **közzététel**lehetőséget.

Az ellenőrzés oldalon a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak készültségi állapotát. Nem tehetközzé, amíg az ajánlat összes szakasza készként van megjelölve.
  - **Nincs elindítva** – Még nem indult el, és be kell fejezni.
  - **Hiányos** – Olyan hibákat tartalmaz, amelyeket ki kell javítani, vagy további információkat kell megadnia. A dokumentum korábbi szakaszaiban segítséget talál.
  - **Kész** – Tartalmazza az összes szükséges adatot hiba nélkül. Az ajánlat minden szakaszának teljesnek kell lennie az ajánlat elküldése előtt.
- Adja meg a tesztelési utasításokat a minősítő csapatnak, hogy megbizonyosodjon az ajánlat helyes teszteléséről. Is, hogy minden olyan kiegészítő megjegyzéseket, amelyek hasznosak a megértés az ajánlatot.

Az ajánlat közzétételre való elküldéséhez válassza a **Közzététel**lehetőséget.

Küldünk Önnek egy e-mailt, hogy tudd, ha az ajánlat előzetes verziója elérhető az ellenőrzéshez és a jóváhagyáshoz.

Ha közzé szeretné tenni az ajánlatot a nyilvánosság számára (vagy ha egy privát ajánlatot, egy privát közönségnek), nyissa meg a Partnerközpontot, és válassza a **Go-live**lehetőséget.

## <a name="next-step"></a>Következő lépés

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
