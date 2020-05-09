---
title: Azure Lighthouse és Azure Managed Applications
description: Az Azure Lighthouse és az Azure által felügyelt alkalmazások...
ms.date: 05/01/2020
ms.topic: conceptual
ms.openlocfilehash: d9923d31e78675927b4ca235607b2a61b24ccc41
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783629"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse és Azure Managed Applications

Az Azure Managed Applications és az Azure Lighthouse is működik azáltal, hogy lehetővé teszi a szolgáltató számára az ügyfél bérlője által használt erőforrások elérését. Hasznos lehet megérteni a különbségeket a működésük módjában, valamint azokat a forgatókönyveket, amelyekkel a szolgáltatás lehetővé teszi, valamint hogy hogyan használhatók együtt.

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Az Azure Lighthouse és az Azure által felügyelt alkalmazások összehasonlítása

### <a name="azure-lighthouse"></a>Azure Lighthouse

Az [Azure Lighthouse](../overview.md)használatával a szolgáltatók számos felügyeleti feladatot végezhetnek el közvetlenül az ügyfél előfizetésén (vagy erőforráscsoporthoz). Ez a hozzáférés egy logikai kivetítésen keresztül érhető el, amely lehetővé teszi a szolgáltatók számára, hogy bejelentkezzenek a saját bérlőbe, és hozzáférhessenek az ügyfél bérlőhöz tartozó erőforrásokhoz. Az ügyfél meghatározhatja, hogy mely előfizetések vagy erőforráscsoportok legyenek delegálva a szolgáltatónak, és az ügyfél teljes hozzáférést tart fenn ezekhez az erőforrásokhoz. A szolgáltató hozzáférését bármikor el is távolíthatja.

Az Azure Lighthouse használatához az Azure-beli erőforrás- [kezelést](azure-delegated-resource-management.md) az ARM-sablonok vagy egy [felügyelt szolgáltatási ajánlat Azure Marketplace-](managed-services-offers.md)en való [üzembe helyezésével](../how-to/onboard-customer.md) lehet előkészíteni. A [partnerek azonosítójának összekapcsolásával](../../cost-management-billing/manage/link-partner-id.md)nyomon követheti az ügyfelek bevonásának hatásait.

Az Azure világítótorony általában akkor használatos, ha egy szolgáltató folyamatosan végzi a kezelési feladatokat az ügyfél számára.

### <a name="azure-managed-applications"></a>Azure felügyelt alkalmazások

Az [Azure által felügyelt alkalmazások](../../azure-resource-manager/managed-applications/overview.md) lehetővé teszik, hogy a szolgáltató vagy az ISV olyan felhőalapú megoldásokat nyújtson, amelyek megkönnyítik az ügyfelek számára a saját előfizetésekben való üzembe helyezést és használatát.

A felügyelt alkalmazásokban az alkalmazás által használt erőforrások össze vannak csomagolva és a közzétevő által felügyelt erőforráscsoporthoz vannak telepítve. Ez az erőforráscsoport szerepel az ügyfél előfizetésében, de a közzétevő bérlője rendelkezik hozzáféréssel a tulajdonoshoz. Az ISV továbbra is felügyeli és karbantartja a felügyelt alkalmazást, míg az ügyfél nem rendelkezik közvetlen hozzáféréssel az erőforráscsoporthoz való munkavégzéshez vagy az erőforrásaihoz való hozzáféréshez.

A felügyelt alkalmazások támogatják a [testreszabott Azure Portal élményt](../../azure-resource-manager/managed-applications/concepts-view-definition.md) és [az egyéni szolgáltatókkal való integrációt](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md). Ezekkel a lehetőségekkel testre szabható és integrált felhasználói élményt biztosíthat, így az ügyfelek könnyebben végezhetnek el felügyeleti feladatokat.

A felügyelt alkalmazások [közzétehető az Azure Marketplace](../../azure-resource-manager/managed-applications/publish-marketplace-app.md)-en, akár privát ajánlatként, akár egy adott ügyfél általi használatra, akár olyan nyilvános ajánlatokra is, amelyeket több ügyfél is vásárolhat. A szervezeten belüli felhasználók számára is elérhetők, ha [felügyelt alkalmazásokat tesznek közzé a szolgáltatás katalógusában](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md). A Service Catalog és a piactér példányait ARM-sablonok használatával is üzembe helyezheti, amely tartalmazhat egy kereskedelmi Piactéri partner egyedi azonosítóját, amellyel nyomon követheti az [ügyfelek használati](../../marketplace/azure-partner-customer-usage-attribution.md)feladatait.

Az Azure által felügyelt alkalmazások jellemzően egy adott ügyfélhez szükségesek, amely a szolgáltató által teljes körűen felügyelt kulcsrakész megoldáson keresztül érhető el.

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Az Azure Lighthouse és az Azure által felügyelt alkalmazások együttes használata

Habár az Azure Lighthouse és az Azure által felügyelt alkalmazások különböző hozzáférési mechanizmusokat használnak különböző célok eléréséhez, előfordulhatnak olyan helyzetek, amikor a szolgáltatónak érdemes mindkét ügyfelet ugyanazzal az ügyféllel használni.

Előfordulhat például, hogy egy ügyfél a szolgáltató által az Azure Világítótoronyon keresztül szállított felügyelt szolgáltatásokat kívánja használni, így a partner által feltett műveletekkel és a delegált előfizetésük folyamatos felügyeletével is betekintést nyerhet. Előfordulhat azonban, hogy a szolgáltató nem szeretné, hogy az ügyfél hozzáférjen bizonyos, az ügyfél bérlője által tárolt erőforrásokhoz, vagy engedélyezze az adott erőforrásokra vonatkozó testreszabott műveleteket. Ezeknek a céloknak a teljesítéséhez a szolgáltató felügyelt alkalmazásként tehet közzé privát ajánlatot. A felügyelt alkalmazás tartalmazhat olyan erőforráscsoportot, amelyet az ügyfél bérlője telepít, de az ügyfél nem fér hozzá közvetlenül.

Előfordulhat, hogy az ügyfelek több szolgáltató által felügyelt alkalmazások is érdeklik, függetlenül attól, hogy a felügyelt szolgáltatásokat is használják-e az Azure Lighthouse-n keresztül ezen szolgáltatók bármelyikén. Emellett a Cloud Solution Provider (CSP) program partnerei olyan felügyelt alkalmazásokat is megadhatnak, amelyeket az Azure Lighthouse-n keresztül támogatnak más ISV-ket. Számos lehetőség közül a szolgáltatók kiválaszthatják a megfelelő egyensúlyt az ügyfelek igényeinek kielégítéséhez, miközben szükség esetén korlátozzák az erőforrásokhoz való hozzáférést.

## <a name="next-steps"></a>További lépések

- További információ az [Azure által felügyelt alkalmazásokról](../../azure-resource-manager/managed-applications/overview.md).
- Ismerje meg, hogyan lehet [előfizetést bevezetni az Azure-beli delegált erőforrás-kezelésbe](../how-to/onboard-customer.md).
