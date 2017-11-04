---
title: "VPN-integráció az Azure MFA használata a hálózati házirend-kiszolgáló bővítmény |} Microsoft Docs"
description: "A cikk ismerteti a VPN-infrastruktúra integrálása az Azure MFA használata a hálózati házirend-kiszolgáló (NPS) bővítmény a Microsoft Azure."
services: active-directory
keywords: "Az Azure MFA integrálja a VPN-, Azure Active Directoryban, hálózati házirend-kiszolgáló bővítmény"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: kgremban
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 8ac4c5d88e724febf21fe6bcc8ecf939283f361e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="integrate-your-vpn-infrastructure-with-azure-multi-factor-authentication-mfa-using-the-network-policy-server-nps-extension-for-azure"></a>A VPN-infrastruktúra integrálása az Azure multi-factor Authentication (MFA) a hálózati házirend-kiszolgáló (NPS) bővítményének használatával az Azure-bA

## <a name="overview"></a>Áttekintés

A hálózati házirend-kiszolgáló (NPS) az Azure-bővítmény lehetővé teszi a szervezetek védelme távoli Authentication Dial-In User Service (RADIUS) ügyfél-hitelesítéshez felhőalapú [Azure multi-factor Authentication (MFA)](multi-factor-authentication-get-started-server-rdg.md), amely biztosítja a kétlépéses ellenőrzést.

Ez a cikk ismerteti a hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure MFA használatával a hálózati házirend-kiszolgáló az Azure-bővítmény engedélyezése a felhasználóknak a hálózathoz egy VPN-kapcsolattal biztonságos kétlépéses ellenőrzést. 

A hálózati házirend- és hozzáférés-szolgáltatások (NPS) révén a szervezetek az alábbi képességek:

* Adja meg a felügyeleti és adhatja meg a kapcsolódást, mely napszakokban kapcsolatok engedélyezve legyenek, a kapcsolatok időtartama, és az ügyfeleket használata a csatlakozáshoz, és így tovább biztonsági szint kérelmek irányítását központi helyét. Helyett adja meg, ezek a szabályzatok minden VPN vagy a távoli asztal (RD) átjáró kiszolgálón, ezek a házirendek egyszer adható meg egy központi helyen. A RADIUS protokollal segítségével biztosít a központosított hitelesítési, engedélyezési és nyilvántartási (AAA). 
* Állítson be, és kényszeríteni a hálózatvédelem (NAP) ügyfél állapotházirendeket, amelyek meghatározzák, hogy eszközök hálózati erőforrások korlátozás nélküli vagy korlátozott hozzáférést kapnak.
* Olyan hitelesítési és engedélyezési 802.1 hozzáférés kényszerítésére biztosít x-kompatibilis vezeték nélküli hozzáférési pontok és Ethernet-kapcsolók.    

