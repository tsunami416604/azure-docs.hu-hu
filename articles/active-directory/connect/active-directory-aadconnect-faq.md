---
title: 'Az Azure Active Directory Connect: Gyakori kérdések – |} Microsoft Docs'
description: Ezen a lapon az Azure AD Connect vonatkozó gyakran ismételt kérdések.
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
ms.openlocfilehash: 2e5a7cab5c9db0c13ca0c0986c18c86adf675562
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850286"
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Gyakori kérdések az Azure Active Directory Connect

## <a name="general-installation"></a>Általános telepítési
**K: telepítési működnek, ha az Azure AD globális rendszergazda 2FA engedélyezve van?**  
2016. februári a buildeket, az ebben a forgatókönyvben esetén támogatott.

**K: az Azure AD Connect felügyelet nélküli telepítéséhez úgy van?**  
Csak a telepítési varázsló segítségével az Azure AD Connect telepítése támogatott. Felügyelet nélküli és a csendes telepítés használata nem támogatott.

**K: erdővel rendelkezem ahol tartománya nem érhető el. Hogyan kell telepíteni az Azure AD Connect?**  
2016. februári a buildeket, az ebben a forgatókönyvben esetén támogatott.

**K: az Active Directory tartományi szolgáltatások health-ügynök működik a server core?**  
Igen. Az ügynök telepítése után végezze el a regisztrációs folyamat során a következő PowerShell-parancsmag használatával: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**K: AADConnect támogatja az Azure ad-val a két tartomány szinkronizálását?**</br>
Igen, ez a forgatókönyv támogatott. Tekintse meg [több tartományban](active-directory-aadconnect-multiple-domains.md)
 
**K: van több összekötőket az Active Directory-tartományhoz az Azure AD connect?**</br> Az azonos AD-tartomány nem, több összekötők használata nem támogatott. 

**K: áthelyezhető az Azure AD Connect-adatbázisban a helyi adatbázis egy távoli SQL Server?**</br> Igen, az alábbi lépéseket pedig általános útmutatást nyújtanak ennek módjáról.  Jelenleg dolgozunk egy részletesebb dokumentumot, amely hamarosan elérhető lesz.


   1. Biztonsági másolat a LocalDB "ADSync" adatbázis ennek legegyszerűbb módja, hogy az Azure AD Connect ugyanazon a számítógépen telepített SQL Server Management Studio használja. Csatlakozás "(localdb)\.\ADSync" – majd az ADSync adatbázis biztonsági mentése
   2. A "ADSync" adatbázis visszaállítása a távoli SQL-példányra
   3. A meglévő szemben Azure AD Connect telepítése [távoli SQL-adatbázis](active-directory-aadconnect-existing-database.md) hivatkozásra a megadása kötelező, ha áttelepíti a helyi SQL-adatbázis használata a lépéseit mutatja be. Ha távoli SQL-adatbázis használatával telepít majd 5. lépésben a folyamat is akkor adja meg, amely a Windows szinkronizálási szolgáltatás fut, mint a meglévő szolgáltatásfiók. A szinkronizálási motor szolgáltatásfiókot az alábbiakban ismertetjük:</br></br>
   **Meglévő szolgáltatásfiók használata**– az Azure AD Connect szinkronizálási szolgáltatások virtuális szolgáltatás-fiókot használ használandó alapértelmezés szerint. Ha távoli SQL-kiszolgálót, vagy egy hitelesítést igénylő proxyt használ, meg kell felügyelt szolgáltatásfiók vagy a szolgáltatás fiók használata a tartományban, és a jelszó ismerete. Ezekben az esetekben adja meg a használni kívánt fiókot. Bizonyosodjon meg róla, hogy a telepítést futtató felhasználó rendszergazda az SQL Serveren, hogy létre lehessen hozni bejelentkezési adatokat a szolgáltatásfiókhoz. További információ: [Azure AD Connect-fiókok és -engedélyek](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account).</br></br> A legújabb buildben az SQL-rendszergazda sávon kívül kiépítheti az adatbázist, majd az Azure AD Connect-rendszergazda adatbázis-tulajdonosi jogosultságokkal telepítheti. További információ: [Az Azure AD Connect telepítése SQL-lel delegált rendszergazdai engedélyekkel](active-directory-aadconnect-sql-delegation.md).

