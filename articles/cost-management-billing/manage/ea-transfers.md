---
title: Azure Enterprise-átvitelek
description: Az Azure EA-átvitelek részleteit írja le.
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: banders
ms.openlocfilehash: 140fc450623f0dcb6c7cf1bf08a8cfc43b094763
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371898"
---
# <a name="azure-enterprise-transfers"></a>Azure Enterprise-átvitelek

A cikk a vállalati átvitelekről nyújt áttekintést.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Vállalati fiók átvitele egy új regisztrációba

A fiókátvitellel egy fióktulajdonos áthelyezhető az egyik regisztrációból egy másikba. A fióktulajdonos alá tartozó összes kapcsolódó előfizetés átkerül a célregisztrációba. Akkor használjon fiókátvitelt, ha több aktív regisztráció van, és csak a kiválasztott fióktulajdonosokat szeretné áthelyezni.

Ez a szakasz csak tájékoztató célt szolgál, mert a műveletet vállalati rendszergazda nem végezheti el. Vállalati fiók új regisztrációba történő átviteléhez támogatási kérés leadása szükséges.

Amikor vállalati fiókokat visz át egy új regisztrációba, tartsa szem előtt a következőket:

- A rendszer csak a kérelemben megadott fiókokat viszi át. Ha az összes fiókot kiválasztotta, az átvitel mindegyikre vonatkozni fog.
- A forrásregisztráció megőrzi az állapotát (aktív vagy meghosszabbítva). Tovább folytathatja a használatát, amíg le nem jár.

### <a name="prerequisites"></a>Előfeltételek

A fiókátvitel kérésekor adja meg az alábbi adatokat:

- A célregisztráció számát, az átvinni kívánt fiók nevét és a fióktulajdonos e-mail-címét
- A forrásregisztrációhoz a regisztrációs számot és az átvinni kívánt fiókot

Egyéb szempontok, amelyeket érdemes észben tartani a fiókok átvitele előtt:

- A cél- és a forrásregisztráció esetében is szükség van egy EA-rendszergazda jóváhagyására
- Ha egy fiókátvitel nem felel meg az elvárásainak, vegye fontolóra a regisztráció átvitelét.
- A fiókátvitel az adott fiókokhoz tartozó összes szolgáltatást és előfizetést átviszi.
- Az átvitel befejezését követően az átvitt fiók inaktívként jelenik meg a forrásregisztráció alatt, és aktívként a célregisztráció alatt.
- A fiók a záró dátumot a forrásregisztráció hatályos átviteli dátumaként jelenít meg és a célregisztráció kezdő dátumaként.
- A fiók esetében a hatályos átviteli dátum előtti bármilyen használat a forrásregisztrációhoz tartozik.

## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Vállalati regisztráció átvitele egy új regisztrációba

A regisztrációátvitel a következő esetben megfontolandó:

- A jelenlegi regisztráció előrefizetési időtartama véget ért.
- A regisztráció lejárt/meghosszabbított állapotban van, és egy új szerződés egyeztetése folyamatban van.
- Több regisztrációval rendelkezik, és egyetlen regisztráció keretében szeretné összevonni az összes fiókot és számlázást.

Ez a szakasz csak tájékoztató célt szolgál, mert a műveletet vállalati rendszergazda nem végezheti el. Egy vállalati regisztráció egy újba történő átviteléhez támogatási kérés leadása szükséges.

Ha egy teljes vállalati regisztráció átvitelét kéri egy regisztrációba, a következő műveletek mennek végbe:

- A rendszer az összes Azure-szolgáltatást, előfizetést, fiókot, részleget, valamint a teljes regisztrációs struktúrát átviszi az új célregisztrációba, az EA-részlegek rendszergazdáival együtt.
- A regisztráció állapota _Átvitt_ értékre módosul. Az átvitt regisztrációt csak a korábbi használatról szóló jelentések elkészítéséhez lehet elérni.
- Az átvitt regisztrációkhoz nem adhat hozzá szerepköröket vagy előfizetéseket. Az átvitt állapot megakadályozza a regisztráció további használatának felszámítását.
- A szerződés fennmaradó előrefizetési Azure-egyenlege elveszik, a későbbi időszakokkal együtt.
-    Ha a regisztráció, amelyről az átvitelt végzi, rendelkezik megvásárolt fenntartott példányokkal, a fenntartott példány vételára továbbra is a forrásregisztrációban marad, azonban a fenntartott példány összes előnye az új regisztrációban lesz elérhető.
-    A Marketplace-en kifizetett egyszeri vételár és a régi regisztrációban felmerült fix havi díjak nem lesznek áthelyezve az új regisztrációba. A használatalapú Marketplace-díjak átvitele megtörténik.

### <a name="effective-transfer-date"></a>Átvitel hatálybalépési dátuma

Az átvitel hatályba lépésének napja a célregisztráció kezdődátuma vagy egy későbbi időpont lehet.

