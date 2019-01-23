---
title: Identitás identitásszinkronizálás és ismétlődő attribútumok rugalmassága |} A Microsoft Docs
description: Új viselkedését, hogy hogyan kezeli az egyszerű felhasználónév vagy ProxyAddress ütközések objektumokat használ az Azure AD Connect címtár-szinkronizálás során.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f3460520b8914a25807c77f631aa4c64f3b2efb0
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464867"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Identitásszinkronizálás és ismétlődő attribútumok rugalmassága
Ismétlődő attribútumok rugalmassága lehetővé teszi az Azure Active Directoryban, amely kiküszöböli a fennakadások nélkül használható által okozott az **UserPrincipalName** és **ProxyAddress** ütközik a Microsoft egyik futtatásakor szinkronizálás eszközökkel.

Ez a két attribútum általában egyedinek kell lennie minden szükséges **felhasználói**, **csoport**, vagy **forduljon** objektumok egy adott Azure Active Directory-bérlőben.

> [!NOTE]
> Csak a felhasználók UPN-EK rendelkezhet.
> 
> 

Ez a funkció lehetővé teszi, hogy új viselkedés szerepel a szinkronizálási folyamat a felhőben tárolt részéhez, ezért a független, és bármely Microsoft szinkronizálási termékhez, többek között az Azure AD Connect, a DirSync és a MIM + összekötő kapcsolódó ügyfél. Az általános kifejezés "a Szinkronizáló ügyfél", amelyek ezeket a termékeket bármelyike szolgál ebben a dokumentumban.

## <a name="current-behavior"></a>Jelenlegi működése
Ha üzembe helyezése egy új objektumot, amely megsérti az egyedi-e megkötés UPN vagy ProxyAddress értékkel tett kísérlet, Azure Active Directory letiltja a adott objektum létrehozását. Hasonlóképpen ha frissül egy objektum nem egyedi UPN vagy ProxyAddress, a frissítés sikertelen lesz. A regisztrációs kísérlet vagy a frissítés a rendszer megpróbálja újból végrehajtani a Szinkronizáló ügyfél minden kiviteli ciklust követően, és a hiba továbbra is fennáll, addig, amíg az ütközés fel lett oldva. Hiba történt a jelentés e-mailt akkor jön létre minden kísérlet után, és a Szinkronizáló ügyfél által naplózott hiba.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Viselkedés az ismétlődő attribútumok rugalmassága
Helyett a teljes, üzembe helyezése és a egy duplikált attribútummal rendelkező objektum frissítése sikertelen, az Azure Active Directory "karanténba" az ismétlődő attribútuma, amely sértene az egyediségre vonatkozó feltételnek. Ha ez az attribútum nem szükséges a kiépítést, UserPrincipalName, például a szolgáltatás egy helyőrző értéket rendeli hozzá. A formátum a következő ideiglenes értékek  
"***<OriginalPrefix>+ < 4DigitNumber > @<InitialTenantDomain>. onmicrosoft.com***".  
Ha az attribútum nem szükséges, például egy **ProxyAddress**, Azure Active Directory egyszerűen karanténba helyezheti a ütközés attribútumot, és folytatja az objektum létrehozása vagy frissítése.

Esetén karanténba az attribútum, az azonos hiba a jelentés e-mailben a régi viselkedés használt megkapja az ütközést vonatkozó adatokat. Azonban ezt az információt csak egyszer jelenik meg a hibajelentés, ha a karanténba helyezett történik, nem továbbra is be kell jelentkeznie a jövőbeli e-mailek. Ezenkívül az objektum az exportálás sikeres volt, mert a Szinkronizáló ügyfél nem naplózza a hibát, és nem próbálja meg újra a Létrehozás / frissítés ezt követő szinkronizálási ciklust követően.

Ez a viselkedés támogatásához új attribútum a felhasználó, csoport és az elérhetőségi objektumosztályok bővült:  
**DirSyncProvisioningErrors**

Ez az egy többértékű attribútum, amely az ütköző attribútumokat, amelyek sértene az egyediségre vonatkozó feltételnek, hozzá kell adni általában tárolására szolgál. A háttérben futó időzítő feladat engedélyezve van az Azure Active Directoryban, és tekintse meg az ismétlődő attribútum ütközéseket, amelyek meg lett oldva, és automatikusan eltávolítja a szóban forgó attribútumokat a karanténból óránként fut le.

