---
title: PaaS üzemelő példányainak biztonságossá tétele |} A Microsoft Docs
description: " Ismerje meg a biztonsági előnyöket, paas és más felhőalapú szolgáltatás, és biztonságossá tétele az Azure PaaS üzemelő példány vonatkozó ajánlott eljárások megismerése. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: techlake
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: da5d59aaaea8e6186609eb5f3419fba5e67d4279
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "42059146"
---
# <a name="securing-paas-deployments"></a>PaaS üzemelő példányainak biztonságossá tétele

Ez a cikk, amely segítséget nyújt információkat:

- Megismerheti az alkalmazások felhőbeli üzemeltetéséhez biztonsági előnyeit
- A platform előnyeinek értékelődik ki a platformszolgáltatás (PaaS) és más cloud service-modellek
- Módosítja a biztonsági fókusz egy hálózati-központú egy identitás-központú szegélyhálózat-alapú biztonsági módszer
- Általános PaaS biztonsági ajánlott eljárások javaslatok megvalósítása

## <a name="cloud-security-advantages"></a>Felhőbeli biztonsági előnyeit
Nincsenek biztonsági előnyök, hogy a felhőben. A helyszíni környezetben, valószínűleg a szervezet rendelkezik a teljesítetlen feladatokról, valamint korlátozott erőforrások biztonsági, amely létrehoz egy környezetet, ahol a támadók képesek minden rétegen biztonsági réseit támogatásán érhető el.

![Felhő korszakának biztonsági előnyeit][1]

Olyan vállalatok, amelyek egy szolgáltató felhőalapú biztonsági képességeket és a felhőbeli intelligens technológiák használatával növelhető a fenyegetések észlelése és a válaszidőket.  A felhőszolgáltató feladatkörök kapacitásértékek szervezetek további biztonsági lefedettség, amely lehetővé teszi, hogy foglalja le újból a biztonsággal kapcsolatos információforrások és más üzleti prioritásokhoz költségvetés kérheti le.

## <a name="division-of-responsibility"></a>A felelősségi körzet
Fontos tudni, hogy az osztálynak a között, és a Microsoft felelőssége. A helyszínen, a teljes verem a saját, de a felhőre váltáskor látnia néhány feladatkört átvitele a Microsoft. A következő feladata mátrix jeleníti meg a verem környezetben SaaS, PaaS és IaaS, amelynek Ön a felelős, és a Microsoft felelős.

![Felelősség zónák][2]

Az összes felhőbeli központi telepítési típusok meg saját adatait és identitások. Ön felelős az adatait és identitások, a helyszíni erőforrások és a felhő összetevők védelme szabályozni (amely a szolgáltatás típusa szerint változó).

Feladatai, amelyek mindig maradnak, függetlenül a típusú telepítés, akkor a következők:

- Adatok
- Végpontok
- Fiók
- Hozzáférés-kezelés

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Egy a PaaS előnyei biztonsági cloud service-modell
Ugyanaz a felelősségi mátrix használja, tekintsük át azokat egy Azure PaaS üzemelő példány és a helyszíni biztonsági előnyeit.

![A PaaS biztonsági előnyei][3]

A veremben, a fizikai infrastruktúra alján indítása Microsoft csökkenti gyakori kockázatok és feladatokról. Mivel a Microsoft folyamatosan figyeli a Microsoft cloud, meglehetősen nehéz a támadásokkal szemben. A támadó szerezni a cél a Microsoft cloud nincs értelme. Kivéve, ha a támadó rendelkezik a nagy mennyiségű költséget takaríthat meg és erőforrásokat, a támadó valószínűleg áthelyezése egy másik cél.  

A verem közepén nem helyszíni és a egy PaaS üzemelő példány között nincs különbség. Az alkalmazási rétegre és a fiók és a hozzáférési réteg hogy hasonló kockázatokat. Következő lépések szakaszban Ez a cikk végigvezeti az ajánlott eljárások a elkerüléséhez, vagy ez a kockázat minimalizálása.

A stack, az adatszabályozást, és a rights management tetején, végrehajtása a egy kockázata, hogy a kulcskezelő enyhíthetők. (Ajánlott eljárások a kulcskezelés vonatkozik.) Egy további felelősségére, amíg egy PaaS üzemelő példány már nem kell erőforrásokat is át a kulcskezelés kezelheti a területek rendelkezik.

