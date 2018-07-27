---
title: Az Azure Active Directory Connect – gyakori kérdések – |} A Microsoft Docs
description: Ez a cikk az Azure AD Connect kapcsolatos gyakori kérdésekre ad választ.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 1897f2c5d532c030b2711a35eabeefd889f560c8
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264995"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect – gyakori kérdések

## <a name="general-installation"></a>Általános telepítési
**K: telepítési működik, ha az Azure Active Directory (Azure AD) globális rendszergazda a kétfaktoros hitelesítés (2FA) engedélyezve van?**  
Ez a forgatókönyv cikk az 2016. február buildek esetében támogatott.

**K: van mód a felügyelet nélküli Azure AD Connect telepítése?**  
Az Azure AD Connect telepítés csak akkor, amikor a telepítési varázsló használata támogatott. Egy felügyelet nélküli, csendes telepítést nem támogatott.

**K: van olyan erdőben, ahol egy tartomány nem érhető el. Hogyan kell telepíteni az Azure AD Connect?**  
Ez a forgatókönyv cikk az 2016. február buildek esetében támogatott.

**K: az Azure Active Directory Domain Services (Azure AD DS) health-ügynök működnek server core-on?**  
Igen. Miután telepítette az ügynököt, a következő PowerShell-parancsmag segítségével befejezheti a regisztrációs folyamat: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**K: az Azure AD Connect támogatási szinkronizálását a két tartomány az Azure ad-ben?**  
Igen, ezt a forgatókönyvet. Tekintse meg [több tartomány](active-directory-aadconnect-multiple-domains.md).
 
**K: van az Active Directory-tartományhoz az Azure AD Connect több összekötőket?**  
Az azonos AD-tartomány nem, több összekötők nem támogatottak. 

**K: helyezhetem át az Azure AD Connect-adatbázist a helyi adatbázis távoli SQL Server-példányra?**   
Igen, az alábbi lépéseket ennek módjáról ad általános útmutatást. Jelenleg dolgozunk egy részletesebb dokumentumot.
1. A LocalDB ADSync-adatbázis biztonsági mentése.
Ennek legegyszerűbb módja, hogy használja az SQL Server Management Studio Azure AD connecttel ugyanarra a gépre telepítve. Csatlakozás *(localdb)\.\ADSync*, majd készítsen biztonsági mentést az ADSync-adatbázis.

2. Az ADSync-adatbázis visszaállítása távoli SQL Server-példányhoz.

