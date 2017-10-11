---
title: "Az Azure AD Connect: Tervezési alapelvek |} Microsoft Docs"
description: "Ez a témakör részletesen bizonyos megvalósítási terv területek"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: f23443d438c95a784f655fb9a5f20dfcf37be189
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-design-concepts"></a>Az Azure AD Connect: Tervezési alapelvek
Ez a témakör célja, hogy az Azure AD Connect megvalósítási tervezése során kell re területek leírásához. Ez a témakör egy részletes bemutatója a bizonyos területeken, és ezekről a fogalmakról rövid leírását, valamint további témakörökre mutatnak.

## <a name="sourceanchor"></a>sourceAnchor
A sourceAnchor attribútum *objektum élettartama alatt megváltoztathatatlan attribútum*. Egyedileg azonosítja, hogy az ugyanazon objektum a helyszíni és az Azure AD-objektum. Az attribútum néven is ismert **immutableId** és a két nevet cserélhető használja, a rendszer.

A word, amely nem módosítható, a "nem módosítható", fontos, hogy ez a témakör. Mivel ez az attribútum értéke nem lehet módosítani, beállítása után, fontos válasszon, amely támogatja a forgatókönyvéhez.

Az attribútum a következő esetekben szolgál:

* Amikor egy új szinkronizálási motor kiszolgálót a beépített, vagy vész-helyreállítási forgatókönyv után úgy, ez az attribútum meglévő objektumokat a helyszíni objektumok Azure AD-ben hivatkozásokat tartalmaz.
* Ha egy csak felhőalapú identitás a szinkronizált identitás modellre, majd ezt az attribútumot lehetővé teszi, hogy "rögzített match" meglévő objektumokon objektumok az Azure AD helyszíni objektummal.
* Ha összevonási, akkor ez az attribútum együtt használja a **userPrincipalName** a jogcímek a felhasználó egyedi azonosítására szolgál.

Ez a témakör csak beszél sourceAnchor, felhasználók vonatkozik. Ugyanazok a szabályok alkalmazása bármilyen objektumtípust, de csak a felhasználók számára a probléma általában a veszélye.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Egy jó sourceAnchor attribútum kiválasztása
Az attribútum értékét meg kell felelnie a következő:

* Kisebb, mint 60 karakter hosszú lehet
  * Nem, a – z, A-Z karaktereket vagy 0-9 kódolású és számít 3 karakternél
* Egy speciális karaktert tartalmaz: &#92;! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @ _
* Egyedinek kell lenniük
* Karakterlánc, egész, vagy bináris kell lennie
* Felhasználó nevét, a módosítások nem alapján
* Nem kell a kis-és nagybetűket és értékek, amelyek esetének eltérhetnek elkerülése érdekében
* Hozzá kell rendelni a objektum létrehozásakor

Ha a kijelölt sourceAnchor nem karakterlánc típusú, majd az Azure AD Connect Base64Encode különleges karaktereket nem biztosítására attribútumérték jelennek meg. Ha az AD FS mint egy másik összevonási kiszolgálót használ, győződjön meg arról is Base64Encode az attribútumot a kiszolgáló listájában.

A sourceAnchor attribútum kis-és nagybetűket. Egy "Kovacsjanos" értéke nem ugyanaz, mint "kovacsjanos". De nem kell két különböző objektum csak a kis különbség.

Ha egyetlen erdővel rendelkezik a helyszínen, majd a használjon attribútum van **objectGUID**. Ez történik akkor is használja, ha az expressz beállításokat az Azure AD Connectben attribútum, valamint a DirSync által használt attribútum.

Ha több erdővel rendelkezik, és nem helyezhető át felhasználók közötti erdők és tartományok, majd **objectGUID** jó attribútum ebben az esetben is használni.

Ha a felhasználók az erdők és tartományok között helyezi át, majd meg kell attribútumot, amely nem változik, vagy helyezheti át a felhasználók az áthelyezés során. A javasolt megoldás, szintetikus attribútum bevezetése. Olyan attribútum, amely sikerült tartsa valami hasonló a GUID lenne megfelelő. Egy új GUID objektum létrehozása során létrehozott és a felhasználó nyomtatva. Egy egyéni szinkronizálási szabályt hozhat létre a Szinkronizáló vezérlő kiszolgálója alapján ez az érték létrehozása a **objectGUID** és frissítése a kijelölt attribútumot ADDS. Amikor az objektum helyezi át, győződjön meg arról, is az ennek az értéknek a tartalom másolása.

