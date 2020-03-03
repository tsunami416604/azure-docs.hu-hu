---
title: Az Azure AD jelszavas védelem üzembe helyezése – Azure Active Directory
description: Az Azure AD jelszavas védelem üzembe helyezése a helytelen jelszavak betiltásához a helyszínen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a3eb121b68311084fd516c6abb7e00ad70eba8b
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226825"
---
# <a name="deploy-azure-ad-password-protection"></a>Azure AD jelszóvédelem üzembe helyezése

Most, hogy megértette, [Hogyan kényszerítheti ki a Windows Server Active Directory Azure ad jelszavas védelmét](concept-password-ban-bad-on-premises.md), a következő lépés az üzemelő példány megtervezése és végrehajtása.

## <a name="deployment-strategy"></a>Központi telepítési stratégia

Javasoljuk, hogy a központi telepítéseket vizsgálati módban indítsa el. A naplózási mód az alapértelmezett kezdeti beállítás, ahol a jelszavak továbbra is megadhatók. A blokkolni kívánt jelszavakat az eseménynapló rögzíti. A proxykiszolgáló és a tartományvezérlő-ügynökök vizsgálati módban való üzembe helyezése után figyelje meg, hogy milyen hatással lesz a jelszó-házirend a felhasználókra és a környezetre, amikor a házirend érvénybe lép.

A naplózási szakaszban számos szervezet találja a következőket:

* A biztonságosabb jelszavak használatához javítaniuk kell a meglévő működési folyamatokat.
* A felhasználók gyakran nem biztonságos jelszavakat használnak.
* Tájékoztatniuk kell a felhasználókat a biztonsági kényszerítés közelgő változásáról, a lehetséges hatásokról, valamint a biztonságosabb jelszavak kiválasztásáról.

Az is lehetséges, hogy az erősebb jelszó-érvényesítés hatással van a meglévő Active Directory tartományvezérlő üzembe helyezési automatizálására. Javasoljuk, hogy a naplózási időszak kiértékelése során legalább egy DC-előléptetést és egy tartományvezérlő-lefokozást hajtson végre, hogy segítse az ilyen problémák előzetes felfedését.  További információkért lásd:

* [Az Ntdsutil. exe nem tud beállítani egy gyenge címtárszolgáltatás-javító mód jelszavát](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [A tartományvezérlő replikájának előléptetése sikertelen a Címtárszolgáltatások helyreállító módjának jelszava miatt](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [A tartományvezérlő lefokozása nem sikerült, mert gyenge a helyi rendszergazda jelszava](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Ha a szolgáltatás egy ésszerű időszakra vonatkozóan a vizsgálati módban fut, a konfigurációt átválthatja a *naplózásból* , hogy a *kényszerítse* a biztonságosabb jelszavak megkövetelését. A célzott monitorozás ebben az időszakban jó ötlet.

## <a name="deployment-requirements"></a>Telepítési követelmények

* Az Azure AD jelszavas védelemmel kapcsolatos licencelési követelmények a következő cikkben találhatók: [helytelen jelszavak eltávolítása a szervezetében](concept-password-ban-bad.md#license-requirements).
* Az Azure AD jelszavas védelem tartományvezérlő-ügynök szoftverét futtató összes gépnek Windows Server 2012 vagy újabb rendszernek kell futnia. Ez a követelmény nem jelenti azt, hogy a Active Directory tartománynak vagy erdőnek Windows Server 2012 tartomány vagy erdő működési szintjén kell lennie. A [tervezési alapelvekben](concept-password-ban-bad-on-premises.md#design-principles)említettek szerint nem szükséges minimális DFL vagy FFL a DC-ügynök vagy a proxy szoftver futtatásához.
* A DC Agent szolgáltatást futtató összes gépnek telepítve kell lennie a .NET 4,5-nek.
* Az Azure AD jelszavas védelem-proxy szolgáltatást futtató összes gépnek Windows Server 2012 R2 vagy újabb rendszernek kell futnia.
   > [!NOTE]
   > A proxykiszolgáló üzembe helyezése kötelezően szükséges az Azure AD jelszavas védelem üzembe helyezéséhez, annak ellenére, hogy a tartományvezérlő rendelkezik kimenő közvetlen internetkapcsolattal. 
   >
* Minden olyan gépen, amelyen telepítve van az Azure AD jelszavas védelmi proxy szolgáltatás, telepítve kell lennie a .NET 4,7-nek.
  A .NET 4,7-es verziójához már telepítve kell lennie egy teljesen frissített Windows Server rendszerre. Ha szükséges, töltse le és futtassa a [Windows rendszerhez készült .NET-keretrendszer 4,7 offline telepítőjének](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)telepítőjét.
* Az Azure AD jelszavas védelem összetevőit tartalmazó összes gépnek telepítve kell lennie az univerzális C futtatókörnyezetnek. A futtatókörnyezet beszerzéséhez győződjön meg arról, hogy Windows Update összes frissítését elvégezte. Azt is megteheti, hogy egy operációs rendszerre vonatkozó frissítési csomagban van. További információ: az [univerzális C futtatókörnyezet frissítése a Windowsban](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* A hálózati kapcsolatnak léteznie kell legalább egy, az egyes tartományokban lévő tartományvezérlő és legalább egy olyan kiszolgáló között, amely a proxykiszolgáló számára a jelszavas védelmet tárolja. Ez a kapcsolat lehetővé teszi, hogy a tartományvezérlő hozzáférhessen az RPC Endpoint Mapper port 135-es portjához és a proxykiszolgáló RPC-kiszolgáló portjához. Alapértelmezés szerint az RPC-kiszolgáló portja egy dinamikus RPC-port, de konfigurálható [statikus port használatára](#static)is.
* Minden olyan gépen, amelyen telepítve van az Azure AD jelszavas védelmi proxy szolgáltatás, hálózati hozzáféréssel kell rendelkeznie a következő végpontokhoz:

    |**Végpont**|**Cél**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Hitelesítési kérelmek|
    |`https://enterpriseregistration.windows.net`|Azure AD jelszavas védelem funkció|
 
* Microsoft Azure AD összekapcsolási ügynök frissítési előfeltételei

  Az Microsoft Azure AD összekapcsolási ügynök frissítési szolgáltatását az Azure AD jelszavas védelem proxy szolgáltatásával párhuzamosan telepíti a rendszer. További konfigurálásra van szükség ahhoz, hogy a Microsoft Azure AD összekapcsolási ügynök frissítési szolgáltatása képes legyen működni:

  Ha a környezet http-proxykiszolgálót használ, a [meglévő helyszíni proxykiszolgálók használata](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)című témakörben megadott irányelvek szerint kell megfelelnie.

  A Microsoft Azure AD összekapcsolási ügynök frissítési szolgáltatásához a [TLS-követelményekben](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements)megadott TLS 1,2-lépéseket is meg kell adni.

  A hálózati hozzáférés engedélyezéséhez engedélyezni kell az [alkalmazásproxy-környezet telepítési eljárásaiban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)megadott portok és URL-címek készletét.

  > [!WARNING]
  > Az Azure AD jelszavas védelem proxyja és az alkalmazásproxy a Microsoft Azure AD összekapcsoló ügynök-frissítési szolgáltatás különböző verzióit telepíti, ezért az utasítások az alkalmazásproxy tartalmára vonatkoznak. Ezek a különböző verziók nem kompatibilisek egymás mellett, ezért nem ajánlott az Azure AD jelszavas védelem proxyját és az alkalmazásproxy egymás melletti telepítését ugyanazon a gépen.

* A rendszer a jelszavas védelemre szolgáló proxy szolgáltatást futtató összes gépet úgy kell konfigurálni, hogy a tartományvezérlők számára engedélyezze a proxy szolgáltatásba való bejelentkezés lehetőségét. Ezt a képességet a "számítógép elérése a hálózatról" jogosultság-hozzárendelésen keresztül vezérelheti.
* A rendszer a jelszavas védelemre szolgáló proxy szolgáltatást futtató összes gépet úgy kell konfigurálni, hogy engedélyezze a kimenő TLS 1,2 HTTP-forgalmat.
* Globális rendszergazdai fiók a proxykiszolgáló regisztrálásához a jelszavas védelemhez és az erdőhöz az Azure AD-vel.
* Egy olyan fiók, amely Active Directory tartományi rendszergazdai jogosultságokkal rendelkezik az erdő gyökértartományában, hogy regisztrálja a Windows Server Active Directory erdőt az Azure AD-ben.
* A DC Agent Service szoftvert futtató Active Directory tartományoknak elosztott fájlrendszer replikációt (DFSR) kell használniuk a SYSVOL-replikációhoz.

  Ha a tartomány még nem használja a DFSR-t, akkor az Azure AD jelszavas védelem telepítése előtt át kell telepítenie a DFSR használatára. További információt a következő hivatkozásra kattintva talál:

  [SYSVOL-replikáció áttelepítési útmutatója: FRS – Elosztott fájlrendszer replikációs szolgáltatása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

  > [!WARNING]
  > Az Azure AD jelszavas védelem TARTOMÁNYVEZÉRLŐi ügynöke jelenleg a SYSVOL-replikációhoz használt FRS (a megelőző technológia DFSR) használatával működő tartományvezérlőkön települ, de a szoftver nem fog megfelelően működni ebben a környezetben. További negatív mellékhatások például az egyes fájlok replikálásának meghiúsulása, a SYSVOL visszaállítási eljárásai pedig sikeresek, de az összes fájl replikálásának csendes sikertelensége esetén sikertelenek lesznek. A tartományt a lehető leghamarabb telepítse át a DFSR használatára, mind a DFSR rejlő előnyeit, mind pedig az Azure AD jelszavas védelem üzembe helyezésének feloldását is. A szoftver jövőbeli verziói automatikusan le lesznek tiltva, ha olyan tartományban fut, amely továbbra is FÁJLREPLIKÁCIÓS szolgáltatást használ.

* A Key Distribution szolgáltatást engedélyezni kell a Windows Server 2012 rendszert futtató tartomány összes tartományvezérlőjén. Ez a szolgáltatás alapértelmezés szerint a manuális trigger indításával engedélyezhető.

## <a name="single-forest-deployment"></a>Egyerdős telepítés

Az alábbi ábra bemutatja, hogyan működik együtt az Azure AD jelszavas védelem alapvető összetevői egy helyszíni Active Directory környezetben.

![Hogyan működnek együtt az Azure AD jelszavas védelmi összetevői?](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Érdemes áttekinteni, hogyan működik a szoftver az üzembe helyezése előtt. Lásd: [Az Azure ad jelszavas védelem fogalmi áttekintése](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>A szoftver letöltése

Az Azure AD jelszavas védelméhez két kötelező telepítő szükséges. Ezek a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071)érhetők el.

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>A proxy szolgáltatás telepítése és konfigurálása jelszavas védelemhez

1. Válasszon ki egy vagy több kiszolgálót a proxykiszolgáló futtatásához a jelszavas védelem érdekében.
   * Minden ilyen szolgáltatás csak egyetlen erdőhöz biztosít jelszavas házirendeket. A gazdagépnek az erdőben lévő tartományhoz kell csatlakoznia. A gyökér és a gyermek tartományok egyaránt támogatottak. Az erdő minden tartományában és a jelszavas védelemben használt számítógépen legalább egy TARTOMÁNYVEZÉRLŐnek hálózati kapcsolatra van szüksége.
   * A proxy szolgáltatást a tartományvezérlőn is futtathatja tesztelés céljából. Azonban a tartományvezérlőhöz internetkapcsolat szükséges, ami biztonsági szempontból fontos lehet. Ezt a konfigurációt csak tesztelésre javasoljuk.
   * Legalább két proxykiszolgáló használatát javasoljuk a redundancia érdekében. Tekintse meg a [magas rendelkezésre állást](howto-password-ban-bad-on-premises-deploy.md#high-availability).
   * A proxy szolgáltatás nem támogatott írásvédett tartományvezérlőn való futtatásához.

1. Telepítse az Azure AD Password Protection proxy szolgáltatást a `AzureADPasswordProtectionProxySetup.exe` Software Installer használatával.
   * A szoftver telepítése nem igényel újraindítást. Előfordulhat, hogy a Szoftvertelepítés szabványos MSI-eljárások használatával automatizálható, például:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > A Windows tűzfal szolgáltatásnak futnia kell a AzureADPasswordProtectionProxySetup. msi csomag telepítése előtt, hogy elkerülje a telepítési hibát. Ha a Windows tűzfal úgy van konfigurálva, hogy ne fusson, a megoldás a telepítés során átmenetileg engedélyezi és futtatja a tűzfal szolgáltatást. A proxykiszolgáló a telepítés után nem rendelkezik specifikus függőséggel a Windows tűzfalon. Ha külső gyártótól származó tűzfalat használ, azt továbbra is konfigurálni kell, hogy az megfeleljen a telepítési követelményeknek. Ezek közé tartozik a 135-es port és a proxy RPC-kiszolgáló portjának bejövő hozzáférésének engedélyezése. Lásd: [telepítési követelmények](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Nyisson meg egy PowerShell-ablakot rendszergazdaként.
   * A jelszavas védelem proxy szoftver tartalmaz egy új PowerShell-modult, a *AzureADPasswordProtection*. A következő lépések különféle parancsmagokat futtatnak ebből a PowerShell-modulból. Importálja az új modult a következőképpen:

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * A szolgáltatás futtatásának megadásához használja a következő PowerShell-parancsot:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     Az eredménynek a "Running" **állapotot** kell mutatnia.

1. Regisztrálja a proxyt.
   * A 3. lépés befejezése után a proxy szolgáltatás fut a gépen, de még nem rendelkezik az Azure AD-vel való kommunikációhoz szükséges hitelesítő adatokkal. Az Azure AD-vel való regisztráció szükséges:

     `Register-AzureADPasswordProtectionProxy`

     Ehhez a parancsmaghoz globális rendszergazdai hitelesítő adatok szükségesek az Azure-bérlőhöz. Helyi Active Directory tartományi rendszergazdai jogosultságokkal is rendelkeznie kell az erdő gyökértartományában. Ezt a parancsmagot helyi rendszergazdai jogosultságokkal rendelkező fiókkal is futtatni kell.

     Miután ez a parancs egyszer sikeres volt egy proxy szolgáltatáshoz, a további meghívások sikeresek lesznek, de szükségtelenek.

      A `Register-AzureADPasswordProtectionProxy` parancsmag a következő három hitelesítési módot támogatja. Az első két mód támogatja az Azure Multi-Factor Authentication-t, de a harmadik mód nem. További részletekért tekintse meg az alábbi megjegyzéseket.

     * Interaktív hitelesítési mód:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ez a mód nem működik a Server Core operációs rendszereken. Ehelyett használja a következő hitelesítési módok egyikét. Ez a mód akkor is meghiúsulhat, ha az Internet Explorer fokozott biztonsági beállításai engedélyezve vannak. A megkerülő megoldás a konfiguráció letiltása, a proxy regisztrálása, majd újbóli engedélyezése.

     * Eszköz-kód hitelesítési mód:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Ezután végezze el a hitelesítést egy másik eszközön megjelenő utasítások követésével.

     * Csendes (jelszó-alapú) hitelesítési mód:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ez a mód meghiúsul, ha az Azure Multi-Factor Authentication szükséges a fiókhoz. Ebben az esetben használja az előző két hitelesítési mód egyikét, vagy használjon egy másik fiókot, amely nem igényel MFA-t.
        >
        > Azt is megteheti, hogy az MFA szükséges, ha az Azure-eszköz regisztrációja (amelyet az Azure AD jelszavas védelem keretében használ) úgy van konfigurálva, hogy globálisan megkövetelje az MFA-t. A megkerülő megoldáshoz használhat egy másik fiókot, amely támogatja az MFA-t az előző két hitelesítési mód egyikével, vagy átmenetileg is kihasználhatja az Azure-eszköz regisztrációjának MFA-követelményét. Ehhez nyissa meg az Azure felügyeleti portálját, majd válassza a Azure Active Directory, majd az eszközök, majd az eszközbeállítások lehetőséget, majd a "többtényezős hitelesítés megkövetelése az eszközök csatlakoztatásához" beállítást. Ügyeljen rá, hogy ezt a beállítást állítsa vissza az Igen értékre, ha a regisztráció befejeződött.
        >
        > Javasoljuk, hogy az MFA-követelményeket csak tesztelési célokra lehessen kihagyni.

       Jelenleg nem kell megadnia a *-ForestCredential* paramétert, amely a jövőbeli funkciók számára van fenntartva.

   A proxy szolgáltatás jelszavas védelemre való regisztrálására csak egyszer van szükség a szolgáltatás élettartama során. Ezt követően a proxy szolgáltatás automatikusan végrehajtja a többi szükséges karbantartást.

   > [!TIP]
   > Előfordulhat, hogy egy adott Azure-bérlő esetében a parancsmag első futtatásakor észrevehető késleltetési idő van. Ha nem jelent hibát, ne aggódjon ez a késés.

1. Regisztrálja az erdőt.
   * Az `Register-AzureADPasswordProtectionForest` PowerShell-parancsmag használatával inicializálja a helyszíni Active Directory erdőt az Azure-nal való kommunikációhoz szükséges hitelesítő adatokkal.

      A parancsmaghoz globális rendszergazdai hitelesítő adatok szükségesek az Azure-bérlőhöz.  Ezt a parancsmagot a helyi rendszergazdai jogosultságokkal rendelkező fiókkal is futtatnia kell. Helyszíni Active Directory vállalati rendszergazdai jogosultságokat is igényel. Ez a lépés erdőn keresztül egyszer fut.

      A `Register-AzureADPasswordProtectionForest` parancsmag a következő három hitelesítési módot támogatja. Az első két mód támogatja az Azure Multi-Factor Authentication-t, de a harmadik mód nem. További részletekért tekintse meg az alábbi megjegyzéseket.

     * Interaktív hitelesítési mód:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ez a mód nem működik a Server Core operációs rendszereken. Ehelyett használja a következő két hitelesítési mód egyikét. Ez a mód akkor is meghiúsulhat, ha az Internet Explorer fokozott biztonsági beállításai engedélyezve vannak. A megkerülő megoldással tiltsa le ezt a konfigurációt, regisztrálja az erdőt, majd engedélyezze újra.  

     * Eszköz-kód hitelesítési mód:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Ezután végezze el a hitelesítést egy másik eszközön megjelenő utasítások követésével.

     * Csendes (jelszó-alapú) hitelesítési mód:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ez a mód meghiúsul, ha az Azure Multi-Factor Authentication szükséges a fiókhoz. Ebben az esetben használja az előző két hitelesítési mód egyikét, vagy használjon egy másik fiókot, amely nem igényel MFA-t.
        >
        > Azt is megteheti, hogy az MFA szükséges, ha az Azure-eszköz regisztrációja (amelyet az Azure AD jelszavas védelem keretében használ) úgy van konfigurálva, hogy globálisan megkövetelje az MFA-t. A megkerülő megoldáshoz használhat egy másik fiókot, amely támogatja az MFA-t az előző két hitelesítési mód egyikével, vagy átmenetileg is kihasználhatja az Azure-eszköz regisztrációjának MFA-követelményét. Ehhez nyissa meg az Azure felügyeleti portálját, majd válassza a Azure Active Directory, majd az eszközök, majd az eszközbeállítások lehetőséget, majd a "többtényezős hitelesítés megkövetelése az eszközök csatlakoztatásához" beállítást. Ügyeljen rá, hogy ezt a beállítást állítsa vissza az Igen értékre, ha a regisztráció befejeződött.
        >
        > Javasoljuk, hogy az MFA-követelményeket csak tesztelési célokra lehessen kihagyni.

       Ezek a példák csak akkor sikeresek, ha az aktuálisan bejelentkezett felhasználó Active Directory tartományi rendszergazda is a legfelső szintű tartományhoz. Ha ez nem igaz, alternatív tartományi hitelesítő adatokat is megadhat a *-ForestCredential* paraméter használatával.

   > [!NOTE]
   > Ha több proxykiszolgáló van telepítve a környezetben, akkor nem számít, hogy melyik proxykiszolgálót használja az erdő regisztrálásához.
   >
   > [!TIP]
   > Előfordulhat, hogy egy adott Azure-bérlő esetében a parancsmag első futtatásakor észrevehető késleltetési idő van. Ha nem jelent hibát, ne aggódjon ez a késés.

   A Active Directory erdő regisztrációját csak egyszer kell elvégezni az erdő élettartama során. Ezt követően az erdőben lévő tartományvezérlő-ügynökök automatikusan elvégzik a többi szükséges karbantartást. Az erdő sikeres futtatását `Register-AzureADPasswordProtectionForest` követően a parancsmag további meghívásai sikeresek lesznek, de szükségtelenek.

   Ahhoz, hogy a `Register-AzureADPasswordProtectionForest` sikeres legyen, legalább egy Windows Server 2012 vagy újabb rendszert futtató tartományvezérlőnek elérhetőnek kell lennie a proxykiszolgáló tartományában. Ennek a lépésnek a megkezdése előtt a tartományvezérlői ügynök szoftverét nem kell a tartományvezérlőkre telepíteni.

1. Konfigurálja a proxy szolgáltatást a jelszavas védelemhez a HTTP-proxyn keresztüli kommunikációhoz.

   Ha a környezete egy adott HTTP-proxy használatát igényli az Azure-vel való kommunikációhoz, használja ezt a metódust: hozzon létre egy *AzureADPasswordProtectionProxy. exe. config* fájlt a%ProgramFiles%\Azure ad Password Protection Proxy\Service mappában. A következő tartalom belefoglalása:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Ha a HTTP-proxy hitelesítést igényel, adja hozzá a *useDefaultCredentials* címkét:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Mindkét esetben cserélje le a `http://yourhttpproxy.com:8080`t az adott HTTP-proxykiszolgáló címe és portja helyére.

   Ha a HTTP-proxy engedélyezési házirend használatára van konfigurálva, hozzáférést kell biztosítania annak a számítógépnek a Active Directory számítógépfiókja számára, amely a proxy szolgáltatást a jelszavas védelemhez futtatja.

   Javasoljuk, hogy a *AzureADPasswordProtectionProxy. exe. config* fájl létrehozása vagy frissítése után állítsa le és indítsa újra a proxy szolgáltatást.

   A proxy szolgáltatás nem támogatja a HTTP-proxyhoz való csatlakozáshoz megadott hitelesítő adatok használatát.

1. Nem kötelező: konfigurálja a proxybeállításokat a jelszavas védelemhez egy adott port figyeléséhez.
   * A tartományvezérlők jelszavas védelméhez használt tartományvezérlői ügynök szoftvere a TCP-n keresztüli RPC protokollt használja a proxy szolgáltatással való kommunikációhoz. Alapértelmezés szerint a proxy szolgáltatás minden elérhető dinamikus RPC-végponton figyeli a szolgáltatást. A szolgáltatás beállítható úgy, hogy egy adott TCP-portot figyelje, szükség esetén a hálózat topológiája vagy a környezete által támasztott tűzfalszabályok miatt.
      * <a id="static" /></a>a szolgáltatás statikus porton való futtatásának konfigurálásához használja a `Set-AzureADPasswordProtectionProxyConfiguration` parancsmagot.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > A módosítások érvénybe léptetéséhez le kell állítania, majd újra kell indítania a szolgáltatást.

      * Ha úgy szeretné konfigurálni a szolgáltatást, hogy egy dinamikus porton fusson, ugyanazt az eljárást használja, de állítsa vissza a *StaticPort* nullára:

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > A módosítások érvénybe léptetéséhez le kell állítania, majd újra kell indítania a szolgáltatást.

   > [!NOTE]
   > A jelszavas védelemhez használt proxy szolgáltatás manuális újraindítást igényel a port konfigurációjának módosítása után. A konfiguráció módosítása után azonban nem kell újraindítania a tartományvezérlői ügynök szolgáltatási szoftverét a tartományvezérlőkön.

   * A szolgáltatás jelenlegi konfigurációjának lekérdezéséhez használja a `Get-AzureADPasswordProtectionProxyConfiguration` parancsmagot:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>A DC Agent szolgáltatás telepítése

   Telepítse az DC Agent szolgáltatást a jelszavas védelemhez a `AzureADPasswordProtectionDCAgentSetup.msi` csomag használatával.

   A szoftver telepítése vagy eltávolítása újraindítást igényel. Ennek a követelménynek az az oka, hogy a rendszer csak a jelszó-szűrő DLL-eket tölti be vagy távolítja el újra.

   A DC Agent szolgáltatást olyan gépen is telepítheti, amely még nem tartományvezérlő. Ebben az esetben a szolgáltatás elindul és futni fog, de mindaddig inaktív marad, amíg a gépet tartományvezérlővé nem előléptetik.

   A Szoftvertelepítés szabványos MSI-eljárások használatával automatizálható. Például:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   Ha szeretné, hogy a telepítő automatikusan újraindítsa a gépet, kihagyhatja a `/norestart` jelzőt.

A telepítés akkor fejeződik be, ha a tartományvezérlő ügynök szoftverét tartományvezérlőre telepítették, és a számítógép újraindul. Nincs szükség más konfigurációra.

## <a name="upgrading-the-proxy-agent"></a>A proxy ügynök frissítése

Ha az Azure AD jelszavas védelmi proxy szoftver újabb verziója érhető el, a frissítés a `AzureADPasswordProtectionProxySetup.exe` Software Installer legújabb verziójának futtatásával valósítható meg. A szoftver legújabb verziója a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071)érhető el.

A proxy szoftver aktuális verziójának eltávolítása nem szükséges – a telepítő helyben történő frissítést hajt végre. A proxy szoftver frissítése során nincs szükség újraindításra. Előfordulhat, hogy a szoftverfrissítés szabványos MSI-eljárások használatával automatizálható, például: `AzureADPasswordProtectionProxySetup.exe /quiet`.

A proxy ügynök támogatja az automatikus frissítést. Az automatikus frissítés a Microsoft Azure AD összekapcsolási ügynök frissítési szolgáltatását használja, amelyet a rendszer a proxy szolgáltatással párhuzamosan telepít. Az automatikus frissítés alapértelmezés szerint be van kapcsolva, és a `Set-AzureADPasswordProtectionProxyConfiguration` parancsmag használatával engedélyezhető vagy letiltható. Az aktuális beállítás a `Get-AzureADPasswordProtectionProxyConfiguration` parancsmag használatával kérdezhető le. A Microsoft azt javasolja, hogy az automatikus frissítési beállítás mindig engedélyezve legyen.

Az `Get-AzureADPasswordProtectionProxy` parancsmag használható a jelenleg telepített proxy-ügynökök szoftveres verziójának lekérdezésére egy erdőben.

## <a name="upgrading-the-dc-agent"></a>A DC-ügynök frissítése

Ha az Azure AD jelszavas védelem tartományvezérlő-ügynökének újabb verziója érhető el, a frissítés a `AzureADPasswordProtectionDCAgentSetup.msi` szoftvercsomag legújabb verziójának futtatásával valósítható meg. A szoftver legújabb verziója a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071)érhető el.

Nincs szükség a DC-ügynök szoftverének aktuális verziójának eltávolítására – a telepítő helyben történő frissítést hajt végre. A DC-ügynök szoftverének frissítésekor mindig újraindítás szükséges – ezt a követelményt az alapvető Windows-viselkedés okozza. 

Előfordulhat, hogy a szoftverfrissítés szabványos MSI-eljárások használatával automatizálható, például: `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`.

Ha szeretné, hogy a telepítő automatikusan újraindítsa a gépet, kihagyhatja a `/norestart` jelzőt.

Az `Get-AzureADPasswordProtectionDCAgent` parancsmag használható a jelenleg telepített DC-ügynökök szoftveres verziójának lekérdezésére egy erdőben.

## <a name="multiple-forest-deployments"></a>Több erdőre üzemelő példányok

Az Azure AD jelszavas védelem több erdőben való üzembe helyezéséhez nem szükséges további követelmény. Az egyes erdők egymástól függetlenül konfigurálhatók az "egyerdős telepítés" szakaszban leírtak szerint. Mindegyik jelszavas védelmi proxy csak abban az erdőben tud tartományvezérlőket támogatni, amelyhez csatlakozik. A jelszavas védelem szoftvere bármely erdőben nem ismeri a más erdőkben üzembe helyezett jelszavas védelmi szoftvereket, függetlenül a Active Directory megbízhatósági konfigurációtól.

## <a name="read-only-domain-controllers"></a>Írásvédett tartományvezérlők

A jelszó módosításait/készleteit a rendszer nem dolgozza fel és nem őrzi meg írásvédett tartományvezérlőkön (írásvédett tartományvezérlők). Ezeket az írható tartományvezérlők továbbítják. Ezért nem kell telepítenie a DC-ügynök szoftverét az írásvédett tartományvezérlőn.

A proxy szolgáltatás nem támogatott írásvédett tartományvezérlőn való futtatásához.

## <a name="high-availability"></a>Magas rendelkezésre állás

A jelszavas védelem fő elérhetősége a proxykiszolgálók rendelkezésre állása, amikor egy erdőben lévő tartományvezérlők megpróbálnak letölteni új házirendeket vagy más, az Azure-ból származó adatok letöltését. Minden tartományvezérlő ügynök egy egyszerű, ciklikusan megjelenő algoritmust használ, amikor eldönti, melyik proxykiszolgálót kell meghívni. Az ügynök kihagyja a nem válaszoló proxykiszolgáló-kiszolgálókat. A címtár-és SYSVOL-mappa állapotának kifogástalan replikálásával rendelkező Active Directory központi telepítések esetében két proxykiszolgáló elegendő a rendelkezésre állás biztosításához. Ez időben letölti az új szabályzatokat és egyéb adatmennyiségeket. Azonban további proxykiszolgálók is üzembe helyezhetők.

A DC-ügynök szoftverének kialakítása csökkenti a magas rendelkezésre álláshoz kapcsolódó szokásos problémákat. A tartományvezérlő ügynöke a legutóbb letöltött jelszóházirend helyi gyorsítótárát tárolja. Még ha az összes regisztrált proxykiszolgáló elérhetetlenné válik, a tartományvezérlő-ügynökök továbbra is kikényszerítik a gyorsítótárazott jelszavas szabályzatot. A nagyméretű üzemelő példányok jelszavas házirendjének ésszerű frissítési gyakorisága általában nap, nem óra vagy kevesebb. Így a proxykiszolgálók rövid kimaradása nem befolyásolja jelentősen az Azure AD jelszavas védelmét.

## <a name="next-steps"></a>Következő lépések

Most, hogy telepítette az Azure AD jelszavas védelméhez szükséges szolgáltatásokat a helyszíni kiszolgálókon, [végezze el a telepítés utáni konfigurálást, és gyűjtsön jelentéskészítési adatokat](howto-password-ban-bad-on-premises-operations.md) a telepítés befejezéséhez.

[Az Azure AD jelszavas védelem fogalmi áttekintése](concept-password-ban-bad-on-premises.md)