### <a name="enabling-duplicate-attribute-resiliency"></a>Ismétlődő attribútumok rugalmassága engedélyezése
Ismétlődő attribútumok rugalmassága az alapértelmezett viselkedést lesz az összes Azure Active Directory-bérlők között. Az összes bérlőre vonatkozóan, amely az először 2016. augusztus 22-én vagy később szinkronizálás engedélyezve alapértelmezés szerint elérhető lesz. Bérlők számára, hogy a dátum előtt szinkronizálás engedélyezve lesz a szolgáltatás engedélyezve van, és kötegekben. 2016 szeptemberétől a bevezetés megkezdődik, és minden bérlő műszaki értesítési kapcsolattartó, az adott dátum, amikor a szolgáltatás engedélyezve van az e-mailben értesítést küld.

> [!NOTE]
> Ha ismétlődő attribútumok rugalmassága be van kapcsolva, nem lehet letiltani.

Ellenőrizze, hogy ha a szolgáltatás engedélyezve van-e a bérlő számára, megteheti az Azure Active Directory PowerShell-modul legújabb verziója letöltésével és futtatásával:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Set-MsolDirSyncFeature parancsmag segítségével proaktív módon engedélyezze a ismétlődő attribútumok rugalmassága szolgáltatást, mielőtt van kapcsolva a bérlő már nem. Az, hogy a szolgáltatás tesztelése, szüksége lesz egy új Azure Active Directory-bérlő létrehozása.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>A DirSyncProvisioningErrors objektumok azonosítása
Rendelkező objektumok, Azure Active Directory PowerShell-lel és az Office 365 felügyeleti portálon, a duplikált tulajdonság ütközés miatt ezek a hibák azonosításához jelenleg két módszer áll rendelkezésre. Tervezzük bővíteni a kiegészítő portál alapján a jelentéskészítés a jövőben.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
A PowerShell-parancsmagok ebben a témakörben a következő érték igaz:

* A következő parancsmagjainak mindegyike kis-és nagybetűket.
* A **– ErrorCategory PropertyConflict** mindig kell foglalni. Jelenleg nincs más típusú **ErrorCategory**, de ez a jövőben is kiterjeszthető.

Először első lépések: futtatása **Connect-MsolService** és a egy Bérlői rendszergazda hitelesítő adatok megadása.

A következő parancsmagokat és a kezelők ezután használja a hibák megtekintéséhez a különböző módon:

1. [Az összes megjelenítése](#see-all)
2. [Tulajdonság típus szerint](#by-property-type)
3. [Ütköző érték szerint](#by-conflicting-value)
4. [Egy karakterlánc-Search használatával](#using-a-string-search)
5. [Rendezve](#sorted)
6. [Az összes vagy egy korlátozott mennyiség](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Az összes megtekintése
A csatlakozás után attribútum kiépítés általános listájának megtekintéséhez a bérlőben lévő hibák futtassa:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Egy eredménye a következőhöz hasonló:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Tulajdonság típus szerint
Hibába ütközik, a tulajdonság típusa szerint, adja hozzá a **- PropertyName** jelzőt a a **UserPrincipalName** vagy **ProxyAddresses** argumentum:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Vagy

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Ütköző érték szerint
Adjon hozzá egy adott tulajdonságra vonatkozó hibaüzenetek jelennek meg a **- tulajdonságérték** jelző (**- PropertyName** kell használni, valamint ez a jelző hozzáadásakor):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Egy karakterlánc-search használatával
A széles körű karakterlánc keresés használata ehhez a **– keresési karakterlánc** jelzőt. Ez használható egymástól függetlenül az összes fenti jelzők, kivéve a **- ErrorCategory PropertyConflict**, amely mindig szükség:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Az összes vagy egy korlátozott mennyiség
1. **MaxResults <Int>**  korlátozza az értékeket egy adott számú lekérdezés segítségével.
2. **Az összes** annak érdekében, hogy minden eredmény abban az esetben, a hibák nagy számú létezik blobnevet is használható.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Office 365 felügyeleti portál
Címtár-szinkronizálási hibák az Office 365 felügyeleti központban tekintheti meg. A jelentés az Office 365 portálon csak megjeleníti **felhasználói** objektumok, amelyek ezeket a hibákat. Nem jelenik meg a kapcsolatos információ közötti ütközések **csoportok** és **névjegyek**.

![Aktív felhasználók](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "aktív felhasználók")

Címtár-szinkronizálási hibák megtekintése az Office 365 felügyeleti központban, lásd: [azonosíthatja a címtár-szinkronizálási hibák az Office 365-ben](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Szinkronizálási hibajelentés identitás
Ha ütközés duplikált attribútummal rendelkező objektum történik az új viselkedéssel értesítést tartalmazza a standard szintű szinkronizálási hibajelentés identitás küldött e-mail sablonjaként szolgál műszaki értesítési forduljon a bérlő számára. Van azonban fontos változás az ezt a viselkedést. Múltbeli időpont ismétlődő attribútum ütközés információ lenne szerepelnek minden ezt követő hibajelentés az ütközést megszüntetéséig. Az új viselkedéssel egy adott ütközés hibaértesítésre csak jelennek meg egyszer – az ütköző attribútum karanténba időpontjában.

Íme egy példa az e-mail-értesítés néz ki egy ProxyAddress ütközés:  
    ![Aktív felhasználók](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "aktív felhasználók")  

## <a name="resolving-conflicts"></a>Ütközések feloldása
Hibaelhárítási stratégia és a megoldási taktika a hibák kell nem eltérnek a duplikált attribútummal kapcsolatos hibák történtek kezelt módja. Az egyetlen különbség, hogy az időzítő feladat halmokat a bérlő automatikusan adja hozzá a szóban forgó attribútumot a megfelelő objektumot az ütközést megszűnése után a szolgáltatás oldalán keresztül.

A következő cikk ismerteti a különböző hibaelhárítási és megoldási stratégiák: [Ismétlődő vagy érvénytelen attribútumok megelőzése az Office 365-ben a címtár-szinkronizálás](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Ismert problémák
Ezek a problémák egyike hatására az adatok elvesztése vagy szolgáltatás teljesítménycsökkenést. Több esztétikai, mások okozhat standard "*előtti rugalmasság*" ismétlődő attribútummal kapcsolatos hibák, az ütközés attribútumot, és a egy másik karanténba helyett hibajelzést okoz bizonyos hibák extra manuális javítás felfelé megkövetelése.

**Core viselkedés:**

1. Attribútum konfigurációk objektumokat továbbra is ellentétben a duplikált attribútumokkal karanténba zárásának visszavonása exportálási hibák lépnek fel.  
   Példa:
   
    a. Új felhasználó létrehozása egy egyszerű Felhasználóneve az AD-ben **Joe@contoso.com** és ProxyAddress **smtp:Joe@contoso.com**
   
    b. Ez az objektum tulajdonságainak ütközik egy meglévő csoportot, ahol ProxyAddress az **SMTP:Joe@contoso.com**.
   
    c. Exportáláskor egy **ProxyAddress ütközés** hiba lépett fel a karanténba helyezett ütközés attribútumok nem. A művelet után minden ezt követő szinkronizálási ciklus rendszer, mielőtt a rugalmasság szolgáltatás engedélyezve lett volna.
2. Két csoport azonos SMTP-jön létre a helyszíni, ha egy nem tud üzembe helyezni az első kísérlet alkalmával a szabvány duplicitní **ProxyAddress** hiba. Az ismétlődő értéket a következő szinkronizálási ciklus után azonban megfelelően karanténba.

**Office portál jelentés**:

1. A részletes hibaüzenetet UPN ütközés készlet két objektum megegyezik. Ez azt jelzi, hogy azok is volt az UPN módosult / karanténba zárva, amikor tulajdonképpen csak egy ezek közül minden módosított adat.
2. A részletes hibaüzenetet az UPN ütközés egy felhasználóhoz, aki módosítva vagy karanténba egyszerű volt-e a megfelelő displayName jeleníti meg. Példa:
   
    a. **A felhasználó** szinkronizálja a az első **UPN = User@contoso.com** .
   
    b. **"B" felhasználó** próbált be a következő szinkronizálható **UPN = User@contoso.com** .
   
    c. **A "B" felhasználó** UPN módosul, amelyikben **User1234@contoso.onmicrosoft.com** és **User@contoso.com** adnak hozzá **DirSyncProvisioningErrors**.
   
    d. A hibaüzenetben **"b" felhasználónak** kell jelzi, hogy **az a felhasználó** már **User@contoso.com** bemutatja egy egyszerű, de mivel **User-B** saját displayName.

**Szinkronizálási hibajelentés identitás**:

A hivatkozás a *a probléma megoldásához szükséges lépéseket* helytelen:  
    ![Aktív felhasználók](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "aktív felhasználók")  

Kell mutatnia [ https://aka.ms/duplicateattributeresiliency ](https://aka.ms/duplicateattributeresiliency).

## <a name="see-also"></a>Lásd még
* [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
* [Azonosítsa a címtár-szinkronizálási hibák az Office 365-ben](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

