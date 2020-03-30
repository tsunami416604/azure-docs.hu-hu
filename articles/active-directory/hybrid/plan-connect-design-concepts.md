---
title: 'Azure AD Connect: Tervezési fogalmak | Microsoft dokumentumok'
description: Ez a témakör bizonyos megvalósítási tervezési területeket részletez
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb41e14a7ecf41a2698a063c3067a98d8acf8f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253883"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Tervezési fogalmak
Ez a dokumentum célja, hogy leírja azokat a területeket, amelyeket át kell gondolni az Azure AD Connect megvalósítási tervezése során. Ez a dokumentum egy mély merülés bizonyos területeken, és ezeket a fogalmakat röviden ismertetik más dokumentumokban is.

## <a name="sourceanchor"></a>sourceAnchor
A sourceAnchor attribútum egy *objektum élettartama alatt nem módosítható attribútumként*van definiálva. Egyedileg azonosítja az objektumot, mint ugyanazt az objektumot a helyszínen és az Azure AD-ben. Az attribútumot **nem módosíthatóazonosítónak is nevezik,** és a két név felcserélhető.

A dokumentum számára fontos, hogy a "nem módosítható" szó nem módosítható. Mivel az attribútum értéke nem módosítható a beállítás után, fontos, hogy válasszon egy tervet, amely támogatja a forgatókönyvet.

Az attribútum a következő esetekben használatos:

* Ha egy új szinkronizálási motor-kiszolgáló épül, vagy egy vész-helyreállítási forgatókönyv után újraépítik, ez az attribútum az Azure AD meglévő objektumait a helyszíni objektumokkal kapcsolja össze.
* Ha csak felhőalapú identitásról szinkronizált identitásmodellre tér át, akkor ez az attribútum lehetővé teszi, hogy az objektumok "nehezen egyeztetjék" a meglévő objektumokat az Azure AD-ben a helyszíni objektumokkal.
* Ha összevonást használ, akkor ez az attribútum a **userPrincipalName-nal** együtt a jogcímben a felhasználó egyedi azonosítására szolgál.

Ez a témakör csak beszél sourceAnchor, mivel a felhasználókra vonatkozik. Ugyanazok a szabályok vonatkoznak minden objektumtípusra, de csak a felhasználók számára ez a probléma általában aggodalomra ad okot.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Egy jó sourceAnchor attribútum kiválasztása
Az attribútumértéknek a következő szabályokat kell követnie:

* 60 karakternél rövidebb
  * A nem a-z, A-Z vagy 0-9 karakterek kódolása és 3 karakter
* Nem tartalmaz egy különleges karakter: &#92;! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) " ; : , [ ] " \@ _
* Globálisan egyedinek kell lennie
* Karakterláncnak, egész számnak vagy binárisnak kell lennie.
* Nem alapulhat a felhasználó neve, mert ezek változhatnak
* Nem szabad a kis- és nagybetűket kitenni, és kerülni kell az okat, amelyek eseti legkülönbözőbben változhatnak
* Az objektum létrehozásakor hozzá kell rendelni

Ha a kijelölt sourceAnchor nem típusú karakterlánc, majd az Azure AD Connect Base64Encode az attribútum értékét, hogy ne jelenjenek meg speciális karakterek. Ha az ADFS-nél más összevonási kiszolgálót használ, győződjön meg arról, hogy a kiszolgáló base64Encode-ot is tud kódolni az attribútummal.

A sourceAnchor attribútum a kis- és nagybetűk et is figyelembe vevő. A "JohnDoe" értéke nem ugyanaz, mint a "johndoe". De nem lehet két különböző tárgy, csak a különbség abban az esetben.

Ha egyetlen erdővel rendelkezik a helyszínen, akkor a használni kívánt attribútum **objectGUID**. Ez is az attribútum használata esetén a gyorsbeállítások az Azure AD Connect és az attribútum által használt DirSync.

Ha több erdővel rendelkezik, és nem mozgatja a felhasználókat az erdők és a tartományok között, akkor az **objectGUID** jó attribútum, amelyet még ebben az esetben is használhat.

