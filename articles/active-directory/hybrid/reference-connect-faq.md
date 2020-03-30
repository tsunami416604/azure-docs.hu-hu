---
title: Azure Active Directory csatlakozás – gyakori kérdések – | Microsoft dokumentumok
description: Ez a cikk választ ad az Azure AD Connectdel kapcsolatos gyakori kérdésekre.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5aa30bc819531ee8cc9cd337648a6cbc661bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149813"
---
# <a name="azure-active-directory-connect-faq"></a>Az Azure Active Directory csatlakozásról – gyakori kérdések

## <a name="general-installation"></a>Általános telepítés

**K: Hogyan keményíthetem meg az Azure AD Connect-kiszolgálómat a biztonsági támadási felület csökkentése érdekében?**

A Microsoft azt javasolja, hogy az Azure AD Connect-kiszolgáló edzése csökkentse az informatikai környezet kritikus összetevőjének biztonsági támadási felületét.  Az alábbi javaslatok követése csökkenti a szervezetre vonatkozó biztonsági kockázatokat.

* Az Azure AD Connect telepítése tartományhoz csatlakozó kiszolgálón, és a rendszergazdai hozzáférés korlátozása tartományi rendszergazdákra vagy más szigorúan ellenőrzött biztonsági csoportokra

További tudnivalókért lásd: 

