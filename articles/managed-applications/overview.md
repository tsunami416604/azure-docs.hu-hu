---
title: Az Azure Managed Applications áttekintése | Microsoft Docs
description: Az Azure Managed Applications fogalmainak ismertetése
author: tfitzmac
ms.service: managed-applications
ms.topic: overview
ms.date: 05/31/2019
ms.author: tomfitz
ms.openlocfilehash: 5b6cb030c6eba5d80dfd046f1c3950609da1ed73
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479820"
---
# <a name="azure-managed-applications-overview"></a>Az Azure Managed Applications áttekintése

Az Azure Managed Applications segítségével az ügyfelek által könnyedén üzembe helyezhető és üzemeltethető felhőalapú megoldások tehetők elérhetővé. Az Ön feladata az infrastruktúra implementálása és a folyamatos támogatás biztosítása. Ha egy felügyelt alkalmazást minden ügyfél számára elérhetővé kíván tenni, tegye közzé az Azure Marketplace-en. Ha csak a saját vállalatán belüli felhasználók számára kívánja elérhetővé tenni, tegye közzé egy belső katalógusban. 

A felügyelt alkalmazások sokban hasonlítanak a Marketplace megoldássablonjaihoz, egy fontos különbséget leszámítva. A felügyelt alkalmazások esetében az erőforrások üzembe helyezése egy erőforráscsoportban történik, amelyet az alkalmazás közzétevője felügyel. Az erőforráscsoport az ügyfél előfizetésében található meg, de a közzétevő bérlőjének egy identitása rendelkezik hozzáféréssel az erőforráscsoporthoz. A megoldás folyamatos támogatásának költségeit a közzétevő határozza meg.

## <a name="advantages-of-managed-applications"></a>A felügyelt alkalmazások előnyei

A felügyelt alkalmazások csökkentik a megoldások ügyfeleire vonatkozó korlátozásokat. A megoldásoknak nem alapfeltétele a felhőinfrastruktúrák használatában való jártasság. Az ügyfelek korlátozott hozzáféréssel rendelkeznek a kritikus erőforrásokhoz, ezért nem kell aggódniuk a használatuk során vétett esetleges hibák miatt. 

A felügyelt alkalmazásokkal folyamatos kapcsolatot tarthat fenn az ügyfelekkel. Ön határozhatja meg az alkalmazás felügyeletének feltételeit, a díjak kifizetése pedig az Azure számlázási rendszerén keresztül történik.

Habár az ügyfelek saját maguk telepítik az előfizetésükhöz tartozó felügyelt alkalmazásokat, de a karbantartással, frissítéssel és javítással nem kell foglalkozniuk. Garantálható, hogy mindig minden ügyfél a jóváhagyott verziót használja. Az ügyfeleknek nem kell alkalmazásspecifikus tartományi ismeretekkel rendelkezniük az alkalmazások felügyeletéhez. Az ügyfelek automatikusan megkapják az alkalmazások frissítéseit, és nem kell foglalkozniuk az esetleges hibák keresésével vagy diagnosztizálásával. 

A felügyelt alkalmazások lehetővé teszik, hogy az informatikai részleg előre jóváhagyott megoldásokat nyújtson a vállalat felhasználói számára. Így biztos lehet abban, hogy a megoldások megfelelnek a vállalati szabványoknak.

Felügyelt alkalmazások támogatási [felügyelt identitások az Azure-erőforrások](./publish-managed-identity.md).

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

Egy felügyelt alkalmazás az erőforrásokat általában a két erőforráscsoport. Ezek közül egyet az ügyfél kezel, a másikat pedig a közzétevő felügyeli. A felügyelt alkalmazás meghatározásakor a közzétevő határozza meg a hozzáférési szinteket. A közzétevő is kérhető az vagy egy állandó szerepkör-hozzárendelés, vagy [igény szerinti elérése](request-just-in-time-access.md) , amely egy adott időszakban je omezeno hozzárendelés.

Az [adatműveletekhez](../role-based-access-control/role-definitions.md) való hozzáférés korlátozása jelenleg nem támogatott az Azure összes adatszolgáltatójánál.

Az alábbi képen egy olyan forgatókönyv látható, ahol a közzétevő a felügyelt erőforráscsoport tulajdonosi szerepkörét kéri. A közzétevő az ügyfél számára csak olvashatóvá tette az erőforráscsoportot. A kezelt erőforráscsoporthoz hozzáféréssel rendelkező közzétevői identitások mentesítve vannak a zárolás alól.

![Hozzáférés az erőforráscsoporthoz](./media/overview/access.png)

### <a name="application-resource-group"></a>Alkalmazás erőforráscsoportja

Ez az erőforrás tartalmazza a felügyelt alkalmazás példányát. Ez az erőforrás csak egy erőforrást tartalmazhat. A felügyelt alkalmazás erőforrástípusa: **Microsoft.Solutions/applications**.

Az ügyfél teljes hozzáféréssel rendelkezik az erőforráscsoporthoz, így kezelni tudja a felügyelt alkalmazás életciklusát.

### <a name="managed-resource-group"></a>Felügyelt erőforráscsoportok

Ez az erőforráscsoport a felügyelt alkalmazáshoz szükséges összes erőforrást tartalmazza. Ez az erőforrás például a megoldáshoz szükséges virtuális gépeket, tárfiókokat és virtuális hálózatokat is tartalmazza. Az ügyfél korlátozott hozzáféréssel rendelkezik ehhez az erőforráscsoporthoz, mivel nem az ügyfél kezeli a felügyelt alkalmazás különálló erőforrásait. A közzétevő hozzáférése az erőforráscsoporthoz megfelel a felügyelt alkalmazás definíciójában megadott szerepkörnek. Például a közzétevő kérheti a tulajdonosi vagy közreműködői szerepkört az erőforráscsoporthoz. Állandó, vagy csak egy adott időpont.

Ha az ügyfél törli a felügyelt alkalmazást, az erőforráscsoport is törlődik.

## <a name="azure-policy"></a>Azure Policy

A felügyelt alkalmazásra [Azure-szabályzatot](../governance/policy/overview.md) alkalmazhat. Szabályzatokat alkalmazhat annak érdekében, hogy a felügyelt alkalmazás üzembe helyezett példányai megfeleljenek a biztonsági és adatkövetelményeknek. Ha az alkalmazás bizalmas adatokkal lép interakcióba, mindenképpen értékelje ki, hogyan kell védeni az adatokat. Ha például az alkalmazás az Office 365-ből származó adatokat használ, alkalmazzon egy szabályzatot az adattitkosítás engedélyezése érdekében.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett a felügyelt alkalmazások használatának előnyeivel. A következő cikkben egy felügyelt alkalmazás definícióját hozza létre.

> [!div class="nextstepaction"]
> [Rövid útmutató: Azure-beli közzététel felügyeltalkalmazás-definíció](publish-managed-app-definition-quickstart.md)
