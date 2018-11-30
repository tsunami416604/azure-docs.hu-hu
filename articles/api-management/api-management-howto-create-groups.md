---
title: Csoportok használata az Azure API Management fejlesztői fiókok kezelése |} A Microsoft Docs
description: Csoportok használata az Azure API Management fejlesztői fiókok kezelése
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 5392cf5463dd0b11d1ce53856c8e4e2e788892b0
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442413"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Hogyan hozhat létre és csoportok használata az Azure API Management fejlesztői fiókok kezelése

Az API Management szolgáltatásban csoportok használatával szabályozható a fejlesztők hozzáférése a termékhez. Termékek először végzett csoportok számára látható, és ezeket a csoportokat a fejlesztők megtekintheti és előfizethet azokra a termékekre a csoportokhoz társított majd. 

Az API Management az alábbi megváltoztathatatlan rendszercsoportokkal rendelkezik:

* **Rendszergazdák** – A csoportot Azure-előfizető rendszergazdák alkotják. A rendszergazdák kezelik az API Management szolgáltatáspéldányokat, valamint ők hozzák létre az API-kat, a műveleteket és a fejlesztők által használt termékeket.
* **Fejlesztők** – A fejlesztői portál hitelesített felhasználói tartoznak ebbe a csoportba. A fejlesztők olyan ügyfelek, akik alkalmazásokat hoznak létre az API-k segítségével. A fejlesztők hozzáférhetnek a fejlesztői portálhoz, és olyan alkalmazásokat készíthetnek, amelyek egy API műveleteit hívják meg.
* **Vendégek** – A fejlesztői portál nem hitelesített felhasználói tartoznak ebbe a csoportba, például az egyik API Management példány fejlesztői portálját meglátogató leendő ügyfelek. A vendégek kaphatnak bizonyos szintű, csak olvasási hozzáférést, például megtekinthetnek API-kat, de nem hívhatják meg őket.

Ezeken a rendszercsoportokon kívül a rendszergazdák létrehozhatnak egyéni csoportokat vagy [kihasználhatja a társított Azure Active Directory-bérlők külső csoportjait][leverage external groups in associated Azure Active Directory tenants]. A fejlesztők mellett az egyéni és külső csoportoknak is lehet adni láthatóságot és hozzáférést az API-termékekhez. Például egy adott partnerszervezet fejlesztői számára létre lehet hozni egy egyéni csoportot, és hozzáférést lehet nekik biztosítani a megfelelő API-kat tartalmazó termék API-jaihoz. Egy felhasználó egyszerre több csoport tagja is lehet.

Ez az útmutató ismerteti, hogyan az API Management példányok rendszergazdái új csoportok hozzáadása és rendelje azokat termékek és a fejlesztők számára.

Csoportok létrehozása és kezelése a közzétevő portálon, valamint létrehozhat és kezelheti a csoportokat az API Management REST API használatával [csoport](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entitás.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben a feladatokat: [Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>Hozzon létre egy csoportot

Ez a szakasz bemutatja, hogyan adhat hozzá egy új csoportot az API Management-fiókhoz.

1. Válassza ki a **csoportok** lapon, a képernyő bal oldalán.
2. Kattintson a **+ Hozzáadás**.
3. Adjon meg egy egyedi nevet a csoport és a egy leírást.
4. Nyomja meg a **Create** (Létrehozás) gombot.

    ![Új csoport hozzáadása](./media/api-management-howto-create-groups/groups001.png)

A csoport létrehozása után felkerül a **csoportok** listája. <br/>Szerkesztheti a **neve** vagy **leírása** a csoporthoz, kattintson a csoport nevét és **beállítások**.<br/>A csoport törléséhez kattintson a nevére, a csoportot, majd nyomja le **törlése**.

Most, hogy a csoport jön létre, termékek és a fejlesztők társított lehet.

## <a name="associate-group-product"> </a>Csoport hozzárendelése egy termékkel

1. Válassza ki a **termékek** lap bal oldalán.
2. Kattintson a kívánt termék nevét.
3. Nyomja meg **hozzáférés-vezérlés**.
4. Kattintson a **+ csoport hozzáadása**.

    ![Csoport hozzárendelése egy termékkel](./media/api-management-howto-create-groups/groups002.png)
5. Válassza ki a hozzáadni kívánt csoportot.

    ![Csoport hozzárendelése egy termékkel](./media/api-management-howto-create-groups/groups003.png)

    Csoport eltávolítása a termékből, kattintson a **törlése**.

    ![Csoport törlése](./media/api-management-howto-create-groups/groups004.png)

Miután egy csoport társítva egy terméket, fejlesztők számára, hogy a csoportban megtekintheti, és iratkozzon fel a termék.

> [!NOTE]
> Azure Active Directory-csoportok hozzáadása, lásd: [hogyan engedélyezheti az Azure Active Directory használatával az Azure API Management fejlesztői fiókok](api-management-howto-aad.md).

## <a name="associate-group-developer"> </a>Csoportok társítása a fejlesztőkhöz

Ez a szakasz bemutatja, hogyan csoportok társítása a tagokat.

1. Válassza ki a **csoportok** lapon, a képernyő bal oldalán.
2. Válassza ki **tagok**.

    ![Tag hozzáadása](./media/api-management-howto-create-groups/groups005.png)
3. Nyomja meg **+ Hozzáadás** , és jelöljön ki egy tagot.

    ![Tag hozzáadása](./media/api-management-howto-create-groups/groups006.png)
4. Nyomja meg **kiválasztása**.

A fejlesztői és a csoport között. a társítás hozzáadása után megtekintheti azt a **felhasználók** fülre.

## <a name="next-steps"></a>Következő lépések

* A fejlesztők egy csoporthoz való hozzáadása után megtekintheti, és előfizethet azokra a termékekre a csoport. További információkért lásd: [létrehozása és egy termék közzététele az Azure API Management][How create and publish a product in Azure API Management],
* Csoportok létrehozása és kezelése a közzétevő portálon, valamint létrehozhat és kezelheti a csoportokat az API Management REST API használatával [csoport](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entitás.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
