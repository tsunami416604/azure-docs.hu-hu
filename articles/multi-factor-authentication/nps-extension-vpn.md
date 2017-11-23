---
title: "VPN integrálása az Azure MFA Használatát a hálózati házirend-kiszolgáló kiterjesztés használatával |} Microsoft Docs"
description: "A cikk ismerteti a VPN-infrastruktúra integrálása az Azure MFA Használatát a hálózati házirend-kiszolgáló bővítmény a Microsoft Azure használatával."
services: active-directory
keywords: "Az Azure MFA integrálja a VPN-, Azure Active Directoryban, hálózati házirend-kiszolgáló bővítmény"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: c058f1e747849cde0f15a039779665c8718da947
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>A VPN-infrastruktúra integrálása az Azure MFA bővítményével a hálózati házirend-kiszolgáló az Azure-bA

## <a name="overview"></a>Áttekintés

A hálózati házirend-kiszolgáló (NPS) az Azure-bővítmény lehetővé teszi a szervezetek védelme távoli Authentication Dial-In User Service (RADIUS) ügyfél-hitelesítéshez felhőalapú [Azure multi-factor Authentication (MFA)](multi-factor-authentication-get-started-server-rdg.md), amely biztosítja a kétlépéses ellenőrzést.

Ez a cikk útmutatást a hálózati házirend-kiszolgáló infrastruktúra integrálásához MFA bővítményével a hálózati házirend-kiszolgáló az Azure-bA. Ez a folyamat lehetővé teszi, hogy a felhasználók számára a VPN-kapcsolattal csatlakoznak a hálózathoz próbál biztonságos kétlépéses ellenőrzést. 

Hálózati házirend- és elérési szolgáltatások révén a szervezetek lehetőséget:

* Rendelje hozzá a felügyeleti és a hálózati kérelmek, adja meg a vezérlő központi helye:

    * Csatlakozás 
    
    * Mely napszakokban nap kapcsolatok engedélyezettek 
    
    * A kapcsolatok időtartama
    
    * Az ügyfelek kell használnia a kapcsolódáshoz biztonsági szint

    Helyett adja meg a házirendeket minden VPN vagy a távoli asztali átjáró kiszolgálón, erre, miután egy központi helyen fontosságúak. A RADIUS protokollt adja meg, központosított hitelesítési, engedélyezési és nyilvántartási (AAA) szolgál. 

* Állítson be, és kényszeríteni a hálózatvédelem (NAP) ügyfél állapotházirendeket, amelyek meghatározzák, hogy eszközök hálózati erőforrások korlátozás nélküli vagy korlátozott hozzáférést kapnak.