3. A meglévő ellen az Azure AD Connect telepítése [távoli SQL-adatbázis](active-directory-aadconnect-existing-database.md).
   A cikk bemutatja, hogyan telepíthető át egy helyi SQL-adatbázis használatával. Ha egy távoli SQL-adatbázis használatával telepít át, a folyamat 5. lépésében is be kell, amely a Windows szinkronizálási szolgáltatást futtató, meglévő szolgáltatásfiók. A szinkronizálási motor szolgáltatásfiók az alábbiakban ismertetjük:
   
      **Meglévő szolgáltatásfiók használata**: az Azure AD Connect alapértelmezés szerint a szinkronizálási szolgáltatások egy helyi szolgáltatásfiókot használja használatára. Ha távoli SQL Server-példányt vagy egy hitelesítést igénylő proxyt használ, egy felügyelt szolgáltatásfiókot vagy szolgáltatásfiók használata a tartományban, és ismernie kell a jelszót. Ezekben az esetekben adja meg a használni kívánt fiókot. Győződjön meg arról, hogy a telepítést futtató felhasználók SQL-rendszergazdák, hogy a szolgáltatás fiók bejelentkezési hitelesítő adatok hozhatók létre. További információkért lásd: [az Azure AD Connect-fiókok és engedélyek](active-directory-aadconnect-accounts-permissions.md#adsync-service-account). 
   
      A legújabb buildben az SQL-rendszergazda sávon kívül kiépítheti az adatbázist, majd az Azure AD Connect-rendszergazda adatbázis-tulajdonosi jogosultságokkal telepítheti. További információkért lásd: [SQL meghatalmazott rendszergazdai engedélyek használatával telepítse az Azure AD Connect](active-directory-aadconnect-sql-delegation.md).

Az egyszerűség kedvéért javasoljuk, hogy a felhasználók, akik az Azure AD Connect telepítése SQL-rendszergazdák lehet. Azonban legutóbbi-buildek használatával, így használja a delegált rendszergazdák SQL, leírtak szerint [SQL meghatalmazott rendszergazdai engedélyek használatával telepítse az Azure AD Connect](active-directory-aadconnect-sql-delegation.md).

## <a name="network"></a>Network (Hálózat)
**K: van egy tűzfal, a hálózati eszköz, illetve bármi más, amely korlátozza a kapcsolatok maradhat, nyissa meg a hálózaton lévő idejét. Az ügyféloldali időkorlát küszöbértéke lennie az Azure AD Connect használata esetén?**  
Az összes hálózati szoftvert, fizikai eszközök vagy bármi olyanra, amely korlátozza a maximális időtartam, a kapcsolat nyitva maradhat kell használnia egy küszöbértéket legalább öt percig (300 másodperc), ahol az Azure AD Connect-ügyfél telepítve van a kiszolgáló közötti kapcsolat és az Azure Active Directory. Ez a javaslat vonatkozik az összes korábban kiadott Microsoft Identity szinkronizálási eszközöket is.

**K: egycímkés tartományok (által) támogatott vannak?**  
Bár javasoljuk, hogy ez a hálózati konfiguráció ellen ([cikkben](https://support.microsoft.com/en-us/help/2269810/microsoft-support-for-single-label-domains)), névvégződés nélküli tartomány Azure AD Connect-szinkronizálás használata támogatott, mindaddig, amíg működik-e a hálózati konfigurációt a egyetlen szintű tartomány megfelelően.

**K: a támogatott különálló AD-tartományok erdők van?**  
Nem, az Azure AD Connect nem támogatja a helyszíni erdők, amelyek tartalmazzák a különálló névtereket.

**K: van "pontozott" NetBIOS-nevek támogatott?**  
Nem, az Azure AD Connect nem támogatja a helyszíni erdők vagy tartományok, ahol a NetBIOS-nevét tartalmazza, egy pontot (.).

**K: van támogatott tiszta IPv6-környezetben?**  
Nem, az Azure AD Connect nem támogatja a tiszta IPv6 környezetben.

## <a name="federation"></a>Összevonás
**K: Mi a teendő, esetén az Office 365 tanúsítvány megújítására kérő e-mailt kapok?**  
A tanúsítvány megújításával kapcsolatos útmutatásért lásd: [tanúsítványok megújítása](active-directory-aadconnect-o365-certs.md).

**K: van "automatikus frissítése függő" állítsa be a függő entitás Office 365-höz. Ha saját jogkivonat-aláíró tanúsítványa automatikusan áthalad teendője kell?**  
A cikkben ismertetett útmutatás [tanúsítványok megújítása](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Környezet
**K: támogatott-e a kiszolgáló átnevezése, az Azure AD Connect telepítése után?**  
Nem. A kiszolgáló nevének módosítása rendereli a szinkronizálási motor nem lehet kapcsolódni az SQL database-példány, és a szolgáltatás nem indult el.

## <a name="identity-data"></a>Azonosító adatok
**K: Miért nem egyezik meg az Azure ad userPrincipalName (UPN) attribútum a helyszíni egyszerű felhasználónév?**  
Információkért tanulmányozza a következő cikkeket:

* [Office 365, Azure vagy Intune-ban szereplő felhasználónevek nem egyeznek, a helyszíni egyszerű Felhasználónévvel vagy másodlagos bejelentkezési Azonosítóval](https://support.microsoft.com/en-us/kb/2523192)
* [Az egyszerű felhasználónév a felhasználói fiókok különböző összevont tartományt használandó módosítása után a módosítások nem az Azure Active Directory szinkronizálási eszköz szinkronizálása](https://support.microsoft.com/en-us/kb/2669550)

Beállíthatja, hogy a szinkronizálási motor frissítése az egyszerű Felhasználónevet, leírtak szerint az Azure AD [az Azure AD Connect szinkronizálási szolgáltatás funkciók](active-directory-aadconnectsyncservice-features.md).

**K: támogatott-e egyezéssel egy helyszíni Azure AD-csoport vagy kapcsolatobjektummal és a egy meglévő Azure AD-csoporthoz, vagy forduljon a objektumot?**  
Igen, a helyreállítható egyezés a proxyAddress alapján történik. Helyreállítható megfelelő vannak nem levelezési csoportok esetében nem támogatott.

**K: támogatott-e manuálisan beállítani az ImmutableId attribútum egy meglévő Azure AD-csoporthoz, vagy forduljon a merevlemez-egyezést, egy helyszíni Azure Active Directory-csoportba, vagy forduljon a objektum objektum?**  
Nem, manuálisan beállítást az ImmutableId attribútum egy meglévő Azure AD-csoport vagy kapcsolatobjektummal merevlemez-match, hogy jelenleg nem támogatott.

## <a name="custom-configuration"></a>Egyéni konfiguráció
**K: hol vannak dokumentálva az Azure AD Connect PowerShell-parancsmagok?**  
Ezen a helyen ismertetett parancsmagokat, kivéve más az Azure AD Connectben található PowerShell-parancsmagok nem támogatottak az ügyfél használja.

**K: használhatok a "Server exportálási/kiszolgáló importálás" lehetőséget, amely a Synchronization Service Managert, helyezze át a konfigurációs kiszolgáló között található?**  
Nem. Ez a beállítás nem kéri le a beállításokat, és nem lehet. Ehelyett a második kiszolgálón az alapkonfiguráció létrehozása varázsló segítségével, és a szinkronizálási Szabályszerkesztő használatával létrehozhat egy PowerShell-parancsfájlokat minden olyan egyéni szabály mozgatása kiszolgálók között. További információkért lásd: [párhuzamos migrálás](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**K: kell gyorsítótárazzák a jelszavakat az Azure bejelentkezési oldala, és a gyorsítótárazás megelőzhető, mert tartalmaz egy jelszó bemeneti elemet a *automatikus kiegészítés = "false"* attribútumot?**  
Jelenleg a HTML attribútumainak módosítása a **jelszó** mező, többek között az automatikus kiegészítés címke nem támogatott. Jelenleg folyamatban van egy szolgáltatás, amely lehetővé teszi, hogy egyedi JavaScript-kódot, amely lehetővé teszi bármely attribútum hozzáadását a **jelszó** mező.

**K: az Azure bejelentkezési oldala a felhasználók, akik korábban már sikeresen bejelentkezett felhasználónevek jeleníti meg. Ez a viselkedés ki lehet kapcsolni?**  
Jelenleg a HTML attribútumainak módosítása a **jelszó** beviteli mező, többek között az automatikus kiegészítés címke nem támogatott. Jelenleg folyamatban van egy szolgáltatás, amely lehetővé teszi, hogy egyedi JavaScript-kódot, amely lehetővé teszi bármely attribútum hozzáadását a **jelszó** mező.

**K: úgy, hogy az egyidejű munkamenetek van?**  
Nem.

## <a name="auto-upgrade"></a>Automatikus frissítés

**K: Mik az előnyei és következményeinek használatával automatikus frissítés?**  
Azt is beküldte ügyfeleknek, hogy azok az Azure AD Connect telepítési automatikus frissítés engedélyezése. Az az előnye, hogy Ön mindig kapják meg a legújabb javításokat, beleértve a biztonsági frissítéseket, a biztonsági réseket, hogy az Azure AD Connectben található. A frissítési folyamat fájdalommentes, és automatikusan megtörténik, amint egy új verzió érhető el. Az Azure AD Connect-ügyfeleket a sok ezer használja az automatikus frissítés minden egyes új kiadással.

Az automatikus frissítési folyamat mindig először hoz létre, hogy jogosult az automatikus frissítés egy telepítés. Ha jogosult, a frissítés végrehajtott és tesztelését. A folyamat is tartalmaz szabályokat és a meghatározott környezeti tényezők egyéni módosításokat keres. Ha a tesztek azt mutatják, hogy a frissítés nem sikerül, automatikusan visszaáll az előző verziót.

A környezet méretétől függően a folyamat eltarthat néhány óra múlva. Amíg a frissítés folyamatban van, nem a Windows Server Active Directory és az Azure AD közötti szinkronizálása nem történik.

**Kérdés érkezett egy e-mailt kapok, hogy az automatikus frissítés nem működik, és a egy új verzió telepítése van szükség. Miért kell ehhez?**  
A múlt évben kiadtunk egy verziója, az Azure AD Connect, bizonyos esetekben előfordulhat, hogy letiltotta az automatikus frissítési szolgáltatás a kiszolgálón. A probléma az Azure AD Connect verziója 1.1.750.0 kijavítása azt. Ha Ön a probléma által érintett, csökkentheti, javítsa ki a PowerShell-parancsprogram futtatásával, vagy manuálisan frissítse az Azure AD Connect legújabb verziójára. 

A PowerShell-parancsprogram futtatásához [töltse le a parancsfájlt](https://aka.ms/repairaadconnect) és futtatása az Azure AD Connect-kiszolgálón rendszergazdai PowerShell-ablakban. Futtassa a parancsfájlt, hogyan [a rövid videó](https://aka.ms/repairaadcau).

Manuálisan frissíteni, töltse le, és a AADConnect.msi fájl legújabb verzióját futtatják.
 
-  Ha a jelenlegi verzió régebbi, mint 1.1.750.0, [töltse le és frissítsen a legújabb verzióra](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
- Ha az Azure AD Connect verziója 1.1.750.0 vagy újabb, semmilyen további műveletet nem szükséges. A verzió, amely tartalmazza az automatikus frissítési javítás már használja. 

**Kérdés érkezett egy e-mailt kapok kívánja újból engedélyezni az automatikus frissítés a legújabb verzióra frissítéséhez. Verzió 1.1.654.0 használok. Kell frissíteni?**  
Igen, meg kell 1.1.750.0 frissítést vagy újabb kívánja újból engedélyezni az automatikus frissítés. [Töltse le és frissítsen a legújabb verzióra](https://www.microsoft.com/en-us/download/details.aspx?id=47594).

**Kérdés érkezett egy e-mailt kapok kívánja újból engedélyezni az automatikus frissítés a legújabb verzióra frissítéséhez. Ha az automatikus frissítés engedélyezéséhez használtam a PowerShell, is meg kell telepíteni a legújabb verzióra?**  
Igen, továbbra is szeretné 1.1.750.0 verzióra való frissítés vagy újabb verziója. A PowerShell-lel az automatikus frissítési szolgáltatás engedélyezése nem az elhárításukra automatikus frissítési 1.1.750.0 előtti verziói találhatók.

**Kérdés egy újabb verzióra frissíteni szeretné, de nem biztos, akik az Azure AD Connect telepített, és hogy nem rendelkezik a felhasználónév és jelszó. Szükséges, hogy ez?**
Ön nem ismernie kell a felhasználónevet és jelszót, amelyet eredetileg használt Azure AD Connect frissítése. A globális rendszergazdai szerepkörrel rendelkező összes Azure AD-fiókot használja.

**K: Hol találhatom meg az Azure AD Connect melyik verziót használok?**  
Annak ellenőrzéséhez, hogy melyik Azure AD Connect verziója van telepítve a kiszolgálón, a Vezérlőpultot, és keressen a Microsoft Azure AD Connect telepített verziója kiválasztásával **programok** > **programok és szolgáltatások** , ahogy az itt látható:

![A Vezérlőpulton az Azure AD Connect verziója](media/active-directory-aadconnect-faq/faq1.png)

**K: hogyan frissíthetem az Azure AD Connect legújabb verziójára?**  
A legújabb verzióra frissítésével kapcsolatban lásd: [az Azure AD Connect: frissítés egy előző verzióról a legújabbra](active-directory-aadconnect-upgrade-previous-version.md). 

**K: Mi már frissítve a legújabb verzióra, az Azure AD Connect tavaly. Szükségünk ismét?**  
Az Azure AD Connect csapata elvégzi a gyakori frissítések a szolgáltatáshoz. Hibajavítások és biztonsági frissítések, valamint az új funkciók kihasználhatják, fontos a kiszolgáló naprakészen tartása a legújabb verzióra. Ha engedélyezi az automatikus frissítés, a szoftver verziója automatikusan frissül. Az Azure AD Connect verziókiadások talál [az Azure AD Connect: verziókiadások](active-directory-aadconnect-version-history.md).

**K: mennyi időt vesz igénybe a frissítés végrehajtásához, és mi az a felhasználóim gyakorolt hatást?**  
Frissítéséhez szükséges idő a bérlő méretétől függ. A nagyobb vállalatok a legjobb, ha a frissítés végrehajtásához az esténként vagy a hétvége folyamán érdemes lehet. A frissítés során nincs szinkronizálási tevékenység kerül sor.

**K: az Azure AD Connect, de az Office portálon frissítettem véleményem továbbra is a DirSync említi. Mi ez?**  
Az Office-csapat dolgozik azon, hogy az Office portál frissítéseinek, hogy a jelenlegi termék nevét. Ezek nem feltétlenül melyik Szinkronizáló eszközt használ.

**K: a automatikus frissítési állapotban lévő példányok, "Felfüggesztett." Miért felfüggesztett azt? Érdemes engedélyezni?**  
Egy korábbi verziót, hogy bizonyos körülmények esetén hagyja meg az automatikus frissítési állapot "Felfüggesztett". Állítsa be a jelent meg. a hiba Manuális engedélyezése, technikailag lehetséges, de számos bonyolult lépéseket igényel. A legjobb dolog, amit az Azure AD Connect legújabb verziójának telepítéséhez.

**K: a vállalat szigorú változáskezelés követelményekkel rendelkezik, és szabályozhatja, mikor, rendelkezik leküldött szeretnék. Szabályozhatom, amikor az automatikus frissítés indul el?**  
Nem, nincs nincs ilyen szolgáltatást még ma. A funkció a bevezetését későbbi verziókhoz kiértékelése megtörténik.

**K: fog szerezhetem be egy e-mailt, ha az automatikus frissítés nem sikerült? Honnan tudom, hogy sikeres volt-e azt?**  
Ön nem kap értesítést, a frissítés eredménye. A funkció a bevezetését későbbi verziókhoz kiértékelése megtörténik.

**K: tegye közzé az idővonalon automatikus frissítésekkel leküldenie tervezésekor?**  
Automatikus frissítés egy újabb verzióra, a kibocsátási folyamat első lépéseként. Új kiadás történik, a frissítések automatikusan leküld. Az Azure AD Connect újabb verzióival az előzetesen bejelentett a [Azure AD ütemterv](../fundamentals/whats-new.md).

**K: automatikus frissítés is frissíteni az Azure AD Connect Health?**  
Igen, az automatikus frissítés frissíti az Azure AD Connect Health is.

**K: ne, emellett automatikus frissítése az Azure AD Connect kiszolgálók átmeneti módban?**  
Igen, akkor is automatikus frissítése az Azure AD Connect-kiszolgáló, amely átmeneti módban van.

**K: Ha automatikus frissítés sikertelen lesz, és saját Azure AD Connect-kiszolgáló nem indul el, mit kell tennem?**  
Bizonyos ritkán előforduló esetekben az Azure AD Connect szolgáltatás nem indul el a frissítés végrehajtása után. Ezekben az esetekben a kiszolgáló újraindítása általában megoldja a problémát. Ha az Azure AD Connect szolgáltatást még mindig nem indul el, nyisson egy támogatási jegyet. További információkért lásd: [létre szolgáltatáskérést, lépjen kapcsolatba Office 365 támogatási](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**K: nem biztos a kockázatok Mik az Azure AD Connect újabb verzióra frissítésekor. Meghívhat me segítik a frissítést?**  
Ha Ön segítségre van szüksége az Azure AD Connect újabb verziójára frissít, nyisson egy támogatási jegyet, [létre szolgáltatáskérést, lépjen kapcsolatba Office 365 támogatási](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Hibaelhárítás
**K: hogyan kaphat segítséget az Azure AD Connect?**

[A Microsoft Tudásbázisban (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Keressen rá a KB, a műszaki megoldásokhoz az Azure AD Connect-támogatással kapcsolatos gyakori javítás/csere problémákra.

[Az Azure Active Directory-fórumok](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Technikai kérdéseket és válaszokat kereshet, vagy a saját kérdései vannak a [az Azure AD-Közösség](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Kérhet támogatást az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
