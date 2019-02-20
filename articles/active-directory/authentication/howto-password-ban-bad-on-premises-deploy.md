---
title: Üzembe helyezése az Azure AD-jelszó Protection előzetes verziója
description: Az Azure AD jelszóvédelem előzetes hozzá tartozó rossz jelszavak helyszíni üzembe helyezése
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8d39b614c373c63cf1405c5db0f64581c481d1f
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417202"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Előzetes verzió: Üzembe helyezése az Azure AD jelszóvédelem

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Most, hogy egy-egy [az Azure AD jelszó védelme a Windows Server Active Directory a kényszerítés módja](concept-password-ban-bad-on-premises.md), és hajtsa végre a központi telepítés a következő lépéssel.

## <a name="deployment-strategy"></a>Üzembe helyezési stratégiához

A Microsoft azt javasolja, hogy minden telepítéshez rendszervizsgálati módban indítsa el. Vizsgálati üzemmód az alapértelmezett kezdeti beállítása ahol folytathatja a jelszavak állítható be, és az eseménynapló-bejegyzéseket létrehozni minden le lesz tiltva. Proxy-kiszolgáló (ko) és a tartományvezérlő ügynökök teljes mértékben telepített rendszervizsgálati módban, rendszeres figyelést kell elvégezni ahhoz, hogy milyen hatást jelszóházirend meghatározása kényszerítési lenne a felhasználók és a környezet, ha a házirend kényszerítve lenne.

Az ellenőrzési szakaszban található számos szervezet:

* A meglévő operatív folyamatok használata biztonságosabb, jelszavak javítása van szükségük.
* Felhasználók vannak bemutatásával nem biztonságos jelszavak rendszeresen kiválasztása
* Tájékoztassa a felhasználóknak a közelgő változást végpontbiztonság kényszerítése, az hatással lehet rájuk, és segít jobban megérteni, hogyan kiválaszthatnak biztonságosabb, jelszavak van szükségük.

A funkció elfogadható időn rendszervizsgálati módban futott, miután a kényszerítési konfigurációját a lehet tükrözni **naplózási** való **érvényesítése** ezáltal igénylő biztonságosabb, jelszavak. Ez idő alatt célirányos figyelése, célszerű.

## <a name="deployment-requirements"></a>Központi telepítésére vonatkozó követelmények

* Minden tartományvezérlő, amelyen telepíteni szeretné az Azure AD jelszó védelmi DC ügynök szolgáltatás futnia kell a Windows Server 2012 vagy újabb.
* Minden olyan gép, amelyen telepíteni szeretné az Azure AD-jelszó védelme Proxy szolgáltatás futnia kell a Windows Server 2012 R2 vagy újabb.
* Minden olyan gép, amelyen telepítve vannak az Azure AD jelszóvédelem összetevők többek között a tartományvezérlők a Universal C futásidejű telepítve kell rendelkeznie.
Ez lehetőleg azáltal, hogy teljes mértékben kijavítja a gép Windows Update-en keresztül történik. Ellenkező esetben lehet, egy megfelelő operációsrendszer-specifikus frissítési csomag telepítve – lásd: [Universal C-modul a Windows Update](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)
* Minden olyan tartományban legalább egy tartományvezérlőre és legalább egy, az Azure AD-jelszó védelme Proxy szolgáltatást futtató kiszolgáló között léteznie kell a hálózati kapcsolatot. A kapcsolat lehetővé teszi a tartományvezérlő hozzáférés RPC-végpont eseményleképező portja (135-ös) és a proxy szolgáltatást az RPC-kiszolgáló portja. Az RPC-kiszolgáló portja alapértelmezés szerint dinamikus RPC-portot, de konfigurálható (lásd alább) a statikus port használatára.
* Minden gép üzemeltetése az Azure AD-jelszó védelme Proxy szolgáltatás az alábbi végpontok hálózati hozzáféréssel kell rendelkeznie:

    |Végpont |Cél|
    | --- | --- |
    |`https://login.microsoftonline.com`|Hitelesítési kérelmek|
    |`https://enterpriseregistration.windows.net`|Az Azure AD jelszó-védelmi funkciók|

* Az Azure AD-jelszó védelme Proxy szolgáltatást futtató összes gép kimenő TLS 1.2-es HTTP-forgalom engedélyezésére kell konfigurálni.
* Egy globális rendszergazdai fiókkal az Azure AD-jelszó védelme Proxy szolgáltatás és az erdő regisztrálhat az Azure ad-ben.
* Az Active Directory tartományi rendszergazdai jogosultságokkal az erdő gyökértartományában, a Windows Server Active Directory-erdő regisztrálni az Azure AD-fiók.
* Minden olyan Active Directory-tartományban, a Tartományvezérlőn futó ügynök szolgáltatás szoftvert kell használnia elosztott fájlrendszer replikációs szolgáltatása a sysvol replikáció.

