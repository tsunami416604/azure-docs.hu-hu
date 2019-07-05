---
title: A Microsoft Ügyfélszerződéséhez számlázási fiókba – az Azure használatának első lépései
description: Megismerheti a Microsoft vevői szerződés számlázási fiók
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 87483c967641489e9548f38c99eebbf121d0d252
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490736"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Ismerkedés a Microsoft vevői szerződés számlázási fiók

Feliratkozás használható az Azure számlázási fiók jön létre. A számlázási fiók használata kezelheti a számlákon, a fizetés, és nyomon követni a költségeket. Több számlázási fiók rendelkezhet. Például előfordulhat, hogy feliratkozott az Azure-hoz a személyes projektekhez. Hozzáférés az Azure-ban a szervezet nagyvállalati szerződéssel vagy Microsoft Ügyfélszerződéséhez keresztül is lehet. Az egyes forgatókönyvek esetében, a számlázási fiókra kell.

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>A számlázási fiók

A Microsoft ügyfél-szerződés az Ön számlázási fiókjához tartalmaz egy vagy több számlázási profilok –, amelyek lehetővé teszik a számlákat és a fizetési módok kezeléséhez. Minden egyes számlázási profil egy vagy több, amelyekkel a számlázási profilja számlán költségek rendszerezése számla szakasz tartalmazza.

Az alábbi ábrán látható egy számlázási fiók, a számlázási profilokat és a számla szakaszok közötti kapcsolat.

![A Microsoft vevői szerződés számlázási hierarchia bemutató ábra.](./media/billing-mca-overview/mca-billing-hierarchy.png)

