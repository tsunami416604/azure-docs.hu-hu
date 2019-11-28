---
title: Bevezetés a Microsoft-ügyfélszerződéshez tartozó számlázási fiók használatába – Azure
description: A Microsoft-ügyfélszerződéshez tartozó számlázási fiók
author: bandersmsft
manager: amberbhargava
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: e27d2116cf58c64fca7fb0273c852d6223f00c6e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226121"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Bevezetés a Microsoft-ügyfélszerződéshez tartozó számlázási fiók használatába

A számlázási fiók az Azure-ba való regisztráció során jön létre. A számlázási fiók használatával kezelheti a számlákat és a kifizetéseket, valamint nyomon követheti a költségeket. Több számlázási fiókhoz is rendelkezhet hozzáféréssel. Előfordulhat például, hogy az egyéni projektjei kezeléséhez regisztrált az Azure-ba. Emellett az Azure-hoz hozzáféréssel rendelkezhet a szervezete Nagyvállalati Szerződésén vagy egy Microsoft-ügyfélszerződésen keresztül is. Mindegyik regisztrációhoz egy külön számlázási fiók tartozik.

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó számlázási fiókokra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>Az Ön számlázási fiókja

A Microsoft-ügyfélszerződéshez tartozó számlázási fiókja egy vagy több számlázási profilt tartalmaz, amelyek lehetővé teszik a számlák és a fizetési módok kezelését. Az egyes számlázási profilok egy vagy több számlaszakaszt tartalmaznak, amelyekkel rendszerezhetőek a számlázási profil számláján szereplő költségek.

Az alábbi ábrán a számlázási fiók, a számlázási profilok és a számlaszakaszok közötti kapcsolat látható.

![A Microsoft-ügyfélszerződés számlázási hierarchiáját bemutató ábra](./media/billing-mca-overview/mca-billing-hierarchy.png)

