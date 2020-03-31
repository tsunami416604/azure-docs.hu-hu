---
title: Identitás-szinkronizálás és az attribútumok duplikált rugalmassága | Microsoft dokumentumok
description: Új viselkedése, hogyan kell kezelni az objektumok at UPN vagy ProxyAddress ütközések során címtár szinkronizálása az Azure AD Connect használatával.
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
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5585f0cd04dca4145f0322db9d625e35372b24b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298343"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Identitásszinkronizálás és ismétlődő attribútumok rugalmassága
A duplikált attribútum-rugalmasság az Azure Active Directory egyik szolgáltatása, amely kiküszöböli a **UserPrincipalName** és az SMTP **ProxyAddress** ütközések által okozott súrlódást a Microsoft egyik szinkronizálási eszközének futtatásakor.

Ez a két attribútum általában egyedinek kell lennie az azure-beli Active Directory-bérlő összes **felhasználó,** **csoport**vagy **kapcsolattartó** objektumában.

> [!NOTE]
> Csak a felhasználók rendelkezhetnek UPN-ekkel.
> 
> 

A szolgáltatás által lehetővé tesz új viselkedés a szinkronizálási folyamat felhőrésze, ezért az ügyfél agnosztikus és releváns minden Microsoft szinkronizálási termék, beleértve az Azure AD Connect, DirSync és MIM + Connector. A dokumentum a "szinkronizálási ügyfélprogram" általános kifejezést használja a termékek bármelyikének ábrázolására.

## <a name="current-behavior"></a>Jelenlegi viselkedés
Ha egy új objektum kiépítésére próbál nak egy upn vagy proxyaddress értékkel, amely megsérti ezt az egyediségi megkötést, az Azure Active Directory blokkolja az objektum létrehozását. Hasonlóképpen, ha egy objektum nem egyedi upn vagy proxycím vel frissül, a frissítés sikertelen lesz. A létesítési kísérletet vagy frissítést a szinkronizálási ügyfél minden exportálási ciklusután újra megkísérli, és az ütközés feloldásáig továbbra is sikertelen. Minden kísérlet után hibajelentés jön létre, és a szinkronizálási ügyfél hibát naplóz.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Viselkedés az attribútum duplikált rugalmasságával
Ahelyett, hogy teljesen nem egy objektum kiépítése vagy frissítése egy duplikált attribútum, az Azure Active Directory "karanténba" a duplikált attribútum, amely megsértené az egyediségi megkötés. Ha ez az attribútum szükséges a kiépítéshez, például a UserPrincipalName, a szolgáltatás helyőrző értéket rendel hozzá. Ezeknek az ideiglenes értékeknek a formátuma  
OriginalPrefix>_**+\<4DigitNumber \@ \<>InitialTenantDomain>.onmicrosoft.com . \<**_

Az attribútum rugalmassági folyamatcsak az UPN és az SMTP **ProxyAddress** értékeket kezeli.

Ha az attribútum nem szükséges, például egy **ProxyAddress**, az Azure Active Directory egyszerűen karanténba helyezi az ütközés attribútumot, és folytatja az objektum létrehozása vagy frissítése.

Az attribútum karanténba vételekor az ütközéssel kapcsolatos információkat a régi viselkedésben használt hibajelentés-e-mailben küldi el a rendszer. Ez az információ azonban csak egyszer jelenik meg a hibajelentésben, amikor a karantén megtörténik, nem kerül továbbra is naplózva a jövőbeli e-mailekbe. Továbbá, mivel az objektum exportálása sikeres volt, a szinkronizálási ügyfél nem naplóz hibát, és nem próbálja meg újra a létrehozási/ frissítési műveletet a következő szinkronizálási ciklusok után.

A viselkedés támogatásához új attribútum került a Felhasználó, csoport és kapcsolattartó objektum osztályaiba:  
**DirSyncProvisioninghibák**

Ez egy többértékű attribútum, amely az ütköző attribútumok tárolására szolgál, amelyek megsértenék az egyediségi megkötést, ha azokat a szokásos módon adnák hozzá. A háttéridőzítő feladat engedélyezve van az Azure Active Directoryban, amely óránként fut, hogy keresse meg a megoldott ismétlődő attribútumütközéseket, és automatikusan eltávolítja a kérdéses attribútumokat a karanténból.

