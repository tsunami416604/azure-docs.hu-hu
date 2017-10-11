---
title: "A StorSimple 8000 series eszköz webproxy beállítása |} Microsoft Docs"
description: "Ismerje meg, hogyan használhatja a Windows PowerShell-lel webes proxykiszolgáló beállításait a StorSimple eszköz."
services: storsimple
documentationcenter: 
author: alkohli
manager: 
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 1109e44ed9c6aa8a0f7305b8a50410316711589c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>A StorSimple eszköz webalkalmazás-proxy konfigurálása

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag leírja, hogyan használható a Windows PowerShell-lel konfigurálásához és a StorSimple eszköz webes proxy beállításainak megtekintése. A webproxy beállításai használják a StorSimple eszköz a felhőre való kommunikáció során. Proxy-webkiszolgáló használatával egy másik biztonsági, szűrő tartalmával, egyszerű sávszélesség-követelményekkel vagy analytics még akkor is segítséget gyorsítótár réteget.

Ez az oktatóanyag az útmutató csak a StorSimple 8000 series fizikai eszközök vonatkozik. Webproxy konfigurálása a StorSimple-felhő készüléken (a 8010-es és a 8020-as modell) nem támogatott.

Webalkalmazás-proxy egy _választható_ a StorSimple eszköz konfigurációját. StorSimple webproxy csak a Windows PowerShell segítségével konfigurálható. A beállítási lehetőségek az alábbiak szerint egy kétlépéses folyamat:

1. Először konfigurálja a webproxy beállításai a telepítő varázsló vagy a Windows PowerShell segítségével a StorSimple-parancsmagokhoz tartozó.
2. A konfigurált webes proxy beállításainak Windows PowerShell parancsmagok StorSimple majd engedélyezése.

A webproxy-konfigurációja befejeződése után megtekintheti a konfigurált webes proxykiszolgáló beállításait a Microsoft Azure StorSimple Device Manager szolgáltatás és a Windows PowerShell a StorSimple.

Ez az oktatóanyag elolvasása, után fogja tudni:

* Webalkalmazás-proxy konfigurálása telepítővarázslóját, és a parancsmagok használatával.
* Engedélyezze a webproxy-parancsmagok használatával.
* Webes proxykiszolgáló beállításainak megtekintéséhez az Azure portálon.
* Webproxy konfigurálásakor felmerülő hibák elhárítása.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Windows PowerShell segítségével a webproxy konfigurálása a StorSimple

Használja a következő webes Proxybeállítások konfigurálása:

* A telepítő varázsló végigvezeti a konfigurációs lépéseket.
* Parancsmagok a Windows PowerShell-lel.

Mindkét módszerhez a következő szakaszban tárgyalt.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>A telepítővarázsló segítségével webalkalmazás-proxy konfigurálása

A telepítő varázsló segítségével, amelyek a webalkalmazás-proxy konfigurációs lépései. A következő lépésekkel webalkalmazás-proxy konfigurálása az eszközön.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>A telepítővarázsló segítségével webproxy konfigurálása

1. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési** , és adja meg a **eszköz rendszergazdai jelszava**. Írja be a telepítő varázsló munkamenetet indítani a következő parancsot:
   
    `Invoke-HcsSetupWizard`
