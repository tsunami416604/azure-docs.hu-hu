---
title: "Hozzon létre egy egyéni mintavételt - Azure Application Gateway - Azure portálon |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy egyéni mintavétel az Alkalmazásátjáró a portál használatával"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: davidmu
ms.openlocfilehash: bb77c9b39e1aa89f6411de8ec3b1fca41e954bf2
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Hozzon létre egy egyéni mintavétel az Alkalmazásátjáró a portál használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-probe-classic-ps.md)

Ebben a cikkben ad hozzá egy egyéni mintavételt meglévő Alkalmazásátjáró az Azure portálon keresztül. Egyéni mintavételt az alkalmazásokat, amelyek egy adott állapotának ellenőrzése lapon vagy az alapértelmezett webes alkalmazás a sikeres válasz nem biztosító alkalmazások hasznosak.

## <a name="before-you-begin"></a>Előkészületek

Ha még nem rendelkezik olyan átjárót, látogasson el [Alkalmazásátjáró létrehozása](application-gateway-create-gateway-portal.md) történő együttműködésre Alkalmazásátjáró létrehozása.

## <a name="createprobe"></a>A mintavétel létrehozása

Mintavételt egy kétlépéses folyamat, a portálon keresztül lehet konfigurálni. Az első lépés, ha a mintavételi. A második lépésben adja hozzá a mintavétel a backendhttpsettings osztályhoz az Alkalmazásátjáró.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). Ha már nincs fiókja, regisztrálhat az egy [ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/free)

1. Az Azure portál Kedvencek ablaktábláján kattintson összes erőforrást. Kattintson az összes erőforrások panelen az Alkalmazásátjáró. Ha már kijelölt előfizetésben több erőforrást tartalmaz, megadhatja partners.contoso.net a szűrő név szerint... mezőbe.

1. Kattintson a **vizsgálat** , és kattintson a **hozzáadása** vizsgálatok hozzáadása gomb.

  ![Adja hozzá a mintavételi panel tölti ki adatokkal][1]

1. Az a **Hozzáadás állapotmintáihoz** panelen, töltse ki a mintavétel a szükséges adatokat, és kattintson teljes **OK**.

  |**Beállítás** | **Érték** | **Részletek**|
  |---|---|---|
  |**Name (Név)**|customProbe|Ez az érték egy rövid nevet az vizsgálatot, amely elérhető a portál.|
  |**Protocol (Protokoll)**|HTTP vagy HTTPS | A protokoll, amely a állapotmintáihoz használja.|
  |**Állomás**|Egytényezős contoso.com|Ez az érték a gazdagép neve a mintavétel használt. Alkalmazandó csak akkor, ha több hely van beállítva az alkalmazás-átjárón, ellenkező esetben használja a "127.0.0.1". Ez az érték eltér a virtuális gép állomásnevét.|
  |**Elérési út**|/ vagy egy másik elérési utat|A teljes URL-címet az egyéni vizsgálat további része. Érvényes elérési utat kezdődik "/". Az alapértelmezett elérési útja pedig csak http://contoso.com használja "/" |
  |**Időtartam (másodperc)**|30|Milyen gyakran a mintavétel futtassa a állapotának ellenőrzése. Nem ajánlott beállítani az alacsonyabb mint 30 másodperc.|
  |**Időkorlátja (másodperc)**|30|A mintavétel vár, mielőtt túllépné idő mennyiségét. Az időkorlát kell lennie, elég nagy, hogy egy http hívni is győződjön meg arról, a háttér health-oldal érhető el.|
  |**Sérült küszöbérték**|3|Akkor, ha a nem megfelelő sikertelen bejelentkezési kísérletek számát. 0, akkor a küszöbérték, ha a rendszerállapot-ellenőrzés sikertelen a háttér-határozza meg. a nem megfelelő azonnal.|

  > [!IMPORTANT]
  > Az állomás neve megegyezik nem a kiszolgáló nevét. Ez az érték az alkalmazáskiszolgáló fut a virtuális gazdagép nevét. A mintavétel a rendszer elküldi http://(host name):(port from httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Mintavételi hozzáadása az átjáró

Most, hogy a mintavétel létrehozása után adja hozzá az átjáró időt is. A backendhttpsettings osztályhoz az Alkalmazásátjáró mintavételi beállítások vannak megadva.

1. Kattintson a **HTTP-beállítások** az alkalmazás-átjárón nyissa meg a konfigurációs panelen kattintson az aktuális háttér http-beállítások szerepel az ablak.

  ![HTTPS-beállítások ablak][2]

1. Az a **appGatewayBackEndHttpSettings** beállítások panelen ellenőrizze a **használata egyéni tesztműveleti** jelölőnégyzetet, és válassza ki a létrehozott mintavétel a [a mintavétel létrehozása](#createprobe) szakaszt, a  **Egyéni tesztműveleti** legördülő...
Amikor végzett, kattintson **mentése** és a beállítások lesznek alkalmazva.

Az alapértelmezett mintavétel az alapértelmezett hozzáférési webalkalmazás ellenőrzi. Most, hogy egy egyéni mintavétel létrehozása után az Alkalmazásátjáró használja a háttérkiszolgálókhoz állapotának figyeléséhez definiált egyéni elérési utat. A megadott feltétel alapján, az Alkalmazásátjáró ellenőrzi a mintavétel a megadott elérési út. Ha a gazdagép és a portszám hívása / elérési út nem ad vissza egy HTTP 200-399 állapotválasz, a kiszolgáló kívül Elforgatás használatban van, a sérült küszöbérték elérése után. Probing továbbra is a nem megfelelő állapotú példányon megállapítani, hogy mikor válik kifogástalan újra. A példány vissza megfelelő kiszolgálókészlethez hozzáadott, forgalom kezdődik áramló rá, és a példányra probing továbbra is fennáll, mint a normál felhasználói megadott időközönként.

## <a name="next-steps"></a>Következő lépések

SSL-Feladatkiszervezést konfigurálása Azure Application Gateway kapcsolatban [SSL-kiszervezés konfigurálása](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

