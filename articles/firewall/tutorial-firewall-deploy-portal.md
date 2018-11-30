---
title: 'Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon'
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe és konfigurálhatja az Azure Firewallt az Azure Portalon.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/15/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: be4cbc7e955e56853809378f98e9733ffe4a20c3
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633724"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon

A kimenő hálózati hozzáférés ellenőrzése az általános hálózati biztonsági terv fontos részét képezi. Például célszerű korlátoznia a webhelyekhez vagy az elérhető kimenő IP-címekhez és portokhoz való hozzáférést.

Az Azure-alhálózatok kimenő hálózati hozzáférése többek között az Azure Firewall használatával vezérelhető. Az Azure Firewall segítségével a következőket konfigurálhatja:

* Alkalmazásszabályokat, amelyek egy alhálózatról elérhető teljes tartományneveket (FQDN) határoznak meg.
* Hálózatszabályokat, amelyek forráscímet, protokollt, valamint célportot és célcímet határoznak meg.

A hálózati forgalmat a konfigurált tűzfalszabályok irányítják, ha alapértelmezett alhálózati átjáróként irányítja a tűzfalhoz a forgalmat.

Ebben az oktatóanyagban az üzembe helyezés érdekében egyetlen egyszerű virtuális hálózatot hozunk létre három alhálózattal. Éles környezetekben javasolt egy [küllős modell](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) használata, ahol a tűzfal a saját virtuális hálózatán található, a számítási feladatok kiszolgálói pedig társított virtuális hálózatokon vannak ugyanabban a régióban, egy vagy több alhálózaton.

- **AzureFirewallSubnet** – ezen az alhálózaton található a tűzfal.
- **Workload-SN** – ezen az alhálózaton található a számítási feladat kiszolgálója. Ennek az alhálózatnak a hálózati forgalma a tűzfalon halad át.
- **Jump-SN** – ezen az alhálózaton található a helyettesítő kiszolgáló. A helyettesítő kiszolgáló egy nyilvános IP-címmel rendelkezik, amelyhez csatlakozni lehet a távoli asztal használatával. Innen pedig egy másik távoli asztallal lehet csatlakozni a számítási feladat kiszolgálójához.

![Az oktatóanyag hálózati infrastruktúrája](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tesztelési hálózati környezet beállítása
> * Tűzfal üzembe helyezése
> * Alapértelmezett útvonal létrehozása
> * Egy alkalmazást, az msn.com való hozzáférés engedélyezése
> * Hálózatszabály konfigurálása külső DNS-kiszolgálókhoz való hozzáférés engedélyezéséhez
> * A tűzfal tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="set-up-the-network"></a>A hálózat beállítása

Először is hozzon létre egy erőforráscsoportot, amely a tűzfal üzembe helyezéséhez szükséges erőforrásokat tartalmazza. Ezután hozzon létre egy virtuális hálózatot, alhálózatokat és tesztkiszolgálókat.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport tartalmazza az oktatóanyag összes erőforrását.

1. Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) webhelyen.
2. Az Azure Portal kezdőlapján kattintson az **Erőforráscsoportok** > **Hozzáadás** elemre.
3. Az **Erőforráscsoport neve** mezőbe írja be a következőt: **Test-FW-RG**.
4. Az **Előfizetés** beállításnál válassza ki az előfizetését.
5. Az **Erőforráscsoport helye** beállításnál válasszon ki egy helyet. Minden ezután létrehozott erőforrásnak ugyanezen a helyen kell lennie.
6. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

A VNet három alhálózatot fog tartalmazni.

