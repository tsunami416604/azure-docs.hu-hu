---
title: A StorSimple 8000 sorozat üzembe helyezés hibáinak elhárítása |} A Microsoft Docs
description: Ismerteti, hogyan diagnosztizálhatja és megoldhatja az első StorSimple üzembe előforduló hibákat.
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
ms.openlocfilehash: 61719d482a4db1c737bbe38277f2ac3b2d684b63
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342433"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>A StorSimple eszköz üzembe helyezés hibáinak elhárítása
## <a name="overview"></a>Áttekintés
Ez a cikk a Microsoft Azure StorSimple telepítés hasznos hibaelhárítási útmutatót. Leírja, gyakoribb problémákhoz, a lehetséges okok és a javasolt lépések az StorSimple konfigurálásakor az esetleg előforduló problémák megoldásához nyújt segítséget. 

Ez az információ a StorSimple 8000 sorozatú fizikai eszköz és a StorSimple felhőalapú készülék is vonatkozik.

> [!NOTE]
> Eszköz konfigurációs kapcsolatos problémák, amelyek fellépő akkor fordulhat elő, amikor az eszköz először telepít, vagy akkor fordulhat elő, később, ha az eszköz működik. Ez a cikk foglalkozik az első központi telepítési problémák elhárítása. Lépjen a egy működési eszköz elhárításához [a egy működési eszköz hibáinak elhárítása a diagnosztikai eszközt használhatja](storsimple-8000-diagnostics.md).

Ez a cikk is ismerteti a StorSimple-környezetek hibaelhárítási eszközei, és a egy részletes hibaelhárítási példa biztosít.

## <a name="first-time-deployment-issues"></a>Első üzembe helyezési problémák
Ha egy probléma az eszköz első telepítésekor, vegye figyelembe a következőket:

* Ha egy fizikai eszköz hibaelhárítást, ellenőrizze, hogy a hardver telepítve lett, és leírtak szerint konfigurálta [telepítse a StorSimple 8100 sorozatú eszköz](storsimple-8100-hardware-installation.md) vagy [telepítse a StorSimple 8600 sorozatú eszköz](storsimple-8600-hardware-installation.md).
* Az üzembe helyezési Előfeltételek ellenőrzése. Győződjön meg arról, hogy rendelkezik-e a leírt adatokat, a [üzembehelyezési konfigurációs ellenőrzőlista](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Tekintse át a StorSimple kibocsátási megjegyzések megtekintéséhez, ha a probléma leírása. A kibocsátási megjegyzések az ismert telepítési hibáinak lehetséges megoldásai közé tartozik. 

Eszköz üzembe helyezése során a leggyakoribb problémák face fordulhat elő, felhasználók által a varázsló futtatásakor, és ha azok regisztrálja az eszközt Windows PowerShell storsimple-höz készült. (Windows PowerShell használatával a storsimple regisztrálta és konfigurálta a StorSimple-eszköz. Az eszköz regisztrálása a további információkért lásd: [3. lépés: eszköz konfigurálása és regisztrálása a Windows PowerShell storsimple-höz készült](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Az alábbi szakaszok segítségével jobban meg először a StorSimple-eszköz konfigurálásakor előforduló problémák megoldására.

## <a name="first-time-setup-wizard-process"></a>Először a telepítő varázsló folyamat
Az alábbi lépéseket a beállítási varázsló folyamat foglalják össze. Részletes információkért lásd: [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).

1. Futtassa a [Invoke-hcssetupwizard parancsmagot](https://technet.microsoft.com/library/dn688135.aspx) parancsmag segítségével indítsa el a telepítővarázslót, amely végigvezeti Önt a hátralévő lépéseket. 
2. A hálózat konfigurálása: a telepítő varázsló lehetővé teszi a DATA 0 hálózati adapter hálózati beállításainak konfigurálása a StorSimple eszközön. Ezek a beállítások a következők:
   * Virtuális IP-cím (VIP), alhálózati maszk és átjáró – a [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) parancsmag a háttérben hajtja végre. Konfigurálja az IP-cím, alhálózati maszk és a DATA 0 hálózati adapter átjárójának a StorSimple eszközön.
   * Elsődleges DNS-kiszolgáló – a [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) parancsmag a háttérben hajtja végre. Konfigurálja a DNS-beállításait a StorSimple-megoldásokra.
   * NTP-kiszolgáló – a [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) parancsmag a háttérben hajtja végre. A StorSimple megoldás az NTP-kiszolgáló beállításait konfigurálja.
   * Nem kötelező webalkalmazás-proxy – a [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) parancsmag a háttérben hajtja végre. Azt állítja be, és lehetővé teszi, hogy a webproxy konfigurálása a StorSimple megoldáshoz.
3. Állítsa be a jelszót: a következő lépés az, hogy állítsa be az eszköz rendszergazdai jelszava.
   Az eszköz rendszergazdai jelszava segítségével jelentkezzen be az eszközt. Az eszköz alapértelmezett jelszava: **Password1**.
        
     > [!IMPORTANT]
     > Jelszavak regisztráció előtt gyűjteni, de csak az eszköz sikeres regisztrálása után alkalmazza. Egy jelszót a alkalmazni hiba történik, ha a program kéri a jelszót újra megadni, mindaddig, amíg a szükséges jelszavakat (összetettségi követelményeit kielégítő) gyűjtött.
     
4. Regisztrálja az eszközt: az utolsó lépés az, hogy regisztrálja az eszközt a Microsoft Azure-ban futó StorSimple-Eszközkezelő szolgáltatással. A regisztráció megköveteli, hogy [kérnie a Szolgáltatásregisztrációs kulcsot](storsimple-8000-manage-service.md#get-the-service-registration-key) az Azure Portalról, és adja meg azt a varázslóban. **Miután az eszköz regisztrálása sikeres volt, a szolgáltatásadat-titkosítási kulcs kapja meg. Ügyeljen arra, a titkosítási kulcsot egy biztonságos helyre, mert lesz szükség az összes ezt követő eszközök regisztrálása a szolgáltatással.**

## <a name="common-errors-during-device-deployment"></a>Eszköz üzembe helyezése során előforduló gyakori hibák
Az alábbi táblázatok sorolják fel, hogy mikor találkozhat a gyakori hibák meg:

* Konfigurálja a szükséges hálózati beállításokat.
* Nem kötelező webproxy-beállításainak konfigurálása.
* Állítsa be az eszköz rendszergazdai jelszava.
* Regisztrálja az eszközt.

## <a name="errors-during-the-required-network-settings"></a>A szükséges hálózati beállításokat során hibák
| Nem. | Hibaüzenet | Lehetséges okok | Javasolt művelet |
| --- | --- | --- | --- |
| 1 |Invoke-hcssetupwizard: parancsmagot A Ez a parancs csak az aktív vezérlőn kell futtatni. |A passzív vezérlő konfigurációs végrehajtása. |Az aktív vezérlőt a következő parancs futtatásával. További információkért lásd: [azonosíthatja az eszközt az aktív vezérlő](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-hcssetupwizard: parancsmagot Az eszköz nem áll készen. |A DATA 0 hálózati kapcsolódási probléma adódik. |Ellenőrizze a fizikai hálózati kapcsolatot, a DATA 0. |
| 3 |Invoke-hcssetupwizard: parancsmagot Nincs IP-címek ütközése a hálózaton lévő más rendszerrel (kivétel HRESULT: 0x80070263). |A megadott DATA 0 IP-címet már egy másik rendszer használatban volt. |Adjon meg egy új IP-címet, amely nem használja. |
| 4 |Invoke-hcssetupwizard: parancsmagot a fürt erőforrásai nem sikerült. (Kivétel HRESULT: 0x800713AE). |Duplikált VIP-címhez. A megadott IP-cím már használatban van. |Adjon meg egy új IP-címet, amely nem használja. |
| 5 |Invoke-hcssetupwizard: parancsmagot Érvénytelen IPv4-cím. |Az IP-cím formátuma nem megfelelő megtalálható. |Ellenőrizze a formátumot, és adja meg újra az IP-címe. További információkért lásd: [Ipv4-címzési][1]. |
| 6 |Invoke-hcssetupwizard: parancsmagot Érvénytelen IPv6-cím. |Az IP-cím formátuma nem megfelelő megtalálható. |Ellenőrizze a formátumot, és adja meg újra az IP-címe. További információkért lásd: [IPv6-címzés][2]. |
| 7 |Invoke-hcssetupwizard: parancsmagot Végpontleképzőben nincs több végpont elérhető. (Kivétel HRESULT: 0x800706D9) |A fürt funkció nem működik. |[Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) a következő lépéseket. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>A webproxy beállításai nem kötelező során hibák
| Nem. | Hibaüzenet | Lehetséges okok | Javasolt művelet |
| --- | --- | --- | --- |
| 1 |Invoke-hcssetupwizard: parancsmagot Érvénytelen paraméter (kivétel HRESULT: 0x80070057) |A proxybeállítások megadott paraméterek egyike érvénytelen. |Nincs megadva az URI-t a megfelelő formátumban. Használja a következő formátumot: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 |Invoke-hcssetupwizard: parancsmagot RPC-kiszolgáló nem érhető el (kivétel HRESULT: 0x800706ba) |Okozza-e az alábbi lehetőségek közül:<ol><li>A fürt nem akár.</li><li>A passzív vezérlő nem tud kommunikálni az aktív vezérlőt, és a parancs futtatása a passzív vezérlő.</li></ol> |Attól függően, az alapvető ok:<ol><li>[Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) , győződjön meg arról, hogy a fürt működik.</li><li>Az aktív vezérlőn futtassa a parancsot. Ha szeretné a passzív vezérlő futtassa a parancsot, szüksége lesz, győződjön meg arról, hogy a passzív vezérlő képes-e kommunikálni az aktív vezérlőt. Kell [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) Ha megszakad a kapcsolat.</li></ol> |
| 3 |Invoke-hcssetupwizard: parancsmagot RPC-hívása sikertelen volt (kivétel HRESULT: 0x800706be) |Fürt nem működik. |[Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) , győződjön meg arról, hogy a fürt működik. |
| 4 |Invoke-hcssetupwizard: parancsmagot a fürt nem található az erőforrás (kivétel HRESULT: 0x8007138f) |A fürt erőforrás nem található. Ez akkor fordulhat elő, amikor a telepítés nem volt megfelelő. |Szükség lehet visszaállítani az eszközt a gyári alapértelmezett beállításokra. [Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) fürt erőforrás létrehozásához. |
| 5 |Invoke-hcssetupwizard: parancsmagot a fürt erőforrás nincs online állapotban (kivétel HRESULT: 0x8007138c) |Fürt erőforrásai nem érhetők el. |[Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) a következő lépéseket. |

## <a name="errors-related-to-device-administrator-password"></a>Eszköz rendszergazdai jelszava kapcsolatos hibák
Az eszköz alapértelmezett rendszergazdai jelszava: **jelszó1**. Ezt a jelszót az első bejelentkezéskor; után jár le ezért szüksége lesz a telepítővarázsló használata a módosításhoz. Egy új eszköz rendszergazdai jelszava meg kell adnia, amikor először regisztrálja az eszközt. 

Győződjön meg arról, hogy a jelszavak az alábbi követelményeknek:

* Az eszköz rendszergazdai jelszavának 8 – 15 karakter között kell lennie.
* Jelszavak 3 az alábbi típusú 4 karaktert tartalmaznia kell: kisbetűk, nagybetűk, számjegyeket és speciális. 
* A jelszó nem lehet ugyanaz, mint az utolsó 24 jelszavakat.

Továbbá ne feledje, hogy a jelszavak évente lejár, és csak az eszköz sikeres regisztráció után módosítható. Ha a regisztráció bármilyen okból nem sikerül, a jelszavak nem módosulnak.

További információk az eszköz rendszergazdai jelszava, [a StorSimple-Eszközkezelő szolgáltatás használata a StorSimple-jelszó módosítása](storsimple-8000-change-passwords.md).

Az eszköz-rendszergazdai és a StorSimple Snapshot Manager jelszavak beállítása során fordulhatnak elő hibák a következők közül legalább egyet.

| Nem. | Hibaüzenet | Javasolt művelet |
| --- | --- | --- |
| 1 |A jelszó meghaladja a maximális hosszúságot. |Az eszköz rendszergazdai jelszavának 8 – 15 karakter között kell lennie. |
| 2 |A jelszó nem felel meg a hosszát. |Az eszköz rendszergazdai jelszavának 8 – 15 karakter között kell lennie.|
| 3 |A jelszónak tartalmaznia kell a kisbetűs karaktereket. |Jelszavak 3 az alábbi típusú 4 karaktert tartalmaznia kell: kisbetűk, nagybetűk, számjegyeket és speciális. Győződjön meg arról, hogy a jelszó megfelel-e ezeket a követelményeket. |
| 4 |A jelszónak tartalmaznia kell karaktereket. |Jelszavak 3 az alábbi típusú 4 karaktert tartalmaznia kell: kisbetűk, nagybetűk, számjegyeket és speciális. Győződjön meg arról, hogy a jelszó megfelel-e ezeket a követelményeket. |
| 5 |A jelszónak tartalmaznia kell a következő speciális karaktereket. |Jelszavak 3 az alábbi típusú 4 karaktert tartalmaznia kell: kisbetűk, nagybetűk, számjegyeket és speciális. Győződjön meg arról, hogy a jelszó megfelel-e ezeket a követelményeket. |
| 6 |A jelszónak tartalmaznia kell a következő 4 karakter típusú 3: nagybetűket, kisbetűket, számjegyeket és speciális. |A jelszó nem tartalmaz a szükséges típusú karakter. Győződjön meg arról, hogy a jelszó megfelel-e ezeket a követelményeket. |
| 7 |A paraméter nem egyezik meg a megerősítést. |Győződjön meg arról, hogy a jelszó összes követelményének megfelel-e, és hogy helyesen írta be. |
| 8 |A jelszó nem egyezhet meg az alapértelmezett. |Az alapértelmezett jelszó az *jelszó1*. A jelszó módosítására, először a bejelentkezés után kell. |
| 9 |A megadott jelszó nem egyezik meg az eszköz jelszavát. Írja be újra a jelszót. |Ellenőrizze a jelszót, és írja be újra. |

Jelszavak összegyűjtése, mielőtt az eszköz regisztrálva van, de csak a sikeres regisztrációt követően lesznek alkalmazva. A jelszó-helyreállítási munkafolyamat szükséges regisztrálni az eszközt.

> [!IMPORTANT]
> Általában egy jelszót a alkalmazni tett kísérlet sikertelen, ha ezután a szoftver ismételten megkísérli gyűjtéséhez a jelszót, egészen addig, amíg a sikeres. Ritka esetekben a jelszó nem lehet alkalmazni. Ebben a helyzetben regisztrálja az eszközt, és folytatható, azonban a jelszavak nem módosulnak. Az Azure Portalról a regisztráció után módosíthatja az eszköz rendszergazdai jelszava.


Alaphelyzetbe állíthatja a jelszót az Azure Portalon a StorSimple-Eszközkezelő szolgáltatáson keresztül. További információért ugorjon [módosítsa az eszköz rendszergazdai jelszava](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Az eszköz regisztrálása során hibák
A Microsoft Azure-ban futó StorSimple-Eszközkezelő szolgáltatás segítségével regisztrálja az eszközt. Sikerült találkozik egy vagy több, a következő problémák eszközregisztráció során.

| Nem. | Hibaüzenet | Lehetséges okok | Javasolt művelet |
| --- | --- | --- | --- |
| 1 |350027. hiba: Nem sikerült regisztrálni az eszközt a StorSimple Device Manager. | |Várjon néhány percet, és ezután próbálja megismételni a műveletet. Ha a probléma tartósan fennáll, [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 2 |350013. hiba: Hiba történt az eszköz regisztrálásakor. Ennek oka hibás Szolgáltatásregisztrációs kulcs lehet. | |Regisztrálja újra az eszközt a helyes regisztrációs kulccsal. További információkért lásd: [kérnie a Szolgáltatásregisztrációs kulcsot.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |350063. hiba: A regisztráció azonban a StorSimple-Eszközkezelő szolgáltatásban való hitelesítés nem sikerült. Ismételje meg a műveletet egy kis idő múlva. |Ez a hiba azt jelzi, hogy az ACS-hitelesítés lejárt, de a szolgáltatáshoz a register-hívás sikertelen. Ennek oka lehet az eredménye, egy szórványos hálózati hiba okozhatta. |Ha a probléma tartósan fennáll, kérjük [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 4 |350049. hiba: A szolgáltatás nem érhető el regisztrációja során. |Ha a kérés érkezett a szolgáltatáshoz, egy webes kivétel érkezett. Bizonyos esetekben ez lehetséges, hogy első által meghatározott megismételni a műveletet később. |Tekintse meg az IP-cím, illetve a DNS-nevet, és próbálkozzon újra a művelettel. Ha a probléma tartósan fennáll, [forduljon a Microsoft Support.](storsimple-8000-contact-microsoft-support.md) |
| 5 |350031. hiba: Az eszköz már regisztrálva van. | |Nincs szükség műveletre. |
| 6 |350016. hiba: Nem sikerült regisztrálni az eszközt. | |Győződjön meg arról, hogy a regisztrációs kulcs helyességét. |
| 7 |Invoke-hcssetupwizard: parancsmagot Hiba történt az eszköz; regisztrálása során Ezt okozhatja helytelen IP-címe vagy DNS-nevét. Ellenőrizze a hálózati beállításokat, és próbálkozzon újra. Ha a probléma tartósan fennáll, [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md). (350050 hiba) |Győződjön meg arról, hogy az eszköz pingelése a külső hálózattal. Ha nem rendelkezik külső hálózathoz való kapcsolódás, a regisztráció sikertelen lehet a hibával. Ez a hiba lehet kombinációját az alábbiak közül:<ul><li>Helytelen IP</li><li>Helytelen alhálózat</li><li>Hibás átjáró</li><li>Helytelen DNS-beállítások</li></ul> |Tekintse meg a lépéseket a [részletes hibaelhárítási példa](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-hcssetupwizard: parancsmagot Az aktuális művelet belső szolgáltatáshiba [0x1FBE2] miatt nem sikerült. Várjon egy kis ideig, majd ismételje meg a műveletet. Ha a probléma tartósan fennáll, forduljon a Microsoft Support. |Ez az általános hiba lépett fel az összes felhasználó nem látható hiba a szolgáltatás vagy az ügynök. Ennek leggyakoribb oka lehet, hogy az ACS-hitelesítés sikertelen. A hiba egyik lehetséges oka az, hogy a probléma adódik az NTP-kiszolgáló konfigurációját, és az eszközön lévő idő nincs megfelelően beállítva. |Javítsa ki a time (ha nincsenek problémák), és ismételje meg a regisztrációs művelet. Ha a Set-HcsSystem - Timezone parancs használatával állítsa be az időzónát, szókezdő időzónájában (például "csendes-óceáni téli idő").  Ha a probléma tartósan fennáll, [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) a következő lépéseket. |
| 9 |Figyelmeztetés: Nem sikerült aktiválni az eszközön. Az eszköz-rendszergazdai és a StorSimple Snapshot Manager jelszavát nem módosult. |Ha a regisztráció meghiúsul, az eszköz-rendszergazdai és a StorSimple Snapshot Manager jelszavát nem változnak. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>A StorSimple-környezetek hibaelhárítási eszközei
A StorSimple segítségével hibaelhárítása a StorSimple megoldás több eszközt biztosít. Ezek a következők:

* Támogatási csomagok és eszköznaplók.
* A parancsmagok kifejezetten a hibaelhárításhoz.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Támogatja a csomagokat és a rendelkezésre álló eszköznaplók hibaelhárításhoz
A támogatási csomagot, amely segíthet az eszközzel kapcsolatos problémák elhárítása a Microsoft Support csapat az összes releváns napló tartalmazza. Storsimple-höz készült Windows PowerShell segítségével hozzon létre egy titkosított támogatási csomagot, majd megoszthatja a támogatási csoporthoz.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>A naplókban vagy a támogatási csomag tartalmának megtekintése
1. Storsimple-höz készült Windows PowerShell segítségével hozzon létre egy támogatási csomagot, leírtak szerint [létrehozása és kezelése a támogatási csomagot](storsimple-8000-create-manage-support-package.md).
2. Töltse le a [visszafejtési parancsfájl](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) helyileg, az ügyfélszámítógépen.
3. Ezzel [lépésről lépésre haladó eljárás](storsimple-8000-create-manage-support-package.md#edit-a-support-package) megnyitásához, és fejti vissza a támogatási csomagot.
4. A visszafejtett támogatási csomag naplók etw/etvx formátumban vannak. Végezheti megtekinteni ezeket a fájlokat a Windows eseménynaplóban az alábbi lépéseket:
   
   1. Futtassa a **eventvwr** parancsot a Windows-ügyfélen. Ekkor elindul az Eseménynapló.
   2. Az a **műveletek** ablaktáblán kattintson a **naplófájl megnyitása** és a naplófájlok etvx/etw-formátumban (a támogatási csomag) mutasson. Most már megtekintheti a fájlt. Miután megnyitotta a fájlt, kattintson a jobb gombbal, és mentse a fájlt szövegként.
      
      > [!IMPORTANT]
      > Is használhatja a **Get-WinEvent** nyissa meg ezeket a fájlokat a Windows PowerShell-parancsmagot. További információkért lásd: [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) a a Windows PowerShell-parancsmagjának referenciadokumentációja.
     
5. Ha a naplók az Eseménynapló megnyitásához keresse meg a következő naplók kapcsolódnak, amely tartalmazza az eszköz konfigurációjával kapcsolatos problémák:
   
   * hcs_pfconfig/műveleti napló
   * hcs_pfconfig/Config
6. A naplófájlokban keressen rá a hívja meg a telepítővarázsló parancsmagokra karakterláncokat. Lásd: [első beállítási varázsló folyamat](#first-time-setup-wizard-process) ezek a parancsmagok listáját.
7. Ha nem tudja megállapítani a hiba okát, is [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) a következő lépéseket. Szereplő lépések segítségével [hozzon létre egy támogatási kérést](storsimple-8000-contact-microsoft-support.md#create-a-support-request) amikor Support kapcsolatba segítségért.

## <a name="cmdlets-available-for-troubleshooting"></a>A parancsmagok felhasználhatók a hibaelhárítás során
A következő Windows PowerShell-parancsmagok használatával észleli a kapcsolati hibákat.

* `Get-NetAdapter`: Ez a parancsmag használata a hálózati adapterek állapotának észleléséhez.
* `Test-Connection`: A parancsmag használatával ellenőrizze a hálózati kapcsolatot belüli és kívüli a hálózaton.
* `Test-HcsmConnection`: A parancsmag használatával ellenőrizze a kapcsolatot az sikeresen regisztrált eszközök.
* `Sync-HcsTime`: A parancsmag használatával megjelenítheti az eszközidőt, és kényszerítheti az idő az NTP-kiszolgálóval.
* `Enable-HcsPing` és `Disable-HcsPing`: ezeket a parancsmagokat használja, hogy a gazdagépek pingelni a hálózati adapterek, a StorSimple eszközön. Alapértelmezés szerint a StorSimple hálózati adapterek nem válaszolnak a ping-kérelmekre.
* `Trace-HcsRoute`: Ezt a parancsmagot használja egy útvonal-nyomkövetési eszközzel. Minden egyes útválasztóhoz a végső rendeltetési csomagokat küld egy időszakban, és majd kiszámítja az egyes ugrásokból visszatért csomagok eredményeket. Mivel `Trace-HcsRoute` tudja melyik útválasztók, vagy hivatkozásokat a hálózati problémát okozó előfordulhat, hogy a csomagvesztés bármely adott útválasztó vagy a hivatkozás, mértékét mutatja.
* `Get-HcsRoutingTable`: A parancsmag használatával megjeleníti a helyi IP-útválasztási táblázatához.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>A Get-NetAdapter parancsmaggal hibaelhárítása
Hálózati adapterek üzembe helyezéséhez először eszköz konfigurálásakor a hardver állapota nem érhető el a StorSimple-Eszközkezelő szolgáltatásban felhasználói felület, mert az eszköz még nincs regisztrálva a szolgáltatásban. Ezenkívül a **hardverállapot** panel nem mindig megfelelően tükrözik az eszköz állapotáról különösen akkor, ha nincsenek problémák, amelyek befolyásolják a szinkronizálási szolgáltatás. Ebben az esetben is használhatja a `Get-NetAdapter` parancsmag állapotát és a hálózati adapterek állapotának meghatározásához.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>A hálózati adapterek listájának megtekintéséhez az eszközön
1. Indítsa el a Windows PowerShell storsimple-höz készült, és írja be `Get-NetAdapter`. 
2. Használja a kimenetét a `Get-NetAdapter` parancsmag és az alábbi irányelveket a hálózati adapter állapotának megismeréséhez.
   
   * Ha a kapcsolat kifogástalan és engedélyezett, a **ifIndex** állapot jelenik meg **mentése**.
   * Ha a kapcsolat állapota kifogástalan, de nem csatlakozik fizikai (a hálózati kábel), a **ifIndex** jelenik meg, mint **letiltott**.
   * Ha a kapcsolat megfelelő, de nincs engedélyezve, a **ifIndex** állapot jelenik meg **NotPresent**.
   * Ha a kapcsolat nem létezik, nem jelenik meg ebben a listában. A StorSimple-Eszközkezelő szolgáltatás felhasználói felületének továbbra is megjeleníti a kapcsolat hibás állapotban.

Ez a parancsmag használatával további információkért lépjen [Get-NetAdapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) a a Windows PowerShell-parancsmagok leírása.

A következő szakaszok bemutatják a kimenetét a minták a `Get-NetAdapter` parancsmagot.

 Ezek a minták vezérlő 0 a passzív vezérlő lett, és a következőképpen volt konfigurálva:

* DATA 0, 1 adatok, DATA 2 és DATA 3 hálózati adapterek létezett az eszközön.
* ADATOK 4 és az adatok 5 hálózati kártyák nem volt jelen; ezért nem szerepelnek a kimenetben.
* DATA 0 engedélyezett.

1. vezérlő lett az aktív vezérlőt, és a következőképpen volt konfigurálva:

* DATA 0, 1 adatok, DATA 2, DATA 3, adatok 4 és DATA 5 hálózati adaptereket létezett az eszközön.
* DATA 0 engedélyezett.

**Példa a kimenetre – 0. vezérlő**

Az alábbiakban látható a vezérlő 0 (a passzív vezérlő) kimenetét. 1 adatok, DATA 2 és DATA 3 nincs csatlakoztatva. 4 adatok és az adatok 5 nem szerepelnek, mivel azok nem található az eszközön.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Példa a kimenetre – 1. vezérlő**

Az alábbiakban látható a vezérlő 1 (az aktív vezérlőn) kimenetét. Csak a DATA 0 hálózati adapter az eszközön van konfigurálva, és működik.

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


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Hibaelhárítás a Test-Connection-parancsmag segítségével
Használhatja a `Test-Connection` parancsmag használatával határozza meg, hogy a StorSimple-eszköz a külső hálózathoz is csatlakoztathatja. Ha a hálózati paramétereket, beleértve a DNS-ben, a telepítővarázsló megfelelően vannak konfigurálva, akkor használhatja a `Test-Connection` parancsmagot, hogy a hálózathoz, például az outlook.com-on kívül egy ismert címet pingelje.

Engedélyeznie kell a ping a ezzel a parancsmaggal a kapcsolati hibák elhárításához, ha a ping parancs le van tiltva.

Tekintse meg a következő minták kimenetét a `Test-Connection` parancsmagot.

> [!NOTE]
> Az eszköz az első példában egy helytelen DNS van beállítva. A második példában a DNS-ben helyességéről.

**Példa a kimenetre – helytelen DNS**

Az alábbi minta nem semmilyen kimenet az IPV4 és IPV6-cím, amely azt jelzi, hogy a DNS-ben továbbra is fennáll. Ez azt jelenti, hogy nincs kapcsolat a külső hálózathoz, és egy megfelelő DNS kell adni.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Példa a kimenetre – helyes DNS**

A következő mintát a DNS-ben adja vissza az IPV4-címet, jelezve, hogy a DNS megfelelően van-e konfigurálva. Ez megerősíti, hogy nincs-e kapcsolat a külső hálózathoz.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Hibaelhárítás a Test-HcsmConnection parancsmag segítségével
Használja a `Test-HcsmConnection` parancsmag egy eszköz, amely már csatlakozik, és regisztrálta a StorSimple-Eszközkezelő szolgáltatásban. Ez a parancsmag segítségével ellenőrizze a kapcsolatot egy regisztrált eszközt és a megfelelő StorSimple-Eszközkezelő szolgáltatás között. Ez a parancs a Windows PowerShell storsimple-höz készült futtassa.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>A Test-HcsmConnection parancsmag futtatásához
1. Győződjön meg arról, hogy az eszköz regisztrálva van-e.
2. Az eszköz állapotának ellenőrzéséhez. Ha az eszköz az inaktiválása, karbantartási módban, vagy offline, láthatja, hibák a következők egyikét:
   
   * ErrorCode.CiSDeviceDecommissioned – Ez jelzi, hogy az eszköz az inaktiválása.
   * ErrorCode.DeviceNotReady – Ez jelzi, hogy az eszköz karbantartási módban van.
   * ErrorCode.DeviceNotReady – Ez azt jelzi, hogy az eszköz nem online.
3. Ellenőrizze, hogy a StorSimple-Eszközkezelő szolgáltatás fut-e (használja a [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) parancsmag). Ha a szolgáltatás nem fut, a következő hibák jelenhetnek meg:
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – Ez jelzi, hogy kivétel történt, a Get-ClusterResource futtatásakor.
4. Ellenőrizze az Access Control Service (ACS) tokent. Ha egy webes kivétel azt jelez, az eredmény egy átjárót a probléma, egy hiányzó proxy hitelesítése, egy helytelen DNS vagy a hitelesítési hiba lehet. A következő hibaüzenetek jelenhetnek meg:
   
   * ErrorCode.CiSApplianceGateway – Ez azt jelzi, hogy HttpStatusCode.BadGateway kivétel: a név feloldó szolgáltatást nem sikerült feloldani a gazdagép nevét.
   * ErrorCode.CiSApplianceProxy – Ez azt jelzi, hogy HttpStatusCode.ProxyAuthenticationRequired kivétel (HTTP-állapotkódot 407-es): az ügyfél nem sikerült hitelesíteni a proxykiszolgálót.
   * ErrorCode.CiSApplianceDNSError – Ez azt jelzi, hogy egy WebExceptionStatus.NameResolutionFailure kivétel: a név feloldó szolgáltatást nem sikerült feloldani a gazdagép nevét.
   * ErrorCode.CiSApplianceACSError – Ez azt jelzi, hogy a szolgáltatás-hitelesítési hibát adott vissza, de nincs kapcsolat.
     
     Ha ez nem lépett egy webes kivétel, ellenőrizze ErrorCode.CiSApplianceFailure. Ez azt jelzi, hogy a berendezés sikertelen volt.
5. Ellenőrizze a cloud service-kapcsolatot. Ha a szolgáltatás egy webes kivételt jelez, a következő hibák jelenhetnek meg:
   
   * ErrorCode.CiSApplianceGateway – Ez azt jelzi, hogy HttpStatusCode.BadGateway kivétel: egy közbülső proxy server hibás kérelem érkezett, egy másik proxy vagy az eredeti kiszolgálón.
   * ErrorCode.CiSApplianceProxy – Ez azt jelzi, hogy HttpStatusCode.ProxyAuthenticationRequired kivétel (HTTP-állapotkódot 407-es): az ügyfél nem sikerült hitelesíteni a proxykiszolgálót.
   * ErrorCode.CiSApplianceDNSError – Ez azt jelzi, hogy egy WebExceptionStatus.NameResolutionFailure kivétel: a név feloldó szolgáltatást nem sikerült feloldani a gazdagép nevét.
   * ErrorCode.CiSApplianceACSError – Ez azt jelzi, hogy a szolgáltatás-hitelesítési hibát adott vissza, de nincs kapcsolat.
     
     Ha ez nem lépett egy webes kivétel, ellenőrizze ErrorCode.CiSApplianceSaasServiceError. Ez a StorSimple-Eszközkezelő szolgáltatással kapcsolatos problémát jelez.
6. Ellenőrizze az Azure Service Bus kapcsolatát. ErrorCode.CiSApplianceServiceBusError azt jelzi, hogy az eszköz a Service Bus nem lehet csatlakozni.

A naplófájlok CiSCommandletLog0Curr.errlog és CiSAgentsvc0Curr.errlog olyan további információkat, például a kivétel részletei.

A parancsmag használatával kapcsolatos további információkért lépjen [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) a Windows PowerShell-referenciák dokumentációiba.

> [!IMPORTANT]
> Ezt a parancsmagot az aktív és a passzív vezérlő is futtathatja.

Tekintse meg a következő minták kimenetét a `Test-HcsmConnection` parancsmagot.

**Kimeneti – a StorSimple Update 3-es sikeresen regisztrált eszközök**

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

**Példa a kimenetre – a kapcsolat nélküli eszköz** 

Ez a minta állapotú eszközről van **Offline** az Azure Portalon.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

Az eszköz nem tudott csatlakozni az aktuális webproxy konfigurálása. Ez lehet egy probléma a webproxy konfigurálása vagy hálózati kapcsolati probléma. Ebben az esetben győződjön meg arról, hogy a webproxy beállításai megfelelőek, és a webproxy-kiszolgálók online üzemmódban és érhető el.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Hibaelhárítás a Sync-HcsTime parancsmaggal
Ez a parancsmag használatával megjelenítheti az eszközidőt. Ha az eszköz ideje szerint az eltolás az NTP-kiszolgálóval rendelkezik, ez a parancsmag ezután használhatja force-szinkronizálása az idő az NTP-kiszolgálóval.
- Ha az eszköz és az NTP-kiszolgáló között az eltolás nagyobb, mint 5 perc, figyelmeztetés jelenik meg. 
- Az eltolás meghaladja a 15 perc, ha az eszköz offline állapotban kerül. Ez a parancsmag használatával kényszerítheti az idő továbbra is. 
- Azonban ha az eltolás nagyobb, mint 15 órát, akkor Ön nem fogja tudni kényszerített szinkronizálni az időt és a egy hibaüzenet jelenik.

**Kimeneti – segítségével a Sync-HcsTime kényszerített idő szinkronizálása**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Hibaelhárítás az Enable-HcsPing és a Disable-HcsPing parancsmagok segítségével
Ezek a parancsmagok használatával győződjön meg arról, hogy az eszközön a hálózati adapterek válaszol az ICMP ping kérelmekre. Alapértelmezés szerint a StorSimple hálózati adapterek nem válaszolnak a ping-kérelmekre. Ez a parancsmag használata a legegyszerűbben úgy tudja, ha az eszköz online és elérhető.

**Kimeneti – Enable-HcsPing és a Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>A nyomkövetés-HcsRoute parancsmaggal hibaelhárítása
Használja ezt a parancsmagot egy útvonal-nyomkövetési eszközzel. Minden egyes útválasztóhoz a végső rendeltetési csomagokat küld egy időszakban, és majd kiszámítja az egyes ugrásokból visszatért csomagok eredményeket. A parancsmag a csomagvesztés bármely adott útválasztó vagy hivatkozás mértékét jeleníti meg, mert tudja melyik útválasztók, vagy előfordulhat, hogy a hivatkozások hálózati problémát okozó.

**Kimeneti: hogyan kell egy csomagot a nyomkövetés-HcsRoute útvonalának követése**

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

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Hibaelhárítás a Get-HcsRoutingTable parancsmag segítségével
Ez a parancsmag segítségével megtekintheti az útválasztási táblázat a StorSimple eszközhöz. Útválasztási táblázat olyan készlete, szabályok, amelyek segíthetnek meghatározni, ahol a rendszer átirányítja az Internet Protocol (IP) hálózaton keresztül továbbított adatok csomagokat.

Az útválasztási táblázat a felületek és az átjáró, amely az adatokat a megadott hálózatok irányítja. Az útválasztási metrikát, amely az adott cél eléréséhez útvonalát döntéshozóként is biztosít. Az alacsonyabb a útválasztási mérőszám, a magasabb prioritást.

Például ha 2 hálózati adaptere, DATA 2 és DATA 3, csatlakozik az internethez. Ha a DATA 2 és DATA 3 útválasztási metrikák 15 és 261 jelölik, az alacsonyabb útválasztási metrikájú DATA 2 az előnyben részesített felület az Internet eléréséhez.

1. frissítést a StorSimple eszközön futnak, ha a DATA 0 hálózati adapter rendelkezik a legmagasabb a felhőalapú forgalom beállításait. Ez azt jelenti, hogy akkor is, ha nincsenek egyéb felhőalapú felületek, a felhőalapú forgalom lesz átirányítva, az adatok 0 keresztül.

Ha futtatja a `Get-HcsRoutingTable` parancsmag (mint az alábbi példában látható) paraméterek megadása nélkül a parancsmag kimenete az IPv4 és az IPv6-útválasztási táblázatok. Másik lehetőségként megadhatja `Get-HcsRoutingTable -IPv4` vagy `Get-HcsRoutingTable -IPv6` egy megfelelő útválasztási tábla beolvasása.

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

## <a name="step-by-step-storsimple-troubleshooting-example"></a>A StorSimple részletes hibaelhárítási példa
Az alábbi példa bemutatja, hogy a StorSimple-KözpontiTelepítés részletes hibaelhárítási. A példaforgatókönyvben az eszköz regisztrálása sikertelen lesz, és a egy üzenet, miszerint a hálózati beállításokat, vagy a DNS-neve helytelen.

A hibaüzenet a következő:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

A hiba oka lehet a következők egyikét:

* Helytelen hardver telepítése
* Hibás hálózati adapter(ek)
* Helytelen az IP-cím, alhálózati maszk, átjáró, elsődleges DNS-kiszolgáló vagy webalkalmazás-proxy
* Nem megfelelő Szolgáltatásregisztrációs kulcs
* Helytelen tűzfal beállításai

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Keresse meg és javítsa ki az eszköz regisztrációs hiba
1. Ellenőrizze az eszköz konfigurációját: az aktív vezérlőn futtassa `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > A telepítővarázsló az aktív vezérlőn kell futtatni. Annak ellenőrzéséhez, hogy csatlakozik az aktív vezérlőt, tekintse meg a soros konzol megjelenő szalagcím. A szalagcím csatlakoznak, hogy a vezérlő 0 vagy 1. vezérlő és a tartományvezérlő-e aktív vagy passzív jelzi. További információért ugorjon [azonosíthatja az eszközt az aktív vezérlő](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Győződjön meg arról, hogy az eszköz megfelelően van-e bekábelezte: Ellenőrizze a hálózati kábeleket vissza adatsík az eszközön. A kábelezést csak az eszköz típusa. További információért ugorjon [telepítse a StorSimple 8100 sorozatú eszköz](storsimple-8100-hardware-installation.md) vagy [telepítse a StorSimple 8600 sorozatú eszköz](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Ha a 10 GbE hálózati portokat használja, szüksége lesz a megadott QSFP-SFP-adapterek és kábelek SFP. További információkért lásd: a [kábelek, kapcsolók és adó-vevők javasolt a 10 GbE-portok listája](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. A hálózati adapter állapotának ellenőrzése:
   
   * A Get-NetAdapter parancsmaggal a hálózati adapterek állapotának észleléséhez a DATA 0. 
   * A hivatkozás nem működik, ha a **ifindex** állapotát jelzi, hogy a kapcsolat le van-e. Ezután ellenőrizze a hálózati kapcsolatot, a port, a készülék és a kapcsolóhoz kell. Zárja ki a hibás kábeleket is kell. 
   * Ha azt gyanítja, hogy az adat 0 port, az aktív vezérlőn sikertelen volt, akkor ezt úgy ellenőrizheti az adatokhoz való csatlakozásról az 1. vezérlő 0 port. Ennek ellenőrzéséhez bontsa a kapcsolatot a hálózati kábel a háttérben az eszköz a vezérlő 0, és csatlakoztassa a kábeleket, 1. vezérlő, majd futtassa ismét a Get-NetAdapter parancsmaggal.
     Ha az adat 0 port egy tartományvezérlőn sikertelen, [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) a következő lépéseket. Szüksége lehet a rendszeren a vezérlő cseréje.
4. Ellenőrizze a kapcsolatot, a kapcsolóhoz:
   
   * Győződjön meg arról, hogy a vezérlő 0 és az elsődleges ház az 1. vezérlő DATA 0 hálózati adapterek ugyanazon az alhálózaton. 
   * Ellenőrizze a hub vagy az útválasztón. Mindkét vezérlő csatlakozzon általában a azonos hub vagy az útválasztón. 
   * Győződjön meg arról, hogy a kapcsolókat, használja a kapcsolat mindkét vezérlőn az ugyanazon VLAN rendelkezik az adatok 0.
5. Bármely felhasználói hibák kiküszöbölése:
   
   * Indítsa újra a telepítővarázslót (Futtatás **Invoke-hcssetupwizard parancsmagot**), és adja meg az értékeket ismét a győződjön meg arról, hogy nincsenek-e hibák. 
   * Ellenőrizze a regisztrációs kulcsot használja. Ugyanazzal a regisztrációs kulccsal való csatlakozáshoz egy StorSimple-Eszközkezelő szolgáltatás több eszközön használható. Ismertetett eljárással [kérnie a Szolgáltatásregisztrációs kulcsot](storsimple-8000-manage-service.md#get-the-service-registration-key) annak érdekében, hogy használja a helyes regisztrációs kulccsal.
     
     > [!IMPORTANT]
     > Ha több szolgáltatást futtató, szüksége lesz, győződjön meg arról, hogy a regisztrációs kulcsot a megfelelő szolgáltatás segítségével regisztrálja az eszközt. Ha a nem megfelelő StorSimple-Eszközkezelő szolgáltatásban regisztrált eszközt, kell [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) a következő lépéseket. Előfordulhat, hogy hajtsa végre a gyári beállítások visszaállításakor az eszköz (ami adatvesztéssel járhat) majd csatlakoztassa a kívánt szolgáltatást.
     > 
     > 
6. A Test-Connection-parancsmag használatával ellenőrizze, hogy rendelkezik-e a külső hálózattal való kapcsolat. További információért ugorjon [hibaelhárítás a Test-Connection parancsmaggal](#troubleshoot-with-the-test-connection-cmdlet).
7. Ellenőrizze, hogy a tűzfal zavaró tényező. Ha meggyőződött róla, hogy a virtuális IP-cím (VIP), alhálózat, átjáró és DNS-beállítások helyességét, és a kapcsolódási problémák továbbra is megjelenik, majd lehetséges, hogy a tűzfal blokkolja az eszköz és a külső hálózat közötti kommunikációt. Győződjön meg arról, hogy elérhetők-e 80-as és 443-as porton a kimenő kommunikáció a StorSimple eszközön kell. További információkért lásd: [a StorSimple eszköz hálózatkezelési követelményei](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Tekintse meg a naplókat. Lépjen a [támogatja a csomagokat és a rendelkezésre álló eszköznaplók hibaelhárítási](#support-packages-and-device-logs-available-for-troubleshooting).
9. Ha a fenti lépések nem oldják meg a probléma elhárításához [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) segítségért.

## <a name="next-steps"></a>További lépések
[Ismerje meg, hogyan a diagnosztikai eszközt használhatja a StorSimple eszköz](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
