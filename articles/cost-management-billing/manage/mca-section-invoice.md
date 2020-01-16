---
title: A számlázási igények alapján rendszerezheti az Azure-t
description: Megtudhatja, hogyan szervezheti meg a számlán a költségeket.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c170a6a1a731a648c16e0081e760947256df8a0e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991060"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>A költségek rendszerezése a Számlázási fiók testreszabásával

A Microsoft Customer szerződéshez tartozó számlázási fiókja rugalmasságot biztosít, hogy az Ön igényeinek megfelelően szervezze a költségeket, akár részleg, projekt vagy fejlesztési környezet alapján. 

Ez a cikk azt ismerteti, hogyan használható a Azure Portal a költségek rendszerezéséhez. Ez egy Microsoft-ügyfél szerződéshez tartozó számlázási fiókra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>A fiók strukturálása számlázási profilokkal és a számla fejezeteivel

A Microsoft ügyfél-szerződés számlázási fiókjában számlázási profilok és számlázási csoportok használatával rendezheti a költségeket.

![A Microsoft ügyfél-szerződés számlázási hierarchiáját bemutató képernyőkép](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Számlázási profil

A számlázási profil a számlát és a kapcsolódó számlázási adatokat, például a fizetési módszereket és a számlázási címeket jelöli. A hónap elején a fiókhoz tartozó összes számlázási profilhoz létrejön egy havi számla. A számla az Azure-használat és az előző hónap egyéb vásárlásainak díját tartalmazza.

A számlázási profil automatikusan létrejön a számlázási fiókkal, amikor regisztrál az Azure-ra. További számlázási profilokat is létrehozhat, hogy a költségek több havi számlán legyenek rendszerezve. 

> [!IMPORTANT]
>
> A további számlázási profilok létrehozása hatással lehet a teljes összegre. További információ: [új számlázási profilok hozzáadásakor megfontolandó szempontok](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Számlázási szakasz

A számla szakasz a számla költségeinek csoportosítását jelöli. A fiókban minden számlázási profilhoz automatikusan létrejön egy számla szakasz. További szakaszt is létrehozhat, hogy az igényeinek megfelelően szervezze a költségeket. Minden számla szakasz megjelenik a számlán az adott hónapban felmerülő díjak alapján. 

Az alábbi képen egy olyan számla látható, amely két számla szakaszt tartalmaz: mérnöki és marketing. Az egyes szakaszok összefoglaló és részletes díjai a számlán jelennek meg. A képen látható árak kizárólag példaként szolgálnak, és nem jelentik az Azure-szolgáltatások tényleges árát. 

![Egy szakaszt tartalmazó számlát ábrázoló kép](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Számlázási fiók szerkezete gyakori forgatókönyvekhez

Ez a szakasz a költségek és a számlázási fiókok megfelelő struktúráinak szervezésével kapcsolatos gyakori forgatókönyveket ismerteti:

|Alkalmazási helyzet  |Struktúra  |
|---------|---------|
|Az Azure-hoz készült Jack-regisztrációhoz egy havi számla szükséges. | Számlázási profil és számla szakasz. Ez a struktúra automatikusan be van állítva az Azure-ra, amikor regisztrál az Azure-ra, és nem igényel további lépéseket. |

![Információs ábra egy egyszerű számlázási forgatókönyvhöz](./media/mca-section-invoice/organize-billing-scenario1.png)

|Alkalmazási helyzet  |Struktúra  |
|---------|---------|
|A contoso egy kisméretű szervezet, amely egyetlen havi számlára van szüksége, de a részlegük a marketing és a pénzügy alapján csoportosítja a költségeket.  | A contoso számlázási profilja, valamint a marketing-és pénzügyi részlegekhez tartozó számla szakasz. |

![Információs ábra egy egyszerű számlázási forgatókönyvhöz](./media/mca-section-invoice/organize-billing-scenario2.png)

|Alkalmazási helyzet  |Struktúra  |
|---------|---------|
|A fabrikam egy közepes méretű szervezet, amely külön számlákat igényel a mérnöki és marketing részlegek számára. A mérnöki részlegek számára a költségeket a környezetek és a fejlesztés alapján kívánják csoportosítani.  | Számlázási profil marketing és pénzügyi részlegek számára. Marketing részleg esetén az üzemi és fejlesztési környezetekhez tartozó számla szakasz. |

![Információs ábra egy egyszerű számlázási forgatókönyvhöz](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Új számla szakasz létrehozása

Számlázási szakasz létrehozásához a **számlázási profil tulajdonosának** vagy a **számlázási profil közreműködőjének** kell lennie. További információ: [a számlázási profilok számláinak kezelése](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről](./media/mca-section-invoice/search-cmb.png)

3. Válassza a **Számlázási profilok** lehetőséget a bal oldali ablaktáblán. A listából válassza ki a számlázási profilt. Az új szakasz a kiválasztott számlázási profil számláján fog megjelenni. 

   [![a számlázási profilok listáját megjelenítő képernyőkép](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. Válassza a **számla szakaszt** a bal oldali ablaktáblán, majd válassza a **Hozzáadás** lehetőséget az oldal tetején.

   [![a számlák hozzáadását bemutató képernyőkép](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Adja meg a számla szakasz nevét. 

   [![a számla szakasz létrehozási lapját megjelenítő képernyőkép](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Kattintson a **Létrehozás** gombra.

## <a name="create-a-new-billing-profile"></a>Új számlázási profil létrehozása

Számlázási profil létrehozásához **Számlázási fiók tulajdonosának** vagy **Számlázási fiók közreműködőinek**kell lennie. További információ: [Számlázási profilok kezelése számlázási fiókhoz](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> A további számlázási profilok létrehozása hatással lehet a teljes összegre. További információ: [új számlázási profilok hozzáadásakor megfontolandó szempontok](#things-to-consider-when-adding-new-billing-profiles).

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről](./media/mca-section-invoice/search-cmb.png)

3. Válassza a **Számlázási profilok** lehetőséget a bal oldali ablaktáblán, majd válassza a **Hozzáadás** lehetőséget az oldal tetején.

   [![a számlázási profilok listáját megjelenítő képernyőkép](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Ha nem látja a Hozzáadás gombot a számlázási profil oldalon, a szolgáltatás nem érhető el a fiókhoz. Jelenleg csak olyan fiókok esetében érhető el, amelyek a Microsoft-képviselővel való együttműködés során lettek beállítva.

4. Töltse ki az űrlapot, és kattintson a **Létrehozás** lehetőségre.

   [a számlázási profil létrehozási lapját bemutató ![képernyőkép](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Mező  |Meghatározás  |
    |---------|---------|
    |Név     | Egy megjelenített név, amely megkönnyíti a számlázási profil azonosítását a Azure Portalban.  |
    |POSTAFIÓK száma    | Egy opcionális beszerzési sorszám. A rendszer a számlázási profilhoz generált számlákon megjeleníti a PO-számot. |
    |Számlázási cím   | A számlázási cím a számlázási profilhoz generált számlákon jelenik meg. |
    |E-mail számla   | Jelölje be az e-mail-számla jelölőnégyzetet a számlázási profilhoz tartozó számlák e-mailben történő fogadásához. Ha nincs bekapcsolva, a Azure Portalban megtekintheti és letöltheti a számlákat.|

5. Kattintson a **Létrehozás** gombra.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>A számlák és a számlázási profilok összekapcsolásának díjai

Miután igény szerint testreszabta a számlázási fiókját, összekapcsolhatja az előfizetéseket és más termékeket a kívánt számla szakasz és számlázási profil alapján.

### <a name="link-a-new-subscription"></a>Új előfizetés csatolása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Keressen rá az **Előfizetések** kifejezésre.

   [az előfizetéshez tartozó keresés a portálon ![képernyőkép](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. A lap tetején válassza a **Hozzáadás** elemet.

   ![Képernyőkép az Előfizetések nézet Hozzáadás gombjáról](./media/mca-section-invoice/subscription-add.png)

4. Ha több számlázási fiókhoz is rendelkezik hozzáféréssel, válassza ki a Microsoft ügyfél-szerződés számlázási fiókját.

   ![Képernyőkép az Előfizetések nézet Hozzáadás gombjáról](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Válassza ki azt a számlázási profilt, amelyet a rendszer az előfizetés használatáért számláz. Az ehhez az előfizetéshez tartozó Azure-használati díjak és egyéb vásárlások díjait a rendszer a kiválasztott számlázási profil számlájára számlázza.

6. Válassza ki a számla szakaszt az előfizetés költségeinek összekapcsolásához. A díjak a számlázási profil számláján jelen szakasz alatt jelennek meg.

7. Válasszon ki egy Azure-csomagot, és adjon meg egy rövid nevet az előfizetéséhez. 

9. Kattintson a **Create** (Létrehozás) gombra.  

### <a name="link-existing-subscriptions-and-products"></a>Meglévő előfizetések és termékek összekapcsolása

Ha már rendelkezik Azure-előfizetésekkel vagy más termékekkel, például az Azure Marketplace-rel és az alkalmazás forrásával, a költségek átrendezéséhez áthelyezheti őket a meglévő számla szakaszból egy másik számla szakaszba. 

> [!IMPORTANT]
>
> Az előfizetések és egyéb termékek csak az ugyanahhoz a számlázási profilhoz tartozó számlázási csoportok között helyezhetők át. Az előfizetések és termékek különböző számlázási profilokban való áthelyezése nem támogatott.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az előfizetés kifejezés kereséséről a portálon](./media/mca-section-invoice/search-cmb.png)

3. Ha egy előfizetést új számla szakaszhoz szeretne kapcsolni, válassza az **Azure-előfizetések** lehetőséget a képernyő bal oldalán. Más termékek, például az Azure Marketplace és az App Source-erőforrások esetében válassza az **ismétlődő díjak**lehetőséget.

   [![a számla módosításának lehetőségét bemutató képernyőkép](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. A lapon kattintson a három pontra az előfizetéshez vagy a termékhez, amelyet egy új számla szakaszhoz szeretne csatolni. Válassza a **számla módosítása szakaszt**.

5. Válassza ki az új számla szakaszt a legördülő listából. A legördülő lista csak azokat a számlázási szakaszokat jeleníti meg, amelyek ugyanahhoz a számlázási profilhoz vannak társítva, mint a meglévő számla szakasz.

    [![az új számla kiválasztása szakaszt bemutató képernyőkép](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Kattintson a **Mentés** gombra.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Új számlázási profilok hozzáadásakor megfontolandó szempontok

### <a name="azure-usage-charges-may-be-impacted"></a>Az Azure-használati díjakat befolyásolhatja

A Microsoft-ügyfél szerződésének számlázási fiókjában az Azure-használatot havonta összesítjük az egyes számlázási profilokhoz. Az Azure-erőforrások többszintes díjszabással történő árának meghatározása az egyes számlázási profilok használati díja alapján történik. A használatot nem összesíti a számlázási profilok között az ár kiszámításakor. Ez hatással lehet a több számlázási profillal rendelkező fiókok esetében az Azure-használat általános költségeit.

Lássunk egy példát arra, hogy a költségek Hogyan változnak két forgatókönyv esetén. A forgatókönyvekben használt árak kizárólag példaként szolgálnak, és nem jelentik az Azure-szolgáltatások tényleges árát.

#### <a name="you-only-have-one-billing-profile"></a>Csak egy számlázási profilja van.

Tegyük fel, hogy az Azure Block blob Storage-t használja, amely az első 50 terabájt (TB) USD. 00184 GB-ot, 00177 majd a következő 450 terabájt (TB) esetében a GB-ot veszi igénybe. A számlázási profilba felszámított előfizetésekben 100 TB-ot használt.

|  Szintek díjszabása (USD) |Mennyiség | Mennyiség (USD)|
|---------|---------|---------|
|1,84/TB az első 50 TB/hó    | 50 TB        | 92,0   |
|1,77/TB a következő 450 TB/hó    |  50 TB         | 88,5   |
|Összes     |     100 TB  | 180,5

Ebben a forgatókönyvben a 100 TB-os adatmennyiség teljes díja **180,5**

#### <a name="you-have-multiple-billing-profiles"></a>Több számlázási profil is van.

Most tegyük fel, hogy létrehozott egy másik számlázási profilt, és 50 GB-ot használ az első számlázási profilra és 50 GB-ra számlázott előfizetéseken keresztül, a második számlázási profilra kifizetett előfizetések alapján, az alábbiakban látható, hogy mennyit számítunk fel.

`Charges for the first billing profile`

|  Szintek díjszabása (USD) |Mennyiség | Mennyiség (USD)|
|---------|---------|---------|
|1,84/TB az első 50 TB/hó    | 50 TB        | 92,0  |
|1,77/TB a következő 450 TB/hó    |  0 TB         | 0,0  |
|Összes     |     50 TB  | 92,0 

`Charges for the second billing profile`

|  Szintek díjszabása (USD) |Mennyiség | Mennyiség (USD)|
|---------|---------|---------|
|1,84/TB az első 50 TB/hó    | 50 TB        | 92,0  |
|1,77/TB a következő 450 TB/hó    |  0 TB         | 0,0  |
|Összes     |     50 TB  | 92,0 

Ebben a forgatókönyvben a 100 TB-os adatmennyiség teljes díja **184,0** (92,0 * 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Előfordulhat, hogy az Azure foglalási előnyei nem vonatkoznak az összes előfizetésre

A megosztott hatókörű Azure-foglalások egyetlen számlázási profilban lévő előfizetésekre lesznek alkalmazva, és nem oszthatók meg a számlázási profilok között. 

![Információs ábra a különböző számlázási fiókokra vonatkozó foglalási alkalmazásokhoz](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

A fenti képen a contoso két előfizetéssel rendelkezik. Az Azure foglalási kedvezmény a Számlázási fiók strukturált módjától függően eltérő módon van alkalmazva. A bal oldali forgatókönyvben a foglalási kedvezményt a mérnöki számlázási profilhoz tartozó előfizetésekre is alkalmazza a rendszer. A jobb oldali forgatókönyvben a foglalási kedvezmény csak az 1. előfizetésre lesz érvényes, mivel ez az egyetlen előfizetés, amely a mérnöki számlázási profilra van kiszámlázva. 

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel az ügyfélszolgálattal

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>Következő lépések

- [További Azure-előfizetés létrehozása a Microsoft-ügyfélszerződésben](create-subscription.md)
- [Számlázási szerepkörök kezelése az Azure Portalon](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Azure-előfizetések számlázási tulajdonjogának beszerzése más számlázási fiókok felhasználóitól](mca-request-billing-ownership.md)
