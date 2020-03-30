---
title: Mi az az Azure Lighthouse?
description: Az Azure Lighthouse lehetővé teszi a szolgáltatók számára, hogy nagyobb automatizálással és hatékonysággal nyújtsanak felügyelt szolgáltatásokat ügyfeleiknek.
ms.date: 11/11/2019
ms.topic: overview
ms.openlocfilehash: 0bec1f5b727ca3ecd604d2654358ca9a5507abe4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75646194"
---
# <a name="what-is-azure-lighthouse"></a>Mi az az Azure Lighthouse?

Az Azure Lighthouse egyetlen vezérlősíkot kínál a szolgáltatóknak, hogy magasabb automatizálással, méretezéssel és fokozott irányítással tekinthessék és kezelhessék az Azure-t az összes ügyféllel. Az Azure Lighthouse segítségével a szolgáltatók az Azure platformba épített átfogó és robusztus felügyeleti eszközök kelve is biztosíthatnak felügyelt szolgáltatásokat. Ez az ajánlat is előnyös vállalati informatikai szervezetek erőforrások kezelése több bérlő között.

![Az Azure világítótorony áttekintő diagramja](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Előnyök

Az Azure Lighthouse segítségével nyereségesen és hatékonyan hozhat létre és biztosíthat felügyelt szolgáltatásokat ügyfelei számára. Ez a következő előnyöket nyújtja:

- **Nagy léptékű kezelés**: Az ügyfelek erőforrásainak kezeléséhez szükséges ügyfél-elköteleződés idoszakos és életciklus-műveletek egyszerűbbek és méretezhetőbbek.
- **Nagyobb láthatóság és pontosság az ügyfelek számára:** Azok az ügyfelek, akiknek az ön által felügyelt erőforrásait kezelik, jobban átláthatják a műveleteket, és pontosan szabályozhatják a felügyelethez delegált hatókört, miközben az IP-cím megmarad.
- **Átfogó és egységes platformeszközök**: Eszközeszköz-felületünk a legfontosabb szolgáltatói forgatókönyvekkel foglalkozik, beleértve több licencelési modellt, például az EA-t, a csp-t és a felosztó-kirovást. Az új funkciók meglévő eszközökkel és API-kkal, licencelési modellekkel és partnerprogramokkal, például a [Felhőszolgáltató programmal (CSP) működnek.](https://docs.microsoft.com/partner-center/csp-overview) Az Azure Világítótorony által választott beállítások integrálhatók a meglévő munkafolyamatokba és alkalmazásokba, és a [partnerazonosító összekapcsolásával](../billing/billing-partner-admin-link-started.md)nyomon követheti az ügyfél-elköteleződésre gyakorolt hatását.

Az Azure Lighthouse azure-beli erőforrások kezeléséhez nem jár további költségek.

## <a name="capabilities"></a>Funkciók

Az Azure Világítótorony többféle módon is egyszerűsítheti az ügyfelek elkötelezettségét és felügyeletét:

- **Azure delegált erőforrás-kezelés:** Kezelje az ügyfelek Azure-erőforrások biztonságosan a saját bérlő, anélkül, hogy váltani környezetés a repülőgépek vezérlése. További információ: [Azure delegált erőforrás-kezelés.](concepts/azure-delegated-resource-management.md)
- **Új Azure-portál-élmények**: A bérlők közötti adatok megtekintése az Azure Portal új **Ügyfeleik** [lapján.](https://portal.azure.com) A megfelelő **Szolgáltatók** panelen az ügyfelek megtekinthetik és kezelhetik a szolgáltató hozzáférését. További információt az [Ügyfelek megtekintése és kezelése,](./how-to/view-manage-customers.md) valamint a szolgáltatók megtekintése és kezelése című témakörben [talál.](how-to/view-manage-service-providers.md)
- **Azure Resource Manager-sablonok:** Egyszerűbben hajthat el felügyeleti feladatokat, beleértve az Azure delegált erőforrás-kezeléséhez felelős ügyfeleket is. További információkért tekintse meg [a mintatárés](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) [az Azure delegált erőforrás-kezelése vevőink fedélzeti.](how-to/onboard-customer.md)
- **Felügyelt szolgáltatások kínál az Azure Marketplace-en:** Kínáljon szolgáltatásokat az ügyfelek nek magán- vagy nyilvános ajánlatok, és azokat automatikusan bekell ruházni az Azure delegált erőforrás-kezelés, mint egy másik a bevezetés az Azure Resource Manager-sablonok használatával. További információ: [Felügyelt szolgáltatások ajánlatai az Azure Marketplace-en.](concepts/managed-services-offers.md)
- **Azure által felügyelt alkalmazások:** Olyan csomagokat és alkalmazásokat szállít, amelyeket az ügyfelek egyszerűen telepíthetnek és használhatnak saját előfizetéseikben. Az alkalmazás üzembe helyezése egy erőforrás-csoportba, amely a bérlő, lehetővé téve a szolgáltatás kezelése részeként a teljes Azure Lighthouse-élmény. További információt az [Azure által felügyelt alkalmazások áttekintése című témakörben talál.](../azure-resource-manager/managed-applications/overview.md)

## <a name="next-steps"></a>További lépések

- Megismerheti az [Azure által delegált erőforrás-kezelés](concepts/azure-delegated-resource-management.md) részleteit.
- További információ a [bérlők közötti felügyeleti élményekről.](concepts/cross-tenant-management-experience.md)
