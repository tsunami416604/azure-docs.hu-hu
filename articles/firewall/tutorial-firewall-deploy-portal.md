---
title: 'Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon'
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe és konfigurálhatja az Azure Firewallt az Azure Portalon.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 4/9/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: c038b899f60a4e04085b2343f2f02be5f7042ebb
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426409"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon

A kimenő hálózati hozzáférés ellenőrzése az általános hálózati biztonsági terv fontos részét képezi. Például előfordulhat, hogy szeretné korlátozni a webhelyekhez való hozzáférést. Vagy előfordulhat, hogy szeretné korlátozni a kimenő IP-címek és portok elérhető.

Az Azure-alhálózatok kimenő hálózati hozzáférése többek között az Azure Firewall használatával vezérelhető. Az Azure Firewall segítségével a következőket konfigurálhatja:

* Alkalmazásszabályokat, amelyek egy alhálózatról elérhető teljes tartományneveket (FQDN) határoznak meg.
* Hálózatszabályokat, amelyek forráscímet, protokollt, valamint célportot és célcímet határoznak meg.

A hálózati forgalmat a konfigurált tűzfalszabályok irányítják, ha alapértelmezett alhálózati átjáróként irányítja a tűzfalhoz a forgalmat.

Ebben az oktatóanyagban az üzembe helyezés érdekében egyetlen egyszerű virtuális hálózatot hozunk létre három alhálózattal. Éles környezetekben üzemelő példányok egy [küllős modell](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) használata javasolt, ha a tűzfal van-e a saját virtuális hálózatában. A számítási feladatok kiszolgálók társviszonyban lévő virtuális hálózatok egy vagy több alhálózattal rendelkező ugyanabban a régióban találhatók.

* **AzureFirewallSubnet** – ezen az alhálózaton található a tűzfal.
* **Workload-SN** – ezen az alhálózaton található a számítási feladat kiszolgálója. Ennek az alhálózatnak a hálózati forgalma a tűzfalon halad át.
* **Jump-SN** – ezen az alhálózaton található a helyettesítő kiszolgáló. A helyettesítő kiszolgáló egy nyilvános IP-címmel rendelkezik, amelyhez csatlakozni lehet a távoli asztal használatával. Innen pedig egy másik távoli asztallal lehet csatlakozni a számítási feladat kiszolgálójához.

