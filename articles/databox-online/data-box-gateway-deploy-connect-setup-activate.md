---
title: Csatlakozni, konfigurálja és aktiválni az Azure Data Box Gateway az Azure Portalon |} A Microsoft Docs
description: A Data Box-átjáró üzembe helyezése harmadik oktatóanyag arra utasítja, hogy csatlakozzon, és állítsa be, és aktiválja a virtuális eszköz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: de2ef4908260a62acf28a270dda6ad738a1760b9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402329"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Oktatóanyag: Csatlakozás, állítsa be, aktiválja az Azure Data Box Gateway

## <a name="introduction"></a>Bevezetés

Ez az oktatóanyag ismerteti csatlakozni, állítsa be, és aktiválja az adatátjáró Box-eszköz helyi webes felhasználói felület használatával. 

A telepítő-aktiválási folyamat befejezéséhez is igénybe vehet körülbelül 10 percet. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Csatlakozás a virtuális eszközhöz
> * Állítsa be, és a virtuális eszköz aktiválása

## <a name="prerequisites"></a>Előfeltételek

Mielőtt konfigurálja, és állítsa be a Data Box-Gateway, ellenőrizze, hogy:

* Sikeresen kiépített egy virtuális eszközt és csatlakoztatott URL-címe, leírt módon beszerzett a [kiépítése a Hyper-V Data Box Gateway](data-box-gateway-deploy-provision-hyperv.md) vagy [üzembe helyezése VMware-ben Data Box Gateway](data-box-gateway-deploy-provision-vmware.md).
* A Data Box-Gateway szolgáltatás Ön által létrehozott Data Box-Gateway-eszközök kezeléséhez az aktiválási kulcs van. További információért ugorjon [üzembe helyezését az Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Csatlakozás a helyi webes felhasználói felület beállítása 

1. Nyisson meg egy böngészőablakot és hozzáférés a helyi webes felhasználói felülete a következő címen:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Használja az előző oktatóanyagban feljegyzett kapcsolati URL-cím. Láthatja, hogy egy hiba vagy figyelmeztetés jelzi, hogy nincs-e a webhely biztonsági tanúsítványával kapcsolatos problémára.

2. Válassza ki **tovább a weblapra a**. Ezeket a lépéseket a használt böngészőtől függően változhat.
   
    ![Webhely biztonsági tanúsítványa hibaüzenet](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Jelentkezzen be a webes felhasználói felületen, a virtuális eszköz. Az alapértelmezett jelszó az *jelszó1*. 
   
    ![Jelentkezzen be a helyi webes felhasználói felületen](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. Amikor a rendszer az eszköz jelszavának módosítása. Az új jelszó 8 – 16 karakterből állhat. A következő 3 tartalmaznia kell: nagybetű, kisbetű, számjegyeket és speciális karaktereket.

    ![Az eszköz jelszavának módosítása](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

A következővel a **irányítópult** eszköze.

## <a name="set-up-and-activate-the-virtual-device"></a>Állítsa be, és a virtuális eszköz aktiválása
 
Az irányítópulton jeleníti meg a különböző beállítások konfigurálásához és a Data Box Gateway szolgáltatással a virtuális eszköz regisztrálásához szükséges. A **eszköznév**, **hálózati beállítások**, **webalkalmazás-proxy beállításai**, és **időbeállítások** megadása nem kötelező. Csak a szükséges beállítások **Felhőbeállítások**.
   
![Local web UI "Dashboard" page](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. A bal oldali ablaktáblán válassza **eszköznév**, majd adja meg az eszköz rövid nevét. A valódi nevet kell tartalmaz 1 – 15 karakter hosszú, és betűvel, számokat és kötőjeleket tartalmazhat.

    ![Helyi webes felhasználói felület "Eszköz neve" lap](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Nem kötelező) A bal oldali ablaktáblán válassza **hálózati beállítások** , majd konfigurálja a beállításokat. A virtuális eszközön látni fogja a legalább egy hálózati adaptert, és hány konfigurálta a mögöttes virtuális gép függően. A **hálózati beállítások** oldala a virtuális eszköz az egyik hálózati adapter engedélyezve van az alább látható módon.
    
    ![Helyi webes felhasználói felület "Hálózati beállítások" lap](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Hálózati beállítások konfigurálását, vegye figyelembe:

    - Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. Az IP-cím, alhálózat, átjáró és DNS automatikusan hozzárendelve.
    - Ha nincs engedélyezve a DHCP, hozzárendelheti statikus IP-címek, szükség esetén.
    - A hálózati adapter IPv4-t konfigurálhatja.

     >[!NOTE] 
     > Javasoljuk, hogy nem váltson át a helyi IP-címét a hálózati adaptert a statikus DHCP, kivéve, ha az eszköz csatlakozni egy másik IP-címet. Ha egy hálózati adaptert, és váltson a DHCP, akkor is nem lehet megállapítani a DHCP-címet. Ha meg szeretné változtatni a DHCP-címére, várja meg, amíg után az eszköz regisztrálva van a szolgáltatással, és lépjen be. Ezután megtekintheti az összes adapter IP-címekről a **eszköztulajdonságok** a szolgáltatás az Azure Portalon.

3. (Nem kötelező) konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, ha olyan webproxyt használ csak ezen a lapon konfigurálhatja.
   
   ![Helyi webes felhasználói felület "Webproxy beállításai" lap](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Az a **webalkalmazás-proxy** lapon, tegye a következőket:
   
   1. Az a **webalkalmazás-proxy URL-címe** mezőbe írja be az URL-cím a következő formátumban: `http://&lt;host-IP address or FQDN&gt;:Port number`. HTTPS URL-címek nem támogatottak.
   2. A **hitelesítési**válassza **nincs** vagy **NTLM**.
   3. Ha használ hitelesítést, írja be a **felhasználónév** és **jelszó**.
   4. Érvényesítéséhez és a alkalmazni a konfigurált webes proxykiszolgáló beállításait, válassza ki a **alkalmaz**.

4. (Nem kötelező) A bal oldali panelen válassza ki a **időbeállítások**, majd konfigurálja az időzónát és az elsődleges és másodlagos NTP-kiszolgálóhoz az eszközhöz. 

    Az NTP-kiszolgálókra szükség, mert az eszköz szinkronizálnia kell az időt, hogy a felhőszolgáltatókkal hitelesítést.
    
    ![Helyi webes felhasználói felület "Idő beállításai" lap](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Az a **időbeállítások** lapon, tegye a következőket:
    
    1. Az a **időzóna** legördülő listára, válassza ki az időzónát, amely megfelel a földrajzi helyet, amely az eszköz üzembe lesz helyezve.
        Az eszköz az alapértelmezett időzóna PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.

    2. Adjon meg egy **elsődleges NTP-kiszolgáló** az eszközhöz, vagy fogadja el alapértékként a `time.windows.com`.   
        Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat.

    3. Szükség esetén a a **másodlagos NTP-kiszolgáló** adja meg az eszköz egy másodlagos kiszolgálón.

    4. Érvényesítéséhez és a konfigurált beállításokat alkalmazni, válassza ki a **alkalmaz**.

6. A bal oldali panelen válassza ki a **Felhőbeállítások**, és aktiválja az eszköz a Data Box Gateway szolgáltatással az Azure Portalon.
    
    1. Az a **aktiválási kulcs** mezőbe írja be a **aktiválási kulcs** kapott [aktiválási kulcs lekérése](data-box-gateway-deploy-prep.md#get-the-activation-key) Box adatátjáróhoz.

    2. Válassza ki **aktiválása**.
       
         ![Local web UI "Cloud settings" page](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Az eszköz akkor aktiválódik, és kritikus frissítések, ha érhető el, a rendszer automatikusan alkalmazza. Megjelenik egy értesítés erre a célra. Figyelheti a frissítési folyamat állapotát az Azure Portalon keresztül.

        ![Local web UI "Cloud settings" page](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **A párbeszédpanelen is tartalmaz a helyreállítási kulcs, amely másolja és mentse egy biztonságos helyre. Ezt a kulcsot használja az adatok helyreállításához, abban az esetben, ha az eszköz nem indul el.**


    4. Szükség lehet Várjon néhány percet a frissítés sikeres végrehajtásához. A frissítés után a rendszer végrehajtásához, jelentkezzen be az eszközt. A **Felhőbeállítások** lap frissül, jelezve, hogy az eszköz sikeresen aktiválva van.

        ![Local web UI "Cloud settings" page updated](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Az eszköz beállítása befejeződik. Most már hozzáadhat megosztások az eszközön.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Csatlakozás a virtuális eszközhöz
> * Állítsa be, és a virtuális eszköz aktiválása

A Data Box Gateway adatátvitel kezelésével kapcsolatos információkért lásd:

> [!div class="nextstepaction"]
> [Adatok áthelyezése az adatátjáró Box](./data-box-gateway-deploy-add-shares.md).
