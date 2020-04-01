---
title: 'Oktatóanyag: A virtuális WAN biztonságossá tétele az Azure Firewall Manager előzetes verziójával'
description: Ebben az oktatóanyagban megtudhatja, hogyan védheti meg a virtuális WAN-t az Azure Firewall Manager használatával az Azure Portalhasználatával.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 3dc94a8be265682fbe2128f2e5870dfdf5850a2d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77443057"
---
# <a name="tutorial-secure-your-virtual-wan-using-azure-firewall-manager-preview"></a>Oktatóanyag: A virtuális WAN biztonságossá tétele az Azure Firewall Manager előzetes verziójával 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Az Azure Firewall Manager előzetes verziójával biztonságos virtuális elosztókat hozhat létre a privát IP-címekre, az Azure PaaS-ra és az internetre irányuló felhőhálózati forgalom védelméhez. A tűzfalra irányuló forgalom átirányítása automatikus, így nincs szükség felhasználó által definiált útvonalak (UDRs) létrehozására.

![a felhőhálózat védelme](media/secure-cloud-network/secure-cloud-network.png)

A Firewall Manager támogatja a központi virtuális hálózati architektúrát is. A biztonságos virtuális központ és a központi virtuális hálózati architektúratípusok összehasonlítását lásd: [Mik az Azure Firewall Manager architektúra beállításai?](vhubs-and-vnets.md)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A küllővirtuális hálózat létrehozása
> * Biztonságos virtuális elosztó létrehozása
> * A hub és a küllővirtuális hálózatok csatlakoztatása
> * Tűzfalházirend létrehozása és a központ védelme
> * Forgalom irányítása a hubra
> * A tűzfal tesztelése

## <a name="create-a-hub-and-spoke-architecture"></a>Hub és küllős architektúra létrehozása

Először hozzon létre egy küllős virtuális hálózatot, ahol elhelyezheti a kiszolgálókat.

### <a name="create-a-spoke-vnet-and-subnets"></a>Küllővirtuális hálózat és alhálózatok létrehozása

1. Az Azure Portal kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**
2. A **Hálózat csoportban**válassza a **Virtuális hálózat**lehetőséget.
4. A **Név mezőbe**írja be a **Küllő-01 nevet.**
5. A **Címtér** mezőbe írja be a következőt: **10.0.0.0/16**.
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **Erőforráscsoport csoportban**válassza **az Új létrehozása**lehetőséget, és írja be az **FW-Manager** nevet a névhez, és válassza az **OK gombot.**
8. A **Hely**területen válassza a **(USA) USA keleti része**lehetőséget.
9. Az **Alhálózat**csoportban a Számítási **feladatok neve** típushoz **.**
10. A **Címtartomány** mezőbe írja be a következőt: **10.0.1.0/24**.
11. Fogadja el a többi alapértelmezett beállítást, és válassza a **Létrehozás gombot.**

Ezután hozzon létre egy alhálózatot egy ugrókiszolgálóhoz.

1. Az Azure Portal kezdőlapján válassza az**FW-Manager** **erőforráscsoportok lehetőséget.** > 
2. Válassza ki a **Küllő-01** virtuális hálózatot.
3. Válassza **az Alhálózatok** > **+Alhálózat lehetőséget.**
4. A **Name mezőbe**írja be a **Jump-SN nevet.**
5. A **Címtartomány** mezőbe írja be a következőt: **10.0.2.0/24**.
6. Válassza **az OK gombot.**

### <a name="create-the-secured-virtual-hub"></a>A biztonságos virtuális elosztó létrehozása

Hozza létre a biztonságos virtuális hubot a Firewall Manager segítségével.

1. Az Azure Portal kezdőlapján válassza a **Minden szolgáltatás**lehetőséget.
2. A keresőmezőbe írja be a **Firewall Manager** parancsot, és válassza a Firewall **Manager lehetőséget.**
3. A **Tűzfalkezelő** lapon válassza a **Biztonságos virtuális központ létrehozása**lehetőséget.
4. Az **Új biztonságos virtuális központi központ létrehozása** lapon válassza ki az előfizetést és az **FW-Manager** erőforráscsoportot.
5. A **Biztonságos virtuális központ nevéhez**írja be a **Hub-01 nevet.**
6. A **Hely területen**válassza az USA keleti **része**lehetőséget.
7. A **Hub címterületéhez**írja be a **10.1.0.0/16 mezőt.**
8. Az új vWAN névhez írja be a **vwan-01**nevet.
9. Hagyja a **VPN-átjáró belefoglalása jelölőnégyzetet a Megbízható biztonsági partnerek engedélyezéséhez** jelölőnégyzetben.
10. Válassza **a Tovább:Azure firewall lehetőséget.**
11. Fogadja el az **Azure tűzfal** **engedélyezésével rendelkező** alapértelmezett beállítást, és válassza a **Tovább: Megbízható biztonsági partner lehetőséget.**
12. Fogadja el a **Megbízható biztonsági partner** alapértelmezett **letiltott** beállítását, és válassza a **Tovább: Véleményezés + létrehozás**lehetőséget.
13. Kattintson a **Létrehozás** gombra. Körülbelül 30 percet vesz igénybe a telepítés.

