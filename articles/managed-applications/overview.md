---
title: Az Azure Managed Applications áttekintése | Microsoft Docs
description: Az Azure Managed Applications fogalmainak ismertetése
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.date: 07/11/2018
ms.author: tomfitz
ms.openlocfilehash: 628a936d85eb94a1ee332205047527b0f9795d50
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990514"
---
# <a name="azure-managed-applications-overview"></a>Az Azure Managed Applications áttekintése

Az Azure Managed Applications segítségével az ügyfelek által könnyedén üzembe helyezhető és üzemeltethető felhőalapú megoldások tehetők elérhetővé. Az Ön feladata az infrastruktúra implementálása és a folyamatos támogatás biztosítása. Ha egy felügyelt alkalmazást minden ügyfél számára elérhetővé kíván tenni, tegye közzé az Azure Marketplace-en. Ha csak a saját vállalatán belüli felhasználók számára kívánja elérhetővé tenni, tegye közzé egy belső katalógusban. 

A felügyelt alkalmazások sokban hasonlítanak a Marketplace megoldássablonjaihoz, egy fontos különbséget leszámítva. A felügyelt alkalmazások esetében az erőforrások létrehozása egy erőforráscsoportban történik, amelyet az alkalmazás közzétevője felügyel. Az erőforráscsoport az ügyfél előfizetésében található meg, de a közzétevő bérlőjének egy identitása rendelkezik hozzáféréssel az erőforráscsoporthoz. A megoldás folyamatos támogatásának költségeit a közzétevő határozza meg.

## <a name="advantages-of-managed-applications"></a>A felügyelt alkalmazások előnyei

A felügyelt alkalmazások csökkentik a megoldások ügyfeleire vonatkozó korlátozásokat. A megoldásoknak nem alapfeltétele a felhőinfrastruktúrák használatában való jártasság. Az ügyfelek korlátozott hozzáféréssel rendelkeznek a kritikus erőforrásokhoz. Nem kell aggódniuk a felügyelet során vétett esetleges hibák miatt. 

A felügyelt alkalmazásokkal folyamatos kapcsolatot tarthat fenn az ügyfelekkel. Ön határozhatja meg az alkalmazás felügyeletének feltételeit, a díjak kifizetése pedig az Azure számlázási rendszerén keresztül történik.

Habár az ügyfelek saját maguk telepítik az előfizetésükhöz tartozó felügyelt alkalmazásokat, de a karbantartással, frissítéssel és javítással nem kell foglalkozniuk. Garantálható, hogy mindig minden ügyfél a jóváhagyott verziót használja. Az ügyfeleknek nem kell alkalmazásspecifikus tartományi ismeretekkel rendelkezniük az alkalmazások felügyeletéhez. Az ügyfelek automatikusan megkapják az alkalmazások frissítéseit, és nem kell foglalkozniuk az esetleges hibák keresésével vagy diagnosztizálásával. 

A felügyelt alkalmazások lehetővé teszik, hogy az informatikai részleg előre jóváhagyott megoldásokat nyújtson a vállalat felhasználói számára. Ezáltal biztosítható, hogy a megoldások megfeleljenek a vállalati szabványoknak.

## <a name="types-of-managed-applications"></a>A felügyelt alkalmazások típusai

A felügyelt alkalmazások közzétehetők külsőleg vagy belsőleg.

