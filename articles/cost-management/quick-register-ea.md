---
title: "Azure Nagyvállalati Szerződés regisztrálása az Azure Cost Managementben | Microsoft Docs"
description: "Regisztrálhat az Azure Cost Managementbe a Nagyvállalati Szerződésével a Cloudynen keresztül."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 41a9df712b07253d9f5f9db8542fb9917592320f
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2017
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Azure Nagyvállalati Szerződés regisztrálása és a költségadatok megtekintése

Regisztrálhat az Azure Cost Managementbe az Azure Nagyvállalati Szerződéssel a Cloudynen keresztül. A regisztráció hozzáférést biztosít a Cloudyn portálhoz. Ez a rövid útmutató részletesen ismerteti a Cloudyn próbaverziójára szóló előfizetés létrehozásához és a Cloudyn-portálra való bejelentkezéshez szükséges folyamatot. Azt is bemutatja, hogyan tekintheti meg azonnal a költségadatokat.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

- Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-trial-registration"></a>Regisztrálás a próbaverzióra

1. Az Azure Portalon kattintson a **Költségkezelés + Számlázás** elemre a szolgáltatások listáján.
2. Az **Overview** (Áttekintés) területen kattintson a **Költségkezelés** elemre.  
    ![Költségkezelés oldal](./media/quick-register-ea/cost-mgt-billing-service.png)
3. A **Költségkezelés** oldalon kattintson a **Go to Cost Management** (Ugrás a költségkezeléshez) elemre a Cloudyn regisztrációs oldalának új ablakban való megnyitásához.
4. A Cloudyn portálon a próbaverzióra való regisztrációhoz írja be a vállalata nevét, majd válassza az **Azure Enterprise Enrollment Administrator** (Azure Nagyvállalati Beléptetés rendszergazdája) lehetőséget.  
    ![regisztráció próbaverzióra](./media/quick-register-ea/trial-reg.png)
5. Adja meg az Enterprise Portal regisztrációs API-kulcsát. Ha ez nincs kéznél, kattintson az [Enterprise Portal](https://ea.azure.com) hivatkozásra, és kövesse az alábbi lépéseket:
  1. Jelentkezzen be az Azure Enterprise webhelyre, és kattintson **Reports** (Jelentések), majd az **API Access Key** (API hozzáférési kulcs) lehetőségre, és másolja ki az elsődleges kulcsát.  
    ![EA API-kulcs](./media/quick-register-ea/ea-key.png)
  3. Térjen vissza a regisztrációs oldalhoz, és illessze be az API-kulcsát.
6. Fogadja el a használati feltételeket, majd ellenőrizze a kulcsot. A **Next** (Tovább) gombra kattintva adjon engedélyt a Cloudynnek az Azure-erőforrásadatok gyűjtésére. A gyűjtött adatok az előfizetéseire vonatkozó használati, teljesítmény-, elszámolási és címkeadatokból állnak.  
    ![kulcs ellenőrzése](./media/quick-register-ea/ea-key-validated.png)
7. Az **Invite other stakeholders** (További érdekelt felek meghívása) területen hozzáadhat felhasználókat az e-mail-címük megadásával. Ha kész van, kattintson a **Next** (Tovább) gombra. Körülbelül két órát vesz igénybe, míg a rendszer az összes elszámolási adatot hozzáadja a Cloudynhez.
8. A **Go to Cloudyn** (Ugrás a Cloudynre) elemre kattintva nyissa meg a Cloudyn portált, ahol a **Cloud Accounts Management** (Felhőalapú fiókok felügyelete) oldalon látnia kell a regisztrált EA-fiók adatait.

Oktatóvideó a Nagyvállalati Szerződés regisztrációjáról: [Az EA regisztrációs azonosító és API-kulcs megkeresése az Azure Cost Management használatához a Cloudynen keresztül](https://youtu.be/u_phLs_udig).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az Azure Nagyvállalati Szerződés adatait használta arra, hogy regisztráljon a Cost Management szolgáltatásra. A Cloudyn portálra is bejelentkezett, és elkezdte a költségadatok áttekintését. Ha bővebb információra van szüksége a Cloudynen keresztül használt Azure Cost Managementről, lépjen tovább a Cost Management oktatóanyagára.

> [!div class="nextstepaction"]
> [A használat és a költségek áttekintése](./tutorial-review-usage.md)