Tartsa dolgot egyszerű javasoljuk, hogy az Azure AD Connect telepítése felhasználó szerepel-e a rendszergazda az SQL. (Az SQL-rendszergazdák azonban a legutóbbi buildek most már használhatja meghatalmazott leírtak [Itt](active-directory-aadconnect-sql-delegation.md).

## <a name="network"></a>Network (Hálózat)
**K: van egy tűzfal, a hálózati eszköz, vagy valami mást, amely korlátozza a maximális időt kapcsolatok maradhat, nyissa meg a hálózaton. Mennyi ideig kell a ügyféloldali időkorlát küszöbértéke lehet, ha az Azure AD Connect használatával?**  
Minden hálózati szoftver, a fizikai eszközök vagy bármi más, amely korlátozza a maximális időt kapcsolatok nyitva maradhat a használata ajánlott a küszöbérték legalább 5 perc (300 másodperc) a kiszolgálóra, ahol az Azure AD Connect-ügyfél telepítve van és az Azure Active Directory közötti kapcsolatot. Ez a javaslat a korábban kiadott Microsoft Identity szinkronizálási eszközöket is vonatkozik.

**K: vannak (egy címke tartományok) által támogatott?**  
Nem, az Azure AD Connect nem támogatja a helyi erdők/tartományok által használatával.

**K: támogatott különálló AD-tartományok erdők vannak?**  
Nem, az Azure AD Connect nem támogatja a helyszíni erdők tartalmazó különálló névtereket.

**K: vannak "pontozott" nevű NetBios támogatott?**  
Nem, az Azure AD Connect nem támogatja a helyi erdők/tartományok ahol a NetBIOS-név pontot tartalmaz "." a neve.

**K: támogatott tiszta IPv6-környezetben van?**  
Nem, az Azure AD Connect nem támogatja a csak IPv6-környezetben.

## <a name="federation"></a>Összevonás
**K: Mit tehetek, ha jelenik meg egy e-mailt, amely az Office 365 tanúsítvány megújítása kérése**  
Útmutatás a a [megújítani a tanúsítványokat](active-directory-aadconnect-o365-certs.md) dokumentum ismerteti a tanúsítvány megújításához.

**K: van "automatikus frissítése függő" állítsa be az Office 365 függő entitáshoz. Kell tennie semmit, amikor a jogkivonat-aláíró tanúsítványa automatikusan visszaállítja a?**  
A cikkben ismertetett útmutatás [megújítani a tanúsítványokat](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Környezet
**K: támogatott a kiszolgáló átnevezése, az Azure AD Connect telepítése után?**  
Nem. A kiszolgálónév módosítása esetén a szinkronizálási motor nem lehet kapcsolódni az SQL-adatbázishoz, és a szolgáltatás nem fogja tudni elindítani.

## <a name="identity-data"></a>Azonosító adatok
**K: az egyszerű felhasználónév (userPrincipalName) attribútum az Azure ad-ben nem egyezik a helyszíni UPN - miért?**  
Ezek a cikkek lásd:

* [Office 365, az Azure vagy az Intune-ban szereplő felhasználónevek nem egyeznek meg, a helyszíni egyszerű Felhasználónévvel vagy másodlagos bejelentkezési Azonosítóval](https://support.microsoft.com/en-us/kb/2523192)
* [A felhasználói fiók egyszerű másik összevont tartományt használandó módosítása után a módosítások nem az Azure Active Directory szinkronizáló eszköz által szinkronizálása](https://support.microsoft.com/en-us/kb/2669550)

Beállíthatja úgy is engedélyezi a szinkronizálási motor frissítése a userPrincipalName leírtak szerint az Azure AD [az Azure AD Connect szinkronizálási szolgáltatás szolgáltatások](active-directory-aadconnectsyncservice-features.md).

**K: enyhe találatra támogatja azt a helyszíni AD-csoport vagy partner meglévő Azure AD-csoport vagy partner objektummal objektumokat?**  
Igen, a letölthető megfelelés a proxyAddress épül.  Letölthető megfelelő csoportokat, amelyek nem levelezési nem támogatott.

**K:, mert az azt manuális módon állítsa be a támogatott merevlemez egyező úgy, hogy a meglévő Azure AD-csoport vagy partner objektumok attribútum ImmutableId a helyszíni AD-csoport vagy partner objektumok?**  
Nem, manuálisan beállítása a ImmutableId attribútum Azure AD-csoport vagy partner objektum rögzített találatra azt jelenleg nem támogatott.

## <a name="custom-configuration"></a>Egyéni konfiguráció
**K: hol szerepelnek a PowerShell-parancsmagok az Azure AD Connect?**  
Kivételével a parancsmagokat ezen a helyen más található az Azure AD Connect PowerShell-parancsmagok nem támogatottak a ügyfél használja.

**K: használhatok "Kiszolgáló exportálási/kiszolgáló importálás" található *Synchronization Service Managert* helyezhető át a konfigurációs kiszolgáló között?**  
Nem. Ez a beállítás az összes konfigurációs beállítások beolvasása sikertelen lesz, és nem használható. Ehelyett használjon a varázsló az alapkonfiguráció létrehozása a második kiszolgálón, és a szerkesztővel szinkronizálási szabály létrehozása a PowerShell-parancsfájlokat egyéni szabályok áthelyezése kiszolgálók között. Lásd: [áttelepítési éppen](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**K: kell gyorsítótárazzák a jelszavakat az Azure bejelentkezési oldal, és ez megelőzhető ugyanis tartalmaz. a jelszó bemeneti elemnek az automatikus kiegészítés = "false" attribútum?**</br>
Jelenleg a jelszó bemeneti mező HTML attribútumainak módosítása, beleértve az automatikus kiegészítés címke nem támogatott. Van jelenleg végzett egy szolgáltatás, amely lehetővé teszi az egyéni Javascript, lehetővé téve azok az attribútumok hozzáadása a jelszó mező.

**K: az Azure bejelentkezési lapon felhasználónevei, akik korábban már sikeresen bejelentkezett felhasználók jelennek meg.  Ez a viselkedés kikapcsolható?**</br>
Jelenleg a jelszó bemeneti mező HTML attribútumainak módosítása, beleértve az automatikus kiegészítés címke nem támogatott. Van jelenleg végzett egy szolgáltatás, amely lehetővé teszi az egyéni Javascript, lehetővé téve azok az attribútumok hozzáadása a jelszó mező.

**K: akadályozza meg, hogy a munkamenetek van?**</br>
Nem.

## <a name="auto-upgrade"></a>Automatikus frissítés

**K: Mi a következő előnyöket és használatával következményeit automatikus frissítését?**</br>
Összes ügyfél automatikus frissítése az Azure AD Connect telepítésének engedélyezése az alábbi intézkedéseket javasoljuk. A következő előnyöket is, hogy a legújabb javítások, beleértve a biztonsági frissítések, az Azure AD Connect található biztonsági réseket mindig fognak kapni. A frissítési folyamat csoportházirendes, és automatikusan történik, amint egy új verziója érhető el. Az Azure AD Connect-ügyfelek a sok ezer automatikus frissítés minden új verziót használja.

Az automatikus frissítési folyamat először mindig meghatározzák hogy nélküli telepítés nem jogosult az automatikus frissítés (Ez a folyamat tartalmazza, szabályok, egyéni módosításait keres adott környezeti tényezők stb.), és ha igen, a frissítés végre és tesztelt. Ha a tesztek azt mutatják, hogy egy frissítés sikertelen volt, az előző verzió automatikusan lekérni vissza.

Attól függően, hogy a környezet mérete a folyamat eltarthat néhány óra múlva, és amíg megtörténik a frissítés, a Nincs szinkronizálás a Windows Server AD és az Azure AD között történik.

**K: kaptam egy e-mailt kapok, amely már nem működik az automatikus frissítés és új verzió telepítéséhez van szükség. Miért kell ehhez?**</br>
Az elmúlt évben, az Azure AD Connect letiltó, bizonyos esetekben előfordulhat, hogy az automatikus frissítési szolgáltatás a kiszolgálón egy verziója jelent meg. Ez a hiba kijavítása az Azure AD Connectben 1.1.750.0 verziója. Az ügyfelek, akik a probléma által érintett előfordulhat, hogy egy PowerShell-parancsprogrammal ez javítására, illetve a probléma elhárítása érdekében az Azure AD Connect legújabb verziójának frissítsen kézzel kell. 

A PowerShell-parancsprogrammal, töltse le a parancsfájl [Itt](https://aka.ms/repairaadconnect) és az AADConnect kiszolgálón egy rendszergazda PowerShell-ablakban futtassa a parancsfájlt. [Ez az egy rövid videót](https://aka.ms/repairaadcau) , amely a részben részletesen olvashat erről.

Frissítsen kézzel, töltse le, és a AADConnect.msi fájl legfrissebb verzióját futtassa.
 
-  Ha az aktuális verziója régebbi, mint 1.1.750.0, frissítenie kell a legújabb verzióra [amely innen tölthető le](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
- Ha az Azure AD Connect verziószáma 1.1.750.0 vagy újabb, nincs teendője elhárítása érdekében az automatikus frissítési probléma, mivel most már a verziót egy javítást a. 

**K: kaptam egy e-mailt kapok kívánja újból engedélyezni az automatikus frissítés legújabb verziójára történő frissítéshez. A 1.1.654.0 vagyok, kell frissíteni?** </br>    
Igen, akkor frissítenie kell a 1.1.750 vagy újabb kívánja újból engedélyezni az automatikus frissítés. Ez a hivatkozás, amely azt ismerteti, hogyan egy újabb verziójára történő frissítéshez

**K: kaptam egy e-mailt kapok kívánja újból engedélyezni az automatikus frissítés legújabb verziójára történő frissítéshez. Engedélyezze az automatikus frissítési hoztam PowerShell, továbbra is kell telepíteni a legújabb verziót?**</br>    
Igen, továbbra is szeretné 1.1.750.0 verzióra való frissítés vagy újabb. Az automatikus frissítés szolgáltatást, amely PowerShell nem csökkenti a található 1.1.750 előtti verziói az automatikus frissítési probléma

**K: kívánt újabb verziójára, de nem biztos, akik az Azure AD Connect telepítése, és azt nem rendelkezik a felhasználónév és jelszó.  Tegye van erre szükség?**</br>
Nem kell tudni, hogy a felhasználónév, és az Azure AD Connect – bármely Azure AD-fiókot, amely a globális rendszergazdai szerepkörrel rendelkezik. frissíteni kezdetben használt jelszót is használható.

**K: Hogyan tudhatom meg, hogy a vagyok az Azure AD Connect melyik verzióját?**</br>   
Ellenőrizze, hogy melyik Azure AD Connect verziója telepítve van a kiszolgálón, nyissa meg a Vezérlőpultot, és keresse meg a telepített programok > Programok és szolgáltatások"" a Microsoft Azure AD Connect:

![verzió:](media/active-directory-aadconnect-faq/faq1.png)

**K: hogyan végezhető el a legújabb verzió és az AADConnect?**</br>    
Ez [cikk](active-directory-aadconnect-upgrade-previous-version.md) egy újabb verzióra történő frissítésének ismerteti. 

**K: azt már a legújabb verzióra frissítve az AADConnect az elmúlt évben, szükséges a frissítéséhez újra?**</br> Az Azure AD Connect csapatok gyakori frissít a szolgáltatáshoz, és fontos, hogy a kiszolgáló legyen naprakész is kihasználhatják a hibajavításokat és biztonsági frissítések, valamint az új szolgáltatásokat a legújabb verzióra. Ha engedélyezi az automatikus frissítés szoftververziók automatikusan frissítve lesz. Az Azure AD Connect verziókiadások megkereséséhez kövesse a [hivatkozás](active-directory-aadconnect-version-history.md).

**K: mennyi ideig tart a hajtsa végre a frissítést, és mi az a a felhasználók számára?**</br>    
Frissítéséhez szükséges idő a bérlő méretétől függ, és nagyobb szervezeteknek célszerű elvégezni ezt a este vagy hétvégi lehet. A frissítés során a Nincs szinkronizálás tevékenység kerül sor.

**K: I úgy érzi, AADConnect frissítettem, de az Office portálon továbbra is említi DirSync.  Miért van szó?**</br>    
Az Office-csapat működik-e az Office portál frissítéseinek a jelenlegi termék név – ezek nem feltétlenül mely Szinkronizáló eszközt használ.

**K: I, az automatikus frissítési állapota be van jelölve, és "Felfüggesztve" felirat látható. Miért felfüggesztve azt? Kell engedélyezhető azt?**</br>     
Hiba az előző verziót, bizonyos körülmények között, akkor hagyja az automatikus frissítési állapot beállítása "felfüggesztett" lett bevezetve. Manuális engedélyezése technikailag lehetséges, de számos összetett lépést igényelnének, így a legjobb művelet, amelyet az Azure AD Connect legújabb verziójának telepítése

**K: a vállalat szigorú módosítás felügyeleti követelményekkel rendelkezik, és szabályozhatja, mikor leküldött szeretnék. Szabályozható, ha nincs elindítva az automatikus frissítési?**</br> Nem, nincs ilyen szolgáltatás ma, ez a funkció olyan dolog, amire folyamatban van egy későbbi kiadásban kiértékelése.

**K: Ha az automatikus frissítés nem sikerült fog hozzá egy e-mailt? Hogyan tudják meg, hogy, hogy az sikeres volt-e?**</br>     
Nem értesítést fog kapni az eredmény a frissítés, hogy ez a funkció olyan dolog, amire folyamatban van egy későbbi kiadásban kiértékelése.

**Tesz közzé, ha azt tervezi, hogy automatikus-frissítésének leküldéses idősorán Q:do?**</br>    
Automatikus frissítési első lépése a kiadás során egy újabb verzió, így ha egy új kiadási automatikus-frissítések leküldött. Az Azure AD Connect újabb verziók az alábbiak az előre bejelentett a [Azure AD terv](../../active-directory/whats-new.md).

**K: nem automatikus frissítési frissítési AAD Connect Health?**</br>   Igen, az automatikus frissítés is frissíti az AAD Connect Health

**K: ne is AAD-csatlakozás kiszolgálók automatikus frissítési az átmeneti környezetű üzemmód?**</br>   
Igen, akkor is automatikus frissítési egy Azure AD Connect-kiszolgáló, amely átmeneti módban van.

**Kérdés: Ha automatikus frissítési sikertelen lesz, és az AAD-Csatlakozás kiszolgálóhoz nem indul el, mi a teendő?**</br>   
Bizonyos ritkán előforduló esetekben az Azure AD Connect szolgáltatás nem indul el a frissítés végrehajtása után. Ezekben az esetekben indítsa újra a kiszolgálót, amely a hibát általában javítja. Az Azure AD Connect szolgáltatás még mindig nem indul el, ha a támogatási jegy megnyitása. Íme egy [hivatkozás](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) , amely azt ismerteti, hogyan ehhez. 

**K: nem biztos a kockázatok Mik az Azure AD Connect egy újabb verzióra történő frissítése során. Meghívhatja szeretnék segítséget kérek a frissítést?**</br>
Ha az Azure AD Connect egy újabb verziójára frissít, a támogatási jegy megnyitása kell, ez egy [hivatkozás](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) , amely bemutatja, hogyan ehhez.

## <a name="troubleshooting"></a>Hibaelhárítás
**K: hogyan kaphat segítséget az Azure AD Connect?**

[A Microsoft Tudásbázisban (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Keresse meg a Microsoft Tudásbázis (KB) az Azure AD Connect-támogatással kapcsolatos gyakori javítás / csere problémákat műszaki megoldások keresése.

[A Microsoft Azure Active Directory-fórumok](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Keressen, és keresse meg a technikai kérdések és a közösségi válaszok vagy saját kérdés feltevése kattintva [Itt](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Hogyan kérhet támogatást az Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)



