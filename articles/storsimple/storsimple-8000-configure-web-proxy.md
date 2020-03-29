---
title: Webproxy beállítása a StorSimple 8000 sorozatú eszközhöz | Microsoft dokumentumok
description: Megtudhatja, hogy a StorSimple-eszköz webproxy-beállításainak konfigurálásához hogyan konfigurálhatja a StorSimple-eszköz webproxy-beállításait a Windows PowerShell for StorSimple használatával.
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
ms.openlocfilehash: 956cf45eb9e246f2e1f917f2bf487ac14deba90e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65204250"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Webproxy konfigurálása a StorSimple eszközhöz

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag bemutatja, hogyan használhatja a Windows PowerShell for StorSimple-t a StorSimple-eszköz webproxy-beállításainak konfigurálásához és megtekintéséhez. A webproxy-beállításokat a StorSimple eszköz használja a felhővel való kommunikáció során. A webproxy-kiszolgáló egy újabb biztonsági réteg hozzáadására, a tartalom szűrésére, a gyorsítótárra, a sávszélesség-követelmények enyhítésére vagy akár az elemzéssel kapcsolatos segítségre szolgál.

Az oktatóanyagban szereplő útmutató csak a StorSimple 8000 sorozatú fizikai eszközökre vonatkozik. Web proxy konfiguráció nem támogatott a StorSimple Cloud Appliance (8010 és 8020).

A webproxy a StorSimple-eszköz _választható_ konfigurációja. A webproxy t csak a StorSimple Windows PowerShell használatával konfigurálhatja. A konfiguráció két lépésből áll a következőképpen:

1. Először konfigurálja a webproxy-beállításokat a telepítő varázsló vagy a Windows PowerShell StorSimple parancsmagok.
2. Ezután engedélyezze a konfigurált webproxy-beállításokat a Windows PowerShell storSimple parancsmagok.

A webproxy-konfiguráció befejezése után megtekintheti a konfigurált webproxy-beállításokat a Microsoft Azure StorSimple Eszközkezelő szolgáltatásban és a Windows PowerShell for StorSimple szolgáltatásban.

Elolvasása után ez a bemutató, akkor képes lesz arra, hogy:

* Konfigurálja a webproxyt a telepítővarázsló és a parancsmagok használatával.
* Webproxy engedélyezése parancsmagok használatával.
* Tekintse meg a webproxy-beállításokat az Azure Portalon.
* Hibák elhárítása a webproxy-konfiguráció során.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Webproxy konfigurálása a Windows PowerShell segítségével a StorSimple számára

Az alábbi beállítások egyikével konfigurálhatja a webproxy-beállításokat:

* A telepítő varázsló végigvezeti a konfigurációs lépéseken.
* Parancsmagok a Windows PowerShell storsimple.Cmdlets in Windows PowerShell for StorSimple.

E módszerek mindegyikét a következő szakaszok ismertetik.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Webproxy konfigurálása a telepítővarázslón keresztül

A beállítási varázsló segítségével végigvezetheti a webproxy-konfiguráció lépésein. Hajtsa végre az alábbi lépéseket a webproxy konfigurálásához az eszközön.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Webproxy konfigurálása a telepítővarázslón keresztül

1. A soros konzol menüjében **Log in with full access** válassza az 1. **device administrator password** A telepítővarázsló munkamenetének elindításához írja be a következő parancsot:
   
    `Invoke-HcsSetupWizard`
