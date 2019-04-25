---
title: A StorSimple 8000 sorozatú eszköz webalkalmazás-proxy beállítása |} A Microsoft Docs
description: 'Útmutató: a StorSimple-eszköz webproxy-beállításainak konfigurálása a storsimple-höz készült Windows PowerShell segítségével.'
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: be5719d2c383c838ef70c6862c1055c3374e05e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362447"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>A StorSimple eszköz webproxy konfigurálása

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti a storsimple-höz készült Windows PowerShell segítségével a konfigurálása és a StorSimple-eszköz webproxy-beállításainak megjelenítése. A webproxy beállításai a StorSimple-eszköz segítségével a felhőbe való kommunikáció során. Proxy-webkiszolgáló hozzáadása egy másik réteget jelent, obsahu filtrování gyorsítótár egyszerű sávszélességre van szükség, vagy az analytics még akkor is kapcsolatban szolgál.

Az útmutató az ebben az oktatóanyagban csak a StorSimple 8000 sorozatú fizikai eszközök vonatkozik. A StorSimple Cloud Appliance (8010 és 8020) a webproxy konfigurálása nem támogatott.

Webalkalmazás-proxy van egy _választható_ a StorSimple eszköz konfigurációs. Webalkalmazás-proxy csak a Windows PowerShell használatával konfigurálhatja a storsimple-höz készült. A konfiguráció a következőképpen történik egy kétlépéses folyamat:

1. Először konfigurálja a webproxy beállításai a telepítővarázsló vagy a Windows Powershellen keresztül a StorSimple-parancsmagok.
2. Ezután engedélyezze a StorSimple-parancsmagok Windows Powershellen keresztül konfigurált webes proxybeállításait.

A webproxy konfigurálása befejezése után megtekintheti a konfigurált webes proxykiszolgáló beállításait a Microsoft Azure StorSimple-Eszközkezelő szolgáltatás és a Windows PowerShell storsimple-höz készült.

Ebben az oktatóanyagban elolvasásával fogja tudni:

* Webalkalmazás-proxy telepítése varázsló és a parancsmagok segítségével konfigurálhatja.
* Webalkalmazás-proxy parancsmagok használatával engedélyezheti.
* Megtekintheti a webproxy beállításai az Azure Portalon.
* Webproxy konfigurálása során felmerülő hibák elhárítása.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Storsimple-höz készült Windows PowerShell segítségével a webalkalmazás-proxy konfigurálása

A következők egyikét használja, konfigurálja a webproxy beállításai:

* A telepítő varázsló végigvezeti a konfigurációs lépéseket.
* A storsimple-höz készült Windows PowerShell-parancsmagok.

Mindkét módszerhez a következő szakaszokban a következő cikkben.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>A beállítási varázslóban a webproxy konfigurálása

Használja a varázsló végigvezeti a webalkalmazás-proxy konfigurációs lépései. A következő lépésekkel a webproxy konfigurálása az eszközön.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>A beállítási varázslóban a webalkalmazás-proxy konfigurálása

1. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel** , és adja meg a **eszköz rendszergazdai jelszava**. Írja be a következő parancsot a telepítő varázsló munkamenet indításához:
   
    `Invoke-HcsSetupWizard`