![Az oktatóanyag hálózati infrastruktúrája](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tesztelési hálózati környezet beállítása
> * Tűzfal üzembe helyezése
> * Alapértelmezett útvonal létrehozása
> * Alkalmazás www.google.com való hozzáférés engedélyezése a szabály konfigurálása
> * Hálózatszabály konfigurálása külső DNS-kiszolgálókhoz való hozzáférés engedélyezéséhez
> * A tűzfal tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="set-up-the-network"></a>A hálózat beállítása

Először is hozzon létre egy erőforráscsoportot, amely a tűzfal üzembe helyezéséhez szükséges erőforrásokat tartalmazza. Ezután hozzon létre egy virtuális hálózatot, alhálózatokat és tesztkiszolgálókat.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport tartalmazza az oktatóanyag összes erőforrását.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Az Azure portál kezdőlapján válassza **erőforráscsoportok** > **Hozzáadás**.
3. Az **Erőforráscsoport neve** mezőbe írja be a következőt: **Test-FW-RG**.
4. Az **Előfizetés** beállításnál válassza ki az előfizetését.
5. Az **Erőforráscsoport helye** beállításnál válasszon ki egy helyet. Minden ezután létrehozott erőforrásnak ugyanezen a helyen kell lennie.
6. Kattintson a **Létrehozás** gombra.

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

A VNet három alhálózatot fog tartalmazni.

1. Az Azure portál kezdőlapján válassza **erőforrás létrehozása**.
2. A **hálózatkezelés**válassza **virtuális hálózati**.
4. A **Név** mezőbe írja be a következőt: **Test-FW-VN**.
5. A **Címtér** mezőbe írja be a következőt: **10.0.0.0/16**.
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. A **erőforráscsoport**válassza **Test-Keretrendszer-RG**.
8. A **Hely** elemnél válassza a korábban használt helyet.
9. Az **Alhálózat** területen a **Név** mezőbe írja be a következőt: **AzureFirewallSubnet**. Ezen az alhálózaton lesz a tűzfal. Az alhálózat neve **kizárólag** AzureFirewallSubnet lehet.
10. A **Címtartomány** mezőbe írja be a következőt: **10.0.1.0/24**.
11. Fogadja el az alapértelmezett beállításokat, és válassza ki **létrehozás**.

> [!NOTE]
> A AzureFirewallSubnet alhálózat minimális mérete/26-os.

### <a name="create-additional-subnets"></a>További alhálózatok létrehozása

Ezután hozzon létre egy-egy alhálózatot a helyettesítő kiszolgáló és a számítási feladatok kiszolgálói számára.

1. Az Azure portál kezdőlapján válassza **erőforráscsoportok** > **Test-Keretrendszer-RG**.
2. Válassza ki a **Test-Keretrendszer-VN** virtuális hálózatot.
3. Válassza ki **alhálózatok** > **+ alhálózat**.
4. A **Név** mezőbe írja be a következőt: **Workload-SN**.
5. A **Címtartomány** mezőbe írja be a következőt: **10.0.2.0/24**.
6. Kattintson az **OK** gombra.

Hozzon létre egy másik alhálózatot, amelynek a neve **Jump-SN**, a címtartománya pedig **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Most hozza létre a helyettesítő és a számítási feladatokat futtató virtuális gépeket, és helyezze el őket a megfelelő alhálózatokon.

1. Az Azure Portalon, válassza ki a **erőforrás létrehozása**.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet a Kiemeltek listából.
3. Adja meg a következő értékeket a virtuális gép számára:

   |Beállítás  |Érték  |
   |---------|---------|
   |Erőforráscsoport     |**Test-FW-RG**|
   |Virtuális gép neve     |**SRV-Jump**|
   |Régió     |Ugyanaz, mint a korábbi|
   |Rendszergazdai felhasználónév     |**azureuser**|
   |Jelszó     |**Azure123456!**|

4. Alatt **bejövőport-szabályok**, a **nyilvános bejövő portok**válassza **lehetővé teszi a kiválasztott portok**.
5. A **bejövő portok kiválasztása**válassza **RDP (3389-es)**.

6. Fogadja el a más alapértelmezett beállításokat, és válasszon **tovább: Lemezek**.
7. Fogadja el a lemez alapértelmezett beállításokat, és válasszon **tovább: Hálózatkezelés**.
8. Győződjön meg arról, hogy **Test-Keretrendszer-VN** a kiválasztott virtuális hálózat és az alhálózat van **Jump-SN**.
9. A **nyilvános IP-cím**, fogadja el az alapértelmezett új nyilvános ip-cím neve (Srv-Jump-ip).
11. Fogadja el a más alapértelmezett beállításokat, és válasszon **tovább: Felügyeleti**.
12. Válassza ki **ki** a rendszerindítási diagnosztika letiltásához. Fogadja el a más alapértelmezett beállításokat, és válasszon **felülvizsgálat + létrehozása**.
13. Tekintse át a beállításokat az Összegzés lapon, és válassza ki **létrehozás**.

Az alábbi táblázat az információk segítségével állítsa be egy másik virtuális gép nevű **Srv-munkahelyi**. A többi beállítás ugyanaz, mint az Srv-Jump virtuális gép esetében.

|Beállítás  |Érték  |
|---------|---------|
|Alhálózat|**Workload-SN**|
|Nyilvános IP-cím|**None**|
|Nyilvános bejövő portok|**None**|

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

Helyezze üzembe a tűzfalat a virtuális hálózaton.

1. A portál kezdőlapján válassza **erőforrás létrehozása**.
2. Típus **tűzfal** a keresési mezőbe, majd nyomja le az **Enter**.
3. Válassza ki **tűzfal** majd **létrehozás**.
4. A **Tűzfal létrehozása** oldalon konfigurálja a tűzfalat a következő táblázatban található értékekkel:

   |Beállítás  |Érték  |
   |---------|---------|
   |Előfizetés     |\<az Ön előfizetése\>|
   |Erőforráscsoport     |**Test-FW-RG** |
   |Name (Név)     |**Test-FW01**|
   |Hely     |Válassza a korábban használt helyet|
   |Válasszon egy virtuális hálózatot     |**Meglévő használata**: **Test-FW-VN**|
   |Nyilvános IP-cím     |**Új létrehozása**. A nyilvános IP-címnek standard termékváltozat típusúnak kell lennie.|

5. Válassza az **Áttekintés + létrehozás** lehetőséget.
6. Tekintse át az összefoglalást, és válassza ki **létrehozás** hozhat létre a tűzfalon.

   Az üzembe helyezés néhány percet vesz igénybe.
7. Üzembe helyezés befejezése után nyissa meg a **Test-Keretrendszer-RG** erőforrás-csoportba, és válassza ki a **Test-FW01** tűzfal.
8. Jegyezze fel a magánhálózati IP-címet. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

## <a name="create-a-default-route"></a>Alapértelmezett útvonal létrehozása

A **Workload-SN** alhálózatot konfigurálja úgy, hogy a kimenő alapértelmezett útvonal áthaladjon a tűzfalon.

1. Az Azure portál kezdőlapján válassza **minden szolgáltatás**.
2. A **hálózatkezelés**válassza **útválasztási táblázatok**.
3. Válassza a **Hozzáadás** lehetőséget.
4. A **Név** mezőbe írja be a következőt: **Firewall-route**.
5. Az **Előfizetés** beállításnál válassza ki az előfizetését.
6. A **erőforráscsoport**válassza **Test-Keretrendszer-RG**.
7. A **Hely** elemnél válassza a korábban használt helyet.
8. Kattintson a **Létrehozás** gombra.
9. Válassza ki **frissítése**, majd válassza ki a **tűzfal-route** útválasztási táblázatot.
10. Válassza ki **alhálózatok** majd **társítása**.
11. Válassza ki **virtuális hálózati** > **Test-Keretrendszer-VN**.
12. A **alhálózati**válassza **munkaterhelés-SN**. Jelölje ki, hogy csak a **munkaterhelés-SN** ezt az útvonalat az alhálózathoz, ellenkező esetben a tűzfal nem fog megfelelően működni.

13. Kattintson az **OK** gombra.
14. Válassza ki **útvonalak** majd **Hozzáadás**.
15. A **útvonalnév**, típus **keretrendszer-dg**.
16. A **Címelőtag** mezőbe írja be a következőt: **0.0.0.0/0**.
17. A **Következő ugrás típusa** beállításnál válassza a **Virtuális berendezés** lehetőséget.

    Az Azure Firewall valójában egy felügyelt szolgáltatás, de ebben a helyzetben a virtuális berendezés beállítás is használható.
18. A **Következő ugrás címe** mezőbe írja be a tűzfal magánhálózati IP-címét, amelyet korábban feljegyzett.
19. Kattintson az **OK** gombra.

## <a name="configure-an-application-rule"></a>Alkalmazásszabály konfigurálása

Ez az a alkalmazás www.google.com a kimenő hozzáférést engedélyező szabállyal.

1. Nyissa meg a **Test-Keretrendszer-RG**, és válassza ki a **Test-FW01** tűzfal.
2. Az a **Test-FW01** lap **beállítások**válassza **szabályok**.
3. Válassza ki a **alkalmazás szabálygyűjtemény** fülre.
4. Válassza ki **adja hozzá az alkalmazás szabálygyűjtemény**.
5. A **Név** mezőbe írja be a következőt: **App-Coll01**.
6. A **Prioritás** mezőbe írja be a következőt: **200**.
7. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.
8. A **szabályok**, **cél teljes tartománynevek**, a **neve**, típus **engedélyezés-Google**.
9. A **Forráscímek** mezőbe írja be a következőt: **10.0.2.0/24**.
10. A **Protokoll:port** mezőbe írja be a következőt: **http, https**.
11. A **cél teljes TARTOMÁNYNEVEK**, típus **www.google.com**
12. Válassza a **Hozzáadás** lehetőséget.

Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről. Ezek a teljes tartománynevek csak az adott platformra vonatkoznak, egyéb célra nem használhatók. További információ: [Infrastruktúra FQDN-jei](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Hálózatszabály konfigurálása

Ez az a hálózatszabály, amely lehetővé teszi a kimenő hozzáférést két IP-címhez az 53-as porton (DNS).

1. Válassza ki a **szabálygyűjtemény hálózati** fülre.
2. Válassza ki **adja hozzá a hálózati szabálygyűjtemény**.
3. A **Név** mezőbe írja be a következőt: **Net-Coll01**.
4. A **Prioritás** mezőbe írja be a következőt: **200**.
5. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.

6. A **szabályok**, a **neve**, típus **engedélyezése – DNS**.
7. A **Protokoll** beállításnál válassza az **UDP** lehetőséget.
8. A **Forráscímek** mezőbe írja be a következőt: **10.0.2.0/24**.
9. A Célcímek mezőbe írja be a következőt: **209.244.0.3,209.244.0.4**
10. A **Célportok** mezőbe írja be a következőt: **53**.
11. Válassza a **Hozzáadás** lehetőséget.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Módosítsa az **Srv-Work** hálózati adapter elsődleges és másodlagos DNS-címét.

Tesztelési célokra ebben az oktatóanyagban a kiszolgáló az elsődleges és másodlagos DNS-címek konfigurálásához. Ez nem egy általános Azure tűzfallal kapcsolatos követelmény.

1. Az Azure Portalon nyissa meg a **Test-FW-RG** erőforráscsoportot.
2. Válassza ki a hálózati adapter számára a **Srv-munkahelyi** virtuális gépet.
3. A **beállítások**válassza **DNS-kiszolgálók**.
4. A **DNS-kiszolgálók**válassza **egyéni**.
5. Írja be a **209.244.0.3** a címet a **DNS-kiszolgáló hozzáadása** szövegmezőbe, és **209.244.0.4** címet a következő szövegmezőbe.
6. Kattintson a **Mentés** gombra.
7. Indítsa újra az **Srv-Work** virtuális gépet.

## <a name="test-the-firewall"></a>A tűzfal tesztelése

Most tesztelje le, a tűzfal, ellenőrizze, hogy az elvárt módon működik.

1. Az Azure Portalon tekintse át az **Srv-Work** virtuális gép hálózati beállításait, és jegyezze fel a gép magánhálózati IP-címét.
2. Csatlakozzon a távoli asztali **Srv-Jump** virtuális gépet, és jelentkezzen be. Itt, nyissa meg a távoli asztali kapcsolatot a **Srv-munkahelyi** magánhálózati IP-címet.

3. Nyissa meg az Internet Explorert, és navigáljon a következő címre: http://www.google.com.
4. Válassza ki **OK** > **Bezárás** a az Internet Explorer biztonsági riasztásokat.

   A Google kezdőlapon megtekintheti.

5. Nyissa meg a következő címet: http://www.microsoft.com.

   A tűzfal blokkolja a hozzáférést.

Így most már ellenőrizte, hogy a tűzfalszabályok működnek:

* Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.
* Fel tudja oldani a DNS-neveket a konfigurált külső DNS-kiszolgálóval.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfalhoz kapcsolódó erőforrásokat a következő oktatóanyagban is használhatja, vagy ha már nincs rá szükség, törölje a **Test-FW-RG** erőforráscsoportot, és vele együtt a tűzfalhoz kapcsolódó összes erőforrást.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: A figyelő Azure tűzfal-naplókon](./tutorial-diagnostics.md)
