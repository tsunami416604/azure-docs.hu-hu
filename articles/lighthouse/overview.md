---
title: Mi az az Azure Lighthouse?
description: Az Azure Lighthouse lehetővé teszi, hogy a szolgáltatók felügyelt szolgáltatásokat nyújtsanak ügyfeleiknek magasabb szintű automatizálással és hatékonysággal.
ms.date: 11/11/2019
ms.topic: overview
ms.openlocfilehash: 0bec1f5b727ca3ecd604d2654358ca9a5507abe4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646194"
---
# <a name="what-is-azure-lighthouse"></a>Mi az az Azure Lighthouse?

Az Azure Lighthouse egyetlen vezérlőt kínál a szolgáltatók számára az Azure megtekintésére és kezelésére az összes ügyfeleik számára, magasabb szintű automatizálással, méretezéssel és fokozott irányítással. Az Azure Lighthouse használatával a szolgáltatók a felügyelt szolgáltatásokat az Azure platformon beépített átfogó és robusztus felügyeleti eszközökkel biztosíthatják. Ez az ajánlat olyan vállalati IT-szervezeteket is használhat, amelyek több bérlőn keresztül kezelik az erőforrásokat.

![Az Azure Lighthouse áttekintési ábrája](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Előnyök

Az Azure Lighthouse segítségével nyereségesen és hatékonyan hozhat létre és kezelhet felügyelt szolgáltatásokat ügyfelei számára. Ez a következő előnyöket nyújtja:

- Nagy **léptékű felügyelet**: az ügyfelek bevonása és a felhasználói erőforrások kezelésének életciklusa egyszerűbbé és méretezhető.
- **Nagyobb láthatóság és pontosság az ügyfelek számára**: a felügyelt erőforrásokkal rendelkező ügyfelek jobban megtekinthetik a műveleteiket, és pontosan szabályozzák a felügyeletre delegált hatókört, az IP-címet pedig megőrzi.
- **Átfogó és egységes platform-eszközök**: az eszközökön a legfontosabb szolgáltatói forgatókönyvek, többek között az EA, a CSP és az utólagos elszámolású licencelési modellek is elérhetők. Az új funkciók a meglévő eszközökkel, API-kkal, licencelési modellekkel és partneri programokkal, például a [felhőalapú megoldás-szolgáltatói programmal (CSP)](https://docs.microsoft.com/partner-center/csp-overview)működnek. Az Azure Lighthouse kiválasztott lehetőségei a meglévő munkafolyamatokhoz és alkalmazásokhoz integrálhatók, és a [partner-azonosító összekapcsolásával](../billing/billing-partner-admin-link-started.md)nyomon követheti az ügyfelek bevonására gyakorolt hatást.

Az Azure Lighthouse használata nem jár további költségekkel az ügyfelek Azure-erőforrásainak kezeléséhez.

## <a name="capabilities"></a>Képességek

Az Azure Lighthouse több módszert is kínál az ügyfelek részvételének és felügyeletének megkönnyítésére:

- **Azure-beli delegált erőforrás-kezelés**: a saját bérlőn belül biztonságosan kezelheti ügyfelei Azure-erőforrásait anélkül, hogy a kontextust és a vezérlési síkokat kellene váltania. További információ: Azure-beli [delegált erőforrás-kezelés](concepts/azure-delegated-resource-management.md).
- **Új Azure Portali tapasztalatok**: a több-bérlős adatok megtekintése a [Azure Portal](https://portal.azure.com)új **ügyfelek** lapján. A megfelelő **szolgáltatói** panel lehetővé teszi az ügyfelek számára a szolgáltatói hozzáférés megtekintését és kezelését. További információ: [ügyfelek megtekintése és kezelése](./how-to/view-manage-customers.md) , [szolgáltatók megtekintése és kezelése](how-to/view-manage-service-providers.md).
- **Azure Resource Manager sablonok**: könnyebben elvégezheti a felügyeleti feladatokat, beleértve az Azure-beli delegált erőforrás-kezeléshez szükséges ügyfelek bevezetését. További információkért tekintse meg a [minták](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) tárházát, és helyezze üzembe az [ügyfelet az Azure-beli delegált erőforrás-kezelésben](how-to/onboard-customer.md).
- **Felügyelt szolgáltatások ajánlatai az Azure Marketplace-** en: magán-vagy nyilvános ajánlatokon keresztül kínál szolgáltatásokat ügyfeleinek, és automatikusan előkészíti azokat az Azure-beli delegált erőforrás-kezelésre, amely a Azure Resource Manager-sablonok használatával történő bevezetéshez is használható. További információ: [felügyelt szolgáltatások ajánlatai az Azure Marketplace-](concepts/managed-services-offers.md)en.
- **Azure által felügyelt alkalmazások**: azok az alkalmazások, amelyek megkönnyítik az ügyfelek számára a saját előfizetésekben való üzembe helyezését és használatát. Az alkalmazás üzembe helyezése egy olyan erőforráscsoporthoz történik, amely a bérlőtől fér hozzá, így a szolgáltatás a teljes Azure Lighthouse-élmény részeként kezelhető. További információ: az [Azure által felügyelt alkalmazások áttekintése](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Következő lépések

- Megismerheti az [Azure által delegált erőforrás-kezelés](concepts/azure-delegated-resource-management.md) részleteit.
- További információ a [bérlők közötti felügyeleti élményekről](concepts/cross-tenant-management-experience.md).
