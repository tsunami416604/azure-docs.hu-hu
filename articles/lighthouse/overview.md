---
title: Mi az Azure Lighthouse?
description: Az Azure Lighthouse lehetővé teszi, hogy a szolgáltatók felügyelt szolgáltatásokat nyújtsanak ügyfeleiknek magasabb szintű automatizálással és hatékonysággal.
ms.date: 08/19/2020
ms.topic: overview
ms.openlocfilehash: d21d11d1316b177f38b6bce197dec4f2c3d3fac8
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660733"
---
# <a name="what-is-azure-lighthouse"></a>Mi az Azure Lighthouse?

Az Azure Lighthouse lehetővé teszi a több-bérlős felügyeletet, amely lehetővé teszi az erőforrások és a bérlők jobb automatizálását, méretezhetőségét és hatékonyabb irányítását. Az Azure Lighthouse használatával a szolgáltatók a felügyelt szolgáltatásokat az Azure platformon beépített átfogó és robusztus felügyeleti eszközökkel biztosíthatják. Ez az ajánlat olyan vállalati IT-szervezeteket is használhat, amelyek több bérlőn keresztül kezelik az erőforrásokat.

![Az Azure Lighthouse áttekintési ábrája](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Előnyök

Az Azure Lighthouse segítségével nyereségesen és hatékonyan hozhat létre és kezelhet felügyelt szolgáltatásokat. Ez a következő előnyöket nyújtja:

- Nagy **léptékű felügyelet**: az ügyfelek bevonása és a felhasználói erőforrások kezelésének életciklusa egyszerűbbé és méretezhető. A meglévő API-k, felügyeleti eszközök és munkafolyamatok a delegált erőforrásokkal is használhatók, függetlenül attól, hogy mely régiókban találhatók.
- **Nagyobb láthatóság és pontosság az ügyfelek számára**: az ügyfelek jobban megtekinthetik a műveleteiket, és pontosan szabályozzák a felügyeletre delegált hatókört, beleértve a hozzáférés teljes eltávolításának lehetőségét is, az IP-cím megőrzése mellett.
- **Átfogó és egységes platform-eszközök**: az eszközökön a legfontosabb szolgáltatói forgatókönyvek, többek között az EA, a CSP és az utólagos elszámolású licencelési modellek is elérhetők. Az új funkciók a meglévő eszközökkel, API-kkal, licencelési modellekkel és partneri programokkal, például a [felhőalapú megoldás-szolgáltatói programmal (CSP)](/partner-center/csp-overview)működnek. Az Azure Lighthouse integrálható a meglévő munkafolyamataiba és alkalmazásaiba, és nyomon követheti az ügyfelek bevonásával kapcsolatos hatásokat a [partner azonosítójának összekapcsolásával](../cost-management-billing/manage/link-partner-id.md).

Az Azure világítótorony az Azure-erőforrások kezeléséhez való használata nem jár további költségekkel. Bármely Azure-ügyfél vagy-partner használhatja az Azure Lighthouse-t.

## <a name="capabilities"></a>Képességek

Az Azure Lighthouse több módszert is kínál az elkötelezettség és a felügyelet egyszerűsítéséhez:

- **Azure-beli delegált erőforrás-kezelés**: a saját bérlőn belül biztonságosan kezelheti ügyfelei Azure-erőforrásait anélkül, hogy a kontextust és a vezérlési síkokat kellene váltania. Az előfizetések és az erőforráscsoportok delegálása a megadott felhasználók és szerepkörök számára a bérlő kezelése lehetőséggel lehetséges, hogy szükség esetén el kell távolítani a hozzáférést. További információ: Azure-beli [delegált erőforrás-kezelés](concepts/azure-delegated-resource-management.md).
- **Új Azure Portal tapasztalatok**: a bérlői információk megtekintése a Azure Portal [ **saját ügyfelek** lapján](./how-to/view-manage-customers.md) . A kapcsolódó [ **szolgáltatók** lapon](how-to/view-manage-service-providers.md) megtekintheti és kezelheti a szolgáltatói hozzáférését.
- **Azure Resource Manager sablonok**: a sablonok segítenek a több-bérlős felügyeleti feladatok elvégzésében és a delegált felhasználói erőforrások bevezetésében. További információkért tekintse meg a [minták](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) tárházát, és az ügyfél bevezetését [Az Azure világítótoronyba](how-to/onboard-customer.md).
- **Felügyelt szolgáltatási ajánlatok az Azure Marketplace-** en: a szolgáltatásokat privát vagy nyilvános ajánlatokon keresztül kínálja ügyfeleinek, és automatikusan bekerülnek az Azure világítótoronyba. További információ: [felügyelt szolgáltatások ajánlatai az Azure Marketplace-](concepts/managed-services-offers.md)en.

## <a name="next-steps"></a>További lépések

- Megismerheti az [Azure által delegált erőforrás-kezelés](concepts/azure-delegated-resource-management.md) részleteit.
- Fedezze fel a [bérlők közötti felügyeleti tapasztalatokat](concepts/cross-tenant-management-experience.md).
- Ismerje meg, hogyan [használhatja az Azure Lighthouse-t egy vállalaton belül](concepts/enterprise.md).
