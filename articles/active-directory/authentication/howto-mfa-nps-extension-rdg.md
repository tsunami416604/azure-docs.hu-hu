---
title: RdG integrálása az Azure MFA NPS-bővítményével – Azure Active Directory
description: A Távoli asztali átjáró infrastruktúrájának integrálása az Azure MFA-val a Microsoft Azure Network Policy Server bővítményével
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c61bea7f3ca1105edfec54501c5f0725a5a10225
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654115"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>A Távoli asztali átjáró infrastruktúrájának integrálása a Hálózati házirend-kiszolgáló (NPS) bővítmény és az Azure AD használatával

Ez a cikk a Távoli asztali átjáró infrastruktúrájának az Azure többtényezős hitelesítéssel (MFA) való integrálásának részleteit ismerteti a Microsoft Azure Hálózati házirend-kiszolgáló (NPS) bővítményhasználatával.

Az Azure Hálózati házirend-kiszolgáló (NPS) bővítménye lehetővé teszi az ügyfelek számára, hogy megvédjék a radius-ügyfélhitelesítést az Azure felhőalapú többtényezős hitelesítésével .The Network Policy Server (NPS) extension for Azure allows customers to safeguard Remote Authentication Dial-In User Service (RADIUS) client authentication using Azure's [cloud-based Multi-Factor Authentication (MFA)](multi-factor-authentication.md). Ez a megoldás kétlépéses ellenőrzést biztosít egy második biztonsági réteg hozzáadásához a felhasználói bejelentkezésekhez és tranzakciókhoz.

Ez a cikk lépésenként ismerteti a nps-infrastruktúra integrálását az Azure MFA-val az Azure-beli n-ps bővítmény használatával. Ez lehetővé teszi a távoli asztali átjáróba bejelentkezni próbáló felhasználók biztonságos ellenőrzését.

> [!NOTE]
> Ez a cikk nem használható Az MFA-kiszolgáló központi telepítések, és csak azure MFA (felhőalapú) központi telepítések.

A hálózati házirend-házirend és -hozzáférés szolgáltatások (NPS) lehetővé teszi a szervezetek számára, hogy a következőket tegyék:

* A hálózati kérelmek felügyeletének és vezérlésének központi helyeit úgy határozza meg, hogy ki csatlakozhat, milyen napszakú kapcsolatok engedélyezettek, mennyi a kapcsolatok időtartama, és milyen szintű biztonságot kell használniuk az ügyfeleknek a csatlakozáshoz, és így tovább. Ahelyett, hogy ezeket a házirendeket minden VPN- vagy távoli asztali (RD) átjárókiszolgálón megadnák, ezek a házirendek egy központi helyen egyszer megadhatók. A RADIUS protokoll biztosítja a központosított hitelesítést, engedélyezést és számlázást (AAA).
* Hozzon létre és kényszerítse nt-ügyfél állapotházirendeket, amelyek meghatározzák, hogy az eszközök korlátlan vagy korlátozott hozzáférést kapnak-e a hálózati erőforrásokhoz.
* A 802.1x-képes vezeték nélküli hozzáférési pontok és Ethernet-kapcsolók hitelesítésének és engedélyezésének kényszerítéséhez.

A szervezetek általában a n-ps (RADIUS) használatával egyszerűsítik és központosítják a VPN-házirendek kezelését. Azonban számos szervezet is használja a nps egyszerűsítésére és központosítására a Távoli asztali kapcsolat engedélyezési házirendek (Távoli asztali kapcsolat engedélyezési házirendek) felügyeletét.

A szervezetek a nps-t is integrálhatják az Azure MFA-val a biztonság növelése és a magas szintű megfelelőség biztosítása érdekében. Ez segít annak biztosításában, hogy a felhasználók kétlépéses ellenőrzést hozzanak létre a Távoli asztali átjáróba való bejelentkezéshez. Ahhoz, hogy a felhasználók hozzáférést kaphassak, meg kell adniuk a felhasználónevük/jelszavuk kombinációját a felhasználó által a felügyeletük alatt álló információkkal együtt. Ezt az információt meg kell bízni, és nem könnyen duplikált, mint például a mobiltelefon számát, vezetékes számot, alkalmazás egy mobil eszközön, és így tovább. Az RDG jelenleg támogatja a 2FA-hoz a Microsoft hitelesítő alkalmazásmetódusaiból származó telefonhívás- és leküldéses értesítéseket. A támogatott hitelesítési módszerekről további információt A felhasználók által [használható hitelesítési módszerek meghatározása](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)című szakaszban talál.

Az Azure hálózati címző-bővítményének rendelkezésre állása előtt azoknak az ügyfeleknek, akik kétlépéses ellenőrzést kívántak végrehajtani az integrált hálózati kiszolgáló- és Az Azure MFA-környezetekben, a [RADIUS használatával](howto-mfaserver-nps-rdg.md)dokumentált anamnézisben egy külön MFA-kiszolgálót kellett konfigurálniuk és karbantartaniuk a helyszíni környezetben.

