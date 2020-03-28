---
title: Oktatóanyag az Azure Data Box Edge-eszközhöz való csatlakozáshoz, konfigurálásához, aktiválásához az Azure Portalon | Microsoft dokumentumok
description: A Data Box Edge üzembe helyezéséhez szükséges oktatóanyag arra utasítja, hogy csatlakozzon, állítsa be és aktiválja a fizikai eszközt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 144c59c8bc24e8e10584702ec6cd48f7aa8c15c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239055"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge"></a>Oktatóanyag: Csatlakozás, beállítás és aktiválás az Azure Data Box Edge 

Ez az oktatóanyag ismerteti, hogyan csatlakozhat, beállítható és aktiválhatja az Azure Data Box Edge-eszközt a helyi webes felhasználói felület használatával.

A beállítási és aktiválási folyamat körülbelül 20 percet is igénybe vehet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Csatlakozás fizikai eszközhöz
> * A fizikai eszköz beállítása és aktiválása

## <a name="prerequisites"></a>Előfeltételek

A Data Box Edge eszköz konfigurálása és beállítása előtt győződjön meg arról, hogy:

* A fizikai eszközt a Data Box Edge telepítése című témakörben részletesen [telepítette.](data-box-edge-deploy-install.md)
* Rendelkezik az aktiválási kulcs a Data Box Edge szolgáltatás, amely a Data Box Edge eszköz kezeléséhez létrehozott. További információért nyissa meg [az Előkészítés az Azure Data Box Edge üzembe helyezéséhez](data-box-edge-deploy-prep.md)című.

## <a name="connect-to-the-local-web-ui-setup"></a>Csatlakozás a helyi webes felhasználói felület beállításához 

1. Konfigurálja úgy a számítógépen lévő Ethernet-adaptert, hogy a Data Box Edge eszközhöz csatlakozzon a 192.168.100.5 statikus IP-címmel és a 255.255.255.0 alhálózattal.

2. Csatlakoztassa a számítógépet az eszköz 1-es portjához. Az alábbi ábrán azonosíthatja az 1.port 1-et az eszközön.

    ![Egy bekábelezett eszköz hátlapja](./media/data-box-edge-deploy-install/backplane-cabled.png)


