---
title: VPN az Azure MFA-val a n-ps-bővítmény használatával – Azure Active Directory
description: Integrálja a VPN-infrastruktúrát az Azure MFA-val a Microsoft Azure Network Policy Server bővítményhasználatával.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f446f1549b3efcd5f27752fac972dfd80c8650d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425408"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>A VPN-infrastruktúra integrálása az Azure MFA-val az Azure Network Policy Server bővítményének használatával

Az Azure hálózati házirend-kiszolgáló (NPS) bővítménye lehetővé teszi a szervezetek számára, hogy a felhőalapú [Azure multi-factor authentication (MFA)](howto-mfaserver-nps-rdg.md)használatával megvédjék a távoli hitelesítésbe tárcsázó felhasználói szolgáltatás (RADIUS) ügyfélhitelesítését, amely kétlépéses ellenőrzést biztosít.

Ez a cikk a nps-infrastruktúra mfa-val való integrálására vonatkozó utasításokat tartalmaz az Azure-beli nps bővítmény használatával. Ez a folyamat lehetővé teszi a biztonságos kétlépéses ellenőrzést azon felhasználók számára, akik VPN-en keresztül próbálnak csatlakozni a hálózathoz.

A Hálózati házirend- és elérési szolgáltatások lehetővé teszik a szervezetek számára, hogy:

* Központi hely hozzárendelése a hálózati kérelmek kezeléséhez és vezérléséhez a következők meghatározásához:

  * Ki tud csatlakozni

  * Milyen napszakos kapcsolatok engedélyezettek

  * A kapcsolatok időtartama

  * Az ügyfelek által a csatlakozáshoz használt biztonsági szint

    Ahelyett, hogy házirendeket ad meg az egyes VPN- vagy távoli asztali átjárókiszolgálókon, ezt akkor tegye meg, ha egy központi helyen vannak. A RADIUS protokoll a központosított hitelesítés, engedélyezés és számlázás (AAA) biztosításához használható.

* Hozzon létre és kényszerítse nt-ügyfél állapotházirendeket, amelyek meghatározzák, hogy az eszközök korlátlan vagy korlátozott hozzáférést kapnak-e a hálózati erőforrásokhoz.

* A 802.1x-kompatibilis vezeték nélküli hozzáférési pontok és Ethernet-kapcsolók hitelesítésének és engedélyezésének kényszerítése.
  További információt a [Hálózati házirend-kiszolgáló című témakörben talál.](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)

A biztonság növelése és a magas szintű megfelelőség biztosítása érdekében a szervezetek integrálhatják a hálózati kiszolgálót az Azure többtényezős hitelesítésével annak érdekében, hogy a felhasználók kétlépéses ellenőrzést használjanak a VPN-kiszolgáló virtuális portjához való csatlakozáshoz. Ahhoz, hogy a felhasználók hozzáférést kaphassak, meg kell adniuk felhasználónevüket és jelszavukat, valamint az általuk szabályozható egyéb információkat. Ezt az információt megbízhatónak kell tekinteni, és nem lehet könnyen lemásolni. Ez magában foglalhatja a mobiltelefon számát, a vezetékes számot, vagy egy alkalmazás a mobil eszközön.

A hálózati kiszolgáló bővítményének azure-beli rendelkezésre állása előtt azoknak az ügyfeleknek, akik kétlépéses ellenőrzést akartak végrehajtani az integrált hálózati szolgáltatói és mfa-környezetekben, egy külön MFA-kiszolgálót kellett konfigurálniuk és karbantartaniuk egy helyszíni környezetben. Az ilyen típusú hitelesítést a Távoli asztali átjáró és az Azure többtényezős hitelesítési kiszolgáló jalia RADIUS használatával kínálja.

Az Azure-beli NPS-bővítménysel a szervezetek biztosíthatják a RADIUS-ügyfélhitelesítést egy helyszíni MFA-megoldás vagy egy felhőalapú MFA-megoldás telepítésével.

## <a name="authentication-flow"></a>Hitelesítési folyamat

Amikor a felhasználók egy VPN-kiszolgáló virtuális portjához csatlakoznak, először különböző protokollok használatával kell hitelesíteniük magukat. A protokollok lehetővé teszik a felhasználónév és jelszó, valamint a tanúsítványalapú hitelesítési módszerek kombinációját.

A személyazonosságuk hitelesítése és ellenőrzése mellett a felhasználóknak rendelkezniük kell a megfelelő betárcsázási engedélyekkel. Az egyszerű implementációkban a hozzáférést lehetővé tévő betárcsázási engedélyek közvetlenül az Active Directory felhasználói objektumain állíthatók be.

![Betárcsázás lap az Active Directory – felhasználók és számítógépek felhasználói tulajdonságai között](./media/howto-mfa-nps-extension-vpn/image1.png)

Egyszerű implementációk esetén minden VPN-kiszolgáló az egyes helyi VPN-kiszolgálókon definiált házirendek alapján biztosít vagy tagad meg hozzáférést.