### <a name="connect-the-hub-and-spoke-vnets"></a>A hub és a küllővirtuális hálózatok csatlakoztatása

Most már koncezika a hub és a küllő virtuális hálózatok.

1. Válassza ki az **FW-Manager** erőforráscsoportot, majd válassza ki a vwan-01 virtuális WAN.Select the FW-Manager resource group, then select the **vwan-01** virtual WAN.
2. A **Kapcsolat csoportban**válassza a **Virtuális hálózati kapcsolatok**lehetőséget.
3. Válassza **a Kapcsolat hozzáadása**lehetőséget.
4. A **Kapcsolat név mezőbe**írja be a hub-spoke című **szöveget.**
5. **Hubs**esetén válassza a **Hub-01**lehetőséget.
6. **Virtuális hálózat**esetén válassza **a Küllő-01**lehetőséget.
7. Válassza **az OK gombot.**

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Tűzfalházirend létrehozása és a központ védelme

A tűzfalházirend szabályok gyűjteményeit határozza meg egy vagy több biztonságos virtuális csomóponton a forgalom irányításához. Létre fogja hozni a tűzfalházirendet, majd biztonságossá kell tenni a központot.

1. A Firewall Manager területen válassza **az Azure tűzfalházirend létrehozása**lehetőséget.
2. Válassza ki az előfizetést, majd válassza az **FW-Manager** erőforráscsoportot.
3. A **Házirend részletei**csoportban a **Policy-01** **névtípusés** a **Régió** csoportban válassza az USA **keleti régióját.**
4. Válassza a **Tovább:Szabályok**lehetőséget.
5. A **Szabályok** lapon válassza **a Szabálygyűjtemény hozzáadása**lehetőséget.
6. A **Szabálygyűjtemény hozzáadása** lapon írja be az **RC-01** értéket a **Név mezőbe.**
7. A **Szabálygyűjtemény típusa mezőben**válassza az **Alkalmazás**lehetőséget.
8. Az **Elsőbbség**mezőbe írja be a **100-as mezőt.**
9. Győződjön meg arról, hogy a **szabálygyűjtési művelet** **az Engedélyezés**.
10. Az **Allow-msft** **szabálytípushoz.**
11. A **Forráscím** **\*** mezőbe írja be a következőt: .Source address
12. A **Protokoll**mezőbe írja be a **http,https**.
13. Győződjön meg arról, hogy **A céltípusa **teljes tartományneve.**
14. A **Cél mezőbe**írja be a ** \*.microsoft.com**.
15. Válassza a **Hozzáadás** lehetőséget.
16. Válassza a **Tovább: Biztonságos virtuális elosztók**lehetőséget.
17. A **Biztonságos virtuális elosztók** lapon válassza a **Hub-01**lehetőséget.
19. Válassza az **Áttekintés + létrehozás** lehetőséget.
20. Kattintson a **Létrehozás** gombra.

Ez körülbelül öt percet vagy többet is igénybe vehet.

## <a name="route-traffic-to-your-hub"></a>Forgalom irányítása a hubra

Most gondoskodnia kell arról, hogy a hálózati forgalom a tűzfalon keresztül irányítva legyen.

1. A Firewall Manager területen válassza a **Biztonságos virtuális elosztók**lehetőséget.
2. Válassza **a Hub-01 lehetőséget.**
3. A **Beállítások csoportban**válassza az **Útvonalbeállítások lehetőséget.**
4. Az **Internetes forgalom**csoportban válassza **a** **Küldés az Azure tűzfalon keresztül**lehetőséget.
5. Az **Azure privát forgalma**( Forgalom a virtuális **hálózatokra**) csoportban válassza a **Küldés az Azure tűzfalon keresztül**lehetőséget.
6. Válassza **az IP-cím előtagja(k) szerkesztése**lehetőséget.
7. Válassza **az IP-cím előtag hozzáadása**lehetőséget.
8. Írja be a **10.0.1.0/24-es** parancsot a Számítási feladat alhálózatcímeként, és válassza a **Mentés lehetőséget.**
9. A **Beállítások csoportban**válassza a **Kapcsolatok**lehetőséget.
10. Jelölje ki a **központi küllős** kapcsolatot, majd válassza **az Internetes forgalom biztonságossá tétele,** majd **az OK**gombot.


## <a name="test-your-firewall"></a>A tűzfal tesztelése

A tűzfalszabályok teszteléséhez telepítenie kell néhány kiszolgálót. A Számítási feladatok-Srv-t a Számítási feladatok-SN alhálózatban telepíti a tűzfalszabályok teszteléséhez, és a Jump-Srv-t, így a Távoli asztal segítségével csatlakozhat az internetről, majd csatlakozhat a Workload-Srv-hez.

### <a name="deploy-the-servers"></a>A kiszolgálók telepítése

