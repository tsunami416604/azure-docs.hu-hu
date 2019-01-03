---
title: Távoli asztali átjáró integráció az Azure MFA NPS-bővítményének |} A Microsoft Docs
description: A távoli asztali átjáró infrastruktúra integrálása az Azure MFA használata a hálózati házirend-kiszolgáló bővítmény a Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: db7591c98147a5728486843efb0c807b6094557a
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53601474"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>A távoli asztali átjáró-infrastruktúra használata a hálózati házirend-kiszolgáló (NPS) bővítmény és az Azure AD integrálása

Ez a cikk részletes adatokat biztosít a távoli asztali átjáró infrastruktúra integrálása az Azure multi-factor Authentication (MFA) a hálózati házirend-kiszolgáló (NPS) bővítmény használata a Microsoft Azure-hoz.

A hálózati házirend-kiszolgáló (NPS) kiterjesztése az Azure lehetővé teszi a felhasználóknak a védelme érdekében távoli Authentication Dial-In User Service (RADIUS) ügyfél-hitelesítés az Azure a felhőalapú [multi-factor Authentication (MFA)](multi-factor-authentication.md). Ez a megoldás második biztonsági réteget ad hozzá felhasználói bejelentkezéseket és tranzakciókat kétlépéses ellenőrzést biztosít.

Ebben a cikkben részletes útmutatást nyújt a hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure MFA számára a hálózati házirend-bővítmény használata az Azure-hoz. Ez lehetővé teszi a távoli asztali átjáró bejelentkezni próbáló felhasználók biztonságos ellenőrzése.

> [!NOTE]
> Ez a cikk nem használható az MFA-kiszolgáló központi, és csak az Azure MFA (felhőalapú) központi használható.

A hálózati házirend- és hozzáférés-szolgáltatások (NPS) révén a szervezetek a következőket teszi:

* Adja meg a központi felügyeleti helyek és a vezérlő megadásával, akik csatlakozhatnak a hálózati kérelmek, milyen alkalommal nap-kapcsolatok engedélyezettek, a kapcsolatok időtartama, és a biztonság, az ügyfelek kell kapcsolódni, és így tovább. Ahelyett, hogy ezek a szabályzatok megadása minden VPN- vagy távoli asztali (RD) átjáró kiszolgálón, ezek a szabályzatok egyszer adható meg egy központi helyen. A RADIUS protokollal biztosít a központosított hitelesítési, engedélyezési és nyilvántartási (AAA). 
* Hozzon létre, és érvényesíti a hálózatvédelem (NAP) ügyfél állapotházirendeket, amelyek meghatározzák, hogy eszközök, amelyekhez hozzáférést korlátozás nélküli vagy korlátozott a hálózati erőforrásokhoz.
* Adja meg a hitelesítési és engedélyezési 802.1 hozzáférés kényszerítésére azt jelenti, hogy x-kompatibilis vezeték nélküli hozzáférési pontok és Ethernet-kapcsolók.

NPS RADIUS-egyszerűbbé tétele és a VPN-kezelésének központosítása szervezetek használata általában a szabályzatok. Azonban számos szervezet is használhatja az NPS egyszerűbbé tétele és a távoli asztali asztali kapcsolatengedélyezési házirendek (RD CAPs) kezelésének központosítása.

