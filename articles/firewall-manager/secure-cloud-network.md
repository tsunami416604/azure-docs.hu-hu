---
title: 'Oktatóanyag: a Azure Firewall Manager előzetes verziójának használatával biztonságossá teheti a felhőalapú hálózatot a Azure Portal'
description: Ebből az oktatóanyagból megtudhatja, hogyan védheti meg a felhőalapú hálózatot a Azure Firewall Managerrel a Azure Portal használatával.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: victorh
ms.openlocfilehash: d2ebfd6003c0bc2b47636be1e38f47e554cc6988
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501910"
---
# <a name="tutorial-secure-your-cloud-network-with-azure-firewall-manager-preview-using-the-azure-portal"></a>Oktatóanyag: a felhőalapú hálózat védelme a Azure Firewall Manager előzetes verziójával a Azure Portal használatával

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

A Azure Firewall Manager előzetes verziójának használatával biztonságos hubokat hozhat létre a privát IP-címekre, az Azure Pástire és az internetre irányuló Felhőbeli hálózati forgalom biztonságossá tételéhez. A tűzfal felé irányuló forgalom-útválasztás automatizált, így nem kell felhasználó által megadott útvonalakat (UDR-ket) létrehoznia.

![a felhőalapú hálózat védelme](media/secure-cloud-network/secure-cloud-network.png)

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> A Azure Firewall Manager előzetes verzióját explicit módon engedélyezni kell a `Register-AzProviderFeature` PowerShell-paranccsal.

Futtassa a következő parancsokat egy PowerShell-parancssorból:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
A szolgáltatás regisztrációjának befejezéséhez akár 30 percet is igénybe vehet. A regisztráció állapotának megtekintéséhez futtassa a következő parancsot:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="create-a-hub-and-spoke-architecture"></a>Sugaras architektúra létrehozása

Először hozzon létre egy küllős VNet, amelyen elhelyezheti a kiszolgálókat.

### <a name="create-a-spoke-vnet-and-subnets"></a>Küllős VNet és alhálózatok létrehozása

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása**lehetőséget.
2. A **hálózat**területen válassza a **virtuális hálózat**lehetőséget.
4. A **név**mezőbe írja be a **küllő-01**értéket.
5. A **Címtér** mezőbe írja be a következőt: **10.0.0.0/16**.
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **erőforráscsoport**területen válassza az **új létrehozása**lehetőséget, és írja be a név mezőbe az **FW-Manager** nevet, majd kattintson **az OK gombra**.
8. A hely mezőben válassza ki az USA **keleti** **régióját**.
9. Az **alhálózat**alatt a **név** típusa **munkaterhelés-SN**.
10. A **Címtartomány** mezőbe írja be a következőt: **10.0.1.0/24**.
11. Fogadja el a többi alapértelmezett beállítást, majd kattintson a **Létrehozás**gombra.

Ezután hozzon létre egy alhálózatot a Jump Serverhez.

1. A Azure Portal kezdőlapon válassza az **erőforráscsoportok** > az **FW-Manager**elemet.
2. Válassza ki a **küllő-01** virtuális hálózatot.
3. Válassza az **Alhálózatok** >  **+ alhálózat**lehetőséget.
4. A **név**mezőbe írja be a következőt: **Jump-SN**.
5. A **Címtartomány** mezőbe írja be a következőt: **10.0.2.0/24**.
6. Kattintson az **OK** gombra.

### <a name="create-the-secured-virtual-hub"></a>A biztonságos virtuális központ létrehozása

Hozza létre a biztonságos virtuális hubot a Firewall Manager használatával.

1. A Azure Portal kezdőlapon válassza a **minden szolgáltatás**lehetőséget.
2. A keresőmezőbe írja be a **Firewall Manager** kifejezést, majd válassza a **Firewall Manager**lehetőséget.
3. A **Firewall Manager** lapon válassza a **biztonságos virtuális központ létrehozása**elemet.
4. Az **új biztonságos virtuális központ létrehozása** lapon válassza ki az előfizetését és az **FW-Manager** erőforráscsoportot.
5. A **biztonságos virtuális központ neve**mezőbe írja be a következőt: **hub-01**.
6. A hely mezőben válassza az **USA keleti** **régiója**lehetőséget.
7. A **hub címterület**mezőbe írja be a következőt: **10.1.0.0/16**.
8. Az új vWAN neve mezőbe írja be a következőt: **vWAN-01**.
9. Hagyja bejelölve a **VPN Gateway belefoglalása a megbízható biztonsági partnerek engedélyezése** jelölőnégyzetet.
10. Válassza a Next (tovább) lehetőséget **: Azure Firewall**.
11. Fogadja el az alapértelmezett **Azure Firewall** **engedélyezve** beállítást, majd válassza a **Tovább: megbízható biztonsági partner**lehetőséget.
12. Fogadja el az alapértelmezett **megbízható biztonsági partner** **letiltott** beállítást, majd kattintson a **Tovább gombra: felülvizsgálat + létrehozás**.
13. Kattintson a **Létrehozás** gombra. A telepítés körülbelül 30 percet vesz igénybe.

