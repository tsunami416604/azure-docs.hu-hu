---
title: Azure – első lépések a verem - portál virtuális gép létrehozása
description: Azure verem Quick Start - Linux virtuális gép létrehozása a portálon
services: azure-stack
cloud: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 2ea07f04d4c566c0add39d75cad3d3a4ed81c6c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32152220"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Gyors üzembe helyezés: Linux server virtuális gép létrehozása az Azure-verem portállal

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A verem Azure portál használatával létrehozhat egy Ubuntu Server 16.04 LTS virtuális gépet. Kövesse a cikkben történő létrehozásáról és használatáról a virtuális gép. Ez a cikk is lehetővé teszi a lépéseket:

* Csatlakoztassa a virtuális gép egy távoli ügyfélhez.
* Egy NGINX-webkiszolgáló telepítéséhez.
* Az erőforrások törlése.

## <a name="prerequisites"></a>Előfeltételek

* **A Linux-lemezkép a verem Azure piactéren**

   A verem Azure piactér nem tartalmazza a Linux-lemezkép alapértelmezés szerint. Linux virtuális gépek létrehozása előtt győződjön meg arról, hogy az Azure-verem üzemeltető biztosítja a **Ubuntu Server 16.04 LTS** lemezkép van szüksége. Az operátor használhatja a leírt lépéseket a [Piactéri elemek töltse le az Azure-ból Azure verem](../azure-stack-download-azure-marketplace-item.md) cikk.

