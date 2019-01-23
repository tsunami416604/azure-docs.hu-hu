---
title: Csatlakozni, konfigurálja és aktiválni az Azure Data Box Gateway az Azure Portalon |} A Microsoft Docs
description: A Data Box-átjáró üzembe helyezése harmadik oktatóanyag arra utasítja, hogy csatlakozzon, és állítsa be, és aktiválja a virtuális eszköz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 887c1d554cd5bd2b935178a77a2de19e687ca3f2
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450404"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway-preview"></a>Oktatóanyag: Csatlakozás, állítsa be, aktiválja az Azure Data Box Gateway (előzetes verzió) 

## <a name="introduction"></a>Bevezetés

Ez az oktatóanyag leírja, hogyan szeretne kapcsolódni, állítsa be, és aktiválja a Data Box átjáróeszköz a helyi webes felhasználói felületen. 

A telepítő-aktiválási folyamat befejezéséhez is igénybe vehet körülbelül 10 percet. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Csatlakozás virtuális eszközhöz
> * Állítsa be, és a virtuális eszköz aktiválása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


> [!IMPORTANT]
> - A Data Box Gateway előzetes verzióban érhető el. A megoldás megrendelése és üzembe helyezése előtt tekintse át az [Azure előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="prerequisites"></a>Előfeltételek

Mielőtt konfigurálja, és állítsa be a Data Box-Gateway, ellenőrizze, hogy:

* Virtuális eszközök kiosztása és csatlakoztatott URL-címe, leírt módon beszerzett a [kiépítése a Hyper-V Data Box Gateway](data-box-gateway-deploy-provision-hyperv.md) vagy [üzembe helyezése VMware-ben Data Box Gateway](data-box-gateway-deploy-provision-vmware.md).
* A Data Box-Gateway szolgáltatás Ön által létrehozott Data Box-Gateway-eszközök kezeléséhez az aktiválási kulcs van. További információért ugorjon [üzembe helyezését az Azure Data Box Gateway](data-box-gateway-deploy-prep.md).

<!--* If this is the second or subsequent virtual device that you are registering with an existing StorSimple Device Manager service, you should have the service data encryption key. This key was generated when the first device was successfully registered with this service. If you have lost this key, see [Get the service data encryption key](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) for your Data Box Gateway.-->

## <a name="connect-to-the-local-web-ui-setup"></a>Csatlakozás a helyi webes felhasználói felület beállítása 

1. Nyisson meg egy böngészőablakot, és csatlakozzon a helyi webes felületén. Típus:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Használja az előző oktatóanyagban feljegyzett kapcsolati URL-cím. Jelzi, hogy nincs-e a webhely biztonsági tanúsítványával kapcsolatos problémára hibaüzenet jelenik meg. Kattintson a **tovább a weblapra a**. (Ezeket a lépéseket eltérő lehet a használt böngésző alapján.)
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

2. Jelentkezzen be a webes felhasználói felületen, a virtuális eszköz. Az alapértelmezett jelszó az *jelszó1*. 
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

3. Módosítani szeretné az eszköz rendszergazdai jelszavát kéri. Írjon be egy új jelszót, 8 és 16 karakter hosszúságú. A jelszónak tartalmaznia kell a 3 az alábbi: nagybetűk, kisbetűk, számjegyeket és speciális karaktereket.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Ekkor a számítógép a **irányítópult** eszköze.

## <a name="set-up-and-activate-the-virtual-device"></a>Állítsa be, és a virtuális eszköz aktiválása
 
1. Az irányítópultról megnyithatja a különböző beállítások konfigurálásához és a Data Box Gateway szolgáltatással a virtuális eszköz regisztrálásához szükséges. A **hálózati beállítások**, **webalkalmazás-proxy beállításai**, és **időbeállítások** megadása nem kötelező. Csak a szükséges beállítások **eszköznév** és **Felhőbeállítások**.
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

2. Az a **eszköznév** lapon, az eszköz rövid nevét konfigurálja. A rövid név 1. és 15 karakter hosszú lehet, és betűvel, számokat és kötőjeleket tartalmazhat.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

3. (Opcionális) konfigurálása a **hálózati beállítások**. Láthatja, hogy legalább 1 hálózati adapter és egyéb, attól függően, hogy hány, konfigurálta a mögöttes virtuális gép. A **hálózati beállítások** oldala a virtuális eszköz az egyik hálózati adapter engedélyezve van az alább látható módon.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Hálózati beállítások konfigurálásakor vegye figyelembe a következőket:

    - Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. Ezért egy IP-cím, alhálózat, átjáró és DNS automatikusan hozzárendeli.
    - Ha a DHCP nem engedélyezett, hozzárendelheti statikus IP-címek, szükség esetén.
    - A hálózati adapter IPv4-t konfigurálhatja.

    >[!NOTE] 
    > Javasoljuk, hogy nem váltson át a helyi IP-címét a hálózati adaptert a statikus DHCP, kivéve, ha az eszköz csatlakozni egy másik IP-címet. Ha egy hálózati adaptert, és váltson a DHCP, akkor is nem lehet megállapítani a DHCP-címet. Ha meg szeretné változtatni a DHCP-címére, várja meg, amíg után az eszköz regisztrálva van a szolgáltatással, és lépjen be. Ezután megtekintheti az összes adapter IP-címekről a **eszköztulajdonságok** a szolgáltatás az Azure Portalon.

4. (Nem kötelező) konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, vegye figyelembe, hogy ha olyan webproxyt használ, csak konfigurálhatja azt itt.
   
   ![](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Az a **webalkalmazás-proxy** oldalon:
   
   1. Adja meg a **webalkalmazás-proxy URL-címe** a következő formátumban: *http://&lt;gazdagép IP-cím vagy teljes tartománynév&gt;: portszám*. Vegye figyelembe, hogy HTTPS URL-címek nem támogatottak.
   2. Adja meg **hitelesítési** , **alapszintű** vagy **None**.
   3. Hitelesítés használata esetén is kell adnia egy **felhasználónév** és **jelszó**.
   4. Kattintson az **Alkalmaz** gombra. Ezzel ellenőrzése és a konfigurált webes proxykiszolgáló beállításait a alkalmazni.

5. (Opcionális) beállításokat az idő az eszköz, például az időzónát és az elsődleges és másodlagos NTP-kiszolgálót. Az NTP-kiszolgálókra szükség, mert az eszköz szinkronizálnia kell az időt, hogy a felhőszolgáltatókkal hitelesítést.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Az a **időbeállítások** oldalon:
    
    1. A legördülő listából válassza ki a **időzóna** , amelyben az eszközre telepített földrajzi helye alapján. Az eszköz az alapértelmezett időzóna PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.
    2. Adjon meg egy **elsődleges NTP-kiszolgáló** az eszközhöz, vagy fogadja el alapértékként a time.windows.com. Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat.
    3. Nem kötelezően megadhatja a **másodlagos NTP-kiszolgáló** az eszközhöz.
    4. Kattintson az **Alkalmaz** gombra. Ezzel ellenőrzése és a beállított idő beállítások alkalmazásához.

6. Az a **Felhőbeállítások** lapon, az eszköz a Data Box Gateway szolgáltatással az Azure Portalon aktiválhatja.
    
    1. Adja meg a **aktiválási kulcs** kapott [aktiválási kulcs lekérése](data-box-gateway-deploy-prep.md#get-the-activation-key) Box adatátjáróhoz.

    2. Kattintson a **aktiválása**. 
       
         ![](./media/data-box-gateway-deploy-connect-setup-activate/image10.png)
    
    3. Előfordulhat, hogy Várjon egy percet, mielőtt az eszköz sikeresen aktiválva van szüksége. Az aktiválás után annak jelzésére, hogy az eszköz aktiválása sikeresen frissül az oldal.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Gatewayjel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Csatlakozás virtuális eszközhöz
> * Állítsa be, és a virtuális eszköz aktiválása


Folytassa a következő oktatóanyaggal, hogyan viheti át az adatokat a Data Box-Gateway.

> [!div class="nextstepaction"]
> [Adatok áthelyezése az adatátjáró Box](./data-box-gateway-deploy-add-shares.md).
