---
title: "Csoportok használata az Azure API Management fejlesztői fiókok kezelése |} Microsoft Docs"
description: "Csoportok használata az Azure API Management fejlesztői fiókok kezelése"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: apimpm
ms.openlocfilehash: f377e1aadc126934fb47f6371f12435d2742efa6
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Hozzon létre és csoportoknak a segítségével az Azure API Management fejlesztői fiókok kezelése
Az API Management szolgáltatásban csoportok használatával szabályozható a fejlesztők hozzáférése a termékhez. Termékek vannak először láthatóvá válnak az csoportok, és ezeket a csoportokat a fejlesztők megtekintheti és a termékek, a csoportokhoz tartozó előfizetés majd. 

Az API Management az alábbi megváltoztathatatlan rendszercsoportokkal rendelkezik:

* **Rendszergazdák** – A csoportot Azure-előfizető rendszergazdák alkotják. A rendszergazdák kezelik az API Management szolgáltatáspéldányokat, valamint ők hozzák létre az API-kat, a műveleteket és a fejlesztők által használt termékeket.
* **Fejlesztők** – A fejlesztői portál hitelesített felhasználói tartoznak ebbe a csoportba. A fejlesztők olyan ügyfelek, akik alkalmazásokat hoznak létre az API-k segítségével. A fejlesztők hozzáférhetnek a fejlesztői portálhoz, és olyan alkalmazásokat készíthetnek, amelyek egy API műveleteit hívják meg.
* **Vendégek** – A fejlesztői portál nem hitelesített felhasználói tartoznak ebbe a csoportba, például az egyik API Management példány fejlesztői portálját meglátogató leendő ügyfelek. A vendégek kaphatnak bizonyos szintű, csak olvasási hozzáférést, például megtekinthetnek API-kat, de nem hívhatják meg őket.

A rendszer csoportokban mellett a rendszergazdák egyéni csoportot hozhat létre vagy [kihasználja a társított Azure Active Directory-bérlők külső csoportok][leverage external groups in associated Azure Active Directory tenants]. A fejlesztők mellett az egyéni és külső csoportoknak is lehet adni láthatóságot és hozzáférést az API-termékekhez. Például egy adott partnerszervezet fejlesztői számára létre lehet hozni egy egyéni csoportot, és hozzáférést lehet nekik biztosítani a megfelelő API-kat tartalmazó termék API-jaihoz. Egy felhasználó egyszerre több csoport tagja is lehet.

Ez az útmutató bemutatja, hogyan API Management példány rendszergazdák új csoportok hozzáadása és rendelheti őket hozzá a termékek és a fejlesztők számára.

Csoportok létrehozása és kezelése a közzétevő portálon, mellett hozhat létre és az API Management REST API használatával csoportok kezelése [csoport](https://msdn.microsoft.com/library/azure/dn776329.aspx) entitás.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feladat-: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"></a>Hozzon létre egy csoportot

Ez a szakasz bemutatja, hogyan hozzá egy új csoportot az API Management-fiókjába.

1. Válassza ki **csoportok** lap bal oldalán a képernyőn.
2. Kattintson a **+ Hozzáadás**.
3. Adjon meg egy egyedi nevet a csoport és opcionális leírását.
4. Nyomja meg a **Create** (Létrehozás) gombot.

    ![Új csoport hozzáadása](./media/api-management-howto-create-groups/groups001.png)

A csoport létrehozása után kerül a **csoportok** listája. <br/>A módosítandó a **neve** vagy **leírás** csoportjában kattintson a csoport nevét és **beállítások**.<br/>A csoport törléséhez kattintson a nevére, a csoport, és nyomja le az **törlése**.

Most, hogy a csoport jön létre, termékek és a fejlesztők kapcsolódó lehet.

## <a name="associate-group-product"></a>Társítsa a termék egy csoportot

1. Válassza ki **termékek** lap bal oldalán.
2. Kattintson a kívánt termék nevét.
3. Nyomja le az **hozzáférés-vezérlés**.
4. Kattintson a **+ csoport hozzáadása**.

    ![Új csoport hozzáadása](./media/api-management-howto-create-groups/groups002.png)
5. Válassza ki a hozzáadni kívánt csoportot.

    ![Új csoport hozzáadása](./media/api-management-howto-create-groups/groups003.png)

    Távolítsa el a csoportot a terméket, kattintson a **törlése**.

    ![Csoport törlése](./media/api-management-howto-create-groups/groups004.png)

Ha a termék egy csoporthoz tartozik, fejlesztők csoport megtekintheti és előfizetni a termék.

> [!NOTE]
> Azure Active Directory-csoportok hozzáadása, lásd: [hogyan szeretné engedélyekkel felruházni fejlesztői fiókok Azure Active Directory használatával az Azure API Management](api-management-howto-aad.md).

## <a name="associate-group-developer"></a>Csoportok társítani a fejlesztők számára

Ez a szakasz bemutatja, hogyan rendelje hozzá a csoportok tagjai.

1. Válassza ki **csoportok** lap bal oldalán a képernyőn.
2. Válassza ki **tagok**.

    ![Tag hozzáadása](./media/api-management-howto-create-groups/groups005.png)
3. Nyomja le az **+ Hozzáadás** , és jelölje ki a tagot.

    ![Tag hozzáadása](./media/api-management-howto-create-groups/groups006.png)
4. Nyomja le az **válasszon**.


A fejlesztői és a csoport között. a társítás hozzáadása után megtekintheti azokat a **felhasználók** fülre.

## <a name="next-steps"></a>Következő lépések
* Egy fejlesztő csoporthoz hozzáadott, tekinthet meg és a termékek, a csoport előfizetni. További információkért lásd: [hogyan létrehozása, és a termék közzététele az Azure API Management][How create and publish a product in Azure API Management],
* Csoportok létrehozása és kezelése a közzétevő portálon, mellett hozhat létre és az API Management REST API használatával csoportok kezelése [csoport](https://msdn.microsoft.com/library/azure/dn776329.aspx) entitás.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
