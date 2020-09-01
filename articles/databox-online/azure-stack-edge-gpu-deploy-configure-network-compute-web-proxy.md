---
title: Oktatóanyag az Azure Stack Edge-eszköz hálózati beállításainak konfigurálásához GPU-val Azure Portalban | Microsoft Docs
description: Az Azure Stack Edge üzembe helyezésére vonatkozó oktatóanyag arra utasítja Önt, hogy konfigurálja a hálózat, a számítási hálózat és a fizikai eszköz WebProxy-beállításait.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 673bd211f3271eceacb18e7fabfcc6ace2ded42c
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89087855"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-with-gpu"></a>Oktatóanyag: hálózat konfigurálása Azure Stack Edge-hez GPU-val

Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja a hálózatot a Azure Stack Edge-eszközhöz egy beépített GPU-val a helyi webes felhasználói felület használatával.

A kapcsolódási folyamat körülbelül 20 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Hálózat konfigurálása
> * Számítási hálózat engedélyezése
> * A webproxy konfigurálása


## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge-eszköz GPU-val való konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* A fizikai eszközt a [Azure stack Edge telepítése](azure-stack-edge-gpu-deploy-install.md)című részletesen telepítette.
* Csatlakoztatta az eszköz helyi webes FELÜLETéhez a [csatlakozás Azure stack Edge](azure-stack-edge-gpu-deploy-connect.md) -hez részletesen


## <a name="configure-network"></a>Hálózat konfigurálása

A **Get Started** (első lépések) oldal megjeleníti azokat a beállításokat, amelyek szükségesek a fizikai eszköz konfigurálásához és regisztrálásához az Azure stack Edge szolgáltatással. 

Az eszközhöz tartozó hálózat konfigurálásához kövesse az alábbi lépéseket.

1. Az eszköz helyi webes FELÜLETén nyissa meg az **első lépések** lapot. 

