---
title: "Csoportok használata az Azure API Management fejlesztői fiókok kezelése |} Microsoft Docs"
description: "Csoportok használata az Azure API Management fejlesztői fiókok kezelése"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 33660b45-442f-44be-9a4a-1899d7f699b0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: b4d71cdfbab535b02542fbb26c7555265e5f9c37
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Hozzon létre és csoportoknak a segítségével az Azure API Management fejlesztői fiókok kezelése
Az API Management szolgáltatásban csoportok használatával szabályozható a fejlesztők hozzáférése a termékhez. Termékek vannak először láthatóvá válnak az csoportok, és ezeket a csoportokat a fejlesztők megtekintheti és a termékek, a csoportokhoz tartozó előfizetés majd. 

Az API Management az alábbi megváltoztathatatlan rendszercsoportokkal rendelkezik.

* **Rendszergazdák** – A csoportot Azure-előfizető rendszergazdák alkotják. A rendszergazdák kezelik az API Management szolgáltatáspéldányokat, valamint ők hozzák létre az API-kat, a műveleteket és a fejlesztők által használt termékeket.
* **Fejlesztők** – A fejlesztői portál hitelesített felhasználói tartoznak ebbe a csoportba. A fejlesztők olyan ügyfelek, akik alkalmazásokat hoznak létre az API-k segítségével. A fejlesztők hozzáférhetnek a fejlesztői portálhoz, és olyan alkalmazásokat készíthetnek, amelyek egy API műveleteit hívják meg.
* **Vendégek** – A fejlesztői portál nem hitelesített felhasználói tartoznak ebbe a csoportba, például az egyik API Management példány fejlesztői portálját meglátogató leendő ügyfelek. A vendégek kaphatnak bizonyos szintű, csak olvasási hozzáférést, például megtekinthetnek API-kat, de nem hívhatják meg őket.

A rendszer csoportokban mellett a rendszergazdák egyéni csoportot hozhat létre vagy [kihasználja a társított Azure Active Directory-bérlők külső csoportok][leverage external groups in associated Azure Active Directory tenants]. A fejlesztők mellett az egyéni és külső csoportoknak is lehet adni láthatóságot és hozzáférést az API-termékekhez. Például egy adott partnerszervezet fejlesztői számára létre lehet hozni egy egyéni csoportot, és hozzáférést lehet nekik biztosítani a megfelelő API-kat tartalmazó termék API-jaihoz. Egy felhasználó egyszerre több csoport tagja is lehet.

Ez az útmutató bemutatja, hogyan API Management példány rendszergazdák új csoportok hozzáadása és rendelheti őket hozzá a termékek és a fejlesztők számára.

> [!NOTE]
> Csoportok létrehozása és kezelése a közzétevő portálon, mellett hozhat létre és az API Management REST API használatával csoportok kezelése [csoport](https://msdn.microsoft.com/library/azure/dn776329.aspx) entitás.
> 
> 

## <a name="create-group"></a>Hozzon létre egy csoportot
Új csoport létrehozásához kattintson a **Publisher portal** az Azure portálon az API Management szolgáltatás. Ezzel továbblép az API Management közzétevő portáljára.

![Közzétevő portál][api-management-management-console]

> Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az [Ismerkedés az Azure API Management szolgáltatással][Get started with Azure API Management] oktatóanyag [API Management szolgáltatáspéldány létrehozása][Create an API Management service instance] című szakaszát.
> 
> 

Kattintson a **csoportok** a a **API Management** a bal oldali menüben, majd **csoport hozzáadása**.

![Új csoport hozzáadása][api-management-add-group]

Adjon meg egy egyedi nevet a csoport és az opcionális leírást, és kattintson a **mentése**.

![Új csoport hozzáadása][api-management-add-group-window]

Az új csoport megjelenik a csoportok lapon. A módosítandó a **neve** vagy **leírás** csoportjában kattintson a listában a csoport nevét. A csoport törléséhez kattintson **törlése**.

![Csoport hozzáadva][api-management-new-group]

Most, hogy a csoport jön létre, termékek és a fejlesztők kapcsolódó lehet.

## <a name="associate-group-product"></a>Társítsa a termék egy csoportot
Társítsa a termék egy csoportot, kattintson a **termékek** a a **API Management** a bal oldali menü és kattintson a kívánt termék nevét.

![Láthatóság megadása][api-management-add-group-to-product]

Válassza ki a **látható** lap hozzáadása és eltávolítása a csoportok és a termék aktuális csoportok megtekintése. Adja hozzá, vagy távolítsa el a csoportokat, ellenőrizze, vagy törölje a négyzet jelölését, a kívánt csoportok számára, majd kattintson **mentése**.

![Láthatóság megadása][api-management-add-group-to-product-visibility]

> [!NOTE]
> Azure Active Directory-csoportok hozzáadása, lásd: [hogyan szeretné engedélyekkel felruházni fejlesztői fiókok Azure Active Directory használatával az Azure API Management](api-management-howto-aad.md).
> 
> A csoportok konfigurálásához a **látható** termék lapra, majd **csoportok kezelése**.
> 
> 

Ha a termék egy csoporthoz tartozik, fejlesztők csoport megtekintheti és előfizetni a termék.

## <a name="associate-group-developer"></a>Csoportok társítani a fejlesztők számára
Csoportok fejlesztőkkel való társításához kattintson **felhasználók** a a **API Management** a bal oldali menüben, majd ellenőrizze a fejlesztők a csoportba felvenni kívánt melletti mezőbe.

![Fejlesztői hozzáadása csoporthoz][api-management-add-group-to-developer]

A kívánt fejlesztők is be van jelölve, kattintson a kívánt csoportot a **hozzáadása csoporthoz** legördülő listán. A fejlesztők lehet eltávolítani a csoportok használatával a **távolítsa el a csoportból** legördülő listán. 

![Fejlesztők][api-management-add-group-to-developer-saved]

A fejlesztői és a csoport között. a társítás hozzáadása után megtekintheti azokat a **felhasználók** fülre.

## <a name="next-steps"> </a>Következő lépések
* Egy fejlesztő csoporthoz hozzáadott, tekinthet meg és a termékek, a csoport előfizetni. További információkért lásd: [hogyan létrehozása, és a termék közzététele az Azure API Management][How create and publish a product in Azure API Management],
* Csoportok létrehozása és kezelése a közzétevő portálon, mellett hozhat létre és az API Management REST API használatával csoportok kezelése [csoport](https://msdn.microsoft.com/library/azure/dn776329.aspx) entitás.

[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group