A számlázási fiók szerepkörök rendelkeznek a legmagasabb szintű engedélyeket. Alapértelmezés szerint csak a felhasználó, aki regisztrált az Azure lekérdezi a hozzáférést a számlázási fiók. Ezek a szerepkörök számlák megtekintése, valamint nyomon követheti a teljes cég számára, mint például a pénzügyi vagy az informatikai vezetők költségek felhasználóknak hozzá kell rendelni. További információkért lásd: [számlázási fiók szerepkörök és feladatok](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>A számlázás profilok

A számlázási profil használatával a számlázási és fizetési módok kezelése. A fiókban lévő egyes számlázási profilok a hónap elején a havi számla jön létre. A számlán minden Azure-előfizetés és az egyéb vásárlási az előző hónap megfelelő díja tartalmazza.

A számlázási fiók automatikusan létrejön egy számlázási profilja. Alapértelmezés szerint tartalmaz egy számla szakaszt. Könnyedén nyomon követheti és költségek az igényei szerint rendszerezheti további szakaszok is létrehozhat e projekt, részleg vagy a fejlesztői környezetben. Ezekben a szakaszokban láthatja a számlázási profilja számlán tükröző minden egyes előfizetés és a társított, a vásárlás használatát.

A számlázási profilban szerepkörök engedélye számlákat és a fizetési módok megtekintése és kezelése. Ezek a szerepkörök hozzárendelése a felhasználókhoz, például a számviteli csoport tagjai számlákat a szervezet fizeti ki. További információkért lásd: [számlázási profil szerepkörök és feladatok](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Minden egyes számlázási profilja lekérdezi a havi számla

Minden egyes számlázási profilja hónap elején a havi számla jön létre. A számlán minden költséget az előző hónap tartalmazza.

A számla megtekintése, töltse le a dokumentumokat, és beszerezni a jövőbeli számlákat e-mailek, az Azure Portalon a beállítás módosításához. További információkért lásd: [számlákat letölteni a Microsoft vevői szerződés](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Számlás fizetési módok

Minden egyes számlázási profilja rendelkezik a saját fizetési módok, amely segítségével a számlákat kell fizetnie. A következő fizetési módokat támogatja:

| Típus             | Meghatározás  |
|------------------|-------------|
|Az Azure-kreditek    |  Kreditek automatikusan lépnek a jogosult díjait a számlán, így csökkenthető a, akkor meg kell fizetni. További információkért lásd: [nyomon követheti a számlázási profilja Azure kreditegyenlegét](billing-mca-check-azure-credits-balance.md). |
|Ellenőrzés/átviteli átvitel | Ha a fiók ellenőrzése/átviteli átviteli keresztül számára jóvá van hagyva. Az összeg fizethet a szolgáltatásért a számla át ellenőrzés/átviteli átvitel esedékes. A fizetési utasításokat kapnak a számlán |
|Hitelkártya | Hitelkártya keresztül regisztráló ügyfelek az Azure-hoz az Azure-webhelyen keresztül fizethet a szolgáltatásért. |

### <a name="apply-policies-to-control-purchases"></a>A alkalmazni szabályzatokat, amelyekkel szabályozható a vásárlások

Házirendek alkalmazása a vezérlő Azure Marketplace-en és a Foglalás vásárlások számlázási profilt használ. Beállíthatja a házirendek letiltása az Azure-foglalásokat és Piactéri termékek vásárlására. A szabályzatok érvénybe lépnek, ha ezek a vásárlások előfizetéseket, amelyek számlázása a számlázási profilja nem használható.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Az Azure-csomagok díjszabása és a szolgáltatás szolgáltatásiszint-szerződés az előfizetések határozza meg.

Az Azure-csomagok határozza meg, a díjszabás, és az Azure-előfizetések számára garantált szolgáltatási szintek. A számlázási profil létrehozásakor automatikusan engedélyezettek. A számlázási profilhoz társított összes számla-szakaszok használhatja az alábbi díjcsomagok. A számla szakasz hozzáféréssel rendelkező felhasználók a tervek használata az Azure-előfizetéseket hozhasson létre. A következő Azure-csomagok támogatottak a számlázási fiókok a Microsoft ügyfél-szerződés:

| Felkészülés             | Meghatározás  |
|------------------|-------------|
|A Microsoft Azure-csomagot   | Engedélyezi a felhasználók hozhatnak létre előfizetéseket, amelyek futtathatók a számítási feladatokat. További információkért lásd: [a Microsoft Azure-csomag](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Microsoft Azure-csomag fejlesztéshez és teszteléshez | Lehetővé teszi a Visual Studio-előfizetők hozhatnak létre előfizetéseket, amelyek korlátozott fejlesztési vagy tesztelési feladatok. Ezeket az előfizetéseket kaphat további előnyöket, például az alacsonyabb díjszabás, valamint az exkluzív virtuálisgép-lemezképek elérését az Azure Portalon. További információkért lásd: [a Microsoft a Azure DevTest tervezése](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="invoice-sections"></a>Számla szakaszok

Hozzon létre rendszerezheti a költségek, a számla invoice szakaszokat. Ha például szükség lehet egyetlen számlán a szervezet, de szeretne osztály, csoport vagy projekt költségeket rendszerezéséhez. Ebben a forgatókönyvben egy számlázási profillal rendelkezik, amellyel létre minden egyes osztály, csoport vagy projekt egy számla című szakaszt.

A számla szakasz létrehozásakor a engedélyt adhat a mások hozhat létre Azure-előfizetések számlázása a szakaszra. A használati díjak és a vásárlások az előfizetések majd számlázása a szakaszra.

A számla szakaszban lévő szerepkörök engedélye szabályozza, ki Azure-előfizetést hoz létre. Ezek a szerepkörök hozzárendelése a felhasználók, akik az Azure környezetet az például a mérnöki érdeklődőket és a műszaki architects a szervezeten belül. További információkért lásd: [szakasz szerepkörök és feladatok számla](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha segítségre van szüksége, kell [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

További információ az Ön számlázási fiókjához a következő cikkekben talál:

- [Megismerheti a Microsoft Ügyfélszerződéséhez rendszergazdai szerepkörök az Azure-ban](billing-understand-mca-roles.md)
- [További Azure-előfizetés létrehozása a Microsoft ügyfél-szerződés](billing-mca-create-subscription.md)
- [A számla a költségek rendszerezéséhez szakaszok létrehozása](billing-mca-section-invoice.md)
