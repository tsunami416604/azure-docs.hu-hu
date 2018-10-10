---
title: Oktatóanyag – Azure DNS-aliasrekord létrehozása egy nyilvános Azure IP-címre való hivatkozáshoz.
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhat egy Azure DNS-aliasrekordot nyilvános Azure IP-címre való hivatkozáshoz.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 842015d853790e3ac78214cca6a70becb7f9fadf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991229"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Oktatóanyag: Aliasrekord konfigurálása nyilvános Azure IP-címre való hivatkozáshoz 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hálózati infrastruktúra létrehozása
> * Webkiszolgálót futtató virtuális gép létrehozása
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
2. Kattintson a portál bal felső sarkában az **Erőforrás létrehozása** elemre, írja be, hogy *erőforráscsoport* a keresési mezőbe, majd hozzon létre egy új erőforráscsoportot **RG-DNS-Alias-pip** néven.
3. Kattintson az **Erőforrás létrehozása**, a **Hálózatkezelés**, végül a **Virtuális hálózat** elemre.
4. Hozzon létre egy **VNet-Server**, nevű virtuális hálózatot, helyezze el az **RG-DNS-Alias-pip** erőforráscsoportban, és adja az alhálózatnak az **SN-Web** nevet.

## <a name="create-a-web-server-virtual-machine"></a>Webkiszolgálót futtató virtuális gép létrehozása
1. Kattintson az **Erőforrás létrehozása**, majd a **Windows Server 2016 VM** hivatkozásra.
2. Névkén írja be, hogy **Web-01**, és helyezze el a virtuális gépet az **RG-DNS-Alias-TM** erőforráscsoportban. Írjon be egy felhasználónevet, jelszót, majd kattintson **OK** gombra.
3. A **Méret** megadásakor válasszon egy termékváltozatot 8 GB RAM-mal.
4. A **Beállítások** területen válassza a **VNet-Servers** virtuális hálózatot és az **SN-Web** alhálózatot. Nyilvános bejövő portként válassza a **HTTP**, **HTTPS** és **RDP (3389)** lehetőséget, majd kattintson az **OK** gombra.
5. Az összefoglalás lapon kattintson a **Létrehozás** gombra.

   Ez eltarthat néhány percig.

### <a name="install-iis"></a>Az IIS telepítése

Telepítse az IIS-t a **Web-01** hálózatra.

1. Csatlakozzon a **Web-01** hálózathoz, és jelentkezzen be.
2. A Kiszolgálókezelő irányítópulton kattintson a **Szerepkörök és szolgáltatások hozzáadása** lehetőségre.
3. Kattintson háromszor a **Következő** gombra, majd a **Kiszolgálói szerepkörök** lapon válassza a **Webkiszolgáló (IIS)** lehetőséget.
4. Kattintson a **Szolgáltatások hozzáadása**, majd a **Következő** gombra.
5. Kattintson négyszer a **Következő** gombra, majd kattintson a **Telepítés** gombra.

   Ez eltarthat pár percig.
6. A telepítés befejezése után kattintson a **Bezárás** gombra.
7. Nyisson meg egy webböngészőt, nyissa meg a **localhost** címet, és győződjön meg róla, hogy megjelenik-e az IIS alapértelmezett weblapja.

## <a name="create-an-alias-record"></a>Aliasrekord létrehozása

Hozzon létre egy aliasrekordot, amely a nyilvános IP-címre mutat.

1. Kattintson az Azure DNS-zónára a zóna megnyitásához.
2. Kattintson a **Rekordhalmaz** gombra.
3. A **Név** szövegmezőbe írja be, hogy **web01**.
4. Hagyja meg a **Típus** mező értékét **A** rekordként.
5. Kattintson az **Alias rekordhalmaz** jelölőnégyzetre.
6. Kattintson az **Azure szolgáltatás kiválasztása** elemre, és válassza a **Web-01-ip** nyilvános IP-címet.

## <a name="test-the-alias-record"></a>Az aliasrekord tesztelése

1. Az **RG-DNS-Alias-pip** erőforráscsoportban kattintson a **Web-01** virtuális gépre. Jegyezz fel a nyilvános IP-címet.
1. Egy webböngészőben keresse meg a Web01-01 virtuális gép teljes tartománynevét. Például: **web01.contoso.com**. Ekkor az IIS alapértelmezett weblapjának kell megjelennie.
2. Zárja be a webböngészőt.
3. Állítsa le a **Web-01** virtuális gépet, majd indítsa újra.
4. Amikor újraindul, jegyezz fel a virtuális gép új nyilvános IP-címét.
5. Nyisson meg egy webböngészőt, és keresse meg ismét a Web01-01 virtuális gép teljes tartománynevét. Például: **web01.contoso.com**.
6. Ennek továbbra is sikerülnie kell, mivel egy aliasrekordot használt, amely a nyilvános IP-cím-erőforrásra mutat, nem pedig standard A rekordot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az **RG-DNS-Alias-pip** erőforráscsoportot az oktatóanyagban létrehozott összes erőforrás törléséhez.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy aliasrekordot egy nyilvános Azure IP-címre való hivatkozáshoz. Az Azure DNS és a webalkalmazások részletesebb megismeréséhez folytassa a webalkalmazásokról szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
