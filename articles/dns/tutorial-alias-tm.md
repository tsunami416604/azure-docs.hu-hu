---
title: Oktatóanyag – Azure DNS-aliasrekord létrehozása tartomány legfelső szintű neveinek támogatásához a Traffic Managerrel
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhat Azure DNS-aliasrekordot, hogy támogassa a tartomány legfelső szintű nevének használatát a Traffic Managerrel.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 4a5d8968861f6f2938e605d2f7106529ca401df4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967438"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Oktatóanyag: aliasrekord konfigurálása a hogy, támogassa a legfelső szintű tartományneveket a Traffic Managerrel 

A Traffic Manager-profilra való hivatkozáshoz létrehozhat egy aliasrekordot a legelső szintű tartománynévhez (pl. contoso.com). Tehát ahelyett, hogy átirányítási szolgáltatást használna ehhez, konfigurálhatja az Azure DNS-t, hogy közvetlenül a zónájából hivatkozzon egy Traffic Manager-profilra. 


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Gazda-virtuálisgép és hálózati infrastruktúra létrehozása
> * Traffic Manager-profil létrehozása
> * Aliasrekord létrehozása
> * Az aliasrekord tesztelése


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Elérhetőnek kell lennie egy tartománynévnek, amelyet üzemeltethet az Azure DNS-ben a teszteléshez. Teljes egészében rendelkeznie kell ezzel a tartománnyal, többek között be kell tudnia állítani a tartomány névkiszolgálói (NS) rekordjait.

A tartomány Azure DNS-ben való üzemeltetésére vonatkozó utasításokért lásd az [Oktatóanyag: A tartomány üzemeltetése az Azure DNS-ben](dns-delegate-domain-azure-dns.md).

Az ebben az oktatóanyagban használt példatartománynév a contoso.com, de a saját tartománynevét kell használnia.

## <a name="create-the-network-infrastructure"></a>A hálózati infrastruktúra létrehozása
Először hozzon létre egy virtuális hálózatot és a egy alhálózatot a webkiszolgálók elhelyezéséhez.
1. Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.
2. Kattintson a portál bal felső sarkában az **Erőforrás létrehozása** elemre, írja be, hogy *erőforráscsoport* a keresési mezőbe, majd hozzon létre egy erőforráscsoportot **RG-DNS-Alias-TM** néven.
3. Kattintson az **Erőforrás létrehozása**, a **Hálózatkezelés**, végül a **Virtuális hálózat** elemre.
4. Hozzon létre egy **VNet-Servers**, nevű virtuális hálózatot, helyezze el az **RG-DNS-Alias-TM** erőforráscsoportban, és adja az alhálózatnak az **SN-Web** nevet.

## <a name="create-two-web-server-virtual-machines"></a>Két webkiszolgáló virtuális gép létrehozása
1. Kattintson az **Erőforrás létrehozása**, majd a **Windows Server 2016 VM** hivatkozásra.
2. Névkén írja be, hogy **Web-01**, és helyezze el a virtuális gépet az **RG-DNS-Alias-TM** erőforráscsoportban. Írjon be egy felhasználónevet, jelszót, majd kattintson **OK** gombra.
3. A **Méret** megadásakor válasszon egy termékváltozatot 8 GB RAM-mal.
4. A **Beállítások** területen válassza a **VNet-Servers** virtuális hálózatot és az **SN-Web** alhálózatot.
5. Kattintson a **Nyilvános IP-cím** elemre, aztán a **Hozzárendelés** alatt a **Statikus** elemre, majd az **OK** gombra.
6. Nyilvános bejövő portként válassza a **HTTP**, **HTTPS** és **RDP (3389)** lehetőséget, majd kattintson az **OK** gombra.
7. Az összefoglalás lapon kattintson a **Létrehozás** gombra.

   Ez eltarthat néhány percig.
6. Ugyanezt az eljárást megismételve hozzon létre egy másik virtuális gépet **Web-02** néven.

### <a name="add-a-dns-label"></a>Adja meg a DNS-címkét
A nyilvános IP-címeknek DNS-címkére van szükségük, hogy működjenek a Traffic Managerrel.
1. Az **RG-DNS-Alias-TM** erőforráscsoportban kattintson a **Web-01-ip** nyilvános IP-címre.
2. A **Beállítások** területen kattintson a **Konfiguráció** elemre.
3. A DNS-név címkéjének megadásához írja a szövegmezőbe, hogy **web01pip**.
4. Kattintson a **Save** (Mentés) gombra.

Ismételje meg ezt az eljárást a **Web-02-ip** nyilvános IP-címnél a **web02pip** értéket megadva a DNS-név címéjeként.

