---
title: Csatlakozás az Azure Data Box Gateway-hez az Azure Portalon
description: A Data Box Gateway központi telepítésére vonatkozó harmadik oktatóanyag arra utasítja, hogy csatlakozzon, állítsa be és aktiválja a virtuális eszközt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: b3cf4fd958202c28586b7c15932dc88a21d7c60f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686865"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Oktatóanyag: Csatlakozás, beállítás, az Azure Data Box-átjáró aktiválása

## <a name="introduction"></a>Introduction (Bevezetés)

Ez az oktatóanyag bemutatja, hogyan csatlakozhat, állíthat be és aktiválhat a Data Box Gateway eszközhöz a helyi webes felhasználói felület használatával. 

A beállítási és aktiválási folyamat körülbelül 10 percet is igénybe vehet. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Csatlakozás virtuális eszközhöz
> * A virtuális eszköz beállítása és aktiválása

## <a name="prerequisites"></a>Előfeltételek

A Data Box Gateway konfigurálása és beállítása előtt győződjön meg arról, hogy:

* Kiépített egy virtuális eszközt, és megszerezte a csatlakoztatott URL-címet, ahogy azt a Data Box Gateway kiépítése a [Hyper-V-ben](data-box-gateway-deploy-provision-hyperv.md) vagy [a Data Box Gateway kiépítése a VMware-ben](data-box-gateway-deploy-provision-vmware.md)részletezi.
* Rendelkezik a Data Box Gateway szolgáltatás aktiválási kulcsa, amelyet a Data Box Gateway eszközök kezeléséhez hozott létre. További információ: Felkészülés az [Azure Data Box Gateway üzembe helyezésére](data-box-gateway-deploy-prep.md)című területen.


## <a name="connect-to-the-local-web-ui-setup"></a>Csatlakozás a helyi webes felhasználói felület beállításához 

1. Nyisson meg egy böngészőablakot, és nyissa meg az eszköz helyi webes felhasználói felületét a következő helyen:
   
   https:\//ip-address-of-network-interface
   
   Használja az előző oktatóanyagban említett kapcsolat URL-címét. Hibaüzenet vagy figyelmeztetés jelenik meg, amely azt jelzi, hogy probléma van a webhely biztonsági tanúsítványával.

