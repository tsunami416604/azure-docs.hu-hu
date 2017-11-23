---
title: "PaaS üzemelő példányok biztonságához |} Microsoft Docs"
description: " Ismerje meg, és egyéb biztonsági PaaS előnyeit felhőalapú szolgáltatási modellt, és ismerje meg, biztonságossá tétele az Azure PaaS központi telepítésének ajánlott eljárásai. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: techlake
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 4629e0ab6bbc9554128a923e92b269df79446b18
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="securing-paas-deployments"></a>PaaS üzemelő példányok biztonságossá tétele

A cikkben információk segítségével:

- A biztonsági előnyeit a felhőben üzemeltetéséhez ismertetése
- A szolgáltatás (PaaS) vagy egyéb felhőalapú szolgáltatási modellt használja inkább a platform biztonsági előnyeit
- Módosítsa a biztonsági fókusz egy hálózati-központú értékről egy identitás-központú szegélyhálózati biztonsági módszert
- Általános PaaS biztonság ajánlott eljárásait megvalósítása

## <a name="cloud-security-advantages"></a>Felhőalapú biztonsági előnyeit
Biztonsági előnyök, ha a felhőben folyamatban van. A helyszíni környezetben, valószínűleg szervezeteknél unmet felelősségi és korlátozott forrásanyag is elérhető, védelem, amely létrehoz egy környezetében, ahol a támadók képes biztonsági réseket, minden réteg beruházásának.

![Felhő éra biztonsági előnyeit][1]

A szervezetek képesek javítása a fenyegetések észlelése és a válaszidők egy szolgáltató felhőalapú biztonsági képességei és felhő eszközintelligencia.  És a Felelősségek túl, a szervezetek több biztonsági érvényességének, amely lehetővé teszi őket foglalja le újból a biztonsági erőforrásokat és más üzleti prioritások költségvetési kérheti le.

## <a name="division-of-responsibility"></a>Felelőssége
Fontos tudni, az osztálynak a felelős, és a Microsoft között. A helyszínen, a teljes verem saját, de a felhőbe mozgatásakor látnia néhány feladatkört átvitele Microsoft. A következő feladata mátrix jeleníti meg a veremben egy SaaS, a PaaS és az infrastruktúra-szolgáltatási telepítés való telepítésért felelős, és a Microsoft felelős.

![Felelős zónák][2]

Összes felhő központi telepítési típusok akkor saját adatait és identitások. Az adatok és identitások, a helyi erőforrások és a felhőalapú összetevőinek az védelme felelőssége vezérelt (ami függ a szolgáltatás típusa).

Feladatok, amelyek mindig maradnak, függetlenül attól, milyen típusú központi telepítését, akkor a következők:

- Adatok
- Végpontok
- Fiók
- Hozzáférés-kezelés

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>A felhő modell egy PaaS biztonsági előnyeit
Az azonos felelősségi mátrix használ, az Azure PaaS központi telepítése a helyi és a biztonsági előnyeit nézzük.

![A PaaS biztonsági előnyeit][3]

A veremben, a fizikai infrastruktúra alján indítása Microsoft csökkenti közös kockázatok és felelősségek meghatározása. A Microsoft cloud folyamatosan figyelni a Microsoft által, mert már rögzített támadásoknak. Egy támadó végzi el a Microsoft cloud célként nem értelmezhető. Kivéve, ha a támadó rendelkezik pénzt és erőforrásokat, a támadó a nagyszámú valószínűleg áthelyezése egy másik cél.  

A verem közepén nincs egy PaaS üzembe helyezése és a helyszíni közötti különbség. Az alkalmazási rétegre és a fiók és a hozzáférés felügyeleti réteg hogy hasonló kockázatokat. A következő lépéseket területen ez a cikk azt ismerteti ajánlott eljárásai elkerüléséhez, vagy a kockázatok minimalizálja a.

A verem, adatszabályozást, és a rights management tetején, igénybe egy kockázata, hogy a kulcskezelő enyhíthetők. (Ajánlott eljárások a kulcskezelési vonatkozik.) Kulcskezelés feladata egy további, amíg a PaaS központi telepítéséhez, kezeléséhez, erőforrások átadhatja a kulcskezelési már nincs a területek rendelkezik.

