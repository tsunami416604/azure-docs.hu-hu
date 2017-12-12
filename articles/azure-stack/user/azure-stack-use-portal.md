---
title: "A verem Azure portál használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan elérése és a felhasználói portál használata az Azure-készletben."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 5aa00123-5b87-45e0-a671-4165e66bfbc6
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 7c34d7a225be63da95f664525b0366ff89b28838
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="using-the-azure-stack-portal"></a>A verem Azure portál használatával

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az Azure-verem services vásárlói nyilvános ajánlatok előfizetni a verem Azure portál használatával, és használhatják a szolgáltatásokat, amely azokat az ajánlatokat érhetők el. Már használta az Azure-portálon előtt, ha már ismeri a felhasználói felületen.

## <a name="access-the-portal"></a>A portál eléréséhez

Az Azure-verem operátor (a szolgáltató vagy a rendszergazda a szervezet), értesíti, hogy a helyes URL-címet a portál eléréséhez. 

- Integrált rendszert az URL-cím attól függően változik, az üzemeltető régió és külső tartomány nevét, és a formátum https://portal lesz. &lt; *régió*&gt;.&lt; *FQDN*&gt;.
- Ha a csomag Azure verem használata esetén a portál címe https://portal.local.azurestack.external.

![Képernyőfelvétel az Azure-verem felhasználói portál](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Testre szabhatja az irányítópultot

Az irányítópult tartalmaz egy alapértelmezett csempék. Kattinthat **Szerkesztés irányítópult** segítségével módosíthatja az alapértelmezett irányítópultot, vagy kattintson **új irányítópult** egyéni irányítópultok felvételéről. Az irányítópult könnyen is vegyen fel csempéket. Kattintson például **új**, kattintson a jobb gombbal **számítási**, és kattintson a **rögzítés az irányítópulton**.

## <a name="create-subscription-and-browse-available-resources"></a>Előfizetés létrehozása, és keresse meg a rendelkezésre álló erőforrások
 
Még nem rendelkezik előfizetéssel, elsőként kell tennie, akkor egy ajánlatot előfizetni. Ezt követően böngészhet a rendelkezésre álló erőforrásokat. Keresse meg, és az erőforrások létrehozásához tegye a következők egyikét:

- Kattintson a **piactér** csempére az irányítópulton. 
- Az a **összes erőforrás** csempére, kattintson a **erőforrások létrehozása**.
- A bal oldali navigációs ablaktábláján kattintson **új**.

## <a name="learn-how-to-use-available-services"></a>Választható szolgáltatások használata

Ha módosítania kell az elérhető szolgáltatások használatára vonatkozó útmutatást, valószínűleg különböző beállítások elérhető.

- A szervezet vagy a szolgáltató által biztosított a saját dokumentációjukban. Ez különösen igaz, ha egyéni szolgáltatások vagy alkalmazások kínálnak.
- Külső alkalmazások is rendelkeznek a saját dokumentációjukban.
- Az Azure-konzisztens szolgáltatások Határozottan javasoljuk, hogy Ön először tekintse meg az Azure verem dokumentációt. Az Azure-verem felhasználói dokumentáció eléréséhez kattintson a Súgó ikonra, és kattintson **súgó + támogatás**.
 
    ![Képernyőfelvétel a Súgó és támogatás beállítás a felhasználói felületen](media/azure-stack-use-portal/HelpAndSupport.png)

    Különösen javasoljuk, hogy tekintse át a kezdéshez a következő cikkeket:

    - [Kulcs szempontok: szolgáltatásokkal vagy az alkalmazások Azure verem](azure-stack-considerations.md)
    - A "Szolgáltatások használata" című részben minden Azure-konzisztens szolgáltatás felsorolt látható. Nincs minden egyes szolgáltatás, amely leírja a szolgáltatás, az Azure-ban, és ugyanazt a szolgáltatást Azure-készletben kínált közötti eltérések "szempontok" témakör. Egy vonatkozó példáért lásd: [VM szempontok](azure-stack-vm-considerations.md). Előfordulhat, hogy a "Szolgáltatások használata" szakaszban, amely egyedi Azure verem más információ. 
     
      Használhatja az Azure dokumentációja általános hivatkozásként egy szolgáltatáshoz, de ezek a különbségek tisztában kell lennie. Megérteni, hogy a dokumentáció a hivatkozásokat tartalmaz a **gyors üzembe helyezési oktatóanyag** csempe az Azure dokumentációja mutasson.

## <a name="get-support"></a>Támogatás kérése

Ha további támogatási van szüksége, lépjen kapcsolatba a szervezet vagy a service provider segítségért. 

Az Azure verem szoftverfejlesztői készlet használata a [Azure verem fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) támogatási egyetlen lehetőség.

## <a name="next-steps"></a>Következő lépések

[Kulcs szempontok: szolgáltatásokkal vagy az alkalmazások Azure verem](azure-stack-considerations.md)
