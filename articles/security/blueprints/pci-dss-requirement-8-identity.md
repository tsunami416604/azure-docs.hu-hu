---
title: Az Azure támogatási feldolgozási tervezetének - identitáskövetelmények
description: PCI DSS követelmény 8
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1a398601-8c48-4f8e-b3d4-eba94edad61c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a83040a6b5174307ea73e5473165835458d217f6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894921"
---
# <a name="identity-requirements-for-pci-dss-compliant-environments"></a>Identitáskövetelmények PCI DSS-kompatibilis környezetben 
## <a name="pci-dss-requirement-8"></a>PCI DSS követelmény 8

**Azonosíthatják és hitelesíthetik a hozzáférés-összetevőkkel**

> [!NOTE]
> Ezeket a követelményeket határozzák meg a [Payment Card Industry (PCI) biztonsági szabványok Tanács](https://www.pcisecuritystandards.org/pci_security/) részeként a [PCI adatok biztonsági szabvány (DSS) 3.2-es verziójú](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Tekintse meg a PCI DSS tesztelési eljárások és az egyes követelményekhez útmutatót olvashat.

Egy egyedi azonosítót (ID) hozzárendelése minden hozzáféréssel rendelkező személy biztosítja, hogy minden egyes egyedileg kiszolgálófelügyeletért felelős a műveleteket. Ha ilyen elszámolási kötelezettségéről szóló van beállítva, a kritikus fontosságú adatok és végrehajtott műveleteket hajtja végre, és visszavezethető legyen, ismert és a jogosult felhasználók és folyamatok.
Jelszó hatékonyságát nagymértékben határozza meg a megtervezését és megvalósítását a hitelesítési rendszere – különösen, milyen gyakran jelszómegadási kísérletek végezhetők által a támadók, és a biztonsági módszerek felhasználói jelszavak, beléptetési védelme átvitel során, és a tároló.

> [!NOTE]
> Ezek a követelmények vonatkoznak az összes fiók, például pénztári fiókok rendszergazdai képességeinek és megtekintésére és kártya tulajdonosát elérésére használt összes fiókok adatok és hozzáférési rendszerekre kártya tulajdonosát adatokkal. Ez magában foglalja a (például a támogatási szolgálathoz vagy karbantartás) a szállítók és az egyéb harmadik felek által használt fiókok. Ezek a követelmények nem vonatkoznak a fogyasztók (pl. cardholders) által használt fiókok. Azonban követelmények 8.1.1 8.2, 8.5, 8.2.5 és a 8.1.6 keresztül 8.1.8 8.2.3 nem feltétlenül vonatkozik rendelkező felhasználói fiókok POS támogatási kérelmet belül csak egy kártya a szám egyszerre csak egy egyetlen tranzakció (elősegítése érdekében például pénztáros fiókok).
 
## <a name="pci-dss-requirement-81"></a>PCI DSS követelmény 8.1

**8.1** definiálása és alkalmazzon házirendet és eljárások, amelyek biztosítják a megfelelő felhasználói azonosító kezelése nem identitásrendszerében a felhasználók és rendszergazdák számára a rendszer minden összetevőjét az alábbiak szerint.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló üzembe helyezési minta használati esetek és egy leírást a helyes szintaxis a rendszergazdák biztosít.|



### <a name="pci-dss-requirement-811"></a>PCI DSS követelmény 8.1.1

**8.1.1** mielőtt engedélyezné számukra a hozzáférést rendszerösszetevők vagy kártya tulajdonosát adatok egyedi Azonosítót minden felhasználók hozzárendelése.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló megvalósítja az Azure Active Directoryban, és Azure Active Directory Role-Based hozzáférés-vezérlés (RBAC) ahhoz, hogy minden felhasználó rendelkezik egy egyedi azonosítót. További információkért lásd: [PCI útmutatást - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-812"></a>PCI DSS követelmény 8.1.2-es

**8.1.2-es** hozzáadását, törlését és tartozó felhasználói azonosítók, hitelesítő adatokat és egyéb azonosító objektumok szabályozzák.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló megvalósítja az Azure Active Directoryban, és Azure Active Directory Role-Based hozzáférés-vezérlés (RBAC) ahhoz, hogy minden felhasználó rendelkezik egy egyedi azonosítót. További információkért lásd: [PCI útmutatást - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-813"></a>PCI DSS követelmény 8.1.3

**8.1.3** azonnal bármely visszavonhatja a hozzáférést a felhasználók megszakadt.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló használja az Azure Active Directory a felhasználók kezelése. A felhasználók a visszavont tanúsítványok az Active Directoryban végezhető.|



### <a name="pci-dss-requirement-814"></a>PCI DSS követelmény 8.1.4

**8.1.4** eltávolíthatja vagy letilthatja az inaktív felhasználói fiókok 90 napon belül.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló Azure Active Directory használ a felhasználókezeléshez. A `-enableADDomainPasswordPolicy` beállítás is, annak érdekében, jelszavak 90 nap múlva lejár.|



### <a name="pci-dss-requirement-815"></a>PCI DSS követelmény 8.1.5

**8.1.5** eléréséhez, harmadik felek által használt kezelése azonosítók támogatja, vagy távolról rendszerösszetevők karbantartása az alábbiak szerint:
- Engedélyezve van, csak az időszakban az szükséges, és le van tiltva, ha nincsenek használatban.
- Figyeli a rendszer, ha használatban.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure elfogadta a megfelelő vállalati és szervezeti biztonsági házirendek, beleértve az információk biztonsági házirend. A házirendek lett jóváhagyott, közzétett, és a Microsoft Azure továbbítani. Információk biztonsági házirend a engedélyezhető a Microsoft Azure eszközökhöz való hozzáférés üzleti indoklásának, az eszköz tulajdonosa engedélyt az alapján, és a "szükség alapú tudás" és "legalacsonyabb jogosultsági" alapelvek alapján korlátozott használatához. Ezenkívül a házirend is javítja a hozzáférés-kezelés életciklusának többek között a hozzáférés-kiépítés, a hitelesítés, a hozzáférés-engedélyezési követelményei, eltávolítás hozzáférési jogokkal, és az időszakos hozzáférés értékelést. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló bemutató javítást alkalmazott, Azure Active Directory és Azure Active Directory Role-Based hozzáférés-vezérlés kezelése a telepítés a felhasználó hozzáférést. További információkért lásd: [PCI útmutatást - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-816"></a>PCI DSS követelmény 8.1.6

**8.1.6** korlát ismételt kísérlete zárolja a felhasználói Azonosítóját, legfeljebb hat kísérlet után.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Contoso webes tároló egyértelmű elkülönítése feladataik (GYEPTÉGLA) a bemutató minden felhasználó részére van megvalósítva. További információkért lásd: ""Azure Active Directory identitás védelem" [PCI útmutatást - Identity Management](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-817"></a>PCI DSS követelmény 8.1.7-es

**8.1.7-es** beállítása a kizárás időtartama legalább 30 perc vagy egy rendszergazda engedélyezi a felhasználói azonosítóját.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Felhasználók létrehozása, érvényesítése és PCI DSS követelményekkel kompatibilis jelszóházirend figyelési felelősek.|



### <a name="pci-dss-requirement-818"></a>PCI DSS követelmény 8.1.8

**8.1.8** munkamenet üresjáratban több mint 15 percig, ha a felhasználónak újra hitelesíteni a Terminálszolgáltatások vagy a munkamenet újbóli aktiválása.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Felhasználók létrehozása, érvényesítése és PCI DSS követelményekkel kompatibilis jelszóházirend figyelési felelősek.|



## <a name="pci-dss-requirement-82"></a>PCI DSS követelmény 8.2

**8.2** amellett, hogy az egyedi Azonosítót, megfelelő felhasználó-hitelesítési kezelése érdekében nem identitásrendszerében a felhasználók és rendszergazdák számára a rendszer minden összetevőjét minden felhasználó hitelesítéséhez az alábbi eljárások közül legalább egy alkalmazásával:
- Valami ismeri, például egy jelszót vagy hozzáférési kódot
- Valami, rendelkezik-e a token eszköz vagy az intelligens kártya
- Valami áll, például egy biometrikus

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló megvalósítása a többtényezős hitelesítés le van tiltva, könnyen használható a bemutató a biztosításához. Többtényezős hitelesítés használatával valósítható [Azure multi-factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/).|



### <a name="pci-dss-requirement-821"></a>PCI DSS követelmény 8.2.1

**8.2.1** az erős titkosítás használata leképezési összes hitelesítő adatok (például a jelszavak/kifejezések) nem olvasható átvitel és a rendszer minden összetevőjét tárolás közben.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure létesített kulcskezelés eljárások az életciklus (pl. generációs, terjesztési, Visszavonás) teljes titkosítási kulcsok kezeléséhez. A Microsoft Azure a Microsoft vállalati nyilvános kulcsokra ÉPÜLŐ infrastruktúra használja. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló erős jelszavakat, a telepítési útmutatóban leírt érvénybe lépteti. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



### <a name="pci-dss-requirement-822"></a>PCI DSS követelmény 8.2.2.

**8.2.2** ellenőrizze felhasználó identitását, mielőtt a hitelesítő adatok módosítása – például jelszó-átállításra hajt végre, amikor új jogkivonatokat, vagy a új kulcs létrehozásakor.


**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure létesített kulcskezelés eljárások az életciklus (pl. generációs, terjesztési, Visszavonás) teljes titkosítási kulcsok kezeléséhez. A Microsoft Azure a Microsoft vállalati nyilvános kulcsokra ÉPÜLŐ infrastruktúra használja. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló erős jelszavakat, a telepítési útmutatóban leírt érvénybe lépteti. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-823"></a>PCI DSS követelmény 8.2.3.

**8.2.3** jelszavak/jelszavaknak meg kell felelnie a következő:
- A minimális hosszának legalább hét karakter megkövetelése.
- Numerikus és alfabetikus karaktereket is tartalmaz.
A jelszavak/jelszavak azt is megteheti, rendelkeznie kell összetettségét és erőssége legalább a fentiekben megadott paraméterek.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló erős jelszavakat, a telepítési útmutatóban leírt érvénybe lépteti.|



### <a name="pci-dss-requirement-824"></a>PCI DSS követelmény 8.2.4.

**8.2.4** felhasználói jelszavak/jelszavak módosítása a 90 naponta legalább egyszer.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló Azure Active Directory használ a felhasználókezeléshez. A `-enableADDomainPasswordPolicy` lehetőséget annak érdekében, hogy a jelszavak lejárnak 90 naponta legalább egyszer állítható be.|



### <a name="pci-dss-requirement-825"></a>PCI DSS követelmény 8.2.5.

**8.2.5** nem teszik lehetővé az egy adott küldeni egy új jelszót vagy hozzáférési kódot, amely ugyanolyan, mint bármelyik, az utolsó négy jelszavak/jelszavak ő használatban van.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló erős jelszavakat, a telepítési útmutatóban leírt érvénybe lépteti. További információkért lásd: [PCI útmutatást - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-826"></a>PCI DSS követelmény 8.2.6.

**8.2.6** jelszavak/jelszavak alaphelyzetbe állítása és az első használatnál beállítása egyedi értéket minden olyan felhasználóhoz, és közvetlenül az első használat után módosítsa.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló erős jelszavakat, a telepítési útmutatóban leírt érvénybe lépteti. További információkért lásd: [PCI útmutatást - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



## <a name="pci-dss-requirement-83"></a>8.3 PCI DSS követelmény

**8.3** biztonságos minden egyes konzolon belüli rendszergazdai hozzáférést és a multi-factor authentication használatának kártya tulajdonosát adatok környezet (CDE) távoli hozzáférést.

> [!NOTE]
> Többtényezős hitelesítés szükséges, hogy legalább két (követelmény 8.2 további hitelesítési módszerek leírása) három hitelesítési módszer használható a hitelesítéshez. Egy tényező kétszer (például két külön jelszóval) segítségével a multi-factor authentication nem számít.


**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure rendszergazdák szükség a multi-factor authentication karbantartási és Azure rendszerekbe és a kiszolgálók felügyeleti végrehajtásakor elérésére használhat. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló üzembe helyezése során hoz létre három fiók: rendszergazda, sqladmin, és edna (az alapértelmezett felhasználó bejelentkezik a web app, bemutató végrehajtása során). A multi-factor authentication a bemutató nincs implementálva.|



### <a name="pci-dss-requirement-831"></a>PCI DSS követelmény 8.3.1

**8.3.1** beépítése a többtényezős hitelesítést a konzolon belüli összes hozzáférés a CDE a személyzet rendszergazdai hozzáféréssel.

> [!NOTE]
> Ez a követelmény a legjobb 2018 január 31-ig elteltével követelmény válik.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure rendszergazdák szükség a multi-factor authentication karbantartási és Azure rendszerekbe és a kiszolgálók felügyeleti végrehajtásakor elérésére használhat. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló üzembe helyezése során hoz létre három fiók: rendszergazda, sqladmin, és edna (az alapértelmezett felhasználó bejelentkezik a web app, bemutató végrehajtása során). A multi-factor authentication a bemutató nincs implementálva.|



### <a name="pci-dss-requirement-832"></a>PCI DSS követelmény 8.3.2

**8.3.2** beépítése többtényezős hitelesítés minden távoli hálózati hozzáférési (felhasználó és rendszergazda, és többek között külső hozzáférést támogatási vagy karbantartás) származó az entitás hálózaton kívülről.


**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure rendszergazdák szükség a multi-factor authentication karbantartási és Azure rendszerekbe és a kiszolgálók felügyeleti végrehajtásakor elérésére használhat. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló üzembe helyezése során hoz létre három fiók: rendszergazda, sqladmin, és edna (az alapértelmezett felhasználó bejelentkezik a web app, bemutató végrehajtása során). A multi-factor authentication a bemutató nincs implementálva.|



## <a name="pci-dss-requirement-84"></a>PCI DSS követelmény 8.4

**8.4** dokumentum, és közölje a hitelesítési házirendek és eljárások az összes felhasználóra, beleértve:
- Útmutatás az erős hitelesítő adatok kiválasztásához
- Útmutató a hogyan kell védelemmel felhasználókat a hitelesítő adatok
- Utasításokat nem használja fel a korábban használt jelszavakat
- Jelszavak módosítását, ha a jelszó gyanúja utasításokat sérülhet

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek a következő útmutatás és dokumentálásáért és hitelesítési eljárások és az összes felhasználóra házirendek kommunikáció felelősek.|



## <a name="pci-dss-requirement-85"></a>PCI DSS követelmény 8.5

**8.5** ne használja a csoport megosztott, vagy általános azonosítók, jelszavak, vagy további hitelesítési módszerek az alábbiak szerint:
- Általános felhasználói azonosítók letiltották, vagy eltávolították.
- Megosztott felhasználói azonosítók nem léteznek a rendszer felügyeleti és más kritikus feladatokat látnak.
- Megosztott, általános felhasználói azonosítók nem használhatók bármely rendszerösszetevők felügyeletéhez.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló üzembe helyezése során hoz létre három fiók: rendszergazda, sqladmin, és edna (az alapértelmezett felhasználó bejelentkezik a web app, bemutató végrehajtása során). A multi-factor authentication a bemutató nincs implementálva.|



### <a name="pci-dss-requirement-851"></a>PCI DSS követelmény 8.5.1

**8.5.1** **szolgáltatók csak további követelményei:** szolgáltatók távelérési ügyfelek helyi (például a támogatáshoz POS rendszerek vagy kiszolgálók) kell használnia egy egyedi hitelesítés hitelesítő adatok () például a jelszó/kifejezés) minden ügyfél esetében. 

> [!NOTE]
> Ez a követelmény nem javasolt fér hozzá a saját üzemeltetési környezet megosztott szolgáltatók vonatkozik több felhasználói környezetben a rendszer hol tárolja.

**Feladatok:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure-ügyfelek nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Microsoft Azure-ügyfelek nem alkalmazható.|



## <a name="pci-dss-requirement-86"></a>PCI DSS követelmény 8.6

**8.6** Ha más hitelesítési mechanizmusok (például fizikai vagy logikai biztonsági jogkivonatokat, az intelligens kártyák, tanúsítványok, stb.), ezek a mechanizmusok használatát hozzá kell rendelni az alábbiak szerint:
- Hitelesítési mechanizmusok rendelt egyes fiókkal legyen, és nincsenek megosztva több fiók között.
- Fizikai és/vagy logikai vezérlők csak a kívánt fiók mechanizmus segítségével hozzáférést garantálása érdekében érvényben kell lennie.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló üzembe helyezése során hoz létre három fiók: rendszergazda, sqladmin, és edna (az alapértelmezett felhasználó bejelentkezik a web app, bemutató végrehajtása során). A multi-factor authentication a bemutató nincs implementálva. Minden hozzáférés keresztül felügyelt [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), amely segítségével megakadályozhatja a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos. |



## <a name="pci-dss-requirement-87"></a>PCI DSS követelmény 8.7

**8.7** bármely (beleértve az alkalmazások, a rendszergazdák és más felhasználók hozzáférését) kártya tulajdonosát adatokat tartalmazó adatbázis hozzáférésének korlátozott a következőképpen történik:
- Minden felhasználói hozzáférést, a felhasználói lekérdezések és adatbázisok felhasználói műveletek olyan programozási módszerek keresztül.
- Csak az adatbázis-rendszergazdák is képes közvetlenül eléréséhez, vagy az adatbázis lekérdezése.
- Adatbázis-alkalmazások Alkalmazásazonosítók csak az alkalmazás (és nem az egyes felhasználókra vagy egyéb nem alkalmazása folyamatok) használható.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló összes kártya tulajdonosát adatok védelmét az Azure Key Vault, és a rekordok titkosítási van a központi telepítési dokumentációjában leírt. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



## <a name="pci-dss-requirement-88"></a>PCI DSS követelmény 8.8

**8.8** győződjön meg arról, hogy a biztonsági házirendek és az azonosítási és hitelesítési műveleti eljárásokká dokumentálva, valamint használatban van, és ismert, hogy az összes érintett fél.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelek felelőssége, hogy a biztonsági házirendek és az azonosítási és hitelesítési műveleti eljárásokká dokumentálva, valamint használatban van, és ismert, hogy az összes érintett fél biztosítja.|




