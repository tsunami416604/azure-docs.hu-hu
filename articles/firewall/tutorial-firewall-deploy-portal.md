---
title: 'Oktatóanyag: Az Azure-tűzfal üzembe helyezése & konfigurálása az Azure Portal használatával'
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe és konfigurálhatja az Azure Firewallt az Azure Portalon.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 02/21/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 064fcf618914bca31ad9e7e60c76df8f599cd8bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239573"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon

A kimenő hálózati hozzáférés ellenőrzése az általános hálózati biztonsági terv fontos részét képezi. Előfordulhat például, hogy korlátozni szeretné a webhelyekhez való hozzáférést. Vagy korlátozhatja a kimenő IP-címeket és portokat, amelyek elérhetők.

Az Azure-alhálózatok kimenő hálózati hozzáférése többek között az Azure Firewall használatával vezérelhető. Az Azure Firewall segítségével a következőket konfigurálhatja:

* Alkalmazásszabályokat, amelyek egy alhálózatról elérhető teljes tartományneveket (FQDN) határoznak meg.
* Hálózatszabályokat, amelyek forráscímet, protokollt, valamint célportot és célcímet határoznak meg.

A hálózati forgalmat a konfigurált tűzfalszabályok irányítják, ha alapértelmezett alhálózati átjáróként irányítja a tűzfalhoz a forgalmat.

Ebben az oktatóanyagban az üzembe helyezés érdekében egyetlen egyszerű virtuális hálózatot hozunk létre három alhálózattal. Éles környezetben éles környezetben, a [hub és a küllőmodell](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) ajánlott. A tűzfal saját virtuális hálózatában található. A számítási feladatok kiszolgálók egyenrangú virtuális hálózatok ugyanabban a régióban egy vagy több alhálózattal.

* **AzureFirewallSubnet** – ezen az alhálózaton található a tűzfal.
* **Workload-SN** – ezen az alhálózaton található a számítási feladat kiszolgálója. Ennek az alhálózatnak a hálózati forgalma a tűzfalon halad át.
* **Jump-SN** – ezen az alhálózaton található a helyettesítő kiszolgáló. A helyettesítő kiszolgáló egy nyilvános IP-címmel rendelkezik, amelyhez csatlakozni lehet a távoli asztal használatával. Innen pedig egy másik távoli asztallal lehet csatlakozni a számítási feladat kiszolgálójához.