További információkért lásd: [hálózati házirend-kiszolgáló (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

Növelje a biztonságot, és magas szintű megfelelőség, a szervezetek integrálható az Azure MFA Használatát annak érdekében, hogy a felhasználók a kétlépéses ellenőrzést tenni használja a hálózati házirend-kiszolgáló csatlakozzon a virtuális port a VPN-kiszolgálón. A felhasználók számára a hozzáférést meg kell adniuk a felhasználónév/jelszó kombináció, amely a felhasználó rendelkezik-e a vezérlőben adatokkal. Ezeket az információkat megbízható kell, és a rendszer egyszerűen nem lettek duplikálva, például a mobiltelefonszám, a vezetékes számát, a kérelem egy mobileszközön, és így tovább.

A bővítmény rendelkezésre állását a hálózati házirend-kiszolgáló az Azure-ba, mielőtt az ügyfelek, akik az integrált hálózati házirend-kiszolgáló és az Azure MFA környezetek kétlépéses ellenőrzés végrehajtásához szükséges kellett konfigurálnia és karbantartania egy külön MFA kiszolgáló a helyszíni környezetben, ahogy Távoli asztali átjáró és Azure multi-factor Authentication kiszolgáló RADIUS használata.

A bővítmény rendelkezésre állását a hálózati házirend-kiszolgáló az Azure lehetőséget nyújt a szervezetek biztonságos RADIUS-ügyfél-hitelesítés vagy egy helyszíni MFA-megoldását, vagy egy MFA felhőalapú megoldás telepítendő választás.
 
## <a name="authentication-flow"></a>Hitelesítési folyamat
Amikor egy felhasználó csatlakozik egy virtuális port a VPN-kiszolgálón, először hitelesítenie kell különböző protokollok, amelyek lehetővé teszik a felhasználónév és jelszó és a tanúsítványalapú hitelesítési módszerek használatát. 

A hitelesítés és identitás ellenőrzése mellett a felhasználók a megfelelő-e tárcsázni engedélyekkel kell rendelkeznie. Olyan egyszerű megvalósításokhoz, a ezek betárcsázási engedélyek, amelyek lehetővé teszik a hozzáférést az Active Directory-felhasználói objektumok közvetlenül a vannak beállítva. 

 ![Felhasználói tulajdonságok](./media/nps-extension-vpn/image1.png)

Olyan egyszerű megvalósításokhoz a VPN-kiszolgáló engedélyezi vagy megtagadja a hozzáférést minden helyi VPN-kiszolgálón meghatározott házirendek alapján.

Nagyobb és több méretezhető implementációk esetén a házirendeket, hogy engedélyezze vagy megtagadják a VPN-hozzáférésre van központi RADIUS-kiszolgálókon. Ebben az esetben a VPN-kiszolgáló működik egy hozzáférési (RADIUS-ügyfél), amely továbbítja a csatlakozási kérelmek és egy RADIUS-kiszolgáló fiók üzenetek. Szeretne csatlakozni a virtuális port a VPN-kiszolgálón, a felhasználók kell hitelesíteni, és a RADIUS-kiszolgálók központilag meghatározott feltételeknek. 

Ha a hálózati házirend-kiszolgáló az Azure-bővítmény integrálva van a hálózati házirend-kiszolgáló, a sikeres hitelesítési folyamat következőképpen történik:

1. A VPN-kiszolgáló-hitelesítési kérést kap egy VPN-felhasználó, amely tartalmazza a felhasználónévvel és jelszóval való csatlakozás erőforráshoz, például a távoli asztali munkamenetet. 
2. Egy RADIUS-ügyfél, VPN-kiszolgáló alakítja át a kérelem egy RADIUS-kérést üzenet és az üzenet küldése (a jelszó titkosított) a RADIUS (NPS) a kiszolgálóra, amelyen telepítve van-e a hálózati házirend-kiszolgáló bővítmény. 
3. A felhasználónév és jelszó kombinációjával ellenőrzése az Active Directoryban. Ha a felhasználónév / jelszó érvénytelen, a RADIUS-kiszolgáló hozzáférés-utasítsa el az üzenetet küld. 
4. Ha a hálózati házirend-kiszolgáló kapcsolódási kérelem és a hálózati házirendek meghatározott feltételek mindegyike teljesül (például időpont vagy csoport tagsági korlátozások), a hálózati házirend-kiszolgáló bővítményt váltja ki az Azure MFA másodlagos hitelesítési kérelmet. 
5. Az Azure MFA kommunikál az Azure Active Directory, lekéri a felhasználó adatait, és végrehajtja a másodlagos hitelesítést (SMS-üzenet, mobilalkalmazás és így tovább) a felhasználó által beállított metódussal. 
6. Az MFA-kérdést sikeres, akkor az Azure MFA kommunikál a hálózati házirend-kiszolgáló bővítmény eredménye.
7. Miután a kapcsolódási kísérlet is hitelesítése és engedélyezése, a hálózati házirend-kiszolgáló, amelyen telepítve van-e a bővítmény RADIUS Access-Accept üzenetet küld a VPN-kiszolgáló (RADIUS-ügyfél).
8. A felhasználó hozzáférést kap a virtuális port a VPN-kiszolgálón, és egy titkosított VPN-alagutat hoz létre.

## <a name="prerequisites"></a>Előfeltételek
Ez a szakasz részletesen az Azure MFA integrálása a távoli asztali átjáró előtt szükséges előfeltételeket. Mielőtt hozzákezd, rendelkeznie kell a következő előfeltételek teljesülését helyen.

* VPN-infrastruktúra
* Hálózati házirend- és hozzáférés-szolgáltatások (NPS) szerepkör
* Az Azure MFA-licenc
* Windows Server szoftver
* Szalagtárak
* Az Azure AD szinkronizálni a helyszíni AD 
* Az Azure Active Directory GUID azonosítója

### <a name="vpn-infrastructure"></a>VPN-infrastruktúra
Ez a cikk feltételezi, hogy rendelkezik-e a Microsoft Windows Server 2016 használatával helyen működő VPN-infrastruktúra és, hogy a VPN-kiszolgáló nincs beállítva továbbítási kapcsolat kéréseket egy RADIUS-kiszolgáló. A VPN-infrastruktúra egy központi RADIUS-kiszolgáló használatára a jelen útmutató konfigurál.

Ha nem rendelkeznek olyan működő infrastruktúra helyen, gyorsan hozhat létre az infrastruktúra számos VPN telepítő oktatóanyagok található a Microsoft és a külső webhelyek által megadott utasítások szerint. 

### <a name="network-policy-and-access-services-nps-role"></a>Hálózati házirend- és hozzáférés-szolgáltatások (NPS) szerepkör

Az NPS szerepkör-szolgáltatást a RADIUS-kiszolgáló és az ügyfél funkciókat biztosít. Ez a cikk feltételezi, hogy telepítette az NPS szerepkör a tagkiszolgáló vagy tartományvezérlő a környezetben. A jelen útmutató egy VPN-konfiguráció RADIUS konfigurálja. Telepítse az NPS szerepkör a kiszolgálón _más_ mint a VPN-kiszolgáló.

A hálózati házirend-kiszolgáló szerepkör telepítésével kapcsolatos információt szolgáltatás a Windows Server 2012 vagy újabb rendszerre, lásd: [a NAP állapotházirend-kiszolgáló telepítése](https://technet.microsoft.com/library/dd296890.aspx). Hálózati házirend (NAP) elavult a Windows Server 2016. Ajánlott eljárások a hálózati házirend-kiszolgáló, beleértve a hálózati házirend-kiszolgáló telepítése tartományvezérlőn, az ajánlás leírását lásd: [ajánlott eljárások a hálózati házirend-kiszolgáló](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Az Azure MFA-licenc

A licenc szükség van az Azure MFA számára, amely elérhető az Azure AD Premium, nagyvállalati mobilitási és biztonsági (EMS) vagy az MFA szolgáltatásra. További információkért lásd: [beolvasásával Azure multi-factor Authentication](multi-factor-authentication-versions-plans.md). Tesztelési célokra használható a próba-előfizetést.

### <a name="windows-server-software"></a>Windows Server szoftver

A hálózati házirend-kiszolgáló-bővítményhez olyan Windows Server 2008 R2 SP1 vagy újabb, az NPS szerepkör-szolgáltatás telepítve. Ez az útmutató lépései a Windows Server 2016 végeztek.

### <a name="libraries"></a>Szalagtárak

Ezek a könyvtárak a rendszer automatikusan telepíti a kiterjesztéssel.

-   [Visual C++ újraterjeszthető csomag a Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Active Directory modul Windows Powershellhez készült Azure 1.1.166.0 verziója](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

A Microsoft Active Directory modul Windows Powershellhez készült Azure telepítve van, ha még nincs jelen, a telepítési folyamat részeként futtatja konfigurációs parancsfájl használatával. Nincs szükség a modul időben telepítésére, ha még nincs telepítve.

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>A helyszíni Active Directoryval szinkronizálva az Azure Active Directory 

A hálózati házirend-kiszolgáló kiterjesztés használatára, a helyszíni felhasználók kell az Azure Active Directoryval szinkronizált és a többtényezős hitelesítés engedélyezve van. Ez az útmutató feltételezi, hogy a helyszíni felhasználók az Azure AD Connect használatával Active Directoryval van szinkronizálva. Így a felhasználók a multi-factor Authentication tartozó utasításokat alatt.
Információk az Azure AD connect című [integrálása a helyszíni címtárakat az Azure Active Directoryval](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>Az Azure Active Directory GUID azonosítója 
Az NPS-bővítményének telepítése kell tudni, hogy az Azure Active Directory GUID-azonosítója. A GUID-azonosítója az Azure Active Directory-kereséshez utasításokat a következő szakaszban.

## <a name="configure-radius-for-vpn-connections"></a>A VPN-kapcsolatok RADIUS konfigurálása

Ha már telepítette a hálózati házirend-kiszolgáló kiszolgálói szerepkör azon a tagkiszolgálón, hitelesítéséhez és engedélyezéséhez a VPN-ügyfél adott kérelem VPN-kapcsolatok konfigurálni szeretné. 

Ez a szakasz azt feltételezi, hogy a hálózati házirend-kiszolgálói szerepkörrel telepített, de nem konfigurált, használja a infrastruktúrában.

>[!NOTE]
>Ha már van egy működő VPN-kiszolgáló egy központi RADIUS-kiszolgálót használ, ez a szakasz kihagyhatja.
>

### <a name="register-server-in-active-directory"></a>Kiszolgáló regisztrálása az Active Directoryban
Ebben a forgatókönyvben megfelelően működni, a hálózati házirend-kiszolgáló regisztrálva kell lennie az Active Directoryban.

1. Nyissa meg a Kiszolgálókezelőt.
2. A Kiszolgálókezelőben kattintson **eszközök**, és kattintson a **hálózati házirend-kiszolgáló**. 
3. A hálózati házirend-kiszolgáló konzol, kattintson a jobb gombbal **hálózati házirend-kiszolgáló (helyi)**, és kattintson a **kiszolgáló regisztrálása az Active Directoryban**. Kattintson a **OK** kétszer.

 ![Hálózati házirend-kiszolgáló](./media/nps-extension-vpn/image2.png)

4. Hagyja nyitva a következő eljárással a konzolt.

### <a name="use-wizard-to-configure-radius-server"></a>RADIUS-kiszolgáló konfigurálása varázsló segítségével
Használhatja a szabványos (varázsló-alapú) vagy speciális konfigurációs beállítás konfigurálása a RADIUS-kiszolgáló. Ez a szakasz azt feltételezi, hogy a varázsló alapú szabványos konfigurációs beállítás használatát.

1. Kattintson a hálózati házirend-kiszolgáló konzolon **hálózati házirend-kiszolgáló (helyi)**.
2. Standard beállítás csoportban **RADIUS-kiszolgáló telefonos vagy VPN-kapcsolatok**, és kattintson a **konfigurálása VPN vagy a telefonos**.

 ![Konfigurálja a VPN](./media/nps-extension-vpn/image3.png)

3. Válassza ki a telefonos vagy virtuális magánhálózati kapcsolatok hálózattípus lapon jelölje be **virtuális magánhálózati kapcsolatok**, és kattintson a **következő**.

 ![Virtuális magánhálózat](./media/nps-extension-vpn/image4.png)

4. Adja meg a telefonos vagy VPN-kiszolgáló lapon kattintson a **Hozzáadás**.
5. Az a **új RADIUS-ügyfél** párbeszédpanelen adjon meg egy rövid nevet, adja meg a VPN-kiszolgáló IP-cím vagy feloldható nevét, és adjon meg egy megosztott titkos jelszót. Győződjön meg a közös titkos jelszó hosszú és összetett. Jegyezze fel ezt a jelszót, csak a következő szakasz lépéseit.

 ![Új RADIUS-ügyfél](./media/nps-extension-vpn/image5.png)

6. Kattintson a **OK**, majd **következő**.
7. Az a **hitelesítési módszerek konfigurálása** lap, fogadja el az alapértelmezésként beállított elemet (Microsoft titkosított hitelesítés 2 (MS-CHAPv2) vagy más lehetőséget választ, és kattintson a **következő**.

  >[!NOTE]
  >Ha konfigurálja az Extensible Authentication Protocol (EAP), MS-CHAPv2 vagy PEAP kell használnia. Nincs más EAP esetén támogatott.
 
8. Adja meg a felhasználói csoportok lapon kattintson **Hozzáadás** , majd válasszon egy megfelelő, ha van ilyen. Ellenkező esetben hagyja meg az üres hozzáférést minden felhasználó számára.

 ![Adja meg a felhasználói csoportok](./media/nps-extension-vpn/image7.png)

9. Kattintson a **Tovább** gombra.
10. Az IP-szűrők megadása lapon kattintson a **következő**.
11. Adja meg a titkosítási beállítások lapon fogadja el az alapértelmezett beállításokat, és kattintson a **következő**.

 ![Adja meg az Encryption](./media/nps-extension-vpn/image8.png)

12. A tartománynév megadása, fogadja el a neve üres, elfogadja az alapértelmezett beállítást, és kattintson **következő**.

 ![Tartománynév megadása](./media/nps-extension-vpn/image9.png)

13. Az új befejezése telefonos vagy virtuális magánhálózati kapcsolatok és a RADIUS-ügyfelek lapot, kattintson a **Befejezés**.

 ![Fejezze be a kapcsolatok száma](./media/nps-extension-vpn/image10.png)

### <a name="verify-radius-configuration"></a>A RADIUS konfigurálásának ellenőrzése
Ez a szakasz részletesen a konfiguráció varázsló segítségével létrehozott.

1. A hálózati házirend-kiszolgálón, a hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki a RADIUS-ügyfelek, és válassza **RADIUS-ügyfelek**.
2. A részleteket tartalmazó ablaktáblán kattintson a jobb gombbal a RADIUS-ügyfél varázslóval létrehozott, és kattintson a **tulajdonságok**. A tulajdonságok a RADIUS-ügyfél (a VPN-kiszolgáló) hasonló a lent látható módon kell lennie.

 ![VPN-tulajdonságai](./media/nps-extension-vpn/image11.png)

3. Kattintson a **Mégse**.
4. A hálózati házirend-kiszolgálón, a hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki a **házirendek**, és válassza ki **kapcsolatkérelem-házirendek**. A VPN-kapcsolatok házirendet, amely hasonlít az alábbi képen kell megjelennie.

 ![Csatlakozási kérések](./media/nps-extension-vpn/image12.png)

5. Válassza ki a házirend, **hálózati házirendek**. Az alábbi képen levő virtuális magánhálózati (VPN) kapcsolatok házirend akkor.

 ![Hálózat tulajdonságai](./media/nps-extension-vpn/image13.png)

## <a name="configure-vpn-server-to-use-radius-authentication"></a>Konfigurálja a VPN-kiszolgáló RADIUS-hitelesítés használata
Ez a szakasz a VPN-kiszolgáló RADIUS-hitelesítés használatára konfigurálja. Ez a szakasz azt feltételezi, hogy VPN-kiszolgáló működő konfigurációval rendelkezik, de nem konfigurálta a VPN-kiszolgáló RADIUS-hitelesítés használata. A VPN-kiszolgáló konfigurálása után, győződjön meg arról, hogy a várt módon működik-e a konfiguráció.

>[!NOTE]
>Ha már van egy működő VPN-kiszolgáló beállítása, amely a RADIUS-hitelesítést használ, ez a szakasz kihagyhatja.
>

### <a name="configure-authentication-provider"></a>Hitelesítésszolgáltató konfigurálása
1. A VPN-kiszolgálón nyissa meg a Kiszolgálókezelőt.
2. A Kiszolgálókezelőben kattintson **eszközök**, majd **Útválasztás és távelérés**.
3. Az Útválasztás és távelérés konzolon kattintson a jobb gombbal  **\[kiszolgálónév\] (helyi)**, és kattintson a **tulajdonságok**.

 ![Útválasztás és távelérés](./media/nps-extension-vpn/image14.png)
 
4. Az a **[kiszolgálónév} (helyi) tulajdonságok** párbeszédpanel, kattintson a **biztonsági** fülre. 
5. Az a **biztonsági** lapon a hitelesítésszolgáltató, kattintson a **RADIUS-hitelesítés**, majd **konfigurálása**.

 ![RADIUS-hitelesítés](./media/nps-extension-vpn/image15.png)
 
6. A RADIUS-hitelesítés párbeszédpanelen kattintson a **Hozzáadás**.
7. A RADIUS-kiszolgáló hozzáadása, a kiszolgáló nevét adja hozzá a nevét vagy a RADIUS-kiszolgáló, az előzőekben konfigurált IP-címét.
8. Kattintson a közös titok **módosítás** és adja meg a közös titkos jelszót létrehozni, és korábban rögzített.
9. Időtúllépés (másodpercben), módosítsa az értéket közötti értékre **30** és **60**. Erre akkor szükség, hagyjon elegendő időt a második hitelesítési tényezővel befejezéséhez.
 
 ![RADIUS-kiszolgáló hozzáadása](./media/nps-extension-vpn/image16.png)
 
10. Kattintson a **OK** összes párbeszédpanel bezárásával végrehajtásáig.

### <a name="test-vpn-connectivity"></a>VPN-kapcsolat tesztelése
Ebben a szakaszban, győződjön meg arról, hogy a VPN-ügyfél hitelesítése és engedélyezése a RADIUS-kiszolgáló csatlakozni a virtuális port VPN tett kísérlet során. Ez a szakasz feltételezi, hogy a Windows 10 és a VPN-ügyfélként. 

>[!NOTE]
>Ha már konfigurált egy VPN-ügyfél a VPN-kiszolgálóhoz való csatlakozáshoz, és mentette a beállítások, konfigurálása és mentése egy VPN-kapcsolati objektum kapcsolatos lépéseket kihagyhatja.
>

1. A VPN-ügyfél számítógépen kattintson **Start**, majd **beállítások** (fogaskerék ikonra).
2. Kattintson az ablak-beállítások **hálózat és Internet**.
3. Kattintson a **VPN**.
4. Kattintson a **egy VPN-kapcsolat hozzáadása**.
5. A VPN-kapcsolat, adjon meg Windows (beépített) a VPN-szolgáltatóként, majd végezze el a többi mező, szükség esetén, és kattintson **mentése**. 

 ![VPN-kapcsolat hozzáadása](./media/nps-extension-vpn/image17.png)
 
6. Nyissa meg a **hálózati és megosztási központ** a Vezérlőpulton.
7. Kattintson a **Adapterbeállítások módosítása**.

 ![Adapterbeállítások módosítása](./media/nps-extension-vpn/image18.png)

8. Kattintson a jobb gombbal a VPN-hálózati kapcsolat, és kattintson a Tulajdonságok elemre. 

 ![VPN-hálózati tulajdonságok](./media/nps-extension-vpn/image19.png)

9. A VPN-tulajdonságai párbeszédpanelen kattintson a **biztonsági** fülre. 
10. A biztonság lapon győződjön meg arról, hogy csak **Microsoft CHAP 2-es Version (MS-CHAP v2)** van kiválasztva, és kattintson az OK gombra.

 ![Protokollok engedélyezése](./media/nps-extension-vpn/image20.png)

11. Kattintson a jobb gombbal a VPN-kapcsolatot, és kattintson a **Connect**.
12. Kattintson a beállítások lap **Connect**.

A sikeres kapcsolat megjelenik a RADIUS-kiszolgálón Event ID 6272, mint a biztonsági napló alább látható módon.

 ![Esemény tulajdonságai](./media/nps-extension-vpn/image21.png)

## <a name="troubleshoot-guide"></a>Az útmutató hibaelhárítása
Tegyük fel, hogy a VPN-konfiguráció dolgozott előtt konfigurálta a VPN-kiszolgáló egy központi RADIUS-kiszolgáló használatára a hitelesítéshez és engedélyezéshez. Ebben az esetben valószínű, hogy a probléma oka lehet egy helytelen konfigurálása a RADIUS-kiszolgáló vagy egy érvénytelen felhasználónév vagy jelszó használata. Például, ha a felhasználónév az alternatív UPN-utótagot használja, a bejelentkezési kísérlet sikertelen lehet (kell használnia a fiók néven a legjobb eredmények elérése érdekében). 

Ezek a problémák elhárításához egy ideális kiindulási, a RADIUS-kiszolgáló biztonsági eseménynaplók vizsgálata. Mentése ideje eseményeket keres, akár is használhatja a szerepkör-alapú hálózati házirend- és kiszolgáló egyéni nézet az eseménynaplóban, az alábbi megjelenítése. A 6273-as Azonosítójú esemény azt jelzi, hogy eseményeket, ahol a hálózati házirend-kiszolgáló megtagadta a hozzáférést egy felhasználó. 

 ![Eseménynapló](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Többtényezős hitelesítés beállítása
A szakasz ismerteti, így a felhasználók a multi-factor Authentication és a kétlépéses ellenőrzéshez fiókok beállításával kapcsolatos utasításokat. 

### <a name="enable-multi-factor-authentication"></a>A többtényezős hitelesítés engedélyezése
Ebben a szakaszban az Azure AD-fiókok a multi-factor Authentication engedélyezése. Használja a **klasszikus portál** engedélyezése a felhasználók a multi-factor Authentication. 

1. Nyisson meg egy böngészőt, és navigáljon a [https://manage.windowsazure.com](https://manage.windowsazure.com). 
2. Jelentkezzen be rendszergazdaként.
3. Kattintson a portálon a bal oldali navigációs **ACTIVE DIRECTORY**.

 ![Alapértelmezett könyvtár](./media/nps-extension-vpn/image23.png)

4. A Név oszlopban kattintson **alapértelmezett címtárat** (vagy egy másik címtárban, ha szükséges).
5. Kattintson a gyors üzembe helyezés lap **konfigurálása**.

 ![Alapértelmezett konfigurálása](./media/nps-extension-vpn/image24.png)

6. A KONFIGURÁLÁS oldalon görgessen lefelé, és a többtényezős hitelesítés területen kattintson a **szolgáltatás beállításainak kezelése**.

 ![Többtényezős hitelesítés beállításainak kezelése](./media/nps-extension-vpn/image25.png)
 
7. A multi-factor Authentication hitelesítés lapon tekintse át a szolgáltatás alapértelmezett beállításait, és kattintson **felhasználók**. 

 ![MFA Users (MFA-felhasználók)](./media/nps-extension-vpn/image26.png)
 
8. A felhasználók oldalon válassza a felhasználók a multi-factor Authentication engedélyezése, és kattintson a kívánt **engedélyezése**.

 ![Tulajdonságok](./media/nps-extension-vpn/image27.png)
 
9. Amikor a rendszer kéri, kattintson a **multi-factor auth engedélyezése**.

 ![Többtényezős hitelesítés engedélyezése](./media/nps-extension-vpn/image28.png)
 
10. Kattintson a **Bezárás** gombra. 
11. Frissítse az oldalt. A többtényezős hitelesítés állapota engedélyezve.

A felhasználók a multi-factor Authentication engedélyezéséről további információért lásd: [Ismerkedés az Azure multi-factor Authentication a felhőben](multi-factor-authentication-get-started-cloud.md). 

### <a name="configure-accounts-for-two-step-verification"></a>A kétlépéses ellenőrzéshez fiókok beállítása
Amennyiben a fiók engedélyezve van az MFA szolgáltatásra, a felhasználók nem rendelkeznek használva jelentkezhet be a többtényezős hitelesítési szabályzat által szabályozott, amíg sikeresen konfigurálta a második hitelesítési tényező, hogy kétlépéses ellenőrzés használandó megbízható eszköz erőforrások.

Ebben a szakaszban egy megbízható eszköz konfigurál a kétlépéses ellenőrzéshez használttal. Többféle módon is konfigurálásukkal, többek között a következőket:

* **Mobilalkalmazás**. A Microsoft Authenticator alkalmazást telepít egy Windows Phone, Android vagy iOS-eszközön. Attól függően, hogy a szervezet házirendjeit szükségesek az alkalmazás használatát a két mód egyikében: kapnak értesítést az ellenőrzések (értesítés leküldeni az eszközre) vagy (a következőket kell végrehajtania a ellenőrző kódot, amely frissíti az ellenőrző kód használata 30 másodpercenként). 
* **Mobiltelefon hívást vagy SMS**. Az automatizált telefonhívást vagy SMS-üzenet vagy fogadni. A telefonhívás kapcsolóval a hívás, majd nyomja meg a # jelet hitelesítéséhez. A szöveg beállítással válaszoljon az üzenetre, vagy a bejelentkezési felületen meg kell adnia az ellenőrző kódot.
* **Irodai telefon hívása**. Ez a folyamat megegyezik, amelyek a fenti automatikus telefonhívásokat.

Kövesse ezeket az utasításokat, az eszköz beállítása a mobilalkalmazás használatára az ellenőrzéshez a leküldéses értesítések fogadásához.

1. Jelentkezzen be [https://aka.ms/mfasetup](https://aka.ms/mfasetup) vagy egyetlen helyen, például a [https://portal.azure.com](https://portal.azure.com), amely szükséges az MFA-kompatibilis hitelesítő adatokkal végezhessenek hitelesítést. 
2. Bejelentkezés a felhasználónevét és jelszavát, akkor lehetősége lesz kéri, hogy további biztonsági ellenőrzés a fiók beállítása képernyő.

 ![További biztonsági](./media/nps-extension-vpn/image29.png)

3. Kattintson a **most beállítása**.
4. A további biztonsági ellenőrzési lapot válassza a kapcsolattartó típusa (hitelesítéshez megadott telefonját, irodai telefon vagy mobilalkalmazás). Válasszon olyan országban vagy régióban, majd válasszon ki egy módszert. A módszer függ a kapcsolattartási típust választja. Például ha a mobilalkalmazás lehetőséget választja, kiválaszthatja hogy az ellenőrzési értesítések fogadásához, vagy egy ellenőrző kód használata. A következő lépések azt feltételezik, hogy úgy dönt, **mobilalkalmazás** kapcsolattartási típusként.

 ![Telefonos hitelesítési](./media/nps-extension-vpn/image30.png)

5. Jelölje ki azt a mobilalkalmazás **értesítéseket az ellenőrzéshez**, majd **beállítása**. 

 ![Mobilalkalmazás ellenőrzése](./media/nps-extension-vpn/image31.png)
 
6. Ha még nem tette meg, telepítse a authenticator mobilalkalmazás az eszközön. 
7. Kövesse az utasításokat a bemutatott vonalkód beolvasása, vagy adja meg manuálisan az adatokat, és kattintson a mobilalkalmazás **végzett**.

 ![Mobilalkalmazás konfigurálása](./media/nps-extension-vpn/image32.png)

8. A további biztonsági ellenőrzési lapot, kattintson **forduljon me** és értesítést küld az eszköz választ.
9. A további biztonsági ellenőrzési lapon adja meg egy számot, ha megszakadna a mobilalkalmazással, és kattintson a **következő**.

 ![Mobiltelefonszám](./media/nps-extension-vpn/image33.png)
 
10. Kattintson a további biztonsági ellenőrzést **végzett**.

Az eszköz konfigurálva van egy második ellenőrzési módszert biztosít. A kétlépéses ellenőrzéshez fiókok beállításával kapcsolatos információkért lásd: [a kétlépéses ellenőrzéshez a fiók beállítása](./end-user/multi-factor-authentication-end-user-first-time.md).

## <a name="install-and-configure-nps-extension"></a>Telepítse és konfigurálja a hálózati házirend-kiszolgáló bővítmény

Ez a szakasz ismerteti VPN konfigurálása az Azure MFA használata az ügyfél-hitelesítéshez a VPN-kiszolgálóval.

Miután telepítése és konfigurálása a hálózati házirend-kiszolgáló bővítményt, a kiszolgáló által feldolgozott összes RADIUS-alapú ügyfél-hitelesítés Azure MFA használatához szükséges. Ha nem a VPN-felhasználók az Azure MFA-ban regisztrált, akkor állíthat be egy másik RADIUS-kiszolgáló hitelesíti a felhasználókat, akik nem MFA használatára vannak konfigurálva. Vagy hozhat létre egy beállításjegyzékbeli bejegyzést, amely lehetővé teszi egy második hitelesítési tényezővel kifogásolt megadását, csak akkor, ha léptetheti be többtényezős Hitelesítést. 

Hozzon létre egy új karakterláncértéket _a HKLM\SOFTWARE\Microsoft\AzureMfa REQUIRE_USER_MATCH_, majd az értékét állítsa igaz vagy HAMIS eredményt ad. 

 ![Felhasználóegyeztetés megkövetelése](./media/nps-extension-vpn/image34.png)
 
Ha az értéke igaz értékre kell beállítani, vagy nincs beállítva, minden hitelesítési kérelemre vonatkoznak az MFA-kérdést. Ha a beállítás értéke FALSE, MFA kihívást csak azoknak a felhasználóknak a többtényezős hitelesítés regisztrált adják ki. Csak akkor használja a FALSE beállítást tesztelési vagy üzemi környezetben bevezetési időszak alatt.

### <a name="acquire-azure-active-directory-guid-id"></a>Szerezzen be az Azure Active Directory GUID azonosítója

A hálózati házirend-kiszolgáló bővítmény konfigurációjának részeként kell megadnia a rendszergazdai hitelesítő adatokat és az Azure Active Directory-azonosítója az Azure AD-bérlő. Az alábbi lépéseket mutatja be a bérlői azonosító beszerzése

1. Jelentkezzen be az Azure portálon, a [https://portal.azure.com](https://portal.azure.com) az Azure-bérlőhöz globális rendszergazdájaként.
2. A bal oldali navigációs, kattintson a **Azure Active Directory** ikonra.
3. Kattintson a **Tulajdonságok** elemre.
4. A könyvtár-azonosítója a vágólapra másolásához jelölje ki a **másolási** ikonra.
 
 ![Könyvtár-azonosítója](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>A hálózati házirend-kiszolgáló-kiterjesztés telepítése
A hálózati házirend-kiszolgáló bővítmény telepítve kell lennie egy kiszolgálót, amely a hálózati házirend- és hozzáférés-szolgáltatások (NPS) szerepkör telepítve van és a funkciók kialakításában RADIUS-kiszolgálóként. Ne telepítse az NPS-bővítmény a távoli asztal-kiszolgálón.

1. Töltse le a hálózati házirend-kiszolgáló bővítményt a [https://aka.ms/npsmfa](https://aka.ms/npsmfa). 
2. Másolja a végrehajtható fájl (NpsExtnForAzureMfaInstaller.exe) a hálózati házirend-kiszolgáló.
3. A hálózati házirend-kiszolgálón kattintson duplán a **NpsExtnForAzureMfaInstaller.exe**. Ha a rendszer kéri, kattintson a **futtatása**.
4. A hálózati házirend-kiszolgáló bővítmény Azure MFA telepítése párbeszédpanelen tekintse át a szoftverlicenc-szerződést, ellenőrizze **elfogadom a licencfeltételeket és a feltételek**, és kattintson a **telepítése**.

 ![Hálózati házirend-kiszolgáló bővítmény](./media/nps-extension-vpn/image36.png)
 
5. Kattintson a hálózati házirend-kiszolgáló bővítmény Azure MFA beállítása párbeszédpanel **Bezárás**.  

 ![A telepítő sikeres](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>A hálózati házirend-kiszolgáló PowerShell-parancsfájl használatával kiterjesztésű használt tanúsítványok konfigurálása
Győződjön meg arról, biztonságos kommunikációt, és biztosítani, az NPS-kiterjesztés által használt tanúsítványok konfigurálása kell. A hálózati házirend-kiszolgáló összetevői az alábbiak a Windows PowerShell-parancsfájlt, amely beállítja a hálózati házirend-kiszolgáló egy önaláírt tanúsítványt. 

A parancsfájl a következő műveleteket hajtja végre:

* Létrehoz egy önaláírt tanúsítványt
* Társítja az egyszerű szolgáltatásnév az Azure AD a tanúsítvány nyilvános kulcsa
* A tanúsítvány a helyi számítógép tárolójában tárolja
* A tanúsítvány titkos kulcsa a hálózati felhasználók számára a hozzáférést
* Hálózati házirend-kiszolgáló szolgáltatás újraindítása

Ha szeretné használni a saját tanúsítványait, szeretné a tanúsítványt, a szolgáltatás egyszerű nyilvános kulcsát társítsa Azure ad-val, és így tovább.
A parancsfájl használatát, adja meg az Azure Active Directory rendszergazdai hitelesítő adatokat és az Azure Active Directory-Bérlőazonosító korábban kimásolt a bővítményt. Futtassa a parancsfájlt minden hálózati házirend-kiszolgálón, ahol az NPS-bővítményének telepítése.

1. Nyisson meg egy felügyeleti Windows PowerShell-parancssort.
2. A PowerShell-parancssorba írja be a _cd "c:\Program Files\Microsoft\AzureMfa\Config"_, és nyomja le az ENTER **ENTER**.
3. Típus _.\AzureMfsNpsExtnConfigSetup.ps1_, és nyomja le az ENTER **ENTER**. 
 * A parancsfájl ellenőrzi, hogy ha az Azure Active Directory PowerShell-modul telepítve van-e. Ha nincs telepítve, a parancsfájl telepíti a modult.
 
 ![PowerShell](./media/nps-extension-vpn/image38.png)
 
4. Miután a parancsfájl a PowerShell-modul ellenőrzi, az Azure Active Directory PowerShell modul párbeszédpanel jelenik meg. A párbeszédpanelen adja meg a Azure AD rendszergazdai hitelesítő adatait, és a jelszót, és kattintson **bejelentkezés**. 
 
 ![PowerShell-bejelentkezés](./media/nps-extension-vpn/image39.png)
 
5. Amikor a rendszer kéri, illessze be a vágólapra korábban kimásolt Bérlőazonosító, és nyomja le az ENTER **ENTER**. 

 ![Bérlőazonosító](./media/nps-extension-vpn/image40.png)

6. A parancsfájl létrehoz egy önaláírt tanúsítványt, és más konfigurációs módosításokat hajt végre. A kimeneti olyan, mint az alább látható kép.

 ![Önaláírt tanúsítvány](./media/nps-extension-vpn/image41.png)

7. Indítsa újra a kiszolgálót.
 
### <a name="verify-configuration"></a>Konfiguráció ellenőrzése
A konfiguráció ellenőrzése kell létrehozni egy új VPN-kapcsolatot a VPN-kiszolgálóval. Belépéskor sikeresen megtörtént a hitelesítő adatait az elsődleges hitelesítéshez, a VPN-kapcsolatot vár a másodlagos hitelesítés sikeres, a kapcsolat létrejötte előtt alább látható módon. 

 ![Konfiguráció ellenőrzése](./media/nps-extension-vpn/image42.png)

Sikeres hitelesítést az Azure MFA korábban konfigurált másodlagos hitelesítési módszert, ha csatlakozik a erőforrás. Ha a másodlagos hitelesítés nem sikeres, azonban vannak az erőforráshoz való hozzáférés megtagadva. 

Az alábbi példában a hitelesítő alkalmazás Windows Phone-eszközön segítségével adja meg a másodlagos hitelesítést.

 ![Fiók ellenőrzése](./media/nps-extension-vpn/image43.png)

Miután sikeresen hitelesítette a másodlagos metódussal, akkor a virtuális port a VPN-kiszolgálón hozzáférési engedéllyel. Azonban Ön volt egy másodlagos hitelesítési módszer használatával, ha megbízható eszközt használatához szükséges, mert a bejelentkezési folyamat biztonságosabb, mint azt kellene használatával csak a felhasználónév / jelszó kombináció.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Eseménynaplók sikeres bejelentkezési események megtekintése
A Windows eseménynaplójában a sikeres bejelentkezési események megtekintéséhez a következő Windows PowerShell-parancsot a Windows biztonsági naplónak a hálózati házirend-kiszolgáló lekérdezéséhez adhat ki.

A biztonsági eseménynapló sikeres bejelentkezés eseményeinek lekérdezéséhez használja a következő parancsot,
* _Get-WinEvent - naplónév biztonsági_ |} ahol {$_.ID - eq "6272"} |} FL 

 ![Biztonsági eseménynapló](./media/nps-extension-vpn/image44.png)
 
Megtekintheti a biztonsági napló vagy a hálózati házirend- és elérési szolgáltatások egyéni nézet alább látható módon:

 ![Hálózati házirend-hozzáférés](./media/nps-extension-vpn/image45.png)

A kiszolgálón, amelyen a hálózati házirend-kiszolgáló-bővítmény telepítése az Azure MFA található alkalmazás eseménynaplók adott, a bővítmény **alkalmazások és szolgáltatások Logs\Microsoft\AzureMfa**. 

* _Get-WinEvent - naplónév biztonsági_ |} ahol {$_.ID - eq "6272"} |} FL

 ![Események (event) száma](./media/nps-extension-vpn/image46.png)

## <a name="troubleshoot-guide"></a>Az útmutató hibaelhárítása
A konfiguráció nem a várt módon működik, ha egy remek kezdőpont elhárítása ellenőrzése, hogy a felhasználó Azure MFA használatára van konfigurálva. A felhasználó csatlakozni [https://portal.azure.com](https://portal.azure.com). Ha a rendszer kéri a másodlagos hitelesítés, és sikeresen be tud hitelesíti, megszüntetheti az Azure MFA helytelen konfigurációban.

Ha a felhasználó Azure MFA szolgáltatás működik, tekintse át a megfelelő eseménynaplóit. Ezek közé tartozik, a biztonsági esemény, az átjáró működik és az előző szakaszban tárgyalt Azure MFA-naplókat. 

Alább egy példa kimenet megjeleníti a sikertelen bejelentkezési esemény (Event ID 6273) a biztonsági napló van:

 ![Biztonsági napló](./media/nps-extension-vpn/image47.png)

Alább az a AzureMFA naplókból kapcsolódó esemény:

 ![Az Azure MFA-naplók](./media/nps-extension-vpn/image48.png)

Végrehajtásához advanced beállítások elhárításával kapcsolatos tudnivalókat tekintse meg a hálózati házirend-kiszolgáló naplófájlok ahol a hálózati házirend-kiszolgáló szolgáltatás telepítve van. Ezekben a naplófájlokban létrejönnek _%SystemRoot%\System32\Logs_ vesszővel tagolt szövegfájlok mappában. Ezek leírását naplófájlok című [értelmezhetők hálózati házirend-kiszolgáló naplófájlok](https://technet.microsoft.com/library/cc771748.aspx). 

Ezekben a naplófájlokban szereplő bejegyzések nehezen nélkül importálja őket egy táblázatot vagy egy adatbázis értelmezhetők. IAS elemzők online segítség nyújtása a naplófájlok értelmezése számos található. Az alábbiakban van a kimenet egy ilyen letölthető [shareware alkalmazás](http://www.deepsoftware.com/iasviewer): 

 ![Shareware alkalmazás](./media/nps-extension-vpn/image49.png)

Végezetül, a további beállítások elhárításával kapcsolatos tudnivalókat is használhat, például a Wireshark protokollelemző vagy [Microsoft Message Analyzert](https://technet.microsoft.com/library/jj649776.aspx). Wireshark az alábbi ábrán a RADIUS-üzenetek a VPN-kiszolgáló és a hálózati házirend-kiszolgáló között.

 ![Microsoft Message Analyzert](./media/nps-extension-vpn/image50.png)

További információkért lásd: [a meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-factor Authentication](multi-factor-authentication-nps-extension.md).  

## <a name="next-steps"></a>Következő lépések
[Az Azure Multi-Factor Authentication beszerzése](multi-factor-authentication-versions-plans.md)

[Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](multi-factor-authentication-get-started-server-rdg.md)

[A helyszíni címtárak integrálása az Azure Active Directoryval](../active-directory/connect/active-directory-aadconnect.md)