### <a name="connect-the-hub-and-spoke-vnets"></a>A hub és a küllős virtuális hálózatok összekötése

Most már elvégezheti a hub és a küllő virtuális hálózatok.

1. Válassza ki az **FW-Manager** erőforráscsoportot, majd válassza ki a **vwan-01** virtuális WAN elemet.
2. A **kapcsolat**területen válassza a **virtuális hálózati kapcsolatok**elemet.
3. Válassza a **kapcsolatok hozzáadása**lehetőséget.
4. A **kapcsolatok neve**mezőbe írja be a következőt: **hub-küllő**.
5. **Hubok**esetében válassza a **hub-01**elemet.
6. **Virtuális hálózat**esetén válassza a **küllő-01**elemet.
7. Kattintson az **OK** gombra.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Tűzfal-házirend létrehozása és a központ biztonságossá tétele

A tűzfalszabályok olyan szabályok gyűjteményeit határozzák meg, amelyek egy vagy több biztonságos virtuális hubhoz irányítják a forgalmat. Hozza létre a tűzfal-házirendet, majd gondoskodjon a központ biztonságossá tételéről.

1. A Firewall Managerben válassza **az Azure Firewall házirend létrehozása**lehetőséget.
2. Válassza ki az előfizetését, majd válassza ki az **FW-Manager** erőforráscsoportot.
3. A szabályzat **részletei**területen, a **következő név** : **Policy-01** és régió esetén válassza az **USA keleti** **régiója** lehetőséget.
4. Válassza a **Tovább: szabályok**lehetőséget.
5. A **szabályok** lapon válassza a **szabálygyűjtemény hozzáadása**elemet.
6. A **szabálygyűjtemény hozzáadása** lapon írja be az **RC-01** **nevet a név**mezőbe.
7. A **szabály gyűjtemény típusa**beállításnál válassza az **alkalmazás**lehetőséget.
8. A **priority**( **100**) mezőbe írja be a következőt:.
9. Győződjön meg arról, hogy a **szabályok gyűjtésének engedélyezése művelet** **engedélyezett**.
10. A szabály **neveként** írja be a következőt: **Allow-MSFT**.
11. A **forrás címe**mezőbe írja be a következőt: **\*** .
12. A **protokoll**mezőben írja be a következőt: **http, HTTPS**.
13. Győződjön meg arról, hogy * * a cél típusa **FQDN**.
14. A **cél**mezőbe írja be a következőt: **\*. microsoft.com**.
15. Válassza a **Hozzáadás** lehetőséget.
16. Válassza a Next (tovább) lehetőséget **: biztonságos virtuális hubok**.
17. A **biztonságos virtuális hubok** lapon válassza a **hub-01**elemet.
19. Válassza az **Áttekintés + létrehozás** lehetőséget.
20. Kattintson a **Létrehozás** gombra.

Ez körülbelül öt percet vesz igénybe.

## <a name="route-traffic-to-your-hub"></a>Forgalom irányítása a hubhoz

Most meg kell győződnie arról, hogy a hálózati forgalom átirányítva lesz a tűzfalon keresztül.

1. A Firewall Managerben válassza a **biztonságos virtuális hubok**lehetőséget.
2. Válassza a **hub-01**elemet.
3. A **Beállítások**területen válassza az **útvonal-beállítások**elemet.
4. Az **internetes forgalom**, **a virtuális hálózatok közötti forgalom**területen válassza a **Küldés Azure Firewall használatával**lehetőséget.
5. Az **Azure Private Traffic**, **a virtuális hálózatok felé irányuló forgalom**területen válassza a **Küldés Azure Firewallon keresztül**lehetőséget.
6. Válassza az **IP-cím előtag (ok) szerkesztése**lehetőséget.
7. Válassza **az IP-cím előtag hozzáadása**lehetőséget.
8. Írja be a **10.0.1.0/24** értéket a munkaterhelés-alhálózat címére, majd válassza a **Mentés**lehetőséget.
9. A **Beállítások**területen válassza a **kapcsolatok**lehetőséget.
10. Válassza ki a **sugaras** kapcsolatot, majd válassza a **biztonságos internetes forgalom** lehetőséget, majd kattintson **az OK gombra**.


## <a name="test-your-firewall"></a>A tűzfal tesztelése

A tűzfalszabályok teszteléséhez néhány kiszolgálót kell telepítenie. Üzembe helyezi a munkaterhelés-SRV-t a munkaterhelés-SN alhálózaton a tűzfalszabályok teszteléséhez, és a Jump-SRV használatával, hogy Távoli asztal az internetről való kapcsolódáshoz, majd a munkaterhelés-SRV-hez való kapcsolódáshoz.

### <a name="deploy-the-servers"></a>A kiszolgálók üzembe helyezése

