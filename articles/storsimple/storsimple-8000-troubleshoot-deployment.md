---
title: A StorSimple 8000 sorozatú telepítési problémák elhárítása | Microsoft dokumentumok
description: Ez a témakör a StorSimple első üzembe helyezésekor előforduló hibák diagnosztizálása és javítása.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: f2b454e812db1eea686f82e92841163f1129b6c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267624"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>A StorSimple eszköztelepítési problémáinak elhárítása
## <a name="overview"></a>Áttekintés
Ez a cikk hasznos hibaelhárítási útmutatást nyújt a Microsoft Azure StorSimple központi telepítéséhez. Ismerteti a gyakori problémákat, a lehetséges okokat és az ajánlott lépéseket, amelyek segítenek a StorSimple konfigurálása során esetlegesen felmerülő problémák megoldásában. 

Ez az információ a StorSimple 8000 sorozatú fizikai eszközre és a StorSimple cloud appliance-ra egyaránt vonatkozik.

> [!NOTE]
> Az eszköz konfigurációval kapcsolatos problémái előfordulhatnak, amikor először telepíti az eszközt, vagy később, amikor az eszköz működőképes. Ez a cikk az első üzembe helyezési problémák elhárítására összpontosít. Egy működő eszköz hibaelhárításához nyissa meg [a Diagnosztikai eszköz használata egy működő eszköz hibaelhárításához című témakört.](storsimple-8000-diagnostics.md)

Ez a cikk a StorSimple-telepítések hibaelhárítási eszközeit is ismerteti, és egy részletes hibaelhárítási példát is tartalmaz.

## <a name="first-time-deployment-issues"></a>Első üzembe helyezési problémák
Ha az eszköz első üzembe helyezésekor problémába ütközik, vegye figyelembe az alábbiakat:

* Ha fizikai eszközt hárít el, győződjön meg arról, hogy a hardver telepítve és konfigurálva van [a StorSimple 8100 eszköz telepítése](storsimple-8100-hardware-installation.md) vagy a [StorSimple 8600 eszköz telepítése](storsimple-8600-hardware-installation.md)című témakörben leírtak szerint.
* Ellenőrizze az üzembe helyezés előfeltételeit. Győződjön meg arról, hogy rendelkezik a [központi telepítési konfigurációs ellenőrzőlistában](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist)leírt összes információval.
* Tekintse át a StorSimple kiadási megjegyzések, hogy ha a probléma leírása. A kibocsátási megjegyzések az ismert telepítési problémák megoldását tartalmazzák. 

