---
title: A hálózati házirend-kiszolgáló bővítmény használatával VPN integrálása az Azure MFA |} A Microsoft Docs
description: A VPN-infrastruktúra integrálása az Azure MFA a hálózati házirend-kiszolgáló bővítmény a Microsoft Azure segítségével.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 08/15/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: cd59e7956855de52750614c800a46e6bf817cd14
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054777"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>A VPN-infrastruktúra integrálása az Azure MFA által a hálózati házirend-kiszolgáló-bővítmény használata az Azure-hoz

## <a name="overview"></a>Áttekintés

A hálózati házirend-kiszolgáló (NPS) kiterjesztése az Azure lehetővé teszi, hogy a szervezetek a védelme érdekében távoli Authentication Dial-In User Service (RADIUS) ügyfél-hitelesítés használatával a felhő alapú [Azure multi-factor Authentication (MFA)](howto-mfaserver-nps-rdg.md), amely biztosítja a kétlépéses ellenőrzést.

Ez a cikk útmutatást az NPS-bővítményének használatával az Azure MFA NPS-infrastruktúrával való integrálásának. Ez a folyamat lehetővé teszi, hogy a felhasználók, akik megpróbálnak csatlakozni a hálózathoz egy VPN-en keresztül biztonságos kétlépéses ellenőrzést. 

Hálózati házirend- és elérési szolgáltatások révén a szervezetek lehetővé teszi:

* Rendelje hozzá a felügyeleti és a hálózati kérelmek, adja meg a vezérlő egy központi helyen:

    * Kapcsolódást 
    
    * Milyen alkalommal nap-kapcsolatok engedélyezettek. 
    
    * A kapcsolatok időtartama
    
    * Biztonság, hogy az ügyfelek csatlakozni kell használnia.

    Helyett adja meg a szabályzatok minden VPN- vagy a távoli asztali átjáró kiszolgálón, ezt követően egy központi helyen jelenleg. A RADIUS protokollal segítségével adja meg, központosított hitelesítési, engedélyezési és nyilvántartási (AAA). 

* Hozzon létre, és érvényesíti a hálózatvédelem (NAP) ügyfél állapotházirendeket, amelyek meghatározzák, hogy eszközök, amelyekhez hozzáférést korlátozás nélküli vagy korlátozott a hálózati erőforrásokhoz.