### <a name="enabling-duplicate-attribute-resiliency"></a>Ismétlődő attribútumrugalmasság engedélyezése
Duplikált attribútum rugalmasság lesz az új alapértelmezett viselkedés az összes Azure Active Directory-bérlők. Ez lesz alapértelmezés szerint az összes bérlő, amely lehetővé tette a szinkronizálást először augusztus 22-én, 2016 vagy újabb. Azok a bérlők, amelyek engedélyezték a szinkronizálást a dátum előtt, a szolgáltatás kötegekben engedélyezve lesz. Ez a bevezetés 2016 szeptemberében kezdődik, és e-mailértesítést küld minden bérlő technikai értesítési kapcsolattartójának az adott dátummal, amikor a funkció engedélyezve lesz.

> [!NOTE]
> Az Attribútum duplikálása után a rugalmasság bekapcsolása után nem tiltható le.

Annak ellenőrzéséhez, hogy a szolgáltatás engedélyezve van-e a bérlő számára, ezt megteheti az Azure Active Directory PowerShell modul legújabb verziójának letöltésével és a következő futtatásával:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> A Set-MsolDirSyncFeature parancsmag már nem használható proaktív módon az attribútum-rugalmasság identifikálási funkció engedélyezéséhez, mielőtt az be lenne kapcsolva a bérlőnél. Ahhoz, hogy tesztelhesd a funkciót, létre kell hoznia egy új Azure Active Directory-bérlőt.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>DirSyncProvisioningError típusú hibákat tartalmazó objektumok azonosítása
Jelenleg két módszer létezik az ismétlődő tulajdonságütközések miatt ezeket a hibákat tartalmazó objektumok azonosítására, az Azure Active Directory PowerShell és a [Microsoft 365 felügyeleti központ.](https://admin.microsoft.com) A tervek szerint a jövőben további portálalapú jelentésekre is kiterjednek.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
A témakörben szereplő PowerShell-parancsmagok esetében a következő igaz:

* Az alábbi parancsmagok mindegyike a kis- és nagybetűket nem figyelembe vevő.
* Az **-ErrorCategory propertyConflict** függvényt mindig bele kell foglalni. Jelenleg nincs más típusú **ErrorCategory**, de ez lehet terjeszteni a jövőben.

Először is a **Connect-MsolService** futtatásával és a bérlői rendszergazda hitelesítő adatainak megadásával.

Ezután a következő parancsmagokkal és operátorokkal különböző módokon tekintheti meg a hibákat:

1. [Az összes megtekintése](#see-all)
2. [Tulajdonság típusa szerint](#by-property-type)
3. [Ütköző érték szerint](#by-conflicting-value)
4. [Karakterlánc-keresés használata](#using-a-string-search)
5. Rendezve
6. [Korlátozott mennyiségben vagy mindenben](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Az összes megjelenítése
Miután csatlakozott, az attribútum-kiépítési hibák általános listájának megtekintéséhez a bérlői futtatásban:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Ez a következőhez hasonló eredményt ad:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Tulajdonságtípus szerint
A hibák tulajdonságtípus szerinti megtekintéséhez adja hozzá a **-PropertyName** jelzőt a **UserPrincipalName** vagy **a ProxyAddresses** argumentummal:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Vagy

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Ütköző értékkel
Egy adott tulajdonsággal kapcsolatos hibák megtekintéséhez adja hozzá a **-PropertyValue** jelzőt (**-PropertyName** függvényt is a jelző hozzáadásakor):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Karakterlánc-keresés használata
A széles karakterlánc-kereséshez használja a **-SearchString** jelzőt. Ez az összes fenti jelzőtől függetlenül használható, kivéve a **-ErrorCategory PropertyConflict**értéket, amely mindig szükséges:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Korlátozott mennyiségben vagy az összes
1. **A \<MaxResults Int>** segítségével a lekérdezést meghatározott számú értékre korlátozhatja.
2. **Mind** felhasználható annak biztosítására, hogy az összes eredmény lekérésre kerüljön abban az esetben, ha nagy számú hiba áll rendelkezésre.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Microsoft 365 felügyeleti központ
A címtár-szinkronizálási hibáka a Microsoft 365 Felügyeleti központban tekinthető kontráda. A Microsoft 365 Felügyeleti központban található jelentés csak azokat a **Felhasználói** objektumokat jeleníti meg, amelyek ezeket a hibákat tartalmazják. Nem jelenít meg adatokat a **csoportok** és a partnerek közötti **ütközésekről.**

![Aktív felhasználók](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "Aktív felhasználók")

A Microsoft 365 Felügyeleti központban a címtár-szinkronizálási hibák megtekintéséről a [Címtár-szinkronizálási hibák azonosítása az Office 365-ben](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)című témakörben talál útmutatást.

### <a name="identity-synchronization-error-report"></a>Identitás-szinkronizálási hibajelentés
Ha egy ismétlődő attribútumütközéssel rendelkező objektumot kezel ez az új viselkedés, egy értesítés szerepel a szabványos identitás-szinkronizálási hibajelentés e-mailben, amelyet a bérlő műszaki értesítési kapcsolattartójának küld. Azonban van egy fontos változás ebben a viselkedésben. A múltban az ismétlődő attribútumütközésekkel kapcsolatos információk minden további hibajelentésben szerepelni fognak, amíg az ütközés meg nem oldódik. Ezzel az új viselkedéssel egy adott ütközéshiba-értesítés csak egyszer jelenik meg, amikor az ütköző attribútum karanténba kerül.

Íme egy példa arra, hogy hogyan néz ki az e-mail értesítés egy ProxyAddress ütközés esetén:  
    ![Aktív felhasználók](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktív felhasználók")  

## <a name="resolving-conflicts"></a>Ütközések feloldása
A hibák hibaelhárítási stratégiája és megoldási taktikája nem térhet el attól, ahogyan a duplikált attribútumhibákat a múltban kezelték. Az egyetlen különbség az, hogy az időzítő feladat végigsöpör a bérlőn a szolgáltatás-oldalon, hogy automatikusan hozzáadja a kérdéses attribútumot a megfelelő objektumhoz, miután az ütközés megoldódott.

A következő cikk különböző hibaelhárítási és megoldási stratégiákat ismertet: [Az attribútumok duplikálása vagy érvénytelen ek megakadályozzák a címtár-szinkronizálást az Office 365-ben.](https://support.microsoft.com/kb/2647098)

## <a name="known-issues"></a>Ismert problémák
Ezen ismert problémák egyike sem okoz adatvesztést vagy szolgáltatáskárosodást. Több közülük esztétikai, mások okoznak standard "*pre-rugalmasság*" kettős attribútum hibákat kell dobni, hanem quarantining a konfliktus attribútum, és egy másik okoz bizonyos hibákat igényel extra kézi javítás.

**Alapvető viselkedés:**

1. Az adott attribútumkonfigurációval rendelkező objektumok továbbra is exportálási hibákat kapnak, szemben a karanténba helyezett ismétlődő attribútumokkal.  
   Példa:
   
    a. Új felhasználó jön létre az AD egy UPN **Joe\@contoso.com** és ProxyAddress **smtp:\@Joe contoso.com**
   
    b. Az objektum tulajdonságai ütköznek egy meglévő csoporttal, ahol a ProxyAddress az **SMTP:Joe\@contoso.com**.
   
    c. Exportáláskor **a proxycím ütközési** hiba jelenik meg, ahelyett, hogy az ütközési attribútumokat karanténba helyeznék. A művelet minden további szinkronizálási ciklus után újra próbálkozik, ahogy az a rugalmassági szolgáltatás engedélyezése előtt történt volna.
2. Ha két csoport jön létre a helyszínen ugyanazzal az SMTP-címmel, az egyik nem tud kiépíteni az első kísérlet egy szabványos duplikált **ProxyAddress** hiba. A duplikált érték azonban megfelelően karanténba kerül a következő szinkronizálási ciklusban.

**Office Portal jelentés**:

1. Az upn-ütközéskészlet két objektumának részletes hibaüzenete megegyezik. Ez azt jelzi, hogy mindketten megváltoztatták / karanténba helyezték az UPN-t, holott valójában csak egyikük nek volt adata.
2. Az upn-ütközés részletes hibaüzenete rossz displayName értéket jelenít meg egy olyan felhasználó számára, akinek az upn-jét módosították/karanténba helyezték. Példa:
   
    a. **Az A felhasználó** először szinkronizálja **az UPN =\@User contoso.com.**
   
    b. **A "B" felhasználó** taszerint a rendszer a következő szinkronizálást kísérli meg az **UPN = User contoso.com .\@**
   
    c. **"B" felhasználó** Az UPN **szolgáltatás user1234\@contoso.onmicrosoft.com-re változik,** és a Felhasználói **\@contoso.com** hozzáadódik a **DirSyncProvisioningErrors értékhez.**
   
    d. A **"B" felhasználó** hibaüzenetének azt kell jeleznie, hogy **az "A" felhasználó** már **rendelkezik felhasználói\@contoso.com,** de **"B" felhasználó** saját displayName azonosítóját jeleníti meg.

**Identitás-szinkronizálási hibajelentés:**

A *probléma megoldásának lépéseit mutató* hivatkozás helytelen:  
    ![Aktív felhasználók](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktív felhasználók")  

Meg kell [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency)mutatnia, hogy .

## <a name="see-also"></a>Lásd még
* [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
* [Címtár-szinkronizálási hibák azonosítása az Office 365-ben](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

