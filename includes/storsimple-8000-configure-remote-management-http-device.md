---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b397d77b525bdb63f2531634e397ec210d4a6202
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165959"
---
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>A távfelügyelet konfigurálása a felhőalapú készüléken

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Válassza ki a felhőalapú készüléket a szolgáltatáshoz csatlakoztatott eszközök listájából, majd kattintson rá.
    ![Felhőalapú készülék kiválasztása a StorSimple-ben](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. Nyissa meg a **Beállítások > Biztonság** menüpont **Biztonsági beállítások** paneljét.

     ![A StorSimple biztonsági beállításai](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. Lépjen a **Távfelügyelet** szakaszhoz. Kattintson a **Távfelügyelet** mezőre.
     ![StorSimple-távfelügyelet](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. A **Távfelügyelet** panelen:

    1. Győződjön meg arról, hogy a **Távfelügyelet engedélyezése** elem engedélyezve van.
    2. Az alapértelmezett csatlakozási mód a HTTPS protokoll használata. Igény szerint HTTP-n keresztül is csatlakozhat. A HTTP-n keresztüli csatlakozást kizárólag megbízható hálózatokon célszerű engedélyezni. Győződjön meg arról, hogy a HTTP lehetőség engedélyezve van.
    3. A panel tetején lévő parancssávban kattintson a **... továbbiak**, majd a **Tanúsítvány letöltése** elemre a távfelügyeleti tanúsítvány letöltéséhez. A fájl mentésének helyét is megadhatja. A tanúsítványt ezt követően telepítenie kell arra az ügyfélszámítógépre vagy gazdagépre, amellyel csatlakozni fog a felhőalapú készülékhez.

        ![Távfelügyeleti panel](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. Kattintson a **Mentés** parancsra, majd a rendszer kérdésének megjelenésekor hagyja jóvá a módosításokat.