* Kényelmesen kényszerítése hitelesítését és engedélyezését a hozzáférést a 802.1 x-kompatibilis vezeték nélküli hozzáférési pontok és Ethernet-kapcsolók.   
További információkért lásd: [hálózati házirend-kiszolgáló](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

Növelje a biztonságot, és adja meg a megfelelőség magas szintű, szervezetek integrálható hálózati házirend-kiszolgáló az Azure multi-factor Authentication szolgáltatást, győződjön meg arról, hogy a felhasználók a kétlépéses ellenőrzést szeretne csatlakozni a virtuális port a VPN-kiszolgálón használja. A felhasználók számára hozzáférést biztosítani a felhasználónév és jelszó kombináció és egyéb információkat, amelyek az általuk kell adnia. Ezeket az információkat megbízható legyen, és nem nehezen másolható. A mobiltelefonszámát, mobiltelefonra szám vagy egy alkalmazást a mobileszközökön is alkalmas.

Az NPS-bővítményt, az Azure-ban rendelkezésre állását, mielőtt ügyfelek, akik szeretne megvalósítani a kétlépéses ellenőrzést a hálózati házirend-kiszolgáló integrált, és MFA környezetek kellett konfigurálnia és karbantartania egy külön MFA-kiszolgáló egy a helyszíni környezetben. Ilyen típusú hitelesítés a távoli asztali átjáró és az Azure multi-factor Authentication kiszolgáló RADIUS-t használó kínálják.

Az NPS-bővítményt az Azure-hoz, a szervezetek gondoskodhat a RADIUS-ügyfél-hitelesítés vagy egy helyszíni MFA-megoldást, vagy egy MFA felhőalapú megoldás üzembe helyezése.
 
## <a name="authentication-flow"></a>A hitelesítési folyamatból
Amikor a felhasználók csatlakoznak egy virtuális port a VPN-kiszolgáló, először hitelesíteniük kell különböző protokollok használatával. A protokollok engedélyezése a felhasználónév és jelszó és a tanúsítványalapú hitelesítési módszerek kombinációját. 

Felhasználók hitelesítése és hitelesítik az identitásukat, mellett a megfelelő betárcsázási engedélyekkel kell rendelkeznie. Olyan egyszerű megvalósításokhoz, a telefonos hozzáférést engedélyező vannak beállítva közvetlenül a felhasználó Active Directory-objektumok. 

![Felhasználói tulajdonságok](./media/howto-mfa-nps-extension-vpn/image1.png)

Az olyan egyszerű megvalósításokhoz minden egyes VPN-kiszolgáló engedélyezi vagy megtagadja a hozzáférést az egyes helyi VPN-kiszolgáló meghatározott szabályzatok alapján.

A nagyobb és jobban skálázható megvalósításokban a házirendekben, amelyek a VPN-hozzáférés engedélyezése vagy megtagadása központilag, RADIUS-kiszolgálók. Ezekben az esetekben a VPN-kiszolgáló-hozzáférési kiszolgáló (a RADIUS-ügyfél), amely továbbítja a kapcsolódási kérelmeket és a egy RADIUS-kiszolgáló fiók üzenetek funkcionál. Szeretne csatlakozni a virtuális port a VPN-kiszolgálón, a felhasználók kell hitelesíteni, és a RADIUS-kiszolgálók központilag meghatározott feltételeknek. 

Ha a hálózati házirend-kiszolgáló Azure-bővítmény integrálva van a hálózati házirend-kiszolgáló, a sikeres hitelesítési folyamat eredménye, a következő:

1. A VPN-kiszolgáló-hitelesítési kérést kap egy VPN-felhasználó, amely tartalmazza a felhasználónevet és jelszót egy erőforrást, például a távoli asztali munkamenetet való kapcsolódáshoz. 

2. RADIUS-ügyfélként működő, a VPN-kiszolgáló alakítja át a kérelem egy RADIUS *hozzáférési-kérelem* üzenetet, és elküldi (a titkosított jelszót) a RADIUS-kiszolgáló amelyen telepítve van-e az NPS bővítményével. 

3. A felhasználónév és jelszó kombinációjával ellenőrzése az Active Directoryban. A felhasználónév vagy jelszó helytelen, ha a RADIUS-kiszolgáló elküldi az *Access-Reject* üzenet. 

4. Ha az összes, a hálózati házirend-kiszolgáló kapcsolódási kérelem és a hálózati házirendeket, a megadott feltételek (például időpont vagy csoport tagsági korlátozások), az NPS-bővítményének elindítja az Azure multi-factor Authentication másodlagos hitelesítési kérést. 

5. Az Azure multi-factor Authentication kommunikál az Azure Active Directoryval, a felhasználó adatait kérdezi le, és a másodlagos hitelesítést végez a metódussal, amely a felhasználó (cella telefonhívás, szöveges üzenet vagy mobilalkalmazás) van konfigurálva. 

6. Ha az MFA-hitelesítést sikeres volt, az Azure multi-factor Authentication kommunikál az NPS-bővítményt, az eredmény.

7. Után a csatlakozási kísérlet egyaránt hitelesítése és engedélyezése, ahol a bővítmény telepítve van-e a hálózati házirend-kiszolgáló küld a RADIUS *hozzáférés engedélyező* az üzenethez, és a VPN-kiszolgáló (a RADIUS-ügyfél).

8. A felhasználó hozzáférést kap a virtuális port a VPN-kiszolgálón, és a egy titkosított VPN-alagutat létesít.

## <a name="prerequisites"></a>Előfeltételek
Ez a szakasz részletesen az Előfeltételek, amely a távoli asztali átjáró is integrálhatja a többtényezős hitelesítés előtt kell elvégezni. Mielőtt elkezdené, helyben kell rendelkeznie a következő előfeltételek vonatkoznak:

* VPN-infrastruktúra
* Hálózati házirend- és elérési szolgáltatások szerepkör
* Az Azure multi-factor Authentication-licenc
* A Windows Servert
* Kódtárak
* Az Azure Active Directory (Azure AD) szinkronizált a helyszíni Active Directory 
* Azure Active Directory GUID ID

### <a name="vpn-infrastructure"></a>VPN-infrastruktúra
Ez a cikk azt feltételezi, hogy rendelkezik egy működő VPN-infrastruktúrát, amely a Microsoft Windows Server 2016 és, hogy a VPN-kiszolgáló jelenleg nincsenek beállítva a továbbítási kapcsolat kéréseket egy RADIUS-kiszolgáló. A cikk a VPN-infrastruktúra központi RADIUS-kiszolgáló használatára konfigurálja.

Ha helyben nem rendelkezik egy működő VPN-infrastruktúrát, gyorsan létrehozhat egyet a Microsoft és harmadik felek webhelyeire számos annak VPN beállítása oktatóanyagok található útmutatást követve. 
            
### <a name="the-network-policy-and-access-services-role"></a>A hálózati házirend- és elérési szolgáltatások szerepkör

Hálózati házirend- és elérési szolgáltatások a RADIUS-kiszolgáló és ügyfél funkciót biztosít. Ez a cikk azt feltételezi, hogy telepítette a hálózati házirend- és elérési szolgáltatások szerepkört egy olyan tagkiszolgáló vagy tartományvezérlő a környezetben. Ebben az útmutatóban egy VPN-konfiguráció RADIUS konfigurálja azt. A hálózati házirend- és elérési szolgáltatások szerepkör telepítése egy kiszolgálóra *eltérő* a VPN-kiszolgáló.

A hálózati házirend- és elérési szolgáltatások szerepkör telepítésével kapcsolatos információkat szolgáltatás Windows Server 2012 vagy újabb, lásd: [egy NAP állapotházirend-kiszolgálókon telepítse](https://technet.microsoft.com/library/dd296890.aspx). NAP elavulttá vált a Windows Server 2016-ban. Ajánlott eljárások a hálózati házirend-kiszolgáló, beleértve azt javasoljuk, hogy a hálózati házirend-kiszolgáló telepítése egy tartományvezérlőn leírását lásd: [ajánlott eljárások az NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Az Azure MFA-licenc

-Licencre szükség az Azure multi-factor Authentication, és a prémium szintű Azure AD, Enterprise Mobility + Security vagy a multi-factor Authentication önálló licenc segítségével érhető el. Fogyasztásalapú licenceinek például az Azure MFA felhasználónkénti vagy hitelesítési licencekkel nem kompatibilisek az NPS-bővítményt. További információkért lásd: [beszerzése az Azure multi-factor Authentication](concept-mfa-licensing.md). Tesztelési célokra használhatja egy próba-előfizetést.

### <a name="windows-server-software"></a>A Windows Servert

Az NPS-bővítményének megköveteli a Windows Server 2008 R2 SP1 vagy újabb verzió, a hálózati házirend- és elérési szolgáltatások szerepkör telepítve van. Ez az útmutató összes lépését a Windows Server 2016-ra lettek végrehajtva.

### <a name="libraries"></a>Kódtárak

A következő kódtárakra az NPS-bővítményéhez automatikusan települnek:

-   [Visual C++ újraterjeszthető csomag a Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [A Microsoft Azure Active Directory modul a Windows PowerShell 1.1.166.0-s](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

A Microsoft Azure Active Directory PowerShell-modul még nem létezik, ha telepíti a rendszer a konfigurációs parancsfájl, amely a telepítési folyamat részeként futtatja. Hiba esetén nem kell előre-modul telepítéséhez, ha nem telepítette.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Az Azure Active Directory a helyszíni Active Directoryval szinkronizált 

Az NPS-bővítményének használatához a helyszíni felhasználók legyen szinkronizálva az Azure Active Directoryval, és engedélyezve van az MFA-hoz. Ez az útmutató feltételezi, hogy a helyszíni felhasználók Azure AD connectben Azure Active Directoryval szinkronizált. Útmutatás a felhasználók a multi-factor Authentication lehetővé teszi az alábbiakban találhatók.

Az Azure AD Connect kapcsolatos információkért lásd: [a helyszíni címtárak integrálása az Azure Active Directory](../connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID ID 

Az NPS-bővítményének telepítése, az Azure Active Directory GUID-ismernie kell. Az Azure Active Directory GUID-kereséséhez utasításokat a következő szakaszban.

## <a name="configure-radius-for-vpn-connections"></a>VPN-kapcsolatok RADIUS konfigurálása

Ha már telepítette az NPS szerepkör azon a tagkiszolgálón, kell konfigurálnia, hogy a hitelesítés és engedélyezés a VPN-ügyfél a kérelmek VPN-kapcsolatok. 

Ez a szakasz azt feltételezi, hogy telepítve van a hálózati házirend- és elérési szolgáltatások szerepkört, de nem konfigurált, használja az infrastruktúra.

> [!NOTE]
> Ha már rendelkezik egy működő VPN-kiszolgálót, amely egy központi RADIUS-kiszolgálót használja a hitelesítéshez, kihagyhatja ezt a szakaszt.
>

### <a name="register-server-in-active-directory"></a>Regisztrálja a kiszolgálót az Active Directoryban
Ebben a forgatókönyvben megfelelő működéséhez, regisztrálni kell az NPS-kiszolgáló az Active Directoryban.

1. Nyissa meg a Kiszolgálókezelőt.

2. A Kiszolgálókezelőben válasza **eszközök**, majd válassza ki **hálózati házirend-kiszolgáló**. 

3. A hálózati házirend-kiszolgáló-konzolon kattintson a jobb gombbal **hálózati házirend-kiszolgáló (helyi)**, majd válassza ki **kiszolgáló regisztrálása az Active Directoryban**. Válassza ki **OK** kétszer.

    ![Hálózati házirend-kiszolgáló](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Hagyja nyitva a következő eljárással a konzolon.

### <a name="use-wizard-to-configure-the-radius-server"></a>A RADIUS-kiszolgáló konfigurálása varázsló segítségével
Használhat egy standard (varázsló-alapú) vagy a speciális konfigurációs beállítás, a RADIUS-kiszolgáló konfigurálása. Ez a szakasz azt feltételezi, hogy a varázsló alapú szabványos konfigurációs beállítást használja.

1. Válassza ki a hálózati házirend-kiszolgáló konzol **hálózati házirend-kiszolgáló (helyi)**.

2. Alatt **szabványos konfigurációs**válassza **RADIUS-kiszolgáló telefonos vagy VPN-kapcsolatok**, majd válassza ki **konfigurálása VPN vagy a telefonos**.

    ![Virtuális Magánhálózat konfigurálása](./media/howto-mfa-nps-extension-vpn/image3.png)

3. Az a **válassza ki a telefonos vagy virtuális magánhálózati kapcsolatok hálózattípus** ablakban válassza ki **virtuális magánhálózati kapcsolatok**, majd válassza ki **tovább**.

    ![Virtuális magánhálózat](./media/howto-mfa-nps-extension-vpn/image4.png)

4. Az a **megadása telefonos vagy VPN-kiszolgáló** ablakban válassza **Hozzáadás**.

5. Az a **új RADIUS-ügyfél** ablakban adjon meg egy kifejező nevet, adja meg a VPN-kiszolgáló IP-címét vagy feloldható nevét, és adja meg a közös titkos jelszavát. Győződjön meg a közös titkos jelszavát a hosszú és összetett. Jegyezze fel, mert a következő szakaszban szüksége lesz rá.

    ![Új RADIUS-ügyfél](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Válassza ki **OK**, majd válassza ki **tovább**.

7. Az a **hitelesítési módszerek konfigurálása** ablakban fogadja el az alapértelmezésként beállított elemet (**Microsoft titkosított hitelesítési [MS-CHAPv2] 2-es verzió)** vagy egy másik lehetőséget, és válasszon **tovább** .

    > [!NOTE]
    > Ha konfigurálja az Extensible Authentication Protocol (EAP), vagy a Microsoft kérdés-kézfogás típusú hitelesítési protokoll (CHAPv2), vagy a védett bővíthető hitelesítési protokoll (PEAP) kell használnia. Nincs más EAP használata támogatott.
 
8. Az a **adja meg a felhasználói csoportok** ablakban válassza **Hozzáadás**, majd válassza ki a megfelelő csoporthoz. Ha nincs csoport már létezik, hagyja üresen hozzáférést biztosítani az összes felhasználó a kijelölést.

    ![A felhasználói csoportok megadása ablak](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Kattintson a **Tovább** gombra.

10. Az a **adjon meg IP-szűrők** ablakban válassza **tovább**.

11. Az a **adja meg a titkosítási beállítások** ablakban fogadja el az alapértelmezett beállításokat, és válassza ki **tovább**.

    ![Az adja meg a titkosítási beállítások ablak](./media/howto-mfa-nps-extension-vpn/image8.png)

12. Az a **tartománynév megadása** ablakban hagyja üresen a tartománynevet, fogadja el az alapértelmezett beállítás, és válassza **tovább**.

    ![Adjon meg egy tartománynevet ablak](./media/howto-mfa-nps-extension-vpn/image9.png)

13. Az a **befejezése új telefonos vagy virtuális magánhálózati kapcsolatok és RADIUS-ügyfelek** ablakban válassza **Befejezés**.

    ![A "Befejezés új telefonos vagy virtuális magánhálózati kapcsolatok és a RADIUS-ügyfelek" ablak](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>A RADIUS-konfiguráció ellenőrzése
Ez a szakasz részletesen a varázslóval létrehozott konfigurációt.

1. A hálózati házirend-kiszolgáló, a hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki a **RADIUS-ügyfelek**, majd válassza ki **RADIUS-ügyfelek**.

2. A részleteket tartalmazó ablaktáblán kattintson jobb gombbal a létrehozott, és válassza ki a RADIUS-ügyfél **tulajdonságok**. A RADIUS-ügyfél (VPN-kiszolgáló) tulajdonságainak kell lennie a hasonló itt látható:

    ![VPN-tulajdonságok](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Válassza ki **Mégse**.

4. A hálózati házirend-kiszolgáló, a hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki a **házirendek**, majd válassza ki **kapcsolatkérelem-házirendek**. A VPN-kapcsolatok házirend megjelenik-e az alábbi képen látható módon:

    ![Csatlakozási kérelmek](./media/howto-mfa-nps-extension-vpn/image12.png)

5. A **házirendek**válassza **hálózati házirendek**. Egy virtuális magánhálózati (VPN) kapcsolatok szabályzatot, amely hasonlít az alábbi képen látható a házirendet kell megjelennie:

    ![Hálózati házirendek](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Konfigurálja a VPN-kiszolgáló RADIUS-hitelesítés használata
Ez a szakasz a VPN-kiszolgáló RADIUS-hitelesítés használatára konfigurálja. Az utasítások feltételezik, hogy egy VPN-kiszolgáló működő konfigurációval rendelkezik, de nem konfigurált RADIUS-hitelesítés használatára. Után konfigurálja a VPN-kiszolgálót, győződjön meg arról, hogy a várt módon működik-e a konfiguráció.

> [!NOTE]
> Ha már rendelkezik egy működő VPN-kiszolgáló beállítása, amely a RADIUS-hitelesítést használ, ezt a szakaszt kihagyhatja.
>

### <a name="configure-authentication-provider"></a>Hitelesítési szolgáltató konfigurálása
1. A VPN-kiszolgálón nyissa meg a Kiszolgálókezelőt.

2. A Kiszolgálókezelőben válasza **eszközök**, majd válassza ki **Útválasztás és távelérés**.

3. Az a **Útválasztás és távelérés** ablakban kattintson a jobb gombbal  **\<kiszolgáló neve > (helyi)**, majd válassza ki **tulajdonságok**.

    ![Az Útválasztás és távelérés szolgáltatás ablak](./media/howto-mfa-nps-extension-vpn/image14.png)
 
4. Az a  **\<kiszolgáló neve > (helyi) tulajdonságok** ablakban válassza ki a **biztonsági** fülre. 

5. Az a **biztonsági** lap **hitelesítési szolgáltató**válassza **RADIUS-hitelesítés**, majd válassza ki **konfigurálása**.

    ![RADIUS-hitelesítés](./media/howto-mfa-nps-extension-vpn/image15.png)
 
6. Az a **RADIUS-hitelesítés** ablakban válassza **Hozzáadás**.

7. Az a **RADIUS-kiszolgáló hozzáadása** ablakban tegye a következőket:

    a. Az a **kiszolgálónév** mezőbe írja be a nevét vagy az előző szakaszban konfigurált RADIUS-kiszolgáló IP-címét.

    b. Az a **közös titkos kulcsot**válassza **módosítása**, majd írja be a közös titkos jelszót létrehozott, és a korábban feljegyzett.

    c. Az a **időtúllépése (másodperc)** mezőben kiválaszthat egy értéket **30** keresztül **60**.  
    Időtúllépés értéke elegendő időt a hitelesítés második tényezőjét engedélyezéséhez szükséges.
 
    ![A RADIUS-kiszolgáló hozzáadása ablak](./media/howto-mfa-nps-extension-vpn/image16.png)
 
8. Kattintson az **OK** gombra.

### <a name="test-vpn-connectivity"></a>VPN-kapcsolat tesztelése
Ebben a szakaszban Ön kijelenti, hogy a VPN-ügyfél hitelesítése és engedélyezése a RADIUS-kiszolgáló, amikor megpróbál csatlakozni a VPN virtuális port. Az utasítások feltételezik, hogy, hogy használja a Windows 10-es VPN-ügyfélként. 

> [!NOTE]
> Ha már konfigurált egy VPN-ügyfél a VPN-kiszolgálóhoz csatlakozhat, és mentette a beállítások, konfigurálása és a egy VPN-kapcsolati objektum mentése kapcsolatos lépéseket kihagyhatja.
>

1. A VPN-ügyfél, válassza ki a **Start** gombra, és válassza ki a **beállítások** gombra.

2. Az a **Windows beállítások** ablakban válassza **hálózat és Internet**.

3. Válassza ki **VPN**.

4. Válassza ki **egy VPN-kapcsolat hozzáadása**.

5. Az a **egy VPN-kapcsolat hozzáadása** ablakban, a a **VPN-szolgáltató** jelölje ki **Windows (beépített)**, a többi mezőhöz, szükség esetén végezze el, és válassza ki **Mentése**. 

    ![A "VPN-kapcsolat hozzáadása" ablak](./media/howto-mfa-nps-extension-vpn/image17.png)
 
6. Lépjen a **Vezérlőpult**, majd válassza ki **hálózati és megosztási központ**.

7. Válassza ki **Adapterbeállítások módosítása**.

    ![Adapterbeállítások módosítása](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Kattintson a jobb gombbal a VPN-hálózati kapcsolat, és válassza **tulajdonságok**. 

    ![VPN hálózat tulajdonságai](./media/howto-mfa-nps-extension-vpn/image19.png)

9. A VPN-tulajdonságok ablakban válassza ki a **biztonsági** fülre. 

10. Az a **biztonsági** lapra, győződjön meg arról, hogy csak **Microsoft CHAP Version 2 (MS-CHAP v2)** kiválasztott, és adja meg a **OK**.

    ![A "Ezeket a protokollokat engedélyezése" beállítás](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Kattintson a jobb gombbal a VPN-kapcsolatot, és válassza **Connect**.

12. Az a **beállítások** ablakban válassza **Connect**.  
    A sikeres kapcsolat megjelenik a RADIUS-kiszolgálón Event ID 6272, mint a biztonsági napló itt látható módon:

    ![Az esemény tulajdonságai ablak](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>RADIUS hibaelhárítása

Tegyük fel, hogy a VPN-konfiguráció lett működik-e előtt konfigurálta a VPN-kiszolgáló központi RADIUS-kiszolgáló használatára a hitelesítéshez és engedélyezéshez. Ha a konfiguráció dolgozott, valószínű, hogy a hibát az okozza a Virtual Network szolgáltatás hibás, a RADIUS-kiszolgáló vagy egy érvénytelen felhasználónév vagy jelszó használata. Például ha a felhasználónév az alternatív UPN-utótagot használ, a bejelentkezési kísérlet sikertelen lehet. Használja ugyanazt a fiók neve, a legjobb eredmények elérése érdekében. 

Ezek a problémák elhárításához szempontokból ideális hely indítása, hogy ellenőrizze a biztonsági eseménynaplóit a RADIUS-kiszolgálón. Szeretné menteni a események keresése, használhatja a szerepkör-alapú hálózati házirend- és kiszolgáló egyéni megtekintése az eseménynaplóban, itt látható módon. "A 6273 eseményazonosító" azt jelzi, hogy eseményeket, ahol a hálózati házirend-kiszolgáló megtagadta a hozzáférést egy felhasználó számára. 

![Eseménynapló](./media/howto-mfa-nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>A multi-factor Authentication szolgáltatás konfigurálása

A multi-factor Authentication a felhasználók konfigurálásával segítségért tekintse meg a cikkeket [igénylése a kétlépéses ellenőrzés egy felhasználó vagy csoport](howto-mfa-userstates.md) és [a kétlépéses ellenőrzéshez a fiók beállítása](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Telepítse és konfigurálja a hálózati házirend-bővítmény

Ez a szakasz útmutatást nyújt a virtuális Magánhálózat konfigurálása MFA használatára az ügyfél-hitelesítéshez, a VPN-kiszolgálóval.

Miután telepítése és konfigurálása az NPS-bővítményt, a kiszolgáló által feldolgozott összes RADIUS-alapú ügyfél-hitelesítés MFA használatához szükséges. Ha a VPN-felhasználók Azure multi-factor Authentication nem regisztrált, a következők egyikét teheti:

* Egy másik RADIUS-kiszolgáló beállítása a felhasználók, akik nem használatára vannak konfigurálva az MFA hitelesítést. 

* Hozzon létre egy beállításjegyzékbeli bejegyzést, amely lehetővé teszi egy második hitelesítési tényezőt biztosít, ha regisztrálja őket az Azure multi-factor Authentication kifogásolt felhasználók. 

Hozzon létre egy új karakterláncértéket _a HKLM\SOFTWARE\Microsoft\AzureMfa REQUIRE_USER_MATCH_, és állítsa az értékét *igaz* vagy *hamis*. 

![A "Felhasználói egyeztetés megkövetelése" beállítás](./media/howto-mfa-nps-extension-vpn/image34.png)
 
Ha az értéke *igaz* , vagy üres, minden hitelesítési kérelemre vonatkoznak rá az MFA-hitelesítést. Ha az értéke *hamis*, MFA kihívások csak az Azure multi-factor Authentication regisztrált felhasználóinak vannak kibocsátva. Használja a *hamis* beállítást csak tesztelési vagy éles környezetekben regisztrációs időszak alatt.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Szerezze be az Azure Active Directory GUID-azonosítója

Az NPS-bővítményének konfigurációjának részeként meg kell adnia a rendszergazdai hitelesítő adatait, és az Azure AD-bérlő azonosítója. Szerezze be az Azonosítót az alábbiak szerint:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure-bérlő globális rendszergazdájaként.

2. A bal oldali panelen válassza ki a **Azure Active Directory** gombra.

3. Válassza ki **tulajdonságok**.

4. Az Azure AD-azonosítója másolásához jelölje ki a **másolási** gombra.
 
    ![Az Azure AD-azonosítója](./media/howto-mfa-nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Az NPS-bővítményének telepítése
Az NPS-bővítményt kell telepíthető egy kiszolgálóra, amelyen a hálózati házirend- és elérési szolgáltatások szerepkör telepítve van, és a functions a tervezés RADIUS-kiszolgálóként. Tegye *nem* az NPS-bővítményének telepítése a távoli asztal kiszolgálón.

1. Töltse le a hálózati házirend-bővítmény [Microsoft Download Center](https://aka.ms/npsmfa). 

2. A végrehajtható fájl másolása (*NpsExtnForAzureMfaInstaller.exe*) a hálózati házirend-kiszolgálóra.

3. A hálózati házirend-kiszolgáló a duplán **NpsExtnForAzureMfaInstaller.exe** és, ha a rendszer kéri, válassza ki a **futtatása**.

4. Az a **hálózati házirend-kiszolgáló kiterjesztése az Azure MFA beállítása** ablak, tekintse át a szoftverlicenc-feltételeket, válassza ki a **elfogadom a szerződés és feltételek** jelölőnégyzetet, majd válassza ki **telepítése**.

    ![A "Hálózati házirend-kiszolgáló kiterjesztése az Azure MFA beállítása" ablak](./media/howto-mfa-nps-extension-vpn/image36.png)
 
5. Az a **hálózati házirend-kiszolgáló kiterjesztése az Azure MFA beállítása** ablakban válassza **Bezárás**.  

    ![A "Telepítés sikeres" megerősítés ablakban](./media/howto-mfa-nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Az NPS-bővítményének használt tanúsítványok konfigurálása egy PowerShell-parancsfájl használatával
Biztonságos kommunikációt és a frissítési garanciát biztosító biztosításához használt tanúsítványok konfigurálásához az NPS-bővítményt. A hálózati házirend-kiszolgáló-összetevők közé tartozik egy Windows PowerShell-parancsprogram, amely beállítja a hálózati házirend-kiszolgáló egy önaláírt tanúsítványt. 

A szkript a következő műveleteket hajtja végre:

* Létrehoz egy önaláírt tanúsítványt.
* A szolgáltatásnévnek az Azure ad-ben a tanúsítvány nyilvános kulcsát társítja.
* Tárolja a tanúsítványt a helyi számítógép tárolójában.
* Hozzáférést biztosít a hálózati felhasználók a tanúsítvány titkos kulcsa.
* A hálózati házirend-kiszolgáló szolgáltatás újraindítása.

Ha azt szeretné, a saját tanúsítványok, az egyszerű szolgáltatás az Azure ad-ben a tanúsítvány nyilvános kulcsát társítani, és az így tovább.

A szkript használatához adja meg a bővítmény az Azure Active Directory rendszergazdai hitelesítő adatokat és az Azure Active Directory-bérlő azonosítója, korábban kimásolt. Futtassa a szkriptet minden egyes hálózati házirend-kiszolgálón, ahol az NPS-bővítményének telepítése.

1. Windows PowerShell futtatása rendszergazdaként.

2. A PowerShell parancssorába írja be a **cd "c:\Program Files\Microsoft\AzureMfa\Config"**, majd válassza ki az ENTER billentyűt.

3. Adja meg a következő parancs parancssorba **.\AzureMfsNpsExtnConfigSetup.ps1**, majd válassza ki az ENTER billentyűt. A parancsfájl ellenőrzi, hogy telepítve van-e az Azure AD PowerShell modul. Ha nincs telepítve, a parancsfájl telepíti a modult.
 
    ![PowerShell](./media/howto-mfa-nps-extension-vpn/image38.png)
 
    Miután a parancsfájl ellenőrzi a PowerShell-modul telepítését, az Azure Active Directory PowerShell modul bejelentkezési ablakban jeleníti meg. 

4. Adja meg az Azure AD rendszergazdai hitelesítő adatait és a jelszót, és válassza ki **jelentkezzen be a**. 
 
    ![A PowerShell bejelentkezési ablak](./media/howto-mfa-nps-extension-vpn/image39.png)
 
5. Parancsot a parancssorba illessze be a bérlő azonosítója, amelyet korábban vágólapra másolt, és válassza le az ENTER billentyűt. 

    ![Bérlőazonosító](./media/howto-mfa-nps-extension-vpn/image40.png)

    A szkript létrehoz egy önaláírt tanúsítványt, és más konfigurációs módosításokat hajt végre. A kimenet a hasonlóan a következő képen:

    ![Önaláírt tanúsítvány](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Indítsa újra a kiszolgálót.

### <a name="verify-the-configuration"></a>Ellenőrizze
A konfiguráció ellenőrzéséhez meg kell létesítenie a VPN-kiszolgáló egy új VPN-kapcsolat. Miután sikeresen megadta a hitelesítő adatait az elsődleges hitelesítéshez, a VPN-kapcsolat megvárja, amíg a másodlagos hitelesítés sikeres, a kapcsolat létrejötte előtt alább látható módon. 

![A Windows-beállítások VPN ablak](./media/howto-mfa-nps-extension-vpn/image42.png)

Sikerült a másodlagos ellenőrzési módszert, amely korábban konfigurált az Azure MFA-hitelesítést, ha csatlakozik az erőforrást. Ha a másodlagos hitelesítés sikertelen, az erőforráshoz való hozzáférés nem engedélyezett. 

A következő példában a Microsoft Authenticator alkalmazást a Windows Phone-a a másodlagos hitelesítést nyújt:

![Fiók ellenőrzése](./media/howto-mfa-nps-extension-vpn/image43.png)

Ön már sikeres hitelesítése után a másodlagos módszer használatával Ön, amelyekhez hozzáférést a virtuális port a VPN-kiszolgálón. Mert szükség volt egy másodlagos hitelesítési módszer használatát egy mobilalkalmazást egy megbízható eszköz használatával, a bejelentkezési folyamat biztonságosabb, mint ha csak egy felhasználónév és jelszó kombinációjával használja azt.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Eseménynaplók sikeres bejelentkezési események megtekintése
Sikeres bejelentkezési események megtekintése a Windows Eseménynapló-naplók, a Windows biztonsági lekérdezés jelentkezzen be a hálózati házirend-kiszolgáló a következő PowerShell-parancs beírásával:

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

![PowerShell biztonsági eseménynapló](./media/howto-mfa-nps-extension-vpn/image44.png)
 
Itt látható módon a biztonsági napló vagy a hálózati házirend- és elérési szolgáltatások egyéni nézet is megtekintheti:

![Hálózati házirend-kiszolgáló naplóját](./media/howto-mfa-nps-extension-vpn/image45.png)

A kiszolgálón, amelyre telepítette az NPS-bővítményt az Azure multi-factor Authentication alkalmazás eseménynaplókat a bővítményt, adott található *alkalmazások és szolgáltatások Logs\Microsoft\AzureMfa*. 

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

![Az Eseménynapló "Események száma" panel](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató
Ha a konfiguráció nem várt módon működik, ellenőrizze, hogy a felhasználó többtényezős hitelesítés használatára van konfigurálva a hibaelhárítás megkezdése. A felhasználó csatlakozhat a [az Azure portal](https://portal.azure.com). Ha a felhasználó kell megadnia a másodlagos hitelesítést, és képes sikeresen hitelesíteni, kiküszöbölheti az MFA, hibát egy helytelen konfiguráció.

Ha a felhasználó MFA működik, tekintse át az érintett eseménynaplókat. A naplók a biztonsági esemény, az átjáró operatív és az előző szakaszban tárgyalt Azure multi-factor Authentication-naplók tartalmazzák. 

A biztonsági napló, amely megjeleníti a sikertelen bejelentkezési event (esemény azonosítója a 6273) példa itt:

![A sikertelen bejelentkezési esemény megjelenítése a biztonsági napló](./media/howto-mfa-nps-extension-vpn/image47.png)

Az Azure multi-factor Authentication naplóból egy kapcsolódó esemény itt jelenik meg:

![Az Azure multi-factor Authentication-naplók](./media/howto-mfa-nps-extension-vpn/image48.png)

Ehhez az speciális hibaelhárításhoz, tekintse meg a hálózati házirend-kiszolgáló adatbázis formátum naplófájlokat ahol a hálózati házirend-kiszolgáló szolgáltatás telepítve van. A naplófájlok létrejönnek a _%SystemRoot%\System32\Logs_ vesszővel tagolt szöveges fájlok mappába. A naplófájlok ismertetését lásd: [értelmezése NPS naplófájlok](https://technet.microsoft.com/library/cc771748.aspx). 

Ezek a naplófájlok bejegyzései nehéz értelmezni, kivéve, ha egy táblázatot vagy egy adatbázis exportálása. Számos internetes hitelesítési szolgáltatás (IAS) elemzőeszközt online segítséget nyújtanak a naplófájlok értelmezése találja. A kimenet egy ilyen letölthető [shareware alkalmazás](http://www.deepsoftware.com/iasviewer) itt látható: 

![Shareware alkalmazás](./media/howto-mfa-nps-extension-vpn/image49.png)

További hibaelhárítási ehhez használhatja például a Wireshark protokollelemző vagy [Microsoft Message Analyzert](https://technet.microsoft.com/library/jj649776.aspx). Az alábbi képen a Wireshark a VPN-kiszolgáló és a hálózati házirend-kiszolgáló között RADIUS üzeneteket jeleníti meg.

![A Microsoft Message Analyzer](./media/howto-mfa-nps-extension-vpn/image50.png)

További információkért lásd: [a meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-factor Authentication](howto-mfa-nps-extension.md). 

## <a name="next-steps"></a>További lépések
[Az Azure multi-factor Authentication beszerzése](concept-mfa-licensing.md)

[Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](howto-mfaserver-nps-rdg.md)

[A helyszíni címtárak integrálása az Azure Active Directoryval](../connect/active-directory-aadconnect.md)