Az Azure-beli n-ps bővítmény rendelkezésre állása most antól lehetővé teszi a szervezetek számára, hogy helyszíni MFA-megoldást vagy felhőalapú MFA-megoldást telepítsenek a RADIUS-ügyfélhitelesítés biztonságossá tétele érdekében.

## <a name="authentication-flow"></a>A hitelesítési folyamat

Ahhoz, hogy a felhasználók távoli asztali átjárón keresztül kapják meg a hálózati erőforrásokhoz való hozzáférést, meg kell felelniük az egy távoli asztali kapcsolatengedélyezési házirendben (Távoli asztali kapcsolat engedélyezési házirend) és egy távoli asztali erőforrás-engedélyezési házirendben (Távoli asztali erőforrásengedélyezési házirend) meghatározott feltételeknek. A távoli asztali hitelesítésszolgáltatók határozzák meg, hogy ki jogosult a Távoli asztali átjárókhoz való csatlakozásra. A távoli asztali erőforrás-házirendek határozzák meg azokat a hálózati erőforrásokat, például a távoli asztalokat vagy a távoli alkalmazásokat, amelyekhez a felhasználó csatlakozhat a Távoli asztali átjárón keresztül.

A Távoli asztali átjáró beállítható úgy, hogy központi házirend-tárolót használjon a távoli asztali hitelesítésszolgáltatókhoz. A Távoli asztali raszp-ok nem használhatnak központi házirendet, mivel a Távoli asztali átjárón dolgozzák fel őket. A távoli asztali ügyfélállomások központi házirend-tárolójának használatára konfigurált Távoli asztali átjáró például egy másik hálózati házirend-kiszolgáló RADIUS-ügyfele, amely központi házirendtárolóként szolgál.

Ha az Azure hálózati kiszolgálóbővítménye integrálva van a hálózati kiszolgálóval és a távoli asztali átjáróval, a sikeres hitelesítési folyamat a következő:

1. A Távoli asztali átjárókiszolgáló hitelesítési kérelmet kap egy távoli asztali felhasználótól, hogy csatlakozzon egy erőforráshoz, például egy Távoli asztal munkamenethez. A Távoli asztali átjárókiszolgáló RADIUS-ügyfélként játszva a kérelmet RADIUS-hozzáférés-kérelem üzenetté alakítja, és elküldi az üzenetet annak a RADIUS-kiszolgálónak, amelyen a hálózati házirend-kiszolgáló bővítmény e-mail-bővítménye telepítve van.
1. A felhasználónév és a jelszó kombinációja az Active Directoryban ellenőrizve, a felhasználó hitelesítése pedig megtörtént.
1. Ha a hálózati házirend-kiszolgáló csatlakozási kérelemben és a hálózati házirendekben megadott összes feltétel teljesül (például napszak vagy csoporttagsági korlátozások), a hálózati házirend-kiszolgáló bővítmény másodlagos hitelesítési kérelmet indít el az Azure MFA-val.
1. Az Azure MFA kommunikál az Azure AD-vel, lekéri a felhasználó adatait, és végrehajtja a másodlagos hitelesítést támogatott módszerekkel.
1. Az MFA-kihívás sikerességére az Azure MFA közli az eredményt a nps-bővítmény.
1. A bővítményt kiszolgáló hálózati házirend-kiszolgáló a Távoli asztali kapcsolatházirend-házirend RADIUS-accept üzenetét küldi a Távoli asztali átjárókiszolgálónak.
1. A felhasználó a Távoli asztali átjárón keresztül kap hozzáférést a kért hálózati erőforráshoz.

## <a name="prerequisites"></a>Előfeltételek

Ez a szakasz részletezi az Azure MFA integrálása előtt szükséges előfeltételeket a távoli asztali átjáró. Mielőtt elkezdené, rendelkeznie kell a következő előfeltételek a helyén.  

* Távoli asztali szolgáltatások (RDS) infrastruktúrája
* Azure MFA-licenc
* Windows Server szoftver
* Hálózati házirend- és elérési szolgáltatások (NPS) szerepköre
* Az Azure Active Directory a helyszíni Active Directoryval szinkronizálva
* Az Azure Active Directory GUID azonosítója

### <a name="remote-desktop-services-rds-infrastructure"></a>Távoli asztali szolgáltatások (RDS) infrastruktúrája