A forrásregisztráció használatának költsége az Azure-előrefizetési keretből lesz levonva, vagy túlhasználatként lesz kiszámlázva. Az átvitel dátuma utáni használat átkerül az új regisztrációba, és ennek megfelelően kell majd érte fizetni.

### <a name="prerequisites"></a>Előfeltételek

A regisztrációátvitel kérésekor adja meg az alábbi adatokat:

- A forrásregisztrációhoz a regisztrációs számot.
- A célregisztrációhoz az átvitel céljának regisztrációs számát.
- A regisztrációátvitel hatálybalépési dátuma lehet a célregisztráció kezdő dátuma vagy egy későbbi időpont. A kiválasztott dátum nem lehet hatással a már kiállított túlhasználati számlákban feltüntetett használatra.

Egyéb szempontok, amelyeket érdemes észben tartani a regisztrációk átvitele előtt:

- A cél- és a forrásregisztráció esetében is szükség van egy EA-rendszergazda jóváhagyására.
- Ha egy regisztrációátvitel nem felel meg az elvárásainak, vegye fontolóra a fiókátvitelt.
- A forrásregisztráció állapota átvitt lesz, és ezt a regisztrációt csak a korábbi használatról szóló jelentések elkészítéséhez lehet majd elérni.

### <a name="azure-prepayment"></a>Azure-előrefizetés

Az Azure-előrefizetést nem lehet átvinni egyik regisztrációból a másikba. Az Azure-előrefizetési egyenlegeket a szerződés ahhoz a regisztrációhoz köti, amelyhez megrendelték őket. A rendszer nem viszi át az Azure-előrefizetést a fiók vagy a regisztráció átviteli folyamatának részeként.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>A fiókok és a regisztrációk átvitele nem érinti a szolgáltatásokat

A fiókok és a regisztrációk átvitele során nincs állásidő. Ha az összes szükséges információt megadja, a kérelem benyújtásának napján is el lehet végezni.

## <a name="change-account-owner"></a>Fiók tulajdonosának módosítása

Az Azure EA Portalon át lehet adni az előfizetéseket egyik fióktulajdonostól a másiknak. További információért lásd: [Fiók tulajdonosának módosítása](ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Az előfizetés-áthelyezés hatásai

Ha az Azure-előfizetést ugyanazon Azure Active Directory-bérlő egy másik fiókjába helyezi át, akkor az összes [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../../role-based-access-control/overview.md) rendelkező felhasználó, csoport és szolgáltatásnév megtartja az erőforrások kezeléséhez való hozzáférését.

Az előfizetéshez Azure RBAC-hozzáféréssel rendelkező felhasználók megtekintése:

1. Az Azure Portalon nyissa meg az **Előfizetések** oldalt.
2. Válassza ki a megtekinteni kívánt előfizetést, majd válassza a **Hozzáférés-vezérlés (IAM)** elemet.
3. Válassza a **Szerepkör-hozzárendelések** lehetőséget. A szerepkör-hozzárendelések oldala felsorolja az összes felhasználót, aki Azure RBAC-hozzáféréssel rendelkezik az előfizetéshez.

Ha az előfizetést egy másik Azure AD-bérlő fiókjába helyezi át, akkor az összes [Azure RBAC-vel](../../role-based-access-control/overview.md) rendelkező felhasználó, csoport és szolgáltatásnév _elveszíti_ a hozzáférését az erőforrások kezeléséhez. Bár nincs Azure RBAC-hozzáférés, mégis előfordulhat, hogy az előfizetést el lehet érni biztonsági mechanizmusokon keresztül, például:

- Felügyeleti tanúsítványok, amelyek rendszergazdai jogosultságokat biztosítanak a felhasználónak az előfizetés erőforrásaihoz. További információért lásd: [Felügyeleti tanúsítvány létrehozása és feltöltése az Azure szolgáltatáshoz](../../cloud-services/cloud-services-certs-create.md).
- A Storage-hoz hasonló szolgáltatások hozzáférési kulcsai. További információkat az [Azure Storage-fiókok áttekintésében](../../storage/common/storage-account-overview.md) találhat.
- Az olyan szolgáltatások távelérési hitelesítő adatai, mint az Azure-beli virtuális gépek.

Ha a címzettnek korlátoznia kell a hozzáférését az Azure-erőforrásokhoz, érdemes megfontolnia a szolgáltatáshoz társított titkok frissítését. A legtöbb erőforrást a következő lépésekkel lehet frissíteni:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben válassza a **Minden erőforrás** elemet.
3. Válassza ki az erőforrást.
4. Az erőforrás oldalán válassza a **Beállítások** lehetőséget a meglévő titkos kulcsok megtekintéséhez és frissítéséhez.

## <a name="next-steps"></a>További lépések

- Ha segítségre van szüksége az Azure EA Portallal kapcsolatos hibák elhárításához, olvassa el a következő részt: [A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása](ea-portal-troubleshoot.md).