Válasszon egy meglévő attribútum nem változtatja meg tudja egy másik megoldás. Gyakran használt attribútumokat tartalmaznak **employeeID**. Ha betűket tartalmazó attribútum vegye figyelembe, ellenőrizze, nincs lehetősége nélkül az eset (a kis-és nagybetű) módosíthatja a attribútumértékhez. Hibás attribútumok nem használhatók a nevű, a felhasználó ezen attribútumait tartalmazza. Házasság vagy felbontására a név várhatóan, ami nem engedélyezett ehhez az attribútumhoz. Ez egyben az egyik oka miért attribútumok például **userPrincipalName**, **mail**, és **targetAddress** nincsenek még akkor is lehetséges, jelölje be az Azure AD Connect telepítés varázsló. Ezek az attribútumok is tartalmazzák a "@" karakter, ami nem engedélyezett a sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>A sourceAnchor attribútum módosítása
A sourceAnchor attribútum értéke nem módosítható, miután az objektum létrehozása az Azure AD és az identitása szinkronizálva.

Ezért a következő korlátozások vonatkoznak, az Azure AD Connect:

* A sourceAnchor attribútum csak akkor állítható kezdeti telepítése során. Ha Újrafuttatja a telepítési varázsló, ezt a beállítást csak olvasható. Ha módosítania kell ezt a beállítást, majd kell távolítania, és újra kell telepítenie.
* Ha egy másik Azure AD Connect kiszolgáló telepítése, majd ki kell választania a korábban már használt azonos sourceAnchor attribútum. Ha korábban használt DirSync és áthelyezése az Azure AD Connect, akkor kell használnia **objectGUID** mivel az a DirSync által használt attribútum.
* Ha az érték a sourceAnchor után módosítva lett az objektum az Azure AD, majd az Azure AD Connect szinkronizálási hibát jelez, és nem teszi lehetővé a további módosításokat az, hogy a probléma megszüntetése előtt objektum és a sourceAnchor vissza a a forrás igazgató módosul exportálása y.