Működő Távoli asztali szolgáltatások (RDS) infrastruktúrával kell rendelkeznie. Ha nem, akkor gyorsan létrehozhatja ezt az infrastruktúrát az Azure-ban a következő rövid útmutató sablon használatával: [Távoli asztali munkamenet-gyűjtemény üzembe helyezése létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Ha azt szeretné, hogy manuálisan hozzon létre egy helyszíni RDS-infrastruktúra gyors tesztelési célokra, kövesse a lépéseket, hogy üzembe helyezése egy.
**További információ**: [RdS telepítése az Azure gyorsindításával](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) és [az alapvető RDS-infrastruktúra telepítésével.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)

### <a name="azure-mfa-license"></a>Azure MFA-licenc

Szükséges az Azure MFA licenc, amely elérhető az Azure AD Premium vagy más csomagok, amelyek tartalmazzák azt. Az Azure MFA-hoz, például felhasználónkénti vagy hitelesítési licencek fogyasztásalapú licencei nem kompatibilisek a nps-bővítménylel. További információ: [Az Azure többtényezős hitelesítésbe beszerezni.](concept-mfa-licensing.md) Tesztelési célokra próba-előfizetést is használhat.

### <a name="windows-server-software"></a>Windows Server szoftver

A n-ps kiterjesztéshez windows Server 2008 R2 SP1 vagy újabb rendszer szükséges, ha telepítve van a n-kiszolgálói szerepkör-szolgáltatás. A szakasz ban ismertetett összes lépés a Windows Server 2016 használatával történt.

### <a name="network-policy-and-access-services-nps-role"></a>Hálózati házirend- és elérési szolgáltatások (NPS) szerepköre

A hálózati házirend-kiszolgáló szerepkör-szolgáltatás biztosítja a RADIUS-kiszolgáló és az ügyfél funkciókat, valamint a hálózati hozzáférési házirend állapotszolgáltatását. Ezt a szerepkört legalább két számítógépre telepíteni kell az infrastruktúrában: a Távoli asztali átjáróra és egy másik tagkiszolgálóra vagy tartományvezérlőre. Alapértelmezés szerint a szerepkör már szerepel a Távoli asztali átjáróként konfigurált számítógépen.  A hálózati kiszolgáló szerepkört legalább egy másik számítógépre, például tartományvezérlőre vagy tagkiszolgálóra kell telepítenie.

A Windows Server 2012 vagy régebbi hálózatházirend-kiszolgáló telepítése című témakörben talál további információt a Hálózat-házirend-kiszolgáló telepítése című [témakörben.](https://technet.microsoft.com/library/dd296890.aspx) A hálózati kiszolgálóval kapcsolatos gyakorlati tanácsok ismertetése, beleértve a hálózati kiszolgáló tartományvezérlőre való telepítésének ajánlását, a [hálózati kiszolgáló ajánlott eljárások a csoporton belüli kiszolgálóhoz](https://technet.microsoft.com/library/cc771746)című témakörben található.

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Az Azure Active Directory a helyszíni Active Directoryval szinkronizálva

A hálózati szolgáltató bővítmény használatához a helyszíni felhasználókat szinkronizálni kell az Azure AD-vel, és engedélyezni kell az MFA számára. Ez a szakasz feltételezi, hogy a helyszíni felhasználók az AD Connect használatával vannak szinkronizálva az Azure AD-vel. Az Azure AD-csatlakozásról a [Helyszíni könyvtárak integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)című témakörben talál.

### <a name="azure-active-directory-guid-id"></a>Az Azure Active Directory GUID azonosítója

A nps-bővítmény telepítéséhez ismernie kell az Azure AD GUID azonosítóját. Az Azure AD GUID-jának megkeresésére vonatkozó utasításokat az alábbiakban találja.

## <a name="configure-multi-factor-authentication"></a>Többtényezős hitelesítés konfigurálása

Ez a szakasz az Azure MFA és a távoli asztali átjáró integrálására vonatkozó utasításokat tartalmaz. Rendszergazdaként konfigurálnia kell az Azure MFA-szolgáltatást, mielőtt a felhasználók saját maguk regisztrálhatnák a többtényezős eszközeiket vagy alkalmazásaikat.

Kövesse az [Azure többtényezős hitelesítéssel](howto-mfa-getstarted.md) való első lépések a felhőben című lépéseit az MFA-hitelesítés engedélyezéséhez az Azure AD-felhasználók számára.

### <a name="configure-accounts-for-two-step-verification"></a>Fiókok konfigurálása a kétlépéses ellenőrzéshez

Miután egy fiók engedélyezve van az MFA-hoz, nem tud bejelentkezni az MFA-házirend által szabályozott erőforrásokba, amíg nem konfigurált egy megbízható eszközt a második hitelesítési tényezőhöz való használatra, és kétlépéses ellenőrzéssel hitelesített.

Kövesse a [Mit jelent számomra az Azure többtényezős hitelesítése](../user-help/multi-factor-authentication-end-user.md) című lépésekben, hogy megértsem és megfelelően konfigurálhassa az eszközeit az MFA-hoz a felhasználói fiókkal.

## <a name="install-and-configure-nps-extension"></a>Hálózati kiszolgálóbővítmény telepítése és konfigurálása

Ez a szakasz az RDS-infrastruktúra konfigurálása az Azure MFA az ügyfél-hitelesítés a távoli asztali átjáró használatával.

### <a name="acquire-azure-active-directory-guid-id"></a>Az Azure Active Directory GUID azonosítójának beszerzése

A hálózati címzési szolgáltató bővítmény konfigurációjának részeként rendszergazdai hitelesítő adatokat és az Azure AD-bérlő azure AD-bérlő i AD-azonosítót kell megadnia. A következő lépések bemutatják, hogyan szerezheti be a bérlői azonosítót.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure-bérlő globális rendszergazdájaként.
1. Az Azure Portal menüben válassza az **Azure Active Directory**, vagy keressen, és válassza az Azure Active **Directory** bármely lapon.
1. Válassza **a Tulajdonságok lehetőséget.**
1. A Tulajdonságok panelen, a Címtárazonosító mellett kattintson a **Másolás** ikonra az alábbi ábrán, és másolja az azonosítót a vágólapra.

   ![A címtár-azonosító beszerzése az Azure Portalról](./media/howto-mfa-nps-extension-rdg/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>A nps-bővítmény telepítése

Telepítse a hálózati házirend-kiszolgáló bővítményt olyan kiszolgálóra, amelyen telepítve van a Hálózati házirend-kiszolgáló (NPS) szerepkör. Ez a terv RADIUS-kiszolgálójaként működik.

> [!Important]
> Ügyeljen arra, hogy ne telepítse a hálózati kiszolgáló bővítményét a Távoli asztali átjárókiszolgálóra.
>

1. Töltse le a [nps kiterjesztést](https://aka.ms/npsmfa).
1. Másolja a telepítő végrehajtható fájlt (NpsExtnForAzureMfaInstaller.exe) a nps-kiszolgálóra.
1. A nps server en kattintson duplán **az NpsExtnForAzureMfaInstaller.exe ikonra.** Ha a rendszer kéri, kattintson a **Futtatás**gombra.
1. Az NPS Extension For Azure MFA setup párbeszédpanelen tekintse át a szoftverlicenc-feltételeket, jelölje **be a Licencfeltételek et,** és kattintson a **Telepítés gombra.**
1. Az Azure MFA-telepítéshez a NPS-bővítmény párbeszédpanelen kattintson a **Bezárás**gombra.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Tanúsítványok konfigurálása a hálózati kiszolgálóbővítményhez PowerShell-parancsfájl használatával

Ezután konfigurálnia kell a tanúsítványokat a hálózati kiszolgáló bővítmény általi használatra a biztonságos kommunikáció és megbízhatóság biztosítása érdekében. A hálózati kiszolgáló összetevői tartalmaznak egy Windows PowerShell-parancsfájlt, amely konfigurálja az önaláírt tanúsítványt a hálózati kiszolgálóval való használatra.

A parancsfájl a következő műveleteket hajtja végre:

* Önaláírt tanúsítvány létrehozása
* A tanúsítvány nyilvános kulcsának társrésze az Azure AD szolgáltatásának
* A tanúsítvány a helyi gépboltban tárolható
* Hozzáférést biztosít a tanúsítvány személyes kulcsához a hálózati felhasználó számára
* Újraindítja a Hálózati házirend-kiszolgáló szolgáltatást

Ha saját tanúsítványokat szeretne használni, a tanúsítvány nyilvános kulcsát az Azure AD szolgáltatásnévhez kell társítania, és így tovább.

A parancsfájl használatához adja meg a bővítményt az Azure AD-rendszergazdai hitelesítő adatokkal és az Azure AD-bérlői azonosítóval, amelyet korábban másolt. Futtassa a parancsfájlt minden olyan nps-kiszolgálón, ahol telepítette a n-ps bővítményt. Ezután tegye a következőket:

1. Nyisson meg egy felügyeleti Windows PowerShell-kérdést.
1. A PowerShell parancssorába írja be a parancsát, `cd 'c:\Program Files\Microsoft\AzureMfa\Config'`és nyomja le az ENTER **billentyűt.**
1. Írja `.\AzureMfaNpsExtnConfigSetup.ps1`be a típust, és nyomja **le az ENTER billentyűt.** A parancsfájl ellenőrzi, hogy az Azure Active Directory PowerShell-modul telepítve van-e. Ha nincs telepítve, a parancsfájl telepíti a modult.

   ![Az AzureMfaNpsExtnConfigSetup.ps1 futtatása az Azure AD PowerShellben](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Miután a parancsfájl ellenőrzi a PowerShell-modul telepítését, megjeleníti az Azure Active Directory PowerShell-modul párbeszédpanelt. A párbeszédpanelen adja meg az Azure AD-rendszergazdahitelesítő adatait és jelszavát, majd kattintson **a Bejelentkezés**gombra.

   ![Hitelesítő azure AD-n a PowerShellben](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Amikor a rendszer kéri, illessze be a vágólapra korábban másolt címtárazonosítót, és nyomja le az **ENTER billentyűt.**

   ![A címtárazonosító bevitele a PowerShellben](./media/howto-mfa-nps-extension-rdg/image6.png)

1. A parancsfájl önaláírt tanúsítványt hoz létre, és egyéb konfigurációs módosításokat hajt végre. A kimenetnek olyannak kell lennie, mint az alábbi képen.

   ![Az önaláírt tanúsítvánnyal megjelenítő PowerShell kimenete](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Hálózati kiszolgáló-összetevők konfigurálása távoli asztali átjárón

Ebben a szakaszban konfigurálja a Távoli asztali átjáró kapcsolatengedélyezési házirendjeit és más RADIUS-beállításokat.

A hitelesítési folyamat megköveteli, hogy a RADIUS-üzenetek cseréje a Távoli asztali átjáró és a hálózati kiszolgáló kiszolgáló között, ahol a hálózati kiszolgáló bővítmény telepítve van. Ez azt jelenti, hogy radius-ügyfélbeállításokat kell konfigurálnia a Távoli asztali átjárón és a hálózati házirend-kiszolgálón, ahol a hálózati házirend-kiszolgáló bővítmény telepítve van.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>A Távoli asztali átjáró kapcsolatengedélyezési házirendjeinek konfigurálása a központi tároló használatára

A távoli asztali kapcsolatengedélyezési házirendek (Távoli asztali hitelesítésszolgáltatók) határozzák meg a távoli asztali átjárókiszolgálóhoz való csatlakozás követelményeit. A távoli asztali kapcsolat-működtető szolgáltatók helyileg (alapértelmezett) tárolhatók, vagy tárolhatók egy központi Távoli asztali kapcsolatszolgáltatás-tárolóban, amely a nps-t futtatja. Az Azure MFA és az RDS integrációjának konfigurálásához meg kell adnia egy központi áruház használatát.

1. A Távoli asztali átjárókiszolgálón nyissa meg a **Kiszolgálókezelőt**.
1. Kattintson a menü **Eszközök parancsára,** mutasson a **Távoli asztali szolgáltatások**pontra, majd kattintson a Távoli asztali **átjárókezelő parancsra.**
1. A Távoli asztali átjárókezelőben kattintson a jobb gombbal ** \[a Kiszolgálónév\] (helyi)** elemre, majd kattintson a Tulajdonságok **parancsra.**
1. A Tulajdonságok párbeszédpanelen válassza a **Távoli asztali kapcsolat, a kap tároló** lapot.
1. A Távoli asztali kapcsolatszolgáltatás tárolója lapon válassza a **Központi kiszolgáló n-ps-t futtató csoportját.** 
1. A **Név- vagy IP-cím megadása a név- vagy IP-címmezőbe** írja be annak a kiszolgálónak az IP-címét vagy kiszolgálónevét, amelyen a nps-bővítményt telepítette.

   ![Adja meg a nps-kiszolgáló nevét vagy IP-címét](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Kattintson a **Hozzáadás** gombra.
1. A **Megosztott titok** párbeszédpanelen adjon meg egy megosztott titkot, majd kattintson az **OK**gombra. Győződjön meg arról, hogy rögzíti ezt a megosztott titkot, és biztonságosan tárolja a rekordot.

   >[!NOTE]
   >A megosztott titkos kulcsot a RADIUS-kiszolgálók és az ügyfelek közötti bizalmi kapcsolat létrehozására használják. Hozzon létre egy hosszú és összetett titkot.
   >

   ![Megosztott titok létrehozása a bizalmi kapcsolat létrehozásához](./media/howto-mfa-nps-extension-rdg/image11.png)

1. A párbeszédpanel bezárásához kattintson az **OK** gombra.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>RADIUS-időtúlérték konfigurálása távoli asztali átjáró hálózati kiszolgálón

Annak érdekében, hogy legyen idő a felhasználók hitelesítő adatainak ellenőrzésére, a kétlépéses ellenőrzés végrehajtására, a válaszok fogadására és a RADIUS-üzenetek megválaszolására, módosítani kell a RADIUS időtúllépési értékét.

1. A Távoli asztali átjárókiszolgálón nyissa meg a Kiszolgálókezelőt. Kattintson a menü **Eszközök parancsára,** majd a **Hálózati házirend-kiszolgáló parancsra.**
1. A **n-ps (helyi)** konzolon bontsa ki a **RADIUS-ügyfelek és -kiszolgálók csomópontot,** és válassza a **Távoli RADIUS-kiszolgáló**lehetőséget.

   ![A Hálózati házirend-kiszolgáló felügyeleti konzolja a Távoli RADIUS-kiszolgálóval](./media/howto-mfa-nps-extension-rdg/image12.png)

1. A részletek ablaktáblán kattintson duplán a **TS GATEWAY SERVER GROUP**elemre.

   >[!NOTE]
   >Ez a RADIUS-kiszolgálócsoport akkor jött létre, amikor konfigurálta a központi kiszolgálót a nps-házirendekhez. A Távoli asztali átjáró radius-üzeneteket továbbít erre a kiszolgálóra vagy kiszolgálócsoportra, ha a csoportban egynél több is van.
   >

1. A **TS GATEWAY SERVER GROUP Tulajdonságai** párbeszédpanelen jelölje ki a távoli asztali hitelesítésszolgáltatók tárolására konfigurált hálózati házirend-kiszolgáló IP-címét vagy nevét, majd kattintson a Szerkesztés **gombra.**

   ![A korábban konfigurált nps-kiszolgáló IP-címének vagy nevének kiválasztása](./media/howto-mfa-nps-extension-rdg/image13.png)

1. A **RADIUS-kiszolgáló szerkesztése** párbeszédpanelen jelölje be a **Terheléselosztás** lapot.
1. A **Terheléselosztás** lap **Válasz nélküli másodpercek száma a kérelem eldobása előtt** mezőben módosítsa az alapértelmezett értéket 3-ról 30 és 60 másodperc közötti értékre.
1. A **kérelmek közötti másodpercek száma, ha a kiszolgáló nem érhető el** mezőben az alapértelmezett 30 másodperces értéket az előző lépésben megadott értékkel egyenlő vagy annál nagyobb értékre módosítsa.

   ![A Radius-kiszolgáló időtúlterhelési beállításainak szerkesztése a terheléselosztás lapon](./media/howto-mfa-nps-extension-rdg/image14.png)

1. A párbeszédpanelek bezárásához kattintson kétszer az **OK** gombra.

### <a name="verify-connection-request-policies"></a>Kapcsolatkérelem-házirendek ellenőrzése

Alapértelmezés szerint, ha úgy konfigurálja a Távoli asztali átjárót, hogy központi házirend-tárolót használjon a kapcsolatengedélyezési házirendekhez, a Távoli asztali átjáró úgy van beállítva, hogy a CAP-kérelmeket továbbítsa a hálózati házirend-kiszolgálónak. A nps-kiszolgáló az Azure MFA-bővítmény telepítve, feldolgozza a RADIUS-hozzáférési kérelmet. A következő lépések bemutatják, hogyan ellenőrizheti az alapértelmezett kapcsolatkérelem-házirendet.  

1. A Távoli asztali átjáró nps (helyi) konzolján bontsa ki a **Házirendek**csomópontot, és válassza a **Kapcsolatkérelem-házirendek lehetőséget.**
1. Kattintson duplán a **TS GATEWAY ENGEDÉLYEZÉSI HÁZIREND elemre.**
1. A **TS GATEWAY ENGEDÉLYEZÉSI HÁZIREND tulajdonságai** párbeszédpanelen kattintson a **Beállítások** fülre.
1. A **Beállítások** lap Átirányítási kapcsolatkérelem csoportjában kattintson a **Hitelesítés**gombra. A RADIUS-ügyfél úgy van beállítva, hogy továbbítsa a hitelesítési kérelmeket.

   ![A kiszolgálócsoportot megmeghatározó hitelesítési beállítások konfigurálása](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Kattintson **a Mégse gombra.**

>[!NOTE]
> A kapcsolatkérelem-házirend ek létrehozásáról a [Kapcsolatkérelem-házirendek dokumentációjának konfigurálása](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) című cikkben olvashat bővebben. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Hálózati kiszolgáló konfigurálása azon a kiszolgálón, amelyen a hálózati kiszolgáló bővítménye telepítve van

Annak a hálózati helyt/ helyt/ helytabővítménynek, amelyen telepítve van a hálózati kiszolgáló bővítménye, képesnek kell lennie RADIUS-üzenetek cseréjére a távoli asztali átjáró nps-kiszolgálójával. Az üzenetváltás engedélyezéséhez konfigurálnia kell a hálózati kiszolgáló összetevőit azon a kiszolgálón, ahol a hálózati kiszolgáló bővítményszolgáltatás telepítve van.

### <a name="register-server-in-active-directory"></a>Kiszolgáló regisztrálása az Active Directoryban

Ebben az esetben a megfelelő működéshez a nps-kiszolgálót regisztrálni kell az Active Directoryban.

1. A nps-kiszolgálón nyissa meg a **Kiszolgálókezelőt.**
1. A Kiszolgálókezelőben kattintson **az Eszközök**menü Hálózati **házirend-kiszolgáló parancsára.**
1. A Hálózati házirend-kiszolgáló konzolon kattintson a jobb gombbal a **hálózati házirend-kiszolgáló (helyi)** elemre, majd kattintson **a Kiszolgáló regisztrálása parancsra az Active Directoryban parancsra.**
1. Kattintson kétszer **az OK** gombra.

   ![A n-kiszolgáló regisztrálása az Active Directoryban](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Hagyja nyitva a konzolt a következő eljáráshoz.

### <a name="create-and-configure-radius-client"></a>RADIUS-ügyfél létrehozása és konfigurálása

A Távoli asztali átjárót RADIUS-ügyfélként kell konfigurálni a hálózati házirend-kiszolgálószámára.

1. Azon a n-ps-kiszolgálón, amelyen a n-házirend-kiszolgáló bővítmény telepítve van, a **helyi (nps)** konzolon kattintson a jobb gombbal a **RADIUS-ügyfelek elemre,** és kattintson az **Új parancsra.**

   ![Új RADIUS-ügyfél létrehozása a nps-konzolon](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Az **Új RADIUS-ügyfél** párbeszédpanelen adjon meg egy rövid nevet, például _az Átjáró_t, valamint a Távoli asztali átjárókiszolgáló IP-címét vagy DNS-nevét.
1. A **Megosztott titok** és a **Megosztott titkos titok megerősítése** mezőkben adja meg ugyanazt a titkos kulcsot, amelyet korábban használt.

   ![Rövid név és IP- vagy DNS-cím beállítása](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Az Új RADIUS-ügyfél párbeszédpanel bezárásához kattintson az **OK** gombra.

### <a name="configure-network-policy"></a>Hálózati házirend konfigurálása

Emlékezzünk vissza, hogy az Azure MFA-bővítményt tartalmazó nps-kiszolgáló a kapcsolatengedélyezési házirend (CAP) kijelölt központi házirend-tárolója. Ezért az érvényes kapcsolatkérelmek engedélyezéséhez a hálózati kiszolgálón be kell tartania a KAP-ot.  

1. A hálózati házirend-kiszolgálón nyissa meg a hálózati házirend-kiszolgáló (helyi) konzolt, bontsa ki a **Házirendeket**, és kattintson a **Hálózati házirendek parancsra.**
1. Kattintson a jobb gombbal **a Kapcsolatok más hozzáférési kiszolgálókkal**elemre, és kattintson a Házirend **másolása parancsra.**

   ![A más hozzáférési kiszolgálókkal való kapcsolat megkettőzése](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Kattintson a jobb gombbal **a Kapcsolatok másolása más hozzáférési kiszolgálókra**, majd kattintson a **Tulajdonságok**parancsra.
1. A **Kapcsolatok másolása más hozzáférési kiszolgálókkal** párbeszédpanel **Házirend neve**mezőjébe írja be a megfelelő nevet, például _RDG_CAP._ Ellenőrizze, **hogy a Házirend engedélyezve**van, és válassza **a Hozzáférés engedélyezése**lehetőséget. A Hálózati **hozzáférést elérő kiszolgáló típusa**mezőben válassza a **Távoli asztali átjáró**lehetőséget, vagy hagyja **meghatározatlan**ként.

   ![A házirend elnevezése, engedélyezése és megadása](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Kattintson a **Kényszerek** fülre, és jelölje be **az Ügyfelek csatlakozásának engedélyezése hitelesítési módszer egyeztetése nélkül jelölőnégyzetet.**

   ![Hitelesítési módszerek módosítása az ügyfelek csatlakozásának engedélyezéséhez](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Ha szükséges, kattintson a **Feltételek** fülre, és adja meg azon feltételeket, amelyeknek teljesülniük kell ahhoz, hogy a kapcsolat engedélyezhető legyen, például egy adott Windows-csoportban való tagság.

   ![Adja meg a kapcsolat feltételeit](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Kattintson az **OK** gombra. Amikor a rendszer a megfelelő súgótémakör megtekintését kéri, kattintson a **Nem**gombra.
1. Győződjön meg arról, hogy az új házirend a lista tetején, hogy a házirend engedélyezve van, és hogy hozzáférést biztosít.

   ![A házirend áthelyezése a lista elejére](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Konfiguráció ellenőrzése

A konfiguráció ellenőrzéséhez be kell jelentkeznie a Távoli asztali átjáróba egy megfelelő RDP-ügyféllel. Ügyeljen arra, hogy olyan fiókot használjon, amelyet a kapcsolatengedélyezési szabályzatok engedélyeznek, és engedélyezve van az Azure MFA számára.

Ahogy az az alábbi képen is látható, használhatja a **Távoli asztali webes elérés** lapot.

![Tesztelés a Távoli asztali webes elérésben](./media/howto-mfa-nps-extension-rdg/image25.png)

Az elsődleges hitelesítéshez szükséges hitelesítő adatok sikeres megadása után a Távoli asztal csatlakoztatása párbeszédpanelen megjelenik a távoli kapcsolat megnyitása, az alábbiak szerint. 

Ha sikeresen hitelesíti a másodlagos hitelesítési módszert, amelyet korábban konfigurált az Azure MFA-ban, akkor csatlakozik az erőforráshoz. Ha azonban a másodlagos hitelesítés sikertelen, a rendszer megtagadja az erőforráshoz való hozzáférést. 

![Távoli asztali kapcsolat távoli kapcsolat létrehozása](./media/howto-mfa-nps-extension-rdg/image26.png)

Az alábbi példában a Windows phone-telefon Hitelesítő alkalmazása biztosítja a másodlagos hitelesítést.

![Példa az ellenőrzést megjelenítő Windows Phone Hitelesítő alkalmazásra](./media/howto-mfa-nps-extension-rdg/image27.png)

Miután sikeresen hitelesítette magát a másodlagos hitelesítési módszerrel, a szokásos módon jelentkezik be a Távoli asztali átjáróba. Mivel azonban egy mobilalkalmazás használatával másodlagos hitelesítési módszert kell használnia egy megbízható eszközön, a bejelentkezési folyamat biztonságosabb, mint egyébként lenne.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Az Eseménynapló naplóinak megtekintése a sikeres bejelentkezési eseményekhez

A Windows Eseménynapló naplóiban a sikeres bejelentkezési események megtekintéséhez a következő Windows PowerShell-parancsot adhatki a Windows terminálszolgáltatások és a Windows biztonsági naplók lekérdezéséhez.

Ha sikeres bejelentkezési eseményeket szeretne lekérdezni az átjáró működési naplóiban _(Eseménynapló\Alkalmazások és szolgáltatások naplói\Microsoft\Windows\TerminalServices-Gateway\Operational),_ használja a következő PowerShell-parancsokat:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Ez a parancs azokat a Windows-eseményeket jeleníti meg, amelyek azt mutatják, hogy a felhasználó megfelelt az erőforrás-engedélyezési házirend követelményeinek, és hozzáférést kapott.

![Események megtekintése a PowerShell használatával](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Ez a parancs azokat az eseményeket jeleníti meg, amelyek azt mutatják, hogy a felhasználó mikor felelt meg a kapcsolatengedélyezési házirend követelményeinek.

![a kapcsolatengedélyezési házirend megtekintése a PowerShell használatával](./media/howto-mfa-nps-extension-rdg/image29.png)

Ezt a naplót megtekintheti, és szűrheti a 300-as és 200-as eseményazonosítókat. A biztonsági eseménynaplóban lévő sikeres bejelentkezési események lekérdezéséhez használja a következő parancsot:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Ez a parancs a központi hálózati kiszolgálón vagy a Távoli asztali átjárókiszolgálón futtatható.

![Sikeres bejelentkezési események mintája](./media/howto-mfa-nps-extension-rdg/image30.png)

A biztonsági napló vagy a Hálózati házirend és hozzáférés-szolgáltatások egyéni nézetét is megtekintheti az alábbi ábrán látható módon:

![Hálózati házirend- és hozzáférés-szolgáltatások eseménynaplója](./media/howto-mfa-nps-extension-rdg/image31.png)

Azon a kiszolgálón, ahol telepítette az Azure MFA nps-bővítményét, az _Alkalmazás- és szolgáltatásnaplók\Microsoft\AzureMfa_mappában megtalálhatja a bővítményre jellemző Eseménynaplókat.

![Eseménynapló AuthZ alkalmazásnaplók](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Útmutató – problémamegoldás

Ha a konfiguráció nem a várt módon működik, az első hibaelhárítási lehetőség az, hogy ellenőrizze, hogy a felhasználó konfigurálva van-e az Azure MFA használatára. A felhasználó csatlakozzon az [Azure Portalhoz.](https://portal.azure.com) Ha a felhasználók másodlagos ellenőrzésre kérnek, és sikeresen hitelesíthetik magukat, megszüntetheti az Azure MFA helytelen konfigurációját.

Ha az Azure MFA a felhasználó(k) számára dolgozik, tekintse át a megfelelő eseménynaplókat. Ezek közé tartozik a biztonsági esemény, átjáró működési és az Azure MFA-naplók, amelyek az előző szakaszban tárgyalt.

Az alábbiakban egy példa a biztonsági napló kimenete mutatja a sikertelen bejelentkezési esemény (Event ID 6273).

![Példa sikertelen bejelentkezési eseményre](./media/howto-mfa-nps-extension-rdg/image33.png)

Az alábbiakban egy kapcsolódó esemény az AzureMFA-naplók:

![Minta Azure MFA-napló az Eseménynaplóban](./media/howto-mfa-nps-extension-rdg/image34.png)

A speciális hibaelhárítási beállítások végrehajtásához tanulmányozza a hálózati kiszolgáló adatbázisformátumú naplófájljait, ahol a hálózati kiszolgáló szolgáltatás telepítve van. Ezek a naplófájlok _a %SystemRoot%\System32\Logs_ mappában vesszővel tagolt szövegfájlokként jönnek létre.

A naplófájlok leírását a [NPS adatbázis-formátumú naplófájlok értelmezése (Interpret NPS Database Format Log Files) (A rendszer naplófájljainak értelmezése) (NPS-adatbázis-formátumú naplófájlok) (NPS-adatbázis-formátumú napló](https://technet.microsoft.com/library/cc771748.aspx) A naplófájlok bejegyzései nehezen értelmezhetők anélkül, hogy táblázatba vagy adatbázisba importálnák őket. Számos IAS-elemző t találhat az interneten, amelyek segítenek a naplófájlok értelmezésében.

Az alábbi képen látható a kimenet egy ilyen letölthető [shareware alkalmazás](https://www.deepsoftware.com/iasviewer).

![Shareware-mintaalkalmazás IAS-elemző](./media/howto-mfa-nps-extension-rdg/image35.png)

Végül további hibaelhárítási lehetőségekhez használhat protokollanalizátort, például [microsoft üzenetelemzőt.](https://technet.microsoft.com/library/jj649776.aspx)

A Microsoft Message Analyzer alábbi képe a **CONTOSO\AliceC**felhasználónevet tartalmazó RADIUS protokollon szűrt hálózati forgalmat mutatja.

![A Szűrt forgalmat megjelenítő Microsoft Message Analyzer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>További lépések

[Az Azure Multi-Factor Authentication beszerzése](concept-mfa-licensing.md)

[Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](howto-mfaserver-nps-rdg.md)

[A helyszíni címtárak integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