Az Azure platform is biztosít erős a DDoS protection különféle a hálózatalapú technológiák használatával. Mindenfajta hálózati alapú DDoS elleni védelmi módszerek azonban azok korlátokkal rendelkeznek a hivatkozás és adatközpont alapján. Nagy DDoS-támadások hatásainak elkerülése érdekében, kihasználhatja az Azure core felhőalapú méretezhetőségi, lehetővé téve a gyors és automatikus horizontális felskálázási DDoS-támadásokkal szembeni védelmét. További részletek a hogyan ezt megteheti az ajánlott eljárásokat cikkekben olvashatja.

## <a name="modernizing-the-defenders-mindset"></a>A defender így modernizálhatja
A paas üzemelő példányok az általános megközelítés a shift jár biztonsági. Át a vezérlőelem minden saját kezűleg felelőssége megosztása a Microsofttal kellene.

Egy másik jelentős PaaS és a hagyományos helyszíni üzemelő példányok közötti különbség egy új nézetet, mi határozza meg az elsődleges biztonsági határt. Hagyományosan az elsődleges helyszíni biztonsági határ a hálózat és a legtöbb a helyszíni biztonsági tervek az elsődleges biztonsági pivot használja a hálózati. PaaS üzemelő példányok akkor jobban által kiszolgált identitás, az elsődleges biztonsági határt is figyelembe véve.

## <a name="identity-as-the-primary-security-perimeter"></a>Identitása, mint az elsődleges biztonsági határ
A felhő-számítástechnika a széleskörű hálózati hozzáférés, ami lehetővé teszi a hálózat-központú felhőmegoldásokat kevésbé jelentős öt alapvető jellemzőt egyikét. A cél az a felhő-számítástechnika lehetővé teszi a felhasználók hozzáférhessenek az erőforrásokhoz, helytől függetlenül jelentős részét. A felhasználók többsége számára azok helyétől fog kell valahol az interneten.

Az alábbi ábra bemutatja, hogyan a biztonsági határt alakult az a hálózat pereme, az identitás szegélyhálózat-alapú. Biztonsági kapcsolatban nyújt a hálózat védelmébe, és további információk az adatok védelme, valamint a biztonság, az alkalmazások és felhasználók kezelése válik. A legfontosabb különbség, hogy szeretné-e biztonsági közelebb leküldése milyen fontos, hogy a vállalat.

![Új biztonsági határt azonosító][4]

Kezdetben az Azure PaaS-szolgáltatások (például a webes szerepkörök és az Azure SQL) megadott alig vagy egyáltalán nem hagyományos hálózati szegélyhálózat-alapú védelem. Volt egyetértés, hogy az elem célja volt az internethez (webes szerepkörből) kell tenni, és ennek a hitelesítésnek biztosít az új szegélyhálózat-alapú (például BLOB vagy Azure SQL).

A modern biztonsági eljárások azt feltételezik, hogy a támadó áthatolt a hálózat szegélyén. Ezért a modern defense eljárások identitás helyezte át. Szervezetek számára, létre kell hoznia egy biztonsági azonosító-alapú szegélyhálózati az erős hitelesítési és engedélyezési higiéniai (ajánlott).

## <a name="recommendations-for-managing-the-identity-perimeter"></a>Javaslatok kezelése az identity szegélyhálózat

Alapelvek és a hálózat szegélyén mintákat volt elérhető évtizedes. Ezzel szemben az iparágaknak is megvannak a viszonylag kevesebb az identitást használja, mint az elsődleges biztonsági határt felhasználói. Az adott mondta hogy rendelkezik halmozott elegendő élményt biztosít, amely a mezőben bizonyítottan és a alkalmazni kell szinte az összes PaaS-szolgáltatások általános ajánlásokat.

A következő egy általános ajánlott eljárásokon alapuló megközelítését való kezelése az identity peremhálózati foglalja össze.

