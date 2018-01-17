---
title: "Identitás-szinkronizálással és duplikált attribútum rugalmassági |} Microsoft Docs"
description: "Új viselkedését, hogy hogyan kezelje az egyszerű Felhasználónevet vagy ProxyAddress ütközések objektumok címtár-szinkronizálás Azure AD Connect használatával."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 7953e218614ba259db3cd45220de6b6c880608ad
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Identitásszinkronizálás és ismétlődő attribútumok rugalmassága
Ismétlődő attribútum rugalmasság az Azure Active Directoryban, amely kiküszöböli a okozta súrlódás szolgáltatása **UserPrincipalName** és **ProxyAddress** ütközik a Microsoft egyik futtatásakor szinkronizálási eszközöket.

Ez a két attribútum általában megköveteli a egyedinek kell lennie összes **felhasználói**, **csoport**, vagy **forduljon** objektumok egy adott Azure Active Directory-bérlő.

> [!NOTE]
> Csak a felhasználók rendelkezhetnek UPN-EK.
> 
> 

Az új módon működnek, hogy ez a funkció lehetővé teszi, hogy megtalálható-e a szinkronizálási folyamat a felhőben tárolt részéhez, ezért a független és bármely Microsoft szinkronizálási termékhez, beleértve az Azure AD Connect, a DirSync és a MIM + összekötő vonatkozó ügyfél. Általános kifejezés "sync ügyfél" ezeket a termékeket a képviselő szolgál ebben a dokumentumban.

## <a name="current-behavior"></a>Jelenlegi viselkedése
Egy egyszerű felhasználónév vagy ProxyAddress érték, amely megsérti az egyediségre vonatkozó új objektumot kiépítéséhez kísérlet esetén, Azure Active Directory blokkolja az objektum a létrehozás alatt áll. Hasonlóképpen ha nem egyedi UPN vagy ProxyAddress frissül egy objektum, a frissítés sikertelen lesz. A létesítési kísérlet vagy a frissítés során minden egyes exportálási ciklusa sync-ügyfél által a rendszer ismét megkísérli, és továbbra is sikertelen, amíg az ütközés fel lett oldva. Hiba történt a jelentés e-mailt minden kísérlet után történik, és a Szinkronizáló ügyfél által naplózott hiba.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Ismétlődő attribútum rugalmasságú viselkedése
Ahelyett, hogy teljesen nem sikerült telepíteni vagy frissíteni egy objektumot a duplikált attribútummal rendelkező, Azure Active Directory "karanténba" a ismétlődő attribútum, amely sértené az egyediségre vonatkozó feltételnek. Ha ez az attribútum szükséges történő üzembe helyezéséhez, UserPrincipalName, például a szolgáltatás egy helyőrző értéket rendeli hozzá. A formátum a következő ideiglenes értékek  
"***<OriginalPrefix>+ < 4DigitNumber > @<InitialTenantDomain>. onmicrosoft.com***".  
Ha az attribútum nincs szükség, például egy **ProxyAddress**, Azure Active Directory egyszerűen karanténba helyezheti a ütközés attribútum, és folytatja az objektum-létrehozás vagy frissítés.

Karanténba helyezés az attribútum, akkor az ütközés kapcsolatos információkat küldött az azonos hiba történt a jelentés e-mail régi működésében. Azonban ezeket az adatokat csak egyszer jelenik meg az esetleges hibajelentésben való megjelenítéshez, ha a karanténba helyezett történik, nem továbbra is be kell jelentkeznie e-maileket. Is, az objektum az exportálás sikerült, mert a Szinkronizáló ügyfél nem naplózza a hibát, és nem próbálja meg újra a create / update művelet során a következő szinkronizálási ciklusok.

Ez a viselkedés támogatásához új attribútum a felhasználó, csoport és az elérhetőségi objektumosztályok bővült:  
**DirSyncProvisioningErrors**

Ez az egy többértékű attribútum, amely az ütköző attribútumokat, amelyek sértené az egyediségre vonatkozó feltételnek kell adni őket általában tárolására szolgál. Az Azure Active Directoryban, amelyen meg lett oldva, és automatikusan eltávolítja a szóban forgó attribútumok karantén ismétlődő attribútum ütközések keres óránként időzítő háttérfeladat engedélyezve van.