### <a name="install-iis"></a>Az IIS telepítése

Telepítse az IIS-t mind a **Web-01**, mind a **Web-02** virtuális gépen.

1. Csatlakozzon a **Web-01** virtuális géphez, és jelentkezzen be.
2. A Kiszolgálókezelő irányítópulton kattintson a **Szerepkörök és szolgáltatások hozzáadása** lehetőségre.
3. Kattintson háromszor a **Következő** gombra, majd a **Kiszolgálói szerepkörök** lapon válassza a **Webkiszolgáló (IIS)** lehetőséget.
4. Kattintson a **Szolgáltatások hozzáadása**, majd a **Következő** gombra.
5. Kattintson négyszer a **Következő** gombra, majd kattintson a **Telepítés** gombra.

   Ez eltarthat pár percig.
6. A telepítés befejezése után kattintson a **Bezárás** gombra.
7. Nyisson meg egy webböngészőt, nyissa meg a **localhost** címet, és győződjön meg róla, hogy megjelenik-e az IIS alapértelmezett weblapja.

Ismételje meg a folyamatot az IIS **Web-02** virtuális gépen való telepítéséhez.


## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Határidő 

1. Nyissa meg az **RG-DNS-Alias-TM** erőforráscsoportot, és kattintson a **Web-01-ip** nyilvános IP-címre. Jegyezze fel az IP-címet későbbi használatra. Ismételje meg a műveletet a **Web-02-ip** nyilvános IP-címmel.
1. Kattintson az **Erőforrás létrehozása**, a **Hálózatkezelés**, végül a **Traffic Manager-profil** elemre.
2. Névkén írja be, hogy **TM-alias-test**, és helyezze el az **RG-DNS-Alias-TM** erőforráscsoportban.
3. Kattintson a **Create** (Létrehozás) gombra.
4. Az üzembe helyezés befejezése után kattintson az **Erőforrás megnyitása** gombra.
5. A Traffic Manager profil lapján a **Beállítások**, alatt kattintson a **Végpontok** elemre.
6. Kattintson a **Hozzáadás** parancsra.
7. A **Típus** értékeként válassza a **Külső végpont** lehetőséget, a **Név** mezőbe írja be, hogy **EP-Web01**.
8. A **Teljes tartománynév (FQDN) vagy IP-cím** szövegmezőbe írja be a **Web-01-ip** IP-címét, amelyet korábban feljegyzett.
9. Válassza ki ugyanazt a **helyet**, mint a többi erőforrásnál, majd kattintson az **OK** gombra.

Ismételje meg ezt az eljárást a **Web-02** végpont hozzáadásához a korábban a **Web-02-ip** esetében lejegyzett IP-címmel.

## <a name="create-an-alias-record"></a>Aliasrekord létrehozása

Hozzon létre egy aliasrekordot, amely a Traffic Manager-profilra mutat.

1. Kattintson az Azure DNS-zónára a zóna megnyitásához.
2. Kattintson a **Rekordhalmaz** gombra.
3. Hagyja a **Név** szövegmezőt üresen, hogy a legfelső szintű tartománynevet (például contoso.com) jelölje.
4. Hagyja meg a **Típus** mező értékét **A** rekordként.
5. Kattintson az **Alias rekordhalmaz** jelölőnégyzetre.
6. Kattintson az **Azure-szolgáltatás kiválasztása** elemre, és válassza a **TM-alias-test** Traffic Manager-profilt.

## <a name="test-the-alias-record"></a>Az aliasrekord tesztelése

1. Egy webböngészőben keresse meg a legfelső szintű tartománynevet (például contoso.com). Ekkor az IIS alapértelmezett weblapjának kell megjelennie. Zárja be a webböngészőt.
2. Állítsa le a **Web-01** virtuális gépet, és várjon néhány percig, amíg teljesen leáll.
3. Nyisson meg egy új böngészőablakot, és keresse meg ismét a legfelső szintű tartománynevet.
4. Újra kell megjelennie az alapértelmezett IIS-oldalnak, mivel a Traffic Manager megoldotta a helyzetet, és átirányította a forgalmat a **Web-02** virtuális gépre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az **RG-DNS-Alias-TM** erőforráscsoportot az oktatóanyagban létrehozott összes erőforrás törléséhez.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy aliasrekordot, hogy a legfelső szintű tartománynevet használhassa a Traffic Manager-profilra való hivatkozáshoz. Az Azure DNS és a webalkalmazások részletesebb megismeréséhez folytassa a webalkalmazásokról szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
