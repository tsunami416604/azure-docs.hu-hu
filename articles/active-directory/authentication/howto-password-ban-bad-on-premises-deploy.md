---
title: Helyszíni Azure AD-jelszóvédelem telepítése
description: Megtudhatja, hogy miként tervezheti meg és helyezheti üzembe az Azure AD jelszavas védelmet egy helyszíni Active Directory tartományi szolgáltatások környezetben
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ac9b76dd8d3c950b14f6d7b331f15647427ac89
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652739"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Helyszíni Azure Active Directory jelszavas védelem tervezése és üzembe helyezése

A felhasználók gyakran hoznak létre olyan jelszavakat, amelyek gyakori helyi szavakat használnak, például iskolát, sportcsapatot vagy híres személyt. Ezek a jelszavak könnyen kitalálható, és gyenge a szótáralapú támadások ellen. Erős jelszavak kényszerítése a szervezetben, az Azure Active Directory (Azure AD) jelszavas védelem globális és egyéni tiltott jelszó listát biztosít. A jelszómódosítási kérelem sikertelen, ha a tiltott jelszavak listájában egyezés van.

A helyszíni Active Directory tartományi szolgáltatások (AD DS) környezet védelme érdekében telepítheti és konfigurálhatja az Azure AD password protection-t a helyszíni tartományvezérlővel való együttműködésre. Ez a cikk bemutatja, hogyan telepítheti és regisztrálhatja az Azure AD Password Protection proxy szolgáltatás és az Azure AD Password Protection DC-ügynök a helyszíni környezetben.

Az Azure AD Password Protection helyszíni környezetben való működéséről a [Windows Server Active Directory azure AD password protection kényszerítése](concept-password-ban-bad-on-premises.md)című témakörben talál további információt.

## <a name="deployment-strategy"></a>Telepítési stratégia

Az alábbi ábra bemutatja, hogyan működnek együtt az Azure AD password protection alapvető összetevői egy helyszíni Active Directory-környezetben:

![Az Azure AD password protection összetevőinek együttműködése](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

A telepítés előtt érdemes áttekinteni a szoftver működését. További információ: [Az Azure AD password protection fogalmi áttekintése.](concept-password-ban-bad-on-premises.md)

Azt javasoljuk, hogy indítsa el a központi telepítéseket *vizsgálati* módban. A naplózási mód az alapértelmezett kezdeti beállítás, ahol a jelszavak továbbra is beállíthatók. A letiltott jelszavakat a rendszer rögzíti az eseménynaplóban. Miután a proxykiszolgálókat és a tartományvezérlő-ügynököket vizsgálati módban telepítette, figyelje, hogy a jelszóházirend milyen hatással lesz a felhasználókra a házirend kényszerítésekén.

Az ellenőrzési szakaszban számos szervezet úgy találja, hogy a következő helyzetek érvényesek:

* Javítaniuk kell a meglévő működési folyamatokat a biztonságosabb jelszavak használatához.
* A felhasználók gyakran használnak nem biztonságos jelszavakat.
* Tájékoztatniuk kell a felhasználókat a biztonsági kényszerítés közelgő változásáról, a rájuk gyakorolt lehetséges hatásról és a biztonságosabb jelszavak kiválasztásáról.

Az is lehetséges, hogy az erősebb jelszó-ellenőrzés hatással legyen a meglévő Active Directory tartományvezérlő központi telepítésének automatizálására. Azt javasoljuk, hogy legalább egy tartományvezérlő-előléptetés és egy tartományvezérlő lefokozás történjen az ellenőrzési időszak értékelése során, hogy segítsen feltárni az ilyen problémákat. További információkért tekintse át a következő cikkeket:

* [Az Ntdsutil.exe nem tudja beállítani a címtárszolgáltatások javítási módjának gyenge jelszavát](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [A tartományvezérlő replikaelőléptetése a címtárszolgáltatások javítási módjának gyenge beállítása miatt sikertelen](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [A tartományvezérlő lefokozása gyenge helyi rendszergazdai jelszó miatt sikertelen](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Miután a szolgáltatás ésszerű ideig futó rendszervizsgálati módban, átválthat a konfiguráció *naplózásról* *kényszerítésre,* hogy biztonságosabb jelszavakat igényeljen. Ez idő alatt további figyelés idáig jó ötlet.

### <a name="multiple-forest-considerations"></a>Több erdővel kapcsolatos szempont

Nincsenek további követelmények az Azure AD password protection több erdőben történő üzembe helyezéséhez.

Minden erdő egymástól függetlenül van konfigurálva, a következő szakaszban leírtak szerint [az Azure AD password protection telepítéséhez.](#download-required-software) Minden Azure AD password protection proxy csak az erdőből származó tartományvezérlőket támogathatja, amelyhez csatlakozott.

Az Azure AD password protection szoftver bármely erdőben nincs tisztában a jelszóvédelmi szoftver, amely más erdőkben telepített, függetlenül az Active Directory megbízhatósági konfigurációk.

### <a name="read-only-domain-controller-considerations"></a>Írásvédett tartományvezérlővel kapcsolatos szempontok

A jelszómódosítás vagy a beállított események nem lesznek feldolgozva, és nem maradnak meg az írásvédett tartományvezérlőkön. Ehelyett a rendszer írható tartományvezérlőkre továbbítja őket. Nem kell telepítenie az Azure AD password protection tartományvezérlő-ügynök szoftver tic-k.

Továbbá nem támogatott az Azure AD Password Protection proxyszolgáltatás futtatása egy írásvédett tartományvezérlőn.

### <a name="high-availability-considerations"></a>Magas rendelkezésre állási szempontok

A jelszavas védelem fő szempontja az Azure AD Password Protection proxykiszolgálók elérhetősége, amikor az erdő tartományvezérlői új szabályzatokat vagy más adatokat próbálnak letölteni az Azure-ból. Minden Azure AD Password Protection DC-ügynök egy egyszerű ciklikus multiplexelés-stílusú algoritmust használ, amikor eldönti, hogy melyik proxykiszolgálót hívja meg. Az ügynök kihagyja a nem válaszoló proxykiszolgálókat.

A legtöbb teljes mértékben csatlakoztatott Active Directory-központi telepítések, amelyek a könyvtár és a sysvol mappa állapotának kifogástalan replikálása esetén két Azure AD Password Protection proxykiszolgáló elegendő a rendelkezésre állás biztosításához. Ez a konfiguráció az új házirendek és egyéb adatok időben letöltését eredményezi. Szükség esetén további Azure AD password protection proxykiszolgálókat is üzembe helyezhet.

Az Azure AD Password Protection DC-ügynök szoftver kialakítása csökkenti a magas rendelkezésre állással kapcsolatos szokásos problémákat. Az Azure AD Password Protection DC-ügynök a legutóbb letöltött jelszóházirend helyi gyorsítótárát tartja fenn. Még akkor is, ha az összes regisztrált proxykiszolgáló elérhetetlenné válik, az Azure AD Password Protection DC-ügynökök továbbra is kényszerítik a gyorsítótárazott jelszóházirendet.

A jelszóházirendek ésszerű frissítési gyakorisága egy nagy központi telepítésben általában napok, nem órák vagy kevesebb. Így a proxykiszolgálók rövid kimaradásai nem befolyásolják jelentősen az Azure AD password protection.So, rövid kimaradások a proxy kiszolgálók nem befolyásolja jelentősen az Azure AD password protection.

## <a name="deployment-requirements"></a>Üzembe helyezésre vonatkozó követelmények

A licenceléssel kapcsolatos információkért tekintse meg az [Azure AD password protection licencelési követelményeit.](concept-password-ban-bad.md#license-requirements)

A következő alapvető követelmények érvényesek:

* Minden olyan gépnek, beleértve a tartományvezérlőket is, amelyeken telepítve van az Azure AD Password Protection összetevő, telepítve kell lennie az Univerzális C-futásidőnek.
    * A futásidőt úgy szerezheti be, hogy meggyőződik arról, hogy rendelkezik-e a Windows Update összes frissítésével. Vagy beszerezheti egy operációs rendszer-specifikus frissítési csomagban. További információt a [Windows univerzális C-futásidejű frissítése című témakörben talál.](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)
* Olyan fiókra van szüksége, amely rendelkezik az erdő gyökértartományában az Active Directory tartományi jogosultságokkal rendelkező fiókkal a Windows Server Active Directory erdő regisztrálásához az Azure AD-vel.
* A kulcsterjesztési szolgáltatást a Windows Server 2012 rendszert futtató tartomány összes tartományvezérlőjén engedélyezni kell. Alapértelmezés szerint ez a szolgáltatás manuális eseményindító indítással érhető el.
* A hálózati kapcsolatnak minden tartománylegalább egy tartományvezérlője és az Azure AD password protection proxyszolgáltatást tartalmazó kiszolgáló között kell lennie. Ennek a kapcsolatnak lehetővé kell tennie, hogy a tartományvezérlő hozzáférjen a 135-ös RPC-végpontleképező porthoz és a proxyszolgáltatás RPC-kiszolgálóportjához.
    * Alapértelmezés szerint az RPC-kiszolgálóport dinamikus RPC-port, de statikus [port használatára](#static)konfigurálható.
* Minden olyan gépnek, amelyen az Azure AD jelszavas védelem proxyszolgáltatás telepítve lesz, hálózati hozzáféréssel kell rendelkeznie a következő végpontokhoz:

    |**Végpont**|**Cél**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Hitelesítési kérelmek|
    |`https://enterpriseregistration.windows.net`|Az Azure AD password protection funkciója|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD jelszóvédelem tartományvezérlő-ügynöke

Az azure AD password protection dc-ügynökre a következő követelmények vonatkoznak:

* Minden olyan gépnek, amelyen az Azure AD Password Protection DC ügynök szoftver telepítve lesz, windows Server 2012-es vagy újabb rendszert kell futtatnia.
    * Az Active Directory tartománynak vagy erdőnek nem kell a Windows Server 2012 tartomány működési vagy erdőműködési szintjén (FFL) lennie. Amint azt a [tervezési elvek,](concept-password-ban-bad-on-premises.md#design-principles)nincs minimális DFL vagy FFL szükséges sem a DC ügynök vagy proxy szoftver futtatásához.
* Az Azure AD password protection dc-ügynököt futtató összes gépen telepítve kell lennie .NET 4.5-ös rendszerrel.
* Az Azure AD Password Protection DC-ügynökszolgáltatást futtató Active Directory-tartományoknak elosztott fájlrendszer-replikációt (DFSR) kell használniuk a sysvol replikációhoz.
   * Ha a tartomány már nem használja a DFSR-t, az Azure AD password protection telepítése előtt át kell telepítenie. További információ: [SYSVOL replikációáttelepítési útmutató: FRS az elosztott fájlrendszer replikációs szolgáltatásához](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Az Azure AD Password Protection DC ügynök szoftver jelenleg telepíti a tartományvezérlők a tartományok, amelyek még mindig frs (a dfsr elődtechnológiája) a sysvol replikáció, de a szoftver nem fog megfelelően működni ebben a környezetben.
    >
    > További negatív mellékhatások közé tartozik az egyes fájlok replikálása, és sysvol visszaállítási eljárások úgy tűnik, hogy sikerül, de csendben nem replikálja az összes fájlt.
    >
    > Telepítse át a tartományt, hogy a DFSR a lehető leghamarabb, mind a DFSR rejlő előnyöket, és feloldja az Azure AD password protection telepítésének feloldásához. A szoftver jövőbeli verziói automatikusan lelesznek tiltva, ha olyan tartományban fut, amely még mindig FRS-t használ.

### <a name="azure-ad-password-protection-proxy-service"></a>Az Azure AD password protection proxyszolgáltatás

Az azure AD password protection proxyszolgáltatásra a következő követelmények vonatkoznak:

* Minden olyan gépnek, amelyen az Azure AD Password Protection proxyszolgáltatás telepítve lesz, windows Server 2012 R2 vagy újabb rendszert kell futtatnia.

    > [!NOTE]
    > Az Azure AD Password Protection proxy szolgáltatás központi telepítése kötelező követelmény az Azure AD password protection üzembe helyezéséhez annak ellenére, hogy a tartományvezérlő kimenő közvetlen internetkapcsolattal rendelkezhet.

* Minden olyan gépnek, ahol az Azure AD Password Protection proxyszolgáltatás telepítve lesz, telepíteni kell a .NET 4.7-es vel.
    * A .NET 4.7-et már telepíteni kell egy teljesen frissített Windows Server rendszerre. Ha szükséges, töltse le és futtassa a [.NET Framework 4.7 offline telepítője windowsos rendszerén található telepítőt.](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)
* Az Azure AD Password Protection proxyszolgáltatást üzemeltető összes gépet úgy kell konfigurálni, hogy a tartományvezérlők nek lehetővé kell tenni a proxyszolgáltatásba való bejelentkezést. Ezt a képességet a "Hozzáférés a hálózatról a számítógéphez" jogosultság-hozzárendelés szabályozza.
* Az Azure AD Password Protection proxyszolgáltatást üzemeltető összes gépet úgy kell konfigurálni, hogy engedélyezze a kimenő TLS 1.2 HTTP-forgalmat.
* Globális *rendszergazdai* fiók az Azure AD Password Protection proxyszolgáltatás és erdő regisztrálásához az Azure AD-vel.
* Az [alkalmazásproxy-környezet beállítási eljárásaiban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)megadott portok és URL-címek készletéhez engedélyezni kell a hálózati hozzáférést.

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>A Microsoft Azure AD Connect Agent Updater előfeltételei

A Microsoft Azure AD Connect Agent Updater szolgáltatás az Azure AD Password Protection Proxy szolgáltatással együtt van telepítve. További konfigurációra van szükség ahhoz, hogy a Microsoft Azure AD Connect Agent Updater szolgáltatás működhessen:

* Ha a környezet HTTP-proxykiszolgálót használ, kövesse a [Meglévő helyszíni proxykiszolgálók használata](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)című, a munka című dokumentumban meghatározott irányelveket.
* A Microsoft Azure AD Connect Agent Updater szolgáltatáshoz a [TLS-követelményekben](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements)megadott TLS 1.2-es lépésekre is szükség van.

> [!WARNING]
> Az Azure AD Password Protection proxy és az Azure AD application proxy a Microsoft Azure AD Connect Agent Updater szolgáltatás különböző verzióit telepíti, ezért az utasítások az alkalmazásproxy tartalmára vonatkoznak. Ezek a különböző verziók nem kompatibilisek, ha egymás mellett vannak telepítve, ezért nem ajánlott az Azure AD Password Protection Proxy és az Application Proxy telepítése ugyanazon a gépen.

## <a name="download-required-software"></a>A szükséges szoftver letöltése

A helyszíni Azure AD password protection telepítéshez két szükséges telepítő van:

* Azure AD password protection dc-ügynök *(AzureADPasswordProtectionDCAgentSetup.msi*)
* Azure AD password protection proxy *(AzureADPasswordProtectionProxySetup.exe)*

Töltse le mindkét telepítőt a [Microsoft letöltőközpontból.](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="install-and-configure-the-proxy-service"></a>A proxyszolgáltatás telepítése és konfigurálása

Az Azure AD Password Protection proxy szolgáltatás általában egy tagkiszolgálón a helyszíni AD DS-környezetben. A telepítés után az Azure AD Password Protection proxy szolgáltatás kommunikál az Azure AD-vel, hogy az Azure AD-bérlő globális és ügyfél által tiltott jelszólistáinak másolatát karbantarthassa.

A következő szakaszban telepíti az Azure AD Password Protection DC-ügynökök tartományvezérlők a helyszíni AD DS-környezetben. Ezek a tartományvezérlő-ügynökök kommunikálnak a proxyszolgáltatással, hogy megkapják a legújabb tiltott jelszólistákat a tartományon belüli jelszómódosítási események feldolgozásakor.

Válasszon ki egy vagy több kiszolgálót az Azure AD Password Protection proxyszolgáltatás üzemeltetéséhez. A kiszolgáló(k)ra a következő szempontok vonatkoznak:

* Minden ilyen szolgáltatás csak egy erdőhöz biztosíthat jelszóházirendeket. A gazdagépnek csatlakoznia kell az erdő tartományához. A gyökér- és gyermektartományok egyaránt támogatottak. Az erdő minden tartományában legalább egy tartományvezérlő és a jelszavas védelmi gép közötti hálózati kapcsolatra van szükség.
* Futtathatja az Azure AD Password Protection proxy szolgáltatás egy tartományvezérlőn tesztelésre, de a tartományvezérlő ezután internetkapcsolatszükséges. Ez a kapcsolat biztonsági problémát okozhat. Ezt a konfigurációt csak tesztelésre javasoljuk.
* Javasoljuk, hogy legalább két Azure AD password protection proxykiszolgálók redundancia, amint azt az előző szakaszban a [magas rendelkezésre állással kapcsolatos szempontokat.](#high-availability-considerations)
* Nem támogatott az Azure AD Password Protection proxyszolgáltatás futtatása csak olvasható tartományvezérlőn.

Az Azure AD Password Protection proxyszolgáltatás telepítéséhez hajtsa végre az alábbi lépéseket:

1. Az Azure AD Password Protection proxyszolgáltatás `AzureADPasswordProtectionProxySetup.exe` telepítéséhez futtassa a szoftvertelepítőt.

    A szoftver telepítése nem igényel újraindítást, és a szabványos MSI-eljárások használatával automatizálható, mint a következő példában:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > A telepítési hiba elkerülése érdekében `AzureADPasswordProtectionProxySetup.exe` a Windows tűzfal szolgáltatásnak futnia kell a csomag telepítése előtt.
    >
    > Ha a Windows tűzfal úgy van beállítva, hogy ne fusson, a megoldás az, hogy ideiglenesen engedélyezi és futtatja a tűzfal szolgáltatást a telepítés során. A proxyszoftver nek nincs specifikus függősége a Windows tűzfaltól a telepítés után.
    >
    > Ha külső gyártótól származó tűzfalat használ, továbbra is konfigurálni kell, hogy megfeleljen a telepítési követelményeknek. Ezek közé tartozik a bejövő hozzáférés engedélyezése a 135-ös porthoz és a proxy RPC kiszolgálóporthoz. További információt a [telepítési követelményekről](#deployment-requirements)szóló előző szakaszban talál.

1. Az Azure AD Password Protection proxyszoftver tartalmaz `AzureADPasswordProtection`egy új PowerShell-modult, . A következő lépések különböző parancsmagokat futtatnak ebből a PowerShell-modulból.

    A modul használatához nyisson meg egy PowerShell-ablakot rendszergazdaként, és importálja az új modult az alábbiak szerint:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Annak ellenőrzéséhez, hogy az Azure AD Password Protection proxyszolgáltatás fut, használja a következő PowerShell-parancsot:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Az eredménynek a *Futás* **állapotának** kell lennie.

1. A proxyszolgáltatás fut a számítógépen, de nem rendelkezik hitelesítő adatokkal az Azure AD-vel való kommunikációhoz. Regisztrálja az Azure AD password protection proxykiszolgálót az Azure AD-vel a `Register-AzureADPasswordProtectionProxy` parancsmag használatával.

    Ez a parancsmag globális rendszergazdai hitelesítő adatokat igényel az Azure-bérlőhöz. Az erdő gyökértartományában helyszíni Active Directory tartományi rendszergazdai jogosultságokra is szükség van. Ezt a parancsmadot helyi rendszergazdai jogosultságokkal rendelkező fiókkal is futtatni kell:

    Miután ez a parancs egyszer sikeres egy Azure AD Password Protection proxyszolgáltatás, további meghívása sikeres, de szükségtelen.

    A `Register-AzureADPasswordProtectionProxy` parancsmag a következő három hitelesítési módot támogatja. Az első két mód támogatja az Azure többtényezős hitelesítést, de a harmadik mód nem.

    > [!TIP]
    > Előfordulhat, hogy észrevehető késleltetés befejezése előtt az első alkalommal, amikor ez a parancsmag fut egy adott Azure-bérlő. Hacsak nem jelent meghibásodást, ne aggódjon a késés miatt.

     * Interaktív hitelesítési mód:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ez a mód server core operációs rendszereken nem működik. Ehelyett használja az alábbi hitelesítési módok egyikét. Ez a mód is sikertelen lehet, ha az Internet Explorer fokozott biztonsági beállításai engedélyezve vannak. A megoldás az, hogy letiltja a konfigurációt, regisztrálja a proxyt, majd újra engedélyezi azt.

     * Eszközkód-hitelesítési mód:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Amikor a rendszer kéri, a webböngésző megnyitására és a hitelesítési kód megadására mutató hivatkozást követve.

     * Csendes (jelszóalapú) hitelesítési mód:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ez a mód sikertelen, ha az Azure többtényezős hitelesítésszükséges a fiókjához. Ebben az esetben használja az előző két hitelesítési mód egyikét, vagy használjon egy másik fiókot, amely nem igényel többhitelesítést.
        >
        > Akkor is szükség lehet az MFA-ra, ha az Azure-eszközregisztráció (amelyet az Azure AD password protection a borítók alatt használ) úgy van konfigurálva, hogy globálisan többszinten többfa-t igényeljen. A követelmény megkerülése érdekében használhat egy másik fiókot, amely támogatja az MFA-t az előző két hitelesítési mód egyikével, vagy ideiglenesen lazíthatja az Azure-eszközregisztrációs mfa-követelményt.
        >
        > A módosítás hoz, keresse meg és válassza ki az **Azure Active Directory** az Azure Portalon, majd válassza eszközök > eszköz **beállítások**. Állítsa **be a Multi-Factor Auth megkövetelése az eszközök nem beállítására.** *No* A regisztráció befejezése után konfigurálja újra ezt a beállítást *Igen-re.*
        >
        > Azt javasoljuk, hogy az MFA-követelményeket csak tesztelési célokra kell megkerülni.

    Jelenleg nem kell megadnia a *-ForestCredential* paramétert, amely a jövőbeli funkciók számára van fenntartva.

    Az Azure AD Password Protection proxy szolgáltatás regisztrációja csak egyszer a szolgáltatás élettartama alatt szükséges. Ezt követően az Azure AD Password Protection proxy szolgáltatás automatikusan elvégzi az egyéb szükséges karbantartást.

1. Most regisztrálja a helyszíni Active Directory-erdőt a PowerShell-parancsmag használatával az `Register-AzureADPasswordProtectionForest` Azure-ral való kommunikációhoz szükséges hitelesítő adatokkal.

    > [!NOTE]
    > Ha több Azure AD password protection proxykiszolgáló van telepítve a környezetben, nem számít, hogy melyik proxykiszolgálót használja az erdő regisztrálásához.

    A parancsmag globális rendszergazdai hitelesítő adatokat igényel az Azure-bérlőhöz. Ezt a parancsmamot helyi rendszergazdai jogosultságokkal rendelkező fiókkal is futtatnia kell. Az Active Directory vállalati rendszergazdai jogosultságait is megköveteli. Ez a lépés erdőnként egyszer fut.

    A `Register-AzureADPasswordProtectionForest` parancsmag a következő három hitelesítési módot támogatja. Az első két mód támogatja az Azure többtényezős hitelesítést, de a harmadik mód nem.

    > [!TIP]
    > Előfordulhat, hogy észrevehető késleltetés befejezése előtt az első alkalommal, amikor ez a parancsmag fut egy adott Azure-bérlő. Hacsak nem jelent meghibásodást, ne aggódjon a késés miatt.

     * Interaktív hitelesítési mód:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ez a mód server core operációs rendszereken nem működik. Ehelyett használja az alábbi két hitelesítési mód egyikét. Ez a mód is sikertelen lehet, ha az Internet Explorer fokozott biztonsági beállításai engedélyezve vannak. A megoldás az, hogy letiltja a konfigurációt, regisztrálja az erdőt, majd újra engedélyezi azt.  

     * Eszközkód-hitelesítési mód:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Amikor a rendszer kéri, a webböngésző megnyitására és a hitelesítési kód megadására mutató hivatkozást követve.

     * Csendes (jelszóalapú) hitelesítési mód:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ez a mód sikertelen, ha az Azure többtényezős hitelesítésszükséges a fiókjához. Ebben az esetben használja az előző két hitelesítési mód egyikét, vagy használjon egy másik fiókot, amely nem igényel többhitelesítést.
        >
        > Akkor is szükség lehet az MFA-ra, ha az Azure-eszközregisztráció (amelyet az Azure AD password protection a borítók alatt használ) úgy van konfigurálva, hogy globálisan többszinten többfa-t igényeljen. A követelmény megkerülése érdekében használhat egy másik fiókot, amely támogatja az MFA-t az előző két hitelesítési mód egyikével, vagy ideiglenesen lazíthatja az Azure-eszközregisztrációs mfa-követelményt.
        >
        > A módosítás hoz, keresse meg és válassza ki az **Azure Active Directory** az Azure Portalon, majd válassza eszközök > eszköz **beállítások**. Állítsa **be a Multi-Factor Auth megkövetelése az eszközök nem beállítására.** *No* A regisztráció befejezése után konfigurálja újra ezt a beállítást *Igen-re.*
        >
        > Azt javasoljuk, hogy az MFA-követelményeket csak tesztelési célokra kell megkerülni.

       Ezek a példák csak akkor sikeresek, ha a jelenleg bejelentkezett felhasználó a gyökértartomány Active Directory tartományi rendszergazdája is. Ha nem ez a helyzet, alternatív tartományi hitelesítő adatokat adhat meg a *-ForestCredential* paraméteren keresztül.

    Az Active Directory erdő regisztrációja csak egyszer szükséges az erdő élettartama során. Ezt követően az Azure AD Password Protection DC-ügynökök az erdőben automatikusan elvégzi kondia minden egyéb szükséges karbantartást. Miután `Register-AzureADPasswordProtectionForest` egy erdő sikeresen lefutott, a parancsmag további meghívása sikeres, de szükségtelen.
    
    A `Register-AzureADPasswordProtectionForest` sikerhez legalább egy Windows Server 2012-es vagy újabb rendszert futtató tartományvezérlőnek elérhetőnek kell lennie az Azure AD Password Protection proxykiszolgáló tartományában. Az Azure AD Password Protection DC ügynök szoftver nem kell telepíteni a tartományvezérlők előtt ezt a lépést.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>A proxyszolgáltatás konfigurálása HTTP-proxyn keresztüli kommunikációra

Ha a környezet ben egy adott HTTP-proxy használatával kommunikálaz Azure-ral, az alábbi lépéseket az Azure AD Password Protection szolgáltatás konfigurálásához.

Hozzon létre egy *AzureADPasswordProtectionProxy.exe.config* fájlt a `%ProgramFiles%\Azure AD Password Protection Proxy\Service` mappában. Adja meg a következő tartalmat:

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

Mindkét esetben cserélje `http://yourhttpproxy.com:8080` le az adott HTTP-proxykiszolgáló címét és portját.

Ha a HTTP-proxy engedélyezési házirend használatára van konfigurálva, hozzáférést kell biztosítania annak a számítógépnek az Active Directory számítógépfiókjához, amely a jelszavas védelmet biztosító proxyszolgáltatást üzemelteti.

Azt javasoljuk, hogy állítsa le és indítsa újra az AzureAD Password Protection proxyszolgáltatás létrehozása vagy frissítése után az *AzureADPasswordProtectionProxy.exe.config* fájlt.

A proxyszolgáltatás nem támogatja a HTTP-proxyhoz való csatlakozáshoz szükséges hitelesítő adatok használatát.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>A proxyszolgáltatás beállítása egy adott porton való figyelésre

Az Azure AD Password Protection DC ügynök szoftver tcp-n keresztüli RPC-t használ a proxyszolgáltatással való kommunikációhoz. Alapértelmezés szerint az Azure AD Password Protection proxy szolgáltatás figyel minden elérhető dinamikus RPC-végponton. Beállíthatja, hogy a szolgáltatás egy adott TCP-porton figyeljen, ha szükséges, a környezethálózati topológia vagy tűzfalkövetelmények miatt.

<a id="static" /></a>Ha a szolgáltatást statikus port alatt történő futtatásra szeretné beállítani, használja a parancsmagát az `Set-AzureADPasswordProtectionProxyConfiguration` alábbiak szerint:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> A módosítások érvénybe léptetéséhez le kell állítania és újra kell indítania az Azure AD Password Protection proxyszolgáltatást.

Ha a szolgáltatást dinamikus port alatt való futtatásra szeretné beállítani, használja ugyanazt az eljárást, de állítsa vissza a *StaticPort* ot nullára:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> A módosítások érvénybe léptetéséhez le kell állítania és újra kell indítania az Azure AD Password Protection proxyszolgáltatást.

Az Azure AD Password Protection proxy szolgáltatás manuális újraindítást igényel a portkonfiguráció bármilyen módosítása után. A konfigurációs módosítások végrehajtása után nem kell újraindítania az Azure AD Password Protection DC-ügynökszolgáltatást a tartományvezérlőkön.

A szolgáltatás aktuális konfigurációjának lekérdezéséhez `Get-AzureADPasswordProtectionProxyConfiguration` használja a parancsmalapot az alábbi példában látható módon

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

A következő példa kimeneti kimenet azt mutatja, hogy az Azure AD Password Protection proxy szolgáltatás dinamikus portot használ:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>A tartományvezérlő-ügynök szolgáltatás telepítése

Az Azure AD Password Protection DC ügynök `AzureADPasswordProtectionDCAgentSetup.msi` szolgáltatás telepítéséhez futtassa a csomagot.

A szoftver telepítését a szabványos MSI-eljárások segítségével automatizálhatja, ahogy az a következő példában látható:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

A `/norestart` jelző elhagyható, ha szeretné, hogy a telepítő automatikusan indítsa újra a gépet.

A szoftver telepítését vagy eltávolítását újra kell indítani. Ez a követelmény azért van, mert a jelszószűrő DIL-ket csak újraindítás sal tölti be vagy tölti ki.

Az on-prem Azure AD password protection telepítése befejeződik, miután a tartományvezérlő-ügynök szoftver e tartományvezérlőre lett telepítve, és a számítógép újraindul. Nincs szükség más konfigurációra, és nem is lehetséges. Jelszómódosítási események az on-prem tartományvezérlők használatával a konfigurált tiltott jelszó listák az Azure AD.Password change events against the on-prem DCs use the configured tiltpassword lists from Azure AD.

Ha engedélyezni szeretné az Azure AD password protection-t az Azure Portalról, vagy egyéni tiltott jelszavakat szeretne konfigurálni, olvassa el a Helyszíni Azure AD password protection engedélyezése című [témakört.](howto-password-ban-bad-on-premises-operations.md)

> [!TIP]
> Telepítheti az Azure AD Password Protection DC-ügynök egy olyan gépen, amely még nem tartományvezérlő. Ebben az esetben a szolgáltatás elindul és fut, de inaktív marad, amíg a gép tartományvezérlővé nem lép.

## <a name="upgrading-the-proxy-service"></a>A proxyszolgáltatás frissítése

Az Azure AD Password Protection proxy szolgáltatás támogatja az automatikus frissítést. Az automatikus frissítés a Microsoft Azure AD Connect Agent Updater szolgáltatást használja, amely a proxyszolgáltatással együtt van telepítve. Az automatikus frissítés alapértelmezés szerint be van kapcsolva, és a `Set-AzureADPasswordProtectionProxyConfiguration` parancsmag használatával engedélyezhető vagy letiltható.

Az aktuális beállítás lekérdezhető `Get-AzureADPasswordProtectionProxyConfiguration` a parancsmag használatával. Azt javasoljuk, hogy az automatikus frissítési beállítás mindig engedélyezve van.

A `Get-AzureADPasswordProtectionProxy` parancsmag segítségével lekérdezheti az összes jelenleg telepített Azure AD Password Protection proxykiszolgálók egy erdőben.

### <a name="manual-upgrade-process"></a>Kézi frissítési folyamat

A manuális frissítés a `AzureADPasswordProtectionProxySetup.exe` szoftvertelepítő legújabb verziójának futtatásával történik. A szoftver legújabb verziója elérhető a [Microsoft letöltőközpontban.](https://www.microsoft.com/download/details.aspx?id=57071)

Nem szükséges eltávolítani az Azure AD Password Protection proxyszolgáltatás aktuális verzióját – a telepítő helyi frissítést hajt végre. A proxyszolgáltatás frissítésekor nincs szükség újraindításra. A szoftverfrissítés automatizálható a szabványos MSI-eljárásokkal, például `AzureADPasswordProtectionProxySetup.exe /quiet`a .

## <a name="upgrading-the-dc-agent"></a>A tartományvezérlő-ügynök frissítése

Ha az Azure AD Password Protection DC-ügynök szoftver újabb verziója érhető el, a `AzureADPasswordProtectionDCAgentSetup.msi` frissítés a szoftvercsomag legújabb verziójának futtatásával történik. A szoftver legújabb verziója elérhető a [Microsoft letöltőközpontban.](https://www.microsoft.com/download/details.aspx?id=57071)

Nem szükséges eltávolítani a dc-ügynök szoftverének aktuális verzióját – a telepítő helyben frissíti a frissítést. A tartományvezérlő-ügynök szoftverének frissítésekor mindig újra kell indítani az újraindítást – ezt a követelményt a Windows alapvető viselkedése okozza.

A szoftverfrissítés automatizálható a szabványos MSI-eljárásokkal, például `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`a .

Kihagyhatja a `/norestart` jelzőt, ha szeretné, hogy a telepítő automatikusan újraindítsa a gépet.

A `Get-AzureADPasswordProtectionDCAgent` parancsmag használható az összes jelenleg telepített Azure AD Password Protection DC-ügynök szoftververziójának lekérdezésére egy erdőben.

## <a name="next-steps"></a>További lépések

Most, hogy telepítette az Azure AD password protection-hez szükséges szolgáltatásokat a helyszíni kiszolgálókon, [engedélyezze az Azure-portálon az Azure-portálon az](howto-password-ban-bad-on-premises-operations.md) üzembe helyezés befejezéséhez.