2. Ha ez az első alkalom, hogy a telepítővarázslót használja az eszköz regisztrációjához, az összes szükséges hálózati beállítást be kell állítania, amíg el nem éri a webproxy-konfigurációt. Ha az eszköz már regisztrálva van, fogadja el az összes konfigurált hálózati beállítást, amíg el nem éri a webproxy-konfigurációt. A telepítővarázslóban, amikor a rendszer a webproxy-beállítások konfigurálását kéri, írja be az **Igen**parancsot.
3. A **webproxy URL-címéhez**adja meg a webproxy-kiszolgáló IP-címét vagy teljes tartománynevét (FQDN), valamint azt a TCP-portszámot, amelyet az eszköznek használnia kell a felhővel való kommunikáció során. Használja az alábbi formátumot:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Alapértelmezés szerint a TCP-port száma 8080.
4. Válassza ki a hitelesítéstípusát **NTLM,** **Basic**vagy **Nincs**néven. Az alapszintű a proxykiszolgáló konfigurációjának legkevésbé biztonságos hitelesítése. Az NT LAN Manager (NTLM) egy rendkívül biztonságos és összetett hitelesítési protokoll, amely háromutas üzenetküldő rendszert használ (néha négyet, ha további integritásra van szükség) a felhasználó hitelesítéséhez. Az alapértelmezett hitelesítés az NTLM. További információ: [Basic](https://hc.apache.org/httpclient-3.x/authentication.html) and [NTLM authentication](https://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **A StorSimple Eszközkezelő szolgáltatásban az eszközfigyelési diagramok nem működnek, ha az alapszintű vagy NTLM-hitelesítés engedélyezve van az eszköz proxykiszolgáló-konfigurációjában. Ahhoz, hogy a figyelési diagramok működjenek, biztosítania kell, hogy a hitelesítés nincs legyen.**
  
5. Ha engedélyezte a hitelesítést, adja meg **a webproxy-felhasználónevet** és a **webproxy-jelszót.** A jelszót is meg kell erősítenie.
   
    ![Webproxy konfigurálása StorSimple eszközön1](./media/storsimple-configure-web-proxy/IC751830.png)

Ha első alkalommal regisztrálja az eszközt, folytassa a regisztrációval. Ha az eszköz már regisztrálva van, a varázsló kilép. A program menti a beállított beállításokat.

A webproxy engedélyezve van. A Webproxy engedélyezése lépést [kihagyhatja,](#enable-web-proxy) és közvetlenül [az Azure Portalon megtekintheti a webproxy-beállításokat.](#view-web-proxy-settings-in-the-azure-portal)

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Webproxy konfigurálása a Windows PowerShell használatával StorSimple-parancsmagokhoz

A webproxy-beállítások konfigurálásának másik módja a Windows PowerShell storSimple-parancsmagok. A webproxy konfigurálásához hajtsa végre az alábbi lépéseket.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Webproxy konfigurálása parancsmagokon keresztül
1. A soros konzol menüjében **Log in with full access**válassza az 1. Amikor a rendszer kéri, adja meg az **eszköz rendszergazdai jelszavát.** Az alapértelmezett `Password1`jelszó a .
2. A parancssorba írja be a következőt:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Adja meg és erősítse meg a jelszót, amikor a rendszer kéri.
   
    ![Webproxy konfigurálása StorSimple eszközön3](./media/storsimple-configure-web-proxy/IC751831.png)

A webproxy most konfigurálva van, és engedélyezni kell.

## <a name="enable-web-proxy"></a>Webproxy engedélyezése

A webproxy alapértelmezés szerint le van tiltva. Miután konfigurálta a webproxy beállításait a StorSimple-eszközön, a Windows PowerShell for StorSimple használatával engedélyezze a webproxy-beállításokat.

> [!NOTE]
> **Ez a lépés nem szükséges, ha a telepítővarázslóval konfigurálta a webproxyt. A webproxy alapértelmezés szerint automatikusan engedélyezve van a telepítővarázsló munkamenete után.**


A webproxy engedélyezéséhez hajtsa végre az alábbi lépéseket a Windows PowerShell for StorSimple rendszerben:

#### <a name="to-enable-web-proxy"></a>Webproxy engedélyezése
1. A soros konzol menüjében **Log in with full access**válassza az 1. Amikor a rendszer kéri, adja meg az **eszköz rendszergazdai jelszavát.** Az alapértelmezett `Password1`jelszó a .
2. A parancssorba írja be a következőt:
   
    `Enable-HcsWebProxy`
   
    Most engedélyezte a webproxy-konfigurációt a StorSimple eszközön.
   
    ![Webproxy konfigurálása StorSimple eszközön4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Webproxy-beállítások megtekintése az Azure Portalon

A webproxy-beállítások a Windows PowerShell-felületen keresztül konfigurálhatók, és nem módosíthatók a portálon belülről. Ezeket a beállításokat azonban megtekintheti a portálon. A webproxy megtekintéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-view-web-proxy-settings"></a>Webproxy-beállítások megtekintése
1. Nyissa meg a **StorSimple Eszközkezelő szolgáltatás > Eszközök**. Jelöljön ki egy eszközt, majd kattintson az **Eszközbeállítások > Hálózat**elemre.

    ![Kattintson a Hálózat gombra](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. A **Hálózati beállítások** panelen kattintson a **webproxy** csempére.

    ![Kattintson a webproxyra](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. A **webproxy** panelen tekintse át a storSimple-eszközön beállított webproxy-beállításokat.
   
    ![Webproxy-beállítások megtekintése](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Hibák a webproxy-konfiguráció során

Ha a webproxy beállításai helytelenül vannak konfigurálva, hibaüzenetek jelennek meg a felhasználó számára a Windows PowerShell for StorSimple rendszerben. Az alábbi táblázat ismerteti a hibaüzenetek némelyikét, azok valószínű okait és az ajánlott műveleteket.

| Sorozatszám | HRESULT hibakód | Lehetséges kiváltó ok | Javasolt művelet |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |A parancs a passzív vezérlőből fut, és nem tud kommunikálni az aktív vezérlővel. |Futtassa a parancsot az aktív vezérlőn. A parancs passzív vezérlőből történő futtatásához ki kell javítania a kapcsolatot a passzívról az aktív vezérlőre. Ha a kapcsolat megszakadt, meg kell támadnia a Microsoft támogatási szolgálatát. |
| 2. |0x800710dd - A műveletazonosító érvénytelen |A StorSimple Cloud Appliance proxybeállításai nem támogatottak. |A StorSimple Cloud Appliance proxybeállításai nem támogatottak. Ezek csak storSimple fizikai eszközön konfigurálhatók. |
| 3. |0x80070057 - Érvénytelen paraméter |A proxybeállításokhoz megadott paraméterek egyike érvénytelen. |Az URI nem megfelelő formátumban van megadva. A következő formátumot használja:`http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - RPC szerver nem érhető el |A kiváltó ok az alábbiak egyike:</br></br>A fürt nem áll fel. </br></br>A Datapath szolgáltatás nem fut.</br></br>A parancs passzív vezérlőből fut, és nem tud kommunikálni az aktív vezérlővel. |Vegye igénybe a Microsoft támogatási szolgálatát annak érdekében, hogy a fürt működik, és a datapath szolgáltatás futjon.</br></br>Futtassa a parancsot az aktív vezérlőből. Ha a parancsot a passzív vezérlőből szeretné futtatni, gondoskodnia kell arról, hogy a passzív vezérlő képes legyen kommunikálni az aktív vezérlővel. Ha a kapcsolat megszakadt, meg kell támadnia a Microsoft támogatási szolgálatát. |
| 5. |0x800706be – Az RPC-hívás sikertelen |A fürt nem. |Vegye igénybe a Microsoft támogatási szolgálatát, és győződjön meg arról, hogy a fürt fel van kapcsolva. |
| 6. |0x8007138f – a fürterőforrás nem található |A platformszolgáltatás fürterőforrása nem található. Ez akkor fordulhat elő, ha a telepítés nem volt megfelelő. |Előfordulhat, hogy gyári beállítások visszaállítását kell végrehajtania az eszközön. Előfordulhat, hogy létre kell hoznia egy platformerőforrást. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától. |
| 7. |0x8007138c – A fürterőforrás nem online |A platform- vagy datapath-fürterőforrások nincsenek online állapotban. |Lépjen kapcsolatba a Microsoft támogatási szolgálatával, és győződjön meg arról, hogy a datapath és a platformszolgáltatás erőforrása online állapotban van. |

> [!NOTE]
> * A hibaüzenetek fenti listája nem teljes körű.
> * A webproxy-beállításokkal kapcsolatos hibák nem jelennek meg az Azure Portalon a StorSimple Eszközkezelő szolgáltatásban. Ha a konfiguráció befejezése után probléma merül fel a webproxyval kapcsolatban, az eszköz állapota **offline** állapotra változik a klasszikus portálon.|

## <a name="next-steps"></a>Következő lépések
* Ha bármilyen problémát tapasztal az eszköz telepítése vagy a webproxy-beállítások konfigurálása során, olvassa el [a StorSimple-eszköz telepítésének elhárítása című témakört.](storsimple-troubleshoot-deployment.md)
* A StorSimple Eszközkezelő szolgáltatás használatáról a [StorSimple Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez című](storsimple-8000-manager-service-administration.md)oldalon olvashat.