### <a name="enabling-duplicate-attribute-resiliency"></a>Ismétlődő attribútum rugalmassági engedélyezése
Ismétlődő attribútum rugalmassági lesz az alapértelmezett viselkedést összes Azure Active Directory-bérlők között. Alapértelmezés szerint egyetlen bérlő számára, amely az először 2016 augusztusától 22. vagy újabb szinkronizálás engedélyezve lesz a. Bérlői, ez a dátum előtt szinkronizálási engedélyezve lesz kötegekben engedélyezett funkció. 2016 szeptemberétől bevezetés megkezdődik, és e-mailben értesítést küld az egyes bérlők technikai értesítés kapcsolatba lépnek a megadott dátum, amikor a szolgáltatás engedélyezve lesz.

> [!NOTE]
> Amennyiben az ismétlődő attribútum rugalmassági be van kapcsolva nem lehet letiltani.

Annak ellenőrzéséhez, ha a szolgáltatás engedélyezve van-e a bérlő számára, hogy ehhez az Azure Active Directory PowerShell-modul legfrissebb verziójának letöltését és futtatását:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Set-MsolDirSyncFeature parancsmag segítségével a duplikált attribútum rugalmassági funkció proaktív engedélyezése előtt van kapcsolva a bérlő nem. Nem fogja tudni a szolgáltatás tesztelése, szüksége lesz egy új Azure Active Directory-bérlő létrehozása.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>A DirSyncProvisioningErrors objektumok azonosítása
Ezek a hibák miatt duplikált tulajdonság ütközik, Azure Active Directory PowerShell és az Office 365 felügyeleti portál rendelkező objektumok azonosítása jelenleg két módszer áll rendelkezésre. Nincsenek további portál alapján Reporting a későbbiekben kibővítheti tervek.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Ez a témakör a PowerShell parancsmagjainak a következő helyzetekben:

* A következő parancsmagok mind kis-és nagybetűket.
* A **– ErrorCategory PropertyConflict** mindig szerepelnie kell függvénykötésnek. Jelenleg nincs más típusú **ErrorCategory**, de ez a jövőben is kiterjeszthető.

Először futtassa a kezdéshez **Connect-MsolService** és a hitelesítő adatok megadása egy Bérlői rendszergazda.

Ezt követően használja a következő parancsmagokat és az operátorok meg hibákat különböző módon:

1. [Összes](#see-all)
2. [Tulajdonság típus szerint](#by-property-type)
3. [Ütköző értékkel](#by-conflicting-value)
4. [Egy karakterlánc-keresés](#using-a-string-search)
5. [Rendezve](#sorted)
6. [Az összes vagy egy korlátozott mennyiség](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Az összes megtekintése
A csatlakozás után listáját általános attribútum kiépítése a bérlő hibáinak futtassa:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Egy eredménye a következőhöz hasonló:  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Tulajdonság típus szerint
Tulajdonság típus szerint hibába ütközik, vegye fel a **- PropertyName** rendelkező jelzőt a **UserPrincipalName** vagy **ProxyAddresses** argumentum:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Vagy

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Ütköző értékkel
Adja hozzá egy adott tulajdonságra vonatkozó hibaüzenetek jelennek meg a **- PropertyValue** jelző (**- PropertyName** kell használni, valamint a jelző hozzáadásakor):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Egy karakterlánc-keresés
A széles körű karakterlánc keresés használata ehhez a **- Keresési_karakterlánc** jelzőt. Használható önállóan az összes fenti jelzők, kivéve a **- ErrorCategory PropertyConflict**, amely mindig szükség:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Az összes vagy egy korlátozott mennyiség
1. **MaxResults <Int>**  korlátozza a lekérdezés egy bizonyos számú értékeket is használható.
2. **Minden** segítségével győződjön meg arról, hogy a rendszer minden eredmény abban az esetben hibák nagy számú létező kérdezi le.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Office 365 felügyeleti portál
Címtár-szinkronizálási hibák tekintheti meg az Office 365 felügyeleti központban. Ez a jelentés az Office 365 portál csak megjeleníti **felhasználói** objektumok, amelyek ezeket a hibákat. Az információ közötti ütközések nem megjelenítése **csoportok** és **névjegyek**.

![Aktív felhasználók](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "aktív felhasználók")

Címtár-szinkronizálási hibák megtekintése az Office 365 felügyeleti központban, lásd: [az Office 365-ben a címtár szinkronizálási hibák](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Identitás szinkronizálási hibajelentés
Ha ismétlődő attribútum ütközés objektum kezeli az új viselkedéssel értesítést tartalmazza a szabványos identitás szinkronizálási esetleges hibajelentésben való megjelenítéshez e-mailben küldi el az a műszaki értesítési lépjen kapcsolatba a bérlő számára. Van azonban fontos változás az ezt a viselkedést. A múltban ismétlődő attribútum ütközés információ szerepel minden későbbi esetleges hibajelentésben való megjelenítéshez az ütközés megoldásáig. Az új viselkedéssel a Hibaértesítések egy adott ütközés csak jelennek meg többször - ütköző attribútum karanténba időpontjában.

Íme egy példa az e-mail értesítés néz egy ProxyAddress ütközés:  
    ![Aktív felhasználók](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "aktív felhasználók")  

## <a name="resolving-conflicts"></a>Az ütközések feloldása
A hibák stratégia és megoldás szerint taktikai hibaelhárítási kell nem eltérnek a ismétlődő attribútum hibák az elmúlt volt kezelése. Az egyetlen különbség az, hogy az időzítő feladat halmokat automatikusan hozzáadása a szóban forgó attribútum a megfelelő objektumot, az ütközés feloldása után a szolgáltatás-oldalán a bérlő keresztül.

A következő cikk ismerteti a különböző hibakeresési és feloldási stratégiák: [duplikált vagy érvénytelen attribútumok megelőzése az Office 365-ben a címtár-szinkronizálás](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Ismert problémák
Ezek a problémák egyike hatására az adatok elvesztése vagy szolgáltatás teljesítménycsökkenést. Ezek esztétikai, a többi standard "*előtti rugalmassági*" ismétlődő attribútum hibákat okozhat az ütközés attribútum, és egy másik karanténba helyezés helyett okoz bizonyos hibák extra manuális javítás létrehozása szükséges.

**Core viselkedést:**

1. A specifikus attribútummal konfigurációival objektumok azután is megkapják az Exportálás hibák szemben a karanténba helyezett duplikált attribútum.  
   Példa:
   
    a. Új felhasználó jön létre egy egyszerű az Active Directory  **Joe@contoso.com**  és ProxyAddress**smtp:Joe@contoso.com**
   
    b. Ez az objektum tulajdonságainak ütközik egy meglévő csoportot, ahol ProxyAddress az  **SMTP:Joe@contoso.com** .
   
    c. Exportáláskor egy **ProxyAddress ütközés** hibát vált ki, ahelyett, hogy a karanténba helyezett ütközés attribútumok. A művelet megismétlése minden ezt követő szinkronizálási ciklust követően, mielőtt az rugalmassági engedélyezve lett volna.
2. Két csoport jön létre a helyszíni azonos SMTP-címével, ha egy nem sikerül az első kísérlet a szabványos duplikált rendelkezés **ProxyAddress** hiba. Azonban az ismétlődő értéket megfelelően karanténban van, a következő szinkronizálási ciklusban.

**Office-portál jelentés**:

1. A részletes hibaüzenet egy egyszerű felhasználónév ütközés készlet két objektumok megegyezik. Ez azt jelzi, hogy azok mind volt az egyszerű Felhasználónevük megváltozott / karanténba helyezve, amikor tulajdonképpen csak egy ezek közül kellett módosult adatokat.
2. A részletes hibaüzenetet UPN ütközés jeleníti meg a megfelelő displayName egy felhasználóhoz, aki volt-e az egyszerű Felhasználónevük megváltozott vagy karanténba helyezve. Példa:
   
    a. **A felhasználó** fel az első szinkronizálásának **UPN = User@contoso.com** .
   
    b. **"B" felhasználó** megpróbálta be a következő szinkronizálva **UPN = User@contoso.com** .
   
    c. **Felhasználói B** UPN változott  **User1234@contoso.onmicrosoft.com**  és  **User@contoso.com**  hozzáadódik **DirSyncProvisioningErrors**.
   
    d. A hibaüzenetet **felhasználói B** kell jeleznie, hogy **a felhasználó** már  **User@contoso.com**  egy egyszerű, de látható **felhasználói B** saját displayName.

**Identitás szinkronizálási esetleges hibajelentésben való megjelenítéshez**:

A mutató *lépéseit a probléma megoldásához* helytelen:  
    ![Aktív felhasználók](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "aktív felhasználók")  

Mutasson kell [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency).

## <a name="see-also"></a>Lásd még
* [Az Azure AD Connect szinkronizálása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
* [Az Office 365-ben a címtár szinkronizálási hibák](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

