---
title: Azure webalkalmazási tűzfal és Azure Policy
description: Az Azure webalkalmazási tűzfal (WAF) és a Azure Policy együttes használata segíthet a szervezeti szabványok betartatásában és a WAF-erőforrások megfelelőségének felmérésében
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: rimansdo
ms.openlocfilehash: 4c1fd53eb6ebf1f1aebdfba99b736e26bd6cff2b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85306912"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure webalkalmazási tűzfal és Azure Policy

Az Azure webalkalmazási tűzfal (WAF) és a Azure Policy együttes használata segíthet a szervezeti szabványok betartatásában és a WAF-erőforrások megfelelőségének felmérésében. Az Azure Policy egy irányítási eszköz, amely összesített nézetet biztosít a környezet általános állapotának kiértékeléséhez, és lehetővé teszi, hogy az erőforrás-és a házirendek részletessége alapján részletezhető legyen. Az Azure Policy emellett a meglévő erőforrások tömeges szervizelése és az új erőforrások automatikus szervizelése révén is lehetővé teszi az erőforrások megfelelőségét.

## <a name="azure-policies-for-web-application-firewall"></a>Azure-szabályzatok webalkalmazási tűzfalhoz

A WAF-erőforrások kezeléséhez számos beépített Azure-szabályzat található. A szabályzatok és funkcióik részletezése a következő:

1. **Engedélyezni kell a webalkalmazási tűzfalat az Azure bejárati szolgáltatásának vagy Application Gateway**: az Azure bejárati szolgáltatásai és az alkalmazás-átjárók kiértékelése akkor történik meg, ha az erőforrás-létrehozási WAF létezik. A házirendnek három hatása van: naplózás, megtagadás és letiltás. A naplózás nyomon követi, ha egy Azure bejárati szolgáltatás vagy Application Gateway nem rendelkezik WAF, és lehetővé teszi, hogy a felhasználók lássák, milyen Azure bejárati szolgáltatás vagy Application Gateway jelenleg nem felel meg a követelményeknek. A Megtagadás megakadályozza, hogy a rendszer ne hozzon létre Azure bejárati szolgáltatást vagy Application Gateway, ha nincs WAF csatlakoztatva. A Letiltva beállítás kikapcsolja ezt a házirendet.

2. A **webalkalmazási tűzfalnak be kell állítani a Application Gateway és az Azure bejárati ajtó szolgáltatást**: a webalkalmazási tűzfal kiértékelése, hogy milyen módban van, vagy megelőzés vagy észlelés. A szabályzat a webalkalmazási tűzfalak között biztosítja a mód egységességét. A házirendnek három hatása van: naplózás, megtagadás és letiltás. A naplózás nyomon követi, ha egy WAF nem felel meg a megadott módnak. A Megtagadás megakadályozza, hogy a rendszer ne hozzon létre WAF, ha az nem megfelelő módban van. A Letiltva beállítás kikapcsolja ezt a házirendet.


## <a name="launch-an-azure-policy"></a>Azure Policy elindítása


1.  Az Azure kezdőlapján írja be a házirend kifejezést a keresősáv mezőbe, és kattintson a Azure Policy ikonra.

2.  Az Azure Policy szolgáltatás **Létrehozás**területén válassza a **hozzárendelések**lehetőséget.

![Azure webalkalmazási tűzfal](../media/waf-azure-policy/policy-home.png)

3.  A hozzárendelések lapon válassza a felül a **házirend hozzárendelése** ikont.

![Azure webalkalmazási tűzfal](../media/waf-azure-policy/assign-policy.png)

4.  A házirend-hozzárendelési lap alapjai lapon frissítse a következő mezőket:
    1.  **Hatókör**: válassza ki, hogy melyik Azure-előfizetéseket és-erőforráscsoportokat kell érintenie a Azure Policy.
    2.  **Kizárások**: válassza ki a hatókörből azokat az erőforrásokat, amelyeket ki szeretne zárni a szabályzatból 
    3.  **Házirend-definíció**: válassza ki azokat a Azure Policy, amelyekre alkalmazni kívánja a hatókört a kizárásokkal. Írja be a "webalkalmazási tűzfal" kifejezést a keresőmezőbe a megfelelő webalkalmazási tűzfal Azure Policy kiválasztásához.

![Azure webalkalmazási tűzfal](../media/waf-azure-policy/policy-listings.png)


5.  Válassza a **Parameters (paraméterek** ) fület, és frissítse a házirendek paramétereit. A paraméter nevének további tisztázásához vigye a kurzort a paraméter neve melletti információs ikonra a további pontosításhoz.

6.  Az Azure-szabályzat véglegesítéséhez válassza a **felülvizsgálat + létrehozás** lehetőséget. Az Azure-szabályzat körülbelül 15 percet vesz igénybe, amíg az új erőforrások nem lesznek aktívak.
