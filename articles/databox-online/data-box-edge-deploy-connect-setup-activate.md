---
title: Csatlakozni, konfigurálja és aktiválni az Azure Data Box Edge-eszköz, az Azure Portalon |} A Microsoft Docs
description: A Data Box Edge üzembe helyezése harmadik oktatóanyag arra utasítja, hogy csatlakozzon, és állítsa be, és a fizikai eszköz aktiválása.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: ad288067fdd40dbfa84352916e84fa1e3ab9af8e
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629136"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge"></a>Oktatóanyag: Csatlakozás, beállítása és aktiválni az Azure Data Box Edge 

Ez az oktatóanyag leírja, hogyan csatlakozhat, állítsa be, és aktiválja az Azure Data Box Edge-eszköz helyi webes felhasználói felület használatával.

A telepítő-aktiválási folyamat befejezéséhez is igénybe vehet körülbelül 20 percet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Csatlakozás egy fizikai eszköz
> * Állítsa be, és a fizikai eszköz aktiválása

## <a name="prerequisites"></a>Előfeltételek

Mielőtt konfigurálja, és állítsa be a Data Box peremhálózati eszköz, ellenőrizze, hogy:

* Ahogy az az, hogy telepítette a fizikai eszköz [telepítése Data Box Edge](data-box-edge-deploy-install.md).
* A Data Box Edge szolgáltatásból, a Data Box peremhálózati eszköz kezeléséhez létrehozott az aktiválási kulcs van. További információért ugorjon [üzembe helyezését az Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Csatlakozás a helyi webes felhasználói felület beállítása 

1. Ethernet-adapterét konfigurálja a Data Box peremhálózati eszköz, és a 255.255.255.0 alhálózati maszkkal 192.168.100.5 statikus IP-címmel csatlakozni a számítógépen.

2. Csatlakoztassa a számítógépet PORTHOZ 1 az eszközön. Az alábbi ábra segítségével azonosíthatja a PORTHOZ 1 az eszközön.

    ![Egy bekábelezett eszköz hátlapja](./media/data-box-edge-deploy-install/backplane-cabled.png)


3. Nyisson meg egy böngészőablakot és hozzáférés a helyi webes felhasználói felülete a következő címen https://192.168.100.10.  
    Ez a művelet már az eszköz bekapcsolása után néhány percet is igénybe vehet. 

    Láthatja, hogy egy hiba vagy figyelmeztetés jelzi, hogy nincs-e a webhely biztonsági tanúsítványával kapcsolatos problémára. 
   
    ![Webhely biztonsági tanúsítványa hibaüzenet](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

4. Válassza ki **tovább a weblapra a**.  
    Ezeket a lépéseket a használt böngészőtől függően változhat.

5. Jelentkezzen be a webes felhasználói felületen, az eszköz. Az alapértelmezett jelszó az *jelszó1*. 
   
    ![Data Box peremhálózati eszköz bejelentkezési oldal](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

6. Amikor a rendszer az eszköz rendszergazdai jelszavának módosítása  
    Az új jelszó 8 – 16 karakterből állhat. A következő karakterek közül hármat tartalmaznia kell: nagybetű, kisbetű, számjegyeket és speciális karaktereket.

Most már az eszköz irányítópultján található.

## <a name="set-up-and-activate-the-physical-device"></a>Állítsa be, és a fizikai eszköz aktiválása
 
Az irányítópulton jeleníti meg a különböző beállítások konfigurálása és regisztrálása a fizikai eszköz a Data Box Edge szolgáltatással szükséges. A **eszköznév**, **hálózati beállítások**, **webalkalmazás-proxy beállításai**, és **időbeállítások** megadása nem kötelező. Csak a szükséges beállítások **Felhőbeállítások**.
   
![Local web UI "Dashboard" page](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. A bal oldali panelen válassza ki a **eszköznév**, majd adja meg az eszköz rövid nevét.  
    A rövid neve 1 – 15 karakter hosszúságú, és rendelkezik betűket, számokat és kötőjeleket tartalmazhat.

    ![Helyi webes felhasználói felület "Eszköz neve" lap](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Nem kötelező) A bal oldali panelen válassza ki a **hálózati beállítások** , majd konfigurálja a beállításokat.  
    A fizikai eszközön nincsenek hat hálózati adapterrel. PORTHOZ 1 és PORT 2 az 1-GB/s sebességű hálózati adapterrel. PORT 3, a PORT 4, PORT 5 és PORT 6 minden 10-GB/s sebességű hálózati adapterrel is szolgálhat 25-GB/s sebességű hálózati adapterek. PORT 1 automatikusan csak felügyeleti port van beállítva, és PORT 2 PORT 6 minden adat port. A **hálózati beállítások** lap az alább látható módon.
    
    ![Helyi webes felhasználói felület "Hálózati beállítások" lap](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Amikor beállítja a hálózati beállításokat, vegye figyelembe:

   - Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. Az IP-cím, alhálózat, átjáró és DNS automatikusan hozzárendelve.
   - Ha nincs engedélyezve a DHCP, hozzárendelheti statikus IP-címek, szükség esetén.
   - A hálózati adapter IPv4-t konfigurálhatja.

     >[!NOTE] 
     > Javasoljuk, hogy nem váltson át a helyi IP-címét a hálózati adaptert a statikus a DCHP, kivéve, ha az eszköz csatlakozni egy másik IP-címet. Ha egy hálózati adaptert, és váltson a DHCP, akkor is nem lehet megállapítani a DHCP-címet. Ha meg szeretné változtatni a DHCP-címére, várja meg, amíg után az eszköz regisztrálva van a szolgáltatással, és lépjen be. Ezután megtekintheti az összes adapter IP-címekről a **eszköztulajdonságok** a szolgáltatás az Azure Portalon.

3. (Nem kötelező) A bal oldali panelen válassza ki a **webalkalmazás-proxy beállításai**, majd konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, ha olyan webproxyt használ, csak ezen az oldalon konfigurálható.
   
   ![Helyi webes felhasználói felület "Webproxy beállításai" lap](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Az a **webalkalmazás-proxy beállításai** lapon, tegye a következőket:
   
   a. Az a **webalkalmazás-proxy URL-címe** mezőbe írja be az URL-cím a következő formátumban: `http://host-IP address or FQDN:Port number`. HTTPS URL-címek nem támogatottak.

   b. A **hitelesítési**válassza **nincs** vagy **NTLM**.

   c. Ha használ hitelesítést, adja meg a felhasználónevet és jelszót.

   d. Érvényesítéséhez és a alkalmazni a konfigurált webes proxykiszolgáló beállításait, válassza ki a **beállítások alkalmazásához**.

4. (Nem kötelező) A bal oldali panelen válassza ki a **időbeállítások**, majd konfigurálja az időzónát és az elsődleges és másodlagos NTP-kiszolgálóhoz az eszközhöz.  
    Az NTP-kiszolgálókra szükség, mert az eszköz szinkronizálnia kell az időt, hogy a felhőszolgáltatókkal hitelesítést.
       
    Az a **időbeállítások** lapon, tegye a következőket:
    
    1. Az a **időzóna** legördülő listára, válassza ki az időzónát, amely megfelel a földrajzi helyet, ahol az eszközön telepített.
        Az eszköz az alapértelmezett időzóna PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.

    2. Az a **elsődleges NTP-kiszolgáló** mezőben adja meg az eszköz az elsődleges kiszolgáló, vagy fogadja el alapértékként a time.windows.com.  
        Győződjön meg arról, hogy a hálózat lehetővé teszi, hogy NTP-adatforgalmat az Adatközpont az internethez.

    3. Szükség esetén a a **másodlagos NTP-kiszolgáló** adja meg az eszköz egy másodlagos kiszolgálón.

    4. Érvényesítéséhez és a konfigurált beállításokat alkalmazni, válassza ki a **beállítások alkalmazásához**.

        ![Helyi webes felhasználói felület "Idő beállításai" lap](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Nem kötelező) A bal oldali panelen válassza ki a **tárolási beállítások** a storage-rugalmasság konfigurálására az eszközön. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Alapértelmezés szerint a storage, az eszközön nem rugalmas és nincs adatvesztés, adatlemez meghibásodásakor az eszközön. Ha engedélyezi a rugalmas beállítást, a storage, az eszközön újra kell konfigurálni, és az eszköz képes elviselni a hiba, az adatvesztés nélküli egy adatlemezt. A tárolás konfigurálása a rugalmas, az eszköz használható kapacitása csökkenthető.

    > [!IMPORTANT] 
    > A rugalmasság csak akkor állítható be, az eszköz aktiválása előtt. 

    ![Helyi webes felhasználói felület "Tárolási beállítások" lap](./media/data-box-edge-deploy-connect-setup-activate/storage-settings.png)

6. A bal oldali panelen válassza ki a **Felhőbeállítások**, és aktiválja az eszköz a Data Box Edge szolgáltatással az Azure Portalon.
    
    1. Az a **aktiválási kulcs** mezőbe írja be a termékaktiválási kulcsot, a kapott [aktiválási kulcs lekérése](data-box-edge-deploy-prep.md#get-the-activation-key) a Data Box Edge-hez.
    2. Kattintson az **Alkalmaz** gombra.
       
        ![Local web UI "Cloud settings" page](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Először az eszköz akkor aktiválódik. Az eszköz ezután a fontos frissítéseket keres a, és ha elérhető, a rendszer automatikusan alkalmazza a frissítéseket. Megjelenik egy értesítés erre a célra.

        A párbeszédpanelen is, hogy másolja és mentse azt biztonságos helyre a helyreállítási kulccsal rendelkezik. Ezt a kulcsot használja az adatok helyreállításához, abban az esetben, ha az eszköz nem indul el.

        ![Local web UI "Cloud settings" page updated](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Szükség lehet Várjon néhány percet, miután a frissítés sikeresen befejeződött. Jelzi, hogy az eszköz aktiválása sikeresen frissül az oldal.

        ![Local web UI "Cloud settings" page updated](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Az eszköz beállítása befejeződik. Most már hozzáadhat megosztások az eszközön.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Csatlakozás egy fizikai eszköz
> * Állítsa be, és a fizikai eszköz aktiválása

Ismerje meg, hogyan viheti át az adatokat a Data Box peremhálózati eszköz, tekintse meg:

> [!div class="nextstepaction"]
> [A Data Box Edge adatátvitel](./data-box-edge-deploy-add-shares.md).