2. Ha először használja a telepítővarázsló számára az eszköz regisztrálása, konfigurálja a szükséges hálózati beállításokat, amíg el nem éri a webproxy konfigurálása szeretne. Ha az eszköz már regisztrálva van, fogadja el a konfigurált hálózati beállításokat, amíg el nem éri a webproxy konfigurálása. A telepítővarázslóban, amikor konfigurálja a webproxy beállításai kéri, írja be a **Igen**.
3. A a **webes Proxy URL-címe**, adja meg az IP-cím vagy teljes tartománynevét (FQDN), a proxy-webkiszolgáló és a TCP-port száma, amelyek az eszköz a felhőn ügyfélböngészőkkel szeretné. Használja a következő formátumot:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Alapértelmezés szerint a 8080-as TCP-port száma van megadva.
4. Válassza ki a hitelesítéstípust, **NTLM**, **alapszintű**, vagy **None**. Alapszintű van a legkevésbé biztonságos hitelesítés esetében a proxykiszolgáló-konfigurációt. NT LAN Manager (NTLM) egy rendkívül biztonságos és összetett hitelesítési protokoll, amely egy háromutas üzenetkezelő rendszer (néha négy további integritás kötelező esetén) a felhasználó hitelesítéséhez. Az alapértelmezett hitelesítési NTLM. További információkért lásd: [alapszintű](http://hc.apache.org/httpclient-3.x/authentication.html) és [NTLM-hitelesítés](http://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **A StorSimple-Eszközkezelő szolgáltatásban az eszköz figyelési diagramok nem működnek, ha ez a alapvető, vagy az NTLM-hitelesítés engedélyezve van az eszköz a proxykiszolgáló beállításait. A figyelési diagramok működik győződjön meg arról, hogy a hitelesítés a NONE értékre van állítva kell.**
  
5. Ha engedélyezte a hitelesítést, adjon meg egy **webes proxykiszolgáló felhasználónév** és a egy **webes Proxy jelszava**. Is kell a jelszavát.
   
    ![Configure Web Proxy On StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Ha az eszköz első alkalommal regisztrál, a regisztráció folytatásához. Ha az eszköz már regisztrálva van, a varázsló kilép. A konfigurált beállítások lesznek mentve.

Webalkalmazás-proxy engedélyezve van. Folytassa a [engedélyezése a webalkalmazás-proxy](#enable-web-proxy) lépést, és lépjen közvetlenül [megtekintése a webproxy beállításai az Azure Portalon](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>A webproxy konfigurálása Windows PowerShell StorSimple-parancsmagok

Konfigurálja a webproxy beállításai másik módszere van a Windows PowerShell StorSimple-parancsmagok használatával. A következő lépésekkel a webproxy konfigurálása.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>A webalkalmazás-proxyn keresztül parancsmagok konfigurálása
1. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel**. Amikor a rendszer kéri, adja meg a **eszköz rendszergazdai jelszava**. Az alapértelmezett jelszó az `Password1`.
2. A parancssorba írja be a következőt:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Adja meg, és erősítse meg a jelszót, amikor a rendszer kéri.
   
    ![Configure Web Proxy On StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

A webalkalmazás-proxy van konfigurálva, és engedélyezni kell.

## <a name="enable-web-proxy"></a>Webalkalmazás-proxy engedélyezése

Webalkalmazás-proxy alapértelmezés szerint le van tiltva. Miután konfigurálta a webproxy beállításai a StorSimple eszközön, a storsimple-höz készült Windows PowerShell használatával engedélyezze a webproxy beállításai.

> [!NOTE]
> **Ebben a lépésben nincs szükség, ha a varázsló a webproxy konfigurálása. Webalkalmazás-proxy automatikusan alapértelmezés szerint engedélyezve van a telepítő varázsló munkamenet után.**


Windows PowerShell-bővítménye engedélyezése az eszközön a webalkalmazás-proxy hajtsa végre az alábbi lépéseket:

#### <a name="to-enable-web-proxy"></a>Webalkalmazás-proxy engedélyezése
1. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel**. Amikor a rendszer kéri, adja meg a **eszköz rendszergazdai jelszava**. Az alapértelmezett jelszó az `Password1`.
2. A parancssorba írja be a következőt:
   
    `Enable-HcsWebProxy`
   
    Most már engedélyezte a webproxy konfigurálása a StorSimple eszközön.
   
    ![Configure Web Proxy On StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Megtekintheti a webproxy beállításai az Azure Portalon

A webproxy beállításai vannak konfigurálva a Windows PowerShell felületen, és nem módosítható a portálon. Azonban megtekintheti a konfigurált beállítások a portálon. A következő lépésekkel megtekintéséhez a webalkalmazás-proxy.

#### <a name="to-view-web-proxy-settings"></a>Webes proxy beállításainak megtekintése
1. Navigáljon a **StorSimple-Eszközkezelő szolgáltatás > eszközök**. Válassza ki, és kattintson egy eszközre, és folytassa a **eszközbeállítások > hálózati**.

    ![Kattintson a hálózat](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. Az a **hálózati beállítások** panelen kattintson a **webalkalmazás-proxy** csempére.

    ![Kattintson a webalkalmazás-proxy](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. Az a **webalkalmazás-proxy** panelen tekintse át a konfigurált webes proxykiszolgáló beállításait a StorSimple-eszköz.
   
    ![Nézet webproxy beállításai](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Hibák a webproxy konfigurálása során

Ha a webproxy beállításai helytelenül vannak konfigurálva, hibaüzenetek jelennek meg a felhasználó a Windows PowerShell storsimple-höz készült. Az alábbi táblázat ismerteti azokat a hibaüzenetek, azok okát és a javasolt műveleteket.

| Soros nem. | HRESULT hiba kódja | Probléma lehetséges kiváltó okai | Javasolt művelet |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |A passzív vezérlő parancsot futtatja, és nem tud kommunikálni az aktív vezérlőn. |Az aktív vezérlőn futtassa a parancsot. Futtassa a parancsot a passzív vezérlő, hogy el kell hárítania a kapcsolat a passzív és aktív vezérlő. Ha a kapcsolat megszakad, Support kell eljárniuk. |
| 2. |0x800710dd - műveletazonosító érvénytelen nem |Proxy beállításai nem támogatottak a StorSimple Cloud Appliance eszközzel. |Proxy beállításai nem támogatottak a StorSimple Cloud Appliance eszközzel. Ezek csak a StorSimple fizikai eszköz konfigurálható. |
| 3. |0x80070057 - paraméter érvénytelen |A proxybeállítások megadott paraméterek egyike érvénytelen. |Nincs megadva az URI-t a megfelelő formátumban. Használja a következő formátumot: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706BA - RPC-kiszolgáló nem érhető el |Okozza-e az alábbi lehetőségek közül:</br></br>Fürt nem áll be. </br></br>DataPath szolgáltatás nem fut.</br></br>A parancs futtatása a passzív vezérlő, és nem tud kommunikálni az aktív vezérlőn. |Győződjön meg arról, hogy a fürt működik és datapath szolgáltatás fut a Microsoft Support léphet.</br></br>Az aktív vezérlőn futtassa a parancsot. Ha meg szeretné futtassa a parancsot a passzív vezérlő, biztosítania kell, hogy a passzív vezérlő képes-e kommunikálni az aktív vezérlőn. Ha a kapcsolat megszakad, Support kell eljárniuk. |
| 5. |0X800706be - RPC-hívása sikertelen volt |Fürt nem működik. |Győződjön meg arról, hogy a fürt működik a Microsoft Support léphet. |
| 6. |0x8007138f - fürt az erőforrás nem található |Platform szolgáltatás fürt erőforrás nem található. Ez akkor fordulhat elő, amikor a telepítés nem volt megfelelő. |Szükség lehet, hogy visszaállítsa a gyári beállításokat az eszközön. Szükség lehet a platform-erőforrás létrehozásához. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától. |
| 7. |0x8007138c - fürt erőforrás nincs online állapotban |Platform vagy datapath fürt erőforrásai nem érhetők el. |Forduljon a Microsoft Support, győződjön meg arról, hogy online állapotban-e az datapath és a platform-szolgáltatási erőforrásnak. |

> [!NOTE]
> * Hibaüzenetek a fenti lista tehát nem tekinthető teljesnek.
> * A webproxy beállításai kapcsolódó hibák nem jelenik meg az Azure Portalon a StorSimple-Eszközkezelő szolgáltatásban. A webalkalmazás-proxy probléma van a konfiguráció befejezése után, ha az eszköz állapota változik **Offline** a klasszikus portálon. |}

## <a name="next-steps"></a>További lépések
* Problémákat tapasztal az eszköz üzembe helyezésének vagy webes proxy beállításainak konfigurálása során, tekintse meg [a StorSimple eszköz üzembe helyezési hibáinak elhárítása](storsimple-troubleshoot-deployment.md).
* További információk a StorSimple-Eszközkezelő szolgáltatás használata, [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

