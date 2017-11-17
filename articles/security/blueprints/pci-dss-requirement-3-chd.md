---
title: "Az Azure támogatási feldolgozási tervezetének - CHD követelmények"
description: "3. követelmény PCI DSS"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9736f7c8-c632-4b86-8b8a-6e4f45c1a7aa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 356599cbe1e4e1948a5ec16d0d504835fa7dcd43
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="chd-requirements-for-pci-dss-compliant-environments"></a>PCI DSS-kompatibilis környezetben CHD követelményei
## <a name="pci-dss-requirement-3"></a>3. követelmény PCI DSS

**Tárolt kártya tulajdonosát adatok védelme**

> [!NOTE]
> Ezeket a követelményeket határozzák meg a [Payment Card Industry (PCI) biztonsági szabványok Tanács](https://www.pcisecuritystandards.org/pci_security/) részeként a [PCI adatok biztonsági szabvány (DSS) 3.2-es verziójú](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Tekintse meg a PCI DSS tesztelési eljárások és az egyes követelményekhez útmutatót olvashat.

Védelmi módszer, például a titkosítás, csonkolása, maszkolási, és a kivonatolás kártya tulajdonosát az adatvédelem kritikus összetevői. Ha egy behatoló megkerüli egyéb biztonsági vezérlőt, és hozzáférést kap azokhoz a titkosított adatok, a megfelelő titkosítási kulcsok nélkül adata nem olvasható, és nem használható az adott személyt. Más hatékony módszer tárolt adatok védelmének esetleges kockázat csökkentése lehetőségek is tekinteni. Minimalizálja a kockázat módszerei például nem tárolni kártya tulajdonosát adatok csak feltétlenül szükséges, csonkolása kártya tulajdonosát adatok teljes PÁSZTÁZÁSHOZ. Ha nincs szükség, és a végfelhasználói üzenetkezelési technológiái, például e-mail, azonnali nem küldő nem védett PANs üzenetküldési.
Tekintse meg a PCI DSS, PA-DSS szószedetben és rövidítések és rövidítések definíciójának "az erős titkosítás" és az egyéb PCI DSS feltételeket.

## <a name="pci-dss-requirement-31"></a>3.1 PCI DSS követelmény

**3.1** megtartása kártya tulajdonosát adattárolás adatok megőrzési és kivezetési házirendek, eljárások és folyamatok, amelyek legalább a következők minden kártya tulajdonosát (CHD) tárolására implementálásával minimális:
- Korlátozza az adatok tárolási mennyiségét és a megőrzési időt, amely pedig szükséges a jogi, szabályozási és üzleti követelmények
- Kártya tulajdonosát adatok vélt megőrzési követelményei
- Ha már nem szükséges adatok a biztonságos törlésre folyamatok
- Negyedéves folyamat azonosítására és a biztonságos törlése, amelynek hossza meghaladja a megadott megőrzési tárolt kártya tulajdonosát adatokat.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Azure felelős az ügyféladatokat, a törlésre kijelölt teljesülnek biztonságosan leszerelt NIST 800-88 biztonságos kivezetési házirendjeit megadott megfelelő protokollok használatával. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló nem törli vagy megsemmisítése bármely tárolt CHD. Azonban az összes adat titkosítva, és nem elsődleges fiók száma (PÁSZTÁZÁS) tárolja.<br /><br />|



## <a name="pci-dss-requirement-32"></a>PCI DSS követelmény 3.2.

**3.2** ne tároljon bizalmas hitelesítési adatok engedélyezést követően (még akkor is, ha titkosított). Ha bizalmas hitelesítési adatok érkezik, képezhető le minden adat helyreállíthatatlan az engedélyezési folyamat befejezése után. 
- Nincs üzleti indoklásának, és 
- A tárolja biztonságos helyen.
Bizalmas hitelesítési adatok adatai, fájljaiban szereplő az alábbi követelmények 3.2.1 3.2.3 keresztül:


**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló nem törölhető és nem minden tárolt CHD; megszüntetése a mintaadatok csak bemutató célokra tárolja. Azonban minden adata titkosított, és nem elsődleges fiók száma (PÁSZTÁZÁS) tárolja.<br /><br />|



### <a name="pci-dss-requirement-321"></a>3.2.1 PCI DSS követelmény

**3.2.1** engedélyezést követően ne tároljon bármely nyomon követése (az egy egylapkás vagy máshol található kártyák, egyenértékű adatok hátoldalán található mágneses paritásos) teljes tartalmát. Ezeket az adatokat is hívják meg a teljes nyomon követése, nyomon követheti, nyomon követheti az 1, 2 és mágneses paritásos adatok követik. 

> [!NOTE]
> Üzleti normális működés során az alábbi adatok elemek a mágneses paritásos megőrzendő lehet szükség: 
> - A kártya tulajdonosát neve 
> - Elsődleges számát (PÁSZTÁZÁS) 
> - Lejárat dátuma 
> - Szolgáltatás kódot 
>
> Minimálisra csökkenthetők a járulékos, csak ezen adatok elemek tárolására üzleti igény szerint.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló nem tárol bármely CHD teljes tartalmát.|



### <a name="pci-dss-requirement-322"></a>3.2.2 PCI DSS követelmény

**3.2.2** ne tárolja a kártya ellenőrzőkódot vagy -értéket (háromjegyű vagy négyjegyű szám elején vagy a fizetési kártya kártya-nincs-e tranzakciók ellenőrzéséhez használt hátsó) engedélyezést követően.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes adatokat, többek között a CVV minták titkosítja.|



### <a name="pci-dss-requirement-323"></a>PCI DSS követelmény 3.2.3.

**3.2.3** ne tárolja a személyes azonosítószámot (PIN-kódot) vagy a titkosított PIN-kód blokk engedélyezést követően.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló a PIN-kód információt nem tárolnak.|



## <a name="pci-dss-requirement-33"></a>3.3 PCI DSS követelmény

**3.3** maszk PÁSZTÁZÁS megjelenésekor (az első hat és utolsó négy számjegye megjelenítendő számjegyek maximális száma), úgy, hogy egy valódi üzleti csak csoporthoz kell látható a teljes eszközökhöz. 

> [!NOTE]
> Ez a követelmény nem ír szigorúbb követelményeinek helyen jeleníti meg a kártya tulajdonosát adatok – például jogi vagy a fizetési kártya márka követelményei POS (POS) visszaigazolások.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló elfedi az elsődleges számát (PÁSZTÁZÁS) átlátható adattitkosítás, mindig titkosított oszlopok és dinamikus adatmaszkolási használatával. További információkért lásd: [PCI útmutatást - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-34"></a>PCI DSS követelmény 3.4

**3.4** leképezési PÁSZTÁZÁS nem olvasható bárhol tárolja (beleértve a digitális hordozható tároló, biztonsági másolat adathordozóját, és a naplók) a következő módszerek egyikével sem:
- Az erős titkosítás alapján készült egyirányú kivonatokat (kivonat a teljes PÁSZTÁZÁS kell lennie)
- Csonkolási (kivonatoláshoz nem használható cserélje le a PÁSZTÁZÁS csonkolt szegmensének)
- Index jogkivonatokat és a kézi (kézi kell biztonságos helyen tárolni)
- Az erős titkosítás tartozó kulcskezelés folyamatok és eljárások. 

> [!NOTE]
> Egy viszonylag trivial elérhető egy rosszindulatú egyedi eredeti PÁSZTÁZÁS adatok helyreállítására férhet hozzá a kialakítása csonkolt és kivonatolt verzióját, ha Ha kivonat készül, és csonkolja a azonos PÁSZTÁZÁS verziói egy entitás környezetben érhető el, további vezérlők a kivonatolt és csonkolt verziója nem feleltethető meg az eredeti eszközökhöz. helyreállítására garantálása érdekében érvényben kell lennie

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes hitelkártya titkosítja és az Azure Key Vault kezelésére használ kulcsok beolvasása a CHD megakadályozza.<br /><br />További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-341"></a>PCI DSS követelmény 3.4.1.

**3.4.1** lemeztitkosítás (ahelyett, hogy az adatbázis-titkosítás fájl - vagy oszlopszintű) használata esetén logikai hozzáférési felügyelni külön-külön és függetlenül operációs rendszere hitelesítési és hozzáférés-vezérlő mechanizmusok (például úgy, hogy nem helyi felhasználói fiók adatbázisok vagy általános hálózati bejelentkezési hitelesítő adatok használatával). Visszafejtési kulcs esetében nem lehet felhasználói fiókokhoz társítva. 

> [!NOTE]
> Ez a követelmény vonatkozik mellett egyéb PCI DSS titkosítás és a kulcs-felügyelettel kapcsolatos követelményeket.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló tárolt összes adat titkosítását, és elkülöníti a forgalmat a DevOps funkciók kiemelt jogok kiterjesztésének megelőzése.<br /><br />Az App Service Environment-környezet védett, és a zárolását, kell adni egy mechanizmus, amellyel lehetővé teszik a DevOps kiadásokban vagy egy webalkalmazást, a Kudu használatával kell megfigyelniük, például szükség lehet a módosításokat.<br /><br />Egy virtuális gépet át nem veszi egy jumpbox (megerősített gazdagépen), a következő beállításokat:<br /><br /><ul><li>[Kártevőirtó-bővítmény](/azure/security/azure-security-antimalware)</li><li>[OMS figyelőbővítmény](/azure/virtual-machines/virtual-machines-windows-extensions-oms)</li><li>[Virtuálisgép-diagnosztika bővítmény](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)</li><li>[A BitLocker titkosított lemez](/azure/security/azure-security-disk-encryption)</li></ul>Az Azure Key Vault használatával igazodik Azure Government, PCI DSS és HIPAA követelményeket.|



## <a name="pci-dss-requirement-35"></a>PCI DSS követelmény 3.5

**3.5** dokumentum és megvalósítása eljárásokat védelmére kulcsok tárolt kártya tulajdonosát adatok közzététele és visszaélés elleni védelmét. 

> [!NOTE]
> Ez a követelmény tárolt kártya tulajdonosát adatok titkosításához használt kulcsok vonatkozik, és vonatkoznak a kulcs titkosított adatok titkosított kulcsok védelmére használt kulcsokat – ilyen kulcs titkosított kulcsok legalább olyan erős, az adatok titkosítása kulcsként kell lennie.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | **A Key vault ügyfelek:**<br /><br />Microsoft Azure biztosítja, hogy ügyfél kulcstárolójának logikailag egymástól különítve, és logikailag különítve a felügyeleti vezérlősík a Key Vault szolgáltatás. Key Vault kialakításának köszönhetően a Microsoft nem rendelkezik az ügyfél kulcstároló folyamatos elérését. <br /><br />Kulcsok vannak védik a Microsoft Azure-ban iparági szabványoknak megfelelő algoritmusok, kulcshosszok és hardveres biztonsági modulokkal (HSM) használatával.<br /><br />A Microsoft Azure Key Vault szolgáltatókban tárolt kulcsok egy másik kulcsának védelmére használhatók. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló dokumentációt bemutatják és telepíthet a bemutató CHD védelméhez védett kulcs megoldást biztosít.|



### <a name="pci-dss-requirement-351"></a>PCI DSS követelmény 3.5.1.

**3.5.1** *szolgáltatók csak további követelményei:* karbantartása, amely tartalmazza a titkosítási architektúra dokumentált leírása:
- Az összes algoritmusok, protokollok és a kártya tulajdonosát adatok védelme, beleértve a kulcs erősségét és a lejárati dátum használt kulcsok részletei
- A kulcshasználat minden kulcs leírása
- Bármilyen hardveres biztonsági modulok és egyéb SCDs kulcskezelés használt készlet 

> [!NOTE]
> Ez a követelmény a legjobb 2018 január 31-ig elteltével követelmény válik.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | **A Key vault ügyfelek:**<br /><br />Microsoft Azure biztosítja, hogy ügyfél kulcstárolójának logikailag egymástól különítve, és logikailag különítve a felügyeleti vezérlősík a Key Vault szolgáltatás. Key Vault kialakításának köszönhetően a Microsoft nem rendelkezik az ügyfél kulcstároló folyamatos elérését. <br /><br />Kulcsok vannak védik a Microsoft Azure-ban iparági szabványoknak megfelelő algoritmusok, kulcshosszok és hardveres biztonsági modulokkal (HSM) használatával.<br /><br />A Microsoft Azure Key Vault szolgáltatókban tárolt kulcsok egy másik kulcsának védelmére használhatók. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló dokumentációt bemutatják és telepíthet a bemutató CHD védelméhez védett kulcs megoldást biztosít.|



### <a name="pci-dss-requirement-352"></a>PCI DSS követelmény 3.5.2

**3.5.2** korlátozza a hozzáférést a jegyektől szükséges legkevesebb kriptográfiai kulcsokat.


**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | **A Key vault ügyfelek:**<br /><br />Key Vault támogatja több szintű hozzáférési házirendeket, amelyek lehetővé teszik a Key Vault tulajdonosa hozzáférést biztosíthat bizonyos funkciók jellemző bejegyzésekre egyedi műveletek végrehajtásához. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A webes tároló contoso kulcskezelés el különítve egy felhasználói fiókhoz (admin ##@contosowebstore.com).|



### <a name="pci-dss-requirement-353"></a>PCI DSS követelmény 3.5.3.

**3.5.3** mindig egy (vagy több), a következő formátumokban kártya tulajdonosát adatok titkosításához/visszafejtéséhez használt titkos és titkos kulcsok tárolására:
- A kulcs titkosított kulcs, amely legalább olyan erős, az adatok titkosítása kulcsként, és az adatok titkosított kulcsból külön tárolt titkosított
- Egy biztonságos kriptográfiai eszköz (például egy (gazda) hardveres biztonsági modul (HSM) vagy egy pont a kommunikáció eszközt PT jóváhagyott) belül
- Legalább két teljes hosszúságú kulcs vagy kulcs megosztások összhangban az iparág - elfogadott módszer 

> [!NOTE]
> Nem szükséges, hogy lehet nyilvános kulcsot tárolni egy formában.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | **A Key vault ügyfelek:**<br /><br />Kulcsok vannak tárolva az ügyfél által azonosított egyedi kulcstárolót.<br /><br />Key Vault hozzáférhet egyszerre és globálisan több alkalmazás, amely csökkenti a másolja a vágólapra a kulcsot, és több helyen tárolja. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes kulcskezelés Azure Key Vault használ. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-354"></a>PCI DSS követelmény 3.5.4

**3.5.4** tárolni a titkosítási kulcsokat a lehető legkevesebb helyeken.


**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | **A Key vault ügyfelek:**<br /><br />Kulcsok vannak tárolva az ügyfél által azonosított egyedi kulcstárolót. <br /><br />Key Vault hozzáférhet egyszerre és globálisan több alkalmazás, amely csökkenti a másolja a vágólapra a kulcsot, és több helyen tárolja. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes kulcskezelés Azure Key Vault használ. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).|



## <a name="pci-dss-requirement-36"></a>PCI DSS követelmény 3.6.

**3.6** teljesen és dokumentálása valósítja meg az összes kulcskezelés folyamatok és eljárások, a kártya tulajdonosát adatok, beleértve a következőket titkosításhoz használt kriptográfiai kulcsokkal. 

> [!NOTE]
> A különböző erőforrások, például az NIST, amely http://csrc.nist.gov helyen találhatók a kulcskezeléshez számos ipari szabványok érhetők el.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes kulcskezelés Azure Key Vault használ. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-361"></a>PCI DSS követelmény 3.6.1.

**3.6.1** erős titkosítási kulcsok generálása

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | **A Key vault ügyfelek:** <br /><br />A Key Vault kulcsok generálásakor Azure felelős az ügyfél vállalatához specifikációi / kulcs létrehozásakor. Kulcsok jönnek létre, a hardveres biztonsági modul használatával. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes kulcskezelés Azure Key Vault használ. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-362"></a>PCI DSS követelmény 3.6.2

**3.6.2** biztonságos kriptográfiai kulcs terjesztése

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | **A Key vault ügyfelek:**<br /><br />A saját kulcs (használatának BYOK) eszközt magában foglalja a felhasználói kulcsot, és támad egy meghatározott biztonsági tárolóban, amely egy adott Azure-előfizetés van kötve. A kulcs csak akkor importálhatók a megadott előfizetés kulcstároló, a megadott régión belül. Ez a folyamat használja a titkosítási eljárásokat a hardver gyártója által biztosított. Ügyfelek, hogy sikeres volt-e az átvitel értesítést kaphat. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes kulcskezelés Azure Key Vault használ. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-363"></a>PCI DSS követelmény 3.6.3

**3.6.3** biztonságos kriptográfiai kulcs tárolása

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | **A Key vault ügyfelek:**<br /><br />Kulcsok a HSM-EK tárolódnak, és használja-e a hardver gyártójának kriptográfiai biztonsági biztosított. A metaadatok kulcsok titkosítására, amely minden kulcstároló egyedi az Azure Storage tárolja. <br /><br /> |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes kulcskezelés Azure Key Vault használ. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-364"></a>PCI DSS követelmény 3.6.4

**3.6.4** kriptográfiai kulcs módosítások kulcsokhoz, amelyek rendelkeznek a cryptoperiod végére ért, (például egy meghatározott idő után, illetve után titkosítási szöveges bizonyos mennyiségű készült egy adott kulcs), a társított által meghatározott módon alkalmazás gyártójának segítségét, vagy a kulcs tulajdonos és a alapján iparági az ajánlott eljárásokról és irányelveket (például NIST különleges közlemény 800-57-es).

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | **A Key vault ügyfelek:**<br /><br />Kulcstároló frissítése, vagy állítsa a kulcsokat, az ügyfél által definiált funkcióját támogatja. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes kulcskezelés Azure Key Vault használ. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-365"></a>PCI DSS követelmény 3.6.5

**3.6.5** kivonása vagy cseréje (például archiválás, megsemmisítése, és/vagy a visszavont tanúsítványok), ha a kulcs integritása rendelkezik lett csökken (például egy alkalmazott tiszta szöveges kulcs ismeretében indító szükségesnek kulcsok az összetevő), vagy kulcsok gyanús feltörésének. 

> [!NOTE]
> Ha kivonják, illetve lecserélik a titkosítási kulcsok kell meg kell őrizni, ezek a kulcsok kell biztonságosan archiválja (például a kulcs-titkosítási kulcs használatával). Archivált titkosítási kulcsok csak használandó visszafejtési/ellenőrzés céljából.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | **A Key vault ügyfelek:**<br /><br />Key Vault kivonása vagy cserélni, az ügyfél által definiált funkcióját támogatja. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes kulcskezelés Azure Key Vault használ. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-366"></a>PCI DSS követelmény 3.6.6

**3.6.6** manuális tiszta szöveges titkosítási kulcs-felügyeleti műveletek használata esetén ezek a műveletek felügyelni vegyes Tudásbázis és kettős-vezérlés használatával. 

> [!NOTE]
> Manuális kulcskezelés művelet tartalmazza, azonban nem csak: kulcs létrehozása, átviteli, betöltését, tárolási és megsemmisítése.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes kulcskezelés Azure Key Vault használ. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-367"></a>PCI DSS követelmény 3.6.7

**3.6.7** jogosulatlan helyettesítő titkosítási kulcsok megelőzéséhez.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | **A Key vault ügyfelek:**<br /><br />Kulcs tárolók logikailag el egymástól, és nem támogatják a kereszt-directory engedélyezési. Emiatt nem engedélyezett helyettesítés miatt meghiúsult. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes kulcskezelés Azure Key Vault használ. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-368"></a>PCI DSS követelmény 3.6.8

**3.6.8** hivatalosan megerősíti, hogy azok tudomásul veszi, és hogy kulcs-gondnoki feladatainak elfogadja a titkosítási kulcs őrzői vonatkozó követelménynek.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A webes tároló contoso kulcskezelés el különítve egy felhasználói fiókhoz (admin ##@contosowebstore.com).|



## <a name="pci-dss-requirement-37"></a>PCI DSS követelmény 3.7.

**3.7** győződjön meg arról, hogy a biztonsági házirendek és üzemeltetési eljárások tárolt kártya tulajdonosát az adatok védelmének dokumentálva, valamint használatban van, és ismert, hogy az összes érintett fél.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló összes kulcskezelés Azure Key Vault használ. További információkért lásd: [PCI útmutatást - titkosítás](payment-processing-blueprint.md#encryption-and-secrets-management).|




