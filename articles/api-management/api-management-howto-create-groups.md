---
title: Fejlesztői fiókok kezelése csoportok használatával az Azure API Management ben
titleSuffix: Azure API Management
description: Ismerje meg, hogyan kezelheti a fejlesztői fiókokat az Azure API Management csoportokkal
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 182f0ea93ddfb2ab64de1c15b36958537fa5431f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430710"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Csoportok létrehozása és használata fejlesztői fiókok kezeléséhez az Azure API Managementben

Az API Management szolgáltatásban csoportok használatával szabályozható a fejlesztők hozzáférése a termékhez. A termékek először láthatóvá válnak a csoportok számára, majd a csoportok fejlesztői megtekinthetik és előfizethetnek a csoportokhoz társított termékekre. 

Az API Management az alábbi megváltoztathatatlan rendszercsoportokkal rendelkezik:

* **Rendszergazdák** – A csoportot Azure-előfizető rendszergazdák alkotják. A rendszergazdák kezelik az API Management szolgáltatáspéldányokat, valamint ők hozzák létre az API-kat, a műveleteket és a fejlesztők által használt termékeket.
* **Fejlesztők** – A fejlesztői portál hitelesített felhasználói tartoznak ebbe a csoportba. A fejlesztők olyan ügyfelek, akik alkalmazásokat hoznak létre az API-k segítségével. A fejlesztők hozzáférhetnek a fejlesztői portálhoz, és olyan alkalmazásokat készíthetnek, amelyek egy API műveleteit hívják meg.
* **Vendégek** – A fejlesztői portál nem hitelesített felhasználói tartoznak ebbe a csoportba, például az egyik API Management példány fejlesztői portálját meglátogató leendő ügyfelek. A vendégek kaphatnak bizonyos szintű, csak olvasási hozzáférést, például megtekinthetnek API-kat, de nem hívhatják meg őket.

Ezeken a rendszercsoportokon kívül a rendszergazdák létrehozhatnak egyéni csoportokat vagy [áttelepíthetik a társított Azure Active Directory-bérlők külső csoportjait is][leverage external groups in associated Azure Active Directory tenants]. A fejlesztők mellett az egyéni és külső csoportoknak is lehet adni láthatóságot és hozzáférést az API-termékekhez. Például egy adott partnerszervezet fejlesztői számára létre lehet hozni egy egyéni csoportot, és hozzáférést lehet nekik biztosítani a megfelelő API-kat tartalmazó termék API-jaihoz. Egy felhasználó egyszerre több csoport tagja is lehet.

Ez az útmutató bemutatja, hogy egy API Management-példány rendszergazdái hogyan vehetnek fel új csoportokat, és társíthatják őket a termékekhez és a fejlesztőkhez.

A csoportok létrehozása és kezelése mellett a publisherportálon is létrehozhat és kezelhet csoportokat az API Management REST API [Group](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entitás használatával.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben szereplő feladatok végrehajtása: [Hozzon létre egy Azure API Management-példányt.](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-group"></a><a name="create-group"> </a>Csoport létrehozása

Ez a szakasz bemutatja, hogyan vehet fel egy új csoportot az API Management-fiókhoz.

1. Válassza a képernyő bal oldalán található **Csoportok** lapot.
2. Kattintson **a +Hozzáadás gombra.**
3. Adja meg a csoport egyedi nevét és a nem kötelező leírást.
4. Nyomja meg a **Create** (Létrehozás) gombot.

    ![Új csoport hozzáadása](./media/api-management-howto-create-groups/groups001.png)

A csoport létrehozása után a csoport felkerül a **Csoportok listára.** <br/>A csoport **nevének** vagy **leírásának** szerkesztéséhez kattintson a csoport nevére és a **Beállítások parancsra.**<br/>A csoport törléséhez kattintson a csoport nevére, és nyomja le a **Delete billentyűt.**

Most, hogy a csoport létrejött, a termékekhez és a fejlesztőkhöz társítható.

## <a name="associate-a-group-with-a-product"></a><a name="associate-group-product"> </a>Csoport társítása termékhez

1. Válassza a Bal oldalon a **Termékek** lapot.
2. Kattintson a kívánt termék nevére.
3. Nyomja **meg a Hozzáférés vezérlését**.
4. Kattintson **a + Csoport hozzáadása gombra.**

    ![Csoport társítása termékhez](./media/api-management-howto-create-groups/groups002.png)
5. Jelölje ki a hozzáadni kívánt csoportot.

    ![Csoport társítása termékhez](./media/api-management-howto-create-groups/groups003.png)

    Ha el szeretne távolítani egy csoportot a termékből, kattintson a **Törlés gombra.**

    ![Csoport törlése](./media/api-management-howto-create-groups/groups004.png)

Miután egy termék társítva van egy csoporthoz, a csoport fejlesztői megtekinthetik és előfizethetnek a termékre.

> [!NOTE]
> Az Azure Active Directory-csoportok hozzáadásáról a [Fejlesztői fiókok engedélyezése az Azure Active Directory használatával az Azure API Management szolgáltatásban](api-management-howto-aad.md).to add Azure Active Directory groups, in

## <a name="associate-groups-with-developers"></a><a name="associate-group-developer"> </a>Csoportok társítása fejlesztőkkel

Ez a szakasz bemutatja, hogyan társíthatók csoportok a tagokhoz.

1. Válassza a képernyő bal oldalán található **Csoportok** lapot.
2. Válassza ki a **Members** (Tagok) elemet.

    ![Tag felvétele](./media/api-management-howto-create-groups/groups005.png)
3. Nyomja **le a +Hozzáadás gombot,** és jelöljön ki egy tagot.

    ![Tag felvétele](./media/api-management-howto-create-groups/groups006.png)
4. Nyomja **le a Kijelölés gombot.**

Miután hozzáadta a társítást a fejlesztő és a csoport között, megtekintheti azt a **Felhasználók** lapon.

## <a name="next-steps"></a><a name="next-steps"> </a>További lépések

* Miután egy fejlesztőt hozzáadtak egy csoporthoz, megtekintheti és előfizethet az adott csoporthoz társított termékekre. További információ: [Termék létrehozása és közzététele az Azure API Management alkalmazásban][How create and publish a product in Azure API Management],
* A csoportok létrehozása és kezelése mellett a publisherportálon is létrehozhat és kezelhet csoportokat az API Management REST API [Group](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entitás használatával.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