A nagyobb és méretezhetőbb implementációkban a VPN-hozzáférést megadó vagy megtagadó házirendek radius-kiszolgálókon vannak központosítva. Ezekben az esetekben a VPN-kiszolgáló hozzáférési kiszolgálóként (RADIUS-ügyfélként) működik, amely a kapcsolatkérelmeket és a fióküzeneteket egy RADIUS-kiszolgálónak továbbítja. A VPN-kiszolgáló virtuális portjához való csatlakozáshoz a felhasználókat hitelesíteni kell, és meg kell felelniük a RADIUS-kiszolgálókon központilag meghatározott feltételeknek.

Ha az Azure-beli nps-bővítmény integrálva van a n-ps-be, a hitelesítési folyamat sikeres eredménye az alábbiak szerint:

1. A VPN-kiszolgáló hitelesítési kérelmet kap egy VPN-felhasználótól, amely tartalmazza az erőforráshoz, például egy távoli asztali munkamenethez való csatlakozáshoz szükséges felhasználónevet és jelszót.
2. A VPN-kiszolgáló RADIUS-ügyfélként játszva a kérelmet *RADIUS-hozzáférés-kérelem* üzenetté alakítja, és (titkosított jelszóval) elküldi annak a RADIUS-kiszolgálónak, amelyen a nps-bővítmény telepítve van.
3. A felhasználónév és a jelszó kombinációját az Active Directory ellenőrzi. Ha a felhasználónév vagy a jelszó helytelen, a *RADIUS-kiszolgáló Access-Reject* üzenetet küld.
4. Ha a hálózati házirend-kiszolgáló kapcsolatkérésében és a hálózati házirendekben megadott összes feltétel teljesül (például napszak- vagy csoporttagsági korlátozások), a hálózati házirend-kiszolgáló bővítmény másodlagos hitelesítési kérelmet indít el az Azure többtényezős hitelesítésével.
5. Az Azure többtényezős hitelesítés kommunikál az Azure Active Directory, lekéri a felhasználó adatait, és végrehajtja a másodlagos hitelesítés a felhasználó által konfigurált módszerrel (mobiltelefon-hívás, szöveges üzenet, vagy mobilalkalmazás).
6. Ha az MFA-kihívás sikeres, az Azure többtényezős hitelesítés közli az eredményt a nps-bővítmény.
7. A csatlakozási kísérlet hitelesítése és engedélyezése után a bővítményt behelyező nps *radius-hozzáférési-elfogadási* üzenetet küld a VPN-kiszolgálónak (RADIUS-ügyfél).
8. A felhasználó hozzáférést kap a VPN-kiszolgáló virtuális portjához, és titkosított VPN-alagutat hoz létre.

## <a name="prerequisites"></a>Előfeltételek

Ez a szakasz részletezi azokat az előfeltételeket, amelyeket be kell fejezni ahhoz, hogy integrálhassa az MFA-t a VPN-nel. Mielőtt elkezdené, rendelkeznie kell a következő előfeltételekkel:

* VPN-infrastruktúra
* Hálózati házirend és hozzáférési szolgáltatások szerepkör
* Azure többtényezős hitelesítési licenc
* Windows Server szoftver
* Kódtárak
* Az Azure Active Directory (Azure AD) szinkronizálva a helyszíni Active Directoryval
* Az Azure Active Directory GUID azonosítója

### <a name="vpn-infrastructure"></a>VPN-infrastruktúra

Ez a cikk feltételezi, hogy a Microsoft Windows Server 2016 rendszert használó működő VPN-infrastruktúrával rendelkezik, és a VPN-kiszolgáló jelenleg nincs konfigurálva a csatlakozási kérelmek RADIUS-kiszolgálónak történő továbbítására. A cikkben a VPN-infrastruktúrát úgy konfigurálja, hogy központi RADIUS-kiszolgálót használjon.

Ha nem rendelkezik működő VPN-infrastruktúrával, gyorsan létrehozhat egyet, ha követi a Microsoft és a külső webhelyeken található számos VPN-beállítási oktatóanyag útmutatását.

### <a name="the-network-policy-and-access-services-role"></a>A Hálózati házirend és a hozzáférési szolgáltatások szerepkör

A Hálózati házirend- és elérési szolgáltatások biztosítják a RADIUS-kiszolgáló és az ügyfél funkciót. Ez a cikk feltételezi, hogy a hálózatházirend- és elérési szolgáltatások szerepkört a környezetében lévő tagkiszolgálóra vagy tartományvezérlőre telepítette. Ebben az útmutatóban a RADIUS-t vpn-konfigurációhoz konfigurálhatja. Telepítse a Hálózati házirend és az Access Services szerepkört a VPN-kiszolgálótól *eltérő* kiszolgálóra.