- **Ne veszítse el a kulcsokat, vagy a hitelesítő adatok** PaaS üzemelő példányainak biztonságossá létfontosságú kulcsok és hitelesítő adatok védelme. Kulcsok és hitelesítő adatok elvesztése gyakori probléma. Egy jó megoldás, ha egy központi megoldást használni, ahol kulcsok és titkos kulcsok tárolhatók hardveres biztonsági modulokban (HSM). Az Azure biztosít a felhőben a hardveres biztonsági modul [Azure Key Vault](../key-vault/key-vault-whatis.md).
- **Ne helyezzen hitelesítő adatait, valamint egyéb titkok forráskód vagy GitHub** csak rosszabb, mint a kulcsok és hitelesítő adatok elvesztése kellene a jogosulatlan személyek érni őket. A támadók képesek kihasználásához bot technológiák kulcsok és titkok forráskódtárakból, például a GitHub tárolt található. Ne tegye kulcs és titkos kulcsok az alábbi nyilvános forráskódú adattárakban.
- **A virtuális gép felügyeleti felületek hibrid PaaS és IaaS-szolgáltatások védelme** IaaS és PaaS-szolgáltatások futtatása virtuális gépeken (VM). Szolgáltatás típusától függően számos felügyeleti felületek vehetők igénybe a távoli felügyelt ezek a virtuális gépek közvetlenül engedélyezése. Távoli felügyeleti protokollok, mint például [Secure Shell Protocol (SSH)](https://en.wikipedia.org/wiki/Secure_Shell), [Remote Desktop Protocol (RDP)](https://support.microsoft.com/kb/186607), és [távoli PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) is használható. Általában azt javasoljuk, hogy nem engedélyezi a közvetlen távelérési virtuális géphez az internetről. Ha elérhető, használjon alternatív módszerekkel, például egy Azure-beli virtuális hálózatban a virtuális magánhálózatok használata. Ha alternatív módszerek nem érhetők el, majd győződjön meg arról, hogy összetett jelszavak, és ha elérhető, kétfaktoros hitelesítés (például [Azure multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md)).
- **Használjon erős hitelesítési és engedélyezési platformot**

  - Használja az összevont identitások kialakítása helyett egyéni felhasználói tárolókkal Azure AD-ben. Ha összevont identitások kialakítása használja, a platform-alapú megközelítés előnyeit, és delegálja a partnerek számára engedélyezett azonosítók kezelését. Egy összevont identitáskezelési megközelítés akkor különösen fontos a forgatókönyvek, ha az alkalmazottak megszűnik, és információkat kell több identitáskezelési és engedélyezési rendszer hatással lehet.
  - Használja a platform által kínált hitelesítési és engedélyezési mechanizmusokkal egyéni kód helyett. A hiba oka egyéni kód fejlesztése, hogy a hibalehetőségeket rejt magában. A fejlesztők a legtöbb nem biztonsági szakértők és nem valószínű, hogy az apró és a legújabb fejlemények a hitelesítés és engedélyezés. Kereskedelmi-kódokat (például Microsoft), gyakran alaposan tekintse át biztonsági.
  - Többtényezős hitelesítés használatát. A multi-factor authentication hitelesítés és engedélyezés a jelenlegi standard, mivel ezzel elkerülheti a felhasználónév és jelszó típusú hitelesítés járó biztonsági gyenge. Mindkét a (portal/távoli PowerShell) az Azure felügyeleti felületek és a ügyfelek által használt szolgáltatások elérésére terveztek és konfigurálni kell az használandó [Azure multi-factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md).
  - Szabványon alapuló hitelesítési protokollok, például OAuth2 és a Kerberos használata. Ezeket a protokollokat nagymértékben lett felülvizsgálva társ, és valószínűleg valósíthatók meg a platform-kódtárak a hitelesítéshez és engedélyezéshez részeként.

## <a name="next-steps"></a>További lépések
Ebben a cikkben összpontosítottunk egy Azure PaaS üzemelő példány biztonsági előnyeit. Következő lépésként megtanulhatja, ajánlott eljárások a PaaS webes és mobil megoldások biztonságossá tételéhez. Kezdjük az Azure App Service, Azure SQL Database és Azure SQL Data warehouse-bA. Amint elérhetővé válnak más Azure-szolgáltatások az ajánlott eljárásokat cikkeket, hivatkozások lesznek közzétéve, az alábbi lista:

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Az Azure SQL Database és az Azure SQL Data warehouse-bA](security-paas-applications-using-sql.md)
- [Azure Storage](security-paas-applications-using-storage.md)
- Az Azure REDIS Cache
- Azure Service Bus
- Webalkalmazás-tűzfalak

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
