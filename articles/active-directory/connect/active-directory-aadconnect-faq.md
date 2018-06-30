---
title: Az Azure Active Directory Connect – gyakori kérdések – |} Microsoft Docs
description: A cikk az Azure AD Connect kapcsolatos gyakori kérdésekre ad választ.
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
ms.openlocfilehash: 7edabc99da5e1466e848336c647a33213c9edd8b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132917"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect – gyakori kérdések

## <a name="general-installation"></a>Általános telepítési
**K: telepítési működnek, ha az Azure Active Directory (Azure AD) globális rendszergazda kéttényezős hitelesítést (2FA) engedélyezve van?**  
Ez a forgatókönyv a 2016. februári buildek frissítésétől támogatott.

**K: az Azure AD Connect felügyelet nélküli telepítéséhez úgy van?**  
Az Azure AD Connect telepítés csak akkor, ha a telepítési varázsló használata támogatott. Egy felügyelet nélküli, csendes telepítése nem támogatott.

**K: erdővel rendelkezem ahol tartománya nem érhető el. Hogyan kell telepíteni az Azure AD Connect?**  
Ez a forgatókönyv a 2016. februári buildek frissítésétől támogatott.

**K: az Azure Active Directory tartományi szolgáltatások (az Azure Active Directory tartományi szolgáltatások) rendszerállapot-ügynöke működnek a server core?**  
Igen. Az ügynök telepítése után az alábbi PowerShell-parancsmag segítségével befejezheti a regisztrációs folyamat során: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**K: az Azure AD Connect támogatási szinkronizálását a két tartomány Azure ad-val?**  
Igen, ez a forgatókönyv támogatott. Tekintse meg [több tartomány](active-directory-aadconnect-multiple-domains.md).
 
**K: az Active Directory-tartományhoz az Azure AD Connectben a több összekötő van?**  
Az azonos AD-tartomány nem, több összekötők használata nem támogatott. 

**K: áthelyezhető az Azure AD Connect-adatbázisban a helyi adatbázis egy távoli SQL Server-példányt?**   
Igen, az alábbi lépéseket ennek ad általános útmutatást. Jelenleg dolgozunk részletesebb dokumentum.
1. Készítsen biztonsági másolatot a LocalDB ADSync adatbázis.
Ennek legegyszerűbb módja az Azure AD Connect ugyanazon a számítógépen telepített SQL Server Management Studio használatával. Csatlakozás *(localdb)\.\ADSync*, és majd készítsen biztonsági másolatot az ADSync adatbázis.

2. Az ADSync adatbázis visszaállítása távoli SQL Server-példányhoz.

3. A meglévő szemben Azure AD Connect telepítése [távoli SQL-adatbázis](active-directory-aadconnect-existing-database.md).
   A cikk bemutatja, hogyan telepíthet át egy helyi SQL-adatbázis használatával. Ha távoli SQL-adatbázis használata telepít, 5. lépésben a folyamat is be kell meglévő szolgáltatásfiók, amely a Windows szinkronizálási szolgáltatás futni fog. A szinkronizálási motor szolgáltatásfiókot az alábbiakban ismertetjük:
   
      **Meglévő szolgáltatásfiók használata**: alapértelmezés szerint az Azure AD Connect szinkronizálási szolgáltatások virtuális szolgáltatás-fiókot használ használatára. Ha távoli SQL Server-példányt használ, vagy egy hitelesítést igénylő proxyt használ, a felügyelt szolgáltatásfiókok vagy szolgáltatásfiók használata a tartományban, és a jelszó ismerete. Ezekben az esetekben adja meg a használni kívánt fiókot. Ellenőrizze, hogy a telepítést futtató felhasználók SQL-ben a rendszergazdák, hogy a bejelentkezési hitelesítő adatokat a szolgáltatás fiók is létrehozható. További információkért lásd: [az Azure AD Connect-fiókok és engedélyek](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account). 
   
      A legújabb buildben az SQL-rendszergazda sávon kívül kiépítheti az adatbázist, majd az Azure AD Connect-rendszergazda adatbázis-tulajdonosi jogosultságokkal telepítheti. További információkért lásd: [telepítse az Azure AD Connect SQL meghatalmazott rendszergazdai jogosultság segítségével](active-directory-aadconnect-sql-delegation.md).