1. Az Azure Portal kezdőlapján kattintson a **Minden szolgáltatás** elemre.
2. A **Hálózat** területen kattintson a **Virtuális hálózatok** elemre.
3. Kattintson a **Hozzáadás** parancsra.
4. A **Név** mezőbe írja be a következőt: **Test-FW-VN**.
5. A **Címtér** mezőbe írja be a következőt: **10.0.0.0/16**.
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **Erőforráscsoport** területen válassza a **Meglévő használata** > **Test-FW-RG** lehetőséget.
8. A **Hely** elemnél válassza a korábban használt helyet.
9. Az **Alhálózat** területen a **Név** mezőbe írja be a következőt: **AzureFirewallSubnet**. Ezen az alhálózaton lesz a tűzfal. Az alhálózat neve **kizárólag** AzureFirewallSubnet lehet.
10. A **Címtartomány** mezőbe írja be a következőt: **10.0.1.0/24**.
11. Használja a többi alapértelmezett beállítást, és kattintson a **Létrehozás** elemre.

> [!NOTE]
> A AzureFirewallSubnet alhálózat minimális mérete/26-os.

### <a name="create-additional-subnets"></a>További alhálózatok létrehozása

Ezután hozzon létre egy-egy alhálózatot a helyettesítő kiszolgáló és a számítási feladatok kiszolgálói számára.

1. Az Azure Portal kezdőlapján kattintson az **Erőforráscsoportok** > **Test-FW-RG** elemre.
2. Kattintson a **Test-FW-VN** virtuális hálózatra.
3. Kattintson az **Alhálózatok** > **+Alhálózat** menüpontra.
4. A **Név** mezőbe írja be a következőt: **Workload-SN**.
5. A **Címtartomány** mezőbe írja be a következőt: **10.0.2.0/24**.
6. Kattintson az **OK** gombra.

Hozzon létre egy másik alhálózatot, amelynek a neve **Jump-SN**, a címtartománya pedig **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Most hozza létre a helyettesítő és a számítási feladatokat futtató virtuális gépeket, és helyezze el őket a megfelelő alhálózatokon.

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra.
2. Kattintson a **számítási** majd **Windows Server 2016 Datacenter** a kiemelt lista.
3. Adja meg a következő értékeket a virtuális gép számára:

    - *Test-Keretrendszer-RG* az erőforráscsoportnak.
    - *SRV-Jump* – a virtuális gép neve.
    - A rendszergazda felhasználóneve: *azureuser*.
    - A jelszó *Azure123456!* .

4. A **bejövőport-szabályok**, a **nyilvános bejövő portok**, kattintson a **lehetővé teszi a kiválasztott portok**.
5. A **bejövő portok kiválasztása**válassza **RDP (3389-es)**.

6. Elfogadhatja az alapértelmezett beállításokat, és kattintson a **tovább: lemezek**.
7. Fogadja el a lemez alapértelmezett beállításokat, és kattintson a **tovább: hálózatkezelés**.
8. Győződjön meg arról, hogy **Test-Keretrendszer-VN** a kiválasztott virtuális hálózat és az alhálózat van **Jump-SN**.
9. A **nyilvános IP-cím**, kattintson a **új létrehozása**.
10. Típus **Srv-Jump-PIP** a nyilvános IP-cím neve, majd kattintson a **OK**.
11. Elfogadhatja az alapértelmezett beállításokat, és kattintson a **tovább: felügyeleti**.
12. Kattintson a **ki** a rendszerindítási diagnosztika letiltásához. Elfogadhatja az alapértelmezett beállításokat, és kattintson a **felülvizsgálat + létrehozása**.
13. Tekintse át a beállításokat az Összegzés lapon, és kattintson **létrehozás**.

Ugyanezt az eljárást megismételve hozzon létre egy virtuális gépet **Srv-Work** néven.

Az alábbi táblázatban az információk segítségével konfigurálhatja az Srv-munkahelyi virtuális gép. A többi beállítás ugyanaz, mint az Srv-Jump virtuális gép esetében.

|Beállítás  |Érték  |
|---------|---------|
|Alhálózat|Workload-SN|
|Nyilvános IP-cím|None|
|Nyilvános bejövő portok|None|

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

Helyezze üzembe a tűzfalat a virtuális hálózaton.