* Hitelesítési és engedélyezési 802.1 elérésére hardvermódosításainak x-kompatibilis vezeték nélküli hozzáférési pontok és Ethernet-kapcsolók.   
További információkért lásd: [hálózati házirend-kiszolgáló](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

Növelje a biztonságot, és a magas szintű való megfelelőség, a szervezetek integrálható hálózati házirend-kiszolgáló Azure multi-factor Authentication annak érdekében, hogy a felhasználók a kétlépéses ellenőrzést csatlakozni a virtuális port a VPN-kiszolgálón használja. A felhasználók számára a hozzáférést meg kell adniuk a felhasználónév és jelszó kombináció és egyéb információkat, amelyek szabályozzák. Ezeket az információkat megbízható legyen, és nem egyszerűen ismétlődik. A mobiltelefon, a vezetékes száma vagy egy alkalmazás egy mobileszközön része lehet.

A bővítmény rendelkezésre állását a hálózati házirend-kiszolgáló az Azure-ba, mielőtt az ügyfelek, akik a kétlépéses ellenőrzés végrehajtásához kívánta integrált hálózati házirend-kiszolgáló és a MFA környezetek konfigurálnia és karbantartania egy külön MFA kiszolgáló a helyszíni környezetben. Ilyen típusú hitelesítés távoli asztali átjáró és az Azure multi-factor Authentication kiszolgáló RADIUS használata kínálják.

A hálózati házirend-kiszolgáló kiterjesztésű az Azure-bA a szervezetek biztonságossá teheti a RADIUS-ügyfél-hitelesítés vagy egy helyszíni MFA-megoldását, vagy egy MFA felhőalapú megoldás üzembe helyezésével.
 
## <a name="authentication-flow"></a>Hitelesítési folyamat
Amikor a felhasználók csatlakoznak egy virtuális port a VPN-kiszolgálón, először hitelesíteniük különböző protokollok használatával. A protokollok engedélyezése a felhasználónév és a jelszó és a tanúsítványalapú hitelesítési módszereket. 

A hitelesítés és hitelesítik az identitásukat mellett a felhasználók a megfelelő betárcsázási engedélyekkel kell rendelkeznie. Olyan egyszerű megvalósításokhoz, a telefonos engedélyek, amelyek lehetővé teszik a hozzáférést legyenek beállítva közvetlenül az Active Directory-felhasználói objektumok. 

![Felhasználói tulajdonságok](./media/nps-extension-vpn/image1.png)

Olyan egyszerű megvalósításokhoz, a VPN-kiszolgáló engedélyezi vagy megtagadja a hozzáférést minden helyi VPN-kiszolgáló meghatározott házirendek alapján.

Nagyobb és több méretezhető implementációk esetén a házirendekben, amelyek a VPN-hozzáférés engedélyezése vagy megtagadása van központi RADIUS-kiszolgálókon. Ezekben az esetekben a VPN-kiszolgáló egy kiszolgáló (RADIUS-ügyfél), amely továbbítja a csatlakozási kérelmek és egy RADIUS-kiszolgáló fiók üzenetek funkcionál. Szeretne csatlakozni a virtuális port a VPN-kiszolgálón, a felhasználók kell hitelesíteni, és a RADIUS-kiszolgálók központilag meghatározott feltételeknek. 

Ha a hálózati házirend-kiszolgáló az Azure-bővítmény integrálva van a hálózati házirend-kiszolgáló, a sikeres hitelesítési folyamat az eredménye, az alábbiak szerint:

1. A VPN-kiszolgáló-hitelesítési kérést kap egy VPN-felhasználó, amely tartalmazza a felhasználónevet és jelszót egy erőforrást, például a távoli asztali munkamenetet való kapcsolódáshoz. 

2. Egy RADIUS-ügyfél működött, a VPN-kiszolgáló alakítja át a kérelem egy RADIUS- *-kérést* üzenet, és elküldi (jelszóval egy titkosított) a RADIUS-kiszolgáló ahol telepítve van-e a hálózati házirend-kiszolgáló bővítmény. 

3. A felhasználónév és jelszó kombinációjával ellenőrzése az Active Directoryban. Ha a felhasználónév vagy a jelszó helytelen, a RADIUS-kiszolgáló küld egy *Access-Reject* üzenet. 

4. Ha az összes, a hálózati házirend-kiszolgáló kapcsolódási kérelem és a hálózati házirendeket, a megadott feltételek (például időpont vagy csoport korlátozása), a hálózati házirend-kiszolgáló bővítmény elindítja a másodlagos hitelesítés az Azure multi-factor Authentication hitelesítést kér. 

5. Az Azure multi-factor Authentication kommunikál az Azure Active Directory, lekérdezi a felhasználó adatait és a másodlagos hitelesítést hajt végre (cella telefonhívás, szöveges üzenet vagy mobilalkalmazás) a felhasználó által konfigurált módon. 

6. Ha az MFA-kérdést sikeres volt, az Azure multi-factor Authentication az eredmény a hálózati házirend-kiszolgáló bővítmény kommunikál.

7. Miután a kapcsolódási kísérlet is hitelesítése és engedélyezése, hálózati házirend-kiszolgáló, amelyen telepítve van-e a bővítmény küld egy RADIUS *Access-Accept* az üzenethez, és a VPN-kiszolgáló (RADIUS-ügyfél).

8. A felhasználó hozzáférést kap a virtuális port a VPN-kiszolgálón, és egy titkosított VPN-alagutat hoz létre.

## <a name="prerequisites"></a>Előfeltételek
Ez a szakasz részletesen az előfeltételeket, amelyeket az MFA integrálható a távoli asztali átjáró előtt kell elvégezni. Megkezdése előtt rendelkeznie kell a következő előfeltételek teljesülnek:

* VPN-infrastruktúra
* Hálózati házirend- és elérési szolgáltatások szerepkör
* Az Azure multi-factor Authentication-licenc
* Windows Server szoftver
* Szalagtárak
* Azure Active Directory (Azure AD) szinkronizálása megtörtént-e a helyszíni Active Directory 
* Az Azure Active Directory GUID azonosítója

### <a name="vpn-infrastructure"></a>VPN-infrastruktúra
Ez a cikk feltételezi, hogy rendelkezik-e egy működő VPN-infrastruktúra, amely Microsoft Windows Server 2016 használja, és, hogy a VPN-kiszolgáló nincs beállítva a továbbítási kapcsolat kérelmekre egy RADIUS-kiszolgáló. A cikk a VPN-infrastruktúra központi RADIUS-kiszolgáló használatára konfigurálja.

Ha nem rendelkeznek olyan működő VPN-infrastruktúra helyen, akkor gyorsan létrehozhat a Microsoft és a külső webhelyek által a számos VPN telepítő oktatóprogramot kínál, amelyek található útmutatást követve. 
            
### <a name="the-network-policy-and-access-services-role"></a>A hálózati házirend- és elérési szolgáltatások szerepkör

Hálózati házirend- és elérési szolgáltatások a RADIUS-kiszolgáló és az ügyfél funkciókat biztosítja. Ez a cikk feltételezi, hogy telepítette a hálózati házirend- és elérési szolgáltatások szerepkör a tagkiszolgáló vagy tartományvezérlő a környezetben. Ebben az útmutatóban RADIUS VPN-konfigurációt konfigurálása. A hálózati házirend- és elérési szolgáltatások szerepkör telepítése a kiszolgálóra *eltérő* a VPN-kiszolgáló.

A hálózati házirend- és elérési szolgáltatások szerepkör telepítésével kapcsolatos információk szolgáltatás Windows Server 2012 vagy újabb, lásd: [a NAP állapotházirend-kiszolgáló telepítése](https://technet.microsoft.com/library/dd296890.aspx). A HÁLÓZATVÉDELEM elavult a Windows Server 2016. Ajánlott eljárások a hálózati házirend-kiszolgáló, beleértve a hálózati házirend-kiszolgáló telepítése tartományvezérlőn, az ajánlás leírását lásd: [gyakorlati tanácsok a hálózati házirend-kiszolgálójának](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Az Azure MFA-licenc

Egy licencre szükség az Azure multi-factor Authentication, és az Azure AD Premium, nagyvállalati mobilitási + biztonsági vagy a multi-factor Authentication különálló licenc keresztül érhető el. Fogyasztás alapján licenceinek többek között az Azure MFA felhasználónként vagy a hitelesítési licencek nem kompatibilisek-e a hálózati házirend-kiszolgáló bővítményt. További információkért lásd: [beolvasásával Azure multi-factor Authentication](multi-factor-authentication-versions-plans.md). Tesztelési célokra használható a próba-előfizetést.

### <a name="windows-server-software"></a>Windows Server szoftver

A hálózati házirend-kiszolgáló-bővítményhez olyan Windows Server 2008 R2 SP1 vagy újabb verzió, a hálózati házirend- és elérési szolgáltatások szerepkör telepítése. Ez az útmutató lépései a Windows Server 2016 lettek végrehajtva.

### <a name="libraries"></a>Szalagtárak

Az alábbi kódtárak is automatikusan települnek a hálózati házirend-kiszolgáló kiterjesztésű:

-   [Visual C++ újraterjeszthető csomag a Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Active Directory modul Windows Powershellhez készült Azure 1.1.166.0 verziója](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

A Microsoft Azure Active Directory PowerShell-modul még nincs jelen, ha telepíti a rendszer egy konfigurációs parancsfájl, amely a telepítési folyamat részeként futtatja. Nincs szükség a időben modul telepítése, ha még nincs telepítve.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Az Azure Active Directory szinkronizálása megtörtént-e a helyszíni Active Directory 

A hálózati házirend-kiszolgáló kiterjesztés használatára, a helyszíni felhasználók legyen szinkronizálva az Azure Active Directoryban, és engedélyezve van a multi-factor Authentication. Ez az útmutató feltételezi, hogy a helyszíni felhasználók szinkronizálása megtörtént-e Azure Active Directory, az Azure AD Connect használatával. Így a felhasználók a multi-factor Authentication tartozó utasításokat alatt.

További információ az Azure AD Connect: [integrálása a helyszíni címtárakat az Azure Active Directoryval](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>Az Azure Active Directory GUID azonosítója 

Az NPS-bővítményének telepítése kell tudni, hogy az Azure Active Directory GUID-azonosítója. A GUID-azonosítója az Azure Active Directory-kereséshez utasításokat a következő szakaszban.

## <a name="configure-radius-for-vpn-connections"></a>A VPN-kapcsolatok RADIUS konfigurálása

Ha már telepítette az NPS szerepkör azon a tagkiszolgálón, konfigurálja úgy, hogy a VPN-ügyfél adott kérelmek VPN-kapcsolatok helyszerepkörre, és szeretné. 

Ez a szakasz azt feltételezi, hogy a hálózati házirend- és elérési szolgáltatások szerepkör telepítése, de nem konfigurált, használja a infrastruktúrában.

> [!NOTE]
> Ha már van egy működő VPN-kiszolgáló egy központi RADIUS-kiszolgálót használ, ez a szakasz kihagyhatja.
>

### <a name="register-server-in-active-directory"></a>Kiszolgáló regisztrálása az Active Directoryban
Ebben a forgatókönyvben megfelelően működni, regisztrálni kell a hálózati házirend-kiszolgáló az Active Directoryban.

1. Nyissa meg a Kiszolgálókezelőt.

2. A Kiszolgálókezelő ablakában válassza **eszközök**, majd válassza ki **hálózati házirend-kiszolgáló**. 

3. A hálózati házirend-kiszolgáló konzol, kattintson a jobb gombbal **hálózati házirend-kiszolgáló (helyi)**, majd válassza ki **kiszolgáló regisztrálása az Active Directoryban**. Válassza ki **OK** kétszer.

    ![Hálózati házirend-kiszolgáló](./media/nps-extension-vpn/image2.png)

4. Hagyja nyitva a következő eljárással a konzolt.

### <a name="use-wizard-to-configure-the-radius-server"></a>A RADIUS-kiszolgáló konfigurálása varázsló segítségével
Használhatja a szabványos (varázsló-alapú) vagy speciális konfigurációs beállítás konfigurálása a RADIUS-kiszolgáló. Jelen szakaszban feltételezzük, hogy a varázsló alapú szabványos konfigurációs beállítást használja.

1. Válassza ki a hálózati házirend-kiszolgáló konzol **hálózati házirend-kiszolgáló (helyi)**.

2. A **szabványos konfigurációs**, jelölje be **RADIUS-kiszolgáló telefonos vagy VPN-kapcsolatok**, majd válassza ki **konfigurálása VPN vagy a telefonos**.

    ![Konfigurálja a VPN](./media/nps-extension-vpn/image3.png)

3. Az a **válassza ki a telefonos vagy virtuális magánhálózati kapcsolatok hálózattípus** ablakban válassza ki **virtuális magánhálózati kapcsolatok**, majd válassza ki **következő**.

    ![Virtuális magánhálózat](./media/nps-extension-vpn/image4.png)

4. Az a **meg telefonos vagy VPN-kiszolgáló** ablakban válassza ki **Hozzáadás**.

5. Az a **új RADIUS-ügyfél** ablakban adja meg egy rövid nevet, adja meg a VPN-kiszolgáló IP-cím vagy feloldható nevét, és írja be a megosztott titkos jelszót.  
    Győződjön meg a közös titkos jelszó hosszú és összetett. Jegyezze fel, mert szüksége lehet rájuk a következő szakaszban.

    ![Új RADIUS-ügyfél](./media/nps-extension-vpn/image5.png)

6. Válassza ki **OK**, majd válassza ki **következő**.

7. Az a **hitelesítési módszerek konfigurálása** ablakban fogadja el az alapértelmezésként beállított elemet (**Microsoft titkosított hitelesítés 2 [MS-CHAPv2])** vagy más lehetőséget választ, és válassza ki **tovább** .

    > [!NOTE]
    > Ha konfigurálja az Extensible Authentication Protocol (EAP), vagy a Microsoft kérdés-kézfogás típusú hitelesítési protokoll (CHAPv2), vagy a védett bővíthető hitelesítési protokoll (PEAP) kell használnia. Nincs más EAP esetén támogatott.
 
8. Az a **adja meg a felhasználói csoportok** ablakban válassza ki **Hozzáadás**, majd válasszon ki egy megfelelő csoportot.  
    Ha nem a csoport létezik, hagyja meg az üres hozzáférést minden felhasználó számára.

    ![A felhasználói csoportok meg ablak](./media/nps-extension-vpn/image7.png)

9. Válassza ki **következő**.

10. Az a **adjon meg IP-szűrők** ablakban válassza ki **következő**.

11. Az a **adja meg a titkosítási beállítások** ablakban fogadja el az alapértelmezett beállításokat, majd válassza ki **következő**.

    ![A titkosítási beállítások megadása ablak](./media/nps-extension-vpn/image8.png)

12. Az a **tartománynév megadása** ablakban, a tartomány neve mező maradjon üres, elfogadja az alapértelmezett beállítást, és válassza **következő**.

    ![Adja meg a tartománynevet ablak](./media/nps-extension-vpn/image9.png)

13. Az a **új befejezése telefonos vagy virtuális magánhálózati kapcsolatok és RADIUS-ügyfelek** ablakban válassza ki **Befejezés**.

    ![Az "új befejezése telefonos vagy virtuális magánhálózati kapcsolatok és RADIUS-ügyfelek" ablak](./media/nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>A RADIUS-konfiguráció ellenőrzése
Ez a szakasz részletesen a konfiguráció varázsló használatával létrehozott.

1. A hálózati házirend-kiszolgálón, a hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki a **RADIUS-ügyfelek**, majd válassza ki **RADIUS-ügyfelek**.

2. A részleteket tartalmazó ablaktáblán kattintson a jobb gombbal a létrehozott, és válassza ki RADIUS-ügyfél **tulajdonságok**.  
    A RADIUS-ügyfél (a VPN-kiszolgáló) tulajdonságait kell lennie, mint itt látható:

    ![VPN-tulajdonságai](./media/nps-extension-vpn/image11.png)

3. Válassza ki **Mégse**.

4. A hálózati házirend-kiszolgálón, a hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki a **házirendek**, majd válassza ki **kapcsolatkérelem-házirendek**.  
    A VPN-kapcsolatok házirend megjelenik a következő ábrán látható módon:

    ![Csatlakozási kérések](./media/nps-extension-vpn/image12.png)

5. A **házirendek**, jelölje be **hálózati házirendek**.  
    Egy alábbihoz hasonló a házirendet, az alábbi ábrán látható virtuális magánhálózati (VPN) kapcsolatok házirendet kell megjelennie:

    ![Hálózati házirendek](./media/nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Konfigurálja a VPN-kiszolgáló RADIUS-hitelesítés használata
Ez a szakasz a VPN-kiszolgáló RADIUS-hitelesítés használatára konfigurálja. Az utasítások feltételezik, hogy a VPN-kiszolgálóként működő konfigurációval rendelkezik, de nem állította be a RADIUS-hitelesítés használatára. A VPN-kiszolgáló konfigurálása után győződjön meg arról, hogy a várt módon működik-e a konfiguráció.

> [!NOTE]
> Ha már van egy működő VPN-kiszolgáló beállítása, amely a RADIUS-hitelesítést használ, ez a szakasz kihagyhatja.
>

### <a name="configure-authentication-provider"></a>Hitelesítésszolgáltató konfigurálása
1. A VPN-kiszolgálón nyissa meg a Kiszolgálókezelőt.

2. A Kiszolgálókezelő ablakában válassza **eszközök**, majd válassza ki **Útválasztás és távelérés**.

3. Az a **Útválasztás és távelérés** ablak, kattintson a jobb gombbal  **\<kiszolgáló neve > (helyi)**, majd válassza ki **tulajdonságok**.

    ![Az Útválasztás és távelérés ablak](./media/nps-extension-vpn/image14.png)
 
4. Az a  **\<kiszolgáló neve > (helyi) tulajdonságai** ablakban válassza ki a **biztonsági** fülre. 

5. Az a **biztonsági** lap **hitelesítési szolgáltató**, jelölje be **RADIUS-hitelesítés**, majd válassza ki **konfigurálása**.

    ![RADIUS-hitelesítés](./media/nps-extension-vpn/image15.png)
 
6. Az a **RADIUS-hitelesítés** ablakban válassza ki **Hozzáadás**.

7. Az a **RADIUS-kiszolgáló hozzáadása** ablakban tegye a következőket:

    a. Az a **kiszolgálónév** mezőbe írja be a nevét vagy IP-címet a RADIUS-kiszolgáló, az előzőekben konfigurált.

    b. Az a **közös titkos kulcs**, jelölje be **módosítás**, majd írja be a megosztott titkos jelszót létrehozni, és korábban feljegyzett.

    c. Az a **időkorlátja (másodperc)** mezőben kiválaszthat egy értéket **30** keresztül **60**.  
    Az időtúllépés értéke szükséges ahhoz, hogy elegendő ideig a második hitelesítési tényezőt.
 
    ![A RADIUS-kiszolgáló hozzáadása ablak](./media/nps-extension-vpn/image16.png)
 
8. Kattintson az **OK** gombra.

### <a name="test-vpn-connectivity"></a>VPN-kapcsolat tesztelése
Ebben a szakaszban, győződjön meg arról, hogy a VPN-ügyfél hitelesítése és engedélyezése a RADIUS-kiszolgáló, a VPN-virtuális port csatlakozni tett kísérlet során. Az utasítások feltételezik, hogy, hogy a VPN-ügyfél Windows 10 használ. 

> [!NOTE]
> Ha már konfigurált egy VPN-ügyfél a VPN-kiszolgálóhoz való csatlakozáshoz, és mentette a beállítások, konfigurálása és mentése egy VPN-kapcsolati objektum kapcsolatos lépéseket kihagyhatja.
>

1. A VPN-ügyfél, jelölje ki a **Start** gombra, és válassza a **beállítások** gombra.

2. Az a **Windows-beállítások** ablakban válassza ki **hálózat és Internet**.

3. Válassza ki **VPN**.

4. Válassza ki **egy VPN-kapcsolat hozzáadása**.

5. Az a **egy VPN-kapcsolat hozzáadása** ablakban, a a **VPN-szolgáltató** mezőben válassza **Windows (beépített)**, a többi mező, szükség esetén végezze el, és válassza a **Mentése**. 

    ![A "VPN-kapcsolat hozzáadása" ablak](./media/nps-extension-vpn/image17.png)
 
6. Ugrás a **Vezérlőpult**, majd válassza ki **hálózati és megosztási központ**.

7. Válassza ki **Adapterbeállítások módosítása**.

    ![Adapterbeállítások módosítása](./media/nps-extension-vpn/image18.png)

8. Kattintson a jobb gombbal a VPN-hálózati kapcsolatot, majd válassza ki **tulajdonságok**. 

    ![VPN-hálózati tulajdonságok](./media/nps-extension-vpn/image19.png)

9. A VPN-tulajdonságai ablakban válassza ki a **biztonsági** fülre. 

10. Az a **biztonsági** lapra, győződjön meg arról, hogy csak **Microsoft CHAP 2-es Version (MS-CHAP v2)** kiválasztva, majd jelölje ki **OK**.

    ![A "Ezeket a protokollokat tiltása" beállítás](./media/nps-extension-vpn/image20.png)

11. Kattintson a jobb gombbal a VPN-kapcsolatot, majd válassza ki **Connect**.

12. Az a **beállítások** ablakban válassza ki **Connect**.  
    Ahogy az itt látható a sikeres kapcsolat jelenik meg a biztonsági napló Event ID 6272, mint a RADIUS-kiszolgálón:

    ![Az esemény tulajdonságai ablakban](./media/nps-extension-vpn/image21.png)

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató
Tegyük fel, hogy a VPN-konfiguráció dolgozott előtt konfigurálta a VPN-kiszolgáló egy központi RADIUS-kiszolgáló használatára a hitelesítéshez és engedélyezéshez. Ha a konfigurációs dolgozott, valószínű, hogy a hibát az okozza a helytelen konfigurálása a RADIUS-kiszolgáló vagy egy érvénytelen felhasználónév vagy jelszó használata. Például ha a felhasználónév az alternatív UPN-utótagot használja, a bejelentkezési kísérlet sikertelen lehet. A legjobb eredmények elérése érdekében ugyanazt a fiók nevet használja. 

Ezek a problémák elhárításához egy ideális kiindulási, a RADIUS-kiszolgáló biztonsági eseménynaplók vizsgálata. Mentés ideje eseményeket keres, használhatja a szerepkör-alapú hálózati házirend- és kiszolgáló egyéni megtekintése az eseménynaplóban, itt látható módon. "A 6273-as eseményazonosító" események, ahol a hálózati házirend-kiszolgáló megtagadta a hozzáférést egy felhasználó jelzi. 

![Eseménynapló](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Többtényezős hitelesítés beállítása
A szakasz ismerteti, így a felhasználók a multi-factor Authentication és a kétlépéses ellenőrzéshez fiókok beállításával kapcsolatos utasításokat. 

### <a name="enable-multi-factor-authentication"></a>A többtényezős hitelesítés engedélyezése
Ebben a szakaszban az Azure AD-fiókok a multi-factor Authentication engedélyezése. A klasszikus Azure portál segítségével lehetővé teszi a felhasználók a multi-factor Authentication. 

1. Lépjen a [Microsoft Azure](https://manage.windowsazure.com) webhelyet. 

2. Jelentkezzen be rendszergazdaként.

3. A bal oldali panelen válassza ki a **Active Directory**.

    ![Alapértelmezett könyvtár](./media/nps-extension-vpn/image23.png)

4. Az a **neve** oszlopból válassza ki **alapértelmezett címtárat** (vagy egy másik címtárban, ha szükséges).

5. Az a **alapértelmezett címtár** ablakban válassza ki **konfigurálása**.

    ![Az alapértelmezett könyvtár konfigurálása](./media/nps-extension-vpn/image24.png)

6. Az a **konfigurálása** ablakban, a **a multi-factor authentication**, jelölje be **szolgáltatás beállításainak kezelése**.

    ![A multi-factor authentication beállításainak kezelése](./media/nps-extension-vpn/image25.png)
 
7. Az a **a multi-factor authentication** ablak, tekintse át az alapértelmezett szolgáltatás beállításai, és válassza a **felhasználók** fülre. 

    ![A felhasználók a multi-factor authentication lap](./media/nps-extension-vpn/image26.png)
 
8. Az a **felhasználók** lapra, válassza ki a felhasználókat, akik engedélyezi az MFA szolgáltatásra, és válassza ki a kívánt **engedélyezése**.

    ![Tulajdonságok](./media/nps-extension-vpn/image27.png)
 
9. Amikor a rendszer kéri, válassza ki a **multi-factor auth engedélyezése**.

    ![A többtényezős hitelesítés engedélyezése](./media/nps-extension-vpn/image28.png)
 
10. Válassza ki **Bezárás**. 

11. Frissítse az oldalt.  
    A többtényezős hitelesítés állapota *engedélyezve*.

A multi-factor Authentication felhasználók engedélyezésével kapcsolatos információkért lásd: [Ismerkedés az Azure multi-factor Authentication a felhőben](multi-factor-authentication-get-started-cloud.md). 

### <a name="configure-accounts-for-two-step-verification"></a>A kétlépéses ellenőrzéshez fiókok beállítása
Fiók engedélyezve van az MFA szolgáltatásra, miután a felhasználók nem tudnak bejelentkezni, amíg sikeresen konfigurálta a második hitelesítési tényezővel használandó megbízható eszköz a többtényezős hitelesítési szabályzat által szabályozott erőforrások.

Ebben a szakaszban egy megbízható eszköz konfigurál a kétlépéses ellenőrzéshez használttal. Több lehetőség közül választhat eszközt, beleértve a következőket:

* **Mobilalkalmazás**: a Microsoft Authenticator alkalmazást telepít egy Windows Phone, Android vagy iOS-eszközön. Attól függően, hogy a szervezet házirendjeit szükségesek az alkalmazás használatát a két mód egyikében: 
    * Értesítések az ellenőrzések (értesítést a rendszer előkészítésre továbbít az eszközhöz).
    * Egy megerősítési kódot (áll meg kell adniuk egy megerősítési kódot, amely 30 másodpercenként frissíti) használja. 

* **Mobiltelefon hívást vagy SMS**: is fogadhatja az vagy egy automatikus telefonhívás, vagy egy szöveges üzenetet. A telefonhívás kapcsolóval a hívás, és válassza ki a kettős kereszt (#) gombot. A szöveg beállítással válaszoljon az üzenetre, vagy adja meg a bejelentkezési felületen.

* **Irodai telefon hívása**: Ez a folyamat megegyezik a folyamat automatikus telefonhívásokat korábban ismertetett.

Eszköz beállítása használni a mobilalkalmazást az ellenőrzéshez a leküldéses értesítések fogadásához, tegye a következőket:

1. Jelentkezzen be [Microsoft Azure](https://aka.ms/mfasetup) vagy egyetlen helyen, például a [Azure-portálon](https://portal.azure.com), amely még az MFA-kompatibilis hitelesítő adatokkal végezhessenek hitelesítést szükséges.  
    Kéri a fiók további biztonsági ellenőrzés, ahogy az itt látható:

    ![További biztonsági](./media/nps-extension-vpn/image29.png)

2. Válassza ki **most beállítása**.

3. Az a **további biztonsági ellenőrzés** írja be a ablakban válassza ki a megfelelő ügyfél (**hitelesítéshez megadott telefonját**, **irodai telefon**, vagy **mobilalkalmazás** ), olyan országban vagy régióban, majd válassza ki és egy metódust. Ne válassza **forduljon me** még.  
    A módszer függ a kapcsolattartó típusa. Ha úgy dönt, például **mobilalkalmazás**, kiválaszthatja, hogy az ellenőrzési értesítések fogadásához, vagy egy ellenőrző kód használata. 

    ![A "további biztonsági ellenőrzés" ablak](./media/nps-extension-vpn/image30.png)

    A következő lépések azt feltételezik, hogy a kiválasztott **mobilalkalmazás** kapcsolattartási típusként.

4. Válassza ki **mobilalkalmazás**, jelölje be **értesítéseket az ellenőrzéshez**, majd válassza ki **beállítása**. 

    ![A "további biztonsági ellenőrzés" ablak](./media/nps-extension-vpn/image31.png)
 
5. Ha még nem tette meg, telepítse a Microsoft Authenticator mobilalkalmazás az eszközön. 

6. A mobilalkalmazásban megjelenített vonalkód beolvasása, vagy adja meg manuálisan az adatokat, majd válassza ki **végzett**.

    ![Microsoft Authenticator mobilalkalmazás konfigurálása](./media/nps-extension-vpn/image32.png)

7. Az a **további biztonsági ellenőrzés** ablakban válassza ki **me forduljon**, és ezután az értesítésre, az eszközre küldött válasz.

8. A a **további biztonsági ellenőrzés** ablakban, a **3. lépés:, ha a jövőben nem fér hozzá a mobilalkalmazás**, írjon be egy számot felhívhatja fog tudni hozzáférni a mobilalkalmazást, és válassza  **Következő**.

    ![A "további biztonsági ellenőrzés" ablak](./media/nps-extension-vpn/image33.png)
 
9. Az a **további biztonsági ellenőrzés** ablakban válassza ki **végzett**.

Az eszköz konfigurálva van egy második ellenőrzési módszert biztosít. A kétlépéses ellenőrzéshez fiókok beállításával kapcsolatos információkért lásd: [a kétlépéses ellenőrzéshez a fiók beállítása](./end-user/multi-factor-authentication-end-user-first-time.md).

## <a name="install-and-configure-the-nps-extension"></a>Telepítse és konfigurálja a hálózati házirend-kiszolgáló bővítmény

Ez a témakör a többtényezős hitelesítés használata az ügyfél-hitelesítéshez a VPN-kiszolgálóval VPN konfigurálására vonatkozó útmutatásokat.

Után telepítette, és a hálózati házirend-kiszolgáló-kiterjesztés konfigurálása, a kiszolgáló által feldolgozott összes RADIUS-alapú ügyfél-hitelesítés többtényezős hitelesítés használatához szükséges. Ha a VPN-felhasználók Azure multi-factor Authentication-ban nem regisztrált, az alábbiak valamelyikét teheti:

* Egy másik RADIUS-kiszolgáló beállítása a felhasználókat, akik nem MFA használatára vannak konfigurálva. 

* Hozzon létre egy beállításjegyzékbeli bejegyzést, amely lehetővé teszi a kifogásolt felhasználóktól egy második hitelesítési tényezővel az Azure multi-factor Authentication regisztrálva van. 

Hozzon létre egy új karakterláncértéket _a HKLM\SOFTWARE\Microsoft\AzureMfa REQUIRE_USER_MATCH_, és állítsa be az értéket *igaz* vagy *hamis*. 

![A "Felhasználói egyeztetés megkövetelése" beállítás](./media/nps-extension-vpn/image34.png)
 
Ha a változó értéke *igaz* , vagy üres, minden hitelesítési kérelmeket a rendszer megvizsgálja az MFA-kérdést. Ha a változó értéke *hamis*, MFA kihívást kiállított csak azoknak a felhasználóknak, akik az Azure multi-factor Authentication regisztrált. Használja a *hamis* beállítást csak tesztelés vagy üzemi környezetben bevezetési időszak alatt.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Az Azure Active Directory GUID azonosító lekéréséhez

A hálózati házirend-kiszolgáló bővítmény konfigurációjának részeként meg kell adni a rendszergazdai hitelesítő adatokat és az Azure AD-bérlő azonosítója. Az azonosító lekéréséhez a következő módon:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) az Azure-bérlőhöz globális rendszergazdájaként.

2. A bal oldali panelen válassza ki a **Azure Active Directory** gombra.

3. Válassza ki **tulajdonságok**.

4. Az Azure AD-azonosítója másolásához jelölje ki a **másolási** gombra.
 
    ![Az Azure AD-azonosító](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>A hálózati házirend-kiszolgáló-kiterjesztés telepítése
A hálózati házirend-kiszolgáló bővítmény telepítenie kell egy kiszolgálót, amelyen a hálózati házirend- és elérési szolgáltatások szerepkör telepítése és a funkciók kialakításában RADIUS-kiszolgálóként. Tegye *nem* az NPS-bővítmény telepítése a távoli asztal kiszolgálón.

1. Töltse le a hálózati házirend-kiszolgáló bővítményt a [Microsoft Download Center](https://aka.ms/npsmfa). 

2. A végrehajtható fájl másolása (*NpsExtnForAzureMfaInstaller.exe*) a hálózati házirend-kiszolgálóra.

3. A hálózati házirend-kiszolgálón kattintson duplán a **NpsExtnForAzureMfaInstaller.exe** értéket, ha a rendszer felszólítja, válassza ki a **futtatása**.

4. Az a **Azure MFA telepítése hálózati házirend-kiszolgáló bővítmény** ablak, tekintse át a szoftverlicenc-feltételeket, válassza ki a **elfogadom a licencfeltételeket és a feltételek** jelölje be a jelölőnégyzetet, majd válassza ki **telepítése**.

    ![A "Hálózati házirend-kiszolgáló bővítményt az Azure MFA beállítása" ablak](./media/nps-extension-vpn/image36.png)
 
5. Az a **Azure MFA telepítése hálózati házirend-kiszolgáló bővítmény** ablakban válassza ki **Bezárás**.  

    ![A "Sikeres telepítés" ablak](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>A hálózati házirend-kiszolgáló kiterjesztésű használt tanúsítványok konfigurálása egy PowerShell-parancsfájl használatával
Győződjön meg arról, biztonságos kommunikációt, és biztosítani, konfigurálja a hálózati házirend-kiszolgáló bővítmény által használt tanúsítványok. A hálózati házirend-kiszolgáló összetevői az alábbiak a Windows PowerShell-parancsfájlt, amely beállítja a hálózati házirend-kiszolgáló egy önaláírt tanúsítványt. 

A parancsfájl a következő műveleteket hajtja végre:

* Létrehoz egy önaláírt tanúsítványt.
* Társítja az egyszerű szolgáltatásnév Azure ad-val a tanúsítvány nyilvános kulcsát.
* Tárolja a tanúsítványt a helyi számítógép tárolójában.
* Hozzáférést biztosít a hálózati felhasználó számára a tanúsítvány titkos kulcsa.
* A hálózati házirend-kiszolgáló szolgáltatás újraindul.

Ha szeretné használni a saját tanúsítványait, rendelje hozzá a tanúsítvány nyilvános kulcsának az egyszerű szolgáltatás Azure ad-val, és az így tovább.

A parancsfájl használatát, adja meg az Azure Active Directory rendszergazdai hitelesítő adatokat és az Azure Active Directory-Bérlőazonosító korábban kimásolt a bővítményt. Futtassa a parancsfájlt minden hálózati házirend-kiszolgálón, ahol az NPS-bővítményének telepítése.

1. A Windows PowerShell futtatása rendszergazdaként.

2. A PowerShell parancssorába írja be a **cd c:\Program Files\Microsoft\AzureMfa\Config**, majd válassza ki az ENTER billentyűt.

3. A következő parancs parancssorba írja be a **.\AzureMfsNpsExtnConfigSetup.ps1**, majd válassza ki az ENTER billentyűt.  
    A parancsfájl ellenőrzi, hogy telepítve van-e az Azure AD PowerShell modult. Ha nincs telepítve, a parancsfájl telepíti a modult.
 
    ![PowerShell](./media/nps-extension-vpn/image38.png)
 
    Miután a parancsfájl ellenőrzi a telepítést, a PowerShell modul, a Windows Azure Active Directory PowerShell modul bejelentkezési jeleníti meg. 

4. Adja meg az Azure AD rendszergazdai hitelesítő adatait és a jelszót, majd válassza ki **bejelentkezés**. 
 
    ![Bejelentkezés a Windows PowerShell](./media/nps-extension-vpn/image39.png)
 
5. A parancssorba illessze be a korábban kimásolt Bérlőazonosító, és válassza le az ENTER billentyűt. 

    ![Bérlőazonosító](./media/nps-extension-vpn/image40.png)

    A parancsfájl létrehoz egy önaláírt tanúsítványt, és más konfigurációs módosításokat hajt végre. A kimeneti hasonlóan az alábbi képen van:

    ![Önaláírt tanúsítvány](./media/nps-extension-vpn/image41.png)

6. Indítsa újra a kiszolgálót.

### <a name="verify-the-configuration"></a>Ellenőrizze
A konfiguráció ellenőrzése a VPN-kiszolgáló egy új VPN-kapcsolatot kell létesítenie. Miután sikeresen beírt hitelesítő adatait az elsődleges hitelesítéshez, a VPN-kapcsolatot vár a másodlagos hitelesítés sikeres, a kapcsolat létrejötte előtt alább látható módon. 

![A Windows VPN-beállítások ablak](./media/nps-extension-vpn/image42.png)

Sikerült a másodlagos ellenőrzési módszert, amelyet korábban már konfigurálta az Azure MFA hitelesítést, ha csatlakozik a erőforrás. Ha a másodlagos hitelesítés nem sikerül, azonban vannak az erőforráshoz való hozzáférés megtagadva. 

A következő példában a Microsoft Authenticator alkalmazást a Windows Phone a másodlagos hitelesítést nyújt:

![Fiók ellenőrzése](./media/nps-extension-vpn/image43.png)

Után már sikeresen hitelesített másodlagos módszer használatával, akkor a virtuális port a VPN-kiszolgáló a hozzáférési engedéllyel. Egy másodlagos hitelesítési módszert használja, ha megbízható eszközt a mobilalkalmazás használatával kellett, mert a bejelentkezési folyamat biztonságosabb, mint ha azt csak egy felhasználónév és jelszó kombinációjával használna.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Eseménynaplók sikeres bejelentkezési események megtekintése
A Windows eseménynaplójában sikeres bejelentkezési események megtekintéséhez a Windows biztonsági lekérdezés jelentkezzen be a hálózati házirend-kiszolgáló a következő PowerShell-parancs kiadásával:

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

![PowerShell biztonsági eseménynapló](./media/nps-extension-vpn/image44.png)
 
Megtekintheti a biztonsági napló vagy a hálózati házirend- és elérési szolgáltatások egyéni nézet, ahogy az itt látható:

![Hálózati házirend-kiszolgáló napló](./media/nps-extension-vpn/image45.png)

A kiszolgálón, amelyen a hálózati házirend-kiszolgáló-bővítmény telepítése az Azure multi-factor Authentication alkalmazás eseménynaplók jellemző, a bővítmény található *alkalmazások és szolgáltatások Logs\Microsoft\AzureMfa*. 

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

![Az Eseménynapló "Események száma" ablak](./media/nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató
A konfiguráció nem a várt módon működik, ha először a hibaelhárítás ellenőrzi, hogy a felhasználó többtényezős hitelesítés használatára van konfigurálva. A felhasználó lehet csatlakozni a [Azure-portálon](https://portal.azure.com). Ha a felhasználó másodlagos hitelesítést a rendszer, és sikeresen tudja elvégezni a hitelesítést, megszüntetheti a problémát, többtényezős hitelesítés helytelen konfigurációban.

Ha a többtényezős hitelesítés a felhasználó nem működik, tekintse át az érintett eseménynaplókat. A naplók a biztonsági esemény, az átjáró működik és az előző szakaszban tárgyalt Azure multi-factor Authentication-naplók tartalmazzák. 

A biztonsági napló, amely megjeleníti a sikertelen bejelentkezési esemény (a 6273-as Azonosítójú esemény) példa itt:

![A sikertelen bejelentkezési esemény megjeleníti a biztonsági napló](./media/nps-extension-vpn/image47.png)

Egy kapcsolódó esemény a Azure multi-factor Authentication naplóból itt jelenik meg:

![Az Azure multi-factor Authentication naplói](./media/nps-extension-vpn/image48.png)

Speciális hibaelhárítás Ehhez tekintse meg a hálózati házirend-kiszolgáló naplófájlok az NPS szolgáltatást futtató. A naplófájlok létrejönnek a _%SystemRoot%\System32\Logs_ vesszővel tagolt szövegfájlok mappában. A naplófájlok, olvassa el [értelmezhetők hálózati házirend-kiszolgáló naplófájlok](https://technet.microsoft.com/library/cc771748.aspx). 

Ezekben a naplófájlokban szereplő bejegyzések nehezen értelmezhetők, kivéve, ha egy táblázatot vagy egy adatbázis exportálhatja őket. Sok internetes hitelesítési szolgáltatás (IAS) elemzőeszközt online segítség nyújtása a naplófájlok értelmezése található. A kimenet egy ilyen letölthető [shareware alkalmazás](http://www.deepsoftware.com/iasviewer) itt jelenik meg: 

![Shareware alkalmazás](./media/nps-extension-vpn/image49.png)

További hibaelhárítási ehhez használhatja például a Wireshark protokollelemző vagy [Microsoft Message Analyzert](https://technet.microsoft.com/library/jj649776.aspx). Wireshark az alábbi ábrán a RADIUS-üzenetek a VPN-kiszolgáló és a hálózati házirend-kiszolgáló között.

![Microsoft Message Analyzert](./media/nps-extension-vpn/image50.png)

További információkért lásd: [a meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-factor Authentication](multi-factor-authentication-nps-extension.md). 

## <a name="next-steps"></a>Következő lépések
[Az Azure multi-factor Authentication hitelesítés beolvasása](multi-factor-authentication-versions-plans.md)

[Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](multi-factor-authentication-get-started-server-rdg.md)

[A helyszíni címtárak integrálása az Azure Active Directoryval](../active-directory/connect/active-directory-aadconnect.md)

