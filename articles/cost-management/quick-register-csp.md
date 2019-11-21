---
title: Register using CSP Partner information with Cloudyn in Azure
description: Ez a rövid útmutató részletesen ismerteti a Cloudyn próbaverziójára szóló előfizetés létrehozásához és a Cloudyn-portálra való bejelentkezéshez szükséges folyamatot.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/18/2019
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
manager: benshy
ms.openlocfilehash: 4255bfc6a0e51e175bd7cba7102069065b477258
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229964"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Regisztrálás a CSP partnerprogrammal és a költségadatok megtekintése

CSP-partnerként regisztrálhat a Cloudynbe. A regisztráció hozzáférést biztosít a Cloudyn portálhoz. Ez a rövid útmutató részletesen ismerteti a Cloudyn próbaverziójára szóló előfizetés létrehozásához és a Cloudyn-portálra való bejelentkezéshez szükséges folyamatot. Azt is bemutatja, hogyan tekintheti meg azonnal a költségadatokat.


> [!NOTE]
>
> A Cloudyn-regisztráció csak közvetlen CSP-partnerek vagy közvetett CSP-szolgáltatók számára érhető el.
>
> Konfigurálni kell a Partner Center API-t a hitelesítéshez és az adatok eléréséhez. Az API-hozzáférés létrehozásához szükség van egy globális rendszergazdai fiókra a partnerközpontban.
> További információkért lásd [a Partner Center API-hoz való csatlakozással](https://msdn.microsoft.com/library/partnercenter/mt709136.aspx) foglalkozó témakört.
>
> A Cloudynhez a közvetett CSP-viszonteladók is hozzáférhetnek, ha közvetett CSP-szolgáltatójuk regisztrál a Cloudynre. Ezután a közvetett CSP-viszonteladók és biztosíthatnak Cloudyn-hozzáférést Azure-ügyfeleknek és -előfizetésekhez.
>
>Cloudy is complaint with the Microsoft Secure Application model. For more information, see [Enabling the Secure Application Model framework](/partner-center/develop/enable-secure-app-model).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="register-with-cloudyn"></a>Regisztráció a Cloudynben

1. Az Azure Portalon kattintson a **Költségkezelés + Számlázás** elemre a szolgáltatások listáján.
2. Az **Áttekintés** területen kattintson a **Cloudyn** elemre.  
    ![Cloudyn page shown in the Azure portal](./media/quick-register-csp/cost-mgt-billing-service.png)
3. A **Cloudyn** lapon kattintson a **Go to Cloudyn** (Ugrás a Cloudynre) elemre a Cloudyn regisztrációs oldalának új ablakban való megnyitásához.
4. A Cloudyn portálon a próbaverzióra való regisztrációhoz írja be a vállalata nevét, válassza a **Microsoft CSP Partner Program Administrator** (Microsoft CSP partnerprogram rendszergazdája) lehetőséget, majd kattintson a **Next** (Tovább) gombra.  
5. Adja meg az **alkalmazásazonosítót**, a **kereskedelmi azonosítót** és az **alkalmazás titkos kulcsát**, majd válassza a **Default Pricing Plan** (Alapértelmezett díjszabás) lehetőséget. Ha nincsenek kéznél ezek az információk, jelentkezzen be a Partnerközpontba a [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) webhelyen az elsődleges rendszergazdai fiókjával, és kövesse az alábbi lépéseket:
   1. Lépjen az **Irányítópultra**, és kattintson a **Beállítások** szimbólumra, majd a **Partnerbeállítások**, végül pedig az **Alkalmazáskezelés** elemre.
   2. Ha rendelkezik egy korábban létrehozott webalkalmazással, hagyja ki ezt a lépést. Ha nem, kattintson a **Webalkalmazás** szakasz **Új webalkalmazás hozzáadása** elemére.
   3. Másolja ki az **Alkalmazásazonosító** GUID azonosítót a webalkalmazásból.
   4. Másolja ki a **Kereskedelmi azonosító** GUID azonosítót a webalkalmazásból.
   5. Jelölje meg, hogy egy vagy két év legyen-e a kulcs érvényességi időtartama. Válassza a **Kulcs hozzáadása** elemet, majd másolja ki és mentse el a titkos kulcs értékét.  
    ![Partner Dashboard where you copy credential information](./media/quick-register-csp/csp-partner-center.png)
   6. Térjen vissza a Cloudyn regisztrációs oldalára, és illessze be az adatokat.  
      ![Paste credential information in the Cloudyn registration page](./media/quick-register-csp/csp-reg.png)
6. Fogadja el a használati feltételeket, majd ellenőrizze az adatokat. A **Next** (Tovább) gombra kattintva adjon engedélyt a Cloudynnek az Azure-erőforrásadatok gyűjtésére. A gyűjtött adatok az előfizetéseire vonatkozó használati, teljesítmény-, elszámolási és címkeadatokból állnak.  
7. Az **Invite other stakeholders** (További érdekelt felek meghívása) területen hozzáadhat felhasználókat az e-mail-címük megadásával. Ha kész van, kattintson a **Next** (Tovább) gombra. Körülbelül két órát vesz igénybe, míg a rendszer az összes elszámolási adatot hozzáadja a Cloudynhez.
8. A **Go to Cloudyn** (Ugrás a Cloudynre) elemre kattintva nyissa meg a Cloudyn portált, ahol a **Cloud Accounts Management** (Felhőalapú fiókok felügyelete) oldalon látnia kell a regisztrált CSP-fiók adatait.

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Közvetett CSP-hozzáférés konfigurálása a Cloudynben

Alapértelmezés szerint a Partner Center API csak közvetlen CSP-k számára érhető el. Egy közvetlen CSP-szolgáltató azonban konfigurálhat hozzáférést közvetett CSP-ügyfeleinek vagy -partnereinek a Cloudyn entitáscsoportjainak segítségével.

To enable access for indirect CSP customers or partners, follow the steps in [Register with Cloudyn](#register-with-cloudyn) to set up a trial registration. Ezután a következő lépéseket végrehajtva szegmentálja a közvetett CSP-adatokat a Cloudyn entitáscsoportjaival. Végül rendelje hozzá az entitáscsoportokhoz a megfelelő felhasználói engedélyeket.

1. Hozzon létre egy entitáscsoportot az [entitások létrehozásával](tutorial-user-access.md#create-and-manage-entities) foglalkozó szakaszban található információk alapján.
2. Kövesse az [előfizetések költségentitásokhoz való hozzárendelésével](https://www.youtube.com/watch?v=d9uTWSdoQYo) foglalkozó videóban ismertetett lépéseket. Társítsa a közvetett CSP-ügyfél fiókját és Azure-előfizetéseit az előzőleg létrehozott entitáshoz.
3. A [rendszergazdai hozzáféréssel rendelkező felhasználó létrehozását](tutorial-user-access.md#create-a-user-with-admin-access) ismertető szakasz lépéseit követve hozzon létre egy felhasználói fiókot rendszergazdai hozzáféréssel. Ezután győződjön meg arról, hogy a felhasználói fiók valóban rendelkezik rendszergazdai hozzáféréssel a közvetett fiókhoz korábban létrehozott entitásokhoz.

A közvetett CSP-partnerek bejelentkezhetnek a Cloudyn portálra a számukra létrehozott fiókok segítségével.


[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a CSP-adatokat használta arra, hogy regisztráljon a Cloudynre. A Cloudyn portálra is bejelentkezett, és elkezdte a költségadatok áttekintését. Ha bővebb információra van szüksége a Cloudynről, lépjen tovább a Cloudyn oktatóanyagára.

> [!div class="nextstepaction"]
> [A használat és a költségek áttekintése](./tutorial-review-usage.md)