* [Rendszergazdák csoportjainak védelme](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Beépített rendszergazdai fiókok védelme](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [A biztonság javítása és fenntartása a támadási felületek csökkentésével](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Az Active Directory támadási felületének csökkentése](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**K: Működni fog a telepítés, ha az Azure Active Directory (Azure AD) globális rendszergazdája kétfaktoros hitelesítést (2FA) engedélyezve van?**  
2016 februárjától ez a forgatókönyv támogatott.

**K: Van-e módja az Azure AD Connect felügyelet nélküli telepítésének?**  
Az Azure AD Connect telepítése csak a telepítővarázsló használata esetén támogatott. Felügyelet nélküli, csendes telepítés nem támogatott.

**K: Olyan erdővel rendelkezem, amelyhez egy tartomány nem érhető el. Hogyan telepíthetem az Azure AD Connectet?**  
2016 februárjától ez a forgatókönyv támogatott.

**K: Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) egészségügyi ügynök működik a kiszolgálómag?**  
Igen. Az ügynök telepítése után a következő PowerShell-parancsmag használatával fejezheti be a regisztrációs folyamatot: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**K: Támogatja az Azure AD Connect a szinkronizálást két tartományból egy Azure AD-re?**  
Igen, ez a forgatókönyv támogatott. Több [tartományból is hivatkozhat.](how-to-connect-install-multiple-domains.md)
 
**K: Rendelkezhet több összekötővel ugyanahhoz az Active Directory-tartományhoz az Azure AD Connectben?**  
Nem, ugyanahhoz az AD-tartományhoz több összekötő nem támogatott. 

**K: Áthelyezhetem az Azure AD Connect adatbázist a helyi adatbázisból egy távoli SQL Server-példányba?**   
Igen, a következő lépések általános útmutatást nyújtanak ehhez. Jelenleg egy részletesebb dokumentumon dolgozunk.
1. Biztonsági másolatot a LocalDB ADSync adatbázisról.
Ennek legegyszerűbb módja az SQL Server Management Studio használata az Azure AD Connecttel ugyanazon a gépen telepített en. Csatlakozzon *a (LocalDb)\ADSync*fájlhoz, majd biztonsági másolatot kell kötnie az ADSync adatbázisról.

2. Állítsa vissza az ADSync-adatbázist a távoli SQL Server-példányra.

3. Telepítse az Azure AD Connectet a meglévő [távoli SQL-adatbázisra.](how-to-connect-install-existing-database.md)
   A cikk bemutatja, hogyan lehet áttelepíteni egy helyi SQL-adatbázis használatával. Ha távoli SQL-adatbázist használ, a folyamat 5. Ezt a szinkronizálási motorszolgáltatás-fiókot itt ismerteti:
   
      **Meglévő szolgáltatásfiók használata:** Alapértelmezés szerint az Azure AD Connect egy virtuális szolgáltatásfiókot használ a használandó szinkronizálási szolgáltatásokhoz. Ha távoli SQL Server-példányt használ, vagy hitelesítést igénylő proxyt használ, használjon felügyelt szolgáltatásfiókot vagy szolgáltatásfiókot a tartományban, és ismerje a jelszót. Ezekben az esetekben adja meg a használni kívánt fiókot. Győződjön meg arról, hogy a telepítést futtató felhasználók az SQL rendszergazdái, hogy létre lehessen hozni a szolgáltatásfiók bejelentkezési hitelesítő adatait. További információ: [Azure AD Connect-fiókok és engedélyek.](reference-connect-accounts-permissions.md#adsync-service-account) 
   
      A legújabb buildben az SQL-rendszergazda sávon kívül kiépítheti az adatbázist, majd az Azure AD Connect-rendszergazda adatbázis-tulajdonosi jogosultságokkal telepítheti. További információt az [Azure AD Connect telepítése SQL delegált rendszergazdai engedélyekkel című témakörben talál.](how-to-connect-install-sql-delegation.md)

Annak érdekében, hogy a dolgok egyszerűek maradjanak, azt javasoljuk, hogy az Azure AD Connectet telepítő felhasználók legyenek az SQL rendszergazdái. A legutóbbi buildek nél azonban most már használhatja a delegált SQL-rendszergazdákat, ahogy azt az [Azure AD Connect telepítése az SQL delegált rendszergazdai engedélyeinek használatával ismerteti.](how-to-connect-install-sql-delegation.md)

**K: Melyek a terület bevált gyakorlatai?**  

Az alábbiakban egy tájékoztató dokumentum, amely bemutatja néhány, a legjobb gyakorlatok, hogy a mérnöki, támogatási és tanácsadóink dolgoztak ki az évek során.  Ez egy gyorsan hivatkozott listajelben jelenik meg.  Bár ez a lista megkísérli, hogy átfogó, előfordulhat, hogy további ajánlott eljárásokat, amelyek nem tette a listán még.

- Ha a Full SQL, akkor meg kell maradnia a helyi vs távoli
    - Kevesebb komló
    - Könnyebb hibaelhárítás
    - Kevésbé összetettség
    - Erőforrásokat kell kijelölni az SQL-hez, és lehetővé kell tennie a többletterhelést az Azure AD Connect és az operációs rendszer számára
- Bypass Proxy, ha egyáltalán lehetséges, ha nem tudja megkerülni a proxy, akkor meg kell győződnie arról, hogy az időtúlérték nagyobb, mint 5 perc.
- Ha proxy szükséges, akkor hozzá kell adnia a proxyt a machine.config fájlhoz
- Vegye figyelembe a helyi SQL-feladatokat és karbantartást, valamint azt, hogy ezek milyen hatással lesznek az Azure AD Connectre – különösen az újraindexelésre
- Győződjön meg arról, hogy a DNS nem tudja feloldani a külső
- Annak ellenőrzése, hogy [a kiszolgáló specifikációi](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) javaslatonként vannak-e, függetlenül attól, hogy fizikai vagy virtuális kiszolgálókat használ
- Győződjön meg arról, hogy ha virtuális kiszolgálót használ, hogy a szükséges erőforrások dedikáltak legyenek
- Győződjön meg arról, hogy a lemez és a lemez konfigurációja megfelel az SQL Server ajánlott eljárásoknak
- Az Azure AD Connect Health telepítése és konfigurálása figyelésre
- Használja az Azure AD Connect beépített törlési küszöbértéket.
- Gondosan tekintse át a kiadási frissítéseket, hogy felkészüljön az összes olyan változásra és új attribútumra, amelyet hozzá lehet adni
- Biztonsági mentés mindenről
    - Biztonsági másolat kulcsai
    - Biztonsági mentés szinkronizálási szabályai
    - Biztonsági másolat kiszolgálójának konfigurációja
    - SQL-adatbázis biztonsági mentése
- Győződjön meg arról, hogy nincsenek harmadik fél biztonsági mentési ügynökei, amelyek az SQL VSS Writer nélkül biztonsági másolatot készítenek (a harmadik féltől származó pillanatképekkel rendelkező virtuális kiszolgálókon gyakori)
- Az összetettség hozzáadásasorán használt egyéni szinkronizálási szabályok mennyiségének korlátozása
- Az Azure AD Connect-kiszolgálók kezelése 0.
- Legyen ebea felhőszinkronizálási szabályok módosításával anélkül, hogy tisztában lenne a hatásés a megfelelő üzleti vezetőkkel
- Győződjön meg arról, hogy a megfelelő URL-cím- és tűzfalportok meg vannak nyitva az Azure AD Connect és az Azure AD Connect Health támogatása érdekében
- Használja ki a felhőalapú szűrt attribútumot a látszólagos objektumok hibaelhárításához és megelőzéséhez
- Az átmeneti kiszolgálóval győződjön meg arról, hogy az Azure AD Connect konfigurációs dokumentálóját használja a kiszolgálók közötti konzisztencia érdekében
- Az átmeneti kiszolgálóknak külön adatközpontokban (fizikai helyek) kell lenniük
- Az átmeneti kiszolgálók nem magas rendelkezésre állású megoldásnak valók, de több átmeneti kiszolgálóval is rendelkezhet
- A "Lag" átmeneti kiszolgálók bevezetése hiba esetén enyhítheti a lehetséges állásidőt
- Az összes frissítés tesztelése és ellenőrzése az átmeneti kiszolgálón először
- Mindig ellenőrizze az exportálást, mielőtt átvált az átmeneti kiszolgálóra.  Használja ki az átmeneti kiszolgálót a teljes importáláshoz és a teljes szinkronizáláshoz az üzleti hatás csökkentése érdekében
- Az Azure AD Connect-kiszolgálók közötti verziók konzisztenciájának lehető legnagyobb mértékben megőrizése 

**K: Engedélyezhetem az Azure AD Connect számára az Azure AD Connector-fiók létrehozását a munkacsoport-gépen?**
Nem.  Annak érdekében, hogy az Azure AD Connect automatikusan létrehozza az Azure AD-összekötő-fiókot, a gép tartományhoz kell csatlakoztatni.  

## <a name="network"></a>Network (Hálózat)
**K: Tűzfallal, hálózati eszközzel vagy valami mással rendelkezem, amely korlátozza, hogy a kapcsolatok mikor maradhatnak nyitva a hálózaton. Mi legyen az ügyféloldali időtúlhasználati küszöbérték az Azure AD Connect használatakor?**  
Minden hálózati szoftvernek, fizikai eszköznek vagy bármi másnak, amely korlátozza a kapcsolatok nyitva maradásának maximális idejét, legalább öt perces (300 másodperces) küszöbértéket kell használnia az Azure AD Connect-ügyfelet tartalmazó kiszolgáló közötti kapcsolathoz. és az Azure Active Directory. Ez a javaslat az összes korábban kiadott Microsoft Identity szinkronizálási eszközre is vonatkozik.

**K: Az egycímkés tartományok (SLD-k) támogatottak?**  
Bár erősen javasoljuk, hogy ez a hálózati konfiguráció[(lásd a cikket),](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)az Azure AD Connect szinkronizálás egyetlen címke tartománnyal támogatott, mindaddig, amíg a hálózati konfiguráció az egyszintű tartomány megfelelően működik.

**K: Támogatottak a különálló AD-tartományokkal rendelkező erdők?**  
Nem, az Azure AD Connect nem támogatja a helyszíni erdők, amelyek különálló névterek.

**K: Támogatottak a "pontozott" NetBIOS-nevek?**  
Nem, az Azure AD Connect nem támogatja a helyszíni erdőket vagy tartományokat, ahol a NetBIOS-név egy pont (.) tartalmaz.

**K: A tiszta IPv6-környezet támogatott?**  
Nem, az Azure AD Connect nem támogatja a tiszta IPv6-környezetet.

**K:I többerdős környezettel rendelkezik, és a két erdő közötti hálózat nat -t (hálózati címfordítást) használ. Az Azure AD Connect használata a két erdő között támogatott?**</br>
Nem, az Azure AD Connect nat-en keresztül i. 

## <a name="federation"></a>Összevonás
**K. Mit tegyek, ha olyan e-mailt kapok, amely az Office 365-ös tanúsítványom megújítását kéri?**  
A tanúsítvány megújításával kapcsolatos útmutatásért tekintse meg a [tanúsítványok megújítását.](how-to-connect-fed-o365-certs.md)

**K: Az Office 365 függő entitáshoz "A függő entitás automatikus frissítése" beállítása van. Kell-e valamilyen műveletet végrehajtanom, amikor a jogkivonat-aláíró tanúsítvány automatikusan átgördül?**  
Használja a cikkben a tanúsítványok [megújítására](how-to-connect-fed-o365-certs.md)című útmutatót.

## <a name="environment"></a>Környezet
**K: Az Azure AD Connect telepítése után támogatja a kiszolgáló átnevezése?**  
Nem. A kiszolgáló nevének módosítása esetén a szinkronizálási motor nem tud csatlakozni az SQL-adatbázispéldányhoz, és a szolgáltatás nem tud elindulni.

**K: A következő generációs kriptográfiai (NGC) szinkronizálási szabályok támogatottak egy FIPS-kompatibilis gépen?**  
Nem.  Ez nem támogatott.

**K. Ha letiltottam egy szinkronizált eszközt (például HAADJ) az Azure Portalon, miért van újra engedélyezve?**<br>
Előfordulhat, hogy a szinkronizált eszközök a helyszínen vannak szerzőinek vagy sajátítva. Ha egy szinkronizált eszköz engedélyezve van a helyszínen, előfordulhat, hogy újra engedélyezve van az Azure Portalon, még akkor is, ha korábban letiltotta egy rendszergazda. Szinkronizált eszköz letiltásához a helyszíni Active Directory segítségével tiltsa le a számítógépfiókot.

**K. Ha letiltom a felhasználók bejelentkezését az Office 365-ben vagy az Azure AD portálon a szinkronizált felhasználók számára, miért oldja fel a zárolást, amikor újra bejelentkezik?**<br>
A szinkronizált felhasználók lehetnek a helyszínen, vagy elsajátította a helyszínen. Ha a fiók engedélyezve van a helyszínen, feloldhatja a rendszergazda által elhelyezett bejelentkezési blokk blokkolását.

## <a name="identity-data"></a>Azonosító adatok
**K: Miért nem felel meg a userPrincipalName (UPN) attribútum az Azure AD-ben a helyszíni egyszerű felhasználónévnek?**  
További információt az alábbi cikkekben talál:

* [Az Office 365,Azure vagy Intune felhasználónevei nem egyeznek meg a helyszíni UPN-nal vagy a másodlagos bejelentkezési azonosítóval](https://support.microsoft.com/kb/2523192)
* [A módosításokat az Azure Active Directory szinkronizálási eszköze nem szinkronizálja, miután módosította egy felhasználói fiók felhasználói felületét egy másik összevont tartomány használatára.](https://support.microsoft.com/kb/2669550)

Az Azure AD-t úgy is beállíthatja, hogy a szinkronizálási motor frissítse az upn-t, az [Azure AD Connect szinkronizálási szolgáltatásának szolgáltatásaiban leírtak szerint.](how-to-connect-syncservice-features.md)

**K: Támogatja egy helyszíni Azure AD-csoport vagy kapcsolattartó-objektum soft-match egy meglévő Azure AD-csoport vagy kapcsolattartó-objektum?**  
Igen, ez a lágy egyezés a proxyAddress-en alapul. Az illesztéses egyeztetés nem támogatott olyan csoportok esetében, amelyek nem engedélyezve vannak a levelezéshez.

**K: Támogatja az ImmutableId attribútum manuális beállítása egy meglévő Azure AD-csoportban vagy kapcsolattartó objektumban, hogy egy helyszíni Azure AD-csoportvagy kapcsolattartó-objektum hozad.**  
Nem, manuálisan állítja be az ImmutableId attribútumot egy meglévő Azure AD-csoportvagy kapcsolattartó-objektum on hard-match jelenleg nem támogatott.

## <a name="custom-configuration"></a>Egyéni konfiguráció
**K: Hol vannak a PowerShell-parancsmagok az Azure AD Connect dokumentált?**  
A jelen helyen dokumentált parancsmagok kivételével az Azure AD Connectben található többi PowerShell-parancsmagok nem támogatottak az ügyfelek számára.

**K: Használhatom a Szinkronizálási szolgáltatáskezelőben található "Kiszolgálóexportálás/-kiszolgáló importálása" lehetőséget a konfiguráció kiszolgálók közötti áthelyezéséhez?**  
Nem. Ez a beállítás nem olvassa be az összes konfigurációs beállítást, és nem szabad használni. Ehelyett a varázsló segítségével hozza létre az alapkonfigurációt a második kiszolgálón, és a szinkronizálási szabályszerkesztővel hozzon létre PowerShell-parancsfájlokat bármely egyéni szabály kiszolgálók közötti áthelyezéséhez. További információ: [Swing migration](how-to-upgrade-previous-version.md#swing-migration).

**K: A jelszavak gyorsítótárazhatók az Azure bejelentkezési lapjához, és ez a gyorsítótárazás megakadályozható, mert tartalmaz egy jelszóbemeneti elemet az *automatikus kiegészítés = "false"* attribútummal?**  
Jelenleg a **Jelszó** mező HTML-attribútumainak módosítása, beleértve az automatikus kiegészítési címkét is, nem támogatott. Jelenleg egy olyan szolgáltatáson dolgozunk, amely lehetővé teszi az egyéni JavaScript használatát, amely lehetővé teszi bármilyen attribútum hozzáadását a **Jelszó** mezőhöz.

**K: Az Azure bejelentkezési lap megjeleníti a felhasználóneveket a felhasználók, akik korábban sikeresen bejelentkezett. Ki lehet kapcsolni ezt a viselkedést?**  
Jelenleg a **Jelszó** beviteli mező HTML-attribútumainak módosítása, beleértve az automatikus kiegészítési címkét is, nem támogatott. Jelenleg egy olyan szolgáltatáson dolgozunk, amely lehetővé teszi az egyéni JavaScript használatát, amely lehetővé teszi bármilyen attribútum hozzáadását a **Jelszó** mezőhöz.

**K: Van-e módja annak, hogy megakadályozzák az egyidejű munkamenetek?**  
Nem.

## <a name="auto-upgrade"></a>Automatikus frissítés

**K: Milyen előnyei és következményei vannak az automatikus frissítés használatának?**  
Azt tanácsoljuk minden ügyfelünknek, hogy engedélyezze az automatikus frissítést az Azure AD Connect telepítéséhez. Ennek előnye, hogy mindig megkapja a legújabb javításokat, beleértve az Azure AD Connectben található biztonsági rések biztonsági frissítéseit. A frissítési folyamat fájdalommentes, és automatikusan megtörténik, amint egy új verzió elérhetővé válik. Az Azure AD Connect-ügyfelek ezrei használnak automatikus frissítést minden új kiadással.

Az automatikus frissítési folyamat mindig először azt határozza meg, hogy a telepítés jogosult-e az automatikus frissítésre. Ha jogosult, a frissítés végrehajtása és tesztelése történik. A folyamat magában foglalja a szabályok és a konkrét környezeti tényezők egyéni módosításának keresését is. Ha a tesztek azt mutatják, hogy a frissítés sikertelen, az előző verzió automatikusan visszaáll.

A környezet méretétől függően a folyamat néhány órát is igénybe vehet. Amíg a frissítés folyamatban van, nem történik szinkronizálás a Windows Server Active Directory és az Azure AD között.

**K: Kaptam egy e-mailt arról, hogy az automatikus frissítés már nem működik, és új verziót kell telepítenem. Miért kell ezt csinálnom?**  
Tavaly kiadtuk az Azure AD Connect egy verzióját, amely bizonyos körülmények között letiltotta az automatikus frissítési funkciót a kiszolgálón. A problémát az Azure AD Connect 1.1.750.0-s verziójában javítottuk. Ha a probléma érintette, enyhítheti azt egy PowerShell-parancsfájl futtatásával a javításhoz, vagy manuálisan frissíthet az Azure AD Connect legújabb verziójára. 

A PowerShell-parancsfájl futtatásához [töltse le a parancsfájlt,](https://aka.ms/repairaadconnect) és futtassa az Azure AD Connect-kiszolgálón egy felügyeleti PowerShell-ablakban. A parancsfájl futtatásának módjáról a [rövid videó megtekintéséhez tekintse meg ezt a rövid videót.](https://aka.ms/repairaadcau)

A manuális frissítéshez le kell töltenie és futtatnia kell az AADConnect.msi fájl legújabb verzióját.
 
-  Ha a jelenlegi verzió régebbi, mint az 1.1.750.0, [töltse le és frissítsen a legújabb verzióra.](https://www.microsoft.com/download/details.aspx?id=47594)
- Ha az Azure AD Connect verziója 1.1.750.0 vagy újabb, nincs szükség további műveletre. Már használja az automatikus frissítési javítást tartalmazó verziót. 

**K: Kaptam egy e-mailt, amely azt mondja, hogy frissítsek a legújabb verzióra az automatikus frissítés újbóli engedélyezéséhez. Az 1.1.654.0 verziót használom. Frissítenem kell?**  
Igen, az automatikus frissítés újbóli engedélyezéséhez frissítenie kell az 1.1.750.0-s vagy újabb verzióra. [Töltse le és frissítsen a legújabb verzióra.](https://www.microsoft.com/download/details.aspx?id=47594)

**K: Kaptam egy e-mailt, amely azt mondja, hogy frissítsek a legújabb verzióra az automatikus frissítés újbóli engedélyezéséhez. Ha a PowerShell t használtam az automatikus frissítés engedélyezéséhez, akkor is telepítenem kell a legújabb verziót?**  
Igen, továbbra is frissítenie kell az 1.1.750.0-s vagy újabb verzióra. Az automatikus frissítési szolgáltatás engedélyezése a PowerShell-lel nem csökkenti az 1.1.750.0 előtti verziókban talált automatikus frissítési problémát.

**K: Szeretnék frissíteni egy újabb verzióra, de nem vagyok biztos benne, hogy ki telepítette az Azure AD Connectet, és nem rendelkezünk a felhasználónévvel és a jelszóval. Szükségünk van erre?**
Nem kell ismernie a felhasználónevet és a jelszót, amelyet eredetileg az Azure AD Connect frissítéséhez használt. Használjon bármely Azure AD-fiókot, amely rendelkezik a globális rendszergazdai szerepkörrel.

**K: Hogyan találhatom meg, hogy az Azure AD Connect melyik verzióját használom?**  
Annak ellenőrzéséhez, hogy az Azure AD Connect melyik verziója van telepítve a kiszolgálón, lépjen a Vezérlőpultra, és keresse meg a Microsoft Azure AD Connect telepített verzióját a **Programok** > **és szolgáltatások**lehetőséget választva, ahogy az itt látható:

![Az Azure AD Connect verziója a Vezérlőpulton](./media/reference-connect-faq/faq1.png)

**K: Hogyan frissíthetek az Azure AD Connect legújabb verziójára?**  
Ha meg szeretné tudni, hogyan frissíthet a legújabb verzióra, olvassa el az [Azure AD Connect: Frissítés egy korábbi verzióról a legújabbra](how-to-upgrade-previous-version.md)című témakört. 

**K: Tavaly már frissítettünk az Azure AD Connect legújabb verziójára. Újra frissítenünk kell?**  
Az Azure AD Connect csapata gyakori frissítéseket készít a szolgáltatáshoz. A hibajavítások és a biztonsági frissítések, valamint az új funkciók előnyeinek kihasználása érdekében fontos, hogy a kiszolgáló naprakész maradjon a legújabb verzióval. Ha engedélyezi az automatikus frissítést, a szoftver verziója automatikusan frissül. Az Azure AD Connect verziókiadási előzményeinek megtekintéséhez tekintse meg az [Azure AD Connect: Verziókiadási előzmények című témakört.](reference-connect-version-history.md)

**K: Mennyi ideig tart a frissítés végrehajtása, és milyen hatással van a felhasználókra?**  
A frissítéshez szükséges idő a bérlő méretétől függ. A nagyobb szervezetek számára a legjobb, ha a frissítést este vagy hétvégén hajtja végre. A frissítés során nem történik szinkronizálási tevékenység.

**K: Azt hiszem, frissítettem az Azure AD Connectre, de az Office-portál továbbra is megemlíti a DirSync-et. Miért van ez?**  
Az Office csapata azon dolgozik, hogy az Office-portál frissítései tükrözzék az aktuális terméknevet. Nem tükrözi a használt szinkronizálási eszközt.

**K: Az automatikus frissítési állapotom azt mondja: "Felfüggesztve". Miért van felfüggesztve? Engedélyezzem?**  
Egy korábbi verzióban olyan hibát vezettek be, amely bizonyos körülmények között az automatikus frissítési állapotot "Felfüggesztett" állapotra állítja. Manuális engedélyezése technikailag lehetséges, de több összetett lépést igényel. A legjobb, amit tehetünk, hogy telepíti az Azure AD Connect legújabb verzióját.

**K: A vállalatomnak szigorú változáskezelési követelményei vannak, és azt szeretném ellenőrizni, hogy mikor tolta ki. Szabályozhatom, hogy mikor indul el az automatikus frissítés?**  
Nem, ma nincs ilyen funkció. A funkció kiértékelése folyamatban van egy későbbi kiadáshoz.

**K: Kapok egy e-mailt, ha az automatikus frissítés nem sikerült? Honnan fogom tudni, hogy sikeres volt?**  
A frissítés eredményéről nem kap értesítést. A funkció kiértékelése folyamatban van egy későbbi kiadáshoz.

**K: Közzétesz egy ütemtervet arra az időpontra vonatkozóan, amikor az automatikus frissítéseket ki szeretné nyomni?**  
Az automatikus frissítés az első lépés az újabb verzió kiadási folyamatában. Amikor új kiadás jelenik meg, a frissítések automatikusan leküldésesek lesznek. Az Azure AD Connect újabb verzióit előre bejelentik az [Azure AD ütemtervben.](../fundamentals/whats-new.md)

**K: Az automatikus frissítés az Azure AD Connect állapotát is frissíti?**  
Igen, az automatikus frissítés az Azure AD Connect Health-t is frissíti.

**K: Automatikusan frissíti az Azure AD Connect kiszolgálókat átmeneti módban?**  
Igen, automatikusan frissítheti az Azure AD Connect-kiszolgálót, amely átmeneti módban van.

**K: Ha az automatikus frissítés sikertelen, és az Azure AD Connect kiszolgáló nem indul el, mit tegyek?**  
Ritka esetekben az Azure AD Connect szolgáltatás nem indul el a frissítés végrehajtása után. Ezekben az esetekben a kiszolgáló újraindítása általában megoldja a problémát. Ha az Azure AD Connect szolgáltatás továbbra sem indul el, nyisson meg egy támogatási jegyet. További információt az [Office 365 ügyfélszolgálatának megkeresésére irányuló szolgáltatáskérés létrehozása című](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)témakörben talál. 

**K: Nem vagyok biztos benne, hogy milyen kockázatokkal jár, amikor az Azure AD Connect újabb verziójára frissítek. Fel tudsz hívni, hogy segítsek a frissítésben?**  
Ha segítségre van szüksége az Azure AD Connect újabb verziójára való frissítéshez, nyisson meg egy támogatási jegyet a Szolgáltatáskérés létrehozása című témakörben, [és forduljon az Office 365 támogatási szolgálatához.](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)

## <a name="troubleshooting"></a>Hibaelhárítás
**K: Hogyan kaphatok segítséget az Azure AD Connect?**

[Keresés a Microsoft Tudásbázisban (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Keressen technikai megoldásokat a kb-ban az Azure AD Connect támogatásával kapcsolatos gyakori hibajavítási problémákhoz.

[Azure Active Directory-fórumok](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Keressen technikai kérdéseket és válaszokat, vagy tegye fel saját kérdéseit [az Azure AD-közösségben.](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)

[Támogatás kérése az Azure AD-hez](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

**K: Miért látom a 6311-es és 6401-es eseményeket a szinkronizálási lépéshibák után?**

A 6311- **A kiszolgáló váratlan hibát észlelt a visszahívás végrehajtása közben,** és a 6401 - A felügyeleti ügynök **vezérlőváratlan hibát észlelt** – a rendszer mindig egy szinkronizálási lépés hiba után naplózza. A hibák elhárításához meg kell tisztítania a szinkronizálási lépéshibákat.  További információ: [Hibaelhárítás a szinkronizálás során,](tshoot-connect-sync-errors.md) valamint [az objektumok szinkronizálásának elhárítása az Azure AD Connect szinkronizálásával című témakörben talál.](tshoot-connect-objectsync.md)
