---
title: Hozzon létre egy Azure IoT Edge modulajánlatot partnerközponttal - Azure Marketplace
description: Megtudhatja, hogyan hozhat létre IoT Edge-modulajánlatot az Azure Piactéren a PartnerCenter használatával
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: cca54e4e456fe766b190f64657cd1aca1d9520e0
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869147"
---
# <a name="create-an-iot-edge-module-offer"></a>IoT Edge-modulajánlat létrehozása

> [!IMPORTANT]
> Az IoT Edge modulajánlatok kezelését áthelyezzük a Cloud Partner Portalról a Partner centerbe. Amíg az ajánlatok áttelepítése, kövesse az utasításokat az [IoT Edge modul ajánlat közzétételi áttekintést](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) cloud partner portál az ajánlatok kezeléséhez.

Ez a cikk ismerteti, hogyan hozhat létre és tehet közzé egy IoT-hálózati modul ajánlat az Azure Marketplace-en.

IoT Edge-modulajánlat létrehozása előtt rendelkeznie kell egy kereskedelmi piactéri fiókkal a Partnerközpontban. Ha még nem hozott létre ilyet, olvassa el [a Kereskedelmi piactér-fiók létrehozása a Partnerközpontban című témakört.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a Partnerközpontba.
2. A bal oldali navigációs menüben válassza a **Kereskedelmi piactér** > **áttekintése parancsot.**

    ![A bal oldali navigációs menüt mutatja be.](./media/cs-menu-overview.png)

3. Válassza **a + Új ajánlat** > **IoT Edge modult.** Megjelenik **az Új ajánlat** párbeszédpanel.

> [!IMPORTANT]
> Az ajánlat közzététele után a Partnerközpontban végzett szerkesztések csak az ajánlat újbóli közzététele után jelennek meg a kirakatokban. A módosítások végrehajtása után mindig tegye közzé újra a közzétételt.

### <a name="offer-id-and-alias"></a>Ajánlatazonosító és alias

Adja meg **az ajánlatazonosítót.** Ez a fiókban lévő minden egyes ajánlat egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a piactéri ajánlat és az Azure Resource Manager-sablonok webcímében, ha vannak ilyenek.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásjeleket, de szóközöket nem, és legfeljebb 50 karakter ből állhat. Ha például beírja az **1-es tesztajánlatot,** az ajánlat webcíme a lesz. `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`
- Az ajánlatazonosító nem módosítható, miután a Létrehozás lehetőséget választja.

Adja meg **az ajánlat aliasát**. Ez a név a Partnerközpontban található ajánlatra vonatkozik.

- Ez a név nem használatos a piactéren, és eltér az ajánlat neve és egyéb értékek jelennek meg az ügyfelek számára.
- Ez nem módosítható a **Létrehozás (Létrehozás) lehetőséget**követően.

Miután megadta ezt a két értéket, válassza a **Létrehozás gombot,** mielőtt a következő oldalra lépne, az Ajánlat áttekintése parancsra.

## <a name="offer-overview"></a>Ajánlat áttekintése

Az **Ajánlat áttekintése** lap az ajánlat közzétételéhez szükséges lépések (befejezett és közelgő) vizuális megjelenítését, valamint az egyes lépések befejezéséhez szükséges lépéseket jeleníti meg.

Ez az oldal hivatkozásokat tartalmaz az ajánlaton végzett műveletek végrehajtására a kijelölés alapján. Például:

- Ha az ajánlat piszkozat - [Ajánlattervezet törlése](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Ha az ajánlat élő - [Hagyja abba az ajánlat értékesítését](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Ha az ajánlat előzetes verzióban érhető el - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Ha még nem fejezte be a közzétevői kijelentkezést – [Közzététel megszakítása.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Ajánlat beállítása

Az ajánlat beállításához kövesse az alábbi lépéseket.

### <a name="connect-lead-management"></a>Csatlakoztassa az érdeklődőkezelést

Amikor a Partner Center rel teszi közzé az ajánlatot a piactéren, opcionálisan csatlakoztathatja azt az ügyfélkapcsolat-kezelés (CRM) rendszeréhez. Ez lehetővé teszi, hogy megkapja az ügyfél elérhetőségi adatait, amint valaki érdeklődést mutat vagy használja a terméket.

1. Válasszon egy érdeklődési célt, ahová küldhetjük az érdeklődőket. A Partnerközpont a következő CRM rendszereket támogatja:

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
- [Érdeklődőkezelés – áttekintés](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Győződjön meg arról, hogy az előugró ablakok blokkolása ki van kapcsolva).

Válassza **a Vázlat mentése lehetőséget,** mielőtt a következő szakaszhoz, tulajdonságokhoz lépne.

### <a name="properties"></a>Tulajdonságok

Ezen az oldalon meghatározhatja az ajánlat a piacon, és a jogi szerződések, amelyek támogatják az ajánlatot.

#### <a name="category"></a>Kategória

Válasszon legalább egy és legfeljebb öt kategóriát. Ezek a kategóriák arra szolgálnak, hogy az ajánlatot a megfelelő piactér keresési területekre helyezze, és megjelennek az ajánlat részleteit tartalmazó oldalon. Az ajánlat leírásában ismertesse, hogyan támogatja az ajánlat ezeket a kategóriákat. A tallózóoldalakon az összes IoT Edge-modul a **dolgok internete > IoT Edge modul** kategória alatt jelenik meg.

#### <a name="legal"></a>Jogi tudnivalók

Meg kell adnia az ajánlat feltételeit. Erre két lehetősége van:

- Használja a Microsoft Kereskedelmi Piactér általános szerződését.
- Adja meg saját feltételeit.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>A Microsoft Kereskedelmi Piactér revonatkozó általános szerződés

Standard szerződés sablont kínálunk, amely megkönnyíti a tranzakciókat a kereskedelmi piacon. Választhat, hogy a megoldást az Általános Szerződés alapján ajánlja fel, amelyet az ügyfeleknek csak egyszer kell ellenőrizniük és elfogadniuk. Ez egy jó lehetőség, ha nem szeretne egyedi feltételeket készíteni.

Ha többet szeretne megtudni a standard szerződésről, olvassa el a Microsoft Kereskedelmi Piactér általános szerződés című [témakörét.](https://docs.microsoft.com/azure/marketplace/standard-contract) Letöltheti a [Szabványos szerződés](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-fájlt is (győződjön meg róla, hogy az előugró ablakok blokkolása ki van kapcsolva).

Az Általános szerződés használatához jelölje be **a Microsoft kereskedelmi piacterének általános szerződésének használata** jelölőnégyzetet, majd kattintson az Elfogadás **gombra.**

> [!NOTE]
> Miután közzétett egy ajánlatot a Microsoft kereskedelmi piactér általános szerződésével, nem használhatja a saját egyéni szerződéseit. Vagy felajánlja a megoldást a Standard Szerződés alapján, vagy a saját feltételei alapján.

![Ez a szemléltetésa a Microsoft kereskedelmi piacterének általános szerződése jelölőnégyzet használatával.](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Az Ön saját feltételei

Saját egyéni feltételek megadásához adja meg azokat az **Általános Szerződési Feltételek** mezőbe. Ebben a mezőben korlátlan mennyiségű karaktert írhat be. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

Válassza **a Vázlat mentése lehetőséget,** mielőtt a következő szakaszra lépne, az Ajánlat listaelem.

## <a name="offer-listing"></a>Ajánlat lista

Itt adhatja meg a piactéren megjelenő ajánlatrészleteit. Ez magában foglalja az ajánlat nevét, leírását, képeit és így tovább. Ügyeljen arra, hogy kövesse a Microsoft szabályzati lapján részletezett szabályzatokat az ajánlat konfigurálása során.

> [!NOTE]
> Az ajánlat részleteinek nem kell angol nyelven lenniük, ha az ajánlat leírása a következő mondattal kezdődik: "Ez az alkalmazás csak [nem angol nyelven] érhető el." Az is rendben van, hogy egy hasznos linket kínál tartalmat olyan nyelven, amely eltér az ajánlat lista részleteit.

### <a name="name"></a>Name (Név)

Az itt megadott név az ajánlat címeként jelenik meg. Ez a mező előre ki van töltve az **Ajánlat alias** mezőjében az ajánlat létrehozásakor megadott szöveggel. A nevet később módosíthatja.

A név:

- Védjeggyel védhető (és védjegy- vagy szerzői jogi szimbólumokat is tartalmazhat).
- Nem lehet hosszabb 50 karakternél.
- Nem tartalmazhat nak emojikat.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását. Ez legfeljebb 100 karakter hosszú lehet, és a piactér keresési eredményei között használatos.

### <a name="long-summary"></a>Hosszú összegzés

Adja meg az ajánlat részletesebb leírását. Ez legfeljebb 256 karakter hosszú lehet, és a piactér keresési eredményei között használatos.

### <a name="description"></a>Leírás

Adjon meg hosszabb leírást az ajánlatról, akár 3000 karakterig. Ez jelenik meg az ügyfelek számára a piactéri lista áttekintése.

A leírásban szerepeljen az alábbiak közül egy vagy több:

- Az ajánlat értékét és legfontosabb előnyeit
- Kategória vagy iparági szövetségek, vagy mindkettő
- Alkalmazáson belüli vásárlási lehetőségek
- Minden szükséges közzététel

Az IoT Edge-modulajánlatoknak tartalmazniuk kell egy minimális hardverkövetelmények bekezdést a leírás alján. Például:

*Minimális hardverkövetelmények: Linux x64 és arm32 operációs rendszer, 1 GB RAM, 500 Mb tárhely*

Íme néhány tipp az írás a leírás:

- Egyértelműen írja le az ajánlat értékét a leírás első néhány mondatában. Adja meg a következő elemeket:
    - Az ajánlat leírása.
    - Az ajánlat előnyeit élvező felhasználó típusa.
    - Az ügyfél igényeinek megfelelően vagy az ajánlat címeinek kérdésén.
- Ne feledje, hogy az első néhány mondat megjelenhet a keresési eredmények között.
- Ne hagyatkozzon a termék értékesítéséhez szükséges funkciókra és funkciókra. Ehelyett összpontosítson az ajánlat által nyújtott értékre.
- Próbálja meg használni az iparág-specifikus szókincs vagy előny-alapú megfogalmazás.

Ahhoz, hogy az ajánlat **leírása** vonzóbbá, használja a rich text szerkesztő formázni a leírást. A Rich Text szerkesztő lehetővé teszi számok, felsorolásjelek, félkövér, dőlt betűk és behúzások hozzáadását, hogy a leírás olvashatóbb legyen.

:::image type="content" source="media/text-editor2.png" alt-text="A Rich Text szerkesztőt mutatja be." border="false":::

- A tartalom formátumának módosításához jelölje ki a formázni kívánt szöveget, és jelöljön ki egy szövegstílust, ahogy az a képernyőképen látható:

     :::image type="content" source="media/text-editor3.png" alt-text="A Rich Text szerkesztő szövegstílus-vezérlőjének szemléltetése." border="false":::

- Ha listajeles vagy számozott listát szeretne hozzáadni a szöveghez, használja a képernyőképen látható beállításokat:
  
    :::image type="content" source="media/text-editor4.png" alt-text="A rich text szerkesztőben a listajeles és számlista-vezérlőket mutatja be." border="false":::

- A szöveg behúzásának hozzáadásához vagy eltávolításához használja a képernyőképen látható beállításokat:

    :::image type="content" source="media/text-editor5.png" alt-text="A Rich Text szerkesztő behúzási vezérlőit mutatja be." border="false":::

#### <a name="privacy-policy-url"></a>Adatvédelmi irányelvek URL-címe

Adja meg a szervezet adatvédelmi szabályzatának webcímét. Ön felelős annak biztosításáért, hogy ajánlata megfeleljen az adatvédelmi törvényeknek és előírásoknak. Ön felelős azért is, hogy érvényes adatvédelmi szabályzatot tesz közzé a webhelyén.

#### <a name="useful-links"></a>Hasznos hivatkozások

Nyújtson kiegészítő online dokumentumokat az ajánlatáról. Legfeljebb 25 hivatkozást adhat hozzá. Hivatkozás hozzáadásához válassza **a + Hivatkozás hozzáadása lehetőséget,** majd töltse ki a következő mezőket:

- **Cím** – Az ügyfelek az ajánlat részletes oldalán láthatják a címet.
- **Hivatkozás (URL)** – Adjon meg egy hivatkozást az ügyfelek számára az online dokumentum megtekintéséhez. A kapcsolatnak http:// vagy https:// kell kezdődnie.

Győződjön meg arról, hogy legalább egy hivatkozást a dokumentációhoz, és egy linket a kompatibilis IoT Edge-eszközök az [Azure IoT eszközkatalógus.](https://catalog.azureiotsolutions.com/)

### <a name="contact-information"></a>Kapcsolattartási adatok

Meg kell adnia egy **támogatási partner** és egy mérnöki partner nevét, e-mail címét és **telefonszámát.** Ez az információ nem jelenik meg az ügyfelek számára. A Microsoft rendelkezésére áll, és a Felhőszolgáltató (CSP) partnerei számára is elérhető.

- Támogatási kapcsolattartó (szükséges): Általános támogatási kérdések esetén.
- Műszaki kapcsolat (szükséges): Műszaki kérdések és tanúsítási kérdések esetén.
- CsP-program kapcsolattartója (nem kötelező): A kriptaprogrammal kapcsolatos viszonteladói kérdések esetén.

A **Támogatási kapcsolattartó** szakaszban adja meg a **támogatási webhely** webcímét, ahol a partnerek támogatást kaphatnak az ajánlathoz annak alapján, hogy az ajánlat elérhető-e a globális Azure-ban, az Azure Government-ben vagy mindkettőben.

A **CSP-program kapcsolati szakaszában** adja meg azt a linket (**CSP Program Marketing Materials**), ahol a CSP-partnerek marketinganyagokat találhatnak az ajánlatához.

#### <a name="additional-marketplace-listing-resources"></a>További piactér-listaforrások

Ha többet szeretne tudni az ajánlatlisták létrehozásáról, olvassa el [az Ajánlott eljárások felsorolása című témakört.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>Marketplace-képek

Adjon meg emblémákat és képeket az ajánlatához. Minden képnek .png formátumúnak kell lennie. Az elmosódott képek elutasításra kerülnek.

>[!Note]
>Ha probléma van a fájlok feltöltésével, győződjön https://upload.xboxlive.com meg arról, hogy a helyi hálózat nem blokkolja a Partnerközpont által használt szolgáltatást.

#### <a name="store-logos"></a>Áruházi emblémák

Adjon meg .png fájlokat az ajánlat emblémájáról a következő négy képpontméret mindegyikében:

- **Kicsi (48 x 48)**
- **Közepes (90 x 90)**
- **Nagy (216 x 216)**
- **Széles (255 x 115)**

Mind a négy embléma kötelező, és a piactér i.

#### <a name="screenshots-optional"></a>Képernyőképek (nem kötelező)

Adjon hozzá legfeljebb öt képernyőképet, amelyek megmutatják, hogyan működik az ajánlata. Mindegyiknek 1280 x 720 képpont méretűnek és .png formátumúnak kell lennie.

#### <a name="videos-optional"></a>Videók (nem kötelező)

Akár öt videót is hozzáadhatsz, amelyek bemutatják az ajánlatodat. Adja meg a videó nevét, webcímét és a videó miniatűr .png képét 1280 x 720 képpont méretűen.

#### <a name="offer-examples"></a>Ajánlatpéldák

A következő példák bemutatják, hogyan jelennek meg az ajánlatlista-mezők az ajánlat különböző helyein.

Ez a képernyőkép az **Ajánlat listalapját** mutatja az Azure Piactéren.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="Az Ajánlat listalap az Azure Marketplace-en.":::

Ez a képernyőkép az Azure Marketplace keresési eredményeit mutatja:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="A keresési eredményeket az Azure Marketplace-en mutatja be.":::

Ez a képernyőkép az **Ajánlat listalapját** mutatja az Azure Portalon.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="Az Ajánlat listalap az Azure Portalon.":::

Ez a képernyőkép az Azure Portalon jeleníti meg a keresési eredményeket.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="Az Ajánlat listalap az Azure Portalon.":::

Válassza **a Vázlat mentése lehetőséget,** mielőtt továbblépne a következő, Előnézet szakaszra.

## <a name="preview"></a>Előzetes verzió

Az **Előnézet lapon**korlátozott **előnézeti közönséget** választhat az ajánlat érvényesítéséhez, mielőtt élőben közzétenné azt a szélesebb piactéri közönség számára.

> [!IMPORTANT]
> Miután megtekintette az ajánlatot az Előzetes verzióban, az Élő ben való **közzétételhez** válassza az Élő ben lehetőséget.

Adja meg az előzetes verzióközönséget az Azure-előfizetésazonosítók GUID-jai, valamint az egyes verziók hozadékának választható leírásával. Egyik mezőt sem látják az ügyfelek.

> [!NOTE]
> Az Azure-előfizetés-azonosítóját az Azure Portal Előfizetések lapján találja.

Adjon hozzá legalább egy Azure-előfizetés-azonosítót, akár egyenként (legfeljebb 10) vagy csv-fájl feltöltésével (legfeljebb 100). Az előfizetési azonosítók hozzáadásával megadhatja, hogy ki tekintheti meg az ajánlat előnézetét az élő közzététel előtt. Ha az ajánlat már él, megadhat egy előnézeti közönséget az ajánlat módosításainak vagy frissítéseinek teszteléséhez.

> [!NOTE]
> Az előnézeti közönség eltér a privát közönségtől. Az **előnézeti** közönség láthatja és megerősítheti az összes ajánlati tervet, mielőtt azok a piactéren megjelennének, beleértve azokat is, amelyeket csak egy **privát** közönség nek tesznek közzé (az Elérhetőség lapon).

Válassza **a Vázlat mentése lehetőséget,** mielőtt továbblépne a következő szakaszra, a Tervezés áttekintése parancsra.

### <a name="plan-overview"></a>Terv – áttekintés

Ezen a lapon különböző csomagbeállításokat adhat meg ugyanazon az ajánlaton belül a Partnerközpontban. Ezeket a terveket korábban SK-nak vagy raktározási egységeknek nevezték. A tervek eltérőek lehetnek a rendelkezésre álló felhők, például a globális felhők, a kormányzati felhők és a terv által hivatkozott kép tekintetében. Az ajánlat marketplace-en való listázásához legalább egy csomagot be kell állítania.

A tervek létrehozása után a **Terv áttekintése** lapon a következők láthatók:

- Terv nevek
- Díjszabási modell
- Felhő elérhetősége (globális vagy kormányzati)
- Jelenlegi közzétételi állapot
- Az elérhető műveletek

A Terv áttekintésében elérhető műveletek a terv aktuális állapotától függően változnak. Ezek például az alábbi jelentések lehetnek:

- **Vázlat törlése**: Ha a terv állapota Vázlat.
- **Az eladási terv leállítása:** Ha a terv állapotát élőben teszik közzé.

#### <a name="create-new-plan"></a>Új terv létrehozása

Válassza **az Új terv létrehozása**lehetőséget. Megjelenik **az Új terv** párbeszédpanel.

A **Terv azonosítója** mezőben hozzon létre egy egyedi tervazonosítót az ajánlatban szereplő minden egyes tervhez. Ez az azonosító látható lesz a termék webcímében szereplő ügyfelek számára. Csak kisbetűket és számokat, kötőjeleket vagy aláhúzásjeleket és legfeljebb 50 karaktert használjon.

A **Terv neve** mezőbe írja be a terv nevét. Az ügyfelek ezt a nevet látják, amikor eldöntik, hogy melyik csomagot válasszák ki az ajánlaton belül. Hozzon létre egy egyedi nevet az ajánlatban szereplő minden tervhez. Használhat például egy **Windows Server** ajánlatnevet **a Windows Server 2016** és a Windows **Server 2019**csomagokkal.

> [!NOTE]
> A csomagazonosító nem módosítható a **Létrehozás (Létrehozás) választódat**választ.

Kattintson a **Létrehozás** gombra.

### <a name="plan-setup"></a>A terv beállítása

Ezen a lapon beállíthatja, hogy a csomag mely felhőkben érhető el. Az ezen a lapon található válaszok hatással vannak arra, hogy mely mezők jelenjenek meg a többi lapon.

#### <a name="cloud-availability"></a>Felhő elérhetősége

A csomagnak legalább egy felhőben elérhetőnek kell lennie az Azure IoT Hub használatával.

Válassza ki az **Azure Global** lehetőséget, hogy a csomag használható az ügyfelek az összes globális Azure-régiókban, amelyek a piacteret. További információt a [Földrajzi elérhetőség és pénznemtámogatás](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Válassza ki az [Azure Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) opciót, hogy a megoldás itt jelenjen meg. Ez egy kormányzati közösségi felhő, amely ellenőrzött hozzáféréssel rendelkezik az Amerikai Egyesült Államok szövetségi, állami és helyi vagy törzsi kormányzati szerveinek, valamint a kiszolgálásra jogosult partnereknek. Közzétevőként Ön a felelős a felhőalapú közösség megfelelőségi szabályozásáért, biztonsági intézkedéseiért és gyakorlati tanácsaiért. Az Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az Egyesült Államokban található). Az Azure Government [nek való közzététel](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) előtt tesztelje és erősítse meg a megoldást ezen a területen belül, mivel az eredmények eltérőek lehetnek. A megoldás megrendezéséhez és teszteléséhez kérjen próbafiókot a [Microsoft Azure Government próbaverziójától.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Miután a csomag közzé, és elérhető egy adott felhőben, nem tudja eltávolítani a felhőt.

#### <a name="azure-government-cloud-certifications"></a>Az Azure Government Felhőminősítései

Ez a beállítás csak akkor látható, ha az **Azure Government Cloud** a Felhő **elérhetősége**alatt van kiválasztva.

Az Azure Government-szolgáltatások olyan adatokat kezelnek, amelyekre bizonyos kormányzati előírások és követelmények vonatkoznak. Például FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 és CJIS. Ha fel szeretné hívni a figyelmet a programok tanúsítványaira, legfeljebb 100 hivatkozást adhat meg, amelyek ismertetik a tanúsítványokat. Ezek lehetnek linkeket a listát a program közvetlenül, vagy a saját honlapján. Ezek a hivatkozások csak az Azure Government-ügyfelek számára láthatók.

## <a name="plan-listing"></a>A terv listája

Ez a lap az ugyanazon ajánlaton belüli egyes tervekre vonatkozó konkrét információkat jeleníti meg.

### <a name="plan-name"></a>Terv neve

Ez előre ki van töltve azzal a névvel, amelyet a terv létrehozásakor adott. Ezt a nevet szükség szerint módosíthatja. Legfeljebb 50 karakter hosszú lehet. Ez a név jelenik meg a csomag címe ként az Azure Marketplace-en és az Azure Portalon. Ez az alapértelmezett modulnév, miután a terv készen áll a használatra.

### <a name="plan-summary"></a>Terv összegzése

Adjon meg egy rövid összefoglalót a csomagról (nem az ajánlatról). Ez az összefoglaló megjelenik az Azure Marketplace keresési eredményei között, és legfeljebb 100 karaktert tartalmazhat.

### <a name="plan-description"></a>Terv leírása

Írja le, mi teszi egyedivé ezt a tervet, valamint az ajánlaton belüli csomagok közötti különbségeket. Ne írja le az ajánlatot, csak a tervet. Ez a leírás megjelenik az Azure Marketplace-en és az Azure Portalon az ajánlat listalapján. Ez lehet ugyanaz a tartalom, amelyet a terv összegzésében megadott, és legfeljebb 2000 karaktert tartalmazhat.

A mezők kitöltése után válassza a **Piszkozat mentése** lehetőséget.

#### <a name="plan-examples"></a>Példák tervezése

Az alábbi példák bemutatják, hogyan jelennek meg a tervlistamezők különböző nézetekben.

Ezek azok a mezők az Azure Marketplace-en, amikor a csomag részleteit tekinti kúra részletei:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="Az Azure Marketplace-en a csomag részleteinek megtekintésekor látható mezőket mutatja be.":::

Ezek a csomag részletei az Azure Portalon:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="A csomag részleteit az Azure Portalon mutatja be.":::

## <a name="availability"></a>Rendelkezésre állás

Ha el szeretné rejteni a közzétett ajánlatot, hogy az ügyfelek ne kereshessenek, tallózhassanak vagy vásárolhassanak a piactéren, jelölje be a **Terv elrejtése** jelölőnégyzetet az Elérhetőség lapon.

Ez a mező gyakran használatos, ha:

- Az ajánlat célja, hogy csak közvetetten, ha hivatkozott egy másik alkalmazás.
- Az ajánlatot nem szabad egyenként megvásárolni.
- A tervet a kezdeti teszteléshez használták, és már nem releváns.
- A tervet ideiglenes vagy szezonális ajánlatokra használták, és a továbbiakban nem ajánlották fel.

## <a name="technical-configuration"></a>Műszaki konfiguráció

Az **IoT Edge-modul** ajánlattípusa egy adott típusú tároló, amely egy IoT Edge-eszközön fut. A **Műszaki konfiguráció** lapon az [Azure Container Registry-en](https://azure.microsoft.com/services/container-registry/)belüli tárolókép-tárház referenciaadatait, valamint olyan konfigurációs beállításokat biztosít, amelyek lehetővé teszik az ügyfelek számára a modul egyszerű használatát.

Az ajánlat közzététele után az IoT Edge-tároló rendszerkép egy adott nyilvános tároló beállításjegyzékben az Azure Piactérre kerül. Az Azure-felhasználók által a modul használatára vonatkozó összes kérés az Azure Marketplace nyilvános tároló beállításjegyzékéből kerül ki, nem pedig a privát tároló beállításjegyzékéből.

Több platformot is megcélozhat, és címkék használatával a modultároló-rendszerkép több verzióját is megadhat. A címkékről és a verziószámozásról az [IoT Edge modul technikai eszközeinek előkészítése](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-iot-edge-module-asset)című témakörben olvashat bővebben.

### <a name="image-repository-details"></a>Képtár részletei

A következő információkat fogja megadni a **Képtár részletei** lapon.

**Válassza ki a lemezkép forrását:** Válassza az **Azure Container Registry** beállítást.

**Azure-előfizetés-azonosító:** Adja meg az előfizetés-azonosítót, ahol az erőforrás-használat jelentett, és a szolgáltatások számlázása az Azure Container Registry, amely tartalmazza a tároló rendszerképet. Ezt az azonosítót az Azure Portal [Előfizetések lapján](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) találja.

**Azure-erőforráscsoport neve:** Adja meg az [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) nevét, amely tartalmazza az Azure Container Registry a tárolórendszerképpel. Az erőforráscsoportnak elérhetőnek kell lennie az előfizetés-azonosítóban (fent). A nevet az [Azure Portalerőforrás-csoportok](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) lapján találja.

**Azure container registry name:** Adja meg a tárolórendszerképet tartalmazó [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) nevét. A tároló beállításjegyzékének jelen kell lennie a korábban megadott Azure-erőforráscsoportban. Csak a rendszerleíró adatbázis nevét adja meg, a teljes bejelentkezési kiszolgáló nevét ne. Ne felejtse el kihagyni **azurecr.io** a névből. A beállításjegyzék nevét az Azure Portalon található [tárolóregisztriók lapon](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) találja.

**Rendszergazdai felhasználónév az Azure Container Registry:** Adja meg a [rendszergazdai felhasználónév](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) társított az Azure Container Registry, amely a tároló rendszerképet. A felhasználónév és a jelszó szükséges ahhoz, hogy a vállalat hozzáférhessen a beállításjegyzékhez. A rendszergazdai felhasználónév és jelszó lekéréséhez állítsa a **rendszergazdai funkcióval rendelkező** tulajdonságot **True** értékre az Azure parancssori felület (CLI) használatával. Igény szerint **beállíthatja a rendszergazdai felhasználó** **engedélyezése** az Azure Portalon.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="A Tároló beállításjegyzékének frissítése párbeszédpanelt mutatja be.":::

**Az Azure Container Registry jelszó:** Adja meg a jelszót a rendszergazdai felhasználónév, amely az Azure Container Registry társított, és a tároló rendszerkép. A felhasználónév és a jelszó szükséges ahhoz, hogy a vállalat hozzáférhessen a beállításjegyzékhez. A jelszót az Azure Portalon a **Container Registry** > **Access Keys** vagy az Azure CLI a [show parancsot.](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="Az Azure Portal hozzáférési kulcsképernyőjének szemlélteti.":::

**Tárház neve az Azure Container Registry**. Adja meg az Azure Container Registry repository, amely a rendszerképet. A tárház nevét akkor adja meg, amikor a lemezképet a rendszerleíró adatbázisba lenyomja. A tárház nevét a [Tárolórendszerleíró](https://azure.microsoft.com/services/container-registry/) > **adattárak lapon**találhatja meg. További információ: [View container registry repositories in the Azure Portal](https://docs.microsoft.com/azure/container-registry/container-registry-repositories). Ne feledje, hogy a név beállítása után nem módosítható. A fiók minden ajánlatához használjon egyedi nevet.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Képcímkék az ajánlat új verzióihoz

Az ügyfeleknek képesnek kell lenniük arra, hogy automatikusan frissítéseket kapjanak az Azure Piactérről, amikor frissítést tesz közzé. Ha nem szeretnének frissíteni, képesnek kell lenniük arra, hogy a kép egy adott verzióján maradjanak. Ezt úgy teheti meg, hogy minden alkalommal új képcímkéket ad hozzá, amikor frissíti a képet.

**Képcímke**. Ennek a mezőnek tartalmaznia kell egy **legújabb** címkét, amely a kép legújabb verziójára mutat az összes támogatott platformon. Tartalmaznia kell egy verziócímkét is (például az xx.xx.xx-től kezdődően, ahol az xx egy szám). Az ügyfeleknek több platformot kell használniuk a [jegyzékfájl-címkék](https://github.com/estesp/manifest-tool) használatával. A jegyzékcímkét hivatkozó összes címkét hozzá kell adni, hogy feltölthessük őket. Az összes jegyzékcímke (a legújabb címke kivételével) x.y- vagy X.Y.Z- betűvel kell kezdődnie, ahol X, Y és Z egész szám. Ha például egy legújabb címke az 1.0.1-linux-x64, az 1.0.1 linux-arm32 és az 1.0.1-windows-arm32 elemre mutat, akkor ezt a hat címkét hozzá kell adni ehhez a mezőhöz. A címkékés verziószámozás, [az IoT Edge modul technikai eszközök előkészítése című](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets) témakörben.

### <a name="default-deployment-settings-optional"></a>Alapértelmezett telepítési beállítások (nem kötelező)

Adja meg a leggyakoribb beállításokat az IoT Edge-modul üzembe helyezéséhez. Optimalizálja az ügyfelek központi telepítését azáltal, hogy lehetővé teszi számukra, hogy az IoT Edge modult az alapértelmezett beállításokkal elindítsák.

**Alapértelmezett útvonalak**. Az IoT Edge Hub kezeli a modulok, az IoT Hub és az eszközök közötti kommunikációt. Beállíthatja a modulok és az IoT Hub közötti adatbeviteli és -kimeneti útvonalakat, így rugalmasan küldhet üzeneteket, ahol további szolgáltatásokra van szükség az üzenetek feldolgozásához vagy további kód írásához. Az útvonalak név-érték párok használatával épülnek fel. Legfeljebb öt alapértelmezett útvonalnevet adhat meg, amelyek mindegyike legfeljebb 512 karakter hosszú.

Ügyeljen arra, hogy a megfelelő [útvonalszintaxist](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) használja az útvonalértékben (általában FROM/message/* INTO $UPSTREAM). Ez azt jelenti, hogy bármely modul által küldött üzenetek az IoT Hubhoz. A modulra való hivatkozáshoz használja az alapértelmezett modulnevét, amely az **Ajánlat neve**lesz , szóközök és speciális karakterek nélkül. Ha más, még nem ismert modulokra szeretne hivatkozni, használja a <FROM_MODULE_NAME> konvenciót, hogy tájékoztassa ügyfeleit arról, hogy frissíteniük kell ezeket az adatokat. Az IoT Edge-útvonalakról az [Útvonalak deklarálása.For](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)details about IoT Edge routes, see Declare routes.

Ha például a ContosoModule modul figyeli a ContosoInput és a ContosoOutput kimeneti adatok bemeneteit, célszerű a következő két alapértelmezett útvonalat meghatározni:

- Név #1: ToContosoModule
- Érték #1: FROM /messages/modules/<FROM_MODULE_NAME>/outputok/* INTO BrokeredEndpoint("/modules/ContosoModule/input/ContosoInput")
- Név #2: FromContosoModuleToCloud
- Érték #2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Alapértelmezett modul iker kívánt tulajdonságokat**. A modul iker egy JSON-dokumentum az IoT Hub, amely tárolja a modulpéldány állapotadatait, beleértve a kívánt tulajdonságokat. A kívánt tulajdonságok a jelentett tulajdonságokkal együtt használják a modul konfigurációjának vagy feltételeinek szinkronizálásához. A megoldás háttérkezelője beállíthatja a kívánt tulajdonságokat, és a modul el tudja olvasni őket. A modul változási értesítéseket is kaphat a kívánt tulajdonságokban. A kívánt tulajdonságok legfeljebb öt név/érték pár használatával jönnek létre, és minden alapértelmezett értéknek 512 karakternél kisebbnek kell lennie. Legfeljebb öt név/iker kívánt tulajdonság definiálható. Az iker kívánt tulajdonságok értékeinek érvényes JSON-nak kell lenniük, nem escaped, négy szintből álló maximális egymásba ágyazott hierarchiával rendelkező tömbök nélkül. Olyan esetben, amikor az alapértelmezett értékhez szükséges paraméternek nincs értelme (például az ügyfél kiszolgálójának IP-címe), alapértelmezett értékként hozzáadhat egy paramétert. Ha többet szeretne megtudni az ikerkívánt tulajdonságokról, olvassa el a [Kívánt tulajdonságok definiálása vagy frissítése ..](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)

Ha például egy modul dinamikusan konfigurálható frissítési gyakoriságot támogat két kívánt tulajdonság használatával, célszerű a következő alapértelmezett ikertulajdonságot meghatározni:

- Név #1: RefreshRate
- #1 érték: 60

**Alapértelmezett környezeti változók**. A környezeti változók kiegészítő információkat nyújtanak egy modulhoz, amely segíti a konfigurációs folyamatot. A környezeti változók név-érték párok használatával jönnek létre. Minden alapértelmezett környezeti változó nevének és értékének 512 karakternél kisebbnek kell lennie, és legfeljebb öt karaktert határozhat meg. Ha egy alapértelmezett értékhez szükséges paraméternek nincs értelme (például az ügyfél kiszolgálójának IP-címe), hozzáadhat egy paramétert alapértelmezett értékként.

Ha például egy modulnak el kell fogadnia a használati feltételeket az indítás előtt, a következő környezeti változót határozhatja meg:

- Név #1: ACCEPT_EULA
- Érték #1: Y

**Alapértelmezett tárolólétrehozási beállítások**. A tároló létrehozási beállításai az IoT Edge-modul Docker-tárolójának létrehozását irányítják. Az IoT Edge támogatja a Docker-motor API-tároló létrehozása beállításait. Tekintse meg a [Listatárolók összes beállítását.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) A létrehozási beállítások mezőnek érvényes JSON-nak, nem escaped és 512 karakternél rövidebbnek kell lennie.

Ha például egy modulhoz portkötés szükséges, adja meg a következő létrehozási beállításokat:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}}}}

## <a name="review-and-publish"></a>Véleményezés és közzététel

Miután elvégezte az ajánlat összes szükséges szakaszát, elküldheti azt véleményezésre és közzétételre.

A portál jobb felső sarkában válassza a **Véleményezés és közzététel**lehetőséget.

Az ellenőrzés lapon láthatja a közzétételállapotát:

- Tekintse meg az ajánlat egyes szakaszainak készültségi állapotát. Nem tehetközzé, amíg az ajánlat összes szakasza készként van megjelölve.
    - **Nincs elindítva** – A szakasz még nem indult el, és ki kell tölteni.
    - **Hiányos** – A szakasz hibákat, amelyeket ki kell javítani, vagy megköveteli, hogy további információkat. A jelen dokumentum korábbi szakaszaiban útmutatást talál.
    - **Kész** – A szakasz tartalmazza az összes szükséges adatot, és nincsenek hibák. Az ajánlat minden szakaszának teljesnek kell lennie az ajánlat elküldése előtt.
- Adja meg a tesztelési utasításokat a minősítő csapatnak, hogy megbizonyosodjon az ajánlat helyes teszteléséről. Is, hogy minden olyan kiegészítő megjegyzéseket, amelyek hasznosak a megértés az ajánlatot.

Az ajánlat közzétételre való elküldéséhez válassza a **Közzététel**lehetőséget.

Küldünk Önnek egy e-mailt, hogy tudd, ha az ajánlat előzetes verziója elérhető az ellenőrzéshez és a jóváhagyáshoz. Ha közzé szeretné tenni az ajánlatot a nyilvánosság számára (vagy ha egy privát ajánlatot, egy privát közönségnek), nyissa meg a Partnerközpontot, és válassza a **Go-live**lehetőséget.

## <a name="next-steps"></a>További lépések

- [Meglévő ajánlat frissítése a kereskedelmi piacon](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)