---
title: Távoli asztali átjáró integrációja Azure MFA NPS kiterjesztésű |} Microsoft Docs
description: A távoli asztali átjáró infrastruktúra integrálása az Azure MFA használata a hálózati házirend-kiszolgáló bővítmény a Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 08/15/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: fbca491e845ad96ecafc6afb5f40d46b99c047bf
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>A távoli asztali átjáró-infrastruktúra használata a hálózati házirend-kiszolgáló (NPS) bővítményt, és az Azure AD integrálása

Ez a cikk részletesen használható a távoli asztali átjáró infrastruktúra az Azure multi-factor Authentication (MFA) a hálózati házirend-kiszolgáló (NPS) bővítmény a Microsoft Azure használatával. 

A hálózati házirend-kiszolgáló (NPS) bővítményt az Azure lehetővé teszi az ügyfelek védelme távoli Authentication Dial-In User Service (RADIUS) ügyfél-hitelesítéshez Azure tartozó felhőalapú [multi-factor Authentication (MFA)](multi-factor-authentication.md). Ez a megoldás biztosítja a kétlépéses ellenőrzést egy második biztonsági réteggel hozzáadása a felhasználói bejelentkezéseket és tranzakciókat.

Ez a cikk részletes útmutatást nyújt a hálózati házirend-kiszolgáló infrastruktúra integrálható az Azure MFA használ a hálózati házirend-kiszolgáló kiterjesztést az Azure-bA. Ez lehetővé teszi a felhasználóknak jelentkezzen be a távoli asztali átjáró biztonságos ellenőrzése. 

A hálózati házirend- és hozzáférés-szolgáltatások (NPS) révén a szervezetek tegye a következőket teszi:
* Adja meg a központi felügyeleti helyének és a vezérlő ad meg a kapcsolódást a hálózati kérelmek, mely napszakokban nap kapcsolatok engedélyezve legyenek, kapcsolatok időtartama, és a szintű biztonságot, az ügyfeleket használata a csatlakozáshoz, és így tovább. Ahelyett, hogy ezek a házirendek megadása minden VPN vagy a távoli asztal (RD) átjáró kiszolgálón, ezek a házirendek egyszer adható meg egy központi helyen. A RADIUS protokollal biztosít a központosított hitelesítési, engedélyezési és nyilvántartási (AAA). 
* Állítson be, és kényszeríteni a hálózatvédelem (NAP) ügyfél állapotházirendeket, amelyek meghatározzák, hogy eszközök hálózati erőforrások korlátozás nélküli vagy korlátozott hozzáférést kapnak.
* Olyan hitelesítési és engedélyezési 802.1 hozzáférés kényszerítésére biztosít x-kompatibilis vezeték nélküli hozzáférési pontok és Ethernet-kapcsolók.    

Általában a szervezetek hálózati házirend-kiszolgáló (RADIUS) egyszerűsítése és VPN kezelésének központosítása házirendeket akkor használja. Azonban számos szervezet segítségével is NPS egyszerűsítésére és a távoli asztali asztali kapcsolatengedélyezési házirendek (RD CAP-ok) kezelésének központosítása. 

A szervezetek hálózati házirend-kiszolgáló is integrálható az Azure MFA növelje a biztonságot, és a magas szintű való megfelelőség. Ezzel biztosíthatja, hogy a felhasználók létrehozásához jelentkezzen be a távoli asztali átjáró a kétlépéses ellenőrzést. A felhasználók számára a hozzáférést meg kell adniuk a felhasználónév/jelszó kombináció, amely a felhasználó rendelkezik-e a vezérlőben információk mellett. Ezeket az információkat megbízható kell, és a rendszer egyszerűen nem lettek duplikálva, például a mobiltelefonszám, a vezetékes számát, a kérelem egy mobileszközön, és így tovább.

A bővítmény rendelkezésre állását a hálózati házirend-kiszolgáló az Azure-ba, mielőtt az ügyfelek, akik az integrált hálózati házirend-kiszolgáló és az Azure MFA környezetek kétlépéses ellenőrzés végrehajtásához szükséges kellett konfigurálnia és karbantartania egy külön MFA kiszolgáló a helyszíni környezetben, ahogy [távoli asztali átjáró és az Azure multi-factor Authentication kiszolgáló RADIUS használata](howto-mfaserver-nps-rdg.md).

A bővítmény rendelkezésre állását a hálózati házirend-kiszolgáló az Azure lehetőséget nyújt a szervezetek biztonságos RADIUS-ügyfél-hitelesítés vagy egy helyszíni MFA-megoldását, vagy egy MFA felhőalapú megoldás telepítendő választás.

## <a name="authentication-flow"></a>Hitelesítési folyamat

