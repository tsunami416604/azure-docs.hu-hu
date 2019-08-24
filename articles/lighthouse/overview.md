---
title: Mi az az Azure Lighthouse?
description: Az Azure Lighthouse lehetővé teszi, hogy a szolgáltatók felügyelt szolgáltatásokat nyújtsanak ügyfeleiknek magasabb szintű automatizálással és hatékonysággal.
author: JnHs
ms.author: jenhayes
ms.date: 08/22/2019
ms.topic: overview
ms.service: lighthouse
manager: carmonm
ms.openlocfilehash: 05fa16504e25a6bf0f6aa1c0a348284abba6e1ed
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011912"
---
# <a name="what-is-azure-lighthouse"></a>Mi az az Azure Lighthouse?

Az Azure Lighthouse egyetlen vezérlőt kínál a szolgáltatók számára az Azure megtekintésére és kezelésére az összes ügyfeleik számára, magasabb szintű automatizálással, méretezéssel és fokozott irányítással. Az Azure Lighthouse használatával a szolgáltatók a felügyelt szolgáltatásokat az Azure platformon beépített átfogó és robusztus felügyeleti eszközökkel biztosíthatják. Ez az ajánlat olyan vállalati IT-szervezeteket is használhat, amelyek több bérlőn keresztül kezelik az erőforrásokat.

![Az Azure Lighthouse áttekintési ábrája](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Előnyök

Az Azure Lighthouse segítségével nyereségesen és hatékonyan hozhat létre és kezelhet felügyelt szolgáltatásokat ügyfelei számára. Az előnyök a következők:

- **Felügyelet**nagy léptékben: Az ügyfelek bevonása és életciklus-műveletei egyszerűbbé és méretezhetők.
- **Nagyobb láthatóság és pontosság az ügyfelek számára**: Azok az ügyfelek, akiknek az Ön által kezelt erőforrásai jobban láthatóvá válnak a műveleteiben, és pontosan szabályozzák a felügyeletre delegált hatókört, az IP-címet pedig megőrzi.
- **Átfogó és egységes platform-eszközök**: Az eszközön a legfontosabb szolgáltatói forgatókönyvek, többek között az EA, a CSP és az utólagos elszámolású licencelési modellek is elérhetők. Az új funkciók a meglévő eszközökkel, API-kkal, licencelési modellekkel és partneri programokkal, például a [felhőalapú megoldás-szolgáltatói programmal (CSP)](https://docs.microsoft.com/partner-center/csp-overview)működnek. Az Azure Lighthouse kiválasztott lehetőségei a meglévő munkafolyamatokhoz és alkalmazásokhoz integrálhatók, és a [partner-azonosító](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started)összekapcsolásával nyomon követheti az ügyfelek bevonására gyakorolt hatást.

Az Azure Lighthouse használata nem jár további költségekkel az ügyfelek Azure-erőforrásainak kezeléséhez.

## <a name="capabilities"></a>Funkciók

Az Azure Lighthouse több módszert is kínál az ügyfelek részvételének és felügyeletének megkönnyítésére:

- **Azure**-beli delegált erőforrás-kezelés: A saját bérlőn belül biztonságosan kezelheti ügyfelei Azure-erőforrásait anélkül, hogy a kontextust és a vezérlési síkokat kellene váltania. További információ: Azure-beli [delegált erőforrás-kezelés](./concepts/azure-delegated-resource-management.md).
- **Új Azure Portal tapasztalatok**: A több-bérlős adatok megtekintése a [Azure Portal](https://portal.azure.com)új **ügyfelek** lapján. A megfelelő **szolgáltatói** panel lehetővé teszi az ügyfelek számára a szolgáltatói hozzáférés megtekintését és kezelését. További információ: [ügyfelek megtekintése és kezelése](./how-to/view-manage-customers.md) , [szolgáltatók megtekintése és kezelése](./how-to/view-manage-service-providers.md).
- **Azure Resource Manager sablonok**: A felügyeleti feladatok könnyebben elvégezhetők, beleértve az Azure-beli delegált erőforrás-kezeléshez szükséges ügyfelek bevezetését. További információkért tekintse meg a [minták](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) tárházát, és helyezze üzembe az [ügyfelet az Azure-beli delegált erőforrás](how-to/onboard-customer.md)-kezelésben.
- **Felügyelt szolgáltatások ajánlatai az Azure Marketplace-** en: Saját szolgáltatásokat biztosíthat ügyfeleinek magán-és nyilvános ajánlatokon keresztül, és automatikusan előkészítheti azokat az Azure-beli delegált erőforrás-kezeléshez, mint a Azure Resource Manager-sablonok használatával történő előkészítést. További információ: felügyelt [szolgáltatások ajánlatai az Azure Marketplace-](./concepts/managed-services-offers.md)en.
- **Azure által felügyelt alkalmazások**: Olyan alkalmazásokat csomagolhat és helyezhet üzembe, amelyek megkönnyítik az ügyfelek számára a saját előfizetésekben való üzembe helyezést és használatát. Az alkalmazás üzembe helyezése egy olyan erőforráscsoporthoz történik, amely a bérlőtől fér hozzá, így a szolgáltatás a teljes Azure Lighthouse-élmény részeként kezelhető. További információ: az [Azure által felügyelt alkalmazások áttekintése](https://docs.microsoft.com/azure/managed-applications/overview).

> [!NOTE]
> A fent ismertetett képességek jelenleg nyilvános felhőkben érhetők el. Az egyes szolgáltatások regionális elérhetőségét lásd: [régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="next-steps"></a>További lépések

- Megismerheti az [Azure által delegált erőforrás-kezelés](concepts/azure-delegated-resource-management.md) részleteit.
- További információ a [bérlők közötti felügyeleti élményekről](concepts/cross-tenant-management-experience.md).
