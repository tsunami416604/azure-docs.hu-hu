---
title: Oktatóanyag – Kísérleti Azure AD Connect felhőkiépítés egy meglévő szinkronizált AD-erdőhöz
description: Bemutató.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aba42e6bd9b11e47d793219c0ff06b9177d609f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78298819"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Kísérleti felhőalapú jogosultságkiosztás meglévő, szinkronizált AD-erdő esetén 

Ez az oktatóanyag bemutatja a felhőkiépítés kísérleti tesztelése egy teszt Active Directory erdő, amely már szinkronizálva van az Azure Active Directory (Azure AD) Connect szinkronizálása.

![Létrehozás](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Megfontolandó szempontok
Az oktatóanyag kipróbálása előtt vegye figyelembe a következő elemeket:
1. Győződjön meg arról, hogy ismeri a felhőkiépítés alapjait. 
2. Győződjön meg arról, hogy az Azure AD Connect szinkronizálási verzióját futtatja az 1.4.32.0-s vagy újabb verzióval, és dokumentáltként konfigurálta a szinkronizálási szabályokat. A tesztelés során egy tesztszervezeti egységet vagy csoportot távolít el az Azure AD Connect szinkronizálási hatóköréből. Az objektumok hatókörön kívüli áthelyezése az objektumok törlését eredményezi az Azure AD-ben. Felhasználói objektumok esetén az Objektumok az Azure AD-ben helyreállíthatóan törlődnek, és visszaállíthatók. Csoportobjektumok esetén az objektumok az Azure AD-ben a törölt, és nem állítható vissza. Egy új kapcsolattípus került bevezetésre az Azure AD Connect szinkronizálás, amely megakadályozza a törlést egy próba-forgatókönyv esetén. 
3. Győződjön meg arról, hogy a kísérleti hatókörben lévő objektumok ms-ds-consistencyGUID-dal rendelkeznek, így a felhőkiépítési kemény megegyezik az objektumokkal. 

   > [!NOTE]
   > Az Azure AD Connect szinkronizálása alapértelmezés szerint nem lép fel *az ms-ds-consistencyGUID* csoportobjektumok esetében.

4. Ez egy speciális forgatókönyv. Győződjön meg arról, hogy pontosan követi az oktatóanyagban ismertetett lépéseket.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag befejezéséhez az alábbiakban az alábbi előfeltételek szükségesek
- Tesztkörnyezet az Azure AD Connect 1.4.32.0-s vagy újabb verziójával
- Olyan szervezeti egység vagy csoport, amely a szinkronizálás hatókörében van, és használható az próbapilótával. Azt javasoljuk, hogy egy kis objektumkészlettel kezdje.
- Windows Server 2012 R2 vagy újabb rendszert futtató kiszolgáló, amely a kiépítési ügynököt fogja üzemeltetni.  Ez nem lehet ugyanaz a kiszolgáló, mint az Azure AD Connect-kiszolgáló.
- Az AAD Connect szinkronizálásának forráshorgonyának *objectGuid* vagy *ms-ds-consistencyGUID* azonosítónak kell lennie

## <a name="update-azure-ad-connect"></a>Az Azure AD Connect frissítése

Legalább az Azure AD-csatlakozás1.4.32.0 [csatlakoztatása kell rendelkeznie.](https://www.microsoft.com/download/details.aspx?id=47594) Az Azure AD Connect szinkronizálásának frissítéséhez hajtsa végre az [Azure AD Connect: Frissítés a legújabb verzióra](../hybrid/how-to-upgrade-previous-version.md)című lépéseit.  

## <a name="stop-the-scheduler"></a>Az ütemező leállítása
Az Azure AD Connect szinkronizálása szinkronizálja a helyszíni címtárban egy ütemező használatával előforduló változásokat. Egyéni szabályok módosításához és hozzáadásához le kell tiltania az ütemezőt, hogy a szinkronizálások ne fussanak, amíg ezen dolgozik.  Ehhez a következő lépések szükségesek:

1.  Az Azure AD Connect szolgáltatást futtató kiszolgálón szinkronizálja a PowerShellt rendszergazdai jogosultságokkal.
2.  Futtassa az `Stop-ADSyncSyncCycle` parancsot.  Hit Enter.
3.  Futtassa az `Set-ADSyncScheduler -SyncCycleEnabled $false` parancsot.

>[!NOTE] 
>Ha saját egyéni ütemezőt futtat az AAD Connect szinkronizálásához, akkor tiltsa le az ütemezőt. 

## <a name="create-custom-user-inbound-rule"></a>Egyéni bejövő felhasználói szabály létrehozása

 1. Indítsa el a szinkronizálási szerkesztőt az asztali alkalmazás menüből az alábbi módon:</br>
 ![Szinkronizálási szabályszerkesztő menü](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. Válassza a **Bejövő** lehetőséget az Irány legördülő listából, és kattintson az **Új szabály hozzáadása**gombra.
 ![Egyéni szabály](media/how-to-cloud-custom-user-rule/user1.png)</br>
 
 3. A **Leírás** lapon írja be a következőt, és kattintson a **Tovább**gombra:

    **Név:** Jelentéssel bíró név a szabállyal<br>
    **Leírás:** Értelmes leírás hozzáadása<br>
    **Csatlakoztatott rendszer:** Válassza ki azt az AD-összekötőt, amelyhez az egyéni szinkronizálási szabályt írja<br>
    **Csatlakoztatott rendszerobjektum típusa:** Felhasználó<br>
    **Metaverzum objektum típusa:** Személy<br>
    **Hivatkozás típusa:** Csatlakozzon<br>
    **Elsőbbség:** Egyedi érték biztosítása a rendszerben<br>
    **Címke:** Hagyja üresen.<br>
    ![Egyéni szabály](media/how-to-cloud-custom-user-rule/user2.png)</br>
 
 4. A **Hatókörszűrő** lapon adja meg azt a szervezeti egységet vagy biztonsági csoportot, amelyet a próbagép en kívül szeretne látni.  A szervezeti egység szűréséhez adja hozzá a megkülönböztető név szervezeti egységrészét. Ez a szabály minden olyan felhasználóra vonatkozik, aki az adott szervezeti egységben van.  Ha tehát a DN végződése "OU=CPUsers,DC=contoso,DC=com, akkor hozzá kell adnia ezt a szűrőt.  Kattintson a **Tovább** gombra. 

    |Szabály|Attribútum|Művelet|Érték|
    |-----|----|----|-----|
    |Hatókör-hatókör|Dn|ENDSWITH|A szervezeti egység megkülönböztető neve.|
    |Hatókörcsoport||ISMEMBEROF (ISMEMBEROF)|A biztonsági csoport megkülönböztető neve.|

    ![Egyéni szabály](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. A Join rules **(Illesztési** szabályok) lapon kattintson a **Tovább**gombra.
 6. Az **Átalakítások** lapon adjon hozzá egy Állandó átalakítást: flow True to cloudNoFlow attribútum. Kattintson a **Hozzáadás** gombra.
 ![Egyéni szabály](media/how-to-cloud-custom-user-rule/user4.png)</br>

Minden objektumtípusnál (felhasználó, csoport és kapcsolattartó) ugyanazokat a lépéseket kell követnie. Ismételje meg a lépéseket konfigurált AD-összekötőnként/ AD-erdőnként. 

## <a name="create-custom-user-outbound-rule"></a>Egyéni kimenő felhasználói szabály létrehozása

 1. Válassza a **Kimenő** lehetőséget az Irány legördülő listából, majd kattintson a **Szabály hozzáadása**gombra.
 ![Egyéni szabály](media/how-to-cloud-custom-user-rule/user5.png)</br>
 
 2. A **Leírás** lapon írja be a következőt, és kattintson a **Tovább**gombra:

    **Név:** Jelentéssel bíró név a szabállyal<br>
    **Leírás:** Értelmes leírás hozzáadása<br>
    **Csatlakoztatott rendszer:** Válassza ki azt az AAD-összekötőt, amelyhez az egyéni szinkronizálási szabályt írja<br>
    **Csatlakoztatott rendszerobjektum típusa:** Felhasználó<br>
    **Metaverzum objektum típusa:** Személy<br>
    **Hivatkozás típusa:** JoinNoFlow (Csatlakozás nélküli folyamat)<br>
    **Elsőbbség:** Egyedi érték biztosítása a rendszerben<br>
    **Címke:** Hagyja üresen.<br>
    
    ![Egyéni szabály](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. A **Hatókörszűrő** lapon válassza a cloudNoFlow equal **True (Felhőszűrő**szűrő) lapot, és válassza a cloudNoFlow equal True **(Felhőalapú folyamat)** egyenlő igaz lehetőséget. Kattintson a **Tovább** gombra.
 ![Egyéni szabály](media/how-to-cloud-custom-user-rule/user7.png)</br>
 
 4. A Join rules **(Illesztési** szabályok) lapon kattintson a **Tovább**gombra.
 5. Az **Átalakítások** lapon kattintson a **Hozzáadás gombra.**

Minden objektumtípusnál (felhasználó, csoport és kapcsolattartó) ugyanazokat a lépéseket kell követnie.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Az Azure AD Connect kiépítési ügynök telepítése
1. Jelentkezzen be a vállalati rendszergazdai engedélyekkel használt kiszolgálóra.  Ha az [alapszintű AD és az Azure-környezet](tutorial-basic-ad-azure.md) bemutató lenne CP1.
2. Töltse le az Azure AD Connect felhőalapú kiépítési ügynökét az [itt](how-to-install.md#install-the-agent)ismertetett lépések segítségével.
3. Az Azure AD Connect felhőalapú kiépítésének futtatása (AADConnectProvisioningAgent.Installer)
3. A kezdőképen **fogadja el** a licencelési feltételeket, és kattintson a Telepítés **gombra.**</br>
![Üdvözlőképernyő](media/how-to-install/install1.png)</br>

4. A művelet befejezése után elindul a konfigurációs varázsló.  Jelentkezzen be az Azure AD globális rendszergazdai fiókjával.
5. Az **Active Directory csatlakoztatása** képernyőn kattintson a **Címtár hozzáadása** elemre, majd jelentkezzen be az Active Directory rendszergazdai fiókjával.  Ez a művelet hozzáadja a helyszíni könyvtárat.  Kattintson a **Tovább** gombra.</br>
![Üdvözlőképernyő](media/how-to-install/install3.png)</br>

6. A **Konfiguráció teljes** képernyőjén kattintson a **Megerősítés gombra.**  Ez a művelet regisztrálja és újraindítja az ügynököt.</br>
![Üdvözlőképernyő](media/how-to-install/install4.png)</br>

7. Miután ez a művelet befejeződött, látnia kell egy értesítést **A sikeresen ellenőrzött.**  Kattintson **a Kilépés gombra.**</br>
![Üdvözlőképernyő](media/how-to-install/install5.png)</br>
8. Ha továbbra is látható a kezdőkép, kattintson a **Bezárás**gombra.

## <a name="verify-agent-installation"></a>Ügynök telepítésének ellenőrzése
Ügynök ellenőrzése történik az Azure Portalon és a helyi kiszolgálón, amely az ügynök fut.

### <a name="azure-portal-agent-verification"></a>Az Azure Portal ügynökének ellenőrzése
Annak ellenőrzéséhez, hogy az azure-beli ügynök az alábbi lépéseket láthatja-e:

1. Jelentkezzen be az Azure portálra.
2. A bal oldalon válassza az **Azure Active Directory**lehetőséget, kattintson az Azure **AD Connect** elemre, és középen válassza a **Kiépítés kezelése (előzetes verzió) lehetőséget.**</br>
![Azure Portal](media/how-to-install/install6.png)</br>

3.  Az **Azure AD-kiépítés (előzetes verzió)** képernyőn kattintson **az Összes ügyintéző áttekintése**elemre.
![Azure AD-kiépítés](media/how-to-install/install7.png)</br>
 
4. A **helyszíni létesítési ügynökök képernyőn** látni fogja a telepített ügynökök.  Ellenőrizze, hogy a kérdéses ügynök létezik-e, és le van **tiltva.**  Az ügynök alapértelmezés ![szerint le van tiltva a kiépítési ügynökök számára](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>A helyi kiszolgálón
Annak ellenőrzéséhez, hogy az ügynök fut-e, kövesse az alábbi lépéseket:

1.  Bejelentkezés a kiszolgálóra rendszergazdai fiókkal
2.  Nyissa **meg a Szolgáltatásokat** úgy, hogy rá navigál, vagy a Start/Run/Services.msc.Open Services by either y navigating to it or by Start/Run/Services.msc.
3.  A **Szolgáltatások** csoportban győződjön meg arról, hogy a **Microsoft Azure AD Connect Agent Updater** és a **Microsoft Azure AD Connect provisioning Agent** ott van, és az állapot **fut.**
![Szolgáltatások](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Az Azure AD Connect felhőalapú kiépítésének konfigurálása
A kiépítés konfigurálásához kövesse az alábbi lépéseket:

 1. Jelentkezzen be az Azure AD-portálra.
 2. Kattintson **az Azure Active Directory elemre**
 3. Kattintson **az Azure AD Connect elemre**
 4. Válassza **a Kiépítés kezelése (előzetes verzió) lehetőséget.**
 ![](media/how-to-configure/manage1.png)</br>
 5.  Kattintson **az Új konfiguráció gombra**
 ![](media/tutorial-single-forest/configure1.png)</br>
 6.  A konfigurációs képernyőn írjon be egy **értesítési e-mailt,** helyezze a választót az **Engedélyezés gombra,** és kattintson a **Mentés gombra.**
 ![](media/tutorial-single-forest/configure2.png)</br>
 7. A **Konfigurálás**csoportban válassza a **Minden felhasználó** lehetőséget a konfigurációs szabály hatókörének módosításához.
 ![](media/how-to-configure/scope2.png)</br>
 8. A jobb oldalon módosítsa a hatókört úgy, hogy tartalmazza az imént létrehozott szervezeti egységet: "OU=CPUsers,DC=contoso,DC=com".
 ![](media/tutorial-existing-forest/scope2.png)</br>
 9.  Kattintson **a Kész** és **mentés gombra.**
 10. A hatókört most egy szervezeti egységre kell állítani. 
 ![](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>Ellenőrizze, hogy a felhasználók ki vannak-e építve a felhőalapú kiépítéssel
Most ellenőrizni fogja, hogy a felhasználók, amelyek a helyszíni címtárban szinkronizálásra került, és most már létezik az Azure AD-bérlő.  Ne feledje, hogy ez eltarthat néhány óráig.  Annak ellenőrzéséhez, hogy a felhasználók felhőalapú kiépítéssel vannak-e kiépítve, kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be egy Azure-előfizetéssel rendelkező fiókkal.
2. A bal oldalon válassza az **Azure Active Directory lehetőséget**
3. Kattintson az **Azure AD Connect**
4. Kattintson a **Kiépítés kezelése (előzetes verzió) elemre**
5. Kattintson a **Naplók gombra**
6. Felhasználónév keresése annak ellenőrzéséhez, hogy a felhasználó ki van-e építve a felhőkiépítés sel

Emellett ellenőrizheti, hogy a felhasználó és a csoport létezik-e az Azure AD-ben.

## <a name="start-the-scheduler"></a>Az ütemező indítása
Az Azure AD Connect szinkronizálása szinkronizálja a helyszíni címtárban egy ütemező használatával előforduló változásokat. Most, hogy módosította a szabályokat, újraindíthatja az ütemezőt.  Ehhez a következő lépések szükségesek:

1.  Az Azure AD Connect szolgáltatást futtató kiszolgálón szinkronizálja a powershell t a rendszergazdai jogosultságokkal való szinkronizálását
2.  Futtassa az `Set-ADSyncScheduler -SyncCycleEnabled $true` parancsot.
3.  Futtassa az `Start-ADSyncSyncCycle` parancsot.  Hit Enter.  

>[!NOTE] 
>Ha saját egyéni ütemezőt futtat az AAD Connect szinkronizálásához, engedélyezze az ütemezőt. 

## <a name="something-went-wrong"></a>Valami hiba történt
Abban az esetben, ha a próbapilóta nem a várt módon működik, az alábbi lépések végrehajtásával visszatérhet az Azure AD Connect szinkronizálási beállításához:
1.  Tiltsa le a létesítési konfigurációt az Azure Portalon. 
2.  Tiltsa le a felhőkiépítéshez létrehozott összes egyéni szinkronizálási szabályt a Szabályszerkesztő szinkronizálása eszközzel. A letiltás nak teljes szinkronizálást kell okoznia az összes összekötőn.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Az Azure AD Connect szinkronizálásának konfigurálása a próbaszervezeti egység kizárásához
Miután meggyőződött arról, hogy a kísérleti szervezeti egység felhasználói sikeresen felügyeltek a felhőkiépítéssel, újrakonfigurálhatja az Azure AD Connectet a fent létrehozott kísérleti szervezeti egység kizárásához.  A felhőkiépítési ügynök kezeli a szinkronizálást ezek a felhasználók a jövőben.  Az Azure AD Connect hatókörének hatóköre a következő lépésekkel.

 1. Az Azure AD Connect szolgáltatást futtató kiszolgálón kattintson duplán az Azure AD Connect ikonra.
 2. Kattintson a **Konfigurálás gombra**
 3. Válassza **a Szinkronizálási beállítások testreszabása lehetőséget,** és kattintson a tovább gombra.
 4. Jelentkezzen be az Azure AD szolgáltatásba, és kattintson a **Tovább**gombra.
 5. A **Könyvtárak csatlakoztatása** képernyőn kattintson a **Tovább gombra.**
 6. A **Tartomány- és szervezetiegység-szűrés** képernyőn jelölje be **a Kijelölt tartományok és szervezeti egységek szinkronizálása**jelölőnégyzetet.
 7. Bontsa ki a tartományt, és törölje a **CPUsers** ou **kijelölését.**  Kattintson a **Tovább** gombra.
![Hatókör](media/tutorial-existing-forest/scope1.png)</br>
 9. A **Választható szolgáltatások** képernyőn kattintson a **Tovább**gombra.
 10. A **Konfigurálásra kész** képernyőn kattintson a **Konfigurálás gombra.**
 11. Miután ez befejeződött, kattintson **a Kilépés**gombra. 

## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)