A számlázási fiók szerepkörei rendelkeznek a legmagasabb szintű engedélyekkel. Alapértelmezés szerint csak az Azure-ba regisztrált felhasználó kap hozzáférést a számlázási fiókhoz. Ezeket a szerepköröket olyan felhasználókhoz kell hozzárendelni, akiknek meg kell tekinteniük a számlákat, és nyomon kell követniük a teljes szervezet költségeit, tehát például pénzügyi vagy informatikai vezetőkhöz. További információkért lásd [a számlázási fiók szerepköreit és azok feladatait](billing-understand-mca-roles.md#billing-account-roles-and-tasks) ismertető cikket.

## <a name="billing-profiles"></a>Számlázási profilok

A számlát és a fizetési módokat a számlázási profilok segítségével kezelheti. A hónap elején a fiókhoz tartozó összes számlázási profilhoz létrejön egy havi számla. A számla az Azure-előfizetések és egyéb vásárlások előző havi díjait tartalmazza.

A számlázási fiókhoz automatikusan létrejön egy számlázási profil. A számlázási profil alapértelmezés szerint egy számlaszakaszt tartalmaz. A költségek egyszerű nyomon követéséhez és rendszerezéséhez létrehozhat további szakaszokat az igényei alapján, például projektenként, részlegenként vagy fejlesztési környezetenként. Ezek a szakaszok megjelennek a számlázási profilhoz tartozó számlán, amely az egyes előfizetések használati adatait és az azokhoz társított vásárlásokat tartalmazza.

A számlázási profilok szerepkörei rendelkeznek a számlák és a fizetési módok megtekintéséhez és kezeléséhez szükséges engedéllyel. Ezeket a szerepköröket hozzárendelheti a számlák kifizetéséért felelős felhasználókhoz, például a szervezet könyvelői csapatának tagjaihoz. További információkért lásd [a számlázási profil szerepköreit és azok feladatait](billing-understand-mca-roles.md#billing-profile-roles-and-tasks) ismertető cikket.

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Minden számlázási profilhoz létrejön egy havi számla.

A hónap elején minden egyes számlázási profilhoz létrejön egy havi számla. A számla az előző hónap összes díját tartalmazza.

Az Azure Portalon megtekintheti a számlát, letöltheti a dokumentumokat, és megadhatja, hogy a jövőbeli számlákat megkapja e-mailben. További információért lásd a [Microsoft-ügyfélszerződéshez tartozó számlák letöltése](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement) című részt.

### <a name="invoice-payment-methods"></a>A számlázás fizetési módjai

Minden számlázási profilhoz saját fizetési mód adható meg a számlák kiegyenlítéséhez. A következő fizetési módok támogatottak:

| Típus             | Meghatározás  |
|------------------|-------------|
|Azure-kreditek    |  A rendszer automatikusan alkalmazza a krediteket a számlán szereplő, arra jogosult díjakra, ezáltal csökkenti a fizetendő összeget. További információkért lásd [a számlázási profilhoz tartozó Azure-kreditek egyenlegének nyomon követését](billing-mca-check-azure-credits-balance.md) ismertető cikket. |
|Csekk vagy átutalás | Abban az esetben, ha a fiókjához jóvá lett hagyva a csekkel vagy átutalással történő fizetés. A számlán szereplő fizetendő összeget kifizetheti csekkel vagy átutalással. A fizetési utasításokat a számlán találja. |
|Hitelkártya | Azok az ügyfelek, akik az Azure webhelyén regisztrálnak az Azure-ba, fizethetnek hitelkártyával. |

### <a name="apply-policies-to-control-purchases"></a>Szabályzatok alkalmazása a vásárlások szabályozásához

Szabályzatokat alkalmazhat az Azure Marketplace- és Reservations-vásárlások számlázási profillal történő szabályozásához. Beállíthat szabályzatokat az Azure Reservations- és Marketplace-termékek megvásárlásának tiltására. Ha alkalmazza a szabályzatokat, a számlázási profillal számlázott előfizetéseket nem lehet ilyen típusú vásárlásokhoz használni.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Az előfizetések díjszabását és szolgáltatói szerződését az Azure-csomagok határozzák meg.

Az Azure-előfizetések díjszabását és szolgáltatói szerződéseit az Azure-csomagok határozzák meg. Ezek a számlázási profil létrehozásakor automatikusan engedélyezve vannak. A számlázási profilhoz társított összes számlaszakasz használhatja ezeket a csomagokat. A csomagokkal a számlaszakaszhoz hozzáféréssel rendelkező felhasználók létrehozhatnak Azure-előfizetéseket. A Microsoft-ügyfélszerződéssel rendelkező számlázási fiókok esetében az alábbi Azure-csomagok támogatottak:

| Felkészülés             | Meghatározás  |
|------------------|-------------|
|Microsoft Azure-csomag   | Lehetővé teszi a felhasználók számára olyan előfizetések létrehozását, amelyek bármilyen számítási feladatot futtathatnak.  |
|Microsoft Azure-csomag fejlesztéshez/teszteléshez | Lehetővé teszi a Visual Studio-előfizetők számára olyan előfizetések létrehozását, amelyek fejlesztési és tesztelési számítási feladatokra vannak korlátozva. Ezek az előfizetések olyan kedvezményeket kapnak, mint az alacsonyabb díjak és az exkluzív virtuálisgép-rendszerképekhez való hozzáférés az Azure Portalon. |

## <a name="invoice-sections"></a>Számlaszakaszok

Számlaszakaszokat hozhat létre a számlán szereplő költségek rendszerezéséhez. Előfordulhat például, hogy a szervezetben csak egy számlára van szüksége, azonban szeretné részlegek, csapatok vagy projektek szerint rendezni a költségeket. Ebben a forgatókönyvben egyetlen számlázási profillal rendelkezik, ahol létrehozhat számlaszakaszokat az egyes részlegek, csapatok vagy projektek számára.

Egy számlaszakasz létrehozásakor engedélyt adhat másoknak a szakaszon keresztül számlázott Azure-előfizetések létrehozására. Ezután az előfizetések használati díjainak és vásárlásainak számlázása az adott szakaszra történik.

A számlaszakasz szerepkörei jogosultak szabályozni, ki hozhat létre Azure-előfizetéseket. Ezeket a szerepköröket hozzárendelheti azokhoz a felhasználókhoz, akik Azure-környezeteket hoznak létre a szervezet csapatai számára, például a mérnöki vezetőkhöz és a rendszertervezőkhöz. További információkért lásd [a számlaszakasz szerepköreit és azok feladatait](billing-understand-mca-roles.md#invoice-section-roles-and-tasks) ismertető részt.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

A számlázási fiókkal kapcsolatos tudnivalókért tekintse meg az alábbi cikkeket:

- [A Microsoft-ügyfélszerződéshez tartozó felügyeleti szerepkörök ismertetése az Azure-ban](billing-understand-mca-roles.md)
- [További Azure-előfizetés létrehozása a Microsoft-ügyfélszerződésben](billing-mca-create-subscription.md)
- [Szakaszok létrehozása a számlán a költségek rendszerezéséhez](billing-mca-section-invoice.md)
