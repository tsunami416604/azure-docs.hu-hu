---
title: Power BI app-ajánlat létrehozása a Microsoft kereskedelmi piactéren
description: Megtudhatja, hogyan hozhat létre és tehet közzé Power BI alkalmazás-ajánlatokat a Microsoft AppSourcehoz.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 0a247626566a4b0156d879954bf6734d248f01d8
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85513521"
---
# <a name="create-a-power-bi-app-for-microsoft-appsource"></a>Power BI-alkalmazás létrehozása Microsoft AppSourcehoz

Ez a cikk bemutatja, hogyan hozhat létre és tehet közzé Power BI alkalmazás-ajánlatokat a Microsoft [AppSource](https://appsource.microsoft.com/).

A Kezdés előtt [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , ha még nem tette meg. Győződjön meg róla, hogy a fiókja regisztrálva van a kereskedelmi piactér programban.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés**lehetőséget.
3. Az Áttekintés lapon válassza az **+ új ajánlat**  >  **Power bi Service app**elemet.

   ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-power-bi-app.png)

> [!NOTE]
> Az ajánlat közzétételét követően a partner Centerben végzett módosítások csak az ajánlat ismételt közzététele után jelennek meg a kirakatokban. Győződjön meg arról, hogy a módosítások végrehajtása után mindig újra közzé kell tennie a módosításokat.

> [!IMPORTANT]
> Ha **Power bi szolgáltatásalkalmazás** nem jelenik meg vagy nem engedélyezett, a fiók nem rendelkezik engedéllyel az ajánlat típusának létrehozásához. Ellenőrizze, hogy teljesítette-e az ajánlat típusával kapcsolatos összes [követelményt](create-power-bi-app-overview.md) , beleértve a fejlesztői fiók regisztrálását is.

## <a name="new-offer"></a>Új ajánlat

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a Piactéri ajánlathoz tartozó webcímek és Azure Resource Manager sablonok esetében, ha van ilyen.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásokat, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1** értéket adja meg, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Az ajánlat azonosítója a **Létrehozás**gombra kattintva nem módosítható.

Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név.

- Ez a név nem használatos a piactéren, és nem egyezik meg az ajánlat nevével és az ügyfelek számára megjelenített egyéb értékekkel.
- Az ajánlat aliasa nem módosítható a **Létrehozás**gombra kattintva.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-overview"></a>Ajánlat áttekintése

Ezen az oldalon látható az ajánlat közzétételéhez szükséges lépések vizuális ábrázolása (a befejezett és a közelgő is), valamint az egyes lépések befejezésének ideje.

Hivatkozásokat tartalmaz az ajánlaton végzett műveletekre, az Ön által választott beállítás alapján. Példa:

- Ha az ajánlat Piszkozat- [törlési ajánlat](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Ha az ajánlat élő – [az ajánlat értékesítésének leállítása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Ha az ajánlat előzetes verzióban érhető [el](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Ha még nem fejezte be a közzétevő kijelentkezését – [Közzététel megszakítása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Ajánlat beállítása

### <a name="customer-leads"></a>Ügyfél-érdeklődők

Ha az ajánlatot a piactéren a partner centerrel teszi közzé, akkor azt az Ügyfélkapcsolat-kezelési (CRM) rendszerhez kell kötnie. Ez lehetővé teszi az ügyfelek kapcsolattartási adatainak megszerzését, amint valaki érdeklődik a termék iránt, vagy használja a terméket.

1. Válasszon egy érdeklődési célt, ahová küldhetjük az érdeklődőket. A partner Center a következő CRM-rendszereket támogatja:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) ügyfél-engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Ha a CRM-rendszer nem szerepel a fentiekben, az [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) vagy a [https-végpont](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) használatával tárolhatja az ügyfél-érdeklődő adatait. Ezután exportálja az adatait a CRM-rendszerbe.

2. Az ajánlat összekötése a vezető célhoz a partner Centerben való közzététel során.
3. Győződjön meg arról, hogy a vezető célhoz való kapcsolódás megfelelően van konfigurálva. Miután közzétette a partner Centerben, érvényesítjük a kapcsolatot, és elküldünk egy tesztelési érdeklődőt. Miközben az ajánlat előzetes verziójának megtekintése előtt megtekinti az ajánlatot, tesztelheti az érdeklődői kapcsolatokat is, ha az előnézeti környezetben próbálja megvásárolni az ajánlatot.
4. Győződjön meg arról, hogy a vezető célhely kapcsolata frissül, így nem veszíti el az érdeklődőket.

Íme néhány további érdeklődő felügyeleti erőforrás:

- [Az érdeklődők kezelése – áttekintés](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Gyakori kérdések az érdeklődők kezeléséről](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Az érdeklődők konfigurálásának gyakori hibái](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- Az [érdeklődők kezelése – áttekintés](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (ellenőrizze, hogy ki van-e kapcsolva az előugró ablakok blokkolása)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="properties"></a>Tulajdonságok

Ezen a lapon megadhatja az ajánlatnak a piactéren való csoportosításához használt kategóriákat és iparágakat, az alkalmazás verzióját, valamint az ajánlatát támogató jogi szerződéseket.

### <a name="category"></a>Kategória

Válasszon legalább egyet és legfeljebb három kategóriát. Ezek a kategóriák arra szolgálnak, hogy az ajánlatot a megfelelő Piactéri keresési területeken helyezze el, és az ajánlat részletei oldalon jelennek meg. Az ajánlat leírásában ismertesse, hogy az ajánlat hogyan támogatja ezeket a kategóriákat.

### <a name="industry"></a>Iparág

Igény szerint két iparágban és két vertikálisan is kiválaszthatja az egyes iparágakat. Míg a kategóriák az ajánlat megjelenítésére szolgálnak, az iparág és a vertikálisok a keresési szűrőkben használatosak, és a kirakatban lesznek alkalmazva. Ha az ajánlat egy adott iparágra és/vagy vertikálisra vonatkozik, az ajánlat leírása alapján megtudhatja, hogyan támogatja az ajánlat a kiválasztott iparágakat vagy vertikális célokat. Ha az ajánlata nem iparági specifikus, hagyja üresen ezt a szakaszt.

> [!NOTE]
> Amikor új iparágakat és vertikális funkciókat teszünk elérhetővé az ajánlat-felderítési élmény javítása érdekében, előfordulhat, hogy egyes iparágak vagy vertikálisok még nem láthatók a kirakatban. A (*) jelöléssel rendelkező iparágak és vertikálisak jövőbeli időpontban lesznek elérhetők. Az összes közzétett ajánlat a kulcsszavas keresés használatával kereshető.
<p>&nbsp;

| **Iparág** | **Alágazat** |
| --- | --- |
| * Autóipar | * Autóipar |
| Mezőgazdaság | * Egyéb – nem szegmentált |
| Disztribúció | * Nagykereskedelmi<br>Csomagok és csomagok szállítása |
| Education | *Felsőoktatás <br> * Elsődleges és másodlagos oktatás/K-12<br>* Könyvtárak és múzeumok |
| Pénzügyi szolgáltatások | *Banki és tőkepiacok <br> * Biztosítási |
| Government | *A védelem és az intelligencia (amelyet a nemzeti és a Közbiztonságnak neveztek) <br> * Közbiztonság és jogérvényesülés<br>* Polgári kormányzat |
| Egészségügyi ellátás (ún. állapot) | *Állapot költségviselő <br> * Állapot szolgáltatója<br>* Gyógyszeripar |
| Gyártási és erőforrás-használat (a gyártás meghívásához használt) | *Vegyi és Agrokémiai <br> * Diszkrét gyártás<br>* Energia |
| Kiskereskedelmi és fogyasztási cikkek (kiskereskedelmi használatra) | *Fogyasztási <br> cikkek* Kiskereskedők |
| * Média és kommunikáció (média és szórakozás néven használatos) | *Média és szórakoztatás <br> * Távközlési |
| Professzionális szolgáltatások | *Jogi <br> * tudnivalók Partneri szakmai szolgáltatások |
| * Architektúra és építőipar (architektúra-mérnöki felépítéshez használatos) | * Egyéb – nem szegmentált |
| * Vendéglátás és utazás | *Hotelek és szabadidő <br> * Utazás és szállítás<br>* Éttermek és élelmiszeripari szolgáltatások |
| * Egyéb állami szektorbeli iparágak | *Erdőgazdálkodás és halászat <br> * Nonprofit szervezeteknek |
| * Ingatlan | * Egyéb – nem szegmentált |

### <a name="legal"></a>Jogi tudnivalók

#### <a name="terms-and-conditions"></a>használati feltételei

A saját használati feltételeinek megadásához a **feltételek és kikötések** mezőben adja meg a 10 000 karaktert. Ha a feltételek és kikötések további leírást igényelnek, adjon meg egy webhivatkozást, ahol megtalálhatók. Aktív hivatkozásként jelenik meg az ügyfelek számára.

Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

Válassza a **Piszkozat mentése** lehetőséget, mielőtt továbblépne a következő szakaszra.

## <a name="offer-listing"></a>Ajánlati lista

Itt megadhatja a piactéren megjelenő ajánlat részleteit. Ebbe beletartozik az ajánlat neve, leírása, képei és így tovább.

### <a name="language"></a>Nyelv

Válassza ki azt a nyelvet, amelyben az ajánlat fel lesz sorolva. Jelenleg az egyetlen elérhető lehetőség az **angol (Egyesült Államok)** .

Adja meg a piactér adatait (például az ajánlat nevét, leírását és lemezképeit) az egyes nyelvekhez/piacokhoz. Válassza ki a nyelv/piac nevét az információ megadásához.

> [!NOTE]
> Az ajánlat részleteit nem kötelező angol nyelven megadni, ha az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelvű] verzióban érhető el." Azt is megteheti, hogy hasznos hivatkozást biztosít, amely az ajánlati listán használt nyelvtől eltérő nyelven kínál tartalmat.

Íme egy példa arra, hogyan jelennek meg az ajánlati információk a Microsoft AppSourceban (a felsorolt árak kizárólag a tényleges költségekkel kapcsolatos célokat szolgálnak):

:::image type="content" source="media/example-power-bi-app.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat a Microsoft AppSourceban.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Embléma
2. Termékek
3. Kategóriák
4. Ágazatok
5. Támogatási címe (hivatkozás)
6. Használati feltételek
7. Adatvédelmi irányelvek
8. Ajánlat neve
9. Összefoglalás
10. Leírás
11. Képernyőképek/videók

### <a name="name"></a>Name (Név)

Az itt megadott név az ajánlat címeként jelenik meg. Ez a mező előre ki van töltve az ajánlat- **alias** mezőben megadott szöveggel az ajánlat létrehozásakor. A nevet később módosíthatja.

A név:

- Védjeggyel is rendelkezhet (és a védjegyek és a szerzői jogi szimbólumok is szerepelhetnek).
- Nem lehet hosszabb 50 karakternél.
- Nem szerepelhetnek hangulatjelek.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását. Ez akár 100 karakter hosszú lehet, és a piactér keresési eredményeiben használható.

### <a name="description"></a>Leírás

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="search-keywords"></a>Kulcsszavak keresése

Legfeljebb három opcionális keresési kulcsszót adhat meg, amelyek segítségével az ügyfelek megtalálják az ajánlatot a piactéren. A legjobb eredmény érdekében ezeket a kulcsszavakat is használhatja a leírásában.

### <a name="helpprivacy-web-addresses"></a>Súgó/adatvédelem webcímek

Olyan hivatkozásokat adhat meg, amelyek segítségével az ügyfelek jobban megismerhetik az ajánlatot.

#### <a name="help-link"></a>Súgó hivatkozása

Adja meg azt a webcímet, ahol az ügyfelek többet tudnak nyújtani az ajánlatról.

#### <a name="privacy-policy-url"></a>Adatvédelmi szabályzat URL-címe

Adja meg a szervezete adatvédelmi szabályzatának webcímet. Ön felelős annak biztosításáért, hogy az ajánlat megfelel az adatvédelmi törvényeknek és előírásoknak. Ön is felelős az érvényes adatvédelmi szabályzatok közzétételéhez a webhelyén.

### <a name="contact-information"></a>Kapcsolattartási adatok

Meg kell adnia egy **támogatási kapcsolattartó** nevét, e-mail-címét és telefonszámát, valamint egy **mérnöki kapcsolattartót**. Ezek az adatok nem jelennek meg az ügyfelek számára. A Microsoft számára elérhető, és a Cloud Solution Provider (CSP) partnerei számára is biztosítható.

- Támogatási kapcsolattartó (kötelező): általános támogatási kérdések.
- Engineering Contact (kötelező): technikai kérdések és minősítési problémák esetén.
- CSP programbeli kapcsolat (nem kötelező): a CSP-programhoz kapcsolódó viszonteladói kérdésekért.

A **támogatási kapcsolattartó** szakaszban adja meg a **támogatási webhely** webcímet, ahol a partnerek az ajánlat támogatását megtalálják.

### <a name="supporting-documents"></a>Támogató dokumentumok

Adjon meg legalább egy három kapcsolódó marketing dokumentumot PDF formátumban. Például a tanulmányokat, a brosúrákat, a feladatlistákat és a bemutatókat.

### <a name="marketplace-images"></a>Marketplace-rendszerképek

Adja meg az ajánlathoz használni kívánt emblémákat és képeket. Minden képnek PNG formátumúnak kell lennie. A rendszer elutasítja a homályos képeket.

>[!NOTE]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a `https://upload.xboxlive.com` partner központ által használt szolgáltatást.

#### <a name="store-logos"></a>Emblémák tárolása

Adja meg az ajánlat emblémájának PNG-fájlját két képpontos méretben:
- **Kicsi** (48 x 48)
- **Nagyméretű** (216 x 216)

Mindkét emblémát meg kell adni, és a piactér-lista különböző helyein használják.

#### <a name="screenshots"></a>Képernyőképek

Vegyen fel legalább egy legfeljebb öt képernyőképet, amely bemutatja, hogyan működik az ajánlata. Mindegyiknek 1280 x 720 képpont méretűnek és PNG formátumúnak kell lennie.

#### <a name="videos-optional"></a>Videók (nem kötelező)

Akár öt videót is hozzáadhat, amelyek bemutatják az ajánlatát. Adja meg a videó nevét, a webcímet és a videó miniatűr PNG-képét, amely 1280 x 720 képpont méretű.

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

Az ajánlatok listájának létrehozásával kapcsolatos további tudnivalókért tekintse meg az ajánlott [eljárások listáját](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

## <a name="technical-configuration"></a>Technikai konfiguráció

Népszerűsítse alkalmazását Power BI Service-ben, és adja meg a Power BI app Installer-hivatkozást, amely lehetővé teszi az ügyfelek számára az alkalmazás telepítését. További információ: [alkalmazások közzététele irányítópultokkal és jelentésekkel Power BIban](https://docs.microsoft.com/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Kiegészítő tartalom

Adja meg az ajánlatával kapcsolatos további információkat, hogy segítsen a hitelesítésben. Ezek az adatok nem jelennek meg az ügyfelek számára, és nem jelennek meg a piactéren.

### <a name="validation-assets"></a>Érvényesítési eszközök

Opcionálisan hozzáadhat utasításokat (legfeljebb 3 000 karakter) a Microsoft ellenőrzési csapatának az alkalmazás konfigurálásához, összekapcsolásához és teszteléséhez. Az adatok összekapcsolásának tesztelésére szolgáló jellemző konfigurációs beállításokat, fiókokat, paramétereket és egyéb információkat is megadhat. Ezek az információk csak az ellenőrzési csapat számára láthatók, és csak ellenőrzési célokra használhatók.

## <a name="review-and-publish"></a>Áttekintés és közzététel

Miután elvégezte az ajánlat összes szükséges részét, elküldheti ajánlatát az áttekintéshez és a közzétételhez.

A portál jobb felső sarkában válassza a **felülvizsgálat és közzététel**lehetőséget.

A felülvizsgálati oldalon a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát. A közzététel addig nem lehetséges, amíg az ajánlat összes része készként van megjelölve.
  - **Nincs elindítva** – a szakasz nem lett elindítva, és el kell végezni.
  - **Hiányos** – a szakasz olyan hibákat tartalmaz, amelyeket meg kell oldania, vagy további információkat kell megadnia. Útmutatásért tekintse meg a jelen dokumentum korábbi szakaszait.
  - **Befejezés** – a szakasz minden szükséges adattal rendelkezik, és nincsenek hibák. Az ajánlat elküldése előtt az ajánlat összes részének teljesnek kell lennie.
- Adja meg a minősítési csapat tesztelési utasításait, hogy az alkalmazás megfelelően legyen tesztelve. Továbbá adjon meg az ajánlat megismeréséhez hasznos kiegészítő megjegyzéseket.

Az ajánlat közzétételre való elküldéséhez válassza a **Közzététel**lehetőséget.

Egy e-mailt küldünk, amelyből megtudhatja, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz. Az ajánlat nyilvános közzétételéhez lépjen a partner Center webhelyre, és válassza a **Go-Live**lehetőséget.
