---
title: Alkalmazás Azure-ajánlat |} A Microsoft Docs
description: A közzétételi folyamat áttekintése az Azure-alkalmazások az Azure Marketplace-en kínálnak.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pbutlerm
ms.openlocfilehash: 9faa38a23b2039902366e5b885ab73c68a2a3d80
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076020"
---
# <a name="azure-application-offer"></a>Az ajánlat Azure-alkalmazás

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Ez a szakasz ismerteti, hogyan tehet közzé egy új Azure-alkalmazás ajánlattal a [Azure Marketplace-en](https://azuremarketplace.microsoft.com).  Minden egyes Azure-alkalmazás az Azure Resource Manager-sablon, amely meghatározza az alkalmazás, amely általában tartalmaz egy vagy több virtuális gépet és egyéb támogató Azure - vagy Web-alapú szolgáltatások által használt összes műszaki eszközöket tartalmazza. Minden Azure-alkalmazás ajánlat engedélyeznie kell a hozzáférés-biztonságot keresztül [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Az Azure apps ikon](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Közzétételi áttekintése

Az alábbi videó [létrehozása Megoldássablonokkal, és a felügyelt alkalmazások az Azure Marketplace-en](https://channel9.msdn.com/Events/Build/2018/BRK3603), bemutatja az: Mit kínálnak típus érhető el technikai eszközök Mik szükséges, hogyan hozhat létre egy Azure Resource Manager sablon, fejlesztés és tesztelés az alkalmazás felhasználói felületén, hogyan teheti közzé az alkalmazást az ajánlat és az alkalmazás elbírálási folyamata.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Az Azure-alkalmazások típusai

Két fajtája van az Azure-alkalmazások: a felügyelt alkalmazások és megoldássablonok. 

- Megoldássablonok a fő módon lehet a megoldás a piactéren közzétett tartoznak. Ez az ajánlat típusát a megoldáshoz szükséges további üzembe helyezési és konfigurációs automation túl egyetlen virtuális gép (VM) használt. Egynél több virtuális gép, megoldássablon használatával biztosító automatizálható. Ezt az automatizálást magában foglalja a hálózati és tárolási erőforrások összetett IaaS-megoldások kiépítése. Megoldás sablon követelményeinek és a számlázási modell áttekintését lásd: [Azure-alkalmazások: megoldássablonok](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Felügyelt alkalmazások hasonlóak megoldássablonokkal, egy fő különbséggel. A felügyelt alkalmazások esetében az erőforrások üzembe helyezése egy erőforráscsoportban történik, amelyet az alkalmazás közzétevője felügyel. Az erőforráscsoport az ügyfél előfizetésében található meg, de a közzétevő bérlőjének egy identitása rendelkezik hozzáféréssel az erőforráscsoporthoz. A megoldás folyamatos támogatásának költségeit a közzétevő határozza meg. Az Azure által felügyelt alkalmazások használatával könnyedén hozhat létre és nyújthat ügyfeleinek teljes körűen felügyelt, kulcsrakész alkalmazásokat.

Az Azure piactéren kívül szolgáltatáskatalógus a felügyelt alkalmazások is rendelkeznek. A szolgáltatáskatalógus egy belső katalógus azokról a megoldásokról, amelyeket egy vállalat jóváhagy a felhasználói számára. A katalógus használatával szervezeti szabványoknak megfelelő megoldások olyan szervezeti csoportok garantál. Az alkalmazottak a katalógussal könnyen felfedezhetik az informatikai részleg által jóváhagyott és ajánlott alkalmazásokat.

>[!Note]
>Cloud Solution Providers (CSP) partner csatorna vehetnek részt már elérhető.  Lásd: [Cloud Solution Providers](../../cloud-solution-providers.md) további tájékoztatást a marketing, az ajánlat keretében a Microsoft CSP partner-csatornákon.

Milyen előnyökkel és a felügyelt alkalmazások további információkért lásd: a [Azure managed applications áttekintése](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Közzétételi folyamat munkafolyamat

Az alábbi ábrán egy Azure-alkalmazás az ajánlat közzétételi magas szintű folyamata látható.

![Az ajánlat közzétételi munkafolyamata](./media/new-offer-process.png)

Az Azure-alkalmazás-ajánlat közzétételéhez a magas szintű lépései a következők:

1. Megfelel a [Előfeltételek](./cpp-prerequisites.md) (nem látható) – Győződjön meg arról, hogy teljesül-e az üzleti és technikai követelmények, egy Azure-alkalmazáshoz az Azure piactéren való közzétételéhez. 

1. [Az ajánlat létrehozásához](./cpp-create-offer.md) – az ajánlat részletes információkat tartalmaznak. Ezen információk közé tartozik: az ajánlat leírása, marketing-adategység-specifikációi, anyagokat és támogatási információk.

1. [Hozzon létre vagy meglévő üzleti és technikai eszközök gyűjtése](./cpp-create-technical-assets.md) – az üzleti eszközök (jogi dokumentumok és marketinganyagokat) és a kapcsolódó megoldás technikai eszközök létrehozása.

1. [A Termékváltozat létrehozása](./cpp-skus-tab.md) – a termékváltozat(ok) társított az ajánlat létrehozása. Egy egyedi Termékváltozat megadása kötelező az egyes lemezképek szeretné közzétenni.

1. Igazolja és [az ajánlat közzététele](./cpp-publish-offer.md) – az ajánlat és a technikai eszközök befejezése után az ajánlatot küldhet. Az elküldés során először a közzétételi folyamat. Ez a folyamat során a megoldás vizsgálják, érvényesíti, minősített, majd "élesíti" az Azure Marketplace-en.

## <a name="next-steps"></a>További lépések

Mielőtt ezeket a lépéseket, meg kell felelnie a [műszaki és üzleti igényeinek](./cpp-prerequisites.md) egy felügyelt alkalmazást a Microsoft Azure piactéren való közzétételéhez.
