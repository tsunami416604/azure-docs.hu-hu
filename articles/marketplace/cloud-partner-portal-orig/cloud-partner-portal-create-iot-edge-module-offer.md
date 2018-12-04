---
title: Hozzon létre egy IoT Edge-modul ajánlatot |} A Microsoft Docs
description: Hogyan teheti közzé egy új IoT Edge-modul a Marketplace-en.
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
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: bf7d639c682e443f29b31b3c6d7438e89c406fde
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838633"
---
# <a name="how-to-publish-a-new-iot-edge-module-in-the-cloud-partner-portal"></a>A Cloud Partner portálra az új IoT Edge-modul közzététele

Ez a cikk egy új IoT Edge-modul ajánlat közzétételének lépéseit ismerteti.

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek vonatkoznak az IoT Edge-modul közzététele az Azure Marketplace-en.

-   A hozzáférést a [Cloud Partner portálra (CPP)](https://cloudpartner.azure.com/#alloffers). További információkért lásd: a [közzétételi útmutató](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   Az IoT Edge-modul egy Azure Container Registry lévő üzemeltetett rendelkezik.

-   Az IoT Edge modul metaadatok kész (beleértve a lista nem teljes) rendelkezik:

    -   Beosztás

    -   Leírás (alapszintű HTML formátumban)

    -   Png-kép formátumban, és a következő méretben embléma: 40 képpont x 40 képpont, 90 képpont x 90 képpont, 115 képpont x 115 képpont, 255 képpont x 115 képpont.

    -   Használati feltételei és adatvédelmi szabályzat

<a name="prepare-your-iot-edge-module-listing-in-cpp"></a>Az IoT Edge-modul nyilvántartásba, vételre CPP előkészítése
-------------------------------------------

### <a name="create-a-new-offer-of-the-type-iot-edge-module"></a>Hozzon létre egy új ajánlatunk, az IoT Edge-modul típusa 

Ezek a lépések segítségével előkészítheti az IoT Edge-modul listázása:

-   Jelentkezzen a [CPP fiók](https://cloudpartner.azure.com/).

>[!Note]
>A Cloud Partner portálra kapcsolatos általános információkért ellenőrizze a [további dokumentáció](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-getting-started-with-the-cloud-partner-portal)

-   Válassza ki **új ajánlat**, majd válassza ki **IoT Edge-modul**.

>[!NOTE]
>Az IoT Edge-modul egy tároló, amely kifejezetten jön létre (IoT Edge) futtatásához. A forgatókönyvek IoT Edge-modul tárgyalt IoT Edge környezetben kell értelme. Az üzembe helyezés egy IoT Edge eszköz lehetünk az alapértelmezett konfigurációs beállítások is tartalmaz. A tároló is az IoT Edge modul SDK-t a edgeHub és az IoT hubbal való kommunikáció engedélyezése.

### <a name="define-your-offer-settings"></a>Az ajánlat beállításainak megadása

Az ajánlat beállítások lapon írja be az ajánlat adatait.

![Az ajánlat identitás](./media/cloud-partner-portal-create-iot-edge-module-offer/offer-identity.png)


-   A **Ajánlatazonosító** egyedileg azonosítja az ajánlatban szereplő CPP, és az ügyfelek által használt URL-CÍMEK használhatók.

-   A **neve** csak akkor látható, Ön által való CPP ezt az ajánlatot.

### <a name="create-one-or-more-skus"></a>Hozzon létre egy vagy több termékváltozatok

Minden SKU felel meg egy tárolórendszerképet. Rendelkeznie kell legalább egy Termékváltozat és egynél több adhat hozzá. A Termékváltozat két részből áll:

-   Termékváltozat-metaadatok

-   Tároló metaadatai

**A Termékváltozat létrehozása:**

Válassza ki a **termékváltozatok** lapra, és válassza ki **új Termékváltozat**.

![Új Termékváltozat](./media/cloud-partner-portal-create-iot-edge-module-offer/SKUs.png)

A Termékváltozat-metaadatokat, amelyek szükségesek a következő mezőket tartalmazza:
- SKU-azonosítója – egy egyedi azonosítója.
- Cím – a Termékváltozat-címhez, legfeljebb 50 karakter hosszú lehet.
- Összegzés – rövid leírását legfeljebb 100 karakter hosszú lehet.
- Leírás – hosszú leírás.
- Ez a Termékváltozat elrejtése – az alapértelmezett érték **nem**.
   
![Termékváltozat részletei](./media/cloud-partner-portal-create-iot-edge-module-offer/SKU-settings.png)

#### <a name="iot-edge-module-metadata-and-the-container-registry"></a>IoT Edge-modul metaadatok és a tároló-beállításjegyzék

Az IoT Edge-modul metaadatok rendelkezik az Azure container registry (ACR) tárolt rendszerkép kapcsolódó információk. Az Azure Marketplace-en a nyilvános piactérről beállításjegyzék másolja át a lemezképet, és minősítést követően érhetők el ügyfeleink számára legyen. Az összes felhasználói kérelem által az IoT Edge-modul rendszerképének szolgálja ki, a Marketplace-en tárolóregisztrációs adatbázisból.

![Tárolólemezképek](./media/cloud-partner-portal-create-iot-edge-module-offer/container-images.png)

**Tárolórendszerképek**

Adattár részletei a következő kötelező mezők rendelkeznek:

-   **Előfizetés-azonosító** ahol az ACR-beállításjegyzékbe megtalálható az Azure előfizetés-azonosító.

-   **Erőforráscsoport neve** – az erőforráscsoport neve, az ACR-beállításjegyzékbe.

-   **Beállításjegyzék neve** – az ACR-beállításjegyzék neve.

-   **Adattár neve** – a tárház nevét. A beállítása után, ezt az értéket később már nem módosítható. A nevének egyedinek kell lennie, hogy a fiók nincs egyéb ajánlattal nem ugyanazzal a névvel.

-   **Felhasználónév** – az ACR-REL (rendszergazdai felhasználónév) tartozó felhasználónév.

-   **Jelszó** – az ACR-REL tartozó jelszót.

    >[!Note]
    >A felhasználónév és jelszó szükségesek győződjön meg arról, hogy partnereink férhetnek hozzá az ACR-REL a közzétételi folyamat leírását.

Amikor egy IoT Edge-modul rendszerképének közzétételére megadhat egy vagy több címkéket. Győződjön meg arról, a modulnak (alapértelmezett) "legújabb" címke hozzáadásához, így könnyedén azonosíthatja a modul tesztelés során.

A következő IoT Edge-modul jellemzőit is megadhat:

-   **createOptions** – az alapértelmezett createOptions át, az IoT Edge-modul beépített is elindítható.

-   **ikereszköz:** – az alapértelmezett ikereszköz át, az IoT Edge-modul segítségével elindítható beépített, amikor az IoT-modul SDK-val.

-   **útvonalak:** – az alapértelmezett útvonalakat, hogy az IoT Edge-modul beépített indítható, ha az IoT-modul SDK-val.

### <a name="describe-your-iot-edge-module-for-your-customers"></a>Az IoT Edge-modul ismertetik az ügyfelek számára

A piactér lap hozzáadása a marketing-specifikus tartalmait. Ezekkel az információkkal már, hogy mi lesz nyilvánosan láthatóvá és a listán az Azure Marketplace-en.

![IoT Edge-modul áttekintése](./media/cloud-partner-portal-create-iot-edge-module-offer/overview.png)

-   **Cím** – az IoT Edge-modul, amely a nyilvános címe.

-   **Összefoglaló** -lapok az IoT Edge-modul, amely a nyilvánosan elérhető a legtöbb oldalt, mint a böngészés összegzését.

-   **Mennyi ideig összefoglaló** – az IoT Edge-modul, amely a nyilvánosan elérhető, ha a modul a kiemelt összegzését. 

-   **Leírás** – leírását, az IoT Edge-modul, amely a nyilvános termék részleteit megjelenítő oldalon. (Segítségével alapszintű HTML-címkéket a leírás formázhatja.)

-   **Marketing azonosító** – egy egyedi azonosítója, amellyel a termék URL-Címének létrehozása. Az URL-cím szerepel a következő formátumban: *azuremarketplace.microsoft.com/enus/marketplace/apps/yourpublisherid.youriotedgemodulemarketingidentifier*.

-   **Előzetes verzió előfizetés azonosítókat** – ezeket az előfizetéseket hozzáféréssel rendelkező felhasználók tudják a minősítési lépésben után jelenik meg az IoT Edge-modul, és mielőtt élő kerül.

-   **Hasznos hivatkozások** -jeleníthető meg. a termék részletes információját legfeljebb 10 hivatkozásokat adhat hozzá.

-   **Kategóriák javasolt** – legfeljebb öt kategóriák kiválasztása. Azok fog megjelenni a termék részletes információját. Jelenleg minden IoT Edge-modulok alatt jelennek-e a *IOT- \> IoT Edge-modul* kategóriákat, tallózási lapjain.

-   **Emblémák** – az IoT Edge-modul embléma PNG formátumú rendszerképek feltöltése. Használja az alábbi méretek: és pontosan a következő méretek: 40 képpont x 40 képpont, 90 képpont x 90 képpont, 115 képpont x 115 képpont, 255 képpont x 115 képpont.

-   **Képernyőképek** – képernyőképek jelennek meg a termék részletes információját. Azok a jó módszer annak szemléltetésére, az IoT Edge-modul célja, és hogyan működik. Architektúra-diagramok megjelenítése, vagy kis illusztrációk példányhoz használja.

-   **Videók** -videók jelennek meg a termék részletes információját. Azok a jó módszer annak szemléltetésére, az IoT Edge-modul célja, és hogyan működik.

-   **Felügyeleti vezethet** – kiválaszthatja, hogy a rendszer, az azt mutatják be, a termékek iránt érdeklődők gyűjtéséhez.

-   **Adatvédelmi** – az adatvédelmi szabályzat mutató URL-címet kell rendelkeznie.

-   **Használati feltételek** – használati feltételeket kell rendelkeznie. HTML-címkék használatával formázása ezen az oldalon, vagy a más oldalakon egyikére mutatnak.

### <a name="enter-your-support-contact-information"></a>Adja meg a támogatási kapcsolattartási adatokat

A támogatási lapon adjon meg **Engineering Contact** és **ügyfél-támogatási** információkat.

![Támogatási kapcsolattartók](./media/cloud-partner-portal-create-iot-edge-module-offer/support-contact-info.png)


## <a name="certify-your-iot-edge-module"></a>Az IoT Edge-modul tanúsítása

Miután megadta a szükséges információkat, válassza ki a **közzététel** küldése az IoT Edge-modul minősítésre. Látni fogja azt mutatják be, ütemterv céljai a minősítési folyamat lépéseit.

**A modul ellenőrzése**

A modul által a minősítési csapatunkhoz ellenőrizve. A modul megkapta a minősítést követően kap egy személyes hivatkozással, a modul tesztelési. Ha módosítja a tesztelés után van szüksége, az ajánlat metaadatainak szerkesztése, és küldje el újra a modult a minősítési csapatának. 

## <a name="publish-your-iot-edge-module"></a>Az IoT Edge-modul közzététele

Miután befejezte a tesztelés alatt, és közzétételre kész, válassza ki a **élő** közzététele az IoT Edge-modul.

>[!Important]
>Az IoT Edge-modul jelentettük be, az Ignite esemény szeretne használni, ha a modul kell nyilvános 09/23/2018 szerint.