Az eszköz telepítése során a leggyakoribb problémák, amelyekkel a felhasználók szembesülnek, amikor futtatják a telepítő varázslót, és amikor regisztrálják az eszközt a Windows PowerShell for StorSimple rendszeren keresztül. (A StorSimple windows PowerShell használatával regisztrálja és konfigurálja a StorSimple-eszközt. Az eszközregisztrációról további információt a [3.](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)

A következő szakaszok segíthetnek a StorSimple eszköz első konfigurálásakor felmerülő problémák megoldásában.

## <a name="first-time-setup-wizard-process"></a>Első beállítási varázsló folyamata
A következő lépések összefoglalják a telepítővarázsló folyamatát. A részletes telepítési információkért [olvassa el a helyszíni StorSimple-eszköz telepítése című témakört.](storsimple-8000-deployment-walkthrough-u2.md)

1. Az [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) parancsmag futtatásával indítsa el a telepítővarázslót, amely végigvezeti a további lépéseken. 
2. A hálózat konfigurálása: a telepítővarázsló lehetővé teszi a Data 0 hálózati felület hálózati beállításainak konfigurálását a StorSimple eszközön. Ezek a beállítások a következők:
   * Virtuális IP (VIP), alhálózati maszk és átjáró – A [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) parancsmag a háttérben kerül végrehajtásra. Konfigurálja az IP-címet, az alhálózati maszkot és az átjárót a Data 0 hálózati adapterhez a StorSimple-eszközön.
   * Elsődleges DNS-kiszolgáló – A [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) parancsmag végrehajtása a háttérben történik. Konfigurálja a StorSimple-megoldás DNS-beállításait.
   * NTP-kiszolgáló – A [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) parancsmag végrehajtása a háttérben történik. Konfigurálja a StorSimple megoldás NTP-kiszolgálóbeállításait.
   * Választható webproxy – A [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) parancsmag végrehajtása a háttérben történik. Beállítja, és lehetővé teszi a web proxy konfigurációa a StorSimple megoldás.
3. Állítsa be a jelszót: a következő lépés az eszközrendszergazdai jelszó beállítása.
   Az eszköz rendszergazdai jelszava az eszközre való bejelentkezéshez használható. Az eszköz alapértelmezett jelszava: **Password1**.
        
     > [!IMPORTANT]
     > A jelszavak at a regisztráció előtt gyűjtjük, de csak az eszköz sikeres regisztrálása után alkalmazza. Ha nem sikerül alkalmazni a jelszót, a rendszer kéri, hogy adja meg újra a jelszót, amíg a szükséges jelszavakat (amelyek megfelelnek az összetettségi követelményeknek) gyűjtik.
     
4. Regisztrálja az eszközt: az utolsó lépés az eszköz regisztrálása a Microsoft Azure-ban futó StorSimple Eszközkezelő szolgáltatással. A regisztráció megköveteli, hogy [a szolgáltatás regisztrációs kulcsot az](storsimple-8000-manage-service.md#get-the-service-registration-key) Azure Portalon, és adja meg a beállítási varázsló. **Az eszköz sikeres regisztrálása után egy szolgáltatásadat-titkosítási kulcsot biztosítunk. Ügyeljen arra, hogy ezt a titkosítási kulcsot biztonságos helyen tartsa, mert minden további eszközt regisztrálnia kell a szolgáltatásban.**

## <a name="common-errors-during-device-deployment"></a>Gyakori hibák az eszköz telepítése során
Az alábbi táblázatok azokat a gyakori hibákat sorolják fel, amelyek a következő kedvéváláskor fordulhatnak elő:

* Adja meg a szükséges hálózati beállításokat.
* Konfigurálja a választható webproxy-beállításokat.
* Állítsa be az eszköz rendszergazdai jelszavát.
* Regisztrálja az eszközt.

## <a name="errors-during-the-required-network-settings"></a>Hibák a szükséges hálózati beállítások során
| Nem. | Hibaüzenet | Lehetséges okok | Javasolt művelet |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Ez a parancs csak az aktív vezérlőn futtatható. |A konfiguráció a passzív vezérlőn történt. |Futtassa ezt a parancsot az aktív vezérlőből. További információt az [Aktív vezérlő azonosítása az eszközön](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)című témakörben talál. |
| 2 |Invoke-HcsSetupWizard: Az eszköz nem áll készen. |Problémák vannak a DATA 0 hálózati kapcsolatával. |Ellenőrizze a fizikai hálózati kapcsolatot a DATA 0-n. |
| 3 |Invoke-HcsSetupWizard: Ip-címütközés van a hálózat egy másik rendszerével (Kivétel a HRESULT-ből: 0x80070263). |A DATA 0 protokollt már egy másik rendszer használta. |Adjon meg egy új IP-címet, amely nincs használatban. |
| 4 |Invoke-HcsSetupWizard: Egy fürterőforrás nem sikerült. (Kivétel a HRESULT alól: 0x800713AE). |Duplikált VIP. A mellékelt IP már használatban van. |Adjon meg egy új IP-címet, amely nincs használatban. |
| 5 |Invoke-HcsSetupWizard: Érvénytelen IPv4-cím. |Az IP-cím nem megfelelő formátumban van megadva. |Ellenőrizze a formátumot, és adja meg újra az IP-címét. További információt az [Ipv4-címzés című témakörben talál.][1] |
| 6 |Invoke-HcsSetupWizard: Érvénytelen IPv6-cím. |Az IP-cím nem megfelelő formátumban van megadva. |Ellenőrizze a formátumot, és adja meg újra az IP-címét. További információt az [Ipv6-címzés című témakörben talál.][2] |
| 7 |Invoke-HcsSetupWizard: Nincs több végpont elérhető a végpont leképező. (Kivétel a HRESULT alól: 0x800706D9) |A fürt funkciónem működik. |A következő lépésekért forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md) |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Hibák a választható webproxy-beállítások során
| Nem. | Hibaüzenet | Lehetséges okok | Javasolt művelet |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Érvénytelen paraméter (Kivétel a HRESULT-ből: 0x80070057) |A proxybeállításokhoz megadott paraméterek egyike érvénytelen. |Az URI nem a megfelelő formátumban van megadva. A*\<webproxy-kiszolgáló>http:// IP-címe vagy teljes tartományszáma: **\<TCP-portszáma>* |
| 2 |Invoke-HcsSetupWizard: Az RPC-kiszolgáló nem érhető el (Kivétel a HRESULT-ből: 0x800706ba) |A kiváltó ok az alábbiak egyike:<ol><li>A fürt nem áll fel.</li><li>A passzív vezérlő nem tud kommunikálni az aktív vezérlővel, és a parancs a passzív vezérlőből fut.</li></ol> |A kiváltó októl függően:<ol><li>Lépjen kapcsolatba a [Microsoft támogatási szolgálatával,](storsimple-8000-contact-microsoft-support.md) és győződjön meg arról, hogy a fürt nem.</li><li>Futtassa a parancsot az aktív vezérlőből. Ha a parancsot a passzív vezérlőből szeretné futtatni, gondoskodnia kell arról, hogy a passzív vezérlő képes legyen kommunikálni az aktív vezérlővel. Ha a kapcsolat megszakadt, kapcsolatba kell lépnie a [Microsoft támogatási szolgálatával.](storsimple-8000-contact-microsoft-support.md)</li></ol> |
| 3 |Invoke-HcsSetupWizard: Az RPC-hívás nem sikerült (Kivétel a HRESULT-ből: 0x800706be) |A fürt nem. |Lépjen kapcsolatba a [Microsoft támogatási szolgálatával,](storsimple-8000-contact-microsoft-support.md) és győződjön meg arról, hogy a fürt nem. |
| 4 |Invoke-HcsSetupWizard: A fürterőforrás nem található (Kivétel a HRESULT-ből: 0x8007138f) |A fürterőforrás nem található. Ez akkor fordulhat elő, ha a telepítés nem volt helyes. |Előfordulhat, hogy vissza kell állítania az eszközt a gyári alapértelmezett beállításokra. Fürterőforrás létrehozásához forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Invoke-HcsSetupWizard: A fürterőforrás nem online (Kivétel a HRESULT-ből: 0x8007138c) |A fürterőforrások nincsenek online állapotban. |A következő lépésekért forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md) |

## <a name="errors-related-to-device-administrator-password"></a>Az eszközrendszergazda jelszavával kapcsolatos hibák
Az eszköz alapértelmezett rendszergazdai jelszava a **Jelszó1**. Ez a jelszó az első bejelentkezés után lejár; ezért a beállítási varázslóval kell módosítania. Az eszköz első regisztrálásakor meg kell adnia egy új eszközrendszergazdai jelszót. 

Győződjön meg arról, hogy a jelszavak megfelelnek az alábbi követelményeknek:

* Az eszköz rendszergazdai jelszavának 8 és 15 karakter közötti nek kell lennie.
* A jelszavaknak a következő 4 karakterközül 3 karaktert kell tartalmazniuk: kisbetű, nagybetű, numerikus és speciális. 
* A jelszó nem egyezhet meg az utolsó 24 jelszóval.

Ezenkívül ne feledje, hogy a jelszavak minden évben lejárnak, és csak az eszköz sikeres regisztrálása után módosíthatók. Ha a regisztráció bármilyen okból sikertelen, a jelszavak nem változnak.

Az eszközrendszergazda jelszavával kapcsolatos további tudnivalókért látogasson el [a StorSimple Eszközkezelő szolgáltatás használata a StorSimple jelszó módosításához című webhelyre.](storsimple-8000-change-passwords.md)

Az eszközadminisztrátor és a StorSimple Snapshot Manager-jelszavak beállításakor az alábbi hibák közül egyet vagy többet tapasztalhat.

| Nem. | Hibaüzenet | Javasolt művelet |
| --- | --- | --- |
| 1 |A jelszó hossza meghaladja a maximális hosszt. |Az eszköz rendszergazdai jelszavának 8 és 15 karakter közötti nek kell lennie. |
| 2 |A jelszó nem felel meg a kívánt hossznak. |Az eszköz rendszergazdai jelszavának 8 és 15 karakter közötti nek kell lennie.|
| 3 |A jelszónak kisbetűket kell tartalmaznia. |A jelszavaknak a következő 4 karakterközül 3 karaktert kell tartalmazniuk: kisbetű, nagybetű, numerikus és speciális. Győződjön meg arról, hogy a jelszó megfelel ezeknek a követelményeknek. |
| 4 |A jelszónak numerikus karaktereket kell tartalmaznia. |A jelszavaknak a következő 4 karakterközül 3 karaktert kell tartalmazniuk: kisbetű, nagybetű, numerikus és speciális. Győződjön meg arról, hogy a jelszó megfelel ezeknek a követelményeknek. |
| 5 |A jelszónak speciális karaktereket kell tartalmaznia. |A jelszavaknak a következő 4 karakterközül 3 karaktert kell tartalmazniuk: kisbetű, nagybetű, numerikus és speciális. Győződjön meg arról, hogy a jelszó megfelel ezeknek a követelményeknek. |
| 6 |A jelszónak a következő 4 karakterközül 3 karaktert kell tartalmaznia: nagybetűs, kisbetűs, numerikus és speciális. |A jelszó nem tartalmazza a szükséges karaktertípusokat. Győződjön meg arról, hogy a jelszó megfelel ezeknek a követelményeknek. |
| 7 |A paraméter nem felel meg a megerősítésnek. |Győződjön meg arról, hogy a jelszó megfelel az összes követelménynek, és helyesen adta meg. |
| 8 |A jelszó nem felel meg az alapértelmezettnek. |Az alapértelmezett jelszó a *Password1*. Ezt a jelszót az első bejelentkezés után módosítania kell. |
| 9 |A megadott jelszó nem egyezik meg az eszköz jelszavával. Írja be újra a jelszót. |Ellenőrizze a jelszót, és írja be újra. |

A jelszavakat az eszköz regisztrálása előtt gyűjtik, de csak a sikeres regisztráció után alkalmazzák. A jelszó-helyreállítási munkafolyamat megköveteli az eszköz regisztrálását.

> [!IMPORTANT]
> Általánosságban elmondható, hogy ha a jelszó alkalmazására tett kísérlet sikertelen, a szoftver ismételten megpróbálja összegyűjteni a jelszót, amíg sikeres nem lesz. Ritka esetekben a jelszó nem alkalmazható. Ebben az esetben regisztrálhatja az eszközt, és folytathatja, azonban a jelszavak nem változnak. Módosíthatja az eszköz rendszergazdai jelszavát az Azure Portalon történő regisztráció után.