1. A portál kezdőlapján kattintson az **Erőforrás létrehozása** gombra.
2. Kattintson a **Hálózat**, majd a **Kiemelt**, végül az **Összes megtekintése** elemre.
3. Kattintson a **Tűzfal** > **Létrehozás** parancsra. 
4. A **Tűzfal létrehozása** oldalon konfigurálja a tűzfalat a következő táblázatban található értékekkel:

   |Beállítás  |Érték  |
   |---------|---------|
   |Name (Név)     |Test-FW01|
   |Előfizetés     |\<az Ön előfizetése\>|
   |Erőforráscsoport     |**Meglévő használata**: Test-FW-RG |
   |Hely     |Válassza a korábban használt helyet|
   |Válasszon egy virtuális hálózatot     |**Meglévő használata**: Test-FW-VN|
   |Nyilvános IP-cím     |**Új létrehozása**. A nyilvános IP-címnek standard termékváltozat típusúnak kell lennie.|

5. Kattintson az **Áttekintés + létrehozás** elemre.
6. Tekintse át az összefoglalást, majd kattintson a **Létrehozás** elemre a tűzfal létrehozásához.

   Az üzembe helyezés néhány percet vesz igénybe.
7. Ha az üzembe helyezés elkészült, nyissa meg a **Test-FW-RG** erőforráscsoportot, majd kattintson a **Test-FW01** tűzfalra.
8. Jegyezze fel a magánhálózati IP-címet. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

## <a name="create-a-default-route"></a>Alapértelmezett útvonal létrehozása

A **Workload-SN** alhálózatot konfigurálja úgy, hogy a kimenő alapértelmezett útvonal áthaladjon a tűzfalon.

1. Az Azure Portal kezdőlapján kattintson a **Minden szolgáltatás** elemre.
2. A **Hálózat** területen kattintson az **Útválasztási táblázatok** elemre.
3. Kattintson a **Hozzáadás** parancsra.
4. A **Név** mezőbe írja be a következőt: **Firewall-route**.
5. Az **Előfizetés** beállításnál válassza ki az előfizetését.
6. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd a **Test-FW-RG** lehetőséget.
7. A **Hely** elemnél válassza a korábban használt helyet.
8. Kattintson a **Create** (Létrehozás) gombra.
9. Kattintson a **Frissítés** elemre, majd a **Firewall-route** útválasztási táblázatra.
10. Kattintson az **Alhálózatok** > **Társítás** parancsra.
11. Kattintson a **Virtuális hálózat** > **Test-FW-VN** elemre.
12. Az **Alhálózat** mezőben válassza a **Workload-SN** elemet. Ügyeljen arra, hogy csak a **Workload-SN** alhálózatot válassza ki ehhez az útvonalhoz, máskülönben a tűzfal nem fog megfelelően működni.

13. Kattintson az **OK** gombra.
14. Kattintson az **Útvonalak** > **Hozzáadás** parancsra.
15. Az **Útvonal neve** mezőbe írja be következőt: **FW-DG**.
16. A **Címelőtag** mezőbe írja be a következőt: **0.0.0.0/0**.
17. A **Következő ugrás típusa** beállításnál válassza a **Virtuális berendezés** lehetőséget.

    Az Azure Firewall valójában egy felügyelt szolgáltatás, de ebben a helyzetben a virtuális berendezés beállítás is használható.
18. A **Következő ugrás címe** mezőbe írja be a tűzfal magánhálózati IP-címét, amelyet korábban feljegyzett.
19. Kattintson az **OK** gombra.

## <a name="configure-an-application-rule"></a>Alkalmazásszabály konfigurálása

Ez az a alkalmazás szabály, amely lehetővé teszi a kimenő hozzáférést msn.com.