A Windows Server 2012-es vagy újabb hálózati házirend- és elérési szolgáltatások szerepkör-szolgáltatás telepítéséről a [Hálózatállapot-házirend-kiszolgáló telepítése](https://technet.microsoft.com/library/dd296890.aspx)című témakörben talál további információt. A hálózatvédelem elavult a Windows Server 2016 rendszerben. A hálózati kiszolgálóval kapcsolatos gyakorlati tanácsok ismertetése, beleértve a hálózati kiszolgáló tartományvezérlőre való telepítésének ajánlását, a [Hálózati kiszolgáló gyakorlati tanácsok című](https://technet.microsoft.com/library/cc771746)témakörben található.

### <a name="azure-mfa-license"></a>Azure MFA-licenc

Az Azure többtényezős hitelesítéshez licenc szükséges, és az Azure AD Premium, Enterprise Mobility + Security vagy a Multi-Factor Authentication önálló licencen keresztül érhető el. Az Azure MFA-hoz, például felhasználónkénti vagy hitelesítési licencek fogyasztásalapú licencei nem kompatibilisek a nps-bővítménylel. További információ: [Az Azure többtényezős hitelesítésbe beszerezni.](concept-mfa-licensing.md) Tesztelési célokra próba-előfizetést is használhat.

### <a name="windows-server-software"></a>Windows Server szoftver

A hálózati házirend-kiszolgáló bővítményhez Windows Server 2008 R2 SP1 vagy újabb szükséges, a Hálózati házirend és az Access Services szerepkör rel. Az útmutató összes lépése a Windows Server 2016 rendszerrel történt.

### <a name="libraries"></a>Kódtárak

A következő tárak automatikusan települnek a nps kiterjesztéssel:

-   [Visual C++ terjeszthető csomagok a Visual Studio 2013-hoz (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure Active Directory module for Windows PowerShell 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Ha a Microsoft Azure Active Directory PowerShell-modul még nincs jelen, a telepítési folyamat részeként futtatott konfigurációs parancsfájlnal van telepítve. Nincs szükség a modul idő előtti telepítésére, ha még nincs telepítve.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Az Azure Active Directory szinkronizálva a helyszíni Active Directoryval

A hálózati címzési bővítmény használatához a helyszíni felhasználókat szinkronizálni kell az Azure Active Directoryval, és engedélyezni kell az MFA számára. Ez az útmutató feltételezi, hogy a helyszíni felhasználók szinkronizálva vannak az Azure Active Directoryval az Azure AD Connecten keresztül. Az MFA felhasználóinak engedélyezésére vonatkozó utasításokat az alábbiakban találja.

Az Azure AD Connectről a [Helyszíni könyvtárak integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)című témakörben talál.

### <a name="azure-active-directory-guid-id"></a>Az Azure Active Directory GUID azonosítója

A n-ps-bővítmény telepítéséhez ismernie kell az Azure Active Directory GUID azonosítóját. Az Azure Active Directory GUID-jának megkeresésére vonatkozó utasításokat a következő szakaszban találja.

## <a name="configure-radius-for-vpn-connections"></a>Radius konfigurálása VPN-kapcsolatokhoz

Ha a hálózati házirend-kiszolgáló szerepkört tagkiszolgálóra telepítette, konfigurálnia kell azt a VPN-kapcsolatokat kérő VPN-ügyfél hitelesítéséhez és engedélyezéséhez. 

Ez a szakasz feltételezi, hogy telepítette a Hálózati házirend és az Access Services szerepkört, de nem konfigurálta az infrastruktúrában való használatra.

> [!NOTE]
> Ha már rendelkezik működő VPN-kiszolgálóval, amely központi RADIUS-kiszolgálót használ a hitelesítéshez, kihagyhatja ezt a szakaszt.
>

### <a name="register-server-in-active-directory"></a>Kiszolgáló regisztrálása az Active Directoryban

Ebben az esetben a megfelelő működéshez a n-kiszolgálókiszolgálót regisztrálni kell az Active Directoryban.

1. Nyissa meg a Kiszolgálókezelőt.

2. A Kiszolgálókezelőben válassza az **Eszközök**lehetőséget, majd a **Hálózati házirend-kiszolgáló**lehetőséget.

3. A Hálózati házirend-kiszolgáló konzolon kattintson a jobb gombbal a **hálózati házirend-kiszolgáló (helyi)** elemre, majd válassza **a Kiszolgáló regisztrálása az Active Directoryban parancsot.** Kétszer válassza **az OK** gombot.

    ![Kiszolgáló regisztrálása az Active Directory menüben](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Hagyja nyitva a konzolt a következő eljáráshoz.

### <a name="use-wizard-to-configure-the-radius-server"></a>A RADIUS-kiszolgáló konfigurálása varázslóval

A RADIUS-kiszolgáló konfigurálásához szabványos (varázslóalapú) vagy speciális konfigurációs beállításokat is használhat. Ez a szakasz feltételezi, hogy a varázsló alapú szabványos konfigurációs beállítást használja.

1. A Hálózati házirend-kiszolgáló konzolon válassza a **Hálózati házirend-kiszolgáló (Helyi)** lehetőséget.

2. A **Szokásos konfiguráció csoportban**válassza a **RADIUS-kiszolgáló telefonos vagy VPN-kapcsolatokhoz lehetőséget,** majd válassza **a VPN vagy a Telefonos hálózat konfigurálása**lehetőséget.

    ![RADIUS-kiszolgáló konfigurálása telefonos vagy VPN-kapcsolatokhoz](./media/howto-mfa-nps-extension-vpn/image3.png)

3. A **Telefonos vagy virtuális magánhálózati kapcsolatok típusa ablakban** válassza a **Virtuális magánhálózati kapcsolatok**lehetőséget, majd a **Tovább**gombot.

    ![Virtuális magánhálózati kapcsolatok konfigurálása](./media/howto-mfa-nps-extension-vpn/image4.png)

4. A **Telefonos vagy VPN-kiszolgáló megadása** ablakban válassza a **Hozzáadás lehetőséget.**

5. Az **Új RADIUS-ügyfél** ablakban adjon meg egy rövid nevet, adja meg a VPN-kiszolgáló feloldható nevét vagy IP-címét, majd adjon meg egy közös titkos jelszót. Legyen a megosztott titkos jelszó hosszú és összetett. Rögzítse, mert szüksége lesz rá a következő szakaszban.

    ![Új RADIUS-ügyfélablak létrehozása](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Válassza **az OK**gombot, majd a **Tovább**gombot.

7. A **Hitelesítési módszerek konfigurálása** ablakban fogadja el az alapértelmezett beállítást (**Microsoft Titkosított hitelesítés 2-es verziója [MS-CHAPv2]),** vagy válasszon másik lehetőséget, és válassza a **Tovább**gombot.

    > [!NOTE]
    > Ha az EAP protokollt konfigurálja, a Microsoft Challenge-Handshake Authentication Protocol (CHAPv2) vagy a Protected Extensible Authentication Protocol (PEAP) protokollt kell használnia. Más EAP nem támogatott.

8. A **Felhasználói csoportok megadása** ablakban válassza a **Hozzáadás**lehetőséget, majd válassza ki a megfelelő csoportot. Ha nincs csoport, hagyja üresen a kijelölést, hogy minden felhasználó számára hozzáférést biztosítson.

    ![A hozzáférés engedélyezéséhez vagy megtagadásához a Felhasználói csoportok ablak megadása](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Válassza a **Tovább lehetőséget.**

10. Az **IP-szűrők megadása** ablakban válassza a **Tovább gombot.**

11. A **Titkosítási beállítások megadása** ablakban fogadja el az alapértelmezett beállításokat, és válassza a **Tovább**gombot.

    ![A Titkosítási beállítások megadása ablak](./media/howto-mfa-nps-extension-vpn/image8.png)

12. A **Tartománynév megadása** ablakban hagyja üresen a tartománynevét, fogadja el az alapértelmezett beállítást, és válassza a **Tovább**gombot.

    ![A Birodalom nevének megadása ablak](./media/howto-mfa-nps-extension-vpn/image9.png)

13. Az **Új telefonos vagy virtuális magánhálózati kapcsolatok és RADIUS-ügyfelek** ablakban válassza a **Befejezés**lehetőséget.

    ![Befejeződött konfigurációs ablak](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>A RADIUS-konfiguráció ellenőrzése

Ez a szakasz a varázsló val létrehozott konfigurációt részletezi.

1. A Hálózati házirend-kiszolgáló hálózati házirend-kiszolgálóján a hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki a **RADIUS-ügyfelek csomópontot,** majd válassza a **RADIUS-ügyfelek lehetőséget.**

2. A jobb oldali ablaktáblában kattintson a jobb gombbal a létrehozott RADIUS-ügyfélre, majd válassza a **Tulajdonságok parancsot.** A RADIUS-ügyfél (a VPN-kiszolgáló) tulajdonságainak az itt láthatóhoz hasonlótulajdonságoknak kell lenniük:

    ![A VPN-tulajdonságok és -konfiguráció ellenőrzése](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Válassza a **Mégse** lehetőséget.

4. A Hálózati házirend-kiszolgáló hálózati házirend-kiszolgálóján a hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki a **Házirendek**csomópontot, majd válassza a **Kapcsolatkérelem-házirendek lehetőséget.** A VPN-kapcsolatok házirend az alábbi képen látható módon jelenik meg:

    ![A VPN-kapcsolatházirendet megjelenítő kapcsolatkérelem-házirend](./media/howto-mfa-nps-extension-vpn/image12.png)

5. A **Házirendek**csoportban válassza a **Hálózati házirendek**lehetőséget. A virtuális magánhálózati (VPN) kapcsolatok házirendnek olyan házirendnek kell megjelennie, amely hasonlít az alábbi képen látható házirendre:

    ![Virtuális magánhálózati kapcsolatok házirendet megjelenítő hálózati házirendek](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>A VPN-kiszolgáló konfigurálása RADIUS-hitelesítés használatára

Ebben a szakaszban a VPN-kiszolgálót RADIUS-hitelesítés használatára konfigurálja. Az utasítások feltételezik, hogy a VPN-kiszolgáló működő konfigurációjával rendelkezik, de nem konfigurálta RADIUS-hitelesítés használatára. A VPN-kiszolgáló konfigurálása után ellenőrizze, hogy a konfiguráció a várt módon működik-e.

> [!NOTE]
> Ha már rendelkezik RADIUS-hitelesítést használó működő VPN-kiszolgálókonfigurációval, kihagyhatja ezt a szakaszt.
>

### <a name="configure-authentication-provider"></a>Hitelesítésszolgáltató konfigurálása

1. A VPN-kiszolgálón nyissa meg a Kiszolgálókezelőt.

2. A Kiszolgálókezelőben válassza az **Eszközök**lehetőséget, majd az **Útválasztás és távelérés**lehetőséget.

3. Az **Útválasztás és távelérés** ablakban kattintson a jobb gombbal ** \<a kiszolgáló nevére> (helyi)** elemre, majd válassza **a Tulajdonságok parancsot.**

4. A ** \<kiszolgáló név> (helyi) tulajdonságok** ablakban válassza a **Biztonság** lapot.

5. A **Biztonság** lap **Hitelesítésszolgáltató**csoportjában válassza a **RADIUS-hitelesítés**lehetőséget, majd kattintson a **Konfigurálás gombra.**

    ![RADIUS-hitelesítésszolgáltató konfigurálása](./media/howto-mfa-nps-extension-vpn/image15.png)

6. A **RADIUS-hitelesítés** ablakban válassza a **Hozzáadás lehetőséget.**

7. A **RADIUS-kiszolgáló hozzáadása** ablakban tegye a következőket:

    a. A **Kiszolgáló neve** mezőbe írja be az előző szakaszban konfigurált RADIUS-kiszolgáló nevét vagy IP-címét.

    b. A **Közös titok**hoz lehetőséget válassza a **Módosítás gombra,** majd adja meg a korábban létrehozott és rögzített közös titkos jelszót.

    c. Az **Időtúl (másodperc)** mezőbe írjon be **egy 30**értéket.  
    Az időtúllépése szükséges ahhoz, hogy elegendő idő legyen a második hitelesítési tényező végrehajtásához.

    ![Radius-kiszolgáló hozzáadása ablak az időtúlidőpont konfigurálása](./media/howto-mfa-nps-extension-vpn/image16.png)

8. Válassza **az OK gombot.**

### <a name="test-vpn-connectivity"></a>Vpn-kapcsolat tesztelése

Ebben a szakaszban meggyőződhet arról, hogy a RADIUS-kiszolgáló hitelesítette és engedélyezte-e a VPN-ügyfelet, amikor megpróbál csatlakozni a VIRTUÁLIS VPN-porthoz. Az utasítások feltételezik, hogy a Windows 10-et VPN-ügyfélként használja.

> [!NOTE]
> Ha már beállított egy VPN-ügyfelet a VPN-kiszolgálóhoz való csatlakozásra, és mentette a beállításokat, akkor átugorhatja a VPN-kapcsolatobjektum konfigurálásával és mentésével kapcsolatos lépéseket.
>

1. A VPN-ügyfélszámítógépen válassza a **Start** gombot, majd a **Beállítások** gombot.

2. A **Windows beállítások** ablakban válassza a **Hálózati & internet lehetőséget.**

3. Válassza a **VPN**lehetőséget.

4. Válassza **a VPN-kapcsolat hozzáadása**lehetőséget.

5. A **VPN-kapcsolat hozzáadása** ablak **VPN-szolgáltató** mezőjében válassza a **Windows (beépített)** lehetőséget, végezze el a fennmaradó mezőket, majd válassza a **Mentés lehetőséget.**

    ![A "VPN-kapcsolat hozzáadása" ablak](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Nyissa meg a **Vezérlőpultot,** és válassza **a Hálózati és megosztási központ lehetőséget.**

7. Válassza **az Adapterbeállítások módosítása**lehetőséget.

    ![Hálózati és megosztási központ – Adapterbeállítások módosítása](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Kattintson a jobb gombbal a VPN-hálózati kapcsolatra, majd válassza **a Tulajdonságok parancsot.**

9. A VPN-tulajdonságok ablakban válassza a **Biztonság** lapot.

10. A **Biztonság** lapon győződjön meg arról, hogy csak a **Microsoft CHAP 2-es verziója (MS-CHAP v2)** van kijelölve, majd kattintson **az OK gombra.**

    ![Az "Engedélyezd ezeket a protokollokat" beállítás](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Kattintson a jobb gombbal a VPN-kapcsolatra, majd válassza a **Csatlakozás parancsot.**

12. A **Beállítások** ablakban válassza a **Csatlakozás**lehetőséget.  
    A sikeres kapcsolat a RADIUS-kiszolgáló biztonsági naplójában 6272-es eseményazonosítóként jelenik meg, az alábbiak szerint:

    ![Sikeres kapcsolatot megjelenítő Eseménytulajdonságai ablak](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>RADIUS– hibaelhárítás – hibaelhárítás

Tegyük fel, hogy a VPN-konfiguráció működött, mielőtt konfigurálta a VPN-kiszolgálót egy központi RADIUS-kiszolgáló használatára hitelesítésre és engedélyezésre. Ha a konfiguráció működött, valószínű, hogy a problémát a RADIUS-kiszolgáló helytelen konfigurálása vagy érvénytelen felhasználónév vagy jelszó használata okozza. Ha például az alternatív UPN-utótagot használja a felhasználónévben, a bejelentkezési kísérlet sikertelen lehet. A legjobb eredmény érdekében ugyanazt a fióknevet használja.

A problémák elhárításához ideális kiindulópont a RADIUS-kiszolgáló biztonsági eseménynaplóinak vizsgálata. Az események keresésének időmegtakarításához használja a szerepköralapú hálózati házirend és az Access Server egyéni nézetét az Eseménynaplóban, ahogy az itt látható. A "6273-as azonosítójú" azokat az eseményeket jelöli, amelyekben a nps megtagadta a hozzáférést egy felhasználótól.

![NPAS-eseményeket megjelenítő Eseménynapló](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Többtényezős hitelesítés konfigurálása

A felhasználók többtényezős hitelesítéshez való konfigurálása a [felhőalapú Azure többtényezős hitelesítés telepítésének megtervezése](howto-mfa-getstarted.md#create-conditional-access-policy) és a [fiókom beállítása a kétlépéses ellenőrzéshez című](../user-help/multi-factor-authentication-end-user-first-time.md) cikkekben található.

## <a name="install-and-configure-the-nps-extension"></a>A hálózati kiszolgáló bővítmény telepítése és konfigurálása

Ez a szakasz a VPN-nek a VPN-kiszolgálóval való ügyfélhitelesítéshez való mfa-használatára vonatkozó beállítását ismerteti.

A hálózati házirend-kiszolgáló bővítmény telepítése és konfigurálása után a kiszolgáló által feldolgozott összes RADIUS-alapú ügyfélhitelesítésszükséges az MFA használatához. Ha az összes VPN-felhasználó nincs bejelentkezve az Azure többtényezős hitelesítésében, az alábbi lehetőségek közül választhat:

* Állítson be egy másik RADIUS-kiszolgálót az OnFa használatára konfigurált felhasználók hitelesítésére.

* Hozzon létre egy beállításjegyzék-bejegyzést, amely lehetővé teszi a kihívást okozó felhasználók számára, hogy egy második hitelesítési tényezőt adjanak meg, ha az Azure többtényezős hitelesítésben vannak bejelentve.

Hozzon létre egy REQUIRE_USER_MATCH nevű új karakterláncértéket _a HKLM\SOFTWARE\Microsoft\AzureMfa mappában,_ és állítsa az értéket *Igaz* vagy *Hamis*értékre.

![A "Felhasználói egyezés megkövetelése" beállítás](./media/howto-mfa-nps-extension-vpn/image34.png)

Ha az érték *Igaz* vagy üres, az összes hitelesítési kérelem egy MFA-kihívás hatálya alá tartozik. Ha az érték *értéke Hamis,* az MFA-kihívások csak az Azure többtényezős hitelesítésben regisztrált felhasználók számára kerülnek kiadásra. A *Hamis* beállítást csak teszteléskor vagy éles környezetben használja a bevezetési időszak alatt.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Az Azure Active Directory GUID-azonosítójának beszerzése

A hálózati cím-szolgáltató bővítmény konfigurációjának részeként rendszergazdai hitelesítő adatokat és az Azure AD-bérlő azonosítóját kell megadnia. Szerezze be az azonosítót az alábbi módon:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure-bérlő globális rendszergazdájaként.

2. Az Azure Portal menüben válassza az **Azure Active Directory**, vagy keressen, és válassza az Azure Active **Directory** bármely lapon.

3. Válassza **a Tulajdonságok lehetőséget.**

4. Az Azure AD-azonosító másolásához válassza a **Másolás gombot.**

    ![Az Azure AD címtárazonosítója az Azure Portalon](./media/howto-mfa-nps-extension-vpn/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>A nps-bővítmény telepítése

A hálózati házirend-kiszolgáló bővítményét olyan kiszolgálóra kell telepíteni, amelyen telepítve van a Hálózati házirend és az Access Services szerepkör, és amely a tervben RADIUS-kiszolgálóként működik. *Ne* telepítse a nps bővítményt a VPN-kiszolgálóra.

1. Töltse le a nps bővítményt a [Microsoft letöltőközpontjából.](https://aka.ms/npsmfa)

2. Másolja a telepítő végrehajtható fájlt (*NpsExtnForAzureMfaInstaller.exe*) a nps-kiszolgálóra.

3. A nps serveren kattintson duplán a **NpsExtnForAzureMfaInstaller.exe ikonra,** és ha a rendszer kéri, válassza a **Futtatás lehetőséget.**

4. Az **NPS Extension For Azure MFA setup** ablakban tekintse át a szoftverlicenc-feltételeket, jelölje be az **Elfogadom a licencfeltételeket** jelölőnégyzetet, majd válassza a **Telepítés**lehetőséget.

    ![Az "NPS extension for Azure MFA Setup" ablak](./media/howto-mfa-nps-extension-vpn/image36.png)

5. Az **NPS-bővítmény az Azure MFA telepítőablakában** válassza a **Bezárás**lehetőséget.  

    ![A "Sikeres beállítás" megerősítési ablak](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Tanúsítványok konfigurálása a hálózati kiszolgálóbővítményhez PowerShell-parancsfájl használatával

A biztonságos kommunikáció és megbízhatóság biztosítása érdekében konfigurálja a tanúsítványokat a hálózati kiszolgáló bővítmény általi használatra. A hálózati kiszolgáló összetevői tartalmaznak egy Windows PowerShell-parancsfájlt, amely konfigurálja az önaláírt tanúsítványt a hálózati kiszolgálóval való használatra.

A parancsfájl a következő műveleteket hajtja végre:

* Önaláírt tanúsítványt hoz létre.
* A tanúsítvány nyilvános kulcsát társítja az Azure AD egyszerű szolgáltatásához.
* A tanúsítványt a helyi géptárolóban tárolja.
* Hozzáférést biztosít a hálózati felhasználónak a tanúsítvány személyes kulcsához.
* Újraindítja a nps szolgáltatást.

Ha saját tanúsítványokat szeretne használni, társítania kell a tanúsítvány nyilvános kulcsát az Azure AD szolgáltatásnévhez, és így tovább.

A parancsfájl használatához adja meg a bővítményt az Azure Active Directory felügyeleti hitelesítő adataival és az Azure Active Directory-bérlői azonosítóval, amelyet korábban másolt. Futtassa a parancsfájlt minden olyan nps-kiszolgálón, amelyen telepíti a n-ps bővítményt.

1. Futtassa a Windows PowerShellt rendszergazdaként.

2. A PowerShell parancssorába írja be a **"c:\Program Files\Microsoft\AzureMfa\Config" cd-t,** majd válassza az Enter lehetőséget.

3. A következő parancssorba írja be a **.\AzureMfaNpsExtnConfigSetup.ps1 parancsot,** majd válassza az Enter lehetőséget. A parancsfájl ellenőrzi, hogy az Azure AD PowerShell-modul telepítve van-e. Ha nincs telepítve, a parancsfájl telepíti a modult.

    ![Az AzureMfsNpsExtnConfigSetup.ps1 konfigurációs parancsfájl futtatása](./media/howto-mfa-nps-extension-vpn/image38.png)

    Miután a parancsfájl ellenőrzi a PowerShell-modul telepítését, megjeleníti az Azure Active Directory PowerShell modul bejelentkezési ablakát.

4. Adja meg az Azure AD rendszergazdai hitelesítő adatait és jelszavát, majd válassza **a Bejelentkezés**lehetőséget.

    ![Hitelesítés az Azure AD PowerShell ben](./media/howto-mfa-nps-extension-vpn/image39.png)

5. A parancssorba illessze be a korábban másolt bérlői azonosítót, és válassza az Enter lehetőséget.

    ![Az Azure AD címtárazonosító nak a beírása, amelyet korábban másoltak](./media/howto-mfa-nps-extension-vpn/image40.png)

    A parancsfájl önaláírt tanúsítványt hoz létre, és egyéb konfigurációs módosításokat hajt végre. A kimenet a következő képen láthatóhoz hasonló:

    ![PowerShell-ablak, amely önaláírt tanúsítványt jelenít meg](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Indítsa újra a kiszolgálót.

### <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése

A konfiguráció ellenőrzéséhez új VPN-kapcsolatot kell létesítenie a VPN-kiszolgálóval. Miután sikeresen megadta a hitelesítő adatait az elsődleges hitelesítéshez, a VPN-kapcsolat megvárja, amíg a másodlagos hitelesítés sikeres lesz a kapcsolat létrehozása előtt, az alábbiak szerint.

![A Windows beállítások VPN ablaka](./media/howto-mfa-nps-extension-vpn/image42.png)

Ha sikeresen hitelesíti a másodlagos ellenőrzési módszert, amely et korábban konfigurált az Azure MFA-ban, akkor csatlakozik az erőforráshoz. Ha azonban a másodlagos hitelesítés sikertelen, a rendszer nem fér hozzá az erőforráshoz.

A következő példában a Windows Phone-telefon Microsoft Authenticator alkalmazása biztosítja a másodlagos hitelesítést:

![Példa MFA-kérdés Windows Phone-telefonon](./media/howto-mfa-nps-extension-vpn/image43.png)

Miután sikeresen hitelesítette magát a másodlagos módszerrel, hozzáférést kap a VPN-kiszolgáló virtuális portjához. Mivel egy mobilalkalmazás használatával másodlagos hitelesítési módszert kellett használnia egy megbízható eszközön, a bejelentkezési folyamat biztonságosabb, mintha csak felhasználónév- és jelszókombinációt használna.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Az Eseménynapló sikeres bejelentkezési eseményeinek megtekintése

A Windows Eseménynapló naplóiban a sikeres bejelentkezési események megtekintéséhez a Windows biztonsági naplót a hálózati kiszolgálón a következő PowerShell-parancs megadásával szeretné lekérdezni:

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![PowerShell biztonsági eseménynaplója](./media/howto-mfa-nps-extension-vpn/image44.png)

A biztonsági naplót vagy a Hálózati házirend és az Access Services egyéni nézetét is megtekintheti, ahogy az itt látható:

![Példa hálózati házirend-kiszolgáló naplójára](./media/howto-mfa-nps-extension-vpn/image45.png)

Azon a kiszolgálón, ahol telepítette a nps bővítményt az Azure többtényezős hitelesítéshez, az *Alkalmazás- és szolgáltatásnaplók\Microsoft\AzureMfa*mappában megtalálhatja a bővítményre jellemző Eseménynapló-alkalmazásnaplókat.

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Példa az Eseménynapló Hitelesítési ablaktáblájára](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Ha a konfiguráció nem a várt módon működik, kezdje el a hibaelhárítást annak ellenőrzésével, hogy a felhasználó be van-e állítva az MFA használatára. A felhasználó csatlakozzon az [Azure Portalhoz.](https://portal.azure.com) Ha a rendszer másodlagos hitelesítést kér a felhasználótól, és sikeresen hitelesítheti magát, kiküszöbölheti az MFA helytelen konfigurációját problémaként.

Ha az MFA a felhasználószámára működik, tekintse át a megfelelő Eseménynapló-naplókat. A naplók közé tartozik a biztonsági esemény, átjáró működési és az Azure többtényezős hitelesítési naplók, amelyek az előző szakaszban tárgyalt.

Egy példa egy biztonsági naplóra, amely egy sikertelen bejelentkezési eseményt (6273-as eseményazonosító) jelenít meg, itt látható:

![Sikertelen bejelentkezési eseményt megjelenítő biztonsági napló](./media/howto-mfa-nps-extension-vpn/image47.png)

Az Azure többtényezős hitelesítési naplójának egy kapcsolódó eseménye itt látható:

![Az Azure többtényezős hitelesítési naplói](./media/howto-mfa-nps-extension-vpn/image48.png)

A speciális hibaelhárításhoz tanulmányozza a hálózati kiszolgáló adatbázisformátumú naplófájljait, ahol a hálózati kiszolgáló szolgáltatás telepítve van. A naplófájlok a _%SystemRoot%\System32\Logs_ mappában vesszővel tagolt szövegfájlként jönnek létre. A naplófájlok leírását a [NPS adatbázis-formátumú naplófájlok értelmezése című témakörben található.](https://technet.microsoft.com/library/cc771748.aspx)

A naplófájlok bejegyzéseinehezen értelmezhetők, hacsak nem exportálja őket számolótáblába vagy adatbázisba. Számos Internetes hitelesítési szolgáltatás (IAS) elemzési eszköz található az interneten, amelyek segítenek a naplófájlok értelmezésében. A kimenet egy ilyen letölthető [shareware alkalmazás](https://www.deepsoftware.com/iasviewer) itt látható:

![Shareware-mintaalkalmazás IAS-elemző](./media/howto-mfa-nps-extension-vpn/image49.png)

További hibaelhárításhoz használhat protokollelemzőt, például wireshark-ot vagy [Microsoft Message Analyzer-t.](https://technet.microsoft.com/library/jj649776.aspx) A Wireshark következő képe a VPN-kiszolgáló és a n-kiszolgáló közötti RADIUS-üzeneteket jeleníti meg.

![A Szűrt forgalmat megjelenítő Microsoft Message Analyzer](./media/howto-mfa-nps-extension-vpn/image50.png)

További információ: [A meglévő nps-infrastruktúra integrálása az Azure többtényezős hitelesítéssel](howto-mfa-nps-extension.md)című témakörben talál.

## <a name="next-steps"></a>További lépések

[Az Azure többtényezős hitelesítésének beszereznie](concept-mfa-licensing.md)

[Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](howto-mfaserver-nps-rdg.md)

[A helyszíni címtárak integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