1. A Azure Portal válassza az **erőforrás létrehozása**lehetőséget.
2. Válassza a **Windows Server 2016 Datacenter** elemet a **népszerű** listában.
3. Adja meg a következő értékeket a virtuális gép számára:

   |Beállítás  |Érték  |
   |---------|---------|
   |Erőforráscsoport     |**FW – kezelő**|
   |Virtuális gép neve     |**Ugrás – SRV**|
   |Régió     |**USA USA keleti régiója)**|
   |Rendszergazda felhasználóneve     |**azureuser**|
   |Jelszó     |A jelszó **Azure123456!**|

4. A **bejövő portszabályok**területen a **nyilvános bejövő portok**esetében válassza a **kijelölt portok engedélyezése**lehetőséget.
5. A **bejövő portok kiválasztása**lapon válassza az **RDP (3389)** lehetőséget.

6. Fogadja el a többi alapértelmezett értéket, és válassza a **Tovább: lemezek**lehetőséget.
7. Fogadja el a lemez alapértelmezett értékeit, és válassza a **Tovább: hálózatkezelés**lehetőséget.
8. Győződjön meg arról, hogy a **küllő-01** a virtuális hálózatra van kiválasztva, és az alhálózat **Ugrás-SN**.
9. A **nyilvános IP**esetében fogadja el az alapértelmezett új nyilvános IP-cím nevét (Jump-SRV-IP).
11. Fogadja el a többi alapértelmezett értéket, és válassza a **Tovább: kezelés**lehetőséget.
12. A rendszerindítási diagnosztika letiltásához válassza a **ki** lehetőséget. Fogadja el a többi alapértelmezett értéket, és válassza a **felülvizsgálat + létrehozás**lehetőséget.
13. Tekintse át a beállításokat az összefoglalás lapon, majd válassza a **Létrehozás**lehetőséget.

A következő táblázatban található információk segítségével konfigurálhat egy **munkaterhelés-SRV**nevű virtuális gépet. A többi beállítás ugyanaz, mint az Srv-Jump virtuális gép esetében.

|Beállítás  |Érték  |
|---------|---------|
|Alhálózat|**Munkaterhelés – SN**|
|Nyilvános IP-cím|**NEz egy**|
|Nyilvános bejövő portok|**NEz egy**|

### <a name="add-a-route-table-and-default-route"></a>Útválasztási táblázat és alapértelmezett útvonal hozzáadása

Ha engedélyezni szeretné az internetkapcsolatot a Jump-SRV használatával, létre kell hoznia egy útválasztási táblázatot és egy alapértelmezett átjáró útvonalat az internetre a **Jump-SN** alhálózatból.

1. A Azure Portal válassza az **erőforrás létrehozása**lehetőséget.
2. Írja be a keresőmezőbe az **útválasztási táblázatot** , majd válassza az **útválasztási táblázat**lehetőséget.
3. Kattintson a **Létrehozás** gombra.
4. Írja **be a**következőt: **RT-01** .
5. Válassza ki az előfizetését, az **FW-Managert** az erőforráscsoport számára, és **(USA) az USA keleti** régióját.
6. Kattintson a **Létrehozás** gombra.
7. Ha az üzembe helyezés befejeződött, válassza ki az **RT-01** útválasztási táblázatot.
8. Válassza az **útvonalak** lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
9. Írja be az **útvonal nevét**az **Ugrás az inet értékre** .
10. Adja meg a **0.0.0.0/0** értéket a **címek előtagja**számára.
11. Válassza az **Internet** lehetőséget a **következő ugrási típushoz**.
12. Kattintson az **OK** gombra.
13. Amikor az üzembe helyezés befejeződött, válassza az **alhálózatok**, majd a **hozzárendelés**lehetőséget.
14. Válassza a **küllő-01** lehetőséget a **virtuális hálózathoz**.
15. Válassza a **Jump-SN** lehetőséget az **alhálózat**elemnél.
16. Kattintson az **OK** gombra.

### <a name="test-the-rules"></a>A szabályok tesztelése

A tűzfalszabályok tesztelésével ellenőrizze, hogy az a vártnak megfelelően működik-e.

1. A Azure Portal tekintse át a **munkaterhelés-SRV** virtuális gép hálózati beállításait, és jegyezze fel a magánhálózati IP-címet.
2. Csatlakoztasson egy távoli asztalt a **Jump-SRV** virtuális géphez, és jelentkezzen be. Onnan nyisson meg egy távoli asztali kapcsolattal a **munkaterhelés-SRV** magánhálózati IP-címet.

3. Nyissa meg az Internet Explorert, és navigáljon a következő címre: https://www.microsoft.com.
4. Válassza az **OK** > **Bezárás** lehetőséget az Internet Explorer biztonsági értesítésein.

   A Microsoft kezdőlapjának kell megjelennie.

5. Nyissa meg a következő címet: https://www.google.com.

   A tűzfal blokkolja a hozzáférést.

Most ellenőrizte, hogy a tűzfalszabályok működnek-e:

* Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a megbízható biztonsági partnerekről](trusted-security-partners.md)
