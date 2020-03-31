---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 7700f1c92aecab76dbc347814b7b161bc3d822a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179175"
---
### <a name="to-configure-and-register-the-device"></a>Az eszköz konfigurálása és regisztrálása
1. Nyissa meg a Windows PowerShell felületet a StorSimple-eszköz soros konzoljában. Az erre vonatkozó utasítások [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console) című szakaszban találhatók. **Pontosan kövesse az ismertetett eljárást, különben nem fog tudni hozzáférni a konzolhoz.**
2. A megnyitott munkamenetben nyomja le egyszer az **Enter** billentyűt a parancssor megjelenítéséhez.
3. Ekkor a rendszer arra kéri, hogy válassza ki az eszközhöz beállítani kívánt nyelvet. Válassza ki a nyelvet, majd nyomja le az **Enter** billentyűt.
   
    ![StorSimple-eszköz konfigurálása és regisztrálása, 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. A soros konzol megjelenő menüjében válassza az 1. lehetőséget a **teljes körű hozzáféréssel való bejelentkezéshez**.
   
    ![StorSimple-eszköz regisztrálása, 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Hajtsa végre az alábbi lépéseket az eszköz minimálisan szükséges hálózati beállításainak konfigurálásához.
   
   > [!IMPORTANT]
   > Ezeket a konfigurálási lépéseket az eszköz aktív vezérlőjén kell elvégezni. A vezérlő állapota a soros konzol menüjének címsorában látható. Ha nem csatlakozik az aktív vezérlőhöz, bontsa a kapcsolatot, majd csatlakozzon az aktív vezérlőhöz.
   
   1. Írja be a jelszót a parancssorba. Az eszköz alapértelmezett jelszava: **Password1**.
   2. Írja be a következő parancsot:
      
        `Invoke-HcsSetupWizard`
   3. Ekkor megjelenik egy telepítővarázsló az eszköz hálózati beállításainak konfigurálásához. Adja meg az alábbi adatokat:
      
      * A DATA 0 hálózati adapter IP-címe
      * Alhálózati maszk
      * Átjáró
      * Az elsődleges DNS-kiszolgáló IP-címe
      * Az elsődleges NTP-kiszolgáló IP-címe
      
      > [!NOTE]
      > Előfordulhat, hogy várnia kell néhány percet az alhálózati maszk és a DNS-beállítások alkalmazására.
    
   4. Szükség esetén konfigurálja a webproxy-kiszolgálót.
      
      > [!IMPORTANT]
      > Bár a webproxy konfigurálása nem kötelező, vegye figyelembe, hogy ha webproxyt használ, azt csak itt tudja beállítani. További információ: [Configure web proxy for your device](../articles/storsimple/storsimple-configure-web-proxy.md) (Webproxy beállítása az eszközhöz).
     
6. Nyomja le a Ctrl + C billentyűkombinációt a beállítási varázslóból való kilépéshez.
8. Futtassa a következő parancsmast, hogy az eszközt a Microsoft Azure Government portálra irányítsa (mivel alapértelmezés szerint a nyilvános Azure klasszikus portálra mutat). Ez mindkét vezérlőt újraindítja. Azt javasoljuk, hogy két PuTTY-munkamenetet használjon egyidejűleg mindkét vezérlőhöz való csatlakozáshoz, hogy láthassa, mikor indul újra az egyes vezérlők.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Megjelenik egy megerősítő üzenet. Fogadja el az alapértelmezett (**Y**).
9. A telepítés folytatásához futtassa a következő parancsmast:
   
    `Invoke-HcsSetupWizard`
   
    ![A telepítő folytatása varázsló](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. Fogadja el a hálózati beállításokat. Az egyes beállítások elfogadása után megjelenik egy érvényesítési üzenet.
11. Biztonsági okokból az eszköz rendszergazdai jelszava az első munkamenet végeztével lejár, így ezt most meg kell változtatnia. Amikor a rendszer erre kéri, adjon meg az eszközhöz egy rendszergazdai jelszót. Az eszköz rendszergazdai jelszavának 8–15 karakter hosszúságúnak kell lennie. A jelszónak az alábbiak közül hármat tartalmaznia kell: kisbetűk, nagybetűk, számok és speciális karakterek.
    
    <br/>![StorSimple-eszköz regisztrálása, 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. A telepítővarázsló utolsó lépéseként regisztrálja az eszközt a StorSimple-eszközkezelő szolgáltatásban. Ehhez szüksége lesz a szolgáltatás regisztrációs kulcs, hogy beszerzett [lépés 2: Szerezd meg a szolgáltatás regisztrációs kulcs](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key). Előfordulhat, hogy a regisztrációs kulcs megadása után 2–3 percet várni kell az eszköz regisztrálására.
    
    > [!NOTE]
    > A Ctrl + C billentyűkombinációval bármikor kiléphet a varázslóból. Ha minden hálózati beállítást megadott (a Data 0 IP-címe, alhálózati maszk és átjáró), akkor a bejegyzéseket megőrzi a rendszer.
    
    ![StorSimple regisztráció folyamata](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. Az eszköz regisztrálása után megjelenik egy szolgáltatásadat-titkosítási kulcs. Másolja ki ezt a kulcsot, és mentse egy biztonságos helyre. **Ez a kulcs szükséges a szolgáltatás regisztrációs kulcsregisztrálásához további eszközök a StorSimple Eszközkezelő szolgáltatás regisztrálásához.** További információk a kulccsal kapcsolatban: [A StorSimple biztonsági megoldásai](../articles/storsimple/storsimple-8000-security.md).
    
    ![StorSimple-eszköz regisztrálása, 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > Ha ki szeretné másolni a szöveget a soros konzol ablakából, egyszerűen jelölje ki azt. A szöveget ezután a vágólapra vagy bármilyen szövegszerkesztőbe beillesztheti.
    > 
    > NE használja a **Ctrl + C** billentyűt a szolgáltatásadat-titkosítási kulcs másolásához. A **Ctrl + C billentyűkombináció** val a beállítási varázslóból való kilépést eredményezi. Ennek eredményeképpen az eszköz rendszergazdai jelszava nem változik meg, és az eszköz visszaállítja az alapértelmezett jelszót.
    
14. Lépjen ki a soros konzolból.
15. Térjen vissza az Azure Government Portalra, és hajtsa végre az alábbi lépéseket:
    
    1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást.
    2. Kattintson az **Eszközök** elemre. Az eszközök listájában azonosítsa a telepíti az eszközt. Ellenőrizze, hogy az eszköz sikeresen csatlakozott-e a szolgáltatáshoz az állapot megkeresésével. Az eszköznek **Online** állapotúnak kell lennie.
            
        Ha az eszköz állapota **Offline**, akkor várjon néhány percet, amíg az eszköz online állapotú nem lesz.
       
        Ha az eszköz néhány perc eltelte után is offline állapotú, akkor ellenőrizze, hogy a tűzfallal védett hálózat [a StorSimple-eszköz hálózati követelményeiben](../articles/storsimple/storsimple-8000-system-requirements.md) leírtak szerint van-e beállítva.
       
        Ellenőrizze, hogy a 9354-es port kimenő kommunikációra nyitva van-e, mivel az a StorSimple-eszközkezelő szolgáltatás és az eszköz közötti kommunikációt lebonyolító Service Bus működéséhez szükséges.

