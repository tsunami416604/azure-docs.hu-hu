---
title: "Hozzon létre vagy meglévő Alkalmazásátjáró frissítése webalkalmazási tűzfal |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy alkalmazást a webalkalmazási tűzfal a portál használatával"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: bfc06c1b44974fd17a3794654503d21d6407a917
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-by-using-the-portal"></a>Hozzon létre egy alkalmazást a webalkalmazási tűzfal a portál használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

Megtudhatja, hogyan hozzon létre egy webes alkalmazás tűzfalat (waf-ot)-Alkalmazásátjáró engedélyezve van.

Az Azure alkalmazás átjáró WAF webalkalmazások védje a közös web-alapú támadások, például az SQL-injektálás, a többhelyes parancsfájlok futtatására és a munkamenet kihasználásának. Egy WAF számos OWASP felső 10 közös webes biztonsági rések elleni védelmet nyújt.

## <a name="scenarios"></a>Forgatókönyvek

Ez a cikk két esetben mutatja be. Az első forgatókönyv, elsajátíthatja, hogyan [hozzon létre egy alkalmazást egy WAF](#create-an-application-gateway-with-web-application-firewall). A második esetben megismerheti, hogyan [egy WAF hozzáadása egy meglévő Alkalmazásátjáró](#add-web-application-firewall-to-an-existing-application-gateway).

![Példaforgatókönyv][scenario]

> [!NOTE]
> Az Alkalmazásátjáró egyéni állapotteljesítmény, a háttér-készlet címek és a további szabályok adhat hozzá. Ezeket az alkalmazásokat úgy vannak konfigurálva, az alkalmazás-átjáró konfigurálása után, és nem a kezdeti üzembe helyezése során.

## <a name="before-you-begin"></a>Előkészületek

 Alkalmazásátjáró saját alhálózatba van szükség. Amikor létrehoz egy virtuális hálózatot, győződjön meg arról, hogy hagyja-e elég hely a cím több alhálózattal rendelkezik. Alkalmazásátjáró alhálózathoz telepítése után csak további alkalmazásátjárót lehet hozzáadni az alhálózathoz.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Webalkalmazási tűzfal hozzáadása egy meglévő Alkalmazásátjáró

Ebben a példában egy WAF a támogatásához egy meglévő Alkalmazásátjáró frissítése **megelőzési** mód.

1. Az Azure portálon **Kedvencek** ablaktáblán válassza előbb **összes erőforrás**. Az a **összes erőforrás** panelen válassza ki a meglévő Alkalmazásátjáró. Ha már kijelölt előfizetésben több erőforrást tartalmaz, adja meg a nevét a **Szűrés név alapján** könnyen hozzáférés mezőben a DNS-zónát.

   ![Meglévő alkalmazás átjáró kiválasztása][1]

2. Válassza ki **webalkalmazási tűzfal**, és az alkalmazás-átjáró beállításainak frissítése. A frissítés befejezése után válassza ki a **mentése**. 

3. A következő beállítások segítségével egy WAF támogatásához meglévő Alkalmazásátjáró frissítése:

   | **Beállítás** | **Érték** | **Részletek**
   |---|---|---|
   |**Váltson WAF-csomagra**| Bejelölve | Ez a beállítás az Alkalmazásátjáró WAF csomagra szintjének beállítása.|
   |**Tűzfal állapota**| Engedélyezve | Ez a beállítás lehetővé teszi, hogy a WAF a tűzfalat.|
   |**Tűzfal módban** | Megelőzés | Ez azért, hogy egy WAF hogyan kezelje a forgalmat. **Észlelési** mód csak naplózza az eseményeket. **Megelőzési** mód az eseményeket naplózza, és leállítja a forgalmat.|
   |**Szabálykészlete**|3.0|Ez a beállítás meghatározza, hogy a [szabálykészlet alapvető](application-gateway-web-application-firewall-overview.md#core-rule-sets) , hogy a háttér-a készlet tagjainak védelmére szolgál.|
   |**Letiltott szabályok konfigurálása**|Változó|Lehetséges téves megelőzése érdekében segítségével ezt a beállítást letilthatja egyes [szabályok és a csoportok](application-gateway-crs-rulegroups-rules.md).|

    >[!NOTE]
    > Amikor frissít egy meglévő Alkalmazásátjáró a WAF másikra, a Termékváltozat-méretét vált **Közepes**. Konfiguráció befejezése után újrakonfigurálhatja az ezt a beállítást.

    ![Alapbeállítások][2-1]

    > [!NOTE]
    > WAF naplók megtekintése, diagnosztika engedélyezése, és válassza ki a **ApplicationGatewayFirewallLog**. Válassza ki a példányszámnak **1** csak tesztelési célokra. Nem ajánlott a példányszám **2** , mert nem fedi a szolgáltatásiszint-szerződést. Kis átjárók nem érhetők el, ha egy WAF használja.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Webalkalmazási tűzfal Alkalmazásátjáró létrehozása

Ez a forgatókönyv tartalma:

* Hozzon létre egy közepes méretű WAF Alkalmazásátjáró két példányt.
* Nevű AdatumAppGatewayVNET egy fenntartott CIDR-blokkja 10.0.0.0/16, a virtuális hálózat létrehozása.
* Létrehoz egy Appgatewaysubnet nevű alhálózatot, amelynek a CIDR-blokkja 10.0.0.0/28 lesz.
* Kiszervezési SSL tanúsítvány konfigurálása.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha már nincs fiókja, regisztrálhat az egy [ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/free).

2. Az a **Kedvencek** ablaktáblán a portálon, válassza a **új**.

3. Az a **új** panelen válassza **hálózati**. Az a **hálózati** panelen válassza **Alkalmazásátjáró**, a következő ábrán látható módon:

    ![Alkalmazás átjáró létrehozása][1]

4. Az a **alapjai** panel, amelyen megjelenik, írja be a következő értékeket, és válassza **OK**:

   | **Beállítás** | **Érték** | **Részletek**
   |---|---|---|
   |**Name (Név)**|AdatumAppGateway|Az Alkalmazásátjáró neve.|
   |**Réteg**|WAF|Lehetséges értékek a következők: Standard és a WAF. Egy WAF kapcsolatos további információkért lásd: [webalkalmazási tűzfal](application-gateway-web-application-firewall-overview.md).|
   |**Termékváltozat-méretét**|Közepes|Standard szint beállítások **kis**, **Közepes**, és **nagy**. WAF réteg beállítások **Közepes** és **nagy** csak.|
   |**A példányok száma**|2|A magas rendelkezésre álláshoz az Alkalmazásátjáró példányainak száma. Példányok számát 1 használata csak tesztelési célokra.|
   |**Előfizetés**|[Az Ön előfizetése]|Válasszon ki egy előfizetést hozhat létre az Alkalmazásátjáró.|
   |**Erőforráscsoport**|**Új:** AdatumAppGatewayRG|Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül. Az erőforráscsoportokkal kapcsolatos további információkért olvassa el [A Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) áttekintése című cikket.|
   |**Hely**|USA nyugati régiója||

   ![Alapvető beállítások][2-2]

5. Az a **beállítások** panel alatt megjelenő **virtuális hálózati**, jelölje be **virtuális hálózatot választ**. Az a **válasszon virtuális hálózati** panelen válassza **hozzon létre új**.

   ![Virtuális hálózat kiválasztása][2]

6. Az a **hozzon létre virtuális hálózat panel**, adja meg a következő értékeket, és válassza **OK**. A **alhálózati** található a **beállítások** panelen kiválasztott alhálózat a telepítéskor.

   |**Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Name (Név)**|AdatumAppGatewayVNET|Az Alkalmazásátjáró neve.|
   |**Címtér**|10.0.0.0/16| Ez az érték a virtuális hálózat a címtér.|
   |**Alhálózat neve**|AppGatewaySubnet|Az Alkalmazásátjáró az alhálózat neve.|
   |**Alhálózati címtartomány**|10.0.0.0/28 | Ez az alhálózat lehetővé teszi, a háttér-a készlet tagjainak több további alhálózatokat a virtuális hálózat.|

7. Az a **beállítások** részen **előtérbeli IP-konfiguráció**, jelölje be **nyilvános** , a **IP-cím típusa**.

8. Az a **beállítások** részen **nyilvános IP-cím**, jelölje be **egy nyilvános IP-cím kiválasztása**. Az a **nyilvános IP-cím kiválasztása** panelen válassza **hozzon létre új**.

   ![Nyilvános IP-cím kiválasztása][3]

9. Az a **nyilvános IP-cím létrehozása** panelen fogadja el az alapértelmezett értéket, és válassza ki **OK**. A **nyilvános IP-cím** mező a telepítéskor választott nyilvános IP-címét.

10. Az a **beállítások** részen **figyelő konfigurációs**, jelölje be **HTTP** alatt **protokoll**. A tanúsítvány használatához szükséges **HTTPS**. A tanúsítványhoz tartozó titkos kulcs szükséges. Adjon meg egy .pfx tanúsítvány exportálása, majd adja meg a jelszót a fájlt.

11. Az adott beállításainak a konfigurálásához a **WAF**.

   |**Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Tűzfal állapota**| Engedélyezve| Ez a beállítás be- vagy kikapcsolja a WAF.|
   |**Tűzfal módban** | Megelőzés| Ez a beállítás határozza meg a WAF időt vesz igénybe, a rosszindulatú forgalom műveleteket. **Észlelési** mód csak a forgalom naplózza. **Megelőzési** mód naplózza, és leállítja a forgalom 403-as jogosulatlan választ.|


12. Tekintse át a **összegzés** lapon, és válassza ki **OK**. Az Alkalmazásátjáró most sorba és létre.

13. Átjáró létrehozása után az alkalmazás, keresse meg a portál az alkalmazás-átjáró konfigurációs folytatja azt.

    ![Alkalmazás átjáró erőforrás-kihasználása nézetét][10]

Ezeket a lépéseket egy alapszintű application gateway a figyelő, a háttér-készlet, a háttér-HTTP-beállítások és a szabályok az alapértelmezett beállításokkal hozza létre. A létesítési befejeztét követően sikeresen, módosíthatja ezeket a beállításokat, a környezet igényeinek megfelelően.

> [!NOTE]
> Az alapvető beállításokkal WAF létre alkalmazásátjárót védelmet CRS 3.0-val van állítva.

## <a name="next-steps"></a>Következő lépések

Az egyéni tartomány aliasa konfigurálása a [nyilvános IP-cím](../dns/dns-custom-domain.md#public-ip-address), használhatja az Azure DNS- vagy egy másik DNS-szolgáltatónál.

Diagnosztikai naplózás jelentkezzen az eseményeket, és megakadályozta WAF konfigurálása, lásd: [Alkalmazásátjáró diagnosztika](application-gateway-diagnostics.md).

Egyéni állapotteljesítmény létrehozásához lásd: [hozzon létre egy egyéni állapotmintáihoz](application-gateway-create-probe-portal.md).

Konfigurálja az SSL-feladatkiszervezést, és tegye meg a költséges SSL előfizetés ki a webkiszolgálók, lásd: [SSL konfigurálása kiszervezési](application-gateway-ssl-portal.md).

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png
