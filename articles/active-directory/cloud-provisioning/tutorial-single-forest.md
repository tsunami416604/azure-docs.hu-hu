---
title: Oktatóanyag – egyetlen erdő integrálása egyetlen Azure AD-bérlővel
description: Ez a témakör ismerteti az előfeltételeket és a hardverkövetelmények felhőkiépítés.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55dab553a93db4650a5d7126d7f1a0c3ca5f808f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332235"
---
# <a name="tutorial-integrate-a-single-forest-with-a-single-azure-ad-tenant"></a>Oktatóanyag: Egyetlen erdő integrálása egyetlen Azure AD-bérlővel

Ez az oktatóanyag bemutatja egy hibrid identitáskörnyezet létrehozását az Azure Active Directory (Azure AD) Connect felhőalapú kiépítés használatával.

![Létrehozás](media/tutorial-single-forest/diagram1.png)

Használhatja az oktatóanyagban létrehozott környezetet tesztelésre vagy a felhőkiépítés jobb megismerésére.

## <a name="prerequisites"></a>Előfeltételek
### <a name="in-the-azure-active-directory-admin-center"></a>Az Azure Active Directory felügyeleti központban

1. Hozzon létre egy csak felhőalapú globális rendszergazdai fiókot az Azure AD-bérlőn. Ily módon kezelheti a bérlő konfigurációját, ha a helyszíni szolgáltatások sikertelenek lesznek, vagy elérhetetlenné válnak. További információ [a csak felhőalapú globális rendszergazdai fiók hozzáadásáról.](../active-directory-users-create-azure-portal.md) Ez a lépés elengedhetetlen annak érdekében, hogy ne zárja ki a bérlő.
2. Adjon hozzá egy vagy több [egyéni tartománynevet](../active-directory-domains-add-azure-portal.md) az Azure AD-bérlőhöz. A felhasználók a tartománynevek egyikével jelentkezhetnek be.

### <a name="in-your-on-premises-environment"></a>A helyszíni környezetben

1. Azonosítsa a tartományhoz csatlakozó gazdakiszolgálót, amelyen windows Server 2012 R2 vagy nagyobb, legalább 4 GB RAM és .NET 4.7.1+ futtatókörnyezet telne 

