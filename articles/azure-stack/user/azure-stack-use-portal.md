---
title: A verem Azure portál használatával |} Microsoft Docs
description: Megtudhatja, hogyan elérése és a felhasználói portál használata az Azure-készletben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: mabrigg
ms.reviewer: efemmano
ms.openlocfilehash: 7ca29ee359349f69c3d5ff21bd9db3f93358206a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724312"
---
# <a name="use-the-azure-stack-portal"></a>Az Azure Stack-portál használata

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Nyilvános ajánlatok előfizetni a verem Azure portál használatával, és használhatják a szolgáltatásokat, amelyek ezekről az ajánlatokról biztosítanak. Ha már használta a globális Azure portálra, már ismeri a hely működése.

## <a name="access-the-portal"></a>A portál eléréséhez

Az Azure-verem operátor (a szolgáltató vagy a rendszergazda a szervezet), értesíti, hogy a helyes URL-címet a portál eléréséhez.

- Egy integrált rendszer, az URL-cím attól függően változik, az üzemeltető régió és külső tartomány nevét, és formátumban lesz https://portal.&lt; *a régióban*&gt;.&lt; *FQDN*&gt;.
- Az Azure verem szoftverfejlesztői készlet használata, a portál címe https://portal.local.azurestack.external.

![Képernyőfelvétel az Azure-verem felhasználói portál](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Testre szabhatja az irányítópultot

Az irányítópult tartalmaz egy alapértelmezett csempék. Kiválaszthatja **Szerkesztés irányítópult** módosíthatja az alapértelmezett irányítópultot, vagy válasszon **új irányítópult** egyéni irányítópult létrehozása. Könnyen teste szabhatja irányítópult hozzáadásával vagy eltávolításával a csempéket. Például egy számítási csempe hozzáadásához válassza **új**. Kattintson a jobb gombbal **számítási**, majd válassza ki **rögzítés az irányítópulton**.

## <a name="create-subscription-and-browse-available-resources"></a>Előfizetés létrehozása, és keresse meg a rendelkezésre álló erőforrások

Még nem rendelkezik előfizetéssel, elsőként kell tennie, akkor egy ajánlatot előfizetni. Ezután keresse meg a rendelkezésre álló erőforrások. Keresse meg, és az erőforrások létrehozásához használja a következő módszerek valamelyikét:

- Válassza ki a **piactér** csempére az irányítópulton.
- Az a **összes erőforrás** csempe, jelölje be **erőforrások létrehozása**.
- A bal oldali navigációs panelen válassza ki a **új**.

## <a name="learn-how-to-use-available-services"></a>Választható szolgáltatások használata

Ha módosítania kell az elérhető szolgáltatások használatára vonatkozó útmutatást, valószínűleg különböző beállítások elérhető.

- A szervezet vagy a szolgáltató által biztosított saját dokumentációját, amely általában a helyzet akkor, ha testre szabott szolgáltatások vagy alkalmazások kínálnak.
- Külső alkalmazások is rendelkeznek a saját dokumentációjukban.
- Az Azure-konzisztens szolgáltatások Határozottan javasoljuk, hogy Ön először tekintse meg az Azure verem dokumentációt. Az Azure-verem felhasználói dokumentáció eléréséhez a Súgó ikonra, majd válassza ki és **súgó + támogatás**.

    ![A Súgó és támogatás a beállítás a felhasználói felületen](media/azure-stack-use-portal/HelpAndSupport.png)

    Különösen javasoljuk, hogy tekintse át a kezdéshez a következő cikkeket:

    - [Kulcs szempontok: szolgáltatásokkal vagy az alkalmazások Azure verem](azure-stack-considerations.md)
    - Az a **szolgáltatásokkal** szakasz dokumentáció, nincs szempontokat egy cikk a minden egyes szolgáltatás. A szempontok lapra a szolgáltatás, az Azure-ban, és ugyanazt a szolgáltatást Azure-készletben kínált közötti különbségeket ismerteti. Egy vonatkozó példáért lásd: [VM szempontok](azure-stack-vm-considerations.md). Egyéb információk lehetnek a **szolgáltatásokkal** Azure verem egyedi szakasz.

      Használhatja az Azure dokumentációja általános hivatkozásként egy szolgáltatáshoz, de ezek a különbségek tisztában kell lennie. Megérteni, hogy a dokumentáció a hivatkozásokat tartalmaz a **gyors üzembe helyezési oktatóanyag** csempe az Azure dokumentációja mutasson.

## <a name="get-support"></a>Támogatás kérése

Ha támogatásra van szüksége, ha a szervezet vagy a service provider kérjen segítséget.

Az Azure verem szoftverfejlesztői készlet használata a [Azure verem fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) támogatási csak forrása.

## <a name="next-steps"></a>További lépések

[Kulcs szempontok: szolgáltatásokkal vagy az alkalmazások Azure verem](azure-stack-considerations.md)
