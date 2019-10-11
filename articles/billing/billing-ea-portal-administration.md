---
title: A nagyvállalati szerződéses Azure Portal adminisztrációja
description: Ez a cikk a rendszergazdák Azure EA Portalon elvégzendő gyakori feladatait ismerteti.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 21e9d4af783ed5d9eb3ace1c8b5189163b89f8b0
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035221"
---
# <a name="azure-ea-portal-administration"></a>A nagyvállalati szerződéses Azure Portal adminisztrációja

Ez a cikk a rendszergazdák Azure EA Portalon (https://ea.azure.com) ) elvégzendő gyakori feladatait ismerteti. Az Azure EA Portal egy online felügyeleti portál, amely segítséget nyújt az ügyfeleknek az Azure EA-szolgáltatások költségének kezelésében. Az Azure EA Portallal kapcsolatos bevezető információkért lásd: [Ismerkedés az Azure EA Portallal](billing-ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Új vállalati rendszergazda hozzáadása

Az Azure EA-regisztrációk kezelésénél a vállalati rendszergazdák rendelkeznek a legtöbb jogosultsággal. Az első Azure EA-rendszergazdát a nagyvállalati szerződés megkötésekor hozták létre. Viszont bármikor hozzá lehet adni új rendszergazdákat, vagy el is lehet távolítani őket. Az új rendszergazdákat csak a meglévő rendszergazdák adhatják hozzá. A vállalati rendszergazdák hozzáadásával kapcsolatos további információkért lásd az [új vállalati rendszergazda létrehozását](billing-ea-portal-get-started.md#create-another-enterprise-admin) ismertető részt. A számlázási profil szerepköreivel és feladataival kapcsolatos további információkért lásd [a számlázási profil szerepköreinek és azok feladatainak ismertetését](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>A felhasználói állapot frissítése Függőben értékről Aktív értékre

Amikor először hozzáadják az új fióktulajdonosokat (AO) egy Azure EA-regisztrációhoz, a _függőben_ állapot jelenik meg náluk. Amikor az új fióktulajdonosok megkapják az aktiváló e-mailt, be tudnak jelentkezni a fiókjuk aktiválásához. A fiókjuk aktiválásakor a fiók állapota _függőben_ értékről _aktív_ értékre módosul. A rendszer kérheti az új felhasználók vezeték- és utónevét egy Kereskedelmi fiók létrehozásához. Ha kéri, akkor meg kell adniuk a szükséges adatokat a folytatáshoz, majd megtörténik a fiók aktiválása.

## <a name="add-a-department-admin"></a>Részlegszintű rendszergazda hozzáadása

Ha az egyik Azure EA-rendszergazda létrehozott egy részleget, az Azure vállalati rendszergazda részlegszintű rendszergazdákat adhat hozzá, és hozzárendelheti őket egy-egy részleghez. A részlegszintű rendszergazdák új fiókokat hozhatnak létre. Az Azure EA-előfizetések létrehozásához új fiókokra van szükség.

A részlegszintű rendszergazdák hozzáadásával kapcsolatos további információkért tekintse meg az [Azure EA-részlegszintű rendszergazdák létrehozását](billing-ea-portal-get-started.md#add-a-department-admin) ismertető témakört.

## <a name="enterprise-user-roles"></a>Vállalati felhasználók szerepkörei

Az Azure EA Portal segítséget nyújt az Azure EA-költségek és -használat felügyeletéhez. Az Azure EA Portalon három fő szerepkör van:

- EA-rendszergazda
- Részlegszintű rendszergazda
- Fióktulajdonos

Minden szerepkör különböző szintű hozzáféréssel és jogosultsággal rendelkezik.

A felhasználói szerepkörökkel kapcsolatos további információkért lásd: Vállalati felhasználók szerepkörei.

## <a name="add-an-azure-ea-account"></a>Azure EA-fiók hozzáadása

Az Azure EA-fiók az Azure EA Portal szervezeti egysége, amelyet az előfizetések felügyeletére, valamint jelentéskészítésre használnak. Az Azure-szolgáltatások eléréséhez és használatához létre kell hoznia vagy hozatnia egy fiókot.

Az Azure-fiókokkal kapcsolatos további információkért lásd: Fiók hozzáadása.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Vállalati fiók átvitele egy új regisztrációba

Amikor vállalati fiókokat visz át egy új regisztrációba, tartsa szem előtt a következőket:

- A rendszer csak a kérelemben megadott fiókokat viszi át. Ha az összes fiókot kiválasztotta, mindet át fogja vinni.
- A forrásregisztráció megőrzi az állapotát (aktív vagy meghosszabbítva). Tovább folytathatja a használatát, amíg le nem jár.

### <a name="effective-transfer-date"></a>Átvitel hatálybalépési dátuma

Az átvitel hatálybalépési dátuma lehet az átvitel céljaként kiválasztott regisztráció kezdő dátuma, vagy egy későbbi időpont. Az átvitel céljaként kiválasztott regisztráció a _célregisztráció_. A fiók átvitele után a fiókban lévő, átvitel dátuma előtti használati adatok abban a regisztrációban maradnak, amelyből átviszi a fiókot. Az átvitel forrásaként szolgáló regisztráció a _forrásregisztráció_.  A forrásregisztráció használatának költsége a pénzügyi keretből lesz levonva, vagy túlhasználatként lesz kiszámlázva. Az átvitel dátuma utáni használat átkerül az új regisztrációba, és ennek megfelelően kell majd érte fizetni.

A fiókátvitelt legfeljebb a célregisztráció kezdő dátumáig lehet visszakeltezni. Vagy legfeljebb a forrásregisztráció hatálybalépési dátumáig.

### <a name="monetary-commitment"></a>Pénzügyi keret

A pénzügyi keretet nem lehet átvinni egyik regisztrációból a másikba. A pénzügyi keret egyenlegeit a szerződés ahhoz a regisztrációhoz köti, amelyhez megrendelték őket. A rendszer nem viszi át a pénzügyi keretet a fiók vagy a regisztráció átviteli folyamatának részeként.

### <a name="services-affected"></a>Érintett szolgáltatások

A fiók átvitele során nincs állásidő. Ha az összes szükséges információt megadja, a kérelem benyújtásának napján is el lehet végezni.

### <a name="prerequisites"></a>Előfeltételek

A fiókátvitel kérésekor adja meg az alábbi adatokat:


- Az átvinni kívánt fiók fióknevét és a fióktulajdonos azonosítóját
- A forrásregisztrációhoz a regisztrációs számot és az átvinni kívánt fiókot
- A célregisztrációhoz az átvitel céljának regisztrációs számát
- A fiókátvitel hatálybalépési dátuma lehet a célregisztráció kezdő dátuma, vagy egy későbbi időpont

Egyéb szempontok, amelyeket érdemes észben tartani a fiókok átvitele előtt:

- A cél- és a forrásregisztráció esetében is szükség van egy EA-rendszergazda jóváhagyására
  - Néhány esetben a Microsoft további jóváhagyást kérhet a forrásregisztráció egyik EA-rendszergazdájától
- Ha egy fiókátvitel nem felel meg az elvárásainak, vegye fontolóra a regisztráció átvitelét.
- A fiókátvitel az adott fiókokhoz tartozó összes szolgáltatást és előfizetést átviszi.
- Az átvitel befejezését követően az átvitt fiók inaktívként jelenik meg a forrásregisztráció alatt.
- A fiókátvitelt a célregisztráció kezdő dátumáig lehet visszakeltezni.
- A fiók a záró dátumot a forrásregisztráció hatályos átviteli dátumaként jelenít meg és a célregisztráció kezdő dátumaként.
- A fiók esetében a hatályos átviteli dátum előtti bármilyen használat a forrásregisztrációhoz tartozik.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Vállalati regisztráció átvitele egy új regisztrációba

Ha egy teljes vállalati regisztráció átvitelét kéri egy regisztrációba, a következő műveletek mennek végbe:

- A rendszer az összes Azure-szolgáltatást, előfizetést, fiókot, részleget, valamint a teljes regisztrációs struktúrát átviszi, az EA-részlegek rendszergazdáival együtt.
- A regisztráció állapota _Átvitt_ értékre módosul. Az átvitt regisztrációt csak a korábbi használatról szóló jelentések elkészítéséhez lehet elérni.
- Az átvitt regisztrációkhoz nem adhat hozzá szerepköröket vagy előfizetéseket. Az átvitt állapot megakadályozza a regisztráció további használatának felszámítását.
- A szerződés pénzügyi keretének fennmaradó egyenlege elveszik, a jövőbeli időszakokkal együtt.

### <a name="effective-transfer-date"></a>Átvitel hatálybalépési dátuma

Az átvitel hatályba lépésének napja lehet a célregisztrációba átvinni kívánt regisztráció kezdő dátuma, vagy egy későbbi időpont.

A forrásregisztráció használatának költsége a pénzügyi keretből lesz levonva, vagy túlhasználatként lesz kiszámlázva. Az átvitel dátuma utáni használat átkerül az új regisztrációba, és ennek megfelelően kell majd érte fizetni.

### <a name="effective-transfer-date-in-the-past"></a>Átvitel múltbéli hatálybalépési dátuma

A fiókátvitelt legfeljebb a célregisztráció kezdő dátumáig lehet visszakeltezni. Vagy legfeljebb forrásregisztráció hatálybalépése dátumáig.

### <a name="monetary-commitment"></a>Pénzügyi keret

A pénzügyi keretet nem lehet átvinni egyik regisztrációból a másikba. A pénzügyi keret egyenlegeit a szerződés ahhoz a regisztrációhoz köti, amelyhez megrendelték őket. A rendszer nem viszi át a pénzügyi keretet a fiók vagy a regisztráció átviteli folyamatának részeként.

### <a name="services-affected"></a>Érintett szolgáltatások

A fiók átvitele során nincs állásidő. Ha az összes szükséges információt megadja, a kérelem benyújtásának napján is el lehet végezni.

### <a name="prerequisites"></a>Előfeltételek

A regisztrációátvitel kérésekor adja meg az alábbi adatokat:

- A forrásregisztrációhoz a regisztrációs számot és az átvinni kívánt fiókot.
- A célregisztrációhoz az átvitel céljának regisztrációs számát.
- A regisztrációátvitel hatálybalépési dátuma lehet a célregisztráció kezdő dátuma vagy egy későbbi időpont. A kiválasztott dátum nem lehet hatással a már kiállított túlhasználati számlákban feltüntetett használatra.

Egyéb szempontok, amelyeket érdemes észben tartani a regisztrációk átvitele előtt:

- A cél- és a forrásregisztráció esetében is szükség van egy EA-rendszergazda jóváhagyására.
  - Néhány esetben a Microsoft további jóváhagyást kérhet a forrásregisztráció egyik EA-rendszergazdájától.
- Ha egy regisztrációátvitel nem felel meg az elvárásainak, vegye fontolóra a fiókátvitelt.
- A rendszer csak a megadott fiókokat viszi át. Az összes fiók átvitelét is kérheti.
- A forrásregisztráció megőrzi az állapotát (aktív/meghosszabbítva). Tovább folytathatja a használatát, amíg le nem jár.

## <a name="change-account-owner"></a>Fiók tulajdonosának módosítása

Az Azure EA Portalon át lehet adni az előfizetéseket egyik fióktulajdonostól a másiknak. További információért lásd: [Fiók tulajdonosának módosítása](billing-ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Az előfizetés-áthelyezés hatásai

Ha az Azure-előfizetést ugyanazon Azure AD-bérlő egy másik fiókjába helyezi át, akkor az összes [szerepköralapú hozzáférés-vezérléssel (RBAC)](../role-based-access-control/overview.md) rendelkező felhasználó, csoport és szolgáltatásnév megtartja az erőforrások kezeléséhez való hozzáférését.

Az előfizetéshez RBAC-hozzáféréssel rendelkező felhasználók megtekintése:

1. Az Azure Portalon nyissa meg az **Előfizetések** oldalt.
2. Válassza ki a megtekinteni kívánt előfizetést, majd válassza a **Hozzáférés-vezérlés (IAM)** elemet.
3. Válassza a **Szerepkör-hozzárendelések** lehetőséget. A szerepkör-hozzárendelések oldala felsorolja az összes felhasználót, aki RBAC-hozzáféréssel rendelkezik az előfizetéshez.

Ha az előfizetést egy másik Azure AD-bérlő fiókjába helyezi át, akkor az összes [RBAC-vel](../role-based-access-control/overview.md) rendelkező felhasználó, csoport és szolgáltatásnév _elveszíti_ a hozzáférését az erőforrások kezeléséhez. Bár nincs RBAC-hozzáférés, mégis előfordulhat, hogy az előfizetést el lehet érni biztonsági mechanizmusokon keresztül, például:

- Felügyeleti tanúsítványok, amelyek rendszergazdai jogosultságokat biztosítanak a felhasználónak az előfizetés erőforrásaihoz. További információért lásd: [Felügyeleti tanúsítvány létrehozása és feltöltése az Azure szolgáltatáshoz](../cloud-services/cloud-services-certs-create.md).
- A Storage-hoz hasonló szolgáltatások hozzáférési kulcsai. További információkat az [Azure Storage-fiókok áttekintésében](../storage/common/storage-account-overview.md) találhat.
- Az olyan szolgáltatások távelérési hitelesítő adatai, mint az Azure-beli virtuális gépek.

Ha a címzettnek korlátoznia kell a hozzáférését az Azure-erőforrásokhoz, érdemes megfontolnia a szolgáltatáshoz társított titkok frissítését. A legtöbb erőforrást a következő lépésekkel lehet frissíteni:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben válassza a **Minden erőforrás** elemet.
3. Válassza ki az erőforrást.
4. Az erőforrás oldalon kattintson a **Beállítások** elemre a meglévő titkos kulcsok megtekintéséhez és frissítéséhez.



## <a name="close-an-azure-enterprise-enrollment"></a>Azure nagyvállalati regisztráció lezárása

Ha le szeretné zárni az Azure EA-regisztrációját, a következőket teheti:

- Szüntesse meg az Azure EA-hoz társított előfizetéseket az Azure Portalon.
- Lépjen kapcsolatba az ügyfélszoftver-tanácsadójával vagy a partnerével, és kérje meg őket, hogy zárják le az Azure Nagyvállalati Szerződést.

## <a name="update-notification-settings"></a>Frissítési értesítés beállításai

A rendszer automatikusan regisztrálja a vállalati rendszergazdákat, hogy megkapják a regisztrációjukkal kapcsolatos használati értesítéseket. A vállalati rendszergazdák módosíthatják az értesítések időközét, vagy teljesen ki is kapcsolhatják őket.

Az értesítési kapcsolattartók az Azure EA Portal **Értesítési kapcsolattartó** területén jelennek meg. Az értesítési kapcsolattartók kezelésével gondoskodhat arról, hogy a szervezet megfelelő tagjai kapják meg az Azure EA-értesítéseket.

Az aktuális értesítési beállítások megtekintése:

1. Az Azure EA Portalon lépjen a **Kezelés** > **Értesítési kapcsolattartó** elemre.
2. E-mail-cím – A vállalati rendszergazda Microsoft-fiókjához, illetve munkahelyi vagy iskolai fiókjához társított e-mail-cím, amelyre a rendszer az értesítéseket küldi.
3. Számlázatlan egyenlegről szóló értesítés gyakorisága – Az egyes vállalati rendszergazdáknak küldött értesítésekhez beállított gyakoriság.

Kapcsolattartó hozzáadása:

1. Kattintson a **+Kapcsolattartó hozzáadása** elemre.
2. Írja be az e-mail-címet, majd erősítse meg.
3. Kattintson a **Save** (Mentés) gombra.

Az új értesítési kapcsolattartó megjelenik az **Értesítendő fél** területen. Az értesítés gyakoriságának módosításához válassza ki az értesítendő felet, majd kattintson a kiválasztott sor jobb oldalán található ceruza szimbólumra. Állítsa be a gyakorisághoz a **naponta**, **hetente**, **havonta** vagy a **nincs** értéket.

Figyelmen kívül hagyhatja a _közelgő fedezeti időszak záró dátumát_, valamint _letilthatja és megszüntetheti a közelgő dátummal_ kapcsolatos életciklus-értesítéseket. Az életciklus-értesítések letiltásával figyelmen kívül hagyja a fedezeti időszakról és a szerződés záró dátumáról szóló értesítéseket.

## <a name="manage-partner-notifications"></a>A partnerértesítések kezelése

A partnerrendszergazdák beállíthatják, hogy milyen gyakran kapjanak a regisztrációikkal kapcsolatos használati értesítéseket. A rendszer minden héten automatikusan értesítéseket küld nekik a számlázatlan egyenlegükről. Módosíthatják az értesítések gyakoriságát havonta, hetente, vagy naponta értékre, vagy teljesen ki is kapcsolhatják őket.

Ha a felhasználó nem kap értesítést, a következő lépésekkel ellenőrizze, hogy helyesek-e a felhasználó értesítési beállításai.

1. Jelentkezzen be az Azure EA Portalra partnerrendszergazdaként.
2. Kattintson a **Kezelés**, majd a **Partner** elemre.
3. Tekintse meg a rendszergazdák listáját a **Rendszergazda** területen.
4. Az értesítési beállítások szerkesztéséhez vigye a mutatót a megfelelő rendszergazda fölé, és kattintson a ceruza szimbólumra.
5. Állítsa be igény szerint az értesítési gyakoriságot és az életciklus-értesítéseket.
6. Szükség esetén adjon hozzá egy kapcsolattartót, és kattintson a **Hozzáadás** gombra.
7. Kattintson a **Save** (Mentés) gombra.

![A Kapcsolattartó hozzáadása panelt mutató példa ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="azure-sponsorship-offer"></a>Azure Sponsorship-ajánlat
Az Azure Sponsorship-ajánlat korlátozott számú szponzorált Microsoft Azure-fiókot takar. A Microsoft által kiválasztott, korlátozott számú ügyfél az e-mailben kapott meghívóval csatlakozhat hozzá. Ha Ön jogosult a Microsoft Azure Sponsorship-ajánlatára, akkor kapni fog egy e-mailes meghívót a fiókazonosítójára.
További információkért lásd:

- Szponzorajánlat áttekintése – https://azure.microsoft.com/en-us/offers/ms-azr-0143p/
- Szponzorálás egyenlege portál – https://www.microsoftazuresponsorships.com/balance  
- Szponzorálás – külső GYIK – https://azuresponsorships-staging.azurewebsites.net/faq
- Támogatási kérés a szponzorálás aktiválásához – http://aka.ms/azrsponsorship

## <a name="next-steps"></a>További lépések
- Olvassa el, hogyan takaríthat meg pénzt a [virtuális gépek lefoglalásával](billing-ea-portal-vm-reservations.md).
- Ha segítségre van szüksége az Azure EA Portallal kapcsolatos hibák elhárításához, olvassa el a következő részt: [A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása](billing-ea-portal-troubleshoot.md).