2. Ha tűzfal van a kiszolgálók és az Azure AD között, konfigurálja a következő elemeket:
   - Győződjön meg arról, hogy az ügynökök *kimenő* kérelmeket tudnak végrehajtani az Azure AD-nek a következő portokon keresztül:

     | Portszám | Hogyan használják |
     | --- | --- |
     | **80** | Letölti a visszavont tanúsítványok listáját a TLS/SSL tanúsítvány érvényesítése közben |
     | **443** | Kezeli a szolgáltatással folytatott összes kimenő kommunikációt |
     | **8080** (nem kötelező) | Az ügynökök 10 percenként jelentik az állapotukat a 8080-as porton keresztül, ha a 443-as port nem érhető el. Ez az állapot jelenik meg az Azure AD portálon. |
     
     Ha a tűzfal a rendszertől függően kényszeríti a szabályokat, nyissa meg ezeket a portokat a hálózati szolgáltatásként futó Windows-szolgáltatásokból érkező forgalom számára.
   - Ha a tűzfal vagy a proxy lehetővé teszi a biztonságos utótagok megadását, akkor adja hozzá a t kapcsolatot ** \*a .msappproxy.net** és ** \*a .servicebus.windows.net**. Ha nem, engedélyezze a hozzáférést az [Azure-adatközpont IP-tartományaihoz,](https://www.microsoft.com/download/details.aspx?id=41653)amelyek hetente frissülnek.
   - Az ügynökeinek hozzáférésre van szükségük **login.windows.net** és **login.microsoftonline.com** az első regisztrációhoz. Nyissa meg a tűzfalat az URL-ek számára is.
   - A tanúsítványok érvényesítéséhez a következő URL-címek blokkolásának feloldásához: **mscrl.microsoft.com:80,** **crl.microsoft.com:80,** **ocsp.msocsp.com:80**és **\.www microsoft.com:80**. Mivel ezeket az URL-címeket más Microsoft-termékek tanúsítvány-ellenőrzésre használják, előfordulhat, hogy ezeket az URL-címeket már feloldotta.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Az Azure AD Connect kiépítési ügynök telepítése
1. Jelentkezzen be a tartományhoz csatlakozó kiszolgálóra.  Ha az [alapszintű AD és az Azure-környezet](tutorial-basic-ad-azure.md) oktatóanyag, lenne DC1.
2. Jelentkezzen be az Azure Portalon csak felhőalapú globális rendszergazdai hitelesítő adatokkal.
3. A bal oldalon válassza az **Azure Active Directory**lehetőséget, kattintson az Azure **AD Connect**elemre, és középen válassza a **Kiépítés kezelése (előzetes verzió) lehetőséget.**

   ![Azure portál](media/how-to-install/install6.png)

4. Kattintson **a Töltőügynök gombra.**
5. Futtassa az Azure AD Connect kiépítési ügynök.
6. A kezdőképen **fogadja el** a licencelési feltételeket, és kattintson a Telepítés **gombra.**

   ![Üdvözlőképernyő](media/how-to-install/install1.png)

7. A művelet befejezése után elindul a konfigurációs varázsló.  Jelentkezzen be az Azure AD globális rendszergazdai fiókjával.  Vegye figyelembe, hogy ha az IE fokozott biztonság engedélyezve van, ez blokkolja a bejelentkezést.  Ebben az esetben zárja be a telepítést, tiltsa le az IE fokozott biztonságát a Kiszolgálókezelőben, és a telepítés újraindításához kattintson az **AAD csatlakozáslétesítési ügynök varázslóra.**
8. Az **Active Directory csatlakoztatása** képernyőn kattintson a **Címtár hozzáadása** elemre, majd jelentkezzen be az Active Directory tartományi rendszergazdai fiókjával.  MEGJEGYZÉS: A tartományi rendszergazdai fióknak nem kell jelszómódosítási követelményekkel rendelkeznie. Abban az esetben, ha a jelszó lejár, vagy megváltozik, újra kell konfigurálnia az ügynököt az új hitelesítő adatokkal. Ez a művelet hozzáadja a helyszíni könyvtárat.  Kattintson a **Tovább** gombra.

   ![Üdvözlőképernyő](media/how-to-install/install3.png)

9. A **Konfiguráció teljes** képernyőjén kattintson a **Megerősítés gombra.**  Ez a művelet regisztrálja és újraindítja az ügynököt.

   ![Üdvözlőképernyő](media/how-to-install/install4.png)

10. A művelet befejezése után meg kell jelennie egy értesítést: **Az ügynök konfigurációja sikeresen ellenőrizhető.**  Kattintson **a Kilépés gombra.**</br>
![Üdvözlőképernyő](media/how-to-install/install5.png)</br>
11. Ha továbbra is látható a kezdőkép, kattintson a **Bezárás**gombra.


## <a name="verify-agent-installation"></a>Ügynök telepítésének ellenőrzése
Ügynök ellenőrzése történik az Azure Portalon és a helyi kiszolgálón, amely az ügynök fut.

### <a name="azure-portal-agent-verification"></a>Az Azure Portal ügynökének ellenőrzése
Annak ellenőrzéséhez, hogy az azure-beli ügynök az alábbi lépéseket láthatja-e:

1. Jelentkezzen be az Azure portálra.
2. A bal oldalon válassza az **Azure Active Directory**lehetőséget, kattintson az Azure **AD Connect** elemre, és középen válassza a **Kiépítés kezelése (előzetes verzió) lehetőséget.**</br>
![Azure Portal](media/how-to-install/install6.png)</br>

3.  Az **Azure AD-kiépítés (előzetes verzió)** képernyőn kattintson **az Összes ügyintéző áttekintése**elemre.
![Azure AD-kiépítés](media/how-to-install/install7.png)</br>
 
4. A **helyszíni létesítési ügynökök képernyőn** látni fogja a telepített ügynökök.  Ellenőrizze, hogy a kérdéses ügynök **active**létezik-e, és aktív-e.
![Kiépítési ügynökök](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>A helyi kiszolgálón
Annak ellenőrzéséhez, hogy az ügynök fut-e, kövesse az alábbi lépéseket:

1.  Bejelentkezés a kiszolgálóra rendszergazdai fiókkal
2.  Nyissa **meg a Szolgáltatásokat** úgy, hogy rá navigál, vagy a Start/Run/Services.msc.Open Services by either y navigating to it or by Start/Run/Services.msc.
3.  A **Szolgáltatások csoportban**győződjön meg arról, hogy a **Microsoft Azure AD Connect Agent Updater** és a **Microsoft Azure AD Connect provisioning Agent** jelen van, és az állapot **fut.**
![Szolgáltatások](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Az Azure AD Connect felhőalapú kiépítésének konfigurálása
 A kiépítés konfigurálásához kövesse az alábbi lépéseket.

1.  Jelentkezzen be az Azure AD-portálra.
2.  Kattintson **az Azure Active Directory elemre**
3.  Kattintson **az Azure AD Connect elemre**
4.  Válassza **a Kiépítés kezelése (előzetes verzió) lehetőséget.**
![](media/how-to-configure/manage1.png)
5.  Kattintson **az Új konfiguráció gombra**
![](media/tutorial-single-forest/configure1.png)
7.  A konfigurációs képernyőn írjon be egy **értesítési e-mailt,** helyezze a választót az **Engedélyezés gombra,** és kattintson a **Mentés gombra.**
![](media/tutorial-single-forest/configure2.png)
1.  A konfigurációs állapotnak most **kifogástalannak**kell lennie.
![](media/how-to-configure/manage4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>A felhasználók létrehozásának és szinkronizálásának ellenőrzése
Most ellenőrizze, hogy a helyszíni címtárban lévő felhasználók szinkronizálva lettek-e, és most már léteznek az Azure AD-bérlőben.  Ne feledje, hogy ez eltarthat néhány óráig.  A felhasználók szinkronizálásának ellenőrzéséhez tegye a következőket.


1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be egy Azure-előfizetéssel rendelkező fiókkal.
2. A bal oldalon válassza az **Azure Active Directory lehetőséget**
3. A **Kezelés** alatt válassza a **Felhasználókat**.
4. Ellenőrizze, hogy látja-e az új felhasználókat a bérlőben</br>
![Szinkronizál](media/tutorial-single-forest/synchronize1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Bejelentkezés tesztelése az egyik felhasználónkkal

1. Tallózás a[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Jelentkezzen be az új bérlőben létrehozott felhasználói fiókkal.  A következő formátumban kell bejelentkeznie: (user@domain.onmicrosoft.com). Használja ugyanazt a jelszót, amelyet a felhasználó a helyszíni bejelentkezéshez használ.</br>
   ![Ellenőrzés](media/tutorial-single-forest/verify1.png)</br>

Most már sikeresen beállított egy hibrid identitáskörnyezetet, amely segítségével tesztelheti és megismerkedhet az Azure ajánlatával.


## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