* **Egy SSH-ügyfél a hozzáférést**

   Ha az Azure verem Development Kit (ASDK) használ, nincs egy SSH-ügyfél a hozzáférést. Ha egy ügyfél van szüksége, nincsenek több, egy SSH-ügyfelet tartalmazó csomagot. Például a PuTTY tartalmaz egy SSH-ügyfél és egy SSH megosztottelérésikulcs-készítő (puttygen.exe). További információ az elérhető csomagokat, olvassa el a következő Azure: [az SSH-kulcsok a Windows Azure hogyan](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   A gyors üzembe helyezés használja a PuTTY az SSH-kulcsok létrehozásához és a Linux virtuális gép csatlakozni. Töltse le és telepítse a PuTTY, keresse fel [ http://www.putty.org/ ](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Ebben a cikkben minden lépést befejezéséhez egy SSH-kulcspárral van szüksége. Ha egy meglévő SSH-kulcspárral, kihagyhatja ezt a lépést.

1. Keresse meg azt a PuTTY telepítési mappát (az alapértelmezett hely: ```C:\Program Files\PuTTY```), és futtassa ```puttygen.exe```.
2. A PuTTY kulcs generátor ablakban ellenőrizze, hogy a **létrehozni kívánt kulcs típusa** értéke **RSA**, és a **létrehozott kulcs bitjeinek száma** értéke **2048**. Ha elkészült, kattintson a **Generate**.

   ![PuTTY kulcs Generator-konfiguráció](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. A kulcs létrehozása, mutasson az egérrel véletlenszerűen a PuTTY kulcs generátor ablakban.
4. A kulcs létrehozásának befejezése után kattintson **mentés nyilvános kulcs** , majd **mentés titkos kulcs** a kulcsok fájlokat menteni.

   ![PuTTY kulcs generátor eredmények](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Jelentkezzen be a verem Azure portálra

Jelentkezzen be a verem Azure portálra. A cím az Azure-verem portál függ, hogy melyik Azure verem termékre való kapcsolódás esetén:

* Ugrás az Azure verem Development Kit (ASDK): https://portal.local.azurestack.external.
* Integrált Azure verem rendszert nyissa meg az Azure-verem operátor megadott URL-címre.

## <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

1. Kattintson a **hozzon létre egy erőforrást** a verem Azure portál bal felső sarokban.

2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Kattintson a **Create** (Létrehozás) gombra.

4. Írja be a virtuális gép adatait. A **Hitelesítés típusa** résznél válassza az **SSH nyilvános kulcs** lehetőséget. Az SSH nyilvános kulcsát, mentett, és kattintson a Beillesztés **OK**.

   >[!NOTE]
 Győződjön meg arról, hogy a kulcs akkor távolítsa el a kezdő vagy záró szóközt.

   ![Alapvető beállítások panel – a virtuális gép konfigurálása](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Válassza ki **D1_V2** a virtuális géphez.

   ![Méretezés panel – a virtuális gép méretének kiválasztása](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Az a **beállítások** lapon hagyja az alapértelmezett beállításokat, majd kattintson **OK**.

7. A a **összegzés** kattintson **OK** elindítani a virtuális gépek telepítése során.

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

1. Kattintson a **Connect** a virtuális gép oldalon. Megjelenik egy SSH-kapcsolati karakterlánc, amelyekre szüksége van a virtuális géphez történő csatlakozáshoz.

   ![Csatlakozás a virtuális gép](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Nyissa meg a PuTTY eszközt.
3. Az a **PuTTY konfigurációs** képernyő fogja használni a **kategória** felfelé vagy lefelé ablak. Görgessen le a **SSH**, bontsa ki a **SSH**, és kattintson a **Auth**. Kattintson a **Tallózás** és a titkos kulcsfájl mentett válasszon.

   ![PuTTY titkos kulcs kiválasztása](media/azure-stack-quick-linux-portal/Putty03.PNG)

4. Görgessen fel, a a **kategória** ablakot, és kattintson **munkamenet**.
5. Az a **állomásnevet (vagy IP-cím)** mezőbe illessze be a kapcsolati karakterlánc jelenik meg a verem Azure portálon. Ebben a példában a karakterlánc értéke ```asadmin@192.168.102.34```.

   ![PuTTY konfigurációs kapcsolati karakterlánc](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Kattintson a **nyissa meg a** a virtuális gép egy munkamenet megnyitását.

   ![Linux-munkamenet](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Az NGINX-webkiszolgáló telepítése

A következő parancsokkal bash csomag források frissítse és telepítse a legújabb NGINX-csomagot a virtuális gépen.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Amikor befejezte a NGINX telepítése, zárja be az SSH-munkamenetet, és nyissa meg a a virtuális gép – áttekintés oldalra a verem Azure-portálon.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

A hálózati biztonsági csoport (NSG) feladata a bejövő és kimenő forgalom védelme. Egy virtuális gép létrehozásakor a verem Azure-portálon létrejön egy bejövő forgalomra vonatkozó szabály az SSH-kapcsolatokhoz 22-es portot. Ezt a virtuális gépet üzemeltető webkiszolgálóra, mert egy NSG-szabály kell létrehozni webes forgalmat engedélyezi a 80-as porton.

1. A virtuális gépen **áttekintése** lapján kattintson a nevét a **erőforráscsoport**.
2. Válassza ki a **hálózati biztonsági csoport** a virtuális géphez. A hálózati biztonsági csoport a **Típus** oszlop segítségével azonosítható.
3. A bal oldali menü alatti **beállítások**, kattintson a **bejövő biztonsági szabályok**.
4. Kattintson a **Hozzáadás** parancsra.
5. A **Név** mezőbe írja be a **http** karakterláncot. Ügyeljen rá, hogy a **Porttartomány** értéke 80, a **Művelet** értéke pedig **Engedélyezés** legyen.
6. Kattintson az **OK** gombra

## <a name="view-the-nginx-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Az NGINX telepítve, és a virtuális gépen nyissa meg a 80-as porton a webkiszolgálón, a virtuális gép nyilvános IP-cím használatával végezheti el. (A nyilvános IP-cím jelenik meg a virtuális gép – áttekintés oldalra.)

Nyisson meg egy webböngészőt, és keresse meg a ```http://<public IP address>```.

![NGINX web server kezdőlap](media/azure-stack-quick-linux-portal/linux-04.PNG)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrásokat, amelyek többé nem kell törölni. A virtuális gép és az erőforrások törléséhez válassza ki az erőforráscsoportot, a virtuális gép oldalon, és kattintson **törlése**.

## <a name="next-steps"></a>További lépések

A gyors üzembe helyezési webkiszolgáló alapvető Linux-kiszolgáló virtuális gépek telepítette. További információt a verem Azure virtuális gépek, továbbra is [szempontok a virtuális gépek Azure-készletben](azure-stack-vm-considerations.md).