Egyszerű dolgot megtartásához ajánlatos, hogy felhasználók, akik az Azure AD Connect telepítése SQL-rendszergazdák. Azonban az legutóbbi buildek segítségével most használja meghatalmazott rendszergazdák SQL, a [SQL meghatalmazott rendszergazdai engedélyek használatával telepítse az Azure AD Connect](active-directory-aadconnect-sql-delegation.md).

## <a name="network"></a>Network (Hálózat)
**K: van egy tűzfal, a hálózati eszköz, vagy valami mást, amely korlátozza a kapcsolatok maradhat, nyissa meg a hálózaton lévő idő. Milyen az ügyféloldali időkorlát küszöbértéke kell az Azure AD Connect használatakor?**  
Minden hálózati szoftver, a fizikai eszközök vagy bármi más, amely korlátozza a maximális időt, amely kapcsolatok nyitva maradhat kell használnia a küszöbérték legalább öt perc (300 másodperc) a kiszolgáló, amelyen telepítve van-e az az Azure AD Connect ügyfél közötti kapcsolat és az Azure Active Directory. Ez a javaslat a korábban kiadott Microsoft Identity szinkronizálási eszközöket is vonatkozik.

**K: egycímkés tartományok (által) támogatott vannak?**  
Nem, az Azure AD Connect nem támogatja a helyszíni erdővel és által használó tartományokban.

**K: támogatott különálló AD-tartományok erdők vannak?**  
Nem, az Azure AD Connect nem támogatja a helyszíni erdők, amelyek tartalmazzák a különálló névtereket.

**K: vannak "pontozott" NetBIOS-nevek támogatott?**  
Nem, az Azure AD Connect nem támogatja a helyi erdők és tartományok, ahol a NetBIOS-nevet tartalmaz egy pontot (.).

**K: támogatott tiszta IPv6-környezetben van?**  
Nem, az Azure AD Connect nem támogatja a csak IPv6-környezetben.

## <a name="federation"></a>Összevonás
**K: Mit tehetek, ha jelenik meg az Office 365 tanúsítvány megújítására kérő e-mailt?**  
A tanúsítvány megújítására vonatkozó útmutatásért lásd: [megújítani a tanúsítványokat](active-directory-aadconnect-o365-certs.md).