A storSimple Eszközkezelő szolgáltatás segítségével alaphelyzetbe állíthatja a jelszót az Azure Portalon. További információt az [Eszköz rendszergazdai jelszavának módosítása](storsimple-8000-change-passwords.md#change-the-device-administrator-password)című területen talál.

## <a name="errors-during-device-registration"></a>Hibák az eszközregisztráció során
Az eszköz regisztrálásához használja a Microsoft Azure-ban futó StorSimple Eszközkezelő szolgáltatást. Az eszköz regisztrációja során az alábbi problémák közül egyet vagy többet is előfordulhat.

| Nem. | Hibaüzenet | Lehetséges okok | Javasolt művelet |
| --- | --- | --- | --- |
| 1 |350027-es hiba: Nem sikerült regisztrálni az eszközt a StorSimple Eszközkezelővel. | |Várjon néhány percet, majd próbálkozzon újra a művelettel. Ha a probléma továbbra is fennáll, [forduljon a Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md) |
| 2 |350013-as hiba: Hiba történt az eszköz regisztrálása során. Ennek oka lehet a helytelen szolgáltatásregisztrációs kulcs. | |Regisztrálja újra az eszközt a megfelelő szolgáltatásregisztrációs kulccsal. További információ: [A szolgáltatás regisztrációs kulcsának beszereznie.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |350063-as hiba: A StorSimple Eszközkezelő szolgáltatás hitelesítése lejárt, de a regisztráció nem sikerült. Próbálkozzon újra a művelettel egy idő után. |Ez a hiba azt jelzi, hogy az ACS-vel végzett hitelesítés sikeres volt, de a szolgáltatásregiszterhívás sikertelen volt. Ez egy szórványos hálózati hiba eredménye lehet. |Ha a probléma továbbra is fennáll, [forduljon a Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md) |
| 4 |350049-es hiba: A szolgáltatás nem érhető el a regisztráció során. |Amikor a szolgáltatás hívása történik, webes kivétel érkezik. Bizonyos esetekben ez a művelet későbbi újbóli megkísérlésével kijavítható. |Ellenőrizze az IP-címet és a DNS-nevet, majd próbálkozzon újra a művelettel. Ha a probléma továbbra is fennáll, [forduljon a Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md) |
| 5 |350031-es hiba: Az eszköz már regisztrálva van. | |Nincs szükség beavatkozásra. |
| 6 |350016-os hiba: Az eszközregisztráció nem sikerült. | |Ellenőrizze, hogy a regisztrációs kulcs helyes-e. |
| 7 |Invoke-HcsSetupWizard: Hiba történt az eszköz regisztrálása közben; ennek oka lehet helytelen IP-cím vagy DNS-név. Ellenőrizze a hálózati beállításokat, majd próbálkozzon újra. Ha a probléma továbbra is fennáll, [forduljon a Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md) (350050-es hiba) |Győződjön meg arról, hogy az eszköz képes pingelni a külső hálózatot. Ha nem rendelkezik kapcsolattal a külső hálózathoz, a regisztráció sikertelen lehet ezzel a hibával. Ez a hiba az alábbiak közül egy vagy több kombinációja lehet:<ul><li>Helytelen IP</li><li>Helytelen alhálózat</li><li>Helytelen átjáró</li><li>Helytelen DNS-beállítások</li></ul> |Tekintse meg a [lépésenkénti hibaelhárítási példában](#step-by-step-storsimple-troubleshooting-example)található lépéseket. |
| 8 |Invoke-HcsSetupWizard: Az aktuális művelet belső szolgáltatáshiba miatt nem sikerült [0x1FBE2] miatt. Próbálkozzon újra a művelettel valamikor. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |Ez egy általános hiba, amely a szolgáltatásból vagy ügynökből származó összes felhasználó láthatatlan hibájára vonatkozóan jelenik meg. A leggyakoribb ok az lehet, hogy az ACS-hitelesítés sikertelen volt. A hiba egyik lehetséges oka, hogy problémák vannak az NTP-kiszolgáló konfigurációjával kapcsolatban, és az eszköz ideje nincs megfelelően beállítva. |Javítsa ki az időt (ha problémák vannak), majd próbálkozzon újra a regisztrációs művelettel. Ha a Set-HcsSystem -Timezone paranccsal állítja be az időzónát, nagybetűvel használja az időzónában lévő egyes szavakat (például "csendes-óceáni téli idő").  Ha a probléma továbbra is fennáll, lépjen kapcsolatba a [Microsoft támogatási szolgálatával](storsimple-8000-contact-microsoft-support.md) a következő lépésekért. |
| 9 |Figyelmeztetés: Nem lehetett aktiválni az eszközt. Az eszköz rendszergazdája és a StorSimple Snapshot Manager jelszavai nem változtak. |Ha a regisztráció sikertelen, az eszköz rendszergazdája és a StorSimple Snapshot Manager jelszavai nem változnak. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>A StorSimple-környezetek hibaelhárítási eszközei
A StorSimple számos eszközt tartalmaz, amelyek segítségével elháríthatja a StorSimple-megoldást. Ezek a következők:

* Támogatási csomagok és eszköznaplók.
* Kifejezetten hibaelhárításra tervezett parancsmagok.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Hibaelhárításhoz rendelkezésre álló támogatási csomagok és eszköznaplók
A támogatási csomag tartalmazza az összes olyan naplót, amely segítséget nyújthat a Microsoft támogatási csapatának az eszközhibák elhárításában. A Windows PowerShell for StorSimple segítségével létrehozhat egy titkosított támogatási csomagot, amelyet ezután megoszthat a támogatási személyzettel.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>A támogatási csomag naplóinak vagy tartalmának megtekintése
1. A StorSimple hez létrehozott Windows PowerShell segítségével hozzon létre egy támogatási csomagot a [Támogatási csomag létrehozása és kezelése](storsimple-8000-create-manage-support-package.md)című részben leírtak szerint.
2. Töltse le a [visszafejtési parancsfájlt](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) helyileg az ügyfélszámítógépen.
3. Ezzel a [lépésről lépésre történő eljárással](storsimple-8000-create-manage-support-package.md#edit-a-support-package) megnyithatja és visszafejtheti a támogatási csomagot.
4. A visszafejtett támogatási csomag naplók etw/etvx formátumban vannak. A fájlok Windows Eseménynaplóban való megtekintéséhez a következő lépéseket hajthatja végre:
   
   1. Futtassa az **eventvwr** parancsot a Windows-ügyfélen. Ezzel elindítja az Eseménynaplót.
   2. A **Műveletek** ablaktáblán kattintson a **Mentett napló megnyitása** elemre, és mutasson a naplófájlokra etvx/etw formátumban (a támogatási csomag). Most már megtekintheti a fájlt. A fájl megnyitása után a jobb gombbal kattinthat, és szövegként mentheti a fájlt.
      
      > [!IMPORTANT]
      > A **Get-WinEvent** parancsmag segítségével is megnyithatja ezeket a fájlokat a Windows PowerShellben. További információ: [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) a Windows PowerShell-parancsmag referenciadokumentációjában.
     
5. Amikor a naplók megnyílnak az Eseménynaplóban, keresse meg az alábbi naplókat, amelyek az eszköz konfigurációjával kapcsolatos problémákat tartalmaznak:
   
   * hcs_pfconfig/működési napló
   * hcs_pfconfig/Config
6. A naplófájlokban keresse meg a telepítővarázsló által hívott parancsmagokhoz kapcsolódó karakterláncokat. Lásd az [első beállítási varázsló folyamatát](#first-time-setup-wizard-process) ezeknek a parancsmagoknak a listájáról.
7. Ha nem tudja kideríteni a probléma okát, a következő lépésekért forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md) Használja a [Támogatási kérelem létrehozása](storsimple-8000-contact-microsoft-support.md#create-a-support-request) című részben leírt lépéseket, amikor segítségért forduljon a Microsoft támogatási szolgálatához.

## <a name="cmdlets-available-for-troubleshooting"></a>Hibaelhárításhoz rendelkezésre álló parancsmagok
A következő Windows PowerShell-parancsmagokkal észlelheti a csatlakozási hibákat.

* `Get-NetAdapter`: Ezzel a parancsmagval észlelheti a hálózati adapterek állapotát.
* `Test-Connection`: Ezzel a parancsmaggal ellenőrizheti a hálózaton belüli és kívüli hálózati kapcsolatot.
* `Test-HcsmConnection`: Ezzel a parancsmagval ellenőrizheti a sikeresen regisztrált eszköz kapcsolatát.
* `Sync-HcsTime`: Ezzel a parancsmaggal megjelenítheti az eszköz idejét, és kényszerítheti az ntp-kiszolgálóval való időszinkronizálást.
* `Enable-HcsPing`és `Disable-HcsPing`: Használja ezeket a parancsmagokat, hogy a gazdagépek pingelhetjék a hálózati adaptereket a StorSimple eszközön. Alapértelmezés szerint a StorSimple hálózati csatolók nem válaszolnak a pingkérésekre.
* `Trace-HcsRoute`: Használja ezt a parancsmagot útvonal-nyomkövetési eszközként. Csomagokat küld minden útválasztónak a végső célfelé vezető úton egy adott időszakban, majd kiszámítja az eredményeket az egyes ugrásokból visszaadott csomagok alapján. Mivel `Trace-HcsRoute` egy adott útválasztón vagy kapcsolaton a csomagvesztés mértékét mutatja, meghatározhatja, hogy mely útválasztók vagy kapcsolatok okozhatnak hálózati problémákat.
* `Get-HcsRoutingTable`: Ezzel a parancsmagval jelenítheti meg a helyi IP-útválasztási táblát.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Hibaelhárítás a Get-NetAdapter parancsmaggal
Amikor hálózati adaptereket állít be az eszköz első központi telepítéséhez, a hardver állapota nem érhető el a StorSimple Eszközkezelő szolgáltatás felhasználói felületén, mert az eszköz még nincs regisztrálva a szolgáltatásban. Emellett előfordulhat, hogy a **Hardver állapotpanel** nem mindig tükrözi megfelelően az eszköz állapotát, különösen akkor, ha olyan problémák merülnek fel, amelyek befolyásolják a szolgáltatás szinkronizálását. Ezekben az esetekben a `Get-NetAdapter` parancsmag segítségével meghatározhatja a hálózati adapterek állapotát és állapotát.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Az eszközön lévő összes hálózati adapter listájának megtekintése
1. Indítsa el a Windows PowerShell torSimple-hez, majd írja be a következőt: `Get-NetAdapter`. 
2. A hálózati adapter `Get-NetAdapter` állapotának megismeréséhez használja a parancsmag kimenetét és az alábbi irányelveket.
   
   * Ha a kapcsolat kifogástalan és engedélyezve van, az **ifIndex** állapota **Up**.
   * Ha a kapcsolat kifogástalan, de fizikailag nem csatlakozik (hálózati kábellel), az **ifIndex** **letiltva**jelenik meg.
   * Ha a kapcsolat kifogástalan, de nincs engedélyezve, az **ifIndex** állapota NotPresent néven jelenik **meg.**
   * Ha a kapcsolat nem létezik, akkor nem jelenik meg ebben a listában. A StorSimple Eszközkezelő szolgáltatás felhasználói felülete továbbra is hibás állapotban jeleníti meg ezt a felületet.

A parancsmag használatáról a [Get-NetAdapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) a Windows PowerShell-parancsmag hivatkozási lapban található további információért.

A következő szakaszok a `Get-NetAdapter` parancsmag kimeneti mintáit mutatják be.

 Ezekben a mintákban a 0 vezérlő volt a passzív vezérlő, és a következőképpen lett konfigurálva:

* Data 0, DATA 1, DATA 2 és DATA 3 hálózati interfészek léteztek az eszközön.
* A DATA 4 és a DATA 5 hálózati kártyák nem voltak jelen; ezért ezek nem szerepelnek a kimenetben.
* A DATA 0 engedélyezve volt.

Az 1-es vezérlő volt az aktív vezérlő, és a következőképpen lett konfigurálva:

* Data 0, DATA 1, DATA 2, DATA 3, DATA 4 és DATA 5 hálózati interfészek léteztek az eszközön.
* A DATA 0 engedélyezve volt.

**Mintakimenet – vezérlő 0**

A következő a 0 vezérlő (a passzív vezérlő) kimenete. Data 1, DATA 2 és DATA 3 nincsenek csatlakoztatva. A DATA 4 és a DATA 5 nem jelenik meg, mert nincsenek jelen az eszközön.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Mintakimenet – vezérlő 1**

A következő az 1-es vezérlő (az aktív vezérlő) kimenete. Csak az eszközön lévő DATA 0 hálózati adapter van konfigurálva és működik.

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


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Hibaelhárítás a testkapcsolat parancsmaggal
A `Test-Connection` parancsmag segítségével meghatározhatja, hogy a StorSimple eszköz csatlakozhat-e a külső hálózathoz. Ha a beállítási varázslóban az összes hálózati paraméter, beleértve a DNS-t is, megfelelően van konfigurálva, a `Test-Connection` parancsmag segítségével pingelhet egy ismert címet a hálózaton kívül, például outlook.com.

Ha a ping le van tiltva, engedélyezze a pingelést a parancsmaggal kapcsolatos csatlakozási problémák elhárításához.

Tekintse meg a `Test-Connection` parancsmagból származó kimenet alábbi mintáit.

> [!NOTE]
> Az első mintában az eszköz helytelen DNS-sel van konfigurálva. A második mintában a DNS helyes.

**Mintakimenet – helytelen DNS**

A következő példában nincs kimenet az IPV4- és Az IPV6-címekhez, ami azt jelzi, hogy a DNS nincs feloldva. Ez azt jelenti, hogy nincs kapcsolat a külső hálózattal, és megfelelő DNS-t kell biztosítani.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Minta kimenet – helyes DNS**

A következő mintában a DNS az IPV4-címet adja vissza, jelezve, hogy a DNS megfelelően van konfigurálva. Ez megerősíti, hogy van kapcsolat a külső hálózattal.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Hibaelhárítás a Test-HcsmConnection parancsmaggal
Használja `Test-HcsmConnection` a parancsmabot egy eszköz, amely már csatlakozik a StorSimple Device Manager szolgáltatáshoz, és regisztrált. Ez a parancsmag segít a regisztrált eszköz és a megfelelő StorSimple Eszközkezelő szolgáltatás közötti kapcsolat ellenőrzésében. Ezt a parancsot a StorSimple-hez való Windows PowerShell en futtathatja.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>A Test-HcsmConnection parancsmag futtatása
1. Ellenőrizze, hogy az eszköz regisztrálva van-e.
2. Ellenőrizze az eszköz állapotát. Ha az eszköz inaktív, karbantartási módban vagy offline módban van, az alábbi hibák egyike jelenhet meg:
   
   * ErrorCode.CiSDeviceDecommissioned – ez azt jelzi, hogy az eszköz inaktív.
   * ErrorCode.DeviceNotReady – ez azt jelzi, hogy az eszköz karbantartási módban van.
   * ErrorCode.DeviceNotReady – ez azt jelzi, hogy az eszköz nincs online állapotban.
3. Ellenőrizze, hogy a StorSimple Eszközkezelő szolgáltatás fut-e (használja a [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) parancsmag). Ha a szolgáltatás nem fut, a következő hibák jelenhetnek meg:
   
   * Errorcode.CiSapplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – ez azt jelzi, hogy kivétel történt a Get-ClusterResource (Get-ClusterResource) futtatásakor.
4. Ellenőrizze a hozzáférés-vezérlési szolgáltatás (ACS) jogkivonatát. Ha webes kivételt okoz, az átjáróprobléma, hiányzó proxyhitelesítés, helytelen DNS vagy hitelesítési hiba eredménye lehet. A következő hibák jelenhetnek meg:
   
   * ErrorCode.CiSApplianceGateway – ez egy HttpStatusCode.BadGateway kivételt jelez: a névfeloldó szolgáltatás nem tudta feloldani az állomásnevet.
   * ErrorCode.CiSApplianceProxy – ez egy HttpStatusCode.ProxyAuthenticationRequired kivételt (407-es HTTP-állapotkód) jelzi): az ügyfél nem tudott hitelesíteni a proxykiszolgálóval.
   * ErrorCode.CiSApplianceDNSError – ez webExceptionstatus.nameresolutionfailure kivételt jelez: a névfeloldó szolgáltatás nem tudta feloldani az állomásnevet.
   * ErrorCode.CiSApplianceACSError – ez azt jelzi, hogy a szolgáltatás hitelesítési hibát adott vissza, de van kapcsolat.
     
     Ha nem jelent webes kivételt, ellenőrizze az ErrorCode.CiSApplianceFailure fájlt. Ez azt jelzi, hogy a készülék meghibásodott.
5. Ellenőrizze a felhőszolgáltatás kapcsolatát. Ha a szolgáltatás webes kivételt okoz, a következő hibák jelenhetnek meg:
   
   * ErrorCode.CiSApplianceGateway – ez egy HttpStatusCode.BadGateway kivételt jelez: egy köztes proxykiszolgáló rossz kérést kapott egy másik proxytól vagy az eredeti kiszolgálótól.
   * ErrorCode.CiSApplianceProxy – ez egy HttpStatusCode.ProxyAuthenticationRequired kivételt (407-es HTTP-állapotkód) jelzi): az ügyfél nem tudott hitelesíteni a proxykiszolgálóval.
   * ErrorCode.CiSApplianceDNSError – ez webExceptionstatus.nameresolutionfailure kivételt jelez: a névfeloldó szolgáltatás nem tudta feloldani az állomásnevet.
   * ErrorCode.CiSApplianceACSError – ez azt jelzi, hogy a szolgáltatás hitelesítési hibát adott vissza, de van kapcsolat.
     
     Ha nem jelent webes kivételt, ellenőrizze az ErrorCode.CiSApplianceSaasServiceError.If it does not throw a web exception, check for ErrorCode.CiSApplianceSaasServiceError. Ez a StorSimple Eszközkezelő szolgáltatással kapcsolatos problémára utal.
6. Ellenőrizze az Azure Service Bus-kapcsolatot. Az ErrorCode.CiSApplianceServiceBusError azt jelzi, hogy az eszköz nem tud csatlakozni a service bushoz.

A CiSCommandletLog0Curr.errlog és a CiSAgentsvc0Curr.errlog naplófájlok további információkat tartalmaznak, például a kivétel részleteit.

A parancsmag használatáról a Windows PowerShell referenciadokumentációjában található [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) című részben olvashat bővebben.

> [!IMPORTANT]
> Ezt a parancsmalapot az aktív és a passzív vezérlőhöz is futtathatja.

Tekintse meg a `Test-HcsmConnection` parancsmagból származó kimenet alábbi mintáit.

**Mintakimenet – sikeresen regisztrált eszköz, amely en fut a StorSimple Update 3**

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

**Mintakimenet – offline eszköz** 

Ez a minta egy olyan eszközről származik, amely **offline** állapotú az Azure Portalon.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

Az eszköz nem tudott csatlakozni az aktuális webproxy-konfigurációval. Ez a probléma a webproxy-konfigurációval vagy a hálózati kapcsolattal kapcsolatos probléma lehet. Ebben az esetben győződjön meg arról, hogy a web proxy beállítások helyesek, és a web proxy szerverek online és elérhető.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Hibaelhárítás a Sync-HcsTime parancsmaggal
Ezzel a parancsmagval megjelenítheti az eszköz idejét. Ha az eszköz idő eltolása van az NTP-kiszolgálóval, akkor ezzel a parancsmaggal kényszerítheti az idő és az NTP-kiszolgáló közötti szinkronizálást.
- Ha az eszköz és az NTP-kiszolgáló közötti eltolás 5 percnél hosszabb, figyelmeztetés jelenik meg. 
- Ha az eltolás meghaladja a 15 percet, akkor az eszköz offline állapotba kerül. Ezzel a parancsmagval továbbra is kényszerítheti az időszinkronizálást. 
- Ha azonban az eltolás meghaladja a 15 órát, akkor nem tudja kényszeríteni az idő szinkronizálását, és hibaüzenet jelenik meg.

**Mintakimenet – kényszerített időszinkronizálás a Sync-HcsTime használatával**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Hibaelhárítás az Enable-HcsPing és disable-HcsPing parancsmagokkal
Ezekkel a parancsmagokkal biztosíthatja, hogy az eszköz hálózati interfészei reagáljanak az ICMP pingkérésekre. Alapértelmezés szerint a StorSimple hálózati csatolók nem válaszolnak a pingkérésekre. Ezzel a parancsmaghasználatával a legegyszerűbben tudhatja, hogy az eszköz online állapotban van-e, és elérhető-e.

**Mintakimenet – Enable-HcsPing és Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Hibaelhárítás a Trace-HcsRoute parancsmaggal
Ezt a parancsmamot útvonalnyomat-ellenőrző eszközként használja. Csomagokat küld minden útválasztónak a végső célfelé vezető úton egy adott időszakban, majd kiszámítja az eredményeket az egyes ugrásokból visszaadott csomagok alapján. Mivel a parancsmag egy adott útválasztón vagy kapcsolaton a csomagvesztés mértékét mutatja, meghatározhatja, hogy mely útválasztók vagy kapcsolatok okozhatnak hálózati problémákat.

**Mintakimenet, amely bemutatja, hogyan lehet nyomon követni egy csomag útvonalát a Trace-HcsRoute segítségével**

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

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Hibaelhárítás a Get-HcsRoutingTable parancsmaggal
Ezzel a parancsmagból megtekintheti a StorSimple-eszköz útválasztási tábláját. Az útválasztási tábla olyan szabálykészlet, amely segít meghatározni, hogy az IP-hálózaton keresztül áthaladó adatcsomagok hová legyenek irányítva.

Az útválasztási tábla az okat a megadott hálózatokhoz vezető összeköttetéseket és átjárót jeleníti meg. Azt is ad az útválasztási metrika, amely a döntéshozó az adott cél eléréséhez megtett útvonalat. Minél alacsonyabb az útválasztási metrika, annál nagyobb a preferencia.

Ha például 2 hálózati interfész ( DATA 2 és DATA 3 ) csatlakozik az internethez. Ha a DATA 2 és a DATA 3 útválasztási metrikák 15, illetve 261, majd a DATA 2 az alacsonyabb útválasztási metrika az előnyben részesített felület az internet eléréséhez használt.

Ha az 1. Ez azt jelenti, hogy még ha vannak más felhőalapú interfészek is, a felhőforgalom a DATA 0-n keresztül lesz irányítva.

Ha a `Get-HcsRoutingTable` parancsmast paraméterek megadása nélkül futtatja (ahogy az a következő példa is mutatja), a parancsmag iPv4- és IPv6-útválasztási táblákat is kiad. Másik lehetőségként `Get-HcsRoutingTable -IPv4` megadhatja `Get-HcsRoutingTable -IPv6` vagy beszerezheti a megfelelő útválasztási táblát.

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

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Példa a StorSimple részletes hibaelhárítási példájára
A következő példa a StorSimple központi telepítésének lépésről lépésre történő hibaelhárítását mutatja be. A példaforgatókönyvben az eszközregisztráció sikertelen, és hibaüzenet jelzi, hogy a hálózati beállítások vagy a DNS-név helytelen.

A visszaadott hibaüzenet a következő:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

A hibát az alábbi lehetőségek bármelyike okozhatja:

* Helytelen hardvertelepítés
* Hibás hálózati adapter(ek)
* Helytelen IP-cím, alhálózati maszk, átjáró, elsődleges DNS-kiszolgáló vagy webproxy
* Helytelen regisztrációs kulcs
* Helytelen tűzfalbeállítások

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Az eszközregisztrációs probléma megkeresése és javítása
1. Ellenőrizze az eszköz konfigurációját: az `Invoke-HcsSetupWizard`aktív vezérlőn futtassa a futtassa a futtassa a futtassa a futtassa.
   
   > [!NOTE]
   > A telepítővarázslónak az aktív vezérlőn kell futnia. Annak ellenőrzéséhez, hogy csatlakozik-e az aktív vezérlőhöz, tekintse meg a soros konzolon megjelenő szalagcímet. A szalagcím azt jelzi, hogy a 0-as vagy az 1-es vezérlőhöz csatlakozik-e, és hogy a vezérlő aktív vagy passzív. További információt az [Aktív vezérlő azonosítása az eszközön](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)című területen talál.
   
2. Ellenőrizze, hogy a készülék megfelelően van-e kábelezve: ellenőrizze a hálózati kábelezést a készülék hátsó síkján. A kábelezés az eszköz típusára jellemző. További információ: [Telepítse a StorSimple 8100 eszközt,](storsimple-8100-hardware-installation.md) vagy [telepítse a StorSimple 8600 eszközt.](storsimple-8600-hardware-installation.md)
   
   > [!NOTE]
   > Ha 10 GbE hálózati portot használ, a megadott QSFP-SFP adaptereket és SFP-kábeleket kell használnia. További információt a [10 GbE-portokhoz ajánlott kábelek, kapcsolók és adó-vevők listájában talál.](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)
  
3. Ellenőrizze a hálózati adapter állapotát:
   
   * A Get-NetAdapter parancsmag segítségével észlelheti a DATA 0 hálózati csatolóinak állapotát. 
   * Ha a kapcsolat nem működik, az **ifindex** állapota azt jelzi, hogy a kapcsolat nem működik. Ezután ellenőriznie kell a port hálózati kapcsolatát a készülékhez és a kapcsolóhoz. Azt is meg kell, hogy zárja ki a rossz kábeleket. 
   * Ha azt gyanítja, hogy az aktív vezérlő DATA 0 portja meghibásodott, ezt az 1-es vezérlő DATA 0 portjához való csatlakozással erősítheti meg. Ennek megerősítéséhez válassza le a hálózati kábelt az eszköz hátuljáról a 0 vezérlőről, csatlakoztassa a kábelt az 1-es vezérlőhöz, majd futtassa újra a Get-NetAdapter parancsmagát.
     Ha egy vezérlő DATA 0 portja meghibásodik, a következő lépésekért forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md) Előfordulhat, hogy ki kell cserélnie a vezérlőt a rendszeren.
4. Ellenőrizze a kapcsolóhoz való csatlakozást:
   
   * Győződjön meg arról, hogy a DATA 0 hálózati csatolók a 0 vezérlőn és az 1-es vezérlőn az elsődleges házban ugyanazon az alhálózaton találhatók. 
   * Ellenőrizze az elosztót vagy az útválasztót. Általában mindkét vezérlőt ugyanahhoz az elosztóhoz vagy útválasztóhoz kell csatlakoztatni. 
   * Győződjön meg arról, hogy a kapcsolathoz használt kapcsolók data 0-val rendelkeznek az azonos vLAN-hálózat mindkét vezérlőjéhez.
5. A felhasználói hibák kiküszöbölése:
   
   * Futtassa újra a telepítővarázslót (futtassa az **Invoke-HcsSetupWizard programot),** és adja meg újra az értékeket, hogy megbizonyosodjon arról, hogy nincsenek hibák. 
   * Ellenőrizze a használt regisztrációs kulcsot. Ugyanaz a regisztrációs kulcs több eszköz csatlakoztatására használható a StorSimple Eszközkezelő szolgáltatáshoz. A Szolgáltatás [regisztrációs kulcsának beszerezhetése](storsimple-8000-manage-service.md#get-the-service-registration-key) című eljárással győződjön meg arról, hogy a megfelelő regisztrációs kulcsot használja.
     
     > [!IMPORTANT]
     > Ha több szolgáltatás fut, meg kell győződnie arról, hogy a regisztrációs kulcs a megfelelő szolgáltatás regisztrálásához. Ha nem a megfelelő StorSimple Eszközkezelő szolgáltatással regisztrált egy eszközt, a következő lépésekért kapcsolatba kell [lépnie](storsimple-8000-contact-microsoft-support.md) a Microsoft támogatási szolgálatával. Előfordulhat, hogy az eszköz gyári alaphelyzetbe állítását kell végrehajtania (ami adatvesztést okozhat), majd csatlakoztatnia kell a kívánt szolgáltatáshoz.
     > 
     > 
6. A Test-Connection parancsmag segítségével ellenőrizze, hogy rendelkezik-e kapcsolattal a külső hálózathoz. További információt a [Hibaelhárítás a Tesztkapcsolat parancsmaggal című témakörben](#troubleshoot-with-the-test-connection-cmdlet)talál.
7. Ellenőrizze, hogy nem interferencia-e a tűzfal. Ha ellenőrizte, hogy a virtuális IP-cím (VIP), az alhálózat, az átjáró és a DNS-beállítások helyesek-e, és továbbra is kapcsolódási problémák at látnak, akkor lehetséges, hogy a tűzfal blokkolja az eszköz és a külső hálózat közötti kommunikációt. Győződjön meg arról, hogy a 80-as és 443-as portok elérhetők a StorSimple-eszközön a kimenő kommunikációhoz. További információ: [Hálózati követelmények a StorSimple eszközhöz.](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)
8. Nézd meg a naplókat. Nyissa meg [a hibaelhárításhoz rendelkezésre álló támogatási csomagok at és eszköznaplókat.](#support-packages-and-device-logs-available-for-troubleshooting)
9. Ha az előző lépések nem oldják meg a problémát, kérjen segítséget a [Microsoft támogatási szolgálatátél.](storsimple-8000-contact-microsoft-support.md)

## <a name="next-steps"></a>További lépések
[Ismerje meg, hogyan hárítható el a Diagnosztika eszköz a StorSimple eszközök hibaelhárításához.](storsimple-8000-diagnostics.md)

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
