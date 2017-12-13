---
title: "Azure-előfizetés regisztrálása az Azure Cost Managementben | Microsoft Docs"
description: "Regisztrálhat az Azure Cost Managementbe az Azure-előfizetésével a Cloudynen keresztül."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 4fed181a004ba6a5ea35dcbe1366e7d48342c96d
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2017
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Egyéni Azure-előfizetés regisztrálása és a költségadatok megtekintése

Regisztrálhat az Azure Cost Managementbe az Azure-előfizetésével a Cloudynen keresztül. A regisztráció hozzáférést biztosít a Cloudyn portálhoz. Ez a rövid útmutató részletesen ismerteti a Cloudyn próbaverziójára szóló előfizetés létrehozásához és a Cloudyn-portálra való bejelentkezéshez szükséges folyamatot. Azt is bemutatja, hogyan tekintheti meg azonnal a költségadatokat.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

- Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-trial-registration"></a>Regisztrálás a próbaverzióra

1. Az Azure Portalon kattintson a **Költségkezelés + Számlázás** elemre a szolgáltatások listáján.
2. Az **Overview** (Áttekintés) területen kattintson a **Költségkezelés** elemre.  
    ![Költségkezelés oldal](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. A **Költségkezelés** oldalon kattintson a **Go to Cost Management** (Ugrás a költségkezeléshez) elemre a Cloudyn regisztrációs oldalának új ablakban való megnyitásához.
4. A Cloudyn portálon a próbaverzióra való regisztrációhoz írja be a vállalata nevét, majd válassza az **Azure Individual Subscription Owner** (Egyéni Azure-előfizetés tulajdonosa) lehetőségre, és kattintson a **Next** (Tovább) gombra. A fiókneve és a bérlőazonosító automatikusan rákerül az űrlapra.  
    ![regisztráció próbaverzióra](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Válassza ki az előfizetéséhez kapcsolódó **Offer ID - Name** (Ajánlatazonosító – Név) elemet. Ha nem biztos abban, milyen díjazonosító tartozik az előfizetéséhez, nézzen meg egy Azure-számlát, és keresse meg rajta az **ajánlatazonosítót**.
6. Fogadja el a használati feltételeket, majd ellenőrizze az adatokat, és kattintson a **Next** (Tovább) gombra.
7. A **Gather additional data** (További adatok gyűjtése) oldalon kattintson a **Next** (Tovább) gombra. Ezzel felhatalmazza fel a Cloudynt az Azure-erőforrásadatok gyűjtésére. A gyűjtött adatok az előfizetéseire vonatkozó használati, teljesítmény-, elszámolási és címkeadatokból állnak.  
    ![további adatok gyűjtése](./media/quick-register-azure-sub/gather-additional.png)
8. A böngészőjében megnyílik a Cloudyn bejelentkezési oldala. Jelentkezzen be az Azure-előfizetése hitelesítő adataival.
9. A **Go to Cloudyn** (Ugrás a Cloudynre) elemre kattintva nyissa meg a Cloudyn portált, ahol az **Accounts Management** (Fiókok kezelése) oldalon látnia kell az Azure-előfizetés fiókjának adatait.  
    ![Fiókkezelés](./media/quick-register-azure-sub/accounts-mgt.png)

Oktatóvideó az Azure-előfizetés regisztrációjáról: [A Directory GUID és a díjazonosító megkeresése az Azure Cost Management használatához a Cloudynen keresztül](https://youtu.be/PaRjnyaNGMI).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az Azure-előfizetése adatait használta arra, hogy regisztráljon a Cost Management szolgáltatásra. A Cloudyn portálra is bejelentkezett, és elkezdte a költségadatok áttekintését. Ha bővebb információra van szüksége a Cloudynen keresztül használt Azure Cost Managementről, lépjen tovább a Cost Management oktatóanyagára.

> [!div class="nextstepaction"]
> [A használat és a költségek áttekintése](./tutorial-review-usage.md)
