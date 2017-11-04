---
title: "Regisztráljon CSP partneradatok az Azure költség Management szolgáltatással |} Microsoft Docs"
description: "A kriptográfiai Szolgáltató partneradatok segítségével regisztrálja az Azure költség Management Cloudyn a."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: bcb072a2f2ab8c0e5097fca2c95309464483cb53
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Regisztrálja a CSP Partner program és az adatok megtekintése

A CSP-partnerként regisztrálhatja az Azure költség Management Cloudyn által. A regisztrációt a Cloudyn portal hozzáférést biztosít. A gyors üzembe helyezés részletek a regisztrációs folyamat szükséges a Cloudyn próba-előfizetés létrehozása, és jelentkezzen be a Cloudyn portálra. Azt is bemutatja, hogyan költség adatok azonnal megtekintéséhez.


>[!NOTE]
>A Regisztrálás, aki hozzáféréssel rendelkezik a Partner Center API partner program rendszergazdának kell lennie. A Partner Center API-beállítás kötelező a hitelesítés és adatokhoz való hozzáférést. További információkért lásd: [a Partner Center API kapcsolódás](https://msdn.microsoft.com/library/partnercenter/mt709136.aspx). Emellett közvetett CSP-hez a felhasználók csak a Cloudyn amikor regisztrálja a közvetlen CSP viszonteladóhoz Cloudyn való hozzáférés biztosítása a felhasználók és az előfizetések.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

- Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-trial-registration"></a>Hozzon létre egy próbaregisztráció.

1. Az Azure portálon kattintson **költség felügyeleti + számlázási** a szolgáltatások listájában.
2. A **áttekintése**, kattintson a **költség kezelése**  
    ![Költség kezelése lap](./media/quick-register-csp/cost-mgt-billing-service.png)
3. A a **költség felügyeleti** kattintson **költség felügyeleti Ugrás** Cloudyn regisztrációs lapjának megnyitásához egy új ablakban.
4. A Cloudyn portál próbaregisztráció lapon írja be a cég nevét, jelölje be **a Microsoft CSP Partner Program rendszergazda**, és kattintson a **következő**.  
5. Adjon meg egy **Alkalmazásazonosító**, **Commerce azonosítója**, **alkalmazás titkos kulcs**, és válassza ki a **alapértelmezett árképzési megtervezése**. Ha még nem rendelkezik a információ hasznos, jelentkezzen be a Partner Center portálon, a [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) a elsődleges rendszergazda fiókot, és hajtsa végre a következő lépéseket:
  1. Ugrás a **irányítópult** > **Fiókbeállítások** > **Alkalmazáskezelés**.
  2. Ha már létrehozott egy webalkalmazást, kihagyhatja ezt a lépést. Ellenkező esetben kattintson a **hozzáadása új webalkalmazás** a a **webalkalmazás** szakasz.
  3. Másolás a **Alkalmazásazonosító** GUID a webalkalmazásból.
  4. Másolás a **Commerce azonosító** GUID a webalkalmazásból.
  5. Szükség esetén jelöljön ki a kulcs érvényességi időtartamát, egy vagy két éven belülre esik. Válassza ki **Hozzáadás kulcs** majd másolja ki és mentse a titkos kulcs értékét.  
    ![Kriptográfiai Szolgáltató Partnerközpontjában](./media/quick-register-csp/csp-partner-center.png)
  6. Lépjen vissza a regisztrációs oldalra, és illessze be az adatokat.  
      ![Kriptográfiai Szolgáltató fiók hitelesítő adatait](./media/quick-register-csp/csp-reg.png)
6. Fogadja el a használati feltételeket, majd az adatok érvényesítéséhez. Kattintson a **következő** engedélyezésére Cloudyn Azure-erőforrás adatainak gyűjtéséről. Összegyűjtött adatok használati, teljesítmény, számlázási és az előfizetések címke adatait tartalmazza.  
7. A **hívhat meg egyéb érintett felek**, írja be az e-mail-címét is hozzáadhat felhasználókat. Amikor végzett, kattintson **következő**. Az elszámolási adatok Cloudyn hozzáadják körülbelül két órát vesz igénybe.
8. Kattintson a **Cloudyn Ugrás** a Cloudyn portál megnyitása és a a **felhő fiókok kezelése** lapon regisztrált CSP fiókadatait kell megjelennie.

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Cloudyn közvetett CSP hozzáférés konfigurálása

Alapértelmezés szerint a Partner Center API érhető el csak CSP-k közvetlen. A közvetlen CSP-szolgáltató, konfigurálja a hozzáférést a közvetett CSP-ügyfelek vagy partnerek entitás csoportokat használnak az Cloudyn.

Hozzáférésének engedélyezésére vonatkozó közvetett CSP ügyfeleket vagy partnereket, kövesse a lépéseket [hozzon létre egy próbaregisztráció](#create-a-trial-registration) egy próbaregisztráció beállítása. Ezután fejezze be az alábbi lépések végrehajtásával szegmens közvetett CSP adatokat Cloudyn entitáscsoportokat. Ezt követően a megfelelő felhasználói jogosultságok hozzárendelése az entitáscsoportokat.

1. Entitás-csoport létrehozása az információkért [entitásokat hozhatnak létre](tutorial-user-access.md#create-entities).
2. Kövesse a [előfizetések hozzárendelése költség entitások](https://support.cloudyn.com/hc/en-us/articles/115005139425-Video-Assigning-subscriptions-to-Cost-Entities). Közvetett CSP felhasználói fiókhoz és az Azure-előfizetések az entitásban, korábban létrehozott rendelni.
3. Kövesse a [hozzon létre egy felhasználó rendszergazdai hozzáférés](tutorial-user-access.md#create-a-user-with-admin-access) rendszergazdai hozzáféréssel rendelkező felhasználói fiók létrehozása. Ezt követően ellenőrizze a felhasználói fiók számára a közvetett fiók korábban létrehozott konkrét személyek rendszergazdai hozzáféréssel rendelkezik.

Közvetett CSP partnerek jelentkezzen be a Cloudyn portálra a fiókokkal, amelyet létrehozott számukra.


[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés, a kriptográfiai Szolgáltató adatainak való költség felügyeleti regisztrálásához használt. Ha is a Cloudyn portal be van jelentkezve és már elindította költség adatokat. További információt az Azure költség Management Cloudyn, továbbra is az oktatóanyag költség-kezelésre.

> [!div class="nextstepaction"]
> [Felülvizsgálati használati és költségek](./tutorial-review-usage.md)
