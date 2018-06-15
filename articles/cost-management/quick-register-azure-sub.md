---
title: Azure-előfizetés regisztrálása az Azure Cost Managementben | Microsoft Docs
description: Az Azure-előfizetésével regisztrálhat az Azure Cost Managementbe.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: 254a7f2904a63381a4eeec02ee32b19890703ae1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296630"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Egyéni Azure-előfizetés regisztrálása és a költségadatok megtekintése

Az Azure-előfizetésével regisztrálhat az Azure Cost Managementbe. A regisztráció hozzáférést biztosít a Cloudyn portálhoz. Ez a rövid útmutató részletesen ismerteti a Cloudyn próbaverziójára szóló előfizetés létrehozásához és a Cloudyn-portálra való bejelentkezéshez szükséges folyamatot. Azt is bemutatja, hogyan tekintheti meg azonnal a költségadatokat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="register-with-azure-cost-management"></a>Regisztrálás az Azure Cost Managementbe

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

Oktatóvideó az Azure-előfizetés regisztrációjáról: [A címtárbeli GUID és a díjazonosító megkeresése az Azure Cost Managementben való használathoz](https://youtu.be/PaRjnyaNGMI).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az Azure-előfizetése adatait használta arra, hogy regisztráljon a Cost Management szolgáltatásra. A Cloudyn portálra is bejelentkezett, és elkezdte a költségadatok áttekintését. Ha bővebb információra van szüksége az Azure Cost Managementről, lépjen tovább a Cost Management oktatóanyagára.

> [!div class="nextstepaction"]
> [A használat és a költségek áttekintése](./tutorial-review-usage.md)
