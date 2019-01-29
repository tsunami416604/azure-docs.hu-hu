---
title: 'Azure AD Connect: Tervezési alapelvek |} A Microsoft Docs'
description: Ez a témakör részletesen bizonyos megvalósítási terv területek
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 668382479cee2f9049c09be1952e684e39077091
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173683"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Tervezési alapelvek
A jelen dokumentum célja, hogy írja le, amely az Azure AD Connect megvalósítási tervezése során kell gondoltam területeket. Ez a dokumentum az egyes területeken egy részletes bemutatása, és ezek a fogalmak rövid leírását, valamint az egyéb dokumentumokat.

## <a name="sourceanchor"></a>sourceAnchor
A sourceAnchor attribútum típusúként van definiálva *objektum élettartama alatt megváltoztathatatlan attribútum*. Azt egyedileg azonosít egy objektumot, hogy az azonos objektum a helyszíni és az Azure ad-ben. Az attribútum néven is ismert **immutableId** és a két nevet használt felcserélhetők.

Nem módosítható, word, azaz "nem módosítható", fontos, hogy ez a dokumentum. Mivel ez az attribútum értéke nem módosítható, miután van beállítva, fontos válasszon, amely támogatja a forgatókönyvet.

Az attribútum a következő célokra szolgál:

* Amikor egy új szinkronizálási motor kiszolgálót fejlesztett, vagy újbóli létrehozása után a vész-helyreállítási helyzetekre, ez az attribútum meglévő objektumokat az Azure AD-objektumokat a helyszíni hivatkozásokat tartalmaz.
* Ha szinkronizált identitás modellre válthatnak egy kizárólag felhőalapú identitás, majd ezt az attribútumot lehetővé teszi objektumok "rögzített egyezik" meglévő objektumokat helyszíni objektumokat az Azure AD-ben.
* Összevonási, akkor ez az attribútum a használatakor a **userPrincipalName** a jogcímek a felhasználó egyedi azonosítására szolgál.

Ez a témakör csak megemlít sourceAnchor, a felhasználók számára vonatkozik. Ugyanazok a szabályok vonatkoznak a összes objektum, de azt csak azoknak a felhasználóknak a probléma általában a szempont.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Egy jó sourceAnchor attribútum kiválasztása
Az attribútum értékét meg kell felelnie a következő:

* Kevesebb mint 60 karakternél
  * Nincs folyamatban a – z, A-Z karakter, vagy 0 – 9 kódolása, és 3 karakternél fog számítani
* Egy speciális karaktert tartalmaz: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " \@ _
* Globálisan egyedinek kell lennie
* Egy karakterlánc, egész szám vagy bináris kell lennie
* Nem lehet alapján kell a felhasználónak a neve, mert ezek módosíthatja
* Nem kell a kis-és nagybetűket és elkerülése az értékeket, amelyek változhatnak, eset szerint
* Hozzá kell rendelni az objektum létrehozásakor

Ha a kiválasztott sourceAnchor nem karakterlánc típusú, majd az Azure AD Connect Base64Encode az attribútum értéke annak biztosítása érdekében a különleges karaktereket nem jelennek meg. Ha egy másik összevonási kiszolgáló AD FS-nál használ, győződjön meg arról, a kiszolgáló az attribútum Base64Encode is lehetséges.

A sourceAnchor attribútum értéke kis-és nagybetűket. Egy "JohnDoe" értéke nem ugyanaz, mint "johndoe". Azonban nem kell két különböző objektumok csak olyan esetben különbséggel.

Egyetlen erdővel rendelkezik a helyszínen, majd az attribútumot használja-e **objectGUID**. Ez az attribútum az Azure AD Connectben gyorsbeállítások használata esetén használja, és a DirSync által használt attribútum is.

Ha több erdővel rendelkezik, és ne helyezze át felhasználók közötti erdők és tartományok, majd **objectGUID** van egy megfelelő attribútumot is ebben az esetben.