## <a name="using-msds-consistencyguid-as-sourceanchor"></a>Az msDS-ConsistencyGuid sourceAnchor használatával
Alapértelmezés szerint az Azure AD Connect (1.1.486.0 verziója és a régebbi) objectGUID használja, mint a sourceAnchor attribútum. ObjectGUID, a rendszer. Az érték létrehozása során a helyszíni Active Directory-objektumok nem adható meg. A szakaszban leírtak szerint [sourceAnchor](#sourceanchor), ahol meg kell adnia a sourceAnchor érték forgatókönyv. A forgatókönyvek a következők akkor alkalmazható, ha a sourceAnchor attribútumaként konfigurálható AD attribútum (például msDS-ConsistencyGuid) kell használnia.

Az Azure AD Connect (1.1.524.0 verziót, és utána) mostantól lehetővé teszi a sourceAnchor attribútum az msDS-ConsistencyGuid használatát. Ez a funkció használata esetén az Azure AD Connect automatikusan konfigurálja a szinkronizálási szabályokat:

1. A sourceAnchor attribútum esetén a felhasználói objektumok msDS-ConsistencyGuid használják. Más típusú objektumokat ObjectGUID használható.

2. Az összes megadott helyszíni AD-felhasználó amelynek msDS-ConsistencyGuid attribútum az msDS-ConsistencyGuid attribútum a helyszíni Active Directory biztonsági objectGUID értéke ki van töltve, az Azure AD Connect írások nem objektum. Miután elkészült az msDS-ConsistencyGuid attribútum, az Azure AD Connect majd exportálja az objektum az Azure ad Szolgáltatásba.

>[!NOTE]
> Egyszer egy helyszíni AD-objektum importálja az Azure AD Connect (amely, importálja a AD kapcsolódási térbe kerülnek, és a metaverzumba tervezett), a sourceAnchor értéke már nem módosítható. A sourceAnchor értéket a megadott helyszíni AD objektumazonosító, a msDS-ConsistencyGuid attribútum konfigurálása az Azure AD Connect az importálás előtt.

### <a name="permission-required"></a>Szükséges engedéllyel
Ez a funkció használatához szinkronizálja a helyszíni Active Directory Tartományi fiók engedéllyel kell rendelkezni írási az msDS-ConsistencyGuid attribútumhoz a helyszíni Active Directoryban.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>A ConsistencyGuid szolgáltatás - új telepítés engedélyezése
Új telepítése során engedélyezheti a sourceAnchor ConsistencyGuid használatát. Ez a fejezet Express és az egyéni telepítés részleteit.

  >[!NOTE]
  > Csak az Azure AD Connect újabb verzióit (1.1.524.0, és utána) sourceAnchor ConsistencyGuid használatát támogatja az új telepítés során.

### <a name="how-to-enable-the-consistencyguid-feature"></a>A ConsistencyGuid szolgáltatás engedélyezése
Jelenleg a szolgáltatás csak akkor engedélyezhető, csak új Azure AD Connect telepítése során.

#### <a name="express-installation"></a>Az Expressz telepítés
Az Azure AD Connect telepítése az Expressz mód esetén az Azure AD Connect varázsló automatikusan meghatározza, hogy a legmegfelelőbb AD attribútum kívánja használni, mint a sourceAnchor attribútum használata a következő programot:

* Először az Azure AD Connect varázsló lekérdezi az Azure AD-bérlő beolvasása az AD attribútum, használja az előző az Azure AD Connect telepítését (ha van ilyen) a sourceAnchor attribútum. Ez az információ áll rendelkezésre, ha az Azure AD Connect használja az ugyanazon AD attribútum.

  >[!NOTE]
  > Csak az Azure AD Connect újabb verzióit (1.1.524.0, és utána) telepítésekor használt információkat tárol az Azure AD-bérlő a kapcsolatos a sourceAnchor attribútum. Az Azure AD Connect korábbi verziói azonban nem.

* Ha a használt sourceAnchor attribútum információ nem érhető el, a varázsló az msDS-ConsistencyGuid attribútum a helyszíni Active Directoryban állapotát ellenőrzi. Ha az attribútum a címtárban található összes objektumról a nincs konfigurálva, a varázsló az msDS-ConsistencyGuid használ a sourceAnchor attribútum. Ha az attribútum egy vagy több objektumot a könyvtárban van beállítva, a varázsló azt állapítja meg az attribútum más alkalmazások használják, és nem felel meg a sourceAnchor attribútum...

* Ebben az esetben a varázsló visszavált a sourceAnchor attribútum az objectGUID.

* A sourceAnchor attribútum születik, miután a varázsló a információkat tárol az Azure AD-bérlő. Az adatokat az Azure AD Connect jövőbeli telepítése használjuk.

Expressz telepítés befejezése után a varázsló tájékoztatja, hogy mely attribútum válogatott Forráshorgony attribútumként.

![Varázsló tájékoztatja a sourceAnchor kivételezett AD attribútum](./media/active-directory-aadconnect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Egyéni telepítés
Az Azure AD Connect telepítése egyéni mód esetén az Azure AD Connect varázsló rendszerben két beállítást biztosít sourceAnchor attribútum konfigurálásakor:

![Egyéni telepítés – a sourceAnchor konfiguráció](./media/active-directory-aadconnect-design-concepts/consistencyGuid-02.png)

| Beállítás | Leírás |
| --- | --- |
| Let Azure manage the source anchor for me (Az Azure kezelje a forráshorgonyt) | Válassza ezt a lehetőséget, ha azt szeretné, hogy az Azure AD válassza ki az attribútumot. Ezt a beállítást, ha az Azure AD Connect varázsló Ugyanez vonatkozik [sourceAnchor attribútum lemezválasztási logika Expressz telepítés során használt](#express-installation). Expressz telepítés hasonló, a varázsló tájékoztatja, hogy mely attribútum válogatott Forráshorgony attribútumként egyéni telepítés befejezése után. |
| A specific attribute (Egy adott attribútum) | Válassza ezt a lehetőséget, ha meglévő AD-attribútumot szeretne megadni forráshorgony-attribútumként. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>A ConsistencyGuid - a meglévő üzemelő szolgáltatás engedélyezése
Ha egy meglévő Azure AD Connect a központi telepítési objectGUID használja, mint a Forráshorgony attribútum, használja helyette: ConsistencyGuid való válthat.

>[!NOTE]
> Csak az Azure AD Connect újabb verzióit (1.1.552.0, és utána) támogatja az ObjectGuid naplókról ConsistencyGuid Forráshorgony attribútumként.

Váltás az objectGUID ConsistencyGuid Forráshorgony attribútumként:

1. Az Azure AD Connect varázsló elindításához, és kattintson a **konfigurálása** gomba a feladatok képernyő.

2. Válassza ki a **Forráshorgony konfigurálása** beállítás feladatot, és kattintson a **következő**.

   ![A meglévő telepítés – 2. lépés ConsistencyGuid engedélyezése](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment01.png)

3. Adja meg Azure AD rendszergazdai hitelesítő adatait, és kattintson a **következő**.

4. Az Azure AD Connect varázsló elemzi az msDS-ConsistencyGuid attribútum a helyszíni Active Directoryban állapotát. Ha az attribútum a címtárban található összes objektumról a nincs konfigurálva, az Azure AD Connect arra a következtetésre jut, hogy egyetlen másik alkalmazás jelenleg használja a attribútum, és biztonságosan a Forráshorgony attribútum használható. Kattintson a **tovább** a folytatáshoz.

   ![A meglévő telepítés – 4. lépés ConsistencyGuid engedélyezése](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment02.png)

5. Az a **készen áll a konfigurálás** kattintson **konfigurálása** számára a konfigurációs módosítást.

   ![A meglévő telepítés – 5. lépés ConsistencyGuid engedélyezése](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment03.png)

6. A konfiguráció után a varázsló azt jelzi, hogy msDS-ConsistencyGuid most már használatban van a Forráshorgony attribútumaként.

   ![A meglévő telepítés – 6. lépés ConsistencyGuid engedélyezése](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment04.png)

(4. lépés) az elemzés során a attribútum konfigurálva van egy vagy több objektumot a könyvtárban, ha a varázsló azt állapítja meg, az attribútum egy másik alkalmazás használja, és hibát ad vissza, az alábbi ábra szemlélteti. Ha biztos benne, hogy az attribútum a meglévő alkalmazásokat nem használja, további információt a hibát forduljon a támogatási szeretné.

![ConsistencyGuid engedélyezése a meglévő telepítés – hiba](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeploymenterror.png)

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Active Directory összevonási szolgáltatások vagy harmadik féltől származó összevonási konfiguráció gyakorolt hatás
Az Azure AD Connect használata kezelheti a helyszíni AD FS üzembe helyezése, az Azure AD Connect automatikusan frissíti a jogcímszabályok sourceAnchor ugyanazon AD attribútum használandó. Ez biztosítja, hogy a az AD FS által generált ImmutableID igényt konzisztensek legyenek az Azure ad Szolgáltatásba exportálni sourceAnchor értékek.

Ha az AD FS az Azure AD Connect kívül kezelt vagy harmadik féltől származó összevonási kiszolgálókat használnak a hitelesítéshez, manuálisan kell frissítenie a jogcímszabályok ImmutableID jogcímet, hogy azok konzisztensek legyenek az Azure AD exportálva a sourceAnchor értékek a szakasz a következő cikket: [módosítás az AD FS jogcímszabályokba](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). A varázsló a következő figyelmeztetést ad vissza, a telepítést követően:

![Harmadik féltől származó összevonási konfigurációja](./media/active-directory-aadconnect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Új könyvtárak hozzáadása a meglévő telepítés
Tegyük fel, hogy telepítette az Azure AD Connect a ConsistencyGuid szolgáltatás engedélyezve van, és most szeretné egy másik címtárban hozzá szeretne adni a telepítéshez. Megkísérlésekor adja hozzá a könyvtárát, az Azure AD Connect varázsló a könyvtárban az mSDS-ConsistencyGuid attribútum állapotát ellenőrzi. Az attribútum egy vagy több objektumot a könyvtárban van beállítva, ha a varázsló azt állapítja meg az attribútum más alkalmazások használják, és hibát ad vissza, az alábbi ábra szemlélteti. Ha biztos benne, hogy az attribútum a meglévő alkalmazásokat nem használja, további információt a hibát forduljon a támogatási szeretné.

![Új könyvtárak hozzáadása a meglévő telepítés](./media/active-directory-aadconnect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Az Azure AD-bejelentkezés
A helyszíni címtár az Azure ad-vel integrálni fontos tudni, hogyan a szinkronizálási beállítások hatással lehet a módon felhasználó hitelesíti magát. Az Azure AD userPrincipalName (UPN) használ a felhasználó hitelesítéséhez. A felhasználók szinkronizálása során használt érték a userPrincipalName gondosan attribútumot kell választania.

### <a name="choosing-the-attribute-for-userprincipalname"></a>A userPrincipalName attribútum kiválasztása
Amikor kiválasztja a biztosítani azt az attribútumot Azure egy használt UPN értékének győződjön meg arról

* Az attribútumértékek felelnek meg a UPN szintaxis (RFC 822), az azt formátumban kell lennieusername@domain
* Az értékek utótag megegyezik egy egyéni ellenőrzött tartományokat Azure AD-ben

A gyorsbeállítások az attribútum feltételezett választott a userPrincipalName nem. Ha a userPrincipalName attribútum nem tartalmaz az érték azt szeretné, hogy a felhasználókat, hogy jelentkezzen be az Azure-ba, majd ki kell választania **egyéni telepítési**.

### <a name="custom-domain-state-and-upn"></a>Az egyéni tartomány állapotának és az egyszerű felhasználónév
Fontos, hogy van-e az egyszerű Felhasználónévi utótagot az ellenőrzött tartományt.

John az a felhasználó a contoso.com webhelyen. John a helyszíni egyszerű Felhasználónévvel használni kívánt john@contoso.com Azure bejelentkezni, miután a felhasználók számára az Azure Active directory contoso.onmicrosoft.com szinkronizálva van. Ehhez szüksége hozzáadásának és hitelesítésének contoso.com, az egyéni tartománynév az Azure ad-ben a felhasználók szinkronizálása előtt. Ha az UPN-utótagot János, például contoso.com, az Azure AD nem egyezik egy ellenőrzött tartomány, majd az Azure AD lecseréli contoso.onmicrosoft.com az UPN-utótagot.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Nem átirányítható helyszíni tartományok és az Azure AD egyszerű felhasználónév
Egyes szervezeteknél nem átirányítható tartományok, mint a contoso.local vagy egyszerű egycímkés tartományok, mint például a contoso. Nem tudunk nem átirányítható tartomány hitelesítése az Azure ad-ben. Az Azure AD Connect Azure AD-ben csak egy ellenőrzött tartomány szinkronizálhatók. Az Azure AD-címtár létrehozásakor az Azure AD például contoso.onmicrosoft.com az alapértelmezett tartomány váló irányítható tartomány hoz létre. Ezért el kell más irányítható tartomány ilyen esetben győződjön meg arról, ha nem kívánja szinkronizálni kívánt alapértelmezett onmicrosoft.com tartomány.

Olvasási [az egyéni tartománynév hozzáadása az Azure Active Directory](../active-directory-add-domain.md) további információt a hozzáadása és tartományok ellenőrzése.

Az Azure AD Connect azt észleli, ha nem átirányítható tartományi környezetben futtatja, és akkor megfelelően figyelmezteti a folytassa a gyorsbeállítások. Ha nem átirányítható tartományban, akkor valószínű, hogy a felhasználók egyszerű túl van-e nem átirányítható utótagok. Például contoso.local le, ha az Azure AD Connect azt javasolja, hogy használja a gyorsbeállítások használata helyett egyéni beállításokat. Egyéni beállítások használatával is, amely kell használható egyszerű felhasználónév jelentkezzen be Azure után a felhasználók attribútumok szinkronizálva lesznek az Azure AD meg.

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
