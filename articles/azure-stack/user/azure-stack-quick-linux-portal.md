---
title: "Azure – első lépések a verem - portál virtuális gép létrehozása"
description: "Azure verem Quick Start - Linux virtuális gép létrehozása a portálon"
services: azure-stack
cloud: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 12/11/2017
ms.author: brenduns
ms.reviewer: 
ms.custom: mvc
ms.openlocfilehash: d4aef23e2de327fabb1f0304d8a3db1497d55827
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="create-a-linux-virtual-machine-with-the-azure-stack-portal"></a>Linux virtuális gép létrehozása az Azure-verem portállal

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A verem Azure portálon keresztül verem Azure virtuális gépek hozhatók létre. Ez a metódus létrehozása és konfigurálása a virtuális gépek böngészőalapú felhasználói felületet biztosít, és az összes kapcsolódó erőforrásokat. A gyors üzembe helyezés bemutatja, hogyan hozhat létre egy Linux virtuális gép és egy webkiszolgáló telepíthető.

## <a name="prerequisites"></a>Előfeltételek

* **A Linux-lemezkép a verem Azure piactéren**

   A verem Azure piactér nem tartalmazza a Linux-lemezkép alapértelmezés szerint. Igen, Linux virtuális gépek létrehozása előtt győződjön meg arról, hogy az Azure-verem operátor letöltötte a **Ubuntu Server 16.04 LTS** kép ismertetett lépések segítségével a [Azure Azure töltse le a Piactéri elemek Verem](../azure-stack-download-azure-marketplace-item.md) témakör.

