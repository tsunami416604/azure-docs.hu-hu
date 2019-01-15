---
title: Csatlakozni, konfigurálja és aktiválni az Azure Data Box Edge-eszköz, az Azure Portalon |} A Microsoft Docs
description: A Data Box Edge üzembe helyezése harmadik oktatóanyag arra utasítja, hogy csatlakozzon, és állítsa be, és a fizikai eszköz aktiválása.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: e5477807e4cff6dc745ccfd0383fd216719a7aa5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259612"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge-preview"></a>Oktatóanyag: Csatlakozás, beállítása és aktiválni az Azure Data Box Edge (előzetes verzió) 

Ez az oktatóanyag leírja, hogyan csatlakozhat, állítsa be, és aktiválja az Azure Data Box Edge-eszköz helyi webes felhasználói felület használatával. 

A telepítő-aktiválási folyamat befejezéséhez is igénybe vehet körülbelül 20 percet. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Csatlakozás egy fizikai eszköz
> * Állítsa be, és a fizikai eszköz aktiválása

> [!IMPORTANT]
> A Data Box Edge előzetes verzióban érhető el. Order, és a megoldás üzembe helyezése előtt tekintse át a [Azure villámnézethez szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="prerequisites"></a>Előfeltételek

Mielőtt konfigurálja, és állítsa be a Data Box peremhálózati eszköz, ellenőrizze, hogy:

* Ahogy az az, hogy telepítette a fizikai eszköz [telepítése Data Box Edge](data-box-edge-deploy-install.md).
* A Data Box Edge szolgáltatásból, a Data Box peremhálózati eszköz kezeléséhez létrehozott az aktiválási kulcs van. További információért ugorjon [üzembe helyezését az Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Csatlakozás a helyi webes felhasználói felület beállítása 

1. Ethernet-adapterét konfigurálja az Edge-eszköz és a 255.255.255.0 alhálózati maszkkal 192.168.100.5 statikus IP-címmel csatlakozni a számítógépen.

1. Csatlakoztassa a számítógépet PORTHOZ 1 az eszközön. 

1. Nyisson meg egy böngészőablakot és hozzáférés a helyi webes felhasználói felülete a következő címen https://192.168.100.10.  
    Ez a művelet már az eszköz bekapcsolása után néhány percet is igénybe vehet. 

    Láthatja, hogy egy hiba vagy figyelmeztetés jelzi, hogy nincs-e a webhely biztonsági tanúsítványával kapcsolatos problémára. 
   
    ![Webhely biztonsági tanúsítványa hibaüzenet](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

1. Válassza ki **tovább a weblapra a**.  
    Ezeket a lépéseket a használt böngészőtől függően változhat.

1. Jelentkezzen be a webes felhasználói felületen, az eszköz. Az alapértelmezett jelszó az *jelszó1*. 
   
    ![Data Box peremhálózati eszköz bejelentkezési oldal](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

1. Amikor a rendszer az eszköz rendszergazdai jelszavának módosítása  
    Az új jelszó 8 – 16 karakter hosszúságú lehet. A következő karakterek közül hármat tartalmaznia kell: nagybetű, kisbetű, számjegyeket és speciális karaktereket.

Most már az eszköz irányítópultján található.

## <a name="set-up-and-activate-the-physical-device"></a>Állítsa be, és a fizikai eszköz aktiválása
 
Az irányítópulton jeleníti meg a különböző beállítások konfigurálása és regisztrálása a fizikai eszköz a Data Box Edge szolgáltatással szükséges. A **eszköznév**, **hálózati beállítások**, **webalkalmazás-proxy beállításai**, és **időbeállítások** megadása nem kötelező. Csak a szükséges beállítások **Felhőbeállítások**.
   
![A Data Box Edge-eszközök irányítópultja](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. A bal oldali panelen válassza ki a **eszköznév**, majd adja meg az eszköz rövid nevét.  
    A valódi neve 1 – 15 karakter hosszúságú, és betűket, számokat és kötőjeleket tartalmazhat.

    ![Az "Eszköz neve" oldal](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

1. (Nem kötelező) A bal oldali panelen válassza ki a **hálózati beállítások** , majd konfigurálja a beállításokat.  
    A fizikai eszközön hat hálózati adapterrel vannak. PORTHOZ 1 és PORT 2 az 1-GB/s sebességű hálózati adapterrel. PORT 3, PORT 4, PORT 5 és PORT 6 a 25-Gbps hálózati adaptereken. PORT 1 automatikusan csak felügyeleti port van beállítva, és PORT 2 PORT 6 minden adat port. A **hálózati beállítások** oldalon, lent látható módon.
    
    ![A "Hálózati beállítások" lap](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Amikor beállítja a hálózati beállításokat, vegye figyelembe:

    - Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. Az IP-cím, alhálózat, átjáró és DNS automatikusan hozzárendelve.
    - Ha nincs engedélyezve a DHCP, hozzárendelheti statikus IP-címek, szükség esetén.
    - A hálózati adapter IPv4-t konfigurálhatja.

    >[!NOTE] 
    > Javasoljuk, hogy nem váltson át a helyi IP-cím a hálózatot felület statikusról DCHP, kivéve, ha az eszköz csatlakozni egy másik IP-címet. Ha egy hálózati adaptert, és váltson a DHCP, akkor is nem lehet megállapítani a DHCP-címet. Ha meg szeretné változtatni a DHCP-címére, várja meg, amíg után az eszköz regisztrálva van a szolgáltatással, és lépjen be. Ezután megtekintheti az összes adpaters, az IP-címek a **eszköztulajdonságok** a szolgáltatás az Azure Portalon.

1. (Nem kötelező) A bal oldali panelen válassza ki a **webalkalmazás-proxy beállításai**, majd konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, ha olyan webproxyt használ, csak ezen az oldalon konfigurálható.
   
   ![A "Webproxy beállításai" lap](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Az a **webalkalmazás-proxy beállításai** lapon, tegye a következőket:
   
   a. Az a **webalkalmazás-proxy URL-címe** mezőbe írja be az URL-cím a következő formátumban: `http://host-IP address or FQDN:Port number`. HTTPS URL-címek nem támogatottak.

   b. A **hitelesítési**válassza **nincs** vagy **NTLM**.

   c. Ha használ hitelesítést, adja meg a felhasználónevet és jelszót.

   d. Érvényesítéséhez és a alkalmazni a konfigurált webes proxykiszolgáló beállításait, válassza ki a **beállítások alkalmazásához**.

1. (Nem kötelező) A bal oldali panelen válassza ki a **időbeállítások**, majd konfigurálja az időzónát és az elsődleges és másodlagos NTP-kiszolgálóhoz az eszközhöz.  
    Az NTP-kiszolgálókra szükség, mert az eszköz szinkronizálnia kell az időt, hogy a felhőszolgáltatókkal hitelesítést.
    
    ![A "Time beállítások" lap](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    Az a **időbeállítások** lapon, tegye a következőket:
    
    a. Az a **időzóna** legördülő listára, válassza ki az időzónát, amely megfelel a földrajzi helyet, ahol az eszközön telepített.  
        Az eszköz az alapértelmezett időzóna PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.

    b. Az a **elsődleges NTP-kiszolgáló** mezőben adja meg az eszköz az elsődleges kiszolgáló, vagy fogadja el alapértékként a time.windows.com.  
        Győződjön meg arról, hogy a hálózat lehetővé teszi, hogy NTP-adatforgalmat az Adatközpont az internethez.

    c. Szükség esetén a a **másodlagos NTP-kiszolgáló** adja meg az eszköz egy másodlagos kiszolgálón.

    d. Érvényesítéséhez és a konfigurált beállításokat alkalmazni, válassza ki a **alkalmaz**.

6. A bal oldali panelen válassza ki a **Felhőbeállítások**, és aktiválja az eszköz a Data Box Edge szolgáltatással az Azure Portalon.
    
    a. Az a **aktiválási kulcs** mezőbe írja be a termékaktiválási kulcsot, a kapott [aktiválási kulcs lekérése](data-box-edge-deploy-prep.md#get-the-activation-key) a Data Box Edge-hez.

    b. Kattintson az **Alkalmaz** gombra. 
       
    ![A "Felhő beállítások" lap](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    Az eszköz sikeresen aktiválása után csatlakozási mód beállításai áll rendelkezésre. Ezek a beállítások vannak konfigurálva, ha az eszközhöz részlegesen leválasztott vagy kapcsolat nélküli üzemmódban van szüksége. 

    ![Az aktiválás "Felhőbeállítások" megerősítése](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

Az eszköz beállítása befejeződik. Most már hozzáadhat megosztások az eszközön.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Csatlakozás egy fizikai eszköz
> * Állítsa be, és a fizikai eszköz aktiválása


Ismerje meg, hogyan viheti át az adatokat a Data Box peremhálózati eszköz, tekintse meg:

> [!div class="nextstepaction"]
> [A Data Box Edge adatátvitel](./data-box-edge-deploy-add-shares.md).
