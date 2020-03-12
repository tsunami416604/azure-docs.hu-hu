---
title: 'Oktatóanyag: Azure Data Box Edge eszköz csatlakoztatása, konfigurálása, aktiválása Azure Portalban | Microsoft Docs'
description: Az Data Box Edge üzembe helyezésére vonatkozó oktatóanyag arra utasítja a fizikai eszköz csatlakoztatását, beállítását és aktiválását.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 144c59c8bc24e8e10584702ec6cd48f7aa8c15c1
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128689"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge"></a>Oktatóanyag: a Azure Data Box Edge összekapcsolása, beállítása és aktiválása 

Ez az oktatóanyag azt ismerteti, hogyan lehet csatlakozni, beállítani és aktiválni a Azure Data Box Edge eszközt a helyi webes KEZELŐFELÜLET használatával.

A telepítés és az aktiválás folyamata körülbelül 20 percet vesz igénybe.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Kapcsolódás fizikai eszközhöz
> * A fizikai eszköz beállítása és aktiválása

## <a name="prerequisites"></a>Előfeltételek

A Data Box Edge eszköz konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* Telepítette a fizikai eszközt a [Data Box Edge telepítésének](data-box-edge-deploy-install.md)részletesen.
* A Data Box Edge eszköz kezeléséhez a létrehozott Data Box Edge szolgáltatásból aktiválhatja az aktiválási kulcsot. További információ: [felkészülés a Azure Data Box Edge üzembe helyezésére](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Kapcsolódás a helyi webes felhasználói felület telepítéséhez 

1. Konfigurálja úgy az Ethernet-adaptert a számítógépén, hogy a 192.168.100.5 és az alhálózat 255.255.255.0 statikus IP-címmel kapcsolódjon a Data Box Edge eszközhöz.

2. A számítógép csatlakoztatása az 1. PORThoz az eszközön. Az alábbi ábra segítségével azonosíthatja az eszközön az 1. PORTOT.

    ![Egy bekábelezett eszköz hátlapja](./media/data-box-edge-deploy-install/backplane-cabled.png)


3. Nyisson meg egy böngészőablakot, és nyissa meg az eszköz helyi webes FELÜLETét `https://192.168.100.10`címen.  
    Ez a művelet néhány percet is igénybe vehet, miután bekapcsolta az eszközt. 

    Hibaüzenet vagy figyelmeztetés jelenik meg, amely jelzi, hogy probléma van a webhely biztonsági tanúsítványával. 
   
    ![A webhely biztonsági tanúsítványának hibaüzenete](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

4. Válassza **a folytatás ehhez a weboldalhoz**lehetőséget.  
    Ezek a lépések a használt böngészőtől függően eltérőek lehetnek.

5. Jelentkezzen be az eszköz webes felhasználói felületére. Az alapértelmezett jelszó a *jelszó1*. 
   
    ![Data Box Edge eszköz bejelentkezési lapja](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

6. A parancssorban módosítsa az eszköz rendszergazdai jelszavát.  
    Az új jelszónak 8 és 16 karakter közöttinek kell lennie. A következő karakterek közül hármat tartalmaznia kell: nagybetűk, kisbetűk, számok és speciális karakterek.

Most már az eszköz irányítópultján van.

## <a name="set-up-and-activate-the-physical-device"></a>A fizikai eszköz beállítása és aktiválása
 
Az irányítópulton a fizikai eszköz a Data Box Edge szolgáltatással való konfigurálásához és regisztrálásához szükséges különböző beállítások láthatók. Az **eszköz neve**, a **hálózati beállítások**, a **webproxy beállításai**és az **időbeállítások** megadása nem kötelező. Az egyetlen szükséges beállítás a **felhő beállításai**.
   
![Helyi webes felhasználói felület "irányítópult" lapja](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. A bal oldali panelen válassza az **eszköznév**lehetőséget, majd adjon meg egy rövid nevet az eszköznek.  
    A felhasználóbarát névnek 1 és 15 karakter közöttinek kell lennie, és betűket, számokat és kötőjeleket kell tartalmaznia.

    ![Helyi webes felhasználói felület "eszköznév" lapja](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. Választható A bal oldali ablaktáblán válassza a **hálózati beállítások** lehetőséget, majd konfigurálja a beállításokat.  
    A fizikai eszközön hat hálózati adapter található. Az 1. és a 2. port 1 GB/s hálózati adapter. A 3. port, a 4-es port, az 5. port és a 6-os PORT mind a 25 GB-os hálózati adapterek, amelyek 10 GB/s hálózati adapterként is használhatók. Az 1. PORT automatikusan csak felügyeleti portként van konfigurálva, a 2-es port pedig minden adatport. A **hálózati beállítások** lap az alábbi ábrán látható.
    
    ![Helyi webes felhasználói felület "hálózati beállítások" lapja](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    A hálózati beállítások konfigurálásakor vegye figyelembe a következőket:

   - Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. A rendszer automatikusan hozzárendeli az IP-címet, az alhálózatot, az átjárót és a DNS-t.
   - Ha a DHCP nincs engedélyezve, szükség esetén statikus IP-címeket is hozzárendelhet.
   - A hálózati adaptert IPv4-ként is konfigurálhatja.

     >[!NOTE] 
     > Azt javasoljuk, hogy ne váltson át a hálózati adapter helyi IP-címére statikusról DHCP-re, hacsak nem rendelkezik másik IP-címmel az eszközhöz való csatlakozáshoz. Ha egy hálózati adaptert használ, és a DHCP-re vált, a DHCP-címeket nem lehet meghatározni. Ha egy DHCP-címről szeretne váltani, várjon, amíg az eszköz regisztrálva van a szolgáltatásban, majd módosítsa a következőt:. Ezután megtekintheti az összes adapter IP-címeit az **eszköz tulajdonságai** között a szolgáltatás Azure Portal.

3. Választható A bal oldali ablaktáblán válassza a **webproxy beállításai**lehetőséget, majd konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, ha webproxyt használ, csak ezen a lapon konfigurálhatja.
   
   ![Helyi webes felhasználói felület "Webproxy-beállítások" lapja](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   A **webproxy beállításai** lapon tegye a következőket:
   
   a. A **webproxy URL-címe** mezőbe írja be az URL-címet a következő formátumban: `http://host-IP address or FQDN:Port number`. A HTTPS URL-címek nem támogatottak.

   b. A **hitelesítés**területen válassza a **nincs** vagy az **NTLM**lehetőséget.

   c. Ha hitelesítést használ, adjon meg egy felhasználónevet és egy jelszót.

   d. A konfigurált Webproxy-beállítások érvényesítéséhez és alkalmazásához válassza a **Beállítások alkalmazása**lehetőséget.
   
   > [!NOTE]
   > A proxy-Auto config (PAC) fájlok nem támogatottak. A PAC-fájlok azt határozzák meg, hogy a böngészők és más felhasználói ügynökök hogyan tudják automatikusan kiválasztani a megfelelő proxykiszolgálót (hozzáférési módszer) egy adott URL beolvasásához.
   > Azok a proxyk, amelyek megpróbálják feltartóztatni és beolvasni az összes forgalmat (majd újra aláírni a saját tanúsítvánnyal), nem kompatibilisek, mert a proxy tanúsítványa nem megbízható.
   > Általában az átlátszó proxyk jól működnek Azure Data Box Edge.

4. Választható A bal oldali panelen válassza az **időbeállítások**lehetőséget, majd konfigurálja az adott eszközhöz tartozó időzónát és az elsődleges és másodlagos NTP-kiszolgálókat.  
    Az NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt ahhoz, hogy hitelesíteni lehessen a felhőalapú szolgáltatóktól.
       
    Az **idő beállításai** lapon tegye a következőket:
    
    1. Az **időzóna** legördülő listában válassza ki azt az időzónát, amely megfelel az eszköz üzembe helyezésének földrajzi helyének.
        Az eszköz alapértelmezett időzónája a PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.

    2. Az **Elsődleges NTP-kiszolgáló** mezőbe írja be az eszköz elsődleges kiszolgálóját, vagy fogadja el a Time.Windows.com alapértelmezett értékét.  
        Győződjön meg arról, hogy a hálózat lehetővé teszi, hogy az NTP-forgalom áthaladjon az adatközpontból az internetre.

    3. Szükség esetén a **másodlagos NTP-kiszolgáló** mezőbe írja be az eszköz másodlagos kiszolgálóját.

    4. A beállított idő beállításainak ellenőrzéséhez és alkalmazásához válassza a **Beállítások alkalmazása**lehetőséget.

        ![Helyi webes felhasználói felület "Time Settings" lapja](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. Választható A bal oldali ablaktáblán válassza a **tárolási beállítások** lehetőséget a tárterület rugalmasságának konfigurálásához az eszközön. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Alapértelmezés szerint az eszközön lévő tárterület nem rugalmas, és adatvesztés történik, ha egy adatlemez meghibásodik az eszközön. Ha engedélyezi a rugalmas beállítást, a rendszer újrakonfigurálja az eszközön lévő tárolót, és az eszköz nem tudja elviselni az adatvesztés nélkül egy adatlemez meghibásodását. A tárterület rugalmasként való konfigurálása csökkenti az eszköz felhasználható kapacitását.

    > [!IMPORTANT] 
    > A rugalmasságot csak az eszköz aktiválása előtt lehet konfigurálni. 

    ![Helyi webes felhasználói felület "tárolási beállítások" lapja](./media/data-box-edge-deploy-connect-setup-activate/storage-settings.png)

6. A bal oldali ablaktáblán válassza a **felhő beállításai**lehetőséget, majd aktiválja az eszközt a Azure Portal Data Box Edge szolgáltatásával.
    
    1. Az **aktiválási kulcs** mezőben adja meg a Data Box Edge [aktiválási kulcsának lekérése](data-box-edge-deploy-prep.md#get-the-activation-key) aktiválási kulcsát.
    2. Kattintson az **Alkalmaz** gombra.
       
        ![Helyi webes felhasználói felület "Cloud Settings" (Felhőbeli beállítások) lapja](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Először az eszköz aktiválva van. Az eszköz ekkor megkeresi a kritikus frissítéseket, és ha elérhető, a rendszer automatikusan alkalmazza a frissítéseket. Ekkor megjelenik egy értesítés erre a hatásra.

        A párbeszédpanelen egy helyreállítási kulcs is található, amelyet egy biztonságos helyen kell átmásolnia és menteni. Ez a kulcs az adatok helyreállításához használható abban az esetben, ha az eszköz nem tud elindulni.

        ![A helyi webes felhasználói felület "Cloud Settings" (Felhőbeli beállítások) lapja frissítve](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Előfordulhat, hogy a frissítés sikeres befejeződése után néhány percet várnia kell. Az oldal frissül, és jelzi, hogy az eszköz aktiválása sikeres volt.

        ![A helyi webes felhasználói felület "Cloud Settings" (Felhőbeli beállítások) lapja frissítve](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Az eszköz telepítése befejeződött. Mostantól megosztásokat is hozzáadhat az eszközhöz.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Kapcsolódás fizikai eszközhöz
> * A fizikai eszköz beállítása és aktiválása

Az adatok Data Box Edge eszközzel történő átviteléről a következő témakörben tájékozódhat:

> [!div class="nextstepaction"]
> [Adatok átvitele Data Box Edgesal](./data-box-edge-deploy-add-shares.md).