2. Válassza **a Tovább erre a weblapra**lehetőséget. Ezek a lépések a használt böngészőtől függően eltérőek lehetnek.
   
    ![A webhely biztonsági tanúsítványának hibaüzenete](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Jelentkezzen be a virtuális eszköz webes felhasználói felületére. Az alapértelmezett jelszó a *Password1*. 
   
    ![Bejelentkezés a helyi webes felhasználói felületre](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. A parancssorban módosítsa az eszköz jelszavát. Az új jelszónak 8 és 16 karakter közötti karaktert kell tartalmaznia. A következők közül 3-at kell tartalmaznia: nagybetűs, kisbetűs, numerikus és speciális karakterek.

    ![Az eszköz jelszavának módosítása](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Most az eszköz **irányítópultján** van.

## <a name="set-up-and-activate-the-virtual-device"></a>A virtuális eszköz beállítása és aktiválása
 
Az irányítópult megjeleníti a virtuális eszköz konfigurálásához és regisztrálásához szükséges különböző beállításokat a Data Box Gateway szolgáltatással. Az **Eszköz név**, a Hálózati **beállítások**, **a Webproxy-beállítások**és **az Idő beállítások** megadása nem kötelező. Az egyetlen szükséges beállítás a **Felhőbeállítások**.
   
![Helyi webes felhasználói felület "Irányítópult" lap](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. A bal oldali ablaktáblában válassza az **Eszköz neve**lehetőséget, majd adja meg az eszköz rövid nevét. A rövid névnek 1 és 15 karakter között kell lennie, és betűvel, számokkal és kötőjelekkel kell rendelkeznie.

    ![Helyi webes felhasználói felület "Eszköznév" lapja](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Nem kötelező) A bal oldali ablaktáblában válassza a **Hálózati beállítások** lehetőséget, majd adja meg a beállításokat. A virtuális eszközön legalább egy hálózati adapter tésszel rendelkezik, attól függően, hogy hányat konfigurált az alapul szolgáló virtuális gépen. Az egy hálózati csatolmányral rendelkező virtuális eszköz **Hálózati beállítások** lapja az alábbi módon látható.
    
    ![Helyi webes felhasználói felület "Hálózati beállítások" lap](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    A hálózati beállítások konfigurálásakor tartsa szem előtt a következőket:

    - Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. A rendszer automatikusan hozzárendel egy IP-címet, alhálózatot, átjárót és DNS-t.
    - Ha a DHCP nincs engedélyezve, szükség esetén statikus IP-címeket rendelhet hozzá.
    - A hálózati adapter iPv4-ként konfigurálható.

     >[!NOTE] 
     > Azt javasoljuk, hogy ne váltson statikusról DHCP-re a hálózati adapter helyi IP-címe között, hacsak nincs másik IP-cím az eszközhöz való csatlakozáshoz. Ha egy hálózati adaptert használ, és DHCP-re vált, nem lehet meghatározni a DHCP-címet. Ha DHCP-címre szeretne váltani, várjon, amíg az eszköz regisztrál a szolgáltatásban, majd módosítsa. Ezután megtekintheti az ip-k az összes adapterek az **eszköz tulajdonságai** az Azure Portalon a szolgáltatás.

3. (Nem kötelező) konfigurálja a webproxy-kiszolgálót. Bár a webproxy-konfiguráció nem kötelező, ha webproxyt használ, azt csak ezen az oldalon állíthatja be.
   
   ![Helyi webes felhasználói felület "Webproxy-beállítások" lap](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   A **webproxy** lapon tegye a következőket:
   
   1. A **webproxy URL-címe** mezőbe írja be `http://&lt;host-IP address or FQDN&gt;:Port number`az URL-címet a következő formátumban: . A HTTPS URL-címek nem támogatottak.
   2. A **Hitelesítés csoportban**válassza a **Nincs** vagy **az NTLM**lehetőséget.
   3. Hitelesítés használata esetén adjon meg **egy felhasználónevet** és **egy jelszót.**
   4. A konfigurált webproxy-beállítások érvényesítéséhez és alkalmazásához válassza az **Alkalmaz**lehetőséget.

   > [!NOTE]
   > A proxy-auto konfigurációs (PAC) fájlok nem támogatottak. A PAC-fájl határozza meg, hogy a webböngészők és más felhasználói ügynökök hogyan választhatják ki automatikusan a megfelelő proxykiszolgálót (hozzáférési módot) egy adott URL lehívásához.
   > Legközelebbi amit megpróbál -hoz elfogó és olvas minden a forgalom ( akkor újra jel minden -val -uk saját igazolás) van nem összeegyeztethető óta a helyettes' cert van nem bízott.
   > Az Azure Data Box Gateway általában átlátszó proxyk jól működnek.

4. (Nem kötelező) A bal oldali ablaktáblában válassza az **Időbeállítások**lehetőséget, majd konfigurálja az eszköz időzónáját, valamint elsődleges és másodlagos NTP-kiszolgálóit. 

    NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt, hogy hitelesíthesse magát a felhőszolgáltatókkal.
    
    ![Helyi webes felhasználói felület "Időbeállítások" oldal](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Az **Időbeállítások** lapon tegye a következőket:
    
    1. Az **Időzóna** legördülő listában válassza ki azt az időzónát, amely megfelel az eszköz üzembe helyezésének földrajzi helyének.
        A készülék alapértelmezett időzónája a PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.

    2. Adja meg **az eszköz elsődleges NTP-kiszolgálóját,** vagy fogadja el a alapértelmezett `time.windows.com`értékét.   
        Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat.

    3. A **Másodlagos NTP-kiszolgáló** mezőben adja meg az eszköz másodlagos kiszolgálóját.

    4. A beállított időbeállítások érvényesítéséhez és alkalmazásához válassza az **Alkalmaz**lehetőséget.

6. A bal oldali ablaktáblában válassza a **Felhőbeállítások**lehetőséget, majd aktiválja az eszközt az Azure Portal Data Box Gateway szolgáltatásával.
    
    1. Az **Aktiválás kulcs** mezőbe írja be **a** Data Box Gateway [aktiválási kulcsának bejutása](data-box-gateway-deploy-prep.md#get-the-activation-key) című kulcsot.

    2. Válassza **az Aktiválás**lehetőséget.
       
         ![Helyi webes felhasználói felület "Felhőbeállítások" oldal](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Az eszköz aktiválódik, és a kritikus frissítések, ha rendelkezésre állnak, automatikusan érvénybe lépnek. Ekkor megjelenik egy értesítés erről. A frissítés ima az Azure Portalon keresztül figyelheti.

        ![Helyi webes felhasználói felület "Felhőbeállítások" oldal](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **A párbeszédablakhoz van egy helyreállítási kulcs is, amelyet biztonságos helyre kell másolnia és mentenie. Ez a kulcs az adatok helyreállítására szolgál abban az esetben, ha az eszköz nem tudja elindítani.**


    4. Előfordulhat, hogy néhány percet kell várnia a frissítés sikeres befejezésére. A frissítés befejezése után jelentkezzen be az eszközre. A **Felhőbeállítások** lap frissül, jelezve, hogy az eszköz sikeresen aktiválva van.

        ![A helyi webes felhasználói felület "Felhőbeállítások" oldala frissítve](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Az eszköz beállítása befejeződött. Most már hozzáadhat megosztásokat az eszközén.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Csatlakozás virtuális eszközhöz
> * A virtuális eszköz beállítása és aktiválása

Az adatdoboz-átjáróval történő adatátvitelről az:

> [!div class="nextstepaction"]
> [Adatátvitel a Data Box Gateway segítségével.](./data-box-gateway-deploy-add-shares.md)