1. Nyissa meg a **Test-FW-RG** erőforráscsoportot, majd kattintson a **Test-FW01** tűzfalra.
2. A **Test-FW01** oldal **Beállítások** területén kattintson a **Szabályok** elemre.
3. Kattintson a **alkalmazás szabálygyűjtemény** fülre.
4. Kattintson az **Alkalmazásszabály-gyűjtemény hozzáadása** elemre.
5. A **Név** mezőbe írja be a következőt: **App-Coll01**.
6. A **Prioritás** mezőbe írja be a következőt: **200**.
7. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.
8. A **szabályok**, **cél teljes tartománynevek**, a **neve**, típus **AllowGH**.
9. A **Forráscímek** mezőbe írja be a következőt: **10.0.2.0/24**.
10. A **Protokoll:port** mezőbe írja be a következőt: **http, https**.
11. A **cél teljes TARTOMÁNYNEVEK**, típus **msn.com**
12. Kattintson a **Hozzáadás** parancsra.

Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről. Ezek a teljes tartománynevek csak az adott platformra vonatkoznak, egyéb célra nem használhatók. További információ: [Infrastruktúra FQDN-jei](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Hálózatszabály konfigurálása

Ez az a hálózatszabály, amely lehetővé teszi a kimenő hozzáférést két IP-címhez az 53-as porton (DNS).

1. Kattintson a **szabálygyűjtemény hálózati** fülre.
2. Kattintson a **Hálózati szabálygyűjtemény hozzáadása** elemre.
3. A **Név** mezőbe írja be a következőt: **Net-Coll01**.
4. A **Prioritás** mezőbe írja be a következőt: **200**.
5. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.

6. A **Szabályok** területen a **Név** mezőbe írja be a következőt: **AllowDNS**.
7. A **Protokoll** beállításnál válassza az **UDP** lehetőséget.
8. A **Forráscímek** mezőbe írja be a következőt: **10.0.2.0/24**.
9. A Célcímek mezőbe írja be a következőt: **209.244.0.3,209.244.0.4**
10. A **Célportok** mezőbe írja be a következőt: **53**.
11. Kattintson a **Hozzáadás** parancsra.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Módosítsa az **Srv-Work** hálózati adapter elsődleges és másodlagos DNS-címét.

Ebben az oktatóanyagban tesztelés céljából konfiguráljuk az elsődleges és másodlagos DNS-címeket. Ez nem az Azure Firewall általános követelménye.

1. Az Azure Portalon nyissa meg a **Test-FW-RG** erőforráscsoportot.
2. Kattintson az **Srv-Work** virtuális gép hálózati adapterére.
3. A **Beállítások** területen kattintson a **DNS-kiszolgálók** elemre.
4. A **DNS-kiszolgálók** területen kattintson az **Egyéni** elemre.
5. Írja be a **209.244.0.3** a címet a **DNS-kiszolgáló hozzáadása** szövegmezőbe, és **209.244.0.4** címet a következő szövegmezőbe.
6. Kattintson a **Save** (Mentés) gombra. 
7. Indítsa újra az **Srv-Work** virtuális gépet.

## <a name="test-the-firewall"></a>A tűzfal tesztelése

Most tesztelje a tűzfalat, hogy meggyőződjön a megfelelő működéséről.

1. Az Azure Portalon tekintse át az **Srv-Work** virtuális gép hálózati beállításait, és jegyezze fel a gép magánhálózati IP-címét.
2. Csatlakoztasson egy távoli asztalt az **Srv-Jump** virtuális géphez, majd onnan hozzon létre egy távoli asztali kapcsolatot az **Srv-Work** magánhálózati IP-címéhez.

3. Nyissa meg az Internet Explorert, és navigáljon a következő címre: http://msn.com.
4. Kattintson az **OK** > **Bezárás** gombra a biztonsági riasztások oldalon.

   Az MSN kezdőlapon megtekintheti.

5. Nyissa meg a következő címet: http://www.msn.com.

   A tűzfal blokkolja a hozzáférést.

Ezzel ellenőrizte, hogy a tűzfalszabályok működnek-e:

- Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.
- Fel tudja oldani a DNS-neveket a konfigurált külső DNS-kiszolgálóval.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfalhoz kapcsolódó erőforrásokat a következő oktatóanyagban is használhatja, vagy ha már nincs rá szükség, törölje a **Test-FW-RG** erőforráscsoportot, és vele együtt a tűzfalhoz kapcsolódó összes erőforrást.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./tutorial-diagnostics.md)
