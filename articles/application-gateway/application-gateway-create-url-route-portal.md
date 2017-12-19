---
title: "Alkalmazásátjáró - Azure-portálon elérési alapú szabály létrehozásához |} Microsoft Docs"
description: "Útmutató az Alkalmazásátjáró elérési alapú szabály létrehozása az Azure portál használatával."
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 87bd93bc-e1a6-45db-a226-555948f1feb7
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: b207e7e7bd83e56db68288190c7bedafa8b5b7fa
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-azure-portal"></a>Az Alkalmazásátjáró elérési alapú szabály létrehozása az Azure portál használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

URL-cím elérési út-alapú útválasztási, a HTTP-kérések URL-címe alapján útvonalak rendeli. Ellenőrzi, hogy egy olyan útvonalat, egy háttér-kiszolgálókészlethez az Alkalmazásátjáró szerepel az URL-lel konfigurálja, és ezután elküldi a hálózati forgalom a meghatározott készletbe. URL-cím elérési út-alapú útválasztási gyakori felhasználási-hoz való különböző háttér-kiszolgálófiók tartalom különböző érkező kérések elosztása.

Alkalmazás-átjárók rendelkeznek két szabály típusa: basic és URL-cím elérési út-alapú szabályok. Alapszintű szabálytípus a háttér-készletek ciklikus multiplexelés szolgáltatást biztosít. Elérési út-alapú szabályok ciklikus multiplexelés mellett is használhatja az elérési út mintája a kérelem URL-címének, a megfelelő háttér-készlet kiválasztásakor.

## <a name="scenario"></a>Forgatókönyv

A következő forgatókönyv olyan elérési utat alapú szabály meglévő Alkalmazásátjáró hoz létre.
Ez a forgatókönyv azt feltételezi, hogy Ön már elvégezte a javasolt lépéseket a [Alkalmazásátjáró létrehozása a portállal](application-gateway-create-gateway-portal.md).

![URL-cím útvonal][scenario]

## <a name="createrule"></a>Az elérési út alapú szabály létrehozása

Egy elérési utat alapú szabály saját figyelő igényel. A szabály létrehozása előtt mindenképpen ellenőrizze, hogy rendelkezik-e használni egy rendelkezésre álló figyelőt.

### <a name="step-1"></a>1. lépés

Lépjen a [Azure-portálon](http://portal.azure.com) válassza ki a meglévő Alkalmazásátjáró. Kattintson a **szabályok**.

![Az Application Gateway áttekintése][1]

### <a name="step-2"></a>2. lépés

Kattintson a **elérési alapú** gombra kattintva vegye fel az új elérési utat-alapú szabályt.

### <a name="step-3"></a>3. lépés

A **Hozzáadás elérési alapú szabály** panel két részből áll. Az első szakaszban, ahol definiálva a figyelő, a nevét, valamint a szabály az alapértelmezett elérési út beállításai. Az alapértelmezett elérési út beállításai vannak, amelyek nem az egyéni elérési út-alapú útvonal útvonalak. A második része a **Hozzáadás elérési alapú szabály** panel, itt adhatja meg az elérési út-alapú szabályok magukat.

**Alapbeállítások**

* **Név**: a szabály, amely elérhető a portál rövid nevét.
* **Figyelő**: A figyelő, amely a szabály szolgál.
* **Alapértelmezett háttérkészlet**: az alapértelmezett szabály használható a háttérben.
* **Alapértelmezett beállítások HTTP**: az alapértelmezett szabály használni kívánt a HTTP-beállításokat.

**Elérési út alapú szabálybeállításai**

* **Név**: egy rövid nevet az elérési út alapuló szabály.
* **Elérési utak**: az elérési út a szabály keres-forgalom.
* **Háttérkészlet**: A hátteret a szabály használható.
* **HTTP-beállítása**: A HTTP-beállítások a szabály használható.

> [!IMPORTANT]
> A **elérési utak** értéke annak a elérési út egyezőre a listában. Minden minta perjellel kell kezdődnie, és csillag csak engedélyezett végén. Érvényes példa: /xyz, /xyz*, és /xyz/*.  

![Hozzáadása az elérési út alapú szabály panel adataival kitöltött][2]

Elérési út alapú szabály hozzáadása egy meglévő Alkalmazásátjáró egy könnyen folyamat az Azure portálon keresztül. Elérési út alapú szabály létrehozása után szerkesztheti úgy, hogy további szabályokat tartalmaznak. 

![További elérési-alapú szabályok hozzáadása][3]

Ez a lépés konfigurál egy útvonal-alapú útvonal. Fontos megérteni, hogy a rendszer nem újraírja kérelmeket. Kérelmek térjen, mint az Alkalmazásátjáró megvizsgálja a kérelmet az URL-minta alapján, és a kérést küld a megfelelő háttér-készlethez.

## <a name="next-steps"></a>Következő lépések

Az Azure Application Gateway kiszervezésével SSL konfigurálása, lásd: [SSL kiszervezési Alkalmazásátjáró konfigurálása az Azure-portál használatával](application-gateway-ssl-portal.md).

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png
