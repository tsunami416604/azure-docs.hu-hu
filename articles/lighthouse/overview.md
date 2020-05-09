---
title: Mi az az Azure Lighthouse?
description: Az Azure Lighthouse lehetővé teszi, hogy a szolgáltatók felügyelt szolgáltatásokat nyújtsanak ügyfeleiknek magasabb szintű automatizálással és hatékonysággal.
ms.date: 05/05/2020
ms.topic: overview
ms.openlocfilehash: bc54a6f67ed3d0ac194793baba4270d524edf5b1
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857131"
---
# <a name="what-is-azure-lighthouse"></a>Mi az az Azure Lighthouse?

Az Azure Lighthouse egyetlen vezérlőt kínál a szolgáltatók számára az Azure megtekintésére és kezelésére az összes ügyfeleik számára, magasabb szintű automatizálással, méretezéssel és fokozott irányítással. Az Azure Lighthouse használatával a szolgáltatók a felügyelt szolgáltatásokat az Azure platformon beépített átfogó és robusztus felügyeleti eszközökkel biztosíthatják. Ez az ajánlat olyan vállalati IT-szervezeteket is használhat, amelyek több bérlőn keresztül kezelik az erőforrásokat. 

![Az Azure Lighthouse áttekintési ábrája](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Előnyök

Az Azure Lighthouse segítségével nyereségesen és hatékonyan hozhat létre és kezelhet felügyelt szolgáltatásokat ügyfelei számára. Ez a következő előnyöket nyújtja:

- Nagy **léptékű felügyelet**: az ügyfelek bevonása és a felhasználói erőforrások kezelésének életciklusa egyszerűbbé és méretezhető. A meglévő API-k, felügyeleti eszközök és munkafolyamatok a delegált ügyfelek erőforrásaival is használhatók.
- **Nagyobb láthatóság és pontosság az ügyfelek számára**: az ügyfelek jobban megtekinthetik a műveleteiket, és pontosan szabályozzák a felügyeletre delegált hatókört, beleértve a hozzáférés teljes eltávolításának lehetőségét is, az IP-cím megőrzése mellett.
- **Átfogó és egységes platform-eszközök**: az eszközökön a legfontosabb szolgáltatói forgatókönyvek, többek között az EA, a CSP és az utólagos elszámolású licencelési modellek is elérhetők. Az új funkciók a meglévő eszközökkel, API-kkal, licencelési modellekkel és partneri programokkal, például a [felhőalapú megoldás-szolgáltatói programmal (CSP)](https://docs.microsoft.com/partner-center/csp-overview)működnek. Az Azure Lighthouse integrálható a meglévő munkafolyamataiba és alkalmazásaiba, és nyomon követheti az ügyfelek bevonásával kapcsolatos hatásokat a [partner azonosítójának összekapcsolásával](../billing/billing-partner-admin-link-started.md).

Az Azure Lighthouse használata nem jár további költségekkel az ügyfelek Azure-erőforrásainak kezeléséhez. Bármely Azure-ügyfél vagy-partner használhatja az Azure Lighthouse-t.

## <a name="capabilities"></a>Funkciók

Az Azure Lighthouse több módszert is kínál az ügyfelek részvételének és felügyeletének megkönnyítésére:

- **Azure-beli delegált erőforrás-kezelés**: a saját bérlőn belül biztonságosan kezelheti ügyfelei Azure-erőforrásait anélkül, hogy a kontextust és a vezérlési síkokat kellene váltania. Az előfizetések és az erőforráscsoportok delegálása a megadott felhasználók és szerepkörök számára a bérlő kezelése lehetőséggel lehetséges, hogy szükség esetén el kell távolítani a hozzáférést. További információ: Azure-beli [delegált erőforrás-kezelés](concepts/azure-delegated-resource-management.md).
- **Új Azure Portali tapasztalatok**: a több-bérlős adatok megtekintése a [Azure Portal](https://portal.azure.com)új **ügyfelek** lapján. A megfelelő **szolgáltatói** panel lehetővé teszi az ügyfelek számára a szolgáltatói hozzáférés megtekintését és kezelését. További információ: [ügyfelek megtekintése és kezelése](./how-to/view-manage-customers.md) , [szolgáltatók megtekintése és kezelése](how-to/view-manage-service-providers.md).
- **Azure Resource Manager sablonok**: könnyebben elvégezheti a felügyeleti feladatokat, beleértve az Azure-beli delegált erőforrás-kezeléshez szükséges ügyfelek bevezetését. További információkért tekintse meg a [minták](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) tárházát, és helyezze üzembe az [ügyfelet az Azure-beli delegált erőforrás-kezelésben](how-to/onboard-customer.md).
- **Felügyelt szolgáltatási ajánlatok az Azure Marketplace-** en: a szolgáltatásokat magán-vagy nyilvános ajánlatokon keresztül kínálja fel ügyfeleinek, és automatikusan előkészíti azokat az Azure-beli delegált erőforrás-kezelésre, mint a Azure Resource Manager-sablonok használatával történő bevezetést. További információ: [felügyelt szolgáltatások ajánlatai az Azure Marketplace-](concepts/managed-services-offers.md)en.

## <a name="next-steps"></a>További lépések

- Megismerheti az [Azure által delegált erőforrás-kezelés](concepts/azure-delegated-resource-management.md) részleteit.
- További információ a [bérlők közötti felügyeleti élményekről](concepts/cross-tenant-management-experience.md).