* **Egy SSH-ügyfél a hozzáférést**

   Az Azure verem Development Kit (ASDK) használ, előfordulhat, hogy nem elérhető lesz az SSH-ügyfél a környezetben. Ez a helyzet, ha több, egy SSH-ügyfelet tartalmazó csomagot közül választhat. Például a PuTTY SSH-ügyfél és a SSH megosztottelérésikulcs-készítő (puttygen.exe) is telepítheti. Opció kapcsolatos további információkért lásd a következő Azure cikk kapcsolódó: [az SSH-kulcsok a Windows Azure hogyan](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   A gyors üzembe helyezés használja a PuTTY az SSH-kulcsok létrehozásához és a Linux virtuális géphez történő csatlakozáshoz. Töltse le és telepítse a PuTTY, keresse fel [http://www.putty.org/](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

A gyors üzembe helyezés befejeződik az SSH-kulcspárral van szüksége. Ha már rendelkezik SSH-kulcspárral, kihagyhatja ezt a lépést.

1. Keresse meg azt a PuTTY telepítési mappát (az alapértelmezett hely: ```C:\Program Files\PuTTY```), és futtassa ```puttygen.exe```.
2. A PuTTY kulcs generátor ablakban ellenőrizze, hogy a **létrehozni kívánt kulcs típusa** értéke **RSA**, és a **létrehozott kulcs bitjeinek száma** értéke **2048**. Ha elkészült, kattintson **Generate**.

   ![puttygen.exe](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. A kulcs létrehozásának befejezéséhez, mutasson az egérrel a PuTTY kulcs generátor ablakon belül.
4. A kulcs létrehozásának befejezése után kattintson **mentés nyilvános kulcs** és **mentés titkos kulcs** fájlokat menteni a nyilvános és titkos kulcsok.

   ![PuTTY kulcsok](media/azure-stack-quick-linux-portal/Putty02.PNG)



## <a name="sign-in-to-the-azure-stack-portal"></a>Jelentkezzen be a verem Azure portálra

Jelentkezzen be a verem Azure portálra. A cím az Azure-verem portál függ, hogy melyik Azure verem termékre való kapcsolódás esetén:

* Ugrás az Azure verem Development Kit (ASDK): https://portal.local.azurestack.external.
* Integrált Azure verem rendszert nyissa meg az Azure-verem operátor megadott URL-címre.

## <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

1. Kattintson a **hozzon létre egy erőforrást** a verem Azure portál bal felső sarokban.

2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Kattintson a **Create** (Létrehozás) gombra.

4. Írja be a virtuális gép adatait. A **Hitelesítés típusa** résznél válassza az **SSH nyilvános kulcs** lehetőséget. Ha az SSH nyilvános kulcs (korábban mentett fájlba), ügyeljen arra, távolítsa el az összes kezdő vagy záró szóközt. Amikor végzett, kattintson az **OK** gombra.

   ![Virtuális gép alapjai](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Válassza ki **D1_V2** a virtuális géphez.

   ![Mérete](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Az a **beállítások** lapon hagyja az alapértelmezett beállításokat, majd kattintson **OK**.

7. A a **összegzés** kattintson **OK** elindítani a virtuális gépek telepítése során.


## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

1. Kattintson a **Connect** a virtuális gép oldalon. Megjelenik egy SSH-kapcsolati karakterlánc, amely segítségével csatlakozzon a virtuális géphez.

   ![Csatlakozás a virtuális gép](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Nyissa meg a PuTTY eszközt.
3. A a **PuTTY konfigurációs** képernyőn, a **kategória**, bontsa ki a **SSH** majd **Auth**. Kattintson a **Tallózás** válassza ki a korábban mentett titkos kulcs fájlját.

   ![PuTTY titkos kulcs](media/azure-stack-quick-linux-portal/Putty03.PNG)
4. A **kategória**, görgessen fel, és kattintson a **munkamenet**.
5. Az a **állomásnevet (vagy IP-cím)** mezőbe illessze be a kapcsolati karakterláncot a verem Azure portálról korábban látott. Ebben a példában a karakterlánc értéke ```asadmin@192.168.102.34```.
 
   ![PuTTY-munkamenet](media/azure-stack-quick-linux-portal/Putty04.PNG)
6. Kattintson a **nyissa meg a** a virtuális gép egy munkamenet megnyitását.

   ![Linus munkamenet](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-nginx"></a>Az NGINX telepítése

A következő bash parancsfájlok segítségével csomag források frissítse és telepítse a legújabb NGINX-csomagot a virtuális gépen. 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Ha befejezte, lépjen ki az SSH-munkamenetet, és térjen vissza a virtuális gép – áttekintés oldalra a verem Azure-portálon.


## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára 

A hálózati biztonsági csoport (NSG) feladata a bejövő és kimenő forgalom védelme. A virtuális gép létrehozásakor a verem Azure-portálról egy bejövő forgalomra vonatkozó szabály az SSH-kapcsolatokhoz 22-es portot jön létre. Ezt a virtuális gépet üzemeltető webkiszolgálóra, mert egy NSG-szabály kell létrehozni a 80-as porton.

1. A virtuális gépen **áttekintése** lapján kattintson a nevét a **erőforráscsoport**.
2. Válassza ki a **hálózati biztonsági csoport** a virtuális géphez. A hálózati biztonsági csoport a **Típus** oszlop segítségével azonosítható. 
3. A bal oldali menü alatti **beállítások**, kattintson a **bejövő biztonsági szabályok**.
4. Kattintson a **Hozzáadás** parancsra.
5. A **Név** mezőbe írja be a **http** karakterláncot. Ügyeljen rá, hogy a **Porttartomány** értéke 80, a **Művelet** értéke pedig **Engedélyezés** legyen. 
6. Kattintson az **OK** gombra.


## <a name="view-the-nginx-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Az NGINX telepítve, és a virtuális gépen nyissa meg a 80-as porton a webkiszolgáló mostantól elérhető a virtuális gép nyilvános IP-címen. A nyilvános IP-címet a virtuális gép áttekintése lapon a verem Azure portálon található.

Nyisson meg egy webböngészőt, és keresse meg a ```http://<public IP address>```.

![Alapértelmezett NGINX-webhely](media/azure-stack-quick-linux-portal/linux-04.PNG)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez a virtuális gép lapon jelölje ki az erőforráscsoportot, majd kattintson **törlése**.

## <a name="next-steps"></a>További lépések

A gyors üzembe helyezési már telepített egy egyszerű Linux virtuális gép hálózati biztonsági csoport szabályt, és a webkiszolgálón telepítve. További információt a verem Azure virtuális gépek, továbbra is [szempontok a virtuális gépek Azure-készletben](azure-stack-vm-considerations.md).

