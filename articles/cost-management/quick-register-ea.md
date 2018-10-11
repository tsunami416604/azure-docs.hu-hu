---
title: Azure Nagyvállalati Szerződés regisztrálása a Cloudynben | Microsoft Docs
description: Regisztráljon a Cloudynre a Nagyvállalati Szerződésével.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/14/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: bed1ea3785c35d1053a0ff2147c3bdd797e28581
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996635"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Azure Nagyvállalati Szerződés regisztrálása és a költségadatok megtekintése

Az Azure Nagyvállalati Szerződésével regisztrálhat a Cloudynre. A regisztráció hozzáférést biztosít a Cloudyn portálhoz. Ez a rövid útmutató részletesen ismerteti a Cloudyn próbaverziójára szóló előfizetés létrehozásához és a Cloudyn-portálra való bejelentkezéshez szükséges folyamatot. Azt is bemutatja, hogyan tekintheti meg azonnal a költségadatokat.

Az Azure Cost Management a Cloudynhez hasonló funkcionalitást kínál. Az Azure Cost Management egy natív Azure költségkezelő megoldás. Segít kezelni a költségvetéseket, exportálni az adatokat, valamint áttekinteni és végrehajtani az optimalizálási javaslatokat pénzmegtakarítás céljából. További információ: [Azure Cost Management](overview-cost-mgt.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="register-with-cloudyn"></a>Regisztráció a Cloudynben

1. Az Azure Portalon kattintson a **Költségkezelés + Számlázás** elemre a szolgáltatások listáján.
2. Az **Áttekintés** területen kattintson a **Cloudyn** elemre.  
    ![Cloudyn lap](./media/quick-register-ea/cost-mgt-billing-service.png)
3. A **Cloudyn** lapon kattintson a **Go to Cloudyn** (Ugrás a Cloudynre) elemre a Cloudyn regisztrációs oldalának új ablakban való megnyitásához.
4. A Cloudyn portálon a próbaverzióra való regisztrációhoz írja be a vállalata nevét, majd válassza az **Azure Enterprise Enrollment Administrator** (Azure Nagyvállalati Beléptetés rendszergazdája) lehetőséget.  
    ![regisztráció próbaverzióra](./media/quick-register-ea/trial-reg.png)
5. Adja meg az Enterprise Portal regisztrációs API-kulcsát. Ha ez nincs kéznél, kattintson az [Enterprise Portal](https://ea.azure.com) hivatkozásra, és kövesse az alábbi lépéseket:
  1. Jelentkezzen be az Azure Enterprise webhelyre, és kattintson **Reports** (Jelentések), majd az **API Access Key** (API hozzáférési kulcs) lehetőségre, és másolja ki az elsődleges kulcsát.  
    ![EA API-kulcs](./media/quick-register-ea/ea-key.png)
  3. Térjen vissza a regisztrációs oldalhoz, és illessze be az API-kulcsát.
6. Fogadja el a használati feltételeket, majd ellenőrizze a kulcsot. A **Next** (Tovább) gombra kattintva adjon engedélyt a Cloudynnek az Azure-erőforrásadatok gyűjtésére. A gyűjtött adatok az előfizetéseire vonatkozó használati, teljesítmény-, elszámolási és címkeadatokból állnak.  
    ![kulcs ellenőrzése](./media/quick-register-ea/ea-key-validated.png)
7. Az **Invite other stakeholders** (További érdekelt felek meghívása) területen hozzáadhat felhasználókat az e-mail-címük megadásával. Ha kész van, kattintson a **Next** (Tovább) gombra. Az Azure-regisztrációja méretétől függően akár 24 órát is igénybe vehet, míg a rendszer az összes elszámolási adatot hozzáadja a Clodynhez.
8. A **Go to Cloudyn** (Ugrás a Cloudynre) elemre kattintva nyissa meg a Cloudyn portált, ahol a **Cloud Accounts Management** (Felhőalapú fiókok felügyelete) oldalon látnia kell a regisztrált EA-fiók adatait.

Oktatóvideó a Nagyvállalati Szerződés regisztrációjáról: [Az EA regisztrációs azonosító és API-kulcs megkeresése a Cloudyn használatához](https://youtu.be/u_phLs_udig).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az Azure Nagyvállalati Szerződés adatait használta arra, hogy regisztráljon a Cloudynre. A Cloudyn portálra is bejelentkezett, és elkezdte a költségadatok áttekintését. Ha bővebb információra van szüksége a Cloudynről, lépjen tovább a Cloudyn oktatóanyagára.

> [!div class="nextstepaction"]
> [A használat és a költségek áttekintése](./tutorial-review-usage.md)
