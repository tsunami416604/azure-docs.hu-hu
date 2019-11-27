---
title: A RDG integrálása az Azure MFA NPS-bővítményrel – Azure Active Directory
description: A távoli asztali átjáró infrastruktúra integrálása az Azure MFA használata a hálózati házirend-kiszolgáló bővítmény a Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ec402cf2c741d88d230e5734485bf9eb0dd1b03
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381814"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>A távoli asztali átjáró-infrastruktúra használata a hálózati házirend-kiszolgáló (NPS) bővítmény és az Azure AD integrálása

Ez a cikk részletes adatokat biztosít a távoli asztali átjáró infrastruktúra integrálása az Azure multi-factor Authentication (MFA) a hálózati házirend-kiszolgáló (NPS) bővítmény használata a Microsoft Azure-hoz.

Az Azure-hoz készült hálózati házirend-kiszolgáló (NPS) bővítmény lehetővé teszi, hogy az ügyfelek az Azure felhőalapú [multi-Factor Authentication (MFA)](multi-factor-authentication.md)használatával megóvják Remote Authentication Dial-In User Service (RADIUS) ügyfél-hitelesítést. Ez a megoldás második biztonsági réteget ad hozzá felhasználói bejelentkezéseket és tranzakciókat kétlépéses ellenőrzést biztosít.

Ebben a cikkben részletes útmutatást nyújt a hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure MFA számára a hálózati házirend-bővítmény használata az Azure-hoz. Ez lehetővé teszi a távoli asztali átjáró bejelentkezni próbáló felhasználók biztonságos ellenőrzése.

> [!NOTE]
> Ez a cikk nem használható az MFA-kiszolgálók üzembe helyezéséhez, és csak az Azure MFA (felhőalapú) környezetekben használható.

A hálózati házirend- és hozzáférés-szolgáltatások (NPS) révén a szervezetek a következőket teszi:

* Adja meg a központi felügyeleti helyek és a vezérlő megadásával, akik csatlakozhatnak a hálózati kérelmek, milyen alkalommal nap-kapcsolatok engedélyezettek, a kapcsolatok időtartama, és a biztonság, az ügyfelek kell kapcsolódni, és így tovább. Ahelyett, hogy ezek a szabályzatok megadása minden VPN- vagy távoli asztali (RD) átjáró kiszolgálón, ezek a szabályzatok egyszer adható meg egy központi helyen. A RADIUS protokollal biztosít a központosított hitelesítési, engedélyezési és nyilvántartási (AAA).
* Hozzon létre, és érvényesíti a hálózatvédelem (NAP) ügyfél állapotházirendeket, amelyek meghatározzák, hogy eszközök, amelyekhez hozzáférést korlátozás nélküli vagy korlátozott a hálózati erőforrásokhoz.
* Adja meg a hitelesítési és engedélyezési 802.1 hozzáférés kényszerítésére azt jelenti, hogy x-kompatibilis vezeték nélküli hozzáférési pontok és Ethernet-kapcsolók.

A szervezetek általában a hálózati házirend-kiszolgáló (RADIUS) használatával egyszerűsítik és központosítják a VPN-házirendek kezelését. Azonban számos szervezet is használhatja az NPS egyszerűbbé tétele és a távoli asztali asztali kapcsolatengedélyezési házirendek (RD CAPs) kezelésének központosítása.

Szervezetek is integrálhatja az Azure MFA megfelelőségi magas szintű biztonság növelése és a hálózati házirend-kiszolgáló. Ezzel biztosíthatja, hogy a felhasználók kétlépéses ellenőrzést, jelentkezzen be a távoli asztali átjáró létrehozásához. A felhasználók számára hozzáférést biztosítani a felhasználónév/jelszó kombináció, amely a felhasználó rendelkezik a hatókörükön információkkal együtt kell adnia. Ezeket az információkat megbízható legyen, és nem nehezen másolható, például egy mobiltelefonszámot, a mobiltelefonra száma, az alkalmazás egy mobileszközön, és így tovább. A RDG jelenleg támogatja a Microsoft hitelesítő alkalmazási módszereinek telefonos hívását és leküldéses értesítéseit a 2FA. A támogatott hitelesítési módszerekkel kapcsolatos további információkért tekintse [meg a felhasználók által használható hitelesítési módszerek meghatározását](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)ismertető szakaszt.