1. Az Azure Portalon válassza az **Erőforrás létrehozása lehetőséget.**
2. Válassza a **Windows Server 2016 Datacenter elemet** a **Népszerű** listában.
3. Adja meg a következő értékeket a virtuális gép számára:

   |Beállítás  |Érték  |
   |---------|---------|
   |Erőforráscsoport     |**FW-Menedzser**|
   |Virtuális gép neve     |**Ugrás-Srv**|
   |Régió     |**(US) USA keleti része)**|
   |Rendszergazdai felhasználónév     |**azureuser**|
   |Jelszó     |írja be a jelszavát|

4. A **Bejövő portszabályok**csoport **Nyilvános bejövő portok csoportban**válassza **a Kijelölt portok engedélyezése**lehetőséget.
5. A **Bejövő portok kiválasztása csoportban**válassza az **RDP (3389) lehetőséget.**

6. Fogadja el a többi alapértelmezést, és válassza **a Tovább: Lemezek**lehetőséget.
7. Fogadja el a lemez alapértelmezéseit, és válassza **a Tovább: Hálózat lehetőséget.**
8. Győződjön meg arról, hogy a **Klisés-01** ki van jelölve a virtuális hálózathoz, és az alhálózat **Jump-SN.**
9. **Nyilvános IP**esetén fogadja el az alapértelmezett új nyilvános IP-címnevet (Jump-Srv-ip).
11. Fogadja el a többi alapértelmezést, és válassza **a Tovább: Kezelés**lehetőséget.
12. A **rendszerindítási** diagnosztika letiltásához válassza a Ki lehetőséget. Fogadja el a többi alapértelmezést, és válassza **a Véleményezés + create lehetőséget.**
13. Tekintse át az összesítő lap beállításait, és válassza a **Létrehozás gombot.**

Az alábbi táblázatban található információk segítségével konfigurálhat egy **másik, Workload-Srv**nevű virtuális gépet. A többi beállítás ugyanaz, mint az Srv-Jump virtuális gép esetében.

|Beállítás  |Érték  |
|---------|---------|
|Alhálózat|**Workload-SN**|
|Nyilvános IP-cím|**Nincs**|
|Nyilvános bejövő portok|**Nincs**|

### <a name="add-a-route-table-and-default-route"></a>Útvonaltábla és alapértelmezett útvonal hozzáadása

Ahhoz, hogy a Jump-Srv-hez internetkapcsolat legyen engedélyezve, létre kell hoznia egy útvonaltáblát és egy alapértelmezett átjáróútvonalat az internetre a **Jump-SN** alhálózatról.

1. Az Azure Portalon válassza az **Erőforrás létrehozása lehetőséget.**
2. Írja be az **útvonaltáblát** a keresőmezőbe, majd válassza **az Útvonal tábla lehetőséget.**
3. Kattintson a **Létrehozás** gombra.
4. Írja be **az RT-01 nevet** a **Név mezőbe.**
5. Válassza ki az előfizetést, az **FW-Manager** t az erőforráscsoporthoz és **(USA keleti régióját)** a régióhoz.
6. Kattintson a **Létrehozás** gombra.
7. Amikor a központi telepítés befejeződött, válassza ki az **RT-01** útvonaltáblát.
8. Válassza **az Útvonalak,** majd **a Hozzáadás**lehetőséget.
9. Írja be az **Útvonal név** **beugrását.**
10. Írja be a **0.0.0.0/0-t** a **Cím előtaghoz.**
11. Válassza az **Internet** lehetőséget a **Következő ugrás típushoz**.
12. Válassza **az OK gombot.**
13. Amikor a központi telepítés befejeződik, válassza **az Alhálózatok**lehetőséget, majd a **Társítás**lehetőséget.
14. Válassza **a Küllő-01** lehetőséget a **virtuális hálózathoz.**
15. Válassza **a Jump-SN** lehetőséget **az Alhálózathoz.**
16. Válassza **az OK gombot.**

### <a name="test-the-rules"></a>A szabályok tesztelése

Most tesztelje a tűzfalszabályokat annak ellenőrzéséhez, hogy az a várt módon működik-e.

1. Az Azure Portalon tekintse át a **számítási feladatok** virtuális gép hálózati beállításait, és vegye figyelembe a privát IP-címet.
2. Csatlakoztasson egy távoli asztalt a **Jump-Srv** virtuális géphez, és jelentkezzen be. Innen nyisson meg egy távoli asztali kapcsolatot a **Workload-Srv** privát IP-címhez.

3. Nyissa meg az Internet Explorert, és navigáljon a következő címre: https://www.microsoft.com.
4. Válassza az **OK** > **bezárás lehetőséget** az Internet Explorer biztonsági riasztásain.

   Meg kell jelennie a Microsoft kezdőlapján.

5. Nyissa meg a következő címet: https://www.google.com.

   A tűzfal blokkolja a hozzáférést.

Így most már ellenőrizte, hogy a tűzfalszabályok működnek:

* Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a megbízható biztonsági partnerekről](trusted-security-partners.md)
