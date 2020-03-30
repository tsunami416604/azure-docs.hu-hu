---
title: Azure alkalmazásajánlat | Azure Piactér
description: Az Azure-alkalmazásajánlat azure-piacon való közzétételének folyamatának áttekintése.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: dsindona
ms.openlocfilehash: ed086ffdc49e21b819c0ee05b38ad882b4e269d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285316"
---
# <a name="azure-application-offer"></a>Azure-alkalmazásajánlat

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Ez a szakasz bemutatja, hogyan tehet közzé egy új Azure-alkalmazásajánlatot az [Azure Piactéren.](https://azuremarketplace.microsoft.com)  Minden Azure-alkalmazás tartalmaz egy Azure Resource Manager-sablont, amely meghatározza az alkalmazás által használt összes technikai eszközt, amely általában egy vagy több virtuális gépet és más támogató Azure- vagy webalapú szolgáltatásokat tartalmaz. Az Azure-alkalmazás minden ajánlatának engedélyeznie kell a hozzáférés biztonságát az [Azure Active Directoryn](https://docs.microsoft.com/azure/active-directory/)keresztül.  </div> | ![Az Azure-alkalmazások ikonja](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Közzététel – áttekintés

A következő videó, [a Building Solution Templates és a Managed Applications for the Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)bemutatkozik: milyen ajánlattípusok érhetők el, milyen technikai eszközökre van szükség, hogyan kell azure Resource Manager-sablont meghozni, fejleszteni és tesztelni az alkalmazás felhasználói felületét, hogyan teheti közzé az alkalmazásajánlatot és az alkalmazásellenőrzési folyamatot.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Az Azure-alkalmazások típusai

Kétféle Azure-alkalmazás létezik: felügyelt alkalmazások és megoldássablonok. 

- A megoldássablonok a megoldások marketplace-en való közzétételének egyik fő módja. Ez az ajánlattípus akkor használatos, ha a megoldás további üzembe helyezést és konfigurációs automatizálást igényel egyetlen virtuális gépen (VM) kívül. Egy megoldássablon használatával automatizálhatja több virtuális gép biztosítását. Ez az automatizálás magában foglalja a hálózati és tárolási erőforrások kiépítését összetett IaaS-megoldások biztosításához. A megoldássablon-követelmények és a számlázási modell áttekintését az [Azure Applications: solution templates című témakörben találja.](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates)

- A felügyelt alkalmazások hasonlóak a megoldássablonokhoz, egy kulcskülönbséggel. A felügyelt alkalmazások esetében az erőforrások üzembe helyezése egy erőforráscsoportban történik, amelyet az alkalmazás közzétevője felügyel. Az erőforráscsoport az ügyfél előfizetésében található meg, de a közzétevő bérlőjének egy identitása rendelkezik hozzáféréssel az erőforráscsoporthoz. A megoldás folyamatos támogatásának költségeit a közzétevő határozza meg. Az Azure Managed alkalmazásokkal egyszerűen hozhat létre és szállíthat teljes körűen felügyelt, kulcsrakész alkalmazásokat ügyfeleinek.

Az Azure Marketplace mellett felügyelt alkalmazásokat is kínálhat egy szolgáltatáskatalógusban. A szolgáltatáskatalógus egy belső katalógus azokról a megoldásokról, amelyeket egy vállalat jóváhagy a felhasználói számára. A katalógus segítségével megfelel a szervezeti szabványoknak, miközben megoldásokat kínál a szervezet csoportainak. Az alkalmazottak a katalógussal könnyen felfedezhetik az informatikai részleg által jóváhagyott és ajánlott alkalmazásokat.

>[!Note]
>A felhőszolgáltatók (CSP) partnercsatorna-opt-in már elérhető.  Az ajánlat microsoftos CSP-partnercsatornákon keresztül történő marketingről további információt a [felhőszolgáltatók](../../cloud-solution-providers.md) ban talál.

A felügyelt alkalmazások előnyeiről és típusairól az [Azure által felügyelt alkalmazások áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/azure/managed-applications/overview)


## <a name="publishing-process-workflow"></a>Közzétételi folyamat munkafolyamata

Az alábbi ábrán az Azure-alkalmazásajánlat közzétételének magas szintű folyamata látható.

![Munkafolyamat közzétételi ajánlathoz](./media/new-offer-process.png)

Az Azure-alkalmazásajánlat közzétételének magas szintű lépései a következők:

1. Az [előfeltételek](./cpp-prerequisites.md) teljesítése – (Nem látható) Ellenőrizze, hogy megfelelt-e az Azure-alkalmazások Azure Piactéren való közzétételére vonatkozó üzleti és műszaki követelményeknek. 

1. [Az ajánlat létrehozása](./cpp-create-offer.md) – Adjon meg részletes információkat az ajánlatról. Ez az információ a következőket tartalmazza: az ajánlat leírása, marketing anyagok, támogatási információk és az eszköz specifikációi.

1. [Meglévő üzleti és technikai eszközök létrehozása vagy összegyűjtése](./cpp-create-technical-assets.md) – Az üzleti eszközök (jogi dokumentumok és marketinganyagok) és a kapcsolódó megoldás technikai eszközeinek létrehozása.

1. [A termékváltozat létrehozása](./cpp-skus-tab.md) – az ajánlathoz társított termékváltozatok létrehozása. Minden közzétenni kívánt lemezképhez egyedi termékváltozat szükséges.

1. Az [ajánlat](./cpp-publish-offer.md) hitelesítése és közzététele – Az ajánlat és a technikai eszközök befejezése után benyújthatja az ajánlatot. Ez a beküldés elindítja a közzétételi folyamatot. A folyamat során a megoldás tesztelt, érvényesített, hitelesített, majd "éles" az Azure Marketplace-en.

## <a name="next-steps"></a>További lépések

Mielőtt megfontolja ezeket a lépéseket, meg kell felelnie a felügyelt alkalmazások microsoft Azure Piactéren való közzétételéhez [szükséges technikai és üzleti követelményeknek.](./cpp-prerequisites.md)