Az Azure platformon is nyújt erős DDoS-védelem különböző hálózati technológiák használatával. Azonban minden típusú DDoS védelmi módszert hálózatalapú korlátozottak csatlakozásonként és adatközpont alapon. A nagy DDoS-támadások káros hatása elkerülése érdekében, kihasználhatja az Azure core felhő használatát, amely lehetővé teszi, hogy gyorsan és automatikusan horizontálisan DDoS-támadások elleni védelemre. Részletesen ismertetjük a hogyan ehhez az ajánlott eljárások cikkekben.

## <a name="modernizing-the-defenders-mindset"></a>A defender alaposan korszerűsítésénél
A PaaS központi telepítések tudomást az átfogó megközelítés eltolódása biztonsági. Ekkor vált, nem szükséges megszabott mindent saját kezűleg felelősségi megosztása a Microsofttal.

Egy másik a jelentős különbség PaaS és a hagyományos helyszínen telepítések esetén, az elsődleges biztonsági szegélyhálózati határozza meg, milyen új nézetét. Hagyományosan a helyszíni elsődleges biztonsági szegélyhálózati volt a hálózat, és a legtöbb helyszíni biztonsági tervek használja a hálózati az elsődleges biztonsági pivot. A PaaS üzemelő példányok meg vannak a legjobban kiszolgálni kell lennie az elsődleges biztonsági szegélyhálózati identitás figyelembe véve.

## <a name="identity-as-the-primary-security-perimeter"></a>Azonosító adatai különböznek a elsődleges biztonsági szegélyhálózat
A felhőalapú számítástechnika a széleskörű hálózati hozzáférés, így kevesebb releváns hálózati-központú végezni öt alapvető jellemzőt egyikét. Az adatdeduplikálás nagy részét a felhőalapú megoldások lehetővé teszi a felhasználók hozzáférhessenek az erőforrások helyétől függetlenül. A legtöbb felhasználó igényeit a helyre lesz valahol az interneten.

Az alábbi ábra bemutatja, hogyan a biztonsági szegélyhálózati már elért egy hálózati szegélyhálózati a egy identitás szegélyhálózati. Biztonsági válik, gondoskodnia kell a hálózati kapcsolatban, és további tájékozódhat az adatok védelme, valamint a biztonság az alkalmazások és felhasználók kezelése. A fő különbség, hogy szeretné-e biztonsági közelebb leküldése fontos, hogy a vállalata milyen.

![Azonosító adatai különböznek új biztonsági szegélyhálózat][4]

Kezdetben Azure PaaS-szolgáltatások (például a webes szerepkörök és az Azure SQL) megadott kevéssé vagy egyáltalán ne hagyományos hálózati szegélyhálózati védelmet. Az értelmezése, hogy az elem célja volt ki vannak téve az interneten (webes szerepkör), és adott hitelesítési biztosítja az új szegélyhálózat (például a BLOB vagy az Azure SQL).

Modern biztonsági eljárások azt feltételezik, hogy az ellenfél lett szegve az a hálózati konfigurációtól. Ezért modern védelmi eljárások identitás helyezte át. A szervezetek létre kell hoznia egy biztonsági azonosító-alapú szegélyhálózati az erős hitelesítési és engedélyezési higiéniai (ajánlott eljárások).

## <a name="recommendations-for-managing-the-identity-perimeter"></a>Javaslatok kezelése az identity szegélyhálózat

Alapelvek és a hálózati szegélyhálózati mintái hozzáférhetők évtizedeken. Ezzel szemben az iparági van használva identity az elsődleges biztonsági szegélyhálózati viszonylag kevesebb szolgáltatást. Az említett, azt kell halmozott elegendő élmény biztosításához néhány általános javaslatokat a mezőben bizonyítottan, és szinte minden PaaS szolgáltatás alkalmazása.

Az alábbiakban összefoglalást láthat egy általános ajánlott eljárásokon alapuló megközelítését való kezelése az identity szegélyhálózati.

