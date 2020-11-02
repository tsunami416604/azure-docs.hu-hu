---
title: Regisztrálás az Azure-beli Cloudynbe CSP-partneradatokkal
description: Megismerheti azt a regisztrációs folyamatot, amelyet a partnerek használnak az ügyfeleiknek a Cloudyn portálon történő regisztrálásához.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 95d8a425314ad1c968915085b35ca89aa3f62711
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543321"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Regisztrálás a CSP partnerprogrammal és a költségadatok megtekintése

CSP-partnerként és regisztrált Cloudyn-felhasználóként megtekintheti és elemezheti a felhővel kapcsolatos költségeit a Cloudynben. Az [Azure Cost Management natív módon érhető el azon közvetlen partnerek számára](../costs/get-started-partners.md), akik regisztrálták ügyfeleiket egy Microsoft Ügyfélszerződésre, illetve vásároltak egy Azure-csomagot.

A regisztráció hozzáférést biztosít a Cloudyn portálhoz. Ez a rövid útmutató részletesen ismerteti a Cloudyn próbaverziójára szóló előfizetés létrehozásához és a Cloudyn-portálra való bejelentkezéshez szükséges folyamatot.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Közvetett CSP-hozzáférés konfigurálása a Cloudynben

Alapértelmezés szerint a Partner Center API csak közvetlen CSP-k számára érhető el. Egy közvetlen CSP-szolgáltató azonban konfigurálhat hozzáférést közvetett CSP-ügyfeleinek vagy -partnereinek a Cloudyn entitáscsoportjainak segítségével.

A hozzáférés közvetett CSP-ügyfelek vagy -partnerek számára történő engedélyezéséhez hajtsa végre az alábbi lépéseket a közvetett CSP-adatoknak a Cloudyn entitáscsoportjaival történő szegmentálásához. Végül rendelje hozzá az entitáscsoportokhoz a megfelelő felhasználói engedélyeket.

1. Hozzon létre egy entitáscsoportot az [entitások létrehozásával](tutorial-user-access.md#create-and-manage-entities) foglalkozó szakaszban található információk alapján.
2. Kövesse az [előfizetések költségentitásokhoz való hozzárendelésével](https://www.youtube.com/watch?v=d9uTWSdoQYo) foglalkozó videóban ismertetett lépéseket. Társítsa a közvetett CSP-ügyfél fiókját és Azure-előfizetéseit az előzőleg létrehozott entitáshoz.
3. A [rendszergazdai hozzáféréssel rendelkező felhasználó létrehozását](tutorial-user-access.md#create-a-user-with-admin-access) ismertető szakasz lépéseit követve hozzon létre egy felhasználói fiókot rendszergazdai hozzáféréssel. Ezután győződjön meg arról, hogy a felhasználói fiók valóban rendelkezik rendszergazdai hozzáféréssel a közvetett fiókhoz korábban létrehozott entitásokhoz.

A közvetett CSP-partnerek bejelentkezhetnek a Cloudyn portálra a számukra létrehozott fiókok segítségével.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a CSP-adatokat használta arra, hogy regisztráljon a Cloudynre. A Cloudyn portálra is bejelentkezett, és elkezdte a költségadatok áttekintését. Ha bővebb információra van szüksége a Cloudynről, lépjen tovább a Cloudyn oktatóanyagára.

> [!div class="nextstepaction"]
> [A használat és a költségek áttekintése](tutorial-review-usage.md)