A felhasználók számára hozzáférést a hálózati erőforrásokhoz a távoli asztali átjárón keresztül egy távoli asztali kapcsolat engedélyezési házirendje (RD CAP) és egy távoli asztali erőforrás engedélyezési házirendje (RD RAP) a megadott feltételeknek kell megfelelniük. Távoli asztali CAPs adja meg, ki jogosult csatlakozni a távoli asztali átjáró. RD RAP-k adja meg a hálózati erőforrások, például a távoli asztali vagy a távoli alkalmazások esetében, amelyet a felhasználó számára a távoli asztali átjárón keresztül. 

Egy távoli asztali átjáró beállítható úgy, hogy az RD CAP-OK központi házirend használjon. RD RAP-k nem használhatja egy központi házirend, az RD-átjárón feldolgozásra kerülnek. Példa egy távoli asztali átjáró használatára konfigurálva egy központi tárolóban az RD CAP-ok: a RADIUS-ügyfél egy másik hálózati házirend-kiszolgálóra, amely a házirend központi tárolóként szolgál.

Ha a hálózati házirend-kiszolgáló az Azure-bővítmény a hálózati házirend-kiszolgáló és a távoli asztali átjáró integrálva van, a sikeres hitelesítési folyamat következőképpen történik:

1. A távoli asztali átjárókiszolgáló-csatlakozás erőforráshoz, például a távoli asztali munkamenetet a távoli asztali felhasználók hitelesítési kérést kap. Egy RADIUS-ügyfél működött, a távoli asztali átjárókiszolgáló alakítja át a kérelem egy RADIUS-kérést üzenetet, és a üzenetet küld a RADIUS (NPS) kiszolgálóra, amelyen telepítve van-e a hálózati házirend-kiszolgáló bővítmény. 
2. A felhasználónév és jelszó kombinációjával ellenőrzése az Active Directoryban, és a felhasználó hitelesítése.
3. Ha a hálózati házirend-kiszolgáló kapcsolódási kérelem és a hálózati házirendeket a feltételek teljesülnek (például időpont vagy csoport tagsági korlátozások), a hálózati házirend-kiszolgáló bővítményt váltja ki az Azure MFA másodlagos hitelesítési kérelmet. 
4. Az Azure MFA az Azure ad-val kommunikál, lekérdezi a felhasználó adatait, és végrehajtja a másodlagos hitelesítést (SMS-üzenet, mobilalkalmazás és így tovább) a felhasználó által beállított metódussal. 
5. Az MFA-kérdést sikeres, akkor az Azure MFA kommunikál a hálózati házirend-kiszolgáló bővítmény eredménye.
6. A hálózati házirend-kiszolgáló, ahol a bővítmény telepítve van, az RD CAP-házirend RADIUS Access-Accept üzenetet küld a távoli asztali átjárókiszolgáló.
7. A felhasználó hozzáférést kap a kért hálózati erőforráshoz az RD átjárón keresztül.

## <a name="prerequisites"></a>Előfeltételek
Ez a szakasz részletesen az Azure MFA integrálása a távoli asztali átjáró előtt szükséges előfeltételeket. Mielőtt hozzákezd, rendelkeznie kell a következő előfeltételek teljesülését helyen.  

* Távoli asztali szolgáltatások (RDS) infrastruktúra
* Az Azure MFA-licenc
* Windows Server szoftver
* Hálózati házirend- és hozzáférés-szolgáltatások (NPS) szerepkör
* A helyszíni Active Directoryval szinkronizálva az Azure Active Directory
* Azure Active Directory GUID ID

### <a name="remote-desktop-services-rds-infrastructure"></a>Távoli asztali szolgáltatások (RDS) infrastruktúra
Távoli asztali szolgáltatások (RDS) infrastruktúra működő helyen kell rendelkeznie. Ha nem, akkor ez az infrastruktúra az Azure-ban a következő gyors üzembe helyezési sablon gyorsan létrehozhat: [távoli asztali munkamenet-gyűjtemény létrehozása központi telepítési](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment). 

Ha manuálisan hozzon létre egy helyszíni távoli asztali infrastruktúra gyors tesztelésre, kövesse az egyik telepítésének a lépéseit. 
**További**: [távoli asztali szolgáltatások telepítése az Azure gyors üzembe helyezési](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) és [alapszintű RDS-infrastruktúra telepítése](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure). 

### <a name="azure-mfa-license"></a>Az Azure MFA-licenc
A licenc szükség van az Azure MFA számára, amely elérhető az Azure AD Premium, nagyvállalati mobilitási és biztonsági (EMS) vagy az MFA szolgáltatásra. Az Azure MFA számára, például az adott felhasználó vagy a hitelesítési licencek fogyasztás alapján licencek nem kompatibilisek-e a hálózati házirend-kiszolgáló bővítményt. További információkért lásd: [beolvasásával Azure multi-factor Authentication](concept-mfa-licensing.md). Tesztelési célokra használható a próba-előfizetést. 

