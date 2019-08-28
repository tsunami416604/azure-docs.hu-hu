---
title: Egyéni mintavétel létrehozása – Azure Application Gateway – Azure Portal | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egyéni mintavételt a Application Gatewayhoz a portál használatával
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: b92b9d953b6dd941b8b5f445ad64059f557c2980
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061792"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Egyéni mintavétel létrehozása Application Gatewayhoz a portál használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-probe-classic-ps.md)

Ebben a cikkben egy egyéni mintavételt ad hozzá egy meglévő Application Gateway-hez a Azure Portalon keresztül. Az egyéni mintavételek olyan alkalmazások esetében hasznosak, amelyek adott állapot-ellenőrzési oldallal rendelkeznek, illetve olyan alkalmazásokhoz, amelyek nem adnak választ az alapértelmezett webalkalmazásra.

## <a name="before-you-begin"></a>Előkészületek

Ha még nem rendelkezik Application Gateway-rel, látogasson el a [Application Gateway létrehozására](application-gateway-create-gateway-portal.md) , és hozzon létre egy Application Gateway-t, amely együttműködik a szolgáltatással.

## <a name="createprobe"></a>A mintavétel létrehozása

A mintavétel két lépésből álló folyamaton keresztül történik a portálon. Első lépésként hozza létre a mintavételt. A második lépésben hozzáadja a mintavételt az Application Gateway háttérbeli http-beállításaihoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nem rendelkezik fiókkal, regisztrálhat egy [hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free)

1. A Azure Portal Kedvencek ablaktáblán kattintson az összes erőforrás elemre. Kattintson az Application Gateway elemre a minden erőforrás panelen. Ha a kiválasztott előfizetés már rendelkezik több erőforrással, a partners.contoso.net megadhatja a szűrés név szerint... mezőbe.

1. Kattintson a mintavételek elemre, majd a **Hozzáadás** gombra a mintavétel hozzáadásához.

   ![Mintavételi panel hozzáadása a kitöltött információkkal][1]

1. Az **állapotfelmérés hozzáadása** panelen töltse ki a mintavételhez szükséges adatokat, és ha elkészült, kattintson **az OK gombra**.

   |**Beállítás** | **Érték** | **Részletek**|
   |---|---|---|
   |**Name**|customProbe|Ez az érték egy rövid név a mintavétel számára, amely elérhető a portálon.|
   |**Protocol (Protokoll)**|HTTP vagy HTTPS | Az állapotfelmérés által használt protokoll.|
   |**Gazdagép**|azaz contoso.com|Ez az érték a mintavételhez használt állomásnév. Csak akkor alkalmazható, ha a többhelyes Application Gateway van konfigurálva, ellenkező esetben a "127.0.0.1"-t használja. Ez az érték eltér a virtuális gép gazdagépének nevétől.|
   |**Path**|/vagy egy másik elérési út|Az egyéni mintavétel teljes URL-címének fennmaradó része. Egy érvényes elérési út a következővel kezdődik: "/". A http:\//contoso.com alapértelmezett elérési útja csak a "/" értéket használja |
   |**Időköz (mp)**|30|Milyen gyakran fut a mintavétel az állapot kereséséhez. A 30 másodpercnél kisebb értéket nem ajánlott beállítani.|
   |**Időkorlát (mp)**|30|Az az időtartam, ameddig a mintavétel időtúllépés előtt várakozik. Az időtúllépési intervallumnak elég magasnak kell lennie ahhoz, hogy http-hívást lehessen biztosítani, hogy a háttér állapota lap elérhető legyen.|
   |**Nem Kifogástalan állapot küszöbértéke**|3|A nem kifogástalannak minősülő sikertelen kísérletek száma. A küszöbérték értéke 1 vagy több lehet.|

   > [!IMPORTANT]
   > Az állomásnév nem egyezik meg a kiszolgáló nevével. Ez az érték az alkalmazáskiszolgáló által futtatott virtuális gazdagép neve. A mintavétel a http://(állomásnév):(portra érkezik a httpsetting-ból)/urlPath

## <a name="add-probe-to-the-gateway"></a>Mintavétel hozzáadása az átjáróhoz

Most, hogy létrejött a mintavétel, itt az ideje, hogy hozzáadja az átjáróhoz. A mintavételi beállítások az Application Gateway háttérbeli http-beállításainál állíthatók be.

1. Kattintson a **http-beállítások** elemre az Application gatewayben a konfiguráció panel megjelenítéséhez, majd kattintson az ablakban az aktuális háttérbeli http-beállítások elemre.

   ![https-beállítások ablak][2]

1. A **appGatewayBackEndHttpSettings** -beállítások panelen jelölje be az **Egyéni mintavétel használata** jelölőnégyzetet, és válassza ki a mintavétel [létrehozása](#createprobe) szakaszban létrehozott mintavételt az **Egyéni** mintavétel legördülő menüben.
   Ha elkészült, kattintson a **Mentés** gombra, és alkalmazza a beállításokat.

Az alapértelmezett mintavétel ellenőrzi az alapértelmezett hozzáférést a webalkalmazáshoz. Most, hogy létrehozta az egyéni mintavételt, az Application Gateway a háttér-kiszolgálók állapotának figyeléséhez megadott egyéni elérési utat használja. A definiált feltételek alapján az Application Gateway ellenőrzi a mintavételben megadott elérési utat. Ha a gazdagépre irányuló hívás: a port/elérési út nem ad vissza HTTP 200-399-es állapotra vonatkozó választ, a rendszer elveszi a kiszolgálót a nem kifogástalan küszöbérték elérésekor. A szondázás a nem kifogástalan állapotú példányon folytatja annak meghatározását, hogy mikor válik újra. Ha a példányt visszaadja a kifogástalan állapotú kiszolgáló készletnek, a forgalom újra áramlik, és a példányhoz való szondázás a szokásosnál továbbra is a felhasználó által megadott időközönként folytatódik.

## <a name="next-steps"></a>További lépések

Az SSL-alapú kiszervezésnek az Azure Application Gateway használatával történő konfigurálásával kapcsolatos további információkért lásd: SSL-kiszervezés [konfigurálása](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

