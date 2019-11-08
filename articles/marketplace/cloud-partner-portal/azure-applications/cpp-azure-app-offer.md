---
title: Azure-alkalmazás ajánlat | Azure piactér
description: Azure-alkalmazási ajánlat Azure Marketplace-en való közzétételének folyamata – áttekintés.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pabutler
ms.openlocfilehash: 9125b5c71b63b27c58ea72b7bfd49f730854b33d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818795"
---
# <a name="azure-application-offer"></a>Azure-alkalmazásajánlat

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Ez a szakasz azt ismerteti, hogyan tehet közzé egy új Azure-alkalmazást az [Azure Marketplace](https://azuremarketplace.microsoft.com)-en.  Minden Azure-alkalmazás tartalmaz egy Azure Resource Manager sablont, amely meghatározza az alkalmazás által használt összes technikai eszközt, amely általában egy vagy több virtuális gépet és más, Azure-vagy webalapú szolgáltatásokat támogató szolgáltatást tartalmaz. Az Azure app-ajánlatoknak [Azure Active Directoryon](https://docs.microsoft.com/azure/active-directory/)keresztül kell engedélyeznie a hozzáférési biztonságot.  </div> | ![Azure-alkalmazások ikon](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Közzététel áttekintése

Az alábbi videó, az Azure Marketplace-hez készült [megoldási sablonok és a felügyelt alkalmazások](https://channel9.msdn.com/Events/Build/2018/BRK3603)bemutatása: a rendelkezésre álló ajánlati típusok, a szükséges technikai eszközök, a Azure Resource Manager-sablonok létrehozása, a fejlesztés az alkalmazás felhasználói felületének tesztelése, az alkalmazás-ajánlat közzététele és az alkalmazás-felülvizsgálati folyamat tesztelése.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Azure-alkalmazások típusai

Kétféle Azure-alkalmazás létezik: felügyelt alkalmazások és megoldási sablonok. 

- A megoldási sablonok az egyik fő módszer a megoldás közzétételére a piactéren. Ezt az ajánlatot akkor kell használni, ha a megoldás további üzembe helyezést és konfigurációs automatizálást igényel egy virtuális gépen (VM) túl. A megoldási sablonnal több virtuális gép is automatizálható. Ez az automatizálás a hálózatkezelési és tárolási erőforrások kiépítését foglalja magában összetett IaaS-megoldások biztosításához. A megoldási sablonra vonatkozó követelmények és a számlázási modell áttekintését az [Azure-alkalmazások: megoldás-sablonok](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates)című részben találja.

- A felügyelt alkalmazások hasonlók a megoldási sablonokhoz, és egyetlen kulcsfontosságú különbséggel rendelkeznek. A felügyelt alkalmazások esetében az erőforrások üzembe helyezése egy erőforráscsoportban történik, amelyet az alkalmazás közzétevője felügyel. Az erőforráscsoport az ügyfél előfizetésében található meg, de a közzétevő bérlőjének egy identitása rendelkezik hozzáféréssel az erőforráscsoporthoz. A megoldás folyamatos támogatásának költségeit a közzétevő határozza meg. Az Azure Managed Applications használatával egyszerűen hozhat létre és biztosíthat teljes körűen felügyelt, kulcsrakész alkalmazásokat ügyfelei számára.

Az Azure piactéren kívül felügyelt alkalmazásokat is biztosíthat a szolgáltatás-katalógusban. A szolgáltatáskatalógus egy belső katalógus azokról a megoldásokról, amelyeket egy vállalat jóváhagy a felhasználói számára. A katalógus segítségével teljesítheti a szervezeti szabványokat, és megoldásokat kínálhat a szervezetek csoportjaihoz. Az alkalmazottak a katalógussal könnyen felfedezhetik az informatikai részleg által jóváhagyott és ajánlott alkalmazásokat.

>[!Note]
>A Cloud Solution Providers (CSP) Partner Channel opt-in mostantól elérhető.  Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [Cloud Solution Providers](../../cloud-solution-providers.md) című témakört.

További információ a felügyelt alkalmazások előnyeiről és típusairól: az [Azure által felügyelt alkalmazások áttekintése](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Közzétételi folyamat munkafolyamata

Az alábbi ábra az Azure-alkalmazások ajánlatának közzétételének magas szintű folyamatát mutatja be.

![Az ajánlat közzétételének munkafolyamata](./media/new-offer-process.png)

Az Azure-alkalmazások ajánlatának közzétételéhez szükséges magas szintű lépések a következők:

1. Teljesítse az [előfeltételeket](./cpp-prerequisites.md) – (nem látható) ellenőrizze, hogy megfelelt-e az Azure-alkalmazások Azure Marketplace-en való közzétételének üzleti és technikai követelményeinek. 

1. [Ajánlat létrehozása](./cpp-create-offer.md) – részletes információkat nyújt az ajánlatról. Ezek az adatok a következők: az ajánlat leírása, a marketing-anyagok, a támogatási információk és az eszközök specifikációja.

1. [Meglévő üzleti és technikai eszközök létrehozása vagy összegyűjtése](./cpp-create-technical-assets.md) – a kapcsolódó megoldáshoz hozzon létre üzleti eszközöket (jogi dokumentumok és marketing anyagok) és technikai eszközöket.

1. [Hozza létre a SKU](./cpp-skus-tab.md) -t – hozza létre az ajánlathoz társított SKU (ka) t. Minden közzétenni kívánt rendszerképhez egyedi SKU szükséges.

1. Az ajánlat tanúsítása és [közzététele](./cpp-publish-offer.md) – az ajánlat és a technikai eszközök befejezése után elküldheti az ajánlatot. Ez a Küldés elindítja a közzétételi folyamatot. A folyamat során a megoldás tesztelése, ellenőrzése, minősítése, majd az Azure piactéren az "élő" állapot érhető el.

## <a name="next-steps"></a>További lépések

Mielőtt figyelembe veszi ezeket a lépéseket, meg kell felelnie a felügyelt alkalmazások Microsoft Azure Marketplace való közzétételének [technikai és üzleti követelményeinek](./cpp-prerequisites.md) .