**K: van "automatikus frissítése függő" állítsa be az Office 365 függő entitás. Kell tennie semmit, amikor a jogkivonat-aláíró tanúsítványa automatikusan visszaállítja a?**  
A cikkben ismertetett útmutatás [megújítani a tanúsítványokat](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Környezet
**K: támogatott a kiszolgáló átnevezése, az Azure AD Connect telepítése után?**  
Nem. A kiszolgáló nevének módosítása Ez a beállítás a szinkronizálási motor nem lehet kapcsolódni az SQL-adatbázispéldányt, és a szolgáltatás nem indult el.

## <a name="identity-data"></a>Azonosító adatok
**K: Miért nem egyezik meg a userPrincipalName (UPN) attribútum az Azure ad-ben a helyszíni egyszerű Felhasználónévvel?**  
Információkért lásd: ezek a cikkek:

* [Office 365, az Azure vagy az Intune-ban szereplő felhasználónevek nem egyeznek meg, a helyszíni egyszerű Felhasználónévvel vagy másodlagos bejelentkezési Azonosítóval](https://support.microsoft.com/en-us/kb/2523192)
* [A felhasználói fiók egyszerű másik összevont tartományt használandó módosítása után a módosítások nem az Azure Active Directory szinkronizáló eszköz által szinkronizálása](https://support.microsoft.com/en-us/kb/2669550)

Beállíthatja úgy is engedélyezi a szinkronizálási motor frissíteni a megadott egyszerű felhasználónév leírtak szerint az Azure AD [az Azure AD Connect szinkronizálási szolgáltatás szolgáltatások](active-directory-aadconnectsyncservice-features.md).

**K: támogatott soft-match egy helyszíni Azure AD-csoport vagy partner objektuma egy meglévő Azure AD-csoporthoz, vagy forduljon a objektum?**  
Igen, a letölthető megfelelés a proxyAddress alapul. Letölthető megfelelő csoportokat, amelyek nem levelezési nem támogatott.

**K: támogatott kézzel állítsa a ImmutableId attribútumot egy meglévő Azure AD-csoport, vagy forduljon a merevlemez-match azt egy helyszíni Azure Active Directory-csoportba, vagy forduljon a objektum objektum?**  
Nem, manuálisan beállítása a ImmutableId attribútum egy meglévő Azure AD-csoport vagy partner objektuma merevlemez-találatra azt jelenleg nem támogatott.

## <a name="custom-configuration"></a>Egyéni konfiguráció
**K: hol szerepelnek a PowerShell-parancsmagok az Azure AD Connect?**  
Ezen a webhelyen ismertetett parancsmagok kivételével más található az Azure AD Connect PowerShell-parancsmagok nem támogatottak a ügyfél használja.

**K: Használhatom a "Kiszolgáló exportálási/kiszolgáló importálás" lehetőséget, amely megtalálható a Synchronization Service Managert helyezhető át a konfigurációs kiszolgáló között?**  
Nem. Ez a beállítás nem kéri le az összes konfigurációs beállításokat, és nem lehet megjeleníteni. Ehelyett a varázsló segítségével hozzon létre az alapkonfigurációt a második kiszolgálón, és a szerkesztővel szinkronizálási szabály létrehozása a PowerShell-parancsfájlokat egyéni szabályok áthelyezése kiszolgálók között. További információkért lásd: [áttelepítési éppen](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**K: kell gyorsítótárazzák a jelszavakat az Azure bejelentkezési oldal, és ez gyorsítótárazási megelőzhető, mert tartalmaz a jelszó bemeneti elemnek a *autocomplete = "false"* attribútum?**  
Jelenleg a HTML attribútumainak módosítása a **jelszó** mező, beleértve az automatikus kiegészítés címke nem támogatott. Lehetővé teszi, hogy az egyéni JavaScript, amely lehetővé teszi, hogy azok az attribútumok hozzáadása jelenleg dolgozunk a **jelszó** mező.

**K: az Azure bejelentkezési oldal megjeleníti a felhasználónevek, a felhasználók, akik korábban már sikeresen bejelentkezett. Ez a viselkedés kikapcsolható?**  
Jelenleg a HTML attribútumainak módosítása a **jelszó** beviteli mezőt, beleértve az automatikus kiegészítés címke nem támogatott. Lehetővé teszi, hogy az egyéni JavaScript, amely lehetővé teszi, hogy azok az attribútumok hozzáadása jelenleg dolgozunk a **jelszó** mező.

**K: akadályozza meg, hogy a munkamenetek van?**  
Nem.

## <a name="auto-upgrade"></a>Automatikus frissítés

**K: Mi a következő előnyöket és használatával következményeit automatikus frissítését?**  
Azt is tanácsadás összes ügyfél automatikus frissítése az Azure AD Connect telepítésének engedélyezése. Az az előnye, hogy Ön mindig kapják meg a legújabb javítások, beleértve a biztonsági frissítések, az Azure AD Connect található biztonsági réseket. A frissítési folyamat csoportházirendes, és automatikusan megtörténik, amint egy új verziója érhető el. Az Azure AD Connect-ügyfelek a sok ezer automatikus frissítés minden új verziót használja.

Az automatikus frissítési folyamat először mindig megállapítja, hogy egy telepítés automatikusan frissíthető. A nem jogosult, ha a frissítés végrehajtásának és tesztelését. A folyamat is tartalmaz, szabályok és a megadott környezeti tényezők egyéni változásai keres. Ha a tesztek jelzik, hogy egy frissítés sikertelen, az előző verzió automatikusan helyreáll.

Attól függően, hogy a környezet mérete a folyamat eltarthat néhány óra múlva. Amíg a frissítés van folyamatban, nem Windows Server Active Directory és az Azure AD közötti szinkronizálás történik.

**K: kaptam egy e-mailt kapok, amely már nem működik az automatikus frissítés és egy új verzió telepítéséhez van szükség. Miért kell ehhez?**  
Az elmúlt évben megjelent egy verziója, az Azure AD Connect letiltó, bizonyos esetekben előfordulhat, hogy az automatikus frissítési szolgáltatás a kiszolgálón. A probléma az Azure AD Connectben verzió 1.1.750.0 megoldottuk azt. Ha Ön a probléma által érintett, csökkentheti az javításának PowerShell-parancsfájl futtatásával, vagy manuálisan frissítse az Azure AD Connect legújabb verziójának. 

A PowerShell parancsfájl futtatásához [töltse le a parancsfájl](https://aka.ms/repairaadconnect) , és futtassa az Azure AD Connect kiszolgálón egy rendszergazda PowerShell-ablakban. Futtassa a parancsfájlt, hogyan [a rövid videó](https://aka.ms/repairaadcau).

Frissítsen kézzel, töltse le, és a AADConnect.msi fájl legfrissebb verzióját futtassa.
 
-  Ha az aktuális verziója régebbi, mint 1.1.750.0, [töltse le, és frissítse a legújabb verzióra](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
- Ha az Azure AD Connect verziója 1.1.750.0 vagy újabb, semmilyen további műveletet. A verzió, amely tartalmazza az automatikus frissítési javítást már használja. 

**K: kaptam egy e-mailt kapok kívánja újból engedélyezni az automatikus frissítés legújabb verziójára történő frissítéshez. Használok 1.1.654.0 verziója. Frissíteni kell?**  
Igen, akkor verziójára 1.1.750.0 vagy újabb verziója szükséges újra engedélyezheti az automatikus frissítés. [Töltse le, és frissítse a legújabb verzióra](https://www.microsoft.com/en-us/download/details.aspx?id=47594).

**K: kaptam egy e-mailt kapok kívánja újból engedélyezni az automatikus frissítés legújabb verziójára történő frissítéshez. Ha hoztam PowerShell automatikus frissítés engedélyezéséhez, tegye továbbra is kell telepíteni a legújabb verziót?**  
Igen, továbbra is szeretné 1.1.750.0 verzióra való frissítés vagy újabb. Az automatikus frissítési szolgáltatás a PowerShell segítségével nem csökkenti az automatikus frissítési probléma 1.1.750.0 előtti verziói találhatók.

**K: kívánt újabb verziójára, de nem biztos, akik az Azure AD Connect telepítése, és azt nem rendelkezik a felhasználónév és jelszó. Tegye van erre szükség?**
Nem kell ismernie a felhasználónevet és jelszót, az Azure AD Connect frissítése eredetileg használt. A globális rendszergazdai szerepkörrel rendelkező bármely Azure AD-fiókot használni.

**K: Hogyan tudhatom meg, hogy milyen verziójú Azure AD Connect használok?**  
Annak ellenőrzéséhez, hogy melyik Azure AD Connect verziója telepítve van a kiszolgálón, nyissa meg a Vezérlőpultot, és keresse meg a telepített Microsoft Azure AD Connect kiválasztásával **programok** > **programok és szolgáltatások** , ahogy az itt látható:

![A Vezérlőpulton az Azure AD Connect-verzió](media/active-directory-aadconnect-faq/faq1.png)

**K: hogyan lehet frissíteni az Azure AD Connect legújabb verziójára?**  
A legújabb verzióra frissítésével kapcsolatban [az Azure AD Connect: frissítés egy korábbi verziójáról a legújabb](active-directory-aadconnect-upgrade-previous-version.md). 

**K: azt már frissített az Azure AD Connect legújabb verzióját az elmúlt évben. Szeretnénk frissítéséhez újra?**  
Az Azure AD Connect csapat gyakori frissít a szolgáltatáshoz. Is kihasználhatják a hibajavításokat és biztonsági frissítések, valamint az új funkciók, fontos a kiszolgáló naprakészen tartása a legújabb verzióra. Ha az automatikus frissítés engedélyezéséhez a szoftververzió automatikusan frissül. Az Azure AD Connect verziókiadások megkereséséhez lásd: [az Azure AD Connect: verziókiadások](active-directory-aadconnect-version-history.md).

**K: mennyi időt vesz igénybe, a frissítés végrehajtásához, és milyen a a felhasználók számára?**  
A frissítés szükséges idő a bérlő méretétől függ. Nagyobb szervezeteknek előfordulhat, a frissítés végrehajtásához a este vagy hétvégi ajánlott. A frissítés során a Nincs szinkronizálás tevékenység kerül sor.

**K: I úgy érzi, hogy az Azure AD Connect, de az Office-portál frissítettem továbbra is említi DirSync. Miért van szó?**  
Az Office-csapat az Office portál frissítéseinek aktuális termék nevének megfelelően működik. Ezek nem feltétlenül mely Szinkronizáló eszközt használ.

**K: az automatikus frissítési állapota azt jelzi, "Felfüggesztve." Miért felfüggesztve azt? Kell engedélyezhető azt?**  
Programhiba jelent a korábbi verziót, bizonyos körülmények között, hagyja el az automatikus frissítési állapot beállítása "Felfüggesztve." Manuális engedélyezése technikailag lehetséges, de számos összetett lépést igényel. Az ajánlott művelet, amelyet az Azure AD Connect legújabb verziójának telepítéséhez.

**K: a vállalat szigorú változáskezelés követelményekkel rendelkezik, és szabályozhatja, ha az rendelkezik leküldött szeretnék. Szabályozható, ha nincs elindítva az automatikus frissítés?**  
Nem, nincs ilyen szolgáltatás ma. A szolgáltatás egy későbbi kiadásban kiértékelése megtörténik.

**K: Ha az automatikus frissítése sikertelen volt fog hozzá egy e-mailt? Hogyan tudják meg, hogy, hogy az sikeres volt-e?**  
Nem lehet értesítést fog az eredmény a frissítés. A szolgáltatás egy későbbi kiadásban kiértékelése megtörténik.

**K: tegye közzé az ütemterv leküldéses automatikus frissítésének tervezésekor?**  
Automatikus frissítés első lépése a kiadás során egy újabb verziója. Ha új kiadását, automatikusan leküldött frissítések. Az Azure AD Connect újabb verziók az alábbiak az előre bejelentett a [Azure AD terv](../fundamentals/whats-new.md).

**K: automatikus frissítés is frissíteni az Azure AD Connect Health?**  
Igen, az automatikus frissítés is az Azure AD Connect Health frissíti.

**K: ne akkor is automatikus frissítési átmeneti módban az Azure AD Connect-kiszolgálók?**  
Igen, akkor is automatikus frissítési egy Azure AD Connect-kiszolgáló, amely átmeneti módban van.

**Kérdés: Ha az automatikus frissítés sikertelen lesz, és a saját Azure AD Connect-kiszolgáló nem indul el, mit kell tennem?**  
Bizonyos ritkán előforduló esetekben az Azure AD Connect szolgáltatás nem indul el a frissítés végrehajtása után. Ebben az esetben a kiszolgáló újraindul általában megoldja a problémát. Az Azure AD Connect szolgáltatás még mindig nem indul el, ha a támogatási jegy megnyitása. További információkért lásd: [Office 365 terméktámogatásának szolgáltatási kérelem létrehozása](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**K: nem biztos a kockázatok Mik az Azure AD Connect újabb verzióra való frissítésekor. Meghívhatja szeretnék segítséget kérek a frissítést?**  
Ha az Azure AD Connect egy újabb verziójára frissít, a következő támogatási jegy megnyitása kell [Office 365 terméktámogatásának szolgáltatási kérelem létrehozása](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Hibaelhárítás
**K: hogyan kaphat segítséget az Azure AD Connect?**

[A Microsoft Tudásbázisban (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Keresse meg az Azure AD Connect-támogatással kapcsolatos gyakori javítás / csere problémákat műszaki megoldásainak KB.

[Az Azure Active Directory-fórumok](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Technikai kérdések és válaszok kereséséhez, vagy kérdései vannak az saját címen [az Azure AD-Közösség](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Segítségre van szüksége az Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