3. Nyisson meg egy böngészőablakot, és nyissa `https://192.168.100.10`meg az eszköz helyi webes felhasználói felületét a helyen:  
    Ez a művelet eltarthat néhány percig az eszköz bekapcsolása után. 

    Hibaüzenet vagy figyelmeztetés jelenik meg, amely azt jelzi, hogy probléma van a webhely biztonsági tanúsítványával. 
   
    ![A webhely biztonsági tanúsítványának hibaüzenete](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

4. Válassza **a Tovább erre a weblapra**lehetőséget.  
    Ezek a lépések a használt böngészőtől függően eltérőek lehetnek.

5. Jelentkezzen be az eszköz webes felhasználói felületére. Az alapértelmezett jelszó a *Password1*. 
   
    ![A Data Box Edge eszköz bejelentkezési lapja](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

6. A kérdésben módosítsa az eszköz rendszergazdai jelszavát.  
    Az új jelszónak 8 és 16 karakter közötti karaktert kell tartalmaznia. A következő karakterek közül háromnak kell lennie: nagybetűs, kisbetűs, numerikus és speciális karakterek.

Most már az eszköz műszerfalán van.

## <a name="set-up-and-activate-the-physical-device"></a>A fizikai eszköz beállítása és aktiválása
 
Az irányítópult megjeleníti a fizikai eszköz konfigurálásához és regisztrálásához szükséges különböző beállításokat a Data Box Edge szolgáltatással. Az **Eszköz név**, a Hálózati **beállítások**, **a Webproxy-beállítások**és **az Idő beállítások** megadása nem kötelező. Az egyetlen szükséges beállítás a **Felhőbeállítások**.
   
![Helyi webes felhasználói felület "Irányítópult" lap](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. A bal oldali ablaktáblában válassza az **Eszköz név**lehetőséget, majd adja meg az eszköz rövid nevét.  
    A rövid névnek 1 és 15 karakter között kell lennie, és betűket, számokat és kötőjeleket tartalmazhat.

    ![Helyi webes felhasználói felület "Eszköznév" lapja](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Nem kötelező) A bal oldali ablaktáblában válassza a **Hálózati beállítások** lehetőséget, majd adja meg a beállításokat.  
    A fizikai eszközön hat hálózati kapcsolat van. Port 1 és PORT 2 1-Gbps hálózati adapterek. A 3-as, A PORT 4-es, az 5-ös és a 6-os port mind 25 Gbit/s sebességű hálózati csatoló, amely 10 Gbit/s-os hálózati interfészként is használható. Az 1-es port automatikusan csak felügyeleti portként van konfigurálva, és a 2-es PORT 6-hoz való port az összes adatport. A **Hálózati beállítások** lap az alábbi módon látható.
    
    ![Helyi webes felhasználói felület "Hálózati beállítások" lap](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    A hálózati beállítások konfigurálásakor tartsa szem előtt a következőket:

   - Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. A rendszer automatikusan hozzárendel egy IP-címet, alhálózatot, átjárót és DNS-t.
   - Ha a DHCP nincs engedélyezve, szükség esetén statikus IP-címeket rendelhet hozzá.
   - A hálózati adapter iPv4-ként konfigurálható.

     >[!NOTE] 
     > Azt javasoljuk, hogy ne váltson statikusról DCHP-re a hálózati adapter helyi IP-címét, hacsak nincs másik IP-cím az eszközhöz való csatlakozáshoz. Ha egy hálózati adaptert használ, és DHCP-re vált, nem lehet meghatározni a DHCP-címet. Ha DHCP-címre szeretne váltani, várjon, amíg az eszköz regisztrál a szolgáltatásban, majd módosítsa. Ezután megtekintheti az ip-k az összes adapterek az **eszköz tulajdonságai** az Azure Portalon a szolgáltatás.

3. (Nem kötelező) A bal oldali ablaktáblában válassza a **Webproxy beállításai**lehetőséget, majd konfigurálja a webproxy-kiszolgálót. Bár a webproxy-konfiguráció nem kötelező, ha webproxyt használ, azt csak ezen az oldalon állíthatja be.
   
   ![Helyi webes felhasználói felület "Webproxy-beállítások" lap](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   A **webproxy beállításai** lapon tegye a következőket:
   
   a. A **webproxy URL-címe** mezőbe írja be `http://host-IP address or FQDN:Port number`az URL-címet a következő formátumban: . A HTTPS URL-címek nem támogatottak.

   b. A **Hitelesítés csoportban**válassza a **Nincs** vagy **az NTLM**lehetőséget.

   c. Ha hitelesítést használ, adjon meg egy felhasználónevet és egy jelszót.

   d. A konfigurált webproxy-beállítások érvényesítéséhez és alkalmazásához válassza **a Beállítások alkalmazása**lehetőséget.
   
   > [!NOTE]
   > A proxy-auto konfigurációs (PAC) fájlok nem támogatottak. A PAC-fájl határozza meg, hogy a webböngészők és más felhasználói ügynökök hogyan választhatják ki automatikusan a megfelelő proxykiszolgálót (hozzáférési módot) egy adott URL lehívásához.
   > Legközelebbi amit megpróbál -hoz elfogó és olvas minden a forgalom ( akkor újra jel minden -val -uk saját igazolás) van nem összeegyeztethető óta a helyettes' cert van nem bízott.
   > Az Azure Data Box Edge általában átlátszó proxyk jól működik.

4. (Nem kötelező) A bal oldali ablaktáblában válassza az **Időbeállítások**lehetőséget, majd konfigurálja az eszköz időzónáját, valamint elsődleges és másodlagos NTP-kiszolgálóit.  
    NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt, hogy hitelesíthesse magát a felhőszolgáltatókkal.
       
    Az **Időbeállítások** lapon tegye a következőket:
    
    1. Az **Időzóna** legördülő listában válassza ki azt az időzónát, amely megfelel annak a földrajzi helynek, ahol az eszközt üzembe helyezik.
        A készülék alapértelmezett időzónája a PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.

    2. Az **Elsődleges NTP-kiszolgáló** mezőben adja meg az eszköz elsődleges kiszolgálóját, vagy fogadja el az alapértelmezett time.windows.com értéket.  
        Győződjön meg arról, hogy a hálózat lehetővé teszi, hogy az NTP-forgalom az adatközpontból az internetre továbbítsa.

    3. A **Másodlagos NTP-kiszolgáló** mezőben adja meg az eszköz másodlagos kiszolgálóját.

    4. A beállított időbeállítások érvényesítéséhez és alkalmazásához válassza a **Beállítások alkalmazása**lehetőséget.

        ![Helyi webes felhasználói felület "Időbeállítások" oldal](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Nem kötelező) A bal oldali ablaktáblában válassza a **Tárolási beállítások lehetőséget** az eszköz tárolási rugalmasságának konfigurálásához. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Alapértelmezés szerint az eszközön lévő tároló nem rugalmas, és adatvesztés áll fenn, ha egy adatlemez meghibásodik az eszközön. Ha engedélyezi a Rugalmas beállítást, az eszköz tárolója újrakonfigurálható, és az eszköz képes ellenállni egy adatlemez adatvesztés nélküli meghibásodásának. A tároló rugalmasként való konfigurálása csökkenti az eszköz használható kapacitását.

    > [!IMPORTANT] 
    > A rugalmasság csak az eszköz aktiválása előtt konfigurálható. 

    ![Helyi webes felhasználói felület "Tárhelybeállítások" lap](./media/data-box-edge-deploy-connect-setup-activate/storage-settings.png)

6. A bal oldali ablaktáblában válassza a **Felhőbeállítások**lehetőséget, majd aktiválja az eszközt az Azure PortalData Box Edge szolgáltatásával.
    
    1. Az **Aktiválás kulcs** mezőbe írja be a Data Box Edge [aktiválási kulcsának bejutása](data-box-edge-deploy-prep.md#get-the-activation-key) című kulcsbejutási kulcsot.
    2. Kattintson az **Alkalmaz** gombra.
       
        ![Helyi webes felhasználói felület "Felhőbeállítások" oldal](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Először az eszköz aktiválódik. A rendszer ezután ellenőrzi az eszköz minden fontos frissítést, és ha rendelkezésre áll, a frissítések automatikusan érvénybe lépnek. Ekkor megjelenik egy értesítés erről.

        A párbeszédablakhoz van egy helyreállítási kulcs is, amelyet biztonságos helyre kell másolnia és mentenie kell. Ez a kulcs az adatok helyreállítására szolgál abban az esetben, ha az eszköz nem tudja elindítani.

        ![A helyi webes felhasználói felület "Felhőbeállítások" oldala frissítve](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Előfordulhat, hogy a frissítés sikeres befejezése után néhány percet kell várnia. A lap frissül, jelezve, hogy az eszköz sikeresen aktiválva van.

        ![A helyi webes felhasználói felület "Felhőbeállítások" oldala frissítve](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Az eszköz beállítása befejeződött. Most már hozzáadhat megosztásokat az eszközén.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Csatlakozás fizikai eszközhöz
> * A fizikai eszköz beállítása és aktiválása

Az adatok Adatdoboz-peremeszközzel történő adatátvitelről a következő témakörben olvashat:

> [!div class="nextstepaction"]
> [Adatátvitel a Data Box Edge segítségével.](./data-box-edge-deploy-add-shares.md)
