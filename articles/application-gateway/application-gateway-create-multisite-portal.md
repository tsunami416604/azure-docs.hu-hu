---
title: "Az Azure Application Gateway több webhelyet |} Microsoft Docs"
description: "Ezen a lapon konfiguráljon egy meglévő Azure-alkalmazásokban átjárót ugyanahhoz az átjáróhoz, és az Azure portál a több webalkalmazás üzemeltetéséhez utasításokat tartalmaz."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 95f892f6-fa27-47ee-b980-7abf4f2c66a9
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 28a7fcb3e08a9c4b6a27e9fbc8d3ebae309adc62
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Konfiguráljon egy meglévő alkalmazás átjárót több webalkalmazás üzemeltetéséhez

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-multisite-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)
> 
> 

Több helyet üzemeltető lehetővé teszi az ugyanazon Alkalmazásátjáró egynél több webalkalmazás telepítését. Az állomásfejlécnek meghatározni, mely figyelő kapja forgalom a bejövő HTTP-kérelmek jelenlétére támaszkodik. A figyelő majd arra utasítja a megfelelő háttérkészlet-forgalom, be az átjáró szabályok meghatározását. Az SSL engedélyezve van a webes alkalmazásokhoz Alkalmazásátjáró a kiszolgálónév jelzése (SNI) bővítménye válassza ki a megfelelő figyelő a webes forgalom támaszkodik. A közös több hely üzemeltetéséhez rendeltetése különböző webtartományok különböző háttér-kiszolgálófiók tárolókészletekben az érkező kérések elosztása. Az azonos gyökértartomány több altartományt hasonló módon is tárolt alkalmazás ugyanahhoz az átjáróhoz.

## <a name="scenario"></a>Forgatókönyv

A következő példában Alkalmazásátjáró van kiszolgáló a contoso.com és fabrikam.com forgalom a két háttér-kiszolgálófiók rendelkezik: contoso kiszolgálókészlet és a fabrikam kiszolgálókészlethez. Hasonló telepítő állomás altartományok például app.contoso.com és blog.contoso.com használható.

![többhelyes forgatókönyv][multisite]

## <a name="before-you-begin"></a>Előkészületek

Ebben a forgatókönyvben egy meglévő Alkalmazásátjáró többhelyes támogatást. A forgatókönyv végrehajtásához, meglévő Alkalmazásátjáró kell konfigurálható. Látogasson el [Alkalmazásátjáró létrehozása a portál használatával](application-gateway-create-gateway-portal.md) hogyan egy alapszintű application gateway létrehozása a portálon.

Az Alkalmazásátjáró frissítéséhez szükséges lépéseket a következők:

1. Az egyes helyek használandó háttér-címkészletek létrehozása.
2. Hozzon létre egy figyelőt a helyekhez Alkalmazásátjáró támogatja.
3. Minden egyes figyelő, amelynek a megfelelő háttér-hozzárendelését szabályokat létrehozni.

## <a name="requirements"></a>Követelmények

* **Háttér-kiszolgálókészlet:** A háttérkiszolgálók IP-címeinek listája. A listán szereplő IP-címeknek a virtuális hálózat alhálózatához kell tartozniuk, vagy nyilvános/virtuális IP-címnek kell lenniük. Teljes Tartománynevét is használható.
* **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtérbeli port:** Az Application Gateway-en megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő:** A figyelő egy előtérbeli porttal, egy protokollal (Http vagy Https, a kis- és a nagybetűk megkülönböztetésével) és SSL tanúsítványnévvel rendelkezik (SSL-kiszervezés konfigurálásakor). A többhelyes engedélyezett alkalmazásátjárót, állomásnév és SNI mutatók is bekerülnek.
* **Szabály:** a szabály van kötve a figyelő, a háttér-kiszolgálófiók-vermet, és határozza meg, mely a forgalom legyenek irányítva, amikor az adott figyelő találatok háttér-kiszolgálófiók készlet. Szabályok feldolgozása a sorrendben, és a forgalmat a rendszer kéri az első szabály, amely megfelel a sajátlagossága figyelembe vétele függetlenül keresztül. Például ha egy szabályt egy alapszintű figyelő és egy többhelyes figyelő mindkét ugyanazt a portot használó szabály, a szabály a többhelyes figyelőjével szerepelnie kell a szabály a vártnak megfelelően működik az alapvető figyelő ahhoz, hogy a többhelyes szabály előtt. 
* **Tanúsítványok:** minden egyes figyelő egy egyedi tanúsítványt igényel, ebben a példában 2 figyelői többhelyes jön létre. Két .pfx-tanúsítványok és azok jelszavait kell létrehozni.