2. Ha ez az első alkalommal használja a telepítővarázsló az eszközregisztrációhoz tartozó, kell konfigurálni a szükséges hálózati beállításokat, amíg el nem éri a webproxy-konfigurációja. Ha az eszköz már regisztrálva van, amíg el nem éri a webproxy-konfigurációja fogadja el a konfigurált hálózati beállításokat. A varázslóban, amikor webes proxy beállításainak konfigurálását kéri, írja be a **Igen**.
3. Az a **webes Proxy URL-címe**, adja meg annak az IP-cím vagy teljes tartománynevét (FQDN), a proxy-webkiszolgáló és a TCP-port száma, amelyek azt szeretné, hogy az eszköz a felhőre való kommunikáció során. Használja a következő formátumot:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Alapértelmezés szerint a TCP-portszámot 8080 van megadva.
4. Válassza ki a hitelesítés típusát, **NTLM**, **alapvető**, vagy **nincs**. Alapszintű van a proxykiszolgáló beállításait a legkevésbé biztonságos hitelesítés. NT LAN Manager-(NTLM-) értéke magas biztonságos és összetett hitelesítés használt protokoll, amely egy háromutas üzenetkezelési rendszeréhez (néha négy sértetlenségét szükség esetén) a felhasználó hitelesítéséhez. Az alapértelmezett hitelesítési NTLM. További információkért lásd: [alapvető](http://hc.apache.org/httpclient-3.x/authentication.html) és [NTLM-hitelesítés](http://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **A StorSimple Device Manager szolgáltatásban az eszköz figyelési diagramok nem egyszerű vagy az NTLM-hitelesítés engedélyezve van a proxykiszolgáló beállításait az eszközön. A figyelési diagramok működjön győződjön meg arról, hogy a hitelesítés beállítása NONE kell.**
  
5. Ha engedélyezte a hitelesítést, adjon meg egy **webes Proxy-felhasználónév** és egy **webes Proxy jelszó**. Szükség erősítse meg a jelszót.
   
    ![Webalkalmazás-Proxy konfigurálása a StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Ha az eszköz az első alkalommal regisztrál, folytassa a regisztrációt. Ha az eszköz már regisztrálva van, a varázsló kilép. A konfigurált beállítások lesznek mentve.

Webalkalmazás-proxy engedélyezve van. Ugorjon a [webes proxy engedélyezése](#enable-web-proxy) . lépés:, és közvetlenül [webes proxykiszolgáló beállításainak megtekintéséhez az Azure portálon](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>StorSimple-parancsmagokhoz tartozó Windows PowerShell segítségével webalkalmazás-proxy konfigurálása

Egy másik webes proxy beállításainak konfigurálása módja a StorSimple-parancsmagok a Windows PowerShell segítségével. A következő lépésekkel webalkalmazás-proxy konfigurálása.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>A parancsmagok használatával webalkalmazás-proxy konfigurálása
1. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**. Amikor a rendszer kéri, adja meg a **eszköz rendszergazdai jelszava**. Az alapértelmezett jelszó `Password1`.
2. A parancssorba írja be:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Adjon meg, majd erősítse meg a jelszót, amikor a rendszer kéri.
   
    ![Webalkalmazás-Proxy konfigurálása a StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

A webalkalmazás-proxy van konfigurálva, és engedélyezni kell.

## <a name="enable-web-proxy"></a>Webalkalmazás-proxy engedélyezése

Webalkalmazás-proxy alapértelmezés szerint le van tiltva. Miután konfigurálta a webes proxykiszolgáló beállításait a StorSimple eszköz, a Windows PowerShell-lel használatával engedélyezze a webproxy beállításai.

> [!NOTE]
> **Ez a lépés nincs szükség, ha a telepítővarázsló segítségével webalkalmazás-proxy konfigurálása. Webalkalmazás-proxy automatikusan alapértelmezés szerint engedélyezve van a telepítő varázsló munkamenet után.**


Windows PowerShell-lel engedélyezése az eszközön a webproxy hajtsa végre a következő lépéseket:

#### <a name="to-enable-web-proxy"></a>Webalkalmazás-proxy engedélyezése
1. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**. Amikor a rendszer kéri, adja meg a **eszköz rendszergazdai jelszava**. Az alapértelmezett jelszó `Password1`.
2. A parancssorba írja be:
   
    `Enable-HcsWebProxy`
   
    Most már engedélyezte a webproxy konfigurálása a StorSimple eszköz.
   
    ![Webalkalmazás-Proxy konfigurálása a StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Webes proxykiszolgáló beállításainak megtekintéséhez az Azure-portálon

A webproxy beállításai vannak konfigurálva a Windows PowerShell felületen, és nem módosítható a portálon. Azonban megtekintheti a konfigurált beállítások a portálon. A következő lépésekkel webproxy megtekintéséhez.

#### <a name="to-view-web-proxy-settings"></a>Webes proxykiszolgáló beállításainak megtekintéséhez
1. Navigáljon a **StorSimple Device Manager szolgáltatás > eszközök**. Válassza ki, és kattintson arra az eszközre, és folytassa a **eszközbeállítások > hálózati**.

    ![Kattintson a hálózat](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. Az a **hálózati beállításai** panelen kattintson a **webalkalmazás-proxy** csempére.

    ![Kattintson a webalkalmazás-proxy](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. Az a **webalkalmazás-proxy** panelt, tekintse át a konfigurált webes proxykiszolgáló beállításait a StorSimple eszköz.
   
    ![Webalkalmazás-proxy beállítások megjelenítése](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Webproxy konfigurálásakor felmerülő hibák

Ha a webproxy beállításai helytelenül vannak konfigurálva, hibaüzenetek jelennek meg a felhasználó a Windows PowerShell a StorSimple. Az alábbi táblázat ismerteti, ezeket a hibaüzeneteket, a lehetséges okairól és a javasolt művelet.

| Sorszám. | HRESULT hibakód | Probléma lehetséges kiváltó okai | Javasolt művelet |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Parancs futtatása a passzív vezérlőből, és nem tud kommunikálni az aktív vezérlőhöz. |Futtassa a parancsot az aktív vezérlőhöz. A parancs futtatásához a passzív vezérlőből, javítsa ki a kapcsolat passzív aktív vezérlőhöz. Microsoft Support kell eljárniuk, ha a kapcsolat megszakad. |
| 2. |0x800710dd - művelet azonosító nem érvényes |Proxy-beállítások nem támogatottak a StorSimple-felhő készüléken. |Proxy-beállítások nem támogatottak a StorSimple-felhő készüléken. Ezek a fizikai StorSimple eszközön csak konfigurálható. |
| 3. |0x80070057 - paraméter érvénytelen |A proxybeállítások megadott paraméterek egyike érvénytelen. |Az URI azonosító nincs megadva, a megfelelő formátumban. Használja a következő formátumot:`http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706BA jelű - RPC-kiszolgáló nem érhető el |Okozza-e a következők egyikét:</br></br>Fürt nem működik-e. </br></br>DataPath szolgáltatás nem fut.</br></br>A parancs futtatása a passzív vezérlőről, és nem tud kommunikálni az aktív vezérlőhöz. |Győződjön meg arról, hogy a fürt működik-e, és datapath szolgáltatás fut a Microsoft Support kódolása.</br></br>Futtassa a parancsot az aktív vezérlőhöz. Ha azt szeretné, a parancs futtatásához a passzív vezérlőből, győződjön meg róla, hogy a passzív vezérlő kommunikál az aktív vezérlőhöz. Microsoft Support kell eljárniuk, ha a kapcsolat megszakad. |
| 5. |0X800706be - RPC-hívása sikertelen volt |Fürt nem működik. |Győződjön meg arról, hogy a fürt működik-e Microsoft Support kódolása. |
| 6. |0x8007138f - fürt erőforrás nem található |Platform szolgáltatás fürt erőforrás nem található. Ez akkor fordulhat elő, amikor a telepítés nem volt megfelelő. |Szükség lehet a gyári beállítások visszaállításának az eszközön. Hozzon létre egy platform erőforrást szeretne. Forduljon a Microsoft Support további lépéseket. |
| 7. |0x8007138c - fürt erőforrás nincs online állapotban |Platform vagy datapath fürt erőforrás nincs online állapotban. |Forduljon a Microsoft Support annak biztosítására, hogy a datapath és platform szolgáltatás erőforrás online-e. |

> [!NOTE]
> * A fenti listába hibaüzenetek nincs teljes körű.
> * Webproxy beállításai kapcsolatos hibákat nem jelenik meg az Azure portálon, a StorSimple Device Manager szolgáltatásban. A webalkalmazás-proxy probléma van a konfiguráció befejezése után, ha az eszköz állapotát módosul **Offline** a klasszikus portálon. |}

## <a name="next-steps"></a>Következő lépések
* Ha probléma merül fel az eszköz üzembe helyezése vagy webes proxy beállításainak konfigurálása során tapasztal, tekintse meg a [hibaelhárítása a StorSimple eszköztelepítő](storsimple-troubleshoot-deployment.md).
* További információk a StorSimple Device Manager szolgáltatás használata, [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