Az Azure-hoz készült NPS-bővítmény rendelkezésre állása előtt azok az ügyfelek, akik az integrált hálózati házirend-kiszolgáló és az Azure MFA-környezetek kétlépéses ellenőrzésének megvalósítását kívánják végrehajtani, külön MFA-kiszolgálót kellett konfigurálniuk és karbantartani a helyszíni környezetben, ahogyan azt a [Távoli asztali átjáró és az azure multi-Factor Authentication-kiszolgáló RADIUS használatával](howto-mfaserver-nps-rdg.md)dokumentálták.

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

Rendelkeznie kell egy működő infrastruktúra a távoli asztali szolgáltatások (RDS) helyen. Ha nem, akkor gyorsan létrehozhatja ezt az infrastruktúrát az Azure-ban a következő rövid útmutató sablon használatával: [Távoli asztal munkamenet-gyűjtemény központi telepítésének létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Ha szeretné manuálisan létrehozni egy helyszíni távoli asztali szolgáltatások infrastruktúrát gyorsan tesztelésre, lépésekkel üzembe helyez egyet.
**További információ**: [az RDS üzembe helyezése az Azure](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) rövid útmutatóval és az [alapszintű RDS infrastruktúra üzembe helyezésével](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-mfa-license"></a>Az Azure MFA-licenc

Required (kötelező) a licenc az Azure MFA-hoz, elérhető prémium szintű Azure AD vagy az egyéb csomagok, amelyek tartalmazzák azt. Fogyasztásalapú licencek az Azure MFA-hoz, például felhasználói megfelelően vagy hitelesítési licencekkel, amelyek nem kompatibilisek az NPS-bővítményt. További információt az [Azure-multi-Factor Authentication beszerzését](concept-mfa-licensing.md)ismertető témakörben talál. Tesztelési célokra használhatja egy próba-előfizetést.

### <a name="windows-server-software"></a>A Windows Servert

Az NPS-bővítményének megköveteli a Windows Server 2008 R2 SP1 vagy újabb verzió és az NPS szerepkör-szolgáltatás telepítve van. Ebben a szakaszban található összes lépést a Windows Server 2016 használatával lettek végrehajtva.

### <a name="network-policy-and-access-services-nps-role"></a>Hálózati házirend- és hozzáférés-szolgáltatások (NPS) szerepkör

A hálózati házirend-kiszolgáló szerepkör-szolgáltatást biztosít a RADIUS-kiszolgáló és az ügyfél funkcióit, valamint a hálózati házirend-Állapotfigyelő szolgáltatás. Az infrastruktúra legalább két számítógépre telepíteni kell a szerepet: A távoli asztali átjáró és a egy másik tagkiszolgáló vagy tartományvezérlő. Alapértelmezés szerint a szerepkör már létezik a számítógépen, a távoli asztali átjáró konfigurálva.  Emellett telepítenie kell az NPS szerepkör a legalább egy másik számítógépre, például egy tartományvezérlő vagy tagkiszolgáló.

A hálózati házirend-kiszolgáló szerepkör-szolgáltatás Windows Server 2012-es vagy régebbi verziójának telepítésével kapcsolatos információkért lásd: NAP állapotházirend- [kiszolgáló telepítése](https://technet.microsoft.com/library/dd296890.aspx). A hálózati házirend-kiszolgálóval kapcsolatos ajánlott eljárások leírását, beleértve a hálózati házirend-kiszolgáló tartományvezérlőre történő telepítésének javaslatát, lásd: [ajánlott eljárások az NPS-hez](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Az Azure Active Directory a helyszíni Active Directoryval szinkronizált

Az NPS-bővítményének használatához a helyszíni felhasználók lehet az Azure ad-vel szinkronizált, és engedélyezve van az MFA-hoz. Ez a szakasz azt feltételezi, hogy a helyszíni felhasználók vannak szinkronizálva az Azure ad-vel AD Connect használatával. Az Azure AD-kapcsolattal kapcsolatos információkért lásd: [a helyszíni címtárak integrálása Azure Active Directorysal](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID ID

Az NPS-bővítményének telepítése, ismernie kell a GUID azonosítóját az Azure ad-ben. Keresés, a GUID azonosítóját az Azure AD útmutatást az alábbiakban találhatók.

## <a name="configure-multi-factor-authentication"></a>A multi-factor Authentication szolgáltatás konfigurálása

Ez a szakasz ismerteti az Azure MFA integrálása a távoli asztali átjáró. A rendszergazdák konfigurálnia kell az Azure MFA szolgáltatás segítségével a felhasználók saját magukat a multi-factor Authentication eszközök vagy alkalmazások.

Kövesse az [azure multi-Factor Authentication használatának első lépései a felhőben](howto-mfa-getstarted.md) című témakör lépéseit, és engedélyezze az MFA használatát az Azure ad-felhasználók számára.

### <a name="configure-accounts-for-two-step-verification"></a>A kétlépéses ellenőrzéshez fiókok beállítása

Ha a multi-factor Authentication-fiók engedélyezve van, nem jelentkezhet be többtényezős hitelesítési szabályzat szabályozzák, amíg sikeresen konfigurálta a megbízható eszköz használata a hitelesítés második tényezőjét, és elvégezte a hitelesítést használni a kétlépéses ellenőrzést erőforrások.

Kövesse a [Mit jelent az Azure multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user.md) című témakörben ismertetett lépéseket a felhasználói fiókkal kapcsolatos MFA-eszközök megismeréséhez és megfelelő konfigurálásához.

## <a name="install-and-configure-nps-extension"></a>Telepítse és konfigurálja az NPS-bővítményének

Ez a szakasz útmutatást nyújt a távoli asztali szolgáltatások-infrastruktúra konfigurálása az Azure MFA használatát az ügyfél-hitelesítéshez és a távoli asztali átjáró.

### <a name="acquire-azure-active-directory-guid-id"></a>Az Azure Active Directory GUID-azonosító beszerzése

Az NPS-bővítményének konfigurációjának részeként meg kell megadnia az Azure AD-bérlő rendszergazdai hitelesítő adatokat és az Azure AD-azonosítója. A következő lépések bemutatják, hogyan tehet szert a bérlő azonosítója.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-bérlő globális rendszergazdájaként.
1. A bal oldali navigációs sávon válassza a **Azure Active Directory** ikont.
1. Válassza ki a **Tulajdonságok** elemet.
1. A Tulajdonságok panelen, a könyvtár azonosítója mellett kattintson a **Másolás** ikonra az alább látható módon az azonosító vágólapra másolásához.

   ![A könyvtár AZONOSÍTÓjának beolvasása a Azure Portalból](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>Az NPS-bővítményének telepítése

Telepítse az NPS-bővítményt egy kiszolgálóra, amelyen telepítve van a hálózati házirend- és hozzáférés-szolgáltatások (NPS) szerepkör. Ez a kialakítás a RADIUS-kiszolgáló működik.

> [!Important]
> Győződjön meg arról, hogy nem telepíti a távoli asztali átjáró kiszolgálón az NPS-bővítményt.
>

1. Töltse le a [hálózati házirend-kiszolgáló bővítményét](https://aka.ms/npsmfa).
1. Másolja a végrehajtható fájl (NpsExtnForAzureMfaInstaller.exe) a hálózati házirend-kiszolgálóra.
1. Az NPS-kiszolgálón kattintson duplán a **NpsExtnForAzureMfaInstaller. exe fájlra**. Ha a rendszer kéri, kattintson a **Futtatás**gombra.
1. Az Azure MFA beállítása párbeszédpanel NPS-bővítménye párbeszédpanelen tekintse át a szoftverlicenc-feltételeket, ellenőrizze, **hogy elfogadom-e a licencfeltételeket**, majd kattintson a **telepítés**gombra.
1. Az Azure MFA beállítása párbeszédpanel NPS-bővítménye párbeszédpanelen kattintson a **Bezárás**gombra.

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
1. A PowerShell parancssorába írja be a `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`parancsot, majd nyomja le az **ENTER**billentyűt.
1. Írja be `.\AzureMfaNpsExtnConfigSetup.ps1`, majd nyomja le az **ENTER**billentyűt. A parancsfájl ellenőrzi, hogy ha az Azure Active Directory PowerShell-modul telepítve van-e. Ha nincs telepítve, a parancsfájl telepíti a modult.

   ![A AzureMfaNpsExtnConfigSetup. ps1 futtatása az Azure AD PowerShellben](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Miután a parancsfájl ellenőrzi a PowerShell-modul telepítését, az Azure Active Directory PowerShell modul párbeszédpanel jeleníti meg. A párbeszédpanelen adja meg az Azure AD-beli rendszergazdai hitelesítő adatait és jelszavát, majd kattintson a **Bejelentkezés**elemre.

   ![Hitelesítés az Azure AD-ben a PowerShellben](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Ha a rendszer kéri, illessze be a vágólapra korábban másolt könyvtárat, majd nyomja le az **ENTER**billentyűt.

   ![A címtár-azonosító üzembe helyezése a PowerShellben](./media/howto-mfa-nps-extension-rdg/image6.png)

1. A szkript létrehoz egy önaláírt tanúsítványt, és más konfigurációs módosításokat hajt végre. A kimenet az alábbi képhez hasonlóan kell lennie.

   ![Az önaláírt tanúsítványt megjelenítő PowerShell kimenete](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>A távoli asztali átjáró NPS összetevők konfigurálása

Ebben a szakaszban konfigurálja a távoli asztali átjáró kapcsolatengedélyezési házirendek és más RADIUS-beállításokat.

A hitelesítési folyamat megköveteli, hogy a RADIUS-üzenetek a Távoli asztali átjáró és az NPS-kiszolgáló között legyenek cserélve, ahol az NPS bővítmény telepítve van. Ez azt jelenti, hogy konfigurálnia kell a RADIUS-ügyfélbeállításokat a távoli asztali átjáró és a hálózati házirend-kiszolgáló, amelyen telepítve van-e az NPS-bővítményének is.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurálja a távoli asztali átjáró kapcsolatengedélyezési házirendek központi tároló használata

A távoli asztali kapcsolat engedélyezési házirendek (RD CAPs) adja meg a távoli asztali átjáró kiszolgálóhoz való kapcsolódás követelményeinek. A távoli asztali CAPs helyben kell tárolni (alapértelmezett) kell tárolni, vagy egy központi, NPS-t futtató RD CAP tárolóban. Az Azure MFA-integráció konfigurálása a távoli asztali szolgáltatások, adjon meg egy központi tárolóban használatát kell.

1. A RD-átjáró-kiszolgálón nyissa meg a **Kiszolgálókezelő eszközt**.
1. A menüben kattintson az **eszközök**elemre, mutasson a **Távoli asztali szolgáltatások**pontra, majd kattintson az **Távoli asztali átjárókezelő**elemre.
1. A RD-átjárókezelő kattintson a jobb gombbal **\[kiszolgáló neve\] (helyi)** elemre, majd kattintson a **Tulajdonságok**elemre.
1. A Tulajdonságok párbeszédpanelen válassza a **RD CAP áruház** lapot.
1. A RD CAP tároló lapon válassza ki a **hálózati házirend-kiszolgálót futtató központi kiszolgáló**elemet. 
1. Az **adja meg a hálózati házirend-kiszolgálót futtató kiszolgáló nevét vagy IP-címét** mezőbe írja be annak a kiszolgálónak az IP-címét vagy kiszolgálójának nevét, amelyre az NPS-bővítményt telepítette.

   ![Adja meg az NPS-kiszolgáló nevét vagy IP-címét](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Kattintson az **Hozzáadás** parancsra.
1. A **közös titok** párbeszédpanelen adjon meg egy közös titkos kulcsot, majd kattintson **az OK**gombra. Győződjön meg arról, jegyezze fel a közös titkos kulcsot, és tárolja biztonságos helyen a rekordot.

   >[!NOTE]
   >Közös titkos kulcsot a RADIUS-kiszolgálók és ügyfelek közötti megbízhatósági kapcsolat létesítésére szolgál. A hosszú és összetett titkos kulcs létrehozása.
   >

   ![Közös titok létrehozása a megbízhatósági kapcsolat létrehozásához](./media/howto-mfa-nps-extension-rdg/image11.png)

1. A párbeszédpanel bezárásához kattintson az **OK** gombra.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>A távoli asztali átjáró NPS RADIUS időkorlátja konfigurálása

Biztosítják, hogy a felhasználók hitelesítő adatainak ellenőrzésére, kétlépéses hitelesítés végrehajtására, válaszokat kaphatnak és válaszolni RADIUS-üzeneteket, a RADIUS-időtúllépési érték beállításához szükséges időt.

1. A távoli asztali átjáró kiszolgálón nyissa meg a Kiszolgálókezelőt. A menüben kattintson az **eszközök**, majd a **hálózati házirend-kiszolgáló**elemre.
1. Az **NPS (helyi)** konzolon bontsa ki a **RADIUS-ügyfelek és-kiszolgálók**csomópontot, és válassza a **távoli RADIUS-kiszolgáló**lehetőséget.

   ![A hálózati házirend-kiszolgáló felügyeleti konzolja, amely a távoli RADIUS-kiszolgálót mutatja](./media/howto-mfa-nps-extension-rdg/image12.png)

1. A részleteket tartalmazó ablaktáblán kattintson duplán a **terminálszolgáltatási ÁTJÁRÓKISZOLGÁLÓ csoport**elemre.

   >[!NOTE]
   >A RADIUS-kiszolgálócsoport konfigurálta az NPS-házirendek központi kiszolgáló hozott létre. A távoli asztali átjáró RADIUS üzeneteket ehhez a kiszolgálóhoz vagy a kiszolgálók, a csoport továbbít, ha egynél több, a csoportban.
   >

1. A **TS Gateway Server-csoport tulajdonságai** párbeszédpanelen válassza ki a RD capss tárolásához konfigurált NPS-kiszolgáló IP-címét vagy nevét, majd kattintson a **Szerkesztés**gombra.

   ![Válassza ki a korábban konfigurált NPS-kiszolgáló IP-címét vagy nevét](./media/howto-mfa-nps-extension-rdg/image13.png)

1. A **RADIUS-kiszolgáló szerkesztése** párbeszédpanelen válassza a **terheléselosztás** lapot.
1. **A terheléselosztás lapon a** **kérelem eldobása előtt másodpercben megadott számú másodpercben, a kérések eldobása előtt** módosítsa az alapértelmezett értéket 3 értékről egy 30 és 60 másodperc közötti értékre.
1. A **kérések közötti másodpercben, ha a kiszolgáló nem elérhető mezőként van azonosítva** , módosítsa az alapértelmezett 30 másodperc értéket olyan értékre, amely egyenlő vagy nagyobb, mint az előző lépésben megadott érték.

   ![A RADIUS-kiszolgáló időtúllépési beállításainak szerkesztése a terheléselosztás lapon](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Kattintson kétszer az **OK** gombra a párbeszédpanelek bezárásához.

### <a name="verify-connection-request-policies"></a>Ellenőrizze a kapcsolatkérelem-házirendek

Alapértelmezés szerint egy központi házirend store használandó kapcsolat engedélyezési házirendek, a távoli asztali átjáró konfigurálásakor a távoli asztali átjáró van konfigurálva a hálózati házirend-kiszolgáló CAP kérelmeket továbbítja. Az Azure MFA-bővítménnyel, telepítve van, a hálózati házirend-kiszolgáló a RADIUS hozzáférési kérés dolgozza fel. A következő lépések bemutatják, hogyan lehet ellenőrizni az alapértelmezett házirendet.

1. A RD-átjáró a hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki a **házirendek**csomópontot, és válassza a **Kapcsolatkérelem-házirendek**elemet.
1. Kattintson duplán a **terminálszolgáltatási ÁTJÁRÓ engedélyezési házirendje**elemre.
1. A **terminálszolgáltatási ÁTJÁRÓ engedélyezési házirendjének tulajdonságai** párbeszédpanelen kattintson a **Beállítások** fülre.
1. A **Beállítások** lap kapcsolatkérelem továbbítása területén kattintson a **hitelesítés**elemre. RADIUS-ügyfél hitelesítési kérelmek előre van konfigurálva.

   ![A kiszolgálói csoport hitelesítési beállításainak megadása](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Kattintson a **Mégse**gombra.

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Az NPS konfigurálásához a kiszolgálón, amelyen telepítve van-e az NPS bővítményével

A hálózati házirend-kiszolgáló, amelyen telepítve van-e az NPS-bővítményének képesnek kell lennie, a hálózati házirend-kiszolgáló a távoli asztali átjáró RADIUS üzeneteket. Ahhoz, hogy ez az üzenet exchange, kell konfigurálni a hálózati házirend-kiszolgáló-összetevők a kiszolgálón, ahol a hálózati házirend-kiszolgáló bővítmény szolgáltatás telepítve van.

### <a name="register-server-in-active-directory"></a>Regisztrálja a kiszolgálót az Active Directoryban

Ebben a forgatókönyvben megfelelő működéséhez, az NPS-kiszolgáló regisztrálva kell lennie az Active Directoryban.

1. A hálózati házirend-kiszolgálón nyissa meg a **Kiszolgálókezelő eszközt**.
1. A Kiszolgálókezelőben kattintson az **eszközök**, majd a **hálózati házirend-kiszolgáló**elemre.
1. A hálózati házirend-kiszolgáló konzolon kattintson a jobb gombbal az **NPS (helyi)** elemre, majd kattintson **a kiszolgáló regisztrálása Active Directory**lehetőségre.
1. Kattintson kétszer **az OK gombra** .

   ![A hálózati házirend-kiszolgáló regisztrálása Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Hagyja nyitva a következő eljárással a konzolon.

### <a name="create-and-configure-radius-client"></a>Hozzon létre, és a RADIUS-ügyfél konfigurálása

A hálózati házirend-kiszolgáló RADIUS-ügyfélként konfigurálni kell a távoli asztali átjáró.

1. Azon az NPS-kiszolgálón, amelyen a hálózati házirend-kiszolgáló bővítmény telepítve van, az **NPS (helyi)** konzolon kattintson a jobb gombbal a **RADIUS-ügyfelek** elemre, majd kattintson az **új**elemre.

   ![Új RADIUS-ügyfél létrehozása az NPS-konzolon](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Az **Új RADIUS-ügyfél** párbeszédpanelen adjon meg egy rövid nevet, például az _átjárót_, valamint a távoli asztali átjáró kiszolgáló IP-címét vagy DNS-nevét.
1. A **közös titok** és a **közös titkos kulcs megerősítése** mezőkben adja meg a korábban használt titkot.

   ![Felhasználóbarát név és az IP-cím vagy a DNS-cím konfigurálása](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Az új RADIUS-ügyfél párbeszédpanel bezárásához kattintson **az OK** gombra.

### <a name="configure-network-policy"></a>A hálózati házirend konfigurálása

Ne felejtse el, hogy az Azure MFA-bővítménnyel a hálózati házirend-kiszolgáló-e a kijelölt központi házirend a kapcsolat engedélyezési házirend (CAP). Ezért kell megvalósítani a Tengelysapka a hálózati házirend-kiszolgálón érvényes kapcsolat kérelmek hitelesítéséhez.  

1. Az NPS-kiszolgálón nyissa meg a hálózati házirend-kiszolgáló (helyi) konzolt, bontsa ki a **házirendek**csomópontot, majd kattintson a **hálózati házirendek**elemre.
1. Kattintson a jobb gombbal **a kapcsolatok más hozzáférési kiszolgálókhoz**lehetőségre, majd kattintson az **ismétlődő házirend**elemre.

   ![A kapcsolat duplikálása más hozzáférési kiszolgálók házirendjéhez](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Kattintson a jobb gombbal a **kapcsolatok másolása más hozzáférési kiszolgálókhoz**lehetőségre, majd kattintson a **Tulajdonságok**elemre.
1. A **kapcsolatok más hozzáférési kiszolgálókhoz való másolásához** párbeszédpanel **Házirend neve**mezőjében adjon meg egy megfelelő nevet, például _RDG_CAP_. Jelölje be a **házirend engedélyezve**jelölőnégyzetet, majd válassza a **hozzáférés engedélyezése**lehetőséget. Ha szükséges, a **hálózati hozzáférési kiszolgáló típusa**területen válassza a **Távoli asztali átjáró**lehetőséget, vagy hagyja meg a **megadott értéket**.

   ![A szabályzat, az engedélyezés és a hozzáférés engedélyezése](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Kattintson a **megkötések** lapra, és jelölje be az **ügyfelek kapcsolódásának engedélyezése a hitelesítési módszer egyeztetése nélkül lehetőséget**.

   ![Hitelesítési módszerek módosítása az ügyfelek kapcsolódásának engedélyezéséhez](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Ha szükséges, kattintson a **feltételek** lapra, és adja meg azokat a feltételeket, amelyeknek teljesülniük kell ahhoz, hogy a csatlakozás engedélyezve legyen (például egy adott Windows-csoport tagsága).

   ![Nem kötelezően megadhatja a kapcsolatok feltételeit](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Kattintson az **OK** gombra. Amikor a rendszer kéri, hogy tekintse meg a megfelelő súgótémakört, kattintson a **nem**gombra.
1. Győződjön meg arról, hogy az új szabályzat tetején található a listában, hogy a szabályzat engedélyezve van, és hozzáférést biztosít.

   ![A szabályzat áthelyezése a lista elejére](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Konfiguráció ellenőrzése

A konfiguráció ellenőrzéséhez jelentkezzen be a távoli asztali átjáró-megfelelő RDP-ügyfelet kell. Győződjön meg arról, fiók, amely a házirendek által engedélyezett, és engedélyezve van az Azure MFA-kiszolgáló használatára.

Ahogy az alábbi képen is látható, használhatja a **Távoli asztal webes elérés** lapot.

![Tesztelés Távoli asztal webes eléréssel](./media/howto-mfa-nps-extension-rdg/image25.png)

Sikeresen megadta a hitelesítő adatait az elsődleges hitelesítéshez, hogy a távoli asztali kapcsolat párbeszédpanel állapotát jeleníti meg egy távoli kapcsolat kezdeményezése alább látható módon. 

Sikerült a hitelesítést az Azure MFA-ban korábban konfigurált másodlagos hitelesítési módszert, ha csatlakozik az erőforrást. Ha a másodlagos hitelesítés nem sikeres, az erőforráshoz való hozzáférés nem engedélyezett. 

![Távoli asztali kapcsolat távoli kapcsolatok kezdeményezése](./media/howto-mfa-nps-extension-rdg/image26.png)

Az alábbi példában az Authenticator alkalmazás Windows Phone-eszközön szolgál a másodlagos hitelesítő adatokat megadnia.

![Példa Windows Phone-telefon hitelesítő alkalmazásra, amely tartalmazza az ellenőrzést](./media/howto-mfa-nps-extension-rdg/image27.png)

A hitelesítés sikerült a másodlagos hitelesítési módszerrel, miután bejelentkezett a távoli asztali átjáró szokásos módon. Mivel azonban másodlagos hitelesítési módszert kell használnia egy megbízható eszközön lévő Mobile alkalmazás használatával, a bejelentkezési folyamat biztonságosabb, mint egyébként.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Sikeres bejelentkezési események Eseménynapló naplóinak megtekintése

A sikeres bejelentkezési események megtekintése a Windows-eseménynaplók, adja ki a következő Windows PowerShell-parancsot a Windows Terminálszolgáltatások és a Windows biztonsági naplók lekérdezése.

A sikeres bejelentkezési események az átjáró operatív naplóiban való lekérdezéséhez _(Event \ alkalmazás és Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_ használja a következő PowerShell-parancsokat:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Ez a parancs azt mutatják be, a felhasználó teljesíti az erőforrás-engedélyezési házirend követelményeinek (RD RAP), és hozzáférést Windows-eseményeket jeleníti meg.

![Események megtekintése a PowerShell használatával](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Ez a parancs a felhasználói kapcsolat engedélyezési házirend követelményeinek teljesülése esetén megjelenítő eseményeket jeleníti meg.

![a kapcsolatkérelem-házirend megtekintése a PowerShell használatával](./media/howto-mfa-nps-extension-rdg/image29.png)

Ez a napló és szűrő azonosítóval, 300, és 200-as esemény is megtekintheti. A biztonsági eseménynapló sikeres bejelentkezési események lekérdezés, használja a következő parancsot:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Ez a parancs futtatható a központi NPS-t vagy a távoli asztali átjárókiszolgálón.

![Sikeres bejelentkezési események mintája](./media/howto-mfa-nps-extension-rdg/image30.png)

Alább látható módon a biztonsági napló vagy a hálózati házirend- és elérési szolgáltatások egyéni nézet is megtekintheti:

![Hálózati házirend-és elérési szolgáltatások Eseménynapló](./media/howto-mfa-nps-extension-rdg/image31.png)

Azon a kiszolgálón, amelyen telepítette az Azure MFA-hoz készült NPS-bővítményt, megtalálhatja a bővítményre vonatkozó Eseménynapló alkalmazás _-és szolgáltatás-Logs\Microsoft\AzureMfa_.

![Eseménynapló AuthZ](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Útmutató hibaelhárítása

A konfiguráció nem várt módon működik, ha az első hely hibaelhárítás indítása az győződjön meg arról, hogy a felhasználó az Azure MFA használatára van konfigurálva. A felhasználó csatlakozik a [Azure Portalhoz](https://portal.azure.com). Ha a felhasználók másodlagos ellenőrzőkulcs a rendszer kéri, és sikeresen be tud hitelesítést, megszüntetheti az Azure MFA nem megfelelő konfiguráció.

Az Azure MFA a felhasználó esetében működik, ha, tekintse át a megfelelő eseménynaplók. Ezek közé tartozik a biztonsági esemény, az átjáró működési és az előző szakaszban tárgyalt Azure MFA-naplókat.

Alább a biztonsági napló a sikertelen bejelentkezési esemény (Event ID 6273) bemutató példa kimenet.

![Sikertelen bejelentkezési esemény mintája](./media/howto-mfa-nps-extension-rdg/image33.png)

Alább az AzureMFA naplók a kapcsolódó esemény:

![Azure MFA-bejelentkezés mintája Eseménynapló](./media/howto-mfa-nps-extension-rdg/image34.png)

Hajtsa végre a Speciális beállítások hibaelhárítása, tekintse meg a hálózati házirend-kiszolgáló adatbázis formátum naplófájlokat ahol a hálózati házirend-kiszolgáló szolgáltatás telepítve van. Ezek a naplófájlok a _%systemroot%\System32\Logs_ mappában, vesszővel tagolt szövegfájlként jönnek létre.

A naplófájlok leírását lásd: a [hálózati házirend-kiszolgáló adatbázis-formátumú naplófájljainak értelmezése](https://technet.microsoft.com/library/cc771748.aspx). Ezekben a naplófájlokban lévő bejegyzéseket nehéz értelmezni anélkül, hogy importálná őket egy külön táblázatban vagy az adatbázis is lehet. Több IAS elemzők online megtalálhatja a segítséget nyújtanak a naplófájlok értelmezése.

Az alábbi képen egy ilyen letölthető [shareware-alkalmazás](https://www.deepsoftware.com/iasviewer)kimenete látható.

![Példa a shareware alkalmazás IAS-elemzője](./media/howto-mfa-nps-extension-rdg/image35.png)

Végül a további hibakeresési lehetőségekhez használhatja a Protocol Analyzert, például a [Microsoft Message Analyzert](https://technet.microsoft.com/library/jj649776.aspx)is.

Az alábbi képen a Microsoft Message Analyzer a **CONTOSO\AliceC**felhasználónevet tartalmazó RADIUS protokollon szűrt hálózati forgalmat jeleníti meg.

![Szűrt forgalmat bemutató Microsoft Message Analyzer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>További lépések

[Az Azure Multi-Factor Authentication beszerzése](concept-mfa-licensing.md)

[Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](howto-mfaserver-nps-rdg.md)

[A helyszíni címtárak integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
