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
ms.date: 12/07/2018
ms.author: pbutlerm
ms.openlocfilehash: 63b7ee4e0d9cb9d0d1f26119fe73573b124d04e8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53197019"
---
# <a name="azure-application-offer"></a>Az ajánlat Azure-alkalmazás

Ez a szakasz azt ismerteti, hogyan egy új Azure-alkalmazás-ajánlat közzététele a Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace-en</a>.
|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Minden egyes Azure-alkalmazás az Azure Resource Manager-sablon, amely meghatározza az alkalmazás, amely általában tartalmaz egy vagy több virtuális gépet és egyéb támogató Azure - vagy Web-alapú szolgáltatások által használt összes műszaki eszközöket tartalmazza. | ![Az Azure apps ikon](./media/azureapp-icon1.png)  |

## <a name="benefits"></a>Előnyök

Az alkalmazások a Microsoft marketplace-en listázása előnyei a következők:

* 100 millió Azure Active Directory-felhasználók elérése az Office 365 és Dynamics 365 között.

* Az értékesítési csoport kiterjesztése: érhet el üzleti felhasználóknak világszerte, és így olyan értékesítési csatorna, amely felkelti az ügyfelek teljes, hozzájárul az érdeklődői és kapcsolatot teremt az új ügyfelek különböző ágazatok között.

* Bevezetés a gyakorlatban hasznosítható elemzéseket nyújt: hogyan működik az alkalmazás az appsource-on, milyen jól működik, és hogyan tovább javíthatja az értékesítési eljárások betekintést nyújtunk.

## <a name="types-of-azure-applications"></a>Az Azure-alkalmazások típusai

Két fajtája van az Azure-alkalmazások: egy felügyelt alkalmazást, és a egy megoldás sablon. Hasonló, bár vannak néhány jelentős különbség.

### <a name="solution-template"></a>Megoldássablon

Megoldássablonok a fő módon lehet a megoldás a piactéren közzétett tartoznak. Ez az ajánlat típusát a megoldáshoz szükséges további üzembe helyezési és konfigurációs automation túl egyetlen virtuális gép (VM) használt. Egynél több virtuális gép, megoldássablon használatával biztosító automatizálható. Ez magában foglalja, hálózati és tárolási erőforrások összetett IaaS-megoldások kiépítése. Megoldás sablon követelményeinek és a számlázási modell áttekintését lásd: [Azure-alkalmazások: megoldássablonok](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

### <a name="managed-application"></a>Felügyelt alkalmazás

A felügyelt alkalmazások sokban hasonlítanak a Marketplace megoldássablonjaihoz, egy fontos különbséget leszámítva. A felügyelt alkalmazások esetében az erőforrások üzembe helyezése egy erőforráscsoportban történik, amelyet az alkalmazás közzétevője felügyel. Az erőforráscsoport az ügyfél előfizetésében található meg, de a közzétevő bérlőjének egy identitása rendelkezik hozzáféréssel az erőforráscsoporthoz. A megoldás folyamatos támogatásának költségeit a közzétevő határozza meg. Az Azure által felügyelt alkalmazások használatával könnyedén hozhat létre és nyújthat ügyfeleinek teljes körűen felügyelt, kulcsrakész alkalmazásokat.

A Marketplace-en kívül is elérhetővé teheti a felügyelt alkalmazások a szolgáltatáskatalógus. A szolgáltatáskatalógus egy belső katalógus azokról a megoldásokról, amelyeket egy vállalat jóváhagy a felhasználói számára. A katalógus használatával szervezeti szabványoknak megfelelő megoldások olyan szervezeti csoportok garantál. Az alkalmazottak a katalógussal könnyen felfedezhetik az informatikai részleg által jóváhagyott és ajánlott alkalmazásokat.

Milyen előnyökkel és a felügyelt alkalmazások további információkért lásd: a [Azure managed applications áttekintése](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="publishing-overview"></a>Közzétételi áttekintése

Az alábbi videó [létrehozása Megoldássablonokkal, és a felügyelt alkalmazások az Azure Marketplace-en](https://channel9.msdn.com/Events/Build/2018/BRK3603), annak áttekintését, hogyan hozhat létre az Azure Resource Manager-sablon egy Azure application megoldás, és ezután hogyan meghatározása Ezt követően tegye közzé az alkalmazást az ajánlat az Azure piactéren.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]

## <a name="publishing-process-workflow"></a>Közzétételi folyamat munkafolyamat

Az alábbi ábrán egy Azure-alkalmazás az ajánlat közzétételi magas szintű folyamata látható.

![Az ajánlat közzétételi munkafolyamata](./media/new-offer-process.png)

## <a name="offer-components"></a>Az ajánlat összetevők

Ebben a szakaszban egy kezelt alkalmazásra vonatkozó ajánlat közzététele elemeit ismerteti, és célja, a közzétevő eligazodhat az Azure Marketplace-en. Közzététel a következő fő részeket osztva: 

* [Előfeltételek](./cpp-prerequisites.md) -létrehozása vagy egy kezelt alkalmazásra vonatkozó ajánlat közzététele előtt technikai és üzleti követelményeit. 
* [Az ajánlat létrehozásához](./cpp-create-offer.md) – lehetővé teszi a felügyelt alkalmazás használata a Cloud Partner portálra ajánlat bejegyzés létrehozásához szükséges lépéseket. 
* [Az ajánlat közzététele](./cpp-publish-offer.md)-ismerteti, hogyan lehet elküldeni az ajánlat az Azure piactéren való közzétételre.

## <a name="steps-in-the-publishing-process"></a>A közzétételi folyamat lépései

Az Azure-alkalmazás-ajánlat közzétételéhez a magas szintű lépései a következők:

1. Az ajánlat létrehozásához – az ajánlat részletes információkat tartalmaznak. Ezen információk közé tartozik: az ajánlat leírása, marketing-adategység-specifikációi, anyagokat és támogatási információk.

2. Hozzon létre az üzleti és technikai eszközök – az üzleti eszközök (jogi dokumentumok és marketinganyagokat) és a kapcsolódó megoldás technikai eszközök létrehozása.

3. A Termékváltozat létrehozása – a termékváltozat(ok) társított az ajánlat létrehozása. Egy egyedi Termékváltozat megadása kötelező az egyes lemezképek szeretné közzétenni.

4. Igazolom, és tegye közzé az ajánlat – az ajánlat és a technikai eszközök befejezése után az ajánlatot küldhet. Az elküldés során először a közzétételi folyamat. Ez a folyamat során a megoldás vizsgálják, érvényesíti, minősített, majd "élesíti" az Azure Marketplace-en.

## <a name="next-steps"></a>További lépések

Mielőtt ezeket a lépéseket, meg kell felelnie a [műszaki és üzleti igényeinek](./cpp-prerequisites.md) egy felügyelt alkalmazást a Microsoft Azure piactéren való közzétételéhez.