## <a name="single-forest-deployment"></a>Egyetlen erdő telepítés

Az alábbi ábrán látható, hogyan az alapvető összetevők az Azure AD jelszóvédelem működik együtt a helyszíni Active Directory-környezetben.

![Hogyan működik az Azure AD jelszóvédelem összetevők együtt](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Üzembe helyezés előtt, egy célszerű áttekinteni, hogyan működik a szoftvert; Lásd: [fogalmi áttekintése az Azure AD jelszóvédelem](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>A szoftver letöltése

Nincsenek Azure AD jelszóvédelem, amely letölthető a két szükséges telepítők a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Az Azure AD jelszóvédelem proxy szolgáltatás telepítése és konfigurálása

1. Válasszon egy vagy több kiszolgálót az Azure AD-jelszó védelme Proxy szolgáltatás üzemeltetéséhez.
   * Minden ilyen szolgáltatás lehet csak jelszóházirendet egyetlen erdő, és a gazdagépen kell lennie egy tartományhoz tartományhoz (gyermek és a legfelső szintű tartományok támogatottak) az adott erdőben. Ahhoz, hogy az Azure AD-jelszó védelme Proxy szolgáltatás feladatának teljesítéséhez, a hálózati kapcsolat, az erdő minden tartományban legalább egy tartományvezérlő és az Azure AD-jelszó védelme Proxy gép között kell lenniük.
   * Támogatott telepítése és futtatása az Azure AD-jelszó védelme Proxy szolgáltatás egy tartományvezérlőn tesztelési célokra; a hátránya, hogy a tartományvezérlő, akkor ez egyik biztonsági szempont lehet internetkapcsolatra van szükség. A Microsoft azt javasolja, hogy ez a konfiguráció csak használható tesztelési célokra.
   * Ajánlott legalább két proxykiszolgáló redundancia céljából. [Tekintse meg a magas rendelkezésre állás](howto-password-ban-bad-on-premises-deploy.md#high-availability)

2. Telepítse az Azure AD-jelszó védelme Proxy szolgáltatást az AzureADPasswordProtectionProxySetup.msi MSI-csomag használatával.
   * A Szoftvertelepítés nem kell újraindítani. A Szoftvertelepítés előfordulhat, hogy automatizálható a szokásos eljárásokkal MSI, például: `msiexec.exe /i AzureADPasswordProtectionProxySetup.msi /quiet /qn`

      > [!NOTE]
      > A Windows tűzfal szolgáltatásnak futnia kell az AzureADPasswordProtectionProxySetup.msi MSI-csomag telepítése előtt, különben telepítési hiba történik. Futtassa a Windows tűzfal van konfigurálva, a megoldás-e ideiglenes engedélyezéséhez, és indítsa el a Windows tűzfal szolgáltatást a telepítési folyamat során. A Proxy szoftvert a Windows tűzfal szoftver telepítése után nincs konkrét függőségre rendelkezik. A külső tűzfalat használ, ha továbbra is kell konfigurálni annak a központi telepítésére vonatkozó követelmények kielégítéséhez (engedélyezi a 135-ös portot a bejövő hozzáférést és az RPC-proxy kiszolgáló portja e dinamikus vagy statikus). [Üzembe helyezési követelményeket lásd:](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)

3. Nyisson meg egy PowerShell-ablakot rendszergazdaként.
   * Az Azure AD-jelszó védelme Proxy AzureADPasswordProtection nevű új PowerShell-modul közé tartozik. A következő lépések alapján különböző parancsmagok futtatja a PowerShell-modult, és feltételezik, hogy megnyitották egy új PowerShell-ablakot, és a következőképpen importálta-e az új modul:

      ```PowerShell
      Import-Module AzureADPasswordProtection
      ```

   * Ellenőrizze, hogy a szolgáltatás fut, a következő PowerShell-paranccsal: `Get-Service AzureADPasswordProtectionProxy | fl`.
     Az eredmény eredményt kell előállítania a **állapot** "Fut" eredményt visszaadó.

4. A proxy regisztrálása.
   * 3. lépés befejezése után az Azure AD-jelszó védelme Proxy szolgáltatás fut a gépen, de még nem rendelkezik a szükséges hitelesítő adatokat az Azure AD-kommunikációhoz. Az Azure AD-regisztráció engedélyezéséhez, hogy lehetővé teszi az szükséges a `Register-AzureADPasswordProtectionProxy` PowerShell-parancsmagot. A parancsmag megköveteli a globális rendszergazdai hitelesítő adatait az Azure-bérlőhöz, valamint a helyszíni Active Directory tartományi rendszergazdai jogosultságokkal az erdő gyökértartományában. Amint azt a megadott proxy Service, a további indítások sikeres `Register-AzureADPasswordProtectionProxy` továbbra is sikeres legyen, de nem szükségesek.

      A Register-AzureADPasswordProtectionProxy parancsmag a következő három különböző hitelesítési módot támogatja.

      * Interaktív hitelesítési mód:

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > Ebben a módban nem működnek Server Core operációs rendszereken. Ehelyett használja az alternatív hitelesítési módok egyikét az alábbi módon.

         > [!NOTE]
         > Ebben a módban meghiúsulhatnak, ha az Internet Explorer fokozott biztonsági beállításai engedélyezve van. A megoldás, hogy tiltsa le a IESC, a proxy, majd engedélyezze újra IESC regisztrálni.

      * Eszköz-kód hitelesítési mód:

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         Majd egy másik eszközön megjelenő utasításokat követve fejezze be a hitelesítést.

      * Beavatkozás nélküli (jelszóalapú) hitelesítési mód:

         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > Ebben a módban sikertelen lesz, ha a hitelesítés valamilyen okból többtényezős Hitelesítést igényel. Ha ez a helyzet, használja az előző két mód közül elvégezni az MFA-alapú hitelesítéshez.

      Jelenleg nem szükséges, adja meg a - ForestCredential paramétert, amely a jövőbeni funkció számára van fenntartva.

   > [!NOTE]
   > Az Azure AD jelszóvédelem proxy szolgáltatás regisztrációja várhatóan egy egyszeri lépés a szolgáltatás élettartama során. A proxy szolgáltatás automatikusan végez bármely más szükséges karbantartási ettől kezdve. Miután azt sikerült a megadott proxy, további indítások "Register-AzureADPasswordProtectionProxy", továbbra is sikeres, de nem szükségesek.

   > [!TIP]
   > Előfordulhatnak olyan jelentős késleltetés (sok másodperc) egy adott Azure-bérlő esetében futtassa ezt a parancsmagot, a parancsmag végrehajtásának befejeződése előtt először. Csak akkor jelent hibát, ezt a késést nem tekinthető ijesztő.

5. Regisztrálja az erdőben.
   * A helyszíni Active Directory-erdő inicializálni kell a szükséges hitelesítő adatokkal való kommunikációhoz, az Azure-ban a `Register-AzureADPasswordProtectionForest` PowerShell-parancsmagot. A parancsmag megköveteli a globális rendszergazdai hitelesítő adatait az Azure-bérlőhöz, valamint a helyszíni Active Directory tartományi rendszergazdai jogosultságokkal az erdő gyökértartományában. Ez a lépés minden erdőre egyszer fut.

      A Register-AzureADPasswordProtectionForest parancsmag a következő három különböző hitelesítési módot támogatja.

      * Interaktív hitelesítési mód:

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > Ebben a módban nem működnek Server Core operációs rendszereken. Ehelyett használja az alternatív hitelesítési módok egyikét az alábbi módon.

         > [!NOTE]
         > Ebben a módban meghiúsulhatnak, ha az Internet Explorer fokozott biztonsági beállításai engedélyezve van. A megoldás, hogy tiltsa le a IESC, a proxy, majd engedélyezze újra IESC regisztrálni.  

      * Eszköz-kód hitelesítési mód:

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         Majd egy másik eszközön megjelenő utasításokat követve fejezze be a hitelesítést.

      * Beavatkozás nélküli (jelszóalapú) hitelesítési mód:
         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > Ebben a módban sikertelen lesz, ha a hitelesítése többtényezős Hitelesítést követel meg. Ha ez a helyzet, használja az előző két mód közül elvégezni az MFA-alapú hitelesítéshez.

      A fenti példákban csak akkor lesz sikeres, ha az aktuálisan bejelentkezett felhasználó egyben a legfelső szintű tartomány Active Directory tartományi rendszergazda. Ha nem ez a helyzet, előfordulhat, hogy adja meg a - ForestCredential paraméter alternatív tartományi hitelesítő adatokat.

   > [!NOTE]
   > Ha a proxykiszolgálók több vannak telepítve a környezetben, nem számít, mely proxykiszolgáló az erdő regisztrálásához használatos.

   > [!TIP]
   > Előfordulhatnak olyan jelentős késleltetés (sok másodperc) egy adott Azure-bérlő esetében futtassa ezt a parancsmagot, a parancsmag végrehajtásának befejeződése előtt először. Csak akkor jelent hibát, ezt a késést nem tekinthető ijesztő.

   > [!NOTE]
   > Az Active Directory-erdő regisztrációs várhatóan egy egyszeri lépés az erdő élettartama során. A tartományt vezérlő ügynökök erdőben futtató automatikusan végre fogja hajtani a bármely más szükséges maintainenance ettől kezdve. Miután egy adott erdő esetén, további indítások sikeres `Register-AzureADPasswordProtectionForest` továbbra is sikeres legyen, de nem szükségesek.

   > [!NOTE]
   > Ahhoz, hogy `Register-AzureADPasswordProtectionForest` legalább egy Windows Server 2012 vagy újabb tartomány sikeres vezérlő elérhetőnek kell lennie a proxykiszolgálót a tartományban. Azonban esetében nem követelmény, hogy a tartományvezérlő ügynök szoftvert telepítenie minden olyan tartományvezérlőn, ez a lépés előtt.

6. Az Azure AD-jelszó védelmi Proxy szolgáltatás HTTP-proxyn keresztüli kommunikáció konfigurálása

   Ha környezete megköveteli egy adott HTTP-proxy kommunikálni az Azure használatát, ez is elvégezhető a következő.

   Hozzon létre egy fájlt `proxyservice.exe.config` fájlt a `%ProgramFiles%\Azure AD Password Protection Proxy\Service` mappában a következő tartalommal:

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

   Ha a HTTP-proxy hitelesítést igényel, adja hozzá a következő useDefaultCredentials címke:

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

   Mindkét esetben cserélje `http://yourhttpproxy.com:8080` -címmel és az adott HTTP-proxykiszolgáló portját.

   Ha a HTTP-proxyt az engedélyezési házirend van beállítva, hozzáférést kell az Azure AD-jelszó védelme Proxy szolgáltatást futtató gép Active Directory számítógépfiókjához.

   Akkor állítsa le és indítsa újra az Azure AD-jelszó védelme Proxy szolgáltatás létrehozása vagy frissítése után a `proxyservice.exe.config` fájlt.

   Az Azure AD-jelszó védelme Proxy szolgáltatás nem támogatja a megadott hitelesítő adatok használatát egy HTTP-proxy való kapcsolódáshoz.

7. Nem kötelező: Konfigurálja az Azure AD-jelszó védelme Proxy szolgáltatás egy adott portot figyeljen.
   * RPC TCP-n keresztül az Azure AD-jelszó DC védelmi ügynök szoftver a tartományvezérlőkön használják az Azure AD-jelszó védelme Proxy szolgáltatással való kommunikációra. Alapértelmezés szerint az Azure AD-jelszó védelme Proxy szolgáltatás figyeli az összes rendelkezésre álló dinamikus RPC-végpont. Hálózati topológia vagy tűzfalra vonatkozó követelmények miatt szükséges, ha a szolgáltatás ehelyett beállítható egy adott TCP-porton figyeljen.
      * A szolgáltatás futtatásához egy statikus port megadásához használja a `Set-AzureADPasswordProtectionProxyConfiguration` parancsmagot.
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Állítsa le kell, és indítsa újra a szolgáltatást, a módosítások érvénybe léptetéséhez.

      * A szolgáltatás futtatásához egy dinamikus port megadásához, használja ugyanazt az eljárást azonban StaticPort vissza 0 értékre, például így:
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Állítsa le kell, és indítsa újra a szolgáltatást, a módosítások érvénybe léptetéséhez.

   > [!NOTE]
   > Az Azure AD-jelszó védelme Proxy szolgáltatás manuális újraindítását igényli, a portkonfigurációjának módosítását követően. Nem kell újraindítani az ilyen jellegű konfigurációs módosítások elvégzése után a tartományvezérlőn futó tartományvezérlő ügynök szoftver.

   * Az aktuális konfigurációját a szolgáltatás használatával lehet lekérdezni a `Get-AzureADPasswordProtectionProxyConfiguration` parancsmag az alábbi példában látható módon:

      ```PowerShell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Az Azure AD jelszóvédelem DC ügynökszolgáltatásának telepítése

   Az Azure AD-jelszó Protection tartományvezérlő telepítési ügynök szolgáltatás szoftver használatával a `AzureADPasswordProtectionDCAgent.msi` MSI-csomag

   A Szoftvertelepítés kell indítani a telepítés, és távolítsa el, mert az operációsrendszer-követelményt, hogy jelszót szűrő DLL-ek csak betöltése vagy a számítógép újraindítását követően a memóriából.

   Olyan számítógépen, amelyen még nem egy tartományvezérlőt a tartományvezérlő ügynökszolgáltatásának telepítése támogatott. Ebben az esetben a szolgáltatás elindul, és futtassa, de más módon lesz inaktívnak lennie amíg után a gép egy tartományvezérlő hitelesítenie kelljen magát.

   A Szoftvertelepítés előfordulhat, hogy automatizálható a szokásos eljárásokkal MSI, például:

   `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > A fenti példa msiexec parancsot egy azonnali újraindítás; fog eredményezni. Ez elkerülhető az megadásával a `/norestart` jelzőt.

Miután egy tartományvezérlőre van telepítve, és újraindul, az Azure AD-jelszó DC védelmi ügynök telepítését akkor fejeződött be. További konfigurálást nem kötelező vagy nem lehetséges.

## <a name="multiple-forest-deployments"></a>Több erdő üzembe helyezés

Nem vonatkoznak további követelmények üzembe helyezéséhez az Azure AD jelszóvédelem több-erdőkön keresztüli. Minden olyan erdőben, egymástól függetlenül van konfigurálva, az egyetlen erdővel telepítési szakaszban leírtak szerint. Minden egyes Azure AD-jelszó védelme Proxy csak támogatja az erdő van csatlakoztatva, a tartományvezérlőket. Az Azure AD-jelszó szoftvert egy adott erdőben nincs tudomása a másik erdő az Active Directory bizalmi kapcsolati konfigurációkat függetlenül üzembe helyezett Azure AD jelszóvédelem szoftver.

## <a name="read-only-domain-controllers"></a>Írásvédett tartományvezérlők

Jelszó changes\sets soha nem feldolgozása, és megőrzi a írásvédett tartományvezérlők (RODC-k); Ehelyett ezeket a rendszer továbbítja az írható tartományvezérlő. Ezért van, a tartományvezérlő ügynök szoftver telepítéséhez az RODC-k nem szükséges.

## <a name="high-availability"></a>Magas rendelkezésre állás

Magas rendelkezésre állás az Azure AD jelszóvédelem biztosítása a fő aggodalma a proxy kiszolgálók rendelkezésre állásának esetén egy erdő tartományvezérlői próbál töltse le az új szabályzatok vagy más adatokat az Azure-ból. Minden tartományvezérlő ügynök egyszerű Ciklikus időszeleteléses stílus algoritmust használ, mely proxykiszolgáló hívást, és kihagyja keresztül, amelyek nem válaszolnak proxykiszolgálók meghatározásakor. A legtöbb teljes csatlakoztatott Active Directory-környezetekben a kifogástalan állapotú replikáció (-directory és a sysvol-állapot) két (2) proxy kiszolgálók rendelkezésre állási, valamint az új szabályzatokat és az egyéb adatok így időben letöltések elegendőnek kell lennie. A kívánt további proxy kiszolgálók, is üzembe helyezhetők.

A magas rendelkezésre állású vonatkozó szokásos problémákat vannak hivatottak DC ügynökszoftver kialakítását. A tartományvezérlő-ügynök a legutóbb letöltött jelszóházirend tartalmazó helyi gyorsítótár tárolja. Akkor is, ha a proxykiszolgálók valamilyen okból elérhetetlenné regisztrált, a tartományvezérlő-ügynökök továbbra is a gyorsítótárazott jelszóházirend kényszerítése. Egy ésszerű frissítési gyakoriság a jelszóházirendek nagyméretű környezetben általában a ahhoz a nap, óra nem vagy kisebb. Ezért a proxykiszolgálók rövid valamilyen okból kimaradás lép, nem váltják ki jelentős hatással az Azure AD-jelszó protection szolgáltatást vagy a biztonsági előnyöket működését.

## <a name="next-steps"></a>További lépések

Most, hogy telepítette a szolgáltatások a helyi kiszolgálóit az Azure AD jelszó-védelemhez szükséges végezze el a [utáni telepítse a konfigurációs és jelentéskészítési információkat összegyűjtési](howto-password-ban-bad-on-premises-operations.md) a telepítés befejezéséhez.

[Az Azure AD jelszóvédelem fogalmi áttekintése](concept-password-ban-bad-on-premises.md)