2. A **hálózat** csempén válassza a **Konfigurálás**lehetőséget.  
    
    ![Helyi webes felhasználói felület "hálózati beállítások" csempe](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    A fizikai eszközön hat hálózati adapter található. Az 1. és a 2. port 1 GB/s hálózati adapter. A 3. port, a 4-es port, az 5. port és a 6-os PORT mind a 25 GB-os hálózati adapterek, amelyek 10 GB/s hálózati adapterként is használhatók. Az 1. PORT automatikusan csak felügyeleti portként van konfigurálva, a 2-es port pedig minden adatport. Új eszköz esetén a **hálózati beállítások** lap az alábbi ábrán látható.
    
    ![Helyi webes felhasználói felület "hálózati beállítások" lapja](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)


   
3. A hálózati beállítások módosításához válasszon ki egy portot, és a megjelenő jobb oldali panelen módosítsa az IP-címet, az alhálózatot, az átjárót, az elsődleges DNS-t és a másodlagos DNS-t. 

    - Ha az 1. portot választja, láthatja, hogy az előre be van konfigurálva statikusként. 

        ![Helyi webes felhasználói felület – 1. port – hálózati beállítások](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - Ha a 2. port, a 3. port vagy az 5. port lehetőséget választja, a rendszer alapértelmezés szerint az összes portot DHCP-ként konfigurálja.

        ![Helyi webes felhasználói felület – 3. port – hálózati beállítások](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    A hálózati beállítások konfigurálásakor vegye figyelembe a következőket:

   * Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. A rendszer automatikusan hozzárendeli az IP-címet, az alhálózatot, az átjárót és a DNS-t.
   * Ha a DHCP nincs engedélyezve, szükség esetén statikus IP-címeket is hozzárendelhet.
   * A hálózati adaptert IPv4-ként is konfigurálhatja.
   * A 25 GB-os felületeknél beállíthatja a RDMA (távoli közvetlen elérésű memória) módot iWarp vagy RoCE (RDMA konvergált Ethernet-kapcsolaton keresztül). Ha az alacsony késés az elsődleges követelmény, és a méretezhetőség nem jelent problémát, használja a RoCE. Ha a késés kulcsfontosságú követelmény, de a könnyű használat és a méretezhetőség is magas prioritású, a iWARP a legjobb választás.
   * Bármely port sorozatszáma a csomópont sorozatszámának felel meg.

    Az eszköz hálózatának konfigurálása után az oldal frissítései az alább látható módon frissülnek.

    ![Helyi webes felhasználói felület "hálózati beállítások" lapja](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     >[!NOTE]
     >
     > * Azt javasoljuk, hogy ne váltson át a hálózati adapter helyi IP-címére statikusról DHCP-re, hacsak nem rendelkezik másik IP-címmel az eszközhöz való csatlakozáshoz. Ha egy hálózati adaptert használ, és a DHCP-re vált, a DHCP-címeket nem lehet meghatározni. Ha egy DHCP-címről szeretne váltani, várjon, amíg az eszköz aktiválta a szolgáltatást, majd módosítsa a következőt:. Ezután megtekintheti az összes adapter IP-címeit az **eszköz tulajdonságai** között a szolgáltatás Azure Portal.
     > * Ha engedélyezi a számítást, és a IoT Edge modult használja az Azure Stack Edge-eszközön, javasoljuk, hogy a webproxy-hitelesítést ne a **none**értékre állítsa be. Az NTLM nem támogatott.

    A hálózati beállítások konfigurálása és alkalmazása után lépjen vissza **a kezdéshez.**

## <a name="enable-compute-network"></a>Számítási hálózat engedélyezése

Az alábbi lépéseket követve engedélyezheti a számítást és konfigurálhatja a számítási hálózatot.

1. Az eszköz helyi webes FELÜLETén nyissa meg az **első lépések** lapot. A **hálózat** csempén válassza a **számítási hálózat**lehetőséget.  

    ![Számítási lap a helyi felhasználói felületen](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)

2. A **számítás** lapon válasszon ki egy hálózati adaptert, amelyet szeretne engedélyezni a számítási feladatokhoz. 

    ![Számítási lap a helyi felhasználói felületen](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

2. A **hálózati beállítások** párbeszédpanelen válassza az **Engedélyezés**lehetőséget. Ha engedélyezi a számítást, a rendszer létrehoz egy virtuális kapcsolót az eszközön az adott hálózati adapteren. A virtuális kapcsolót a számítási infrastruktúra használja az eszközön. 
    
3. **Kubernetes-csomópontok IP**-címeinek kiosztása. Ezek a statikus IP-címek a számítási virtuális géphez tartoznak. 

    *N*-csomópontos eszköz esetén legalább *n + 1* IPv4-cím (vagy több) összefüggő tartománya van megadva a számítási virtuális géphez a kezdő és záró IP-címek használatával. A megadott Azure Stack Edge egy 1 csomópontos eszköz, amely legalább 2 egybefüggő IPv4-címet biztosít. 

4. **Kubernetes külső szolgáltatás IP**-címeinek kiosztása. Ezek a terheléselosztási IP-címek is. Ezek a folytonos IP-címek olyan szolgáltatások, amelyeket a Kubernetes-fürtön kívül szeretne közzétenni, és a statikus IP-címtartományt az elérhető szolgáltatások számától függően kell megadnia. 
    
    > [!IMPORTANT]
    > Javasoljuk, hogy legalább 1 IP-címet határozzon meg az Azure Stack Edge hub szolgáltatáshoz a számítási modulok eléréséhez. Ezután további IP-címeket is megadhat más szolgáltatások/IoT Edge modulok számára (1/szolgáltatás/modul), amelyeket a fürtön kívülről kell elérni. A szolgáltatás IP-címei később is frissíthetők. 
    
5. Válassza az **Alkalmaz** lehetőséget.

    ![Számítási lap a helyi felhasználói felületen](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)


## <a name="configure-web-proxy"></a>A webproxy konfigurálása

Ez egy opcionális konfiguráció.

1. Az eszköz helyi webes FELÜLETén nyissa meg az első lépések lapot.
2. A **hálózat** csempén konfigurálja a webproxy-kiszolgáló beállításait. Bár a webproxy konfigurálása nem kötelező, ha webproxyt használ, csak ezen a lapon konfigurálhatja.

   ![Helyi webes felhasználói felület "Webproxy-beállítások" lapja](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

3. A **webproxy beállításai** oldalon hajtsa végre a következő lépéseket:

    1. A **webproxy URL-címe** mezőbe írja be az URL-címet a következő formátumban: `http://host-IP address or FQDN:Port number` . A HTTPS URL-címek nem támogatottak.

    2. A **hitelesítés**területen válassza a **nincs** vagy az **NTLM**lehetőséget. Ha engedélyezi a számítást, és a IoT Edge modult használja az Azure Stack Edge-eszközön, javasoljuk, hogy a webproxy-hitelesítést a **none**értékre állítsa be. Az **NTLM** nem támogatott.

    3. Ha hitelesítést használ, adjon meg egy felhasználónevet és egy jelszót.

    4. A konfigurált Webproxy-beállítások érvényesítéséhez és alkalmazásához kattintson az **alkalmaz**gombra.
    
   ![Helyi webes felhasználói felület "Webproxy-beállítások" lapja](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

4. A beállítások alkalmazása után lépjen vissza **a kezdéshez.**


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Hálózat konfigurálása
> * Számítási hálózat engedélyezése
> * A webproxy konfigurálása


Az Azure Stack Edge-eszköz beállításával kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Eszközbeállítások megadása](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