Ha a felhasználókat erdők és tartományok között helyezi át, akkor olyan attribútumot kell találnia, amely nem változik, vagy áthelyezhető a felhasználókkal az áthelyezés során. Az ajánlott megközelítés egy szintetikus attribútum bevezetése. Egy attribútum, amely képes tartani valamit, ami úgy néz ki, mint egy GUID megfelelő lenne. Az objektum létrehozása során egy új GUID jön létre, és lepecsételi a felhasználót. A szinkronizálási motorkiszolgálón létrehozhat egy egyéni szinkronizálási szabályt, amely az **objectGUID** azonosító alapján hozza létre ezt az értéket, és frissíti a kijelölt attribútumot az ADDS programban. Az objektum áthelyezésekekénél ügyeljen arra, hogy az érték tartalmát is másolja.

Egy másik megoldás egy meglévő attribútum kiválasztása, amelyről tudja, hogy nem változik. A gyakran használt attribútumok közé tartozik **az alkalmazottazonosító**. Ha olyan attribútumot vesz figyelembe, amely betűket tartalmaz, győződjön meg arról, hogy nincs esély arra, hogy a kisbetű (nagybetű és kisbetű) megváltozhat az attribútum értékén. A nem használandó hibás attribútumok közé tartoznak azok az attribútumok, amelyek a felhasználó nevével vannak feltüntetve. Házasságban vagy házasságfelbontásban a név várhatóan megváltozik, ami nem engedélyezett ehhez az attribútumhoz. Ez az egyik oka annak is, hogy az olyan attribútumok, mint **például a userPrincipalName**, **mail**és **targetAddress,** még csak ki sem választhatók az Azure AD Connect telepítővarázslójában. Ezek az attribútumok\@tartalmazzák a " " karaktert is, amely nem engedélyezett a sourceAnchorban.

### <a name="changing-the-sourceanchor-attribute"></a>A sourceAnchor attribútum módosítása
A sourceAnchor attribútum értéke nem módosítható, miután az objektum létrejött az Azure AD-ben, és az identitás szinkronizálva van.

Ezért a következő korlátozások vonatkoznak az Azure AD Connectre:

* A sourceAnchor attribútum csak a kezdeti telepítés során állítható be. Ha újrafuttatja a telepítővarázslót, ez a beállítás írásvédett. Ha módosítania kell ezt a beállítást, akkor el kell távolítania és újra kell telepítenie.
* Ha telepít egy másik Azure AD Connect-kiszolgálót, akkor ugyanazt a sourceAnchor attribútumot kell kiválasztania, mint korábban. Ha korábban már használta a DirSync-et, és az Azure AD Connectre való áttérést használta, akkor **objectGUID-t kell használnia,** mivel ez a DirSync által használt attribútum.
* Ha a sourceAnchor értéke az objektum Azure AD-be való exportálása után módosul, akkor az Azure AD Connect szinkronizálása hibát jelez, és nem engedélyezi az objektum további módosításait a probléma megoldása és a forrásanchor módosítása előtt Taglista.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Ms-DS-ConsistencyGuid használata sourceAnchor néven
Alapértelmezés szerint az Azure AD Connect (1.1.486.0-s és újabb verzió) az objectGUID-t használja sourceAnchor attribútumként. Az ObjectGUID rendszer által generált. A helyszíni AD-objektumok létrehozásakor nem adhatja meg annak értékét. Aszakasz [sourceAnchor](#sourceanchor), vannak olyan forgatókönyvek, ahol meg kell adnia a sourceAnchor érték. Ha az esetek alkalmazhatók Önre, egy konfigurálható AD attribútumot (például ms-DS-ConsistencyGuid) kell használnia a sourceAnchor attribútumként.

Az Azure AD Connect (1.1.524.0-s és újabb verzió) mostantól megkönnyíti az ms-DS-ConsistencyGuid forrásanchor attribútumként való használatát. A funkció használatakor az Azure AD Connect automatikusan konfigurálja a szinkronizálási szabályokat a következőkre:

1. Használja az ms-DS-ConsistencyGuid-ot a felhasználói objektumok sourceAnchor attribútumaként. Az ObjectGUID más objektumtípusokhoz is használható.

2. Minden olyan helyszíni AD-felhasználó objektum, amelynek ms-DS-consistencyGuid attribútum nincs feltöltve, az Azure AD Connect írja az objectGUID értékét vissza az ms-DS-consistencyGuid attribútum a helyszíni Active Directoryban. Az ms-DS-ConsistencyGuid attribútum feltöltése után az Azure AD Connect exportálja az objektumot az Azure AD-be.

>[!NOTE]
> Miután egy helyszíni AD-objektum ot importált az Azure AD Connect (azaz az AD-összekötő térbe importálva és a Metaverzumba vetítve), többé nem módosíthatja a sourceAnchor értékét. Egy adott helyszíni AD-objektum sourceAnchor értékének megadásához konfigurálja az ms-DS-ConsistencyGuid attribútumot az Azure AD Connectbe történő importálás előtt.

### <a name="permission-required"></a>Engedély szükséges
Ahhoz, hogy ez a szolgáltatás működjön, a helyszíni Active Directoryval való szinkronizáláshoz használt Active Directory-fióknak írási engedélyt kell adni az ms-DS-ConsistencyGuid attribútumhoz a helyszíni Active Directoryban.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>A KonzisztenciaGuid szolgáltatás engedélyezése - Új telepítés
Az új telepítés során engedélyezheti a ConsistencyGuid sourceAnchor néven való használatát. Ez a rész részletesen ismerteti az Expressz és az Egyéni telepítést.

  >[!NOTE]
  > Csak az Azure AD Connect újabb verziói (1.1.524.0 és azt követően) támogatják a ConsistencyGuid sourceAnchor használatával az új telepítés során.

### <a name="how-to-enable-the-consistencyguid-feature"></a>A KonzisztenciaGuid funkció engedélyezése

#### <a name="express-installation"></a>Expressz telepítés
Az Azure AD Connect express móddal történő telepítésekor az Azure AD Connect varázsló automatikusan meghatározza a legmegfelelőbb AD attribútumot, amelyet sourceAnchor attribútumként kell használni a következő logika használatával:

* Először az Azure AD Connect varázsló lekérdezi az Azure AD-bérlőt, hogy lekérje az AD attribútumot, amelyet az előző Azure AD Connect-telepítésben sourceAnchor attribútumként használnak (ha van ilyen). Ha ez az információ elérhető, az Azure AD Connect ugyanazt az AD attribútumot használja.

  >[!NOTE]
  > Csak az Azure AD Connect (1.1.524.0 és azt követő) újabb verziói tárolnak adatokat az Azure AD-bérlőben a telepítés során használt sourceAnchor attribútumról. Az Azure AD Connect régebbi verziói nem.

* Ha a használt sourceAnchor attribútummal kapcsolatos információ nem érhető el, a varázsló ellenőrzi az ms-DS-ConsistencyGuid attribútum állapotát a helyszíni Active Directoryban. Ha az attribútum nincs konfigurálva a címtár egyetlen objektumán sem, a varázsló az ms-DS-ConsistencyGuid-ot használja sourceAnchor attribútumként. Ha az attribútum a címtár egy vagy több objektumán van konfigurálva, a varázsló arra a következtetésre jut, hogy az attribútumot más alkalmazások használják, és nem alkalmas sourceAnchor attribútumként...

* Ebben az esetben a varázsló visszaáll az objectGUID forrásanchor attribútumként való használatára.

* Miután a sourceAnchor attribútum eldőlt, a varázsló tárolja az adatokat az Azure AD-bérlőben. Az adatokat az Azure AD Connect jövőbeli telepítése fogja használni.

Az Express telepítése után a varázsló tájékoztatja, hogy melyik attribútum lett a Forráshorgony attribútum.

![A varázsló tájékoztatja a sourceAnchorhoz kiválasztott AD attribútumot](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Egyéni telepítés
Az Azure AD Connect egyéni móddal történő telepítésekor az Azure AD Connect varázsló két lehetőséget biztosít a sourceAnchor attribútum konfigurálásakor:

![Egyéni telepítés - sourceAnchor konfiguráció](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Beállítás | Leírás |
| --- | --- |
| Let Azure manage the source anchor for me (Az Azure kezelje a forráshorgonyt) | Válassza ezt a lehetőséget, ha azt szeretné, hogy az Azure AD válassza ki az attribútumot. Ha ezt a lehetőséget választja, az Azure AD Connect varázsló ugyanazt a [sourceAnchor attribútumkijelölési logikát](#express-installation)alkalmazza, amelyet az Express telepítése során használt. Az Expressz telepítéshez hasonlóan a varázsló tájékoztatja, hogy az egyéni telepítés befejezése után melyik attribútum lett a Forráshorgony attribútum. |
| A specific attribute (Egy adott attribútum) | Válassza ezt a lehetőséget, ha meglévő AD-attribútumot szeretne megadni forráshorgony-attribútumként. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>A Konzisztenciaguid szolgáltatás engedélyezése – Meglévő telepítés
Ha rendelkezik egy meglévő Azure AD Connect-központi telepítéssel, amely az objectGUID-t használja forráshorgony attribútumként, átválthat a ConsistencyGuid használatára.

>[!NOTE]
> Csak az Azure AD Connect újabb verziói (1.1.552.0 és azt követően) támogatják az ObjectGuid-ről a Konzisztencia guid-ra való áttérést forráshorgony attribútumként.

Váltás objectGUID-ról KonzisztenciaGuid forráshorgony attribútumra:

1. Indítsa el az Azure AD Connect varázslót, és kattintson a **Konfigurálás** gombra a Feladatok képernyő re.

2. Jelölje be a **Forráshorgony konfigurálása** feladat lehetőséget, és kattintson a **Tovább gombra.**

   ![KonzisztenciaGuid engedélyezése a meglévő telepítéshez - 2.](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Adja meg az Azure AD-rendszergazda hitelesítő adatait, és kattintson a **Tovább**gombra.

4. Az Azure AD Connect varázsló elemzi az ms-DS-consistencyGuid attribútum állapotát a helyszíni Active Directoryban. Ha az attribútum nincs konfigurálva a címtáregyetlen objektumon sem, az Azure AD Connect arra a következtetésre jut, hogy jelenleg egyetlen más alkalmazás sem használja az attribútumot, és biztonságosan használható forráshorgony attribútumként. A folytatáshoz kattintson a **Tovább** gombra.

   ![KonzisztenciaGuid engedélyezése a meglévő telepítéshez - 4.](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. A **Konfigurálás ra** **gombra** kattintva módosítsa a konfigurációt.

   ![KonzisztenciaGuid engedélyezése a meglévő telepítéshez – 5.](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. A konfiguráció befejezése után a varázsló azt jelzi, hogy az ms-DS-ConsistencyGuid most forráshorgony attribútumként van használva.

   ![KonzisztenciaGuid engedélyezése a meglévő telepítéshez - 6.](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Az elemzés során (4. lépés), ha az attribútum a címtár egy vagy több objektumán van konfigurálva, a varázsló arra a következtetésre jut, hogy az attribútumot egy másik alkalmazás használja, és az alábbi ábrán látható hibát ad vissza. Ez a hiba akkor is előfordulhat, ha korábban engedélyezte a KonzisztenciaGuid funkciót az elsődleges Azure AD Connect-kiszolgálón, és ugyanezt próbálja megtenni az átmeneti kiszolgálón.

![KonzisztenciaGuid engedélyezése meglévő telepítéshez - hiba](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Ha biztos abban, hogy az attribútumot nem használja más meglévő alkalmazások, a hibát az Azure AD Connect varázsló újraindításával a **/SkipLdapSearch** kapcsolóval. Ehhez futtassa a következő parancsot a parancssorban:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Az AD FS vagy a külső összevonási konfigurációra gyakorolt hatás
Ha az Azure AD Connect használatával kezeli a helyszíni AD FS-telepítést, az Azure AD Connect automatikusan frissíti a jogcímszabályokat, hogy ugyanazt az AD attribútumot használja sourceAnchorként. Ez biztosítja, hogy az ADFS által létrehozott ImmutableID-jogcím konzisztens az Azure AD-be exportált sourceAnchor-értékekkel.

Ha az AD FS szolgáltatást az Azure AD Connecten kívül kezeli, vagy külső összevonási kiszolgálókat használ a hitelesítéshez, manuálisan kell frissítenie az ImmutableID-jogcím jogcímszabályait, hogy azok összhangban legyenek az Azure AD-be exportált sourceAnchor-értékekkel az [AD FS jogcímszabályok módosítása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims)című szakaszban leírtak szerint. A varázsló a telepítés befejezése után a következő figyelmeztetést adja vissza:

![Külső összevonási konfiguráció](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Új könyvtárak hozzáadása a meglévő telepítéshez
Tegyük fel, hogy üzembe helyezte az Azure AD Connectet a ConsistencyGuid funkcióval, és most egy másik könyvtárat szeretne hozzáadni a központi telepítéshez. Amikor megpróbálja hozzáadni a címt, az Azure AD Connect varázsló ellenőrzi az ms-DS-ConsistencyGuid attribútum állapotát a címtárban. Ha az attribútum a címtár egy vagy több objektumán van konfigurálva, a varázsló arra a következtetésre jut, hogy az attribútumot más alkalmazások használják, és az alábbi ábrán látható hibát ad vissza. Ha biztos abban, hogy az attribútumot nem használják a meglévő alkalmazások, letilthatja a hibát az Azure AD Connect varázsló újraindításával a **/SkipLdapSearch** kapcsolóval a fent leírtak szerint, vagy további információkért forduljon a támogatási szolgálathoz.

![Új könyvtárak hozzáadása a meglévő telepítéshez](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Azure AD bejelentkezés
A helyszíni címtár integrálása az Azure AD-vel, fontos megérteni, hogy a szinkronizálási beállítások hogyan befolyásolhatják a felhasználó hitelesítési módját. Az Azure AD userPrincipalName (UPN) használatával hitelesíti a felhasználót. A felhasználók szinkronizálásakor azonban gondosan ki kell választania azt az attribútumot, amelyet a userPrincipalName értékéhez használni kell.

### <a name="choosing-the-attribute-for-userprincipalname"></a>A userPrincipalName attribútumának kiválasztása
Amikor kiválasztja az Azure-ban használandó UPN értékét biztosító attribútumot, biztosítania kell

* Az attribútumértékek megfelelnek az UPN szintaxisnak (RFC 822), azaz\@felhasználónév tartományformátumúnak kell lenniük.
* Az értékek utótagja megegyezik az Azure AD egyik ellenőrzött egyéni tartományával

A gyorsbeállításokban az attribútum feltételezett választási lehetősége a userPrincipalName. Ha a userPrincipalName attribútum nem tartalmazza azt az értéket, amelyet a felhasználóknak be kell jelentkezniük az Azure-ba, akkor az Egyéni telepítés lehetőséget kell **választania.**

### <a name="custom-domain-state-and-upn"></a>Egyéni tartomány állapota és upn
Fontos annak biztosítása, hogy az UPN-utótagnak legyen ellenőrzött tartománya.

John a contoso.com felhasználója. Azt szeretné, hogy János használja a\@helyszíni UPN john contoso.com jelentkezzen be az Azure-ba, miután szinkronizálta a felhasználókat az Azure AD-címtárcontoso.onmicrosoft.com. Ehhez hozzá kell adnia, és ellenőriznie kell contoso.com egyéni tartományként az Azure AD-ben, mielőtt elkezdené a felhasználók szinkronizálását. Ha az UPN-utótag János, például contoso.com, nem egyezik egy ellenőrzött tartomány az Azure AD-ben, majd az Azure AD lecseréli az UPN-utótag contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Nem irányítható helyszíni tartományok és upn az Azure AD-hez
Egyes szervezetek nem irányítható tartományokkal rendelkeznek, például contoso.local vagy egyszerű egycímkés tartományokkal, például contoso. Nem tudja ellenőrizni a nem irányítható tartomány az Azure AD-ben. Az Azure AD Connect csak ellenőrzött tartományhoz szinkronizálhat az Azure AD-ben. Amikor létrehoz egy Azure AD-címtárat, létrehoz egy irányítható tartományt, amely például az Azure AD alapértelmezett tartományává válik, contoso.onmicrosoft.com. Ezért ilyen esetben szükség van bármely más irányítható tartomány ellenőrzésére abban az esetben, ha nem szeretne szinkronizálni az alapértelmezett onmicrosoft.com tartománysal.

Az [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](../active-directory-domains-add-azure-portal.md) című további információért a tartományok hozzáadásáról és ellenőrzéséről.

Az Azure AD Connect észleli, ha nem irányítható tartományi környezetben fut, és megfelelően figyelmezteti, hogy folytassa a gyorsbeállításokat. Ha nem irányítható tartományban dolgozik, akkor valószínű, hogy a felhasználók upn-je is rendelkezik nem irányítható utótagokkal. Ha például a contoso.local alatt fut, az Azure AD Connect azt javasolja, hogy a gyorsbeállítások helyett egyéni beállításokat használjon. Az egyéni beállítások használatával megadhatja azt az attribútumot, amelyet upn-ként kell használni az Azure-ba való bejelentkezéshez, miután a felhasználók szinkronizálva vannak az Azure AD-vel.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