### <a name="windows-server-software"></a>Windows Server szoftver
A hálózati házirend-kiszolgáló-bővítményhez olyan Windows Server 2008 R2 SP1 vagy újabb, az NPS szerepkör-szolgáltatás telepítve. Ebben a szakaszban a lépéseket a Windows Server 2016 végeztek.

### <a name="network-policy-and-access-services-nps-role"></a>Hálózati házirend- és hozzáférés-szolgáltatások (NPS) szerepkör
Az NPS szerepkör-szolgáltatást biztosít a RADIUS-kiszolgáló és az ügyfél funkcióit, valamint a hálózati házirend Állapotfigyelő szolgáltatás. A infrastruktúrában legalább két számítógépen telepíteni kell a szerepet: A távoli asztali átjáró és egy másik tagkiszolgáló vagy tartományvezérlő. Alapértelmezés szerint a szerepkör már telepítve a számítógép a távoli asztali átjáró konfigurálva.  Telepítenie kell az NPS szerepkör a legalább egy másik számítógépre, például egy tartományvezérlő vagy tagkiszolgáló.

Az NPS szerepkör telepítéséről további információt a Windows Server 2012 vagy régebbi szolgáltatás című [a NAP állapotházirend-kiszolgáló telepítése](https://technet.microsoft.com/library/dd296890.aspx). Ajánlott eljárások a hálózati házirend-kiszolgáló, beleértve a hálózati házirend-kiszolgáló telepítése tartományvezérlőn, az ajánlás leírását lásd: [ajánlott eljárások a hálózati házirend-kiszolgáló](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>A helyszíni Active Directoryval szinkronizálva az Azure Active Directory
Az NPS-bővítmény használatához a helyi felhasználók kell az Azure ad-val szinkronizálva és engedélyezve van az MFA szolgáltatásra. Ez a szakasz azt feltételezi, hogy a helyi felhasználók és az Azure AD Connect használatával AD vannak szinkronizálva. Információk az Azure AD connect című [integrálása a helyszíni címtárakat az Azure Active Directoryval](../connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID ID
NPS-bővítményének telepítése kell tudni, hogy az Azure AD GUID-azonosítója. A GUID-azonosítója az Azure AD-kereséshez utasításokat alatt.

## <a name="configure-multi-factor-authentication"></a>Többtényezős hitelesítés beállítása 
Ez a szakasz ismerteti az Azure MFA integrálása a távoli asztali átjáró. Ha Ön rendszergazda konfigurálnia kell az Azure MFA szolgáltatással a felhasználók saját maguk regisztrálhatják a multi-factor Authentication eszközök vagy alkalmazások.

Kövesse a [Ismerkedés az Azure multi-factor Authentication a felhőben](howto-mfa-getstarted.md) engedélyezi az MFA Használatát az Azure Active Directory-felhasználók számára. 

### <a name="configure-accounts-for-two-step-verification"></a>A kétlépéses ellenőrzéshez fiókok beállítása
Ha a fiók engedélyezve van az MFA szolgáltatásra, nem tud bejelentkezni szabályozzák a többtényezős hitelesítési szabályzat, amíg sikeresen konfigurálta a második hitelesítési tényezővel és segítségével kétlépéses hitelesítéssel hitelesített megbízható eszköz erőforrások.

Kövesse a [mit Azure multi-factor Authentication jelent a számomra?](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user.md) megértéséhez, valamint megfelelően konfigurálni a felhasználói fiókjához a multi-factor Authentication az eszközök.

## <a name="install-and-configure-nps-extension"></a>Telepítse és konfigurálja a hálózati házirend-kiszolgáló bővítmény
Ez a témakör az Azure MFA használata az ügyfél-hitelesítéshez a távoli asztali átjáró az RDS-infrastruktúra konfigurálására vonatkozó útmutatásokat.

### <a name="acquire-azure-active-directory-guid-id"></a>Szerezzen be az Azure Active Directory GUID azonosítója

A hálózati házirend-kiszolgáló bővítmény konfigurációjának részeként kell megadnia a rendszergazdai hitelesítő adatokat és az Azure AD-azonosítója az Azure AD-bérlő. A következő lépések bemutatják a beolvasni a bérlő.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) az Azure-bérlőhöz globális rendszergazdájaként.
2. A bal oldali navigációs, válassza ki a **Azure Active Directory** ikonra.
3. Válassza ki **tulajdonságok**.
4. A könyvtár-azonosító, kattintson a Tulajdonságok panelen kattintson a **másolási** ikonra, az alábbi módon való másolása a vágólapra az Azonosítót.

 ![Tulajdonságok](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>A hálózati házirend-kiszolgáló-kiterjesztés telepítése
Telepítse az NPS-bővítmény olyan kiszolgálón, amelyre telepítve van a hálózati házirend- és hozzáférés-szolgáltatások (NPS) szerepkör. Ez a kialakítás a RADIUS-kiszolgáló funkcionál. 

>[!Important]
> Győződjön meg arról, hogy a hálózati házirend-kiszolgáló bővítmény nem telepíti a távoli asztali átjáró kiszolgálón.
> 

1. Töltse le a [hálózati házirend-kiszolgáló bővítmény](https://aka.ms/npsmfa). 
2. Másolja a végrehajtható fájl (NpsExtnForAzureMfaInstaller.exe) a hálózati házirend-kiszolgáló.
3. A hálózati házirend-kiszolgálón kattintson duplán a **NpsExtnForAzureMfaInstaller.exe**. Ha a rendszer kéri, kattintson a **futtatása**.
4. A hálózati házirend-kiszolgáló bővítmény Azure MFA telepítése párbeszédpanelen tekintse át a szoftverlicenc-szerződést, ellenőrizze **elfogadom a licencfeltételeket és a feltételek**, és kattintson a **telepítése**.
 
  ![Az Azure MFA beállítása](./media/howto-mfa-nps-extension-rdg/image2.png)

5. Kattintson a hálózati házirend-kiszolgáló bővítmény Azure MFA beállítása párbeszédpanel **Bezárás**. 

  ![Az Azure MFA használatára a hálózati házirend-kiszolgáló bővítmény](./media/howto-mfa-nps-extension-rdg/image3.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>A hálózati házirend-kiszolgáló PowerShell-parancsfájl használatával kiterjesztésű használt tanúsítványok konfigurálása
Ezt követően kell biztonságos kommunikációt, és biztosítani biztosításához a hálózati házirend-kiszolgáló bővítmény által használt tanúsítványok konfigurálása. A hálózati házirend-kiszolgáló összetevői az alábbiak a Windows PowerShell-parancsfájlt, amely beállítja a hálózati házirend-kiszolgáló egy önaláírt tanúsítványt. 

A parancsfájl a következő műveleteket hajtja végre:

* Létrehoz egy önaláírt tanúsítványt
* Társítja az egyszerű szolgáltatásnév az Azure AD a tanúsítvány nyilvános kulcsa
* A tanúsítvány a helyi számítógép tárolójában tárolja
* A tanúsítvány titkos kulcsa a hálózati felhasználók számára a hozzáférést
* Hálózati házirend-kiszolgáló szolgáltatás újraindítása

Ha szeretné használni a saját tanúsítványait, szeretné a tanúsítványt, a szolgáltatás egyszerű nyilvános kulcsát társítsa Azure ad-val, és így tovább.

A parancsfájl használatát, adja meg a bővítményt a Azure AD rendszergazdai hitelesítő adataival és a korábban kimásolt az Azure AD-bérlő azonosítója. Futtassa a parancsfájlt minden hálózati házirend-kiszolgálón, amelyre telepítette az NPS-bővítmény. Ezután tegye a következőket:

1. Nyisson meg egy felügyeleti Windows PowerShell-parancssort.
2. A PowerShell-parancssorba írja be a `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`, és nyomja le az ENTER **ENTER**.
3. Típus `.\AzureMfsNpsExtnConfigSetup.ps1`, és nyomja le az ENTER **ENTER**. A parancsfájl ellenőrzi, hogy ha az Azure Active Directory PowerShell-modul telepítve van-e. Ha nincs telepítve, a parancsfájl telepíti a modult.

  ![Az Azure AD PowerShell segítségével](./media/howto-mfa-nps-extension-rdg/image4.png)
  
4. Miután a parancsfájl a PowerShell-modul ellenőrzi, az Azure Active Directory PowerShell modul párbeszédpanel jelenik meg. A párbeszédpanelen adja meg a Azure AD rendszergazdai hitelesítő adatait, és a jelszót, és kattintson **bejelentkezés**.

  ![Powershell-fiók megnyitása](./media/howto-mfa-nps-extension-rdg/image5.png)

5. Amikor a rendszer kéri, illessze be a vágólapra korábban kimásolt Bérlőazonosító, és nyomja le az ENTER **ENTER**.

  ![Adja meg a bérlő azonosítója](./media/howto-mfa-nps-extension-rdg/image6.png)

6. A parancsfájl létrehoz egy önaláírt tanúsítványt, és más konfigurációs módosításokat hajt végre. A kimeneti kell lennie, például az alább látható kép.

  ![Önaláírt tanúsítvány](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Hálózati házirend-kiszolgáló-összetevők konfigurálása a távoli asztali átjáró
Ez a szakasz konfigurálja a távoli asztali átjáró kapcsolatengedélyezési házirendek és más RADIUS-beállításai.

A hitelesítési folyamat megköveteli, hogy a RADIUS-üzenetek felcserélhetők-e a távoli asztali átjáró és a hálózati házirend-kiszolgáló, amelyen telepítve van-e az a hálózati házirend-kiszolgáló között. Ez azt jelenti, hogy konfigurálnia kell RADIUS-ügyfélbeállításokat távoli asztali átjáró és a hálózati házirend-kiszolgáló, amelyen telepítve van-e a hálózati házirend-kiszolgáló bővítmény is. 

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurálja a távoli asztali átjáró kapcsolatengedélyezési házirendek központi tároló használatára
Távoli asztali kapcsolat engedélyezési házirendek (RD CAP-ok) adja meg a távoli asztali átjárókiszolgáló való kapcsolódás követelményeinek. Távoli asztali CAPs helyileg tárolhatja (alapértelmezett), illetve kell tárolni az NPS szolgáltatást futtató RD CAP üzlet központi. Az Azure MFA-integráció konfigurálása a távoli asztali szolgáltatások, meg kell adnia egy központi áruház.

1. Nyissa meg a távoli asztali átjárókiszolgáló **Kiszolgálókezelő**. 
2. A menüben kattintson a **eszközök**, mutasson a **távoli asztali szolgáltatások**, és kattintson a **távoli asztali átjárókezelő**.

  ![Távoli asztali szolgáltatások](./media/howto-mfa-nps-extension-rdg/image8.png)

3. A távoli asztali átjáró Manger, kattintson a jobb egérgombbal  **\[kiszolgálónév\] (helyi)**, és kattintson a **tulajdonságok**.

  ![Kiszolgáló neve](./media/howto-mfa-nps-extension-rdg/image9.png)

4. A Tulajdonságok párbeszédpanelen válassza ki a **távoli asztali házirendjeihez** fülre.
5. Válassza ki a távoli asztali házirendjeihez lapon **központi házirend-kiszolgálót futtató**. 
6. Az a **adjon meg egy nevet vagy IP-címet a hálózati házirend-kiszolgáló** mezőbe írja be a kiszolgálón, amelyre telepítve a hálózati házirend-kiszolgáló bővítmény IP-címét vagy kiszolgálónevét.

  ![Adja meg nevét vagy IP-cím](./media/howto-mfa-nps-extension-rdg/image10.png)
  
7. Kattintson a **Hozzáadás** parancsra.
8. Az a **közös titkos kulcs** párbeszédpanelen adja meg a közös titkos kulcsot, és kattintson **OK**. Győződjön meg arról, jegyezze fel a közös titkos kulcsot, és tárolja biztonságos helyen a rekordot.

 >[!NOTE]
 >Közös titkos kulcsot a RADIUS-kiszolgálók és ügyfelek közötti megbízhatósági kapcsolat létrehozására szolgál. Hosszú és összetett titkos kulcs létrehozása.
 >

 ![Közös titok](./media/howto-mfa-nps-extension-rdg/image11.png)

9. A párbeszédpanel bezárásához kattintson az **OK** gombra.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>A távoli asztali átjáró NPS RADIUS időtúllépési értékének konfigurálása
Gondoskodjon arról, hogy a felhasználói fiók hitelesítő adatainak érvényesítéséhez idő, hajtsa végre a kétlépéses ellenőrzést, válasz fogadása és válaszolni RADIUS-üzenetek, úgy, hogy a RADIUS-időtúllépési érték szükséges.

1. A távoli asztali átjáró kiszolgálón nyissa meg a Kiszolgálókezelőt. A menüben kattintson a **eszközök**, és kattintson a **hálózati házirend-kiszolgáló**. 
2. Az a **hálózati házirend-kiszolgáló (helyi)** konzolt, bontsa ki a **RADIUS-ügyfelek és kiszolgálók**, és válassza ki **távoli RADIUS-kiszolgáló**.

 ![Távoli RADIUS-kiszolgáló](./media/howto-mfa-nps-extension-rdg/image12.png)

3. A részleteket tartalmazó ablaktáblán kattintson duplán a **TS ÁTJÁRÓ kiszolgáló csoport**.

 >[!NOTE]
 >A RADIUS-kiszolgálócsoport jött létre, amikor az NPS-házirendek központi server konfigurálta. A távoli asztali átjáró továbbítja a kiszolgáló vagy kiszolgálók, a RADIUS-üzenetek, ha egynél több csoport.
 >

4. Az a **TS ÁTJÁRÓ kiszolgáló csoport tulajdonságai** párbeszédpanelen jelölje ki az IP-cím vagy a állított be a távoli asztali CAPs tárolja, és kattintson a hálózati házirend-kiszolgáló neve **szerkesztése**. 

 ![TS átjáró kiszolgáló csoport](./media/howto-mfa-nps-extension-rdg/image13.png)

5. Az a **RADIUS-kiszolgáló szerkesztése** párbeszédpanelen jelölje ki a **terheléselosztás** fülre.
6. Az a **terheléselosztás** lap a **eldobása előtt válasz nélkül másodpercben** mezőben, majd az alapértelmezett érték 3 30 – 60 másodperc közötti értéket.
7. Az a **kiszolgáló nem érhető el, amelynél kérelmek között eltelt másodpercek száma** mezőben, majd az alapértelmezett érték 30 másodperc az előző lépésben megadott értéknél nagyobb vagy egyenlő értéket.

 ![RADIUS-kiszolgáló szerkesztése](./media/howto-mfa-nps-extension-rdg/image14.png)

8.  Kattintson a **OK** kétszer a párbeszédpanelek bezárásához.

### <a name="verify-connection-request-policies"></a>Kapcsolatkérelem-házirend ellenőrzése 
Alapértelmezés szerint egy központi házirendtároló használandó kapcsolatengedélyezési házirendek, a távoli asztali átjáró konfigurálásakor a távoli asztali átjáró van konfigurálva a hálózati házirend-kiszolgáló CAP kérelmeket továbbítja. A hálózati házirend-kiszolgáló és az Azure MFA-kiterjesztés telepítése, a RADIUS-hozzáférési kérelem dolgozza fel. A következő lépések bemutatják a ellenőrzése az alapértelmezett házirendet. 

1. Az RD-átjárón, a hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki a **házirendek**, és válassza ki **kapcsolatkérelem-házirendek**.
2. Kattintson a jobb gombbal **házirendek csatlakozás**, és kattintson duplán a **TS ÁTJÁRÓ engedélyezési házirend**.
3. Az a **TS ÁTJÁRÓ engedélyezési házirend tulajdonságai** párbeszédpanel, kattintson a **beállítások** fülre.
4. A **beállítások** kattintson a lap Kapcsolatkérelem továbbítása **hitelesítési**. RADIUS-ügyfél kérelmek továbbítása hitelesítésre van konfigurálva.

 ![Hitelesítési beállítások](./media/howto-mfa-nps-extension-rdg/image15.png)
 
5. Kattintson a **Mégse**. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Hálózati házirend-kiszolgáló konfigurálása a kiszolgálón, amelyen telepítve van-e a hálózati házirend-kiszolgáló bővítmény
A hálózati házirend-kiszolgáló, amelyen telepítve van-e a hálózati házirend-kiszolgáló bővítmény kell tennie a hálózati házirend-kiszolgáló, a távoli asztali átjárón RADIUS üzeneteket. Ahhoz, hogy az üzenet exchange, meg kell konfigurálni a hálózati házirend-kiszolgáló-összetevők a kiszolgálón, ahol a hálózati házirend-kiszolgáló bővítmény szolgáltatás telepítve van. 

### <a name="register-server-in-active-directory"></a>Kiszolgáló regisztrálása az Active Directoryban
Ebben a forgatókönyvben megfelelően működni, a hálózati házirend-kiszolgáló regisztrálva kell lennie az Active Directoryban.

1. Nyissa meg a hálózati házirend-kiszolgáló **Kiszolgálókezelő**.
2. A Kiszolgálókezelőben kattintson **eszközök**, és kattintson a **hálózati házirend-kiszolgáló**. 
3. A hálózati házirend-kiszolgáló konzol, kattintson a jobb gombbal **hálózati házirend-kiszolgáló (helyi)**, és kattintson a **kiszolgáló regisztrálása az Active Directoryban**. 
4. Kattintson a **OK** kétszer.

 ![Regisztrálja a kiszolgálót az ad-ben](./media/howto-mfa-nps-extension-rdg/image16.png)

5. Hagyja nyitva a következő eljárással a konzolt.

### <a name="create-and-configure-radius-client"></a>Hozza létre és konfigurálja a RADIUS-ügyfél 
A távoli asztali átjáró kell beállítani, mint egy RADIUS-ügyfél a hálózati házirend-kiszolgálóra. 

1. Ha a hálózati házirend-kiszolgáló bővítmény telepítve van, a hálózati házirend-kiszolgálón a **hálózati házirend-kiszolgáló (helyi)** konzoljában a jobb gombbal **RADIUS-ügyfelek** kattintson **új**.

 ![Új RADIUS-ügyfelek](./media/howto-mfa-nps-extension-rdg/image17.png)

2. Az a **új RADIUS-ügyfél** párbeszédpanelen adja meg egy rövid nevet, például a _átjáró_, és az IP-címet, vagy a DNS-nevét a távoli asztali átjárókiszolgáló. 
3. Az a **közös titkos kulcs** és a **közös titkos kulcs jóváhagyása** mezőkben adja meg ugyanazt a titkos kulcsot előtt használt.

 ![Nevét és címét](./media/howto-mfa-nps-extension-rdg/image18.png)

4. Kattintson a **OK** az új RADIUS-ügyfél párbeszédpanel bezárásához.

### <a name="configure-network-policy"></a>Hálózati házirend konfigurálása
A visszaírási, hogy a hálózati házirend-kiszolgáló, az Azure MFA-bővítménnyel-e a kijelölt központi házirend a kapcsolat engedélyezési házirend (CAP). Ezért kell megvalósítani a Tengelysapka a hálózati házirend-kiszolgálón érvényes kapcsolatok kérések engedélyezésére.  

1. A hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki **házirendek**, és kattintson a **hálózati házirendek**.
2. Kattintson a jobb gombbal **más kiszolgálók eléréséhez kapcsolatok**, és kattintson a **házirend ismétlődő**. 

 ![Ismétlődő házirend](./media/howto-mfa-nps-extension-rdg/image19.png)

3. Kattintson a jobb gombbal **másolása a kapcsolatok egyéb kiszolgálók eléréséhez**, és kattintson a **tulajdonságok**.

 ![Hálózat tulajdonságai](./media/howto-mfa-nps-extension-rdg/image20.png)

4. Az a **másolása a kapcsolatok egyéb kiszolgálók eléréséhez** párbeszédpanel **házirendnév**, adja meg a megfelelő nevét, például _RDG_CAP_. Ellenőrizze **házirenddel**, és válassza ki **hozzáférést**. Nem kötelező **hálózat-hozzáférési kiszolgáló típusa**, jelölje be **távoli asztali átjáró**, vagy hagyhatja azt **meghatározatlan**.

 ![A kapcsolatok másolása](./media/howto-mfa-nps-extension-rdg/image21.png)

5.  Kattintson a **megkötések** fülre, és ellenőrizze **engedélyezése az ügyfeleknek, hogy hitelesítés nélkül**.

 ![Az ügyfelek csatlakozni](./media/howto-mfa-nps-extension-rdg/image22.png)

6. Választható lehetőségként kattintson a **feltételek** lapra, és adja hozzá a feltételeket, amelyeknek teljesülniük kell a kapcsolat engedélyezett, ha például egy adott Windows-csoport tagjának kell lennie.

 ![Feltételek](./media/howto-mfa-nps-extension-rdg/image23.png)

7. Kattintson az **OK** gombra. A megfelelő súgó-témakört felkéréskor kattintson **nem**.
8. Győződjön meg arról, hogy az új házirend, hogy a házirend engedélyezve van, a lista tetején, és hozzáférést biztosít.

 ![Hálózati házirendek](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Konfiguráció ellenőrzése
A konfigurációjának ellenőrzéséhez jelentkezzen be a távoli asztali átjáró megfelelő RDP-ügyféllel kell. Ne feledje, hogy engedélyezi-e a kapcsolat engedélyezési házirendeket, és engedélyezve van az Azure MFA a fiók használatára. 

Az alábbi képen a megjelenítése, használhatja a **távoli asztali webes elérés** lap.

![Távoli asztali webes elérés](./media/howto-mfa-nps-extension-rdg/image25.png)

Belépéskor sikeresen megtörtént a hitelesítő adatait az elsődleges hitelesítéshez, a távoli asztali kapcsolat párbeszédpanel állapotát jeleníti meg a távoli kapcsolat kezdeményezése a lent látható módon. 

Sikeres hitelesítést az Azure MFA korábban konfigurált másodlagos hitelesítési módszert, ha csatlakozik a erőforrás. Ha a másodlagos hitelesítés nem sikeres, azonban vannak az erőforráshoz való hozzáférés megtagadva. 

![Távoli kapcsolat kezdeményezése](./media/howto-mfa-nps-extension-rdg/image26.png)

Az alábbi példában a hitelesítő alkalmazás Windows Phone-eszközön segítségével adja meg a másodlagos hitelesítést.

![Fiókok](./media/howto-mfa-nps-extension-rdg/image27.png)

A másodlagos hitelesítési módszerrel sikeresen hitelesítette, miután bejelentkezett a távoli asztali átjáró szokásos módon. Meg kell egy másodlagos hitelesítési módszer használatával, ha megbízható eszközt használja, mivel a napló folyamat biztonságosabb, mint egyébként lenne.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Eseménynaplók sikeres bejelentkezési események megtekintése
A Windows eseménynaplójában a sikeres bejelentkezési események megtekintéséhez a következő Windows PowerShell-parancsot lekérdezéséhez a Windows Terminálszolgáltatások, a Windows biztonsági naplókat adhat ki.

Sikeres bejelentkezési események az átjáró műveleti naplókat lekérdezni _(eseménynapló\alkalmazás és szolgáltatások Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_, a következő PowerShell-parancsokkal:

* _Get-WinEvent - naplónév Microsoft-Windows-TerminalServices-átjáró/működési_ |} ahol {$_.ID - eq "300"} |} FL 
* A parancs a Windows-eseményeket, amelyek megjelenítik a felhasználó teljesülését erőforrás engedélyezési házirend követelményeinek (RD RAP), és nyert hozzáférést jeleníti meg.

![Az Eseménynapló megtekintése](./media/howto-mfa-nps-extension-rdg/image28.png)

* _Get-WinEvent - naplónév Microsoft-Windows-TerminalServices-átjáró/működési_ |} ahol {$_.ID - eq "200"} |} FL
* A parancs megjeleníti az eseményeket, amelyek megjelenítik a felhasználói kapcsolat engedélyezési házirend követelményeinek teljesülése esetén.

![Kapcsolat engedélyezési](./media/howto-mfa-nps-extension-rdg/image29.png)

Ez a napló és szűrő az eseményazonosítót, 300 és 200 is megtekintheti. A biztonsági eseménynapló sikeres bejelentkezés eseményeinek lekérdezéséhez használja a következő parancsot:

* _Get-WinEvent - naplónév biztonsági_ |} ahol {$_.ID - eq "6272"} |} FL 
* Ez a parancs futtatható a központi hálózati házirend-kiszolgáló vagy a távoli asztali átjárókiszolgáló. 

![Sikeres bejelentkezési események](./media/howto-mfa-nps-extension-rdg/image30.png)

Megtekintheti a biztonsági napló vagy a hálózati házirend- és elérési szolgáltatások egyéni nézet alább látható módon:

![Hálózati házirend- és elérési szolgáltatások](./media/howto-mfa-nps-extension-rdg/image31.png)

A kiszolgálón, amelyen a hálózati házirend-kiszolgáló-bővítmény telepítése az Azure MFA található alkalmazás eseménynaplók adott, a bővítmény _alkalmazások és szolgáltatások Logs\Microsoft\AzureMfa_. 

![Az Eseménynapló alkalmazást](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Az útmutató hibaelhárítása

A konfiguráció nem a várt módon működik, ha az első kiindulási elhárítása ellenőrzése, hogy a felhasználó Azure MFA használatára van konfigurálva. A felhasználó lehet csatlakozni a [Azure-portálon](https://portal.azure.com). Ha a felhasználó másodlagos ellenőrzési kéri, és sikeresen tudja elvégezni a hitelesítést, megszüntetheti az Azure MFA helytelen konfigurációban.

Ha a felhasználó Azure MFA szolgáltatás működik, tekintse át a megfelelő eseménynaplóit. Ezek közé tartozik, a biztonsági esemény, az átjáró működik és az előző szakaszban tárgyalt Azure MFA-naplókat. 

Alább egy példa kimenet megjeleníti a sikertelen bejelentkezési esemény (Event ID 6273) a biztonsági napló van.

![Sikertelen bejelentkezési esemény](./media/howto-mfa-nps-extension-rdg/image33.png)

Alább az a AzureMFA naplókból kapcsolódó esemény:

![Az Azure MFA-napló](./media/howto-mfa-nps-extension-rdg/image34.png)

Végrehajtásához advanced beállítások elhárításával kapcsolatos tudnivalókat tekintse meg a hálózati házirend-kiszolgáló naplófájlok ahol a hálózati házirend-kiszolgáló szolgáltatás telepítve van. Ezekben a naplófájlokban létrejönnek _%SystemRoot%\System32\Logs_ vesszővel tagolt szövegfájlok mappában. 

Ezek leírását naplófájlok című [értelmezhetők hálózati házirend-kiszolgáló naplófájlok](https://technet.microsoft.com/library/cc771748.aspx). Ezekben a naplófájlokban szereplő bejegyzések nélkül importálja őket egy táblázatot vagy egy adatbázis értelmezése nehézkes lehet. Több IAS elemzők online segítség nyújtása a naplófájlok értelmezése találja. 

Az alábbi képen láthatók a kimenet egy ilyen letölthető [shareware alkalmazás](http://www.deepsoftware.com/iasviewer). 

![Shareware alkalmazás](./media/howto-mfa-nps-extension-rdg/image35.png)

Végezetül, a további beállítások elhárításával kapcsolatos tudnivalókat, protokollelemző is használhat ilyen [Microsoft Message Analyzert](https://technet.microsoft.com/library/jj649776.aspx). 

A Microsoft Message Analyzert alábbi képen láthatók a RADIUS protokollal a felhasználónevet tartalmazó szűrt forgalmat **Contoso\AliceC**.

![Microsoft Message Analyzert](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>További lépések
[Az Azure Multi-Factor Authentication beszerzése](concept-mfa-licensing.md)

[Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](howto-mfaserver-nps-rdg.md)

[A helyszíni címtárak integrálása az Azure Active Directoryval](../connect/active-directory-aadconnect.md)