Ha a felhasználók erdők és tartományok között helyezi át, majd, meg kell találnia olyan attribútum, amely nem változik, vagy áthelyezhetők azokkal a felhasználókkal az áthelyezés során. Ajánlott eljárás, hogy vezessen be egy szintetikus attribútum. Olyan attribútum, amely tartalmazhat, hiba, amely úgy tűnik, egy GUID lenne megfelelő. Objektum létrehozásakor egy új GUID létrejött, és a felhasználó megjelölve. A szinkronizálási motor kiszolgálón ez az érték alapján hozzon létre egy egyéni szinkronizálási szabályt hozhat létre a **objectGUID** és frissítése a kijelölt attribútumot ADDS. Amikor helyezi át az objektumot, ügyeljen arra, hogy ez az érték tartalmát is másolja.

Egy másik megoldás, hogy válasszon ki egy meglévő attribútum tudja nem változik. Gyakran használt attribútuma például **employeeID**. Ha a betűket tartalmazó attribútum, győződjön meg arról, hogy az eset (a kis-és nagybetű) lehetősége nélkül módosíthatja az attribútum-érték van. Hibás az attribútumokat, amelyek nem használható ezek attribútumait a felhasználó nevét tartalmazza. Házasság vagy felbontására a név módosításához várt ez nem engedélyezett ehhez az attribútumhoz. Ez egyben az egyik oka miért attribútumok, például **userPrincipalName**, **mail**, és **targetAddress** nem engedélyezettek akkor jelölje be az Azure AD Connect telepítése a varázsló. Ezek az attribútumok is tartalmazhat. a "\@" karakter, ami nem engedélyezett a sourceAnchor a.

### <a name="changing-the-sourceanchor-attribute"></a>A sourceAnchor attribútum módosítása
A sourceAnchor attribútum értéke nem módosítható, miután az objektum létrehozása az Azure ad-ben és a identitása szinkronizálva van.

Ebből kifolyólag a következő korlátozások vonatkoznak, az Azure AD Connect:

* A sourceAnchor attribútum csak akkor állítható kezdeti telepítése során. Ha újra futtatja a telepítési varázsló, ezt a beállítást csak olvasható. Ha módosítania kell ezt a beállítást, majd kell eltávolítása és újratelepítése.
* Ha egy másik Azure AD Connect kiszolgáló telepítése, majd ki kell választania a korábban használt azonos sourceAnchor attribútum. Ha és áthelyezése az Azure AD Connect a korábban DirSync már használta, akkor kell használni **objectGUID** mivel az a DirSync által használt attribútum.
* Ha az érték a sourceAnchor után módosítva lett az objektum az Azure ad Szolgáltatásba, majd az Azure AD Connect szinkronizálási hibát jelez, és nem engedélyezi a további módosításokat az, hogy objektum, mielőtt a probléma megoldódott, és a sourceAnchor módosítják a forrás igazgató vissza a exportálása y.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Ms-DS-ConsistencyGuid használata sourceanchorként
Az Azure AD Connect alapértelmezés szerint (1.1.486.0 verziója és a régebbi) objectGUID használja Forráshorgony-attribútumként. ObjectGUID, a rendszer. Nem adható meg az értékét, amikor létrehozza a helyszíni AD-objektumok. A szakaszban leírtak szerint [sourceAnchor](#sourceanchor), ahol meg kell adnia a sourceAnchor érték forgatókönyv közül választhat. Ha a forgatókönyveket akkor alkalmazható, konfigurálható AD-attribútum kell használnia (például az ms-DS-ConsistencyGuid) Forráshorgony-attribútumként.

Az Azure AD Connect (1.1.524.0 verziót, és utána) ms-DS-ConsistencyGuid sourceAnchor attribútum használatát teszik lehetővé. Ez a funkció használatakor az Azure AD Connect automatikusan konfigurálja a szinkronizálási szabályok:

1. Ms-DS-ConsistencyGuid használja Forráshorgony-attribútumként felhasználói objektumok. Egyéb típusú objektumokat ObjectGUID használható.

2. Bármely adott a helyszíni AD-felhasználói objektum, amelynek az ms-DS-ConsistencyGuid attribútum nem az ms-DS-ConsistencyGuid attribútum a helyszíni Active Directoryban az objectGUID értékét biztonsági töltve, az Azure AD Connect írások. Követően az ms-DS-ConsistencyGuid attribútum feltöltése az Azure AD Connect majd exportálja az objektum az Azure ad-hez.

>[!NOTE]
> Ha egy helyszíni AD-objektum importálja az Azure AD Connect (amely, importálja a az AD Összekötőtérben, és előre jelzett költségről a metaverzumba), a sourceAnchor érték már nem módosítható. Adjon értéket a sourceAnchor egy adott a helyszíni AD objektumazonosító, az ms-DS-ConsistencyGuid attribútum konfigurálása az Azure AD Connect az importálás előtt.

### <a name="permission-required"></a>Engedély szükséges
Ez a funkció működéséhez az AD DS-fiókot a helyszíni Active Directoryval való szinkronizálásához használt engedéllyel kell rendelkezni írási az ms-DS-ConsistencyGuid attribútumra, a helyszíni Active Directoryban.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>A ConsistencyGuid szolgáltatás – új telepítés engedélyezése
ConsistencyGuid használata sourceanchorként új telepítése során engedélyezheti. Ez a szakasz ismertet Express és az egyéni telepítés részletei között.

  >[!NOTE]
  > Csak az Azure AD Connect újabb verzióit (1.1.524.0, és utána) ConsistencyGuid használata sourceanchorként támogatásához új telepítése során.

### <a name="how-to-enable-the-consistencyguid-feature"></a>A ConsistencyGuid funkció engedélyezése
Jelenleg a funkció engedélyezése csak új Azure AD Connect telepítése során.

#### <a name="express-installation"></a>Az Expressz telepítés
Az Azure AD Connect telepítése az Expressz mód esetén az Azure AD Connect varázsló automatikusan meghatározza, hogy a leginkább megfelelő AD attribútumot használja Forráshorgony-attribútumként az alábbi logika használata:

* Először az Azure AD Connect varázsló lekérdezi az Azure AD-bérlő lekéréséhez az AD attribútumot használja Forráshorgony-attribútumként az előző az Azure AD Connect telepítése (ha van). Ha ez az információ érhető el, az Azure AD Connect használja az azonos AD-attribútum.

  >[!NOTE]
  > Csak az Azure AD Connect újabb verzióit (1.1.524.0, és utána) kapcsolatos információk tárolása az Azure AD-bérlőben a sourceAnchor attribútum a telepítés során használt. Az Azure AD Connect régebbi verzióit viszont nem.

* Ha a használt sourceAnchor attribútum információ nem érhető el, a varázsló az ms-DS-ConsistencyGuid attribútum a helyszíni Active Directory állapotát ellenőrzi. Ha az attribútum a címtárban lévő összes objektum nincs konfigurálva, a varázsló használja az ms-DS-ConsistencyGuid Forráshorgony-attribútumként. Az attribútum konfigurálva van egy vagy több objektumot a címtárban, ha a varázsló azt állapítja meg az attribútum más alkalmazások által használt, és nem felel meg a sourceAnchor attribútum...

* Ebben az esetben a varázsló visszaáll a objectGUID Forráshorgony-attribútumként.

* A sourceAnchor attribútum dönthető el, miután a varázsló az adatokat az Azure AD-bérlő tárolja. Az adatokat későbbi telepítése az Azure AD Connect által használható.

Expressz telepítés befejezése után a varázsló tájékoztatja, hogy melyik attribútum lett kiválasztva a Forráshorgony-attribútumként.

![Varázsló tájékoztatja a sourceAnchor kivételezett AD-attribútum](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Egyéni telepítés
Az Azure AD Connect telepítése egyéni mód esetén az Azure AD Connect varázsló két lehetőséget biztosít a sourceAnchor attribútum konfigurálása:

![Egyéni telepítés – a sourceAnchor konfiguráció](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Beállítás | Leírás |
| --- | --- |
| Let Azure manage the source anchor for me (Az Azure kezelje a forráshorgonyt) | Válassza ezt a lehetőséget, ha azt szeretné, hogy az Azure AD válassza ki az attribútumot. Ha ezt a lehetőséget választja, az Azure AD Connect varázsló vonatkozik azonos [sourceAnchor attribútum lemezválasztási logika Expressz telepítés során használt](#express-installation). Expressz telepítés hasonlóan, a varázsló tájékoztatja, hogy melyik attribútum lett kiválasztva a Forráshorgony-attribútumként egyéni telepítés befejezése után. |
| A specific attribute (Egy adott attribútum) | Válassza ezt a lehetőséget, ha meglévő AD-attribútumot szeretne megadni forráshorgony-attribútumként. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>A ConsistencyGuid funkció – meglévő környezet engedélyezése
Ha rendelkezik egy meglévő Azure AD Connect üzemelő objectGUID használja Forráshorgony-attribútumként, átválthat ConsistencyGuid használata helyett.

>[!NOTE]
> Csak az Azure AD Connect újabb verzióit (: 1.1.552.0, és utána) támogatja az ObjectGuid átváltása ConsistencyGuid Forráshorgony-attribútumként.

Váltás az objectGUID ConsistencyGuid Forráshorgony-attribútumként:

1. Az Azure AD Connect varázsló elindításához, és kattintson a **konfigurálása** , nyissa meg a feladatok képernyő.

2. Válassza ki a **Forráshorgony konfigurálása** feladatot a beállítást, és kattintson a **tovább**.

   ![A meglévő üzembe helyezés – 2. lépés ConsistencyGuid engedélyezése](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Adja meg az Azure AD-rendszergazda hitelesítő adatait, és kattintson a **tovább**.

4. Az Azure AD Connect varázsló elemzi az ms-DS-ConsistencyGuid attribútum a helyszíni Active Directory állapotát. Ha az attribútum a címtárban lévő összes objektum nincs konfigurálva, az Azure AD Connect azt állapítja meg, hogy egyetlen másik alkalmazás éppen használja az attribútum, és használhatja azt a Forráshorgony-attribútumként. Kattintson a **tovább** folytatásához.

   ![A meglévő üzembe helyezése – 4. lépés ConsistencyGuid engedélyezése](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. Az a **készen áll a konfigurálás** kattintson **konfigurálása** , hogy a konfiguráció módosítását.

   ![A meglévő üzembe helyezés – 5. lépés ConsistencyGuid engedélyezése](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. A konfiguráció befejezése után a varázsló azt jelzi, hogy az ms-DS-ConsistencyGuid most már használja a Forráshorgony-attribútumként.

   ![A meglévő üzembe helyezés – 6. lépés ConsistencyGuid engedélyezése](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

(4. lépés) az elemzés során, ha az attribútum a címtárban, egy vagy több objektum van konfigurálva a varázsló azt állapítja meg az attribútum egy másik alkalmazás használja, és hibaüzenetet jelenít meg, az alábbi ábrán szemléltetett módon. Ez a hiba akkor is előfordulhat, ha korábban engedélyezte a ConsistencyGuid funkció az elsődleges Azure AD Connect a kiszolgálón, és próbálja végezze el ugyanezt az átmeneti kiszolgálón.

![ConsistencyGuid előkészítése a meglévő üzembe helyezés – hiba](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Ha biztos benne, hogy az attribútum nem használja másik meglévő alkalmazásokat, tilthatja le a hiba az Azure AD Connect varázsló újraindításával a **/SkipLdapSearchcontact** megadott. Ehhez futtassa a következő parancsot a parancssorba:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Active Directory összevonási szolgáltatások vagy harmadik féltől származó összevonási konfiguráció gyakorolt hatás
Az Azure AD Connect használata kezelheti a helyszíni AD FS üzembe helyezése, az Azure AD Connect automatikusan frissíti a jogcímszabályok az azonos AD-attribútum használata sourceanchorként. Ez biztosítja, hogy az AD FS által generált ImmutableID jogcím konzisztensek legyenek a sourceAnchor értékek exportálva az Azure ad-ben.

Ha az AD FS az Azure AD Connect-en kívül kezeli, vagy harmadik féltől származó összevonási kiszolgálókat használ a hitelesítéshez, manuálisan kell frissítenie a jogcímszabályok, ImmutableID jogcím konzisztens a sourceAnchor értékekkel exportálva az Azure AD leírtak szerint: Article szakasz [módosítás az AD FS-jogcímszabályok](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). A varázsló a következő figyelmeztetés a telepítés befejezése után adja vissza:

![Harmadik féltől származó összevonási konfiguráció](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Új könyvtárak hozzáadása meglévő üzemelő példányt
Tegyük fel, hogy telepítette az Azure AD Connect a ConsistencyGuid szolgáltatás engedélyezve van, és most szeretné a központi telepítés hozzáadása egy másik címtárban. Próbál meg hozzáadni a könyvtár, amikor az Azure AD Connect varázsló a címtárban az ms-DS-ConsistencyGuid attribútum állapotát ellenőrzi. Az attribútum konfigurálva van egy vagy több objektumot a címtárban, ha a varázsló azt állapítja meg az attribútum az egyéb alkalmazások használják, és hibát ad vissza, az alábbi ábrán szemléltetett módon. Ha biztos benne, hogy az attribútum nem használja meglévő alkalmazásokat, meg kell le a hiba részleteiért forduljon az ügyfélszolgálathoz.

![Új könyvtárak hozzáadása meglévő üzemelő példányt](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Bejelentkezés az Azure AD-ba
A helyszíni címtár Azure ad-ben és azt integrálni, fontos tudni, hogyan a szinkronizálási beállítások hatással lehetnek a módon felhasználó hitelesíti magát. Az Azure AD userPrincipalName (UPN) használ a felhasználó hitelesítéséhez. Azonban ha szinkronizálja a felhasználókat, ki kell választania az attribútum a userPrincipalName értéke gondosan használandó.

### <a name="choosing-the-attribute-for-userprincipalname"></a>A userPrincipalName attribútum kiválasztása
Amikor kiválasztja az attribútumot, amelyek biztosítják az Azure egy használt UPN értékét biztosítania kell

* Az attribútum értékek megfelelnek az egyszerű szintaxis (az RFC 822), van-e a formátum felhasználónevet kell\@tartomány
* Az Azure ad-ben egyik ellenőrzött egyéni tartománynak megfelelő az értékeket az utótag

A gyorsbeállítások feltételezett az attribútum, amely userPrincipalName. A userPrincipalName attribútum nem tartalmaz az érték azt szeretné, hogy a felhasználókat, hogy jelentkezzen be az Azure-ba, akkor ki kell választania **egyéni telepítési**.

### <a name="custom-domain-state-and-upn"></a>Egyéni tartomány állapota és az egyszerű felhasználónév
Fontos, hogy van-e az egyszerű felhasználónév utótagja ellenőrzött tartományt.

John a felhasználó a contoso.com. Azt szeretné, hogy János használata a helyszíni egyszerű Felhasználónévvel john\@contoso.com való bejelentkezéshez az Azure-ba, amikor az Azure AD directory contoso.onmicrosoft.com a felhasználó szinkronizálva van. Ehhez szüksége hozzáadásának és hitelesítésének contoso.com egyéni tartományként ahhoz, hogy a felhasználók szinkronizálása az Azure AD-ben. Ha János, például a contoso.com, az UPN-utótagja nem egyezik egy ellenőrzött tartomány Azure AD-ben, majd az Azure AD lecseréli contoso.onmicrosoft.com az UPN-utótagot.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Nem irányítható a helyszíni tartományokra és az Azure ad egyszerű felhasználónév
Egyes szervezetek nem átirányítható tartományok, mint a contoso.local vagy egyszerű névvégződés nélküli tartományok, például: contoso rendelkezik. Ön nem tudja nem átirányítható tartomány hitelesítése az Azure ad-ben. Az Azure AD Connect csak egy ellenőrzött tartomány szinkronizálhatók az Azure ad-ben. Amikor létrehoz egy Azure AD-címtár, alapértelmezett tartomány válik az Azure AD például contoso.onmicrosoft.com irányítható tartományban hoz létre. Ezért szükségessé válik a bármely más irányítható tartomány ilyen esetben ellenőrizze, abban az esetben, ha nem szeretné az alapértelmezett onmicrosoft.com tartomány való szinkronizálását.

Olvasási [az egyéni tartománynév hozzáadása az Azure Active Directory](../active-directory-domains-add-azure-portal.md) hozzáadásával és a tartományok ellenőrzéséről a további információk.

Az Azure AD Connect észleli, ha nem átirányítható tartományi környezetben futtatja, és szeretné megfelelően figyelmezteti a gyorsbeállítások történő feladatbeküldés. Ha olyan nem átirányítható tartományban, majd valószínű, hogy az egyszerű Felhasználónevet, a felhasználók túl van-e nem átirányítható utótagot. Például ha contoso.local le, az Azure AD Connect ajánl fel használatát gyorsbeállításokkal helyett egyéni beállításokat. Egyéni beállítások használatával is tudja adja meg, amely való bejelentkezéshez az Azure-ba, miután a felhasználók szinkronizálva lesznek az Azure AD UPN-t kell használni.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
