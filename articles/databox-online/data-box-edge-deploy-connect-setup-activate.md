---
title: Csatlakozni, konfigurálja és az Azure Data Box Edge aktiválni az Azure Portalon |} A Microsoft Docs
description: A Data Box Edge üzembe helyezése harmadik oktatóanyag arra utasítja, hogy csatlakozzon, és állítsa be, és a fizikai eszköz aktiválása.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3d6742bec54e612b5dca6d9ef6c4f67a33929448
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166576"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-edge-preview"></a>Oktatóanyag: Csatlakozás, állítsa be, aktiválja az Azure Data Box Edge (előzetes verzió) 

Ez az oktatóanyag leírja, hogyan lehet csatlakozni, állítsa be, és aktiválja a Data Box peremhálózati eszköz, a helyi webes felhasználói felület. 

A telepítő-aktiválási folyamat befejezéséhez is igénybe vehet körülbelül 20 percet. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Fizikai eszköz csatlakozni
> * Állítsa be, és a fizikai eszköz aktiválása

> [!IMPORTANT]
> Data Box Edge egy előzetes verzióban érhető el. A megoldás megrendelése és üzembe helyezése előtt tekintse át az [Azure előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="prerequisites"></a>Előfeltételek

Mielőtt konfigurálja, és állítsa be a Data Box Edge ellenőrizze, hogy:

* A fizikai eszköz leírt módon telepített [telepítése Data Box Edge](data-box-edge-deploy-install.md).
* A Data Box Edge szolgáltatásból, a Data Box peremhálózati eszköz kezeléséhez létrehozott az aktiválási kulcs van. További információért ugorjon [üzembe helyezését az Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Csatlakozás a helyi webes felhasználói felület beállítása 

1. Ethernet-adapterét konfigurálja a számítógépen, a peremhálózati eszköz, és a 255.255.255.0 alhálózati maszkkal 192.168.100.5 statikus IP-címmel való kapcsolódáshoz használ.
2. Csatlakoztassa a számítógépet PORTHOZ 1 az eszközön. 
3. Nyisson meg egy böngészőablakot és hozzáférés a helyi webes felhasználói felülete a következő címen https://192.168.100.10. Ez a művelet az eszköz bekapcsolása után néhány percet is igénybe vehet. 
4. Láthatja, hogy egy hiba vagy figyelmeztetés jelzi, hogy nincs-e a webhely biztonsági tanúsítványával kapcsolatos problémára. Kattintson a **tovább a weblapra a**. (Ezeket a lépéseket eltérő lehet a használt böngésző alapján.)
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

2. Jelentkezzen be a webes felhasználói felületen, az eszköz. Az alapértelmezett jelszó az *jelszó1*. 
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

3. Kéri, hogy az eszköz rendszergazdai jelszavának módosítása. Írjon be egy új jelszót, 8 és 16 karakter hosszúságú. A jelszónak tartalmaznia kell a következő karakterek közül 3: nagybetűket, kisbetűket, számjegyeket és speciális karaktereket.

A következővel a **irányítópult** eszköze.

## <a name="set-up-and-activate-the-physical-device"></a>Állítsa be, és a fizikai eszköz aktiválása
 
1. Az irányítópultról megnyithatja a különböző beállítások konfigurálása és regisztrálása a fizikai eszköz a Data Box Edge szolgáltatással szükséges. A **eszköznév**, **hálózati beállítások**, **webalkalmazás-proxy beállításai**, és **időbeállítások** megadása nem kötelező. Csak a szükséges beállítások **Felhőbeállítások**.
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

2. Az a **eszköznév** lapon, az eszköz rövid nevét konfigurálja. A rövid név 1. és 15 karakter hosszú lehet, és betűvel, számokat és kötőjeleket tartalmazhat.

    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

3. (Opcionális) konfigurálása a **hálózati beállítások**. A fizikai eszközön látni fogja a hat hálózati adapterrel. PORTHOZ 1 és PORT 2 az 1-GB/s sebességű hálózati adapterrel. PORT 3, PORT 4, PORT 5 és PORT 6 a 25-Gbps hálózati adaptereken. PORT 1 konfigurálása automatikusan történik csak felügyeleti port bár PORT 2 PORT 6 minden adat port. A **hálózati beállítások** oldalon, lent látható módon.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Hálózati beállítások konfigurálásakor vegye figyelembe:

    - Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. Az IP-cím, alhálózat, átjáró és DNS automatikusan hozzárendelve.
    - Ha nincs engedélyezve a DHCP, hozzárendelheti statikus IP-címek, szükség esetén.
    - A hálózati adapter IPv4-t konfigurálhatja.
   
4. (Nem kötelező) konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, ha olyan webproxyt használ, csak konfigurálhatja azt itt.
   
   ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Az a **webalkalmazás-proxy** oldalon:
   
   1. Adja meg a **webalkalmazás-proxy URL-címe** a következő formátumban: `http://host-IP address or FDQN:Port number`. HTTPS URL-címek nem támogatottak.
   2. Adja meg **hitelesítési** , **alapszintű** vagy **None**.
   3. Hitelesítés használata esetén is kell adnia egy **felhasználónév** és **jelszó**.
   4. Kattintson a **alkalmaz** érvényesítéséhez és a alkalmazni a konfigurált webes proxykiszolgáló beállításait.

5. (Opcionális) beállításokat az idő az eszköz, például az időzónát és az elsődleges és másodlagos NTP-kiszolgálót. Az NTP-kiszolgálókra szükség, mert az eszköz szinkronizálnia kell az időt, hogy a felhőszolgáltatókkal hitelesítést.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    Az a **időbeállítások** oldalon:
    
    1. A legördülő listából válassza ki a **időzóna** , amelyben az eszközre telepített földrajzi helye alapján. Az eszköz az alapértelmezett időzóna PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.
    2. Adjon meg egy **elsődleges NTP-kiszolgáló** az eszközhöz, vagy fogadja el alapértékként a time.windows.com. Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat.
    3. Nem kötelezően megadhatja a **másodlagos NTP-kiszolgáló** az eszközhöz.
    4. Kattintson a **alkalmaz** érvényesítéséhez és a alkalmazni a konfigurált beállításokat.

6. Az a **Felhőbeállítások** lapon, az eszköz a Data Box Edge szolgáltatással az Azure Portalon aktiválhatja.
    
    1. Adja meg a **aktiválási kulcs** kapott [aktiválási kulcs lekérése](data-box-edge-deploy-prep.md#get-the-activation-key) a Data Box Edge-hez.

    2. Kattintson az **Alkalmaz** gombra. 
       
         ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    3. Az eszköz sikeresen aktiválása után csatlakozási mód beállításai áll rendelkezésre. Ezek a beállítások vannak konfigurálva, ha az eszközhöz részlegesen leválasztott vagy kapcsolat nélküli üzemmódban van szüksége. 

        ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

Az eszköz beállítása befejeződik. Most már hozzáadhat megosztások az eszközön.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerkedett a Data Box Edge témakörök például:

> [!div class="checklist"]
> * Fizikai eszköz csatlakozni
> * Állítsa be, és a fizikai eszköz aktiválása


Folytassa a következő oktatóanyaggal, megtudhatja, hogyan viheti át az adatokat a Data Box oldala.

> [!div class="nextstepaction"]
> [A Data Box Edge adatátvitel](./data-box-edge-deploy-add-shares.md).