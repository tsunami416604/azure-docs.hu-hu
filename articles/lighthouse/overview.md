---
title: Mi az az Azure Lighthouse?
description: Az Azure Lighthouse lehetővé teszi, hogy a szolgáltatók felügyelt szolgáltatásokat nyújtsanak ügyfeleiknek magasabb szintű automatizálással és hatékonysággal.
ms.date: 10/19/2020
ms.topic: overview
ms.openlocfilehash: a76606ff48a09c0c31584882e3d2aa164ec97325
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203789"
---
# <a name="what-is-azure-lighthouse"></a>Mi az az Azure Lighthouse?

Az Azure Lighthouse lehetővé teszi a bérlők közötti és a több-bérlős felügyeletet, ami nagyobb fokú automatizálást, skálázhatóságot és szabályozást nyújt a bérlőkre és az erőforrásokra vonatkozóan.

Az Azure Lighthouse használatával a szolgáltatók a felügyelt szolgáltatásokat az Azure platformon beépített átfogó és robusztus felügyeleti eszközökkel biztosíthatják. Az ügyfelek szabályozzák, hogy ki férhet hozzá a bérlőhöz, milyen erőforrásokhoz férhet hozzá, és milyen műveleteket lehet elvégezni. Ez az ajánlat olyan [vállalati IT-szervezeteket](concepts/enterprise.md) is használhat, amelyek több bérlőn keresztül kezelik az erőforrásokat.

![Az Azure Lighthouse áttekintési ábrája](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Előnyök

Az Azure Lighthouse segítségével a szolgáltatók hatékonyan hozhatnak létre és kezelhetnek felügyelt szolgáltatásokat. Az előnyök:

- Nagy **léptékű felügyelet**: az ügyfelek bevonása és a felhasználói erőforrások kezelésének életciklusa egyszerűbbé és méretezhető. A meglévő API-k, felügyeleti eszközök és munkafolyamatok a delegált erőforrásokkal használhatók, beleértve az Azure-on kívül üzemeltetett gépeket is, függetlenül attól, hogy mely régiókban találhatók.
- **Nagyobb láthatóság és felügyelet ügyfeleink számára**: az ügyfelek pontosan szabályozzák a felügyeletre delegált hatóköröket, valamint az engedélyezett engedélyeket. Megtekinthetik a szolgáltatói műveleteket, és szükség esetén teljes mértékben eltávolíthatók a hozzáférések.
- **Átfogó és egységes platform-eszközök**: az eszközökön a legfontosabb szolgáltatói forgatókönyvek, többek között az EA, a CSP és az utólagos elszámolású licencelési modellek is elérhetők. Az Azure Lighthouse a meglévő eszközökkel és API-kkal, licencelési modellel, [Azure által felügyelt alkalmazásokkal](concepts/managed-applications.md)és partner programokkal, például a [Cloud Solution Provider program (CSP)](/partner-center/csp-overview)szolgáltatással működik. Integrálhatja az Azure Lighthouse-t a meglévő munkafolyamataiba és alkalmazásaiba, és nyomon követheti az ügyfelek bevonásával kapcsolatos hatásokat a [partner azonosítójának összekapcsolásával](./how-to/partner-earned-credit.md).

Az Azure világítótorony az Azure-erőforrások kezeléséhez való használata nem jár további költségekkel. Bármely Azure-ügyfél vagy-partner használhatja az Azure Lighthouse-t.

## <a name="capabilities"></a>Képességek

Az Azure Lighthouse több módszert is kínál az elkötelezettség és a felügyelet egyszerűsítéséhez:

- **Azure-beli delegált erőforrás-kezelés**: a [saját bérlőn belül biztonságosan kezelheti ügyfelei Azure-erőforrásait](concepts/azure-delegated-resource-management.md)anélkül, hogy a kontextust és a vezérlési síkokat kellene váltania. Az ügyfél-előfizetések és erőforráscsoportok delegálása a megadott felhasználókhoz és szerepkörökhöz a bérlő kezelése lehetőséggel lehetséges, hogy szükség esetén el kell távolítani a hozzáférést.
- **Új Azure Portal tapasztalatok**: a bérlői információk megtekintése a Azure Portal [ **saját ügyfelek** lapján](how-to/view-manage-customers.md) . A kapcsolódó [ **szolgáltatók** lapon](how-to/view-manage-service-providers.md) megtekintheti és kezelheti a szolgáltatói hozzáférését.
- **Azure Resource Manager sablonok**: az ARM-sablonokkal [delegált felhasználói erőforrásokat](how-to/onboard-customer.md) készíthet, és [több-bérlős felügyeleti feladatokat hajthat végre](samples/index.md).
- **Felügyelt szolgáltatási ajánlatok az Azure Marketplace-** en: [a szolgáltatásokat](concepts/managed-services-offers.md) privát vagy nyilvános ajánlatokon keresztül kínálja ügyfeleinek, és automatikusan bevezetheti őket az Azure világítótoronyba.

## <a name="next-steps"></a>További lépések

- Megismerheti az [Azure által delegált erőforrás-kezelés](concepts/azure-delegated-resource-management.md) részleteit.
- Fedezze fel a [bérlők közötti felügyeleti tapasztalatokat](concepts/cross-tenant-management-experience.md).
- Ismerje meg, hogyan [használhatja az Azure Lighthouse-t egy vállalaton belül](concepts/enterprise.md).
