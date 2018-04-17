---
title: Az Azure támogatási feldolgozási tervezetének - jelszavakra vonatkozó követelmények
description: PCI DSS követelmény 2
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 0df24870-6156-4415-a608-dd385b6ae807
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 8fafc277d7410ab3f8f54b0defee127fd6ec2f4d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="password-requirements-for-pci-dss-compliant-environments"></a>PCI DSS-kompatibilis környezetben jelszavakra vonatkozó követelmények 
## <a name="pci-dss-requirement-2"></a>PCI DSS követelmény 2

**Ne használjon szállító által biztosított alapértelmezett rendszer jelszavakat és egyéb biztonsági paraméterek**

> [!NOTE]
> Ezeket a követelményeket határozzák meg a [Payment Card Industry (PCI) biztonsági szabványok Tanács](https://www.pcisecuritystandards.org/pci_security/) részeként a [PCI adatok biztonsági szabvány (DSS) 3.2-es verziójú](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Tekintse meg a PCI DSS tesztelési eljárások és az egyes követelményekhez útmutatót olvashat.

Rosszindulatú támadók (külső és belső egy entitás) gyakran használnak a gyártó alapértelmezett jelszavakat és egyéb gyártó alapértelmezett beállításai rendszerek biztonsági sérüléséhez. A jelszavak és a beállítások támadók Közösségek által jól ismert, és könnyen nyilvános információk segítségével határozza meg.

## <a name="pci-dss-requirement-21"></a>PCI DSS követelmény 2.1
 
**2.1** mindig szállító által biztosított alapértelmezett értékeinek módosítása és eltávolítása, vagy tiltsa le a szükségtelen alapértelmezett fiókok **előtt** rendszerek telepítése a hálózaton.
Ez vonatkozik az összes alapértelmezett jelszavak, beleértve többek között operációs rendszerek által használt biztonsági szolgáltatások, az alkalmazás- és fiókok, a pénztári (POS) terminálok, a Simple Network Management Protocol (SNMP) közösségi szoftverekhez karakterláncok stb.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure Active Directory-jelszó házirend követelményeinek az új jelszót a AADUX portálon találja meg az ügyfelek által biztosított lépnek érvénybe. Ügyfél által kezdeményezett önkiszolgáló jelszó-változtatásának előzetes jelszó érvényesítést. Rendszergazda jelszavak alaphelyzetbe állítása is későbbi bejelentkezés után módosítható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló megköveteli a felhasználóktól set erős jelszavakat használjon, az összes felhasználó számára. A bemutató nem minta vagy Vendég fiók engedélyezett.<br /><br />Vezeték nélküli és SNMP nem valósult meg a megoldást.|



### <a name="pci-dss-requirement-211"></a>PCI DSS követelmény 2.1.1

**2.1.1** a kártya tulajdonosát adatok környezet vagy a kártya tulajdonosát adatokat továbbít az csatlakoztatott vezeték nélküli környezetben módosítása az összes vezeték nélküli szállító alapértelmezett beállításokat, beleértve többek között a alapértelmezett vezeték nélküli titkosítási kulcsokhoz, jelszavak, a telepítés és SNMP közösségi karakterlánc.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Vezeték nélküli és SNMP nem valósult meg a megoldást.|



## <a name="pci-dss-requirement-22"></a>2.2 PCI DSS követelmény

**2.2** konfigurációs szabványokat, a rendszer minden összetevőjét. Biztosítsa, hogy ezeknek a szabványoknak minden ismert biztonsági réseit, és megegyeznek a iparágban elfogadott rendszer-megerősítési szabványok.
Iparágban elfogadott rendszer-megerősítési szabványok információforrásokat is, azonban nem csak:
- Internetes biztonsági (CIS) Center
- Nemzetközi szabványügyi (ISO)
- SysAdmin naplózási hálózati biztonsági (SAN) intézet
- Nemzeti szabványügyi Technology (NIST)

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure-ban a OSSC műszaki biztonsági csapatnak házon belül fejlesztett alkalmazásokra biztonsági konfigurációs szabványok konzisztensek iparágban elfogadott szabványok korlátozására, a Microsoft Azure környezetben rendszerek számára. Ezeket a beállításokat a rendszer alaptervek szerepelnek és megfelelő konfigurációs módosítások közlik érintett csoportok (pl. IPAK csoport). Eljárások a biztonsági konfigurációs szabványok alapján a megfelelőség figyeléséhez valósíthatók meg. A biztonsági konfigurációs követelmények a Microsoft Azure környezetben rendszerek konzisztens iparágban elfogadott korlátozási szabványok és legalább évente felülvizsgálata. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló biztosít az összes olyan szolgáltatás hatókörében a kártya tulajdonosát adatok környezet (CDE) vezethet. <br /><br />A Contoso webes tároló is telepíti a [az Azure Security Center](https://azure.microsoft.com/services/security-center/), amely az összes Azure-erőforrások biztonsági állapotát központosított áttekintést nyújt. Egy pillanat alatt ellenőrizze, hogy a megfelelő biztonsági vezérlőket teljesül, és megfelelően konfigurálva, és gyorsan azonosíthatja a figyelmet igénylő erőforrásokat.<br /><br />A Contoso webes tároló Operations Management Suite minden rendszer naplózni használja. A Naplóelemzési változások kiterjedt naplózás biztosít. Módosítások tekintse át, és pontossága ellenőrizni. Részletesebb útmutatásért lásd: [PCI útmutatást - az Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-221"></a>PCI DSS követelmény 2.2.1-en

**2.2.1-en** kiszolgálónként függvények, amelyek ugyanazon a kiszolgálón közös meglévő különböző biztonsági szintjeit követelik meg megelőzése érdekében csak egy elsődleges függvény végrehajtása. (Például webkiszolgálókon, adatbázis-kiszolgálók és DNS-kell végrehajtani a különálló kiszolgálókra.) 

> [!NOTE]
> Virtualizálási technológiák használatban vannak, ahol valósítja meg a virtuális rendszer Összetevőnként csak egy elsődleges funkciója.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A webes tároló contoso szolgáltatások PaaS szolgáltatásként vannak telepítve. Minden szolgáltatás el választva egymástól, és szegmentált hálózati szegmentálást használni.<br /><br />A Contoso webes tároló is használ egy [App Service-környezet (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) kulcs eljárások érvényesítését. További információkért lásd: [PCI útmutatást - App Service Environment-környezet](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-222"></a>PCI DSS követelmény 2.2.2.

**2.2.2** engedélyezése csak a szükséges szolgáltatások, protokollok, démonok, stb., pedig ez szükséges lenne a rendszer funkciója.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure hardver- és konfigurációk azonosítása, és kiszűri a szükségtelen funkciók, a portok, a protokollok és a szolgáltatások legalább negyedévente felülvizsgálata. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A webes tároló contoso szolgáltatások PaaS szolgáltatásként vannak telepítve. Minden szolgáltatás el választva egymástól, és szegmentált hálózati szegmentálást használni.<br /><br />A Contoso webes tároló is használ egy [App Service-környezet (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) kulcs eljárások érvényesítését. További információkért lásd: [PCI útmutatást - App Service Environment-környezet](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-223"></a>PCI DSS követelmény 2.2.3

**2.2.3** valósít meg olyan szükséges szolgáltatás, protokollok, és nem biztonságosnak tekintett démonok további biztonsági funkciókat. 

> [!NOTE]
> Korai SSL/TLS használatos, ahol a követelmények az A2 el kell végezni.


**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A webes tároló contoso szolgáltatások PaaS szolgáltatásként vannak telepítve. Minden szolgáltatás el választva egymástól, és szegmentált hálózati szegmentálást használni. A központi telepítés is biztosít a CDE hatókörében minden szolgáltatás vezethet. <br /><br />A Contoso webes tároló is telepíti a [az Azure Security Center](https://azure.microsoft.com/services/security-center/), amely az összes Azure-erőforrások biztonsági állapotát központosított áttekintést nyújt. Egy pillanat alatt ellenőrizze, hogy a megfelelő biztonsági vezérlőket teljesül, és megfelelően konfigurálva, és gyorsan azonosíthatja a figyelmet igénylő erőforrásokat.<br /><br />A Contoso webes tároló is használ egy [App Service-környezet (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) kulcs eljárások érvényesítését. További információkért lásd: [PCI útmutatást - App Service Environment-környezet](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-224"></a>PCI DSS követelmény 2.2.4

**2.2.4** konfigurálhatja a rendszer biztonsági paramétereket való visszaélés megakadályozására.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure biztosítja csak az arra jogosult személyek konfigurálhatja az Azure platform biztonsági vezérlőket, többtényezős hozzáférés-vezérlést és dokumentált üzleti igények használatával. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló használja az aad-ben, és kezelheti a biztonsági paraméterek AD RBAC megfelelően vannak-e telepítve. További információkért lásd: [PCI útmutatást - Identity Management](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-225"></a>PCI DSS követelmény 2.2.5

**2.2.5** távolítsa el az összes szükségtelen funkciókat, például parancsfájlokat, illesztőprogramok, szolgáltatások, alrendszereket, fájlrendszerek és szükségtelen webkiszolgálók.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló dokumentációja nyújt hogyan határok jönnek létre. Contoso-fenyegetések modellezése és adatfolyam-diagram szemlélteti a használt szolgáltatások és a vezérlőket engedélyezve van.|



## <a name="pci-dss-requirement-23"></a>2.3 a PCI DSS követelmény

**2.3** titkosítása minden konzolon belüli rendszergazdai hozzáférést az erős titkosítás használata. 

> [!NOTE]
> Korai SSL/TLS használatos, ahol a követelmények az A2 el kell végezni.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure gondoskodik arról, hogy az erős titkosítás használata az elérésekor a hipervizor-infrastruktúra. A Microsoft Azure is biztosítja, hogy az ügyfelek a Microsoft Azure felügyeleti portál használatával érhessék el az erős titkosítás a szolgáltatás/IaaS konzolok. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló bemutatja, hogyan erős jelszavak megvalósítható megoldás; Emellett az összes teszt ellenőrzése, hogy a titkosítás meg van valósítva a megoldás teljes hajtható végre.<br /><br />A Contoso webes tároló is használ egy [App Service-környezet (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) kulcs eljárások érvényesítését. További információkért lásd: [PCI útmutatást - App Service Environment-környezet](payment-processing-blueprint.md#app-service-environment).|



## <a name="pci-dss-requirement-24"></a>2.4 a PCI DSS követelmény

**2.4** készletnyilvántartás vezetése a rendszer összetevőit a PCI DSS hatókörébe.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló bemutató PaaS megoldás készlet a megadott dokumentáció tekinthető meg. További információkért lásd: [PCI útmutatást - alkalmazáshiba megoldások](payment-processing-blueprint.md#management-solutions).|



## <a name="pci-dss-requirement-25"></a>PCI DSS követelmény 2,5

**2.5** győződjön meg arról, hogy a biztonsági házirendek és a gyártó alapértelmezett és egyéb biztonsági paraméterek kezelésére szolgáló műveleti eljárásokká dokumentálva, valamint használatban van, és ismert, hogy az összes érintett fél.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló tartalmazza azt a dokumentációt, amely a biztonsági paraméterek betekintést biztosít, illetve a dokumentumok elemek. |



## <a name="pci-dss-requirement-26"></a>2.6 a PCI DSS követelmény

**2.6** megosztott szolgáltatók kell adatvédelemben minden entitás üzemeltetett környezetben és a kártya tulajdonosát. Ezek a szolgáltatók meg kell felelnie a konkrét követelmények *függelék: megfigyelendő további PCI DSS követelményei megosztott üzemeltetési szolgáltatók.*

**Feladatok:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. Microsoft Azure a nem megosztott szolgáltatóhoz. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Nem alkalmazható. Microsoft Azure a nem megosztott szolgáltatóhoz.|




