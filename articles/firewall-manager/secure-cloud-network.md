---
title: 'Oktatóanyag: virtuális WAN biztonságossá tétele a Azure Firewall Manager előzetes verziójával'
description: Ebből az oktatóanyagból megtudhatja, hogyan védheti a virtuális WAN-t a Azure Firewall Managerrel a Azure Portal használatával.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: victorh
ms.openlocfilehash: b13f3b4eeb57c34f51152bb6d1914f6c80f31be1
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691025"
---
# <a name="tutorial-secure-your-virtual-wan-using-azure-firewall-manager-preview"></a>Oktatóanyag: virtuális WAN biztonságossá tétele a Azure Firewall Manager előzetes verziójával 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

A Azure Firewall Manager előzetes verziójának használatával biztonságos virtuális hubokat hozhat létre, amelyekkel biztonságossá teheti a saját IP-címekre, az Azure-ra és az internetre irányuló Felhőbeli hálózati forgalmat. A tűzfal felé irányuló forgalom-útválasztás automatizált, így nem kell felhasználó által megadott útvonalakat (UDR-ket) létrehoznia.

![a felhőalapú hálózat védelme](media/secure-cloud-network/secure-cloud-network.png)

A Firewall Manager egy hub virtuális hálózati architektúrát is támogat. A biztonságos virtuális központ és a hub virtuális hálózati architektúrájának összevetéséhez tekintse meg [a mi a Azure Firewall Manager architektúrájának beállításait?](vhubs-and-vnets.md)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A küllős virtuális hálózat létrehozása
> * Biztonságos virtuális központ létrehozása
> * A hub és a küllős virtuális hálózatok összekötése
> * Tűzfal-házirend létrehozása és a központ biztonságossá tétele
> * Forgalom irányítása a hubhoz
> * A tűzfal tesztelése

## <a name="create-a-hub-and-spoke-architecture"></a>Sugaras architektúra létrehozása

Először is hozzon létre egy küllős virtuális hálózatot, ahol elhelyezheti a kiszolgálókat.

### <a name="create-a-spoke-virtual-network-and-subnets"></a>Küllős virtuális hálózat és alhálózatok létrehozása

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása**lehetőséget.
2. A **hálózat**területen válassza a **virtuális hálózat**lehetőséget.
2. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **erőforráscsoport**területen válassza az **új létrehozása**lehetőséget, és írja be a név mezőbe az **FW-Manager** nevet, majd kattintson **az OK gombra**.
2. A **név**mezőbe írja be a **küllő-01**értéket.
3. A régió területen válassza az USA **keleti** **régióját**.
4. Válassza a **Tovább: IP-címek**lehetőséget.
1. A **címterület**esetében fogadja el az alapértelmezett **10.0.0.0/16**értéket.
3. Az **alhálózat neve**területen válassza az **alapértelmezett**lehetőséget.
4. Módosítsa az alhálózat nevét a **munkaterhelés-SN**értékre.
5. **Alhálózati címtartomány**esetében írja be a következőt: **10.0.1.0/24**.
6. Válassza a **Mentés**lehetőséget.

Ezután hozzon létre egy alhálózatot a Jump Serverhez.

1. Válassza az **alhálózat hozzáadása**lehetőséget.
4. Az **alhálózat neve**mezőbe írja be a következőt: **Jump-SN**.
5. **Alhálózati címtartomány**esetében írja be a következőt: **10.0.2.0/24**.
6. Válassza a **Hozzáadás** lehetőséget.

Most hozza létre a virtuális hálózatot.

1. Válassza az **Áttekintés + létrehozás** lehetőséget.
2. Kattintson a **Létrehozás** gombra.

### <a name="create-the-secured-virtual-hub"></a>A biztonságos virtuális központ létrehozása

Hozza létre a biztonságos virtuális hubot a Firewall Manager használatával.

1. A Azure Portal kezdőlapon válassza a **minden szolgáltatás**lehetőséget.
2. A keresőmezőbe írja be a **Firewall Manager** kifejezést, majd válassza a **Firewall Manager**lehetőséget.
3. A **Firewall Manager** lapon válassza a **biztonságos virtuális hubok megtekintése**lehetőséget.
4. A **Firewall Managerben | Biztonságos virtuális hubok** lapon válassza az **új biztonságos virtuális központ létrehozása**lehetőséget.
5. Az **erőforráscsoport**területen válassza az **FW-Manager**lehetőséget.
7. A régió területen válassza az **USA keleti** **régiója**lehetőséget.
1. A **biztonságos virtuális központ neve**mezőbe írja be a következőt: **hub-01**.
2. A **hub címterület**mezőbe írja be a következőt: **10.1.0.0/16**.
3. Az új vWAN neve mezőbe írja be a következőt: **vWAN-01**.
4. Hagyja bejelölve a **VPN Gateway belefoglalása a megbízható biztonsági partnerek engedélyezése** jelölőnégyzetet.
5. Válassza a Next (tovább) lehetőséget **: Azure Firewall**.
6. Fogadja el az alapértelmezett **Azure Firewall** **engedélyezve** beállítást, majd válassza a **Tovább: megbízható biztonsági partner**lehetőséget.
7. Fogadja el az alapértelmezett **megbízható biztonsági partner** **letiltott** beállítást, majd kattintson a **Tovább gombra: felülvizsgálat + létrehozás**.
8. Kattintson a **Létrehozás** gombra. A telepítés körülbelül 30 percet vesz igénybe.

