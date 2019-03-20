---
title: Egy meglévő virtuális Gépet kínál az Azure piactéren elérhető frissítés |} A Microsoft Docs
description: Azt ismerteti, hogyan frissíthető egy meglévő Virtuálisgép-ajánlat az Azure Marketplace-en.
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
ms.topic: article
ms.date: 08/27/2018
ms.author: Ankit.Sud
ms.openlocfilehash: 254215389d129874f7ac3fc855b26819d41a095c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852474"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Az Azure Marketplace-en egy meglévő Virtuálisgép-ajánlat frissítése

Ez a cikk végigvezeti a frissítése a virtuális gép (VM) az ajánlat a különböző aspektusait a [Cloud Partner Portalon](https://cloudpartner.azure.com/) és majd újbóli közzététele az ajánlatot. 

Nincsenek számos gyakori oka, hogy frissíteni az ajánlatot, többek között:

-  Adjon hozzá egy új Virtuálisgép-lemezkép verziója meglévő termékváltozatokra
-  A Termékváltozat módosítása régiókban érhető el
-  Adja hozzá az új termékváltozatokra
-  Frissítés a Marketplace-en metaadatait az ajánlat vagy egyes termékváltozatok
-  Frissítse a csomagok esetén, amelyek díjszabása

A portál kínál segítséget nyújt a ezeket a módosításokat, a **összehasonlítása** és **előzmények** funkciókat.  

>[!Note]
>Cloud Solution Providers (CSP) partner csatorna vehetnek részt már elérhető.  Lásd: [Cloud Solution Providers](../../cloud-solution-providers.md) további tájékoztatást a marketing, az ajánlat keretében a Microsoft CSP partner-csatornákon.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Virtuálisgép-ajánlat és az SKU engedett módosítása

Van néhány olyan Virtuálisgép-ajánlat vagy Termékváltozat, amely nem módosítható, ha az ajánlat élő az Azure Marketplace-en, főleg attribútumai:

-  **Ajánlat azonosítója** és **közzétevő-azonosító** az ajánlat
-  **SKU-azonosítója** a meglévő termékváltozatok
-  Adatlemez meglévő SKU-k száma
-  Számlázási és licencelési modell módosítja a meglévő termékváltozatok
-  Egy közzétett termékváltozatra növeli ár


## <a name="common-update-operations"></a>Gyakori frissítési műveletek

Habár számos különféle jellemzőit, módosíthatja a Virtuálisgép-ajánlat, a következő műveletek gyakoriak.

### <a name="update-the-vm-image-version-for-a-sku"></a>A Termékváltozat a Virtuálisgép-lemezkép verziója frissítés

Szokás Virtuálisgép-lemezkép rendszeresen frissíteni kell a biztonsági javítások, további funkciók és így tovább.  Ilyen esetekben alatt szeretné frissíteni a Virtuálisgép-rendszerképet, amely a Termékváltozat hivatkozik az alábbi lépések segítségével:

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

2.  A **minden ajánlat**, keresse meg a frissítésére vonatkozó ajánlatot.

3.  Az a **termékváltozatok** lapra, majd frissíteni a Virtuálisgép-lemezkép társított termékváltozat.

4.  A **lemez verziója**, kattintson a **+ új lemez verziója** egy új Virtuálisgép-lemezkép hozzáadása.

5.  Adja meg az új Virtuálisgép-rendszerképek **lemez verziója**. A lemez verziója szükséges a [sémantická verze](https://semver.org/) formátumban. Verziók az űrlap X.Y.Z, ha X, Y és Z egész számoknak kell lenniük. Ellenőrizze, hogy megadta az új verzió nagyobb, mint a korábbi verziók; Ellenkező esetben újbóli közzététele után az új verzió nem jelennek meg a portálon vagy az Azure piactéren.

6.  A **OS VHD URL-cím**, adja meg a [közös hozzáférésű jogosultságkód (SAS) URI](./cpp-get-sas-uri.md) az operációs rendszer virtuális Merevlemeze számára létrehozott. 

    > [!WARNING] 
    > Az adatlemezek száma a Termékváltozat különböző verziói között nem lehet módosítani. Ha a korábbi verziók adatlemezek konfigurálva volt, az új verzió is rendelkeznie kell azonos számú adatlemezeket.

7.  Kattintson a **közzététel** az új virtuális gép verzió közzététele az Azure piactér a munkafolyamat elindításához.


### <a name="change-region-availability-of-a-sku"></a>Régiónkénti elérhetőség, a Termékváltozat módosítása

Idővel előfordulhat, hogy szeretné az ajánlat/SKU több régióban elérhetővé.  Azt is megteheti érdemes leállítani az ajánlat/SKU támogatása egy adott régióban.
Rendelkezésre állási módosításához használja az alábbi lépéseket:

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

2.  A **minden ajánlat** keresse meg az ajánlat, amelyet szeretne frissíteni.

3.  Az a **termékváltozatok** lapra, majd a termékváltozat, amely a rendelkezésre állási módosítani szeretne.

4.  Kattintson a a **kiválasztása országok** gomb alatt a **ország/régió rendelkezésre állási** mező.

5.  Az előugró régiónkénti elérhetőség régiók hozzáadása vagy eltávolítása a Ez a termékváltozat.

6.  Kattintson a **közzététel** frissíteni az SKU-régiók rendelkezésre állása a közzététel munkafolyamat elindításához.

A Termékváltozat egy új régióban elérhetővé van folyamatban, létre kell keresztül adott régió díjszabás megadásának lehetőségét a **díjszabási adatok exportálása** funkciót. Egy régiót vissza után előtt ad hozzá, ha nem lehet frissíteni, a díjszabás, mert a díjszabási nem engedélyezettek.


### <a name="add-a-new-sku"></a>Adjon hozzá egy új Termékváltozatot

Használja az alábbi lépéseket, hogy egy új Termékváltozatban érhető el a létező ajánlat: 

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

2.  A **minden ajánlat** keresse meg az ajánlat, amelyet szeretne frissíteni.

3.  Alatt a **termékváltozatok** fülre, kattintson a **adja hozzá az új Termékváltozat** , és adja meg egy **SKU-azonosítója** az előugró.

4.  Tegye közzé újra a virtuális Gépet, a cikkben leírt módon [közzététele egy virtuális gépet az Azure piactéren](./cpp-publish-offer.md).

5.  Kattintson a **közzététel** közzététele az új Termékváltozat a munkafolyamat elindításához.


### <a name="update-offer-marketplace-metadata"></a>Az ajánlat marketplace metaadatok frissítése

Kövesse az alábbi lépéseket a Marketplace-en metaadatok frissítése – cég neve, emblémák, stb. – a kötelezettségvállaláshoz kapcsolódó: 

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

2.  A **minden ajánlat** keresse meg az ajánlat, amelyet szeretne frissíteni.

3.  Ugrás a **Marketplace** lapfülre, majd kövesse a cikk a [közzététele egy virtuális gépet az Azure piactéren](./cpp-publish-offer.md) metaadatok módosításokat.

4.  Kattintson a **közzététel** a módosítások közzétételéhez a munkafolyamat elindításához.


### <a name="update-pricing-on-published-offers"></a>Frissítse a közzétett ajánlatok díjszabása

Miután közzétette az utólagos számlázású, nem közvetlenül növelhető a Termékváltozat díjszabása.  (Azonban hozzon létre egy új Termékváltozatban ugyanazt az ajánlatnak, törölje a régi Termékváltozat, és az ajánlat az új ügyfelek majd újbóli.)  Ezzel szemben a csökkenthető az ár egy közzétett ajánlat az alábbi lépéseket követve:

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

2.  A **minden ajánlat**, keresse meg a frissítésére vonatkozó ajánlatot.

3.  Kattintson az SKU-t, amelynek díjszabása csökkentése érdekében.

4.  Ha megadta, 1 x 1 díjszabását grafikus felhasználói Felülettel, az ár közvetlenül a felhasználói felületen módosíthatja. Ha beállította az importálási/exportálási számolótábla keresztül díjszabás, csak csökkentheti az árak az importálási/exportálási funkciójával.

3.  Kattintson a **Save** (Mentés) gombra.

4.  Kattintson a **közzététel** a módosítások közzétételéhez a munkafolyamat elindításához.

Az új csökkent díjszabás az új ügyfelek számára látható lesz, ha a webhelyen.  Ez az új ár az ügyfeleket érinti, a következő módon:

- Új ügyfeleket az új arány számítjuk. 
- A meglévő ügyfelek az árcsökkenést meg fognak jelenni visszamenőlegesen a Start a számlázási ciklus során, ami az árcsökkenést hatékony vált.
Ha rendelkezik már lett számlázásra a ciklus során, ami árcsökkenés történt, visszatérítés kapja meg, hogy biztosítsák a csökkent díj a következő elszámolási időszakban.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Egyszerűsített pénznem díjszabása

2018 szeptember 1-től kezdődően új szakasz nevű **pénznem díjszabás egyszerűsített** megjelenik a portálon. A Microsoft Azure Marketplace-en üzleti van egyszerűsítésével világszerte több kiszámítható díjszabás és a gyűjtemény ügyfelei engedélyezésével. Ez egyszerűsítheti tartalmazza az ügyfelek feléjük azt pénznemek számának csökkentése.

Az új szakasz ezen új pénznemekben díjszabás vesz igénybe. Után minden ügyfél ezen új elszámolási pénznemekben át lettek telepítve, az eredeti díjszabási szakaszban kivonjuk a forgalomból, és csak a pénznem díjszabás egyszerűsített szakasz marad.

Amíg nem módosul a 2018. November 1. viselkedésmintáit beállítása egy új ár régiókra vonatkozó elszámolás pénznem kell. Nem lehet növelheti a régiók viselkedésmintáit nem módosul az elszámolás pénznem díja.

> [!NOTE] 
> API-k használatával az ajánlat közzététele, egy új szakaszt az ajánlat JSON belül jelenhet meg. Ez akkor lehet feliratozva `virtualMachinePricingV2` vagy `monthlyPricingV2`ajánlat típusától függően. 

Ha ez a változás kapcsolatban bármilyen kérdése van, lépjen kapcsolatba [Azure piactér támogatási](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>A szolgáltatás összehasonlítása

Ha módosítja a már közzétett ajánlat, használhatja a **összehasonlítása** naplózása a módosításokat, hogy történtek-e a szolgáltatás. Ez a funkció használatához:

1.  A szerkesztési folyamat bármely mozzanata, kattintson a **összehasonlítása** gomb az ajánlatban.

    ![Hasonlítsa össze a szolgáltatás gomb](./media/publishvm_037.png)


2.  Marketing eszközök és metaadatok verzióinak egymás mellett tekintheti meg.


## <a name="history-of-publishing-actions"></a>Közzétételi műveletek előzményei

Közzétételi tevékenységi előzményeket megtekintéséhez kattintson a a **előzmények** elemét a bal oldali menüsáv a Cloud Partner portálra. Itt lesz, amely az Azure Marketplace-ajánlat teljes élettartama során került sor időbélyegzővel műveletek megtekintéséhez.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

