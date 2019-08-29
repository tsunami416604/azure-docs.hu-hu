---
title: 'Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon'
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe és konfigurálhatja az Azure Firewallt az Azure Portalon.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 0892bde09891d2edbd7f8cc8715ccc0d2f047ed4
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70113469"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon

A kimenő hálózati hozzáférés ellenőrzése az általános hálózati biztonsági terv fontos részét képezi. Előfordulhat például, hogy korlátozni szeretné a webhelyekhez való hozzáférést. Az is előfordulhat, hogy korlátozni szeretné az elérhető kimenő IP-címeket és portokat.

Az Azure-alhálózatok kimenő hálózati hozzáférése többek között az Azure Firewall használatával vezérelhető. Az Azure Firewall segítségével a következőket konfigurálhatja:

* Alkalmazásszabályokat, amelyek egy alhálózatról elérhető teljes tartományneveket (FQDN) határoznak meg.
* Hálózatszabályokat, amelyek forráscímet, protokollt, valamint célportot és célcímet határoznak meg.

A hálózati forgalmat a konfigurált tűzfalszabályok irányítják, ha alapértelmezett alhálózati átjáróként irányítja a tűzfalhoz a forgalmat.

Ebben az oktatóanyagban az üzembe helyezés érdekében egyetlen egyszerű virtuális hálózatot hozunk létre három alhálózattal. Éles környezetekben a [hub és a küllős modell](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) használata ajánlott, ahol a tűzfal a saját VNet van. A munkaterhelés-kiszolgálók egy vagy több alhálózattal azonos régióban lévő, egymással azonos régióba tartozó virtuális hálózatok találhatók.

* **AzureFirewallSubnet** – ezen az alhálózaton található a tűzfal.
* **Workload-SN** – ezen az alhálózaton található a számítási feladat kiszolgálója. Ennek az alhálózatnak a hálózati forgalma a tűzfalon halad át.
* **Jump-SN** – ezen az alhálózaton található a helyettesítő kiszolgáló. A helyettesítő kiszolgáló egy nyilvános IP-címmel rendelkezik, amelyhez csatlakozni lehet a távoli asztal használatával. Innen pedig egy másik távoli asztallal lehet csatlakozni a számítási feladat kiszolgálójához.

