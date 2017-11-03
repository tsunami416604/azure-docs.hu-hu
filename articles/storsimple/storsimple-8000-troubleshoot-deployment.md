---
title: "A StorSimple 8000 series telepítési problémák elhárításához |} Microsoft Docs"
description: "Ismerteti, hogyan diagnosztizálhatja és javíthatja ki a StorSimple első telepítésekor előforduló hibákat."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: d6642231bf9d012b0015902c82d2bfde3b86285c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>A StorSimple eszköz telepítési problémák elhárításához
## <a name="overview"></a>Áttekintés
Ez a cikk hasznos elhárításához nyújt útmutatást a Microsoft Azure StorSimple üzembe helyezésére. Kapcsolatos gyakori hibák, lehetséges okok, és a StorSimple konfigurálásakor az esetleg előforduló problémák megoldását segítő javasolt lépéseket ismerteti. 

Ezek az információk a StorSimple 8000 series fizikai eszköz és a StorSimple felhő készülék is vonatkoznak.

> [!NOTE]
> Eszköz konfigurációs kapcsolatos problémák, előfordulhat, hogy szembesülhetnek akkor fordulhat elő, amikor az eszköz üzembe helyezése, először, vagy azok akkor fordulhat elő, később, amikor az eszköz nem működik. Ez a cikk foglalkozik az első központi telepítési problémák elhárításához. Az operatív eszköz hibáinak elhárítása, keresse fel [a diagnosztikai eszköz segítségével az üzemeltetési eszköz hibáinak elhárítása](storsimple-8000-diagnostics.md).

Ez a cikk is ismerteti a StorSimple központi telepítések hibaelhárítási eszközök, és a részletes hibaelhárítási példa.

## <a name="first-time-deployment-issues"></a>Első telepítésekkel kapcsolatos problémákhoz
Ha futtatja a problémát az eszköz az első alkalommal való telepítésekor, vegye figyelembe a következőket:

* Ha egy fizikai eszköz hibaelhárítást, ellenőrizze, hogy a hardver telepítve és leírtak szerint konfigurálta [a StorSimple 8100 eszköz telepítése](storsimple-8100-hardware-installation.md) vagy [a StorSimple 8600 eszköz telepítése](storsimple-8600-hardware-installation.md).
* Ellenőrizze a telepítés előfeltételeit. Győződjön meg arról, hogy rendelkezik-e leírt adatokat a [üzembehelyezési konfigurációs ellenőrzőlista](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Tekintse át a StorSimple kibocsátási megjegyzéseket talál, ha a probléma leírását. A kibocsátási megjegyzések a lehetséges megoldások a ismert telepítési problémákat. 

Eszköz telepítése során a leggyakoribb problémák felhasználók által tapasztalt fordulhat elő, a telepítő varázsló futtatásakor, és ha azok az eszközt regisztrálni kell a Windows PowerShell segítségével a StorSimple. (Használható Windows PowerShell-lel regisztrálásához és konfigurálhatja a StorSimple eszközt. Az eszközregisztráció további információkért lásd: [3. lépés: konfigurálása és a Windows PowerShell segítségével az eszköz regisztrálása a StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Az alábbi szakaszok segítséget nyújtanak az első alkalommal a StorSimple eszköz konfigurálásakor előforduló problémák megoldásához.

## <a name="first-time-setup-wizard-process"></a>Először a telepítő varázsló folyamat
A következő lépésekkel foglalható össze a telepítő varázsló. A telepítő részletes információkért lásd: [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).

1. Futtassa a [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) parancsmag segítségével indítsa el a telepítővarázslót, amely végigvezeti Önt a fennmaradó lépéseit. 
2. A hálózat konfigurálása: a telepítő varázsló lehetővé teszi a DATA 0 hálózati adapter hálózati beállításainak konfigurálása a StorSimple eszköz. Ezek a beállítások a következők:
   * Virtuális IP-cím (VIP), alhálózati maszk és átjáró – a [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) parancsmag végrehajtása a háttérben. A StorSimple eszköz azt konfigurálja az IP-cím, alhálózati maszk és átjáró a DATA 0 hálózati adapterén.
   * Elsődleges DNS-kiszolgáló – a [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) parancsmag végrehajtása a háttérben. Beállítja a StorSimple megoldásban a DNS-beállításait.
   * NTP-kiszolgáló – a [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) parancsmag végrehajtása a háttérben. A StorSimple megoldásban NTP-kiszolgáló beállításait konfigurálja azt.
   * Nem kötelező web-proxy – a [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) parancsmag végrehajtása a háttérben. Beállítja, és lehetővé teszi, hogy a webproxy konfigurálása a StorSimple megoldásban.
3. Állítsa be a jelszavát: a következő lépés az, hogy az eszköz rendszergazdai jelszavának beállítása.
   Az eszköz rendszergazdai jelszava segítségével jelentkezzen be az eszközt. Az eszköz alapértelmezett jelszava: **Password1**.
        
     > [!IMPORTANT]
     > Jelszavak bejegyzése előtt gyűjtött, de csak a sikeresen megtörtént az eszköz regisztrációját követően alkalmazza. Ha hiba történik a jelszót, újra adni a jelszót, amíg a szükséges jelszavakat (bonyolultsági megfeleljen) gyűjtött bekéri.
     
4. Az eszköz regisztrálása: a végső lépés az, hogy az eszközt regisztrálni kell a Microsoft Azure-beli StorSimple Device Manager szolgáltatásban. A regisztráció megköveteli, hogy [Szolgáltatásregisztrációs kulcs lekérése](storsimple-8000-manage-service.md#get-the-service-registration-key) Azure-portálról, és adja meg a varázslóban. **Miután az eszköz regisztrálása sikeres volt, a szolgáltatásadat-titkosítási kulcs Önnek valósul meg. Mindenképpen tartsa biztonságos helyen a titkosítási kulcsot, mert lesz szükség az összes későbbi eszközök regisztrálása a szolgáltatásban.**

## <a name="common-errors-during-device-deployment"></a>Eszköz telepítése során előforduló hibákat
Az alábbi táblázatok tartalmazzák a közös észlelt hibák, hogy előfordulhat, hogy mikor meg:

* Konfigurálja a szükséges hálózati beállításait.
* Nem kötelező web proxy beállításainak konfigurálása.
* Állítsa be az eszköz rendszergazdai jelszava.
* Az eszköz regisztrációját.

## <a name="errors-during-the-required-network-settings"></a>A szükséges hálózati beállításokat hibák
| Nem. | Hibaüzenet | Lehetséges okok | Javasolt művelet |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Ez a parancs csak futtatható az aktív vezérlőhöz. |A passzív vezérlő konfigurációs végrehajtása. |Futtassa a parancsot az aktív vezérlőhöz. További információkért lásd: [azonosíthatja az aktív vezérlőhöz az eszközön](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: Az eszköz nem üzemkész. |Nincsenek a DATA 0 hálózati kapcsolat problémái. |Ellenőrizze a fizikai hálózati kapcsolatot, a DATA 0. |
| 3 |Invoke-HcsSetupWizard: Nincs más rendszerrel, a hálózat IP-címütközést (kivétel HRESULT: 0x80070263). |Az adott DATA 0 IP-cím már egy másik rendszer használatban volt. |Adjon meg egy új IP-cím nincs használatban. |
| 4 |Invoke-HcsSetupWizard: A fürterőforrás nem sikerült. (Kivétel HRESULT: 0x800713AE). |Ismétlődő VIP. Az a megadott IP-cím már használatban van. |Adjon meg egy új IP-cím nincs használatban. |
| 5 |Invoke-HcsSetupWizard: Érvénytelen IPv4-címet. |Az IP-cím formátuma nem megfelelő valósul meg. |Ellenőrizze a formátumát, és adja meg újra az IP-címe. További információkért lásd: [Ipv4-címzési][1]. |
| 6 |Invoke-HcsSetupWizard: Érvénytelen IPv6-címet. |Az IP-cím formátuma nem megfelelő valósul meg. |Ellenőrizze a formátumát, és adja meg újra az IP-címe. További információkért lásd: [IPv6-címzés][2]. |
| 7 |Invoke-HcsSetupWizard: Érhetők el további végpontok végpont. (Kivétel HRESULT: 0x800706D9) |A fürt nem működőképes. |[Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) további lépéseket. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>A választható webproxy beállításai során hibák
| Nem. | Hibaüzenet | Lehetséges okok | Javasolt művelet |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Érvénytelen paraméter (kivétel HRESULT: 0x80070057) |A proxybeállítások megadott paraméterek egyike érvénytelen. |Az URI azonosító nincs megadva, a megfelelő formátumban. Használja a következő formátumot: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: RPC-kiszolgáló nem érhető el (kivétel HRESULT: 0x800706BA jelű) |Okozza-e a következők egyikét:<ol><li>A fürt nincs legfeljebb.</li><li>A passzív tartományvezérlő nem tud kommunikálni az aktív vezérlővel, és a parancs futtatása a passzív vezérlőről.</li></ol> |Attól függően, hogy az alapvető ok:<ol><li>[Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) győződjön meg arról, hogy a fürt működik-e.</li><li>Futtassa a parancsot az aktív vezérlőhöz. Ha azt szeretné, a parancs futtatásához a passzív vezérlőből, akkor győződjön meg arról, hogy a passzív vezérlő kommunikál az aktív vezérlőhöz. Szüksége lesz a [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) Ha a kapcsolat megszakad.</li></ol> |
| 3 |Invoke-HcsSetupWizard: RPC-hívása sikertelen volt (kivétel HRESULT: 0x800706be) |Fürt nem működik. |[Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) győződjön meg arról, hogy a fürt működik-e. |
| 4 |Invoke-HcsSetupWizard: Fürt erőforrás nem található (kivétel HRESULT: 0x8007138f) |A fürt erőforrás nem található. Ez akkor fordulhat elő, amikor a telepítés nem volt megfelelő. |Szükség lehet visszaállítani az eszközt a gyári alapértelmezett beállításokra. [Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) fürt erőforrás létrehozása. |
| 5 |Invoke-HcsSetupWizard: Fürt erőforrás nincs online állapotban (kivétel HRESULT: 0x8007138c) |Fürt erőforrás nincs online állapotban. |[Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) további lépéseket. |

## <a name="errors-related-to-device-administrator-password"></a>Az eszköz rendszergazdai jelszava kapcsolatos hibák
Az alapértelmezett eszköz rendszergazdai jelszava van **jelszó1**. A jelszó lejárati után az első bejelentkezés; ezért szüksége lesz a telepítővarázsló segítségével módosíthatja. Az eszköz regisztrálásakor, először meg kell adnia egy új eszköz rendszergazdai jelszava. 

Győződjön meg arról, hogy a jelszavak az alábbi követelményeknek:

* Az eszköz rendszergazdai jelszavának 8 – 15 karakter között kell lennie.
* Jelszavak 3 az alábbi típusú 4 karaktert kell tartalmaznia:, kisbetű, nagybetű, numerikus és speciális. 
* A jelszó nem lehet ugyanaz, mint a legutóbbi 24 jelszó.

Továbbá ne feledje, hogy a jelszavak évente lejár, és csak a sikeresen az eszköz regisztrálása után módosíthatja. A regisztráció bármilyen okból nem sikerül, a jelszó nem módosítható.

További információt az eszköz rendszergazdai jelszava, [a StorSimple jelszó megváltoztatása a StorSimple Device Manager szolgáltatás használata](storsimple-8000-change-passwords.md).

Hibák a következők közül az eszköz-rendszergazdai és a StorSimple Snapshot Manager jelszavak beállítása során fordulhatnak elő.

| Nem. | Hibaüzenet | Javasolt művelet |
| --- | --- | --- |
| 1 |A jelszó hosszabb a megengedettnél. |Az eszköz rendszergazdai jelszavának 8 – 15 karakter között kell lennie. |
| 2 |A jelszó nem felel meg a szükséges hossza. |Az eszköz rendszergazdai jelszavának 8 – 15 karakter között kell lennie.|
| 3 |A jelszónak kisbetűs karaktert kell tartalmaznia. |Jelszavak 3 az alábbi típusú 4 karakter kell tartalmaznia:, kisbetű, nagybetű, numerikus és speciális. Győződjön meg arról, hogy a jelszó megfelel-e ezek a követelmények. |
| 4 |A jelszónak számokat kell. |Jelszavak 3 az alábbi típusú 4 karakter kell tartalmaznia:, kisbetű, nagybetű, numerikus és speciális. Győződjön meg arról, hogy a jelszó megfelel-e ezek a követelmények. |
| 5 |A jelszónak különleges karaktereket kell tartalmaznia. |Jelszavak 3 az alábbi típusú 4 karakter kell tartalmaznia:, kisbetű, nagybetű, numerikus és speciális. Győződjön meg arról, hogy a jelszó megfelel-e ezek a követelmények. |
| 6 |A jelszónak tartalmaznia kell a következő 4 karakter típusú 3: nagybetűk, a kis, a numerikus és a speciális. |A jelszó nem tartalmaz karaktert szükséges típusú. Győződjön meg arról, hogy a jelszó megfelel-e ezek a követelmények. |
| 7 |A paraméter nem felel meg a megerősítő. |Győződjön meg arról, hogy a jelszó megfelel-e az összes követelményeknek, és hogy helyesen írta be. |
| 8 |A jelszó nem egyezhet meg az alapértelmezett. |Az alapértelmezett jelszó *jelszó1*. Módosítsa ezt a jelszót, az első alkalommal való bejelentkezés után kell. |
| 9 |A megadott jelszó nem egyezik meg a jelszót. Írja be újra a jelszót. |Ellenőrizze a jelszót, és írja be újra. |

Jelszavak, mielőtt az eszköz regisztrálva van, de csak a sikeres regisztrációt követően alkalmazza a rendszer gyűjti. A jelszó-helyreállítási munkafolyamat használatához az eszközt regisztrálni kell.

> [!IMPORTANT]
> Általában egy jelszó alkalmazása sikertelen lesz, ha a szoftver ismételten megpróbálja a gyűjtése a jelszót, amíg az sikeres nem lesz. Ritka esetekben a jelszó nem lehet alkalmazni. Ebben a helyzetben az eszköz regisztrációját, és továbblép, azonban a jelszavak nem fognak megváltozni. Az eszköz rendszergazdai jelszava az Azure-portálon a regisztrálás után módosíthatja.


Visszaállíthatja a jelszót az Azure portálon, a StorSimple eszköz kezelő szolgáltatásával. További információkért látogasson el [módosíthatja az eszköz rendszergazdai jelszava](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Eszközök regisztrációja során hibák
A Microsoft Azure-beli StorSimple Device Manager szolgáltatás segítségével regisztrálja az eszközt. Eszközök regisztrációja során legalább egy, az alábbi problémákat sikerült tapasztal.

| Nem. | Hibaüzenet | Lehetséges okok | Javasolt művelet |
| --- | --- | --- | --- |
| 1 |350027. hiba: Nem sikerült regisztrálni az eszközt a StorSimple eszköz-kezelő használatával. | |Várjon néhány percet, és próbálkozzon újra a művelettel. Ha a probléma továbbra is fennáll, [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 2 |350013. hiba: Hiba történt az eszköz regisztrálásához. A hiba lehetséges oka Szolgáltatásregisztrációs kulcs helytelen. | |Regisztrálja újra az eszközt a helyes regisztrációs kulccsal. További információkért lásd: [Szolgáltatásregisztrációs kulcs lekérése.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |350063. hiba: Hitelesítési átadott StorSimple Device Manager szolgáltatást, de a regisztráció sikertelen. Próbálkozzon újra a művelettel valamivel később. |Ez a hiba azt jelzi, hogy a rendszer megfelelt a hitelesítés és az ACS, de a szolgáltatáshoz a register-hívás sikertelen. Ennek oka lehet egy szórványos hálózati működési hiba eredménye. |Ha a probléma továbbra is fennáll, ellenőrizze [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 4 |350049. hiba: A szolgáltatás nem érhető el regisztrálás során. |Ha a kérés érkezett a szolgáltatás, egy webes kivétel érkezett. Néhány esetben ez lehetséges, hogy első által meghatározott megpróbálná megismételni a műveletet később. |Ellenőrizze az IP-cím és DNS-nevét, és próbálkozzon újra a művelettel. Ha a probléma továbbra is fennáll, [forduljon a Microsoft Support.](storsimple-8000-contact-microsoft-support.md) |
| 5 |350031. hiba: Az eszköz már regisztrálva van. | |Nincs szükség műveletre. |
| 6 |350016. hiba: Nem sikerült regisztrálni. | |Győződjön meg arról, hogy a regisztrációs kulcs helyességéről. |
| 7 |Invoke-HcsSetupWizard: Hiba történt az eszköz; regisztrálása során Ezt okozhatja hibás IP-címe vagy DNS-nevét. A hálózati beállításokat, és próbálkozzon újra. Ha a probléma továbbra is fennáll, [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md). (350050 hiba) |Győződjön meg arról, hogy az eszköz pingelhető-e a külső hálózattal. Ha nem rendelkezik külső hálózati kapcsolat, a regisztráció sikertelen lehet a hibával. Ez a hiba lehet kombinációját az alábbiak közül:<ul><li>Helytelen IP</li><li>Helytelen alhálózati</li><li>Helytelen átjáró</li><li>Helytelen DNS-beállítások</li></ul> |A lépések a [részletes hibaelhárítási példa](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: Az aktuális művelet [0x1FBE2] belső szolgáltatási hiba miatt sikertelen volt. Várjon egy kis ideig, majd ismételje meg a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft Support. |Ez egy általános hiba lépett fel az összes felhasználó nem látható hiba a szolgáltatás vagy az ügynök. Ennek leggyakoribb oka lehet, hogy az ACS-hitelesítés sikertelen volt. A lehetséges a hiba oka, hogy az NTP-kiszolgáló konfigurációs problémák és idő alapján az eszköz nem megfelelően van beállítva. |Javítsa ki a idő (Ha problémák vannak), és ismételje meg a regisztrációs műveletet. Ha a Set-HcsSystem - időzóna paranccsal módosítsa az időzónát, szókezdő időzónája (például "csendes-óceáni téli idő").  Ha a probléma tartósan fennáll, [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) további lépéseket. |
| 9 |Figyelmeztetés: Az eszköz nem aktiválható. Az eszköz-rendszergazdai és a StorSimple Snapshot Manager jelszavát nem módosult. |A regisztráció sikertelen lesz, ha az eszköz-rendszergazdai és a StorSimple Snapshot Manager jelszavát nem változnak. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>StorSimple központi telepítések hibaelhárítási eszközök
StorSimple hibaelhárítása a StorSimple megoldás használatával több eszközt biztosít. Ezek a következők:

* Csomagok és eszköznaplók támogatja.
* A parancsmagok kifejezetten a hibaelhárításhoz.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Csomagok és a rendelkezésre álló eszköznaplók hibaelhárítás támogatása
Egy támogatási csomag tartalmazza a megfelelő naplókat, amely segíthet az eszközök kapcsolatos problémák elhárítása a Microsoft Support csoport. A Windows PowerShell-lel segítségével egy titkosított támogatási csomagot, majd megosztása a technikai tanácsadási csoporthoz.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>A naplók és a támogatási csomag tartalmának megtekintése
1. Egy támogatási csomag létrehozása a Windows PowerShell-lel segítségével [létrehozása és kezelése egy támogatási csomag](storsimple-8000-create-manage-support-package.md).
2. Töltse le a [visszafejtési parancsfájl](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) helyileg az ügyfélszámítógépen.
3. Ezzel [lépésről lépésre](storsimple-8000-create-manage-support-package.md#edit-a-support-package) megnyitásához, és a támogatási csomag visszafejtéséhez.
4. A visszafejtett támogatási csomag naplófájlok etw/etvx formátumban vannak. Ezek a fájlok megtekintéséhez a Windows Eseménynapló a következőket végezheti el:
   
   1. Futtassa a **eventvwr** parancsot a Windows-ügyfeleken. Ekkor elindul az Eseménynapló használatával.
   2. Az a **műveletek** ablaktáblán kattintson a **naplófájl megnyitása** és a naplófájlok etvx/etw-formátumban (a támogatási csomag) mutasson. Most már megtekintheti a fájlt. A fájl megnyitása után kattintson a jobb gombbal, és mentse a fájlt szöveg.
      
      > [!IMPORTANT]
      > Használhatja a **Get-WinEvent** nyissa meg ezeket a fájlokat a Windows PowerShell-parancsmagot. További információkért lásd: [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) a a Windows PowerShell-parancsmag ismertető dokumentációjában.
     
5. Ha a naplók nyissa meg az eseménynaplóban, keresse meg a következő naplók kapcsolódnak, amely tartalmazza az eszköz konfigurációjával kapcsolatos problémák:
   
   * hcs_pfconfig/műveleti napló
   * hcs_pfconfig/Config
6. A naplófájlok keresse meg a parancsmagok, a telepítővarázsló által meghívott kapcsolódó karakterláncok. Lásd: [először a telepítő varázsló folyamat](#first-time-setup-wizard-process) ezek a parancsmagok listáját.
7. Ha nem tudunk kideríthesse, mi okozta a problémát, akkor [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) további lépéseket. Az alábbi témakörben található lépésekkel [hozzon létre egy támogatási kérést](storsimple-8000-contact-microsoft-support.md#create-a-support-request) amikor Microsoft Support szolgálatához segítségért.

## <a name="cmdlets-available-for-troubleshooting"></a>A hibaelhárításhoz elérhető parancsmagok
A következő Windows PowerShell-parancsmagok használatával észleli a kapcsolódási hibákat.

* `Get-NetAdapter`: Ez a parancsmag észleléséhez használni a hálózati adapterek állapotát.
* `Test-Connection`: Ez a parancsmag használata a hálózaton kívül és belül a hálózati kapcsolat ellenőrzése.
* `Test-HcsmConnection`: A parancsmag segítségével ellenőrizze a kapcsolatot a sikeresen regisztrált egy eszközt.
* `Sync-HcsTime`: Ez a parancsmag megjelenítéséhez eszköz és használja az NTP-kiszolgáló az időszinkronizálást.
* `Enable-HcsPing`és `Disable-HcsPing`: ezek a parancsmagok segítségével lehetővé kell Pingelje meg a hálózati adapterek a StorSimple eszköz. Alapértelmezés szerint a StorSimple hálózati illesztők válaszol a ping-kérelmekre.
* `Trace-HcsRoute`: Ez a parancsmag használata olyan útvonal eszköz. A végső rendeltetési módja az egyes útválasztók egy meghatározott időtartamra vonatkozóan csomagokat küld, és majd kiszámítja az egyes csomagok eredmény. Mivel `Trace-HcsRoute` fokának csomagvesztés bármely adott útválasztó vagy a hivatkozás, jeleníti meg, mely útválasztók tudja, vagy előfordulhat, hogy a hivatkozások hálózati problémát okoz.
* `Get-HcsRoutingTable`: Ezt a parancsmagot használja a helyi IP-útválasztási táblázat megjelenítéséhez.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>A Get-NetAdapter parancsmaggal hibaelhárításához
Hálózati illesztők első eszköz üzembe helyezés konfigurálásakor hardver állapota nem érhető el a StorSimple Device Manager szolgáltatás felhasználói felületi, mert az eszköz még nincs regisztrálva a szolgáltatásban. Emellett a **hardver állapotának** panel előfordulhat, hogy nem mindig tükrözi megfelelően az eszköz állapotának különösen akkor, ha a szinkronizálási szolgáltatást érintő problémák vannak. Ezekben a helyzetekben használhatja a `Get-NetAdapter` parancsmag használatával határozza meg a hálózati adapterek állapotát.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>A hálózati adapterek listájának megtekintéséhez az eszközön
1. Indítsa el a Windows PowerShell a StorSimple, és írja be `Get-NetAdapter`. 
2. Használja a kimenetét a `Get-NetAdapter` parancsmag és a következő irányelveket tudni, hogy a hálózati illesztő állapotát.
   
   * Ha a kapcsolat kifogástalan, és engedélyezve van, a **ifIndex** állapot jelenik meg **be**.
   * Ha a kapcsolat állapota kifogástalan, de nem fizikailag kapcsolódás (hálózati kábel), a **ifIndex** jelenik meg, mint **letiltott**.
   * Ha a kapcsolat megfelelő, de nincs engedélyezve, a **ifIndex** állapot jelenik meg **NotPresent**.
   * Ha a kapcsolat nem létezik, nem jelenik meg ebben a listában. A StorSimple Device Manager szolgáltatás felhasználói felületi továbbra is jelennek meg az illesztő hibás állapotban.

E parancsmag használatáról további információkért látogasson el [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) a Windows PowerShell parancsmag-referencia a.

A következő szakaszok bemutatják minták kimenetét a `Get-NetAdapter` parancsmag.

 Ezeket a mintákat vezérlő 0 a passzív vezérlő lett, és a következőképpen volt konfigurálva:

* DATA 0, az adatok 1, a DATA 2 és a DATA 3 hálózati adapterek már létezett az eszközön.
* ADATOK 4 és az adatok 5 hálózati kártyák nem volt jelen; ezért nem szerepelnek a kimenetben.
* DATA 0 volt engedélyezve.

1. vezérlő aktív vezérlőjén volt, és a következőképpen volt konfigurálva:

* DATA 0, adatok 1, 2 adatok, a DATA 3, adatok 4 és a DATA 5 hálózati adapterek már létezett az eszközön.
* DATA 0 volt engedélyezve.

**Példa a kimenetre – vezérlő 0**

A vezérlő 0 (a passzív vezérlő) kimenete a következő: ADATOK 1, a DATA 2 és a DATA 3 nincs csatlakoztatva. ADATOK 4 és az adatok 5 nem tartalmazzák, mivel nincsenek jelen az eszközön.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Példa a kimenetre – 1. vezérlő**

A vezérlő 1 (az aktív vezérlővel) kimenete a következő: Csak a DATA 0 hálózati csatoló az eszköz konfigurálása és működik.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>A Test-Connection parancsmaggal hibaelhárításához
Használhatja a `Test-Connection` parancsmag használatával határozza meg, hogy a StorSimple eszköz kapcsolódhatnak a külső hálózathoz. Ha a hálózati paramétereket, beleértve a DNS, a telepítővarázsló megfelelően vannak konfigurálva, használhatja a `Test-Connection` parancsmag a hálózaton, például az Outlook.com-on kívüli egy ismert cím pingelését.

Engedélyeznie kell a ping csatlakozási hibák léptek fel ezzel a parancsmaggal hibaelhárításához, ha ping le van tiltva.

Tekintse meg a következő minták kimenetét a `Test-Connection` parancsmag.

> [!NOTE]
> Az első mintában az eszköz egy helytelen DNS van beállítva. A második példában a DNS helyességéről.

**Példa a kimenetre – helytelen DNS**

A következő példában nincs nincs kimenet az IPV4 és IPv6 típusú címek, amely azt jelzi, hogy a DNS nincs feloldva. Ez azt jelenti, hogy nincs kapcsolat a külső hálózathoz, és a megfelelő DNS meg kell adni.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Példa a kimenetre – megfelelő DNS**

A következő példában a DNS adja vissza az IPv4-cím, jelezve, hogy a DNS megfelelően van-e. Ez megerősíti, hogy nincs-e a külső hálózathoz csatlakoznak.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>A Test-HcsmConnection parancsmag elhárítása
Használja a `Test-HcsmConnection` parancsmag már csatlakoztatva van és a StorSimple Device Manager szolgáltatásban regisztrált eszközökhöz. Ez a parancsmag segítségével a regisztrált eszközhöz és a megfelelő StorSimple Device Manager szolgáltatás közötti kapcsolat ellenőrzése. Ez a parancs a StorSimple futtathatja a Windows PowerShell.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>A Test-HcsmConnection parancsmag futtatásához
1. Győződjön meg arról, hogy az eszköz regisztrálva van-e.
2. Az eszköz állapotának ellenőrzéséhez. Ha az eszközt az Inaktiválás karbantartási módban, vagy offline módban vannak, megjelenhet hibák a következők egyikét:
   
   * ErrorCode.CiSDeviceDecommissioned – Ez azt jelzi, hogy az eszközt az Inaktiválás.
   * ErrorCode.DeviceNotReady – Ez azt jelzi, hogy az eszköz karbantartási módban van.
   * ErrorCode.DeviceNotReady – Ez azt jelzi, hogy az eszköz nem online.
3. Győződjön meg arról, hogy a StorSimple Device Manager szolgáltatás fut. (használja a [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) parancsmag). A szolgáltatás nem fut, nem látható a következő hibákat:
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – Ez azt jelzi, hogy kivételt Get-ClusterResource futtatásakor.
4. Ellenőrizze a Access Control Service (ACS) jogkivonatot. Ha azt a webes kivételt okoz, lehet, egy átjáró probléma, egy hiányzó proxyhitelesítés, egy helytelen DNS vagy hitelesítési hiba eredménye. Előfordulhat, hogy tekintse meg a következő hibákat:
   
   * ErrorCode.CiSApplianceGateway – Ez jelzi a HttpStatusCode.BadGateway kivétel: a feloldó szolgáltatás nem sikerült feloldani a gazdagép nevét.
   * ErrorCode.CiSApplianceProxy – Ez jelzi a HttpStatusCode.ProxyAuthenticationRequired kivételt (HTTP-állapotkód 407): az ügyfél nem tudta hitelesíteni a proxyn.
   * ErrorCode.CiSApplianceDNSError – Ez jelzi a WebExceptionStatus.NameResolutionFailure kivétel: a feloldó szolgáltatás nem sikerült feloldani a gazdagép nevét.
   * ErrorCode.CiSApplianceACSError – Ez azt jelzi, hogy a szolgáltatás hitelesítési hibát adott vissza, de nincs kapcsolat.
     
     Ha azt nem lépett egy webes kivétel, ellenőrizze, hogy ErrorCode.CiSApplianceFailure. Ez azt jelzi, hogy a készülék.
5. Ellenőrizze a felhőalapú szolgáltatás kapcsolatot. Ha a szolgáltatás webes kivételt okoz, láthatja a következő hibákat:
   
   * ErrorCode.CiSApplianceGateway – Ez jelzi a HttpStatusCode.BadGateway kivétel: egy köztes proxykiszolgáló hibás kérelmet kapott a másik proxy vagy az eredeti kiszolgálón.
   * ErrorCode.CiSApplianceProxy – Ez jelzi a HttpStatusCode.ProxyAuthenticationRequired kivételt (HTTP-állapotkód 407): az ügyfél nem tudta hitelesíteni a proxyn.
   * ErrorCode.CiSApplianceDNSError – Ez jelzi a WebExceptionStatus.NameResolutionFailure kivétel: a feloldó szolgáltatás nem sikerült feloldani a gazdagép nevét.
   * ErrorCode.CiSApplianceACSError – Ez azt jelzi, hogy a szolgáltatás hitelesítési hibát adott vissza, de nincs kapcsolat.
     
     Ha azt nem lépett egy webes kivétel, ellenőrizze, hogy ErrorCode.CiSApplianceSaasServiceError. Ez a StorSimple Device Manager szolgáltatással kapcsolatos problémát jelez.
6. Ellenőrizze az Azure Service Bus-kapcsolatot. ErrorCode.CiSApplianceServiceBusError azt jelzi, hogy az eszköz nem tudja-e csatlakozni a Service Bus.

A naplófájlok CiSCommandletLog0Curr.errlog pedig a CiSAgentsvc0Curr.errlog további információkat, például a kivétel részletei.

A parancsmag használatával kapcsolatos további információkért látogasson el [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) a Windows PowerShell dokumentáció.

> [!IMPORTANT]
> Ez a parancsmag az aktív és passzív vezérlő is futtathatja.

Tekintse meg a következő minták kimenetét a `Test-HcsmConnection` parancsmag.

**Minta kimenet – StorSimple Update 3 fut sikeresen regisztrált eszközre**

      Controller1>Test-HcsmConnection

      Checking device registration state  ... Success
      Device registered successfully

      Checking primary NTP server [time.windows.com] ... Success

      Checking web proxy  ... NOT SET

      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET

      Checking device online  ... Success

      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success

      Checking connectivity from device to service  ... This will take a few minutes.

      Checking connectivity from device to service  ... Success

      Checking connectivity from service to device  ... Success

      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Példa a kimenetre – offline eszköz** 

Ez a minta egy eszközről, amely állapota van **Offline** az Azure portálon.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

Az eszköz nem sikerült csatlakozni az aktuális webproxy-konfigurációja. Ennek oka lehet a webproxy-konfigurációja vagy a hálózati csatlakozási probléma kapcsolatos problémát. Ebben az esetben meg kell győződnie arról, hogy a webes proxykiszolgáló beállításainak helyességét és a webproxy-kiszolgálók online és elérhető-e.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>A Sync-HcsTime parancsmaggal hibaelhárításához
Ez a parancsmag segítségével az eszköz időpontjának megjelenítéséhez. Ha az eszköz egy eltolva, az NTP-kiszolgáló még, majd segítségével ennek a parancsmagnak kényszerített – az idő szinkronizálása a NTP-kiszolgáló.
- Ha az eszköz és az NTP-kiszolgáló között az eltolás nagyobb, mint 5 perc, megjelenik egy figyelmeztetés. 
- Az eltolás meghaladja a 15 perc, ha az eszköz offline kerül. Ez a parancsmag segítségével továbbra is időszinkronizálást. 
- Azonban ha az eltolás meghaladja a 15 óra, majd csak akkor tudja kényszerített szinkronizálási idő, és egy hibaüzenet jelenik meg.

**Minta kimenet – segítségével a Sync-HcsTime kényszerített az idő szinkronizálása**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Az Enable-HcsPing és a Disable-HcsPing parancsmagok elhárítása
Ezek a parancsmagok használatával győződjön meg arról, hogy az eszköz a hálózati adapterek ICMP ping kérésekre válaszol. Alapértelmezés szerint a StorSimple hálózati illesztők válaszol a ping-kérelmekre. Ez a parancsmag használata a legegyszerűbben úgy tudja, hogy ha az eszköz online és elérhető-e.

**Minta kimenet – Enable-HcsPing és a Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>A nyomkövetés-HcsRoute parancsmaggal hibaelhárításához
Ez a parancsmag olyan útvonal eszköz használható. A végső rendeltetési módja az egyes útválasztók egy meghatározott időtartamra vonatkozóan csomagokat küld, és majd kiszámítja az egyes csomagok eredmény. A parancsmag fokának csomagvesztés bármely adott útválasztó vagy hivatkozás jeleníti meg, mert mely útválasztók tudja, vagy előfordulhat, hogy a hivatkozások hálózati problémát okoz.

**Bemutatja, hogyan nyomkövetési-HcsRoute csomagot útvonalának követése minta kimenet**

     Controller0>Trace-HcsRoute -Target 10.126.174.25

     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]

     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]

     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>A Get-HcsRoutingTable parancsmaggal hibaelhárításához
Ez a parancsmag segítségével megtekintheti az útválasztási táblában a StorSimple eszközt. Útválasztási táblázat olyan szabályok, amelyek segíthetnek meghatározni, ha az Internet Protocol (IP) hálózaton keresztül továbbított adatok csomagokat a rendszer kéri.

Az útválasztási táblázat a felületek és az átjáró, amely továbbítja az adatokat a megadott hálózatokat. A útválasztási mérték, amely az adott cél eléréséhez szükséges elérési utat a döntéshozó is biztosít. Az alsó útválasztási metrika, annál magasabb prioritást.

Például ha 2 hálózati illesztőt, DATA 2 és a DATA 3 csatlakozik az internethez. Ha a DATA 2 és a DATA 3 útválasztási metrikáját 15 és 261 rendre, kisebb útválasztási metrikájú DATA 2 az előnyben részesített interface, az Internet eléréséhez használt.

Ha a StorSimple eszköz frissítése 1 futtatja, akkor a DATA 0 hálózati adapterén a legmagasabb beállítása a felhőalapú forgalom van. Ez azt jelenti, hogy akkor is, ha nincsenek más felhőalapú felülettel, a felhőalapú forgalom lesz átirányítva a DATA 0.

Ha futtatja a `Get-HcsRoutingTable` parancsmag (ahogy az alábbi példa mutatja) paraméterek megadása nélkül a parancsmag kimeneteként IPv4 és IPv6-útválasztási táblázataiba. Másik lehetőségként megadhat `Get-HcsRoutingTable -IPv4` vagy `Get-HcsRoutingTable -IPv6` beolvasni a megfelelő útválasztási táblázatban.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================

      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================

      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None

      Controller0>

## <a name="step-by-step-storsimple-troubleshooting-example"></a>StorSimple részletes hibaelhárítási példa
A következő példa bemutatja a StorSimple központi telepítés részletes hibaelhárítási. A példaforgatókönyvben eszközök regisztrációja sikertelen, és egy hiba üzenet arról, hogy a hálózati beállításokat, vagy a DNS-név érvénytelen.

A következő hibaüzenet jelenik a következő:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

A hiba oka lehet az alábbiak valamelyikét:

* Helytelen hardver telepítése
* Hibás hálózati adaptert
* Helytelen IP-cím, alhálózati maszk, átjáró, elsődleges DNS-kiszolgáló vagy webes proxy
* Nem megfelelő regisztrációs kulcs
* Helytelen tűzfal beállításai

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Keresse meg és hárítsa el az eszköz regisztrációs probléma
1. Ellenőrizze az eszköz konfigurációját: az aktív vezérlővel, futtassa `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > A telepítő varázsló aktív vezérlőjén kell futtatni. Annak ellenőrzéséhez, hogy csatlakozik az aktív vezérlővel, tekintse meg a szalagcím a soros konzol állnak rendelkezésre. A szalagcím azt jelzi, 0 vagy 1 vezérlőjéhez kapcsolat, és hogy a vezérlő aktív vagy passzív-e. További információkért látogasson el [azonosíthatja az aktív vezérlőhöz az eszközön](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Győződjön meg arról, hogy az eszköz megfelelően van-e csatlakoztatva: Ellenőrizze a hálózati kábelek vissza vezérlősík az eszközön. A kábelezést csak a eszközmodell az. További információkért látogasson el [a StorSimple 8100 eszköz telepítése](storsimple-8100-hardware-installation.md) vagy [a StorSimple 8600 eszköz telepítése](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Ha 10 GbE hálózati portokat használ, akkor a megadott QSFP-SFP adapterek és kábelek SFP. További információkért lásd: a [kábelek, kapcsolók és a 10 GbE-portokhoz ajánlott adó listája](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. A hálózati adapter állapotának ellenőrzése:
   
   * A Get-NetAdapter parancsmag használatával a DATA 0 a hálózati adapterek állapotának észleléséhez. 
   * Ha a kapcsolat nem működik, a **ifindex** állapotát jelzi, hogy a kapcsolat le van-e. Szüksége lesz majd, hogy ellenőrizze a hálózati kapcsolatot, a készülék, és a kapcsolón a port. Szüksége lesz is, a rossz kábelek kizárása. 
   * Ha azt gyanítja, hogy a DATA 0 aktív vezérlőjén port sikertelen volt, akkor ezt úgy ellenőrizheti az adatokhoz való kapcsolódásról 1. vezérlő 0 portjához. Ellenőrizheti, hogy megszakítja a kapcsolatot a hálózati kábel hátoldalán található az eszköz a vezérlőről 0, 1-vezérlőhöz csatlakoztassa a kábelt, és futtassa újból a a Get-NetAdapter parancsmag.
     Ha a DATA 0 portszámot, egy tartományvezérlő sikertelen lesz, [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) további lépéseket. Előfordulhat, hogy szükség a tartományvezérlő a rendszeren.
4. Ellenőrizze a kapcsolatot a kapcsolóhoz:
   
   * Győződjön meg arról, hogy a vezérlő 0 és 1. az elsődleges szolgáltatással vezérlő DATA 0 hálózati illesztők ugyanazon az alhálózaton. 
   * Ellenőrizze a központ vagy az útválasztó. Általában mindkét tartományvezérlők csatlakoznia kell a központi vagy a útválasztó. 
   * Győződjön meg arról, a kapcsolók, használhatja a kapcsolatot a DATA 0 mindkét az ugyanazon VLAN-vezérlők.
5. Bármely felhasználó hibák kiküszöbölése:
   
   * Indítsa újra a telepítővarázslót (Futtatás **Invoke-HcsSetupWizard**), és írja be újra az értékeket győződjön meg arról, hogy nincsenek-e hibák. 
   * Ellenőrizze a regisztrációs kulcs. Az azonos regisztrációs kulcs segítségével több eszközök csatlakoznak a StorSimple eszköz Manager szolgáltatást. Az eljárás a [Szolgáltatásregisztrációs kulcs lekérése](storsimple-8000-manage-service.md#get-the-service-registration-key) annak érdekében, hogy a megfelelő kulcsot használ.
     
     > [!IMPORTANT]
     > Ha több szolgáltatás fut, akkor ellenőrizze, hogy használja-e a megfelelő szolgáltatáshoz a regisztrációs kulccsal kell regisztrálni az eszközt. Ha a nem megfelelő a StorSimple eszköz Manager szolgáltatásban regisztrált egy eszközt, akkor [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) további lépéseket. Előfordulhat, hogy végezzen el (amely adatvesztést eredményezhet) az eszköz gyári beállításainak visszaállítását, majd csatlakoztassa a tervezett szolgáltatás.
     > 
     > 
6. A Test-Connection parancsmag segítségével ellenőrizze, hogy rendelkezik-e a külső hálózathoz csatlakoznak. További információkért látogasson el [kapcsolatos problémák elhárítása a Test-Connection parancsmaggal](#troubleshoot-with-the-test-connection-cmdlet).
7. Ellenőrizze, hogy a tűzfal zavaró tényező. Ha ellenőrizte, hogy a virtuális IP-cím (VIP), az alhálózati, az átjárót és a DNS-beállítások helyességét, és problémák továbbra is megjelenik, majd lehetséges, hogy a tűzfal blokkolja az eszköz és a külső hálózat közötti kommunikáció. Győződjön meg arról, hogy elérhetők-e 80-as és 443-as port kimenő kommunikációra a StorSimple-eszköz szükség. További információkért lásd: [a StorSimple eszköz hálózatkezelési követelményei](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Tekintse meg a naplókat. Ugrás a [támogatja a csomagok és a rendelkezésre álló eszköznaplók hibaelhárítási](#support-packages-and-device-logs-available-for-troubleshooting).
9. Ha az előző lépések nem oldja meg a problémát, [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) segítségért.

## <a name="next-steps"></a>Következő lépések
[A diagnosztikai eszköz használata a StorSimple eszköz hibaelhárításához](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