Szervezetek is integrálhatja az Azure MFA megfelelőségi magas szintű biztonság növelése és a hálózati házirend-kiszolgáló. Ezzel biztosíthatja, hogy a felhasználók kétlépéses ellenőrzést, jelentkezzen be a távoli asztali átjáró létrehozásához. A felhasználók számára hozzáférést biztosítani a felhasználónév/jelszó kombináció, amely a felhasználó rendelkezik a hatókörükön információkkal együtt kell adnia. Ezeket az információkat megbízható legyen, és nem nehezen másolható, például egy mobiltelefonszámot, a mobiltelefonra száma, az alkalmazás egy mobileszközön, és így tovább. Támogatott hitelesítési módszerekkel kapcsolatos további információk: a szakasz [állapítsa meg, milyen hitelesítési módszerek használatát](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Az NPS-bővítményt, az Azure-ban rendelkezésre állását, előtt a felhasználókat a kétlépéses ellenőrzést, az integrált hálózati házirend-kiszolgáló és az Azure MFA-környezetek megvalósításához szükséges kellett konfigurálnia és karbantartania egy külön MFA-kiszolgáló a helyszíni környezetben leírtakszerint[ Távoli asztali átjáró és az Azure multi-factor Authentication kiszolgáló RADIUS-t használó](howto-mfaserver-nps-rdg.md).

Az NPS-bővítményt, az Azure-ban rendelkezésre állásának most vállalatok számára teszi lehetővé a kiválasztott vagy egy helyszíni MFA-megoldást, vagy egy MFA felhőalapú megoldás üzembe biztonságos RADIUS-ügyfél-hitelesítés.

## <a name="authentication-flow"></a>A hitelesítési folyamatból

Távoli asztali átjárón keresztül a hálózati erőforrásokhoz való hozzáférést nyerni korlátozott felhasználók számára egy távoli asztali kapcsolat engedélyezési házirendje (RD CAP) és a egy távoli asztali erőforrás engedélyezési házirendje (RD RAP) a megadott feltételeknek kell megfelelniük. A távoli asztali CAPs adja meg, ki jogosult csatlakozni a távoli asztali átjáró. RD RAP-k adja meg a hálózati erőforrások, például a távoli asztali vagy távoli alkalmazások esetében, amelyet a felhasználó számára az RD-átjárón keresztül. 

Az RD átjáró központi házirend áruházbeli használandó távoli asztali CAPs konfigurálható. RD RAP-k nem használhatja egy központi házirend feldolgozása az RD-átjárón. RD-átjárón konfigurált központi házirend áruházbeli használandó távoli asztali CAPs egyik példája egy RADIUS-ügyfél egy másik hálózati házirend-kiszolgálóra, amely a központi házirend tárolóként szolgál.

Ha az NPS-bővítményt, az Azure-ban a hálózati házirend-kiszolgáló és a távoli asztali átjáró integrálva van, a sikeres hitelesítési folyamat következőképpen történik:

1. A távoli asztali átjáró kiszolgálójának szeretne csatlakozni egy erőforrást, például a távoli asztali munkamenetet a távoli asztali felhasználók hitelesítési kérést kap. RADIUS-ügyfélként működő, a távoli asztali átjáró kiszolgálójának RADIUS Access-kérelem üzenet konvertálja a kérést, és a üzenetet küld a RADIUS (NPS) kiszolgálóhoz, amelyen telepítve van-e az NPS-bővítményének.
1. A felhasználónév és jelszó kombinációjával ellenőrzése az Active Directoryban, és a felhasználó hitelesítését.
1. Ha a hálózati házirend-kiszolgáló kapcsolódási kérelmet, és a hálózati házirendek megadott feltételek mindegyike teljesül (például időpont vagy csoport tagsági korlátozások), az NPS-bővítményének elindítja az Azure MFA másodlagos hitelesítési kérést.
1. Az Azure MFA kommunikál az Azure ad-vel, a felhasználó adatait kérdezi le, és végrehajtja a másodlagos hitelesítést támogatott módszerrel.
1. Követően sikeres, az MFA-hitelesítést az Azure MFA kommunikál az NPS-bővítményt az eredményt.
1. A hálózati házirend-kiszolgáló, amelyen a bővítményt telepíti, a távoli asztali átjáró kiszolgálójának az RD CAP házirend RADIUS Access-Accept üzenetet küld.
1. A felhasználó hozzáférést kap a kért hálózati erőforrás az RD átjárón keresztül.

## <a name="prerequisites"></a>Előfeltételek

Ez a szakasz részletesen előtt az Azure MFA integrálása a távoli asztali átjáró szükséges előfeltételeket. Mielőtt elkezdené, az alábbi előfeltételek teljesülnek kell rendelkeznie.  

* Távoli asztali szolgáltatások (RDS) infrastruktúra
* Az Azure MFA-licenc
* A Windows Servert
* Hálózati házirend- és hozzáférés-szolgáltatások (NPS) szerepkör
* Az Azure Active Directory a helyszíni Active Directoryval szinkronizált
* Azure Active Directory GUID ID

### <a name="remote-desktop-services-rds-infrastructure"></a>Távoli asztali szolgáltatások (RDS) infrastruktúra

Rendelkeznie kell egy működő infrastruktúra a távoli asztali szolgáltatások (RDS) helyen. Ha nem, majd gyorsan létrehozhat az infrastruktúra az Azure-ban az alábbi gyors üzembe helyezési sablon használatával: [Távoli asztali munkamenet-gyűjteményhez. üzemelő példány létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment). 

Ha szeretné manuálisan létrehozni egy helyszíni távoli asztali szolgáltatások infrastruktúrát gyorsan tesztelésre, lépésekkel üzembe helyez egyet. 
**További**: [A távoli asztali szolgáltatások telepítése az Azure gyors üzembe helyezés](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) és [alapszintű RDS-infrastruktúra telepítése](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure). 

### <a name="azure-mfa-license"></a>Az Azure MFA-licenc

Required (kötelező) a licenc az Azure MFA-hoz, elérhető prémium szintű Azure AD vagy az egyéb csomagok, amelyek tartalmazzák azt. Fogyasztásalapú licencek az Azure MFA-hoz, például felhasználói megfelelően vagy hitelesítési licencekkel, amelyek nem kompatibilisek az NPS-bővítményt. További információkért lásd: [beszerzése az Azure multi-factor Authentication](concept-mfa-licensing.md). Tesztelési célokra használhatja egy próba-előfizetést.

### <a name="windows-server-software"></a>A Windows Servert

Az NPS-bővítményének megköveteli a Windows Server 2008 R2 SP1 vagy újabb verzió és az NPS szerepkör-szolgáltatás telepítve van. Ebben a szakaszban található összes lépést a Windows Server 2016 használatával lettek végrehajtva.

### <a name="network-policy-and-access-services-nps-role"></a>Hálózati házirend- és hozzáférés-szolgáltatások (NPS) szerepkör

A hálózati házirend-kiszolgáló szerepkör-szolgáltatást biztosít a RADIUS-kiszolgáló és az ügyfél funkcióit, valamint a hálózati házirend-Állapotfigyelő szolgáltatás. Ez a szerepkör az infrastruktúra legalább két számítógépre kell telepíteni: A távoli asztali átjáró és a egy másik tagkiszolgáló vagy tartományvezérlő. Alapértelmezés szerint a szerepkör már létezik a számítógépen, a távoli asztali átjáró konfigurálva.  Emellett telepítenie kell az NPS szerepkör a legalább egy másik számítógépre, például egy tartományvezérlő vagy tagkiszolgáló.

Az NPS szerepkör telepítéséről további információt a Windows Server 2012 vagy régebbi szolgáltatás, lásd: [egy NAP állapotházirend-kiszolgálókon telepítse](https://technet.microsoft.com/library/dd296890.aspx). Ajánlott eljárások a hálózati házirend-kiszolgáló, beleértve azt javasoljuk, hogy a hálózati házirend-kiszolgáló telepítése egy tartományvezérlőn leírását lásd: [ajánlott eljárások a hálózati házirend-kiszolgáló](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Az Azure Active Directory a helyszíni Active Directoryval szinkronizált

Az NPS-bővítményének használatához a helyszíni felhasználók lehet az Azure ad-vel szinkronizált, és engedélyezve van az MFA-hoz. Ez a szakasz azt feltételezi, hogy a helyszíni felhasználók vannak szinkronizálva az Azure ad-vel AD Connect használatával. Az Azure AD connect, információt: [a helyszíni címtárak integrálása az Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID ID

Az NPS-bővítményének telepítése, ismernie kell a GUID azonosítóját az Azure ad-ben. Keresés, a GUID azonosítóját az Azure AD útmutatást az alábbiakban találhatók.

## <a name="configure-multi-factor-authentication"></a>A multi-factor Authentication szolgáltatás konfigurálása 

Ez a szakasz ismerteti az Azure MFA integrálása a távoli asztali átjáró. A rendszergazdák konfigurálnia kell az Azure MFA szolgáltatás segítségével a felhasználók saját magukat a multi-factor Authentication eszközök vagy alkalmazások.

Kövesse a [Ismerkedés az Azure multi-factor Authentication a felhőben](howto-mfa-getstarted.md) MFA engedélyezése az Azure AD-felhasználók számára.

### <a name="configure-accounts-for-two-step-verification"></a>A kétlépéses ellenőrzéshez fiókok beállítása

Ha a multi-factor Authentication-fiók engedélyezve van, nem jelentkezhet be többtényezős hitelesítési szabályzat szabályozzák, amíg sikeresen konfigurálta a megbízható eszköz használata a hitelesítés második tényezőjét, és elvégezte a hitelesítést használni a kétlépéses ellenőrzést erőforrások.

Kövesse a [Mi az Azure multi-factor Authentication jelent a számomra?](../user-help/multi-factor-authentication-end-user.md) megértéséhez, és megfelelő konfigurálásához a felhasználói fiókjához a multi-factor Authentication az eszközöket.

## <a name="install-and-configure-nps-extension"></a>Telepítse és konfigurálja az NPS-bővítményének

Ez a szakasz útmutatást nyújt a távoli asztali szolgáltatások-infrastruktúra konfigurálása az Azure MFA használatát az ügyfél-hitelesítéshez és a távoli asztali átjáró.

### <a name="acquire-azure-active-directory-guid-id"></a>Az Azure Active Directory GUID-azonosító beszerzése

Az NPS-bővítményének konfigurációjának részeként meg kell megadnia az Azure AD-bérlő rendszergazdai hitelesítő adatokat és az Azure AD-azonosítója. A következő lépések bemutatják, hogyan tehet szert a bérlő azonosítója.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure-bérlő globális rendszergazdájaként.
1. A bal oldali navigációs sávján válassza ki a **Azure Active Directory** ikonra.
1. Válassza ki **tulajdonságok**.
1. A címtár-azonosító mellett a Tulajdonságok panelen kattintson a **másolási** ikonra, ahogy az alábbi, másolja vágólapra az Azonosítót a.

 ![Tulajdonságok](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>Az NPS-bővítményének telepítése

Telepítse az NPS-bővítményt egy kiszolgálóra, amelyen telepítve van a hálózati házirend- és hozzáférés-szolgáltatások (NPS) szerepkör. Ez a kialakítás a RADIUS-kiszolgáló működik.

>[!Important]
> Győződjön meg arról, hogy nem telepíti a távoli asztali átjáró kiszolgálón az NPS-bővítményt.
>

1. Töltse le a [NPS-bővítményének](https://aka.ms/npsmfa). 
1. Másolja a végrehajtható fájl (NpsExtnForAzureMfaInstaller.exe) a hálózati házirend-kiszolgálóra.
1. A hálózati házirend-kiszolgáló a duplán **NpsExtnForAzureMfaInstaller.exe**. Ha a rendszer kéri, kattintson a **futtatása**.
1. A hálózati házirend-kiszolgáló kiterjesztése az Azure MFA beállítása párbeszédpanelen tekintse át a szoftverlicenc-feltételeket, ellenőrizze **elfogadom a szerződés és feltételek**, és kattintson a **telepítése**.

  ![Az Azure MFA beállítása](./media/howto-mfa-nps-extension-rdg/image2.png)

1. A hálózati házirend-kiszolgáló kiterjesztése az Azure MFA beállítása párbeszédpanelen kattintson a **Bezárás**.

  ![Az Azure MFA NPS-bővítmény](./media/howto-mfa-nps-extension-rdg/image3.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Az NPS-bővítményt egy PowerShell-szkripttel használt tanúsítványok konfigurálása

Ezután meg kell az NPS-bővítményének annak biztosítása érdekében a biztonságos kommunikációt és az ellenőrzés által használt tanúsítványok konfigurálása. A hálózati házirend-kiszolgáló-összetevők közé tartozik egy Windows PowerShell-parancsprogram, amely beállítja a hálózati házirend-kiszolgáló egy önaláírt tanúsítványt.

A szkript a következő műveleteket hajtja végre:

* Létrehoz egy önaláírt tanúsítványt
* Társítja az Azure AD egyszerű szolgáltatás a tanúsítvány nyilvános kulcsa
* Tárolja a tanúsítvány a helyi számítógép tárolójában
* A tanúsítvány titkos kulcsa a hálózati felhasználó számára a hozzáférést
* Hálózati házirend-kiszolgáló szolgáltatás újraindítása

Ha azt szeretné, a saját tanúsítványok, szeretne társítani az egyszerű szolgáltatás a tanúsítvány nyilvános kulcsát az Azure ad-ben, és így tovább.

A szkript használatához adja meg a bővítmény az Azure AD rendszergazdai hitelesítő adatok és az Azure AD-bérlő azonosítója, amelyet korábban vágólapra másolt. Futtassa a szkriptet minden egyes hálózati házirend-kiszolgálón, amelyre telepítve van az NPS-bővítményt. Ezután tegye a következőket:

1. Nyisson meg egy rendszergazda Windows PowerShell-parancssorban.
1. A PowerShell-parancssorba írja be a `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`, és nyomja le az **ENTER**.
1. Típus `.\AzureMfaNpsExtnConfigSetup.ps1`, és nyomja le az **ENTER**. A parancsfájl ellenőrzi, hogy ha az Azure Active Directory PowerShell-modul telepítve van-e. Ha nincs telepítve, a parancsfájl telepíti a modult.

  ![Az Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Miután a parancsfájl ellenőrzi a PowerShell-modul telepítését, az Azure Active Directory PowerShell modul párbeszédpanel jeleníti meg. A párbeszédpanelen adja meg az Azure AD rendszergazdai hitelesítő adatait és a jelszót, és kattintson a **bejelentkezés**.

  ![Nyissa meg a Powershell-fiók](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Amikor a rendszer kéri, illessze be a bérlő Azonosítóját, korábban kimásolt a vágólapra, és nyomja le az **ENTER**.

  ![Adja meg a bérlő azonosítója](./media/howto-mfa-nps-extension-rdg/image6.png)

1. A szkript létrehoz egy önaláírt tanúsítványt, és más konfigurációs módosításokat hajt végre. A kimenet az alábbi képhez hasonlóan kell lennie.

  ![Önaláírt tanúsítvány](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>A távoli asztali átjáró NPS összetevők konfigurálása

Ebben a szakaszban konfigurálja a távoli asztali átjáró kapcsolatengedélyezési házirendek és más RADIUS-beállításokat.

A hitelesítési folyamat megköveteli, hogy a RADIUS-üzenetek között a távoli asztali átjáró és a hálózati házirend-kiszolgáló, ahol a hálózati házirend-kiszolgáló telepítve van-e kell cserélni. Ez azt jelenti, hogy konfigurálnia kell a RADIUS-ügyfélbeállításokat a távoli asztali átjáró és a hálózati házirend-kiszolgáló, amelyen telepítve van-e az NPS-bővítményének is.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurálja a távoli asztali átjáró kapcsolatengedélyezési házirendek központi tároló használata

A távoli asztali kapcsolat engedélyezési házirendek (RD CAPs) adja meg a távoli asztali átjáró kiszolgálóhoz való kapcsolódás követelményeinek. A távoli asztali CAPs helyben kell tárolni (alapértelmezett) kell tárolni, vagy egy központi, NPS-t futtató RD CAP tárolóban. Az Azure MFA-integráció konfigurálása a távoli asztali szolgáltatások, adjon meg egy központi tárolóban használatát kell.

1. Nyissa meg az RD átjárókiszolgáló **Kiszolgálókezelő**.
1. Kattintson a menü **eszközök**, mutasson a **távoli asztali szolgáltatások**, és kattintson a **távoli asztali átjárókezelő**.

  ![Távoli asztali szolgáltatások](./media/howto-mfa-nps-extension-rdg/image8.png)

1. A távoli asztali átjáró Managerben, kattintson a jobb gombbal  **\[kiszolgálónév\] (helyi)**, és kattintson a **tulajdonságok**.

  ![Kiszolgáló neve](./media/howto-mfa-nps-extension-rdg/image9.png)

1. A Tulajdonságok párbeszédpanelen válassza ki a **RD CAP Store** fülre.
1. Válassza ki az RD CAP Store lapon **NPS-t futtató központi kiszolgáló**. 
1. Az a **adja meg egy nevet vagy IP-címet a hálózati házirend-kiszolgáló** mezőbe írja be a kiszolgáló, amelyre telepítve van az NPS-bővítményének IP-cím vagy a kiszolgáló nevét.

  ![Adja meg a nevét vagy IP-cím](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Kattintson a **Hozzáadás** parancsra.
1. Az a **közös titkos kulcsot** párbeszédpanelen adja meg a közös titkos kulcsot, és kattintson **OK**. Győződjön meg arról, jegyezze fel a közös titkos kulcsot, és tárolja biztonságos helyen a rekordot.

 >[!NOTE]
 >Közös titkos kulcsot a RADIUS-kiszolgálók és ügyfelek közötti megbízhatósági kapcsolat létesítésére szolgál. A hosszú és összetett titkos kulcs létrehozása.
 >

 ![Közös titok](./media/howto-mfa-nps-extension-rdg/image11.png)

1. A párbeszédpanel bezárásához kattintson az **OK** gombra.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>A távoli asztali átjáró NPS RADIUS időkorlátja konfigurálása

Biztosítják, hogy a felhasználók hitelesítő adatainak ellenőrzésére, kétlépéses hitelesítés végrehajtására, válaszokat kaphatnak és válaszolni RADIUS-üzeneteket, a RADIUS-időtúllépési érték beállításához szükséges időt.

1. A távoli asztali átjáró kiszolgálón nyissa meg a Kiszolgálókezelőt. Kattintson a menü **eszközök**, és kattintson a **hálózati házirend-kiszolgáló**. 
1. Az a **hálózati házirend-kiszolgáló (helyi)** konzolról, bontsa ki a **RADIUS-ügyfelek és kiszolgálók**, és válassza ki **távoli RADIUS-kiszolgáló**.

 ![Távoli RADIUS-kiszolgáló](./media/howto-mfa-nps-extension-rdg/image12.png)

1. A részleteket tartalmazó ablaktáblán kattintson duplán a **TS GATEWAY SERVER GROUP**.

 >[!NOTE]
 >A RADIUS-kiszolgálócsoport konfigurálta az NPS-házirendek központi kiszolgáló hozott létre. A távoli asztali átjáró RADIUS üzeneteket ehhez a kiszolgálóhoz vagy a kiszolgálók, a csoport továbbít, ha egynél több, a csoportban.
 >

1. Az a **TS ÁTJÁRÓ kiszolgáló csoport tulajdonságai** párbeszédpanelen jelölje ki az IP-cím vagy a távoli asztali CAPs tárolja, és kattintson a konfigurált hálózati házirend-kiszolgáló neve **szerkesztése**. 

 ![TS Gateway Server Group](./media/howto-mfa-nps-extension-rdg/image13.png)

1. Az a **RADIUS-kiszolgáló szerkesztése** párbeszédpanelen válassza ki a **terheléselosztás** fülre.
1. Az a **terheléselosztás** lap a **előtt válasz nélküli másodpercek száma** mezőben módosítsa az alapértelmezett érték a 3., 30 és 60 másodperc közötti értékre.
1. Az a **kiszolgáló nyilvánítása kérelmek között eltelt másodpercek száma** mezőbe, majd az alapértelmezett érték 30 másodperc egy értéket, amely egyenlő vagy nagyobb, mint az előző lépésben megadott értéket.

 ![Radius-kiszolgáló szerkesztése](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Kattintson a **OK** kétszer a párbeszédpanelek bezárásához.

### <a name="verify-connection-request-policies"></a>Ellenőrizze a kapcsolatkérelem-házirendek

Alapértelmezés szerint egy központi házirend store használandó kapcsolat engedélyezési házirendek, a távoli asztali átjáró konfigurálásakor a távoli asztali átjáró van konfigurálva a hálózati házirend-kiszolgáló CAP kérelmeket továbbítja. Az Azure MFA-bővítménnyel, telepítve van, a hálózati házirend-kiszolgáló a RADIUS hozzáférési kérés dolgozza fel. A következő lépések bemutatják, hogyan lehet ellenőrizni az alapértelmezett házirendet.

1. Az RD-átjárón a hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki a **házirendek**, és válassza ki **kapcsolatkérelem-házirendek**.
1. Kattintson duplán a **TS GATEWAY AUTHORIZATION POLICY**.
1. Az a **TS GATEWAY AUTHORIZATION POLICY tulajdonságok** párbeszédpanelen kattintson a **beállítások** fülre.
1. A **beállítások** lapon, a kérelem továbbítása a kapcsolatot, kattintson a **hitelesítési**. RADIUS-ügyfél hitelesítési kérelmek előre van konfigurálva.

 ![Hitelesítési beállítások](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Kattintson a **Mégse**.

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Az NPS konfigurálásához a kiszolgálón, amelyen telepítve van-e az NPS bővítményével

A hálózati házirend-kiszolgáló, amelyen telepítve van-e az NPS-bővítményének képesnek kell lennie, a hálózati házirend-kiszolgáló a távoli asztali átjáró RADIUS üzeneteket. Ahhoz, hogy ez az üzenet exchange, kell konfigurálni a hálózati házirend-kiszolgáló-összetevők a kiszolgálón, ahol a hálózati házirend-kiszolgáló bővítmény szolgáltatás telepítve van.

### <a name="register-server-in-active-directory"></a>Regisztrálja a kiszolgálót az Active Directoryban

Ebben a forgatókönyvben megfelelő működéséhez, az NPS-kiszolgáló regisztrálva kell lennie az Active Directoryban.

1. Nyissa meg a hálózati házirend-kiszolgáló **Kiszolgálókezelő**.
1. A Kiszolgálókezelőben kattintson a **eszközök**, és kattintson a **hálózati házirend-kiszolgáló**.
1. A hálózati házirend-kiszolgáló-konzolon kattintson a jobb gombbal **hálózati házirend-kiszolgáló (helyi)**, és kattintson a **kiszolgáló regisztrálása az Active Directoryban**.
1. Kattintson a **OK** kétszer.

 ![Regisztrálja a kiszolgálót az ad-ben](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Hagyja nyitva a következő eljárással a konzolon.

### <a name="create-and-configure-radius-client"></a>Hozzon létre, és a RADIUS-ügyfél konfigurálása

A hálózati házirend-kiszolgáló RADIUS-ügyfélként konfigurálni kell a távoli asztali átjáró.

1. A hálózati házirend-kiszolgálón, ahol a hálózati házirend-bővítmény telepítve van, a a **hálózati házirend-kiszolgáló (helyi)** konzolon kattintson a jobb gombbal a **RADIUS-ügyfelek** kattintson **új**.

 ![Új RADIUS-ügyfelek](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Az a **új RADIUS-ügyfél** párbeszédpanelen adja meg egy rövid nevet, például _átjáró_, és IP-címe vagy DNS-nevét a távoli asztali átjárókiszolgáló. 
1. Az a **közös titkos kulcsot** és a **közös titok megerősítése** mezőknél adja meg a titkos kulcsot, amely a korábban használt.

 ![Nevét és címét](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Kattintson a **OK** az új RADIUS-ügyfél párbeszédpanel bezárásához.

### <a name="configure-network-policy"></a>A hálózati házirend konfigurálása

Ne felejtse el, hogy az Azure MFA-bővítménnyel a hálózati házirend-kiszolgáló-e a kijelölt központi házirend a kapcsolat engedélyezési házirend (CAP). Ezért kell megvalósítani a Tengelysapka a hálózati házirend-kiszolgálón érvényes kapcsolat kérelmek hitelesítéséhez.  

1. A hálózati házirend-kiszolgálón nyissa meg a hálózati házirend-kiszolgáló (helyi) konzolt, bontsa ki a **házirendek**, és kattintson a **hálózati házirendek**.
1. Kattintson a jobb gombbal **más kiszolgálók eléréséhez kapcsolatok**, és kattintson a **házirend duplikálása**.

 ![Ismétlődő házirend](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Kattintson a jobb gombbal **másolási kapcsolatok más kiszolgálók eléréséhez**, és kattintson a **tulajdonságok**.

 ![Hálózati tulajdonságok](./media/howto-mfa-nps-extension-rdg/image20.png)

1. Az a **másolási kapcsolatok más kiszolgálók eléréséhez** párbeszédpanel **házirendnév**, adjon meg egy megfelelő nevet, például _RDG_CAP_. Ellenőrizze **házirenddel**, és válassza ki **hozzáférést**. Szükség esetén a **hálózat-hozzáférési kiszolgáló típusa**válassza **távoli asztali átjáró**, vagy hagyhatja, **meghatározatlan**.

 ![A kapcsolatok másolása](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Kattintson a **megkötések** fülre, és ellenőrizze **engedélyezése az ügyfelek hitelesítési módszerként nélkül is kapcsolódhatnak**.

 ![Csatlakozás engedélyezése](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Másik lehetőségként kattinthat a **feltételek** lapra, és adja meg a kapcsolat, engedélyt kapjon, ha például egy adott Windows-csoport tagsága szükséges feltételeket.

 ![Feltételek](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Kattintson az **OK** gombra. Amikor a rendszer kéri, a hozzá tartozó súgó-témakör megtekintése, kattintson a **nem**.
1. Győződjön meg arról, hogy az új szabályzat tetején található a listában, hogy a szabályzat engedélyezve van, és hozzáférést biztosít.

 ![Hálózati házirendek](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Konfiguráció ellenőrzése

A konfiguráció ellenőrzéséhez jelentkezzen be a távoli asztali átjáró-megfelelő RDP-ügyfelet kell. Győződjön meg arról, fiók, amely a házirendek által engedélyezett, és engedélyezve van az Azure MFA-kiszolgáló használatára.

Az alábbi képen show, használhatja a **távoli asztali webes elérés** lapot.

![Távoli asztali webes elérés](./media/howto-mfa-nps-extension-rdg/image25.png)

Sikeresen megadta a hitelesítő adatait az elsődleges hitelesítéshez, hogy a távoli asztali kapcsolat párbeszédpanel állapotát jeleníti meg egy távoli kapcsolat kezdeményezése alább látható módon. 

Sikerült a hitelesítést az Azure MFA-ban korábban konfigurált másodlagos hitelesítési módszert, ha csatlakozik az erőforrást. Ha a másodlagos hitelesítés nem sikeres, az erőforráshoz való hozzáférés nem engedélyezett. 

![Távoli kapcsolatot kezdeményezni](./media/howto-mfa-nps-extension-rdg/image26.png)

Az alábbi példában az Authenticator alkalmazás Windows Phone-eszközön szolgál a másodlagos hitelesítő adatokat megadnia.

![Fiókok](./media/howto-mfa-nps-extension-rdg/image27.png)

A hitelesítés sikerült a másodlagos hitelesítési módszerrel, miután bejelentkezett a távoli asztali átjáró szokásos módon. Azonban meg kell használnia egy másodlagos hitelesítési módszer a megbízható eszköz mobilalkalmazások használatát, mert a bejelentkezési folyamat nem biztonságosabb, mint egyébként lenne.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Sikeres bejelentkezési események Eseménynapló naplóinak megtekintése

A sikeres bejelentkezési események megtekintése a Windows-eseménynaplók, adja ki a következő Windows PowerShell-parancsot a Windows Terminálszolgáltatások és a Windows biztonsági naplók lekérdezése.

Sikeres bejelentkezési események az átjáró műveleti naplókban lekérdezéséhez _(esemény eseménynapló\alkalmazás- és szolgáltatások Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_, használja a következő PowerShell-parancsokat:

* _Get-WinEvent - naplónév Microsoft-Windows-TerminalServices-átjáró/Operational_ |}, {$_.ID - eq '300'} |} FL 
* Ez a parancs azt mutatják be, a felhasználó teljesíti az erőforrás-engedélyezési házirend követelményeinek (RD RAP), és hozzáférést Windows-eseményeket jeleníti meg.

![Az Eseménynapló megtekintése](./media/howto-mfa-nps-extension-rdg/image28.png)

* _Get-WinEvent - naplónév Microsoft-Windows-TerminalServices-átjáró/Operational_ |}, {$_.ID - eq "200"} |} FL
* Ez a parancs a felhasználói kapcsolat engedélyezési házirend követelményeinek teljesülése esetén megjelenítő eseményeket jeleníti meg.

![Kapcsolat engedélyezése](./media/howto-mfa-nps-extension-rdg/image29.png)

Ez a napló és szűrő azonosítóval, 300, és 200-as esemény is megtekintheti. A biztonsági eseménynapló sikeres bejelentkezési események lekérdezés, használja a következő parancsot:

* _Get-WinEvent - naplónév biztonsági_ |}, {$_.ID - eq '6272'} |} FL 
* Ez a parancs futtatható a központi NPS-t vagy a távoli asztali átjárókiszolgálón. 

![Sikeres bejelentkezési események](./media/howto-mfa-nps-extension-rdg/image30.png)

Alább látható módon a biztonsági napló vagy a hálózati házirend- és elérési szolgáltatások egyéni nézet is megtekintheti:

![Hálózati házirend- és elérési szolgáltatások](./media/howto-mfa-nps-extension-rdg/image31.png)

A kiszolgálón, az Azure MFA NPS bővítményével amelyen, annak az Eseménynapló az alkalmazásnaplókat a bővítményt, az adott _alkalmazások és szolgáltatások Logs\Microsoft\AzureMfa_. 

![Az Eseménynapló alkalmazást](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Útmutató hibaelhárítása

A konfiguráció nem várt módon működik, ha az első hely hibaelhárítás indítása az győződjön meg arról, hogy a felhasználó az Azure MFA használatára van konfigurálva. A felhasználó csatlakozhat a [az Azure portal](https://portal.azure.com). Ha a felhasználók másodlagos ellenőrzőkulcs a rendszer kéri, és sikeresen be tud hitelesítést, megszüntetheti az Azure MFA nem megfelelő konfiguráció.

Az Azure MFA a felhasználó esetében működik, ha, tekintse át a megfelelő eseménynaplók. Ezek közé tartozik a biztonsági esemény, az átjáró működési és az előző szakaszban tárgyalt Azure MFA-naplókat. 

Alább a biztonsági napló a sikertelen bejelentkezési esemény (Event ID 6273) bemutató példa kimenet.

![Sikertelen bejelentkezési esemény](./media/howto-mfa-nps-extension-rdg/image33.png)

Alább az AzureMFA naplók a kapcsolódó esemény:

![Az Azure MFA-log](./media/howto-mfa-nps-extension-rdg/image34.png)

Hajtsa végre a Speciális beállítások hibaelhárítása, tekintse meg a hálózati házirend-kiszolgáló adatbázis formátum naplófájlokat ahol a hálózati házirend-kiszolgáló szolgáltatás telepítve van. Ezek a naplófájlok létrejönnek _%SystemRoot%\System32\Logs_ vesszővel tagolt szöveges fájlok mappába. 

Ezek leírását naplófájljainak listájáért lásd [értelmezése NPS naplófájlok](https://technet.microsoft.com/library/cc771748.aspx). Ezekben a naplófájlokban lévő bejegyzéseket nehéz értelmezni anélkül, hogy importálná őket egy külön táblázatban vagy az adatbázis is lehet. Több IAS elemzők online megtalálhatja a segítséget nyújtanak a naplófájlok értelmezése. 

Az alábbi képen látható a kimenet egy ilyen letölthető [shareware alkalmazás](https://www.deepsoftware.com/iasviewer). 

![Shareware alkalmazás](./media/howto-mfa-nps-extension-rdg/image35.png)

Végül, a további beállítások hibaelhárítása, protokollelemző, használja az ilyen [Microsoft Message Analyzert](https://technet.microsoft.com/library/jj649776.aspx). 

Microsoft Message Analyzert az alábbi ábrán látható hálózati forgalmat, amely tartalmazza a felhasználó nevét a RADIUS protokollal a szűrt **CONTOSO\AliceC**.

![A Microsoft Message Analyzer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>További lépések

[Az Azure Multi-Factor Authentication beszerzése](concept-mfa-licensing.md)

[Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](howto-mfaserver-nps-rdg.md)

[A helyszíni címtárak integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
