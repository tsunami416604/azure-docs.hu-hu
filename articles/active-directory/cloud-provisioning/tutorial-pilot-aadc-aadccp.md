---
title: Oktatóanyag – kísérleti Azure AD Connect felhőalapú kiépítés meglévő szinkronizált AD-erdőhöz
description: Megtudhatja, hogyan tesztelheti a felhőalapú üzembe helyezést olyan tesztelési Active Directory erdőben, amely már szinkronizálva van Azure Active Directory (Azure AD) csatlakozási szinkronizálással.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b510464145f434f761cc28ba4e1c1361f983f8b
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2020
ms.locfileid: "95237391"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Kísérleti felhőalapú jogosultságkiosztás meglévő, szinkronizált AD-erdő esetén 

Ez az oktatóanyag végigvezeti egy olyan tesztelési célú felhőalapú kiépítési folyamaton, Active Directory erdőben, amely már szinkronizálva van a Azure Active Directory (Azure AD) csatlakozási szinkronizálás használatával.

![Létrehozás](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Megfontolandó szempontok
Az oktatóanyag kipróbálása előtt vegye figyelembe a következő elemeket:
1. Győződjön meg arról, hogy már ismeri a Felhőbeli kiépítés alapjait. 
2. Győződjön meg arról, hogy Azure AD Connect Sync 1.4.32.0 vagy újabb verzióját futtatja, és a szinkronizálási szabályokat dokumentált módon konfigurálta. A tesztelés során a rendszer eltávolítja egy teszt szervezeti egységet vagy csoportot Azure AD Connect szinkronizálási hatókörből. Az objektumok Hatókörön kívüli áthelyezése az objektumok az Azure AD-ben való törléséhez. Felhasználói objektumok esetén az Azure AD-objektumok nem törlődnek, és visszaállíthatók. Csoportosítási objektumok esetén az Azure AD-objektumok nem módosíthatók, és nem állíthatók vissza. Egy új hivatkozási típus lett bevezetve Azure AD Connect szinkronizálásban, amely megakadályozza a törlést a tesztelési forgatókönyvek esetén. 
3. Győződjön meg arról, hogy a kísérleti hatókörben lévő objektumok rendelkeznek ms-DS-consistencyGUID feltöltve, így a Felhőbeli kiépítés megnehezíti az objektumokat. 

   > [!NOTE]
   > Azure AD Connect a szinkronizálás nem tölti fel az *MS-DS-consistencyGUID* alapértelmezés szerint a csoport objektumaihoz.

4. Ez egy speciális forgatókönyv. Ügyeljen arra, hogy pontosan kövesse az ebben az oktatóanyagban ismertetett lépéseket.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következő előfeltételek szükségesek
- Tesztelési környezet Azure AD Connect Sync 1.4.32.0 vagy újabb verzióval
- Egy olyan szervezeti egység vagy csoport, amely a szinkronizálási hatókörben van, és használható a próba használatával. Azt javasoljuk, hogy kezdjen el egy kis mennyiségű objektummal.
- Egy Windows Server 2012 R2 vagy újabb rendszert futtató kiszolgáló, amely a kiépítési ügynököt fogja üzemeltetni.  Ez nem lehet a Azure AD Connect kiszolgálóval megegyező kiszolgáló.
- Azure AD Connect Sync forrás-horgonyának *ObjectGUID* vagy *MS-DS-consistencyGUID* értékűnek kell lennie

## <a name="update-azure-ad-connect"></a>Azure AD Connect frissítése

Legalább az [Azure ad-csatlakozási](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0 kell rendelkeznie. Azure AD Connect szinkronizálásának frissítéséhez hajtsa végre a [Azure ad Connect: frissítés a legújabb verzióra](../hybrid/how-to-upgrade-previous-version.md)című témakör lépéseit.  

## <a name="stop-the-scheduler"></a>Ütemező leállítása
Azure AD Connect szinkronizálás szinkronizálja a helyszíni címtárban bekövetkező változásokat egy ütemező használatával. Egyéni szabályok módosításához és hozzáadásához le szeretné tiltani az ütemező szolgáltatást, hogy a szinkronizálások ne fussanak, amíg éppen dolgozik.  Ehhez a következő lépések szükségesek:

1.  Azure AD Connect Sync alkalmazást futtató kiszolgálón nyissa meg a PowerShellt rendszergazdai jogosultságokkal.
2.  Futtassa a `Stop-ADSyncSyncCycle` parancsot.  Nyomja meg az ENTER billentyűt.
3.  Futtassa a `Set-ADSyncScheduler -SyncCycleEnabled $false` parancsot.

>[!NOTE] 
>Ha Azure AD Connect szinkronizáláshoz saját egyéni ütemező alkalmazást futtat, tiltsa le az ütemező szolgáltatást. 

## <a name="create-custom-user-inbound-rule"></a>Egyéni felhasználó bejövő szabályának létrehozása

 1. Indítsa el a szinkronizációs szerkesztőt az asztal alkalmazás menüjében az alábbi ábrán látható módon:</br>
 ![Szinkronizációs szabály szerkesztője menü](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. Válassza a **bejövő** lehetőséget a legördülő listából az irányhoz, és kattintson az **új szabály hozzáadása** lehetőségre.
 ![A szinkronizálási szabályok megtekintése és kezelése ablak "bejövő" és "új szabály hozzáadása" gombjának bejelölését bemutató képernyőkép.](media/how-to-cloud-custom-user-rule/user1.png)</br>
 
 3. A **Leírás** lapon adja meg a következőt, majd kattintson a **tovább** gombra:

    **Név:** Adjon egy értelmes nevet a szabálynak<br>
    **Leírás:** Adjon hozzá egy értelmes Leírást<br>
    **Csatlakoztatott rendszerek:** Válassza ki azt az AD-összekötőt, amelyhez egyéni szinkronizálási szabályt írunk<br>
    **Csatlakoztatott rendszerobjektum típusa:** Felhasználói<br>
    **Metaverse objektum típusa:** Személy<br>
    **Hivatkozás típusa:** Csatlakozás<br>
    **Elsőbbség:** A rendszer egyedi értékének megadása<br>
    **Címke:** Hagyja üresen<br>
    ![A "bejövő szinkronizálási szabály létrehozása – Leírás" oldal megjelenítése a megadott értékekkel.](media/how-to-cloud-custom-user-rule/user2.png)</br>
 
 4. A **hatókör-szűrő** lapon adja meg azt a szervezeti egységet vagy biztonsági csoportot, amelyet a próbaüzem alapján ki szeretne kapcsolni.  A szervezeti egység szűréséhez adja hozzá a megkülönböztető név OU részét. Ez a szabály az adott szervezeti egységben lévő összes felhasználóra vonatkozik.  Tehát ha a DN "OU = processzorok, DC = contoso, DC = com" végződéssel végződik, akkor ezt a szűrőt fogja felvenni.  Ezután kattintson a **Tovább** gombra. 

    |Szabály|Attribútum|Operátor|Érték|
    |-----|----|----|-----|
    |Hatóköri szervezeti egység|DN|ENDSWITH|A szervezeti egység megkülönböztető neve.|
    |Hatókör-csoport||ISMEMBEROF|A biztonsági csoport megkülönböztető neve.|

    ![Képernyőfelvétel: a "bejövő szinkronizálási szabály létrehozása – hatóköri szűrő" lap, amely tartalmazza a megadott hatókör-szűrő értékét.](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. Az **illesztési** szabályok lapon kattintson a **tovább** gombra.
 6. Az **átalakítások** lapon adjon hozzá egy állandó átalakítást: a flow igaz értéket a cloudNoFlow attribútumhoz. Kattintson a **Hozzáadás** parancsra.
 ![Képernyőkép a "bejövő szinkronizálási szabály létrehozása – átalakítások" oldal "állandó átalakítás" folyamatával.](media/how-to-cloud-custom-user-rule/user4.png)</br>

Ugyanezeket a lépéseket kell követni minden objektumtípus esetében (felhasználó, csoport és kapcsolattartó). Ismételje meg a lépéseket egy konfigurált AD-összekötő/AD-erdő alapján. 

## <a name="create-custom-user-outbound-rule"></a>Egyéni felhasználói Kimenő szabály létrehozása

 1. Válassza a **kimenő** lehetőséget a legördülő listából az irányhoz, és kattintson a **szabály hozzáadása** elemre.
 ![Képernyőkép, amely a "kimenő" irányt jeleníti meg, és az "új szabály hozzáadása" gomb ki van emelve.](media/how-to-cloud-custom-user-rule/user5.png)</br>
 
 2. A **Leírás** lapon adja meg a következőt, majd kattintson a **tovább** gombra:

    **Név:** Adjon egy értelmes nevet a szabálynak<br>
    **Leírás:** Adjon hozzá egy értelmes Leírást<br>
    **Csatlakoztatott rendszerek:** Válassza ki azt az Azure AD-összekötőt, amelyhez egyéni szinkronizálási szabályt írunk<br>
    **Csatlakoztatott rendszerobjektum típusa:** Felhasználói<br>
    **Metaverse objektum típusa:** Személy<br>
    **Hivatkozás típusa:** JoinNoFlow<br>
    **Elsőbbség:** A rendszer egyedi értékének megadása<br>
    **Címke:** Hagyja üresen<br>
    
    ![Képernyőkép, amely a "Description" (Leírás) lapot mutatja a megadott tulajdonságokkal.](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. A **hatókör-szűrő** lapon válassza a **cloudNoFlow** egyenlő **igaz** értéket. Ezután kattintson a **Tovább** gombra.
 ![Egyéni szabály](media/how-to-cloud-custom-user-rule/user7.png)</br>
 
 4. Az **illesztési** szabályok lapon kattintson a **tovább** gombra.
 5. Az **átalakítások** lapon kattintson a **Hozzáadás** gombra.

Ugyanezeket a lépéseket kell követni minden objektumtípus esetében (felhasználó, csoport és kapcsolattartó).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Az Azure AD Connect létesítési ügynök telepítése
1. Jelentkezzen be a-kiszolgálóra, amelyet a vállalati rendszergazdai engedélyekkel fog használni.  Ha az  [alapszintű ad-és Azure-környezetbeli](tutorial-basic-ad-azure.md) oktatóanyagot használja, az CP1 lenne.
2. Töltse le a Azure AD Connect Cloud kiépítési ügynököt az [itt](how-to-install.md#install-the-agent)ismertetett lépések segítségével.
3. Az Azure AD Connect Cloud kiépítés (AADConnectProvisioningAgent. Installer) futtatása
3. A splash képernyőn **fogadja el** a licencelési feltételeket, majd kattintson a **telepítés** gombra.</br>
![Képernyőfelvétel: "Microsoft Azure A D kapcsolat létesítési ügynöke" splash screen.](media/how-to-install/install-1.png)</br>

4. A művelet befejezése után elindul a konfigurációs varázsló.  Jelentkezzen be az Azure AD globális rendszergazdai fiókjával.
5. A **Active Directory összekapcsolása** képernyőn kattintson a **könyvtár hozzáadása** lehetőségre, majd jelentkezzen be a Active Directory rendszergazdai fiókjával.  Ezzel a művelettel a helyszíni címtárat fogja felvenni.  Kattintson a **Tovább** gombra.</br>
![Képernyőfelvétel: a "kapcsolat Active Directory" képernyő egy megadott könyvtár értékkel.](media/how-to-install/install-3a.png)</br>

6. A **konfiguráció kész** képernyőn kattintson a **Confirm (megerősítés**) gombra.  Ez a művelet regisztrálja és újraindítja az ügynököt.</br>
![Képernyőfelvétel: a "jóváhagyás" gomb kiválasztásakor megjelenik a "konfigurálás kész" képernyő.](media/how-to-install/install-4a.png)</br>

7. Miután a művelet befejeződik, meg kell jelennie arról, hogy **a sikeres ellenőrzést** észlelte.  Kattintson a **Kilépés** lehetőségre.</br>
![Üdvözlőképernyő](media/how-to-install/install-5.png)</br>
8. Ha továbbra is megjelenik a kezdeti splash képernyő, kattintson a **Bezárás** gombra.

## <a name="verify-agent-installation"></a>Ügynök telepítésének ellenőrzése
Az ügynök ellenőrzése a Azure Portal és az ügynököt futtató helyi kiszolgálón történik.

### <a name="azure-portal-agent-verification"></a>Azure Portal ügynök ellenőrzése
Az alábbi lépéseket követve ellenőrizheti, hogy az ügynök látja-e az Azure-t:

1. Jelentkezzen be az Azure Portalra.
2. A bal oldalon válassza a **Azure Active Directory**, majd a **Azure ad Connect** , és a központban válassza a **felügyelet kiépítés (előzetes verzió)** lehetőséget.</br>
![Azure Portal](media/how-to-install/install-6.png)</br>

3.  Az **Azure ad-kiépítés (előzetes verzió)** képernyőn kattintson az **összes ügynök áttekintése** elemre.
![Azure AD-kiépítés](media/how-to-install/install-7.png)</br>
 
4. A helyszíni **kiépítési ügynökök képernyőjén** látni fogja a telepített ügynököket.  Ellenőrizze, hogy a szóban forgó ügynök van-e **Letiltva**, és le van-e jelölve.  Az ügynök alapértelmezés szerint le van tiltva a ![ kiépítési ügynököknél](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>A helyi kiszolgálón
Az ügynök futtatásának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Jelentkezzen be a kiszolgálóra egy rendszergazdai fiókkal
2.  Nyissa meg a szolgáltatásokat vagy navigáljon a **szolgáltatáshoz** , vagy indítsa el a Start/Run/Services. msc parancsot.
3.  A **szolgáltatások** területen győződjön meg arról, hogy **Microsoft Azure ad az ügynök frissítése** és a **Microsoft Azure ad kapcsolat létesítése ügynök** van ott, és az állapota **fut**.
![Szolgáltatások](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect felhőalapú kiépítés konfigurálása
A kiépítés konfigurálásához kövesse az alábbi lépéseket:

 1. Jelentkezzen be az Azure AD-portálra.
 2. Kattintson **Azure Active Directory**
 3. Kattintson **Azure ad Connect**
 4. Válassza a **felügyelet létesítése (előzetes verzió)** 
  ![ képernyőképet, amely a "kiépítés kezelése (előzetes verzió)" hivatkozást mutatja.](media/how-to-configure/manage1.png)</br>
 5.  Az **New Configuration** 
  ![ Azure ad-kiépítés (előzetes verzió) képernyő új konfiguráció képernyőképére kattintva kiemelve jelenik meg az "új konfiguráció" hivatkozás.](media/tutorial-single-forest/configure1.png)</br>
 6.  A konfiguráció képernyőn adja meg az **értesítő e-mailt**, helyezze át a választót az **engedélyezéshez** , majd kattintson a **Mentés** gombra.
 ![Képernyőkép a konfigurálásról a képernyőn megjelenő értesítő e-mailben és a kijelölés engedélyezése lapon.](media/tutorial-single-forest/configure2.png)</br>
 7. A **Konfigurálás** területen válassza a **minden felhasználó** lehetőséget a konfigurációs szabály hatókörének módosításához.
 ![Képernyőkép a konfigurálás képernyőről "minden felhasználó" lehetőség mellett, a "hatókör felhasználói" elem mellett.](media/how-to-configure/scope2.png)</br>
 8. A jobb oldalon módosítsa a hatókört úgy, hogy az imént létrehozott adott szervezeti egységet tartalmazza: "OU = processzorok, DC = contoso, DC = com".
 ![Képernyőfelvétel: a hatókör felhasználói képernyő kiemelte a hatókört a létrehozott szervezeti egységre.](media/tutorial-existing-forest/scope2.png)</br>
 9.  Kattintson a **kész** és a **Mentés** gombra.
 10. A hatókört most egy szervezeti egységre kell beállítani. 
 ![Képernyőkép a configure (Konfigurálás) képernyőről "1 szervezeti egység" a "hatókörű felhasználók" elem mellett.](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>A felhőalapú kiépítés által kiépített felhasználók ellenőrzése
Ekkor ellenőrzi, hogy a helyszíni címtárban lévő felhasználók szinkronizálva lettek-e, és már léteznek-e az Azure AD-bérlőben.  Vegye figyelembe, hogy ez eltarthat néhány óráig.  Az alábbi lépéseket követve ellenőrizheti, hogy a felhasználók kiépítik-e a felhőalapú kiépítési folyamatokat:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be egy Azure-előfizetéssel rendelkező fiókkal.
2. A bal oldalon válassza a **Azure Active Directory**
3. Kattintson **Azure ad Connect**
4. Kattintson a **kiépítés kezelése (előzetes verzió)** elemre.
5. Kattintson a **naplók** gombra
6. Keresse meg a felhasználónevet, és győződjön meg arról, hogy a felhasználó kiosztotta a Felhőbeli kiépítés

Ezen felül ellenőrizheti, hogy a felhasználó és a csoport létezik-e az Azure AD-ben.

## <a name="start-the-scheduler"></a>Az ütemező elindítása
Azure AD Connect szinkronizálás szinkronizálja a helyszíni címtárban bekövetkező változásokat egy ütemező használatával. Most, hogy módosította a szabályokat, újraindíthatja az ütemező programot.  Ehhez a következő lépések szükségesek:

1.  Azure AD Connect Sync alkalmazást futtató kiszolgálón rendszergazdai jogosultságokkal nyissa meg a PowerShellt
2.  Futtatja a `Set-ADSyncScheduler -SyncCycleEnabled $true` parancsot.
3.  Futtatja a `Start-ADSyncSyncCycle` parancsot.  Nyomja meg az ENTER billentyűt.  

>[!NOTE] 
>Ha Azure AD Connect szinkronizáláshoz saját egyéni ütemező alkalmazást futtat, engedélyezze az ütemező szolgáltatást. 

Ha az ütemező engedélyezve lett, Azure AD Connect leállítja az objektumon végzett módosítások exportálását a `cloudNoFlow=true` metaverse-ban, kivéve, ha a hivatkozási attribútum (például a kezelő) frissítése folyamatban van. Ha az objektumon van egy hivatkozási attribútum frissítése, Azure AD Connect figyelmen kívül hagyja a `cloudNoFlow` jelet, és exportálja az összes frissítést az objektumra.

## <a name="something-went-wrong"></a>Valami hiba történt
Ha a próbaüzem nem a várt módon működik, térjen vissza a Azure AD Connect Sync telepítőhöz az alábbi lépésekkel:
1.  A Azure Portal kiépítési konfigurációjának letiltása. 
2.  Tiltsa le a Felhőbeli kiépítés során létrehozott összes egyéni szinkronizálási szabályt a szinkronizálási szabály szerkesztő eszközének használatával. A letiltás esetén az összes összekötőn teljes szinkronizálást kell okozni.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Azure AD Connect szinkronizálás konfigurálása a kísérleti szervezeti egység kizárásához
Miután meggyőződött róla, hogy a kísérleti szervezeti egység felhasználóinak felügyelete sikeresen megtörtént a felhőalapú kiépítés során, a Azure AD Connect újbóli konfigurálásával kizárhatja a fentebb létrehozott kísérleti szervezeti egységet.  A felhő-kiépítési ügynök kezeli a felhasználók szinkronizálását a jövőben.  Az alábbi lépéseket követve Azure AD Connect hatókörét.

 1. A Azure AD Connect rendszert futtató kiszolgálón kattintson duplán a Azure AD Connect ikonra.
 2. Kattintson a **Konfigurálás** elemre.
 3. Válassza a **szinkronizálási beállítások testreszabása lehetőséget** , majd kattintson a Tovább gombra.
 4. Jelentkezzen be az Azure AD-be, és kattintson a **tovább** gombra.
 5. A **címtárak összekapcsolása** képernyőn kattintson a **tovább** gombra.
 6. A **tartomány és szervezeti egység szűrése** képernyőn válassza a **kiválasztott tartományok és szervezeti egységek szinkronizálása** lehetőséget.
 7. Bontsa ki a tartományt, és **törölje** a **processzorok** szervezeti egységét.  Kattintson a **Tovább** gombra.
![hatókör](media/tutorial-existing-forest/scope1.png)</br>
 9. A **választható szolgáltatások** képernyőn kattintson a **tovább** gombra.
 10. A **konfigurálásra kész** képernyőn kattintson a **Konfigurálás** elemre.
 11. A befejezést követően kattintson a **Kilépés** gombra. 

## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)