## <a name="create-back-end-pools-for-each-site"></a>Minden egyes hely esetében a háttér-címkészletek létrehozása

A háttér-készlet minden egyes hely esetében, hogy szükség van az alkalmazás átjáró támogatja, ebben az esetben 2 vannak hozható létre, egy a contoso11.com és egy fabrikam11.com.

### <a name="step-1"></a>1. lépés

Nyissa meg az Azure portálon (https://portal.azure.com) meglévő Alkalmazásátjáró. Válassza ki **háttérkészletek** kattintson **hozzáadása**

![háttér-készletek hozzáadása][7]

### <a name="step-2"></a>2. lépés

Írja be az adatokat a háttér-készlet **pool1**, az IP-cím vagy teljes tartománynevek hozzáadása a háttér-kiszolgálókhoz, és kattintson a **OK**

![háttér Készletbeállítások pool1][8]

### <a name="step-3"></a>3. lépés

A háttér-készletek panelen kattintson a **Hozzáadás** hozzáadása egy további háttér címkészletet **pool2**, az IP-cím vagy teljes TARTOMÁNYNEVEK hozzáadása a háttér-kiszolgálókhoz, és kattintson a **OK**

![háttér alkalmazáskészlet pool2 beállításai][9]

## <a name="create-listeners-for-each-back-end"></a>Az egyes háttér-figyelők létrehozása

Az Application Gateway a HTTP 1.1-állomásfejlécek segítségével üzemeltet egynél több webhelyet ugyanarról a nyilvános IP-címről és portról. Az alapvető a portálon létrehozott figyelő nem tartalmazza ezt a tulajdonságot.

### <a name="step-1"></a>1. lépés

Kattintson a **figyelői** a meglévő Alkalmazásátjáró, majd kattintson a **többhelyes** hozzáadása az első figyelő.

![figyelők áttekintése panel][1]

### <a name="step-2"></a>2. lépés

Töltse ki a figyelő adatait. Ebben a példában SSL lezárást van konfigurálva, hozzon létre egy új elülső rétegbeli portot. Az SSL-lezárást használandó PFX-tanúsítvány feltöltése. Az egyetlen különbség a panel a szabványos alapvető figyelő panel képest az állomásnevet.

![figyelő tulajdonságok panelen][2]

### <a name="step-3"></a>3. lépés

Kattintson a **többhelyes** , és hozzon létre egy másik figyelő, a második helyet az előző lépésben leírtak szerint. Ügyeljen arra, hogy egy másik tanúsítványt használ a második figyelő. Az egyetlen különbség a panel a szabványos alapvető figyelő panel képest az állomásnevet. A figyelőre, majd kattintson az adatok **OK**.

![figyelő tulajdonságok panelen][3]

> [!NOTE]
> Figyelők az Azure portálon az Alkalmazásátjáró létrehozása egy hosszú ideig futó feladatot, a hosszabb ideig tart a két figyelői ebben a forgatókönyvben is eltarthat. Ha végzett a figyelők megjelenítése a portálon, az alábbi képen látható módon:

![figyelő áttekintése][4]

## <a name="create-rules-to-map-listeners-to-backend-pools"></a>Figyelők hozzárendelése háttérkészletek szabályok létrehozása

### <a name="step-1"></a>1. lépés

Nyissa meg az Azure portálon (https://portal.azure.com) meglévő Alkalmazásátjáró. Válassza ki **szabályok** , és válassza a meglévő alapértelmezett szabály **Szabály1** kattintson **szerkesztése**.

### <a name="step-2"></a>2. lépés

Töltse ki a szabályok panelt, az alábbi képen látható módon. A figyelő első és az első készlet kiválasztása, és kattintson a **mentése** teljes.

![meglévő szabály szerkesztése][6]

### <a name="step-3"></a>3. lépés

Kattintson a **alapvető szabály** a második szabály létrehozásához. Töltse ki az űrlapot, a második figyelő, a második háttérkészlet, és kattintson a **OK** mentéséhez.

![hozzáadása alapszintű szabály panel][10]

Ez a forgatókönyv befejezése meglévő Alkalmazásátjáró konfigurálása a többhelyes támogatás az Azure portálon keresztül.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan védi meg a webhelyek [Application Gateway - webalkalmazási tűzfal](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png