![Külső vagy belső közzététel](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Szolgáltatáskatalógus

A szolgáltatáskatalógus egy belső katalógus azokról a megoldásokról, amelyeket egy vállalat jóváhagy a felhasználói számára. A katalógus segítségével biztosítható, hogy a vállalat számára elérhető megoldások megfeleljenek bizonyos vállalati szabványoknak. Az alkalmazottak a katalógussal könnyen felfedezhetik az informatikai részleg által jóváhagyott és ajánlott alkalmazásokat. Láthatják, hogy a vállalat más dolgozói milyen felügyelt alkalmazásokat osztottak meg velük.

A szolgáltatáskatalógusban elérhető felügyelt alkalmazások közzétételével kapcsolatban lásd a [szolgáltatáskatalógusban elérhető alkalmazások létrehozását](publish-service-catalog-app.md) ismertető témakört.

### <a name="marketplace"></a>Piactér

A szolgáltatásaikat értékesíteni kívánó szállítók a felügyelt alkalmazásokat elérhetővé tehetik az Azure Marketplace-en. Miután a szállító közzétesz egy alkalmazást, az elérhetővé válik a vállalaton kívüli felhasználók számára. Ezzel a módszerrel a felügyelt szolgáltatások szolgáltatói (MSP-k), a független szoftverszállítók (ISV-k) és a rendszerintegrátorok (SI-k) minden Azure-ügyfél számára felkínálhatják a megoldásaikat.

A felügyelt alkalmazások Azure Marketplace-en való közzétételével kapcsolatban lásd a [Marketplace-alkalmazás létrehozását](publish-marketplace-app.md) ismertető témakört.

## <a name="resource-groups-for-managed-applications"></a>Felügyelt alkalmazások erőforráscsoportjai

A felügyelt alkalmazások erőforrásai általában két erőforráscsoportban találhatók. Ezek közül egyet az ügyfél kezel, a másikat pedig a közzétevő felügyeli. A felügyelt alkalmazás meghatározásakor a közzétevő határozza meg a hozzáférési szinteket. Az [adatműveletekhez](../role-based-access-control/role-definitions.md) való hozzáférés korlátozása jelenleg nem támogatott az Azure összes adatszolgáltatójánál.

Az alábbi képen egy olyan forgatókönyv látható, ahol a közzétevő a felügyelt erőforráscsoport tulajdonosi szerepkörét kéri. A közzétevő az ügyfél számára csak olvashatóvá tette az erőforráscsoportot. A kezelt erőforráscsoporthoz hozzáféréssel rendelkező közzétevői identitások mentesítve vannak a zárolás alól.

![Hozzáférés az erőforráscsoporthoz](./media/overview/access.png)

### <a name="application-resource-group"></a>Alkalmazás erőforráscsoportja

Ez az erőforrás tartalmazza a felügyelt alkalmazás példányát. Ez az erőforrás csak egy erőforrást tartalmazhat. A felügyelt alkalmazás erőforrástípusa: **Microsoft.Solutions/applications**.

Az ügyfél teljes hozzáféréssel rendelkezik az erőforráscsoporthoz, így kezelni tudja a felügyelt alkalmazás életciklusát.

### <a name="managed-resource-group"></a>Felügyelt erőforráscsoportok

Ez az erőforráscsoport a felügyelt alkalmazáshoz szükséges összes erőforrást tartalmazza. Ez az erőforrás például a megoldáshoz szükséges virtuális gépeket, tárfiókokat és virtuális hálózatokat is tartalmazza. Az ügyfél korlátozott hozzáféréssel rendelkezik ehhez az erőforráscsoporthoz, mivel nem az ügyfél kezeli a felügyelt alkalmazás különálló erőforrásait. A közzétevő hozzáférése az erőforráscsoporthoz megfelel a felügyelt alkalmazás definíciójában megadott szerepkörnek. Például a közzétevő kérheti a tulajdonosi vagy közreműködői szerepkört az erőforráscsoporthoz.

Ha az ügyfél törli a felügyelt alkalmazást, az erőforráscsoport is törlődik.

## <a name="azure-policy"></a>Azure Policy

A felügyelt alkalmazásra [Azure-szabályzatot](../azure-policy/azure-policy-introduction.md) alkalmazhat. Szabályzatokat alkalmazhat annak érdekében, hogy a felügyelt alkalmazás üzembe helyezett példányai megfeleljenek a biztonsági és adatkövetelményeknek. Ha az alkalmazás bizalmas adatokkal lép interakcióba, mindenképpen értékelje ki, hogyan kell védeni az adatokat. Ha például az alkalmazás az Office 365-ből származó adatokat használ, alkalmazzon egy szabályzatot az adattitkosítás engedélyezése érdekében.

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazások meghatározásával és üzembe helyezésével kapcsolatban lásd az [Azure-beli felügyelt alkalmazások az Azure CLI-vel történő létrehozását és üzembe helyezését](managed-apps-quickstart-cli.md) ismertető témakört.
* A belső alkalmazások közzétételével kapcsolatban lásd a [szolgáltatáskatalógusban elérhető alkalmazások létrehozását](publish-service-catalog-app.md) ismertető témakört.
* A felügyelt alkalmazások Marketplace-en való közzétételével kapcsolatos tudnivalókért tekintse meg a [Marketplace-alkalmazás létrehozásával kapcsolatos](publish-marketplace-app.md) témakört.
