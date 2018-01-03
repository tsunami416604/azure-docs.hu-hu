---
title: "SSL kiszervezési - konfigurálása Azure Application Gateway - Azure portálon |} Microsoft Docs"
description: "Ez a cikk ismerteti az utasításokat követve hozzon létre egy alkalmazás SSL-kiszervezés az Azure portál használatával"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 2f7f5d4132e28c8c192d90d5f4bfb2a9034f8b8c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-azure-portal"></a>SSL kiszervezési Alkalmazásátjáró konfigurálása az Azure-portál használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Az Azure klasszikus PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Az Azure Application Gateway konfigurálható úgy, hogy leállítsa a Secure Sockets Layer (SSL) munkamenetét az átjárónál, így elkerülhetők a költséges SSL visszafejtési feladatok a webfarmon. Az SSL-alapú kiszervezés emellett leegyszerűsíti az előtér-kiszolgáló számára webalkalmazás telepítését és kezelését.

## <a name="scenario"></a>Forgatókönyv

A következő forgatókönyv végigvezeti Önt egy meglévő Alkalmazásátjáró SSL kiszervezési konfigurálása. A forgatókönyv azt feltételezi, hogy lépéseit már követte [Alkalmazásátjáró létrehozása](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Előkészületek

Az Alkalmazásátjáró SSL kiszervezési konfigurálásához egy tanúsítványra szükség. Ez a tanúsítvány be töltve az alkalmazás-átjárón, és adatok titkosítása és visszafejtése a keresztül SSL használatával. Személyes információcsere (.pfx) formátumban lennie kell a tanúsítványt. Ezt a formátumot exportálja a titkos kulcsot, a titkosítási és visszafejtési forgalom az alkalmazás-átjáró által igényelt teszi lehetővé.

## <a name="add-an-https-listener"></a>Adja hozzá a HTTPS-figyelő

A HTTPS-figyelő konfigurációja alapján keres, és segít irányíthatja a forgalmat a háttér-tárolókészletekben. Egy HTTPS-figyelő hozzáadásához kövesse az alábbi lépéseket:

   1. Keresse meg az Azure-portálon, és válassza ki a meglévő Alkalmazásátjáró.

   2. Válassza ki **figyelői**, majd válassza ki a **Hozzáadás** gombra kattintva adja hozzá egy figyelő.

   ![Alkalmazás átjáró áttekintése ablaktáblájának][1]


   3. Töltse ki a figyelő a következő szükséges adatokat, és a PFX-tanúsítvány feltöltése:
      - **Név**: a figyelő rövid nevét.

      - **Előtérbeli IP-konfiguráció**: a figyelő használt előtér-IP-konfigurációja.

      - **Elülső rétegbeli portot (név/Port)**: egy rövid nevet az Alkalmazásátjáró és a tényleges használt port megszüntetni használt port.

      - **Protokoll**: határozza meg, ha HTTPS vagy HTTP Protokollt használja az előtér váltani.

      - **Tanúsítvány (és jelszó)**: Ha SSL kiszervezési szolgál, a .pfx-tanúsítványra szükség a ezt a beállítást. Felhasználóbarát név és jelszó is szükség.

   4. Kattintson az **OK** gombra.

![Egy figyelő ablak hozzáadása][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Hozzon létre egy szabályt, és rendelje hozzá azt a figyelőt

A figyelő létrehozása megtörtént. Ezután hozzon létre egy szabályt, amely a figyelő a forgalom kezelésére. Szabályok határozzák meg, hogyan továbbítódik a háttér-címkészletek több konfigurációs beállítás alapján. Ezek a beállítások tartalmazzák a protokollt, a port és a rendszerállapot mintavételek menüpontban, és hogy-e a munkamenet cookie-alapú kapcsolat használata. Hozzon létre, és rendelje hozzá a szabály a figyelőt, kövesse az alábbi lépéseket:


   1. Válassza ki a **szabályok** az Alkalmazásátjáró, és válassza a **Hozzáadás**.

   ![Alkalmazás átjáró szabályok ablak][3]


   2. A **hozzáadása alapszintű szabály**, adjon egy rövid nevet a szabály a **neve** mezőben, majd válassza ki a **figyelő** az előző lépésben létrehozott. Válassza ki a megfelelő **háttérkészlet** és **HTTP beállítása**, majd válassza ki **OK**.

   ![HTTPS-beállítások ablak][4]

Az Alkalmazásátjáró most menti a beállításokat. A mentési feldolgozni az ezeket a beállításokat eltarthat egy kis ideig, után lesznek a portálon keresztül vagy a Powershellen keresztül. Után azok mentésekor, az Alkalmazásátjáró kezeli a titkosítási és visszafejtési forgalom. Az Alkalmazásátjáró és a háttér-webkiszolgálók közötti összes forgalom kezelése HTTP Protokollon keresztül. Minden kommunikáció az ügyfél által kezdeményezett HTTPS, ha visszatér az ügyfélhez titkosított.

## <a name="next-steps"></a>További lépések

Egy egyéni állapotmintáihoz konfigurálása Azure Application Gateway kapcsolatban [hozzon létre egy egyéni állapotmintáihoz](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