### <a name="connect-the-hub-and-spoke-vnets"></a>A hub és a küllős virtuális hálózatok összekötése

Most már elvégezheti a hub és a küllő virtuális hálózatok.

1. Válassza ki az **FW-Manager** erőforráscsoportot, majd válassza ki a **Vwan-01** virtuális WAN elemet.
2. A **kapcsolat**területen válassza a **virtuális hálózati kapcsolatok**elemet.
3. Válassza a **kapcsolatok hozzáadása**lehetőséget.
4. A **kapcsolatok neve**mezőbe írja be a következőt: **hub-küllő**.
5. **Hubok**esetében válassza a **hub-01**elemet.
6. Az **erőforráscsoport**területen válassza az **FW-Manager**lehetőséget.
7. **Virtuális hálózat**esetén válassza a **küllő-01**elemet.
8. Kattintson az **OK** gombra.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Tűzfal-házirend létrehozása és a központ biztonságossá tétele

A tűzfalszabályok olyan szabályok gyűjteményeit határozzák meg, amelyek egy vagy több biztonságos virtuális hubhoz irányítják a forgalmat. Hozza létre a tűzfal-házirendet, majd gondoskodjon a központ biztonságossá tételéről.

1. A Firewall Managerben válassza a **Azure Firewall házirendek megtekintése**lehetőséget.
2. Válassza a **Azure Firewall házirend létrehozása**lehetőséget.
3. A szabályzat **részletei**területen, a **következő név** : **Policy-01** és régió esetén válassza az **USA keleti** **régiója** lehetőséget.
4. Válassza a **Tovább: szabályok**lehetőséget.
5. A **szabályok** lapon válassza a **szabálygyűjtemény hozzáadása**elemet.
6. A **szabálygyűjtemény hozzáadása** lapon írja be az **RC-01** **nevet a név**mezőbe.
7. A **szabály gyűjtemény típusa**beállításnál válassza az **alkalmazás**lehetőséget.
8. A **priority**( **100**) mezőbe írja be a következőt:.
9. Győződjön meg arról, hogy a **szabályok gyűjtésének engedélyezése művelet** **engedélyezett**.
10. A szabály **neveként** írja be a következőt: **Allow-MSFT**.
11. A **forrás címe**mezőbe írja **\*** be a következőt:.
12. A **protokoll**mezőben írja be a következőt: **http, HTTPS**.
13. Győződjön meg arról, hogy * * a cél típusa **FQDN**.
14. A **cél**mezőbe írja be ** \*** a következőt:. microsoft.com.
15. Válassza a **Hozzáadás** lehetőséget.
16. Válassza a **Next (tovább): hubok**lehetőséget.
17. A **hubok** lapon válassza a **virtuális hubok hozzárendelése**lehetőséget.
18. Válassza a **hub-01** elemet, majd kattintson a **Hozzáadás**gombra.
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
2. Kattintson a **Létrehozás** gombra.

Ez körülbelül öt percet vesz igénybe.

## <a name="route-traffic-to-your-hub"></a>Forgalom irányítása a hubhoz

Most meg kell győződnie arról, hogy a hálózati forgalom átirányítva lesz a tűzfalon keresztül.

1. A Firewall Managerben válassza a **biztonságos virtuális hubok**lehetőséget.
2. Válassza a **hub-01**elemet.
3. A **Beállítások**területen válassza az **útvonal-beállítások**elemet.
4. Az **internetes forgalom**, **a virtuális hálózatok közötti forgalom**területen válassza a **Küldés Azure Firewall használatával**lehetőséget.
5. Az **Azure Private Traffic**, **a virtuális hálózatok felé irányuló forgalom**területen válassza a **Küldés Azure Firewallon keresztül**lehetőséget.
6. Válassza az **IP-cím előtag (ok) szerkesztése**lehetőséget.
8. Írja be a **10.0.1.0/24** értéket a munkaterhelés-alhálózat címére, majd válassza a **Mentés**lehetőséget.
9. A **Beállítások**területen válassza a **kapcsolatok**lehetőséget.
10. Győződjön meg arról, hogy a **sugaras** kapcsolatok **biztonságosként**jeleníti meg az **internetes forgalmat** .


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
   |Rendszergazda felhasználóneve     |adja meg a felhasználónevet|
   |Jelszó     |írjon be egy jelszót|

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
|Alhálózat|**Workload-SN**|
|Nyilvános IP-cím|**Nincs**|
|Nyilvános bejövő portok|**Nincs**|

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
4. Az Internet Explorer biztonsági riasztások ablakában kattintson **az OK** > **Bezárás** gombra.

   A Microsoft kezdőlapjának kell megjelennie.

5. Nyissa meg a következő címet: https://www.google.com.

   A tűzfal blokkolja a hozzáférést.

Most ellenőrizte, hogy a tűzfalszabályok működnek-e:

* Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a megbízható biztonsági partnerekről](trusted-security-partners.md)