![Az oktatóanyag hálózati infrastruktúrája](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tesztelési hálózati környezet beállítása
> * Tűzfal üzembe helyezése
> * Alapértelmezett útvonal létrehozása
> * Alkalmazásszabály konfigurálása a www.google.com való hozzáférés engedélyezéséhez
> * Hálózatszabály konfigurálása külső DNS-kiszolgálókhoz való hozzáférés engedélyezéséhez
> * A tűzfal tesztelése

Igény szerint az oktatóanyag az [Azure PowerShell](deploy-ps.md) használatával is elvégezhető.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="set-up-the-network"></a>A hálózat beállítása

Először is hozzon létre egy erőforráscsoportot, amely a tűzfal üzembe helyezéséhez szükséges erőforrásokat tartalmazza. Ezután hozzon létre egy virtuális hálózatot, alhálózatokat és tesztkiszolgálókat.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport tartalmazza az oktatóanyag összes erőforrását.

1. Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .
2. Az Azure Portal menüben válassza az **Erőforráscsoportok** lehetőséget, vagy keressen rá, és válassza *ki az Erőforráscsoportokat* bármelyik lapon. Ezután válassza **a Hozzáadás**lehetőséget.
3. Az **Erőforráscsoport nevéhez**adja meg *a Test-FW-RG*értéket.
4. Az **Előfizetés** beállításnál válassza ki az előfizetését.
5. Az **Erőforráscsoport helye** beállításnál válasszon ki egy helyet. Minden más létrehozott erőforrásnak ugyanazon a helyen kell lennie.
6. Kattintson a **Létrehozás** gombra.

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

A VNet három alhálózatot fog tartalmazni.

> [!NOTE]
> Az AzureFirewallSubnet alhálózat mérete /26. Az alhálózat méretéről az [Azure Firewall gyIK](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)című témakörben talál további információt.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
1. Válassza **a** > **Hálózati virtuális hálózat lehetőséget.**
1. A **Név** mezőbe írja be a következőt: **Test-FW-VN**.
1. A **Címtér** mezőbe írja be a következőt: **10.0.0.0/16**.
1. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **Erőforrás csoport csoportban**válassza **a Test-FW-RG**lehetőséget.
1. A **Hely** elemnél válassza a korábban használt helyet.
1. Az **Alhálózat** területen a **Név** mezőbe írja be a következőt: **AzureFirewallSubnet**. Ezen az alhálózaton lesz a tűzfal. Az alhálózat neve **kizárólag** AzureFirewallSubnet lehet.
1. A **Címtartomány**mezőbe írja be a **10.0.1.0/26 mezőt.**
1. Fogadja el a többi alapértelmezett beállítást, és válassza a **Létrehozás gombot.**

### <a name="create-additional-subnets"></a>További alhálózatok létrehozása

Ezután hozzon létre egy-egy alhálózatot a helyettesítő kiszolgáló és a számítási feladatok kiszolgálói számára.

1. Az Azure Portal menüben válassza az **Erőforráscsoportok** lehetőséget, vagy keressen rá, és válassza *ki az Erőforráscsoportokat* bármelyik lapon. Ezután válassza **a Test-FW-RG**lehetőséget.
2. Válassza ki a **Test-FW-VN** virtuális hálózatot.
3. Válassza **az Alhálózatok** > **+Alhálózat lehetőséget.**
4. A **Név** mezőbe írja be a következőt: **Workload-SN**.
5. A **Címtartomány** mezőbe írja be a következőt: **10.0.2.0/24**.
6. Válassza **az OK gombot.**

Hozzon létre egy másik alhálózatot, amelynek a neve **Jump-SN**, a címtartománya pedig **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Most hozza létre a helyettesítő és a számítási feladatokat futtató virtuális gépeket, és helyezze el őket a megfelelő alhálózatokon.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet a Kiemeltek listából.
3. Adja meg a következő értékeket a virtuális gép számára:

   |Beállítás  |Érték  |
   |---------|---------|
   |Erőforráscsoport     |**Fw-RG teszt**|
   |Virtuális gép neve     |**Srv-ugrás**|
   |Régió     |Ugyanaz, mint az előző|
   |Rendszergazdai felhasználónév     |**azureuser**|
   |Jelszó     |A jelszó **Azure123456!**|

4. A **Bejövő portszabályok**csoport **Nyilvános bejövő portok csoportban**válassza **a Kijelölt portok engedélyezése**lehetőséget.
5. A **Bejövő portok kiválasztása csoportban**válassza az **RDP (3389) lehetőséget.**

6. Fogadja el a többi alapértelmezést, és válassza **a Tovább: Lemezek**lehetőséget.
7. Fogadja el a lemez alapértelmezéseit, és válassza **a Tovább: Hálózat lehetőséget.**
8. Győződjön meg arról, hogy a **Test-FW-VN** a virtuális hálózathoz van kiválasztva, és az alhálózat **Jump-SN.**
9. **Nyilvános IP esetén**fogadja el az alapértelmezett új nyilvános IP-címnevet (Srv-Jump-ip).
11. Fogadja el a többi alapértelmezést, és válassza **a Tovább: Kezelés**lehetőséget.
12. A **rendszerindítási** diagnosztika letiltásához válassza a Ki lehetőséget. Fogadja el a többi alapértelmezést, és válassza **a Véleményezés + create lehetőséget.**
13. Tekintse át az összesítő lap beállításait, és válassza a **Létrehozás gombot.**

Az alábbi táblázatban található információk segítségével konfigurálhat egy **másik, Srv-Work**nevű virtuális gépet. A többi beállítás ugyanaz, mint az Srv-Jump virtuális gép esetében.

|Beállítás  |Érték  |
|---------|---------|
|Alhálózat|**Workload-SN**|
|Nyilvános IP-cím|**Nincs**|
|Nyilvános bejövő portok|**Nincs**|

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

Helyezze üzembe a tűzfalat a virtuális hálózaton.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
2. Írja be a **tűzfalat** a keresőmezőbe, és nyomja **le az Enter billentyűt.**
3. Válassza **a Tűzfal,** majd a **Létrehozás**lehetőséget.
4. A **Tűzfal létrehozása** oldalon konfigurálja a tűzfalat a következő táblázatban található értékekkel:

   |Beállítás  |Érték  |
   |---------|---------|
   |Előfizetés     |\<az Ön előfizetése\>|
   |Erőforráscsoport     |**Fw-RG teszt** |
   |Név     |**Test-FW01**|
   |Hely     |Válassza a korábban használt helyet|
   |Válasszon egy virtuális hálózatot     |**Meglévő használata**: **Test-FW-VN**|
   |Nyilvános IP-cím     |**Új hozzáadása.** A nyilvános IP-címnek standard termékváltozat típusúnak kell lennie.|

5. Válassza az **Áttekintés + létrehozás** lehetőséget.
6. Tekintse át az összegzést, majd a **Létrehozás gombra** a tűzfal létrehozásához.

   Az üzembe helyezés néhány percet vesz igénybe.
7. A telepítés befejezése után lépjen a **Test-FW-RG** erőforráscsoportra, és válassza ki a **Test-FW01** tűzfalat.
8. Jegyezze fel a magánhálózati IP-címet. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

## <a name="create-a-default-route"></a>Alapértelmezett útvonal létrehozása

A **Workload-SN** alhálózatot konfigurálja úgy, hogy a kimenő alapértelmezett útvonal áthaladjon a tűzfalon.

1. Az Azure Portal menüben válassza a **Minden szolgáltatás** lehetőséget, vagy keressen rá, és válassza a *Minden szolgáltatás* bármely oldalon lehetőséget.
2. A **Hálózat csoportban**válassza **az Útvonaltáblák lehetőséget.**
3. Válassza a **Hozzáadás** lehetőséget.
4. A **Név** mezőbe írja be a következőt: **Firewall-route**.
5. Az **Előfizetés** beállításnál válassza ki az előfizetését.
6. Az **Erőforrás csoport csoportban**válassza **a Test-FW-RG**lehetőséget.
7. A **Hely** elemnél válassza a korábban használt helyet.
8. Kattintson a **Létrehozás** gombra.
9. Válassza **a Frissítés**lehetőséget, majd válassza a **Tűzfal-útvonal** tábla lehetőséget.
10. Válassza **az Alhálózatok,** majd **a Társítás**lehetőséget.
11. Válassza **a Virtuális hálózati** > **teszt-FW-VN lehetőséget.**
12. Az **Alhálózat**csoportban válassza **a Workload-SN**lehetőséget. Győződjön meg arról, hogy csak a **számítási feladatok-SN** alhálózatot választotta ki ehhez az útvonalhoz, különben a tűzfal nem fog megfelelően működni.

13. Válassza **az OK gombot.**
14. Válassza **az Útvonalak,** majd **a Hozzáadás**lehetőséget.
15. Az **Útvonal név mezőbe**írja be az **fw-dg**nevet.
16. A **Címelőtag** mezőbe írja be a következőt: **0.0.0.0/0**.
17. A **Következő ugrás típusa** beállításnál válassza a **Virtuális berendezés** lehetőséget.

    Az Azure Firewall valójában egy felügyelt szolgáltatás, de ebben a helyzetben a virtuális berendezés beállítás is használható.
18. A **Következő ugrás címe** mezőbe írja be a tűzfal magánhálózati IP-címét, amelyet korábban feljegyzett.
19. Válassza **az OK gombot.**

## <a name="configure-an-application-rule"></a>Alkalmazásszabály konfigurálása

Ez az alkalmazásszabály, amely lehetővé teszi a kimenő hozzáférést www.google.com.

1. Nyissa meg a **Test-FW-RG**-t, és válassza a **Test-FW01** tűzfalat.
2. A **Test-FW01 (Teszt-FW01)** **lap Beállítások**területén válassza a **Szabályok**lehetőséget.
3. Válassza az **Alkalmazásszabály-gyűjtemény** lapot.
4. Válassza **az Alkalmazásszabály-gyűjtemény hozzáadása**lehetőséget.
5. A **Név** mezőbe írja be a következőt: **App-Coll01**.
6. A **Prioritás** mezőbe írja be a következőt: **200**.
7. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.
8. A **Szabályok csoportban**a **Célteljes tartománynevek**csoportban a **Name (Név)** mezőbe írja be az **Allow-Google (Engedélyező- Google**) mezőbe.
9. A **Forrástípus mezőben**válassza az **IP-cím**lehetőséget.
10. A **Forrás**mezőbe írja be a **10.0.2.0/24 mezőt.**
11. A **Protokoll:port** mezőbe írja be a következőt: **http, https**.
12. A **Cél FQDNS**mezőbe írja be **www.google.com**
13. Válassza a **Hozzáadás** lehetőséget.

Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről. Ezek a teljes tartománynevek csak az adott platformra vonatkoznak, egyéb célra nem használhatók. További információ: [Infrastruktúra FQDN-jei](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Hálózatszabály konfigurálása

Ez az a hálózatszabály, amely lehetővé teszi a kimenő hozzáférést két IP-címhez az 53-as porton (DNS).

1. Válassza a **Hálózati szabály gyűjtemény** lapot.
2. Válassza **a Hálózati szabálygyűjtemény hozzáadása**lehetőséget.
3. A **Név** mezőbe írja be a következőt: **Net-Coll01**.
4. A **Prioritás** mezőbe írja be a következőt: **200**.
5. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.
6. A **Szabályok csoportban**a **(Név)** mezőbe írja be az **Allow-DNS (Engedélyező)** mezőbe az **IP-címeket.**
7. A **Protokoll** beállításnál válassza az **UDP** lehetőséget.
9. A **Forrástípus mezőben**válassza az **IP-cím**lehetőséget.
1. A **Forrás**mezőbe írja be a **10.0.2.0/24 mezőt.**
2. A **Célcím**mezőbe írja be a **209.244.0.3,209.244.0.4**

   Ezek a CenturyLink által üzemeltetett nyilvános DNS-kiszolgálók.
1. A **Célportok** mezőbe írja be a következőt: **53**.
2. Válassza a **Hozzáadás** lehetőséget.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Módosítsa az **Srv-Work** hálózati adapter elsődleges és másodlagos DNS-címét.

Az oktatóanyag tesztelési célokra konfigurálja a kiszolgáló elsődleges és másodlagos DNS-címeit. Ez nem általános Azure-tűzfal követelmény.

1. Az Azure Portal menüben válassza az **Erőforráscsoportok** lehetőséget, vagy keressen rá, és válassza *ki az Erőforráscsoportokat* bármelyik lapon. Válassza ki a **Test-FW-RG** erőforráscsoportot.
2. Válassza ki az **Srv-Work** virtuális gép hálózati illesztőfelületét.
3. A **Beállítások csoportban**válassza a **DNS-kiszolgálók lehetőséget.**
4. A **DNS-kiszolgálók csoportban**válassza az **Egyéni**lehetőséget.
5. Írja be a **209.244.0.3** a címet a **DNS-kiszolgáló hozzáadása** szövegmezőbe, és **209.244.0.4** címet a következő szövegmezőbe.
6. Kattintson a **Mentés** gombra.
7. Indítsa újra az **Srv-Work** virtuális gépet.

## <a name="test-the-firewall"></a>A tűzfal tesztelése

Most tesztelje a tűzfalat, hogy ellenőrizze, hogy a várt módon működik-e.

1. Az Azure Portalon tekintse át az **Srv-Work** virtuális gép hálózati beállításait, és jegyezze fel a gép magánhálózati IP-címét.
2. Csatlakoztasson egy távoli asztalt **a Srv-Jump** virtuális géphez, és jelentkezzen be. Innen nyisson meg egy távoli asztali kapcsolatot az **Srv-Work** privát IP-címhez.
3. Nyissa meg az Internet Explorert, és navigáljon a következő címre: https://www.google.com.
4. Válassza az **OK** > **bezárás lehetőséget** az Internet Explorer biztonsági riasztásain.

   Meg kell jelennie a Google kezdőlapján.

5. Nyissa meg a következő címet: https://www.microsoft.com.

   A tűzfal blokkolja a hozzáférést.

Így most már ellenőrizte, hogy a tűzfalszabályok működnek:

* Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.
* Fel tudja oldani a DNS-neveket a konfigurált külső DNS-kiszolgálóval.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfalhoz kapcsolódó erőforrásokat a következő oktatóanyagban is használhatja, vagy ha már nincs rá szükség, törölje a **Test-FW-RG** erőforráscsoportot, és vele együtt a tűzfalhoz kapcsolódó összes erőforrást.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./tutorial-diagnostics.md)