![Az oktatóanyag hálózati infrastruktúrája](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tesztelési hálózati környezet beállítása
> * Tűzfal üzembe helyezése
> * Alapértelmezett útvonal létrehozása
> * Alkalmazás-szabály konfigurálása a www.google.com való hozzáférés engedélyezéséhez
> * Hálózatszabály konfigurálása külső DNS-kiszolgálókhoz való hozzáférés engedélyezéséhez
> * A tűzfal tesztelése

Igény szerint az oktatóanyag az [Azure PowerShell](deploy-ps.md) használatával is elvégezhető.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="set-up-the-network"></a>A hálózat beállítása

Először is hozzon létre egy erőforráscsoportot, amely a tűzfal üzembe helyezéséhez szükséges erőforrásokat tartalmazza. Ezután hozzon létre egy virtuális hálózatot, alhálózatokat és tesztkiszolgálókat.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport tartalmazza az oktatóanyag összes erőforrását.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. A Azure Portal kezdőlapon válassza az **erőforráscsoportok** > **Hozzáadás**lehetőséget.
3. Az **Erőforráscsoport neve** mezőbe írja be a következőt: **Test-FW-RG**.
4. Az **Előfizetés** beállításnál válassza ki az előfizetését.
5. Az **Erőforráscsoport helye** beállításnál válasszon ki egy helyet. Minden ezután létrehozott erőforrásnak ugyanezen a helyen kell lennie.
6. Kattintson a **Létrehozás** gombra.

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

A VNet három alhálózatot fog tartalmazni.

> [!NOTE]
> A AzureFirewallSubnet-alhálózat mérete/26. További információ az alhálózat méretétől: [Azure Firewall GYIK](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása**lehetőséget.
2. A **hálózat**területen válassza a **virtuális hálózat**lehetőséget.
4. A **Név** mezőbe írja be a következőt: **Test-FW-VN**.
5. A **Címtér** mezőbe írja be a következőt: **10.0.0.0/16**.
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **erőforráscsoport**területen válassza a **test-FW-RG**elemet.
8. A **Hely** elemnél válassza a korábban használt helyet.
9. Az **Alhálózat** területen a **Név** mezőbe írja be a következőt: **AzureFirewallSubnet**. Ezen az alhálózaton lesz a tűzfal. Az alhálózat neve **kizárólag** AzureFirewallSubnet lehet.
10. A **címtartomány**mezőbe írja be a következőt: **10.0.1.0/26**.
11. Fogadja el a többi alapértelmezett beállítást, majd kattintson a **Létrehozás**gombra.

### <a name="create-additional-subnets"></a>További alhálózatok létrehozása

Ezután hozzon létre egy-egy alhálózatot a helyettesítő kiszolgáló és a számítási feladatok kiszolgálói számára.

1. A Azure Portal kezdőlapon válassza az **erőforráscsoportok** > **test-FW-RG**elemet.
2. Válassza ki a **test-FW-vn** virtuális hálózatot.
3. Válassza > az alhálózatok és alhálózatok lehetőséget.
4. A **Név** mezőbe írja be a következőt: **Workload-SN**.
5. A **Címtartomány** mezőbe írja be a következőt: **10.0.2.0/24**.
6. Kattintson az **OK** gombra.

Hozzon létre egy másik alhálózatot, amelynek a neve **Jump-SN**, a címtartománya pedig **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Most hozza létre a helyettesítő és a számítási feladatokat futtató virtuális gépeket, és helyezze el őket a megfelelő alhálózatokon.

1. A Azure Portal válassza az **erőforrás létrehozása**lehetőséget.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet a Kiemeltek listából.
3. Adja meg a következő értékeket a virtuális gép számára:

   |Beállítás  |Value  |
   |---------|---------|
   |Resource group     |**Test-FW-RG**|
   |Virtuális gép neve     |**SRV – ugrás**|
   |Régió     |Ugyanaz, mint az előző|
   |Rendszergazda felhasználóneve     |**azureuser**|
   |Windows 10     |**Azure123456!**|

4. A **bejövő portszabályok**területen a **nyilvános bejövő portok**esetében válassza a **kijelölt portok engedélyezése**lehetőséget.
5. A **bejövő portok kiválasztása**lapon válassza az **RDP (3389)** lehetőséget.

6. Fogadja el a többi alapértelmezett értéket **, majd válassza a Next (tovább) gombot: Lemezek**.
7. Fogadja el a lemez alapértelmezett értékeit, és kattintson **a Tovább gombra: Hálózatkezelés**.
8. Győződjön meg arról, hogy a **test-FW-vn** beállítás van kiválasztva a virtuális hálózathoz, és az alhálózat a **Jump-SN**.
9. A **nyilvános IP-** címek esetében fogadja el az alapértelmezett új nyilvános IP-cím nevét (SRV-Jump-IP).
11. Fogadja el a többi alapértelmezett értéket **, majd válassza a Next (tovább) gombot: Felügyelet**.
12. A rendszerindítási diagnosztika letiltásához válassza a **ki** lehetőséget. Fogadja el a többi alapértelmezett értéket, és válassza a **felülvizsgálat + létrehozás**lehetőséget.
13. Tekintse át a beállításokat az összefoglalás lapon, majd válassza a **Létrehozás**lehetőséget.

A következő táblázatban található információk segítségével konfigurálhat egy **SRV-Work**nevű virtuális gépet. A többi beállítás ugyanaz, mint az Srv-Jump virtuális gép esetében.

|Beállítás  |Value  |
|---------|---------|
|Subnet|**Munkaterhelés – SN**|
|Nyilvános IP-cím|**Nincsenek**|
|Nyilvános bejövő portok|**Nincsenek**|

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

Helyezze üzembe a tűzfalat a virtuális hálózaton.

1. A portál kezdőlapján válassza az **erőforrás létrehozása**lehetőséget.
2. Írja be a **tűzfal** kifejezést a keresőmezőbe, majd nyomja le az **ENTER**billentyűt.
3. Válassza a **tűzfal** lehetőséget, majd válassza a **Létrehozás**lehetőséget.
4. A **Tűzfal létrehozása** oldalon konfigurálja a tűzfalat a következő táblázatban található értékekkel:

   |Beállítás  |Value  |
   |---------|---------|
   |Subscription     |\<az Ön előfizetése\>|
   |Resource group     |**Test-FW-RG** |
   |Name (Név)     |**Test-FW01**|
   |Location     |Válassza a korábban használt helyet|
   |Virtuális hálózat választása     |**Meglévő használata**: **Teszt – FW-VN**|
   |Nyilvános IP-cím     |**Új létrehozása**. A nyilvános IP-címnek standard termékváltozat típusúnak kell lennie.|

5. Válassza az **Áttekintés + létrehozás** lehetőséget.
6. Tekintse át az összegzést, majd válassza a **Létrehozás** lehetőséget a tűzfal létrehozásához.

   Az üzembe helyezés néhány percet vesz igénybe.
7. Az üzembe helyezés befejeződése után nyissa meg a **test-FW-RG** erőforráscsoportot, és válassza ki a **test-FW01** tűzfalat.
8. Jegyezze fel a magánhálózati IP-címet. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

## <a name="create-a-default-route"></a>Alapértelmezett útvonal létrehozása

A **Workload-SN** alhálózatot konfigurálja úgy, hogy a kimenő alapértelmezett útvonal áthaladjon a tűzfalon.

1. A Azure Portal kezdőlapon válassza a **minden szolgáltatás**lehetőséget.
2. A **hálózat**területen válassza az **útválasztási táblák**elemet.
3. Válassza a **Hozzáadás** lehetőséget.
4. A **Név** mezőbe írja be a következőt: **Firewall-route**.
5. Az **Előfizetés** beállításnál válassza ki az előfizetését.
6. Az **erőforráscsoport**területen válassza a **test-FW-RG**elemet.
7. A **Hely** elemnél válassza a korábban használt helyet.
8. Kattintson a **Létrehozás** gombra.
9. Válassza a **frissítés**lehetőséget, majd válassza ki a **tűzfal-útvonal** útválasztási táblázatot.
10. Válassza ki az alhálózatok elemet, majd válassza a **hozzárendelés**lehetőséget.
11. Válassza a **Virtual Network** > **test-FW-vn**elemet.
12. **Alhálózat**esetében válassza a **munkaterhelés-SN**lehetőséget. Győződjön meg arról, hogy csak a **munkaterhelés-SN** alhálózatot választotta ehhez az útvonalhoz, ellenkező esetben a tűzfal nem fog megfelelően működni.

13. Kattintson az **OK** gombra.
14. Válassza az **útvonalak** lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
15. Az **útvonal neve**mezőbe írja be a következőt: **FW-DG**.
16. A **Címelőtag** mezőbe írja be a következőt: **0.0.0.0/0**.
17. A **Következő ugrás típusa** beállításnál válassza a **Virtuális berendezés** lehetőséget.

    Az Azure Firewall valójában egy felügyelt szolgáltatás, de ebben a helyzetben a virtuális berendezés beállítás is használható.
18. A **Következő ugrás címe** mezőbe írja be a tűzfal magánhálózati IP-címét, amelyet korábban feljegyzett.
19. Kattintson az **OK** gombra.

## <a name="configure-an-application-rule"></a>Alkalmazásszabály konfigurálása

Ez az az alkalmazási szabály, amely lehetővé teszi a kimenő hozzáférést a www.google.com.

1. Nyissa meg a **test-FW-RG**elemet, és válassza ki a **test-FW01** tűzfalat.
2. A **test-FW01** oldalon a **Beállítások**területen válassza a **szabályok**elemet.
3. Válassza ki az **alkalmazás-szabály gyűjtemény** fület.
4. Válassza az **alkalmazás-szabály gyűjtemény hozzáadása**lehetőséget.
5. A **Név** mezőbe írja be a következőt: **App-Coll01**.
6. A **Prioritás** mezőbe írja be a következőt: **200**.
7. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.
8. A **szabályok**, **cél teljes tartománynevek** **neve**mezőbe írja be a következőt: **Allow-Google**.
9. A **Forráscímek** mezőbe írja be a következőt: **10.0.2.0/24**.
10. A **Protokoll:port** mezőbe írja be a következőt: **http, https**.
11. **Cél teljes tartománynevek**esetén írja be a következőt: **www.Google.com**
12. Válassza a **Hozzáadás** lehetőséget.

Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről. Ezek a teljes tartománynevek csak az adott platformra vonatkoznak, egyéb célra nem használhatók. További információ: [Infrastruktúra FQDN-jei](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Hálózatszabály konfigurálása

Ez az a hálózatszabály, amely lehetővé teszi a kimenő hozzáférést két IP-címhez az 53-as porton (DNS).

1. Válassza a **hálózati szabályok gyűjteménye** fület.
2. Válassza a **hálózati szabálygyűjtemény hozzáadása**lehetőséget.
3. A **Név** mezőbe írja be a következőt: **Net-Coll01**.
4. A **Prioritás** mezőbe írja be a következőt: **200**.
5. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.

6. A **szabályok**területen a **név**mezőbe írja be a következőt: **Allow-DNS**.
7. A **Protokoll** beállításnál válassza az **UDP** lehetőséget.
8. A **Forráscímek** mezőbe írja be a következőt: **10.0.2.0/24**.
9. A Célcímek mezőbe írja be a következőt: **209.244.0.3,209.244.0.4**
10. A **Célportok** mezőbe írja be a következőt: **53**.
11. Válassza a **Hozzáadás** lehetőséget.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Módosítsa az **Srv-Work** hálózati adapter elsődleges és másodlagos DNS-címét.

Az oktatóanyag tesztelési célokra konfigurálja a kiszolgáló elsődleges és másodlagos DNS-címeit. Ez nem általános Azure Firewall követelmény.

1. Az Azure Portalon nyissa meg a **Test-FW-RG** erőforráscsoportot.
2. Válassza ki az **SRV-Work** virtuális gép hálózati adapterét.
3. A **Beállítások**területen válassza a **DNS-kiszolgálók**elemet.
4. A **DNS-kiszolgálók**területen válassza az **Egyéni**lehetőséget.
5. Írja be a **209.244.0.3** a címet a **DNS-kiszolgáló hozzáadása** szövegmezőbe, és **209.244.0.4** címet a következő szövegmezőbe.
6. Kattintson a **Mentés** gombra.
7. Indítsa újra az **Srv-Work** virtuális gépet.

## <a name="test-the-firewall"></a>A tűzfal tesztelése

Most tesztelje a tűzfalat, és ellenőrizze, hogy az a várt módon működik-e.

1. Az Azure Portalon tekintse át az **Srv-Work** virtuális gép hálózati beállításait, és jegyezze fel a gép magánhálózati IP-címét.
2. Csatlakoztasson egy távoli asztalt a **SRV-Jump** virtuális géphez, és jelentkezzen be. Onnan nyisson meg egy távoli asztali kapcsolattal az **SRV-Work** magánhálózati IP-címet.

3. Nyissa meg az Internet Explorert, és navigáljon a következő címre: https://www.google.com.
4. Az Internet Explorer biztonsági riasztások ablakában kattintson **az OK** > **Bezárás** gombra.

   Ekkor meg kell jelennie a Google kezdőlapjának.

5. Nyissa meg a következő címet: https://www.microsoft.com.

   A tűzfal blokkolja a hozzáférést.

Most ellenőrizte, hogy a tűzfalszabályok működnek-e:

* Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.
* Fel tudja oldani a DNS-neveket a konfigurált külső DNS-kiszolgálóval.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfalhoz kapcsolódó erőforrásokat a következő oktatóanyagban is használhatja, vagy ha már nincs rá szükség, törölje a **Test-FW-RG** erőforráscsoportot, és vele együtt a tűzfalhoz kapcsolódó összes erőforrást.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure Firewall naplók figyelése](./tutorial-diagnostics.md)