- **Ne veszítse el a kulcsok vagy a hitelesítő adatok** kulcsok és hitelesítő adatok védelme érdekében elengedhetetlen a PaaS üzemelő példányok biztonságos. Kulcsok és a hitelesítő adatok elvesztése gyakori probléma. Egy jó megoldás, hogy egy központi megoldást használni, ahol kulcsok és titkos tárolhatja hardveres biztonsági modulokkal (HSM). Azure kínál a felhőben a hardveres biztonsági MODULT [Azure Key Vault](../key-vault/key-vault-whatis.md).
- **Hitelesítő adatok és más titkos adatokat nem kerüljenek, a forráskód vagy GitHub** egyedül, mint a kulcsok és a hitelesítő adatok elvesztése hogy a jogosulatlan személyek rosszabb érni őket. A támadók képesek előnyeit botot technológiák kulcsok és titkos tárolt adattárakban, például a Githubon található. Nem be kulcs és a titkos kulcsok ezek nyilvános forráskódú adattárakban.
- **A virtuális gép felületek hibrid PaaS és IaaS-szolgáltatásoknak védelme** IaaS és PaaS szolgáltatások futó virtuális gépek (VM). Attól függően, hogy a szolgáltatás típusának, több felügyeleti felület érhetők el a távoli felügyelt virtuális gépeken közvetlenül engedélyezése. Például a távoli felügyeleti protokollokat [Secure Shell-protokoll (SSH)](https://en.wikipedia.org/wiki/Secure_Shell), [Remote Desktop Protocol (RDP)](https://support.microsoft.com/kb/186607), és [távoli PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) is használható. Általában javasoljuk, hogy nem engedélyezi a távoli közvetlen hozzáférést a virtuális gépeknek az internetről. Ha elérhető, más megközelítést választani, például az Azure virtuális hálózat virtuális magánhálózat használatával kell használnia. Ha alternatív módszerek nem érhetők el, akkor győződjön meg arról, hogy használja-e összetett jelszavak, és ha elérhető, kéttényezős hitelesítést (például [Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)).
- **Használjon erős hitelesítési és engedélyezési platformok**

  - Összevont identitások kialakítása használata helyett egyéni felhasználói tárolja az Azure AD-ben. Ha összevont identitások kialakítása, a platform-alapú megközelítés előnyeit, és delegálja a hitelesített identitások kezelését a partnerek számára. Egy összevont identitáskezelési megközelítés esetén különösen fontos az olyan forgatókönyvek alkalmazottak leáll, és adatokat kell több identitáskezelési és engedélyezési rendszer keresztül tükrözi.
  - A megadott hitelesítési és engedélyezési mechanizmusok helyett egyéni kód használata platform. A hiba oka, hogy az egyéni hitelesítési kód fejlesztése lehet nagyon eséllyel fordulnak elő hiba. A fejlesztők nincsenek biztonsági szakértők és nem valószínű, hogy tisztában legyen a subtleties, valamint a legújabb fejlemények hitelesítési és engedélyezési. Kereskedelmi kód (például a Microsoft): gyakran széles körben biztonsági vizsgálni.
  - Többtényezős hitelesítés használata. A multi-factor authentication az aktuális szabvány a hitelesítéshez és engedélyezéshez, mivel ezzel elkerülheti a biztonsági szerves része a felhasználónév és jelszó típusú hitelesítés gyengeségei miatt. Mindkét az Azure (portal/távoli PowerShell) felületek és az ügyfelek által használt szolgáltatások elérésére kell megtervezni és használatára konfigurált [Azure multi-factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md).
  - Szabványon alapuló hitelesítési protokollok, például OAuth2 és a Kerberos használatára. Ezeket a protokollokat nagymértékben társ áttekintette és valószínűleg valósíthatók meg a platform-könyvtárakban hitelesítési és engedélyezési részeként.

## <a name="next-steps"></a>Következő lépések
Ez a cikk azt arra irányul, az Azure PaaS üzembe helyezésének biztonsági előnyeit. Ismerje meg a következő, a PaaS webes és mobil megoldások biztonságossá tételének ajánlott eljárásai. Először az Azure App Service, Azure SQL Database és Azure SQL Data Warehouse foglalkozunk. Amint elérhetővé válnak más Azure-szolgáltatásokhoz ajánlott gyakorlati tanácsok a cikkeket, hivatkozások lesznek közzétéve, az alábbi listában:

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Az Azure SQL Database és az Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- Azure Storage
- Azure REDIS gyorsítótár
- Azure Service Bus
- Webalkalmazási tűzfalak

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
